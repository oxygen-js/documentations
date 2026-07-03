# Обратное давление в потоках

Существует общая проблема, возникающая при обработке данных, которая называется [`обратным давлением`](https://en.wikipedia.org/wiki/Backpressure_routing) (backpressure) и описывает накопление данных за буфером во время их передачи. Когда принимающая сторона передачи выполняет сложные операции или по какой-либо причине работает медленнее, возникает тенденция к накоплению данных из входящего источника — словно засор.

Чтобы решить эту проблему, должна существовать система делегирования, обеспечивающая плавный поток данных от одного источника к другому. Разные сообщества решали эту задачу по-своему, применительно к своим программам; хорошими примерами являются каналы Unix (pipes) и сокеты TCP, и часто это называют _управлением потоком_ (flow control). В Node.js принятым решением стали потоки (streams).

Цель этого руководства — более подробно рассказать, что такое обратное давление и как именно потоки решают эту задачу в исходном коде Node.js. Вторая часть руководства познакомит вас с рекомендуемыми лучшими практиками, обеспечивающими безопасность и оптимизацию кода вашего приложения при реализации потоков.

Мы предполагаем некоторое знакомство с общим определением [`обратного давления`](https://en.wikipedia.org/wiki/Backpressure_routing), [`Buffer`](https://nodejs.org/api/buffer.html) и [`EventEmitters`](https://nodejs.org/api/events.html) в Node.js, а также некоторый опыт работы со [`Stream`](https://nodejs.org/api/stream.html). Если вы не читали эти документы, будет неплохой идеей сначала заглянуть в документацию по API, поскольку это поможет расширить ваше понимание при чтении данного руководства.

## Проблема обработки данных

В компьютерной системе данные передаются от одного процесса к другому через каналы (pipes), сокеты и сигналы. В Node.js мы находим схожий механизм под названием [`Stream`](https://nodejs.org/api/stream.html). Потоки прекрасны! Они делают так много для Node.js, и почти каждая часть внутренней кодовой базы использует этот модуль. Как разработчику, вам более чем рекомендуется тоже их использовать!
```js
const readline = require('node:readline');

// process.stdin и process.stdout — оба являются экземплярами потоков (Streams).
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question('Why should you use streams? ', answer => {
  console.log(`Maybe it's ${answer}, maybe it's because they are awesome! :)`);

  rl.close();
});
```

Хороший пример того, почему механизм обратного давления, реализованный через потоки, является отличной оптимизацией, можно продемонстрировать, сравнив внутренние системные инструменты из реализации [`Stream`](https://nodejs.org/api/stream.html) в Node.js.

В одном сценарии мы возьмём большой файл (примерно ~9 ГБ) и сожмём его с помощью привычного инструмента [`zip(1)`](https://linux.die.net/man/1/zip).
```
zip The.Matrix.1080p.mkv
```

Пока это будет выполняться несколько минут, в другой оболочке мы можем запустить скрипт, который использует модуль Node.js [`zlib`](https://nodejs.org/api/zlib.html), являющийся обёрткой над другим инструментом сжатия — [`gzip(1)`](https://linux.die.net/man/1/gzip).
```js
const fs = require('node:fs');
const gzip = require('node:zlib').createGzip();

const inp = fs.createReadStream('The.Matrix.1080p.mkv');
const out = fs.createWriteStream('The.Matrix.1080p.mkv.gz');

inp.pipe(gzip).pipe(out);
```

Чтобы проверить результаты, попробуйте открыть каждый сжатый файл. Файл, сжатый инструментом [`zip(1)`](https://linux.die.net/man/1/zip), уведомит вас о том, что он повреждён, тогда как сжатие, завершённое через [`Stream`](https://nodejs.org/api/stream.html), распакуется без ошибок.

> В этом примере мы используем `.pipe()`, чтобы передать источник данных с одного конца на другой. Однако обратите внимание, что здесь не подключены надлежащие обработчики ошибок. Если фрагмент данных не будет корректно получен, источник `Readable` или поток `gzip` не будут уничтожены. [`pump`](https://github.com/mafintosh/pump) — это вспомогательный инструмент, который корректно уничтожит все потоки в конвейере, если один из них завершится с ошибкой или закроется, и в данном случае он обязателен!

[`pump`](https://github.com/mafintosh/pump) необходим только для Node.js 8.x или более ранних версий, поскольку для Node.js 10.x и более поздних версий был введён [`pipeline`](https://nodejs.org/api/stream.html#stream_stream_pipeline_streams_callback) на замену [`pump`](https://github.com/mafintosh/pump). Это метод модуля для соединения потоков каналом с пробросом ошибок, корректной очисткой и предоставлением колбэка по завершении конвейера.

Вот пример использования pipeline:
```js
const fs = require('node:fs');
const { pipeline } = require('node:stream');
const zlib = require('node:zlib');

// Используем API pipeline, чтобы легко соединить каналом серию потоков
// вместе и получить уведомление, когда конвейер полностью завершится.
// Конвейер для эффективного gzip-сжатия потенциально огромного видеофайла:

pipeline(
  fs.createReadStream('The.Matrix.1080p.mkv'),
  zlib.createGzip(),
  fs.createWriteStream('The.Matrix.1080p.mkv.gz'),
  err => {
    if (err) {
      console.error('Pipeline failed', err);
    } else {
      console.log('Pipeline succeeded');
    }
  }
);
```

Вы также можете использовать модуль [`stream/promises`](https://nodejs.org/api/stream.html#streampipelinesource-transforms-destination-options), чтобы применять pipeline с `async` / `await`:
```js
const fs = require('node:fs');
const { pipeline } = require('node:stream/promises');
const zlib = require('node:zlib');

async function run() {
  try {
    await pipeline(
      fs.createReadStream('The.Matrix.1080p.mkv'),
      zlib.createGzip(),
      fs.createWriteStream('The.Matrix.1080p.mkv.gz')
    );
    console.log('Pipeline succeeded');
  } catch (err) {
    console.error('Pipeline failed', err);
  }
}
```

## Слишком много данных, слишком быстро

Бывают случаи, когда поток [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) может отдавать данные потоку [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) слишком быстро — намного быстрее, чем потребитель способен обработать!

Когда это происходит, потребитель начинает ставить все фрагменты данных в очередь для последующего потребления. Очередь записи становится всё длиннее и длиннее, и из-за этого всё больше данных приходится держать в памяти, пока весь процесс не завершится.

Запись на диск гораздо медленнее, чем чтение с диска, поэтому, когда мы пытаемся сжать файл и записать его на наш жёсткий диск, возникнет обратное давление, поскольку диск для записи не сможет угнаться за скоростью чтения.
```js
// Втайне поток говорит: "стоп, стоп! погоди, это слишком много!"
// Данные начнут накапливаться на стороне чтения буфера данных, пока
// `write` пытается угнаться за входящим потоком данных.
inp.pipe(gzip).pipe(outputFile);
```

Вот почему механизм обратного давления так важен. Если бы системы обратного давления не было, процесс израсходовал бы всю память вашей системы, эффективно замедляя другие процессы и монополизируя большую часть вашей системы до самого завершения.

Это приводит к нескольким вещам:

  * Замедлению всех остальных текущих процессов
  * Сильно перегруженному сборщику мусора
  * Исчерпанию памяти

В следующих примерах мы уберём [возвращаемое значение](https://github.com/nodejs/node/blob/55c42bc6e5602e5a47fb774009cfe9289cb88e71/lib/_stream_writable.js#L239) функции `.write()` и заменим его на `true`, что фактически отключает поддержку обратного давления в ядре Node.js. В любом упоминании «изменённого» бинарного файла речь идёт о запуске бинарного файла `node` без строки `return ret;`, а вместо неё с заменой на `return true;`.

## Излишняя нагрузка на сборку мусора

Давайте взглянем на быстрый бенчмарк. Используя тот же пример, что и выше, мы провели несколько замеров времени, чтобы получить медианное время для обоих бинарных файлов.
```
   trial (#)  | `node` binary (ms) | modified `node` binary (ms)
=================================================================
      1       |      56924         |           55011
      2       |      52686         |           55869
      3       |      59479         |           54043
      4       |      54473         |           55229
      5       |      52933         |           59723
=================================================================
average time: |      55299         |           55975
```

Оба выполняются около минуты, так что разница совсем невелика, но давайте присмотримся поближе, чтобы подтвердить, верны ли наши подозрения. Мы используем Linux-инструмент [`dtrace`](https://dtrace.org/about/), чтобы оценить, что происходит со сборщиком мусора V8.

Измеренное время GC (сборщика мусора) указывает интервалы полного цикла одного прохода (sweep), выполняемого сборщиком мусора:
```
approx. time (ms) | GC (ms) | modified GC (ms)
=================================================
          0       |    0    |      0
          1       |    0    |      0
         40       |    0    |      2
        170       |    3    |      1
        300       |    3    |      1

         *             *           *
         *             *           *
         *             *           *

      39000       |    6    |     26
      42000       |    6    |     21
      47000       |    5    |     32
      50000       |    8    |     28
      54000       |    6    |     35
```

Хотя оба процесса начинают одинаково и, по-видимому, задействуют GC с одинаковой частотой, становится очевидно, что уже через несколько секунд при исправно работающей системе обратного давления она распределяет нагрузку GC на согласованные интервалы в 4–8 миллисекунд вплоть до конца передачи данных.

Однако, когда система обратного давления отсутствует, сборка мусора V8 начинает затягиваться. Обычный бинарный файл вызывает GC примерно **75** раз в минуту, тогда как изменённый бинарный файл срабатывает лишь **36** раз.

Это медленно и постепенно накапливающийся долг от растущего потребления памяти. По мере передачи данных, без системы обратного давления, на каждый переносимый фрагмент используется всё больше памяти.

Чем больше памяти выделяется, тем больше приходится обрабатывать GC за один проход. Чем крупнее проход, тем больше GC нужно решать, что можно освободить, а сканирование в поисках отсоединённых указателей в большем пространстве памяти будет потреблять больше вычислительной мощности.

## Исчерпание памяти

Чтобы определить потребление памяти каждым бинарным файлом, мы засекли время каждого процесса по отдельности с помощью `/usr/bin/time -lp sudo ./node ./backpressure-example/zlib.js`.

Вот вывод для обычного бинарного файла:
```
Respecting the return value of .write()
=============================================
real        58.88
user        56.79
sys          8.79
  87810048  maximum resident set size
         0  average shared memory size
         0  average unshared data size
         0  average unshared stack size
     19427  page reclaims
      3134  page faults
         0  swaps
         5  block input operations
       194  block output operations
         0  messages sent
         0  messages received
         1  signals received
        12  voluntary context switches
    666037  involuntary context switches
```

Максимальный размер в байтах, занимаемый виртуальной памятью, оказывается равным примерно 87,81 МБ.

А теперь, изменив [возвращаемое значение](https://github.com/nodejs/node/blob/55c42bc6e5602e5a47fb774009cfe9289cb88e71/lib/_stream_writable.js#L239) функции [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback), мы получаем:
```
Without respecting the return value of .write():
==================================================
real        54.48
user        53.15
sys          7.43
1524965376  maximum resident set size
         0  average shared memory size
         0  average unshared data size
         0  average unshared stack size
    373617  page reclaims
      3139  page faults
         0  swaps
        18  block input operations
       199  block output operations
         0  messages sent
         0  messages received
         1  signals received
        25  voluntary context switches
    629566  involuntary context switches
```

Максимальный размер в байтах, занимаемый виртуальной памятью, оказывается равным примерно 1,52 ГБ.

Без потоков, которые делегируют обратное давление, выделяется на порядок больше пространства памяти — огромная разница для одного и того же процесса!

Этот эксперимент показывает, насколько оптимизированным и экономичным является механизм обратного давления Node.js для вашей вычислительной системы. Теперь давайте разберём, как он работает!

## Как обратное давление решает эти проблемы?

Существуют разные функции для передачи данных от одного процесса к другому. В Node.js есть внутренняя встроенная функция [`.pipe()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_pipe_destination_options). Существуют и [другие пакеты](https://github.com/sindresorhus/awesome-nodejs#streams), которые вы тоже можете использовать! В конечном счёте, однако, на базовом уровне этого процесса у нас есть два отдельных компонента: _источник_ данных и _потребитель_.

Когда [`.pipe()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_pipe_destination_options) вызывается из источника, он сигнализирует потребителю о наличии данных для передачи. Функция pipe помогает настроить соответствующие замыкания обратного давления для триггеров событий.

В Node.js источник — это поток [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams), а потребитель — поток [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) (оба они могут быть заменены на поток [`Duplex`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) или [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams), но это выходит за рамки данного руководства).

Момент, когда срабатывает обратное давление, можно точно свести к возвращаемому значению функции [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) потока [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams). Это возвращаемое значение, разумеется, определяется несколькими условиями.

В любом сценарии, где буфер данных превысил [`highWaterMark`](https://nodejs.org/api/stream.html#stream_buffering) или очередь записи в данный момент занята, [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) вернёт `false`.

Когда возвращается значение `false`, включается система обратного давления. Она приостановит входящий поток [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) от отправки каких-либо данных и будет ждать, пока потребитель снова не будет готов. Как только буфер данных опустеет, будет сгенерировано событие [`'drain'`](https://nodejs.org/api/stream.html#stream_event_drain), которое возобновит входящий поток данных.

Когда очередь опустеет, обратное давление снова позволит отправлять данные. Использованное пространство в памяти освободится и подготовится к следующей партии данных.

Это эффективно позволяет использовать фиксированный объём памяти в любой момент времени для функции [`.pipe()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_pipe_destination_options). Не будет ни утечек памяти, ни бесконечной буферизации, и сборщику мусора придётся иметь дело лишь с одной областью в памяти!

Итак, если обратное давление настолько важно, почему вы (вероятно) о нём не слышали? Что ж, ответ прост: Node.js делает всё это автоматически за вас.

Это же здорово! Но не так уж здорово, когда мы пытаемся понять, как реализовать наши собственные потоки.

> На большинстве машин существует размер в байтах, определяющий, когда буфер заполнен (он будет различаться на разных машинах). Node.js позволяет вам задать свой пользовательский [`highWaterMark`](https://nodejs.org/api/stream.html#stream_buffering), но обычно значение по умолчанию равно 16 КБ (16384, или 16 для потоков в objectMode). В случаях, когда вам может понадобиться поднять это значение, дерзайте, но делайте это с осторожностью!

## Жизненный цикл `.pipe()`

Чтобы лучше понять обратное давление, вот блок-схема жизненного цикла потока [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams), который [соединяется каналом](https://nodejs.org/docs/latest/api/stream.html#stream_readable_pipe_destination_options) с потоком [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams):
```
                                                     +===================+
                         x-->  Piping functions   +-->   src.pipe(dest)  |
                         x     are set up during     |===================|
                         x     the .pipe method.     |  Event callbacks  |
  +===============+      x                           |-------------------|
  |   Your Data   |      x     They exist outside    | .on('close', cb)  |
  +=======+=======+      x     the data flow, but    | .on('data', cb)   |
          |              x     importantly attach    | .on('drain', cb)  |
          |              x     events, and their     | .on('unpipe', cb) |
+---------v---------+    x     respective callbacks. | .on('error', cb)  |
|  Readable Stream  +----+                           | .on('finish', cb) |
+-^-------^-------^-+    |                           | .on('end', cb)    |
  ^       |       ^      |                           +-------------------+
  |       |       |      |
  |       ^       |      |
  ^       ^       ^      |    +-------------------+         +=================+
  ^       |       ^      +---->  Writable Stream  +--------->  .write(chunk)  |
  |       |       |           +-------------------+         +=======+=========+
  |       |       |                                                 |
  |       ^       |                              +------------------v---------+
  ^       |       +-> if (!chunk)                |    Is this chunk too big?  |
  ^       |       |     emit .end();             |    Is the queue busy?      |
  |       |       +-> else                       +-------+----------------+---+
  |       ^       |     emit .write();                   |                |
  |       ^       ^                                   +--v---+        +---v---+
  |       |       ^-----------------------------------<  No  |        |  Yes  |
  ^       |                                           +------+        +---v---+
  ^       |                                                               |
  |       ^               emit .pause();          +=================+     |
  |       ^---------------^-----------------------+  return false;  <-----+---+
  |                                               +=================+         |
  |                                                                           |
  ^            when queue is empty     +============+                         |
  ^------------^-----------------------<  Buffering |                         |
               |                       |============|                         |
               +> emit .drain();       |  ^Buffer^  |                         |
               +> emit .resume();      +------------+                         |
                                       |  ^Buffer^  |                         |
                                       +------------+   add chunk to queue    |
                                       |            <---^---------------------<
                                       +============+
```

> Если вы настраиваете конвейер, чтобы связать несколько потоков вместе для манипуляции вашими данными, вы, скорее всего, будете реализовывать поток [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams).

В этом случае вывод из вашего потока [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) попадёт в [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) и будет соединён каналом с [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams).
```
Readable.pipe(Transformable).pipe(Writable);
```

Обратное давление будет применено автоматически, но учтите, что как входящее, так и исходящее значение `highWaterMark` потока [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) можно изменять, и это повлияет на систему обратного давления.

## Рекомендации по обратному давлению

Начиная с [Node.js v0.10](https://nodejs.org/docs/v0.10.0/), класс [`Stream`](https://nodejs.org/api/stream.html) предлагает возможность изменять поведение [`.read()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_read_size) или [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback), используя версии этих функций с подчёркиванием ([`._read()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_read_size_1) и [`._write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback_1)).

Существуют задокументированные рекомендации по [реализации потоков Readable](https://nodejs.org/docs/latest/api/stream.html#stream_implementing_a_readable_stream) и [реализации потоков Writable](https://nodejs.org/docs/latest/api/stream.html#stream_implementing_a_writable_stream). Мы будем предполагать, что вы их прочитали, и следующий раздел углубится в тему немного подробнее.

## Правила, которых следует придерживаться при реализации собственных потоков

Золотое правило потоков — **всегда уважать обратное давление**. То, что является лучшей практикой, — это непротиворечивая практика. Пока вы аккуратно избегаете поведения, конфликтующего с внутренней поддержкой обратного давления, вы можете быть уверены, что следуете хорошей практике.

В общем случае:

  1. Никогда не вызывайте `.push()`, если вас об этом не просят.
  2. Никогда не вызывайте `.write()` после того, как он вернул false, а вместо этого дождитесь события 'drain'.
  3. Потоки меняются между разными версиями Node.js и в зависимости от используемой библиотеки. Будьте осторожны и тестируйте.

> Относительно пункта 3: невероятно полезным пакетом для построения браузерных потоков является [`readable-stream`](https://github.com/nodejs/readable-stream). Родд Вэгг (Rodd Vagg) написал [отличную запись в блоге](https://r.va.gg/2014/06/why-i-dont-use-nodes-core-stream-module.html), описывающую полезность этой библиотеки. Вкратце, она обеспечивает своего рода автоматическую плавную деградацию для потоков [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) и поддерживает более старые версии браузеров и Node.js.

## Правила, специфичные для потоков Readable

До сих пор мы рассматривали, как [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) влияет на обратное давление, и во многом сосредотачивались на потоке [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams). Из-за особенностей работы Node.js данные технически текут вниз по течению от [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) к [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams). Однако, как мы можем наблюдать при любой передаче данных, материи или энергии, источник так же важен, как и приёмник, и поток [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) жизненно важен для того, как обрабатывается обратное давление.

Оба этих процесса полагаются друг на друга для эффективного взаимодействия: если [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) игнорирует то, что поток [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) просит его прекратить отправку данных, это может быть столь же проблематично, как и неверное возвращаемое значение [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback).

Итак, помимо уважения возвращаемого значения [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback), мы также должны уважать возвращаемое значение [`.push()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_push_chunk_encoding), используемого в методе [`._read()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_read_size_1). Если [`.push()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_push_chunk_encoding) возвращает значение `false`, поток прекратит чтение из источника. В противном случае он продолжит без паузы.

Вот пример плохой практики использования [`.push()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_push_chunk_encoding):
```js
// Это проблематично, поскольку полностью игнорирует значение, возвращаемое push,
// которое может быть сигналом обратного давления (backpressure) от потока-приёмника!
class MyReadable extends Readable {
  _read(size) {
    let chunk;
    while (null !== (chunk = getNextChunk())) {
      this.push(chunk);
    }
  }
}
```

Вот пример хорошей практики, где поток `Readable` уважает обратное давление, проверяя возвращаемое значение `this.push()`:
```js
class MyReadable extends Readable {
  _read(size) {
    let chunk;
    let canPushMore = true;
    while (canPushMore && null !== (chunk = getNextChunk())) {
      canPushMore = this.push(chunk);
    }
  }
}
```

Кроме того, за пределами пользовательского потока есть свои подводные камни при игнорировании обратного давления. В этом контрпримере хорошей практики код приложения проталкивает данные всякий раз, когда они доступны (сигнализируется [событием `'data'`](https://nodejs.org/api/stream.html#stream_event_data)):
```js
// Это игнорирует механизмы обратного давления, установленные в Node.js,
// и безусловно проталкивает данные, независимо от того,
// готов ли к ним поток-приёмник или нет.
readable.on('data', data => writable.write(data));
```

Вот пример использования [`.push()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_push_chunk_encoding) с потоком Readable.
```js
const { Readable } = require('node:stream');

// Создаём пользовательский поток Readable
const myReadableStream = new Readable({
  objectMode: true,
  read(size) {
    // Проталкиваем немного данных в поток
    this.push({ message: 'Hello, world!' });
    this.push(null); // Помечаем конец потока
  },
});

// Потребляем поток
myReadableStream.on('data', chunk => {
  console.log(chunk);
});

// Вывод:
// { message: 'Hello, world!' }
```

В этом примере мы создаём пользовательский поток Readable, который проталкивает один объект в поток с помощью [`.push()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_push_chunk_encoding). Метод [`._read()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_read_size_1) вызывается, когда поток готов потреблять данные, и в данном случае мы немедленно проталкиваем немного данных в поток и помечаем его конец, протолкнув null.

Затем мы потребляем поток, слушая событие 'data' и логируя каждый фрагмент данных, который проталкивается в поток. В данном случае мы проталкиваем в поток только один фрагмент данных, поэтому видим лишь одно сообщение в логе.

## Правила, специфичные для потоков Writable

Напомним, что [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) может возвращать true или false в зависимости от некоторых условий. К счастью для нас, при построении нашего собственного потока [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) [`конечный автомат потока`](https://en.wikipedia.org/wiki/Finite-state_machine) будет обрабатывать наши колбэки и определять, когда обрабатывать обратное давление и оптимизировать поток данных за нас.

Однако, когда мы хотим использовать [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) напрямую, мы должны уважать возвращаемое значение [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) и обращать пристальное внимание на следующие условия:

  * Если очередь записи занята, [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) вернёт false.
  * Если фрагмент данных слишком велик, [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) вернёт false (предел указывается переменной [`highWaterMark`](https://nodejs.org/api/stream.html#stream_buffering)).

```js
// Этот writable некорректен из-за асинхронной природы колбэков в JavaScript.
// Без оператора return для каждого колбэка, кроме последнего,
// велика вероятность, что будет вызвано несколько колбэков.
class MyWritable extends Writable {
  _write(chunk, encoding, callback) {
    if (chunk.toString().indexOf('a') >= 0) {
      callback();
    } else if (chunk.toString().indexOf('b') >= 0) {
      callback();
    }
    callback();
  }
}

// Правильный способ написать это следующий:
if (chunk.contains('a')) {
  return callback();
}

if (chunk.contains('b')) {
  return callback();
}
callback();
```

Есть также некоторые моменты, на которые стоит обратить внимание при реализации [`._writev()`](https://nodejs.org/api/stream.html#stream_writable_writev_chunks_callback). Эта функция связана с [`.cork()`](https://nodejs.org/api/stream.html#writablecork), но при написании кода часто допускают одну распространённую ошибку:
```js
// Двойной вызов .uncork() здесь делает два обращения к слою C++, из-за чего
// техника cork/uncork становится бесполезной.
ws.cork();
ws.write('hello ');
ws.write('world ');
ws.uncork();

ws.cork();
ws.write('from ');
ws.write('Matteo');
ws.uncork();

// Правильный способ написать это — использовать process.nextTick(), который срабатывает
// на следующем цикле событий.
ws.cork();
ws.write('hello ');
ws.write('world ');
process.nextTick(doUncork, ws);

ws.cork();
ws.write('from ');
ws.write('Matteo');
process.nextTick(doUncork, ws);

// Как глобальная функция.
function doUncork(stream) {
  stream.uncork();
}
```

[`.cork()`](https://nodejs.org/api/stream.html#writablecork) можно вызывать сколько угодно раз, нужно лишь быть внимательным и вызвать [`.uncork()`](https://nodejs.org/api/stream.html#stream_writable_uncork) столько же раз, чтобы поток снова заработал.

## Заключение

Потоки — часто используемый модуль в Node.js. Они важны для внутренней структуры, а для разработчиков — для расширения и связывания в рамках экосистемы модулей Node.js.

Надеемся, что теперь вы сможете диагностировать проблемы и безопасно писать свои собственные потоки [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) и [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams), помня об обратном давлении, и делиться своими знаниями с коллегами и друзьями.

Обязательно почитайте подробнее о [`Stream`](https://nodejs.org/api/stream.html), чтобы узнать о других функциях API, которые помогут улучшить и раскрыть ваши возможности работы с потоками при создании приложения на Node.js.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/modules/backpressuring-in-streams>. Оригинал распространяется по лицензии MIT._

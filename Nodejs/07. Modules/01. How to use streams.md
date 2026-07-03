# Как использовать потоки

Работа с большими объёмами данных в приложениях Node.js — палка о двух концах. Возможность обрабатывать огромные объёмы данных крайне удобна, но может также приводить к узким местам в производительности и исчерпанию памяти. Традиционно разработчики решали эту задачу, считывая весь набор данных в память сразу. Этот подход, хоть и интуитивно понятен для небольших наборов данных, становится неэффективным и ресурсоёмким для больших данных (например, файлов, сетевых запросов…).

Именно здесь на сцену выходят потоки Node.js. Потоки предлагают принципиально иной подход, позволяя обрабатывать данные постепенно и оптимизировать использование памяти. Обрабатывая данные управляемыми порциями (chunks), потоки дают вам возможность создавать масштабируемые приложения, способные эффективно справляться даже с самыми пугающими наборами данных. Как гласит популярная фраза, «потоки — это массивы во времени».

В этом руководстве мы даём обзор концепции потоков, их истории и API, а также некоторые рекомендации по их использованию и работе с ними.

## Что такое потоки в Node.js?

Потоки Node.js предлагают мощную абстракцию для управления потоком данных в ваших приложениях. Они отлично подходят для обработки больших наборов данных, таких как чтение или запись из файлов и сетевых запросов, без ущерба для производительности.

Этот подход отличается от загрузки всего набора данных в память сразу. Потоки обрабатывают данные порциями, значительно сокращая использование памяти. Все потоки в Node.js наследуются от класса [`EventEmitter`](https://nodejs.org/api/events.html#class-eventemitter), что позволяет им испускать события на различных этапах обработки данных. Эти потоки могут быть читаемыми, записываемыми или и теми, и другими, обеспечивая гибкость для различных сценариев обработки данных.

### Событийно-ориентированная архитектура

Node.js процветает на событийно-ориентированной архитектуре, что делает его идеальным для ввода-вывода в реальном времени. Это означает потребление входных данных, как только они становятся доступны, и отправку выходных данных, как только приложение их генерирует. Потоки бесшовно интегрируются с этим подходом, обеспечивая непрерывную обработку данных.

Достигается это за счёт испускания событий на ключевых этапах. Среди этих событий — сигналы о полученных данных (событие [`data`](https://nodejs.org/api/stream.html#stream_event_data)) и о завершении потока (событие [`end`](https://nodejs.org/api/stream.html#event-end)). Разработчики могут прослушивать эти события и выполнять соответствующую пользовательскую логику. Такая событийно-ориентированная природа делает потоки крайне эффективными для обработки данных из внешних источников.

## Зачем использовать потоки?

Потоки дают три ключевых преимущества перед другими методами обработки данных:

  * **Эффективность по памяти** : потоки обрабатывают данные постепенно, потребляя и обрабатывая их порциями, а не загружая весь набор данных в память. Это большое преимущество при работе с большими наборами данных, поскольку значительно сокращает использование памяти и предотвращает проблемы производительности, связанные с памятью.
  * **Улучшенное время отклика** : потоки позволяют обрабатывать данные немедленно. Когда прибывает порция данных, её можно обработать, не дожидаясь получения всей полезной нагрузки или набора данных. Это снижает задержку и улучшает общую отзывчивость вашего приложения.
  * **Масштабируемость для обработки в реальном времени** : обрабатывая данные порциями, потоки Node.js способны эффективно справляться с большими объёмами данных при ограниченных ресурсах. Такая масштабируемость делает потоки идеальными для приложений, обрабатывающих большие объёмы данных в реальном времени.

Эти преимущества делают потоки мощным инструментом для создания высокопроизводительных, масштабируемых приложений Node.js, особенно при работе с большими наборами данных или обработке данных в реальном времени.

### Замечание о производительности

Если в вашем приложении все данные уже доступны в памяти, использование потоков может добавить излишние накладные расходы и сложность, а также замедлить ваше приложение.

## История потоков

Этот раздел — справка по истории потоков в Node.js. Если только вы не работаете с кодовой базой, написанной для версии Node.js до 0.11.5 (2013), вы редко будете сталкиваться со старыми версиями API потоков, но термины могут всё ещё использоваться.

### Streams 0

Первая версия потоков была выпущена одновременно с Node.js. Хотя класса Stream ещё не было, разные модули использовали эту концепцию и реализовывали функции `read`/`write`. Для управления потоком данных между потоками была доступна функция `util.pump()`.

### Streams 1 (Classic)

С выпуском Node v0.4.0 в 2011 году были представлены класс Stream, а также метод `pipe()`.

### Streams 2

В 2012 году, с выпуском Node v0.10.0, были представлены Streams 2. Это обновление принесло новые подклассы потоков, включая Readable, Writable, Duplex и Transform. Кроме того, было добавлено событие `readable`. Для сохранения обратной совместимости потоки можно было переключить в старый режим, добавив слушатель события `data` или вызвав методы `pause()` или `resume()`.

### Streams 3

В 2013 году вместе с Node v0.11.5 были выпущены Streams 3, чтобы решить проблему потока, имеющего одновременно обработчики событий `data` и `readable`. Это устранило необходимость выбирать между «текущим» и «старым» режимами. Streams 3 — текущая версия потоков в Node.js.

## Типы потоков

### Readable

[`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) — это класс, который мы используем для последовательного чтения источника данных. Типичные примеры потоков `Readable` в API Node.js — [`fs.ReadStream` ](https://nodejs.org/api/fs.html#class-fsreadstream) при чтении файлов, [`http.IncomingMessage` ](https://nodejs.org/api/http.html#class-httpincomingmessage) при чтении HTTP-запросов и [`process.stdin` ](https://nodejs.org/api/process.html#processstdin) при чтении из стандартного ввода.

#### Ключевые методы и события

Читаемый поток работает с несколькими основными методами и событиями, которые позволяют точно контролировать обработку данных:

  * **[`on('data')`](https://nodejs.org/api/stream.html#stream_event_data)** : это событие срабатывает всякий раз, когда из потока доступны данные. Оно очень быстрое, так как поток проталкивает данные настолько быстро, насколько может справиться, что делает его подходящим для сценариев с высокой пропускной способностью.
  * **[`on('end')`](https://nodejs.org/api/stream.html#event-end)** : испускается, когда из потока больше нечего читать. Означает завершение доставки данных. Это событие срабатывает только тогда, когда все данные из потока потреблены.
  * **[`on('readable')`](https://nodejs.org/api/stream.html#event-readable)** : это событие срабатывает, когда из потока доступны данные для чтения или когда достигнут конец потока. Оно позволяет при необходимости более контролируемо читать данные.
  * **[`on('close')`](https://nodejs.org/api/stream.html#event-close_1)** : это событие испускается, когда поток и его базовые ресурсы закрыты, и указывает, что больше событий испускаться не будет.
  * **[`on('error')`](https://nodejs.org/api/stream.html#event-error_1)** : это событие может быть испущено в любой момент, сигнализируя о том, что при обработке произошла ошибка. Обработчик этого события можно использовать, чтобы избежать неперехваченных исключений.

Демонстрацию использования этих событий можно увидеть в следующих разделах.

#### Простой читаемый поток

Вот пример простой реализации читаемого потока, который генерирует данные динамически:
```js
class MyStream extends Readable {
  #count = 0;
  _read(size) {
    this.push(':-)');
    if (++this.#count === 5) {
      this.push(null);
    }
  }
}

const stream = new MyStream();

stream.on('data', chunk => {
  console.log(chunk.toString());
});
```

В этом коде класс `MyStream` расширяет Readable и переопределяет метод [`_read()`](https://nodejs.org/api/stream.html#readable_readsize), чтобы проталкивать строку ":-)" во внутренний буфер. После пятикратного проталкивания строки он сигнализирует о конце потока, проталкивая `null`. Обработчик события [`on('data')`](https://nodejs.org/api/stream.html#stream_event_data) выводит каждую порцию в консоль по мере её получения.

#### Продвинутый контроль с помощью события readable

Для ещё более тонкого контроля над потоком данных можно использовать событие readable. Это событие сложнее, но обеспечивает лучшую производительность для некоторых приложений, позволяя явно контролировать, когда данные читаются из потока:
```js
const stream = new MyStream({
  highWaterMark: 1,
});

stream.on('readable', () => {
  console.count('>> readable event');
  let chunk;
  while ((chunk = stream.read()) !== null) {
    console.log(chunk.toString()); // Обрабатываем порцию данных
  }
});
stream.on('end', () => console.log('>> end event'));
```

Здесь событие readable используется для того, чтобы вручную извлекать данные из потока по мере необходимости. Цикл внутри обработчика события readable продолжает читать данные из буфера потока, пока он не вернёт `null`, что указывает на то, что буфер временно пуст или поток завершился. Установка `highWaterMark` в 1 сохраняет буфер небольшим, заставляя событие readable срабатывать чаще и обеспечивая более детальный контроль над потоком данных.

С предыдущим кодом вы получите вывод вроде этого
```bash
>> readable event: 1
:-):-)
:-)
:-)
:-)
>> readable event: 2
>> readable event: 3
>> readable event: 4
>> end event
```

Давайте попробуем это осмыслить. Когда мы прикрепляем событие `on('readable')`, оно делает первый вызов `read()`, потому что именно это может запустить испускание события `readable`. После испускания указанного события мы вызываем `read` на первой итерации цикла `while`. Вот почему мы получаем первые два смайлика в одной строке. После этого мы продолжаем вызывать `read`, пока не будет протолкнут `null`. Каждый вызов `read` программирует испускание нового события `readable`, но поскольку мы находимся в режиме «flow» (т. е. используем событие `readable`), испускание планируется на `nextTick`. Вот почему мы получаем их все в конце, когда синхронный код цикла завершён.

ПРИМЕЧАНИЕ: вы можете попробовать запустить код с `NODE_DEBUG=stream`, чтобы увидеть, что `emitReadable` срабатывает после каждого `push`.

Если мы хотим видеть события readable, вызываемые перед каждым смайликом, мы можем обернуть `push` в `setImmediate` или `process.nextTick` так:
```js
class MyStream extends Readable {
  #count = 0;
  _read(size) {
    setImmediate(() => {
      this.push(':-)');
      if (++this.#count === 5) {
        return this.push(null);
      }
    });
  }
}
```

И мы получим:
```bash
>> readable event: 1
:-)
>> readable event: 2
:-)
>> readable event: 3
:-)
>> readable event: 4
:-)
>> readable event: 5
:-)
>> readable event: 6
>> end event
```

### Writable

Потоки [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) полезны для создания файлов, загрузки данных или любой задачи, связанной с последовательным выводом данных. Если читаемые потоки служат источником данных, то записываемые потоки в Node.js выступают в роли назначения для ваших данных. Типичные примеры записываемых потоков в API Node.js — [`fs.WriteStream` ](https://nodejs.org/api/fs.html#class-fswritestream), [`process.stdout` ](https://nodejs.org/api/process.html#processstdout) и [`process.stderr` ](https://nodejs.org/api/process.html#processstderr).

#### Ключевые методы и события в записываемых потоках

  * **[`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback)** : этот метод используется для записи порции данных в поток. Он обрабатывает данные, буферизуя их до заданного предела (highWaterMark), и возвращает логическое значение, указывающее, можно ли немедленно записать ещё данные.
  * **[`.end()`](https://nodejs.org/api/stream.html#writableendchunk-encoding-callback)** : этот метод сигнализирует о конце процесса записи данных. Он сигнализирует потоку завершить операцию записи и, возможно, выполнить необходимую очистку.

#### Создание записываемого потока

Вот пример создания записываемого потока, который преобразует все входящие данные в верхний регистр перед записью их в стандартный вывод:
```js
const { once } = require('node:events');
const { Writable } = require('node:stream');

class MyStream extends Writable {
  constructor() {
    super({ highWaterMark: 10 /* 10 байт */ });
  }
  _write(data, encode, cb) {
    process.stdout.write(data.toString().toUpperCase() + '\n', cb);
  }
}

async function main() {
  const stream = new MyStream();

  for (let i = 0; i < 10; i++) {
    const waitDrain = !stream.write('hello');

    if (waitDrain) {
      console.log('>> wait drain');
      await once(stream, 'drain');
    }
  }

  stream.end('world');
}

// Вызываем асинхронную функцию
main().catch(console.error);
```

В этом коде `MyStream` — пользовательский поток [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) с ёмкостью буфера ([`highWaterMark`](https://nodejs.org/api/stream.html#stream_buffering)) 10 байт. Он переопределяет метод [`_write`](https://nodejs.org/api/stream.html#writable_writechunk-encoding-callback), чтобы преобразовывать данные в верхний регистр перед их выводом.

Цикл пытается десять раз записать hello в поток. Если буфер заполняется (`waitDrain` становится `true`), он ждёт события [`drain`](https://nodejs.org/api/stream.html#stream_event_drain) перед продолжением, гарантируя, что мы не переполним буфер потока.

Вывод будет таким:
```bash
HELLO
>> wait drain
HELLO
HELLO
>> wait drain
HELLO
HELLO
>> wait drain
HELLO
HELLO
>> wait drain
HELLO
HELLO
>> wait drain
HELLO
WORLD
```

### Duplex

Потоки [`Duplex`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) реализуют оба интерфейса — и читаемый, и записываемый.

#### Ключевые методы и события в дуплексных потоках

Дуплексные потоки реализуют все методы и события, описанные в разделах о читаемых и записываемых потоках.

Хороший пример дуплексного потока — класс `Socket` в модуле `net`:
```js
const net = require('node:net');

// Создаём TCP-сервер
const server = net.createServer(socket => {
  socket.write('Hello from server!\n');

  socket.on('data', data => {
    console.log(`Client says: ${data.toString()}`);
  });

  // Обрабатываем отключение клиента
  socket.on('end', () => {
    console.log('Client disconnected');
  });
});

// Запускаем сервер на порту 8080
server.listen(8080, () => {
  console.log('Server listening on port 8080');
});
```

Предыдущий код откроет TCP-сокет на порту 8080, отправит `Hello from server!` любому подключающемуся клиенту и будет логировать любые полученные данные.
```js
const net = require('node:net');

// Подключаемся к серверу на localhost:8080
const client = net.createConnection({ port: 8080 }, () => {
  client.write('Hello from client!\n');
});

client.on('data', data => {
  console.log(`Server says: ${data.toString()}`);
});

// Обрабатываем закрытие соединения сервером
client.on('end', () => {
  console.log('Disconnected from server');
});
```

Предыдущий код подключится к TCP-сокету, отправит сообщение `Hello from client` и будет логировать любые полученные данные.

### Transform

Потоки [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) — это дуплексные потоки, где выходные данные вычисляются на основе входных. Как следует из названия, они обычно используются между читаемым и записываемым потоком для преобразования данных по мере их прохождения.

#### Ключевые методы и события в преобразующих потоках

Помимо всех методов и событий дуплексных потоков, есть:

  * **[`_transform`](https://nodejs.org/api/stream.html#transform_transformchunk-encoding-callback)** : эта функция вызывается внутренне для управления потоком данных между читаемой и записываемой частями. Она НЕ ДОЛЖНА вызываться кодом приложения.

#### Создание преобразующего потока

Чтобы создать новый преобразующий поток, мы можем передать объект `options` в конструктор `Transform`, включив в него функцию `transform`, которая определяет, как выходные данные вычисляются из входных с помощью метода `push`.
```js
const { Transform } = require('node:stream');

const upper = new Transform({
  transform(data, enc, cb) {
    this.push(data.toString().toUpperCase());
    cb();
  },
});
```

Этот поток принимает любой ввод и выводит его в верхнем регистре.

## Как работать с потоками

При работе с потоками мы обычно хотим читать из источника и записывать в назначение, возможно, с необходимостью какого-то преобразования данных между ними. В следующих разделах рассматриваются различные способы сделать это.

### `.pipe()`

Метод [`.pipe()`](https://nodejs.org/docs/latest/api/stream.html#stream_readable_pipe_destination_options) соединяет один читаемый поток с записываемым (или преобразующим) потоком. Хотя это кажется простым способом достичь нашей цели, он перекладывает всю обработку ошибок на программиста, из-за чего сделать всё правильно затруднительно.

В следующем примере показан pipe, пытающийся вывести текущий файл в консоль в верхнем регистре.
```js
const fs = require('node:fs');
const { Transform } = require('node:stream');

let errorCount = 0;
const upper = new Transform({
  transform(data, enc, cb) {
    if (errorCount === 10) {
      return cb(new Error('BOOM!'));
    }
    errorCount++;
    this.push(data.toString().toUpperCase());
    cb();
  },
});

const readStream = fs.createReadStream(__filename, { highWaterMark: 1 });
const writeStream = process.stdout;

readStream.pipe(upper).pipe(writeStream);

readStream.on('close', () => {
  console.log('Readable stream closed');
});

upper.on('close', () => {
  console.log('Transform stream closed');
});

upper.on('error', err => {
  console.error('\nError in transform stream:', err.message);
});

writeStream.on('close', () => {
  console.log('Writable stream closed');
});
```

После записи 10 символов `upper` вернёт ошибку в колбэке, что приведёт к закрытию потока. Однако другие потоки не будут уведомлены, что приведёт к утечкам памяти. Вывод будет таким:
```bash
CONST FS =
Error in transform stream: BOOM!
Transform stream closed
```

M

### pipeline
```
pipeline(): void
```

Чтобы избежать подводных камней и низкоуровневой сложности метода `.pipe()`, в большинстве случаев рекомендуется использовать метод [`pipeline()`](https://nodejs.org/api/stream.html#stream_stream_pipeline_streams_callback). Этот метод — более безопасный и надёжный способ соединять потоки вместе, автоматически обрабатывая ошибки и очистку.

Следующий пример демонстрирует, как использование `pipeline()` предотвращает подводные камни предыдущего примера:
```js
const fs = require('node:fs');
const { Transform, pipeline } = require('node:stream');

let errorCount = 0;
const upper = new Transform({
  transform(data, enc, cb) {
    if (errorCount === 10) {
      return cb(new Error('BOOM!'));
    }
    errorCount++;
    this.push(data.toString().toUpperCase());
    cb();
  },
});

const readStream = fs.createReadStream(__filename, { highWaterMark: 1 });
const writeStream = process.stdout;

readStream.on('close', () => {
  console.log('Readable stream closed');
});

upper.on('close', () => {
  console.log('\nTransform stream closed');
});

writeStream.on('close', () => {
  console.log('Writable stream closed');
});

pipeline(readStream, upper, writeStream, err => {
  if (err) {
    return console.error('Pipeline error:', err.message);
  }
  console.log('Pipeline succeeded');
});
```

В этом случае все потоки будут закрыты со следующим выводом:
```bash
CONST FS =
Transform stream closed
Writable stream closed
Pipeline error: BOOM!
Readable stream closed
```

У метода [`pipeline()`](https://nodejs.org/api/stream.html#stream_stream_pipeline_streams_callback) также есть версия [`async pipeline()`](https://nodejs.org/api/stream.html#streampipelinesource-transforms-destination-options), которая не принимает колбэк, а вместо этого возвращает промис, который отклоняется, если pipeline завершается неудачей.

### Асинхронные итераторы

Асинхронные итераторы рекомендуются как стандартный способ взаимодействия с API потоков. По сравнению со всеми примитивами потоков как в Web, так и в Node.js, асинхронные итераторы проще понять и использовать, что способствует уменьшению количества ошибок и повышению сопровождаемости кода. В последних версиях Node.js асинхронные итераторы стали более элегантным и читаемым способом взаимодействия с потоками. Опираясь на основу из событий, асинхронные итераторы предоставляют более высокоуровневую абстракцию, которая упрощает потребление потоков.

В Node.js все читаемые потоки являются асинхронными итерируемыми объектами. Это означает, что вы можете использовать синтаксис `for await...of`, чтобы перебирать данные потока по мере их поступления, обрабатывая каждый фрагмент данных с эффективностью и простотой асинхронного кода.

#### Преимущества использования асинхронных итераторов с потоками

Использование асинхронных итераторов с потоками упрощает обработку асинхронных потоков данных несколькими способами:

  * **Улучшенная читаемость** : структура кода становится чище и читабельнее, особенно при работе с несколькими асинхронными источниками данных.
  * **Обработка ошибок** : асинхронные итераторы позволяют легко обрабатывать ошибки с помощью блоков try/catch, подобно обычным асинхронным функциям.
  * **Управление потоком** : они изначально управляют обратным давлением (backpressure), поскольку потребитель контролирует поток, ожидая следующий фрагмент данных, что обеспечивает более эффективное использование памяти и обработку.

Асинхронные итераторы предлагают более современный и зачастую более читаемый способ работы с читаемыми потоками, особенно при работе с асинхронными источниками данных или когда вы предпочитаете более последовательный, основанный на циклах подход к обработке данных.

Вот пример, демонстрирующий использование асинхронных итераторов с читаемым потоком:
```js
const fs = require('node:fs');
const { pipeline } = require('node:stream/promises');

async function main() {
  await pipeline(
    fs.createReadStream(__filename),
    async function* (source) {
      for await (let chunk of source) {
        yield chunk.toString().toUpperCase();
      }
    },
    process.stdout
  );
}

main().catch(console.error);
```

Этот код достигает того же результата, что и предыдущие примеры, без необходимости определять новый преобразующий поток. Ошибка из предыдущих примеров была убрана для краткости. Использована асинхронная версия pipeline, и её следует обернуть в блок `try...catch` для обработки возможных ошибок.

### Объектный режим

По умолчанию потоки могут работать со строками, [`Buffer`](https://nodejs.org/api/buffer.html), [`TypedArray`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) или [`DataView`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DataView). Если в поток протолкнуть произвольное значение, отличное от этих (например, объект), будет выброшено исключение `TypeError`. Однако с объектами можно работать, установив опцию `objectMode` в `true`. Это позволяет потоку работать с любым значением JavaScript, кроме `null`, которое используется для сигнала о конце потока. Это означает, что вы можете `push` и `read` любое значение в читаемом потоке и `write` любое значение в записываемом потоке.
```js
const { Readable } = require('node:stream');

const readable = Readable({
  objectMode: true,
  read() {
    this.push({ hello: 'world' });
    this.push(null);
  },
});
```

При работе в объектном режиме важно помнить, что опция `highWaterMark` относится к количеству объектов, а не байтов.

### Обратное давление (backpressure)

При использовании потоков важно убедиться, что производитель не переполняет потребителя. Для этого во всех потоках API Node.js используется механизм обратного давления (backpressure), и реализующие его несут ответственность за поддержание этого поведения.

В любом сценарии, когда буфер данных превысил [`highWaterMark`](https://nodejs.org/api/stream.html#stream_buffering) или очередь записи в данный момент занята, [`.write()`](https://nodejs.org/api/stream.html#stream_writable_write_chunk_encoding_callback) вернёт `false`.

Когда возвращается значение `false`, вступает в действие система обратного давления. Она приостановит отправку данных входящим потоком [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams) и будет ждать, пока потребитель снова не будет готов. Как только буфер данных опустеет, будет испущено событие [`'drain'`](https://nodejs.org/api/stream.html#stream_event_drain), чтобы возобновить входящий поток данных.

Для более глубокого понимания обратного давления ознакомьтесь с [`руководством по обратному давлению`](/learn/modules/backpressuring-in-streams).

## Потоки против Web Streams

Концепция потоков не является исключительной для Node.js. На самом деле у Node.js есть другая реализация концепции потоков под названием [`Web Streams`](https://nodejs.org/api/webstreams.html), которая реализует [`стандарт WHATWG Streams`](https://streams.spec.whatwg.org/). Хотя концепции, лежащие в их основе, схожи, важно понимать, что у них разные API и они не совместимы напрямую.

[`Web Streams`](https://nodejs.org/api/webstreams.html) реализуют классы [`ReadableStream`](https://nodejs.org/api/webstreams.html#class-readablestream), [`WritableStream`](https://nodejs.org/api/webstreams.html#class-writablestream) и [`TransformStream`](https://nodejs.org/api/webstreams.html#class-transformstream), которые гомологичны потокам Node.js [`Readable`](https://nodejs.org/api/stream.html#stream_readable_streams), [`Writable`](https://nodejs.org/api/stream.html#stream_writable_streams) и [`Transform`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams).

### Взаимодействие потоков и Web Streams

Node.js предоставляет вспомогательные функции для преобразования между Web Streams и потоками Node.js в обоих направлениях. Эти функции реализованы как методы `toWeb` и `fromWeb` в каждом классе потока.

Следующий пример с классом [`Duplex`](https://nodejs.org/api/stream.html#stream_duplex_and_transform_streams) демонстрирует, как работать и с читаемыми, и с записываемыми потоками, преобразованными в Web Streams:
```js
const { Duplex } = require('node:stream');

const duplex = Duplex({
  read() {
    this.push('world');
    this.push(null);
  },
  write(chunk, encoding, callback) {
    console.log('writable', chunk);
    callback();
  },
});

const { readable, writable } = Duplex.toWeb(duplex);
writable.getWriter().write('hello');

readable
  .getReader()
  .read()
  .then(result => {
    console.log('readable', result.value);
  });
```

Вспомогательные функции полезны, если вам нужно вернуть Web Stream из модуля Node.js или наоборот. Для обычного потребления потоков асинхронные итераторы обеспечивают бесшовное взаимодействие как с Node.js, так и с Web Streams.
```js
const { pipeline } = require('node:stream/promises');

async function main() {
  const { body } = await fetch('https://nodejs.org/api/stream.html');

  await pipeline(
    body,
    new TextDecoderStream(),
    async function* (source) {
      for await (const chunk of source) {
        yield chunk.toString().toUpperCase();
      }
    },
    process.stdout
  );
}

main().catch(console.error);
```

Учтите, что тело ответа fetch — это `ReadableStream<Uint8Array>`, и поэтому для работы с порциями данных как со строками нужен [`TextDecoderStream`](https://developer.mozilla.org/en-US/docs/Web/API/TextDecoderStream).

Эта работа основана на материале, опубликованном [Matteo Collina](https://github.com/mcollina) в [блоге Platformatic](https://blog.platformatic.dev/a-guide-to-reading-and-writing-nodejs-streams).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/modules/how-to-use-streams. Оригинал распространяется по лицензии MIT._

# Асинхронное управление потоком выполнения

> Материал этой статьи во многом навеян [книгой Mixu про Node.js](http://book.mixu.net/node/ch7.html).

В своей основе JavaScript спроектирован так, чтобы быть неблокирующим в «главном» потоке выполнения — том самом, где отрисовываются представления. Легко представить, насколько это важно в браузере. Когда главный поток блокируется, происходит печально известное «подвисание», которого так боятся конечные пользователи, и никакие другие события не могут быть обработаны, что приводит, например, к потере получаемых данных.

Это создаёт ряд уникальных ограничений, которые может снять только функциональный стиль программирования. Именно здесь на сцену выходят колбэки.

Однако с колбэками становится непросто работать в более сложных процедурах. Часто это выливается в «ад колбэков» (callback hell), когда множество вложенных функций с колбэками делают код труднее для чтения, отладки, организации и т. д.
```js
async1(function (input, result1) {
  async2(function (result2) {
    async3(function (result3) {
      async4(function (result4) {
        async5(function (output) {
          // делаем что-то с output
        });
      });
    });
  });
});
```

Разумеется, в реальной жизни здесь наверняка были бы дополнительные строки кода для обработки `result1`, `result2` и т. д., так что из-за длины и сложности этой проблемы код обычно выглядит гораздо более запутанным, чем в примере выше.

**Вот где _функции_ приносят огромную пользу. Более сложные операции складываются из множества функций:**

  1. инициатор / ввод (initiator style / input)
  2. промежуточное звено (middleware)
  3. терминатор (terminator)

**«Инициатор / ввод» — это первая функция в последовательности. Эта функция принимает исходный ввод для операции, если он есть. Операция представляет собой исполняемую серию функций, а исходный ввод в основном бывает в виде:**

  1. переменных в глобальном окружении
  2. прямого вызова с аргументами или без них
  3. значений, полученных из файловой системы или сетевых запросов

Сетевые запросы могут быть входящими запросами, инициированными сторонней сетью, другим приложением в той же сети или самим приложением в той же или сторонней сети.

Функция-промежуточное звено возвращает другую функцию, а функция-терминатор вызывает колбэк. Далее показан поток управления для сетевых запросов или запросов к файловой системе. Здесь задержка (латентность) равна 0, потому что все эти значения доступны в памяти.
```js
function final(someInput, callback) {
  callback(`${someInput} and terminated by executing callback `);
}

function middleware(someInput, callback) {
  return final(`${someInput} touched by middleware `, callback);
}

function initiate() {
  const someInput = 'hello this is a function ';
  middleware(someInput, function (result) {
    console.log(result);
    // требует колбэк, чтобы `вернуть` результат
  });
}

initiate();
```

## Управление состоянием

Функции могут зависеть или не зависеть от состояния. Зависимость от состояния возникает, когда ввод или иная переменная функции опирается на внешнюю функцию.

**Таким образом, есть две основные стратегии управления состоянием:**

  1. передача переменных непосредственно в функцию и
  2. получение значения переменной из кэша, сессии, файла, базы данных, сети или иного внешнего источника.

Обратите внимание: я не упомянул глобальные переменные. Управление состоянием через глобальные переменные часто оказывается неаккуратным антипаттерном, из-за которого сложно или невозможно гарантировать состояние. Глобальных переменных в сложных программах по возможности следует избегать.

## Управление потоком выполнения (control flow)

Если объект доступен в памяти, итерация по нему возможна, и потока управления это не изменит:
```js
function getSong() {
  let _song = '';
  let i = 100;
  for (i; i > 0; i -= 1) {
    _song += `${i} beers on the wall, you take one down and pass it around, ${
      i - 1
    } bottles of beer on the wall\n`;
    if (i === 1) {
      _song += "Hey let's get some more beer";
    }
  }

  return _song;
}

function singSong(_song) {
  if (!_song) {
    throw new Error("song is '' empty, FEED ME A SONG!");
  }

  console.log(_song);
}

const song = getSong();
// это сработает
singSong(song);
```

Однако если данные существуют за пределами памяти, итерация больше не работает:
```js
function getSong() {
  let _song = '';
  let i = 100;
  for (i; i > 0; i -= 1) {
    setTimeout(function () {
      _song += `${i} beers on the wall, you take one down and pass it around, ${
        i - 1
      } bottles of beer on the wall\n`;
      if (i === 1) {
        _song += "Hey let's get some more beer";
      }
    }, 0);
  }

  return _song;
}

function singSong(_song) {
  if (!_song) {
    throw new Error("song is '' empty, FEED ME A SONG!");
  }

  console.log(_song);
}

const song = getSong('beer');
// это не сработает
singSong(song);
// Uncaught Error: song is '' empty, FEED ME A SONG!
```

Почему так вышло? `setTimeout` предписывает CPU сохранить инструкции в другом месте на шине и указывает, что данные запланированы к получению позже. Проходят тысячи циклов CPU, прежде чем функция снова сработает на отметке в 0 миллисекунд, CPU извлекает инструкции с шины и выполняет их. Единственная проблема в том, что song (`''`) был возвращён тысячами циклов ранее.

Та же ситуация возникает при работе с файловыми системами и сетевыми запросами. Главный поток выполнения просто нельзя блокировать на неопределённый период времени — поэтому мы используем колбэки, чтобы контролируемым образом планировать выполнение кода во времени.

Почти все свои операции вы сможете реализовать с помощью следующих трёх шаблонов:

  1. **Последовательно (in series):** функции выполняются в строгом последовательном порядке, этот вариант больше всего похож на циклы `for`.

```js
// операции определены в другом месте и готовы к выполнению
const operations = [
  { func: function1, args: args1 },
  { func: function2, args: args2 },
  { func: function3, args: args3 },
];

function executeFunctionWithArgs(operation, callback) {
  // выполняет функцию
  const { args, func } = operation;
  func(args, callback);
}

function serialProcedure(operation) {
  if (!operation) {
    process.exit(0); // завершено
  }

  executeFunctionWithArgs(operation, function (result) {
    // продолжаем ПОСЛЕ колбэка
    serialProcedure(operations.shift());
  });
}

serialProcedure(operations.shift());
```

  2. **Ограниченно последовательно (limited in series):** функции выполняются в строгом последовательном порядке, но с ограничением на количество выполнений. Полезно, когда нужно обработать большой список, но с ограничением на число успешно обработанных элементов.

```js
let successCount = 0;

function final() {
  console.log(`dispatched ${successCount} emails`);
  console.log('finished');
}

function dispatch(recipient, callback) {
  // `sendMail` — гипотетический SMTP-клиент
  sendMail(
    {
      subject: 'Dinner tonight',
      message: 'We have lots of cabbage on the plate. You coming?',
      smtp: recipient.email,
    },
    callback
  );
}

function sendOneMillionEmailsOnly() {
  getListOfTenMillionGreatEmails(function (err, bigList) {
    if (err) {
      throw err;
    }

    function serial(recipient) {
      if (!recipient || successCount >= 1000000) {
        return final();
      }

      dispatch(recipient, function (_err) {
        if (!_err) {
          successCount += 1;
        }

        serial(bigList.pop());
      });
    }

    serial(bigList.pop());
  });
}

sendOneMillionEmailsOnly();
```

  3. **Полностью параллельно (full parallel):** когда порядок не имеет значения, например при рассылке письма списку из 1 000 000 получателей.

```js
let count = 0;
let success = 0;
const failed = [];
const recipients = [
  { name: 'Bart', email: 'bart@tld' },
  { name: 'Marge', email: 'marge@tld' },
  { name: 'Homer', email: 'homer@tld' },
  { name: 'Lisa', email: 'lisa@tld' },
  { name: 'Maggie', email: 'maggie@tld' },
];

function dispatch(recipient, callback) {
  // `sendMail` — гипотетический SMTP-клиент
  sendMail(
    {
      subject: 'Dinner tonight',
      message: 'We have lots of cabbage on the plate. You coming?',
      smtp: recipient.email,
    },
    callback
  );
}

function final(result) {
  console.log(`Result: ${result.count} attempts \
      & ${result.success} succeeded emails`);
  if (result.failed.length) {
    console.log(`Failed to send to: \
        \n${result.failed.join('\n')}\n`);
  }
}

recipients.forEach(function (recipient) {
  dispatch(recipient, function (err) {
    if (!err) {
      success += 1;
    } else {
      failed.push(recipient.name);
    }
    count += 1;

    if (count === recipients.length) {
      final({
        count,
        success,
        failed,
      });
    }
  });
});
```

У каждого из них есть свои сценарии применения, преимущества и подводные камни, с которыми вы можете поэкспериментировать и почитать подробнее. Самое главное — не забывайте разбивать свои операции на модули и использовать колбэки! Если сомневаетесь — считайте всё промежуточным звеном (middleware)!

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/asynchronous-work/asynchronous-flow-control>. Оригинал распространяется по лицензии MIT._

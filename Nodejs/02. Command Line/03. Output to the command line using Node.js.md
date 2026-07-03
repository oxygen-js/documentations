# Вывод в командную строку с помощью Node.js

### Базовый вывод с помощью модуля console

Node.js предоставляет [модуль `console`](https://nodejs.org/docs/latest-v22.x/api/console.html), в котором есть множество очень удобных способов взаимодействия с командной строкой.

По сути, это тот же объект `console`, что и в браузере.

Самый базовый и самый часто используемый метод — `console.log()`, который выводит переданную ему строку в консоль.

Если передать объект, он будет отрендерен как строка.

В `console.log` можно передать несколько переменных, например:
```js
const x = 'x';
const y = 'y';

console.log(x, y);
```

и Node.js выведет обе.

Мы также можем формировать красивые фразы, передавая переменные и спецификатор формата.

Например:
```
console.log('My %s has %d ears', 'cat', 2);
```

  * `%s` форматирует переменную как строку
  * `%d` форматирует переменную как число
  * `%i` форматирует переменную только как её целую часть
  * `%o` форматирует переменную как объект

Пример:
```
console.log('%o', Number);
```

### Очистка консоли

`console.clear()` очищает консоль (поведение может зависеть от используемой консоли)

### Подсчёт элементов

`console.count()` — удобный метод.

Возьмём такой код:
```js
const x = 1;
const y = 2;
const z = 3;

console.count(
  'The value of x is ' + x + ' and has been checked .. how many times?'
);

console.count(
  'The value of x is ' + x + ' and has been checked .. how many times?'
);

console.count(
  'The value of y is ' + y + ' and has been checked .. how many times?'
);
```

Происходит следующее: `console.count()` подсчитывает, сколько раз была выведена строка, и печатает счётчик рядом с ней:

Можно просто считать яблоки и апельсины:
```js
const oranges = ['orange', 'orange'];
const apples = ['just one apple'];

oranges.forEach(fruit => {
  console.count(fruit);
});
apples.forEach(fruit => {
  console.count(fruit);
});
```

### Сброс счётчика

Метод console.countReset() сбрасывает счётчик, используемый в console.count().

Чтобы продемонстрировать это, воспользуемся примером с яблоками и апельсинами.
```js
const oranges = ['orange', 'orange'];
const apples = ['just one apple'];

oranges.forEach(fruit => {
  console.count(fruit);
});
apples.forEach(fruit => {
  console.count(fruit);
});

console.countReset('orange');

oranges.forEach(fruit => {
  console.count(fruit);
});
```

Обратите внимание, как вызов `console.countReset('orange')` сбрасывает счётчик значения до нуля.

### Вывод трассировки стека

Иногда бывает полезно вывести трассировку стека вызовов функции — например, чтобы ответить на вопрос _как вы вообще добрались до этой части кода?_

Сделать это можно с помощью `console.trace()`:
```js
const function2 = () => console.trace();
const function1 = () => function2();
function1();
```

Это выведет трассировку стека. Вот что выводится, если попробовать это в Node.js REPL:
```bash
Trace
    at function2 (repl:1:33)
    at function1 (repl:1:25)
    at repl:1:1
    at ContextifyScript.Script.runInThisContext (vm.js:44:33)
    at REPLServer.defaultEval (repl.js:239:29)
    at bound (domain.js:301:14)
    at REPLServer.runBound [as eval] (domain.js:314:12)
    at REPLServer.onLine (repl.js:440:10)
    at emitOne (events.js:120:20)
    at REPLServer.emit (events.js:210:7)
```

### Подсчёт затраченного времени

Вы можете легко посчитать, сколько времени занимает выполнение функции, с помощью `time()` и `timeEnd()`
```js
const doSomething = () => console.log('test');
const measureDoingSomething = () => {
  console.time('doSomething()');
  // сделать что-нибудь и замерить, сколько времени это займёт
  doSomething();
  console.timeEnd('doSomething()');
};
measureDoingSomething();
```

### stdout и stderr

Как мы видели, console.log отлично подходит для вывода сообщений в консоль. Это то, что называется стандартным выводом, или `stdout`.

`console.error` выводит в поток `stderr`.

Он появится в консоли, но может обрабатываться отдельно от обычного вывода.

### Раскрашивание вывода

> **ПРИМЕЧАНИЕ** Эта часть материала написана с расчётом на версию 22.11, в которой `styleText` помечен как «в активной разработке».

Во многих случаях у вас будет соблазн вставить определённый текст, чтобы получить красивый вывод в терминале.

В модуле `node:util` есть функция `styleText`. Давайте разберёмся, как ей пользоваться.

Прежде всего нужно импортировать функцию `styleText` из модуля `node:util`:
```
import { styleText } from 'node:util';
```

Затем её можно использовать для стилизации текста:
```js
console.log(
  styleText(['red'], 'This is red text ') +
    styleText(['green', 'bold'], 'and this is green bold text ') +
    'this is normal text'
);
```

Первый аргумент — это массив стилей, а второй аргумент — текст, который вы хотите стилизовать. Рекомендуем прочитать [документацию](https://nodejs.org/docs/latest-v22.x/api/util.html#utilstyletextformat-text-options)

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/command-line/output-to-the-command-line-using-nodejs. Оригинал распространяется по лицензии MIT._

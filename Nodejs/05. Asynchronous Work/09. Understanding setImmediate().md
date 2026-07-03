# Разбираемся с setImmediate()

Когда вам нужно выполнить некоторый фрагмент кода асинхронно, но как можно скорее, один из вариантов — воспользоваться функцией `setImmediate()`, которую предоставляет Node.js:
```js
setImmediate(() => {
  // выполнить что-нибудь
});
```

Любая функция, переданная в качестве аргумента setImmediate(), является колбэком, который выполняется на следующей итерации цикла событий.

Чем `setImmediate()` отличается от `setTimeout(() => {}, 0)` (передача таймаута в 0 мс), а также от `process.nextTick()` и `Promise.then()`?

Функция, переданная в `process.nextTick()`, будет выполнена на текущей итерации цикла событий, после того как завершится текущая операция. Это значит, что она всегда выполнится раньше, чем `setTimeout` и `setImmediate`.

Колбэк `setTimeout()` с задержкой 0 мс очень похож на `setImmediate()`. Порядок выполнения будет зависеть от разных факторов, но оба будут выполнены на следующей итерации цикла событий.

Колбэк `process.nextTick` добавляется в `process.nextTick queue`. Колбэк `Promise.then()` добавляется в `promises microtask queue`. Колбэк `setTimeout`, `setImmediate` добавляется в `macrotask queue`.

Цикл событий сначала выполняет задачи из `process.nextTick queue`, затем выполняет `promises microtask queue`, а затем выполняет `macrotask queue`.

Вот пример, показывающий порядок между `setImmediate()`, `process.nextTick()` и `Promise.then()`:
```js
const baz = () => console.log('baz');
const foo = () => console.log('foo');
const zoo = () => console.log('zoo');

const start = () => {
  console.log('start');
  setImmediate(baz);
  new Promise((resolve, reject) => {
    resolve('bar');
  }).then(resolve => {
    console.log(resolve);
    process.nextTick(zoo);
  });
  process.nextTick(foo);
};

start();

// start foo bar zoo baz
```

Этот код сначала вызовет `start()`, затем вызовет `foo()` в `process.nextTick queue`. После этого он обработает `promises microtask queue`, который выведет `bar` и одновременно добавит `zoo()` в `process.nextTick queue`. Затем он вызовет `zoo()`, который только что был добавлен. В конце вызывается `baz()` из `macrotask queue`.

Вышеописанный принцип справедлив для случаев CommonJS, но имейте в виду, что в ES Modules, например в файлах `mjs`, порядок выполнения будет другим:
```
// start bar foo zoo baz
```

Это связано с тем, что загружаемый ES Module обёрнут как асинхронная операция, и, таким образом, весь скрипт фактически уже находится в `promises microtask queue`. Поэтому, когда промис немедленно разрешается, его колбэк добавляется в очередь `microtask`. Node.js попытается очистить очередь, прежде чем перейти к любой другой очереди, и поэтому вы увидите, что сначала выводится `bar`.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/asynchronous-work/understanding-setimmediate. Оригинал распространяется по лицензии MIT._

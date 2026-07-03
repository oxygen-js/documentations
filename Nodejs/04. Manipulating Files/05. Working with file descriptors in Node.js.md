# Работа с файловыми дескрипторами в Node.js

Прежде чем вы сможете взаимодействовать с файлом, который находится в вашей файловой системе, вам нужно получить файловый дескриптор.

Файловый дескриптор — это ссылка на открытый файл, число (fd), возвращаемое при открытии файла с помощью метода `open()`, предоставляемого модулем `fs`. Это число (`fd`) уникально идентифицирует открытый файл в операционной системе:
```js
const fs = require('node:fs');

fs.open('/Users/joe/test.txt', 'r', (err, fd) => {
  // fd — это наш файловый дескриптор
});
```

Обратите внимание на `r`, который мы использовали в качестве второго параметра при вызове `fs.open()`.

Этот флаг означает, что мы открываем файл для чтения.

**Другие флаги, которые вы будете часто использовать:**

Флаг| Описание| Файл создаётся, если он не существует| r+| Этот флаг открывает файл для чтения и записи| ❌| w+| Этот флаг открывает файл для чтения и записи, а также устанавливает поток в начало файла| ✅| a| Этот флаг открывает файл для записи, а также устанавливает поток в конец файла| ✅| a+| Этот флаг открывает файл для чтения и записи, а также устанавливает поток в конец файла| ✅  
---|---|---  
  
Вы также можете открыть файл с помощью метода `fs.openSync`, который возвращает файловый дескриптор напрямую, вместо того чтобы передавать его в колбэк:
```js
const fs = require('node:fs');

try {
  const fd = fs.openSync('/Users/joe/test.txt', 'r');
} catch (err) {
  console.error(err);
}
```

Как только вы получили файловый дескриптор — любым выбранным способом, — вы можете выполнять все операции, которые его требуют, например вызывать `fs.close()` и множество других операций, взаимодействующих с файловой системой.

Вы также можете открыть файл с помощью метода `fsPromises.open` на основе промисов, предоставляемого модулем `fs/promises`.

Модуль `fs/promises` доступен только начиная с Node.js v14. До v14, но после v10, вы можете использовать вместо него `require('fs').promises`. До v10, но после v8, вы можете использовать `util.promisify`, чтобы преобразовать методы `fs` в методы на основе промисов.
```js
const fs = require('node:fs/promises');
// Или const fs = require('fs').promises до v14.
async function example() {
  let filehandle;
  try {
    filehandle = await fs.open('/Users/joe/test.txt', 'r');
    console.log(filehandle.fd);
    console.log(await filehandle.readFile({ encoding: 'utf8' }));
  } finally {
    if (filehandle) {
      await filehandle.close();
    }
  }
}
example();
```

Вот пример использования `util.promisify`:
```js
const fs = require('node:fs');
const util = require('node:util');

async function example() {
  const open = util.promisify(fs.open);
  const fd = await open('/Users/joe/test.txt', 'r');
}
example();
```

Чтобы узнать больше о модуле `fs/promises`, обратитесь к [API fs/promises](https://nodejs.org/api/fs.html#promise-example).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/manipulating-files/working-with-file-descriptors-in-nodejs. Оригинал распространяется по лицензии MIT._

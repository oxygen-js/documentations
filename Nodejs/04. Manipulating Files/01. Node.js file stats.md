# Информация о файлах в Node.js

У каждого файла есть набор сведений, которые мы можем изучить средствами Node.js. В частности, с помощью метода `stat()`, предоставляемого [модулем `fs`](https://nodejs.org/api/fs.html).

Вы вызываете его, передавая путь к файлу, и как только Node.js получит сведения о файле, он вызовет переданную вами функцию обратного вызова с 2 параметрами: сообщением об ошибке и информацией о файле:
```js
const fs = require('node:fs');

fs.stat('/Users/joe/test.txt', (err, stats) => {
  if (err) {
    console.error(err);
  }
  // у нас есть доступ к информации о файле в `stats`
});
```

Node.js также предоставляет синхронный метод, который блокирует поток выполнения, пока информация о файле не будет готова:
```js
const fs = require('node:fs');

try {
  const stats = fs.statSync('/Users/joe/test.txt');
} catch (err) {
  console.error(err);
}
```

Информация о файле содержится в переменной stats. Какую информацию мы можем извлечь с помощью stats?

**Очень много, в том числе:**

  * является ли объект каталогом или файлом — с помощью `stats.isFile()` и `stats.isDirectory()`
  * является ли файл символической ссылкой — с помощью `stats.isSymbolicLink()`
  * размер файла в байтах — с помощью `stats.size`.

Есть и другие продвинутые методы, но основная часть того, что вы будете использовать в повседневном программировании, — это перечисленное выше.
```js
const fs = require('node:fs');

fs.stat('/Users/joe/test.txt', (err, stats) => {
  if (err) {
    console.error(err);
    return;
  }

  stats.isFile(); // true
  stats.isDirectory(); // false
  stats.isSymbolicLink(); // false
  console.log(stats.size); // 1024000 //= 1MB
});
```

При желании вы также можете использовать метод `fsPromises.stat()` на основе промисов, предоставляемый модулем `fs/promises`:
```js
const fs = require('node:fs/promises');

async function example() {
  try {
    const stats = await fs.stat('/Users/joe/test.txt');
    stats.isFile(); // true
    stats.isDirectory(); // false
    stats.isSymbolicLink(); // false
    console.log(stats.size); // 1024000 //= 1MB
  } catch (err) {
    console.log(err);
  }
}
example();
```

Подробнее о модуле `fs` можно прочитать в [официальной документации](https://nodejs.org/api/fs.html).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/manipulating-files/nodejs-file-stats. Оригинал распространяется по лицензии MIT._

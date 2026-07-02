# Работа с папками в Node.js

Основной модуль Node.js `fs` предоставляет множество удобных методов, которые вы можете использовать для работы с папками.

## Проверка существования папки

Используйте `fs.access()` (и его аналог на основе промисов `fsPromises.access()`), чтобы проверить, существует ли папка и может ли Node.js получить к ней доступ со своими правами.

## Создание новой папки

Используйте `fs.mkdir()`, `fs.mkdirSync()` или `fsPromises.mkdir()`, чтобы создать новую папку.
```js
const fs = require('node:fs');

const folderName = '/Users/joe/test';

try {
  if (!fs.existsSync(folderName)) {
    fs.mkdirSync(folderName);
  }
} catch (err) {
  console.error(err);
}
```

## Чтение содержимого каталога

Используйте `fs.readdir()`, `fs.readdirSync()` или `fsPromises.readdir()`, чтобы прочитать содержимое каталога.

Этот фрагмент кода читает содержимое папки — как файлы, так и подпапки — и возвращает их относительные пути:
```js
const fs = require('node:fs');

const folderPath = '/Users/joe';

fs.readdirSync(folderPath);
```

Вы можете получить полный путь:
```js
fs.readdirSync(folderPath).map(fileName => {
  return path.join(folderPath, fileName);
});
```

Вы также можете отфильтровать результаты, чтобы вернуть только файлы и исключить папки:
```js
const fs = require('node:fs');

const isFile = fileName => {
  return fs.lstatSync(fileName).isFile();
};

fs.readdirSync(folderPath)
  .map(fileName => {
    return path.join(folderPath, fileName);
  })
  .filter(isFile);
```

## Переименование папки

Используйте `fs.rename()`, `fs.renameSync()` или `fsPromises.rename()`, чтобы переименовать папку. Первый параметр — текущий путь, второй — новый путь:
```js
const fs = require('node:fs');

fs.rename('/Users/joe', '/Users/roger', err => {
  if (err) {
    console.error(err);
  }
  // готово
});
```

`fs.renameSync()` — синхронная версия:
```js
const fs = require('node:fs');

try {
  fs.renameSync('/Users/joe', '/Users/roger');
} catch (err) {
  console.error(err);
}
```

`fsPromises.rename()` — версия на основе промисов:
```js
const fs = require('node:fs/promises');

async function example() {
  try {
    await fs.rename('/Users/joe', '/Users/roger');
  } catch (err) {
    console.log(err);
  }
}
example();
```

## Удаление папки

Используйте `fs.rmdir()`, `fs.rmdirSync()` или `fsPromises.rmdir()`, чтобы удалить папку.
```js
const fs = require('node:fs');

fs.rmdir(dir, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

Чтобы удалить папку с содержимым, используйте `fs.rm()` с опцией `{ recursive: true }` для рекурсивного удаления содержимого.

`{ recursive: true, force: true }` делает так, что исключения будут игнорироваться, если папка не существует.
```js
const fs = require('node:fs');

fs.rm(dir, { recursive: true, force: true }, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/manipulating-files/working-with-folders-in-nodejs. Оригинал распространяется по лицензии MIT._

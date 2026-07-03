# Запись файлов в Node.js

## Запись файла

Самый простой способ записать данные в файлы в Node.js — использовать API `fs.writeFile()`.
```js
const fs = require('node:fs');

const content = 'Some content!';

fs.writeFile('/Users/joe/test.txt', content, err => {
  if (err) {
    console.error(err);
  } else {
    // файл успешно записан
  }
});
```

### Синхронная запись файла

Как вариант, можно использовать синхронную версию `fs.writeFileSync()`:
```js
const fs = require('node:fs');

const content = 'Some content!';

try {
  fs.writeFileSync('/Users/joe/test.txt', content);
  // файл успешно записан
} catch (err) {
  console.error(err);
}
```

Также можно использовать метод `fsPromises.writeFile()` на основе промисов, предоставляемый модулем `fs/promises`:
```js
const fs = require('node:fs/promises');

async function example() {
  try {
    const content = 'Some content!';
    await fs.writeFile('/Users/joe/test.txt', content);
  } catch (err) {
    console.log(err);
  }
}

example();
```

По умолчанию этот API **заменит содержимое файла**, если он уже существует.

**Поведение по умолчанию можно изменить, указав флаг:**
```
fs.writeFile('/Users/joe/test.txt', content, { flag: 'a+' }, err => {});
```

#### Флаги, которые вы, скорее всего, будете использовать

Флаг| Описание| Файл создаётся, если не существует| `r+`| Этот флаг открывает файл для **чтения** и **записи**|  ❌| `w+`| Этот флаг открывает файл для **чтения** и **записи**, а также устанавливает поток в **начало** файла| ✅| `a`| Этот флаг открывает файл для **записи**, а также устанавливает поток в **конец** файла| ✅| `a+`| Этот флаг открывает файл для **чтения** и **записи**, а также устанавливает поток в **конец** файла| ✅  
---|---|---  
  
  * Больше информации о флагах можно найти в [документации fs](https://nodejs.org/api/fs.html#file-system-flags).

## Добавление содержимого в файл

Добавление в файлы удобно, когда вы не хотите перезаписывать файл новым содержимым, а хотите дополнить его.

### Примеры

Удобный метод для добавления содержимого в конец файла — `fs.appendFile()` (и его аналог `fs.appendFileSync()`):
```js
const fs = require('node:fs');

const content = 'Some content!';

fs.appendFile('file.log', content, err => {
  if (err) {
    console.error(err);
  } else {
    // готово!
  }
});
```

#### Пример с промисами

Вот пример использования `fsPromises.appendFile()`:
```js
const fs = require('node:fs/promises');

async function example() {
  try {
    const content = 'Some content!';
    await fs.appendFile('/Users/joe/test.txt', content);
  } catch (err) {
    console.log(err);
  }
}

example();
```

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/manipulating-files/writing-files-with-nodejs. Оригинал распространяется по лицензии MIT._

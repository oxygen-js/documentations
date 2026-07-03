# Чтение файлов с помощью Node.js

Самый простой способ прочитать файл в Node.js — использовать метод `fs.readFile()`, передав ему путь к файлу, кодировку и функцию-колбэк, которая будет вызвана с данными файла (и ошибкой):
```js
const fs = require('node:fs');

fs.readFile('/Users/joe/test.txt', 'utf8', (err, data) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(data);
});
```

В качестве альтернативы вы можете использовать синхронную версию `fs.readFileSync()`:
```js
const fs = require('node:fs');

try {
  const data = fs.readFileSync('/Users/joe/test.txt', 'utf8');
  console.log(data);
} catch (err) {
  console.error(err);
}
```

Вы также можете использовать основанный на промисах метод `fsPromises.readFile()`, который предоставляет модуль `fs/promises`:
```js
const fs = require('node:fs/promises');

async function example() {
  try {
    const data = await fs.readFile('/Users/joe/test.txt', { encoding: 'utf8' });
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
example();
```

Все три метода — `fs.readFile()`, `fs.readFileSync()` и `fsPromises.readFile()` — считывают всё содержимое файла в память перед тем, как вернуть данные.

Это означает, что большие файлы будут оказывать серьёзное влияние на потребление памяти и скорость выполнения программы.

В этом случае лучшим вариантом будет чтение содержимого файла с помощью потоков, поскольку это более эффективно с точки зрения использования памяти.
```js
import fs from 'fs';
import { pipeline } from 'node:stream/promises';
import path from 'path';

const fileUrl = 'https://www.gutenberg.org/files/2701/2701-0.txt';
const outputFilePath = path.join(process.cwd(), 'moby.md');

async function downloadFile(url, outputPath) {
  const response = await fetch(url);

  if (!response.ok || !response.body) {
    // потребление тела ответа обязательно: https://undici.nodejs.org/#/?id=garbage-collection
    await response.body?.cancel();
    throw new Error(`Failed to fetch ${url}. Status: ${response.status}`);
  }

  const fileStream = fs.createWriteStream(outputPath);
  console.log(`Downloading file from ${url} to ${outputPath}`);

  await pipeline(response.body, fileStream);
  console.log('File downloaded successfully');
}

async function readFile(filePath) {
  const readStream = fs.createReadStream(filePath, { encoding: 'utf8' });

  try {
    for await (const chunk of readStream) {
      console.log('--- File chunk start ---');
      console.log(chunk);
      console.log('--- File chunk end ---');
    }
    console.log('Finished reading the file.');
  } catch (error) {
    console.error(`Error reading file: ${error.message}`);
  }
}

try {
  await downloadFile(fileUrl, outputFilePath);
  await readFile(outputFilePath);
} catch (error) {
  console.error(`Error: ${error.message}`);
}
```

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/manipulating-files/reading-files-with-nodejs. Оригинал распространяется по лицензии MIT._

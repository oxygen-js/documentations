# Использование Fetch API с Undici в Node.js

## Введение

[Undici](https://undici.nodejs.org) — это библиотека HTTP-клиента, на которой основан fetch API в Node.js. Она написана с нуля и не использует встроенный HTTP-клиент Node.js. Undici включает ряд возможностей, которые делают её хорошим выбором для высокопроизводительных приложений.

Информацию о соответствии Undici спецификации смотрите в [документации Undici](https://undici.nodejs.org/#/?id=specification-compliance-1).

## Базовое использование GET
```js
async function main() {
  // Как и в браузерном fetch API, метод по умолчанию — GET
  const response = await fetch('https://jsonplaceholder.typicode.com/posts');
  const data = await response.json();
  console.log(data);
  // возвращает примерно следующее:
  //   {
  //   userId: 1,
  //   id: 1,
  //   title: 'sunt aut facere repellat provident occaecati excepturi optio reprehenderit',
  //   body: 'quia et suscipit\n' +
  //     'suscipit recusandae consequuntur expedita et cum\n' +
  //     'reprehenderit molestiae ut ut quas totam\n' +
  //     'nostrum rerum est autem sunt rem eveniet architecto'
  // }
}

main().catch(console.error);
```

## Базовое использование POST
```js
// Данные, отправляемые от клиента к серверу
const body = {
  title: 'foo',
  body: 'bar',
  userId: 1,
};

async function main() {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
    method: 'POST',
    headers: {
      'User-Agent': 'undici-stream-example',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(body),
  });
  const data = await response.json();
  console.log(data);
  // возвращает примерно следующее:
  // { title: 'foo', body: 'bar', userId: 1, id: 101 }
}

main().catch(console.error);
```

## Настройка Fetch API с помощью Undici

Undici позволяет настраивать Fetch API, передавая параметры в функцию `fetch`. Например, вы можете задать пользовательские заголовки, метод запроса и тело запроса. Вот пример того, как можно настроить Fetch API с помощью Undici:

Функция [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) принимает два аргумента: URL для запроса и объект параметров. Объект параметров — это [объект опций](https://undici.nodejs.org/#/docs/api/Dispatcher?id=parameter-requestoptions), с помощью которого вы можете настроить запрос. Функция возвращает [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises), который разрешается в объект [Response](https://undici.nodejs.org/#/docs/api/Dispatcher?id=parameter-responsedata).

В следующем примере мы отправляем POST-запрос к Ollama API с полезной нагрузкой в формате JSON. Ollama — это инструмент командной строки, позволяющий запускать большие языковые модели (LLM, Large Language Models) на вашей локальной машине. Скачать его можно [здесь](https://ollama.com/download)
```
ollama run mistral
```

Эта команда скачает модель `mistral` и запустит её на вашей локальной машине.

С помощью пула вы можете повторно использовать соединения с одним и тем же сервером, что повышает производительность. Вот пример того, как можно использовать пул с Undici:
```js
import { Pool } from 'undici';

const ollamaPool = new Pool('http://localhost:11434', {
  connections: 10,
});

/**
 * Потоковая передача завершения запроса с использованием Ollama API.
 * @param {string} prompt - Запрос для завершения.
 * @link https://github.com/ollama/ollama/blob/main/docs/api.md
 **/
async function streamOllamaCompletion(prompt) {
  const { statusCode, body } = await ollamaPool.request({
    path: '/api/generate',
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ prompt, model: 'mistral' }),
  });

  // Про коды состояния HTTP можно прочитать здесь: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
  // 200 означает, что запрос выполнен успешно.
  if (statusCode !== 200) {
    // потребление тела ответа обязательно: https://undici.nodejs.org/#/?id=garbage-collection
    await body.dump();
    throw new Error(`Ollama request failed with status ${statusCode}`);
  }

  let partial = '';

  const decoder = new TextDecoder();
  for await (const chunk of body) {
    partial += decoder.decode(chunk, { stream: true });
    console.log(partial);
  }

  console.log('Streaming complete.');
}

try {
  await streamOllamaCompletion('What is recursion?');
} catch (error) {
  console.error('Error calling Ollama:', error);
} finally {
  console.log('Closing Ollama pool.');
  ollamaPool.close();
}
```

## Потоковая обработка ответов с помощью Undici

[Потоки](https://nodejs.org/docs/v22.14.0/api/stream.html#stream) — это возможность Node.js, позволяющая читать и записывать данные по частям (chunk).
```js
import { Writable } from 'node:stream';

import { stream } from 'undici';

async function fetchGitHubRepos() {
  const url = 'https://api.github.com/users/nodejs/repos';

  await stream(
    url,
    {
      method: 'GET',
      headers: {
        'User-Agent': 'undici-stream-example',
        Accept: 'application/json',
      },
    },
    res => {
      let buffer = '';

      return new Writable({
        write(chunk, encoding, callback) {
          buffer += chunk.toString();
          callback();
        },
        final(callback) {
          try {
            const json = JSON.parse(buffer);
            console.log(
              'Repository Names:',
              json.map(repo => repo.name)
            );
          } catch (error) {
            console.error('Error parsing JSON:', error);
          }
          console.log('Stream processing completed.');
          console.log(`Response status: ${res.statusCode}`);
          callback();
        },
      });
    }
  );
}

fetchGitHubRepos().catch(console.error);
```

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/getting-started/fetch>. Оригинал распространяется по лицензии MIT._

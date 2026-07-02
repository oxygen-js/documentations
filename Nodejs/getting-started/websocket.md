# Нативный клиент WebSocket в Node.js

## Введение

Начиная с [Node.js v21](https://github.com/nodejs/node/blob/47a59bde2aadb3ad1b377c0ef12df7abc28840e9/doc/changelogs/CHANGELOG_V21.md#L1329-L1345), [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) был улучшен с использованием библиотеки [Undici](https://undici.nodejs.org), что привнесло встроенный клиент WebSocket. Это упрощает обмен данными в реальном времени для приложений Node.js. В выпуске [Node.js v22.4.0](https://github.com/nodejs/node/releases/tag/v22.4.0) WebSocket API был отмечен как стабильный, что указывает на его готовность к использованию в продакшене.

## Что такое WebSocket

[WebSocket](https://en.wikipedia.org/wiki/WebSocket) — это стандартизированный протокол обмена данными, который обеспечивает одновременную двустороннюю связь по одному TCP-соединению. Его полнодуплексные, или двунаправленные, возможности отличают его от HTTP. WebSocket достигает совместимости с HTTP, используя HTTP-заголовок Upgrade для перехода между протоколами. Он позволяет серверам отправлять контент клиентам без первоначальных запросов и поддерживает открытые соединения для непрерывного обмена сообщениями, что делает его идеальным для передачи данных в реальном времени с меньшими накладными расходами, чем у альтернатив вроде HTTP-опроса. Обмен данными по WebSocket обычно происходит через TCP-порты 443 (защищённый) или 80 (незащищённый), что помогает обходить ограничения брандмауэра на невеб-соединения. Протокол определяет собственные схемы URI (ws:// и wss://) для незашифрованных и зашифрованных соединений соответственно и поддерживается всеми основными браузерами.

## Нативный клиент WebSocket

Node.js теперь может выступать в роли `client` WebSocket без обращения к внешним библиотекам, таким как [ws](https://www.npmjs.com/package/ws) или [socket.io](https://www.npmjs.com/package/socket.io), для клиентских соединений. Это позволяет приложениям Node.js напрямую инициировать и управлять исходящими WebSocket-соединениями, упрощая такие задачи, как подключение к каналам данных реального времени или взаимодействие с другими WebSocket-серверами. Теперь пользователи могут создавать клиентское WebSocket-соединение с помощью стандартного конструктора [new WebSocket()](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/WebSocket).

Опираясь на вышесказанное, давайте добавим больше практических примеров, чтобы продемонстрировать новую функциональность клиента WebSocket на базовых сценариях использования.

### Базовое соединение и обработка сообщений
```js
// Создаёт новое WebSocket-соединение по указанному URL.
const socket = new WebSocket('ws://localhost:8080');

// Выполняется, когда соединение успешно установлено.
socket.addEventListener('open', event => {
  console.log('WebSocket connection established!');
  // Отправляет сообщение на WebSocket-сервер.
  socket.send('Hello Server!');
});

// Слушает сообщения и выполняется, когда сообщение получено от сервера.
socket.addEventListener('message', event => {
  console.log('Message from server: ', event.data);
});

// Выполняется, когда соединение закрывается, предоставляя код закрытия и причину.
socket.addEventListener('close', event => {
  console.log('WebSocket connection closed:', event.code, event.reason);
});

// Выполняется, если во время обмена данными по WebSocket возникает ошибка.
socket.addEventListener('error', error => {
  console.error('WebSocket error:', error);
});
```

### Отправка и получение данных JSON
```js
const socket = new WebSocket('ws://localhost:8080');

socket.addEventListener('open', () => {
  const data = { type: 'message', content: 'Hello from Node.js!' };
  socket.send(JSON.stringify(data));
});

socket.addEventListener('message', event => {
  try {
    const receivedData = JSON.parse(event.data);
    console.log('Received JSON:', receivedData);
  } catch (error) {
    console.error('Error parsing JSON:', error);
    console.log('Received data was:', event.data);
  }
});
```

Приведённый выше код на `javascript` демонстрирует отправку и получение данных [JSON](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON), что часто встречается в приложениях WebSocket. Он использует [JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) для преобразования объектов JavaScript в строки JSON перед отправкой. И преобразует полученную строку обратно в объект JavaScript с помощью [JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). Наконец, он включает обработку ошибок при разборе JSON.

Это обеспечивает сокращение объёма управления зависимостями и улучшенную совместимость. Разработчики могут избежать установки и сопровождения дополнительных клиентских библиотек WebSocket. Встроенная реализация соответствует современным веб-стандартам, обеспечивая лучшую совместимость. Это улучшение сосредоточено на клиентской стороне обмена данными по WebSocket, позволяя Node.js выступать в роли клиента WebSocket.

## Важно понимать

Node.js v22 **не** предоставляет встроенной нативной реализации сервера WebSocket. Чтобы создать WebSocket-сервер, принимающий входящие соединения от веб-браузеров или других клиентов, по-прежнему необходимо использовать библиотеки вроде [ws](https://www.npmjs.com/package/ws) или [socket.io](https://www.npmjs.com/package/socket.io). Это означает, что, хотя Node.js теперь может легко **подключаться** к WebSocket-серверам, ему по-прежнему требуются внешние инструменты, чтобы **стать** WebSocket-сервером.

## Итог

Node.js v22 даёт приложениям возможность беспрепятственно взаимодействовать с WebSocket-серверами в качестве `clients`, но создание WebSocket-серверов внутри Node.js по-прежнему зависит от устоявшихся библиотек. Это различие крайне важно понимать разработчикам при реализации обмена данными в реальном времени в их проектах на Node.js.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/getting-started/websocket. Оригинал распространяется по лицензии MIT._

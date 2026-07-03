# Анатомия HTTP-транзакции

Цель этого руководства — дать прочное понимание процесса обработки HTTP в Node.js. Мы предполагаем, что вы в общих чертах знаете, как работают HTTP-запросы, независимо от языка или среды программирования. Мы также предполагаем некоторое знакомство с [`EventEmitters`](https://nodejs.org/api/events.html) и [`Streams`](https://nodejs.org/api/stream.html) в Node.js. Если вы не совсем знакомы с ними, стоит бегло прочитать документацию API по каждому из них.

## Создание сервера

Любое веб-серверное приложение на node в какой-то момент должно будет создать объект веб-сервера. Это делается с помощью [`createServer`](https://nodejs.org/api/http.html#http_http_createserver_requestlistener).
```js
const http = require('node:http');

const server = http.createServer((request, response) => {
  // здесь происходит магия!
});
```

Функция, которая передаётся в [`createServer`](https://nodejs.org/api/http.html#http_http_createserver_requestlistener), вызывается один раз для каждого HTTP-запроса, поступающего на этот сервер, поэтому её называют обработчиком запросов. По сути, объект [`Server`](https://nodejs.org/api/http.html#http_class_http_server), возвращаемый [`createServer`](https://nodejs.org/api/http.html#http_http_createserver_requestlistener), является [`EventEmitter`](https://nodejs.org/api/events.html#events_class_eventemitter), и то, что мы видим здесь, — просто сокращённая запись для создания объекта `server` с последующим добавлением слушателя.
```js
const server = http.createServer();
server.on('request', (request, response) => {
  // здесь происходит та же самая магия!
});
```

Когда HTTP-запрос достигает сервера, Node вызывает функцию-обработчик запросов с несколькими удобными объектами для работы с транзакцией — `request` и `response`. Мы вскоре до них доберёмся.

Чтобы фактически обслуживать запросы, необходимо вызвать метод [`listen`](https://nodejs.org/api/http.html#http_server_listen_port_hostname_backlog_callback) на объекте `server`. В большинстве случаев всё, что вам нужно передать в `listen`, — это номер порта, на котором вы хотите, чтобы сервер прослушивал соединения. Есть и другие параметры, поэтому загляните в [справочник по API](https://nodejs.org/api/http.html).

## Метод, URL и заголовки

При обработке запроса первое, что вы, вероятно, захотите сделать, — посмотреть на метод и URL, чтобы предпринять соответствующие действия. Node.js делает это относительно безболезненно, помещая удобные свойства в объект `request`.
```
const { method, url } = request;
```

> Объект `request` является экземпляром [`IncomingMessage`](https://nodejs.org/api/http.html#http_class_http_incomingmessage).

`method` здесь всегда будет обычным HTTP-методом/глаголом. `url` — это полный URL без сервера, протокола или порта. Для типичного URL это означает всё, что идёт после третьего слэша (включая его).

Заголовки тоже недалеко. Они находятся в своём собственном объекте на `request` под названием `headers`.
```js
const { headers } = request;
const userAgent = headers['user-agent'];
```

Здесь важно отметить, что все заголовки представлены только в нижнем регистре, независимо от того, как клиент их фактически отправил. Это упрощает задачу разбора заголовков для любых целей.

Если некоторые заголовки повторяются, то их значения либо перезаписываются, либо объединяются в строки, разделённые запятыми, в зависимости от заголовка. В некоторых случаях это может быть проблематично, поэтому доступен также [`rawHeaders`](https://nodejs.org/api/http.html#http_message_rawheaders).

## Тело запроса

При получении запроса `POST` или `PUT` тело запроса может быть важно для вашего приложения. Получить данные тела немного сложнее, чем обратиться к заголовкам запроса. Объект `request`, который передаётся в обработчик, реализует интерфейс [`ReadableStream`](https://nodejs.org/api/stream.html#stream_class_stream_readable). Этот поток можно прослушивать или направлять (pipe) куда-то ещё, как и любой другой поток. Мы можем извлекать данные прямо из потока, прослушивая события `'data'` и `'end'` потока.

Фрагмент (chunk), выдаваемый в каждом событии `'data'`, является [`Buffer`](https://nodejs.org/api/buffer.html). Если вы знаете, что это будут строковые данные, лучше всего собирать данные в массив, а затем, при наступлении события `'end'`, объединить их и преобразовать в строку.
```js
let body = [];
request
  .on('data', chunk => {
    body.push(chunk);
  })
  .on('end', () => {
    body = Buffer.concat(body).toString();
    // на этом этапе `body` содержит всё тело запроса в виде строки
  });
```

> Это может показаться немного утомительным, и во многих случаях так оно и есть. К счастью, в [`npm`](https://www.npmjs.com) есть такие модули, как [`concat-stream`](https://www.npmjs.com/package/concat-stream) и [`body`](https://www.npmjs.com/package/body), которые помогают скрыть часть этой логики. Важно хорошо понимать, что происходит, прежде чем идти по этому пути, — и именно поэтому вы здесь!

## Небольшое замечание об ошибках

Поскольку объект `request` является [`ReadableStream`](https://nodejs.org/api/stream.html#stream_class_stream_readable), он также является [`EventEmitter`](https://nodejs.org/api/events.html#events_class_eventemitter) и ведёт себя как таковой, когда происходит ошибка.

Ошибка в потоке `request` проявляется путём генерации события `'error'` на потоке. **Если у вас нет слушателя для этого события, ошибка будет _выброшена_, что может привести к аварийному завершению вашей программы Node.js.** Поэтому вам следует добавить слушатель `'error'` на ваши потоки запросов, даже если вы просто логируете ошибку и продолжаете работу. (Хотя, вероятно, лучше всего отправить какой-либо HTTP-ответ об ошибке. Подробнее об этом позже.)
```js
request.on('error', err => {
  // Это выводит сообщение об ошибке и трассировку стека в `stderr`.
  console.error(err.stack);
});
```

Существуют и другие способы [обработки таких ошибок](https://nodejs.org/api/errors.html), например другие абстракции и инструменты, но всегда помните, что ошибки могут случаться и случаются, и вам придётся с ними иметь дело.

## Что у нас есть на данный момент

К этому моменту мы рассмотрели создание сервера, а также извлечение метода, URL, заголовков и тела из запросов. Если собрать всё это вместе, это может выглядеть примерно так:
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    const { headers, method, url } = request;
    let body = [];
    request
      .on('error', err => {
        console.error(err);
      })
      .on('data', chunk => {
        body.push(chunk);
      })
      .on('end', () => {
        body = Buffer.concat(body).toString();
        // На этом этапе у нас есть заголовки, метод, url и тело, и теперь мы можем
        // делать всё, что нужно, чтобы ответить на этот запрос.
      });
  })
  .listen(8080); // Активирует этот сервер, слушающий порт 8080.
```

Если мы запустим этот пример, то сможем _получать_ запросы, но не _отвечать_ на них. На самом деле, если вы обратитесь к этому примеру из веб-браузера, ваш запрос завершится по тайм-ауту, поскольку клиенту ничего не отправляется обратно.

Пока что мы вообще не касались объекта `response`, который является экземпляром [`ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse), являющегося [`WritableStream`](https://nodejs.org/api/stream.html#stream_class_stream_writable). Он содержит множество полезных методов для отправки данных обратно клиенту. Мы рассмотрим это далее.

## HTTP-код состояния

Если вы не потрудитесь его установить, HTTP-код состояния ответа всегда будет равен 200. Разумеется, не каждый HTTP-ответ этого заслуживает, и в какой-то момент вам определённо захочется отправить другой код состояния. Чтобы сделать это, вы можете установить свойство `statusCode`.
```
response.statusCode = 404; // Сообщаем клиенту, что ресурс не найден.
```

Есть и другие сокращения для этого, как мы вскоре увидим.

## Установка заголовков ответа

Заголовки устанавливаются с помощью удобного метода под названием [`setHeader`](https://nodejs.org/api/http.html#http_response_setheader_name_value).
```js
response.setHeader('Content-Type', 'application/json');
response.setHeader('X-Powered-By', 'bacon');
```

При установке заголовков ответа регистр их имён не имеет значения. Если вы устанавливаете заголовок несколько раз, отправляется последнее установленное вами значение.

## Явная отправка данных заголовков

Методы установки заголовков и кода состояния, которые мы уже обсудили, предполагают, что вы используете «неявные заголовки». Это означает, что вы рассчитываете на то, что node отправит заголовки за вас в нужный момент, прежде чем вы начнёте отправлять данные тела.

При желании вы можете _явно_ записать заголовки в поток ответа. Для этого существует метод под названием [`writeHead`](https://nodejs.org/api/http.html#http_response_writehead_statuscode_statusmessage_headers), который записывает код состояния и заголовки в поток.
```js
response.writeHead(200, {
  'Content-Type': 'application/json',
  'X-Powered-By': 'bacon',
});
```

После того как вы установили заголовки (неявно или явно), вы готовы начать отправку данных ответа.

## Отправка тела ответа

Поскольку объект `response` является [`WritableStream`](https://nodejs.org/api/stream.html#stream_class_stream_writable), запись тела ответа клиенту — это просто вопрос использования обычных методов потока.
```js
response.write('<html>');
response.write('<body>');
response.write('<h1>Hello, World!</h1>');
response.write('</body>');
response.write('</html>');
response.end();
```

Функция `end` на потоках также может принимать некоторые необязательные данные для отправки в качестве последней порции данных в потоке, поэтому мы можем упростить приведённый выше пример следующим образом.
```
response.end('<html><body><h1>Hello, World!</h1></body></html>');
```

> Важно установить статус и заголовки _до_ того, как вы начнёте записывать фрагменты данных в тело. В этом есть смысл, поскольку в HTTP-ответах заголовки идут перед телом.

## Ещё одно небольшое замечание об ошибках

Поток `response` также может генерировать события `'error'`, и в какой-то момент вам придётся иметь дело и с этим. Все рекомендации по ошибкам потока `request` по-прежнему применимы здесь.

## Собираем всё вместе

Теперь, когда мы узнали о формировании HTTP-ответов, давайте соберём всё вместе. Отталкиваясь от предыдущего примера, мы создадим сервер, который отправляет обратно все данные, отправленные нам пользователем. Мы отформатируем эти данные как JSON с помощью `JSON.stringify`.
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    const { headers, method, url } = request;
    let body = [];
    request
      .on('error', err => {
        console.error(err);
      })
      .on('data', chunk => {
        body.push(chunk);
      })
      .on('end', () => {
        body = Buffer.concat(body).toString();
        // НАЧАЛО НОВОГО КОДА

        response.on('error', err => {
          console.error(err);
        });

        response.statusCode = 200;
        response.setHeader('Content-Type', 'application/json');
        // Примечание: 2 строки выше можно заменить следующей одной:
        // response.writeHead(200, {'Content-Type': 'application/json'})

        const responseBody = { headers, method, url, body };

        response.write(JSON.stringify(responseBody));
        response.end();
        // Примечание: 2 строки выше можно заменить следующей одной:
        // response.end(JSON.stringify(responseBody))

        // КОНЕЦ НОВОГО КОДА
      });
  })
  .listen(8080);
```

## Пример эхо-сервера

Давайте упростим предыдущий пример, чтобы создать простой эхо-сервер, который просто отправляет обратно в ответе любые данные, полученные в запросе. Всё, что нам нужно сделать, — это извлечь данные из потока запроса и записать эти данные в поток ответа, подобно тому, как мы делали ранее.
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    let body = [];
    request
      .on('data', chunk => {
        body.push(chunk);
      })
      .on('end', () => {
        body = Buffer.concat(body).toString();
        response.end(body);
      });
  })
  .listen(8080);
```

Теперь давайте немного изменим это. Мы хотим отправлять эхо только при следующих условиях:

  * Метод запроса — POST.
  * URL — `/echo`.

В любом другом случае мы хотим просто ответить кодом 404.
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    if (request.method === 'POST' && request.url === '/echo') {
      let body = [];
      request
        .on('data', chunk => {
          body.push(chunk);
        })
        .on('end', () => {
          body = Buffer.concat(body).toString();
          response.end(body);
        });
    } else {
      response.statusCode = 404;
      response.end();
    }
  })
  .listen(8080);
```

> Проверяя URL таким образом, мы выполняем разновидность «маршрутизации». Другие формы маршрутизации могут быть такими же простыми, как операторы `switch`, или такими же сложными, как целые фреймворки, например [`express`](https://www.npmjs.com/package/express). Если вы ищете что-то, что выполняет маршрутизацию и ничего больше, попробуйте [`router`](https://www.npmjs.com/package/router).

Отлично! Теперь давайте попробуем упростить это. Помните, что объект `request` является [`ReadableStream`](https://nodejs.org/api/stream.html#stream_class_stream_readable), а объект `response` является [`WritableStream`](https://nodejs.org/api/stream.html#stream_class_stream_writable). Это означает, что мы можем использовать [`pipe`](https://nodejs.org/api/stream.html#stream_readable_pipe_destination_options), чтобы направить данные из одного в другой. Именно это нам и нужно для эхо-сервера!
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    if (request.method === 'POST' && request.url === '/echo') {
      request.pipe(response);
    } else {
      response.statusCode = 404;
      response.end();
    }
  })
  .listen(8080);
```

Ура, потоки!

Однако мы ещё не совсем закончили. Как неоднократно упоминалось в этом руководстве, ошибки могут случаться и случаются, и нам нужно с ними разбираться.

Чтобы обработать ошибки в потоке запроса, мы залогируем ошибку в `stderr` и отправим код состояния 400, чтобы указать на `Bad Request`. Однако в реальном приложении нам бы хотелось изучить ошибку, чтобы определить, какими должны быть правильный код состояния и сообщение. Как обычно в случае ошибок, вам следует обратиться к [документации по `Error`](https://nodejs.org/api/errors.html).

Что касается ответа, мы просто залогируем ошибку в `stderr`.
```js
const http = require('node:http');

http
  .createServer((request, response) => {
    request.on('error', err => {
      console.error(err);
      response.statusCode = 400;
      response.end();
    });
    response.on('error', err => {
      console.error(err);
    });
    if (request.method === 'POST' && request.url === '/echo') {
      request.pipe(response);
    } else {
      response.statusCode = 404;
      response.end();
    }
  })
  .listen(8080);
```

Теперь мы рассмотрели большинство основ обработки HTTP-запросов. На этом этапе вы должны уметь:

  * Создавать экземпляр HTTP-сервера с функцией-обработчиком запросов и заставлять его слушать порт.
  * Получать заголовки, URL, метод и данные тела из объектов `request`.
  * Принимать решения о маршрутизации на основе URL и/или других данных в объектах `request`.
  * Отправлять заголовки, HTTP-коды состояния и данные тела через объекты `response`.
  * Направлять (pipe) данные из объектов `request` и в объекты `response`.
  * Обрабатывать ошибки потоков как в потоке `request`, так и в потоке `response`.

На основе этих основ можно строить HTTP-серверы Node.js для многих типичных сценариев использования. Эти API предоставляют множество других возможностей, поэтому обязательно прочитайте документацию API по [`EventEmitters`](https://nodejs.org/api/events.html), [`Streams`](https://nodejs.org/api/stream.html) и [`HTTP`](https://nodejs.org/api/http.html).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/http/anatomy-of-an-http-transaction. Оригинал распространяется по лицензии MIT._

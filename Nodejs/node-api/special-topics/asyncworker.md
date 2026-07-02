# AsyncWorker

У вас может быть проект, в котором есть фрагмент долго работающего кода на C/C++, который вы хотите выполнять в фоне, а не в главном цикле событий Node. Класс [`AsyncWorker`](https://github.com/nodejs/node-addon-api/blob/main/doc/async_worker.md) из Node-API разработан специально для этого случая.

Как программисту, ваша задача по сути состоит в том, чтобы создать подкласс `AsyncWorker` и реализовать метод `Execute`. Вы также, вероятно, реализуете функцию-обёртку, чтобы упростить использование вашего `AsyncWorker`.

В этом примере мы собираемся создать класс `SimpleAsyncWorker`, который является подклассом `AsyncWorker`. Воркер будет принимать целочисленное значение, указывающее продолжительность времени, в течение которого он должен «работать». Когда воркер завершится, он вернёт текстовую строку, указывающую, как долго он работал. В одном случае воркер вместо этого сообщит об ошибке.

## SimpleAsyncWorker

[**SimpleAsyncWorker.h**](https://github.com/nodejs/node-addon-examples/blob/main/src/5-async-work/napi-asyncworker-example/node-addon-api/src/SimpleAsyncWorker.h) — заголовочный файл на C++ для `SimpleAsyncWorker`. В нём объявляется конструктор, который принимает в качестве аргумента продолжительность времени (в секундах), в течение которого должен работать `SimpleAsyncWorker`. Для хранения этого значения объявляется приватный член данных.

В заголовке также объявляются два метода, `Execute` и `OnOK`, которые переопределяют методы, объявленные в `AsyncWorker`, и подробнее описаны ниже.

[**SimpleAsyncWorker.cc**](https://github.com/nodejs/node-addon-examples/blob/main/src/5-async-work/napi-asyncworker-example/node-addon-api/src/SimpleAsyncWorker.cc) — реализация на C++. Конструктор принимает два аргумента. `callback` — это функция JavaScript, которая вызывается, когда метод `Execute` возвращает управление. `callback` вызывается независимо от того, была ошибка или нет. Второй аргумент конструктора, `runTime`, — это целочисленное значение, указывающее, как долго (в секундах) должен работать воркер.

Node будет выполнять код метода `Execute` в потоке выполнения, отдельном от потока, в котором выполняется главный цикл событий Node. Метод `Execute` _не имеет доступа_ ни к какой части окружения Node-API. По этой причине входное значение `runTime` было сохранено конструктором как приватный член данных.

В этой реализации метод `Execute` просто ждёт число секунд, указанное ранее через `runTime`. Именно сюда в реальной реализации помещается долго работающий код. Чтобы продемонстрировать, как работает обработка ошибок, этот метод `Execute` объявляет ошибку, когда его просят работать 4 секунды.

Метод `OnOK` вызывается после того, как метод `Execute` возвращает управление, если только метод `Execute` не вызвал `SetError` или в случае, когда исключения C++ включены и выбрасывается исключение. В случае ошибки вместо `OnOK` вызывается метод `OnError`. Реализация `OnError` по умолчанию просто вызывает функцию-колбэк `AsyncWorker` с ошибкой в качестве единственного аргумента.

В этой реализации метод `OnOK` формирует строковое значение и передаёт его как _второй_ аргумент функции `callback`, указанной в конструкторе. Первым аргументом, передаваемым функции `callback`, является значение JavaScript `null`. Причина в том, что единая функция-колбэк вызывается независимо от того, произошла ошибка или нет. Метод `OnError` по умолчанию, который `SimpleAsyncWorker` не переопределяет, передаёт ошибку как первый аргумент колбэка. Это станет яснее в следующем разделе.

Обратите внимание, что в отличие от `Execute`, методы `OnOK` и `OnError` _действительно_ имеют доступ к окружению Node-API.

## RunSimpleAsyncWorker

Нам нужна функция на C++, которая создаёт объекты `SimpleAsyncWorker` и запрашивает постановку их в очередь. Эту функцию нужно зарегистрировать в Node-API, чтобы она была доступна из кода JavaScript. [**RunSimpleAsyncWorker.cc**](https://github.com/nodejs/node-addon-examples/blob/main/src/5-async-work/napi-asyncworker-example/node-addon-api/src/RunSimpleAsyncWorker.cc) предоставляет эту обёртку.

Функция `runSimpleAsyncWorker`, доступная из JavaScript, принимает два аргумента, которые передаются через аргумент `info`. Первый аргумент, передаваемый как `info[0]`, — это `runTime`, а второй аргумент — функция-колбэк JavaScript, которая вызывается, когда метод `Execute` возвращает управление.

Затем код создаёт объект `SimpleAsyncWorker` и запрашивает постановку его в очередь для возможного выполнения на следующем тике. Пока объект `SimpleAsyncWorker` не поставлен в очередь, его метод `Execute` никогда не будет вызван.

Как только объект `SimpleAsyncWorker` поставлен в очередь, `runSimpleAsyncWorker` формирует текстовую строку и возвращает её вызывающей стороне.

## Запуск в JavaScript

[**Test.js**](https://github.com/nodejs/node-addon-examples/blob/main/src/5-async-work/napi-asyncworker-example/node-addon-api/test/Test.js) — простая программа на JavaScript, которая показывает, как запускать экземпляры `SimpleAsyncWorker`. Она вызывает `runSimpleAsyncWorker` три раза, каждый раз с разным параметром `runTime`. Каждый вызов указывает `AsyncWorkerCompletion` в качестве функции-колбэка.

Функция `AsyncWorkerCompletion` написана так, чтобы обрабатывать случаи, когда метод `Execute` сообщает об ошибке и когда не сообщает. Она просто выводит сообщение в консоль при вызове.

Вот как выглядит вывод, когда JavaScript успешно выполняется:
```
runSimpleAsyncWorker returned 'SimpleAsyncWorker for 2 seconds queued.'.
runSimpleAsyncWorker returned 'SimpleAsyncWorker for 4 seconds queued.'.
runSimpleAsyncWorker returned 'SimpleAsyncWorker for 8 seconds queued.'.
SimpleAsyncWorker returned 'SimpleAsyncWorker returning after 'working' 2 seconds.'.
SimpleAsyncWorker returned an error:  [Error: Oops! Failed after 'working' 4 seconds.]
SimpleAsyncWorker returned 'SimpleAsyncWorker returning after 'working' 8 seconds.'.
```

Как и ожидалось, каждый вызов `runSimpleAsyncWorker` немедленно возвращает управление. Функция `AsyncWorkerCompletion` вызывается, когда завершается каждый `SimpleAsyncWorker`.

## Оговорки

  * _Абсолютно необходимо_, чтобы метод `Execute` не делал никаких вызовов Node-API. Это означает, что метод `Execute` _не имеет доступа_ ни к каким входным значениям, переданным кодом JavaScript.

Обычно конструктор класса `AsyncWorker` собирает нужную ему информацию из объектов JavaScript и сохраняет _копии_ этой информации как члены данных. Результаты метода `Execute` затем можно превратить обратно в объекты JavaScript в методе `OnOK`.

  * Процесс Node осведомлён обо всех выполняющихся методах `Execute` и не завершится, пока все выполняющиеся методы `Execute` не вернут управление.

  * AsyncWorker можно безопасно завершить вызовом `AsyncWorker::Cancel` из главного потока выполнения.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/node-api/special-topics/asyncworker>. Оригинал распространяется по лицензии MIT._

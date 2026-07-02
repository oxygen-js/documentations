# Ссылки на объекты и функции

JavaScript реализует динамическую модель памяти. Когда объекты становятся недостижимыми, они становятся кандидатами на освобождение сборщиком мусора, работающим в фоне.

Бывают ситуации, когда вам нужно гарантировать, что объекты, созданные вашим кодом Node-API, остаются размещёнными в памяти. В этом случае вам нужно явно создать ссылку на них. Именно для этого предназначены классы `ObjectReference` и `FunctionReference`.

## Постоянная ссылка (Persistent Reference)

Ссылки на объекты и функции можно создавать либо как `Weak` (слабые), либо как `Persistent` (постоянные).

Ссылка `Persistent` инициализирует внутренний счётчик ссылок значением один, что предотвращает освобождение памяти объекта сборщиком мусора. Объект, на который указывает ссылка, будет оставаться в памяти в течение всего времени жизни ссылки `Persistent`.

Использование ссылки `Persistent` имеет смысл в случае, когда продолжительность существования ссылки известна заранее. Внутренний счётчик ссылок уменьшается при удалении ссылки `Persistent`. Это сделает объект, на который указывает ссылка, кандидатом на удаление, если внутренний счётчик ссылок дойдёт до нуля.

Наиболее распространённый случай использования ссылки `Persistent` — когда вы создаёте класс JavaScript в своём коде Node-API и вам нужно гарантировать, что его конструктор остаётся размещённым движком среды выполнения JavaScript.

## Слабая ссылка (Weak Reference)

Для более сложных реализаций, где на объект, на который указывает ссылка, полагаются несколько AsyncWorker'ов, может иметь больше смысла использовать ссылку `Weak`, которая инициализирует внутренний счётчик ссылок значением ноль. Счётчик ссылок затем можно поддерживать с помощью методов `Ref` и `Unref` класса `Reference`.

Наиболее распространённый сценарий использования ссылки `Weak` — когда вашему коду Node-API нужно отслеживать, когда объект JavaScript, созданный вами в коде Node-API, освобождается сборщиком мусора.

## ObjectReference

Класс `ObjectReference` наследуется от класса `Reference`. Он добавляет коллекцию методов `Get` и `Set`, которые манипулируют свойствами объекта, на который указывает ссылка.

## FunctionReference

Как и `ObjectReference`, класс `FunctionReference` наследуется от класса `Reference`. В то время как класс `ObjectReference` добавляет методы `Get` и `Set`, класс `FunctionReference` добавляет набор методов `Call`, которые реализуют вызовы функции.

## Пример

Этот пример кода показывает, как использовать класс `FunctionReference`.

### src/native-addon.h

[**native-addon.h**](https://github.com/nodejs/node-addon-examples/blob/main/src/4-references-and-handle-scope/function-reference-demo/node-addon-api/src/native-addon.h) объявляет C++ класс `NativeAddon`, у которого есть два члена данных, заполняемых в конструкторе:

  * `Napi::FunctionReference jsFnRef`
  * `Napi::Function jsFn`

### src/native-addon.cc

[**native-addon.cc**](https://github.com/nodejs/node-addon-examples/blob/main/src/4-references-and-handle-scope/function-reference-demo/node-addon-api/src/native-addon.cc) содержит реализацию `NativeAddon`. Конструктор, который вызывается из JavaScript, принимает два аргумента-функции. Первый аргумент сохраняется как `Napi::FunctionReference`, а второй сохраняется как `Napi::Function`.

> _В этом коде есть намеренная ошибка._

Вторая функция сохраняется в члене данных `Napi::Function jsFn`. **Это ошибка**, потому что время жизни второго аргумента ограничено временем жизни конструктора. Значение члена данных `jsFn` станет недействительным после того, как конструктор вернёт управление. Первый аргумент сохраняется в `Napi::FunctionReference jsFnRef`. Благодаря использованию `Napi::FunctionReference` значение `jsFnRef` останется действительным после того, как конструктор вернёт управление.

Класс `NativeAddon` реализует два метода, которые можно вызывать из JavaScript: `TryCallByStoredReference` и `TryCallByStoredFunction`. Обратите внимание, что метод `Call` используется одинаково как для члена данных `jsFnRef`, так и для `jsFn`.

### src/binding.cc

[**binding.cc**](https://github.com/nodejs/node-addon-examples/blob/main/src/4-references-and-handle-scope/function-reference-demo/node-addon-api/src/binding.cc) — стандартный файл привязки (binding), который регистрирует класс `NativeAddon` в Node-API.

### index.js

[**index.js**](https://github.com/nodejs/node-addon-examples/blob/main/src/4-references-and-handle-scope/function-reference-demo/node-addon-api/index.js) показывает использование класса `NativeAddon` из JavaScript. Обратите внимание, что вызов нативного метода `tryCallByStoredFunction` завершается неудачей, потому что член данных, на который он полагается, недействителен.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/node-api/special-topics/object-function-refs>. Оригинал распространяется по лицензии MIT._

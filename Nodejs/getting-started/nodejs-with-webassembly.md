# Node.js с WebAssembly

**[WebAssembly](https://webassembly.org)** — это высокопроизводительный язык, похожий на ассемблер, который можно скомпилировать из различных языков, включая C/C++, Rust и AssemblyScript. В настоящее время он поддерживается Chrome, Firefox, Safari, Edge и Node.js!

Спецификация WebAssembly описывает два формата файлов: бинарный формат под названием WebAssembly Module с расширением `.wasm` и соответствующее текстовое представление под названием WebAssembly Text format с расширением `.wat`.

## Ключевые понятия

  * Module (модуль) — скомпилированный бинарный файл WebAssembly, то есть файл `.wasm`.
  * Memory (память) — изменяемый по размеру ArrayBuffer.
  * Table (таблица) — изменяемый по размеру типизированный массив ссылок, не хранящихся в Memory.
  * Instance (экземпляр) — экземпляр Module с его Memory, Table и переменными.

Чтобы использовать WebAssembly, вам нужен бинарный файл `.wasm` и набор API для взаимодействия с WebAssembly. Node.js предоставляет необходимые API через глобальный объект `WebAssembly`.
```js
console.log(WebAssembly);
/*
Object [WebAssembly] {
  compile: [Function: compile],
  validate: [Function: validate],
  instantiate: [Function: instantiate]
}
*/
```

## Генерация модулей WebAssembly

Существует несколько способов сгенерировать бинарные файлы WebAssembly, в том числе:

  * Написание WebAssembly (`.wat`) вручную и преобразование в бинарный формат с помощью таких инструментов, как [wabt](https://github.com/webassembly/wabt)
  * Использование [emscripten](https://emscripten.org/) с приложением на C/C++
  * Использование [wasm-pack](https://rustwasm.github.io/wasm-pack/book/) с приложением на Rust
  * Использование [AssemblyScript](https://www.assemblyscript.org/), если вы предпочитаете опыт, похожий на TypeScript

> Некоторые из этих инструментов генерируют не только бинарный файл, но и связующий («glue») код на JavaScript и соответствующие HTML-файлы для запуска в браузере.

## Как это использовать

Как только у вас есть модуль WebAssembly, вы можете использовать объект `WebAssembly` из Node.js, чтобы создать его экземпляр.
```js
// Предположим, существует файл add.wasm, содержащий единственную функцию, которая складывает два переданных аргумента
const fs = require('node:fs');

// Используем функцию readFileSync для чтения содержимого файла "add.wasm"
const wasmBuffer = fs.readFileSync('/path/to/add.wasm');

// Используем метод WebAssembly.instantiate для создания экземпляра модуля WebAssembly
WebAssembly.instantiate(wasmBuffer).then(wasmModule => {
  // Экспортированная функция находится в объекте instance.exports
  const { add } = wasmModule.instance.exports;
  const sum = add(5, 6);
  console.log(sum); // Выводит: 11
});
```

## Взаимодействие с ОС

Модули WebAssembly не могут напрямую обращаться к функциональности ОС самостоятельно. Для доступа к этой функциональности можно использовать сторонний инструмент [Wasmtime](https://docs.wasmtime.dev/). `Wasmtime` использует API [WASI](https://wasi.dev/) для доступа к функциональности ОС.

## Ресурсы

  * [Общая информация о WebAssembly](https://webassembly.org/)
  * [Документация MDN](https://developer.mozilla.org/en-US/docs/WebAssembly)
  * [Написание WebAssembly вручную](https://webassembly.github.io/spec/core/text/index.html)

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/getting-started/nodejs-with-webassembly. Оригинал распространяется по лицензии MIT._

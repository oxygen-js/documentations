# Асинхронный JavaScript — перевод раздела MDN на русский

Перевод учебного модуля **[Asynchronous JavaScript](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Async_JS)** из MDN Web Docs (раздел «Learn web development → Extensions»).

В этом модуле рассматривается [асинхронный](https://developer.mozilla.org/en-US/docs/Glossary/Asynchronous) JavaScript: почему он важен и как с его помощью эффективно обрабатывать потенциально блокирующие операции — например, получение ресурсов с сервера.

## Предварительные требования

Асинхронный JavaScript — довольно продвинутая тема, поэтому перед её изучением рекомендуется проработать модули [«Динамические скрипты на JavaScript» (Dynamic scripting with JavaScript)](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting).

> [!NOTE]
> Если вы работаете на компьютере, планшете или другом устройстве, где нельзя создавать файлы, попробуйте запускать код в онлайн-редакторе — например, в [CodePen](https://codepen.io/) или [JSFiddle](https://jsfiddle.net/).

## Учебные статьи и задания

- [01. Введение в асинхронный JavaScript](<01. Introducing asynchronous JavaScript.md>)
  — Что такое **синхронное** и **асинхронное** программирование, почему часто приходится прибегать к асинхронным приёмам и какие проблемы связаны с тем, как асинхронные функции исторически реализовывались в JavaScript.
- [02. Как использовать промисы](<02. How to use promises.md>)
  — Знакомство с промисами и работой с API на их основе. Также разбираются ключевые слова `async` и `await`.
- [03. Реализация API на основе промисов](<03. Implementing a promise-based API.md>)
  — Как реализовать собственный API, возвращающий промисы.
- [04. Знакомство с воркерами](<04. Introducing workers.md>)
  — Воркеры позволяют выполнять некоторые задачи в отдельном потоке, чтобы основной код оставался отзывчивым. В статье мы переписываем долго выполняющуюся синхронную функцию, чтобы она работала через воркер.
- [05. Последовательный запуск анимаций](<05. Sequencing animations.md>) <sup>задание</sup>
  — В этом задании нужно с помощью промисов проиграть набор анимаций в заданной последовательности.

## Смотрите также

- [Asynchronous Programming](https://eloquentjavascript.net/11_async.html) — глава замечательной онлайн-книги [Eloquent JavaScript](https://eloquentjavascript.net/) Марейна Хавербеке (Marijn Haverbeke).

---

> _Перевод учебного модуля MDN Web Docs «Asynchronous JavaScript». Источник: https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Async_JS. Оригинал распространяется по лицензии [CC-BY-SA 2.5](https://creativecommons.org/licenses/by-sa/2.5/)._

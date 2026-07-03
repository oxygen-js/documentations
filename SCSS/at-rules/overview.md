# At-правила

Большая часть дополнительных возможностей Sass реализована в виде новых
[at-правил](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule), которые
добавляются поверх CSS:

- [`@use`](https://sass-lang.com/documentation/at-rules/use) загружает миксины, функции и переменные из других таблиц стилей Sass и объединяет CSS из нескольких таблиц стилей вместе.

- [`@forward`](https://sass-lang.com/documentation/at-rules/forward) загружает таблицу стилей Sass и делает доступными её миксины, функции и переменные, когда ваша таблица стилей загружается с помощью правила `@use`.

- [`@import`](https://sass-lang.com/documentation/at-rules/import) расширяет at-правило CSS, позволяя загружать стили, миксины, функции и переменные из других таблиц стилей.

- [`@mixin` и `@include`](https://sass-lang.com/documentation/at-rules/mixin) позволяют легко повторно использовать фрагменты стилей.

- [`@function`](https://sass-lang.com/documentation/at-rules/function) определяет пользовательские функции, которые можно использовать в [выражениях SassScript][].

- [`@extend`](https://sass-lang.com/documentation/at-rules/extend) позволяет селекторам наследовать стили друг от друга.

- [`@at-root`](https://sass-lang.com/documentation/at-rules/at-root) помещает вложенные в него стили в корень CSS-документа.

- [`@error`](https://sass-lang.com/documentation/at-rules/error) приводит к прерыванию компиляции с сообщением об ошибке.

- [`@warn`](https://sass-lang.com/documentation/at-rules/warn) выводит предупреждение, не останавливая компиляцию полностью.

- [`@debug`](https://sass-lang.com/documentation/at-rules/debug) выводит сообщение в целях отладки.

- Правила управления потоком выполнения, такие как [`@if`][], [`@each`][], [`@for`][] и [`@while`][], определяют, будут ли выведены стили и сколько раз.

[выражениях SassScript]: https://sass-lang.com/documentation/syntax/structure#expressions
[`@if`]: https://sass-lang.com/documentation/at-rules/control/if
[`@each`]: https://sass-lang.com/documentation/at-rules/control/each
[`@for`]: https://sass-lang.com/documentation/at-rules/control/for
[`@while`]: https://sass-lang.com/documentation/at-rules/control/while

Sass также обладает особым поведением для [обычных at-правил CSS][]: они могут содержать [интерполяцию][], а также могут быть вложены в стилевые правила. Некоторые из них, например [`@media`][] и [`@supports`][], также позволяют использовать SassScript непосредственно в самом правиле без интерполяции.

[обычных at-правил CSS]: https://sass-lang.com/documentation/at-rules/css
[интерполяцию]: https://sass-lang.com/documentation/interpolation
[`@media`]: https://sass-lang.com/documentation/at-rules/css#media
[`@supports`]: https://sass-lang.com/documentation/at-rules/css#supports

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

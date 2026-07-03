# Структура таблицы стилей

Как и в CSS, большинство таблиц стилей Sass в основном состоят из стилевых
правил, содержащих объявления свойств. Но в таблицах стилей Sass есть
гораздо больше возможностей, которые могут существовать наряду с ними.

## Инструкции

Таблица стилей Sass состоит из последовательности _инструкций_, которые
вычисляются по порядку для построения результирующего CSS. Некоторые
инструкции могут иметь _блоки_, заданные с помощью `{` и `}`, которые
содержат другие инструкции. Например, стилевое правило — это инструкция с
блоком. Этот блок содержит другие инструкции, такие как объявления свойств.

В SCSS инструкции разделяются точкой с запятой (она необязательна, если
инструкция использует блок). В отступном синтаксисе они просто разделяются
переводами строк. Хотя в отступном синтаксисе можно поставить точку с
запятой в конце инструкции, перевод строки всё равно обязателен.

### Универсальные инструкции

Эти типы инструкций можно использовать где угодно в таблице стилей Sass:

- [Объявления переменных](https://sass-lang.com/documentation/variables), например `$var: value`.
- [At-правила управления потоком выполнения](https://sass-lang.com/documentation/at-rules/control), такие как `@if` и
  `@each`.
- Правила [`@error`](https://sass-lang.com/documentation/at-rules/error),
  [`@warn`](https://sass-lang.com/documentation/at-rules/warn) и
  [`@debug`](https://sass-lang.com/documentation/at-rules/debug).

### CSS-инструкции

Эти инструкции порождают CSS. Их можно использовать где угодно, кроме как
внутри `@function`:

- [Стилевые правила](https://sass-lang.com/documentation/style-rules), например `h1 { /* ... */ }`.
- [At-правила CSS](https://sass-lang.com/documentation/at-rules/css), такие как `@media` и `@font-face`.
- [Использования миксинов](https://sass-lang.com/documentation/at-rules/mixin) с помощью `@include`.
- Правило [`@at-root`](https://sass-lang.com/documentation/at-rules/at-root).

### Инструкции верхнего уровня

Эти инструкции можно использовать только на верхнем уровне таблицы стилей
либо вложенными в CSS-инструкцию на верхнем уровне:

- [Загрузки модулей](https://sass-lang.com/documentation/at-rules/use) с помощью `@use`.
- [Импорты](https://sass-lang.com/documentation/at-rules/import) с помощью `@import`.
- [Определения миксинов](https://sass-lang.com/documentation/at-rules/mixin) с помощью `@mixin`.
- [Определения функций](https://sass-lang.com/documentation/at-rules/function) с помощью `@function`.

### Прочие инструкции

- [Объявления свойств](https://sass-lang.com/documentation/style-rules/declarations), например `width:
100px`, можно использовать только внутри стилевых правил и некоторых at-правил CSS.
- Правило [`@extend`](https://sass-lang.com/documentation/at-rules/extend) можно использовать только внутри
  стилевых правил.

## Выражения

_Выражение_ — это всё, что стоит в правой части объявления свойства или
переменной. Каждое выражение производит _[значение][]_. Любое допустимое
значение свойства CSS также является выражением Sass, но выражения Sass
гораздо мощнее обычных значений CSS. Они передаются в качестве аргументов
[миксинам][] и [функциям][], используются для управления потоком выполнения
с помощью [правила `@if`][], и обрабатываются с помощью [арифметики][]. Мы
называем синтаксис выражений Sass — _SassScript_.

[значение]: https://sass-lang.com/documentation/values
[миксинам]: https://sass-lang.com/documentation/at-rules/mixin
[функциям]: https://sass-lang.com/documentation/at-rules/function
[правила `@if`]: https://sass-lang.com/documentation/at-rules/control/if
[арифметики]: https://sass-lang.com/documentation/operators/numeric

### Литералы

Простейшие выражения просто представляют статические значения:

- [Числа](https://sass-lang.com/documentation/values/numbers), которые могут иметь единицы измерения, а могут и не
  иметь, например `12` или `100px`.
- [Строки](https://sass-lang.com/documentation/values/strings), которые могут быть в кавычках, а могут быть без них,
  например `"Helvetica Neue"` или `bold`.
- [Цвета](https://sass-lang.com/documentation/values/colors), на которые можно ссылаться по их шестнадцатеричному
  представлению или по имени, например `#c6538c` или `blue`.
- [Логические](https://sass-lang.com/documentation/values/booleans) литералы `true` или `false`.
- Единственное значение [`null`](https://sass-lang.com/documentation/values/null).
- [Списки значений](https://sass-lang.com/documentation/values/lists), которые могут разделяться пробелами или
  запятыми и могут быть заключены в квадратные скобки либо вообще без скобок,
  например `1.5em 1em 0 2em`, `Helvetica, Arial, sans-serif` или
  `[col1-start]`.
- [Карты (map)](https://sass-lang.com/documentation/values/maps), связывающие значения с ключами, например
  `("background": red, "foreground": pink)`.

### Операции

Sass определяет синтаксис для ряда операций:

- [`==` и `!=`](https://sass-lang.com/documentation/operators/equality) используются для проверки равенства двух
  значений.
- [`+`, `-`, `*`, `/` и `%`](https://sass-lang.com/documentation/operators/numeric) имеют обычное математическое
  значение для чисел, с особым поведением для единиц измерения,
  соответствующим использованию единиц измерения в научной математике.
- [`<`, `<=`, `>` и `>=`](https://sass-lang.com/documentation/operators/relational) проверяют, больше или меньше
  одно число по сравнению с другим.
- [`and`, `or` и `not`](https://sass-lang.com/documentation/operators/boolean) ведут себя как обычные логические
  операторы. Sass считает «истинным» любое значение, кроме `false` и `null`.
- [`+`, `-` и `/`](https://sass-lang.com/documentation/operators/string) можно использовать для конкатенации
  строк.

- [`(` и `)`](https://sass-lang.com/documentation/operators#parentheses) можно использовать для явного управления
  порядком выполнения операций.



### Прочие выражения

- [Переменные](https://sass-lang.com/documentation/variables), например `$var`.
- [Вызовы функций](https://sass-lang.com/documentation/at-rules/function), например `nth($list, 1)` или
  `var(--main-bg-color)`, которые могут вызывать функции основной библиотеки
  Sass или функции, определённые пользователем, либо компилироваться
  напрямую в CSS.
- [Специальные функции](https://sass-lang.com/documentation/syntax/special-functions), например
  `calc(1px + 100%)` или `url(http://myapp.com/assets/logo.png)`, у которых
  свои особые правила разбора.
- [Родительский селектор](https://sass-lang.com/documentation/style-rules/parent-selector), `&`.
- Значение `!important`, которое разбирается как строка без кавычек.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/syntax/structure/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

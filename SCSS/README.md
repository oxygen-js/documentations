# Документация Sass — перевод на русский

Перевод официальной документации [Sass](https://sass-lang.com/documentation/) на
русский язык. Структура папок повторяет структуру официального сайта. В примерах
кода сохранён исходный код SCSS/Sass и приведён скомпилированный CSS.

> Источник: <https://sass-lang.com/documentation/>. Оригинальный текст
> распространяется по лицензии MIT (© Sass team). Перевод неофициальный.

## Оглавление

- [Обзор](overview.md)

### Синтаксис
- [Обзор](syntax/overview.md)
- [Парсинг таблицы стилей](syntax/parsing.md)
- [Структура таблицы стилей](syntax/structure.md)
- [Комментарии](syntax/comments.md)
- [Специальные функции](syntax/special-functions.md)

### Стилевые правила
- [Обзор](style-rules/overview.md)
- [Объявления свойств](style-rules/declarations.md)
- [Родительский селектор](style-rules/parent-selector.md)
- [Селекторы-заглушки](style-rules/placeholder-selectors.md)

### Прочее
- [Переменные](variables.md)
- [Интерполяция](interpolation.md)

### At-правила
- [Обзор](at-rules/overview.md)
- [`@use`](at-rules/use.md)
- [`@forward`](at-rules/forward.md)
- [`@import`](at-rules/import.md)
- [`@mixin` и `@include`](at-rules/mixin.md)
- [`@function`](at-rules/function.md)
- [`@extend`](at-rules/extend.md)
- [`@error`](at-rules/error.md)
- [`@warn`](at-rules/warn.md)
- [`@debug`](at-rules/debug.md)
- [`@at-root`](at-rules/at-root.md)
- Управление потоком
  - [Обзор](at-rules/control/overview.md)
  - [`@if` и `@else`](at-rules/control/if.md)
  - [`@each`](at-rules/control/each.md)
  - [`@for`](at-rules/control/for.md)
  - [`@while`](at-rules/control/while.md)
- [Из CSS](at-rules/css.md)

### Значения
- [Обзор](values/overview.md)
- [Числа](values/numbers.md)
- [Строки](values/strings.md)
- [Цвета](values/colors.md)
- [Списки](values/lists.md)
- [Карты](values/maps.md)
- [`true` и `false`](values/booleans.md)
- [`null`](values/null.md)
- [Вычисления](values/calculations.md)
- [Функции](values/functions.md)
- [Миксины](values/mixins.md)

### Операторы
- [Обзор](operators/overview.md)
- [Равенство](operators/equality.md)
- [Отношения](operators/relational.md)
- [Числовые](operators/numeric.md)
- [Строковые](operators/string.md)
- [Логические](operators/boolean.md)

### Встроенные модули
- [Обзор](modules/overview.md)
- [`sass:color`](modules/color.md)
- [`sass:list`](modules/list.md)
- [`sass:map`](modules/map.md)
- [`sass:math`](modules/math.md)
- [`sass:meta`](modules/meta.md)
- [`sass:selector`](modules/selector.md)
- [`sass:string`](modules/string.md)

### Ломающие изменения
- [Обзор](breaking-changes/overview.md)
- [Смежные составные селекторы](breaking-changes/adjacent-compounds.md)
- [Имена специальных функций](breaking-changes/function-name.md)
- [Устаревшая функция `if()`](breaking-changes/if-function.md)
- [Приватная конфигурация](breaking-changes/with-private.md)
- [Неуместные rest-аргументы](breaking-changes/misplaced-rest.md)
- [Функция `type()`](breaking-changes/type-function.md)
- [`@import`](breaking-changes/import.md)
- [Строгие унарные операторы](breaking-changes/strict-unary.md)
- [Недопустимые комбинаторы](breaking-changes/bogus-combinators.md)
- [Media Queries Level 4](breaking-changes/media-logic.md)
- [`/` как деление](breaking-changes/slash-div.md)
- [Единицы измерения в функциях](breaking-changes/function-units.md)
- [`-moz-document`](breaking-changes/moz-document.md)
- [Расширение составных селекторов](breaking-changes/extend-compound.md)
- [Синтаксис CSS-переменных](breaking-changes/css-vars.md)
- [Дублирование флагов переменных](breaking-changes/duplicate-var-flags.md)
- [Экспорты по умолчанию](breaking-changes/default-export.md)
- [Нулевой альфа-канал](breaking-changes/null-alpha.md)
- [Проценты в `abs()`](breaking-changes/abs-percent.md)
- [Функции и миксины, начинающиеся с `--`](breaking-changes/css-function-mixin.md)
- [Смешанные объявления](breaking-changes/mixed-decls.md)
- [`meta.feature-exists`](breaking-changes/feature-exists.md)
- [Функции работы с цветом](breaking-changes/color-functions.md)
- [Color JS API](breaking-changes/color-4-api.md)
- [Устаревший JS API](breaking-changes/legacy-js-api.md)

### Командная строка
- [Обзор](cli/overview.md)
- [Dart Sass](cli/dart-sass.md)
- [Ruby Sass](cli/ruby-sass.md)
- [Migrator](cli/migrator.md)

### JavaScript API
- Справочник [JavaScript API](https://sass-lang.com/documentation/js-api/) генерируется
  автоматически (TypeDoc) и не входит в этот перевод — см. официальный сайт.

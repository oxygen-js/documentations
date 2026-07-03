# Ломающие изменения

Новые версии Sass максимально стараются сохранять обратную совместимость, но иногда ломающие изменения необходимы. Sass должен идти в ногу с развивающейся спецификацией CSS, а старые ошибки в дизайне языка порой нужно исправлять.

Перед выпуском каждого ломающего изменения реализации Sass будут выводить предупреждения об устаревании для таблиц стилей, поведение которых изменится. По возможности эти предупреждения будут содержать рекомендации о том, как обновить устаревшие стили, чтобы сделать их совместимыми с будущими версиями.

У разных реализаций разная политика в отношении ломающих изменений и устареваний. [Dart Sass][] будет выводить предупреждения об устаревании как минимум три месяца до выпуска ломающего изменения и будет выпускать такие изменения с новым major-номером версии, **если только это изменение не требуется для совместимости с CSS**. Изменения ради совместимости с CSS часто одновременно и ненавязчивы, и чувствительны ко времени, поэтому вместо этого они могут выпускаться с новым minor-номером версии.

[Dart Sass]: https://sass-lang.com/dart-sass

Эти ломающие изменения скоро появятся или были выпущены недавно:

* [Смежные составные селекторы](https://sass-lang.com/documentation/breaking-changes/adjacent-compounds) начиная с
  Dart Sass 1.100.0.

* [Дополнительные имена обычных CSS-функций](https://sass-lang.com/documentation/breaking-changes/function-name/) начиная с Dart Sass
  1.98.0

* [Устаревшая функция `if()`](https://sass-lang.com/documentation/breaking-changes/if-function/)
  начиная с Dart Sass 1.95.0

* [Приватные переменные в `with`](https://sass-lang.com/documentation/breaking-changes/with-private/)
  начиная с Dart Sass 1.92.0

* [Rest-аргументы не на своём месте](https://sass-lang.com/documentation/breaking-changes/misplaced-rest/)
  начиная с Dart Sass 1.91.0

* [Функция `type()`](https://sass-lang.com/documentation/breaking-changes/type-function/) начиная с Dart
  Sass 1.86.0

* [`@import`](https://sass-lang.com/documentation/breaking-changes/import/) начиная с Dart
  Sass 1.80.0

* [Устаревший JS API](https://sass-lang.com/documentation/breaking-changes/legacy-js-api/) начиная
  с Dart Sass 1.79.0.

* [Некоторые способы использования JS Color API объявлены
  устаревшими](https://sass-lang.com/documentation/breaking-changes/color-4-api/) начиная с Dart
  Sass 1.79.0.

* [Ряд цветовых функций объявлен
  устаревшим](https://sass-lang.com/documentation/breaking-changes/color-functions/) начиная с
  Dart Sass 1.79.0, в пользу новых функций, совместимых с CSS Color 4.

* [Функция `meta.feature-exists()` объявлена
  устаревшей](https://sass-lang.com/documentation/breaking-changes/feature-exists/) начиная с Dart
  Sass 1.78.0.

* [Смешивание объявлений свойств с вложенными правилами меняет
  поведение](https://sass-lang.com/documentation/breaking-changes/mixed-decls/) начиная с Dart Sass
  1.77.7.

* [Функции и миксины, начинающиеся с `--`, объявлены
  устаревшими](https://sass-lang.com/documentation/breaking-changes/css-function-mixin/) начиная с Dart
  Sass 1.76.0.

* [Передача процентной единицы в глобальную функцию `abs()` объявлена
  устаревшей](https://sass-lang.com/documentation/breaking-changes/abs-percent/) начиная с Dart
  Sass 1.65.0.

* [Передача `null` в качестве альфа-канала в `new SassColor()` меняет
  поведение](https://sass-lang.com/documentation/breaking-changes/null-alpha) начиная с Dart
  Sass 1.64.3.

* [Загрузка Sass как экспорта по умолчанию в JS больше не
  допускается](https://sass-lang.com/documentation/breaking-changes/default-export) начиная с Dart
  Sass 1.63.0.

* [У переменной может быть только один флаг `!global` или
  `!default`](https://sass-lang.com/documentation/breaking-changes/duplicate-var-flags) начиная с Dart
  Sass 1.62.0.

* [Строгие унарные операторы](https://sass-lang.com/documentation/breaking-changes/strict-unary/)
  начиная с Dart Sass 1.55.0.

* [Media Queries Level 4](https://sass-lang.com/documentation/breaking-changes/media-logic/) начиная с Dart
  Sass 1.54.0, с завершением периода устаревания в Dart Sass 1.56.0.

* [Селекторы с недопустимыми комбинаторами становятся
  недопустимыми](https://sass-lang.com/documentation/breaking-changes/bogus-combinators) начиная с Dart
  Sass 1.54.0.

* [`/` меняется с операции деления на разделитель
  списка](https://sass-lang.com/documentation/breaking-changes/slash-div) начиная с Dart Sass
  1.33.0.

* [Функции стали строже в отношении допустимых единиц
  измерения](https://sass-lang.com/documentation/breaking-changes/function-units) начиная с Dart Sass
  1.32.0.

* [Разбор особого синтаксиса `@-moz-document` станет
  недопустимым](https://sass-lang.com/documentation/breaking-changes/moz-document) начиная с Dart Sass
  1.7.2.

* [Составные селекторы нельзя было
  расширять](https://sass-lang.com/documentation/breaking-changes/extend-compound) в Dart Sass 1.0.0
  и Ruby Sass 4.0.0.

* [Синтаксис значений пользовательских CSS-свойств
  изменился](https://sass-lang.com/documentation/breaking-changes/css-vars) в Dart Sass 1.0.0, LibSass
  3.5.0 и Ruby Sass 3.5.0.

## Досрочное включение

Пользователи Dart Sass могут заранее включить режим, в котором устаревания трактуются как ошибки, с помощью [параметра командной строки
`--fatal-deprecation`](https://sass-lang.com/documentation/cli/dart-sass/#fatal-deprecation).

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

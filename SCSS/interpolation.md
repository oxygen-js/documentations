# Интерполяция

Интерполяцию можно использовать почти в любом месте таблицы стилей Sass, чтобы
встроить результат [выражения SassScript](https://sass-lang.com/documentation/syntax/structure#expressions) в
фрагмент CSS. Просто оберните выражение в `#{}` в любом из следующих мест:

* [Селекторы в стилевых правилах](https://sass-lang.com/documentation/style-rules#interpolation)
* [Имена свойств в объявлениях](https://sass-lang.com/documentation/style-rules/declarations#interpolation)
* [Значения пользовательских свойств](https://sass-lang.com/documentation/style-rules/declarations#custom-properties)
* [CSS at-правила](https://sass-lang.com/documentation/at-rules/css)
* [Инструкции `@extend`](https://sass-lang.com/documentation/at-rules/extend)
* [Обычные CSS-инструкции `@import`](https://sass-lang.com/documentation/at-rules/import/#plain-css-imports)
* [Строки в кавычках или без кавычек](https://sass-lang.com/documentation/values/strings)
* [Специальные функции](https://sass-lang.com/documentation/syntax/special-functions)
* [Имена обычных CSS-функций](https://sass-lang.com/documentation/at-rules/function/#plain-css-functions)
* [Громкие комментарии](https://sass-lang.com/documentation/syntax/comments)

**SCSS**

```scss
@mixin corner-icon($name, $top-or-bottom, $left-or-right) {
  .icon-#{$name} {
    background-image: url("/icons/#{$name}.svg");
    position: absolute;
    #{$top-or-bottom}: 0;
    #{$left-or-right}: 0;
  }
}

@include corner-icon("mail", top, left);
```

**Sass**

```sass
@mixin corner-icon($name, $top-or-bottom, $left-or-right)
  .icon-#{$name}
    background-image: url("/icons/#{$name}.svg")
    position: absolute
    #{$top-or-bottom}: 0
    #{$left-or-right}: 0



@include corner-icon("mail", top, left)
```

**Результат CSS**

```css
.icon-mail {
  background-image: url("/icons/mail.svg");
  position: absolute;
  top: 0;
  left: 0;
}
```

## В SassScript

**Совместимость (современный синтаксис):** Dart Sass: ✓ · LibSass: ✗ · Ruby Sass: начиная с 4.0.0 (не выпущено)

LibSass и Ruby Sass в настоящее время используют более старый синтаксис разбора
  интерполяции в SassScript. В большинстве практических случаев результат совпадает,
  но интерполяция может вести себя странно в сочетании с [операторами][]. Подробности —
  в [этом документе][].

  [операторами]: https://sass-lang.com/documentation/operators
  [этом документе]: https://github.com/sass/sass/blob/main/accepted/free-interpolation.md#old-interpolation-rules


Интерполяцию можно использовать в SassScript, чтобы подставить SassScript в [строки
без кавычек][]. Это особенно полезно при динамической генерации имён (например, для
анимаций) или при использовании [значений, разделённых слэшем][]. Обратите внимание:
интерполяция в SassScript всегда возвращает строку без кавычек.

[строки без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted
[значений, разделённых слэшем]: https://sass-lang.com/documentation/operators/numeric#slash-separated-values

<!-- Add explicit CSS here to prevent diffs due to the use of unique-id -->

**SCSS**

```scss
@mixin inline-animation($duration) {
  $name: inline-#{unique-id()};

  @keyframes #{$name} {
    @content;
  }

  animation-name: $name;
  animation-duration: $duration;
  animation-iteration-count: infinite;
}

.pulse {
  @include inline-animation(2s) {
    from { background-color: yellow }
    to { background-color: red }
  }
}
```

**Sass**

```sass
@mixin inline-animation($duration)
  $name: inline-#{unique-id()}

  @keyframes #{$name}
    @content


  animation-name: $name
  animation-duration: $duration
  animation-iteration-count: infinite


.pulse
  @include inline-animation(2s)
    from
      background-color: yellow
    to
      background-color: red
```

**Результат CSS**

```css
.pulse {
  animation-name: inline-uifpe6h;
  animation-duration: 2s;
  animation-iteration-count: infinite;
}
@keyframes inline-uifpe6h {
  from {
    background-color: yellow;
  }
  to {
    background-color: red;
  }
}
```

> 💡 **Занятный факт**
>
> Интерполяция полезна для подстановки значений в строки, но помимо этого она редко
> бывает нужна в выражениях SassScript. Вам точно *не* нужно использовать её, чтобы
> просто использовать переменную в значении свойства. Вместо того чтобы писать `color:
> #{$accent}`, можно просто написать `color: $accent`!

> ⚠️ **Внимание**
>
> Использовать интерполяцию с числами почти всегда плохая идея. Интерполяция возвращает
> строки без кавычек, которые нельзя использовать для дальнейших вычислений, а ещё это
> отключает встроенные защитные механизмы Sass, гарантирующие правильное использование
> единиц измерения.
>
> В Sass есть мощная [арифметика единиц измерения][], которую можно использовать вместо
> этого. Например, вместо того чтобы писать `#{$width}px`, напишите `$width * 1px` — а
> ещё лучше сразу объявите переменную `$width` в единицах `px`. Тогда, если у `$width`
> уже есть единицы измерения, вы получите понятное сообщение об ошибке вместо компиляции
> некорректного CSS.

[арифметика единиц измерения]: https://sass-lang.com/documentation/values/numbers#units

## Строки в кавычках

В большинстве случаев интерполяция вставляет точно такой же текст, какой использовался
бы, если бы выражение было [значением свойства][]. Но есть одно исключение: кавычки
вокруг строк в кавычках удаляются (даже если эти строки в кавычках находятся внутри
списков). Это позволяет писать строки в кавычках, содержащие синтаксис, не допустимый
в SassScript (например, селекторы), и подставлять их в стилевые правила с помощью
интерполяции.

[значением свойства]: https://sass-lang.com/documentation/style-rules/declarations

**SCSS**

```scss
.example {
  unquoted: #{"string"};
}
```

**Sass**

```sass
.example
  unquoted: #{"string"}
```

**Результат CSS**

```css
.example {
  unquoted: string;
}
```

> ⚠️ **Внимание**
>
> Хотя может возникнуть соблазн использовать эту возможность для преобразования строк
> в кавычках в строки без кавычек, гораздо понятнее использовать [функцию
> `string.unquote()`][]. Вместо `#{$string}` напишите `string.unquote($string)`!

[функцию `string.unquote()`]: https://sass-lang.com/documentation/modules/string#unquote

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/interpolation/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

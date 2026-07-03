# @warn

При написании [миксинов](https://sass-lang.com/documentation/at-rules/mixin) и
[функций](https://sass-lang.com/documentation/at-rules/function) вы можете захотеть
отговорить пользователей от передачи определённых аргументов или определённых значений.
Возможно, они передают устаревшие аргументы, которые теперь считаются нежелательными,
или вызывают ваш API не самым оптимальным способом.

Именно для этого предназначено правило `@warn`. Оно записывается как `@warn <expression>`
и выводит для пользователя значение [выражения][expression] (обычно строки) вместе со
стеком вызовов, показывающим, как был вызван текущий миксин или функция. В отличие от
[правила `@error`][`@error` rule], оно не останавливает работу Sass полностью.

[expression]: https://sass-lang.com/documentation/syntax/structure#expressions
[`@error` rule]: https://sass-lang.com/documentation/at-rules/error

**SCSS**

```scss
$known-prefixes: webkit, moz, ms, o;

@mixin prefix($property, $value, $prefixes) {
  @each $prefix in $prefixes {
    @if not index($known-prefixes, $prefix) {
      @warn "Unknown prefix #{$prefix}.";
    }

    -#{$prefix}-#{$property}: $value;
  }
  #{$property}: $value;
}

.tilt {
  // Oops, we typo'd "webkit" as "wekbit"!
  @include prefix(transform, rotate(15deg), wekbit ms);
}
```

**Sass**

```sass
$known-prefixes: webkit, moz, ms, o

@mixin prefix($property, $value, $prefixes)
  @each $prefix in $prefixes
    @if not index($known-prefixes, $prefix)
      @warn "Unknown prefix #{$prefix}."


    -#{$prefix}-#{$property}: $value

  #{$property}: $value


.tilt
  // Oops, we typo'd "webkit" as "wekbit"!
  @include prefix(transform, rotate(15deg), wekbit ms)
```

**Результат CSS**

```css
.tilt {
  -wekbit-transform: rotate(15deg);
  -ms-transform: rotate(15deg);
  transform: rotate(15deg);
}
```

Точный формат предупреждения и стека вызовов различается в зависимости от реализации.
Вот как это выглядит в Dart Sass:

```
Warning: Unknown prefix wekbit.
    example.scss 6:7   prefix()
    example.scss 16:3  root stylesheet
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/warn/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

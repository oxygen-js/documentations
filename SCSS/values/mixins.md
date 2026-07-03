# Значения-миксины

**Совместимость:** Dart Sass: начиная с 1.69.0 · LibSass: ✗ · Ruby Sass: ✗

[Миксины][] тоже могут быть значениями! Вы не можете написать миксин как значение
напрямую, но можете передать имя миксина в [функцию `meta.get-mixin()`][], чтобы
получить его как значение. Получив значение-миксин, вы можете передать его в
[миксин `meta.apply()`][], чтобы вызвать его. Это нужно для того, чтобы библиотеки
могли расширяться сложными и мощными способами.

[Миксины]: https://sass-lang.com/documentation/at-rules/mixin
[функцию `meta.get-mixin()`]: https://sass-lang.com/documentation/modules/meta#get-mixin
[миксин `meta.apply()`]: https://sass-lang.com/documentation/modules/meta#apply

**SCSS**

```scss
@use "sass:meta";
@use "sass:string";

/// Passes each element of $list to a separate invocation of $mixin.
@mixin apply-to-all($mixin, $list) {
  @each $element in $list {
    @include meta.apply($mixin, $element);
  }
}

@mixin font-class($size) {
  .font-#{$size} {
    font-size: $size;
  }
}

$sizes: [8px, 12px, 2rem];

@include apply-to-all(meta.get-mixin("font-class"), $sizes);
```

**Sass**

```sass
@use "sass:meta"
@use "sass:string"

/// Passes each element of $list to a separate invocation of $mixin.
@mixin apply-to-all($mixin, $list)
  @each $element in $list
    @include meta.apply($mixin, $element)



@mixin font-class($size)
  .font-#{$size}
    font-size: $size



$sizes: 8px, 12px 2rem

@include apply-to-all(meta.get-mixin("font-class"), $sizes)
```

**CSS output**

```css
.font-8px {
  font-size: 8px;
}

.font-12px {
  font-size: 12px;
}

.font-2rem {
  font-size: 2rem;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/mixins/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

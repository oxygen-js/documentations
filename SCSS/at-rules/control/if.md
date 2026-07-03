# @if и @else

Правило `@if` записывается как `@if <expression> { ... }` и определяет, будет ли
выполнен его блок (в том числе будут ли выведены какие-либо стили в виде CSS).
[Выражение](https://sass-lang.com/documentation/syntax/structure#expressions) обычно
возвращает [`true` или `false`](https://sass-lang.com/documentation/values/booleans) —
если выражение возвращает `true`, блок выполняется, а если оно возвращает `false` —
не выполняется.

**SCSS**

```scss
@use "sass:math";

@mixin avatar($size, $circle: false) {
  width: $size;
  height: $size;

  @if $circle {
    border-radius: math.div($size, 2);
  }
}

.square-av {
  @include avatar(100px, $circle: false);
}
.circle-av {
  @include avatar(100px, $circle: true);
}
```

**Sass**

```sass
@use "sass:math"

@mixin avatar($size, $circle: false)
  width: $size
  height: $size

  @if $circle
    border-radius: math.div($size, 2)



.square-av
  @include avatar(100px, $circle: false)

.circle-av
  @include avatar(100px, $circle: true)
```

**CSS output**

```css
.square-av {
  width: 100px;
  height: 100px;
}

.circle-av {
  width: 100px;
  height: 100px;
  border-radius: 50px;
}
```


## `@else`

После правила `@if` может опционально следовать правило `@else`, записываемое как
`@else { ... }`. Блок этого правила выполняется, если выражение `@if` возвращает
`false`.

**SCSS**

```scss
$light-background: #f2ece4;
$light-text: #036;
$dark-background: #6b717f;
$dark-text: #d2e1dd;

@mixin theme-colors($light-theme: true) {
  @if $light-theme {
    background-color: $light-background;
    color: $light-text;
  } @else {
    background-color: $dark-background;
    color: $dark-text;
  }
}

.banner {
  @include theme-colors($light-theme: true);
  body.dark & {
    @include theme-colors($light-theme: false);
  }
}
```

**Sass**

```sass
$light-background: #f2ece4
$light-text: #036
$dark-background: #6b717f
$dark-text: #d2e1dd

@mixin theme-colors($light-theme: true)
  @if $light-theme
    background-color: $light-background
    color: $light-text
  @else
    background-color: $dark-background
    color: $dark-text



.banner
  @include theme-colors($light-theme: true)
  body.dark &
    @include theme-colors($light-theme: false)
```

**CSS output**

```css
.banner {
  background-color: #f2ece4;
  color: #036;
}
body.dark .banner {
  background-color: #6b717f;
  color: #d2e1dd;
}
```

Условные выражения могут содержать [логические операторы][] (`and`, `or`, `not`).

[логические операторы]: https://sass-lang.com/documentation/operators/boolean

### `@else if`

Вы также можете указать условие выполнения блока правила `@else`, записав его как
`@else if <expression> { ... }`. В этом случае блок выполняется только если выражение
предшествующего `@if` возвращает `false` *и* выражение `@else if` возвращает `true`.

На самом деле, после `@if` можно указать сколько угодно `@else if` подряд. Будет
выполнен первый блок в цепочке, чьё выражение вернёт `true`, и никакой другой. Если
в конце цепочки есть обычный `@else`, его блок будет выполнен, если все остальные
блоки не сработают.

**SCSS**

```scss
@use "sass:math";

@mixin triangle($size, $color, $direction) {
  height: 0;
  width: 0;

  border-color: transparent;
  border-style: solid;
  border-width: math.div($size, 2);

  @if $direction == up {
    border-bottom-color: $color;
  } @else if $direction == right {
    border-left-color: $color;
  } @else if $direction == down {
    border-top-color: $color;
  } @else if $direction == left {
    border-right-color: $color;
  } @else {
    @error "Unknown direction #{$direction}.";
  }
}

.next {
  @include triangle(5px, black, right);
}
```

**Sass**

```sass
@use "sass:math"

@mixin triangle($size, $color, $direction)
  height: 0
  width: 0

  border-color: transparent
  border-style: solid
  border-width: math.div($size, 2)

  @if $direction == up
    border-bottom-color: $color
  @else if $direction == right
    border-left-color: $color
  @else if $direction == down
    border-top-color: $color
  @else if $direction == left
    border-right-color: $color
  @else
    @error "Unknown direction #{$direction}."



.next
  @include triangle(5px, black, right)
```

**CSS output**

```css
.next {
  height: 0;
  width: 0;
  border-color: transparent;
  border-style: solid;
  border-width: 2.5px;
  border-left-color: black;
}
```

## Истинность и ложность

Везде, где допустимы значения `true` или `false`, можно использовать и другие
значения. Значения `false` и [`null`][] являются *ложными* (falsey) — это означает,
что Sass считает их признаком ложности и они приводят к невыполнению условий. Любое
другое значение считается *истинным* (truthy), поэтому Sass рассматривает такие
значения как аналог `true`, и они приводят к выполнению условий.

[`null`]: https://sass-lang.com/documentation/values/null

Например, если вы хотите проверить, содержит ли строка пробел, можно просто написать
`string.index($string, " ")`. [Функция `string.index()`][] возвращает `null`, если
строка не найдена, и число в противном случае.

[Функция `string.index()`]: https://sass-lang.com/documentation/modules/string#index

> ⚠️ **Внимание**
>
> В некоторых языках ложными считаются и другие значения, помимо `false` и `null`.
> Sass — не один из таких языков! Пустые строки, пустые списки и число `0` — все
> они истинны в Sass.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/control/if/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

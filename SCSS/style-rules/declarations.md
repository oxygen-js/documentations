# Объявления свойств

В Sass, как и в CSS, объявления свойств определяют, как стилизуются элементы,
соответствующие селектору. Но Sass добавляет дополнительные возможности, чтобы
писать их было проще и чтобы их можно было автоматизировать. Прежде всего,
значением объявления может быть любое
[выражение SassScript](https://sass-lang.com/documentation/syntax/structure#expressions), которое
будет вычислено и включено в результат.

**SCSS**

```scss
.circle {
  $size: 100px;
  width: $size;
  height: $size;
  border-radius: $size * 0.5;
}
```

**Sass**

```sass
.circle
  $size: 100px
  width: $size
  height: $size
  border-radius: $size * 0.5
```

**Результат CSS**

```css
.circle {
  width: 100px;
  height: 100px;
  border-radius: 50px;
}
```

## Интерполяция

Имя свойства может включать [интерполяцию][interpolation], что делает возможным
динамически генерировать свойства по мере необходимости. Можно даже
интерполировать имя свойства целиком!

[interpolation]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
@mixin prefix($property, $value, $prefixes) {
  @each $prefix in $prefixes {
    -#{$prefix}-#{$property}: $value;
  }
  #{$property}: $value;
}

.gray {
  @include prefix(filter, grayscale(50%), moz webkit);
}
```

**Sass**

```sass
@mixin prefix($property, $value, $prefixes)
  @each $prefix in $prefixes
    -#{$prefix}-#{$property}: $value

  #{$property}: $value


.gray
  @include prefix(filter, grayscale(50%), moz webkit)
```

**Результат CSS**

```css
.gray {
  -moz-filter: grayscale(50%);
  -webkit-filter: grayscale(50%);
  filter: grayscale(50%);
}
```

## Вложенность

Многие свойства CSS начинаются с одинакового префикса, который выступает
своего рода пространством имён. Например, `font-family`, `font-size` и
`font-weight` начинаются с `font-`. Sass упрощает это и делает менее
избыточным, позволяя вкладывать объявления свойств друг в друга. Имена внешних
свойств добавляются к внутренним через дефис.

**SCSS**

```scss
.enlarge {
  font-size: 14px;
  transition: {
    property: font-size;
    duration: 4s;
    delay: 2s;
  }

  &:hover { font-size: 36px; }
}
```

**Sass**

```sass
.enlarge
  font-size: 14px
  transition:
    property: font-size
    duration: 4s
    delay: 2s

  &:hover
    font-size: 36px
```

**Результат CSS**

```css
.enlarge {
  font-size: 14px;
  transition-property: font-size;
  transition-duration: 4s;
  transition-delay: 2s;
}
.enlarge:hover {
  font-size: 36px;
}
```

У некоторых из этих свойств CSS есть сокращённые версии, которые используют
пространство имён в качестве имени свойства. Для них можно записать сразу
*и* значение в сокращённой записи, *и* более явные вложенные версии.

**SCSS**

```scss
.info-page {
  margin: auto {
    bottom: 10px;
    top: 2px;
  }
}
```

**Sass**

```sass
.info-page
  margin: auto
    bottom: 10px
    top: 2px
```

**Результат CSS**

```css
.info-page {
  margin: auto;
  margin-bottom: 10px;
  margin-top: 2px;
}
```

## Скрытые объявления

Иногда нужно, чтобы объявление свойства появлялось лишь в некоторых случаях.
Если значением объявления является [`null`][] или пустая [строка без
кавычек][unquoted string], Sass вообще не скомпилирует это объявление в CSS.

[`null`]: https://sass-lang.com/documentation/values/null
[unquoted string]: https://sass-lang.com/documentation/values/strings#unquoted

**SCSS**

```scss
$rounded-corners: false;

.button {
  border: 1px solid black;
  border-radius: if(sass($rounded-corners): 5px);
}
```

**Sass**

```sass
$rounded-corners: false

.button
  border: 1px solid black
  border-radius: if(sass($rounded-corners): 5px)
```

**Результат CSS**

```css
.button {
  border: 1px solid black;
}
```

## Пользовательские свойства

**Совместимость (SassScript Syntax):** Dart Sass: ✓ · LibSass: начиная с 3.5.0 · Ruby Sass: начиная с 3.5.0

Более старые версии LibSass и Ruby Sass разбирали объявления пользовательских
  свойств так же, как и любые другие объявления свойств, допуская полный набор
  выражений SassScript в качестве значений. Даже при использовании этих версий
  рекомендуется использовать интерполяцию для внедрения значений SassScript —
  ради совместимости с будущими версиями.

  Подробнее см. [страницу о ломающих изменениях][the breaking change page].

  [the breaking change page]: https://sass-lang.com/documentation/breaking-changes/css-vars


[Пользовательские свойства CSS][CSS custom properties], также известные как
переменные CSS, имеют необычный синтаксис объявления: они допускают почти
любой текст в качестве значения объявления. Более того, эти значения доступны
из JavaScript, поэтому любое значение потенциально может быть важно для
пользователя. Это касается и значений, которые обычно разбирались бы как
SassScript.

[CSS Custom Properties]: https://developer.mozilla.org/en-US/docs/Web/CSS/--*

Из-за этого Sass разбирает объявления пользовательских свойств иначе, чем
остальные объявления свойств. Все токены, включая те, что выглядят как
SassScript, передаются в CSS как есть. Единственное исключение —
[интерполяция][interpolation], которая является единственным способом внедрить
динамические значения в пользовательское свойство.

[interpolation]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
$primary: #81899b;
$accent: #302e24;
$warn: #dfa612;

:root {
  --primary: #{$primary};
  --accent: #{$accent};
  --warn: #{$warn};

  // Even though this looks like a Sass variable, it's valid CSS so it's not
  // evaluated.
  --consumed-by-js: $primary;
}
```

**Sass**

```sass
$primary: #81899b
$accent: #302e24
$warn: #dfa612

:root
  --primary: #{$primary}
  --accent: #{$accent}
  --warn: #{$warn}

  // Even though this looks like a Sass variable, it's valid CSS so it's not
  // evaluated.
  --consumed-by-js: $primary
```

**Результат CSS**

```css
:root {
  --primary: #81899b;
  --accent: #302e24;
  --warn: #dfa612;
  --consumed-by-js: $primary;
}
```

> ⚠️ **Внимание**
>
> К сожалению, [интерполяция][interpolation] убирает кавычки из строк, из-за
> чего сложно использовать строки в кавычках в качестве значений
> пользовательских свойств, когда они приходят из переменных Sass. В качестве
> обходного пути можно использовать [функцию `meta.inspect()`][`meta.inspect()` function],
> которая сохраняет кавычки.
>
> [interpolation]: https://sass-lang.com/documentation/interpolation
> [`meta.inspect()` function]: https://sass-lang.com/documentation/modules/meta#inspect
>
> **SCSS**

```scss
@use "sass:meta";

$font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto;
$font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas;

:root {
  --font-family-sans-serif: #{meta.inspect($font-family-sans-serif)};
  --font-family-monospace: #{meta.inspect($font-family-monospace)};
}
```

> **Sass**

```sass
@use "sass:meta"

$font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto
$font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas

:root
  --font-family-sans-serif: #{meta.inspect($font-family-sans-serif)}
  --font-family-monospace: #{meta.inspect($font-family-monospace)}
```

> **Результат CSS**

```css
:root {
  --font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto;
  --font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas;
}
```

### Результаты `@function`

**Совместимость:** Dart Sass: начиная с 1.94.0 · LibSass: ✗ · Ruby Sass: ✗


Свойство `result` [обычного at-правила CSS `@function`][plain-CSS `@function` rule]
работает так же, как пользовательское свойство: оно тоже может быть
использовано в любом месте значения свойства, доступно из JavaScript и,
соответственно, может принимать почти любое возможное значение. Sass разбирает
его так же, как значение пользовательского свойства, а значит для включения
значений SassScript в него нужно использовать интерполяцию.

[plain-CSS `@function` rule]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@function

**SCSS**

```scss
$highlight: #ddf;

@function --highlight() {
  result: var(--highlight, #{$highlight});
}
```

**Sass**

```sass
$highlight: #ddf

@function --highlight()
  result: var(--highlight, #{$highlight})
```

**Результат CSS**

```css
@function --highlight() {
  result: var(--highlight, #ddf);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/style-rules/declarations/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# At-правила CSS


**Совместимость (интерполяция имён):** Dart Sass: начиная с 1.15.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и более старые версии Dart Sass не поддерживают
  [интерполяцию][] в именах at-правил. При этом они поддерживают интерполяцию в значениях.

  [интерполяцию]: https://sass-lang.com/documentation/interpolation


Sass поддерживает все at-правила, являющиеся частью самого CSS. Чтобы оставаться
гибким и совместимым с будущими версиями CSS, Sass по умолчанию имеет общую
поддержку, охватывающую почти все at-правила. CSS at-правило записывается как
`@<name> <value>`, `@<name> { ... }` или `@<name> <value> { ... }`. Имя должно быть
идентификатором, а значение (если оно есть) может быть практически любым. И имя, и
значение могут содержать [интерполяцию][].

[интерполяцию]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
@namespace svg url(http://www.w3.org/2000/svg);

@font-face {
  font-family: "Open Sans";
  src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
}

@counter-style thumbs {
  system: cyclic;
  symbols: "\1F44D";
}
```

**Sass**

```sass
@namespace svg url(http://www.w3.org/2000/svg)

@font-face
  font-family: "Open Sans"
  src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2")

@counter-style thumbs
  system: cyclic
  symbols: "\1F44D"
```

**CSS output**

```css
@charset "UTF-8";
@namespace svg url(http://www.w3.org/2000/svg);
@font-face {
  font-family: "Open Sans";
  src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
}
@counter-style thumbs {
  system: cyclic;
  symbols: "👍";
}
```

Если CSS at-правило вложено в стилевое правило, они автоматически меняются местами,
так что at-правило оказывается на верхнем уровне выходного CSS, а стилевое правило —
внутри него. Это упрощает добавление условных стилей без необходимости переписывать
селектор стилевого правила.

**SCSS**

```scss
.print-only {
  display: none;

  @media print { display: block; }
}
```

**Sass**

```sass
.print-only
  display: none

  @media print
    display: block
```

**CSS output**

```css
.print-only {
  display: none;
}
@media print {
  .print-only {
    display: block;
  }
}
```

## `@media`

**Совместимость (синтаксис диапазонов):** Dart Sass: начиная с 1.11.0 · LibSass: ✗ · Ruby Sass: начиная с 3.7.0

LibSass и более старые версии Dart Sass и Ruby Sass не поддерживают медиазапросы
  с функциями, записанными в [контексте диапазона][]. При этом они поддерживают
  другие стандартные медиазапросы.

  [контексте диапазона]: https://www.w3.org/TR/mediaqueries-4/#mq-range-context

  **SCSS**

```scss
@media (width <= 700px) {
  body {
    background: green;
  }
}
```

**Sass**

```sass
@media (width <= 700px)
  body
    background: green
```

**CSS output**

```css
@media (width <= 700px) {
  body {
    background: green;
  }
}
```


[Правило `@media`][] делает всё вышеперечисленное и даже больше. Помимо разрешения
интерполяции, оно позволяет использовать [выражения SassScript][] непосредственно в
[запросах характеристик][].

[Правило `@media`]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries
[выражения SassScript]: https://sass-lang.com/documentation/syntax/structure#expressions
[запросах характеристик]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Targeting_media_features

**SCSS**

```scss
$layout-breakpoint-small: 960px;

@media (min-width: $layout-breakpoint-small) {
  .hide-extra-small {
    display: none;
  }
}
```

**Sass**

```sass
$layout-breakpoint-small: 960px

@media (min-width: $layout-breakpoint-small)
  .hide-extra-small
    display: none
```

**CSS output**

```css
@media (min-width: 960px) {
  .hide-extra-small {
    display: none;
  }
}
```

Когда это возможно, Sass также объединяет медиазапросы, вложенные друг в друга,
чтобы упростить поддержку браузеров, которые пока не поддерживают вложенные
правила `@media` нативно.

**SCSS**

```scss
@media (hover: hover) {
  .button:hover {
    border: 2px solid black;

    @media (color) {
      border-color: #036;
    }
  }
}
```

**Sass**

```sass
@media (hover: hover)
  .button:hover
    border: 2px solid black

    @media (color)
      border-color: #036
```

**CSS output**

```css
@media (hover: hover) {
  .button:hover {
    border: 2px solid black;
  }
}
@media (hover: hover) and (color) {
  .button:hover {
    border-color: #036;
  }
}
```

## `@supports`

[Правило `@supports`][] также позволяет использовать [выражения SassScript][] в
запросах объявлений.

[выражения SassScript]: https://sass-lang.com/documentation/syntax/structure#expressions
[Правило `@supports`]: https://developer.mozilla.org/en-US/docs/Web/CSS/@supports

**SCSS**

```scss
@mixin sticky-position {
  position: fixed;
  @supports (position: sticky) {
    position: sticky;
  }
}

.banner {
  @include sticky-position;
}
```

**Sass**

```sass
@mixin sticky-position
  position: fixed
  @supports (position: sticky)
    position: sticky



.banner
  @include sticky-position
```

**CSS output**

```css
.banner {
  position: fixed;
}
@supports (position: sticky) {
  .banner {
    position: sticky;
  }
}
```

## `@keyframes`

[Правило `@keyframes`][] работает точно так же, как обычное at-правило, за
исключением того, что его дочерние правила должны быть корректными правилами
ключевых кадров (`<number>%`, `from` или `to`), а не обычными селекторами.

[Правило `@keyframes`]: https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes

**SCSS**

```scss
@keyframes slide-in {
  from {
    margin-left: 100%;
    width: 300%;
  }

  70% {
    margin-left: 90%;
    width: 150%;
  }

  to {
    margin-left: 0%;
    width: 100%;
  }
}
```

**Sass**

```sass
@keyframes slide-in
  from
    margin-left: 100%
    width: 300%


  70%
    margin-left: 90%
    width: 150%


  to
    margin-left: 0%
    width: 100%
```

**CSS output**

```css
@keyframes slide-in {
  from {
    margin-left: 100%;
    width: 300%;
  }
  70% {
    margin-left: 90%;
    width: 150%;
  }
  to {
    margin-left: 0%;
    width: 100%;
  }
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/css/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

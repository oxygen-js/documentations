# null

Значение `null` — единственное значение своего типа. Оно представляет отсутствие
значения и часто возвращается [функциями](https://sass-lang.com/documentation/at-rules/function)
для обозначения отсутствия результата.

**SCSS**

```scss
@use "sass:map";
@use "sass:string";

@debug string.index("Helvetica Neue", "Roboto"); // null
@debug map.get(("large": 20px), "small"); // null
@debug &; // null
```

**Sass**

```sass
@use "sass:map"
@use "sass:string"

@debug string.index("Helvetica Neue", "Roboto")  // null
@debug map.get(("large": 20px), "small")  // null
@debug &  // null
```

Если [список][] содержит `null`, этот `null` опускается из сгенерированного CSS.

[список]: https://sass-lang.com/documentation/values/lists

**SCSS**

```scss
$fonts: ("serif": "Helvetica Neue", "monospace": "Consolas");

h3 {
  font: 18px bold map-get($fonts, "sans");
}
```

**Sass**

```sass
$fonts: ("serif": "Helvetica Neue", "monospace": "Consolas")

h3
  font: 18px bold map-get($fonts, "sans")
```

**CSS output**

```css
h3 {
  font: 18px bold;
}
```

Если значение свойства — `null`, это свойство опускается полностью.

**SCSS**

```scss
$fonts: ("serif": "Helvetica Neue", "monospace": "Consolas");

h3 {
  font: {
    size: 18px;
    weight: bold;
    family: map-get($fonts, "sans");
  }
}
```

**Sass**

```sass
$fonts: ("serif": "Helvetica Neue", "monospace": "Consolas")

h3
  font:
    size: 18px
    weight: bold
    family: map-get($fonts, "sans")
```

**CSS output**

```css
h3 {
  font-size: 18px;
  font-weight: bold;
}
```

`null` также является [*ложным*][] (falsy) значением, а значит считается `false` для
любых правил или [операторов][], принимающих логические значения. Благодаря этому
значения, которые могут быть `null`, удобно использовать как условия для [`@if`][] и
[`if()`].

[*ложным*]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness
[операторов]: https://sass-lang.com/documentation/operators/boolean
[`@if`]: https://sass-lang.com/documentation/at-rules/control/if
[`if()`]: https://sass-lang.com/documentation/syntax/special-functions#if

**SCSS**

```scss
@mixin app-background($color) {
  #{if(sass(&): '&.app-background'; else: '.app-background')} {
    background-color: $color;
    color: rgba(#fff, 0.75);
  }
}

@include app-background(#036);

.sidebar {
  @include app-background(#c6538c);
}
```

**Sass**

```sass
@mixin app-background($color)
  #{if(sass(&): '&.app-background'; else: '.app-background')}
    background-color: $color
    color: rgba(#fff, 0.75)



@include app-background(#036)

.sidebar
  @include app-background(#c6538c)
```

**CSS output**

```css
.app-background {
  background-color: #036;
  color: rgba(255, 255, 255, 0.75);
}

.sidebar.app-background {
  background-color: #c6538c;
  color: rgba(255, 255, 255, 0.75);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/null/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

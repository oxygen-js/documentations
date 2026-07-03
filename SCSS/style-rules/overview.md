# Стилевые правила

Стилевые правила — основа Sass, точно так же, как и для CSS. И работают они
так же: вы выбираете, какие элементы стилизовать, с помощью селектора, и
[объявляете свойства](https://sass-lang.com/documentation/style-rules/declarations), которые
влияют на то, как эти элементы выглядят.

**SCSS**

```scss
.button {
  padding: 3px 10px;
  font-size: 12px;
  border-radius: 3px;
  border: 1px solid #e1e4e8;
}
```

**Sass**

```sass
.button
  padding: 3px 10px
  font-size: 12px
  border-radius: 3px
  border: 1px solid #e1e4e8
```

**Результат CSS**

```css
.button {
  padding: 3px 10px;
  font-size: 12px;
  border-radius: 3px;
  border: 1px solid #e1e4e8;
}
```

## Вложенность

Но Sass хочет облегчить вам жизнь. Вместо того чтобы раз за разом повторять
одни и те же селекторы, можно писать одно стилевое правило внутри другого.
Sass автоматически объединит селектор внешнего правила с селектором
внутреннего.

**SCSS**

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

**Sass**

```sass
nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block

  a
    display: block
    padding: 6px 12px
    text-decoration: none
```

**Результат CSS**

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```


> ⚠️ **Внимание**
>
> Вложенные правила очень полезны, но из-за них также сложно представить,
> сколько CSS вы на самом деле генерируете. Чем глубже вложенность, тем больше
> трафика уходит на отдачу вашего CSS и тем больше работы приходится браузеру
> выполнять, чтобы его отрендерить. Держите селекторы неглубокими!

### Списки селекторов

Вложенные правила умно обрабатывают списки селекторов (то есть селекторы,
разделённые запятыми). Каждый сложный селектор (те, что между запятыми)
вкладывается отдельно, а затем они снова объединяются в список селекторов.

**SCSS**

```scss
.alert, .warning {
  ul, p {
    margin-right: 0;
    margin-left: 0;
    padding-bottom: 0;
  }
}
```

**Sass**

```sass
.alert, .warning
  ul, p
    margin-right: 0
    margin-left: 0
    padding-bottom: 0
```

**Результат CSS**

```css
.alert ul, .alert p, .warning ul, .warning p {
  margin-right: 0;
  margin-left: 0;
  padding-bottom: 0;
}
```

### Комбинаторы селекторов

Можно также вкладывать селекторы, использующие [комбинаторы][combinators].
Комбинатор можно поставить в конце внешнего селектора, в начале внутреннего
селектора или даже отдельно между ними.

[combinators]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators#Combinators

**SCSS**

```scss
ul > {
  li {
    list-style-type: none;
  }
}

h2 {
  + p {
    border-top: 1px solid gray;
  }
}

p {
  ~ {
    span {
      opacity: 0.8;
    }
  }
}
```

**Sass**

```sass
ul >
  li
    list-style-type: none



h2
  + p
    border-top: 1px solid gray



p
  ~
    span
      opacity: 0.8
```

**Результат CSS**

```css
ul > li {
  list-style-type: none;
}

h2 + p {
  border-top: 1px solid gray;
}

p ~ span {
  opacity: 0.8;
}
```

### Расширенная вложенность

Если вы хотите делать со своими вложенными стилевыми правилами больше, чем
просто объединять их по порядку с разделением дочерним комбинатором (то есть
обычным пробелом), у Sass есть решение. Подробнее см. [документацию по
родительскому селектору][parent selector documentation].

[parent selector documentation]: https://sass-lang.com/documentation/style-rules/parent-selector

## Интерполяция

Вы можете использовать [интерполяцию][interpolation], чтобы внедрять значения
из [выражений][expressions], таких как переменные и вызовы функций, в свои
селекторы. Это особенно полезно при написании [миксинов][mixins], поскольку
позволяет создавать селекторы из параметров, переданных вашими пользователями.

[interpolation]: https://sass-lang.com/documentation/interpolation
[expressions]: https://sass-lang.com/documentation/syntax/structure#expressions
[mixins]: https://sass-lang.com/documentation/at-rules/mixin

**SCSS**

```scss
@mixin define-emoji($name, $glyph) {
  span.emoji-#{$name} {
    font-family: IconFont;
    font-variant: normal;
    font-weight: normal;
    content: $glyph;
  }
}

@include define-emoji("women-holding-hands", "👭");
```

**Sass**

```sass
@mixin define-emoji($name, $glyph)
  span.emoji-#{$name}
    font-family: IconFont
    font-variant: normal
    font-weight: normal
    content: $glyph



@include define-emoji("women-holding-hands", "👭")
```

**Результат CSS**

```css
@charset "UTF-8";
span.emoji-women-holding-hands {
  font-family: IconFont;
  font-variant: normal;
  font-weight: normal;
  content: "👭";
}
```

> 💡 **Занятный факт**
>
> Sass разбирает селекторы только *после* того, как разрешена интерполяция.
> Это значит, что вы можете безопасно использовать интерполяцию для генерации
> любой части селектора, не беспокоясь о том, что он не разберётся.

Вы можете сочетать интерполяцию с родительским селектором `&`, [at-правилом
`@at-root`][`@at-root` rule] и [функциями для селекторов][selector functions],
чтобы получить серьёзную мощь при динамической генерации селекторов. Подробнее
см. [документацию по родительскому селектору][parent selector documentation].

[`@at-root` rule]: https://sass-lang.com/documentation/at-rules/at-root
[selector functions]: https://sass-lang.com/documentation/modules/selector
[parent selector documentation]: https://sass-lang.com/documentation/style-rules/parent-selector

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/style-rules/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

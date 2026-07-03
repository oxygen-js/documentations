# Родительский селектор

Родительский селектор, `&`, — это специальный селектор, изобретённый Sass,
который используется во [вложенных селекторах](https://sass-lang.com/documentation/style-rules#nesting),
чтобы ссылаться на внешний селектор. Он позволяет повторно использовать
внешний селектор более сложными способами, например добавляя
[псевдокласс](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)
или добавляя селектор *перед* родительским.

Когда родительский селектор используется во внутреннем селекторе, он
заменяется на соответствующий внешний селектор. Это происходит вместо
обычного поведения вложенности.

**SCSS**

```scss
.alert {
  // The parent selector can be used to add pseudo-classes to the outer
  // selector.
  &:hover {
    font-weight: bold;
  }

  // It can also be used to style the outer selector in a certain context, such
  // as a body set to use a right-to-left language.
  [dir=rtl] & {
    margin-left: 0;
    margin-right: 10px;
  }

  // You can even use it as an argument to pseudo-class selectors.
  :not(&) {
    opacity: 0.8;
  }
}
```

**Sass**

```sass
.alert
  // The parent selector can be used to add pseudo-classes to the outer
  // selector.
  &:hover
    font-weight: bold


  // It can also be used to style the outer selector in a certain context, such
  // as a body set to use a right-to-left language.
  [dir=rtl] &
    margin-left: 0
    margin-right: 10px


  // You can even use it as an argument to pseudo-class selectors.
  :not(&)
    opacity: 0.8
```

**Результат CSS**

```css
.alert:hover {
  font-weight: bold;
}
[dir=rtl] .alert {
  margin-left: 0;
  margin-right: 10px;
}
:not(.alert) {
  opacity: 0.8;
}
```

> ⚠️ **Внимание**
>
> Поскольку родительский селектор может быть заменён селектором типа, таким
> как `h1`, он допустим только в начале составных селекторов — там же, где
> допустим и селектор типа. Например, `span&` не допускается.
>
> Мы рассматриваем возможность ослабить это ограничение. Если хотите помочь
> это осуществить, посмотрите [этот issue на GitHub][this GitHub issue].
>
> [this GitHub issue]: https://github.com/sass/sass/issues/1425

## Добавление суффиксов

Родительский селектор также можно использовать, чтобы добавить дополнительные
суффиксы к внешнему селектору. Это особенно полезно при использовании такой
методологии, как [BEM][], которая опирается на строго структурированные имена
классов. Пока внешний селектор заканчивается буквенно-цифровым именем (как
селекторы класса, ID и элемента), вы можете использовать родительский
селектор, чтобы добавить дополнительный текст.

[BEM]: http://getbem.com/

**SCSS**

```scss
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;

  &__copy {
    display: none;
    padding: 1rem 1.5rem 2rem 1.5rem;
    color: gray;
    line-height: 1.6;
    font-size: 14px;
    font-weight: 500;

    &--open {
      display: block;
    }
  }
}
```

**Sass**

```sass
.accordion
  max-width: 600px
  margin: 4rem auto
  width: 90%
  font-family: "Raleway", sans-serif
  background: #f4f4f4

  &__copy
    display: none
    padding: 1rem 1.5rem 2rem 1.5rem
    color: gray
    line-height: 1.6
    font-size: 14px
    font-weight: 500

    &--open
      display: block
```

**Результат CSS**

```css
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;
}
.accordion__copy {
  display: none;
  padding: 1rem 1.5rem 2rem 1.5rem;
  color: gray;
  line-height: 1.6;
  font-size: 14px;
  font-weight: 500;
}
.accordion__copy--open {
  display: block;
}
```

## Без вложенности

**Совместимость:** Dart Sass: начиная с 1.100.0 · LibSass: ✗ · Ruby Sass: ✗


Как и в обычном CSS, родительский селектор можно использовать и вне вложенного
правила. В этом случае он выводится как обычный CSS, обработка которого
ложится на браузер, который интерпретирует его как [корень области видимости
селекторов][scoping root].

[scoping root]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Nesting_selector#using_outside_nested_rule

> ⚠️ **Внимание**
>
> Нельзя добавить суффикс к родительским селекторам, которые не вложены,
> потому что не к чему применять этот суффикс.

**SCSS**

```scss
& {
  background-color: blue;
}
```

**Sass**

```sass
&
  background-color: blue
```

**Результат CSS**

```css
& {
  background-color: blue;
}
```

## В SassScript

Родительский селектор также можно использовать внутри SassScript. Это
специальное выражение, которое возвращает текущий родительский селектор в том
же формате, что используется [функциями для селекторов][selector functions]:
список, разделённый запятыми (список селекторов), который содержит списки,
разделённые пробелами (сложные селекторы), которые содержат строки без
кавычек (составные селекторы).

[selector functions]: https://sass-lang.com/documentation/modules/selector#selector-values

**SCSS**

```scss
.main aside:hover,
.sidebar p {
  parent-selector: &;
  // => ((unquote(".main") unquote("aside:hover")),
  //     (unquote(".sidebar") unquote("p")))
}
```

**Sass**

```sass
.main aside:hover,
.sidebar p
  parent-selector: &
  // => ((unquote(".main") unquote("aside:hover")),
  //     (unquote(".sidebar") unquote("p")))
```

**Результат CSS**

```css
.main aside:hover,
.sidebar p {
  parent-selector: .main aside:hover, .sidebar p;
}
```

Если выражение `&` используется вне каких-либо стилевых правил, оно возвращает
`null`. Поскольку `null` является [ложным значением][falsey], это значит, что
с его помощью легко определить, вызывается ли миксин внутри стилевого правила
или нет.

[falsey]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness

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

**Результат CSS**

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


### Расширенная вложенность

Вы можете использовать `&` как обычное выражение SassScript, а значит можете
передавать его в функции или включать в интерполяцию — даже в других
селекторах! Использование его в сочетании с [функциями для
селекторов][selector functions] и [at-правилом `@at-root`][`@at-root` rule]
позволяет вкладывать селекторы очень мощными способами.

[selector functions]: https://sass-lang.com/documentation/modules/selector#selector-values
[`@at-root` rule]: https://sass-lang.com/documentation/at-rules/at-root

Например, предположим, вы хотите написать селектор, который соответствует
внешнему селектору *и* селектору элемента. Вы могли бы написать вот такой
миксин, который использует [функцию `selector.unify()`][`selector.unify()` function],
чтобы объединить `&` с пользовательским селектором.

[`selector.unify()` function]: https://sass-lang.com/documentation/modules/selector#unify

**SCSS**

```scss
@use "sass:selector";

@mixin unify-parent($child) {
  @at-root #{selector.unify(&, $child)} {
    @content;
  }
}

.wrapper .field {
  @include unify-parent("input") {
    /* ... */
  }
  @include unify-parent("select") {
    /* ... */
  }
}
```

**Sass**

```sass
@use "sass:selector"

@mixin unify-parent($child)
  @at-root #{selector.unify(&, $child)}
    @content



.wrapper .field
  @include unify-parent("input")
    /* ... */

  @include unify-parent("select")
    /* ... */
```

**Результат CSS**

```css
.wrapper input.field {
  /* ... */
}

.wrapper select.field {
  /* ... */
}
```


> ⚠️ **Внимание**
>
> Когда Sass вкладывает селекторы, он не знает, какая интерполяция была
> использована для их генерации. Это значит, что он автоматически добавит
> внешний селектор к внутреннему, *даже если* вы использовали `&` как
> выражение SassScript. Именно поэтому вам нужно явно использовать
> [at-правило `@at-root`][`@at-root` rule], чтобы указать Sass не включать
> внешний селектор.
>
> [`@at-root` rule]: https://sass-lang.com/documentation/at-rules/at-root

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/style-rules/parent-selector/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# @mixin и @include

Миксины позволяют определять стили, которые можно повторно использовать в разных частях таблицы стилей. Они помогают избежать использования несемантичных классов вроде `.float-left`, а также распространять наборы стилей в библиотеках.

Миксины определяются с помощью at-правила `@mixin`, которое записывается как `@mixin <name> {
... }` или `@mixin name(<arguments...>) { ... }`. Именем миксина может быть любой идентификатор Sass, не начинающийся с `--`, и он может содержать любую [инструкцию], кроме [инструкций верхнего уровня]. Миксины можно использовать для инкапсуляции стилей, которые помещаются в одно [стилевое правило]; они могут содержать собственные стилевые правила, которые можно вкладывать в другие правила или подключать на верхнем уровне таблицы стилей; либо они могут просто служить для изменения переменных.

[инструкцию]: https://sass-lang.com/documentation/syntax/structure#statements
[инструкций верхнего уровня]: https://sass-lang.com/documentation/syntax/structure#top-level-statements
[стилевое правило]: https://sass-lang.com/documentation/style-rules

Миксины подключаются в текущий контекст с помощью at-правила `@include`, которое записывается как `@include <name>` или `@include <name>(<arguments...>)`, где указывается имя подключаемого миксина.

**SCSS**

```scss
@mixin reset-list {
  margin: 0;
  padding: 0;
  list-style: none;
}

@mixin horizontal-list {
  @include reset-list;

  li {
    display: inline-block;
    margin: {
      left: -2px;
      right: 2em;
    }
  }
}

nav ul {
  @include horizontal-list;
}
```

**Sass**

```sass
@mixin reset-list
  margin: 0
  padding: 0
  list-style: none


@mixin horizontal-list
  @include reset-list

  li
    display: inline-block
    margin:
      left: -2px
      right: 2em




nav ul
  @include horizontal-list
```

**Результат CSS**

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav ul li {
  display: inline-block;
  margin-left: -2px;
  margin-right: 2em;
}
```

> 💡 **Занятный факт**
>
> Имена миксинов, как и все идентификаторы Sass, не различают дефисы и подчёркивания. Это значит, что `reset-list` и `reset_list` обозначают один и тот же миксин. Это историческое наследие самых ранних версий Sass, когда в именах идентификаторов допускались *только* подчёркивания. Когда в Sass добавили поддержку дефисов для соответствия синтаксису CSS, оба варианта сделали эквивалентными, чтобы упростить миграцию.

## Аргументы

Миксины также могут принимать аргументы, что позволяет настраивать их поведение при каждом вызове. Аргументы указываются в правиле `@mixin` после имени миксина в виде списка имён переменных, заключённого в круглые скобки. При подключении миксина необходимо передать то же количество аргументов в виде [выражений SassScript][]. Значения этих выражений доступны внутри тела миксина в виде соответствующих переменных.

[выражений SassScript]: https://sass-lang.com/documentation/syntax/structure#expressions

**SCSS**

```scss
@mixin rtl($property, $ltr-value, $rtl-value) {
  #{$property}: $ltr-value;

  [dir=rtl] & {
    #{$property}: $rtl-value;
  }
}

.sidebar {
  @include rtl(float, left, right);
}
```

**Sass**

```sass
@mixin rtl($property, $ltr-value, $rtl-value)
  #{$property}: $ltr-value

  [dir=rtl] &
    #{$property}: $rtl-value



.sidebar
  @include rtl(float, left, right)
```

**Результат CSS**

```css
.sidebar {
  float: left;
}
[dir=rtl] .sidebar {
  float: right;
}
```

> 💡 **Занятный факт**
>
> Списки аргументов также могут содержать завершающую запятую! Это упрощает избежание синтаксических ошибок при рефакторинге таблиц стилей.

### Необязательные аргументы

Обычно при подключении миксина необходимо передать каждый аргумент, объявленный в нём. Однако аргумент можно сделать необязательным, задав для него *значение по умолчанию*, которое будет использовано, если аргумент не передан. Значения по умолчанию используют тот же синтаксис, что и [объявления переменных][]: имя переменной, за которым следует двоеточие и [выражение SassScript][]. Это позволяет легко создавать гибкие API миксинов, которые можно использовать как в простых, так и в сложных случаях.

[объявления переменных]: https://sass-lang.com/documentation/variables
[выражение SassScript]: https://sass-lang.com/documentation/syntax/structure#expressions

**SCSS**

```scss
@mixin replace-text($image, $x: 50%, $y: 50%) {
  text-indent: -99999em;
  overflow: hidden;
  text-align: left;

  background: {
    image: $image;
    repeat: no-repeat;
    position: $x $y;
  }
}

.mail-icon {
  @include replace-text(url("/images/mail.svg"), 0);
}
```

**Sass**

```sass
@mixin replace-text($image, $x: 50%, $y: 50%)
  text-indent: -99999em
  overflow: hidden
  text-align: left

  background:
    image: $image
    repeat: no-repeat
    position: $x $y

.mail-icon
  @include replace-text(url("/images/mail.svg"), 0)
```

**Результат CSS**

```css
.mail-icon {
  text-indent: -99999em;
  overflow: hidden;
  text-align: left;
  background-image: url("/images/mail.svg");
  background-repeat: no-repeat;
  background-position: 0 50%;
}
```

> 💡 **Занятный факт**
>
> Значением по умолчанию может быть любое выражение SassScript, и оно может даже ссылаться на предыдущие аргументы!

### Именованные аргументы

При подключении миксина аргументы можно передавать по имени, а не только по их позиции в списке аргументов. Это особенно полезно для миксинов с несколькими необязательными аргументами либо с аргументами [логического типа][boolean], смысл которых не очевиден без явного указания имени. Именованные аргументы используют тот же синтаксис, что и [объявления переменных][] и [необязательные аргументы][optional arguments].

[boolean]: https://sass-lang.com/documentation/values/booleans
[optional arguments]: #optional-arguments

**SCSS**

```scss
@mixin square($size, $radius: 0) {
  width: $size;
  height: $size;

  @if $radius != 0 {
    border-radius: $radius;
  }
}

.avatar {
  @include square(100px, $radius: 4px);
}
```

**Sass**

```sass
@mixin square($size, $radius: 0)
  width: $size
  height: $size

  @if $radius != 0
    border-radius: $radius



.avatar
  @include square(100px, $radius: 4px)
```

**Результат CSS**

```css
.avatar {
  width: 100px;
  height: 100px;
  border-radius: 4px;
}
```

> ⚠️ **Внимание**
>
> Поскольку *любой* аргумент можно передать по имени, будьте осторожны при переименовании аргументов миксина... это может сломать код ваших пользователей! Может быть полезно на некоторое время оставить старое имя в качестве [необязательного аргумента][optional argument] и выводить [предупреждение][warning], если кто-то его передаёт, чтобы дать понять, что нужно перейти на новый аргумент.
>
> [optional argument]: #optional-arguments
> [warning]: https://sass-lang.com/documentation/at-rules/warn

### Приём произвольных аргументов

Иногда бывает полезно, чтобы миксин мог принимать произвольное количество аргументов. Если последний аргумент в объявлении `@mixin` заканчивается на `...`, то все дополнительные аргументы, переданные этому миксину, попадают в этот аргумент в виде [списка][list]. Такой аргумент называется [списком аргументов][argument list].

[list]: https://sass-lang.com/documentation/values/lists
[argument list]: https://sass-lang.com/documentation/values/lists#argument-lists

**SCSS**

```scss
@mixin order($height, $selectors...) {
  @for $i from 0 to length($selectors) {
    #{nth($selectors, $i + 1)} {
      position: absolute;
      height: $height;
      margin-top: $i * $height;
    }
  }
}

@include order(150px, "input.name", "input.address", "input.zip");
```

**Sass**

```sass
@mixin order($height, $selectors...)
  @for $i from 0 to length($selectors)
    #{nth($selectors, $i + 1)}
      position: absolute
      height: $height
      margin-top: $i * $height




@include order(150px, "input.name", "input.address", "input.zip")
```

**Результат CSS**

```css
input.name {
  position: absolute;
  height: 150px;
  margin-top: 0px;
}

input.address {
  position: absolute;
  height: 150px;
  margin-top: 150px;
}

input.zip {
  position: absolute;
  height: 150px;
  margin-top: 300px;
}
```

#### Приём произвольных именованных аргументов

Списки аргументов также можно использовать для приёма произвольных именованных аргументов. [Функция `meta.keywords()`][] принимает список аргументов и возвращает все дополнительные именованные аргументы, переданные миксину, в виде [карты (map)][] — от имён аргументов (без `$`) к значениям этих аргументов.

[Функция `meta.keywords()`]: https://sass-lang.com/documentation/modules/meta#keywords
[карты (map)]: https://sass-lang.com/documentation/values/maps

**SCSS**

```scss
@use "sass:meta";

@mixin syntax-colors($args...) {
  @debug meta.keywords($args);
  // (string: #080, comment: #800, variable: #60b)

  @each $name, $color in meta.keywords($args) {
    pre span.stx-#{$name} {
      color: $color;
    }
  }
}

@include syntax-colors(
  $string: #080,
  $comment: #800,
  $variable: #60b,
)
```

**Sass**

```sass
@use "sass:meta"

@mixin syntax-colors($args...)
  @debug meta.keywords($args)
  // (string: #080, comment: #800, variable: #60b)

  @each $name, $color in meta.keywords($args)
    pre span.stx-#{$name}
      color: $color




@include syntax-colors($string: #080, $comment: #800, $variable: #60b)
```

**Результат CSS**

```css
pre span.stx-string {
  color: #080;
}

pre span.stx-comment {
  color: #800;
}

pre span.stx-variable {
  color: #60b;
}
```

> 💡 **Занятный факт**
>
> Если вы никогда не передаёте список аргументов в [функцию `meta.keywords()`][], этот список аргументов не позволит передавать дополнительные именованные аргументы. Это помогает тем, кто вызывает ваш миксин, убедиться, что они случайно не ошиблись в написании имени аргумента.
>
> [функцию `meta.keywords()`]: https://sass-lang.com/documentation/modules/meta#keywords

#### Передача произвольных аргументов

Подобно тому как списки аргументов позволяют миксинам принимать произвольные позиционные или именованные аргументы, тот же синтаксис можно использовать, чтобы *передать* миксину позиционные и именованные аргументы. Если передать список, за которым следует `...`, в качестве последнего аргумента при подключении, его элементы будут рассматриваться как дополнительные позиционные аргументы. Аналогично, карта, за которой следует `...`, будет рассматриваться как дополнительные именованные аргументы. Можно даже передать оба варианта одновременно!

**SCSS**

```scss
$form-selectors: "input.name", "input.address", "input.zip" !default;

@include order(150px, $form-selectors...);
```

**Sass**

```sass
$form-selectors: "input.name", "input.address", "input.zip" !default

@include order(150px, $form-selectors...)
```

> 💡 **Занятный факт**
>
> Поскольку [список аргументов][] отслеживает как позиционные, так и именованные аргументы, его можно использовать, чтобы передать оба сразу в другой миксин. Это делает определение псевдонима для миксина невероятно простым!
>
> [список аргументов]: https://sass-lang.com/documentation/values/lists#argument-lists

**SCSS**

```scss
@mixin btn($args...) {
  @warn "The btn() mixin is deprecated. Include button() instead.";
  @include button($args...);
}
```

**Sass**

```sass
@mixin btn($args...)
  @warn "The btn() mixin is deprecated. Include button() instead."
  @include button($args...)
```

## Блоки содержимого

Помимо аргументов, миксин может принимать целый блок стилей, который называется *блоком содержимого*. Миксин может объявить, что он принимает блок содержимого, включив в своё тело at-правило `@content`. Блок содержимого передаётся с помощью фигурных скобок, как и любой другой блок в Sass, и подставляется на место правила `@content`.

**SCSS**

```scss
@mixin hover {
  &:not([disabled]):hover {
    @content;
  }
}

.button {
  border: 1px solid black;
  @include hover {
    border-width: 2px;
  }
}
```

**Sass**

```sass
@mixin hover
  &:not([disabled]):hover
    @content



.button
  border: 1px solid black
  @include hover
    border-width: 2px
```

**Результат CSS**

```css
.button {
  border: 1px solid black;
}
.button:not([disabled]):hover {
  border-width: 2px;
}
```

> 💡 **Занятный факт**
>
> Миксин может содержать несколько at-правил `@content`. В этом случае блок содержимого будет подключён отдельно для каждого `@content`.

> ⚠️ **Внимание**
>
> Блок содержимого имеет *лексическую область видимости* — это значит, что ему видны только [локальные переменные][] той области видимости, в которой подключён миксин. Ему не видны переменные, определённые в миксине, которому он передан, даже если они определены до вызова блока содержимого.
>
> [локальные переменные]: https://sass-lang.com/documentation/variables#scope

### Передача аргументов в блоки содержимого

**Совместимость:** Dart Sass: начиная с 1.15.0 · LibSass: ✗ · Ruby Sass: ✗

Миксин может передавать аргументы своему блоку содержимого точно так же, как он передавал бы аргументы другому миксину, — записав `@content(<arguments...>)`. Автор блока содержимого может принять аргументы, написав `@include <name> using (<arguments...>)`. Список аргументов блока содержимого работает точно так же, как список аргументов миксина, а аргументы, передаваемые ему через `@content`, работают так же, как передача аргументов миксину.

> ⚠️ **Внимание**
>
> Если миксин передаёт аргументы своему блоку содержимого, этот блок содержимого *обязан* объявить, что он принимает эти аргументы. Поэтому аргументы стоит передавать только по позиции (а не по имени); кроме того, передача дополнительных аргументов является ломающим изменением.
>
> Если вы хотите гибко определять, какую информацию передавать блоку содержимого, попробуйте передать ему [карту][map], содержащую нужную информацию!
>
> [map]: https://sass-lang.com/documentation/values/maps

**SCSS**

```scss
@mixin media($types...) {
  @each $type in $types {
    @media #{$type} {
      @content($type);
    }
  }
}

@include media(screen, print) using ($type) {
  h1 {
    font-size: 40px;
    @if $type == print {
      font-family: Calluna;
    }
  }
}
```

**Sass**

```sass
@mixin media($types...)
  @each $type in $types
    @media #{$type}
      @content($type)




@include media(screen, print) using ($type)
  h1
    font-size: 40px
    @if $type == print
      font-family: Calluna
```

**Результат CSS**

```css
@media screen {
  h1 {
    font-size: 40px;
  }
}
@media print {
  h1 {
    font-size: 40px;
    font-family: Calluna;
  }
}
```

## Синтаксис миксинов в отступном синтаксисе

У [отступного синтаксиса][] есть особый синтаксис для определения и использования миксинов, помимо стандартных `@mixin` и `@include`. Миксины определяются с помощью символа `=`, а подключаются с помощью `+`. Хотя этот синтаксис короче, его сложнее понять с первого взгляда, поэтому пользователям рекомендуется его избегать.

[отступного синтаксиса]: https://sass-lang.com/documentation/syntax#the-indented-syntax

```sass
=reset-list
  margin: 0
  padding: 0
  list-style: none

=horizontal-list
  +reset-list

  li
    display: inline-block
    margin:
      left: -2px
      right: 2em

nav ul
  +horizontal-list
```

**Результат CSS**

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav ul li {
  display: inline-block;
  margin-left: -2px;
  margin-right: 2em;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/mixin/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

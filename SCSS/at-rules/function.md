# @function

Функции позволяют определять сложные операции над
[значениями SassScript](https://sass-lang.com/documentation/values), которые
можно повторно использовать в вашей таблице стилей. Они позволяют легко
абстрагировать распространённые формулы и типы поведения в удобочитаемом
виде.

Функции определяются с помощью at-правила `@function`, которое записывается
как `@function <имя>(<аргументы...>) { ... }`. Именем функции может быть любой
идентификатор Sass, не начинающийся с `--`. Оно может содержать только
[универсальные инструкции][universal statements], а также at-правило
[`@return`][`@return` at-rule], указывающее значение, используемое в качестве
результата вызова функции. Функции вызываются с использованием обычного
синтаксиса функций CSS.

[universal statements]: https://sass-lang.com/documentation/syntax/structure#universal-statements
[`@return` at-rule]: #return

**SCSS**

```scss
@function fibonacci($n) {
  $sequence: 0 1;
  @for $_ from 1 through $n {
    $new: nth($sequence, length($sequence)) + nth($sequence, length($sequence) - 1);
    $sequence: append($sequence, $new);
  }
  @return nth($sequence, length($sequence));
}

.sidebar {
  float: left;
  margin-left: fibonacci(4) * 1px;
}
```

**Sass**

```sass
@function fibonacci($n)
  $sequence: 0 1
  @for $_ from 1 through $n
    $new: nth($sequence, length($sequence)) + nth($sequence, length($sequence) - 1)
    $sequence: append($sequence, $new)
  @return nth($sequence, length($sequence))

.sidebar
  float: left
  margin-left: fibonacci(4) * 1px
```

**Результат CSS**

```css
.sidebar {
  float: left;
  margin-left: 5px;
}
```

> 💡 **Занятный факт**
>
> Имена функций, как и все идентификаторы Sass, считают дефисы и подчёркивания
> идентичными. Это значит, что `scale-color` и `scale_color` относятся к одной
> и той же функции. Это исторический пережиток самых ранних дней Sass, когда
> в именах идентификаторов допускались *только* подчёркивания. Когда Sass
> добавил поддержку дефисов, чтобы соответствовать синтаксису CSS, эти два
> варианта сделали эквивалентными, чтобы упростить миграцию.

> ⚠️ **Внимание**
>
> Хотя технически функции могут иметь побочные эффекты, например установку
> [глобальных переменных][global variables], делать это настоятельно не
> рекомендуется. Используйте [миксины][mixins] для побочных эффектов, а
> функции — только для вычисления значений.

[global variables]: https://sass-lang.com/documentation/variables#scope
[mixins]: https://sass-lang.com/documentation/at-rules/mixin

## Аргументы



Аргументы позволяют настраивать поведение функций при каждом вызове.
Аргументы указываются в правиле `@function` после имени функции в виде списка
имён переменных, заключённого в круглые скобки. Функция должна вызываться с
тем же числом аргументов в виде [выражений SassScript][SassScript expressions].
Значения этих выражений доступны внутри тела функции как соответствующие
переменные.

[SassScript expressions]: https://sass-lang.com/documentation/syntax/structure#expressions

> 💡 **Занятный факт**
>
> В списках аргументов также допускаются завершающие запятые! Это упрощает
> избежание синтаксических ошибок при рефакторинге таблиц стилей.

### Необязательные аргументы

Обычно каждый аргумент, объявленный функцией, должен быть передан при её
вызове. Однако можно сделать аргумент необязательным, задав для него
*значение по умолчанию*, которое будет использовано, если этот аргумент не
передан. Значения по умолчанию используют тот же синтаксис, что и
[объявления переменных][variable declarations]: имя переменной, за которым
следуют двоеточие и [выражение SassScript][SassScript expression]. Это
позволяет легко определять гибкие API функций, которые можно использовать
как простыми, так и сложными способами.

[variable declarations]: https://sass-lang.com/documentation/variables
[SassScript expression]: https://sass-lang.com/documentation/syntax/structure#expressions

**SCSS**

```scss
@function invert($color, $amount: 100%) {
  $inverse: change-color($color, $hue: hue($color) + 180);
  @return mix($inverse, $color, $amount);
}

$primary-color: #036;
.header {
  background-color: invert($primary-color, 80%);
}
```

**Sass**

```sass
@function invert($color, $amount: 100%)
  $inverse: change-color($color, $hue: hue($color) + 180)
  @return mix($inverse, $color, $amount)


$primary-color: #036
.header
  background-color: invert($primary-color, 80%)
```

**Результат CSS**

```css
.header {
  background-color: rgb(81.6, 51, 20.4);
}
```

> 💡 **Занятный факт**
>
> Значениями по умолчанию может быть любое выражение SassScript, и они даже
> могут ссылаться на более ранние аргументы!

### Именованные аргументы

При вызове функции аргументы можно передавать по имени в дополнение к
передаче по позиции в списке аргументов. Это особенно полезно для функций с
несколькими необязательными аргументами или с [логическими][boolean]
аргументами, значение которых неочевидно без сопровождающего имени.
Именованные аргументы используют тот же синтаксис, что
[объявления переменных][variable declarations] и
[необязательные аргументы][optional arguments].

[variable declarations]: https://sass-lang.com/documentation/variables
[boolean]: https://sass-lang.com/documentation/values/booleans
[optional arguments]: #optional-arguments

**SCSS**

```scss
$primary-color: #036;
.banner {
  background-color: $primary-color;
  color: scale-color($primary-color, $lightness: +40%);
}
```

**Sass**

```sass
$primary-color: #036
.banner
  background-color: $primary-color
  color: scale-color($primary-color, $lightness: +40%)
```

**Результат CSS**

```css
.banner {
  background-color: #036;
  color: rgb(10.2, 132.6, 255);
}
```

> ⚠️ **Внимание**
>
> Поскольку *любой* аргумент можно передать по имени, будьте осторожны при
> переименовании аргументов функции... это может сломать код ваших
> пользователей! Может быть полезно на некоторое время сохранить старое имя в
> качестве [необязательного аргумента][optional argument] и выводить
> [предупреждение][warning], если кто-то его передаёт, чтобы они знали о
> необходимости перейти на новый аргумент.

[optional argument]: #optional-arguments
[warning]: https://sass-lang.com/documentation/at-rules/warn

### Приём произвольных аргументов

Иногда функции полезно уметь принимать любое количество аргументов. Если
последний аргумент в объявлении `@function` заканчивается на `...`, то все
дополнительные аргументы, переданные этой функции, передаются в этот аргумент
в виде [списка][list]. Такой аргумент называется
[списком аргументов][argument list].

[list]: https://sass-lang.com/documentation/values/lists
[argument list]: https://sass-lang.com/documentation/values/lists#argument-lists

**SCSS**

```scss
@function sum($numbers...) {
  $sum: 0;
  @each $number in $numbers {
    $sum: $sum + $number;
  }
  @return $sum;
}

.micro {
  width: sum(50px, 30px, 100px);
}
```

**Sass**

```sass
@function sum($numbers...)
  $sum: 0
  @each $number in $numbers
    $sum: $sum + $number

  @return $sum


.micro
  width: sum(50px, 30px, 100px)
```

**Результат CSS**

```css
.micro {
  width: 180px;
}
```

#### Приём произвольных именованных аргументов

Списки аргументов также можно использовать для приёма произвольных
именованных аргументов. Функция [`meta.keywords()`][`meta.keywords()` function]
принимает список аргументов и возвращает любые дополнительные именованные
аргументы, переданные функции, в виде [карты (map)][map] из имён аргументов
(без `$`) в значения этих аргументов.

[`meta.keywords()` function]: https://sass-lang.com/documentation/modules/meta#keywords
[map]: https://sass-lang.com/documentation/values/maps

> 💡 **Занятный факт**
>
> Если вы никогда не передаёте список аргументов функции
> [`meta.keywords()`][`meta.keywords()` function], этот список аргументов не
> будет допускать дополнительные именованные аргументы. Это помогает
> вызывающим вашу функцию убедиться, что они случайно не опечатались в имени
> какого-либо аргумента.

[`meta.keywords()` function]: https://sass-lang.com/documentation/modules/meta#keywords

#### Передача произвольных аргументов

Подобно тому, как списки аргументов позволяют функциям принимать произвольные
позиционные или именованные аргументы, тот же синтаксис можно использовать
для *передачи* позиционных и именованных аргументов функции. Если вы
передаёте список, за которым следует `...`, в качестве последнего аргумента
вызова функции, его элементы будут рассматриваться как дополнительные
позиционные аргументы. Аналогично, карта, за которой следует `...`, будет
рассматриваться как дополнительные именованные аргументы. Вы даже можете
передать и то, и другое одновременно!

**SCSS**

```scss
$widths: 50px, 30px, 100px;
.micro {
  width: min($widths...);
}
```

**Sass**

```sass
$widths: 50px, 30px, 100px
.micro
  width: min($widths...)
```

**Результат CSS**

```css
.micro {
  width: 30px;
}
```

> 💡 **Занятный факт**
>
> Поскольку [список аргументов][argument list] отслеживает как позиционные,
> так и именованные аргументы, вы можете использовать его, чтобы передать оба
> вида сразу в другую функцию. Это делает невероятно простым определение
> псевдонима для функции!

[argument list]: https://sass-lang.com/documentation/values/lists#argument-lists

**SCSS**

```scss
@function fg($args...) {
  @warn "The fg() function is deprecated. Call foreground() instead.";
  @return foreground($args...);
}
```

**Sass**

```sass
@function fg($args...)
  @warn "The fg() function is deprecated. Call foreground() instead."
  @return foreground($args...)
```

## `@return`

At-правило `@return` указывает значение, используемое в качестве результата
вызова функции. Оно допустимо только внутри тела `@function`, и каждая
`@function` должна заканчиваться `@return`.

Когда встречается `@return`, функция немедленно завершается и возвращает свой
результат. Ранний возврат может быть полезен для обработки крайних случаев
или случаев, для которых доступен более эффективный алгоритм, без
необходимости оборачивать всю функцию в блок [`@else`][`@else` block].

[`@else` block]: https://sass-lang.com/documentation/at-rules/control/if#else

**SCSS**

```scss
@use "sass:string";

@function str-insert($string, $insert, $index) {
  // Avoid making new strings if we don't need to.
  @if string.length($string) == 0 {
    @return $insert;
  }

  $before: string.slice($string, 0, $index);
  $after: string.slice($string, $index);
  @return $before + $insert + $after;
}
```

**Sass**

```sass
@use "sass:string"

@function str-insert($string, $insert, $index)
  // Avoid making new strings if we don't need to.
  @if string.length($string) == 0
    @return $insert


  $before: string.slice($string, 0, $index)
  $after: string.slice($string, $index)
  @return $before + $insert + $after
```

## Другие функции

Помимо пользовательских функций, Sass предоставляет обширную
[базовую библиотеку][core library] встроенных функций, которые всегда
доступны для использования. Реализации Sass также позволяют определять
[пользовательские функции][custom functions] на языке хоста. И, конечно, вы
всегда можете вызывать [обычные функции CSS][plain CSS functions] (даже с
[необычным синтаксисом][weird syntax]).

[core library]: https://sass-lang.com/documentation/modules
[custom functions]: https://sass-lang.com/documentation/js-api/interfaces/LegacySharedOptions#functions
[plain CSS functions]: #plain-css-functions
[weird syntax]: https://sass-lang.com/documentation/syntax/special-functions

### Обычные функции CSS

Любой вызов функции, не являющейся ни пользовательской, ни
[встроенной](https://sass-lang.com/documentation/modules) функцией,
компилируется в обычную функцию CSS (если только он не использует
[синтаксис аргументов Sass](https://sass-lang.com/documentation/at-rules/function/#arguments)).
Аргументы будут скомпилированы в CSS и включены в вызов функции как есть. Это
гарантирует, что Sass поддерживает все функции CSS без необходимости
выпускать новые версии каждый раз, когда добавляется новая функция.

**SCSS**

```scss
@debug var(--main-bg-color); // var(--main-bg-color)

$primary: #f2ece4;
$accent: #e1d7d2;
@debug radial-gradient($primary, $accent); // radial-gradient(#f2ece4, #e1d7d2)
```

**Sass**

```sass
@debug var(--main-bg-color)  // var(--main-bg-color)

$primary: #f2ece4
$accent: #e1d7d2
@debug radial-gradient($primary, $accent)  // radial-gradient(#f2ece4, #e1d7d2)
```

> ⚠️ **Внимание**
>
> Поскольку любая неизвестная функция будет скомпилирована в CSS, легко
> пропустить опечатку в имени функции. Рассмотрите возможность запуска
> [линтера CSS][CSS linter] для результата вашей таблицы стилей, чтобы
> получать уведомление, когда это происходит!

[CSS linter]: https://stylelint.io/

> 💡 **Занятный факт**
>
> Некоторые функции CSS, такие как `calc()` и `element()`, имеют необычный
> синтаксис. Sass [обрабатывает эти функции особым образом][parses these functions specially]
> как [строки без кавычек][unquoted strings].

[parses these functions specially]: https://sass-lang.com/documentation/syntax/special-functions
[unquoted strings]: https://sass-lang.com/documentation/values/strings#unquoted

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/function/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

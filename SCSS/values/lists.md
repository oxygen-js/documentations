# Списки

**Совместимость (квадратные скобки):** Dart Sass: ✓ · LibSass: начиная с 3.5.0 · Ruby Sass: начиная с 3.5.0

Старые версии LibSass и Ruby Sass не поддерживали списки с квадратными скобками.

Списки содержат последовательность других значений. В Sass элементы списка могут
разделяться запятыми (`Helvetica, Arial, sans-serif`), пробелами (`10px 15px 0 0`)
или [слэшами], если это единообразно в пределах списка. В отличие от большинства
других языков, спискам в Sass не нужны специальные скобки: любые [выражения],
разделённые пробелами или запятыми, считаются списком. Тем не менее вы можете
записывать списки в квадратных скобках (`[line1 line2]`), что полезно при
использовании [`grid-template-columns`].

[слэшами]: #slash-separated-lists
[выражения]: https://sass-lang.com/documentation/syntax/structure#expressions
[`grid-template-columns`]: https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns

При записи списков без скобок можно использовать круглые скобки, чтобы вкладывать
списки друг в друга или устранять неоднозначность между разделителями списка и
другими случаями использования пробелов или запятых. Например, `(1, 2), (3, 4)` —
это список, содержащий два списка, каждый из которых содержит два числа; а
`adjust-font-stack((Helvetica, Arial, sans-serif))` передаёт функции
`adjust-font-stack` единственный аргумент, содержащий три названия шрифтов.

Списки в Sass могут содержать один элемент или даже ноль элементов. Список с одним
элементом можно записать как `(<expression>,)` либо как `[<expression>]`, а список
без элементов — как `()` либо как `[]`. Кроме того, все [функции для работы со
списками][] рассматривают отдельные значения, не являющиеся списками, так, как будто
это списки, содержащие данное значение, поэтому вам редко приходится явно создавать
списки из одного элемента.

[функции для работы со списками]: https://sass-lang.com/documentation/modules/list

> ⚠️ **Внимание**
>
> Пустые списки без скобок не являются допустимым CSS, поэтому Sass не позволит
> использовать такой список в значении свойства.

## Списки, разделённые слэшами

Списки в Sass могут разделяться слэшами — для представления таких значений, как
сокращённая запись `font: 12px/30px` для задания `font-size` и `line-height`, или
синтаксис `hsl(80 100% 50% / 0.5)` для создания цвета с заданной прозрачностью.
Однако **на данный момент списки, разделённые слэшами, нельзя записать буквально.**
Исторически Sass использовал символ `/` для обозначения деления, поэтому, пока
существующие таблицы стилей переходят на использование [`math.div()`], списки,
разделённые слэшами, можно создавать только с помощью [`list.slash()`].

[`math.div()`]: https://sass-lang.com/documentation/modules/math#div
[`list.slash()`]: https://sass-lang.com/documentation/modules/list#slash

Подробнее см. [Ломающее изменение: слэш как деление].

[Ломающее изменение: слэш как деление]: https://sass-lang.com/documentation/breaking-changes/slash-div

## Использование списков

Sass предоставляет набор [функций][], которые позволяют использовать списки для
написания мощных библиотек стилей или для того, чтобы сделать таблицу стилей вашего
приложения чище и удобнее в поддержке.

[функций]: https://sass-lang.com/documentation/modules/list

### Индексы

Многие из этих функций принимают или возвращают числа, называемые *индексами*,
которые указывают на элементы списка. Индекс 1 обозначает первый элемент списка.
Обратите внимание: это отличается от многих языков программирования, где индексы
начинаются с 0! Sass также позволяет легко обращаться к концу списка. Индекс -1
указывает на последний элемент списка, -2 — на предпоследний, и так далее.

### Доступ к элементу

Списки не очень полезны, если из них нельзя извлечь значения. Вы можете
использовать [функцию `list.nth($list, $n)`][], чтобы получить элемент по заданному
индексу в списке. Первый аргумент — сам список, а второй — индекс значения, которое
вы хотите получить.

[функцию `list.nth($list, $n)`]: https://sass-lang.com/documentation/modules/list#nth

**SCSS**

```scss
@use 'sass:list';

@debug list.nth(10px 12px 16px, 2); // 12px
@debug list.nth([line1, line2, line3], -1); // line3
```

**Sass**

```sass
@use 'sass:list'

@debug list.nth(10px 12px 16px, 2)  // 12px
@debug list.nth([line1, line2, line3], -1)  // line3
```

### Выполнение действия для каждого элемента

Здесь функция на самом деле не используется, но это всё равно один из самых
распространённых способов работы со списками. [Правило `@each`][] выполняет блок
стилей для каждого элемента списка и присваивает этот элемент переменной.

[правило `@each`]: https://sass-lang.com/documentation/at-rules/control/each

**SCSS**

```scss
$sizes: 40px, 50px, 80px;

@each $size in $sizes {
  .icon-#{$size} {
    font-size: $size;
    height: $size;
    width: $size;
  }
}
```

**Sass**

```sass
$sizes: 40px, 50px, 80px

@each $size in $sizes
  .icon-#{$size}
    font-size: $size
    height: $size
    width: $size
```

**CSS output**

```css
.icon-40px {
  font-size: 40px;
  height: 40px;
  width: 40px;
}

.icon-50px {
  font-size: 50px;
  height: 50px;
  width: 50px;
}

.icon-80px {
  font-size: 80px;
  height: 80px;
  width: 80px;
}
```

### Добавление в список

Также полезно добавлять элементы в список. [Функция `list.append($list, $val)`][]
принимает список и значение и возвращает копию списка с добавленным в конец
значением. Обратите внимание: поскольку списки в Sass [неизменяемы][], это не
изменяет исходный список.

[Функция `list.append($list, $val)`]: https://sass-lang.com/documentation/modules/list#append
[неизменяемы]: #immutability

**SCSS**

```scss
@debug append(10px 12px 16px, 25px); // 10px 12px 16px 25px
@debug append([col1-line1], col1-line2); // [col1-line1, col1-line2]
```

**Sass**

```sass
@debug append(10px 12px 16px, 25px)  // 10px 12px 16px 25px
@debug append([col1-line1], col1-line2)  // [col1-line1, col1-line2]
```

### Поиск элемента в списке

Если нужно проверить, есть ли элемент в списке, или узнать, под каким индексом он
находится, используйте [функцию `list.index($list, $value)`][]. Она принимает
список и значение для поиска в этом списке и возвращает индекс этого значения.

[функцию `list.index($list, $value)`]: https://sass-lang.com/documentation/modules/list#index

**SCSS**

```scss
@use 'sass:list';

@debug list.index(1px solid red, 1px); // 1
@debug list.index(1px solid red, solid); // 2
@debug list.index(1px solid red, dashed); // null
```

**Sass**

```sass
@use 'sass:list'

@debug list.index(1px solid red, 1px)  // 1
@debug list.index(1px solid red, solid)  // 2
@debug list.index(1px solid red, dashed)  // null
```

Если значения нет в списке вообще, `list.index()` возвращает [`null`][]. Поскольку
`null` является [ложным][] (falsy), вы можете использовать `list.index()` вместе с
[`@if`][] или [`if()`], чтобы проверить, содержит ли список заданное значение.

[`null`]: https://sass-lang.com/documentation/values/null
[ложным]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness
[`@if`]: https://sass-lang.com/documentation/at-rules/control/if
[`if()`]: https://sass-lang.com/documentation/syntax/special-functions#if

**SCSS**

```scss
@use "sass:list";

$valid-sides: top, bottom, left, right;

@mixin attach($side) {
  @if not list.index($valid-sides, $side) {
    @error "#{$side} is not a valid side. Expected one of #{$valid-sides}.";
  }

  // ...
}
```

**Sass**

```sass
@use "sass:list"

$valid-sides: top, bottom, left, right

@mixin attach($side)
  @if not list.index($valid-sides, $side)
    @error "#{$side} is not a valid side. Expected one of #{$valid-sides}."


  // ...
```

## Неизменяемость

Списки в Sass *неизменяемы* — это значит, что содержимое значения-списка никогда не
меняется. Все функции для работы со списками в Sass возвращают новые списки, а не
изменяют исходные. Неизменяемость помогает избежать множества коварных ошибок,
которые могут возникнуть, если один и тот же список используется в разных частях
таблицы стилей.

Тем не менее вы всё ещё можете обновлять состояние во времени, присваивая новые
списки той же переменной. Это часто используется в функциях и миксинах, чтобы
собрать несколько значений в один список.

**SCSS**

```scss
@use "sass:list";
@use "sass:map";

$prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);

@function prefixes-for-browsers($browsers) {
  $prefixes: ();
  @each $browser in $browsers {
    $prefixes: list.append($prefixes, map.get($prefixes-by-browser, $browser));
  }
  @return $prefixes;
}

@debug prefixes-for-browsers("firefox" "ie"); // moz ms
```

**Sass**

```sass
@use "sass:list"
@use "sass:map"

$prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms)

@function prefixes-for-browsers($browsers)
  $prefixes: ()
  @each $browser in $browsers
    $prefixes: list.append($prefixes, map.get($prefixes-by-browser, $browser))

  @return $prefixes


@debug prefixes-for-browsers("firefox" "ie")  // moz ms
```

## Списки аргументов

Когда вы объявляете миксин или функцию, принимающую [произвольные аргументы][],
получаемое значение — это специальный список, известный как *список аргументов*. Он
ведёт себя как обычный список, содержащий все аргументы, переданные миксину или
функции, но с одной дополнительной особенностью: если пользователь передал
именованные аргументы, к ним можно обратиться как к карте, передав список
аргументов в [функцию `meta.keywords()`][].

[произвольные аргументы]: https://sass-lang.com/documentation/at-rules/mixin#taking-arbitrary-arguments
[функцию `meta.keywords()`]: https://sass-lang.com/documentation/modules/meta#keywords

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

**CSS output**

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

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/lists/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

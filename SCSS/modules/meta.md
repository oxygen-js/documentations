# sass:meta

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

В настоящее время загрузку встроенных модулей с помощью `@use` поддерживает только Dart Sass. Пользователям других реализаций вместо этого нужно вызывать функции по их глобальным именам.

## Миксины

---

**`meta.apply($mixin, $args...)`**

**Совместимость:** Dart Sass: начиная с 1.69.0 · LibSass: ✗ · Ruby Sass: ✗

Подключает `$mixin` с `$args`. Если передан [блок `@content`], он перенаправляется в `$mixin`.

[блок `@content`]: https://sass-lang.com/documentation/at-rules/mixin#content-blocks

`$mixin` должен быть [значением миксина], например таким, которое возвращает [`meta.get-mixin()`].

[значением миксина]: https://sass-lang.com/documentation/values/mixins
[`meta.get-mixin()`]: #get-mixin

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

**Результат CSS**

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

**`meta.load-css($url, $with: null)`**

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

В настоящее время этот миксин поддерживает только Dart Sass.

Загружает [модуль] по адресу `$url` и включает его CSS так, как если бы он был написан как содержимое этого миксина. Параметр `$with` задаёт [конфигурацию] для модуля; если он передан, это должна быть карта (map), сопоставляющая имена переменных (без `$`) значениям, которые нужно использовать для этих переменных в загружаемом модуле.

[модуль]: https://sass-lang.com/documentation/at-rules/use
[конфигурацию]: https://sass-lang.com/documentation/at-rules/use#configuration

Если `$url` является относительным, он интерпретируется относительно файла, в котором подключён `meta.load-css()`.

**Как и [правило `@use`]**:

[правило `@use`]: https://sass-lang.com/documentation/at-rules/use

* Данный модуль будет вычислен только один раз, даже если он загружается несколько раз разными способами.

* Он не может передать конфигурацию модулю, который уже был загружен, — независимо от того, был ли тот загружен с конфигурацией или нет.

**В отличие от [правила `@use`]**:

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

* Он не делает члены загруженного модуля доступными в текущем модуле.

* Его можно использовать где угодно в таблице стилей. Его даже можно вкладывать в стилевые правила, чтобы создавать вложенные стили!

* URL загружаемого модуля может браться из переменной и включать [интерполяцию].

  [интерполяцию]: https://sass-lang.com/documentation/interpolation

> ⚠️ **Внимание**
>
> Параметр `$url` должен быть строкой, содержащей URL, как тот, что вы передали бы в правило `@use`. Это не должен быть CSS `url()`!

**SCSS**

```scss
// dark-theme/_code.scss
$border-contrast: false !default;

code {
  background-color: #6b717f;
  color: #d2e1dd;
  @if $border-contrast {
    border-color: #dadbdf;
  }
}
```
```scss
// style.scss
@use "sass:meta";

body.dark {
  @include meta.load-css("dark-theme/code",
      $with: ("border-contrast": true));
}
```

**Sass**

```sass
// dark-theme/_code.sass
$border-contrast: false !default

code
  background-color: #6b717f
  color: #d2e1dd
  @if $border-contrast
    border-color: #dadbdf
```
```sass
// style.sass
@use "sass:meta"

body.dark
  $configuration: ("border-contrast": true)
  @include meta.load-css("dark-theme/code", $with: $configuration)
```

**Результат CSS**

```css
body.dark code {
  background-color: #6b717f;
  color: #d2e1dd;
  border-color: #dadbdf;
}
```

## Функции

---

**`meta.accepts-content($mixin)`**

*Возвращает: `boolean`*

**Совместимость:** Dart Sass: начиная с 1.69.0 · LibSass: ✗ · Ruby Sass: ✗

Возвращает, может ли переданное [значение миксина] принимать [блок `@content`].

[значение миксина]: https://sass-lang.com/documentation/values/mixins
[блок `@content`]: https://sass-lang.com/documentation/at-rules/mixin#content-blocks

Возвращает true, если миксин _может_ принять блок `@content`, даже если он делает это не всегда.

---

**`meta.calc-args($calc)`**

*Возвращает: `list`*

**Совместимость:** Dart Sass: начиная с 1.40.0 · LibSass: ✗ · Ruby Sass: ✗

Возвращает аргументы для переданного [вычисления].

[вычисления]: https://sass-lang.com/documentation/values/calculations

Если аргумент — число или вложенное вычисление, он возвращается как значение этого типа. В противном случае он возвращается как строка без кавычек.

**SCSS**

```scss
@use 'sass:meta';

@debug meta.calc-args(calc(100px + 10%)); // unquote("100px + 10%")
@debug meta.calc-args(clamp(50px, var(--width), 1000px)); // 50px, unquote("var(--width)"), 1000px
```

**Sass**

```sass
@use 'sass:meta'

@debug meta.calc-args(calc(100px + 10%))  // unquote("100px + 10%")
@debug meta.calc-args(clamp(50px, var(--width), 1000px))  // 50px, unquote("var(--width)"), 1000px
```

---

**`meta.calc-name($calc)`**

*Возвращает: `quoted string`*

**Совместимость:** Dart Sass: начиная с 1.40.0 · LibSass: ✗ · Ruby Sass: ✗

Возвращает имя переданного [вычисления].

[вычисления]: https://sass-lang.com/documentation/values/calculations

**SCSS**

```scss
@use 'sass:meta';

@debug meta.calc-name(calc(100px + 10%)); // "calc"
@debug meta.calc-name(clamp(50px, var(--width), 1000px)); // "clamp"
```

**Sass**

```sass
@use 'sass:meta'

@debug meta.calc-name(calc(100px + 10%))  // "calc"
@debug meta.calc-name(clamp(50px, var(--width), 1000px))  // "clamp"
```

---

**`meta.call($function, $args...)`**

**`call($function, $args...)`**

**Совместимость (тип аргумента):** Dart Sass: ✓ · LibSass: начиная с 3.5.0 · Ruby Sass: начиная с 3.5.0

В более старых версиях LibSass и Ruby Sass [функция `call()`] принимала строку, представляющую имя функции. Это было изменено так, чтобы вместо этого принималось значение функции, — в рамках подготовки к новой модульной системе, где функции больше не глобальны и поэтому одно и то же имя не всегда ссылается на одну и ту же функцию.

[функция `call()`]: https://sass-lang.com/documentation/modules/meta#call

Передача строки в `call()` по-прежнему работает во всех реализациях, но такой способ считается устаревшим и будет запрещён в будущих версиях.

Вызывает `$function` с `$args` и возвращает результат.

`$function` должен быть [значением функции], например таким, которое возвращает [`meta.get-function()`].

[значением функции]: https://sass-lang.com/documentation/values/functions
[`meta.get-function()`]: #get-function

**SCSS**

```scss
@use "sass:list";
@use "sass:meta";
@use "sass:string";

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition) {
  $new-list: ();
  $separator: list.separator($list);
  @each $element in $list {
    @if not meta.call($condition, $element) {
      $new-list: list.append($new-list, $element, $separator: $separator);
    }
  }
  @return $new-list;
}

$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;

.content {
  @function contains-helvetica($string) {
    @return string.index($string, "Helvetica");
  }
  font-family: remove-where($fonts, meta.get-function("contains-helvetica"));
}
```

**Sass**

```sass
@use "sass:list"
@use "sass:meta"
@use "sass:string"

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition)
  $new-list: ()
  $separator: list.separator($list)
  @each $element in $list
    @if not meta.call($condition, $element)
      $new-list: list.append($new-list, $element, $separator: $separator)


  @return $new-list


$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif

.content
  @function contains-helvetica($string)
    @return string.index($string, "Helvetica")

  font-family: remove-where($fonts, meta.get-function("contains-helvetica"))
```

**Результат CSS**

```css
.content {
  font-family: Tahoma, Geneva, Arial, sans-serif;
}
```

---

**`meta.content-exists()`**

**`content-exists()`**

*Возвращает: `boolean`*

Возвращает, был ли текущему миксину передан [блок `@content`].

[блок `@content`]: https://sass-lang.com/documentation/at-rules/mixin#content-blocks

Выбрасывает ошибку, если вызвана вне миксина.

**SCSS**

```scss
@use 'sass:meta';

@mixin debug-content-exists {
  @debug meta.content-exists();
  @content;
}

@include debug-content-exists; // false
@include debug-content-exists { // true
  // Content!
}
```

**Sass**

```sass
@use 'sass:meta'

@mixin debug-content-exists
  @debug meta.content-exists()
  @content


@include debug-content-exists  // false
@include debug-content-exists   // true
  // Content!
```

---

**`meta.feature-exists($feature)`**

**`feature-exists($feature)`**

*Возвращает: `boolean`*

Возвращает, поддерживает ли текущая реализация Sass `$feature`.

`$feature` должен быть строкой. В настоящее время распознаются следующие возможности:

* `global-variable-shadowing` означает, что локальная переменная будет [затенять] глобальную переменную, если у неё нет флага `!global`.
* `extend-selector-pseudoclass` означает, что [правило `@extend`] будет влиять на селекторы, вложенные в псевдоклассы наподобие `:not()`.
* `units-level3` означает, что [арифметика единиц измерения] поддерживает единицы, определённые в [CSS Values and Units Level 3].
* `at-error` означает, что поддерживается [правило `@error`].
* `custom-property` означает, что значения [объявления пользовательского свойства] не поддерживают никаких [выражений], кроме [интерполяции].

[затенять]: https://sass-lang.com/documentation/variables#shadowing
[правило `@extend`]: https://sass-lang.com/documentation/at-rules/extend
[арифметика единиц измерения]: https://sass-lang.com/documentation/values/numbers#units
[CSS Values and Units Level 3]: http://www.w3.org/TR/css3-values
[правило `@error`]: https://sass-lang.com/documentation/at-rules/error
[объявления пользовательского свойства]: https://sass-lang.com/documentation/style-rules/declarations#custom-properties
[выражений]: https://sass-lang.com/documentation/syntax/structure#expressions
[интерполяции]: https://sass-lang.com/documentation/interpolation

Возвращает `false` для любого нераспознанного `$feature`.

> ⚠️ **Внимание**
>
> Эта функция устарела, использовать её не стоит. Подробнее см. [страницу о ломающем изменении].
>
> [страницу о ломающем изменении]: https://sass-lang.com/documentation/breaking-changes/feature-exists

**SCSS**

```scss
@use "sass:meta";

@debug meta.feature-exists("at-error"); // true
@debug meta.feature-exists("unrecognized"); // false
```

**Sass**

```sass
@use "sass:meta"

@debug meta.feature-exists("at-error")  // true
@debug meta.feature-exists("unrecognized")  // false
```

---

**`meta.function-exists($name, $module: null)`**

**`function-exists($name)`**

*Возвращает: `boolean`*

Возвращает, определена ли функция с именем `$name` — либо как встроенная функция, либо как пользовательская.

Если передан `$module`, также проверяется наличие определения функции в модуле с именем `$module`. `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

**SCSS**

```scss
@use "sass:meta";
@use "sass:math";

@debug meta.function-exists("div", "math"); // true
@debug meta.function-exists("scale-color"); // true
@debug meta.function-exists("add"); // false

@function add($num1, $num2) {
  @return $num1 + $num2;
}
@debug meta.function-exists("add"); // true
```

**Sass**

```sass
@use "sass:meta"
@use "sass:math"

@debug meta.function-exists("div", "math")  // true
@debug meta.function-exists("scale-color")  // true
@debug meta.function-exists("add")  // false

@function add($num1, $num2)
  @return $num1 + $num2

@debug meta.function-exists("add")  // true
```

---

**`meta.get-function($name, $css: false, $module: null)`**

**`get-function($name, $css: false, $module: null)`**

*Возвращает: `function`*

Возвращает [значение функции] с именем `$name`.

[значение функции]: https://sass-lang.com/documentation/values/functions

Если `$module` — `null`, возвращается функция с именем `$name` без пространства имён (включая [глобальные встроенные функции]). В противном случае `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле, и в этом случае возвращается функция с именем `$name` из этого модуля.

[глобальные встроенные функции]: https://sass-lang.com/documentation/modules#global-functions
[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

По умолчанию выбрасывается ошибка, если `$name` не ссылается на функцию Sass. Однако если `$css` — `true`, вместо этого возвращается [обычная функция CSS].

[обычная функция CSS]: https://sass-lang.com/documentation/at-rules/function/#plain-css-functions

Возвращённую функцию можно вызвать с помощью [`meta.call()`](#call).

**SCSS**

```scss
@use "sass:list";
@use "sass:meta";
@use "sass:string";

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition) {
  $new-list: ();
  $separator: list.separator($list);
  @each $element in $list {
    @if not meta.call($condition, $element) {
      $new-list: list.append($new-list, $element, $separator: $separator);
    }
  }
  @return $new-list;
}

$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;

.content {
  @function contains-helvetica($string) {
    @return string.index($string, "Helvetica");
  }
  font-family: remove-where($fonts, meta.get-function("contains-helvetica"));
}
```

**Sass**

```sass
@use "sass:list"
@use "sass:meta"
@use "sass:string"

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition)
  $new-list: ()
  $separator: list.separator($list)
  @each $element in $list
    @if not meta.call($condition, $element)
      $new-list: list.append($new-list, $element, $separator: $separator)


  @return $new-list


$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif

.content
  @function contains-helvetica($string)
    @return string.index($string, "Helvetica")

  font-family: remove-where($fonts, meta.get-function("contains-helvetica"))
```

**Результат CSS**

```css
.content {
  font-family: Tahoma, Geneva, Arial, sans-serif;
}
```

---

**`meta.get-mixin($name, $module: null)`**

*Возвращает: `function`*

**Совместимость:** Dart Sass: начиная с 1.69.0 · LibSass: ✗ · Ruby Sass: ✗

Возвращает [значение миксина] с именем `$name`.

[значение миксина]: https://sass-lang.com/documentation/values/mixins

Если `$module` — `null`, возвращается миксин с именем `$name`, определённый в текущем модуле. В противном случае `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле, и в этом случае возвращается миксин с именем `$name` из этого модуля.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

По умолчанию выбрасывается ошибка, если `$name` не ссылается на миксин.

Возвращённый миксин можно подключить с помощью [`meta.apply()`](#apply).

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

**Результат CSS**

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

**`meta.global-variable-exists($name, $module: null)`**

**`global-variable-exists($name, $module: null)`**

*Возвращает: `boolean`*

Возвращает, существует ли [глобальная переменная] с именем `$name` (без `$`).

[глобальная переменная]: https://sass-lang.com/documentation/variables#scope

Если `$module` — `null`, возвращается, существует ли переменная с именем `$name` без пространства имён. В противном случае `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле, и в этом случае возвращается, есть ли в этом модуле переменная с именем `$name`.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

См. также [`meta.variable-exists()`](#variable-exists).

**SCSS**

```scss
@use "sass:meta";

@debug meta.global-variable-exists("var1"); // false

$var1: value;
@debug meta.global-variable-exists("var1"); // true

h1 {
  // $var2 is local.
  $var2: value;
  @debug meta.global-variable-exists("var2"); // false
}
```

**Sass**

```sass
@use "sass:meta"

@debug meta.global-variable-exists("var1")  // false

$var1: value
@debug meta.global-variable-exists("var1")  // true

h1
  // $var2 is local.
  $var2: value
  @debug meta.global-variable-exists("var2")  // false
```

---

**`meta.inspect($value)`**

**`inspect($value)`**

*Возвращает: `unquoted string`*

Возвращает строковое представление `$value`.

Возвращает представление *любого* значения Sass, а не только тех, что могут быть представлены в CSS. Поэтому не гарантируется, что возвращаемое значение является корректным CSS.

> ⚠️ **Внимание**
>
> Эта функция предназначена для отладки; формат её вывода не гарантированно одинаков в разных версиях и реализациях Sass.

**SCSS**

```scss
@use "sass:meta";

@debug meta.inspect(10px 20px 30px); // unquote("10px 20px 30px")
@debug meta.inspect(("width": 200px)); // unquote('("width": 200px)')
@debug meta.inspect(null); // unquote("null")
@debug meta.inspect("Helvetica"); // unquote('"Helvetica"')
```

**Sass**

```sass
@use "sass:meta"

@debug meta.inspect(10px 20px 30px)  // unquote("10px 20px 30px")
@debug meta.inspect(("width": 200px))  // unquote('("width": 200px)')
@debug meta.inspect(null)  // unquote("null")
@debug meta.inspect("Helvetica")  // unquote('"Helvetica"')
```

---

**`meta.keywords($args)`**

**`keywords($args)`**

*Возвращает: `map`*

Возвращает именованные аргументы, переданные миксину или функции, которая принимает [произвольные аргументы]. Аргумент `$args` должен быть [списком аргументов].

[произвольные аргументы]: https://sass-lang.com/documentation/at-rules/mixin#taking-arbitrary-arguments
[списком аргументов]: https://sass-lang.com/documentation/values/lists#argument-lists

Именованные аргументы возвращаются в виде карты (map), где имена аргументов — строки без кавычек (без `$`) — сопоставлены значениям этих аргументов.

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

---

**`meta.mixin-exists($name, $module: null)`**

**`mixin-exists($name, $module: null)`**

*Возвращает: `boolean`*

Возвращает, существует ли [миксин] с именем `$name`.

[миксин]: https://sass-lang.com/documentation/at-rules/mixin

Если `$module` — `null`, возвращается, существует ли миксин с именем `$name` без пространства имён. В противном случае `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле, и в этом случае возвращается, есть ли в этом модуле миксин с именем `$name`.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

**SCSS**

```scss
@use "sass:meta";

@debug meta.mixin-exists("shadow-none"); // false

@mixin shadow-none {
  box-shadow: none;
}

@debug meta.mixin-exists("shadow-none"); // true
```

**Sass**

```sass
@use "sass:meta"

@debug meta.mixin-exists("shadow-none")  // false

@mixin shadow-none
  box-shadow: none


@debug meta.mixin-exists("shadow-none")  // true
```

---

**`meta.module-functions($module)`**

*Возвращает: `map`*

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

В настоящее время эту функцию поддерживает только Dart Sass.

Возвращает все функции, определённые в модуле, в виде карты, сопоставляющей имена функций [значениям функций].

[значениям функций]: https://sass-lang.com/documentation/values/functions

Параметр `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

**SCSS**

```scss
// _functions.scss
@function pow($base, $exponent) {
  $result: 1;
  @for $_ from 1 through $exponent {
    $result: $result * $base;
  }
  @return $result;
}
```
```scss
@use "sass:map";
@use "sass:meta";

@use "functions";

@debug meta.module-functions("functions"); // ("pow": get-function("pow"))

@debug meta.call(map.get(meta.module-functions("functions"), "pow"), 3, 4); // 81
```

**Sass**

```sass
// _functions.sass
@function pow($base, $exponent)
  $result: 1
  @for $_ from 1 through $exponent
    $result: $result * $base

  @return $result
```
```sass
@use "sass:map"
@use "sass:meta"

@use "functions"

@debug meta.module-functions("functions") // ("pow": get-function("pow"))

@debug meta.call(map.get(meta.module-functions("functions"), "pow"), 3, 4) // 81
```

---

**`meta.module-mixins($module)`**

*Возвращает: `map`*

**Совместимость:** Dart Sass: начиная с 1.69.0 · LibSass: ✗ · Ruby Sass: ✗

Возвращает все миксины, определённые в модуле, в виде карты, сопоставляющей имена миксинов [значениям миксинов].

[значениям миксинов]: https://sass-lang.com/documentation/values/mixins

Параметр `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

**SCSS**

```scss
// _mixins.scss
@mixin stretch() {
  align-items: stretch;
  display: flex;
  flex-direction: row;
}
```
```scss
@use "sass:map";
@use "sass:meta";

@use "mixins";

@debug meta.module-mixins("mixins"); // => ("stretch": get-mixin("stretch"))

.header {
  @include meta.apply(map.get(meta.module-mixins("mixins"), "stretch"));
}
```

**Sass**

```sass
// _mixins.scss
@mixin stretch()
  align-items: stretch
  display: flex
  flex-direction: row
```
```sass
@use "sass:map"
@use "sass:meta"

@use "mixins"

@debug meta.module-mixins("mixins") // => ("stretch": get-mixin("stretch"))

.header
  @include meta.apply(map.get(meta.module-mixins("mixins"), "stretch"))
```

**Результат CSS**

```css
.header {
  align-items: stretch;
  display: flex;
  flex-direction: row;
}
```

---

**`meta.module-variables($module)`**

*Возвращает: `map`*

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

В настоящее время эту функцию поддерживает только Dart Sass.

Возвращает все переменные, определённые в модуле, в виде карты, сопоставляющей имена переменных (без `$`) значениям этих переменных.

Параметр `$module` должен быть строкой, соответствующей пространству имён [правила `@use`] в текущем файле.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

**SCSS**

```scss
// _variables.scss
$hopbush: #c69;
$midnight-blue: #036;
$wafer: #e1d7d2;
```
```scss
@use "sass:meta";

@use "variables";

@debug meta.module-variables("variables");
// (
//   "hopbush": #c69,
//   "midnight-blue": #036,
//   "wafer": #e1d7d2
// )
```

**Sass**

```sass
// _variables.sass
$hopbush: #c69
$midnight-blue: #036
$wafer: #e1d7d2
```
```sass
@use "sass:meta"

@use "variables"

@debug meta.module-variables("variables")
// (
//   "hopbush": #c69,
//   "midnight-blue": #036,
//   "wafer": #e1d7d2
// )
```

---

**`meta.type-of($value)`**

**`type-of($value)`**

*Возвращает: `unquoted string`*

Возвращает тип `$value`.

Может возвращать следующие значения:

* [`number`](https://sass-lang.com/documentation/values/numbers)
* [`string`](https://sass-lang.com/documentation/values/strings)
* [`color`](https://sass-lang.com/documentation/values/colors)
* [`list`](https://sass-lang.com/documentation/values/lists)
* [`map`](https://sass-lang.com/documentation/values/maps)
* [`calculation`](https://sass-lang.com/documentation/values/calculations)
* [`bool`](https://sass-lang.com/documentation/values/booleans)
* [`null`](https://sass-lang.com/documentation/values/null)
* [`function`](https://sass-lang.com/documentation/values/functions)
* [`mixin`](https://sass-lang.com/documentation/values/mixins)
* [`arglist`](https://sass-lang.com/documentation/values/lists#argument-lists)

В будущем могут быть добавлены новые возможные значения. Для `()` может быть возвращено как `list`, так и `map` — в зависимости от того, было ли оно возвращено [функцией для работы с картами] (map function) или нет.

[функцией для работы с картами]: https://sass-lang.com/documentation/modules/map

**SCSS**

```scss
@use 'sass:meta';

@debug meta.type-of(10px); // number
@debug meta.type-of(10px 20px 30px); // list
@debug meta.type-of(()); // list
```

**Sass**

```sass
@use 'sass:meta'

@debug meta.type-of(10px)  // number
@debug meta.type-of(10px 20px 30px)  // list
@debug meta.type-of(())  // list
```

---

**`meta.variable-exists($name)`**

**`variable-exists($name)`**

*Возвращает: `boolean`*

Возвращает, существует ли переменная с именем `$name` (без `$`) в текущей [области видимости].

[области видимости]: https://sass-lang.com/documentation/variables#scope

См. также [`meta.global-variable-exists()`](#global-variable-exists).

**SCSS**

```scss
@use "sass:meta";

@debug meta.variable-exists("var1"); // false

$var1: value;
@debug meta.variable-exists("var1"); // true

h1 {
  // $var2 is local.
  $var2: value;
  @debug meta.variable-exists("var2"); // true
}
```

**Sass**

```sass
@use "sass:meta"

@debug meta.variable-exists("var1")  // false

$var1: value
@debug meta.variable-exists("var1")  // true

h1
  // $var2 is local.
  $var2: value
  @debug meta.variable-exists("var2")  // true
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/meta/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

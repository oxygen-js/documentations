# Переменные

Переменные Sass просты: вы присваиваете значение имени, начинающемуся с `$`, а затем можете обращаться по этому имени вместо самого значения. Но, несмотря на свою простоту, они — один из самых полезных инструментов, которые Sass привносит в разработку. Переменные позволяют уменьшить повторение кода, выполнять сложные вычисления, настраивать библиотеки и многое другое.

Объявление переменной во многом похоже на [объявление свойства][]: оно записывается как `<variable>: <expression>`. В отличие от свойства, которое можно объявить только внутри стилевого правила или at-правила, переменные можно объявлять где угодно. Чтобы использовать переменную, достаточно включить её в значение.

[объявление свойства]: https://sass-lang.com/documentation/style-rules/declarations

**SCSS**

```scss
$base-color: #c6538c;
$border-dark: rgba($base-color, 0.88);

.alert {
  border: 1px solid $border-dark;
}
```

**Sass**

```sass
$base-color: #c6538c
$border-dark: rgba($base-color, 0.88)

.alert
  border: 1px solid $border-dark
```

**Результат CSS**

```css
.alert {
  border: 1px solid rgba(198, 83, 140, 0.88);
}
```

> ⚠️ **Внимание**
>
> В CSS есть [свои переменные][], которые полностью отличаются от переменных Sass. Учитывайте эти различия!
>
> [свои переменные]: https://sass-lang.com/documentation/style-rules/declarations#custom-properties
>
> * Переменные Sass полностью компилируются и исчезают. Переменные CSS попадают в итоговый CSS.
>
> * Переменные CSS могут иметь разные значения для разных элементов, а у переменных Sass в любой момент времени только одно значение.
>
> * Переменные Sass *императивны* — это значит, что если вы используете переменную, а затем меняете её значение, более раннее использование останется прежним. Переменные CSS *декларативны* — это значит, что при изменении значения оно повлияет как на более ранние, так и на более поздние использования.

**SCSS**

```scss
$variable: value 1;
.rule-1 {
  value: $variable;
}

$variable: value 2;
.rule-2 {
  value: $variable;
}
```

**Sass**

```sass
$variable: value 1
.rule-1
  value: $variable


$variable: value 2
.rule-2
  value: $variable
```

**Результат CSS**

```css
.rule-1 {
  value: value 1;
}

.rule-2 {
  value: value 2;
}
```

> 💡 **Занятный факт**
>
> Переменные Sass, как и все идентификаторы Sass, считают дефисы и подчёркивания одинаковыми. Это значит, что `$font-size` и `$font_size` ссылаются на одну и ту же переменную. Это историческое наследие самых ранних дней Sass, когда в именах идентификаторов допускались *только* подчёркивания. Когда Sass добавил поддержку дефисов, чтобы соответствовать синтаксису CSS, оба варианта были сделаны эквивалентными — чтобы облегчить миграцию.

## Значения по умолчанию

Обычно, когда вы присваиваете переменной значение, если у этой переменной уже было значение, старое значение перезаписывается. Но если вы пишете библиотеку на Sass, вам может понадобиться дать пользователям возможность настраивать переменные вашей библиотеки перед тем, как вы используете их для генерации CSS.

Для этого Sass предоставляет флаг `!default`. Он присваивает значение переменной *только если* эта переменная ещё не определена или её значение — [`null`][]. В противном случае используется уже существующее значение.

[`null`]: https://sass-lang.com/documentation/values/null

### Настройка модулей

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

На данный момент `@use` поддерживает только Dart Sass. Пользователям других реализаций вместо этого нужно использовать [правило `@import`][].

[правило `@import`]: https://sass-lang.com/documentation/at-rules/import

Переменные, определённые с `!default`, можно настраивать при загрузке модуля с помощью [правила `@use`][]. Библиотеки Sass часто используют переменные с `!default`, чтобы позволить своим пользователям настраивать CSS библиотеки.

[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

Чтобы загрузить модуль с конфигурацией, напишите `@use <url> with (<variable>: <value>, <variable>: <value>)`. Заданные значения переопределят значения переменных по умолчанию. Настраивать можно только переменные, написанные на верхнем уровне таблицы стилей с флагом `!default`.

**SCSS**

```scss
// _library.scss
$black: #000 !default;
$border-radius: 0.25rem !default;
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default;

code {
  border-radius: $border-radius;
  box-shadow: $box-shadow;
}
```
```scss
// style.scss
@use 'library' with (
  $black: #222,
  $border-radius: 0.1rem
);
```

**Sass**

```sass
// _library.sass
$black: #000 !default
$border-radius: 0.25rem !default
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default

code
  border-radius: $border-radius
  box-shadow: $box-shadow
```
```sass
// style.sass
@use 'library' with ($black: #222, $border-radius: 0.1rem)
```

**Результат CSS**

```css
code {
  border-radius: 0.1rem;
  box-shadow: 0 0.5rem 1rem rgba(34, 34, 34, 0.15);
}
```

## Встроенные переменные

Переменные, определённые [встроенным модулем][], изменить нельзя.

[встроенным модулем]: https://sass-lang.com/documentation/modules

**SCSS**

```scss
@use "sass:math" as math;

// This assignment will fail.
math.$pi: 0;
```

**Sass**

```sass
@use "sass:math" as math

// This assignment will fail.
math.$pi: 0
```

## Область видимости

Переменные, объявленные на верхнем уровне таблицы стилей, являются *глобальными*. Это значит, что к ним можно обращаться в любом месте их модуля после того, как они были объявлены. Но это верно не для всех переменных. Переменные, объявленные внутри блоков (фигурные скобки в SCSS или блок с отступом в Sass), обычно являются *локальными* и доступны только внутри блока, в котором они объявлены.

**SCSS**

```scss
$global-variable: global value;

.content {
  $local-variable: local value;
  global: $global-variable;
  local: $local-variable;
}

.sidebar {
  global: $global-variable;

  // This would fail, because $local-variable isn't in scope:
  // local: $local-variable;
}
```

**Sass**

```sass
$global-variable: global value

.content
  $local-variable: local value
  global: $global-variable
  local: $local-variable


.sidebar
  global: $global-variable

  // This would fail, because $local-variable isn't in scope:
  // local: $local-variable
```

**Результат CSS**

```css
.content {
  global: global value;
  local: local value;
}

.sidebar {
  global: global value;
}
```

### Затенение

Локальную переменную можно даже объявить с тем же именем, что и у глобальной переменной. В этом случае фактически существуют две разные переменные с одинаковым именем: одна локальная и одна глобальная. Это помогает гарантировать, что автор, пишущий локальную переменную, случайно не изменит значение глобальной переменной, о существовании которой он даже не подозревает.

**SCSS**

```scss
$variable: global value;

.content {
  $variable: local value;
  value: $variable;
}

.sidebar {
  value: $variable;
}
```

**Sass**

```sass
$variable: global value

.content
  $variable: local value
  value: $variable


.sidebar
  value: $variable
```

**Результат CSS**

```css
.content {
  value: local value;
}

.sidebar {
  value: global value;
}
```

Если вам нужно задать значение глобальной переменной из локальной области видимости (например, внутри миксина), можно использовать флаг `!global`. Объявление переменной с флагом `!global` *всегда* присваивает значение в глобальной области видимости.

**SCSS**

```scss
$variable: first global value;

.content {
  $variable: second global value !global;
  value: $variable;
}

.sidebar {
  value: $variable;
}
```

**Sass**

```sass
$variable: first global value

.content
  $variable: second global value !global
  value: $variable


.sidebar
  value: $variable
```

**Результат CSS**

```css
.content {
  value: second global value;
}

.sidebar {
  value: second global value;
}
```

> ⚠️ **Внимание**
>
> **Совместимость:** Dart Sass: начиная с 2.0.0 · LibSass: ✗ · Ruby Sass: ✗
>
> Более старые версии Sass позволяли использовать `!global` для переменной, которая ещё не существует. Такое поведение было признано устаревшим, чтобы гарантировать, что каждая таблица стилей объявляет одни и те же переменные независимо от того, как она выполняется.
>
> Флаг `!global` можно использовать только для задания значения переменной, которая уже была объявлена на верхнем уровне файла. Его *нельзя* использовать для объявления новой переменной.

### Область видимости в управлении потоком выполнения

Переменные, объявленные в [правилах управления потоком выполнения][], имеют особые правила области видимости: они не затеняют переменные того же уровня, что и правило управления потоком выполнения. Вместо этого они просто присваивают значение этим переменным. Благодаря этому гораздо проще условно присваивать значение переменной или собирать значение в рамках цикла.

[правилах управления потоком выполнения]: https://sass-lang.com/documentation/at-rules/control

**SCSS**

```scss
$dark-theme: true !default;
$primary-color: #f8bbd0 !default;
$accent-color: #6a1b9a !default;

@if $dark-theme {
  $primary-color: darken($primary-color, 60%);
  $accent-color: lighten($accent-color, 60%);
}

.button {
  background-color: $primary-color;
  border: 1px solid $accent-color;
  border-radius: 3px;
}
```

**Sass**

```sass
$dark-theme: true !default
$primary-color: #f8bbd0 !default
$accent-color: #6a1b9a !default

@if $dark-theme
  $primary-color: darken($primary-color, 60%)
  $accent-color: lighten($accent-color, 60%)


.button
  background-color: $primary-color
  border: 1px solid $accent-color
  border-radius: 3px
```

**Результат CSS**

```css
.button {
  background-color: rgb(116.96, 12.04, 48.16);
  border: 1px solid rgb(245.4696132597, 235.4309392265, 251.5690607735);
  border-radius: 3px;
}
```

> ⚠️ **Внимание**
>
> Переменные в области видимости управления потоком выполнения могут присваивать значения существующим переменным во внешней области видимости, но новые переменные, объявленные в области видимости управления потоком выполнения, будут недоступны во внешней области видимости. Убедитесь, что переменная уже объявлена, прежде чем присваивать ей значение, даже если для этого нужно объявить её как `null`.

## Продвинутые функции для работы с переменными

Основная библиотека Sass предоставляет несколько продвинутых функций для работы с переменными. Функция [`meta.variable-exists()`][] возвращает, существует ли переменная с заданным именем в текущей области видимости, а функция [`meta.global-variable-exists()`][] делает то же самое, но только для глобальной области видимости.

[`meta.variable-exists()`]: https://sass-lang.com/documentation/modules/meta#variable-exists
[`meta.global-variable-exists()`]: https://sass-lang.com/documentation/modules/meta#global-variable-exists

> ⚠️ **Внимание**
>
> Иногда пользователям хочется использовать интерполяцию, чтобы определить имя переменной на основе другой переменной. Sass этого не позволяет, потому что так гораздо сложнее с первого взгляда понять, какие переменные где определены. Однако вы *можете* определить [карту (map)][] из имён в значения, к которой затем можно обращаться с помощью переменных.
>
> [карту (map)]: https://sass-lang.com/documentation/values/maps

**SCSS**

```scss
@use "sass:map";

$theme-colors: (
  "success": #28a745,
  "info": #17a2b8,
  "warning": #ffc107,
);

.alert {
  // Instead of $theme-color-#{warning}
  background-color: map.get($theme-colors, "warning");
}
```

**Sass**

```sass
@use "sass:map"

$theme-colors: ("success": #28a745, "info": #17a2b8, "warning": #ffc107)

.alert
  // Instead of $theme-color-#{warning}
  background-color: map.get($theme-colors, "warning")
```

**Результат CSS**

```css
.alert {
  background-color: #ffc107;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/variables/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

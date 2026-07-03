# @forward

Правило `@forward` загружает таблицу стилей Sass и делает её
[миксины](https://sass-lang.com/documentation/at-rules/mixin),
[функции](https://sass-lang.com/documentation/at-rules/function) и
[переменные](https://sass-lang.com/documentation/variables) доступными, когда
ваша таблица стилей загружается с помощью правила
[`@use`](https://sass-lang.com/documentation/at-rules/use). Оно позволяет
организовывать библиотеки Sass в виде множества файлов, при этом позволяя их
пользователям загружать единственный файл-точку входа.

Правило записывается как `@forward "<url>"`. Оно загружает модуль по
указанному URL точно так же, как `@use`, но делает
[публичные][public]-члены загруженного модуля доступными для пользователей
вашего модуля, как если бы они были определены непосредственно в вашем
модуле. Однако сами эти члены недоступны в вашем модуле — если вам это нужно,
придётся дополнительно написать правило `@use`. Не волнуйтесь, модуль будет
загружен только один раз!

[public]: https://sass-lang.com/documentation/at-rules/use#private-members

Если вы *действительно* пишете и `@forward`, и `@use` для одного и того же
модуля в одном и том же файле, всегда стоит писать `@forward` первым. Тогда,
если ваши пользователи захотят
[настроить перенаправляемый модуль][configure the forwarded module], эта
настройка будет применена к `@forward` до того, как ваш `@use` загрузит его
без какой-либо настройки.

[configure the forwarded module]: https://sass-lang.com/documentation/at-rules/use#configuration

> 💡 **Занятный факт**
>
> Правило `@forward` ведёт себя точно так же, как `@use`, когда речь идёт о
> CSS модуля. Стили из перенаправляемого модуля будут включены в
> скомпилированный CSS, и модуль с `@forward` может [расширять][extend] его,
> даже если он также не был загружен через `@use`.

[extend]: https://sass-lang.com/documentation/at-rules/extend

**SCSS**

```scss
// src/_list.scss
@mixin list-reset {
  margin: 0;
  padding: 0;
  list-style: none;
}
```
```scss
// bootstrap.scss
@forward "src/list";
```
```scss
// styles.scss
@use "bootstrap";

li {
  @include bootstrap.list-reset;
}
```

**Sass**

```sass
// src/_list.sass
@mixin list-reset
  margin: 0
  padding: 0
  list-style: none
```
```sass
// bootstrap.sass
@forward "src/list"
```
```sass
// styles.sass
@use "bootstrap"

li
  @include bootstrap.list-reset
```

**Результат CSS**

```css
li {
  margin: 0;
  padding: 0;
  list-style: none;
}
```

## Добавление префикса

Поскольку члены модуля обычно используются с [пространством имён][a namespace],
короткие и простые имена обычно являются наиболее читаемым вариантом. Но эти
имена могут не иметь смысла за пределами модуля, в котором они определены,
поэтому у `@forward` есть возможность добавлять дополнительный префикс ко
всем перенаправляемым им членам.

Это записывается как `@forward "<url>" as <префикс>-*` и добавляет указанный
префикс в начало имени каждого миксина, функции и переменной, перенаправляемых
модулем. Например, если модуль определяет член с именем `reset`, и он
перенаправляется `as list-*`, нижестоящие таблицы стилей будут ссылаться на
него как на `list-reset`.

[a namespace]: https://sass-lang.com/documentation/at-rules/use#loading-members

**SCSS**

```scss
// src/_list.scss
@mixin reset {
  margin: 0;
  padding: 0;
  list-style: none;
}
```
```scss
// bootstrap.scss
@forward "src/list" as list-*;
```
```scss
// styles.scss
@use "bootstrap";

li {
  @include bootstrap.list-reset;
}
```

**Sass**

```sass
// src/_list.sass
@mixin reset
  margin: 0
  padding: 0
  list-style: none
```
```sass
// bootstrap.sass
@forward "src/list" as list-*
```
```sass
// styles.sass
@use "bootstrap"

li
  @include bootstrap.list-reset
```

**Результат CSS**

```css
li {
  margin: 0;
  padding: 0;
  list-style: none;
}
```

## Управление видимостью

Иногда вы не хотите перенаправлять *все* члены модуля. Возможно, вы захотите
оставить некоторые члены приватными, чтобы их мог использовать только ваш
пакет, или захотите потребовать от пользователей загружать некоторые члены
другим способом. Вы можете точно контролировать, какие члены перенаправляются,
написав `@forward "<url>" hide <члены...>` или `@forward "<url>" show <члены...>`.

Форма `hide` означает, что перечисленные члены не должны перенаправляться, а
всё остальное — должно. Форма `show` означает, что перенаправляться должны
*только* названные члены. В обеих формах вы перечисляете имена миксинов,
функций или переменных (включая `$`).

**SCSS**

```scss
// src/_list.scss
$horizontal-list-gap: 2em;

@mixin list-reset {
  margin: 0;
  padding: 0;
  list-style: none;
}

@mixin list-horizontal {
  @include list-reset;

  li {
    display: inline-block;
    margin: {
      left: -2px;
      right: $horizontal-list-gap;
    }
  }
}
```
```scss
// bootstrap.scss
@forward "src/list" hide list-reset, $horizontal-list-gap;
```

**Sass**

```sass
// src/_list.sass
$horizontal-list-gap: 2em

@mixin list-reset
  margin: 0
  padding: 0
  list-style: none


@mixin list-horizontal
  @include list-rest

  li
    display: inline-block
    margin:
      left: -2px
      right: $horizontal-list-gap
```
```sass
// bootstrap.sass
@forward "src/list" hide list-reset, $horizontal-list-gap
```

## Настройка модулей

**Совместимость:** Dart Sass: начиная с 1.24.0 · LibSass: ✗ · Ruby Sass: ✗


Правило `@forward` также может загружать модуль с
[настройкой (конфигурацией)][configuration]. По большей части это работает
так же, как и для `@use`, с одним дополнением: настройка правила `@forward`
может использовать флаг `!default`. Это позволяет модулю изменять значения по
умолчанию вышестоящей таблицы стилей, при этом по-прежнему позволяя
нижестоящим таблицам стилей переопределять их.

[configuration]: https://sass-lang.com/documentation/at-rules/use#configuration

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
// _opinionated.scss
@forward 'library' with (
  $black: #222 !default,
  $border-radius: 0.1rem !default
);
```
```scss
// style.scss
@use 'opinionated' with ($black: #333);
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
// _opinionated.sass
@forward 'library' with ($black: #222 !default, $border-radius: 0.1rem !default)
```
```sass
// style.sass
@use 'opinionated' with ($black: #333)
```

**Результат CSS**

```css
code {
  border-radius: 0.1rem;
  box-shadow: 0 0.5rem 1rem rgba(51, 51, 51, 0.15);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/forward/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

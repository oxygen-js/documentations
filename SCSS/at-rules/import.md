# @import

Sass расширяет CSS-[правило `@import`](https://developer.mozilla.org/en-US/docs/Web/CSS/@import), добавляя возможность импортировать таблицы стилей Sass и CSS, что даёт доступ к
[миксинам](https://sass-lang.com/documentation/at-rules/mixin),
[функциям](https://sass-lang.com/documentation/at-rules/function) и
[переменным](https://sass-lang.com/documentation/variables), а также объединяет CSS нескольких таблиц
стилей вместе. В отличие от обычных CSS-импортов, которые заставляют браузер
выполнять несколько HTTP-запросов при отрисовке страницы, импорты Sass
обрабатываются полностью во время компиляции.

Импорты Sass используют тот же синтаксис, что и импорты CSS, за исключением того,
что несколько импортов можно перечислять через запятую, а не оформлять для
каждого отдельный `@import`. Кроме того, в [отступном синтаксисе][indented syntax] импортируемые URL не
обязательно заключать в кавычки.

[indented syntax]: https://sass-lang.com/documentation/syntax#the-indented-syntax

> ⚠️ **Внимание**
>
> Начиная с Dart Sass 1.80.0, правило `@import` [устарело][deprecated] и будет удалено
> из языка в Dart Sass 3.0.0. Вместо него используйте [правило `@use`][`@use` rule].
>
> #### Что не так с `@import`?
>
> У правила `@import` есть ряд серьёзных проблем:
>
> * `@import` делает все переменные, миксины и функции доступными глобально.
>   Из-за этого людям (или инструментам) очень сложно понять, где что
>   определено.
>
> * Поскольку всё глобально, библиотекам приходится добавлять префикс ко всем
>   своим членам, чтобы избежать конфликтов имён.
>
> * [Правила `@extend`][`@extend` rules] тоже глобальны, из-за чего трудно предсказать,
>   какие стилевые правила будут расширены.
>
> * Каждая таблица стилей выполняется и её CSS выводится *каждый раз*, когда
>   её `@import`-ят, что увеличивает время компиляции и раздувает вывод.
>
> * Не было способа определить приватные члены или селекторы-заглушки,
>   недоступные для нижестоящих таблиц стилей.
>
> Новая система модулей и правило `@use` решают все эти проблемы.
>
> #### Как мне перейти на новую систему?
>
> Мы написали [инструмент миграции][migration tool], который автоматически преобразует
> большую часть кода на основе `@import` в код на основе `@use` в мгновение ока.
> Просто укажите ему точки входа и запустите!

[deprecated]: https://sass-lang.com/documentation/breaking-changes/import
[`@use` rule]: https://sass-lang.com/documentation/at-rules/use
[`@extend` rules]: https://sass-lang.com/documentation/at-rules/extend
[migration tool]: https://sass-lang.com/documentation/cli/migrator

**SCSS**

```scss
// foundation/_code.scss
code {
  padding: .25em;
  line-height: 0;
}
```
```scss
// foundation/_lists.scss
ul, ol {
  text-align: left;

  & & {
    padding: {
      bottom: 0;
      left: 0;
    }
  }
}
```
```scss
// style.scss
@import 'foundation/code', 'foundation/lists';
```

**Sass**

```sass
// foundation/_code.sass
code
  padding: .25em
  line-height: 0
```
```sass
// foundation/_lists.sass
ul, ol
  text-align: left

  & &
    padding:
      bottom: 0
      left: 0
```
```sass
// style.sass
@import foundation/code, foundation/lists
```

**Результат CSS**

```css
code {
  padding: .25em;
  line-height: 0;
}

ul, ol {
  text-align: left;
}
ul ul, ol ol {
  padding-bottom: 0;
  padding-left: 0;
}
```

Когда Sass импортирует файл, этот файл выполняется так, как если бы его
содержимое было подставлено прямо на место `@import`. Все [миксины][mixins], [функции][functions] и
[переменные][variables] из импортируемого файла становятся доступны, а весь его CSS
включается ровно в том месте, где был написан `@import`. Более того, любые
миксины, функции и переменные, определённые до `@import`
(в том числе из других `@import`), доступны в импортируемой таблице стилей.

[mixins]: https://sass-lang.com/documentation/at-rules/mixin
[functions]: https://sass-lang.com/documentation/at-rules/function
[variables]: https://sass-lang.com/documentation/variables

> ⚠️ **Внимание**
>
> Если одна и та же таблица стилей импортируется несколько раз, она будет
> заново выполняться каждый раз. Если в ней только определяются функции и
> миксины, это обычно не страшно, но если в ней есть стилевые правила, они
> будут скомпилированы в CSS более одного раза.

## Поиск файла

Было бы совсем не весело каждый раз писать абсолютные URL для каждой
импортируемой таблицы стилей, поэтому алгоритм поиска файла для импорта в Sass
немного упрощает жизнь. Во-первых, не нужно явно указывать расширение
импортируемого файла: `@import "variables"` автоматически загрузит
`variables.scss`, `variables.sass` или `variables.css`.

> ⚠️ **Внимание**
>
> Чтобы таблицы стилей работали в любой операционной системе, Sass импортирует
> файлы по *URL*, а не по *пути к файлу*. Это значит, что нужно использовать
> прямые слэши, а не обратные, даже если вы работаете в Windows.

### Пути загрузки

Все реализации Sass позволяют указывать *пути загрузки*: пути в файловой
системе, в которых Sass будет искать файлы при разрешении импортов. Например,
если передать `node_modules/susy/sass` в качестве пути загрузки, можно
использовать `@import "susy"`, чтобы загрузить `node_modules/susy/sass/susy.scss`.

При этом импорты всегда разрешаются в первую очередь относительно текущего
файла. Пути загрузки используются только тогда, когда не существует подходящего
относительного файла. Это гарантирует, что вы случайно не сломаете свои
относительные импорты, добавив новую библиотеку.

> 💡 **Занятный факт**
>
> В отличие от некоторых других языков, Sass не требует использовать `./` для
> относительных импортов. Относительные импорты всегда доступны.

### Партиалы

По соглашению файлы Sass, которые предназначены только для импорта, а не для
самостоятельной компиляции, начинаются с `_` (как в `_code.scss`). Такие файлы
называются *партиалами* (partials), и это указывает инструментам Sass не
пытаться компилировать их отдельно. При импорте партиала символ `_` можно
опустить.

### Индексные файлы

**Совместимость:** Dart Sass: ✓ · LibSass: начиная с 3.6.0 · Ruby Sass: начиная с 3.6.0


Если создать файл `_index.scss` или `_index.sass` в папке, то при импорте самой
папки будет загружен именно этот файл.

**SCSS**

```scss
// foundation/_code.scss
code {
  padding: .25em;
  line-height: 0;
}
```
```scss
// foundation/_lists.scss
ul, ol {
  text-align: left;

  & & {
    padding: {
      bottom: 0;
      left: 0;
    }
  }
}
```
```scss
// foundation/_index.scss
@import 'code', 'lists';
```
```scss
// style.scss
@import 'foundation';
```

**Sass**

```sass
// foundation/_code.sass
code
  padding: .25em
  line-height: 0
```
```sass
// foundation/_lists.sass
ul, ol
  text-align: left

  & &
    padding:
      bottom: 0
      left: 0
```
```sass
// foundation/_index.sass
@import code, lists
```
```sass
// style.sass
@import foundation
```

**Результат CSS**

```css
code {
  padding: .25em;
  line-height: 0;
}

ul, ol {
  text-align: left;
}
ul ul, ol ol {
  padding-bottom: 0;
  padding-left: 0;
}
```

### Пользовательские импортёры

Все реализации Sass предоставляют способ определять пользовательские
импортёры, которые управляют тем, как `@import` находит таблицы стилей:

* [Node Sass][] и [Dart Sass on npm][] предоставляют [опцию `importer`][`importer` option] как часть
  своего JS API.

* [Dart Sass on pub][] предоставляет абстрактный [класс `Importer`][`Importer` class], который можно
  расширить пользовательским импортёром.

* [Ruby Sass][] предоставляет абстрактный [класс `Importers::Base`][`Importers::Base` class], который
  можно расширить пользовательским импортёром.

[Node Sass]: https://npmjs.com/package/node-sass
[Dart Sass on npm]: https://npmjs.com/package/sass
[`importer` option]: https://github.com/sass/node-sass#importer--v200---experimental
[Dart Sass on pub]: https://pub.dartlang.org/packages/sass
[`Importer` class]: https://pub.dartlang.org/documentation/sass/latest/sass/Importer-class.html
[Ruby Sass]: https://sass-lang.com/ruby-sass
[`Importers::Base` class]: https://www.rubydoc.info/gems/sass/Sass/Importers/Base

## Вложенность

Обычно импорты пишут на верхнем уровне таблицы стилей, но это не обязательно.
Их можно вкладывать в [стилевые правила][style rules] или в [обычные CSS at-правила][plain CSS at-rules] тоже.
Импортируемый CSS оказывается вложенным в этот контекст, что делает вложенные
импорты полезными для ограничения области действия куска CSS конкретным
элементом или медиазапросом. Миксины, функции и переменные верхнего уровня,
определённые во вложенном импорте, доступны только во вложенном контексте.

[style rules]: https://sass-lang.com/documentation/style-rules
[plain CSS at-rules]: https://sass-lang.com/documentation/at-rules/css
[mixins]: https://sass-lang.com/documentation/at-rules/mixin
[functions]: https://sass-lang.com/documentation/at-rules/function
[variables]: https://sass-lang.com/documentation/variables

**SCSS**

```scss
// _theme.scss
pre, code {
  font-family: 'Source Code Pro', Helvetica, Arial;
  border-radius: 4px;
}
```
```scss
// style.scss
.theme-sample {
  @import "theme";
}
```

**Sass**

```sass
// _theme.sass
pre, code
  font-family: 'Source Code Pro', Helvetica, Arial
  border-radius: 4px
```
```sass
// style.sass
.theme-sample
  @import theme
```

**Результат CSS**

```css
.theme-sample pre, .theme-sample code {
  font-family: 'Source Code Pro', Helvetica, Arial;
  border-radius: 4px;
}
```

> 💡 **Занятный факт**
>
> Вложенные импорты очень полезны для ограничения области действия сторонних
> таблиц стилей, но если вы сами являетесь автором импортируемой таблицы
> стилей, обычно лучше написать стили в виде [миксина][mixin] и подключить этот миксин во
> вложенном контексте. Миксин можно использовать более гибко, и по
> импортируемой таблице стилей становится понятнее, как его предполагается
> использовать.

[mixin]: https://sass-lang.com/documentation/at-rules/mixin

> ⚠️ **Внимание**
>
> CSS во вложенных импортах вычисляется как миксин, а значит, любые
> [родительские селекторы][parent selectors] будут ссылаться на селектор, в который вложена таблица
> стилей.
>
> **SCSS**
>
> ```scss
> // _theme.scss
> ul li {
>   $padding: 16px;
>   padding-left: $padding;
>   [dir=rtl] & {
>     padding: {
>       left: 0;
>       right: $padding;
>     }
>   }
> }
> ```
> ```scss
> // style.scss
> .theme-sample {
>   @import "theme";
> }
> ```
>
> **Sass**
>
> ```sass
> // _theme.sass
> ul li
>   $padding: 16px
>   padding-left: $padding
>   [dir=rtl] &
>     padding:
>       left: 0
>       right: $padding
> ```
> ```sass
> // style.sass
> .theme-sample
>   @import theme
> ```
>
> **Результат CSS**
>
> ```css
> .theme-sample ul li {
>   padding-left: 16px;
> }
> [dir=rtl] .theme-sample ul li {
>   padding-left: 0;
>   padding-right: 16px;
> }
> ```

[parent selectors]: https://sass-lang.com/documentation/style-rules/parent-selector

## Импорт CSS

**Совместимость:** Dart Sass: начиная с 1.11.0 · LibSass: начиная с частичной поддержки · Ruby Sass: ✗

LibSass поддерживает импорт файлов с расширением `.css`, но вопреки
  спецификации они обрабатываются как файлы SCSS, а не разбираются как CSS.
  Такое поведение объявлено устаревшим, и ведётся работа над обновлением,
  которое будет поддерживать описанное ниже поведение.


Помимо импорта файлов `.sass` и `.scss`, Sass умеет импортировать обычные
файлы `.css`. Единственное правило — импорт *не должен* явно указывать
расширение `.css`, потому что оно используется для обозначения [обычного CSS `@import`][plain CSS `@import`].

[plain CSS `@import`]: #plain-css-imports

**SCSS**

```scss
// code.css
code {
  padding: .25em;
  line-height: 0;
}
```
```scss
// style.scss
@import 'code';
```

**Sass**

```sass
// code.css
code {
  padding: .25em;
  line-height: 0;
}
```
```sass
// style.sass
@import code
```

**Результат CSS**

```css
code {
  padding: .25em;
  line-height: 0;
}
```

CSS-файлы, импортируемые Sass, не допускают никаких специальных возможностей
Sass. Чтобы авторы случайно не написали код Sass в своём CSS, все возможности
Sass, не являющиеся при этом допустимым CSS, приведут к ошибкам. В остальном
CSS выводится как есть. Его даже можно [расширить][extended]!

[extended]: https://sass-lang.com/documentation/at-rules/extend

## Обычные `@import` в CSS

**Совместимость:** Dart Sass: ✓ · LibSass: начиная с частичной поддержки · Ruby Sass: ✓

По умолчанию LibSass корректно обрабатывает обычные CSS-импорты. Однако любые
  [пользовательские импортёры][custom importers] будут ошибочно применяться и к обычным CSS-правилам
  `@import`, из-за чего эти правила смогут загружать файлы Sass.

  [custom importers]: https://sass-lang.com/documentation/js-api/interfaces/LegacySharedOptions#importer


Поскольку `@import` определён и в CSS, Sass нужен способ компилировать обычные
CSS-импорты `@import`, не пытаясь импортировать сами файлы во время
компиляции. Чтобы этого добиться и обеспечить максимальное соответствие SCSS
надмножеству CSS, Sass компилирует в обычный CSS-импорт любой `@import`,
обладающий следующими характеристиками:

* Импорт, чей URL заканчивается на `.css`.
* Импорт, чей URL начинается с `http://` или `https://`.
* Импорт, чей URL записан как `url()`.
* Импорт, у которого есть медиазапросы.

**SCSS**

```scss
@import "theme.css";
@import "http://fonts.googleapis.com/css?family=Droid+Sans";
@import url(theme);
@import "landscape" screen and (orientation: landscape);
```

**Sass**

```sass
@import "theme.css"
@import "http://fonts.googleapis.com/css?family=Droid+Sans"
@import url(theme)
@import "landscape" screen and (orientation: landscape)
```

**Результат CSS**

```css
@import "theme.css";
@import "http://fonts.googleapis.com/css?family=Droid+Sans";
@import url(theme);
@import "landscape" screen and (orientation: landscape);
```

### Интерполяция

Хотя импорты Sass не могут использовать [интерполяцию][interpolation] (чтобы всегда можно было
точно понять, откуда берутся [миксины][mixins], [функции][functions] и [переменные][variables]), обычные CSS-импорты —
могут. Это позволяет динамически генерировать импорты, например, на основе
параметров миксина.

[interpolation]: https://sass-lang.com/documentation/interpolation
[mixins]: https://sass-lang.com/documentation/at-rules/mixin
[functions]: https://sass-lang.com/documentation/at-rules/function
[variables]: https://sass-lang.com/documentation/variables

**SCSS**

```scss
@mixin google-font($family) {
  @import url("http://fonts.googleapis.com/css?family=#{$family}");
}

@include google-font("Droid Sans");
```

**Sass**

```sass
@mixin google-font($family)
  @import url("http://fonts.googleapis.com/css?family=#{$family}")


@include google-font("Droid Sans")
```

**Результат CSS**

```css
@import url("http://fonts.googleapis.com/css?family=Droid Sans");
```

## Импорт и модули

**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

На данный момент `@use` поддерживает только Dart Sass. Пользователям других
  реализаций вместо этого нужно использовать [правило `@import`][`@import` rule].

  [`@import` rule]: https://sass-lang.com/documentation/at-rules/import



[Система модулей][module system] Sass бесшовно интегрируется с `@import`, независимо от того,
импортируете ли вы файл, содержащий правила `@use`, или загружаете как модуль
файл, содержащий импорты. Мы хотим сделать переход от `@import` к `@use` как
можно более плавным.

[module system]: https://sass-lang.com/documentation/at-rules/use

### Импорт файла из системы модулей

Когда вы импортируете файл, содержащий правила `@use`, импортирующий файл
получает доступ ко всем членам (даже приватным), определённым непосредственно
в этом файле, но *не* к членам из модулей, которые загрузил этот файл. Однако
если в этом файле есть [правила `@forward`][`@forward` rules], импортирующий файл получит доступ к
перенаправленным членам. Это значит, что можно импортировать библиотеку,
написанную для использования с системой модулей.

[`@forward` rules]: https://sass-lang.com/documentation/at-rules/forward

> ⚠️ **Внимание**
>
> Когда импортируется файл с правилами `@use`, весь CSS, транзитивно
> загруженный этими правилами, включается в итоговую таблицу стилей, даже
> если он уже был включён через другой импорт. Если не быть внимательным,
> это может привести к раздутому CSS на выходе!

#### Файлы только для импорта

API, разумный для `@use`, может не подходить для `@import`. Например, `@use`
по умолчанию добавляет пространство имён ко всем членам, поэтому можно
безопасно использовать короткие имена, а `@import` этого не делает, так что
может понадобиться имя подлиннее. Если вы автор библиотеки, вас может
беспокоить, что при обновлении библиотеки для использования новой системы
модулей сломаются существующие пользователи, работающие через `@import`.

Чтобы упростить эту задачу, Sass также поддерживает *файлы только для
импорта*. Если назвать файл `<name>.import.scss`, он будет загружаться только
для импортов, но не для `@use`. Таким образом можно сохранить совместимость
для пользователей `@import`, одновременно предоставляя удобный API для
пользователей новой системы модулей.

**SCSS**

```scss
// _reset.scss

// Module system users write `@include reset.list()`.
@mixin list() {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }
}
```
```scss
// _reset.import.scss

// Legacy import users can keep writing `@include reset-list()`.
@forward "reset" as reset-*;
```

**Sass**

```sass
// _reset.sass

// Module system users write `@include reset.list()`.
@mixin list()
  ul
    margin: 0
    padding: 0
    list-style: none
```
```sass
// _reset.import.sass

// Legacy import users can keep writing `@include reset-list()`.
@forward "reset" as reset-*
```

#### Настройка модулей через импорты

**Совместимость:** Dart Sass: начиная с 1.24.0 · LibSass: ✗ · Ruby Sass: ✗


Можно [настраивать модули][configure modules], загружаемые через `@import`, определив глобальные
переменные перед первым `@import`, который загружает этот модуль.

[configure modules]: https://sass-lang.com/documentation/at-rules/use#configuration

**SCSS**

```scss
// _library.scss
$color: blue !default;

a {
  color: $color;
}
```
```scss
// _library.import.scss
@forward 'library' as lib-*;
```
```scss
// style.sass
$lib-color: green;
@import "library";
```

**Sass**

```sass
$color: blue !default

a
  color: $color
```
```sass
// _library.import.sass
@forward 'library' as lib-*
```
```sass
// style.sass
$lib-color: green
@import "library"
```

**Результат CSS**

```css
a {
  color: green;
}
```

> ⚠️ **Внимание**
>
> Модули загружаются только один раз, поэтому если изменить конфигурацию
> после того, как модуль был впервые `@import`-ирован (даже опосредованно),
> это изменение будет проигнорировано при повторном `@import` этого модуля.

### Загрузка модуля, содержащего импорты

Когда вы через `@use` (или `@forward`) загружаете модуль, использующий
`@import`, этот модуль будет содержать все публичные члены, определённые в
загружаемой таблице стилей, *и* всё, что эта таблица стилей транзитивно
импортирует. Другими словами, всё импортированное обрабатывается так, как
если бы оно было написано в одной большой таблице стилей.

Благодаря этому легко начать использовать `@use` в таблице стилей ещё до того,
как все библиотеки, от которых вы зависите, перейдут на новую систему
модулей. Однако имейте в виду: если они всё же перейдут, их API вполне может
измениться!

## Отличия от `@use`

Правило `@use` призвано заменить старое [правило `@import`][`@import` rule], но оно намеренно
спроектировано так, чтобы работать иначе. Вот несколько основных различий
между ними:

* `@use` делает переменные, функции и миксины доступными только в области
  видимости текущего файла. Он никогда не добавляет их в глобальную область
  видимости. Благодаря этому легко понять, откуда берётся каждое имя, на
  которое ссылается ваш файл Sass, а значит, можно использовать более
  короткие имена без риска конфликтов.

* `@use` загружает каждый файл только один раз. Это гарантирует, что CSS
  ваших зависимостей случайно не задублируется много раз.

* `@use` должен находиться в начале файла и не может быть вложен в стилевые
  правила. Вложенные импорты можно перевести на [вызовы миксинов или `meta.load-css()`][mixin calls or `meta.load-css()`].

* У каждого правила `@use` может быть только один URL.

* `@use` требует кавычек вокруг своего URL, даже при использовании
  [отступного синтаксиса][indented syntax].

[`@import` rule]: https://sass-lang.com/documentation/at-rules/import
[mixin calls or `meta.load-css()`]: https://sass-lang.com/documentation/breaking-changes/import/#nested-imports
[indented syntax]: https://sass-lang.com/documentation/syntax#the-indented-syntax

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/import/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# @use

At-правило `@use` загружает [миксины](https://sass-lang.com/documentation/at-rules/mixin),
[функции](https://sass-lang.com/documentation/at-rules/function) и
[переменные](https://sass-lang.com/documentation/variables) из других таблиц стилей Sass и
объединяет CSS из нескольких таблиц стилей вместе. Таблицы стилей, загружаемые через `@use`,
называются «модулями». Sass также предоставляет [встроенные
модули](https://sass-lang.com/documentation/modules), полные полезных функций.

Простейшее правило `@use` записывается как `@use "<url>"` и загружает модуль по
указанному URL. Любые стили, загруженные таким образом, будут включены в скомпилированный
CSS-код ровно один раз, независимо от того, сколько раз эти стили загружаются.

> ⚠️ **Внимание**
>
> Правила `@use` таблицы стилей должны располагаться перед любыми правилами, кроме
> `@forward`, включая [стилевые правила][style rules]. Однако вы можете объявлять переменные
> перед правилами `@use`, чтобы использовать их при [настройке модулей][configuring modules].
>
> [style rules]: https://sass-lang.com/documentation/style-rules
> [configuring modules]: #configuration

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
@use 'foundation/code';
@use 'foundation/lists';
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
@use 'foundation/code'
@use 'foundation/lists'
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

## Загрузка членов модуля

Вы можете обращаться к переменным, функциям и миксинам из другого модуля, записывая
`<пространство_имён>.<переменная>`, `<пространство_имён>.<функция>()` или `@include
<пространство_имён>.<миксин>()`. По умолчанию пространство имён — это просто последний
компонент URL модуля.

Члены модуля (переменные, функции и миксины), загруженные через `@use`, видны только в
той таблице стилей, которая их загружает. Другим таблицам стилей потребуется написать
собственные правила `@use`, если они тоже хотят получить к ним доступ. Это помогает легко
определить, откуда именно происходит каждый член. Если вы хотите загрузить члены сразу из
многих файлов, вы можете использовать [правило `@forward`][`@forward` rule], чтобы
переслать их все из одного общего файла.

[`@forward` rule]: https://sass-lang.com/documentation/at-rules/forward

> 💡 **Занятный факт**
>
> Поскольку `@use` добавляет пространства имён к именам членов, безопасно выбирать
> очень простые имена, например `$radius` или `$width`, при написании таблицы стилей.
> Это отличается от старого [правила `@import`][`@import` rule], которое поощряло
> пользователей писать длинные имена вроде `$mat-corner-radius`, чтобы избежать
> конфликтов с другими библиотеками, и это помогает делать ваши таблицы стилей понятными
> и лёгкими для чтения!
>
> [`@import` rule]: https://sass-lang.com/documentation/at-rules/import

**SCSS**

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
  border-radius: $radius;
}
```
```scss
// style.scss
@use "src/corners";

.button {
  @include corners.rounded;
  padding: 5px + corners.$radius;
}
```

**Sass**

```sass
// src/_corners.sass
$radius: 3px

@mixin rounded
  border-radius: $radius
```
```sass
// style.sass
@use "src/corners"

.button
  @include corners.rounded
  padding: 5px + corners.$radius
```

**Результат CSS**

```css
.button {
  border-radius: 3px;
  padding: 8px;
}
```

### Выбор пространства имён

По умолчанию пространство имён модуля — это просто последний компонент его URL без
расширения файла. Однако иногда вы можете захотеть выбрать другое пространство имён —
например, использовать более короткое имя для модуля, к которому вы часто обращаетесь,
или вы загружаете несколько модулей с одинаковым именем файла. Это можно сделать, написав
`@use "<url>" as <namespace>`.

**SCSS**

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
  border-radius: $radius;
}
```
```scss
// style.scss
@use "src/corners" as c;

.button {
  @include c.rounded;
  padding: 5px + c.$radius;
}
```

**Sass**

```sass
// src/_corners.sass
$radius: 3px

@mixin rounded
  border-radius: $radius
```
```sass
// style.sass
@use "src/corners" as c

.button
  @include c.rounded
  padding: 5px + c.$radius
```

**Результат CSS**

```css
.button {
  border-radius: 3px;
  padding: 8px;
}
```

Вы даже можете загрузить модуль *без* пространства имён, написав `@use "<url>" as *`.
Мы рекомендуем делать это только для таблиц стилей, написанных вами же, поскольку иначе
они могут ввести новые члены, которые вызовут конфликты имён!

**SCSS**

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
  border-radius: $radius;
}
```
```scss
// style.scss
@use "src/corners" as *;

.button {
  @include rounded;
  padding: 5px + $radius;
}
```

**Sass**

```sass
// src/_corners.sass
$radius: 3px

@mixin rounded
  border-radius: $radius
```
```sass
// style.sass
@use "src/corners" as *

.button
  @include rounded
  padding: 5px + $radius
```

**Результат CSS**

```css
.button {
  border-radius: 3px;
  padding: 8px;
}
```

### Приватные члены

Как автор таблицы стилей, вы можете не хотеть, чтобы все определяемые вами члены были
доступны за пределами вашей таблицы стилей. Sass позволяет легко определить приватный
член, начав его имя с `-` или `_`. Такие члены будут работать как обычно внутри
таблицы стилей, в которой они определены, но не будут частью публичного API модуля. Это
значит, что таблицы стилей, загружающие ваш модуль, не смогут их увидеть!

> 💡 **Занятный факт**
>
> Если вы хотите сделать член приватным для всего *пакета*, а не только для отдельного
> модуля, просто не [пересылайте его модуль][forward its module] ни из одной из точек
> входа вашего пакета (таблиц стилей, которые вы указываете пользователям загружать для
> использования вашего пакета). Вы даже можете [скрыть этот член][hide that member],
> пересылая остальную часть его модуля!
>
> [forward its module]: https://sass-lang.com/documentation/at-rules/forward
> [hide that member]: https://sass-lang.com/documentation/at-rules/forward#controlling-visibility

**SCSS**

```scss
// src/_corners.scss
$-radius: 3px;

@mixin rounded {
  border-radius: $-radius;
}
```
```scss
// style.scss
@use "src/corners";

.button {
  @include corners.rounded;

  // This is an error! $-radius isn't visible outside of `_corners.scss`.
  padding: 5px + corners.$-radius;
}
```

**Sass**

```sass
// src/_corners.sass
$-radius: 3px

@mixin rounded
  border-radius: $-radius
```
```sass
// style.sass
@use "src/corners"

.button
  @include corners.rounded

  // This is an error! $-radius isn't visible outside of `_corners.scss`.
  padding: 5px + corners.$-radius
```

## Конфигурация

Таблица стилей может определять переменные с флагом [`!default`][`!default` flag], чтобы
сделать их настраиваемыми. Чтобы загрузить модуль с конфигурацией, напишите `@use <url> with
(<variable>: <value>, <variable>: <value>)`. Заданные значения переопределят значения
переменных по умолчанию.

[`!default` flag]: https://sass-lang.com/documentation/variables#default-values

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


Модуль сохраняет одну и ту же конфигурацию (или её отсутствие) даже если он загружается
несколько раз. Из-за этого `@use ... with` можно использовать только один раз для модуля,
при первой его загрузке. Это также позволяет использовать конфигурацию для создания «тем»,
применяемых во всей компиляции:

**SCSS**

```scss
// components/_button.scss
@use "../theme";

button {
  color: theme.$text-color;
  background-color: theme.$background-color;
}
```
```scss
// _theme.scss
$text-color: black !default;
$background-color: white !default;
```
```scss
// dark.scss
@use "theme" with (
  $text-color: white,
  $background-color: black,
);

@use "components/button";
// More components are usually imported here.
```

**Sass**

```sass
// components/_button.scss
@use "../theme"

button
  color: theme.$text-color
  background-color: theme.$background-color
```
```sass
// _theme.scss
$text-color: black !default
$background-color: white !default
```
```sass
// dark.scss
@use "theme" with (
  $text-color: white,
  $background-color: black,
)

@use "components/button"
// More components are usually imported here.
```

**Результат CSS**

```css
button {
  color: white;
  background-color: black;
}
```


> ⚠️ **Внимание**
>
> Если вы хотите настроить модуль *и* задать ему собственное пространство имён, то
> уточнение `as` должно идти перед уточнением `with`, как в `@use ... as ... with ...`.

### С миксинами

Настройка модулей через `@use ... with` может быть очень удобной, особенно при
использовании библиотек, изначально написанных для работы с [правилом
`@import`][`@import` rule]. Но этот способ не особенно гибкий, и мы не рекомендуем его
для более сложных сценариев использования. Если вы хотите настроить сразу много
переменных, передавать [карты][maps] в качестве конфигурации или обновлять
конфигурацию после загрузки модуля — рассмотрите вариант написания миксина для задания
переменных и ещё одного миксина для внедрения стилей.

[`@import` rule]: https://sass-lang.com/documentation/at-rules/import
[maps]: https://sass-lang.com/documentation/values/maps

**SCSS**

```scss
// _library.scss
$-black: #000;
$-border-radius: 0.25rem;
$-box-shadow: null;

/// If the user has configured `$-box-shadow`, returns their configured value.
/// Otherwise returns a value derived from `$-black`.
@function -box-shadow() {
  @return $-box-shadow or (0 0.5rem 1rem rgba($-black, 0.15));
}

@mixin configure($black: null, $border-radius: null, $box-shadow: null) {
  @if $black {
    $-black: $black !global;
  }
  @if $border-radius {
    $-border-radius: $border-radius !global;
  }
  @if $box-shadow {
    $-box-shadow: $box-shadow !global;
  }
}

@mixin styles {
  code {
    border-radius: $-border-radius;
    box-shadow: -box-shadow();
  }
}
```
```scss
// style.scss
@use 'library';

@include library.configure(
  $black: #222,
  $border-radius: 0.1rem
);

@include library.styles;
```

**Sass**

```sass
// _library.sass
$-black: #000
$-border-radius: 0.25rem
$-box-shadow: null

/// If the user has configured `$-box-shadow`, returns their configured value.
/// Otherwise returns a value derived from `$-black`.
@function -box-shadow()
  @return $-box-shadow or (0 0.5rem 1rem rgba($-black, 0.15))


@mixin configure($black: null, $border-radius: null, $box-shadow: null)
  @if $black
    $-black: $black !global
  @if $border-radius
    $-border-radius: $border-radius !global
  @if $box-shadow
    $-box-shadow: $box-shadow !global


@mixin styles
  code
    border-radius: $-border-radius
    box-shadow: -box-shadow()
```
```sass
// style.sass
@use 'library'
@include library.configure($black: #222, $border-radius: 0.1rem)
@include library.styles
```

**Результат CSS**

```css
code {
  border-radius: 0.1rem;
  box-shadow: 0 0.5rem 1rem rgba(34, 34, 34, 0.15);
}
```

### Переприсваивание переменных

После загрузки модуля вы можете переприсвоить его переменные.

**SCSS**

```scss
// _library.scss
$color: red;
```
```scss
// _override.scss
@use 'library';
library.$color: blue;
```
```scss
// style.scss
@use 'library';
@use 'override';
@debug library.$color;  //=> blue
```

**Sass**

```sass
// _library.sass
$color: red
```
```sass
// _override.sass
@use 'library'
library.$color: blue
```
```sass
// style.sass
@use 'library'
@use 'override'
@debug library.$color  //=> blue
```

Это работает даже если вы импортировали модуль без пространства имён с помощью `as *`.
Присваивание имени переменной, определённой в этом модуле, перезапишет её значение в
этом модуле.

> ⚠️ **Внимание**
>
> Переменные встроенных модулей (например, [`math.$pi`]) нельзя переприсваивать.
>
> [`math.$pi`]: https://sass-lang.com/documentation/modules/math#$pi

## Поиск модуля

Было бы совсем не весело писать абсолютные URL для каждой загружаемой таблицы стилей,
поэтому алгоритм Sass для поиска модуля немного упрощает эту задачу. Прежде всего, вам
не нужно явно писать расширение файла, который вы хотите загрузить; `@use "variables"`
автоматически загрузит `variables.scss`, `variables.sass` или `variables.css`.

> ⚠️ **Внимание**
>
> Чтобы гарантировать, что таблицы стилей работают в любой операционной системе, Sass
> загружает файлы по *URL*, а не по *пути к файлу*. Это значит, что вам нужно
> использовать прямые слэши, а не обратные, даже в Windows.
>
> Это также значит, что URL чувствительны к регистру, поэтому Sass будет считать
> `Styles.scss` и `styles.scss` разными модулями, даже если вы используете файловую
> систему без учёта регистра. Убедитесь, что ваши URL соответствуют реальному регистру
> файлов на диске, иначе ваши таблицы стилей могут загрузиться дважды и точно не будут
> работать на других операционных системах.

### Пути загрузки

Все реализации Sass позволяют пользователям задавать *пути загрузки* (load paths):
пути в файловой системе, в которых Sass будет искать модули. Например, если вы передадите
`node_modules/susy/sass` в качестве пути загрузки, вы сможете использовать `@use "susy"`
для загрузки `node_modules/susy/sass/susy.scss` (хотя [URL `pkg:`][`pkg:` URLs] — более
удачный способ решить эту задачу).

[`pkg:` URLs]: #pkg-ur-ls

Однако модули всегда будут загружаться в первую очередь относительно текущего файла. Пути
загрузки будут использоваться, только если не существует подходящего относительного файла,
соответствующего URL модуля. Это гарантирует, что вы не сможете случайно сломать свои
относительные импорты, добавив новую библиотеку.

> 💡 **Занятный факт**
>
> В отличие от некоторых других языков, Sass не требует использовать `./` для
> относительных импортов. Относительные импорты доступны всегда.

### Частичные файлы

По соглашению файлы Sass, предназначенные только для загрузки в качестве модулей, а не
для самостоятельной компиляции, начинаются с `_` (как в `_code.scss`). Они называются
*частичными файлами* (partials) и указывают инструментам Sass не пытаться компилировать
эти файлы самостоятельно. При импорте частичного файла `_` можно опускать.

### Индексные файлы

Если вы напишете `_index.scss` или `_index.sass` в папке, индексный файл будет
загружен автоматически при загрузке URL самой этой папки.

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
@use 'code';
@use 'lists';
```
```scss
// style.scss
@use 'foundation';
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
@use 'code'
@use 'lists'
```
```sass
// style.sass
@use 'foundation'
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

## URL `pkg:`

Sass использует схему URL `pkg:` для загрузки таблиц стилей, распространяемых через
различные менеджеры пакетов. Поскольку Sass используется в контексте многих разных
языков программирования с разными соглашениями по управлению пакетами, URL `pkg:` почти
не имеют заданного смысла. Вместо этого пользователям рекомендуется реализовывать
собственные импортеры (используя [JS API][JS API] или [протокол Embedded Sass][Embedded Sass protocol]),
которые разрешают эти URL с использованием логики родного менеджера пакетов.

Это позволяет URL `pkg:` и использующим их таблицам стилей быть переносимыми между
разными языковыми экосистемами. Устанавливаете ли вы Sass-библиотеку через npm (для
которого Sass предоставляет [встроенный импортер `pkg:`][a built-in `pkg:` importer])
или через самый экзотический менеджер пакетов, какой только сможете найти — если вы
напишете `@use 'pkg:library'`, всё сработает как надо.

[JS API]: https://sass-lang.com/documentation/js-api/interfaces/Options/#importers
[Embedded Sass protocol]: https://github.com/sass/sass/blob/main/spec/embedded-protocol.md
[a built-in `pkg:` importer]: #node-js-package-importer

> 💡 **Занятный факт**
>
> URL `pkg:` предназначены не только для `@use`. Вы можете использовать их везде,
> где можно загрузить файл Sass, включая [`@forward`], [`meta.load-css()`] и даже
> старое правило [`@import`].
>
> [`@forward`]: https://sass-lang.com/documentation/at-rules/forward
> [`meta.load-css()`]: https://sass-lang.com/documentation/modules/meta/#load-css
> [`@import`]: https://sass-lang.com/documentation/at-rules/import

### Правила для импортера `pkg:`

Есть несколько общих правил, которым, как ожидает Sass, должны следовать все импортеры
`pkg:`. Эти правила помогают гарантировать, что URL `pkg:` обрабатываются одинаково во
всех менеджерах пакетов, чтобы таблицы стилей были максимально переносимыми.

В дополнение к стандартным правилам для пользовательских импортеров, импортер `pkg:`
должен обрабатывать только неканонические URL, которые:

* имеют схему `pkg`, и
* путь которых начинается с имени пакета, и
* за которым опционально следует путь, где сегменты пути разделены прямым слэшем.

Имя пакета может содержать прямые слэши, в зависимости от того, поддерживает ли это
конкретный менеджер пакетов. Например, npm допускает имена пакетов вида
`@namespace/name`. Обратите внимание, что имена пакетов, содержащие не буквенно-цифровые
символы, могут быть менее переносимыми между разными менеджерами пакетов.

Импортеры `pkg:` должны отклонять следующие шаблоны:

* URL, путь которого начинается с `/`.
* URL с непустыми/ненулевыми именем пользователя, паролем, хостом, портом, запросом
  или фрагментом.

Если импортер `pkg:` встречает URL, который нарушает соглашения его собственного
менеджера пакетов, но _не_ вышеуказанные правила, ему следует просто отказаться от
загрузки этого URL, а не выбрасывать ошибку. Это позволяет пользователям при
необходимости использовать несколько импортеров `pkg:` одновременно.

### Импортер пакетов Node.js

**Совместимость:** Dart Sass: начиная с 1.71.0 · LibSass: ✗ · Ruby Sass: ✗


Поскольку Sass наиболее широко используется вместе с экосистемой Node.js, он
поставляется с импортером `pkg:`, который использует тот же алгоритм, что и Node.js,
для загрузки таблиц стилей Sass. Это не включено по умолчанию, но легко включается:

* Если вы используете JavaScript API, просто добавьте [`new NodePackageImporter()`] в
  опцию `importers`.

* Если вы используете Dart API, добавьте [`NodePackageImporter()`] в опцию `importers`.

* Если вы используете командную строку, передайте [`--pkg-importer=node`].

[`new NodePackageImporter()`]: https://sass-lang.com/documentation/js-api/classes/NodePackageImporter/
[`NodePackageImporter()`]: https://pub.dev/documentation/sass/latest/sass/NodePackageImporter-class.html
[`--pkg-importer=node`]: https://sass-lang.com/documentation/cli/dart-sass/#pkg-importer-node

Если вы загружаете URL `pkg:`, импортер `pkg:` для Node.js посмотрит в файл
`package.json`, чтобы определить, какой файл Sass загрузить. Он проверит по порядку:

* Поле [`"exports"`][`"exports"` field] с условиями `"sass"`, `"style"` и `"default"`.
  Это рекомендуемый способ для пакетов раскрывать точки входа Sass в будущем.

* Поле `"sass"` или поле `"style"`, которое должно быть путём к файлу Sass. Это
  работает только если URL `pkg:` не имеет подпути — `pkg:library` загрузит файл,
  указанный в поле `"sass"`, но `pkg:library/button` загрузит `button.scss` из
  корня пакета.

* [Индексный файл][index file] в корне пакета. Это также работает только если URL
  `pkg:` не имеет подпути.

[`"exports"` field]: https://nodejs.org/api/packages.html#conditional-exports
[index file]: https://sass-lang.com/documentation/at-rules/use/#index-files

Импортер пакетов Node.js поддерживает полный набор возможностей `"exports"`, поэтому
вы также можете указывать разные расположения для разных подпутей (обратите внимание,
что ключ должен включать расширение файла):

```json
{
  "exports": {
    ".": {
      "sass": "styles/index.scss",
    },
    "./button.scss": {
      "sass": "styles/button.scss",
    },
    "./accordion.scss": {
      "sass": "styles/accordion.scss",
    }
  }
}
```

...или даже шаблоны:

```json
{
  "exports": {
    ".": {
      "sass": "styles/index.scss",
    },
    "./*.scss": {
      "sass": "styles/*.scss",
    },
  }
}
```

## Загрузка CSS

Помимо загрузки файлов `.sass` и `.scss`, Sass может загружать обычные файлы `.css`.

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
@use 'code';
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
@use 'code'
```

**Результат CSS**

```css
code {
  padding: .25em;
  line-height: 0;
}
```

Файлы CSS, загруженные как модули, не допускают никаких специальных возможностей Sass
и потому не могут раскрывать никакие переменные, функции или миксины Sass. Чтобы
гарантировать, что авторы случайно не напишут Sass в своём CSS, все возможности Sass,
которые не являются также допустимым CSS, будут приводить к ошибкам. В остальном CSS
будет отрендерен как есть. Его даже можно [расширять][extended]!

[extended]: https://sass-lang.com/documentation/at-rules/extend

## Отличия от `@import`

Правило `@use` предназначено для замены старого [правила `@import`][`@import` rule], но
намеренно спроектировано так, чтобы работать иначе. Вот некоторые основные различия
между ними:

* `@use` делает переменные, функции и миксины доступными только в области видимости
  текущего файла. Оно никогда не добавляет их в глобальную область видимости. Это
  упрощает выяснение того, откуда берётся каждое имя, на которое ссылается ваш файл
  Sass, и означает, что вы можете использовать более короткие имена без риска
  конфликтов.

* `@use` загружает каждый файл только один раз. Это гарантирует, что вы случайно не
  дублируете CSS ваших зависимостей много раз.

* `@use` должно располагаться в начале вашего файла и не может быть вложено в
  стилевые правила. Вложенные импорты можно перенести на [вызовы миксинов или
  `meta.load-css()`][mixin calls or `meta.load-css()`].

* Каждое правило `@use` может иметь только один URL.

* `@use` требует кавычки вокруг своего URL, даже при использовании [отступного
  синтаксиса][indented syntax].

[`@import` rule]: https://sass-lang.com/documentation/at-rules/import
[mixin calls or `meta.load-css()`]: https://sass-lang.com/documentation/breaking-changes/import/#nested-imports
[indented syntax]: https://sass-lang.com/documentation/syntax#the-indented-syntax

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/use/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

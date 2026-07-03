# Мигратор

Мигратор Sass автоматически обновляет ваши файлы Sass, помогая перейти на
новейшую и лучшую версию языка. Каждая из его команд мигрирует одну отдельную
функцию, чтобы дать вам как можно больше контроля над тем, что и когда вы
обновляете.

## Использование

Чтобы использовать мигратор Sass, укажите ему, [какую миграцию][] вы хотите
запустить, и какие файлы Sass нужно мигрировать:

[какую миграцию]: #migrations

```shellsession
sass-migrator <migration> <entrypoint.scss...>
```

По умолчанию мигратор изменяет только те файлы, которые вы явно передали в
командной строке. Передача опции [`--migrate-deps`][] указывает мигратору
также изменить все таблицы стилей, загружаемые с помощью правила [`@use`][],
правила [`@forward`][] или правила [`@import`][]. А если вы хотите сделать
пробный запуск, чтобы увидеть, какие изменения будут внесены, не сохраняя их
на самом деле, можно передать <code>[--dry-run][] [--verbose][]</code> (или
сокращённо `-nv`).

[`--migrate-deps`]: #migrate-deps
[`@use`]: https://sass-lang.com/documentation/at-rules/use
[`@forward`]: https://sass-lang.com/documentation/at-rules/forward
[`@import`]: https://sass-lang.com/documentation/at-rules/import
[--dry-run]: #dry-run
[--verbose]: #verbose

```shellsession
$ cat style.scss
$body-bg: #000;
$body-color: #111;

@import "bootstrap";

@include media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
$ sass-migrator --migrate-deps module style.scss
$ cat style.scss
@use "bootstrap" with (
  $body-bg: #000,
  $body-color: #111
);

@include bootstrap.media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
```


## Установка

Мигратор Sass можно установить из большинства тех же источников, что и
[Dart Sass](https://sass-lang.com/dart-sass):

### Автономная версия

Вы можете установить мигратор Sass в Windows, Mac или Linux, скачав пакет для
вашей операционной системы [с GitHub][] и [добавив его в `PATH`][].

[с GitHub]: https://github.com/sass/migrator/releases
[добавив его в `PATH`]: https://katiek2.github.io/path-doc/

### npm

Если вы используете Node.js, вы также можете установить мигратор Sass с
помощью [npm][], выполнив

[npm]: https://www.npmjs.com

```shellsession
npm install -g sass-migrator
```

### Chocolatey

Если вы используете [менеджер пакетов Chocolatey][] для Windows, вы можете
установить мигратор Sass, выполнив

[менеджер пакетов Chocolatey]: https://chocolatey.org

```shellsession
choco install sass-migrator
```

### Homebrew

Если вы используете [менеджер пакетов Homebrew][] для Mac OS X, вы можете
установить Dart Sass, выполнив

[менеджер пакетов Homebrew]: https://brew.sh

```shellsession
brew install sass/sass/migrator
```

## Глобальные опции

Эти опции доступны для всех миграторов.

### `--migrate-deps`

Эта опция (сокращённо `-d`) указывает мигратору изменять не только таблицы
стилей, явно переданные в командной строке, но и любые таблицы стилей, от
которых они зависят через правило [`@use`][], правило [`@forward`][] или
правило [`@import`][].

```shellsession
$ sass-migrator module --verbose style.scss
Migrating style.scss
$ sass-migrator module --verbose --migrate-deps style.scss
Migrating style.scss
Migrating _theme.scss
Migrating _fonts.scss
Migrating _grid.scss
```

> ⚠️ **Внимание**
>
> [Мигратор модулей][] исходит из того, что любая таблица стилей, от которой
> есть зависимость через правило [`@use`][] или правило [`@forward`][], уже
> мигрирована на систему модулей, поэтому он не будет пытаться мигрировать их,
> даже когда передана опция `--migrate-deps`.
>
> [Мигратор модулей]: #module
> [`@use`]: https://sass-lang.com/documentation/at-rules/use
> [`@forward`]: https://sass-lang.com/documentation/at-rules/forward

### `--load-path`

Эта опция (сокращённо `-I`) указывает мигратору [путь загрузки][], по
которому следует искать таблицы стилей. Её можно передавать несколько раз,
чтобы указать несколько путей загрузки. Более ранние пути загрузки имеют
приоритет над более поздними.

Предполагается, что зависимости, загруженные из путей загрузки, — это
сторонние библиотеки, поэтому мигратор не будет мигрировать их, даже если
передана опция [`--migrate-deps`][].

[путь загрузки]: https://sass-lang.com/documentation/at-rules/use#load-paths

### `--dry-run`

Этот флаг (сокращённо `-n`) указывает мигратору не сохранять никакие изменения
на диск. Вместо этого он выводит список файлов, которые были бы изменены. Его
часто комбинируют с опцией [`--verbose`][], чтобы также вывести содержимое
изменений, которые были бы внесены.

[`--verbose`]: #verbose

```shellsession
$ sass-migrator module --dry-run --migrate-deps style.scss
Dry run. Logging migrated files instead of overwriting...

style.scss
_theme.scss
_fonts.scss
_grid.scss
```

#### `--no-unicode`

Этот флаг указывает мигратору Sass выводить в терминал как часть сообщений об
ошибках только символы ASCII. По умолчанию, или если передан `--unicode`,
мигратор будет выводить не-ASCII символы для этих сообщений. Этот флаг не
влияет на вывод CSS.

```shellsession
$ sass-migrator --no-unicode module style.scss
line 1, column 9 of style.scss: Error: Could not find Sass file at 'typography'.
  ,
1 | @import "typography";
  |         ^^^^^^^^^^^^
  '
Migration failed!
$ sass-migrator --unicode module style.scss
line 1, column 9 of style.scss: Error: Could not find Sass file at 'typography'.
  ╷
1 │ @import "typography";
  │         ^^^^^^^^^^^^
  ╵
Migration failed!
```

### `--verbose`

Этот флаг (сокращённо `-v`) указывает мигратору выводить в консоль
дополнительную информацию. По умолчанию он просто выводит имена изменённых
файлов, но в сочетании с опцией [`--dry-run`][] он также выводит новое
содержимое этих файлов.

[`--dry-run`]: #dry-run

```shellsession
$ sass-migrator module --verbose --dry-run style.scss
Dry run. Logging migrated files instead of overwriting...
<==> style.scss
@use "bootstrap" with (
  $body-bg: #000,
  $body-color: #111
);

@include bootstrap.media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
$ sass-migrator module --verbose style.scss
Migrating style.scss
```

## Миграции

### `if()`

Эта миграция преобразует устаревшую функцию `if()` в новый синтаксис CSS
`if()`. Подробности см. в [/d/if-function].

[/d/if-function]: https://sass-lang.com/documentation/breaking-changes/if-function

### Цвет

Эта миграция преобразует устаревшие функции цвета в новые функции,
совместимые с цветовыми пространствами.

### Деление

Эта миграция преобразует таблицы стилей, использующие [`/` для деления][], на
использование встроенной функции `math.div` вместо этого.

[`/` для деления]: https://sass-lang.com/documentation/breaking-changes/slash-div

#### `--pessimistic`

По умолчанию мигратор преобразует операции `/` в `math.div`, даже если не
уверен, что при вычислении это будет именно деление. Он оставляет их без
изменений, только если может статически определить, что они делают что-то
другое (например, когда в выражении нет SassScript или один из операндов —
строка). Функция `math.div` сейчас работает идентично оператору `/`, поэтому
это безопасное преобразование, но оно может привести к новым предупреждениям,
если во время выполнения один из аргументов `math.div` окажется не числом.

Если вы хотите избежать такого поведения, можно передать флаг `--pessimistic`.
С этим флагом мигратор будет преобразовывать только те операции `/`, которые
точно являются делением. Это предотвратит ненужные преобразования в
`math.div`, но, вероятно, оставит немигрированной часть операций деления, если
их нельзя определить статически.

### Модуль

Эта миграция преобразует таблицы стилей, использующие старое правило
[`@import`][] для загрузки зависимостей, так, чтобы вместо этого они
использовали систему модулей Sass через правило [`@use`][]. Она не просто
наивно заменяет `@import` на `@use` — она обновляет таблицы стилей
интеллектуально, чтобы они продолжали работать так же, как раньше, включая:

- Добавление пространств имён при использовании элементов (переменных,
  миксинов и функций) из других модулей.

- Добавление новых правил `@use` в таблицы стилей, которые использовали
  элементы, не импортируя их.

- Преобразование переопределённых значений по умолчанию переменных в
  конструкции [`with`][].

  [`with`]: https://sass-lang.com/documentation/at-rules/use#configuration

- Автоматическое удаление префиксов `-` и `_` у элементов, используемых из
  других файлов (иначе они считались бы [приватными][] и могли бы
  использоваться только в модуле, где объявлены).

  [приватными]: https://sass-lang.com/documentation/at-rules/use#private-members

- Преобразование [вложенных импортов][] для использования вместо них миксина
  [`meta.load-css()`][].

  [вложенных импортов]: https://sass-lang.com/documentation/at-rules/import/#nesting
  [`meta.load-css()`]: https://sass-lang.com/documentation/modules/meta#load-css

> ⚠️ **Внимание**
>
> Поскольку мигратору модулей может понадобиться изменить как определения
> элементов, _так и_ их имена, важно либо запустить его с опцией
> [`--migrate-deps`][], либо убедиться, что вы передаёте ему все таблицы
> стилей вашего пакета или приложения.
>
> [`--migrate-deps`]: #migrate-deps

```shellsession
$ cat style.scss
$body-bg: #000;
$body-color: #111;

@import "bootstrap";

@include media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
$ sass-migrator --migrate-deps module style.scss
$ cat style.scss
@use "bootstrap" with (
  $body-bg: #000,
  $body-color: #111
);

@include bootstrap.media-breakpoint-up(sm) {
  .navbar {
    display: block;
  }
}
```


#### Загрузка зависимостей

Мигратору модулей нужно уметь читать все таблицы стилей, от которых зависят
мигрируемые файлы, даже если опция [`--migrate-deps`][] не передана. Если
мигратору не удастся найти зависимость, вы получите ошибку.

```shellsession
$ ls .
style.scss  node_modules
$ sass-migrator module style.scss
Error: Could not find Sass file at 'dependency'.
  ,
1 | @import "dependency";
  |         ^^^^^^^^^^^^
  '
  style.scss 1:9  root stylesheet
Migration failed!
$ sass-migrator --load-path node_modules module style.scss
```

Если при компиляции таблиц стилей вы используете [путь загрузки][], обязательно
передайте его мигратору с помощью опции [`--load-path`][].

[путь загрузки]: https://sass-lang.com/documentation/at-rules/use#load-paths
[`--load-path`]: #load-path

К сожалению, мигратор не поддерживает пользовательские импортеры, но у него
есть встроенная поддержка разрешения URL, начинающихся с `~`, путём поиска в
`node_modules`, аналогично [тому, как это делает Webpack][].

[тому, как это делает Webpack]: https://github.com/webpack-contrib/sass-loader#resolving-import-at-rules

#### `--remove-prefix`

Эта опция (сокращённо `-p`) принимает префикс идентификатора, который нужно
удалить из начала всех имён переменных, миксинов и функций при миграции.
Элементы, не начинающиеся с этого префикса, останутся без изменений.

Правило [`@import`][] помещало все элементы верхнего уровня в одну глобальную
область видимости, поэтому, пока оно было стандартным способом загрузки
таблиц стилей, у всех был стимул добавлять префиксы ко всем именам своих
элементов, чтобы случайно не переопределить одноимённый элемент из другой
таблицы стилей. Система модулей решает эту проблему, поэтому полезно
автоматически убирать эти старые префиксы, раз в них больше нет
необходимости.

```shellsession
$ cat style.scss
@import "theme";

@mixin app-inverted {
  color: $app-bg-color;
  background-color: $app-color;
}
$ sass-migrator --migrate-deps module --remove-prefix=app- style.scss
$ cat style.scss
@use "theme";

@mixin inverted {
  color: theme.$bg-color;
  background-color: theme.$color;
}
```

Когда вы передаёте эту опцию, мигратор также сгенерирует [таблицу стилей
только для импорта][], которая [пересылает][] все элементы с добавленным
обратно префиксом, чтобы сохранить обратную совместимость для тех, кто
импортировал библиотеку.

[таблицу стилей только для импорта]: https://sass-lang.com/documentation/at-rules/import/#import-only-files
[пересылает]: https://sass-lang.com/documentation/at-rules/forward

Эту опцию можно передавать несколько раз или с несколькими значениями,
разделёнными запятыми. Каждый префикс будет удалён из всех элементов, у
которых он есть. Если элемент соответствует нескольким префиксам, будет
удалён самый длинный из совпавших префиксов.

#### `--forward`

Эта опция указывает мигратору, какие элементы пересылать с помощью правила
[`@forward`][]. Она поддерживает следующие значения:

- `none` (по умолчанию) не пересылает никакие элементы.

- `all` пересылает все элементы, кроме тех, что в исходной таблице стилей
  начинались с `-` или `_`, поскольку это обычно использовалось для
  обозначения элемента, приватного для пакета, до появления системы модулей.

- `prefixed` пересылает только элементы, начинающиеся с префикса, переданного
  в опцию [`--remove-prefix`][]. Эту опцию можно использовать только вместе с
  опцией `--remove-prefix`.

  [`--remove-prefix`]: #remove-prefix

Все файлы, переданные явно в командной строке, будут пересылать элементы,
транзитивно загружаемые этими файлами через правило `@import`. Файлы,
загруженные с помощью опции [`--migrate-deps`][], не будут пересылать никакие
новые элементы. Эта опция особенно полезна при миграции библиотеки Sass,
поскольку она гарантирует, что пользователи этой библиотеки по-прежнему
смогут получить доступ ко всем определённым в ней элементам.

```shellsession
$ cat _index.scss
@import "theme";
@import "typography";
@import "components";
$ sass-migrator --migrate-deps module --forward=all style.scss
$ cat _index.scss
@forward "theme";
@forward "typography";
@forward "components";
```

### Пространство имён

Эта миграция позволяет легко изменить [пространства имён][] правил `@use` в
таблице стилей. Это полезно, если пространства имён, которые генерирует
мигратор модулей для разрешения конфликтов, не идеальны, или если вы не
хотите использовать пространство имён по умолчанию, которое Sass определяет
на основе URL правила.

[пространства имён]: https://sass-lang.com/documentation/at-rules/use#choosing-a-namespace

#### `--rename`

Вы можете указать мигратору, какое пространство имён (или несколько) вы
хотите изменить, передав выражения в опцию `--rename`.

Эти выражения имеют вид `<old-namespace> to <new-namespace>` или
`url <rule-url> to <new-namespace>`. В этих выражениях `<old-namespace>` и
`<rule-url>` — это [регулярные выражения][], которые сопоставляются целиком с
существующим пространством имён или, соответственно, с URL правила `@use`.

[регулярные выражения]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions

В простых случаях это выглядит просто как `--rename 'old to new'` — это
переименует пространство имён `old` правила `@use` в `new`.

Однако с помощью этого можно выполнять и более сложные переименования.
Например, предположим, что раньше у вас была таблица стилей вида:

```scss
@import 'components/button/lib/mixins';
@import 'components/input/lib/mixins';
@import 'components/table/lib/mixins';
// ...
```

Поскольку все эти URL при миграции в правила `@use` получили бы пространство
имён по умолчанию `mixins`, мигратор модулей может в итоге сгенерировать
что-то вроде:

```scss
@use 'components/button/lib/mixins' as button-lib-mixins;
@use 'components/input/lib/mixins' as input-lib-mixins;
@use 'components/table/lib/mixins' as table-lib-mixins;
// ...
```

Это валидный код, поскольку пространства имён не конфликтуют, но они
значительно сложнее, чем нужно. Значимая часть URL — имя компонента, поэтому
мы можем использовать мигратор пространств имён, чтобы извлечь именно эту
часть.

Если запустить мигратор пространств имён с
`--rename 'url components/(\w+)/lib/mixins to \1'`, в итоге получится:

```scss
@use 'components/button/lib/mixins' as button;
@use 'components/input/lib/mixins' as input;
@use 'components/table/lib/mixins' as table;
// ...
```

Этот скрипт переименования говорит найти все правила `@use`, чьи URL выглядят
как `components/(\w+)/lib/mixins` (`\w+` в регулярном выражении означает
совпадение с любым словом из одного или более символов). `\1` в выходной
части означает подстановку содержимого первой группы скобок регулярного
выражения (называемой [группой][]).

[группой]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges

Если вы хотите применить несколько переименований, можно передать опцию
`--rename` несколько раз или разделить их точкой с запятой или переносом
строки. Для каждого правила будет использовано только первое подходящее
переименование, поэтому можно передать что-то вроде `--rename 'a to b; b to a'`,
чтобы поменять местами пространства имён `a` и `b`.

#### `--force`

По умолчанию, если после миграции два или более правил `@use` получают
одинаковое пространство имён, мигратор завершится с ошибкой, и никакие
изменения не будут внесены.

В этом случае обычно стоит скорректировать скрипт `--rename`, чтобы избежать
конфликтов, но если вы предпочитаете принудительно выполнить миграцию, вместо
этого можно передать `--force`.

С `--force`, если возникают конфликты, первое правило `@use` получит своё
предпочитаемое пространство имён, а к последующим правилам `@use` с тем же
предпочитаемым пространством имён вместо этого будет добавлен числовой
суффикс.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/cli/migrator/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

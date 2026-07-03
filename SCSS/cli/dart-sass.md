# Интерфейс командной строки Dart Sass


## Использование

Исполняемый файл Dart Sass можно запускать в одном из двух режимов.

### Режим «один к одному»

```shellsession
sass <input.scss> [output.css]
```

Режим «один к одному» компилирует один входной файл (`input.scss`) в один
выходной файл (`output.css`). Если путь для вывода не указан, скомпилированный
CSS выводится в терминал.

Входной файл разбирается как [SCSS][], если его расширение — `.scss`, как
[отступный синтаксис][], если расширение — `.sass`, или как [обычный CSS][],
если расширение — `.css`. Если файл не имеет ни одного из этих трёх расширений
или поступает из стандартного ввода, по умолчанию он разбирается как SCSS. За
это отвечает [флаг `--indented`][].

[SCSS]: https://sass-lang.com/documentation/syntax#scss
[отступный синтаксис]: https://sass-lang.com/documentation/syntax#the-indented-syntax
[обычный CSS]: https://sass-lang.com/documentation/at-rules/import/#importing-css
[флаг `--indented`]: #indented

Вместо входного файла можно передать специальную строку `-`, чтобы указать
Sass считывать входной файл из [стандартного ввода][]. Sass по умолчанию
разбирает его как SCSS, если не передан [флаг `--indented`][].

[стандартного ввода]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)

### Режим «многие-ко-многим»

**Совместимость:** Dart Sass: начиная с 1.4.0


```shellsession
sass [<input.scss>:<output.css>] [<input/>:<output/>]...
```

Режим «многие-ко-многим» компилирует один или несколько входных файлов в один
или несколько выходных файлов. Входные файлы отделяются от выходных
двоеточиями. Он также позволяет скомпилировать все файлы Sass в каталоге в
CSS-файлы с такими же именами в другом каталоге.

```shellsession
​# Compiles style.scss to style.css.
$ sass style.scss:style.css

​# Compiles light.scss and dark.scss to light.css and dark.css.
$ sass light.scss:light.css dark.scss:dark.css

​# Compiles all Sass files in themes/ to CSS files in public/css/.
$ sass themes:public/css
```

При компиляции целых каталогов Sass будет игнорировать [частичные файлы][],
имена которых начинаются с `_`. С помощью частичных файлов можно разделять
таблицы стилей, не создавая при этом лишних выходных файлов.

[частичные файлы]: https://sass-lang.com/documentation/at-rules/import/#partials

## Опции

### Ввод и вывод

Эти опции управляют тем, как Sass загружает входные файлы и как формирует
выходные файлы.

#### `--stdin`

Этот флаг — альтернативный способ указать Sass, что входной файл нужно читать
из [стандартного ввода][]. При его использовании входной файл передавать
нельзя.

```shellsession
$ echo "h1 {font-size: 40px}" | sass --stdin h1.css
$ echo "h1 {font-size: 40px}" | sass --stdin
h1 {
  font-size: 40px;
}
```

Флаг `--stdin` нельзя использовать в [режиме «многие-ко-многим»][].

[режиме «многие-ко-многим»]: #many-to-many-mode

#### `--indented`

Этот флаг указывает Sass разбирать входной файл как [отступный синтаксис][].
Если он используется в [режиме «многие-ко-многим»][], все входные файлы
разбираются как отступный синтаксис, хотя для файлов, которые они
[используют][], синтаксис по-прежнему определяется как обычно. Обратный флаг,
`--no-indented`, можно использовать, чтобы принудительно разбирать все
входные файлы как [SCSS][].

[используют]: https://sass-lang.com/documentation/at-rules/use

Флаг `--indented` полезен в основном тогда, когда входной файл поступает из
[стандартного ввода][], и его синтаксис невозможно определить автоматически.

```shellsession
$ echo -e 'h1\n  font-size: 40px' | sass --indented -
h1 {
  font-size: 40px;
}
```

#### `--load-path`

Эта опция (сокращённо `-I`) добавляет дополнительный [путь загрузки][], по
которому Sass ищет таблицы стилей. Её можно передавать несколько раз, указывая
несколько путей загрузки. Более ранние пути загрузки имеют приоритет над более
поздними.

[путь загрузки]: https://sass-lang.com/documentation/at-rules/use#load-paths

```shellsession
$ sass --load-path=node_modules/bootstrap/dist/css style.scss style.css
```

#### `--pkg-importer=node`

**Совместимость:** Dart Sass: начиная с 1.71.0


Эта опция (сокращённо `-p node`) добавляет [Node.js-импортёр `pkg:`][] в конец
пути загрузки, чтобы таблицы стилей могли загружать зависимости с помощью
алгоритма разрешения модулей Node.js.

[Node.js-импортёр `pkg:`]: https://sass-lang.com/documentation/at-rules/use#node-js-package-importer

Поддержка дополнительных встроенных импортёров `pkg:` может быть добавлена в
будущем.

```shellsession
$ sass --pkg-importer=node style.scss style.css
```

#### `--style`

Эта опция (сокращённо `-s`) управляет стилем вывода итогового CSS. Dart Sass
поддерживает два стиля вывода:

- `expanded` (используется по умолчанию) — записывает каждый селектор и
  объявление на отдельной строке.
- `compressed` — убирает как можно больше лишних символов и записывает всю
  таблицу стилей в одну строку.

```shellsession
$ sass --style=expanded style.scss
h1 {
  font-size: 40px;
}

$ sass --style=compressed style.scss
h1{font-size:40px}
```

#### `--no-charset`

**Совместимость:** Dart Sass: начиная с 1.19.0


Этот флаг указывает Sass никогда не выводить объявление `@charset` или UTF-8
[метку порядка байтов][]. По умолчанию, либо если передан `--charset`, Sass
вставит либо объявление `@charset` (в развёрнутом режиме вывода), либо метку
порядка байтов (в сжатом режиме вывода), если таблица стилей содержит символы
не из ASCII.

[метку порядка байтов]: https://en.wikipedia.org/wiki/Byte_order_mark#UTF-8

```shellsession
$ echo 'h1::before {content: "👭"}' | sass --no-charset
h1::before {
  content: "👭";
}

$ echo 'h1::before {content: "👭"}' | sass --charset
@charset "UTF-8";
h1::before {
  content: "👭";
}
```

#### `--error-css`

**Совместимость:** Dart Sass: начиная с 1.20.0


Этот флаг указывает Sass, нужно ли выводить CSS-файл при возникновении ошибки
во время компиляции. Такой CSS-файл описывает ошибку в комментарии _и_ в
свойстве `content` селектора `body::before`, чтобы сообщение об ошибке было
видно прямо в браузере, без необходимости переключаться обратно в терминал.

По умолчанию вывод CSS с ошибкой включён, если вы компилируете хотя бы в один
файл на диске (в отличие от вывода в стандартный поток). Можно явно передать
`--error-css`, чтобы включить это поведение даже при выводе в стандартный
поток, либо `--no-error-css`, чтобы отключить его повсюду. Когда оно
отключено, [флаг `--update`][] и [флаг `--watch`][] будут вместо этого удалять
CSS-файлы при возникновении ошибки.

[флаг `--watch`]: #watch

```shellsession
$ sass --error-css style.scss style.css
/* Error: Incompatible units em and px.
 *   ,
 * 1 | $width: 15px + 2em;
 *   |         ^^^^^^^^^^
 *   '
 *   test.scss 1:9  root stylesheet */

body::before {
  font-family: "Source Code Pro", "SF Mono", Monaco, Inconsolata, "Fira Mono",
      "Droid Sans Mono", monospace, monospace;
  white-space: pre;
  display: block;
  padding: 1em;
  margin-bottom: 1em;
  border-bottom: 2px solid black;
  content: "Error: Incompatible units em and px.\a   \2577 \a 1 \2502  $width: 15px + 2em;\a   \2502          ^^^^^^^^^^\a   \2575 \a   test.scss 1:9  root stylesheet";
}
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em;
  │         ^^^^^^^^^^
  ╵
  test.scss 1:9  root stylesheet
```

#### `--update`

**Совместимость:** Dart Sass: начиная с 1.4.0


Если передан флаг `--update`, Sass будет компилировать только те таблицы
стилей, зависимости которых были изменены позже, чем был сгенерирован
соответствующий CSS-файл. Также при обновлении таблиц стилей будут выводиться
сообщения о состоянии.

```shellsession
$ sass --update themes:public/css
Compiled themes/light.scss to public/css/light.css.
```

### Карты источников

**Совместимость:** Dart Sass: начиная с 1.3.0


Карты источников (source map) — это файлы, которые сообщают браузерам или
другим инструментам, работающим с CSS, как этот CSS соответствует файлам Sass,
из которых он был сгенерирован. Они позволяют просматривать и даже
редактировать файлы Sass прямо в браузере. См. инструкции по использованию
карт источников в [Chrome][] и [Firefox][].

[Chrome]: https://developers.google.com/web/tools/chrome-devtools/javascript/source-maps
[Firefox]: https://developer.mozilla.org/en-US/docs/Tools/Style_Editor#Source_map_support


Dart Sass по умолчанию генерирует карту источников для каждого выводимого
CSS-файла.

#### `--no-source-map`

Если передан флаг `--no-source-map`, Sass не будет генерировать карты
источников. Его нельзя передавать вместе с другими опциями карт источников.

```shellsession
$ sass --no-source-map style.scss style.css
```

#### `--source-map-urls`

Эта опция управляет тем, как сгенерированные Sass карты источников ссылаются
на файлы Sass, участвовавшие в получении итогового CSS. Dart Sass поддерживает
два типа URL:

- `relative` (по умолчанию) использует относительные URL от расположения
  файла карты источников до расположения исходного файла Sass.
- `absolute` использует абсолютные [URL-адреса `file:`][] исходных файлов
  Sass. Обратите внимание, что абсолютные URL будут работать только на том же
  компьютере, на котором был скомпилирован CSS.

[URL-адреса `file:`]: https://en.wikipedia.org/wiki/File_URI_scheme

```shellsession
​# Generates a URL like "../sass/style.scss".
$ sass --source-map-urls=relative sass/style.scss css/style.css

​# Generates a URL like "file:///home/style-wiz/sassy-app/sass/style.scss".
$ sass --source-map-urls=absolute sass/style.scss css/style.css
```

#### `--embed-sources`

Этот флаг указывает Sass встраивать всё содержимое файлов Sass, участвовавших
в получении итогового CSS, в карту источников. Это может привести к очень
большим картам источников, но гарантирует, что исходный код будет доступен на
любом компьютере независимо от того, как обслуживается CSS.

```shellsession
$ sass --embed-sources sass/style.scss css.style.css
```

#### `--embed-source-map`

Этот флаг указывает Sass встраивать содержимое файла карты источников в
сгенерированный CSS, вместо того чтобы создавать отдельный файл и ссылаться
на него из CSS.

```shellsession
$ sass --embed-source-map sass/style.scss css.style.css
```

### Прочие опции

#### `--watch`

**Совместимость:** Dart Sass: начиная с 1.6.0


Этот флаг (сокращённо `-w`) действует как [флаг `--update`][], но после
завершения первого раунда компиляции Sass остаётся открытым и продолжает
компилировать таблицы стилей при каждом изменении их самих или их
зависимостей.

[флаг `--update`]: #update

Sass отслеживает только те каталоги, которые вы передаёте в командной строке
как есть, родительские каталоги файлов, переданных в командной строке, и пути
загрузки. Он не отслеживает дополнительные каталоги на основе правил
`@import`/`@use`/`@forward` файла.

```shellsession
$ sass --watch themes:public/css
Compiled themes/light.scss to public/css/light.css.

​# Then when you edit themes/dark.scss...
Compiled themes/dark.scss to public/css/dark.css.
```

#### `--poll`

**Совместимость:** Dart Sass: начиная с 1.8.0


Этот флаг, который можно передавать только вместе с `--watch`, указывает Sass
вручную проверять изменения в исходных файлах через определённые промежутки
времени, вместо того чтобы полагаться на уведомления операционной системы об
изменениях. Это может понадобиться, если вы редактируете Sass на удалённом
диске, где система уведомлений операционной системы не работает.

```shellsession
$ sass --watch --poll themes:public/css
Compiled themes/light.scss to public/css/light.css.

​# Then when you edit themes/dark.scss...
Compiled themes/dark.scss to public/css/dark.css.
```

#### `--stop-on-error`

**Совместимость:** Dart Sass: начиная с 1.8.0


Этот флаг указывает Sass немедленно останавливать компиляцию при обнаружении
ошибки, вместо того чтобы пытаться скомпилировать остальные файлы Sass, в
которых ошибок может не быть. Он полезен в основном в [режиме
«многие-ко-многим»][].

```shellsession
$ sass --stop-on-error themes:public/css
Error: Expected expression.
   ╷
42 │ h1 {font-face: }
   │                ^
   ╵
  themes/light.scss 42:16  root stylesheet
```

#### `--interactive`

**Совместимость:** Dart Sass: начиная с 1.5.0


Этот флаг (сокращённо `-i`) указывает Sass работать в интерактивном режиме, в
котором можно писать [SassScript-выражения][] и видеть результат их
вычисления. Интерактивный режим также поддерживает [переменные][] и
[правила `@use`][].

[SassScript-выражения]: https://sass-lang.com/documentation/syntax/structure#expressions
[переменные]: https://sass-lang.com/documentation/variables
[правила `@use`]: https://sass-lang.com/documentation/at-rules/use

```shellsession
$ sass --interactive
>> 1px + 1in
97px
>> @use "sass:map"
>> $map: ("width": 100px, "height": 70px)
("width": 100px, "height": 70px)
>> map.get($map, "width")
100px
```

#### `--color`

Этот флаг (сокращённо `-c`) указывает Sass выводить [цвета терминала][], а
обратный флаг `--no-color` указывает не выводить их. По умолчанию цвета
выводятся, если похоже, что Sass запущен в терминале, который их
поддерживает.

[цвета терминала]: https://en.wikipedia.org/wiki/ANSI_escape_code#Colors

<pre class="language-plaintext"><code>$ sass --color style.scss style.css
Error: Incompatible units em and px.
  <span style="color: blue">╷</span>
<span style="color: blue">1 │</span> $width: <span style="color: crimson">15px + 2em</span>
  <span style="color: blue">│</span>         <span style="color: crimson">^^^^^^^^^^</span>
  <span style="color: blue">╵</span>
  style.scss 1:9  root stylesheet

$ sass --no-color style.scss style.css
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em
  │         ^^^^^^^^^^
  ╵
  style.scss 1:9  root stylesheet</code></pre>

#### `--no-unicode`

**Совместимость:** Dart Sass: начиная с 1.17.0


Этот флаг указывает Sass выводить в терминал в сообщениях об ошибках только
символы ASCII. По умолчанию, либо если передан `--unicode`, Sass будет
выводить в таких сообщениях символы не из ASCII. Этот флаг не влияет на вывод
CSS.

```shellsession
$ sass --no-unicode style.scss style.css
Error: Incompatible units em and px.
  ,
1 | $width: 15px + 2em;
  |         ^^^^^^^^^^
  '
  test.scss 1:9  root stylesheet

$ sass --unicode style.scss style.css
Error: Incompatible units em and px.
  ╷
1 │ $width: 15px + 2em;
  │         ^^^^^^^^^^
  ╵
  test.scss 1:9  root stylesheet
```

#### `--verbose`

Этот флаг указывает Sass выводить при компиляции _все_ предупреждения об
устаревании. По умолчанию Sass выводит не более пяти предупреждений для
каждого типа устаревания при использовании устаревших возможностей, а
остальные предупреждения сверх этого числа подавляет.

```shellsession
$ sass --verbose style.scss style.css
```

#### `--quiet`

Этот флаг (сокращённо `-q`) указывает Sass не выводить при компиляции никаких
предупреждений. По умолчанию Sass выводит предупреждения при использовании
устаревших возможностей либо когда встречается [правило `@warn`][]. Он также
подавляет [правило `@debug`][].

[правило `@warn`]: https://sass-lang.com/documentation/at-rules/warn
[правило `@debug`]: https://sass-lang.com/documentation/at-rules/debug

```shellsession
$ sass --quiet style.scss style.css
```

#### `--quiet-deps`

Этот флаг указывает Sass не выводить предупреждения об устаревании, которые
приходят из зависимостей. Любой файл, который транзитивно импортируется через
[путь загрузки], считается «зависимостью». Этот флаг не влияет на
[правило `@warn`] или [правило `@debug`].

```shellsession
$ sass --load-path=node_modules --quiet-deps style.scss style.css
```

#### `--fatal-deprecation`

**Совместимость:** Dart Sass: начиная с 1.59.0


Эта опция указывает Sass считать предупреждение об устаревании определённого
типа ошибкой. Например, следующая команда указывает Sass считать ошибками
предупреждения об устаревании `/` как оператора деления:

```shellsession
$ sass --fatal-deprecation=slash-div style.scss style.css
Error: Using / for division outside of calc() is deprecated and will be removed in Dart Sass 2.0.0.

Recommendation: math.div(4, 2) or calc(4 / 2)

More info and automated migrator: /documentation/breaking-changes/slash-div

This is only an error because you've set the slash-div deprecation to be fatal.
Remove this setting if you need to keep using this feature.
  ╷
1 │ a { b: (4/2); }
  │         ^^^
  ╵
  style.scss 1:9  root stylesheet
```

> **Примечание:** См. [актуальный список действующих устареваний](https://sass-lang.com/documentation/breaking-changes/) на официальном сайте Sass.

Также можно передать версию Dart Sass, чтобы все устаревания, присутствовавшие
в этой версии, считались ошибками. Например, `--fatal-deprecation=1.33.0`
приведёт к тому, что все устаревания из таблицы выше вплоть до `slash-div`
включительно будут считаться ошибками, а более новые устаревания останутся
предупреждениями.

> **Примечание:** См. [актуальный список действующих устареваний](https://sass-lang.com/documentation/breaking-changes/) на официальном сайте Sass.

#### `--future-deprecation`

**Совместимость:** Dart Sass: начиная с 1.59.0


Эта опция указывает Sass заранее включить предупреждение об устаревании
будущего типа, выводя предупреждения, даже если это устаревание ещё не
действует. Эту опцию можно комбинировать с `--fatal-deprecation`, чтобы для
будущего устаревания выводились ошибки вместо предупреждений.

```shellsession
$ sass --future-deprecation=import style.scss style.css
Deprecation Warning on line 1, column 9 of style.scss:
Sass @import rules will be deprecated in the future.
Remove the --future-deprecation=import flag to silence this warning for now.
  ╷
1 │ @import 'dependency';
  │         ^^^^^^^^^^^^
  ╵
```

> **Примечание:** См. [актуальный список действующих устареваний](https://sass-lang.com/documentation/breaking-changes/) на официальном сайте Sass.

#### `--silence-deprecation`

**Совместимость:** Dart Sass: начиная с 1.74.0


Эта опция указывает Sass подавлять предупреждение об устаревании
определённого типа, если вы хотите временно игнорировать это устаревание.
Сюда можно передать любое из устареваний, перечисленных выше в разделе
`--fatal-deprecation`, однако передача версии здесь не поддерживается.

```shellsession
$ sass --silence-deprecation=slash-div style.scss style.css
```

#### `--trace`

Этот флаг указывает Sass выводить полный стек вызовов Dart или JavaScript при
возникновении ошибки. Он используется командой Sass для отладки ошибок.

```shellsession
$ sass --trace style.scss style.css
Error: Expected expression.
   ╷
42 │ h1 {font-face: }
   │                ^
   ╵
  themes/light.scss 42:16  root stylesheet

package:sass/src/visitor/evaluate.dart 1846:7                        _EvaluateVisitor._addExceptionSpan
package:sass/src/visitor/evaluate.dart 1128:12                       _EvaluateVisitor.visitBinaryOperationExpression
package:sass/src/ast/sass/expression/binary_operation.dart 39:15     BinaryOperationExpression.accept
package:sass/src/visitor/evaluate.dart 1097:25                       _EvaluateVisitor.visitVariableDeclaration
package:sass/src/ast/sass/statement/variable_declaration.dart 50:15  VariableDeclaration.accept
package:sass/src/visitor/evaluate.dart 335:13                        _EvaluateVisitor.visitStylesheet
package:sass/src/visitor/evaluate.dart 323:5                         _EvaluateVisitor.run
package:sass/src/visitor/evaluate.dart 81:10                         evaluate
package:sass/src/executable/compile_stylesheet.dart 59:9             compileStylesheet
package:sass/src/executable.dart 62:15                               main
```

#### `--help`

Этот флаг (сокращённо `-h`) выводит краткую сводку этой документации.

```shellsession
$ sass --help
Compile Sass to CSS.

Usage: sass <input.scss> [output.css]
       sass <input.scss>:<output.css> <input/>:<output/>

...
```

#### `--version`

Этот флаг выводит текущую версию Sass.

```shellsession
$ sass --version
the latest version
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/cli/dart-sass/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# Интерфейс командной строки Ruby Sass


> ⚠️ **Внимание**
>
> [Жизненный цикл Ruby Sass завершён][], и теперь он полностью не поддерживается. Пожалуйста, при первой возможности переходите на [Dart Sass].
>
> [Жизненный цикл Ruby Sass завершён]: https://sass-lang.com/blog/ruby-sass-is-unsupported
> [Dart Sass]: https://sass-lang.com/dart-sass

## Использование

Исполняемый файл Ruby Sass можно вызывать в одном из двух режимов.

### Режим «один к одному»

```shellsession
sass [input.scss] [output.css]
```

Режим «один к одному» компилирует один входной файл (`input.scss`) в одно
расположение вывода (`output.css`). Если расположение вывода не указано,
скомпилированный CSS выводится в терминал. Если не указаны ни вход, ни выход,
CSS считывается из [стандартного ввода][standard input] и выводится в терминал.

[standard input]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)

Входной файл разбирается как [SCSS][] если его расширение — `.scss`, или как
[отступный синтаксис][indented syntax], если расширение — `.sass`. Если у файла
ни одно из этих двух расширений, либо он поступает из стандартного ввода, по
умолчанию он разбирается как отступный синтаксис. Это можно настроить с помощью
[флага `--scss`][`--scss` flag].

[SCSS]: https://sass-lang.com/documentation/syntax#scss
[indented syntax]: https://sass-lang.com/documentation/syntax#the-indented-syntax
[`--scss` flag]: #scss

### Режим «многие ко многим»

```shellsession
sass [<input.css>:<output.css>] [<input/>:<output/>] [input.css] [input/]...
```

Режим «многие ко многим» компилирует один или несколько входных файлов в один
или несколько выходных файлов. Входы отделяются от выходов двоеточиями. Также
он может скомпилировать все файлы Sass в директории в CSS-файлы с теми же
именами в другой директории. Режим «многие ко многим» включается, когда любой
из аргументов содержит двоеточие, _или_ когда передан [флаг `--update`][`--update` flag]
либо [флаг `--watch`][`--watch` flag].

[`--update` flag]: #update
[`--watch` flag]: #watch

Если входной файл передан без соответствующего выходного файла, он
компилируется в CSS-файл, названный так же, как входной файл, но с заменённым
на `.css` расширением. Если входная директория передана без соответствующей
выходной директории, все файлы Sass внутри неё компилируются в CSS-файлы в той
же директории.

```shellsession
$ sass style.scss:style.css
      write style.css
      write style.css.map
$ sass light.scss:light.css dark.scss:dark.css
      write light.css
      write light.css.map
      write dark.css
      write dark.css.map
$ sass themes:public/css
      write public/css/light.css
      write public/css/light.css.map
      write public/css/dark.css
      write public/css/dark.css.map
```

При компиляции целых директорий Sass будет игнорировать [файлы-частичники][partial files],
имена которых начинаются с `_`. Частичники можно использовать, чтобы разделять
таблицы стилей на части, не создавая при этом лишних выходных файлов.

[partial files]: https://sass-lang.com/documentation/at-rules/use/#partials

Режим «многие ко многим» будет компилировать только те таблицы стилей, чьи
зависимости были изменены позже, чем был сгенерирован соответствующий
CSS-файл. Он также выводит статусные сообщения при обновлении таблиц стилей.

## Опции

### Общие

#### `--load-path`

Эта опция (сокращённо `-I`) добавляет дополнительный [путь загрузки][load path],
по которому Sass ищет таблицы стилей. Её можно передавать несколько раз, чтобы
указать несколько путей загрузки. Более ранние пути загрузки имеют приоритет
над более поздними.

[load path]: https://sass-lang.com/documentation/at-rules/use#load-paths

```shellsession
$ sass --load-path=node_modules/bootstrap/dist/css style.scss style.css
```

Пути загрузки также подгружаются из [переменной окружения][environment variable]
`SASS_PATH`, если она задана. Эта переменная должна представлять собой список
путей, разделённых `;` (в Windows) или `:` (в других операционных системах).
Пути загрузки из `SASS_PATH` имеют приоритет над путями, переданными в
командной строке.

[environment variable]: https://en.wikipedia.org/wiki/Environment_variable

```shellsession
$ SASS_PATH=node_modules/bootstrap/dist/css sass style.scss style.css
```

#### `--require`

Эта опция (сокращённо `-r`) загружает [Ruby-гем][Ruby gem] перед запуском Sass.
Её можно использовать, чтобы подключить в окружение Sass функции, определённые
на Ruby.

[Ruby gem]: https://rubygems.org/

```shellsession
$ sass --require=rails-sass-images style.scss style.css
```

#### `--compass`

Этот флаг загружает [фреймворк Compass][Compass framework] и делает доступными
для использования его миксины и функции.

[Compass framework]: http://compass-style.org/

```shellsession
$ sass --compass style.scss style.css
```

#### `--style`

Эта опция (сокращённо `-t`) управляет стилем вывода итогового CSS. Ruby Sass
поддерживает четыре стиля вывода:

- `nested` (по умолчанию) отступает правила CSS в соответствии с вложенностью
  исходного кода Sass.
- `expanded` записывает каждый селектор и объявление на отдельной строке.
- `compact` помещает каждое правило CSS на одну отдельную строку.
- `compressed` убирает как можно больше лишних символов и записывает всю
  таблицу стилей на одной строке.

```shellsession
$ sass --style=nested
h1 {
  font-size: 40px; }
  h1 code {
    font-face: Roboto Mono; }

$ sass --style=expanded style.scss
h1 {
  font-size: 40px;
}
h1 code {
  font-face: Roboto Mono;
}

$ sass --style=compact style.scss
h1 { font-size: 40px; }
h1 code { font-face: Roboto Mono; }

$ sass --style=compressed style.scss
h1{font-size:40px}h1 code{font-face:Roboto Mono}
```

#### `--help`

Этот флаг (сокращённо `-h` и `-?`) выводит сводку этой документации.

```shellsession
$ sass --help
Usage: sass [options] [INPUT] [OUTPUT]

Description:
  Converts SCSS or Sass files to CSS.

...
```

#### `--version`

Этот флаг выводит текущую версию Sass.

```shellsession
$ sass --version
Sass 3.7.4
```

### Отслеживание и обновление

Эти опции влияют на [режим «многие ко многим»][many-to-many mode].

[many-to-many mode]: #many-to-many-mode

#### `--watch`

Включает [режим «многие ко многим»][many-to-many mode] и заставляет Sass
оставаться запущенным, продолжая компилировать таблицы стилей при каждом
изменении их самих или их зависимостей.

```shellsession
$ sass --watch themes:public/css
      write public/css/light.css
      write public/css/light.css.map

​# Затем, когда вы отредактируете themes/dark.scss...
      write public/css/dark.css
      write public/css/dark.css.map
```

#### `--poll`

Этот флаг, который можно передавать только вместе с `--watch`, указывает Sass
вручную проверять изменения исходных файлов через определённые промежутки
времени, вместо того чтобы полагаться на уведомления операционной системы об
изменениях. Это может понадобиться, если вы редактируете Sass на удалённом
диске, где система уведомлений операционной системы не работает.

```shellsession
$ sass --watch --poll themes:public/css
      write public/css/light.css
      write public/css/light.css.map

​# Затем, когда вы отредактируете themes/dark.scss...
      write public/css/dark.css
      write public/css/dark.css.map
```

#### `--update`

Этот флаг включает [режим «многие ко многим»][many-to-many mode], даже если ни
один из аргументов не является парой, разделённой двоеточием.

```shellsession
$ sass --update style.scss
      write style.css
      write style.css.map
```

#### `--force`

Этот флаг (сокращённо `-f`) можно передавать только в [режиме «многие ко многим»][many-to-many mode].
Он заставляет файлы Sass _всегда_ компилироваться в файлы CSS, вместо того
чтобы компилироваться только тогда, когда исходные файлы новее, чем результат.

Флаг `--force` нельзя передавать вместе с [флагом `--watch`][`--watch` flag].

```shellsession
$ sass --force style.scss:style.css
      write style.css
      write style.css.map
```

#### `--stop-on-error`

Этот флаг можно передавать только в [режиме «многие ко многим»][many-to-many mode].
Он указывает Sass немедленно останавливать компиляцию при обнаружении ошибки,
вместо того чтобы пытаться скомпилировать другие файлы Sass, в которых ошибок
может и не быть.

```shellsession
$ sass --stop-on-error themes:public/css
Error: Invalid CSS after "h1 {font-size: ": expected expression (e.g. 1px, bold), was "}"
        on line 1 of test.scss
  Use --trace for backtrace.
```

### Ввод и вывод

Эти опции управляют тем, как Sass загружает входные файлы и как он производит
выходные файлы.

#### `--scss`

Этот флаг указывает Sass разбирать [стандартный ввод][standard input] как
[SCSS][].

```shellsession
$ echo "h1 {font-size: 40px}" | sass --scss
h1 {
  font-size: 40px;
}
```

#### `--sourcemap`

Эта опция управляет тем, как Sass генерирует карты источников — файлы, которые
сообщают браузерам или другим инструментам, потребляющим CSS, как этот CSS
соотносится с файлами Sass, из которых он был сгенерирован. Они позволяют
просматривать и даже редактировать файлы Sass прямо в браузере. Инструкции по
использованию карт источников см. в [Chrome][] и [Firefox][]. У опции есть
четыре возможных значения:

[Chrome]: https://developers.google.com/web/tools/chrome-devtools/javascript/source-maps
[Firefox]: https://developer.mozilla.org/en-US/docs/Tools/Style_Editor#Source_map_support

- `auto` (по умолчанию) использует относительные URL для связи карты
  источников с таблицами стилей Sass там, где это возможно, а в остальных
  случаях — абсолютные [`file:`-URL][`file:` URLs].
- `file` всегда использует абсолютные `file:`-URL для связи карты источников с
  таблицами стилей Sass.
- `inline` включает текст таблиц стилей Sass непосредственно в карту
  источников.
- `none` вообще не генерирует карты источников.

[`file:` URLs]: https://en.wikipedia.org/wiki/File_URI_scheme

```shellsession
​# Генерирует URL вида "../sass/style.scss".
$ sass --sourcemap=auto sass/style.scss css/style.css

​# Генерирует URL вида "file:///home/style-wiz/sassy-app/sass/style.scss".
$ sass --sourcemap=file sass/style.scss css/style.css

​# Включает полный текст sass/style.scss в карту источников.
$ sass --sourcemap=inline sass/style.scss css/style.css

​# Не генерирует карту источников.
$ sass --sourcemap=none sass/style.scss css/style.css
```

#### `--stdin`

Этот флаг (сокращённо `-s`) указывает Sass читать входной файл из
[стандартного ввода][standard input]. При его передаче входной файл передавать
нельзя.

```shellsession
$ echo -e 'h1\n  font-size: 40px' | sass --stdin
h1 {
  font-size: 40px;
}
```

Флаг `--stdin` нельзя использовать вместе с [режимом «многие ко многим»][many-to-many mode].

#### `--default-encoding`

Эта опция (сокращённо `-E`) управляет [кодировкой символов][character encoding]
по умолчанию, которую Sass будет использовать для загрузки исходных файлов, не
[указывающих кодировку явно][explicitly specify]. По умолчанию используется
кодировка операционной системы по умолчанию.

[character encoding]: https://en.wikipedia.org/wiki/Character_encoding
[explicitly specify]: https://sass-lang.com/documentation/syntax/parsing#input-encoding

```shellsession
$ sass --default-encoding=Shift-JIS style.scss style.css
```

#### `--unix-newlines`

Этот флаг указывает Sass генерировать выходные файлы, строки которых
разделены символом U+000A LINE FEED, в отличие от значения по умолчанию для
операционной системы (в Windows это U+000D CARRIAGE RETURN, за которым следует
U+000A LINE FEED). Он всегда истинен в системах, где по умолчанию используются
переносы строк в стиле Unix.

```shellsession
$ sass --unix-newlines style.scss style.css
```

#### `--debug-info`

Этот флаг (сокращённо `-g`) заставляет Sass генерировать фиктивные `@media`-запросы,
указывающие, где в исходной таблице стилей было определено каждое стилевое
правило.

> ⚠️ **Внимание**
>
> Этот флаг существует только для обратной совместимости. Теперь
> рекомендуемый способ сопоставления CSS с исходным кодом Sass — карты
> источников.

```shellsession
$ sass --debug-info style.scss
@media -sass-debug-info{filename{font-family:file\:\/\/\/home\/style-wiz\/sassy-app\/style\.scss}line{font-family:\000031}}
h1 {
  font-size: 40px; }
```

#### `--line-comments`

Этот флаг (также доступен как `--line-numbers`, сокращённо `-l`) заставляет
Sass генерировать комментарии для каждого стилевого правила, указывающие, где
в исходной таблице стилей оно было определено.

```shellsession
$ sass --line-numbers style.scss
/* line 1, style.scss */
h1 {
  font-size: 40px; }
```

### Другие опции

#### `--interactive`

Этот флаг (сокращённо `-i`) указывает Sass работать в интерактивном режиме, в
котором можно писать [выражения SassScript][SassScript expressions] и видеть
их результаты. Интерактивный режим также поддерживает [переменные][variables].

[SassScript expressions]: https://sass-lang.com/documentation/syntax/structure#expressions
[variables]: https://sass-lang.com/documentation/variables

```shellsession
$ sass --interactive
>> 1px + 1in
97px
>> $map: ("width": 100px, "height": 70px)
("width": 100px, "height": 70px)
>> map-get($map, "width")
100px
```

#### `--check`

Этот флаг (сокращённо `-c`) указывает Sass проверить корректность синтаксиса
входного файла, не выполняя его. Если синтаксис корректен, Sass завершает
работу с [кодом завершения][status] 0. Флаг нельзя использовать в
[режиме «многие ко многим»][many-to-many mode].

[status]: https://en.wikipedia.org/wiki/Exit_status

```shellsession
$ sass --check style.scss
```

#### `--precision`

Эта опция указывает Sass, сколько знаков [точности][precision] использовать
при выводе десятичных чисел.

[precision]: https://sass-lang.com/documentation/values/numbers#precision

```shellsession
$ echo -e 'h1\n  font-size: (100px / 3)' | sass --precision=20
h1 {
  font-size: 33.333333333333336px; }
```

#### `--cache-location`

Эта опция указывает Sass, где хранить кеш разобранных файлов, чтобы ускорить
последующие запуски. По умолчанию используется `.sass-cache`.

```shellsession
$ sass --cache-location=/tmp/sass-cache style.scss style.css
```

#### `--no-cache`

Этот флаг (сокращённо `-C`) указывает Sass вообще не кешировать разобранные
файлы.

```shellsession
$ sass --no-cache style.scss style.css
```

#### `--trace`

Этот флаг указывает Sass выводить полную трассировку стека Ruby при
возникновении ошибки. Используется командой разработки Sass для отладки
ошибок.

```shellsession
Traceback (most recent call last):
        25: from /usr/share/gems/sass/bin/sass:13:in `<main>'
        24: from /usr/share/gems/sass/lib/sass/exec/base.rb:18:in `parse!'
        23: from /usr/share/gems/sass/lib/sass/exec/base.rb:50:in `parse'
        22: from /usr/share/gems/sass/lib/sass/exec/sass_scss.rb:63:in `process_result'
        21: from /usr/share/gems/sass/lib/sass/exec/sass_scss.rb:396:in `run'
        20: from /usr/share/gems/sass/lib/sass/engine.rb:290:in `render'
        19: from /usr/share/gems/sass/lib/sass/engine.rb:414:in `_to_tree'
        18: from /usr/share/gems/sass/lib/sass/scss/parser.rb:41:in `parse'
        17: from /usr/share/gems/sass/lib/sass/scss/parser.rb:137:in `stylesheet'
        16: from /usr/share/gems/sass/lib/sass/scss/parser.rb:697:in `block_contents'
        15: from /usr/share/gems/sass/lib/sass/scss/parser.rb:707:in `block_child'
        14: from /usr/share/gems/sass/lib/sass/scss/parser.rb:681:in `ruleset'
        13: from /usr/share/gems/sass/lib/sass/scss/parser.rb:689:in `block'
        12: from /usr/share/gems/sass/lib/sass/scss/parser.rb:697:in `block_contents'
        11: from /usr/share/gems/sass/lib/sass/scss/parser.rb:708:in `block_child'
        10: from /usr/share/gems/sass/lib/sass/scss/parser.rb:743:in `declaration_or_ruleset'
         9: from /usr/share/gems/sass/lib/sass/scss/parser.rb:820:in `try_declaration'
         8: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1281:in `rethrow'
         7: from /usr/share/gems/sass/lib/sass/scss/parser.rb:807:in `block in try_declaration'
         6: from /usr/share/gems/sass/lib/sass/scss/parser.rb:999:in `value!'
         5: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1161:in `sass_script'
         4: from /usr/share/gems/sass/lib/sass/script/parser.rb:68:in `parse'
         3: from /usr/share/gems/sass/lib/sass/script/parser.rb:855:in `assert_expr'
         2: from /usr/share/gems/sass/lib/sass/script/lexer.rb:240:in `expected!'
         1: from /usr/share/gems/sass/lib/sass/scss/parser.rb:1305:in `expected'
test.scss:1: Invalid CSS after "h1 {font-size: ": expected expression (e.g. 1px, bold), was "}" (Sass::SyntaxError)
```

#### `--quiet`

Этот флаг (сокращённо `-q`) указывает Sass не выводить никаких предупреждений
при компиляции. По умолчанию Sass выводит предупреждения при использовании
устаревших возможностей или при встрече [правила `@warn`][`@warn` rule]. Он
также отключает [правило `@debug`][`@debug` rule].

[`@warn` rule]: https://sass-lang.com/documentation/at-rules/warn
[`@debug` rule]: https://sass-lang.com/documentation/at-rules/debug

```shellsession
$ sass --quiet style.scss style.css
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/cli/ruby-sass/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

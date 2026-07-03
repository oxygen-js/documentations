# Строки

Строки — это последовательности символов (точнее, [кодовых точек Unicode](https://en.wikipedia.org/wiki/Code_point)). Sass поддерживает два вида строк, у которых внутренняя структура одинакова, но которые выводятся по-разному: [строки в кавычках](#quoted), например `"Helvetica Neue"`, и [строки без кавычек](#unquoted) (также известные как *идентификаторы*), например `bold`. Вместе они охватывают различные виды текста, встречающиеся в CSS.

> 💡 **Занятный факт**
>
> Вы можете преобразовать строку в кавычках в строку без кавычек с помощью функции [`string.unquote()`][], а строку без кавычек — в строку в кавычках с помощью функции [`string.quote()`][].
>
> [`string.unquote()`]: https://sass-lang.com/documentation/modules/string#unquote
> [`string.quote()`]: https://sass-lang.com/documentation/modules/string#quote

**SCSS**

```scss
@use "sass:string";

@debug string.unquote(".widget:hover"); // .widget:hover
@debug string.quote(bold); // "bold"
```

**Sass**

```sass
@use "sass:string"

@debug string.unquote(".widget:hover")  // .widget:hover
@debug string.quote(bold)  // "bold"
```

## Escape-последовательности

Все строки Sass поддерживают стандартные CSS [escape-коды][]:

[escape-коды]: https://developer.mozilla.org/en-US/docs/Web/CSS/string#Syntax

* Любой символ, кроме буквы от A до F или цифры от 0 до 9 (даже перевод строки!), можно включить в строку, написав перед ним `\`.

* Любой символ можно включить в строку, написав `\`, а за ним — номер [кодовой точки Unicode][] этого символа в [шестнадцатеричном формате][]. После номера кодовой точки можно по желанию добавить пробел, чтобы обозначить, где заканчивается число.

  [кодовой точки Unicode]: https://en.wikipedia.org/wiki/List_of_Unicode_characters
  [шестнадцатеричном формате]: https://en.wikipedia.org/wiki/Hexadecimal

**SCSS**

```scss
@debug "\""; // '"'
@debug \.widget; // \.widget
@debug "\a"; // "\a" (a string containing only a newline)
@debug "line1\a line2"; // "line1\a line2"
@debug "Nat + Liz \1F46D"; // "Nat + Liz 👭"
```

**Sass**

```sass
@debug "\""  // '"'
@debug \.widget  // \.widget
@debug "\a"  // "\a" (a string containing only a newline)
@debug "line1\a line2"  // "line1\a line2" (foo and bar are separated by a newline)
@debug "Nat + Liz \1F46D"  // "Nat + Liz 👭"
```

> 💡 **Занятный факт**
>
> Для символов, которые разрешено использовать в строках, запись Unicode escape-последовательности даёт точно такую же строку, как и запись самого символа.

## Строки в кавычках

Строки в кавычках записываются между одинарными или двойными кавычками, например `"Helvetica Neue"`. Они могут содержать [интерполяцию][], а также любой неэкранированный символ, кроме:

[интерполяцию]: https://sass-lang.com/documentation/interpolation

* `\` — экранируется как `\\`;
* `'` или `"` — тот символ, которым определена данная строка, экранируется как `\'` или `\"`;
* переводов строк — экранируются как `\a ` (включая завершающий пробел).

Строки в кавычках гарантированно компилируются в CSS-строки с тем же содержимым, что и исходные строки Sass. Точный формат может отличаться в зависимости от реализации или конфигурации — строка, содержащая двойную кавычку, может быть скомпилирована как `"\""` или `'"'`, а не-[ASCII][]-символ может быть экранирован или нет. Но в любой соответствующей стандартам реализации CSS, включая все браузеры, это должно интерпретироваться одинаково.

[ASCII]: https://en.wikipedia.org/wiki/ASCII

**SCSS**

```scss
@debug "Helvetica Neue"; // "Helvetica Neue"
@debug "C:\\Program Files"; // "C:\\Program Files"
@debug "\"Don't Fear the Reaper\""; // "\"Don't Fear the Reaper\""
@debug "line1\a line2"; // "line1\a line2"

$roboto-variant: "Mono";
@debug "Roboto #{$roboto-variant}"; // "Roboto Mono"
```

**Sass**

```sass
@debug "Helvetica Neue"  // "Helvetica Neue"
@debug "C:\\Program Files"  // "C:\\Program Files"
@debug "\"Don't Fear the Reaper\""  // "\"Don't Fear the Reaper\""
@debug "line1\a line2"  // "line1\a line2"

$roboto-variant: "Mono"
@debug "Roboto #{$roboto-variant}"  // "Roboto Mono"
```

> 💡 **Занятный факт**
>
> Когда строка в кавычках вставляется в другое значение через интерполяцию, [её кавычки удаляются][]! Благодаря этому легко, например, писать строки, содержащие селекторы, которые можно вставлять в стилевые правила без добавления кавычек.
>
> [её кавычки удаляются]: https://sass-lang.com/documentation/interpolation#quoted-strings

## Строки без кавычек

Строки без кавычек записываются как CSS [идентификаторы][], согласно синтаксической диаграмме ниже. Они могут включать [интерполяцию][] в любом месте.

[идентификаторы]: https://drafts.csswg.org/css-syntax-3/#ident-token-diagram
[интерполяцию]: https://sass-lang.com/documentation/interpolation

<figure>
  <object type="image/svg+xml" data="/assets/img/illustrations/identifier-diagram.svg"></object>
  <figcaption class="copyright">
    Диаграмма «железной дороги» © 2018 W3C<sup>®</sup> (MIT, ERCIM, Keio, Beihang). Действуют правила W3C о <a href="http://www.w3.org/Consortium/Legal/ipr-notice#Legal_Disclaimer">материальной ответственности</a>, <a href="http://www.w3.org/Consortium/Legal/ipr-notice#W3C_Trademarks">товарных знаках</a> и <a href="http://www.w3.org/Consortium/Legal/2015/copyright-software-and-document">лицензии на документы со свободным доступом</a>.
  </figcaption>
</figure>

**SCSS**

```scss
@debug bold; // bold
@debug -webkit-flex; // -webkit-flex
@debug --123; // --123

$prefix: ms;
@debug -#{$prefix}-flex; // -ms-flex
```

**Sass**

```sass
@debug bold  // bold
@debug -webkit-flex  // -webkit-flex
@debug --123  // --123

$prefix: ms
@debug -#{$prefix}-flex  // -ms-flex
```

> ⚠️ **Внимание**
>
> Не все идентификаторы интерпретируются как строки без кавычек:
>
> * [названия цветов CSS][] интерпретируются как [цвета][];
> * `null` интерпретируется как [значение `null` в Sass][];
> * `true` и `false` интерпретируются как [логические значения][];
> * `not`, `and` и `or` интерпретируются как [логические операторы][].
>
> [названия цветов CSS]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#Color_keywords
> [цвета]: https://sass-lang.com/documentation/values/colors
> [значение `null` в Sass]: https://sass-lang.com/documentation/values/null
> [логические значения]: https://sass-lang.com/documentation/values/booleans
> [логические операторы]: https://sass-lang.com/documentation/operators/boolean
>
> Поэтому обычно лучше писать строки в кавычках, если вы не пишете именно значение CSS-свойства, которое использует строки без кавычек.

### Escape-последовательности в строках без кавычек

**Совместимость (нормализация):** Dart Sass: начиная с 1.11.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и старые версии Dart Sass не нормализуют escape-последовательности в идентификаторах. Вместо этого текст строки без кавычек — это именно тот текст, который написал пользователь. Например, `\1F46D` и `👭` не считаются эквивалентными.

При разборе строки без кавычек буквальный текст escape-последовательностей разбирается как часть строки. Например, `\a ` разбирается как символы `\`, `a` и пробел. Однако, чтобы гарантировать одинаковый разбор строк без кавычек, имеющих одинаковый смысл в CSS, эти escape-последовательности *нормализуются*. Для каждой кодовой точки, экранирована она или нет:

* Если это допустимый символ идентификатора, он включается в строку без кавычек без экранирования. Например, `\1F46D` возвращает строку без кавычек `👭`.

* Если это печатаемый символ, отличный от перевода строки или табуляции, он включается после `\`. Например, `\21 ` возвращает строку без кавычек `\!`.

* В противном случае включается escape-последовательность Unicode в нижнем регистре с завершающим пробелом. Например, `\7Fx` возвращает строку без кавычек `\7f x`.

**SCSS**

```scss
@use "sass:string";

@debug \1F46D; // 👭
@debug \21; // \!
@debug \7Fx; // \7f x
@debug string.length(\7Fx); // 5
```

**Sass**

```sass
@use "sass:string"

@debug \1F46D  // 👭
@debug \21  // \!
@debug \7Fx  // \7f x
@debug string.length(\7Fx)  // 5
```

### Другие строки без кавычек

Помимо идентификаторов, есть несколько необычных уголков синтаксиса CSS, которые интерпретируются как строки без кавычек. К ним относятся:

* [Специальные функции][] вроде `url()` и `element()`, аргументы которых имеют синтаксис, отличающийся от обычного синтаксиса выражений CSS.

* Токены [диапазона Unicode][] вроде `U+0-7F` или `U+4??`.

* [Хеш-токены][], значения которых являются идентификаторами, но не являются допустимыми hex-цветами.

* Значение `%`. (Оно доступно только тогда, когда не находится непосредственно между двумя другими значениями, иначе оно было бы неоднозначным с [оператором деления по модулю][].)

* Специальное значение `!important`.

[Специальные функции]: https://sass-lang.com/documentation/syntax/special-functions/
[диапазона Unicode]: https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/unicode-range
[Хеш-токены]: https://www.w3.org/TR/css-syntax-3/#ref-for-typedef-hash-token%E2%91%A0
[оператором деления по модулю]: https://sass-lang.com/documentation/operators/numeric/

**SCSS**

```scss
@debug url(https://example.org); // url(https://example.org)
@debug U+4??; // U+4??
@debug #my-background; // #my-background
@debug %; // %
@debug !important; // !important
```

**Sass**

```sass
@debug url(https://example.org)  // url(https://example.org)
@debug U+4??  // U+4??
@debug #my-background  // #my-background
@debug %  // %
@debug !important  // !important
```

## Индексы строк

В Sass есть ряд [функций для работы со строками][], которые принимают или возвращают числа, называемые *индексами*, указывающие на символы в строке. Индекс 1 обозначает первый символ строки. Обратите внимание: это отличается от многих языков программирования, где индексы начинаются с 0! Sass также позволяет легко ссылаться на конец строки. Индекс -1 обозначает последний символ строки, -2 — предпоследний, и так далее.

[функций для работы со строками]: https://sass-lang.com/documentation/modules/string

**SCSS**

```scss
@use "sass:string";

@debug string.index("Helvetica Neue", "Helvetica"); // 1
@debug string.index("Helvetica Neue", "Neue"); // 11
@debug string.slice("Roboto Mono", -4); // "Mono"
```

**Sass**

```sass
@use "sass:string"

@debug string.index("Helvetica Neue", "Helvetica")  // 1
@debug string.index("Helvetica Neue", "Neue")  // 11
@debug string.slice("Roboto Mono", -4)  // "Mono"
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/strings/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

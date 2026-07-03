# Операторы равенства


**Совместимость (равенство без учёта единиц измерения):** Dart Sass: ✓ · LibSass: ✗ · Ruby Sass: начиная с 4.0.0 (не выпущена)

LibSass и старые версии Ruby Sass считают числа без единиц измерения
  равными тем же числам с любыми единицами измерения. Это поведение было признано устаревшим и
  удалено в более новых релизах, поскольку оно нарушает [транзитивность][].

  [транзитивность]: https://en.wikipedia.org/wiki/Transitive_relation


Операторы равенства возвращают, одинаковы ли два значения. Они записываются как
`<expression> == <expression>` — возвращает, равны ли два
[выражения][], и `<expression> != <expression>` — возвращает,
*не* равны ли два выражения. Два значения считаются равными, если они
одного типа *и* имеют одинаковое значение, что означает разное для
разных типов:

[выражения]: https://sass-lang.com/documentation/syntax/structure#expressions

* [Числа][] равны, если у них одинаковое значение *и* одинаковые единицы измерения, либо если
  их значения равны при преобразовании единиц измерения друг в друга.
* [Строки][] необычны тем, что [без кавычек][] и [в кавычках][] строки с
  одинаковым содержимым считаются равными.
* [Цвета] равны, если они находятся в одном [цветовом пространстве] и имеют одинаковые
  значения каналов, *или* если оба находятся в [устаревших цветовых пространствах] и имеют
  одинаковые значения каналов RGBA.
* [Списки][] равны, если равно их содержимое. Списки через запятую не
  равны спискам через пробел, а списки в скобках не равны
  спискам без скобок.
* [Карты (map)][] равны, если равны и их ключи, и их значения.
* [Вычисления] равны, если равны их имена и все аргументы. Аргументы операций
  сравниваются текстуально.
* [`true`, `false`][] и [`null`][] равны только самим себе.
* [Функции][] равны, если это одна и та же функция. Функции сравниваются *по
  ссылке*, поэтому даже если у двух функций одинаковое имя и определение, они
  считаются разными, если определены не в одном и том же месте.

[Числа]: https://sass-lang.com/documentation/values/numbers
[Строки]: https://sass-lang.com/documentation/values/strings
[в кавычках]: https://sass-lang.com/documentation/values/strings#quoted
[без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted
[Цвета]: https://sass-lang.com/documentation/values/colors
[цветовом пространстве]: https://sass-lang.com/documentation/values/colors#color-spaces
[устаревших цветовых пространствах]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces
[Списки]: https://sass-lang.com/documentation/values/lists
[`true`, `false`]: https://sass-lang.com/documentation/values/booleans
[`null`]: https://sass-lang.com/documentation/values/null
[Карты (map)]: https://sass-lang.com/documentation/values/maps
[Вычисления]: https://sass-lang.com/documentation/values/calculations
[Функции]: https://sass-lang.com/documentation/values/functions

**SCSS**

```scss
@debug 1px == 1px; // true
@debug 1px != 1em; // true
@debug 1 != 1px; // true
@debug 96px == 1in; // true

@debug "Helvetica" == Helvetica; // true
@debug "Helvetica" != "Arial"; // true

@debug hsl(34, 35%, 92.1%) == #f2ece4; // true
@debug rgba(179, 115, 153, 0.5) != rgba(179, 115, 153, 0.8); // true

@debug (5px 7px 10px) == (5px 7px 10px); // true
@debug (5px 7px 10px) != (10px 14px 20px); // true
@debug (5px 7px 10px) != (5px, 7px, 10px); // true
@debug (5px 7px 10px) != [5px 7px 10px]; // true

$theme: ("venus": #998099, "nebula": #d2e1dd);
@debug $theme == ("venus": #998099, "nebula": #d2e1dd); // true
@debug $theme != ("venus": #998099, "iron": #dadbdf); // true

@debug true == true; // true
@debug true != false; // true
@debug null != false; // true

@debug get-function("rgba") == get-function("rgba"); // true
@debug get-function("rgba") != get-function("hsla"); // true
```

**Sass**

```sass
@debug 1px == 1px  // true
@debug 1px != 1em  // true
@debug 1 != 1px  // true
@debug 96px == 1in  // true

@debug "Helvetica" == Helvetica  // true
@debug "Helvetica" != "Arial"  // true

@debug hsl(34, 35%, 92.1%) == #f2ece4  // true
@debug rgba(179, 115, 153, 0.5) != rgba(179, 115, 153, 0.8)  // true

@debug (5px 7px 10px) == (5px 7px 10px)  // true
@debug (5px 7px 10px) != (10px 14px 20px)  // true
@debug (5px 7px 10px) != (5px, 7px, 10px)  // true
@debug (5px 7px 10px) != [5px 7px 10px]  // true

$theme: ("venus": #998099, "nebula": #d2e1dd)
@debug $theme == ("venus": #998099, "nebula": #d2e1dd)  // true
@debug $theme != ("venus": #998099, "iron": #dadbdf)  // true

@debug calc(10px + 10%) == calc(10px + 10%)  // true
@debug calc(10% + 10px) == calc(10px + 10%)  // false

@debug true == true  // true
@debug true != false  // true
@debug null != false  // true

@debug get-function("rgba") == get-function("rgba")  // true
@debug get-function("rgba") != get-function("hsla")  // true
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/equality/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

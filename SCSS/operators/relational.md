# Операторы отношения

Операторы отношения определяют, являются ли
[числа](https://sass-lang.com/documentation/values/numbers) больше или меньше друг
друга. Они автоматически преобразуют между совместимыми единицами измерения.

* `<expression> < <expression>` возвращает, меньше ли значение первого [выражения][]
  значения второго.
* `<expression> <= <expression>` возвращает, меньше ли или равно
  значение первого [выражения][] значению второго.
* `<expression> > <expression>` возвращает, больше ли значение первого [выражения][]
  значения второго.
* `<expression> >= <expression>` возвращает, больше ли или равно
  значение первого [выражения][] значению второго.

[выражения]: https://sass-lang.com/documentation/syntax/structure#expressions

**SCSS**

```scss
@debug 100 > 50; // true
@debug 10px < 17px; // true
@debug 96px >= 1in; // true
@debug 1000ms <= 1s; // true
```

**Sass**

```sass
@debug 100 > 50  // true
@debug 10px < 17px  // true
@debug 96px >= 1in  // true
@debug 1000ms <= 1s  // true
```

Числа без единиц измерения можно сравнивать с любым числом. Они автоматически
преобразуются в единицу измерения этого числа.

**SCSS**

```scss
@debug 100 > 50px; // true
@debug 10px < 17; // true
```

**Sass**

```sass
@debug 100 > 50px  // true
@debug 10px < 17  // true
```

Числа с несовместимыми единицами измерения нельзя сравнивать.

**SCSS**

```scss
@debug 100px > 10s;
//     ^^^^^^^^^^^
// Error: Incompatible units px and s.
```

**Sass**

```sass
@debug 100px > 10s
//     ^^^^^^^^^^^
// Error: Incompatible units px and s.
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/relational/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

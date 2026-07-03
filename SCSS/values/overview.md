# Значения

Sass поддерживает ряд типов значений, большинство из которых пришли прямо из CSS.
Каждое [выражение](https://sass-lang.com/documentation/syntax/structure#expressions) порождает
значение, [переменные](https://sass-lang.com/documentation/variables) хранят значения. Большинство типов значений
пришли прямо из CSS:

* [Числа](https://sass-lang.com/documentation/values/numbers), которые могут иметь единицы измерения, а могут и не иметь,
  например `12` или `100px`.

* [Строки](https://sass-lang.com/documentation/values/strings), которые могут быть в кавычках, а могут быть без,
  например `"Helvetica Neue"` или `bold`.

* [Цвета](https://sass-lang.com/documentation/values/colors), на которые можно ссылаться по их шестнадцатеричному
  представлению или по имени, например `#c6538c` или `blue`, либо возвращаемые
  функциями, например `rgb(107, 113, 127)` или `hsl(210, 100%, 20%)`.

* [Списки значений](https://sass-lang.com/documentation/values/lists), которые могут разделяться
  пробелами или запятыми и могут быть заключены в квадратные скобки либо вообще без скобок,
  например `1.5em 1em 0 2em`, `Helvetica, Arial, sans-serif` или
  `[col1-start]`.

Ещё несколько типов специфичны для Sass:

* [Логические](https://sass-lang.com/documentation/values/booleans) значения `true` и `false`.

* Единственное значение [`null`](https://sass-lang.com/documentation/values/null).

* [Карты](https://sass-lang.com/documentation/values/maps), связывающие значения с ключами, например
  `("background": red, "foreground": pink)`.

* [Ссылки на функции](https://sass-lang.com/documentation/values/functions), возвращаемые
  функцией [`get-function()`][] и вызываемые с помощью [`call()`][].

  [`get-function()`]: https://sass-lang.com/documentation/modules/meta#get-function
  [`call()`]: https://sass-lang.com/documentation/modules/meta#call

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

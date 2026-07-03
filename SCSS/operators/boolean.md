# Логические операторы

В отличие от таких языков, как JavaScript, Sass использует слова, а не символы, для своих
[логических](https://sass-lang.com/documentation/values/booleans) операторов.

* `not <expression>` возвращает противоположное значение выражения: превращает
  `true` в `false`, а `false` в `true`.
* `<expression> and <expression>` возвращает `true`, если значения *обоих* выражений
  равны `true`, и `false`, если хотя бы одно из них равно `false`.
* `<expression> or <expression>` возвращает `true`, если значение *хотя бы одного* выражения
  равно `true`, и `false`, если оба равны `false`.

**SCSS**

```scss
@debug not true; // false
@debug not false; // true

@debug true and true; // true
@debug true and false; // false

@debug true or false; // true
@debug false or false; // false
```

**Sass**

```sass
@debug not true  // false
@debug not false  // true

@debug true and true  // true
@debug true and false  // false

@debug true or false  // true
@debug false or false  // false
```

## Истинность и ложность

Везде, где допустимы `true` или `false`, можно использовать и другие значения. Значения
`false` и [`null`][] считаются *ложными* (falsey) — это означает, что Sass рассматривает их как
признак ложности и заставляет условия завершаться неудачей. Любое другое значение считается
*истинным* (truthy), поэтому Sass рассматривает их как работающие подобно `true` и заставляет условия
выполняться успешно.

[`null`]: https://sass-lang.com/documentation/values/null

Например, если вы хотите проверить, содержит ли строка пробел, можно просто
написать `string.index($string, " ")`. [Функция `string.index()`][] возвращает
`null`, если строка не найдена, и число в противном случае.

[Функция `string.index()`]: https://sass-lang.com/documentation/modules/string#index

> ⚠️ **Внимание**
>
> Некоторые языки считают ложными больше значений, чем просто `false` и `null`. Sass —
> не один из таких языков! Пустые строки, пустые списки и число `0` —
> все они истинны (truthy) в Sass.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/boolean/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

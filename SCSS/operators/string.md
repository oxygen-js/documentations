# Строковые операторы

Sass поддерживает несколько операторов, которые создают
[строки](https://sass-lang.com/documentation/values/strings):

* `<expression> + <expression>` возвращает строку, содержащую значения обоих выражений.
  Если хотя бы одно из значений — [строка в кавычках][], результат будет в кавычках;
  в противном случае он будет без кавычек.

* `<expression> - <expression>` возвращает строку без кавычек, содержащую значения обоих
  выражений, разделённые `-`. Это устаревший оператор, и вместо него, как правило,
  следует использовать [интерполяцию][].

[строка в кавычках]: https://sass-lang.com/documentation/values/strings#quoted
[интерполяцию]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
@debug "Helvetica" + " Neue"; // "Helvetica Neue"
@debug sans- + serif; // sans-serif
@debug sans - serif; // sans-serif
```

**Sass**

```sass
@debug "Helvetica" + " Neue"  // "Helvetica Neue"
@debug sans- + serif  // sans-serif
@debug sans - serif  // sans-serif
```

Эти операторы работают не только со строками! Их можно использовать с любыми значениями,
которые можно записать в CSS, за небольшими исключениями:

* Числа нельзя использовать как значение слева, поскольку у них есть [собственные
  операторы][numeric].
* Цвета нельзя использовать как значение слева, поскольку раньше у них были [собственные
  операторы][color].

[numeric]: https://sass-lang.com/documentation/operators/numeric
[color]: https://sass-lang.com/documentation/operators

**SCSS**

```scss
@debug "Elapsed time: " + 10s; // "Elapsed time: 10s";
@debug true + " is a boolean value"; // "true is a boolean value";
```

**Sass**

```sass
@debug "Elapsed time: " + 10s  // "Elapsed time: 10s";
@debug true + " is a boolean value"  // "true is a boolean value";
```

> 💡 **Занятный факт**
>
> Часто чище и понятнее использовать [интерполяцию][] для создания строк,
> вместо того чтобы полагаться на эти операторы.
>
> [интерполяцию]: https://sass-lang.com/documentation/interpolation

## Унарные операторы

По историческим причинам Sass также поддерживает `/` и `-` в качестве унарных операторов,
которые принимают только одно значение:

* `/<expression>` возвращает строку без кавычек, начинающуюся с `/`, за которой следует
  значение выражения.
* `-<expression>` возвращает строку без кавычек, начинающуюся с `-`, за которой следует
  значение выражения.

**SCSS**

```scss
@debug / 15px; // /15px
@debug - moz; // -moz
```

**Sass**

```sass
@debug / 15px  // /15px
@debug - moz  // -moz
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/string/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# @debug

Иногда бывает полезно увидеть значение
[переменной](https://sass-lang.com/documentation/variables) или
[выражения](https://sass-lang.com/documentation/syntax/structure#expressions) во время
разработки таблицы стилей. Именно для этого нужно правило `@debug`: оно записывается
как `@debug <expression>` и выводит значение этого выражения вместе с именем файла и
номером строки.

**SCSS**

```scss
@mixin inset-divider-offset($offset, $padding) {
  $divider-offset: (2 * $padding) + $offset;
  @debug "divider offset: #{$divider-offset}";

  margin-left: $divider-offset;
  width: calc(100% - #{$divider-offset});
}
```

**Sass**

```sass
@mixin inset-divider-offset($offset, $padding)
  $divider-offset: (2 * $padding) + $offset
  @debug "divider offset: #{$divider-offset}"

  margin-left: $divider-offset
  width: calc(100% - #{$divider-offset})
```

Точный формат отладочного сообщения зависит от конкретной реализации. Вот как это
выглядит в Dart Sass:

```
test.scss:3 Debug: divider offset: 132px
```

> 💡 **Занятный факт**
>
> В `@debug` можно передать любое значение, а не только строку! Он выводит то же
> представление этого значения, что и [функция `meta.inspect()`][].

[функция `meta.inspect()`]: https://sass-lang.com/documentation/modules/meta#inspect

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/debug/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

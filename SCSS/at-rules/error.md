# @error

При написании [миксинов](https://sass-lang.com/documentation/at-rules/mixin) и
[функций](https://sass-lang.com/documentation/at-rules/function), принимающих
аргументы, обычно требуется убедиться, что эти аргументы имеют типы и форматы,
ожидаемые вашим API. Если это не так, пользователя нужно уведомить, а выполнение
миксина/функции — остановить.

Sass упрощает эту задачу с помощью правила `@error`, которое записывается как
`@error <expression>`. Оно выводит значение [выражения][] (обычно строки) вместе
с трассировкой стека, показывающей, как был вызван текущий миксин или функция.
После вывода ошибки Sass прекращает компиляцию таблицы стилей и сообщает системе,
запустившей компиляцию, что произошла ошибка.

[выражения]: https://sass-lang.com/documentation/syntax/structure#expressions

**SCSS**

```scss
@mixin reflexive-position($property, $value) {
  @if $property != left and $property != right {
    @error "Property #{$property} must be either left or right.";
  }

  $left-value: if(sass($property == right): initial; else: $value);
  $right-value: if(sass($property == right): $value; else: initial);

  left: $left-value;
  right: $right-value;
  [dir=rtl] & {
    left: $right-value;
    right: $left-value;
  }
}

.sidebar {
  @include reflexive-position(top, 12px);
  //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  // Error: Property top must be either left or right.
}
```

**Sass**

```sass
@mixin reflexive-position($property, $value)
  @if $property != left and $property != right
    @error "Property #{$property} must be either left or right."


  $left-value: if(sass($property == right): initial; else: $value)
  $right-value: if(sass($property == right): $value; else: initial)

  left: $left-value
  right: $right-value
  [dir=rtl] &
    left: $right-value
    right: $left-value



.sidebar
  @include reflexive-position(top, 12px)
  //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  // Error: Property top must be either left or right.
```

Точный формат ошибки и трассировки стека зависит от конкретной реализации, а также
может зависеть от вашей системы сборки. Вот как это выглядит в Dart Sass при запуске
из командной строки:

```
Error: "Property top must be either left or right."
  ╷
3 │     @error "Property #{$property} must be either left or right.";
  │     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  ╵
  example.scss 3:5   reflexive-position()
  example.scss 19:3  root stylesheet
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/error/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

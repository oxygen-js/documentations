# Селекторы-заглушки

В Sass есть особый вид селектора, известный как «заглушка» (placeholder). Он
выглядит и ведёт себя во многом как селектор класса, но начинается с `%` и не
включается в результирующий CSS. Более того, любой сложный селектор (те, что
между запятыми), который *содержит* селектор-заглушку, не включается в CSS, как
и любое стилевое правило, все селекторы которого содержат заглушки.

**SCSS**

```scss
.alert:hover, %strong-alert {
  font-weight: bold;
}

%strong-alert:hover {
  color: red;
}
```

**Sass**

```sass
.alert:hover, %strong-alert
  font-weight: bold


%strong-alert:hover
  color: red
```

**Результат CSS**

```css
.alert:hover {
  font-weight: bold;
}
```


В чём польза селектора, который не выводится в результат? Его всё ещё можно
[расширить][extended]! В отличие от селекторов класса, заглушки не засоряют
CSS, если их не расширяют, и не обязывают пользователей библиотеки применять
конкретные имена классов в своём HTML.

[extended]: https://sass-lang.com/documentation/at-rules/extend

**SCSS**

```scss
%toolbelt {
  box-sizing: border-box;
  border-top: 1px rgba(#000, .12) solid;
  padding: 16px 0;
  width: 100%;

  &:hover { border: 2px rgba(#000, .5) solid; }
}

.action-buttons {
  @extend %toolbelt;
  color: #4285f4;
}

.reset-buttons {
  @extend %toolbelt;
  color: #cddc39;
}
```

**Sass**

```sass
%toolbelt
  box-sizing: border-box
  border-top: 1px rgba(#000, .12) solid
  padding: 16px 0
  width: 100%

  &:hover
    border: 2px rgba(#000, .5) solid

.action-buttons
  @extend %toolbelt
  color: #4285f4


.reset-buttons
  @extend %toolbelt
  color: #cddc39
```

**Результат CSS**

```css
.reset-buttons, .action-buttons {
  box-sizing: border-box;
  border-top: 1px rgba(0, 0, 0, 0.12) solid;
  padding: 16px 0;
  width: 100%;
}
.reset-buttons:hover, .action-buttons:hover {
  border: 2px rgba(0, 0, 0, 0.5) solid;
}

.action-buttons {
  color: #4285f4;
}

.reset-buttons {
  color: #cddc39;
}
```

Селекторы-заглушки полезны при написании библиотеки Sass, где каждое стилевое
правило может использоваться, а может и нет. Как правило, если вы пишете
таблицу стилей только для своего собственного приложения, часто лучше просто
расширить селектор класса, если он есть.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/style-rules/placeholder-selectors/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# @each

Правило `@each` позволяет легко выводить стили или выполнять код для каждого элемента
[списка](https://sass-lang.com/documentation/values/lists) или каждой пары в
[карте](https://sass-lang.com/documentation/values/maps) (map). Оно отлично подходит для
повторяющихся стилей, которые лишь немного отличаются друг от друга. Обычно оно
записывается как `@each <variable> in <expression> { ... }`, где
[выражение](https://sass-lang.com/documentation/syntax/structure#expressions) возвращает
список. Блок выполняется поочерёдно для каждого элемента списка, который присваивается
указанному имени переменной.

**SCSS**

```scss
$sizes: 40px, 50px, 80px;

@each $size in $sizes {
  .icon-#{$size} {
    font-size: $size;
    height: $size;
    width: $size;
  }
}
```

**Sass**

```sass
$sizes: 40px, 50px, 80px

@each $size in $sizes
  .icon-#{$size}
    font-size: $size
    height: $size
    width: $size
```

**CSS output**

```css
.icon-40px {
  font-size: 40px;
  height: 40px;
  width: 40px;
}

.icon-50px {
  font-size: 50px;
  height: 50px;
  width: 50px;
}

.icon-80px {
  font-size: 80px;
  height: 80px;
  width: 80px;
}
```


## С картами

Также можно использовать `@each` для перебора всех пар ключ/значение в карте,
записав это как `@each <variable>, <variable> in <expression> { ... }`. Ключ
присваивается первому имени переменной, а элемент — второму.

**SCSS**

```scss
$icons: ("eye": "\f112", "start": "\f12e", "stop": "\f12f");

@each $name, $glyph in $icons {
  .icon-#{$name}:before {
    display: inline-block;
    font-family: "Icon Font";
    content: $glyph;
  }
}
```

**Sass**

```sass
$icons: ("eye": "\f112", "start": "\f12e", "stop": "\f12f")

@each $name, $glyph in $icons
  .icon-#{$name}:before
    display: inline-block
    font-family: "Icon Font"
    content: $glyph
```

**CSS output**

```css
.icon-eye:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f112";
}

.icon-start:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12e";
}

.icon-stop:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12f";
}
```


## Деструктуризация

Если у вас есть список списков, вы можете использовать `@each` для автоматического
присваивания переменным значений из вложенных списков, записав это как `@each
<variable...> in <expression> { ... }`. Это называется *деструктуризацией*, поскольку
переменные соответствуют структуре вложенных списков. Каждому имени переменной
присваивается значение на соответствующей позиции в списке, либо [`null`][], если
в списке недостаточно значений.

[`null`]: https://sass-lang.com/documentation/values/null

**SCSS**

```scss
$icons:
  "eye" "\f112" 12px,
  "start" "\f12e" 16px,
  "stop" "\f12f" 10px;

@each $name, $glyph, $size in $icons {
  .icon-#{$name}:before {
    display: inline-block;
    font-family: "Icon Font";
    content: $glyph;
    font-size: $size;
  }
}
```

**Sass**

```sass
$icons: "eye" "\f112" 12px, "start" "\f12e" 16px, "stop" "\f12f" 10px




@each $name, $glyph, $size in $icons
  .icon-#{$name}:before
    display: inline-block
    font-family: "Icon Font"
    content: $glyph
    font-size: $size
```

**CSS output**

```css
.icon-eye:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f112";
  font-size: 12px;
}

.icon-start:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12e";
  font-size: 16px;
}

.icon-stop:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12f";
  font-size: 10px;
}
```

> 💡 **Занятный факт**
>
> Поскольку `@each` поддерживает деструктуризацию, а [карты считаются списками
> списков][], поддержка карт в `@each` работает без необходимости в какой-либо
> особой поддержке карт как таковых.

[карты считаются списками списков]: https://sass-lang.com/documentation/values/maps

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/control/each/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

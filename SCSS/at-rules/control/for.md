# @for

Правило `@for`, записываемое как `@for <variable> from <expression> to <expression> {
... }` или `@for <variable> from <expression> through <expression> { ... }`, отсчитывает
вверх или вниз от одного числа (результата первого
[выражения](https://sass-lang.com/documentation/syntax/structure#expressions)) до другого
(результата второго) и выполняет блок для каждого числа между ними. Каждое число по
ходу отсчёта присваивается указанному имени переменной. Если используется `to`,
конечное число исключается; если используется `through` — включается.

**SCSS**

```scss
$base-color: #036;

@for $i from 1 through 3 {
  ul:nth-child(3n + #{$i}) {
    background-color: lighten($base-color, $i * 5%);
  }
}
```

**Sass**

```sass
$base-color: #036

@for $i from 1 through 3
  ul:nth-child(3n + #{$i})
    background-color: lighten($base-color, $i * 5%)
```

**CSS output**

```css
ul:nth-child(3n+1) {
  background-color: rgb(0, 63.75, 127.5);
}

ul:nth-child(3n+2) {
  background-color: rgb(0, 76.5, 153);
}

ul:nth-child(3n+3) {
  background-color: rgb(0, 89.25, 178.5);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/control/for/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

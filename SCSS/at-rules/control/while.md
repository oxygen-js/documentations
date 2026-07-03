# @while

Правило `@while`, записываемое как `@while <expression> { ... }`, выполняет свой блок,
если его [выражение](https://sass-lang.com/documentation/syntax/structure#expressions)
возвращает `true`. Затем, если выражение всё ещё возвращает `true`, блок выполняется
снова. Это продолжается до тех пор, пока выражение наконец не вернёт `false`.

**SCSS**

```scss
@use "sass:math";

/// Divides `$value` by `$ratio` until it's below `$base`.
@function scale-below($value, $base, $ratio: 1.618) {
  @while $value > $base {
    $value: math.div($value, $ratio);
  }
  @return $value;
}

$normal-font-size: 16px;
sup {
  font-size: scale-below(20px, 16px);
}
```

**Sass**

```sass
@use "sass:math"

/// Divides `$value` by `$ratio` until it's below `$base`.
@function scale-below($value, $base, $ratio: 1.618)
  @while $value > $base
    $value: math.div($value, $ratio)
  @return $value



$normal-font-size: 16px
sup
  font-size: scale-below(20px, 16px)
```

**CSS output**

```css
sup {
  font-size: 12.3609394314px;
}
```

> ⚠️ **Внимание**
>
> Хотя `@while` необходим для нескольких особенно сложных таблиц стилей, обычно
> лучше использовать [`@each`][] или [`@for`][], если один из них подходит для
> задачи. Они понятнее для читателя, а также зачастую компилируются быстрее.

[`@each`]: https://sass-lang.com/documentation/at-rules/control/each
[`@for`]: https://sass-lang.com/documentation/at-rules/control/for

## Истинность и ложность

Везде, где допустимы значения `true` или `false`, можно использовать и другие
значения. Значения `false` и [`null`][] являются *ложными* (falsey) — это означает,
что Sass считает их признаком ложности и они приводят к невыполнению условий. Любое
другое значение считается *истинным* (truthy), поэтому Sass рассматривает такие
значения как аналог `true`, и они приводят к выполнению условий.

[`null`]: https://sass-lang.com/documentation/values/null

Например, если вы хотите проверить, содержит ли строка пробел, можно просто написать
`string.index($string, " ")`. [Функция `string.index()`][] возвращает `null`, если
строка не найдена, и число в противном случае.

[Функция `string.index()`]: https://sass-lang.com/documentation/modules/string#index

> ⚠️ **Внимание**
>
> В некоторых языках ложными считаются и другие значения, помимо `false` и `null`.
> Sass — не один из таких языков! Пустые строки, пустые списки и число `0` — все
> они истинны в Sass.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/control/while/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

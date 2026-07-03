# Логические значения

Логические значения — это `true` и `false`. Помимо своей литеральной формы,
логические значения возвращаются операторами [равенства](https://sass-lang.com/documentation/operators/equality)
и [сравнения](https://sass-lang.com/documentation/operators/relational), а также многими встроенными функциями, такими как
[`math.comparable()`](https://sass-lang.com/documentation/modules/math#comparable) и
[`map.has-key()`](https://sass-lang.com/documentation/modules/map#has-key).

**SCSS**

```scss
@use "sass:math";

@debug 1px == 2px; // false
@debug 1px == 1px; // true
@debug 10px < 3px; // false
@debug math.comparable(100px, 3in); // true
```

**Sass**

```sass
@use "sass:math"

@debug 1px == 2px  // false
@debug 1px == 1px  // true
@debug 10px < 3px  // false
@debug math.comparable(100px, 3in)  // true
```

С логическими значениями можно работать с помощью [логических операторов][].
Оператор `and` возвращает `true`, если *обе* стороны равны `true`, а оператор
`or` возвращает `true`, если *хотя бы одна* сторона равна `true`. Оператор
`not` возвращает значение, противоположное одному логическому значению.

[логических операторов]: https://sass-lang.com/documentation/operators/boolean

**SCSS**

```scss
@debug true and true; // true
@debug true and false; // false

@debug true or false; // true
@debug false or false; // false

@debug not true; // false
@debug not false; // true
```

**Sass**

```sass
@debug true and true  // true
@debug true and false  // false

@debug true or false  // true
@debug false or false  // false

@debug not true  // false
@debug not false  // true
```

## Использование логических значений

Логические значения можно использовать, чтобы выбирать, выполнять ли в Sass
те или иные действия. [Правило `@if`][] вычисляет блок стилей, если его
аргумент — `true`:

[Правило `@if`]: https://sass-lang.com/documentation/at-rules/control/if

**SCSS**

```scss
@use "sass:math";

@mixin avatar($size, $circle: false) {
  width: $size;
  height: $size;

  @if $circle {
    border-radius: math.div($size, 2);
  }
}

.square-av {
  @include avatar(100px, $circle: false);
}
.circle-av {
  @include avatar(100px, $circle: true);
}
```

**Sass**

```sass
@use "sass:math"

@mixin avatar($size, $circle: false)
  width: $size
  height: $size

  @if $circle
    border-radius: math.div($size, 2)



.square-av
  @include avatar(100px, $circle: false)

.circle-av
  @include avatar(100px, $circle: true)
```

**Результат CSS**

```css
.square-av {
  width: 100px;
  height: 100px;
}

.circle-av {
  width: 100px;
  height: 100px;
  border-radius: 50px;
}
```


[Функция `if()`] возвращает одно значение, если её аргумент — `true`, и
другое, если её аргумент — `false`:

[Функция `if()`]: https://sass-lang.com/documentation/syntax/special-functions#if

**SCSS**

```scss
@debug if(true: 10px; else: 30px); // 10px
@debug if(false: 10px; else: 30px); // 30px
```

**Sass**

```sass
@debug if(true: 10px; else: 30px)  // 10px
@debug if(false: 10px; else: 30px)  // 30px
```

## Истинность и ложность

Везде, где допустимы `true` или `false`, можно использовать и другие
значения. Значения `false` и [`null`][] являются *ложными* (falsey) — это
означает, что Sass считает их признаком лжи и заставляет условия
завершаться неудачей. Любое другое значение считается *истинным* (truthy),
поэтому Sass считает, что оно работает как `true`, и условия с ним
выполняются успешно.

[`null`]: https://sass-lang.com/documentation/values/null

Например, если вы хотите проверить, содержит ли строка пробел, можно просто
написать `string.index($string, " ")`. [Функция `string.index()`][]
возвращает `null`, если строка не найдена, и число — в противном случае.

[Функция `string.index()`]: https://sass-lang.com/documentation/modules/string#index

> ⚠️ **Внимание**
>
> В некоторых языках ложными считаются больше значений, чем просто `false` и
> `null`. Sass — не из таких языков! Пустые строки, пустые списки и число `0`
> в Sass являются истинными (truthy).

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/booleans/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

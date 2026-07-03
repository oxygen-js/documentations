# Вычисления

Вычисления — это то, как Sass представляет функцию `calc()`, а также похожие
функции, такие как `clamp()`, `min()` и `max()`. Sass максимально упрощает их,
даже если они скомбинированы друг с другом.

**Совместимость:** Dart Sass: начиная с 1.40.0 · LibSass: ✗ · Ruby Sass: ✗

  LibSass, Ruby Sass и версии Dart Sass до 1.40.0 разбирают `calc()`
  как [специальную функцию], аналогично `element()`.

  [специальную функцию]: https://sass-lang.com/documentation/syntax/special-functions#element-progid-and-expression

  LibSass, Ruby Sass и версии Dart Sass до 1.31.0 разбирают `clamp()`
  как [обычную CSS-функцию], а не поддерживают внутри неё специальный синтаксис.
  Версии Dart Sass между 1.31.0 и 1.40.0 разбирают `clamp()` как [специальную
  функцию], аналогично `element()`.

  [обычную CSS-функцию]: https://sass-lang.com/documentation/at-rules/function/#plain-css-functions


**Совместимость (смежные значения):** Dart Sass: начиная с 1.67.0 · LibSass: ✗ · Ruby Sass: ✗

Версии Dart Sass между 1.40.0 и 1.67.0 не допускают несколько значений в
  вычислениях, не разделённых оператором, даже в таких случаях, как `calc(1
  var(--plus-two))`, что является допустимым CSS (поскольку `--plus-two` можно
  определить как `+ 2`).

  Начиная с Dart Sass 1.67.0 несколько значений в вычислении можно разделять
  пробелами, если каждое второе значение является строкой без кавычек
  (например, выражением `var()` или строкой без кавычек `"+ 2"`).


**SCSS**

```scss
@debug calc(400px + 10%); // calc(400px + 10%)
@debug calc(400px / 2); // 200px
@debug min(100px, calc(1rem + 10%)); // min(100px, 1rem + 10%)
```

**Sass**

```sass
@debug calc(400px + 10%)  // calc(400px + 10%)
@debug calc(400px / 2)  // 200px
@debug min(100px, calc(1rem + 10%)) ; // min(100px, 1rem + 10%)
```

Вычисления используют специальный синтаксис, отличающийся от обычного
SassScript. Это тот же синтаксис, что и у CSS `calc()`, но с дополнительной
возможностью использовать [переменные Sass] и вызывать [функции Sass]. Это
значит, что `/` внутри вычисления всегда является оператором деления!

[переменные Sass]: https://sass-lang.com/documentation/variables
[функции Sass]: https://sass-lang.com/documentation/modules

> 💡 **Занятный факт**
>
> Аргументы вызова функции Sass используют обычный синтаксис Sass, а не
> специальный синтаксис вычислений!

Внутри вычисления также можно использовать [интерполяцию]. Однако если вы это
сделаете, операции, использующие эту интерполяцию, не будут упрощены или
проверены на тип, поэтому легко получить излишне многословный или даже
недопустимый CSS. Вместо того чтобы писать `calc(10px + #{$var})`, просто
напишите `calc(10px + $var)`!

[интерполяцию]: https://sass-lang.com/documentation/interpolation

## Упрощение

Sass упрощает соседние операции в вычислениях, если их единицы измерения
можно объединить на этапе компиляции, например `1in + 10px` или `5s * 2`. Если
возможно, он даже упростит всё вычисление до одного числа — например,
`clamp(0px, 30px, 20px)` вернёт `20px`.

> ⚠️ **Внимание**
>
> Это значит, что выражение вычисления не обязательно всегда будет возвращать
> вычисление! Если вы пишете библиотеку Sass, вы всегда можете использовать
> функцию [`meta.type-of()`], чтобы определить, с каким типом вы имеете дело.
>
> [`meta.type-of()`]: https://sass-lang.com/documentation/modules/meta#type-of

Вычисления также упрощаются внутри других вычислений. В частности, если
`calc()` оказывается внутри другого вычисления, вызов функции будет удалён и
заменён обычной операцией.

**SCSS**

```scss
$width: calc(400px + 10%);

.sidebar {
  width: $width;
  padding-left: calc($width / 4);
}
```

**Sass**

```sass
$width: calc(400px + 10%)

.sidebar
  width: $width
  padding-left: calc($width / 4)
```

**Результат CSS**

```css
.sidebar {
  width: calc(400px + 10%);
  padding-left: calc((400px + 10%) / 4);
}
```

## Операции

Вы не можете использовать вычисления с обычными операциями SassScript, такими
как `+` и `*`. Если вы хотите написать математические функции, допускающие
вычисления, просто пишите их внутри собственных выражений `calc()` — если им
передать набор чисел с совместимыми единицами измерения, они также вернут
обычные числа, а если им передать вычисления, они вернут вычисления.

Это ограничение введено для того, чтобы, если вычисления *не* нужны, они как
можно раньше вызывали ошибку. Вычисления нельзя использовать везде, где можно
обычные числа: например, их нельзя вставить в CSS-идентификаторы (такие как
`.item-#{$n}`), и их нельзя передавать во встроенные [математические функции]
Sass. То, что операции SassScript зарезервированы для обычных чисел, чётко
показывает, где вычисления допустимы, а где нет.

[математические функции]: https://sass-lang.com/documentation/modules/math

**SCSS**

```scss
$width: calc(100% + 10px);
@debug $width * 2; // Error!
@debug calc($width * 2); // calc((100% + 10px) * 2);
```

**Sass**

```sass
$width: calc(100% + 10px);
@debug $width * 2; // Error!
@debug calc($width * 2); // calc((100% + 10px) * 2);
```

## Константы

**Совместимость:** Dart Sass: начиная с 1.60.0 · LibSass: ✗ · Ruby Sass: ✗


Вычисления также могут содержать константы, которые записываются как
CSS-идентификаторы. Для совместимости с будущими спецификациями CSS разрешены
*любые* идентификаторы, и по умолчанию они просто трактуются как строки без
кавычек, которые передаются как есть.

**SCSS**

```scss
@debug calc(h + 30deg); // calc(h + 30deg);
```

**Sass**

```sass
@debug calc(h + 30deg)  // calc(h + 30deg);
```

Sass автоматически преобразует несколько специальных имён констант,
определённых в CSS, в числа без единиц измерения:

* `pi` — сокращённая запись для [математической константы *π*].

  [математической константы *π*]: https://en.wikipedia.org/wiki/Pi

* `e` — сокращённая запись для [математической константы *e*].

  [математической константы *e*]: https://en.wikipedia.org/wiki/E_(mathematical_constant)

* `infinity`, `-infinity` и `NaN` представляют соответствующие значения с
  плавающей точкой.

**SCSS**

```scss
@use 'sass:math';

@debug calc(pi); // 3.1415926536
@debug calc(e);  // 2.7182818285
@debug calc(infinity) > math.$max-number;  // true
@debug calc(-infinity) < math.$min-number; // true
```

**Sass**

```sass
@use 'sass:math'

@debug calc(pi)  // 3.1415926536
@debug calc(e)   // 2.7182818285
@debug calc(infinity) > math.$max-number   // true
@debug calc(-infinity) < math.$min-number  // true
```

## Функции вычислений

**Совместимость (дополнительные функции):** Dart Sass: начиная с 1.65.0 · LibSass: ✗ · Ruby Sass: ✗

Версии Dart Sass 1.65.0 и новее, _кроме_ 1.66.x, поддерживают выполнение
  следующих функций вычислений: `round()`, `mod()`, `rem()`, `sin()`, `cos()`,
  `tan()`, `asin()`, `acos()`, `atan()`, `atan2()`, `pow()`, `sqrt()`,
  `hypot()`, `log()`, `exp()`, `abs()` и `sign()`.

  В Dart Sass 1.65.x любой вызов функции, имя которой совпадало с функцией
  вычисления, _всегда_ разбирался как функция вычисления. Это ломало
  некоторые существующие пользовательские функции, поэтому поддержка новых
  функций вычислений была удалена в 1.66.0, пока её не удалось вернуть в
  1.67.0, _не_ ломая существующее поведение.


Sass разбирает следующие функции как [вычисления]:
* Функции сравнения: [`min()`], [`max()`] и [`clamp()`]
* Функции ступенчатых значений: [`round()`], [`mod()`] и [`rem()`].
* Тригонометрические функции: [`sin()`], [`cos()`], [`tan()`], [`asin()`], [`acos()`],
  [`atan()`] и [`atan2()`].
* Экспоненциальные функции: [`pow()`], [`sqrt()`], [`hypot()`], [`log()`] и [`exp()`].
* Функции знака: [`abs()`] и [`sign()`].

[вычисления]: https://www.w3.org/TR/css-values-4/#math
[`min()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/min
[`max()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/max
[`clamp()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/clamp
[`round()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/round
[`abs()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/abs
[`sin()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sin
[`cos()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/cos
[`tan()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/tan
[`asin()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/asin
[`acos()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/acos
[`atan()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/atan
[`atan2()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/atan2
[`pow()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/pow
[`sqrt()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sqrt
[`hypot()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/hypot
[`log()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/log
[`exp()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/exp
[`mod()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/mod
[`rem()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/rem
[`sign()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/sign

> 💡 **Занятный факт**
>
> Если вы определили [функцию Sass] с тем же именем, что и функция вычисления,
> Sass всегда будет вызывать вашу функцию вместо создания значения вычисления.
>
> [функцию Sass]: https://sass-lang.com/documentation/at-rules/function

### Устаревшие глобальные функции

CSS добавил поддержку [математических выражений] в Values and Units Level
4. Однако Sass поддерживал собственные [`round()`], [`abs()`], [`min()`] и
[`max()`] задолго до этого, и ему нужно было сохранять обратную совместимость
со всеми существующими таблицами стилей. Это потребовало особой синтаксической
изощрённости.

[математических выражений]: https://www.w3.org/TR/css-values-4/#math
[`round()`]: ../modules/math#round
[`abs()`]: ../modules/math#abs
[`min()`]: ../modules/math#min
[`max()`]: ../modules/math#max

Если вызов `round()`, `abs()`, `min()` или `max()` является допустимым
выражением вычисления, он будет разобран как вычисление. Но как только любая
часть вызова содержит возможность SassScript, не поддерживаемую в вычислении,
например [оператор деления по модулю], он вместо этого разбирается как вызов
соответствующей математической функции Sass.

Поскольку вычисления в любом случае упрощаются до чисел, когда это возможно,
единственное существенное отличие в том, что функции Sass поддерживают только
единицы измерения, которые можно объединить на этапе сборки, поэтому
`min(12px % 10, 10%)` вызовет ошибку.

[оператор деления по модулю]: https://sass-lang.com/documentation/operators/numeric/

> ⚠️ **Внимание**
>
> Другие вычисления не допускают, чтобы числа без единиц измерения
> складывались, вычитались или сравнивались с числами, имеющими единицы
> измерения. Однако [`min()`], [`max()`], [`abs()`] и [`round()` с одним
> аргументом] отличаются: ради обратной совместимости с глобальными
> устаревшими функциями Sass, которые по историческим причинам допускают
> смешивание величин с единицами и без них, эти единицы можно смешивать, если
> они находятся непосредственно внутри вычисления `min()`, `max()`, `abs()`
> или `round()` с одним аргументом.
>
> Например, `min(5 + 10px, 20px)` даст в результате `15px`. Однако
> `sqrt(5 + 10px)` вызовет ошибку, так как `sqrt(5 + 10px)` никогда не была
> глобальной функцией Sass, и эти единицы несовместимы.
>
> [`round()` с одним аргументом]: https://developer.mozilla.org/en-US/docs/Web/CSS/round
> [`abs()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/abs
> [`min()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/min
> [`max()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/max

#### `min()` и `max()`

**Совместимость (синтаксис min и max):** Dart Sass: начиная с >=1.11.0 <1.42.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до 1.11.0 *всегда* разбирают
  `min()` и `max()` как функции Sass. Чтобы создать обычный CSS-вызов
  `min()` или `max()` для этих реализаций, вместо этого можно написать что-то
  вроде `unquote("min(#{$padding}, env(safe-area-inset-left))")`.

  CSS добавил поддержку [функций `min()` и `max()`] в Values and Units
  Level 4, откуда их быстро переняла Safari [для поддержки iPhone X].
  Поскольку мы уже поддерживали `min()` и `max()` как устаревшие функции Sass,
  нам пришлось реализовать логику для обратной совместимости и для поддержки
  их как CSS-функций.

  Версии Dart Sass между 1.11.0 и 1.40.0, а также между 1.40.1
  и 1.42.0 разбирают функции `min()` и `max()` как [специальные функции], если
  они являются допустимым обычным CSS, но разбирают их как функции Sass, если
  они содержат возможности Sass, отличные от интерполяции, например переменные
  или вызовы функций.

  Dart Sass 1.41.0 разбирает функции `min()` и `max()` как вычисления, но не
  допускает объединения чисел без единиц измерения с числами, имеющими
  единицы. Это было обратно несовместимо с глобальными функциями `min()` и
  `max()`, поэтому такое поведение было отменено.

  [функций `min()` и `max()`]: https://www.w3.org/TR/css-values-4/#math
  [для поддержки iPhone X]: https://webkit.org/blog/7929/designing-websites-for-iphone-x/
  [специальные функции]: https://sass-lang.com/documentation/syntax/special-functions/


**SCSS**

```scss
$padding: 12px;

.post {
  // Since these max() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: max($padding, env(safe-area-inset-left));
  padding-right: max($padding, env(safe-area-inset-right));
}

.sidebar {
  // Since these use the SassScript-only modulo operator, they're parsed as
  // SassScript function calls.
  padding-left: max($padding % 10, 20px);
  padding-right: max($padding % 10, 20px);
}
```

**Sass**

```sass
$padding: 12px

.post
  // Since these max() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: max($padding, env(safe-area-inset-left))
  padding-right: max($padding, env(safe-area-inset-right))


.sidebar
  // Since these use the SassScript-only modulo operator, they're parsed as
  // SassScript function calls.
  padding-left: max($padding % 10, 20px)
  padding-right: max($padding % 10, 20px)
```

**Результат CSS**

```css
.post {
  padding-left: max(12px, env(safe-area-inset-left));
  padding-right: max(12px, env(safe-area-inset-right));
}

.sidebar {
  padding-left: 20px;
  padding-right: 20px;
}
```

#### `round()`

**Совместимость (синтаксис min и max):** Dart Sass: начиная с 1.65.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до 1.65.0, а также Dart
  Sass 1.66.x, *всегда* разбирают `round()` как функцию Sass. Чтобы
  использовать обычную CSS-функцию для этих реализаций, вместо этого можно
  написать что-то вроде `round(#{$strategy, $number, $step})`.


Функция [`round(<strategy>, number, step)`] принимает необязательную стратегию
округления, значение для округления и интервал округления `step`. `strategy`
должен быть `nearest`, `up`, `down` или `to-zero`.

[`round(<strategy>, number, step)`]: https://developer.mozilla.org/en-US/docs/Web/CSS/round#parameter

**SCSS**

```scss
$number: 12.5px;
$step: 15px;

.post-image {
  // Since these round() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: round(nearest, $number, $step);
  padding-right: round($number + 10px);
  padding-bottom: round($number + 10px, $step + 10%);
}
```

**Sass**

```sass
$number: 12.5px
$step: 15px

.post-image
  // Since these round() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: round(nearest, $number, $step)
  padding-right: round($number + 10px)
  padding-bottom: round($number + 10px, $step + 10%)
```

**Результат CSS**

```css
.post-image {
  padding-left: 15px;
  padding-right: 23px;
  padding-bottom: round(22.5px, 15px + 10%);
}
```

#### `abs()`

**Совместимость (синтаксис min и max):** Dart Sass: начиная с 1.67.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до 1.67.0 *всегда* разбирают
  `abs()` как функцию Sass. Чтобы создать обычное CSS-вычисление для этих
  реализаций, вместо этого можно написать что-то вроде `abs(#{$number})`.


> ⚠️ **Внимание**
>
> Совместимость глобальной функции `abs()` с [параметрами в процентах
> устарела]. В будущем это будет генерировать CSS-функцию abs(), которую
> будет разрешать браузер.
>
> [параметрами в процентах устарела]: https://sass-lang.com/documentation/breaking-changes/abs-percent/

Функция [`abs(value)`] принимает единственное выражение в качестве параметра
и возвращает абсолютное значение `$value`. Если `$value` отрицательно,
возвращается `-$value`, а если `$value` положительно, возвращается `$value`
как есть.

[`abs(value)`]: https://developer.mozilla.org/en-US/docs/Web/CSS/abs

**SCSS**

```scss
.post-image {
  // Since these abs() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: abs(10px);
  padding-right: math.abs(-7.5%);
  padding-top: abs(1 + 1px);
}
```

**Sass**

```sass
.post-image
  // Since these abs() calls are valid calculation expressions, they're
  // parsed as calculations.
  padding-left: abs(-10px)
  padding-right: math.abs(-7.5%)
  padding-top: abs(1 + 1px)
```

**Результат CSS**

```css
.post-image {
  padding-left: 10px;
  padding-right: 7.5%;
  padding-top: 2px;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/calculations/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

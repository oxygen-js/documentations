# sass:math


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass в настоящее время поддерживает загрузку встроенных модулей с помощью `@use`. Пользователям других реализаций вместо этого нужно вызывать функции по их глобальным именам.



## Переменные


---


**`math.$e`**

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Наиболее близкое 64-битное приближение с плавающей точкой к [математической константе *e*][].

  [математической константе *e*]: https://en.wikipedia.org/wiki/E_(mathematical_constant)

  **SCSS**

```scss
@use 'sass:math';

@debug math.$e; // 2.7182818285
```

**Sass**

```sass
@use 'sass:math'

@debug math.$e  // 2.7182818285
```



---


**`math.$epsilon`**

  **Совместимость:** Dart Sass: начиная с 1.55.0 · LibSass: ✗ · Ruby Sass: ✗


  Разница между 1 и наименьшим 64-битным числом с плавающей точкой, которое
  больше 1, согласно сравнению чисел с плавающей точкой. Из-за того что числа
  в Sass имеют [10 цифр точности](https://sass-lang.com/documentation/values/numbers),
  в некоторых случаях результат будет выглядеть как 0.



---


**`math.$max-number`**

  **Совместимость:** Dart Sass: начиная с 1.55.0 · LibSass: ✗ · Ruby Sass: ✗


  Максимальное конечное число, которое можно представить в виде 64-битного
  числа с плавающей точкой.

  **SCSS**

```scss
@use 'sass:math';

@debug math.$max-number; // 179769313486231570000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
```

**Sass**

```sass
@use 'sass:math'

@debug math.$max-number  // 179769313486231570000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
```



---


**`math.$max-safe-integer`**

  **Совместимость:** Dart Sass: начиная с 1.55.0 · LibSass: ✗ · Ruby Sass: ✗


  Максимальное целое число `n`, такое что и `n`, и `n + 1` можно точно
  представить в виде 64-битного числа с плавающей точкой.

  **SCSS**

```scss
@use 'sass:math';

@debug math.$max-safe-integer; // 9007199254740991
```

**Sass**

```sass
@use 'sass:math'

@debug math.$max-safe-integer  // 9007199254740991
```



---


**`math.$min-number`**

  **Совместимость:** Dart Sass: начиная с 1.55.0 · LibSass: ✗ · Ruby Sass: ✗


  Наименьшее положительное число, которое можно представить в виде 64-битного
  числа с плавающей точкой. Из-за того что числа в Sass имеют [10 цифр
  точности](https://sass-lang.com/documentation/values/numbers), в некоторых случаях результат будет
  выглядеть как 0.



---


**`math.$min-safe-integer`**

  **Совместимость:** Dart Sass: начиная с 1.55.0 · LibSass: ✗ · Ruby Sass: ✗


  Минимальное целое число `n`, такое что и `n`, и `n - 1` можно точно
  представить в виде 64-битного числа с плавающей точкой.

  **SCSS**

```scss
@use 'sass:math';

@debug math.$min-safe-integer; // -9007199254740991
```

**Sass**

```sass
@use 'sass:math'

@debug math.$min-safe-integer  // -9007199254740991
```



---


**`math.$pi`**

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Наиболее близкое 64-битное приближение с плавающей точкой к [математической
  константе *π*][].

  [математической константе *π*]: https://en.wikipedia.org/wiki/Pi

  **SCSS**

```scss
@use 'sass:math';

@debug math.$pi; // 3.1415926536
```

**Sass**

```sass
@use 'sass:math'

@debug math.$pi  // 3.1415926536
```


## Функции ограничения


---


**`math.ceil($number)`**

**`ceil($number)`**

*Возвращает: `number`*

  Округляет `$number` вверх до ближайшего большего целого числа.

  **SCSS**

```scss
@use 'sass:math';

@debug math.ceil(4); // 4
@debug math.ceil(4.2); // 5
@debug math.ceil(4.9); // 5
```

**Sass**

```sass
@use 'sass:math'

@debug math.ceil(4)  // 4
@debug math.ceil(4.2)  // 5
@debug math.ceil(4.9)  // 5
```



---


**`math.clamp($min, $number, $max)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Ограничивает `$number` диапазоном между `$min` и `$max`. Если `$number`
  меньше `$min`, возвращается `$min`, а если больше `$max` — возвращается
  `$max`.

  `$min`, `$number` и `$max` должны иметь совместимые единицы измерения либо
  не иметь единиц измерения вовсе.

  **SCSS**

```scss
@use 'sass:math';

@debug math.clamp(-1, 0, 1); // 0
@debug math.clamp(1px, -1px, 10px); // 1px
@debug math.clamp(-1in, 1cm, 10mm); // 10mm
```

**Sass**

```sass
@use 'sass:math'

@debug math.clamp(-1, 0, 1) // 0
@debug math.clamp(1px, -1px, 10px) // 1px
@debug math.clamp(-1in, 1cm, 10mm) // 10mm
```



---


**`math.floor($number)`**

**`floor($number)`**

*Возвращает: `number`*

  Округляет `$number` вниз до ближайшего меньшего целого числа.

  **SCSS**

```scss
@use 'sass:math';

@debug math.floor(4); // 4
@debug math.floor(4.2); // 4
@debug math.floor(4.9); // 4
```

**Sass**

```sass
@use 'sass:math'

@debug math.floor(4)  // 4
@debug math.floor(4.2)  // 4
@debug math.floor(4.9)  // 4
```



---


**`math.max($number...)`**

**`max($number...)`**

*Возвращает: `number`*

  Возвращает наибольшее из одного или нескольких чисел.

  **SCSS**

```scss
@use 'sass:math';

@debug math.max(1px, 4px); // 4px

$widths: 50px, 30px, 100px;
@debug math.max($widths...); // 100px
```

**Sass**

```sass
@use 'sass:math'

@debug math.max(1px, 4px)  // 4px

$widths: 50px, 30px, 100px
@debug math.max($widths...)  // 100px
```



---


**`math.min($number...)`**

**`min($number...)`**

*Возвращает: `number`*

  Возвращает наименьшее из одного или нескольких чисел.

  **SCSS**

```scss
@use 'sass:math';

@debug math.min(1px, 4px); // 1px

$widths: 50px, 30px, 100px;
@debug math.min($widths...); // 30px
```

**Sass**

```sass
@use 'sass:math'

@debug math.min(1px, 4px)  // 1px

$widths: 50px, 30px, 100px
@debug math.min($widths...)  // 30px
```



---


**`math.round($number)`**

**`round($number)`**

*Возвращает: `number`*

  Округляет `$number` до ближайшего целого числа.

  **SCSS**

```scss
@use 'sass:math';

@debug math.round(4); // 4
@debug math.round(4.2); // 4
@debug math.round(4.9); // 5
```

**Sass**

```sass
@use 'sass:math'

@debug math.round(4)  // 4
@debug math.round(4.2)  // 4
@debug math.round(4.9)  // 5
```


## Функции расстояния


---


**`math.abs($number)`**

**`abs($number)`**

*Возвращает: `number`*

  Возвращает [абсолютное значение][] числа `$number`. Если `$number`
  отрицательное, возвращается `-$number`, а если `$number` положительное,
  оно возвращается как есть.

  [абсолютное значение]: https://en.wikipedia.org/wiki/Absolute_value

  **SCSS**

```scss
@use 'sass:math';

@debug math.abs(10px); // 10px
@debug math.abs(-10px); // 10px
```

**Sass**

```sass
@use 'sass:math'

@debug math.abs(10px) // 10px
@debug math.abs(-10px) // 10px
```



---


**`math.hypot($number...)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает длину *n*-мерного [вектора][], компоненты которого равны каждому
  из `$number`. Например, для трёх чисел *a*, *b* и *c* эта функция
  возвращает квадратный корень из *a² + b² + c²*.

  Все числа должны либо иметь совместимые единицы измерения, либо не иметь
  единиц измерения вовсе. А поскольку единицы измерения чисел могут
  различаться, результат принимает единицу измерения первого числа.

  [вектора]: https://en.wikipedia.org/wiki/Euclidean_vector

  **SCSS**

```scss
@use 'sass:math';

@debug math.hypot(3, 4); // 5

$lengths: 1in, 10cm, 50px;
@debug math.hypot($lengths...); // 4.0952775683in
```

**Sass**

```sass
@use 'sass:math'

@debug math.hypot(3, 4) // 5

$lengths: 1in, 10cm, 50px
@debug math.hypot($lengths...) // 4.0952775683in
```


## Экспоненциальные функции


---


**`math.log($number, $base: null)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [логарифм][] числа `$number` по основанию `$base`. Если `$base`
  равно `null`, вычисляется [натуральный логарифм][].

  `$number` и `$base` не должны иметь единиц измерения.

  [логарифм]: https://en.wikipedia.org/wiki/Logarithm
  [натуральный логарифм]: https://en.wikipedia.org/wiki/Natural_logarithm

  **SCSS**

```scss
@use 'sass:math';

@debug math.log(10); // 2.302585093
@debug math.log(10, 10); // 1
```

**Sass**

```sass
@use 'sass:math'

@debug math.log(10) // 2.302585093
@debug math.log(10, 10) // 1
```



---


**`math.pow($base, $exponent)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возводит `$base` [в степень][] `$exponent`.

  `$base` и `$exponent` не должны иметь единиц измерения.

  [в степень]: https://en.wikipedia.org/wiki/Exponentiation

  **SCSS**

```scss
@use 'sass:math';

@debug math.pow(10, 2); // 100
@debug math.pow(100, math.div(1, 3)); // 4.6415888336
@debug math.pow(5, -2); // 0.04
```

**Sass**

```sass
@use 'sass:math'

@debug math.pow(10, 2) // 100
@debug math.pow(100, math.div(1, 3)) // 4.6415888336
@debug math.pow(5, -2) // 0.04
```



---


**`math.sqrt($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [квадратный корень][] числа `$number`.

  `$number` не должен иметь единиц измерения.

  [квадратный корень]: https://en.wikipedia.org/wiki/Square_root

  **SCSS**

```scss
@use 'sass:math';

@debug math.sqrt(100); // 10
@debug math.sqrt(math.div(1, 3)); // 0.5773502692
@debug math.sqrt(-1); // NaN
```

**Sass**

```sass
@use 'sass:math'

@debug math.sqrt(100) // 10
@debug math.sqrt(math.div(1, 3)) // 0.5773502692
@debug math.sqrt(-1) // NaN
```


## Тригонометрические функции


---


**`math.cos($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [косинус][] числа `$number`.

  `$number` должен быть углом (его единица измерения должна быть совместима
  с `deg`) либо не иметь единиц измерения. Если у `$number` нет единиц
  измерения, предполагается, что он задан в `rad`.

  [косинус]: https://en.wikipedia.org/wiki/Trigonometric_functions#Right-angled_triangle_definitions

  **SCSS**

```scss
@use 'sass:math';

@debug math.cos(100deg); // -0.1736481777
@debug math.cos(1rad); // 0.5403023059
@debug math.cos(1); // 0.5403023059
```

**Sass**

```sass
@use 'sass:math'

@debug math.cos(100deg) // -0.1736481777
@debug math.cos(1rad) // 0.5403023059
@debug math.cos(1) // 0.5403023059
```



---


**`math.sin($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [синус][] числа `$number`.

  `$number` должен быть углом (его единица измерения должна быть совместима
  с `deg`) либо не иметь единиц измерения. Если у `$number` нет единиц
  измерения, предполагается, что он задан в `rad`.

  [синус]: https://en.wikipedia.org/wiki/Trigonometric_functions#Right-angled_triangle_definitions

  **SCSS**

```scss
@use 'sass:math';

@debug math.sin(100deg); // 0.984807753
@debug math.sin(1rad); // 0.8414709848
@debug math.sin(1); // 0.8414709848
```

**Sass**

```sass
@use 'sass:math'

@debug math.sin(100deg) // 0.984807753
@debug math.sin(1rad) // 0.8414709848
@debug math.sin(1) // 0.8414709848
```



---


**`math.tan($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [тангенс][] числа `$number`.

  `$number` должен быть углом (его единица измерения должна быть совместима
  с `deg`) либо не иметь единиц измерения. Если у `$number` нет единиц
  измерения, предполагается, что он задан в `rad`.

  [тангенс]: https://en.wikipedia.org/wiki/Trigonometric_functions#Right-angled_triangle_definitions

  **SCSS**

```scss
@use 'sass:math';

@debug math.tan(100deg); // -5.6712818196
@debug math.tan(1rad); // 1.5574077247
@debug math.tan(1); // 1.5574077247
```

**Sass**

```sass
@use 'sass:math'

@debug math.tan(100deg) // -5.6712818196
@debug math.tan(1rad) // 1.5574077247
@debug math.tan(1) // 1.5574077247
```



---


**`math.acos($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [арккосинус][] числа `$number` в `deg`.

  `$number` не должен иметь единиц измерения.

  [арккосинус]: https://en.wikipedia.org/wiki/Inverse_trigonometric_functions#Basic_properties

  **SCSS**

```scss
@use 'sass:math';

@debug math.acos(0.5); // 60deg
@debug math.acos(2); // NaNdeg
```

**Sass**

```sass
@use 'sass:math'

@debug math.acos(0.5) // 60deg
@debug math.acos(2) // NaNdeg
```



---


**`math.asin($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [арксинус][] числа `$number` в `deg`.

  `$number` не должен иметь единиц измерения.

  [арксинус]: https://en.wikipedia.org/wiki/Inverse_trigonometric_functions#Basic_properties

  **SCSS**

```scss
@use 'sass:math';

@debug math.asin(0.5); // 30deg
@debug math.asin(2); // NaNdeg
```

**Sass**

```sass
@use 'sass:math'

@debug math.asin(0.5) // 30deg
@debug math.asin(2) // NaNdeg
```



---


**`math.atan($number)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [арктангенс][] числа `$number` в `deg`.

  `$number` не должен иметь единиц измерения.

  [арктангенс]: https://en.wikipedia.org/wiki/Inverse_trigonometric_functions#Basic_properties

  **SCSS**

```scss
@use 'sass:math';

@debug math.atan(10); // 84.2894068625deg
```

**Sass**

```sass
@use 'sass:math'

@debug math.atan(10) // 84.2894068625deg
```



---


**`math.atan2($y, $x)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.25.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [арктангенс с двумя аргументами][] чисел `$y` и `$x` в `deg`.

  `$y` и `$x` должны иметь совместимые единицы измерения либо не иметь единиц
  измерения.

  [арктангенс с двумя аргументами]: https://en.wikipedia.org/wiki/Atan2

  > 💡 **Занятный факт**
  >
  > `math.atan2($y, $x)` отличается от `atan(math.div($y, $x))` тем, что
  > сохраняет квадрант рассматриваемой точки. Например, `math.atan2(1,
  > -1)` соответствует точке `(-1, 1)` и возвращает `135deg`. А
  > `math.atan(math.div(1, -1))` и `math.atan(math.div(-1, 1))` сначала
  > сводятся к `atan(-1)`, поэтому оба возвращают `-45deg`.

  **SCSS**

```scss
@use 'sass:math';

@debug math.atan2(-1, 1); // 135deg
```

**Sass**

```sass
@use 'sass:math'

@debug math.atan2(-1, 1) // 135deg
```


## Функции для единиц измерения


---


**`math.compatible($number1, $number2)`**

**`comparable($number1, $number2)`**

*Возвращает: `boolean`*

  Возвращает, имеют ли `$number1` и `$number2` совместимые единицы измерения.

  Если возвращается `true`, `$number1` и `$number2` можно безопасно
  [складывать][], [вычитать][] и [сравнивать][]. В противном случае это
  приведёт к ошибкам.

  [складывать]: https://sass-lang.com/documentation/operators/numeric
  [вычитать]: https://sass-lang.com/documentation/operators/numeric
  [сравнивать]: https://sass-lang.com/documentation/operators/relational

  > ⚠️ **Внимание**
  >
  > Глобальное имя этой функции — <code>compa<strong>ra</strong>ble</code>,
  > но при добавлении в модуль `sass:math` имя изменили на
  > <code>compa<strong>ti</strong>ble</code>, чтобы точнее передать смысл
  > того, что делает функция.

  **SCSS**

```scss
@use 'sass:math';

@debug math.compatible(2px, 1px); // true
@debug math.compatible(100px, 3em); // false
@debug math.compatible(10cm, 3mm); // true
```

**Sass**

```sass
@use 'sass:math'

@debug math.compatible(2px, 1px)  // true
@debug math.compatible(100px, 3em)  // false
@debug math.compatible(10cm, 3mm)  // true
```



---


**`math.is-unitless($number)`**

**`unitless($number)`**

*Возвращает: `boolean`*

  Возвращает, не имеет ли `$number` единиц измерения.

  **SCSS**

```scss
@use 'sass:math';

@debug math.is-unitless(100); // true
@debug math.is-unitless(100px); // false
```

**Sass**

```sass
@use 'sass:math'

@debug math.is-unitless(100)  // true
@debug math.is-unitless(100px)  // false
```



---


**`math.unit($number)`**

**`unit($number)`**

*Возвращает: `quoted string`*

  Возвращает строковое представление единиц измерения числа `$number`.

  > ⚠️ **Внимание**
  >
  > Эта функция предназначена для отладки; формат её вывода не гарантированно
  > одинаков в разных версиях и реализациях Sass.

  **SCSS**

```scss
@use 'sass:math';

@debug math.unit(100); // ""
@debug math.unit(100px); // "px"
@debug math.unit(5px * 10px); // "px*px"
@debug math.unit(math.div(5px, 1s)); // "px/s"
```

**Sass**

```sass
@use 'sass:math'

@debug math.unit(100)  // ""
@debug math.unit(100px)  // "px"
@debug math.unit(5px * 10px)  // "px*px"
@debug math.unit(math.div(5px, 1s))  // "px/s"
```


## Другие функции


---


**`math.div($number1, $number2)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.33.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает результат деления `$number1` на `$number2`.

  Любые единицы измерения, общие для обоих чисел, взаимно сокращаются.
  Единицы измерения `$number1`, отсутствующие в `$number2`, окажутся в
  числителе результата, а единицы измерения `$number2`, отсутствующие в
  `$number1`, — в его знаменателе.

  > ⚠️ **Внимание**
  >
  > Для обратной совместимости эта функция возвращает *точно такой же
  > результат*, как и [устаревший оператор `/`], включая конкатенацию двух
  > строк с символом `/` между ними. Однако со временем это поведение будет
  > удалено, и его не следует использовать в новых таблицах стилей.
  >
  > [устаревший оператор `/`]: https://sass-lang.com/documentation/breaking-changes/slash-div

  **SCSS**

```scss
@use 'sass:math';

@debug math.div(1, 2); // 0.5
@debug math.div(100px, 5px); // 20
@debug math.div(100px, 5); // 20px
@debug math.div(100px, 5s); // 20px/s
```

**Sass**

```sass
@use 'sass:math'

@debug math.div(1, 2)  // 0.5
@debug math.div(100px, 5px)  // 20
@debug math.div(100px, 5)  // 20px
@debug math.div(100px, 5s)  // 20px/s
```



---


**`math.percentage($number)`**

**`percentage($number)`**

*Возвращает: `number`*

  Преобразует не имеющее единиц измерения `$number` (обычно десятичную дробь
  от 0 до 1) в проценты.

  > 💡 **Занятный факт**
  >
  > Эта функция идентична `$number * 100%`.

  **SCSS**

```scss
@use 'sass:math';

@debug math.percentage(0.2); // 20%
@debug math.percentage(math.div(100px, 50px)); // 200%
```

**Sass**

```sass
@use 'sass:math'

@debug math.percentage(0.2)  // 20%
@debug math.percentage(math.div(100px, 50px))  // 200%
```



---


**`math.random($limit: null)`**

**`random($limit: null)`**

*Возвращает: `number`*

  Если `$limit` равен `null`, возвращается случайное десятичное число от 0
  до 1.

  **SCSS**

```scss
@use 'sass:math';

@debug math.random(); // 0.2821251858
@debug math.random(); // 0.6221325814
```

**Sass**

```sass
@use 'sass:math'

@debug math.random()  // 0.2821251858
@debug math.random()  // 0.6221325814
```

  * * *

  Если `$limit` — число, большее или равное 1, возвращается случайное целое
  число от 1 до `$limit`.

  > ⚠️ **Внимание**
  >
  > `random()` игнорирует единицы измерения в `$limit`. [Это поведение
  > считается устаревшим], и `random($limit)` будет возвращать случайное
  > целое число с теми же единицами измерения, что и аргумент `$limit`.
  >
  > [Это поведение считается устаревшим]: https://sass-lang.com/documentation/breaking-changes/function-units

  **SCSS**

```scss
@use 'sass:math';

@debug math.random(100px); // 42
```

**Sass**

```sass
@use 'sass:math'

@debug math.random(100px)  // 42
```

  **SCSS**

```scss
@use 'sass:math';

@debug math.random(10); // 4
@debug math.random(10000); // 5373
```

**Sass**

```sass
@use 'sass:math'

@debug math.random(10)  // 4
@debug math.random(10000)  // 5373
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/math/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

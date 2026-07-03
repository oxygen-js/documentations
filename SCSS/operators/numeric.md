# Числовые операторы

Sass поддерживает стандартный набор математических операторов для
[чисел](https://sass-lang.com/documentation/values/numbers). Они автоматически преобразуют между
совместимыми единицами измерения.

* `<expression> + <expression>` складывает значение первого [выражения][] со
  значением второго.
* `<expression> - <expression>` вычитает значение первого [выражения][] из
  значения второго.
* `<expression> * <expression>` умножает значение первого [выражения][] на
  значение второго.
* `<expression> % <expression>` возвращает остаток от деления значения первого
  [выражения][] на значение второго. Это называется оператором [*деления по модулю*
  (modulo)][].

[выражения]: https://sass-lang.com/documentation/syntax/structure#expressions
[*деления по модулю* (modulo)]: https://en.wikipedia.org/wiki/Modulo_operation

**SCSS**

```scss
@debug 10s + 15s; // 25s
@debug 1in - 10px; // 0.8958333333in
@debug 5px * 3px; // 15px*px
@debug 1in % 9px; // 0.0625in
```

**Sass**

```sass
@debug 10s + 15s  // 25s
@debug 1in - 10px  // 0.8958333333in
@debug 5px * 3px  // 15px*px
@debug 1in % 9px  // 0.0625in
```

Числа без единиц измерения можно использовать с числами любых единиц измерения.

**SCSS**

```scss
@debug 100px + 50; // 150px
@debug 4s * 10; // 40s
```

**Sass**

```sass
@debug 100px + 50  // 150px
@debug 4s * 10  // 40s
```

Числа с несовместимыми единицами измерения нельзя использовать со сложением, вычитанием или
делением по модулю.

**SCSS**

```scss
@debug 100px + 10s;
//     ^^^^^^^^^^^
// Error: Incompatible units px and s.
```

**Sass**

```sass
@debug 100px + 10s
//     ^^^^^^^^^^^
// Error: Incompatible units px and s.
```

## Унарные операторы

Вы также можете писать `+` и `-` как унарные операторы, которые принимают только одно значение:

* `+<expression>` возвращает значение выражения без изменений.
* `-<expression>` возвращает отрицательную версию значения выражения.

**SCSS**

```scss
@debug +(5s + 7s); // 12s
@debug -(50px + 30px); // -80px
@debug -(10px - 15px); // 5px
```

**Sass**

```sass
@debug +(5s + 7s)  // 12s
@debug -(50px + 30px)  // -80px
@debug -(10px - 15px)  // 5px
```

> ⚠️ **Внимание**
>
> Поскольку `-` может обозначать как вычитание, так и унарное отрицание, в списке через пробел
> может быть непонятно, что имеется в виду. Чтобы избежать проблем:
>
> * Всегда пишите пробелы с обеих сторон `-` при вычитании.
> * Пишите пробел перед `-`, но не после него, для отрицательного числа или унарного
>   отрицания.
> * Оборачивайте унарное отрицание в скобки, если оно находится в списке через пробел.
>
> Разные значения `-` в Sass имеют приоритет в следующем порядке:
>
> 1. `-` как часть идентификатора. Единственное исключение — единицы измерения; Sass обычно
>   позволяет использовать любой допустимый идентификатор в качестве идентификатора, но единицы измерения не
>   могут содержать дефис, за которым следует цифра.
> 2. `-` между выражением и числовым литералом без пробела, что
>   разбирается как вычитание.
> 3. `-` в начале числового литерала, что разбирается как отрицательное
>   число.
> 4. `-` между двумя числами независимо от пробелов, что разбирается как
>   вычитание.
> 5. `-` перед значением, отличным от числового литерала, что разбирается как унарное
>   отрицание.
>
> **SCSS**
>
> ```scss
> @debug a-1; // a-1
> @debug 5px-3px; // 2px
> @debug 5-3; // 2
> @debug 1 -2 3; // 1 -2 3
>
> $number: 2;
> @debug 1 -$number 3; // -1 3
> @debug 1 (-$number) 3; // 1 -2 3
> ```
>
> **Sass**
>
> ```sass
> @debug a-1  // a-1
> @debug 5px-3px  // 2px
> @debug 5-3  // 2
> @debug 1 -2 3  // 1 -2 3
>
> $number: 2
> @debug 1 -$number 3  // -1 3
> @debug 1 (-$number) 3  // 1 -2 3
> ```

## Деление

**Совместимость (`math.div()`):** Dart Sass: начиная с 1.33.0 · LibSass: ✗ · Ruby Sass: ✗


В отличие от других математических операций, деление в Sass выполняется с помощью функции
[`math.div()`]. Хотя многие языки программирования используют `/` как оператор
деления, в CSS `/` используется как разделитель (как в `font: 15px/32px` или
`hsl(120 100% 50% / 0.8)`). Хотя Sass и поддерживает использование `/` как оператора
деления, это устарело и [будет удалено] в будущей версии.

[`math.div()`]: https://sass-lang.com/documentation/modules/math#div
[будет удалено]: https://sass-lang.com/documentation/breaking-changes/slash-div

### Значения, разделённые слэшем

Пока Sass всё ещё поддерживает `/` как оператор деления, ему нужен способ
различать `/` как разделитель и `/` как деление. Чтобы это работало,
если два числа разделены `/`, Sass выведет
результат как разделённый слэшем, а не как результат деления, если только не выполнено одно из следующих условий:

* Любое из выражений — что-либо, кроме числового литерала.
* Результат сохраняется в переменной или возвращается функцией.
* Операция обёрнута в скобки, если только эти скобки не находятся
  снаружи списка, содержащего операцию.
* Результат используется как часть другой операции (кроме `/`).
* Результат возвращается [вычислением].

[вычислением]: https://sass-lang.com/documentation/values/calculations

Вы можете использовать [`list.slash()`], чтобы принудительно использовать `/` как разделитель.

[`list.slash()`]: https://sass-lang.com/documentation/modules/list#slash

**SCSS**

```scss
@use "sass:list";

@debug 15px / 30px; // 15px/30px
@debug (10px + 5px) / 30px; // 0.5
@debug list.slash(10px + 5px, 30px); // 15px/30px

$result: 15px / 30px;
@debug $result; // 0.5

@function fifteen-divided-by-thirty() {
  @return 15px / 30px;
}
@debug fifteen-divided-by-thirty(); // 0.5

@debug (15px/30px); // 0.5
@debug (bold 15px/30px sans-serif); // bold 15px/30px sans-serif
@debug 15px/30px + 1; // 1.5
```

**Sass**

```sass
@use "sass:list";

@debug 15px / 30px  // 15px/30px
@debug (10px + 5px) / 30px  // 0.5
@debug list.slash(10px + 5px, 30px)  // 15px/30px

$result: 15px / 30px
@debug $result  // 0.5

@function fifteen-divided-by-thirty()
  @return 15px / 30px

@debug fifteen-divided-by-thirty()  // 0.5

@debug (15px/30px)  // 0.5
@debug (bold 15px/30px sans-serif)  // bold 15px/30px sans-serif
@debug 15px/30px + 1  // 1.5
```

## Единицы измерения

Sass обладает мощной поддержкой манипулирования единицами измерения, основанной на том, как работают
[вычисления с реальными единицами измерения][]. Когда два числа умножаются, их единицы измерения
тоже перемножаются. Когда одно число делится на другое, результат берёт свои
единицы числителя из первого числа, а единицы знаменателя — из второго.
Число может иметь любое количество единиц измерения в числителе и/или знаменателе.

[вычисления с реальными единицами измерения]: https://en.wikipedia.org/wiki/Unit_of_measurement#Calculations_with_units_of_measurement

**SCSS**

```scss
@use 'sass:math';

@debug 4px * 6px; // calc(24px * 1px) (read "square pixels")
@debug math.div(5px, 2s); // calc(2.5px / 1s) (read "pixels per second")

// calc(3.125px * 1deg / 1s / 1em) (read "pixel-degrees per second-em")
@debug 5px * math.div(math.div(30deg, 2s), 24em);

$degrees-per-second: math.div(20deg, 1s);
@debug $degrees-per-second; // calc(20deg / 1s)
@debug math.div(1, $degrees-per-second); // calc(0.05s / 1deg)
```

**Sass**

```sass
@use 'sass:math'

@debug 4px * 6px  // calc(24px * 1px) (read "square pixels")
@debug math.div(5px, 2s)  // calc(2.5px / 1s) (read "pixels per second")

// calc(3.125px * 1deg / 1s / 1em) (read "pixel-degrees per second-em")
@debug 5px * math.div(math.div(30deg, 2s), 24em)

$degrees-per-second: math.div(20deg, 1s)
@debug $degrees-per-second  // calc(20deg / 1s)
@debug math.div(1, $degrees-per-second)  // calc(0.05s / 1deg)
```

> ⚠️ **Внимание**
>
> В CSS числа со сложными единицами измерения могут быть представлены только в `calc()` и
> других математических выражениях, поэтому Sass выводит такие числа как выражения `calc()`.
> Обычные CSS-свойства не принимают сложные единицы измерения, но они могут быть
> полезны в [пользовательских свойствах] или [функциях].
>
> [пользовательских свойствах]: https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Cascading_variables/Using_custom_properties
> [функциях]: https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Custom_functions_and_mixins/Using_custom_functions
>
> **Совместимость (сложные единицы измерения в CSS):** Dart Sass: начиная с 1.96.0 · LibSass: ✗ · Ruby Sass: ✗
>
> CSS указал поддержку сложных единиц измерения, таких как квадратные пиксели, только в [Values
> and Units Level 4], а браузеры начали реализовывать её только в 2025 году. Более ранние
> версии Sass выдавали ошибки при попытке использовать значение со сложными
> единицами измерения в CSS, хотя их всё ещё можно было использовать в переменных и
> функциях Sass.
>
> [Values and Units Level 4]: https://www.w3.org/TR/css-values-4/
>
> Начиная с Dart Sass 1.69.6, [`meta.inspect()`] возвращает корректные выражения `calc()`
> для чисел со сложными единицами измерения.
>
> [`meta.inspect()`]: https://sass-lang.com/documentation/modules/meta#inspect

Sass автоматически преобразует между совместимыми единицами измерения, хотя то, какую единицу
измерения он выберет для результата, зависит от того, какую реализацию Sass вы используете.
Если вы попытаетесь объединить несовместимые единицы измерения, например `1in + 1em`, Sass выдаст
ошибку.

**SCSS**

```scss
// CSS defines one inch as 96 pixels.
@debug 1in + 6px; // 102px or 1.0625in

@debug 1in + 1s;
//     ^^^^^^^^
// Error: Incompatible units s and in.
```

**Sass**

```sass
// CSS defines one inch as 96 pixels.
@debug 1in + 6px  // 102px or 1.0625in

@debug 1in + 1s
//     ^^^^^^^^
// Error: Incompatible units s and in.
```

Как и в вычислениях с реальными единицами измерения, если числитель содержит единицы измерения, совместимые
с единицами измерения в знаменателе (как в `math.div(96px, 1in)`), они сократятся. Это позволяет легко
задать соотношение, которое можно использовать для преобразования между единицами измерения. В примере ниже
мы задаём желаемую скорость как одну секунду на 50 пикселей, а затем умножаем её
на количество пикселей, которое проходит переход, чтобы получить время, которое он должен занять.

**SCSS**

```scss
@use 'sass:math';

$transition-speed: math.div(1s, 50px);

@mixin move($left-start, $left-stop) {
  position: absolute;
  left: $left-start;
  transition: left ($left-stop - $left-start) * $transition-speed;

  &:hover {
    left: $left-stop;
  }
}

.slider {
  @include move(10px, 120px);
}
```

**Sass**

```sass
@use 'sass:math'

$transition-speed: math.div(1s, 50px)

@mixin move($left-start, $left-stop)
  position: absolute
  left: $left-start
  transition: left ($left-stop - $left-start) * $transition-speed

  &:hover
    left: $left-stop



.slider
  @include move(10px, 120px)
```

**Результат CSS**

```css
.slider {
  position: absolute;
  left: 10px;
  transition: left 2.2s;
}
.slider:hover {
  left: 120px;
}
```

> ⚠️ **Внимание**
>
> Если ваши вычисления дают неверную единицу измерения, вам, вероятно, нужно проверить свои
> расчёты. Возможно, вы упускаете единицы измерения для величины, у которой они должны быть!
> Соблюдение чистоты единиц измерения позволяет Sass выдавать полезные ошибки, когда что-то не
> так.
>
> Особенно стоит избегать использования интерполяции вроде `#{$number}px`. Это
> на самом деле не создаёт число! Это создаёт [строку без кавычек][], которая
> *выглядит* как число, но не будет работать ни с какими [числовыми операциями][] или
> [функциями][]. Старайтесь делать свои вычисления чистыми по единицам измерения, чтобы `$number` уже имела
> единицу измерения `px`, или пишите `$number * 1px`.
>
> [строку без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted
> [числовыми операциями]: https://sass-lang.com/documentation/operators/numeric
> [функциями]: https://sass-lang.com/documentation/modules/math

> ⚠️ **Внимание**
>
> Проценты в Sass работают так же, как и любая другая единица измерения. Они *не*
> взаимозаменяемы с десятичными дробями, поскольку в CSS десятичные дроби и проценты означают
> разные вещи. Например, `50%` — это число с единицей измерения `%`, и
> Sass считает его отличным от числа `0.5`.
>
> Вы можете преобразовывать между десятичными дробями и процентами с помощью арифметики единиц измерения.
> `math.div($percentage, 100%)` вернёт соответствующую десятичную дробь, а
> `$decimal * 100%` вернёт соответствующий процент. Вы также можете использовать
> [функцию `math.percentage()`][] как более явный способ записи
> `$decimal * 100%`.
>
> [функцию `math.percentage()`]: https://sass-lang.com/documentation/modules/math#percentage

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/numeric/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

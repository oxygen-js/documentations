# Специальные функции

CSS определяет множество функций, и большинство из них прекрасно работают с
обычным синтаксисом функций Sass. Они разбираются как вызовы функций,
приводятся к [обычным функциям CSS](https://sass-lang.com/documentation/at-rules/function/#plain-css-functions) и
компилируются в CSS как есть. Однако есть несколько исключений — функций со
специальным синтаксисом, которые нельзя разобрать просто как [выражение
SassScript](https://sass-lang.com/documentation/syntax/structure#expressions). Все вызовы специальных
функций возвращают [строки без кавычек](https://sass-lang.com/documentation/values/strings#unquoted).

## `if()`

**Совместимость (calc()):** Dart Sass: начиная с 1.95.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до этой разбирают `if()` как функцию
  Sass с сигнатурой `if($condition, $if-true, $if-false)`. Если `$condition`
  [приводится к истине][truthy], функция возвращает `$if-true`; в противном
  случае — `$if-false`. У этой функции особый синтаксис, который позволяет не
  вычислять ветку, не соответствующую `$condition`.

  [truthy]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness

  Версии Dart Sass 1.95.0 и новее разбирают `if()` так, как описано ниже.
  Версии Dart Sass до 3.0.0 всё ещё поддерживают старый синтаксис `if()`, но
  он считается устаревшим. См. [/d/if-function].

  [/d/if-function]: https://sass-lang.com/documentation/breaking-changes/if-function


Sass поддерживает [функцию CSS `if()`][CSS `if()` function] с одним важным
дополнением: условие `sass(...)`, которое принимает выражение SassScript и
срабатывает, если это выражение вычисляется в [истинное][truthy] значение.
Функция `if()`, содержащая только условия `sass(...)` (и, возможно, `else`),
будет полностью вычислена Sass и вернёт соответствующее значение.

[CSS `if()` function]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/if
[truthy]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness

SassScript в условиях функции `if()` допускается *только* внутри условия
`sass(...)` или в [интерполяции][interpolation]. Значения же, напротив,
представляют собой обычные выражения SassScript и не требуют какого-либо
особого оформления. Вычисляется только то значение, чьё условие совпало,
поэтому остальные значения могут ссылаться на несуществующие переменные или
вызывать функции, которые привели бы к ошибке.

Если ни одно условие в «чисто-Sass»-функции `if()` не совпадает, она
возвращает `null`.

[interpolation]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
@use 'sass:meta';

$hungry: true;
@debug if(sass($hungry): breakfast burrito; else: cereal); // breakfast burrito

// You can use CSS boolean expressions with sass(...) conditions.
@debug if(not sass($hungry): skip lunch); // null

// Only the matching branch is evaluated.
@debug if(sass(meta.variable-exists("thirsty")): thirsty; else: hungry); // hungry
```

**Sass**

```sass
@use 'sass:meta'

$hungry: true
@debug if(sass($hungry): breakfast burrito; else: cereal)  // breakfast burrito

// You can use CSS boolean expressions with sass(...) conditions.
@debug if(not sass($hungry): skip lunch)  // null

// Only the matching branch is evaluated.
@debug if(sass(meta.variable-exists("thirsty")): thirsty; else: hungry)  // hungry
```

Условия `sass(...)` также можно комбинировать с обычными условиями CSS.
Условия Sass будут вычислены самим Sass, но если остаются какие-либо условия
CSS, Sass вернёт весь результат в виде строки.

**SCSS**

```scss
$support-widescreen: true;
@debug if(
  sass($support-widescreen) and media(width >= 3000px): big;
  else: small
); // if(media(width >= 3000px): big; else: small)

// If Sass conditions mean a branch will never match (or always match), Sass
// eagerly removes that branch and returns the final value if possible.
$support-widescreen: false;
@debug if(
  sass($support-widescreen) and media(width >= 3000px): big;
  else: small
); // small
```

**Sass**

```sass
$support-widescreen: true
@debug if(
  sass($support-widescreen) and media(width >= 3000px): big;
  else: small
)  // if(media(width >= 3000px): big; else: small)

// If Sass conditions mean a branch will never match (or always match), Sass
// eagerly removes that branch and returns the final value if possible.
$support-widescreen: false
@debug if(
  sass($support-widescreen) and media(width >= 3000px): big;
  else: small
)  // small
```

## `url()`

[Функция `url()`][`url()` function] широко используется в CSS, но её синтаксис
отличается от синтаксиса других функций: она может принимать URL как в
кавычках, так и без них. Поскольку URL без кавычек не является допустимым
выражением SassScript, Sass требуется специальная логика для его разбора.

[`url()` function]: https://developer.mozilla.org/en-US/docs/Web/CSS/url

Если аргумент `url()` является допустимым URL без кавычек, Sass разбирает его
как есть, хотя для внедрения значений SassScript также можно использовать
[интерполяцию][interpolation]. Если это не допустимый URL без кавычек —
например, если он содержит [переменные][variables] или [вызовы
функций][function calls] — он разбирается как обычный [вызов простой функции
CSS][plain CSS function call].

[interpolation]: https://sass-lang.com/documentation/interpolation
[variables]: https://sass-lang.com/documentation/variables
[function calls]: https://sass-lang.com/documentation/at-rules/function
[plain CSS function call]: https://sass-lang.com/documentation/at-rules/function/#plain-css-functions

**SCSS**

```scss
$roboto-font-path: "../fonts/roboto";

@font-face {
    // This is parsed as a normal function call that takes a quoted string.
    src: url("#{$roboto-font-path}/Roboto-Thin.woff2") format("woff2");

    font-family: "Roboto";
    font-weight: 100;
}

@font-face {
    // This is parsed as a normal function call that takes an arithmetic
    // expression.
    src: url($roboto-font-path + "/Roboto-Light.woff2") format("woff2");

    font-family: "Roboto";
    font-weight: 300;
}

@font-face {
    // This is parsed as an interpolated special function.
    src: url(#{$roboto-font-path}/Roboto-Regular.woff2) format("woff2");

    font-family: "Roboto";
    font-weight: 400;
}
```

**Sass**

```sass
$roboto-font-path: "../fonts/roboto"

@font-face
    // This is parsed as a normal function call that takes a quoted string.
    src: url("#{$roboto-font-path}/Roboto-Thin.woff2") format("woff2")

    font-family: "Roboto"
    font-weight: 100


@font-face
    // This is parsed as a normal function call that takes an arithmetic
    // expression.
    src: url($roboto-font-path + "/Roboto-Light.woff2") format("woff2")

    font-family: "Roboto"
    font-weight: 300


@font-face
    // This is parsed as an interpolated special function.
    src: url(#{$roboto-font-path}/Roboto-Regular.woff2) format("woff2")

    font-family: "Roboto"
    font-weight: 400
```

**Результат CSS**

```css
@font-face {
  src: url("../fonts/roboto/Roboto-Thin.woff2") format("woff2");
  font-family: "Roboto";
  font-weight: 100;
}
@font-face {
  src: url("../fonts/roboto/Roboto-Light.woff2") format("woff2");
  font-family: "Roboto";
  font-weight: 300;
}
@font-face {
  src: url(../fonts/roboto/Roboto-Regular.woff2) format("woff2");
  font-family: "Roboto";
  font-weight: 400;
}
```

## `element()`, `progid:...()` и `expression()`

**Совместимость (calc()):** Dart Sass: начиная с 1.40.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до 1.40.0 разбирают `calc()` как
  специальную синтаксическую функцию, подобно `element()`.

  Версии Dart Sass 1.40.0 и новее разбирают `calc()` как [вычисление][calculation].

  [calculation]: https://sass-lang.com/documentation/values/calculations


**Совместимость (clamp()):** Dart Sass: начиная с >=1.31.0 <1.40.0 · LibSass: ✗ · Ruby Sass: ✗

LibSass, Ruby Sass и версии Dart Sass до 1.31.0 разбирают `clamp()` как
  [обычную функцию CSS][plain CSS function], не поддерживая внутри неё
  специальный синтаксис.

  [plain CSS function]: https://sass-lang.com/documentation/at-rules/function/#plain-css-functions

  Версии Dart Sass между 1.31.0 и 1.40.0 разбирают `clamp()` как специальную
  синтаксическую функцию, подобно `element()`.

  Версии Dart Sass 1.40.0 и новее разбирают `clamp()` как [вычисление][calculation].

  [calculation]: https://sass-lang.com/documentation/values/calculations


[Функция `element()`][`element()`] определена в спецификации CSS, и, поскольку
её идентификаторы могли бы быть разобраны как цвета, для них требуется
специальный разбор.

[`element()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/element

[`expression()`][] и функции, начинающиеся с [`progid:`][], — устаревшие
особенности Internet Explorer, использующие нестандартный синтаксис. Хотя
современные браузеры больше их не поддерживают, Sass продолжает их
разбирать для обратной совместимости.

[`expression()`]:
    https://blogs.msdn.microsoft.com/ie/2008/10/16/ending-expressions/
[`progid:`]:
    https://blogs.msdn.microsoft.com/ie/2009/02/19/the-css-corner-using-filters-in-ie8/

Sass допускает *любой текст* в этих вызовах функций, включая вложенные
скобки. Ничто не интерпретируется как выражение SassScript, за исключением
того, что [интерполяция][interpolation] может использоваться для внедрения
динамических значений.

[interpolation]: https://sass-lang.com/documentation/interpolation

**SCSS**

```scss
$logo-element: logo-bg;

.logo {
  background: element(##{$logo-element});
}
```

**Sass**

```sass
$logo-element: logo-bg

.logo
  background: element(##{$logo-element})
```

**Результат CSS**

```css
.logo {
  background: element(#logo-bg);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/syntax/special-functions/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

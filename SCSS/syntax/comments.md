# Комментарии

То, как работают комментарии в Sass, существенно различается между SCSS и
отступным синтаксисом. Оба синтаксиса поддерживают два типа комментариев:
комментарии, определённые с помощью `/* */`, которые (обычно) компилируются в
CSS, и комментарии, определённые с помощью `//`, которые не компилируются.

## В SCSS

Комментарии в SCSS работают похоже на комментарии в других языках, например в
JavaScript. **Однострочные комментарии** начинаются с `//` и продолжаются до
конца строки. Ничто в однострочном комментарии не выводится в CSS; с точки
зрения Sass, его как будто вовсе не существует. Их также называют **тихими
комментариями** (silent comments), потому что они не производят никакого CSS.

**Многострочные комментарии** начинаются с `/*` и заканчиваются на ближайшем
`*/`. Если многострочный комментарий написан там, где допустима
[инструкция][statement], он компилируется в комментарий CSS. Их также называют
**громкими комментариями** (loud comment), в противовес тихим комментариям.
Многострочный комментарий, который компилируется в CSS, может содержать
[интерполяцию][interpolation], которая будет вычислена до того, как комментарий
скомпилируется.

По умолчанию многострочные комментарии удаляются из скомпилированного CSS в
[сжатом режиме][compressed mode]. Однако если комментарий начинается с `/*!`,
он всегда будет включён в результирующий CSS.

[statement]: https://sass-lang.com/documentation/syntax/structure#statements
[interpolation]: https://sass-lang.com/documentation/interpolation
[compressed mode]: https://sass-lang.com/documentation/cli/dart-sass/#style

```scss
// This comment won't be included in the CSS.

/* But this comment will, except in compressed mode. */

/* It can also contain interpolation:
* 1 + 1 = #{1 + 1} */

/*! This comment will be included even in compressed mode. */

p /* Multi-line comments can be written anywhere
  * whitespace is allowed. */ .sans {
  font: Helvetica, // So can single-line comments.
        sans-serif;
}
```

**Результат CSS**

```css
/* But this comment will, except in compressed mode. */
/* It can also contain interpolation:
* 1 + 1 = 2 */
/*! This comment will be included even in compressed mode. */
p .sans {
  font: Helvetica, sans-serif;
}
```

## В Sass

Комментарии в отступном синтаксисе работают немного иначе: они основаны на
отступах, как и весь остальной синтаксис. Как и в SCSS, тихие комментарии,
написанные с помощью `//`, никогда не выводятся в CSS, но, в отличие от SCSS,
всё, что имеет отступ ниже открывающего `//`, тоже комментируется.

Комментарии отступного синтаксиса, начинающиеся с `/*`, работают с отступами
так же, за исключением того, что они компилируются в CSS. Поскольку протяжённость
комментария определяется отступом, закрывающий `*/` необязателен. Также, как
и в SCSS, комментарии `/*` могут содержать [интерполяцию][interpolation] и
могут начинаться с `/*!`, чтобы не удаляться в сжатом режиме.

Комментарии также можно использовать внутри [выражений][expressions] в
отступном синтаксисе. В этом случае у них точно такой же синтаксис, как и в
SCSS.

[interpolation]: https://sass-lang.com/documentation/interpolation
[expressions]: https://sass-lang.com/documentation/syntax/structure#expressions

```sass
// This comment won't be included in the CSS.
  This is also commented out.

/* But this comment will, except in compressed mode.

/* It can also contain interpolation:
  1 + 1 = #{1 + 1}

/*! This comment will be included even in compressed mode.

p .sans
  font: Helvetica, /* Inline comments must be closed. */ sans-serif
```

**Результат CSS**

```css
/* But this comment will, except in compressed mode. */
/* It can also contain interpolation:
 * 1 + 1 = 2 */
/*! This comment will be included even in compressed mode. */
p .sans {
  font: Helvetica, sans-serif;
}
```

## Комментарии для документации

При написании библиотек стилей на Sass вы можете использовать комментарии,
чтобы задокументировать [миксины][mixins], [функции][functions],
[переменные][variables] и [селекторы-заглушки][placeholder selectors],
которые предоставляет ваша библиотека, а также саму библиотеку. Эти
комментарии считываются инструментом [SassDoc][], которая использует их для
генерации красивой документации. Посмотрите документацию [движка сеток
Susy][susy], чтобы увидеть это в действии!

[mixins]: https://sass-lang.com/documentation/at-rules/mixin
[functions]: https://sass-lang.com/documentation/at-rules/function
[variables]: https://sass-lang.com/documentation/variables
[placeholder selectors]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
[SassDoc]: http://sassdoc.com
[susy]: http://oddbird.net/susy/docs/index.html

Комментарии для документации — это тихие комментарии, записанные с помощью
трёх слэшей (`///`) непосредственно над тем, что вы документируете. SassDoc
разбирает текст в комментариях как [Markdown][] и поддерживает множество
полезных [аннотаций][annotations] для его подробного описания.

[Markdown]: https://www.markdownguide.org/getting-started
[annotations]: http://sassdoc.com/annotations/

**SCSS**

```scss
/// Computes an exponent.
///
/// @param {number} $base
///   The number to multiply by itself.
/// @param {integer (unitless)} $exponent
///   The number of `$base`s to multiply together.
/// @return {number} `$base` to the power of `$exponent`.
@function pow($base, $exponent) {
  $result: 1;
  @for $_ from 1 through $exponent {
    $result: $result * $base;
  }
  @return $result;
}
```

**Sass**

```sass
/// Computes an exponent.
///
/// @param {number} $base
///   The number to multiply by itself.
/// @param {integer (unitless)} $exponent
///   The number of `$base`s to multiply together.
/// @return {number} `$base` to the power of `$exponent`.
@function pow($base, $exponent)
  $result: 1
  @for $_ from 1 through $exponent
    $result: $result * $base

  @return $result
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/syntax/comments/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

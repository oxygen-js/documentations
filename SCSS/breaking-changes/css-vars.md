# Ломающее изменение: синтаксис CSS-переменных

Старые версии LibSass и Ruby Sass разбирали объявления пользовательских свойств
точно так же, как и любые другие объявления свойств, допуская в качестве
значений весь набор [выражений SassScript](https://sass-lang.com/documentation/syntax/structure#expressions). Но это
было несовместимо с CSS.

**Совместимость:** Dart Sass: ✓ · LibSass: начиная с 3.5.0 · Ruby Sass: начиная с 3.5.0


Спецификация CSS допускает использование практически любой строки символов в
объявлении пользовательского свойства. Даже если эти значения не имеют смысла
ни для одного свойства CSS, к ним можно было обращаться из JavaScript. Когда
они разбирались как значения SassScript, синтаксис, который был бы допустимым
обычным CSS, не мог быть разобран. Например, [библиотека Polymer][] использовала
это для поддержки обычных CSS-миксинов:

[библиотека Polymer]: https://polymer-library.polymer-project.org/3.0/docs/devguide/custom-css-properties#use-custom-css-mixins

```scss
:root {
  --flex-theme: {
    border: 1px solid var(--theme-dark-blue);
    font-family: var(--theme-font-family);
    padding: var(--theme-wide-padding);
    background-color: var(--theme-light-blue);
  };
}
```

**Результат CSS**

```css
:root {
  --flex-theme: {
    border: 1px solid var(--theme-dark-blue);
    font-family: var(--theme-font-family);
    padding: var(--theme-wide-padding);
    background-color: var(--theme-light-blue);
  };
}
```

Чтобы обеспечить максимальную совместимость с обычным CSS, более новые версии
Sass требуют, чтобы выражения SassScript в значениях пользовательских свойств
записывались внутри [интерполяции](https://sass-lang.com/documentation/interpolation). Интерполяция также работает и в
более старых версиях Sass, поэтому она рекомендуется для всех таблиц стилей.

**SCSS**

```scss
$accent-color: #fbbc04;

:root {
  // WRONG, will not work in recent Sass versions.
  --accent-color-wrong: $accent-color;

  // RIGHT, will work in all Sass versions.
  --accent-color-right: #{$accent-color};
}
```

**Sass**

```sass
$accent-color: #fbbc04

:root
  // WRONG, will not work in recent Sass versions.
  --accent-color-wrong: $accent-color

  // RIGHT, will work in all Sass versions.
  --accent-color-right: #{$accent-color}
```

**Результат CSS**

```css
:root {
  --accent-color-wrong: $accent-color;
  --accent-color-right: #fbbc04;
}
```

> ⚠️ **Внимание**
>
> Поскольку интерполяция убирает кавычки из строк в кавычках, может
> понадобиться обернуть их в [функцию `meta.inspect()`], чтобы сохранить
> кавычки.

[функция `meta.inspect()`]: https://sass-lang.com/documentation/modules/meta#inspect

**SCSS**

```scss
@use "sass:meta";

$font-family-monospace: Menlo, Consolas, "Courier New", monospace;

:root {
  --font-family-monospace: #{meta.inspect($font-family-monospace)};
}
```

**Sass**

```sass
@use "sass:meta"

$font-family-monospace: Menlo, Consolas, "Courier New", monospace

:root
  --font-family-monospace: #{meta.inspect($font-family-monospace)}
```

**Результат CSS**

```css
:root {
  --font-family-monospace: Menlo, Consolas, "Courier New", monospace;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/css-vars/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

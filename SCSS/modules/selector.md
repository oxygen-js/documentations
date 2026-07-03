# sass:selector


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

На данный момент только Dart Sass поддерживает загрузку встроенных модулей с
  помощью `@use`. Пользователям других реализаций необходимо вызывать функции,
  используя их глобальные имена.



## Значения селекторов

Функции этого модуля исследуют и обрабатывают селекторы. Когда они
возвращают селектор, это всегда разделённый запятыми [список][] (список
селекторов), содержащий разделённые пробелами списки (сложные селекторы),
которые содержат [строки без кавычек][] (составные селекторы). Например,
селектор `.main aside:hover, .sidebar p` будет возвращён как:

[список]: https://sass-lang.com/documentation/values/lists
[строки без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted

```scss
@debug ((unquote(".main") unquote("aside:hover")),
        (unquote(".sidebar") unquote("p")));
// .main aside:hover, .sidebar p
```

Аргументы-селекторы, передаваемые в эти функции, могут быть в том же формате,
но также могут быть просто обычными строками (в кавычках или без), либо их
комбинацией. Например, `".main aside:hover, .sidebar p"` является допустимым
аргументом-селектором.


---


**`selector.is-superselector($super, $sub)`**

**`is-superselector($super, $sub)`**

*Возвращает: `boolean`*

  Возвращает, соответствует ли селектор `$super` всем элементам, которым
  соответствует селектор `$sub`.

  Всё равно возвращает true, даже если `$super` соответствует *большему*
  количеству элементов, чем `$sub`.

  Селекторы `$super` и `$sub` могут содержать [селекторы-заглушки][], но не
  [родительские селекторы][].

  [селекторы-заглушки]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
  [родительские селекторы]: https://sass-lang.com/documentation/style-rules/parent-selector

  **SCSS**

```scss
@use "sass:selector";

@debug selector.is-superselector("a", "a.disabled"); // true
@debug selector.is-superselector("a.disabled", "a"); // false
@debug selector.is-superselector("a", "sidebar a"); // true
@debug selector.is-superselector("sidebar a", "a"); // false
@debug selector.is-superselector("a", "a"); // true
```

**Sass**

```sass
@use "sass:selector"

@debug selector.is-superselector("a", "a.disabled")  // true
@debug selector.is-superselector("a.disabled", "a")  // false
@debug selector.is-superselector("a", "sidebar a")  // true
@debug selector.is-superselector("sidebar a", "a")  // false
@debug selector.is-superselector("a", "a")  // true
```



---


**`selector.append($selectors...)`**

**`selector-append($selectors...)`**

*Возвращает: `selector`*

  Объединяет `$selectors` без [комбинаторов потомков][] — то есть без
  пробелов между ними.

  [комбинаторов потомков]: https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_selectors

  Если какой-либо селектор в `$selectors` является списком селекторов, каждый
  сложный селектор объединяется отдельно.

  `$selectors` может содержать [селекторы-заглушки][], но не [родительские
  селекторы][].

  [селекторы-заглушки]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
  [родительские селекторы]: https://sass-lang.com/documentation/style-rules/parent-selector

  См. также [`selector.nest()`](#nest).

  **SCSS**

```scss
@use "sass:selector";

@debug selector.append("a", ".disabled"); // a.disabled
@debug selector.append(".accordion", "__copy"); // .accordion__copy
@debug selector.append(".accordion", "__copy, __image");
// .accordion__copy, .accordion__image
```

**Sass**

```sass
@use "sass:selector"

@debug selector.append("a", ".disabled")  // a.disabled
@debug selector.append(".accordion", "__copy")  // .accordion__copy
@debug selector.append(".accordion", "__copy, __image")
// .accordion__copy, .accordion__image
```



---


**`selector.extend($selector, $extendee, $extender)`**

**`selector-extend($selector, $extendee, $extender)`**

*Возвращает: `selector`*

  Расширяет `$selector` так же, как это делает [`@extend`-правило][].

  [`@extend`-правило]: https://sass-lang.com/documentation/at-rules/extend

  Возвращает копию `$selector`, изменённую с помощью следующего
  `@extend`-правила:

  ```scss
  #{$extender} {
    @extend #{$extendee};
  }
  ```

  Другими словами, заменяет все вхождения `$extendee` в `$selector` на
  `$extendee, $extender`. Если `$selector` не содержит `$extendee`,
  возвращает его без изменений.

  Селекторы `$selector`, `$extendee` и `$extender` могут содержать
  [селекторы-заглушки][], но не [родительские селекторы][].

  [селекторы-заглушки]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
  [родительские селекторы]: https://sass-lang.com/documentation/style-rules/parent-selector

  См. также [`selector.replace()`](#replace).

  **SCSS**

```scss
@use "sass:selector";

@debug selector.extend("a.disabled", "a", ".link"); // a.disabled, .link.disabled
@debug selector.extend("a.disabled", "h1", "h2"); // a.disabled
@debug selector.extend(".guide .info", ".info", ".content nav.sidebar");
// .guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar
```

**Sass**

```sass
@use "sass:selector"

@debug selector.extend("a.disabled", "a", ".link")  // a.disabled, .link.disabled
@debug selector.extend("a.disabled", "h1", "h2")  // a.disabled
@debug selector.extend(".guide .info", ".info", ".content nav.sidebar")
// .guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar
```



---


**`selector.nest($selectors...)`**

**`selector-nest($selectors...)`**

*Возвращает: `selector`*

  Объединяет `$selectors` так, как если бы они были вложены друг в друга в
  таблице стилей.

  `$selectors` может содержать [селекторы-заглушки][]. В отличие от других
  селекторных функций, все элементы, кроме первого, также могут содержать
  [родительские селекторы][].

  [селекторы-заглушки]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
  [родительские селекторы]: https://sass-lang.com/documentation/style-rules/parent-selector

  См. также [`selector.append()`](#append).

  **SCSS**

```scss
@use "sass:selector";

@debug selector.nest("ul", "li"); // ul li
@debug selector.nest(".alert, .warning", "p"); // .alert p, .warning p
@debug selector.nest(".alert", "&:hover"); // .alert:hover
@debug selector.nest(".accordion", "&__copy"); // .accordion__copy
```

**Sass**

```sass
@use "sass:selector"

@debug selector.nest("ul", "li")  // ul li
@debug selector.nest(".alert, .warning", "p")  // .alert p, .warning p
@debug selector.nest(".alert", "&:hover")  // .alert:hover
@debug selector.nest(".accordion", "&__copy")  // .accordion__copy
```



---


**`selector.parse($selector)`**

**`selector-parse($selector)`**

*Возвращает: `selector`*

  Возвращает `$selector` в формате [значения селектора](#selector-values).

  **SCSS**

```scss
@use "sass:selector";

@debug selector.parse(".main aside:hover, .sidebar p");
// ((unquote(".main") unquote("aside:hover")),
//  (unquote(".sidebar") unquote("p")))
```

**Sass**

```sass
@use "sass:selector"

@debug selector.parse(".main aside:hover, .sidebar p")
// ((unquote(".main") unquote("aside:hover")),
//  (unquote(".sidebar") unquote("p")))
```



---


**`selector.replace($selector, $original, $replacement)`**

**`selector-replace($selector, $original, $replacement)`**

*Возвращает: `selector`*

  Возвращает копию `$selector`, в которой все вхождения `$original` заменены
  на `$replacement`.

  Для этого используется [интеллектуальное объединение][]
  [`@extend`-правила][], которое гарантирует бесшовную интеграцию
  `$replacement` в `$selector`. Если `$selector` не содержит `$original`,
  возвращает его без изменений.

  [`@extend`-правила]: https://sass-lang.com/documentation/at-rules/extend
  [интеллектуальное объединение]: https://sass-lang.com/documentation/at-rules/extend#how-it-works

  Селекторы `$selector`, `$original` и `$replacement` могут содержать
  [селекторы-заглушки][], но не [родительские селекторы][].

  [селекторы-заглушки]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
  [родительские селекторы]: https://sass-lang.com/documentation/style-rules/parent-selector

  См. также [`selector.extend()`](#extend).

  **SCSS**

```scss
@use "sass:selector";

@debug selector.replace("a.disabled", "a", ".link"); // .link.disabled
@debug selector.replace("a.disabled", "h1", "h2"); // a.disabled
@debug selector.replace(".guide .info", ".info", ".content nav.sidebar");
// .guide .content nav.sidebar, .content .guide nav.sidebar
```

**Sass**

```sass
@use "sass:selector"

@debug selector.replace("a.disabled", "a", ".link")  // .link.disabled
@debug selector.replace("a.disabled", "h1", "h2")  // a.disabled
@debug selector.replace(".guide .info", ".info", ".content nav.sidebar")
// .guide .content nav.sidebar, .content .guide nav.sidebar
```



---


**`selector.unify($selector1, $selector2)`**

**`selector-unify($selector1, $selector2)`**

*Возвращает: `selector | null`*

  Возвращает селектор, который соответствует только тем элементам, которым
  соответствуют *оба* — `$selector1` и `$selector2`.

  Возвращает `null`, если `$selector1` и `$selector2` не соответствуют ни
  одному общему элементу, либо если не существует селектора, способного
  выразить их пересечение.

  Как и в случае с селекторами, генерируемыми [`@extend`-правилом][], нет
  гарантии, что возвращённый селектор соответствует *всем* элементам, которым
  соответствуют оба селектора — `$selector1` и `$selector2` — если оба они
  являются сложными селекторами.

  [`@extend`-правилом]: https://sass-lang.com/documentation/at-rules/extend#html-heuristics

  **SCSS**

```scss
@use "sass:selector";

@debug selector.unify("a", ".disabled"); // a.disabled
@debug selector.unify("a.disabled", "a.outgoing"); // a.disabled.outgoing
@debug selector.unify("a", "h1"); // null
@debug selector.unify(".warning a", "main a"); // .warning main a, main .warning a
```

**Sass**

```sass
@use "sass:selector"

@debug selector.unify("a", ".disabled")  // a.disabled
@debug selector.unify("a.disabled", "a.outgoing")  // a.disabled.outgoing
@debug selector.unify("a", "h1")  // null
@debug selector.unify(".warning a", "main a")  // .warning main a, main .warning a
```



---


**`selector.simple-selectors($selector)`**

**`simple-selectors($selector)`**

*Возвращает: `list`*

  Возвращает список простых селекторов в `$selector`.

  `$selector` должен быть одиночной строкой, содержащей составной селектор.
  Это означает, что он не может содержать комбинаторы (включая пробелы) или
  запятые.

  Возвращаемый список разделён запятыми, а простые селекторы представлены
  строками без кавычек.

  **SCSS**

```scss
@use "sass:selector";

@debug selector.simple-selectors("a.disabled"); // a, .disabled
@debug selector.simple-selectors("main.blog:after"); // main, .blog, :after
```

**Sass**

```sass
@use "sass:selector"

@debug selector.simple-selectors("a.disabled")  // a, .disabled
@debug selector.simple-selectors("main.blog:after")  // main, .blog, :after
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/selector/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

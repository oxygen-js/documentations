# sass:string


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass в настоящее время поддерживает загрузку встроенных модулей с помощью `@use`. Пользователи
  других реализаций должны вызывать функции, используя их глобальные имена.




---


**`string.quote($string)`**

**`quote($string)`**

*Возвращает: `string`*

  Возвращает `$string` в виде строки в кавычках.

  **SCSS**

```scss
@use "sass:string";

@debug string.quote(Helvetica); // "Helvetica"
@debug string.quote("Helvetica"); // "Helvetica"
```

**Sass**

```sass
@use "sass:string"

@debug string.quote(Helvetica)  // "Helvetica"
@debug string.quote("Helvetica")  // "Helvetica"
```



---


**`string.index($string, $substring)`**

**`str-index($string, $substring)`**

*Возвращает: `number`*

  Возвращает первый [индекс][] `$substring` в `$string`, или `null`, если
  `$string` не содержит `$substring`.

  [индекс]: https://sass-lang.com/documentation/values/strings#string-indexes

  **SCSS**

```scss
@use "sass:string";

@debug string.index("Helvetica Neue", "Helvetica"); // 1
@debug string.index("Helvetica Neue", "Neue"); // 11
```

**Sass**

```sass
@use "sass:string"

@debug string.index("Helvetica Neue", "Helvetica")  // 1
@debug string.index("Helvetica Neue", "Neue")  // 11
```



---


**`string.insert($string, $insert, $index)`**

**`str-insert($string, $insert, $index)`**

*Возвращает: `string`*

  Возвращает копию `$string` со вставленным `$insert` по позиции [`$index`][].

  [`$index`]: https://sass-lang.com/documentation/values/strings#string-indexes

  **SCSS**

```scss
@use "sass:string";

@debug string.insert("Roboto Bold", " Mono", 7); // "Roboto Mono Bold"
@debug string.insert("Roboto Bold", " Mono", -6); // "Roboto Mono Bold"
```

**Sass**

```sass
@use "sass:string"

@debug string.insert("Roboto Bold", " Mono", 7)  // "Roboto Mono Bold"
@debug string.insert("Roboto Bold", " Mono", -6)  // "Roboto Mono Bold"
```

  Если `$index` больше длины `$string`, `$insert` добавляется в
  конец. Если `$index` меньше отрицательной длины строки, `$insert`
  добавляется в начало.

  **SCSS**

```scss
@use "sass:string";

@debug string.insert("Roboto", " Bold", 100); // "Roboto Bold"
@debug string.insert("Bold", "Roboto ", -100); // "Roboto Bold"
```

**Sass**

```sass
@use "sass:string"

@debug string.insert("Roboto", " Bold", 100)  // "Roboto Bold"
@debug string.insert("Bold", "Roboto ", -100)  // "Roboto Bold"
```



---


**`string.length($string)`**

**`str-length($string)`**

*Возвращает: `number`*

  Возвращает количество символов в `$string`.

  **SCSS**

```scss
@use "sass:string";

@debug string.length("Helvetica Neue"); // 14
@debug string.length(bold); // 4
@debug string.length(""); // 0
```

**Sass**

```sass
@use "sass:string"

@debug string.length("Helvetica Neue")  // 14
@debug string.length(bold)  // 4
@debug string.length("")  // 0
```



---


**`string.slice($string, $start-at, $end-at: -1)`**

**`str-slice($string, $start-at, $end-at: -1)`**

*Возвращает: `string`*

  Возвращает срез `$string`, начинающийся с [индекса][] `$start-at` и заканчивающийся
  индексом `$end-at` (оба включительно).

  [индекса]: https://sass-lang.com/documentation/values/strings#string-indexes

  **SCSS**

```scss
@use "sass:string";

@debug string.slice("Helvetica Neue", 11); // "Neue"
@debug string.slice("Helvetica Neue", 1, 3); // "Hel"
@debug string.slice("Helvetica Neue", 1, -6); // "Helvetica"
```

**Sass**

```sass
@use "sass:string"

@debug string.slice("Helvetica Neue", 11)  // "Neue"
@debug string.slice("Helvetica Neue", 1, 3)  // "Hel"
@debug string.slice("Helvetica Neue", 1, -6)  // "Helvetica"
```



---


**`string.split($string, $separator, $limit: null)`**

*Возвращает: `list`*

  **Совместимость:** Dart Sass: начиная с 1.57.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает заключённый в скобки список через запятую из подстрок `$string`,
  разделённых `$separator`. Сами `$separator` в эти
  подстроки не включаются.

  Если `$limit` — число `1` или больше, разбиение выполняется не более чем по
  такому количеству `$separator` (и, соответственно, возвращает не более `$limit + 1` строк).
  Последняя подстрока содержит остаток строки, включая любые
  оставшиеся `$separator`.

  **SCSS**

```scss
@use "sass:string";

@debug string.split("Segoe UI Emoji", " "); // ["Segoe", "UI", "Emoji"]
@debug string.split("Segoe UI Emoji", " ", $limit: 1); // ["Segoe", "UI Emoji"]
```

**Sass**

```sass
@use "sass:string"

@debug string.split("Segoe UI Emoji", " ")  // ["Segoe", "UI", "Emoji"]
@debug string.split("Segoe UI Emoji", " ", $limit: 1)  // ["Segoe", "UI Emoji"]
```



---


**`string.to-upper-case($string)`**

**`to-upper-case($string)`**

*Возвращает: `string`*

  Возвращает копию `$string` с [ASCII][]-буквами, преобразованными в верхний
  регистр.

  [ASCII]: https://en.wikipedia.org/wiki/ASCII

  **SCSS**

```scss
@use "sass:string";

@debug string.to-upper-case("Bold"); // "BOLD"
@debug string.to-upper-case(sans-serif); // SANS-SERIF
```

**Sass**

```sass
@use "sass:string"

@debug string.to-upper-case("Bold")  // "BOLD"
@debug string.to-upper-case(sans-serif)  // SANS-SERIF
```



---


**`string.to-lower-case($string)`**

**`to-lower-case($string)`**

*Возвращает: `string`*

  Возвращает копию `$string` с [ASCII][]-буквами, преобразованными в нижний
  регистр.

  [ASCII]: https://en.wikipedia.org/wiki/ASCII

  **SCSS**

```scss
@use "sass:string";

@debug string.to-lower-case("Bold"); // "bold"
@debug string.to-lower-case(SANS-SERIF); // sans-serif
```

**Sass**

```sass
@use "sass:string"

@debug string.to-lower-case("Bold")  // "bold"
@debug string.to-lower-case(SANS-SERIF)  // sans-serif
```



---


**`string.unique-id()`**

**`unique-id()`**

*Возвращает: `string`*

  Возвращает случайно сгенерированную строку без кавычек, которая гарантированно является допустимым
  CSS-идентификатором и уникальна в пределах текущей компиляции Sass.

  **SCSS**

```scss
@use "sass:string";

@debug string.unique-id(); // uabtrnzug
@debug string.unique-id(); // u6w1b1def
```

**Sass**

```sass
@use "sass:string"

@debug string.unique-id(); // uabtrnzug
@debug string.unique-id(); // u6w1b1def
```



---


**`string.unquote($string)`**

**`unquote($string)`**

*Возвращает: `string`*

  Возвращает `$string` в виде строки без кавычек. Это может создавать строки, не являющиеся
  корректным CSS, поэтому используйте с осторожностью.

  **SCSS**

```scss
@use "sass:string";

@debug string.unquote("Helvetica"); // Helvetica
@debug string.unquote(".widget:hover"); // .widget:hover
```

**Sass**

```sass
@use "sass:string"

@debug string.unquote("Helvetica")  // Helvetica
@debug string.unquote(".widget:hover")  // .widget:hover
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/string/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# sass:map


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass в настоящее время поддерживает загрузку встроенных модулей с помощью `@use`. Пользователям других реализаций вместо этого нужно вызывать функции по их глобальным именам.



> 💡 **Занятный факт**
>
> Библиотеки Sass и дизайн-системы часто делятся конфигурациями и переопределяют их — такие конфигурации представлены в виде вложенных карт (map): карт, которые содержат карты, которые содержат карты.
>
> Чтобы помочь вам работать со вложенными картами, некоторые функции для карт поддерживают глубокие операции. Например, если передать в `map.get()` несколько ключей, функция пройдёт по этим ключам, чтобы найти нужную вложенную карту:
>
> **SCSS**
>
> ```scss
> @use "sass:map";
>
> $config: (a: (b: (c: d)));
> @debug map.get($config, a, b, c); // d
> ```
>
> **Sass**
>
> ```sass
> @use "sass:map"
>
> $config: (a: (b: (c: d)))
> @debug map.get($config, a, b, c) // d
> ```


---


**`map.deep-merge($map1, $map2)`**

*Возвращает: `map`*

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗


  Идентична [`map.merge()`](#merge), за исключением того, что вложенные значения-карты *также* объединяются рекурсивно.

  **SCSS**

```scss
@use "sass:map";

$helvetica-light: (
  "weights": (
    "lightest": 100,
    "light": 300
  )
);
$helvetica-heavy: (
  "weights": (
    "medium": 500,
    "bold": 700
  )
);

@debug map.deep-merge($helvetica-light, $helvetica-heavy);
// (
//   "weights": (
//     "lightest": 100,
//     "light": 300,
//     "medium": 500,
//     "bold": 700
//   )
// )
@debug map.merge($helvetica-light, $helvetica-heavy);
// (
//   "weights": (
//     "medium: 500,
//     "bold": 700
//   )
// )
```

**Sass**

```sass
@use "sass:map"

$helvetica-light: ("weights": ("lightest": 100, "light": 300))
$helvetica-heavy: ("weights": ("medium": 500, "bold": 700))

@debug map.deep-merge($helvetica-light, $helvetica-heavy)
// (
//   "weights": (
//     "lightest": 100,
//     "light": 300,
//     "medium": 500,
//     "bold": 700
//   )
// )
@debug map.merge($helvetica-light, $helvetica-heavy);
// (
//   "weights": (
//     "medium: 500,
//     "bold": 700
//   )
// )
```



---


**`map.deep-remove($map, $key, $keys...)`**

*Возвращает: `map`*

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗


  Если `$keys` пуст, возвращает копию `$map` без значения, связанного с `$key`.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.deep-remove($font-weights, "regular");
// ("medium": 500, "bold": 700)
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.deep-remove($font-weights, "regular")
// ("medium": 500, "bold": 700)
```

  ---

  Если `$keys` не пуст, идёт по набору ключей, включающему `$key` и исключающему последний ключ в `$keys`, слева направо, чтобы найти вложенную карту, предназначенную для обновления.

  Возвращает копию `$map`, в которой у целевой карты отсутствует значение, связанное с последним ключом в `$keys`.

  **SCSS**

```scss
@use "sass:map";

$fonts: (
  "Helvetica": (
    "weights": (
      "regular": 400,
      "medium": 500,
      "bold": 700
    )
  )
);

@debug map.deep-remove($fonts, "Helvetica", "weights", "regular");
// (
//   "Helvetica": (
//     "weights: (
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```

**Sass**

```sass
@use "sass:map"

$fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

@debug map.deep-remove($fonts, "Helvetica", "weights", "regular")
// (
//   "Helvetica": (
//     "weights: (
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```



---


**`map.get($map, $key, $keys...)`**

**`map-get($map, $key, $keys...)`**

  Если `$keys` пуст, возвращает значение в `$map`, связанное с `$key`.

  Если у `$map` нет значения, связанного с `$key`, возвращается [`null`][].

  [`null`]: https://sass-lang.com/documentation/values/null

  **SCSS**

```scss
@use "sass:map";
$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.get($font-weights, "medium"); // 500
@debug map.get($font-weights, "extra-bold"); // null
```

**Sass**

```sass
@use "sass:map"
$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.get($font-weights, "medium")  // 500
@debug map.get($font-weights, "extra-bold")  // null
```


  ---

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass поддерживает вызов `map.get()` с более чем двумя аргументами.


  Если `$keys` не пуст, идёт по набору ключей, включающему `$key` и исключающему последний ключ в `$keys`, слева направо, чтобы найти вложенную карту, предназначенную для поиска.

  Возвращает значение в целевой карте, связанное с последним ключом в `$keys`.

  Возвращает [`null`][], если карта не содержит значения, связанного с этим ключом, либо если какой-либо ключ из `$keys` отсутствует в карте или ссылается на значение, не являющееся картой.

  [`null`]: https://sass-lang.com/documentation/values/null

  **SCSS**

```scss
@use "sass:map";

$fonts: (
  "Helvetica": (
    "weights": (
      "regular": 400,
      "medium": 500,
      "bold": 700
    )
  )
);

@debug map.get($fonts, "Helvetica", "weights", "regular"); // 400
@debug map.get($fonts, "Helvetica", "colors"); // null
```

**Sass**

```sass
@use "sass:map"

$fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

@debug map.get($fonts, "Helvetica", "weights", "regular") // 400
@debug map.get($fonts, "Helvetica", "colors") // null
```



---


**`map.has-key($map, $key, $keys...)`**

**`map-has-key($map, $key, $keys...)`**

*Возвращает: `boolean`*

  Если `$keys` пуст, возвращает, содержит ли `$map` значение, связанное с `$key`.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.has-key($font-weights, "regular"); // true
@debug map.has-key($font-weights, "bolder"); // false
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.has-key($font-weights, "regular") // true
@debug map.has-key($font-weights, "bolder") // false
```

  ---

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass поддерживает вызов `map.has-key()` с более чем двумя
    аргументами.


  Если `$keys` не пуст, идёт по набору ключей, включающему `$key` и исключающему последний ключ в `$keys`, слева направо, чтобы найти вложенную карту, предназначенную для поиска.

  Возвращает true, если целевая карта содержит значение, связанное с последним ключом в `$keys`.

  Возвращает false, если это не так, либо если какой-либо ключ из `$keys` отсутствует в карте или ссылается на значение, не являющееся картой.

  **SCSS**

```scss
@use "sass:map";

$fonts: (
  "Helvetica": (
    "weights": (
      "regular": 400,
      "medium": 500,
      "bold": 700
    )
  )
);

@debug map.has-key($fonts, "Helvetica", "weights", "regular"); // true
@debug map.has-key($fonts, "Helvetica", "colors"); // false
```

**Sass**

```sass
@use "sass:map"

$fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

@debug map.has-key($fonts, "Helvetica", "weights", "regular") // true
@debug map.has-key($fonts, "Helvetica", "colors") // false
```



---


**`map.keys($map)`**

**`map-keys($map)`**

*Возвращает: `list`*

  Возвращает список через запятую из всех ключей `$map`.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.keys($font-weights); // "regular", "medium", "bold"
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.keys($font-weights)  // "regular", "medium", "bold"
```



---


**`map.merge($map1, $map2)`**

**`map-merge($map1, $map2)`**

**`map.merge($map1, $keys..., $map2)`**

**`map-merge($map1, $keys..., $map2)`**

*Возвращает: `map`*

  > ⚠️ **Внимание**
  >
  > На практике фактические аргументы `map.merge($map1, $keys..., $map2)` передаются как `map.merge($map1, $args...)`. Здесь они описаны как `$map1, $keys..., $map2` исключительно в целях объяснения.

  Если `$keys` не переданы, возвращает новую карту со всеми ключами и значениями из `$map1` и `$map2`.

  Если и `$map1`, и `$map2` содержат один и тот же ключ, приоритет имеет значение из `$map2`.

  Все ключи в результирующей карте, которые также присутствуют в `$map1`, идут в том же порядке, что и в `$map1`. Новые ключи из `$map2` добавляются в конец карты.

  **SCSS**

```scss
@use "sass:map";

$light-weights: ("lightest": 100, "light": 300);
$heavy-weights: ("medium": 500, "bold": 700);

@debug map.merge($light-weights, $heavy-weights);
// ("lightest": 100, "light": 300, "medium": 500, "bold": 700)
```

**Sass**

```sass
@use "sass:map"

$light-weights: ("lightest": 100, "light": 300)
$heavy-weights: ("medium": 500, "bold": 700)

@debug map.merge($light-weights, $heavy-weights)
// ("lightest": 100, "light": 300, "medium": 500, "bold": 700)
```

  ---

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass поддерживает вызов `map.merge()` с более чем двумя аргументами.


  Если `$keys` не пуст, идёт по `$keys`, чтобы найти вложенную карту, предназначенную для слияния. Если какой-либо ключ из `$keys` отсутствует в карте или ссылается на значение, не являющееся картой, значение по этому ключу устанавливается в пустую карту.

  Возвращает копию `$map1`, в которой целевая карта заменена новой картой, содержащей все ключи и значения как из целевой карты, так и из `$map2`.

  **SCSS**

```scss
@use "sass:map";

$fonts: (
  "Helvetica": (
    "weights": (
      "lightest": 100,
      "light": 300
    )
  )
);
$heavy-weights: ("medium": 500, "bold": 700);

@debug map.merge($fonts, "Helvetica", "weights", $heavy-weights);
// (
//   "Helvetica": (
//     "weights": (
//       "lightest": 100,
//       "light": 300,
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```

**Sass**

```sass
@use "sass:map"

$fonts: ("Helvetica": ("weights": ("lightest": 100, "light": 300)))
$heavy-weights: ("medium": 500, "bold": 700)

@debug map.merge($fonts, "Helvetica", "weights", $heavy-weights)
// (
//   "Helvetica": (
//     "weights": (
//       "lightest": 100,
//       "light": 300,
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```



---


**`map.remove($map, $keys...)`**

**`map-remove($map, $keys...)`**

*Возвращает: `map`*

  Возвращает копию `$map` без значений, связанных с `$keys`.

  Если у ключа из `$keys` нет соответствующего значения в `$map`, он игнорируется.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.remove($font-weights, "regular"); // ("medium": 500, "bold": 700)
@debug map.remove($font-weights, "regular", "bold"); // ("medium": 500)
@debug map.remove($font-weights, "bolder");
// ("regular": 400, "medium": 500, "bold": 700)
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.remove($font-weights, "regular")  // ("medium": 500, "bold": 700)
@debug map.remove($font-weights, "regular", "bold")  // ("medium": 500)
@debug map.remove($font-weights, "bolder")
// ("regular": 400, "medium": 500, "bold": 700)
```



---


**`map.set($map, $key, $value)`**

**`map.set($map, $keys..., $key, $value)`**

*Возвращает: `map`*

  > ⚠️ **Внимание**
  >
  > На практике фактические аргументы `map.set($map, $keys..., $key, $value)` передаются как `map.set($map, $args...)`. Здесь они описаны как `$map, $keys..., $key, $value` исключительно в целях объяснения.

  Если `$keys` не переданы, возвращает копию `$map`, в которой значение по `$key` установлено в `$value`.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.set($font-weights, "regular", 300);
// ("regular": 300, "medium": 500, "bold": 700)
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.set($font-weights, "regular", 300)
// ("regular": 300, "medium": 500, "bold": 700)
```

  ---

  **Совместимость:** Dart Sass: начиная с 1.27.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass поддерживает вызов `map.set()` с более чем тремя аргументами.


  Если `$keys` переданы, идёт по `$keys`, чтобы найти вложенную карту, предназначенную для обновления. Если какой-либо ключ из `$keys` отсутствует в карте или ссылается на значение, не являющееся картой, значение по этому ключу устанавливается в пустую карту.

  Возвращает копию `$map`, в которой у целевой карты значение по `$key` установлено в `$value`.

  **SCSS**

```scss
@use "sass:map";

$fonts: (
  "Helvetica": (
    "weights": (
      "regular": 400,
      "medium": 500,
      "bold": 700
    )
  )
);

@debug map.set($fonts, "Helvetica", "weights", "regular", 300);
// (
//   "Helvetica": (
//     "weights": (
//       "regular": 300,
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```

**Sass**

```sass
@use "sass:map"

$fonts: ("Helvetica": ("weights": ("regular": 400, "medium": 500, "bold": 700)))

@debug map.set($fonts, "Helvetica", "weights", "regular", 300)
// (
//   "Helvetica": (
//     "weights": (
//       "regular": 300,
//       "medium": 500,
//       "bold": 700
//     )
//   )
// )
```



---


**`map.values($map)`**

**`map-values($map)`**

*Возвращает: `list`*

  Возвращает список через запятую из всех значений `$map`.

  **SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.values($font-weights); // 400, 500, 700
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.values($font-weights)  // 400, 500, 700
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/map/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

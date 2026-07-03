# sass:color


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

В настоящее время загрузку встроенных модулей через `@use` поддерживает только Dart Sass.
  Пользователям других реализаций необходимо вызывать функции по их глобальным именам.




---


```scss
color.adjust($color,
  $red: null, $green: null, $blue: null,
  $hue: null, $saturation: null, $lightness: null,
  $whiteness: null, $blackness: null,
  $x: null, $y: null, $z: null,
  $chroma: null,
  $alpha: null,
  $space: null)
```

**`adjust-color(...)`**

*Возвращает: `color`*

  **Совместимость (`$x`, `$y`, `$z`, `$chroma` и `$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗

  **Совместимость (`$whiteness` и `$blackness`):** Dart Sass: начиная с 1.28.0 · LibSass: ✗ · Ruby Sass: ✗


  Увеличивает или уменьшает один или несколько каналов `$color` на фиксированные значения.

  Прибавляет значение, переданное для каждого именованного аргумента, к соответствующему
  каналу цвета, и возвращает изменённый цвет. По умолчанию можно изменять только
  каналы в пространстве `$color`, но можно передать другое цветовое пространство в качестве
  `$space`, чтобы изменять каналы в нём. Функция всегда возвращает цвет в том же
  пространстве, что и `$color`.

  > ⚠️ **Внимание**
  >
  > По историческим причинам, если `$color` находится в [устаревшем цветовом пространстве], можно
  > изменять _любые_ каналы устаревших цветовых пространств. Однако является ошибкой
  > указывать RGB-канал (`$red`, `$green` и/или `$blue`) одновременно
  > с HSL-каналом (`$hue`, `$saturation` и/или `$lightness`) либо любой из них
  > одновременно с каналом [HWB] (`$hue`, `$whiteness` и/или
  > `$blackness`).
  >
  > Тем не менее рекомендуется явно указывать `$space` даже для устаревших цветов.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces
  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  Все аргументы каналов должны быть числами с единицами измерения, допустимыми
  для этих каналов в конструкторе цветового пространства. Если сумма текущего значения
  канала и значения изменения выходит за пределы допустимого диапазона канала,
  значение ограничивается для:
  
  * красного, зелёного и синего каналов пространства `rgb`;
  * канала светлоты пространств `lab`, `lch`, `oklab` и `oklch`;
  * нижней границы каналов насыщенности и цветности пространств `hsl`, `lch`
    и `oklch`;
  * и альфа-канала для всех пространств.

  Смотрите также:

  * [`color.scale()`](#scale) — для плавного масштабирования свойств цвета.
  * [`color.change()`](#change) — для установки свойств цвета.

  **SCSS**

```scss
@use 'sass:color';

@debug color.adjust(#6b717f, $red: 15); // #7a717f
@debug color.adjust(lab(40% 30 40), $lightness: 10%, $a: -20); // lab(50% 10 40)
@debug color.adjust(#d2e1dd, $hue: 45deg, $space: oklch);
// rgb(209.7987626149, 223.8632000471, 229.3988769575)
```

**Sass**

```sass
@use 'sass:color'

@debug color.adjust(#6b717f, $red: 15)  // #7a717f
@debug color.adjust(lab(40% 30 40), $lightness: 10%, $a: -20)  // lab(50% 10 40)
@debug color.adjust(#d2e1dd, $hue: 45deg, $space: oklch)
// rgb(209.7987626149, 223.8632000471, 229.3988769575)
```




---


```scss
color.change($color,
  $red: null, $green: null, $blue: null,
  $hue: null, $saturation: null, $lightness: null,
  $whiteness: null, $blackness: null,
  $x: null, $y: null, $z: null,
  $chroma: null,
  $alpha: null,
  $space: null)
```

**`change-color(...)`**

*Возвращает: `color`*

  **Совместимость (`$x`, `$y`, `$z`, `$chroma` и `$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗

  **Совместимость (`$whiteness` и `$blackness`):** Dart Sass: начиная с 1.28.0 · LibSass: ✗ · Ruby Sass: ✗


  Устанавливает один или несколько каналов цвета в новые значения.

  Использует значение, переданное для каждого именованного аргумента, вместо соответствующего
  канала цвета, и возвращает изменённый цвет. По умолчанию можно изменять только
  каналы в пространстве `$color`, но можно передать другое цветовое пространство в качестве
  `$space`, чтобы изменять каналы в нём. Функция всегда возвращает цвет в том же
  пространстве, что и `$color`.

  > ⚠️ **Внимание**
  >
  > По историческим причинам, если `$color` находится в [устаревшем цветовом пространстве], можно
  > изменять _любые_ каналы устаревших цветовых пространств. Однако является ошибкой
  > указывать RGB-канал (`$red`, `$green` и/или `$blue`) одновременно
  > с HSL-каналом (`$hue`, `$saturation` и/или `$lightness`) либо любой из них
  > одновременно с каналом [HWB] (`$hue`, `$whiteness` и/или
  > `$blackness`).
  >
  > Тем не менее рекомендуется явно указывать `$space` даже для устаревших цветов.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces
  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  Все аргументы каналов должны быть числами с единицами измерения, допустимыми
  для этих каналов в конструкторе цветового пространства. Каналы никогда не
  ограничиваются для `color.change()`.

  Смотрите также:

  * [`color.scale()`](#scale) — для плавного масштабирования свойств цвета.
  * [`color.adjust()`](#adjust) — для изменения свойств цвета на фиксированные
  величины.

  **SCSS**

```scss
@use 'sass:color';

@debug color.change(#6b717f, $red: 100); // #64717f
@debug color.change(color(srgb 0 0.2 0.4), $red: 0.8, $blue: 0.1);
// color(srgb 0.8 0.2 0.1)
@debug color.change(#998099, $lightness: 30%, $space: oklch);
// rgb(58.0719961509, 37.2631531594, 58.4201613409)
```

**Sass**

```sass
@use 'sass:color'

@debug color.change(#6b717f, $red: 100)  // #64717f
@debug color.change(color(srgb 0 0.2 0.4), $red: 0.8, $blue: 0.1)
// color(srgb 0.8 0.2 0.1)
@debug color.change(#998099, $lightness: 30%, $space: oklch)
// rgb(58.0719961509, 37.2631531594, 58.4201613409)
```



---


**`color.channel($color, $channel, $space: null)`**

*Возвращает: `number`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает значение `$channel` в `$space`, которое по умолчанию равно пространству `$color`.
  `$channel` должен быть строкой в кавычках, а `$space` — строкой без кавычек.

  Возвращает число с единицей `deg` для канала `hue` пространств `hsl`,
  `hwb`, `lch` и `oklch`. Возвращает число с единицей `%` для каналов
  `saturation`, `lightness`, `whiteness` и `blackness` пространств `hsl`,
  `hwb`, `lab`, `lch`, `oklab` и `oklch`. Для всех остальных каналов
  возвращает число без единицы измерения.

  Вернёт `0` (возможно, с соответствующей единицей измерения), если `$channel`
  отсутствует в `$color`. Вы можете использовать [`color.is-missing()`], чтобы явно
  проверить отсутствующие каналы.

  [`color.is-missing()`]: #is-missing

  **SCSS**

```scss
@use 'sass:color';

@debug color.channel(hsl(80deg 30% 50%), "hue"); // 80deg
@debug color.channel(hsl(80deg 30% 50%), "hue", $space: oklch); // 124.279238779deg
@debug color.channel(hsl(80deg 30% 50%), "red", $space: rgb); // 140.25
```

**Sass**

```sass
@use 'sass:color'

@debug color.channel(hsl(80deg 30% 50%), "hue")  // 80deg
@debug color.channel(hsl(80deg 30% 50%), "hue", $space: oklch)  // 124.279238779deg
@debug color.channel(hsl(80deg 30% 50%), "red", $space: rgb)  // 140.25
```



---


**`color.complement($color, $space: null)`**

**`complement($color, $space: null)`**

*Возвращает: `color`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает [дополнительный цвет] для `$color` в `$space`.

  [дополнительный цвет]: https://en.wikipedia.org/wiki/Complementary_colors

  Поворачивает оттенок `$color` на `180deg` в `$space`. Это значит, что `$space`
  должно быть полярным цветовым пространством: `hsl`, `hwb`, `lch` или `oklch`. Функция
  всегда возвращает цвет в том же пространстве, что и `$color`.

  > ⚠️ **Внимание**
  >
  > По историческим причинам `$space` необязателен, если `$color` находится в
  > [устаревшем цветовом пространстве]. В этом случае `$space` по умолчанию равен `hsl`. В любом
  > случае рекомендуется указывать `$space` явно.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  **SCSS**

```scss
@use 'sass:color';

// HSL hue 222deg becomes 42deg.
@debug color.complement(#6b717f); // #7f796b

// Oklch hue 267.1262408996deg becomes 87.1262408996deg
@debug color.complement(#6b717f, oklch);
// rgb(118.8110604298, 112.5123650034, 98.1616586336)

// Hue 70deg becomes 250deg.
@debug color.complement(oklch(50% 0.12 70deg), oklch); // oklch(50% 0.12 250deg)
```

**Sass**

```sass
@use 'sass:color'

// HSL hue 222deg becomes 42deg.
@debug color.complement(#6b717f)  // #7f796b

// Oklch hue 267.1262408996deg becomes 87.1262408996deg
@debug color.complement(#6b717f, oklch) 
// rgb(118.8110604298, 112.5123650034, 98.1616586336)

// Hue 70deg becomes 250deg.
@debug color.complement(oklch(50% 0.12 70deg), oklch)  // oklch(50% 0.12 250deg)
```



---


**`color.grayscale($color)`**

**`grayscale($color)`**

*Возвращает: `color`*

  Возвращает серый цвет с той же светлотой, что и `$color`.

  Если `$color` находится в [устаревшем цветовом пространстве], устанавливает насыщенность HSL в 0%.
  В противном случае устанавливает цветность Oklch в 0%.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  **SCSS**

```scss
@use 'sass:color';

@debug color.grayscale(#6b717f); // #757575
@debug color.grayscale(color(srgb 0.4 0.2 0.6)); // color(srgb 0.3233585271 0.3233585411 0.3233585792)
@debug color.grayscale(oklch(50% 80% 270deg)); // oklch(50% 0% 270deg)
```

**Sass**

```sass
@use 'sass:color'

@debug color.grayscale(#6b717f)  // #757575
@debug color.grayscale(color(srgb 0.4 0.2 0.6))  // color(srgb 0.3233585271 0.3233585411 0.3233585792)
@debug color.grayscale(oklch(50% 80% 270deg))  // oklch(50% 0% 270deg)
```



---


**`color.ie-hex-str($color)`**

**`ie-hex-str($color)`**

*Возвращает: `unquoted string`*

  Возвращает строку без кавычек, представляющую `$color` в формате `#AARRGGBB`,
  который ожидает свойство [`-ms-filter`] Internet Explorer.

  [`-ms-filter`]: https://learn.microsoft.com/en-us/previous-versions/ms530752(v=vs.85)

  Если `$color` ещё не находится в пространстве `rgb`, он преобразуется в `rgb`
  и при необходимости приводится к нужному цветовому охвату (gamut). Конкретный алгоритм
  приведения к охвату может измениться в будущих версиях Sass по мере развития современных
  подходов; в настоящий момент используется [`local-minde`].

  [`local-minde`]: #to-gamut

  **SCSS**

```scss
@use 'sass:color';

@debug color.ie-hex-str(#b37399); // #FFB37399
@debug color.ie-hex-str(rgba(242, 236, 228, 0.6)); // #99F2ECE4
@debug color.ie-hex-str(oklch(70% 10% 120deg)); // #FF9BA287
```

**Sass**

```sass
@use 'sass:color'

@debug color.ie-hex-str(#b37399)  // #FFB37399
@debug color.ie-hex-str(rgba(242, 236, 228, 0.6))  // #99F2ECE4
@debug color.ie-hex-str(oklch(70% 10% 120deg))  // #FF9BA287
```



---


**`color.invert($color, $weight: 100%, $space: null)`**

**`invert($color, $weight: 100%, $space: null)`**

*Возвращает: `color`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает инверсию, или [негатив], `$color` в `$space`.

  [негатив]: https://en.wikipedia.org/wiki/Negative_(photography)

  `$weight` должен быть числом от `0%` до `100%` (включительно). Чем больше
  вес, тем ближе результат к негативу, а чем меньше вес, тем ближе он к
  `$color`. Вес `50%` всегда даст серый цвет со средней светлотой в `$space`.
  
  > ⚠️ **Внимание**
  >
  > По историческим причинам `$space` необязателен, если `$color` находится в
  > [устаревшем цветовом пространстве]. В этом случае `$space` по умолчанию равен собственному
  > пространству `$color`. В любом случае рекомендуется указывать `$space` явно.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  **SCSS**

```scss
@use 'sass:color';

@debug color.invert(#b37399, $space: rgb); // #4c8c66
@debug color.invert(#550e0c, 20%, $space: display-p3); // rgb(103.4937692017, 61.3720912206, 59.430641338)
```

**Sass**

```sass
@use 'sass:color';

@debug color.invert(#b37399, $space: rgb)  // #4c8c66
@debug color.invert(#550e0c, 20%, $space: display-p3)  // rgb(103.4937692017, 61.3720912206, 59.430641338)
```



---


**`color.is-legacy($color)`**

*Возвращает: `boolean`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает, находится ли `$color` в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  **SCSS**

```scss
@use 'sass:color';

@debug color.is-legacy(#b37399); // true
@debug color.is-legacy(hsl(90deg 30% 90%)); // true
@debug color.is-legacy(oklch(70% 10% 120deg)); // false
```

**Sass**

```sass
@use 'sass:color'

@debug color.is-legacy(#b37399)  // true
@debug color.is-legacy(hsl(90deg 30% 90%))  // true
@debug color.is-legacy(oklch(70% 10% 120deg))  // false
```



---


**`color.is-missing($color, $channel)`**

*Возвращает: `boolean`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает, [отсутствует] ли `$channel` в `$color`. `$channel` должен быть
  строкой в кавычках.

  [отсутствует]: https://sass-lang.com/documentation/values/colors#missing-channels

  **SCSS**

```scss
@use 'sass:color';

@debug color.is-missing(#b37399, "green"); // false
@debug color.is-missing(rgb(100 none 200), "green"); // true
@debug color.is-missing(color.to-space(grey, lch), "hue"); // true
```

**Sass**

```sass
@use 'sass:color'

@debug color.is-legacy(#b37399)  // true
@debug color.is-legacy(hsl(90deg 30% 90%))  // true
@debug color.is-legacy(oklch(70% 10% 120deg))  // false
```



---


**`color.is-powerless($color, $channel, $space: null)`**

*Возвращает: `boolean`*

  **Совместимость (`$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает, является ли `$channel` цвета `$color` [нейтральным] (powerless) в `$space`,
  которое по умолчанию равно пространству `$color`. `$channel` должен быть строкой в
  кавычках, а `$space` — строкой без кавычек.

  [нейтральным]: https://sass-lang.com/documentation/values/colors#powerless-channels

  Каналы считаются нейтральными в следующих случаях:

  * В пространстве `hsl` канал `hue` нейтрален, если `saturation` равна 0%.
  * В пространстве `hwb` канал `hue` нейтрален, если сумма `whiteness` и
    `blackness` больше 100%.
  * В пространствах `lch` и `oklch` канал `hue` нейтрален, если `chroma` равна
    0%.

  **SCSS**

```scss
@use 'sass:color';

@debug color.is-powerless(hsl(180deg 0% 40%), "hue"); // true
@debug color.is-powerless(hsl(180deg 0% 40%), "saturation"); // false
@debug color.is-powerless(#999, "hue", $space: hsl); // true
```

**Sass**

```sass
@use 'sass:color'

@debug color.is-powerless(hsl(180deg 0% 40%), "hue")  // true
@debug color.is-powerless(hsl(180deg 0% 40%), "saturation")  // false
@debug color.is-powerless(#999, "hue", $space: hsl)  // true
```



---


**`color.mix($color1, $color2, $weight: 50%, $method: null)`**

**`mix($color1, $color2, $weight: 50%, $method: null)`**

*Возвращает: `color`*

  **Совместимость (`$method`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает цвет, являющийся смесью `$color1` и `$color2`, с использованием `$method` —
  имени цветового пространства, за которым может опционально следовать [метод интерполяции
  оттенка], если это полярное цветовое пространство (`hsl`, `hwb`, `lch` или `oklch`).
  
  [метод интерполяции оттенка]: https://developer.mozilla.org/en-US/docs/Web/CSS/hue-interpolation-method

  Использует тот же алгоритм смешивания цветов, что и [функция CSS `color-mix()`].
  Это также означает, что если у любого из цветов есть [отсутствующий канал] в пространстве
  интерполяции, он примет значение соответствующего канала другого цвета. Функция
  всегда возвращает цвет в пространстве `$color1`.

  [функция CSS `color-mix()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix
  [отсутствующий канал]: https://sass-lang.com/documentation/values/colors#missing-channels
  
  `$weight` должен быть числом от `0%` до `100%` (включительно). Больший
  вес означает, что в результате должно быть больше `$color1`, а меньший вес —
  что больше должно быть `$color2`.

  > ⚠️ **Внимание**
  >
  > По историческим причинам `$method` необязателен, если и `$color1`, и `$color2`
  > находятся в [устаревших цветовых пространствах]. В этом случае смешивание цветов
  > выполняется по тому же алгоритму, который Sass использовал исторически, где итоговое
  > соотношение цветов в результате определяется как весом `$weight`, так и
  > относительной непрозрачностью каждого цвета.

  [устаревших цветовых пространствах]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  **SCSS**

```scss
@use 'sass:color';

@debug color.mix(#036, #d2e1dd, $method: rgb); // #698aa2
@debug color.mix(#036, #d2e1dd, $method: oklch); // rgb(87.864037264, 140.601918773, 154.2876826946)
@debug color.mix(
  color(rec2020 1 0.7 0.1),
  color(rec2020 0.8 none 0.3),
  $weight: 75%,
  $method: rec2020
); // color(rec2020 0.95 0.7 0.15)
@debug color.mix(
  oklch(80% 20% 0deg),
  oklch(50% 10% 120deg),
  $method: oklch longer hue
); // oklch(65% 0.06 240deg)
```

**Sass**

```sass
@use 'sass:color';

@debug color.mix(#036, #d2e1dd, $method: rgb)  // #698aa2
@debug color.mix(#036, #d2e1dd, $method: oklch)  // rgb(87.864037264, 140.601918773, 154.2876826946)
@debug color.mix(color(rec2020 1 0.7 0.1), color(rec2020 0.8 none 0.3), $weight: 75%, $method: rec2020)  // color(rec2020 0.95 0.7 0.15)





@debug color.mix(oklch(80% 20% 0deg), oklch(50% 10% 120deg), $method: oklch longer hue)  // oklch(65% 0.06 240deg)
```



---


**`color.same($color1, $color2)`**

*Возвращает: `boolean`*

  **Совместимость:** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает, отображаются ли `$color1` и `$color2` визуально как один и тот же цвет.
  В отличие от `==`, здесь цвета считаются эквивалентными, даже если они находятся в
  разных цветовых пространствах, если они представляют одно и то же значение цвета в
  пространстве `xyz`. При этом [отсутствующие каналы] приравниваются к нулю.

  [отсутствующие каналы]: https://sass-lang.com/documentation/values/colors#missing-channels

  **SCSS**

```scss
@use 'sass:color';

@debug color.same(#036, #036); // true
@debug color.same(#036, #037); // false
@debug color.same(#036, color.to-space(#036, oklch)); // true
@debug color.same(hsl(none 50% 50%), hsl(0deg 50% 50%)); // true
```

**Sass**

```sass
@use 'sass:color'

@debug color.same(#036, #036)  // true
@debug color.same(#036, #037)  // false
@debug color.same(#036, color.to-space(#036, oklch))  // true
@debug color.same(hsl(none 50% 50%), hsl(0deg 50% 50%))  // true
```




---


```scss
color.scale($color,
  $red: null, $green: null, $blue: null,
  $saturation: null, $lightness: null,
  $whiteness: null, $blackness: null,
  $x: null, $y: null, $z: null,
  $chroma: null,
  $alpha: null,
  $space: null)
```

**`scale-color(...)`**

*Возвращает: `color`*

  **Совместимость (`$x`, `$y`, `$z`, `$chroma` и `$space`):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗

  **Совместимость (`$whiteness` и `$blackness`):** Dart Sass: начиная с 1.28.0 · LibSass: ✗ · Ruby Sass: ✗


  Плавно масштабирует одно или несколько свойств `$color`.

  Каждый именованный аргумент должен быть числом от `-100%` до `100%` (включительно).
  Он указывает, насколько соответствующее свойство должно сместиться от своего
  исходного положения к максимуму (если аргумент положительный) или к
  минимуму (если аргумент отрицательный). Это означает, что, например,
  `$lightness: 50%` сделает все цвета на `50%` ближе к максимальной светлоте,
  не делая их полностью белыми. По умолчанию можно масштабировать только цвета в
  пространстве `$color`, но можно передать другое цветовое пространство в качестве `$space`,
  чтобы масштабировать каналы в нём. Функция всегда возвращает цвет в том же пространстве,
  что и `$color`.

  > ⚠️ **Внимание**
  >
  > По историческим причинам, если `$color` находится в [устаревшем цветовом пространстве],
  > можно масштабировать _любые_ каналы устаревших цветовых пространств. Однако является
  > ошибкой указывать RGB-канал (`$red`, `$green` и/или `$blue`) одновременно с HSL-каналом
  > (`$saturation` и/или `$lightness`) либо любой из них одновременно с каналом [HWB]
  > (`$hue`, `$whiteness` и/или `$blackness`).
  >
  > Тем не менее рекомендуется явно указывать `$space` даже для устаревших цветов.

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces
  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  Смотрите также:

  * [`color.adjust()`](#adjust) — для изменения свойств цвета на фиксированные
    величины.
  * [`color.change()`](#change) — для установки свойств цвета.

  **SCSS**

```scss
@use 'sass:color';

@debug color.scale(#6b717f, $red: 15%); // rgb(129.2, 113, 127)
@debug color.scale(#d2e1dd, $lightness: -10%, $space: oklch);
// rgb(181.2580722731, 195.8949200496, 192.0059024063)
@debug color.scale(oklch(80% 20% 120deg), $chroma: 50%, $alpha: -40%);
// oklch(80% 0.24 120deg / 0.6)
```

**Sass**

```sass
@use 'sass:color'

@debug color.scale(#6b717f, $red: 15%)  // rgb(129.2, 113, 127)
@debug color.scale(#d2e1dd, $lightness: -10%, $space: oklch)
// rgb(181.2580722731, 195.8949200496, 192.0059024063)
@debug color.scale(oklch(80% 20% 120deg), $chroma: 50%, $alpha: -40%)
// oklch(80% 0.24 120deg / 0.6)
```



---


**`color.space($color)`**

*Возвращает: `unquoted string`*

  **Совместимость:** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает имя пространства `$color` в виде строки без кавычек.

  **SCSS**

```scss
@use 'sass:color';

@debug color.space(#036); // rgb
@debug color.space(hsl(120deg 40% 50%)); // hsl
@debug color.space(color(xyz-d65 0.1 0.2 0.3)); // xyz
```

**Sass**

```sass
@use 'sass:color'

@debug color.space(#036)  // rgb
@debug color.space(hsl(120deg 40% 50%))  // hsl
@debug color.space(color(xyz-d65 0.1 0.2 0.3))  // xyz
```



---


**`color.to-gamut($color, $space: null, $method: null)`**

*Возвращает: `color`*

  **Совместимость:** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает визуально похожий на `$color` цвет в пределах цветового охвата `$space`,
  которое по умолчанию равно пространству `$color`. Если `$color` уже находится в пределах
  охвата `$space`, он возвращается без изменений. Функция всегда возвращает цвет в
  исходном пространстве `$color`. `$space` должен быть строкой без кавычек.

  `$method` указывает, как Sass должен выбирать «похожий» цвет:

  * `local-minde`: этот метод в настоящее время рекомендован спецификацией CSS Colors 4.
    Он выполняет бинарный поиск по цветности Oklch данного цвета, пока не найдёт
    цвет, чьё значение, обрезанное по охвату, максимально близко к варианту с
    уменьшенной цветностью.

  * `clip`: просто обрезает все каналы в пределах охвата `$space`, устанавливая
    их в минимальное или максимальное значение охвата, если они выходят за его пределы.
  
  > ⚠️ **Внимание**
  >
  > Рабочая группа CSS и производители браузеров всё ещё активно обсуждают
  > альтернативные варианты рекомендуемого алгоритма приведения к цветовому охвату. Пока
  > они не придут к единой рекомендации, параметр `$method` в `color.to-gamut()`
  > является обязательным, чтобы впоследствии можно было сделать его значение по умолчанию
  > таким же, как в CSS.

  **SCSS**

```scss
@use 'sass:color';

@debug color.to-gamut(#036, $method: local-minde); // #036
@debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: local-minde);
// oklch(61.2058838235% 0.2466052584 22.0773325274deg)
@debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: clip);
// oklch(62.5026609544% 0.2528579741 24.1000466758deg)
```

**Sass**

```sass
@use 'sass:color'

@debug color.to-gamut(#036, $method: local-minde)  // #036
@debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: local-minde)
// oklch(61.2058838235% 0.2466052584 22.0773325274deg)
@debug color.to-gamut(oklch(60% 70% 20deg), $space: rgb, $method: clip)
// oklch(62.5026609544% 0.2528579741 24.1000466758deg)
```



---


**`color.to-space($color, $space)`**

*Возвращает: `color`*

  **Совместимость:** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


  Преобразует `$color` в указанное `$space`, которое должно быть строкой без кавычек.

  Если цветовой охват исходного пространства `$color` шире охвата `$space`, функция
  может вернуть цвет, выходящий за пределы охвата `$space`. Вы можете преобразовать его в
  похожий цвет в пределах охвата с помощью [`color.to-gamut()`].

  [`color.to-gamut()`]: #to-gamut

  Это может привести к появлению цветов с [отсутствующими каналами] — либо если у `$color`
  есть [аналогичный канал], который отсутствует, либо если канал [нейтрален] в целевом
  пространстве. Чтобы преобразование в устаревшие цветовые пространства всегда давало
  цвет, совместимый со старыми браузерами, если `$space` является устаревшим, функция
  никогда не вернёт новый отсутствующий канал.

  [отсутствующими каналами]: https://sass-lang.com/documentation/values/colors#missing-channels
  [аналогичный канал]: https://www.w3.org/TR/css-color-4/#analogous-components
  [нейтрален]: https://sass-lang.com/documentation/values/colors#powerless-channels
  
  > 💡 **Занятный факт**
  >
  > Это единственная функция Sass, которая возвращает цвет в пространстве, отличном
  > от переданного.

  **SCSS**

```scss
@use 'sass:color';

@debug color.to-space(#036, display-p3); // lch(20.7457453073% 35.0389733355 273.0881809283deg)
@debug color.to-space(oklab(44% 0.09 -0.13)); // rgb(103.1328911972, 50.9728091281, 150.8382311692)
@debug color.to-space(xyz(0.8 0.1 0.1)); // color(a98-rgb 1.2177586808 -0.7828263424 0.3516847577)
@debug color.to-space(grey, lch); // lch(53.5850134522% 0 none)
@debug color.to-space(lch(none 10% 30deg), oklch); // oklch(none 0.3782382429 11.1889160032deg)
```

**Sass**

```sass
@use 'sass:color'

@debug color.to-space(#036, display-p3)  // lch(20.7457453073% 35.0389733355 273.0881809283deg)
@debug color.to-space(oklab(44% 0.09 -0.13))  // rgb(103.1328911972, 50.9728091281, 150.8382311692)
@debug color.to-space(xyz(0.8 0.1 0.1))  // color(a98-rgb 1.2177586808 -0.7828263424 0.3516847577)
@debug color.to-space(grey, lch)  // lch(53.5850134522% 0 none)
@debug color.to-space(lch(none 10% 30deg), oklch)  // oklch(none 0.3782382429 11.1889160032deg)
```


## Устаревшие функции


---


**`adjust-hue($color, $degrees)`**

*Возвращает: `color`*

  Увеличивает или уменьшает оттенок HSL цвета `$color`.

  `$hue` должен быть числом от `-360deg` до `360deg` (включительно), прибавляемым
  к оттенку `$color`. Он может быть [безразмерным] или иметь любую единицу измерения угла.
  `$color` должен находиться в [устаревшем цветовом пространстве].

  [безразмерным]: https://sass-lang.com/documentation/values/numbers#units
  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  Смотрите также [`color.adjust()`](#adjust), которая может изменять любое свойство
  цвета.

  > ⚠️ **Внимание**
  >
  > Поскольку `adjust-hue()` дублирует функциональность [`color.adjust()`](#adjust), она не
  > включена напрямую в новую систему модулей. Вместо `adjust-hue($color,
  > $amount)` можно написать [`color.adjust($color, $hue: $amount, $space:
  > hsl)`](#adjust).

  **SCSS**

```scss
// Hue 222deg becomes 282deg.
@debug adjust-hue(#6b717f, 60deg); // #796b7f

// Hue 164deg becomes 104deg.
@debug adjust-hue(#d2e1dd, -60deg); // #d6e1d2

// Hue 210deg becomes 255deg.
@debug adjust-hue(#036, 45); // #1a0066
```

**Sass**

```sass
// Hue 222deg becomes 282deg.
@debug adjust-hue(#6b717f, 60deg)  // #796b7f

// Hue 164deg becomes 104deg.
@debug adjust-hue(#d2e1dd, -60deg)  // #d6e1d2

// Hue 210deg becomes 255deg.
@debug adjust-hue(#036, 45)  // #1a0066
```



---


**`color.alpha($color)`**

**`alpha($color)`**

**`opacity($color)`**

*Возвращает: `number`*

  Возвращает альфа-канал `$color` в виде числа от 0 до 1.
  
  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  Как особый случай, поддерживается синтаксис Internet Explorer
  `alpha(opacity=20)`, для которого возвращается [строка без кавычек].

  [строка без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted

  > ⚠️ **Внимание**
  >
  > Поскольку `color.alpha()` дублирует функциональность [`color.channel()`](#channel),
  > она больше не рекомендуется к использованию. Вместо `color.alpha($color)` можно
  > написать [`color.channel($color, "alpha")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.alpha(#e1d7d2); // 1
@debug color.opacity(rgb(210, 225, 221, 0.4)); // 0.4
@debug alpha(opacity=20); // alpha(opacity=20)
```

**Sass**

```sass
@use 'sass:color'

@debug color.alpha(#e1d7d2)  // 1
@debug color.opacity(rgb(210, 225, 221, 0.4))  // 0.4
@debug alpha(opacity=20)  // alpha(opacity=20)
```



---


**`color.blackness($color)`**

**`blackness($color)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.28.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает черноту `$color` в модели [HWB] в виде числа от `0%` до `100%`.

  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.blackness()` дублирует функциональность [`color.channel()`](#channel),
  > она больше не рекомендуется к использованию. Вместо `color.blackness($color)` можно
  > написать [`color.channel($color, "blackness")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.blackness(#e1d7d2); // 11.7647058824%
@debug color.blackness(white); // 0%
@debug color.blackness(black); // 100%
```

**Sass**

```sass
@use 'sass:color'

@debug color.blackness(#e1d7d2)  // 11.7647058824%
@debug color.blackness(white)  // 0%
@debug color.blackness(black)  // 100%
```



---


**`color.blue($color)`**

**`blue($color)`**

*Возвращает: `number`*

  Возвращает синий канал `$color` в виде числа от 0 до 255.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.blue()` дублирует функциональность [`color.channel()`](#channel), она
  > больше не рекомендуется к использованию. Вместо `color.blue($color)` можно написать
  > [`color.channel($color, "blue")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.blue(#e1d7d2); // 210
@debug color.blue(white); // 255
@debug color.blue(black); // 0
```

**Sass**

```sass
@use 'sass:color'

@debug color.blue(#e1d7d2)  // 210
@debug color.blue(white)  // 255
@debug color.blue(black)  // 0
```



---


**`darken($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` темнее.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0%` до `100%` (включительно). Уменьшает
  светлоту HSL цвета `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `darken()` уменьшает светлоту на фиксированную величину, что часто
  > не даёт нужного результата. Чтобы сделать цвет темнее на определённый процент
  > относительно исходного, используйте вместо неё [`color.scale()`](#scale).
  >
  > Поскольку `darken()` обычно не лучший способ сделать цвет темнее, она не
  > включена напрямую в новую систему модулей. Однако если нужно сохранить
  > прежнее поведение, `darken($color, $amount)` можно записать как
  > [`color.adjust($color, $lightness: -$amount, $space: hsl)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// #036 has lightness 20%, so when darken() subtracts 30% it just returns black.
@debug darken(#036, 30%); // black

// scale() instead makes it 30% darker than it was originally.
@debug color.scale(#036, $lightness: -30%); // #002447
```

**Sass**

```sass
@use 'sass:color'

// #036 has lightness 20%, so when darken() subtracts 30% it just returns black.
@debug darken(#036, 30%)  // black

// scale() instead makes it 30% darker than it was originally.
@debug color.scale(#036, $lightness: -30%)  // #002447
```

  **SCSS**

```scss
// Lightness 92% becomes 72%.
@debug darken(#b37399, 20%); // #7c4465

// Lightness 85% becomes 45%.
@debug darken(#f2ece4, 40%); // #b08b5a

// Lightness 20% becomes 0%.
@debug darken(#036, 30%); // black
```

**Sass**

```sass
// Lightness 92% becomes 72%.
@debug darken(#b37399, 20%)  // #7c4465

// Lightness 85% becomes 45%.
@debug darken(#f2ece4, 40%)  // #b08b5a

// Lightness 20% becomes 0%.
@debug darken(#036, 30%)  // black
```



---


**`desaturate($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` менее насыщенным.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0%` до `100%` (включительно). Уменьшает
  насыщенность HSL цвета `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `desaturate()` уменьшает насыщенность на фиксированную величину, что
  > часто не даёт нужного результата. Чтобы сделать цвет менее насыщенным на определённый
  > процент относительно исходного, используйте вместо неё [`color.scale()`](#scale).
  >
  > Поскольку `desaturate()` обычно не лучший способ уменьшить насыщенность цвета,
  > она не включена напрямую в новую систему модулей. Однако если нужно сохранить
  > прежнее поведение, `desaturate($color, $amount)` можно записать как
  > [`color.adjust($color, $saturation: -$amount, $space: hsl)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// #d2e1dd has saturation 20%, so when desaturate() subtracts 30% it just
// returns gray.
@debug desaturate(#d2e1dd, 30%); // #dadada

// scale() instead makes it 30% less saturated than it was originally.
@debug color.scale(#6b717f, $saturation: -30%); // #6e727c
```

**Sass**

```sass
@use 'sass:color'

// #6b717f has saturation 20%, so when desaturate() subtracts 30% it just
// returns gray.
@debug desaturate(#d2e1dd, 30%)  // #dadada

// scale() instead makes it 30% less saturated than it was originally.
@debug color.scale(#6b717f, $saturation: -30%)  // #6e727c
```

  **SCSS**

```scss
// Saturation 100% becomes 80%.
@debug desaturate(#036, 20%); // #0a335c

// Saturation 35% becomes 15%.
@debug desaturate(#f2ece4, 20%); // #eeebe8

// Saturation 20% becomes 0%.
@debug desaturate(#d2e1dd, 30%); // #dadada
```

**Sass**

```sass
// Saturation 100% becomes 80%.
@debug desaturate(#036, 20%)  // #0a335c

// Saturation 35% becomes 15%.
@debug desaturate(#f2ece4, 20%)  // #eeebe8

// Saturation 20% becomes 0%.
@debug desaturate(#d2e1dd, 30%)  // #dadada
```



---


**`color.green($color)`**

**`green($color)`**

*Возвращает: `number`*

  Возвращает зелёный канал `$color` в виде числа от 0 до 255.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.green()` дублирует функциональность [`color.channel()`](#channel),
  > она больше не рекомендуется к использованию. Вместо `color.green($color)` можно
  > написать [`color.channel($color, "green")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.green(#e1d7d2); // 215
@debug color.green(white); // 255
@debug color.green(black); // 0
```

**Sass**

```sass
@use 'sass:color'

@debug color.green(#e1d7d2)  // 215
@debug color.green(white)  // 255
@debug color.green(black)  // 0
```



---


**`color.hue($color)`**

**`hue($color)`**

*Возвращает: `number`*

  Возвращает оттенок `$color` в виде числа от `0deg` до `360deg`.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.hue()` дублирует функциональность [`color.channel()`](#channel), она
  > больше не рекомендуется к использованию. Вместо `color.hue($color)` можно написать
  > [`color.channel($color, "hue")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.hue(#e1d7d2); // 20deg
@debug color.hue(#f2ece4); // 34.2857142857deg
@debug color.hue(#dadbdf); // 228deg
```

**Sass**

```sass
@use 'sass:color'

@debug color.hue(#e1d7d2)  // 20deg
@debug color.hue(#f2ece4)  // 34.2857142857deg
@debug color.hue(#dadbdf)  // 228deg
```



---


**`lighten($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` светлее.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0%` до `100%` (включительно). Увеличивает
  светлоту HSL цвета `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `lighten()` увеличивает светлоту на фиксированную величину, что часто
  > не даёт нужного результата. Чтобы сделать цвет светлее на определённый процент
  > относительно исходного, используйте вместо неё [`scale()`](#scale).
  >
  > Поскольку `lighten()` обычно не лучший способ сделать цвет светлее, она не
  > включена напрямую в новую систему модулей. Однако если нужно сохранить
  > прежнее поведение, `lighten($color, $amount)` можно записать как
  > [`color.adjust($color, $lightness: $amount, $space: hsl)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// #e1d7d2 has lightness 85%, so when lighten() adds 30% it just returns white.
@debug lighten(#e1d7d2, 30%); // white

// scale() instead makes it 30% lighter than it was originally.
@debug color.scale(#e1d7d2, $lightness: 30%); // #eae3e0
```

**Sass**

```sass
@use 'sass:color'

// #e1d7d2 has lightness 85%, so when lighten() adds 30% it just returns white.
@debug lighten(#e1d7d2, 30%)  // white

// scale() instead makes it 30% lighter than it was originally.
@debug color.scale(#e1d7d2, $lightness: 30%)  // #eae3e0
```

  **SCSS**

```scss
// Lightness 46% becomes 66%.
@debug lighten(#6b717f, 20%); // #a1a5af

// Lightness 20% becomes 80%.
@debug lighten(#036, 60%); // #99ccff

// Lightness 85% becomes 100%.
@debug lighten(#e1d7d2, 30%); // white
```

**Sass**

```sass
// Lightness 46% becomes 66%.
@debug lighten(#6b717f, 20%)  // #a1a5af

// Lightness 20% becomes 80%.
@debug lighten(#036, 60%)  // #99ccff

// Lightness 85% becomes 100%.
@debug lighten(#e1d7d2, 30%)  // white
```



---


**`color.lightness($color)`**

**`lightness($color)`**

*Возвращает: `number`*

  Возвращает светлоту HSL цвета `$color` в виде числа от `0%` до `100%`.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.lightness()` дублирует функциональность
  > [`color.channel()`](#channel), она больше не рекомендуется к использованию. Вместо
  > `color.lightness($color)` можно написать
  > [`color.channel($color, "lightness")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.lightness(#e1d7d2); // 85.2941176471%
@debug color.lightness(#f2ece4); // 92.1568627451%
@debug color.lightness(#dadbdf); // 86.4705882353%
```

**Sass**

```sass
@use 'sass:color'

@debug color.lightness(#e1d7d2)  // 85.2941176471%
@debug color.lightness(#f2ece4)  // 92.1568627451%
@debug color.lightness(#dadbdf)  // 86.4705882353%
```



---


**`opacify($color, $amount)`**

**`fade-in($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` более непрозрачным.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0` до `1` (включительно). Увеличивает
  альфа-канал `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `opacify()` увеличивает альфа-канал на фиксированную величину, что
  > часто не даёт нужного результата. Чтобы сделать цвет более непрозрачным на определённый
  > процент относительно исходного, используйте вместо неё [`scale()`](#scale).
  >
  > Поскольку `opacify()` обычно не лучший способ сделать цвет более непрозрачным,
  > она не включена напрямую в новую систему модулей. Однако если нужно сохранить
  > прежнее поведение, `opacify($color, $amount)` можно записать как
  > [`color.adjust($color, $alpha: -$amount)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// rgba(#036, 0.7) has alpha 0.7, so when opacify() adds 0.3 it returns a fully
// opaque color.
@debug opacify(rgba(#036, 0.7), 0.3); // #036

// scale() instead makes it 30% more opaque than it was originally.
@debug color.scale(rgba(#036, 0.7), $alpha: 30%); // rgba(0, 51, 102, 0.79)
```

**Sass**

```sass
@use 'sass:color'

// rgba(#036, 0.7) has alpha 0.7, so when opacify() adds 0.3 it returns a fully
// opaque color.
@debug opacify(rgba(#036, 0.7), 0.3)  // #036

// scale() instead makes it 30% more opaque than it was originally.
@debug color.scale(rgba(#036, 0.7), $alpha: 30%)  // rgba(0, 51, 102, 0.79)
```

  **SCSS**

```scss
@debug opacify(rgba(#6b717f, 0.5), 0.2); // rgba(107, 113, 127, 0.7)
@debug fade-in(rgba(#e1d7d2, 0.5), 0.4); // rgba(225, 215, 210, 0.9)
@debug opacify(rgba(#036, 0.7), 0.3); // #036
```

**Sass**

```sass
@debug opacify(rgba(#6b717f, 0.5), 0.2)  // rgba(107, 113, 127, 0.7)
@debug fade-in(rgba(#e1d7d2, 0.5), 0.4)  // rgba(225, 215, 210, 0.9)
@debug opacify(rgba(#036, 0.7), 0.3)  // #036
```



---


**`color.red($color)`**

**`red($color)`**

*Возвращает: `number`*

  Возвращает красный канал `$color` в виде числа от 0 до 255.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.red()` дублирует функциональность [`color.channel()`](#channel), она
  > больше не рекомендуется к использованию. Вместо `color.red($color)` можно написать
  > [`color.channel($color, "red")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.red(#e1d7d2); // 225
@debug color.red(white); // 255
@debug color.red(black); // 0
```

**Sass**

```sass
@use 'sass:color'

@debug color.red(#e1d7d2)  // 225
@debug color.red(white)  // 255
@debug color.red(black)  // 0
```



---


**`saturate($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` более насыщенным.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0%` до `100%` (включительно). Увеличивает
  насыщенность HSL цвета `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `saturate()` увеличивает насыщенность на фиксированную величину, что
  > часто не даёт нужного результата. Чтобы сделать цвет более насыщенным на определённый
  > процент относительно исходного, используйте вместо неё [`scale()`](#scale).
  >
  > Поскольку `saturate()` обычно не лучший способ увеличить насыщенность цвета,
  > она не включена напрямую в новую систему модулей. Однако если нужно сохранить
  > прежнее поведение, `saturate($color, $amount)` можно записать как
  > [`color.adjust($color, $saturation: $amount, $space: hsl)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// #0e4982 has saturation 80%, so when saturate() adds 30% it just becomes
// fully saturated.
@debug saturate(#0e4982, 30%); // #004990

// scale() instead makes it 30% more saturated than it was originally.
@debug color.scale(#0e4982, $saturation: 30%); // #0a4986
```

**Sass**

```sass
@use 'sass:color'

// #0e4982 has saturation 80%, so when saturate() adds 30% it just becomes
// fully saturated.
@debug saturate(#0e4982, 30%)  // #004990

// scale() instead makes it 30% more saturated than it was originally.
@debug color.scale(#0e4982, $saturation: 30%)  // #0a4986
```

  **SCSS**

```scss
// Saturation 50% becomes 70%.
@debug saturate(#c69, 20%); // #e05299

// Saturation 35% becomes 85%.
@debug desaturate(#f2ece4, 50%); // #ebebeb

// Saturation 80% becomes 100%.
@debug saturate(#0e4982, 30%)  // #004990
```

**Sass**

```sass
// Saturation 50% becomes 70%.
@debug saturate(#c69, 20%); // #e05299

// Saturation 35% becomes 85%.
@debug desaturate(#f2ece4, 50%); // #ebebeb

// Saturation 80% becomes 100%.
@debug saturate(#0e4982, 30%)  // #004990
```



---


**`color.saturation($color)`**

**`saturation($color)`**

*Возвращает: `number`*

  Возвращает насыщенность HSL цвета `$color` в виде числа от `0%` до `100%`.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.saturation()` дублирует функциональность
  > [`color.channel()`](#channel), она больше не рекомендуется к использованию. Вместо
  > `color.saturation($color)` можно написать
  > [`color.channel($color, "saturation")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.saturation(#e1d7d2); // 20%
@debug color.saturation(#f2ece4); // 30%
@debug color.saturation(#dadbdf); // 7.2463768116%
```

**Sass**

```sass
@use 'sass:color'

@debug color.saturation(#e1d7d2)  // 20%
@debug color.saturation(#f2ece4)  // 30%
@debug color.saturation(#dadbdf)  // 7.2463768116%
```



---


**`transparentize($color, $amount)`**

**`fade-out($color, $amount)`**

*Возвращает: `color`*

  Делает `$color` более прозрачным.

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  `$amount` должен быть числом от `0` до `1` (включительно). Уменьшает
  альфа-канал `$color` на это значение.

  > ⚠️ **Внимание**
  >
  > Функция `transparentize()` уменьшает альфа-канал на фиксированную величину,
  > что часто не даёт нужного результата. Чтобы сделать цвет более прозрачным на
  > определённый процент относительно исходного, используйте вместо неё
  > [`color.scale()`](#scale).
  >
  > Поскольку `transparentize()` обычно не лучший способ сделать цвет более
  > прозрачным, она не включена напрямую в новую систему модулей. Однако если
  > нужно сохранить прежнее поведение, `transparentize($color, $amount)`
  > можно записать как [`color.adjust($color, $alpha: -$amount,
  > $space: hsl)`](#adjust).

  **SCSS**

```scss
@use 'sass:color';

// rgba(#036, 0.3) has alpha 0.3, so when transparentize() subtracts 0.3 it
// returns a fully transparent color.
@debug transparentize(rgba(#036, 0.3), 0.3); // rgba(0, 51, 102, 0)

// scale() instead makes it 30% more transparent than it was originally.
@debug color.scale(rgba(#036, 0.3), $alpha: -30%); // rgba(0, 51, 102, 0.21)
```

**Sass**

```sass
@use 'sass:color'

// rgba(#036, 0.3) has alpha 0.3, so when transparentize() subtracts 0.3 it
// returns a fully transparent color.
@debug transparentize(rgba(#036, 0.3), 0.3)  // rgba(0, 51, 102, 0)

// scale() instead makes it 30% more transparent than it was originally.
@debug color.scale(rgba(#036, 0.3), $alpha: -30%)  // rgba(0, 51, 102, 0.21)
```

  **SCSS**

```scss
@debug transparentize(rgba(#6b717f, 0.5), 0.2);  // rgba(107, 113, 127, 0.3)
@debug fade-out(rgba(#e1d7d2, 0.5), 0.4);  // rgba(225, 215, 210, 0.1)
@debug transparentize(rgba(#036, 0.3), 0.3);  // rgba(0, 51, 102, 0)
```

**Sass**

```sass
@debug transparentize(rgba(#6b717f, 0.5), 0.2)  // rgba(107, 113, 127, 0.3)
@debug fade-out(rgba(#e1d7d2, 0.5), 0.4)  // rgba(225, 215, 210, 0.1)
@debug transparentize(rgba(#036, 0.3), 0.3)  // rgba(0, 51, 102, 0)
```



---


**`color.whiteness($color)`**

*Возвращает: `number`*

  **Совместимость:** Dart Sass: начиная с 1.28.0 · LibSass: ✗ · Ruby Sass: ✗


  Возвращает белизну `$color` в модели [HWB] в виде числа от `0%` до `100%`.

  [HWB]: https://en.wikipedia.org/wiki/HWB_color_model

  `$color` должен находиться в [устаревшем цветовом пространстве].

  [устаревшем цветовом пространстве]: https://sass-lang.com/documentation/values/colors#legacy-color-spaces

  > ⚠️ **Внимание**
  >
  > Поскольку `color.whiteness()` дублирует функциональность [`color.channel()`](#channel),
  > она больше не рекомендуется к использованию. Вместо `color.whiteness($color)` можно
  > написать [`color.channel($color, "whiteness")`](#channel).

  **SCSS**

```scss
@use 'sass:color';

@debug color.whiteness(#e1d7d2); // 82.3529411765%
@debug color.whiteness(white); // 100%
@debug color.whiteness(black); // 0%
```

**Sass**

```sass
@use 'sass:color'

@debug color.whiteness(#e1d7d2)  // 82.3529411765%
@debug color.whiteness(white)  // 100%
@debug color.whiteness(black)  // 0%
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/color/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

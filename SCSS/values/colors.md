# Цвета


**Совместимость (цветовые пространства):** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗

  LibSass, Ruby Sass и старые версии Dart Sass не поддерживают цветовые
  пространства, отличные от `rgb` и `hsl`.

  Помимо добавления поддержки новых цветовых пространств, этот релиз изменил
  некоторые детали обработки цветов. В частности, даже устаревшие цветовые
  пространства `rgb` и `hsl` больше не ограничиваются своим гамутом (gamut);
  теперь можно представить `rgb(500 0 0)` или другие значения за пределами
  диапазона. Кроме того, цвета `rgb` больше не округляются до ближайшего
  целого числа, поскольку спецификация CSS теперь требует от реализаций
  сохранять точность, где это возможно.


**Совместимость (синтаксис уровня 4):** Dart Sass: начиная с 1.14.0 · LibSass: ✗ · Ruby Sass: начиная с 3.6.0

  LibSass и старые версии Dart или Ruby Sass не поддерживают [hex-цвета с
  альфа-каналом][].

  [hex-цвета с альфа-каналом]: https://drafts.csswg.org/css-color/#hex-notation


Sass имеет встроенную поддержку значений цвета. Как и цвета CSS, каждый цвет
представляет точку в определённом цветовом пространстве, таком как `rgb` или
`lab`. Цвета Sass можно записывать в виде hex-кодов (`#f2ece4` или
`#b37399aa`), [имён цветов CSS] (`midnightblue`, `transparent`) или цветовых
функций, таких как [`rgb()`], [`lab()`] или [`color()`].

[пространство sRGB]: https://en.wikipedia.org/wiki/SRGB
[цветовых функций]: https://sass-lang.com/documentation/modules/color
[имён цветов CSS]: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#Color_keywords
[`rgb()`]: https://sass-lang.com/documentation/modules#rgb
[`lab()`]: https://sass-lang.com/documentation/modules#lab
[`color()`]: https://sass-lang.com/documentation/modules#color

**SCSS**

```scss
@debug #f2ece4; // #f2ece4
@debug #b37399aa; // rgba(179, 115, 153, 67%)
@debug midnightblue; // #191970
@debug rgb(204 102 153); // #c69
@debug lab(32.4% 38.4 -47.7 / 0.7); // lab(32.4% 38.4 -47.7 / 0.7)
@debug color(display-p3 0.597 0.732 0.576); // color(display-p3 0.597 0.732 0.576)
```

**Sass**

```sass
@debug #f2ece4  // #f2ece4
@debug #b37399aa  // rgba(179, 115, 153, 67%)
@debug midnightblue  // #191970
@debug rgb(204 102 153)  // #c69
@debug lab(32.4% 38.4 -47.7 / 0.7)  // lab(32.4% 38.4 -47.7 / 0.7)
@debug color(display-p3 0.597 0.732 0.576)  // color(display-p3 0.597 0.732 0.576)
```

## Цветовые пространства

Sass поддерживает тот же набор цветовых пространств, что и CSS. Цвет Sass
всегда выводится в том же цветовом пространстве, в котором он был записан,
если только он не находится в [устаревшем цветовом пространстве] или вы не
преобразуете его в другое пространство с помощью [`color.to-space()`]. Все
остальные цветовые функции в Sass всегда возвращают цвет в том же
пространстве, что и исходный цвет, даже если функция вносила изменения в этот
цвет в другом пространстве.

[устаревшем цветовом пространстве]: #legacy-color-spaces
[`color.to-space()`]: https://sass-lang.com/documentation/modules/color#to-space

Хотя каждое цветовое пространство имеет границы гамута, которые оно ожидает
для своих каналов, Sass может представлять значения за пределами гамута для
любого цветового пространства. Это позволяет безопасно конвертировать цвет из
широкогамутного пространства в узкогамутное и обратно без потери информации.

> ⚠️ **Внимание**
>
> CSS требует, чтобы некоторые цветовые функции обрезали входные каналы.
> Например, `rgb(500 0 0)` обрезает свой красный канал до диапазона [0, 255]
> и поэтому эквивалентен `rgb(255 0 0)`, хотя `rgb(500 0 0)` — это отдельное
> значение, которое Sass может представить. Вы всегда можете использовать
> функцию Sass [`color.change()`], чтобы задать значение за пределами гамута
> для любого пространства.
>
> [`color.change()`]: https://sass-lang.com/documentation/modules/color#change

Ниже приведён полный список всех цветовых пространств, поддерживаемых Sass.
Подробнее об этих пространствах можно узнать [на MDN].

**Совместимость (display-p3-linear):** Dart Sass: начиная с 1.97.0 · LibSass: ✗ · Ruby Sass: ✗


[на MDN]: https://developer.mozilla.org/en-US/docs/Glossary/Color_space

<table class="sl-c-table">
  <tr>
    <th scope="col">Пространство</th>
    <th scope="col">Синтаксис</th>
    <th scope="col">Каналы [мин, макс]</th>
  </tr>
  <tr>
    <th scope="row"><code>rgb</code>*</th>
    <td>
      <code>rgb(102 51 153)</code><br>
      <code>#663399</code><br>
      <code>rebeccapurple</code>
    </td>
    <td>
      красный <span class="fade">[0, 255]</span>;
      зелёный <span class="fade">[0, 255]</span>;
      синий <span class="fade">[0, 255]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>hsl</code>*</th>
    <td><code>hsl(270 50% 40%)</code></td>
    <td>
      оттенок <span class="fade">[0, 360]</span>;
      насыщенность <span class="fade">[0%, 100%]</span>;
      светлота <span class="fade">[0%, 100%]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>hwb</code>*</th>
    <td><code>hwb(270 20% 40%)</code></td>
    <td>
      оттенок <span class="fade">[0, 360]</span>;
      белизна <span class="fade">[0%, 100%]</span>;
      чернота <span class="fade">[0%, 100%]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>srgb</code></th>
    <td><code>color(srgb 0.4 0.2 0.6)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>srgb-linear</code></th>
    <td><code>color(srgb-linear 0.133 0.033 0.319)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>display-p3</code></th>
    <td><code>color(display-p3 0.1154 0.0363 0.2946)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>display-p3-linear</code></th>
    <td><code>color(display-p3-linear 0.374 0.21 0.579)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>a98-rgb</code></th>
    <td><code>color(a98-rgb 0.358 0.212 0.584)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>prophoto-rgb</code></th>
    <td><code>color(prophoto-rgb 0.316 0.191 0.495)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>rec2020</code></th>
    <td><code>color(rec2020 0.305 0.168 0.531)</code></td>
    <td>
      красный <span class="fade">[0, 1]</span>;
      зелёный <span class="fade">[0, 1]</span>;
      синий <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>xyz</code>, <code>xyz-d65</code></th>
    <td>
      <code>color(xyz 0.124 0.075 0.309)</code><br>
      <code>color(xyz-d65 0.124 0.075 0.309)</code>
    </td>
    <td>
      x <span class="fade">[0, 1]</span>;
      y <span class="fade">[0, 1]</span>;
      z <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>xyz-d50</code></th>
    <td><code>color(xyz-d50 0.116 0.073 0.233)</code></td>
    <td>
      x <span class="fade">[0, 1]</span>;
      y <span class="fade">[0, 1]</span>;
      z <span class="fade">[0, 1]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>lab</code></th>
    <td><code>lab(32.4% 38.4 -47.7)</code></td>
    <td>
      светлота <span class="fade">[0%, 100%]</span>;
      a <span class="fade">[-125, 125]</span>;
      b <span class="fade">[-125, 125]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>lch</code></th>
    <td><code>lch(32.4% 61.2 308.9deg)</code></td>
    <td>
      светлота <span class="fade">[0%, 100%]</span>;
      цветность <span class="fade">[0, 150]</span>;
      оттенок <span class="fade">[0deg, 360deg]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>oklab</code></th>
    <td><code>oklab(44% 0.088 -0.134)</code></td>
    <td>
      светлота <span class="fade">[0%, 100%]</span>;
      a <span class="fade">[-0.4, 0.4]</span>;
      b <span class="fade">[-0.4, 0.4]</span>
    </td>
  </tr>
  <tr>
    <th scope="row"><code>oklch</code></th>
    <td><code>oklch(44% 0.16 303.4deg)</code></td>
    <td>
      светлота <span class="fade">[0%, 100%]</span>;
      цветность <span class="fade">[0, 0.4]</span>;
      оттенок <span class="fade">[0deg, 360deg]</span>
    </td>
  </tr>
</table>

Пространства, отмеченные *, являются [устаревшими цветовыми пространствами].

[устаревшими цветовыми пространствами]: #legacy-color-spaces

## Отсутствующие каналы

Цвета в CSS и Sass могут иметь «отсутствующие каналы» — они записываются как
`none` и представляют канал, значение которого неизвестно или не влияет на
то, как отображается цвет. Например, можно написать `hsl(none 0% 50%)`,
потому что оттенок не важен, если насыщенность равна `0%`. В большинстве
случаев отсутствующие каналы просто трактуются как нулевые значения, но иногда
они всё же имеют значение:

* Если вы смешиваете цвета — будь то в рамках CSS-интерполяции для чего-то
  вроде анимации, или с помощью функции Sass [`color.mix()`], — отсутствующие
  каналы, по возможности, всегда принимают значение соответствующего канала
  другого цвета.

  [`color.mix()`]: https://sass-lang.com/documentation/modules/color#mix

* Если вы конвертируете цвет с отсутствующим каналом в другое пространство, в
  котором есть аналогичный канал, этот канал после завершения конвертации
  будет установлен в `none`.

Хотя [`color.channel()`] возвращает 0 для отсутствующих каналов, вы всегда
можете проверить их с помощью [`color.is-missing()`].

[`color.channel()`]: https://sass-lang.com/documentation/modules/color#channel
[`color.is-missing()`]: https://sass-lang.com/documentation/modules/color#is-missing

**SCSS**

```scss
@use 'sass:color';

$grey: hsl(none 0% 50%);

@debug color.mix($grey, blue, $method: hsl); // hsl(240, 50%, 50%)
@debug color.to-space($grey, lch); // lch(53.3889647411% 0 none)
```

**Sass**

```sass
@use 'sass:color'

$grey: hsl(none 0% 50%)

@debug color.mix($grey, blue, $method: hsl)  // hsl(240, 50%, 50%)
@debug color.to-space($grey, lch)  // lch(53.3889647411% 0 none)
```

### Бессильные каналы

Канал цвета считается «бессильным» (powerless), если при определённых
условиях его значение не влияет на то, как цвет отображается на экране.
Спецификация CSS требует, чтобы при конвертации цвета в новое пространство все
бессильные каналы заменялись на `none`. Sass делает это во всех случаях, кроме
конвертации в устаревшие пространства, чтобы гарантировать, что конвертация в
устаревшее пространство всегда даёт цвет, совместимый со старыми браузерами.

Подробнее о бессильных каналах см. [`color.is-powerless()`].

[`color.is-powerless()`]: https://sass-lang.com/documentation/modules/color#is-powerless

## Устаревшие цветовые пространства

Исторически CSS и Sass поддерживали только стандартный гамут RGB и только
функции `rgb`, `hsl` и `hwb` для определения цветов. Поскольку в то время все
цвета использовали один и тот же гамут, каждая цветовая функция работала с
любым цветом независимо от его цветового пространства. Sass по-прежнему
сохраняет такое поведение, но только для старых функций и только для цветов в
этих трёх «устаревших» цветовых пространствах. Тем не менее, при использовании
цветовых функций всё же рекомендуется явно указывать `$space`, в котором вы
хотите работать.

Sass также свободно конвертирует между разными устаревшими цветовыми
пространствами при преобразовании устаревших значений цвета в CSS. Это всегда
безопасно, поскольку все они используют одну и ту же базовую цветовую модель,
и это помогает гарантировать, что Sass выводит цвета в максимально совместимом
формате.

## Цветовые функции

Sass поддерживает много полезных [цветовых функций], с помощью которых можно
создавать новые цвета на основе существующих, [смешивая цвета] или
[масштабируя значения их каналов]. При вызове цветовых функций цветовые
пространства всегда следует записывать как строки без кавычек, чтобы
соответствовать CSS, а имена каналов следует записывать как строки в
кавычках, чтобы такие каналы, как `"red"`, не разбирались как значения цвета.

[смешивая цвета]: https://sass-lang.com/documentation/modules/color#mix
[масштабируя значения их каналов]: https://sass-lang.com/documentation/modules/color#scale

> 💡 **Занятный факт**
>
> Цветовые функции Sass могут автоматически конвертировать цвета между
> пространствами, что упрощает трансформации в перцептивно-однородных
> цветовых пространствах, таких как Oklch. Но они *всегда* возвращают цвет в
> том же пространстве, которое вы им передали, если вы явно не вызовете
> [`color.to-space()`], чтобы преобразовать его.
>
> [`color.to-space()`]: https://sass-lang.com/documentation/modules/color#to-space

**SCSS**

```scss
@use 'sass:color';

$venus: #998099;

@debug color.scale($venus, $lightness: +15%, $space: oklch);
// rgb(170.1523703626, 144.612080603, 170.1172627174)
@debug color.mix($venus, midnightblue, $method: oklch);
// rgb(95.9363315581, 74.5687109346, 133.2082569526)
```

**Sass**

```sass
@use 'sass:color'

$venus: #998099

@debug color.scale($venus, $lightness: +15%, $space: oklch)
// rgb(170.1523703626, 144.612080603, 170.1172627174)
@debug color.mix($venus, midnightblue, $method: oklch)
// rgb(95.9363315581, 74.5687109346, 133.2082569526)
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/colors/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# Ломающее изменение: функции для работы с цветом

Некоторые функции для работы с цветом, спроектированные исходя из
предположения, что все цвета взаимно совместимы, больше не имеют смысла теперь,
когда Sass поддерживает все цветовые пространства CSS Color 4.

Исторически все значения цвета в Sass охватывали один и тот же гамут:
независимо от того, были ли цвета заданы как RGB, HSL или HWB, они охватывали
только [гамут `sRGB`] и могли представлять лишь те цвета, которые мониторы
способны отображать начиная с середины 1990-х годов. Когда в Sass добавлялся
исходный набор функций для работы с цветом, предполагалось, что любой цвет
можно свободно преобразовывать между любыми из этих представлений и что у
каждого имени канала, такого как «red» или «hue», есть единое однозначное
значение.

[гамут `sRGB`]: https://en.wikipedia.org/wiki/SRGB

Выход [CSS Color 4] всё это изменил. Он добавил поддержку множества новых
цветовых пространств с иными (более широкими), чем `sRGB`, гамутами. Чтобы
поддержать эти цвета, в Sass пришлось переосмыслить принцип работы функций для
работы с цветом. Помимо добавления новых функций, таких как [`color.channel()`]
и [`color.to-space()`], ряд старых функций был объявлен устаревшим, поскольку
они основывались на предположениях, которые больше не соответствуют
действительности.

[CSS Color 4]: https://developer.mozilla.org/en-US/blog/css-color-module-level-4/

[`color.channel()`]: https://sass-lang.com/documentation/modules/color/#channel
[`color.to-space()`]: https://sass-lang.com/documentation/modules/color/#to-space

### Старые функции для каналов

Теперь имена каналов неоднозначны в разных цветовых пространствах. У
устаревшего пространства RGB есть канал `red`, но он также есть у
`display-p3`, `rec2020` и многих других. Это означает, что [`color.red()`],
[`color.green()`], [`color.blue()`], [`color.hue()`], [`color.saturation()`],
[`color.lightness()`], [`color.whiteness()`], [`color.blackness()`],
[`color.alpha()`] и [`color.opacity()`] будут удалены. Вместо этого можно
использовать функцию [`color.channel()`], чтобы получить значение конкретного
канала, обычно с явным аргументом `$space`, указывающим, с каким цветовым
пространством вы работаете.

[`color.red()`]: https://sass-lang.com/documentation/modules/color/#red
[`color.green()`]: https://sass-lang.com/documentation/modules/color/#green
[`color.blue()`]: https://sass-lang.com/documentation/modules/color/#blue
[`color.hue()`]: https://sass-lang.com/documentation/modules/color/#hue
[`color.saturation()`]: https://sass-lang.com/documentation/modules/color/#saturation
[`color.lightness()`]: https://sass-lang.com/documentation/modules/color/#lightness
[`color.whiteness()`]: https://sass-lang.com/documentation/modules/color/#whiteness
[`color.blackness()`]: https://sass-lang.com/documentation/modules/color/#blackness
[`color.alpha()`]: https://sass-lang.com/documentation/modules/color/#alpha
[`color.opacity()`]: https://sass-lang.com/documentation/modules/color/#opacity

**SCSS**

```scss
@use "sass:color";

$color: #c71585;
@debug color.channel($color, "red", $space: rgb);
@debug color.channel($color, "red", $space: display-p3);
@debug color.channel($color, "hue", $space: oklch);
```

**Sass**

```sass
@use "sass:color"

$color: #c71585
@debug color.channel($color, "red", $space: rgb)
@debug color.channel($color, "red", $space: display-p3)
@debug color.channel($color, "hue", $space: oklch)
```

### Функции корректировки одного канала

У них та же проблема неоднозначности, что и у старых функций для каналов, и
вдобавок они уже были избыточны по сравнению с [`color.adjust()`] ещё до
появления поддержки Color 4. Более того, зачастую лучше в любом случае
использовать [`color.scale()`], поскольку она лучше подходит для внесения
изменений относительно существующего цвета, а не в абсолютных величинах. Это
означает, что [`adjust-hue()`], [`saturate()`], [`desaturate()`], [`lighten()`],
[`darken()`], [`opacify()`], [`fade-in()`], [`transparentize()`] и
[`fade-out()`] будут удалены. Обратите внимание, что у этих функций никогда не
было аналогов в пространстве модуля, поскольку их использование и так не
приветствовалось.

[`color.adjust()`]: https://sass-lang.com/documentation/modules/color/#adjust
[`color.scale()`]: https://sass-lang.com/documentation/modules/color/#scale
[`adjust-hue()`]: https://sass-lang.com/documentation/modules/color/#adjust-hue
[`saturate()`]: https://sass-lang.com/documentation/modules/color/#saturate
[`desaturate()`]: https://sass-lang.com/documentation/modules/color/#desaturate
[`lighten()`]: https://sass-lang.com/documentation/modules/color/#lighten
[`darken()`]: https://sass-lang.com/documentation/modules/color/#darken
[`opacify()`]: https://sass-lang.com/documentation/modules/color/#opacify
[`fade-in()`]: https://sass-lang.com/documentation/modules/color/#fade-in
[`transparentize()`]: https://sass-lang.com/documentation/modules/color/#transparentize
[`fade-out()`]: https://sass-lang.com/documentation/modules/color/#fade-out

**SCSS**

```scss
@use "sass:color";

$color: #c71585;
@debug color.adjust($color, $lightness: 15%, $space: hsl);
@debug color.adjust($color, $lightness: 15%, $space: oklch);
@debug color.scale($color, $lightness: 15%, $space: oklch);
```

**Sass**

```sass
@use "sass:color"

$color: #c71585
@debug color.adjust($color, $lightness: 15%, $space: hsl)
@debug color.adjust($color, $lightness: 15%, $space: oklch)
@debug color.scale($color, $lightness: 15%, $space: oklch)
```

## Переходный период

**Совместимость:** Dart Sass: начиная с 1.79.0 · LibSass: ✗ · Ruby Sass: ✗


Сначала мы будем выдавать предупреждения об устаревании для всех обращений к
функциям, которые планируется удалить. В Dart Sass 2.0.0 эти функции будут
удалены полностью. Попытки вызвать версии из пространства модуля будут
выбрасывать ошибку, а глобальные функции будут трактоваться как обычные
CSS-функции и выводиться как обычные строки.

Вы можете использовать [Sass migrator], чтобы автоматически перейти с
устаревших API на их новые замены.

[Sass migrator]: https://sass-lang.com/documentation/cli/migrator/#color

## Можно ли отключить предупреждения?

Sass предоставляет широкий набор параметров для управления тем, какие
предупреждения об устаревании вы видите и когда.

### Лаконичный и подробный режим

По умолчанию Sass работает в лаконичном режиме, в котором он выводит каждый тип
предупреждения об устаревании только пять раз, после чего дальнейшие
предупреждения подавляются. Это помогает пользователям узнавать о предстоящих
ломающих изменениях, не создавая при этом избыточного шума в консоли.

Если вместо этого запустить Sass в подробном режиме, он будет выводить *каждое*
встреченное предупреждение об устаревании. Это может быть полезно для
отслеживания оставшейся работы при исправлении устареваний. Включить подробный
режим можно с помощью

[флага `--verbose`] в командной строке, либо

[параметра `verbose`] в JavaScript API.

[флага `--verbose`]: https://sass-lang.com/documentation/cli/dart-sass/#verbose
[параметра `verbose`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#verbose

> ⚠️ **Внимание**
>
> При запуске через [JS API] Sass не передаёт никакой информации между
> компиляциями, поэтому по умолчанию он выводит по пять предупреждений для
> *каждой компилируемой таблицы стилей*. Однако это можно исправить, написав
> (или попросив автора вашего любимого Sass-плагина для фреймворка написать)
> [пользовательский `Logger`], который выводит только пять ошибок на каждое
> устаревание и может использоваться совместно для нескольких компиляций.

[JS API]: https://sass-lang.com/documentation/js-api/
[пользовательский `Logger`]: https://sass-lang.com/documentation/js-api/interfaces/Logger-1/

### Отключение предупреждений для зависимостей

Иногда в ваших зависимостях есть предупреждения об устаревании, с которыми вы
ничего не можете поделать. Вы можете отключить предупреждения об устаревании
для зависимостей, сохранив их вывод для вашего приложения, с помощью

[флага `--quiet-deps`] в командной строке, либо

[параметра `quietDeps`] в JavaScript API.

[флага `--quiet-deps`]: https://sass-lang.com/documentation/cli/dart-sass/#quiet-deps
[параметра `quietDeps`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#quietDeps

Для целей этого флага «зависимостью» считается любая таблица стилей, которая не
является просто цепочкой относительных загрузок от точки входа. Это означает
всё, что загружается из пути загрузки, а также большинство таблиц стилей,
загружаемых через пользовательские загрузчики (importers).

### Отключение отдельных предупреждений об устаревании

Если вы знаете, что конкретное устаревание не представляет для вас проблемы, вы
можете отключить предупреждения для этого конкретного устаревания с помощью

[флага `--silence-deprecation`] в командной строке, либо

[параметра `silenceDeprecations`] в JavaScript API.

[флага `--silence-deprecation`]: https://sass-lang.com/documentation/cli/dart-sass/#silence-deprecation
[параметра `silenceDeprecations`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#silenceDeprecations

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/color-functions/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

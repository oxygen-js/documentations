# Карты

Карты (map) в Sass хранят пары ключей и значений и позволяют легко находить значение
по соответствующему ключу. Они записываются как `(<expression>: <expression>,
<expression>: <expression>)`. [Выражение](https://sass-lang.com/documentation/syntax/structure#expressions)
перед `:` — это ключ, а выражение после — значение, связанное с этим ключом. Ключи
должны быть уникальными, а значения могут повторяться. В отличие от
[списков](https://sass-lang.com/documentation/values/lists), карты *обязательно* нужно заключать в круглые
скобки. Пустая карта (без пар) записывается как `()`.

> 💡 **Занятный факт**
>
> Внимательные читатели могут заметить, что пустая карта `()` записывается так же, как
> пустой список. Это потому, что она считается одновременно и картой, и списком. На
> самом деле *все* карты считаются списками! Каждая карта считается списком, который
> содержит двухэлементный список для каждой пары ключ/значение. Например, `(1: 2, 3:
> 4)` считается `(1 2, 3 4)`.

Карты позволяют использовать в качестве ключей любые значения Sass. Для определения
того, одинаковы ли два ключа, используется [оператор `==`][].

[оператор `==`]: https://sass-lang.com/documentation/operators/equality

> ⚠️ **Внимание**
>
> В большинстве случаев для ключей карты лучше использовать [строки в кавычках][], а не
> [строки без кавычек][]. Дело в том, что некоторые значения, например названия цветов,
> могут *выглядеть* как строки без кавычек, а на самом деле относиться к другому типу.
> Чтобы избежать путаницы в дальнейшем, просто используйте кавычки!

[строки в кавычках]: https://sass-lang.com/documentation/values/strings#quoted
[строки без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted

## Использование карт

Поскольку карты не являются допустимыми CSS-значениями, сами по себе они мало что
делают. Именно поэтому Sass предоставляет набор [функций][] для создания карт и
доступа к содержащимся в них значениям.

[функций]: https://sass-lang.com/documentation/modules/map

### Получение значения

Смысл карт — в связывании ключей и значений, поэтому естественно, что есть способ
получить значение, связанное с ключом: [функция `map.get($map, $key)`][]! Эта функция
возвращает значение карты, связанное с указанным ключом. Она возвращает [`null`][],
если карта не содержит такого ключа.

[функция `map.get($map, $key)`]: https://sass-lang.com/documentation/modules/map#get
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

### Выполнение действия для каждой пары

Здесь функция на самом деле не используется, но это всё равно один из самых
распространённых способов работы с картами. [Правило `@each`][] выполняет блок стилей
для каждой пары ключ/значение в карте. Ключ и значение присваиваются переменным, чтобы
их было легко использовать внутри блока.

[правило `@each`]: https://sass-lang.com/documentation/at-rules/control/each

**SCSS**

```scss
$icons: ("eye": "\f112", "start": "\f12e", "stop": "\f12f");

@each $name, $glyph in $icons {
  .icon-#{$name}:before {
    display: inline-block;
    font-family: "Icon Font";
    content: $glyph;
  }
}
```

**Sass**

```sass
$icons: ("eye": "\f112", "start": "\f12e", "stop": "\f12f")

@each $name, $glyph in $icons
  .icon-#{$name}:before
    display: inline-block
    font-family: "Icon Font"
    content: $glyph
```

**CSS output**

```css
.icon-eye:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f112";
}

.icon-start:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12e";
}

.icon-stop:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "\f12f";
}
```

### Добавление в карту

Также полезно уметь добавлять в карту новые пары или заменять значение для уже
существующего ключа. Для этого служит [функция `map.set($map, $key, $value)`][]: она
возвращает копию `$map`, в которой значение по ключу `$key` установлено равным
`$value`.

[функция `map.set($map, $key, $value)`]: https://sass-lang.com/documentation/modules/map#set

**SCSS**

```scss
@use "sass:map";

$font-weights: ("regular": 400, "medium": 500, "bold": 700);

@debug map.set($font-weights, "extra-bold", 900);
// ("regular": 400, "medium": 500, "bold": 700, "extra-bold": 900)
@debug map.set($font-weights, "bold", 900);
// ("regular": 400, "medium": 500, "bold": 900)
```

**Sass**

```sass
@use "sass:map"

$font-weights: ("regular": 400, "medium": 500, "bold": 700)

@debug map.set($font-weights, "extra-bold": 900)
// ("regular": 400, "medium": 500, "bold": 700, "extra-bold": 900)
@debug map.set($font-weights, "bold", 900)
// ("regular": 400, "medium": 500, "bold": 900)
```

Вместо того чтобы устанавливать значения по одному, можно также объединить две
существующие карты с помощью [`map.merge($map1, $map2)`][].

[`map.merge($map1, $map2)`]: https://sass-lang.com/documentation/modules/map#merge

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

Если обе карты содержат одинаковые ключи, в возвращаемой карте используются значения
второй карты.

**SCSS**

```scss
@use "sass:map";

$weights: ("light": 300, "medium": 500);

@debug map.merge($weights, ("medium": 700));
// ("light": 300, "medium": 700)
```

**Sass**

```sass
@use "sass:map";

$weights: ("light": 300, "medium": 500)

@debug map.merge($weights, ("medium": 700))
// ("light": 300, "medium": 700)
```

Обратите внимание: поскольку карты Sass [неизменяемы][], `map.set()` и `map.merge()` не
изменяют исходный список.

[неизменяемы]: #immutability

## Неизменяемость

Карты в Sass *неизменяемы* — это значит, что содержимое значения-карты никогда не
меняется. Все функции для работы с картами в Sass возвращают новые карты, а не
изменяют исходные. Неизменяемость помогает избежать множества коварных ошибок,
которые могут возникнуть, если одна и та же карта используется в разных частях
таблицы стилей.

Тем не менее вы всё ещё можете обновлять состояние во времени, присваивая новые карты
той же переменной. Это часто используется в функциях и миксинах для отслеживания
конфигурации в карте.

**SCSS**

```scss
@use "sass:map";

$prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);

@mixin add-browser-prefix($browser, $prefix) {
  $prefixes-by-browser: map.merge($prefixes-by-browser, ($browser: $prefix)) !global;
}

@include add-browser-prefix("opera", o);
@debug $prefixes-by-browser;
// ("firefox": moz, "safari": webkit, "ie": ms, "opera": o)
```

**Sass**

```sass
@use "sass:map"

$prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms)

@mixin add-browser-prefix($browser, $prefix)
  $prefixes-by-browser: map.merge($prefixes-by-browser, ($browser: $prefix)) !global


@include add-browser-prefix("opera", o)
@debug $prefixes-by-browser
// ("firefox": moz, "safari": webkit, "ie": ms, "opera": o)
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/maps/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

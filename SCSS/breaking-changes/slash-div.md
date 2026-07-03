# Ломающее изменение: слэш как деление

В настоящее время Sass трактует `/` как операцию деления в одних контекстах и как разделитель — в других. Из-за этого пользователям Sass сложно понять, что будет означать конкретный `/`, а также усложняется работа с новыми возможностями CSS, использующими `/` как разделитель.

**Совместимость:** Dart Sass: частичная поддержка · LibSass: ✗ · Ruby Sass: ✗


Сегодня Sass использует [сложную эвристику][], чтобы понять, следует ли трактовать `/` как деление или как разделитель. Но даже в роли разделителя `/` просто создаёт строку без кавычек, которую трудно анализировать средствами Sass. По мере того как всё больше возможностей CSS, таких как [CSS Grid][] и [новый синтаксис `rgb()` и `hsl()`][], используют `/` как разделитель, это становится всё более болезненным для пользователей Sass.

[сложную эвристику]: https://sass-lang.com/documentation/operators/numeric#slash-separated-values
[CSS Grid]: https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row
[новый синтаксис `rgb()` и `hsl()`]: https://drafts.csswg.org/css-color/#rgb-functions

Поскольку Sass является надмножеством CSS, мы приводим синтаксис в соответствие с CSS, переопределяя `/` так, чтобы он был *только* разделителем. `/` будет трактоваться как новый тип разделителя списка, подобно тому как сегодня работает `,`. Деление вместо этого будет записываться с помощью новой функции `math.div()`. Эта функция будет вести себя точно так же, как сегодня ведёт себя `/`.

Это устаревание не затрагивает использование `/` внутри выражений `calc()`.

**SCSS**

```scss
@use "sass:math";

// Future Sass, doesn't work yet!
.item3 {
  $row: span math.div(6, 2) / 7; // A two-element slash-separated list.
  grid-row: $row;
}
```

**Sass**

```sass
@use "sass:math"

// Future Sass, doesn't work yet!
.item3
  $row: span math.div(6, 2) / 7 // A two-element slash-separated list.
  grid-row: $row
```

**Результат CSS**

```css
.item3 {
  grid-row: span 3 / 7;
}
```

## Переходный период

**Совместимость (`math.div()` и `list.slash()`):** Dart Sass: начиная с 1.33.0 · LibSass: ✗ · Ruby Sass: ✗


Чтобы облегчить переход, мы начали с добавления функции `math.div()`. Оператор `/` пока по-прежнему выполняет деление, но при этом выводит предупреждение об устаревании. Пользователям следует переключить все операции деления на использование `math.div()`.

> 💡 **Занятный факт**
>
> Помните, что вы можете отключить предупреждения об устаревании для библиотек, которые вы не контролируете! Если вы используете интерфейс командной строки, передайте [флаг `--quiet-deps`], а если используете JavaScript API — установите [опцию `quietDeps`] в `true`.
>
> [флаг `--quiet-deps`]: https://sass-lang.com/documentation/cli/dart-sass/#quiet-deps
> [опцию `quietDeps`]: https://sass-lang.com/documentation/js-api/interfaces/StringOptions#quietDeps


**SCSS**

```scss
@use "sass:math";

// WRONG, will not work in future Sass versions.
@debug (12px/4px); // 3

// RIGHT, will work in future Sass versions.
@debug math.div(12px, 4px); // 3
```

**Sass**

```sass
@use "sass:math"

// WRONG, will not work in future Sass versions.
@debug (12px/4px) // 3

// RIGHT, will work in future Sass versions.
@debug math.div(12px, 4px) // 3
```

Слэш-разделённые списки также будут доступны в переходный период. Поскольку их пока нельзя создать с помощью `/`, будет добавлена функция `list.slash()`, позволяющая их создавать. Также вы сможете передавать `"slash"` как `$separator` в [функцию `list.join()`][] и [функцию `list.append()`][].

[функцию `list.join()`]: https://sass-lang.com/documentation/modules/list#join
[функцию `list.append()`]: https://sass-lang.com/documentation/modules/list#append

**SCSS**

```scss
@use "sass:list";
@use "sass:math";

.item3 {
  $row: list.slash(span math.div(6, 2), 7);
  grid-row: $row;
}
```

**Sass**

```sass
@use "sass:list"
@use "sass:math"

.item3
  $row: list.slash(span math.div(6, 2), 7)
  grid-row: $row
```

**Результат CSS**

```css
.item3 {
  grid-row: span 3 / 7;
}
```

**Совместимость (First-class calc):** Dart Sass: начиная с 1.40.0 · LibSass: ✗ · Ruby Sass: ✗


В качестве альтернативы можно обернуть операции деления в выражение `calc()`, которое Sass упростит до одного числа.

**SCSS**

```scss
// WRONG, will not work in future Sass versions.
@debug (12px/4px); // 3

// RIGHT, will work in future Sass versions.
@debug calc(12px / 4px); // 3
```

**Sass**

```sass
// WRONG, will not work in future Sass versions.
@debug (12px/4px) // 3

// RIGHT, will work in future Sass versions.
@debug calc(12px / 4px) // 3
```

## Автоматическая миграция

Вы можете использовать [Sass-мигратор][], чтобы автоматически обновить ваши таблицы стилей и перейти на использование `math.div()` и `list.slash()`.

[Sass-мигратор]: https://github.com/sass/migrator#readme

```shellsession
$ npm install -g sass-migrator
$ sass-migrator division **/*.scss
```

## Можно ли отключить предупреждения?

Sass предоставляет широкий набор параметров для управления тем, какие предупреждения об устаревании вы видите и когда.

### Краткий и подробный режимы

По умолчанию Sass работает в кратком режиме: для каждого типа предупреждения об устаревании выводится не более пяти сообщений, после чего дополнительные предупреждения подавляются. Это помогает пользователям узнавать о предстоящих ломающих изменениях, не создавая избыточного шума в консоли.

Если вместо этого запустить Sass в подробном режиме, он будет выводить *каждое* встреченное предупреждение об устаревании. Это может быть полезно для отслеживания оставшегося объёма работы по исправлению устаревшего кода. Включить подробный режим можно с помощью

[флага `--verbose`] в командной строке, либо

[опции `verbose`] в JavaScript API.

[флага `--verbose`]: https://sass-lang.com/documentation/cli/dart-sass/#verbose
[опции `verbose`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#verbose

> ⚠️ **Внимание**
>
> При запуске через [JS API] Sass не передаёт информацию между разными компиляциями, поэтому по умолчанию он выводит по пять предупреждений для *каждой* компилируемой таблицы стилей. Однако это можно исправить, написав собственный [`Logger`] (или попросив автора Sass-плагина для вашего любимого фреймворка сделать это), который выводит не более пяти ошибок на каждое устаревание и может использоваться совместно в нескольких компиляциях.
>
> [JS API]: https://sass-lang.com/documentation/js-api/
> [`Logger`]: https://sass-lang.com/documentation/js-api/interfaces/Logger-1/

### Отключение предупреждений для зависимостей

Иногда в зависимостях появляются предупреждения об устаревании, на которые вы не можете повлиять. Отключить предупреждения об устаревании для зависимостей, сохранив их вывод для вашего приложения, можно с помощью

[флага `--quiet-deps`] в командной строке, либо

[опции `quietDeps`] в JavaScript API.

[флага `--quiet-deps`]: https://sass-lang.com/documentation/cli/dart-sass/#quiet-deps
[опции `quietDeps`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#quietDeps

В контексте этого флага «зависимостью» считается любая таблица стилей, которая не является просто цепочкой относительных загрузок из точки входа. Это означает любую таблицу стилей, загруженную из пути загрузки, а также большинство таблиц стилей, загруженных через пользовательские импортёры (custom importers).

### Отключение конкретных предупреждений об устаревании

Если вы точно знаете, что конкретное устаревание не представляет для вас проблемы, вы можете отключить предупреждения именно для него с помощью

[флага `--silence-deprecation`] в командной строке, либо

[опции `silenceDeprecations`] в JavaScript API.

[флага `--silence-deprecation`]: https://sass-lang.com/documentation/cli/dart-sass/#silence-deprecation
[опции `silenceDeprecations`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#silenceDeprecations

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/slash-div/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

# Ломающее изменение: обычная CSS-функция if()

Устаревшая (legacy) функция `if()` в Sass объявляется устаревшей в пользу официального синтаксиса CSS-функции `if()`. Этот синтаксис позволяет свободно комбинировать условия Sass и CSS.

**Совместимость:** Dart Sass: начиная с 1.95.0 · LibSass: ✗ · Ruby Sass: ✗


В 2010 году, вскоре после добавления [логического типа значений], Sass добавил глобальную функцию `if()` как способ легко использовать логические значения в одном выражении, не прибегая к написанию целого [правила `@if`]. Эта функция имела сигнатуру `if($condition, $if-true, $if-false)` и возвращала `$if-true`, если `$condition` являлось [истинным значением], и `$if-false` в противном случае.

[логического типа значений]: https://sass-lang.com/documentation/values/booleans
[правила `@if`]: https://sass-lang.com/documentation/at-rules/control/if
[истинным значением]: https://sass-lang.com/documentation/at-rules/control/if#truthiness-and-falsiness

В то время браузеры не поддерживали даже `@media`-запросы, и мы даже представить не могли, что когда-нибудь CSS обзаведётся собственной функцией `if()`. Но пятнадцать лет спустя поддержка [CSS-функции `if()`] начала появляться в браузерах, и нам пришлось сделать то же самое, чтобы оставаться полностью совместимыми с CSS.

[CSS-функции `if()`]: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/if

Теперь Sass поддерживает [синтаксис обычной CSS-функции `if()`], а также специальное условие `sass(...)`, которое вычисляет выражения Sass. Чтобы избежать избыточности и стандартизироваться на наиболее CSS-совместимом варианте, мы планируем в конечном счёте удалить из языка устаревшую функцию `if()`.

[синтаксис обычной CSS-функции `if()`]: https://sass-lang.com/documentation/syntax/special-functions#if

Вы можете использовать [мигратор Sass] для автоматического перехода от устаревшей функции `if()` к синтаксису CSS-функции `if()`.

[мигратор Sass]: https://sass-lang.com/documentation/cli/migrator/#if

**SCSS**

```scss
@use 'sass:meta';

// Instead of if(true, 10px, 15px)
@debug if(sass(true): 10px; else: 15px);

// Instead of if(meta.variable-defined($var), $var, null)
@debug if(sass(meta.variable-defined($var)): $var);
```

**Sass**

```sass
@use 'sass:meta'

// Instead of if(true, 10px, 15px)
@debug if(sass(true): 10px; else: 15px)

// Instead of if(meta.variable-defined($var), $var, null)
@debug if(sass(meta.variable-defined($var)): $var)
```


## Как отключить предупреждения?

Sass предоставляет широкий набор опций для управления тем, какие предупреждения об устаревании вы видите и когда.

### Краткий и подробный режим

По умолчанию Sass работает в кратком режиме (terse mode), в котором каждый вид предупреждения об устаревании выводится не более пяти раз, после чего дальнейшие предупреждения подавляются. Это помогает пользователям узнавать о предстоящих ломающих изменениях, не создавая при этом излишний шум в консоли.

Если же запустить Sass в подробном режиме (verbose mode), он будет выводить *каждое* встреченное предупреждение об устаревании. Это может быть полезно для отслеживания того, сколько работы осталось для устранения устареваний. Включить подробный режим можно с помощью

[флага `--verbose`] в командной строке, или

[опции `verbose`] в JavaScript API.

[флага `--verbose`]: https://sass-lang.com/documentation/cli/dart-sass/#verbose
[опции `verbose`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#verbose

> ⚠️ **Внимание**
>
> При запуске из [JS API] Sass не сохраняет никакой информации между отдельными компиляциями, поэтому по умолчанию для *каждой* компилируемой таблицы стилей будет выведено до пяти предупреждений. Однако это можно исправить, написав (или попросив автора вашего любимого фреймворкового плагина Sass написать) [пользовательский `Logger`], который выводит не более пяти ошибок на каждое устаревание и может использоваться совместно для нескольких компиляций.

[JS API]: https://sass-lang.com/documentation/js-api/
[пользовательский `Logger`]: https://sass-lang.com/documentation/js-api/interfaces/Logger-1/

### Отключение предупреждений в зависимостях

Иногда в ваших зависимостях появляются предупреждения об устаревании, с которыми вы ничего не можете поделать. Вы можете отключить предупреждения об устаревании из зависимостей, продолжая при этом выводить их для вашего приложения, с помощью

[флага `--quiet-deps`] в командной строке, или

[опции `quietDeps`] в JavaScript API.

[флага `--quiet-deps`]: https://sass-lang.com/documentation/cli/dart-sass/#quiet-deps
[опции `quietDeps`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#quietDeps

Для целей этого флага «зависимостью» считается любая таблица стилей, которая не является просто цепочкой относительных загрузок из входной таблицы стилей. Это означает всё, что приходит из пути загрузки, а также большинство таблиц стилей, загружаемых через пользовательские импортеры (custom importers).

### Отключение отдельных предупреждений

Если вы знаете, что конкретное устаревание не представляет для вас проблемы, вы можете отключить предупреждения именно для него с помощью

[флага `--silence-deprecation`] в командной строке, или

[опции `silenceDeprecations`] в JavaScript API.

[флага `--silence-deprecation`]: https://sass-lang.com/documentation/cli/dart-sass/#silence-deprecation
[опции `silenceDeprecations`]: https://sass-lang.com/documentation/js-api/interfaces/Options/#silenceDeprecations

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/if-function/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

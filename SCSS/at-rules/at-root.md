# @at-root

Правило `@at-root` обычно записывается как `@at-root <selector> { ... }` и
приводит к тому, что всё, что находится внутри него, выводится в корень документа
вместо обычной вложенности. Чаще всего оно используется при [продвинутой
вложенности](https://sass-lang.com/documentation/style-rules/parent-selector#advanced-nesting) с
[родительским селектором
SassScript](https://sass-lang.com/documentation/style-rules/parent-selector#in-sassscript) и
[функциями селекторов](https://sass-lang.com/documentation/modules/selector).

Например, предположим, вы хотите написать селектор, который соответствует внешнему
селектору *и* селектору элемента. Вы могли бы написать миксин наподобие этого, который
использует [функцию `selector.unify()`][] для объединения `&` с селектором пользователя.

[функция `selector.unify()`]: https://sass-lang.com/documentation/modules/selector#unify

**SCSS**

```scss
@use "sass:selector";

@mixin unify-parent($child) {
  @at-root #{selector.unify(&, $child)} {
    @content;
  }
}

.wrapper .field {
  @include unify-parent("input") {
    /* ... */
  }
  @include unify-parent("select") {
    /* ... */
  }
}
```

**Sass**

```sass
@use "sass:selector"

@mixin unify-parent($child)
  @at-root #{selector.unify(&, $child)}
    @content



.wrapper .field
  @include unify-parent("input")
    /* ... */

  @include unify-parent("select")
    /* ... */
```

**CSS output**

```css
.wrapper input.field {
  /* ... */
}

.wrapper select.field {
  /* ... */
}
```


Правило `@at-root` необходимо здесь, потому что Sass не знает, какая интерполяция
использовалась для генерации селектора, когда он выполняет вложение селекторов. Это
означает, что он автоматически добавит внешний селектор к внутреннему селектору *даже
если* вы использовали `&` как выражение SassScript. `@at-root` явно указывает Sass не
включать внешний селектор (хотя он _всегда_ будет включён в `&` как выражение).

> 💡 **Занятный факт**
>
> Правило `@at-root` также можно записать как `@at-root { ... }`, чтобы поместить
> несколько стилевых правил в корень документа. По сути, `@at-root <selector> { ...
> }` — это просто сокращённая запись для `@at-root { <selector> { ... } }`!

## За пределами стилевых правил

Сам по себе `@at-root` избавляется только от [стилевых правил][]. Любые at-правила,
такие как [`@media`][] или [`@supports`][], останутся на месте. Однако если это не то,
что вам нужно, вы можете точно контролировать, что он включает или исключает, с
помощью синтаксиса наподобие [функций медиазапросов][], записываемого как `@at-root
(with: <rules...>) { ... }` или `@at-root (without: <rules...>) { ... }`. Запрос
`(without: ...)` указывает Sass, какие правила следует исключить; запрос
`(with: ...)` исключает все правила *кроме* тех, что перечислены.

[стилевых правил]: https://sass-lang.com/documentation/style-rules
[`@media`]: https://sass-lang.com/documentation/at-rules/css#media
[`@supports`]: https://sass-lang.com/documentation/at-rules/css#supports
[функций медиазапросов]: https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Targeting_media_features

**SCSS**

```scss
@media print {
  .page {
    width: 8in;

    @at-root (without: media) {
      color: #111;
    }

    @at-root (with: rule) {
      font-size: 1.2em;
    }
  }
}
```

**Sass**

```sass
@media print
  .page
    width: 8in

    @at-root (without: media)
      color: #111


    @at-root (with: rule)
      font-size: 1.2em
```

**CSS output**

```css
@media print {
  .page {
    width: 8in;
  }
}
.page {
  color: #111;
}
.page {
  font-size: 1.2em;
}
```

Помимо имён at-правил, в запросах можно использовать два специальных значения:

* `rule` относится к стилевым правилам. Например, `@at-root (with: rule)` исключает
  все at-правила, но сохраняет стилевые правила.

* `all` относится ко всем at-правилам, *и* стилевые правила при этом также должны
  быть исключены.

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/at-root/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

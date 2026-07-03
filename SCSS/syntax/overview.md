# Синтаксис

Sass поддерживает два разных синтаксиса. Каждый из них может подключать
(load) другой, так что вам и вашей команде решать, какой выбрать.

## SCSS

Синтаксис SCSS использует расширение файла `.scss`. За немногими небольшими
исключениями это надмножество CSS, а значит, по сути, **весь допустимый CSS
является допустимым SCSS**. Благодаря сходству с CSS это самый простой
синтаксис для освоения и самый популярный.

SCSS выглядит так:

```scss
@mixin button-base() {
  @include typography(button);
  @include ripple-surface;
  @include ripple-radius-bounded;

  display: inline-flex;
  position: relative;
  height: $button-height;
  border: none;
  vertical-align: middle;

  &:hover {
    cursor: pointer;
  }

  &:disabled {
    color: $mdc-button-disabled-ink-color;
    cursor: default;
    pointer-events: none;
  }
}
```

## Отступный синтаксис

Отступный синтаксис был оригинальным синтаксисом Sass, поэтому он использует
расширение файла `.sass`. Из-за этого расширения его иногда называют просто
«Sass». Отступный синтаксис поддерживает те же возможности, что и SCSS, но
использует отступы вместо фигурных скобок и точек с запятой для описания
структуры документа.

В целом, везде, где в CSS или SCSS вы бы написали фигурные скобки, в
отступном синтаксисе можно просто сделать отступ на один уровень глубже. И
везде, где строка заканчивается в месте, где может закончиться инструкция,
это засчитывается как точка с запятой. Есть также несколько дополнительных
отличий отступного синтаксиса, которые отмечены по всему справочнику.

Отступный синтаксис выглядит так:

```sass
@mixin button-base()
  @include typography(button)
  @include ripple-surface
  @include ripple-radius-bounded

  display: inline-flex
  position: relative
  height: $button-height
  border: none
  vertical-align: middle

  &:hover
    cursor: pointer

  &:disabled
    color: $mdc-button-disabled-ink-color
    cursor: default
    pointer-events: none
```

### Многострочные инструкции

**Совместимость:** Dart Sass: начиная с 1.84.0 · LibSass: ✗ · Ruby Sass: ✗


В отступном синтаксисе инструкции могут занимать несколько строк, если
переносы строк происходят в местах, где инструкция не может закончиться. Сюда
относятся места внутри круглых или других скобок, а также между ключевыми
словами в специфичном для Sass at-правиле.

```sass
.grid
  display: grid
  grid-template: (
    "header" min-content
    "main" 1fr
  )

@for 
  $i from 
  1 through 3
    ul:nth-child(3n + #{$i})
      margin-left: $i * 10
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/syntax/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

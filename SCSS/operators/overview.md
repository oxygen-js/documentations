# Операторы

Sass поддерживает набор полезных `операторов` для работы с различными
значениями. Среди них — стандартные математические операторы, такие как `+` и `*`, а
также операторы для различных других типов:

- [`==` и `!=`](https://sass-lang.com/documentation/operators/equality) используются для проверки, одинаковы ли
  два значения.
- [`+`, `-`, `*`, `/` и `%`](https://sass-lang.com/documentation/operators/numeric) имеют
  обычное математическое значение для чисел, с особым поведением для единиц измерения, которое
  соответствует использованию единиц измерения в научной математике.
- [`<`, `<=`, `>` и `>=`](https://sass-lang.com/documentation/operators/relational) проверяют,
  больше или меньше одно число другого.
- [`and`, `or` и `not`](https://sass-lang.com/documentation/operators/boolean) имеют обычное
  логическое поведение. Sass считает «истинным» любое значение, кроме `false` и
  `null`.
- [`+`, `-` и `/`](https://sass-lang.com/documentation/operators/string) можно использовать для
  конкатенации строк.



> ⚠️ **Внимание**
>
> На заре истории Sass в него была добавлена поддержка математических операций над
> [цветами][]. Эти операции выполнялись над каждым из RGB-каналов цвета
> по отдельности, поэтому сложение двух цветов давало цвет, у которого красный канал был
> суммой их красных каналов, и так далее.
>
> [цветами]: https://sass-lang.com/documentation/values/colors
>
> Это поведение оказалось не очень полезным, поскольку поканальная арифметика RGB
> плохо соответствовала тому, как люди воспринимают цвет. Были добавлены [функции для работы с цветом][],
> которые гораздо полезнее, а операции над цветами были признаны устаревшими.
> Они всё ещё поддерживаются в LibSass и Ruby Sass, но выдают предупреждения,
> и пользователям настоятельно рекомендуется их избегать.
>
> [функции для работы с цветом]: https://sass-lang.com/documentation/modules/color

## Порядок операций

В Sass действует довольно стандартный [порядок операций][], от самого строгого к самому
слабому:

[порядок операций]: https://en.wikipedia.org/wiki/Order_of_operations#Programming_languages

1. Унарные операторы [`not`][], [`+`, `-`][] и [`/`][].
2. Операторы [`*`, `/` и `%`][].
3. Операторы [`+` и `-`][].
4. Операторы [`>`, `>=`, `<` и `<=`][].
5. Операторы [`==` и `!=`][].
6. Оператор [`and`][].
7. Оператор [`or`][].
8. Оператор [`=`][], когда он доступен.

[`not`]: https://sass-lang.com/documentation/operators/boolean
[`+`, `-`]: https://sass-lang.com/documentation/operators/numeric#unary-operators
[`/`]: https://sass-lang.com/documentation/operators/string#unary-operators
[`*`, `/` и `%`]: https://sass-lang.com/documentation/operators/numeric
[`+` и `-`]: https://sass-lang.com/documentation/operators/numeric
[`>`, `>=`, `<` и `<=`]: https://sass-lang.com/documentation/operators/relational
[`==` и `!=`]: https://sass-lang.com/documentation/operators/equality
[`and`]: https://sass-lang.com/documentation/operators/boolean
[`or`]: https://sass-lang.com/documentation/operators/boolean
[`=`]: #single-equals

**SCSS**

```scss
@debug 1 + 2 * 3 == 1 + (2 * 3); // true
@debug true or false and false == true or (false and false); // true
```

**Sass**

```sass
@debug 1 + 2 * 3 == 1 + (2 * 3)  // true
@debug true or false and false == true or (false and false)  // true
```

### Скобки

Вы можете явно управлять порядком операций с помощью скобок. Операция внутри
скобок всегда выполняется раньше любых операций вне их. Скобки можно даже
вкладывать друг в друга — в этом случае первыми будут вычислены самые внутренние
скобки.

**SCSS**

```scss
@debug (1 + 2) * 3; // 9
@debug ((1 + 2) * 3 + 4) * 5; // 65
```

**Sass**

```sass
@debug (1 + 2) * 3  // 9
@debug ((1 + 2) * 3 + 4) * 5  // 65
```

## Одиночное равно

Sass поддерживает специальный оператор `=`, который допустим только в аргументах функций
и просто создаёт [строку без кавычек][], в которой два операнда разделены
`=`. Он существует ради обратной совместимости с очень старым синтаксисом, использовавшимся только в IE.

[строку без кавычек]: https://sass-lang.com/documentation/values/strings#unquoted

**SCSS**

```scss
.transparent-blue {
  filter: chroma(color=#0000ff);
}
```

**Sass**

```sass
.transparent-blue
  filter: chroma(color=#0000ff)
```

**Результат CSS**

```css
.transparent-blue {
  filter: chroma(color=#0000ff);
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/operators/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

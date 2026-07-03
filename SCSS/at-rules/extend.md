# @extend

При проектировании страницы часто возникают случаи, когда один класс должен
иметь все стили другого класса, а также свои собственные специфические стили.
Например, [методология BEM](http://getbem.com/naming/) поощряет
классы-модификаторы, которые размещаются на тех же элементах, что и классы
блока или элемента. Но это может привести к загромождённому HTML, к ошибкам
из-за забытого включения обоих классов, а также может привнести
несемантические стилевые заботы в вашу разметку.

<!-- TODO(jina): I think these code blocks should be side-by-side -->
```html
<div class="error error--serious">
  Oh no! You've been hacked!
</div>
```

```css
.error {
  border: 1px #f00;
  background-color: #fdd;
}

.error--serious {
  border-width: 3px;
}
```

Правило Sass `@extend` решает эту проблему. Оно записывается как
`@extend <селектор>` и сообщает Sass, что один селектор должен наследовать
стили другого.

**SCSS**

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;

  &--serious {
    @extend .error;
    border-width: 3px;
  }
}
```

**Sass**

```sass
.error
  border: 1px #f00
  background-color: #fdd

  &--serious
    @extend .error
    border-width: 3px
```

**Результат CSS**

```css
.error, .error--serious {
  border: 1px #f00;
  background-color: #fdd;
}
.error--serious {
  border-width: 3px;
}
```

Когда один класс расширяет другой, Sass стилизует все элементы, которые
соответствуют расширяющему классу, так, как будто они соответствуют и
расширяемому классу. Когда один селектор класса расширяет другой, это
работает в точности так, как если бы вы добавили расширяемый класс к каждому
элементу в вашем HTML, у которого уже есть расширяющий класс. Вы можете просто
написать `class="error--serious"`, и Sass позаботится о том, чтобы он был
стилизован так, как если бы у него также был `class="error"`.

Разумеется, селекторы используются в стилевых правилах не только сами по
себе. Sass умеет расширять *везде*, где используется селектор. Это
гарантирует, что ваши элементы будут стилизованы в точности так, как если бы
они соответствовали расширяемому селектору.

**SCSS**

```scss
.error:hover {
  background-color: #fee;
}

.error--serious {
  @extend .error;
  border-width: 3px;
}
```

**Sass**

```sass
.error:hover
  background-color: #fee


.error--serious
  @extend .error
  border-width: 3px
```

**Результат CSS**

```css
.error:hover, .error--serious:hover {
  background-color: #fee;
}

.error--serious {
  border-width: 3px;
}
```

> ⚠️ **Внимание**
>
> Расширения разрешаются после того, как остальная часть вашей таблицы стилей
> скомпилирована. В частности, это происходит после разрешения
> [родительских селекторов][parent selectors]. Это означает, что если вы
> сделаете `@extend .error`, это не повлияет на внутренний селектор в
> `.error { &__icon { ... } }`. Это также означает, что
> [родительские селекторы в SassScript][parent selectors in SassScript] не
> могут видеть результаты расширения.

[parent selectors]: https://sass-lang.com/documentation/style-rules/parent-selector
[parent selectors in SassScript]: https://sass-lang.com/documentation/style-rules/parent-selector#in-sassscript

## Как это работает

В отличие от [миксинов][mixins], которые копируют стили в текущее стилевое
правило, `@extend` обновляет стилевые правила, содержащие расширяемый
селектор, так, чтобы они содержали и расширяющий селектор. При расширении
селекторов Sass выполняет *интеллектуальное объединение*:

[mixins]: https://sass-lang.com/documentation/at-rules/mixin

* Он никогда не генерирует селекторы вроде `#main#footer`, которые в принципе
  не могут соответствовать ни одному элементу.

* Он гарантирует, что сложные селекторы чередуются так, чтобы работать
  независимо от того, в каком порядке вложены элементы HTML.

* Он максимально обрезает избыточные селекторы, при этом гарантируя, что
  специфичность будет больше или равна специфичности расширяющего селектора.

* Он умеет определять, когда один селектор соответствует всему, чему
  соответствует другой, и может объединять их.

* Он интеллектуально обрабатывает [комбинаторы][combinators],
  [универсальные селекторы][universal selectors] и
  [псевдоклассы, содержащие селекторы][pseudo-classes that contain selectors].

[combinators]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators
[pseudo-classes that contain selectors]: https://developer.mozilla.org/en-US/docs/Web/CSS/:not
[universal selectors]: https://developer.mozilla.org/en-US/docs/Web/CSS/Universal_selectors

**SCSS**

```scss
.content nav.sidebar {
  @extend .info;
}

// This won't be extended, because `p` is incompatible with `nav`.
p.info {
  background-color: #dee9fc;
}

// There's no way to know whether `<div class="guide">` will be inside or
// outside `<div class="content">`, so Sass generates both to be safe.
.guide .info {
  border: 1px solid rgba(#000, 0.8);
  border-radius: 2px;
}

// Sass knows that every element matching "main.content" also matches ".content"
// and avoids generating unnecessary interleaved selectors.
main.content .info {
  font-size: 0.8em;
}
```

**Sass**

```sass
.content nav.sidebar
  @extend .info


// This won't be extended, because `p` is incompatible with `nav`.
p.info
  background-color: #dee9fc


// There's no way to know whether `<div class="guide">` will be inside or
// outside `<div class="content">`, so Sass generates both to be safe.
.guide .info
  border: 1px solid rgba(#000, 0.8)
  border-radius: 2px


// Sass knows that every element matching "main.content" also matches ".content"
// and avoids generating unnecessary interleaved selectors.
main.content .info
  font-size: 0.8em
```

**Результат CSS**

```css
p.info {
  background-color: #dee9fc;
}

.guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar {
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 2px;
}

main.content .info, main.content nav.sidebar {
  font-size: 0.8em;
}
```

> 💡 **Занятный факт**
>
> Вы можете напрямую использовать интеллектуальное объединение Sass с помощью
> [функций селекторов][selector functions]! Функция
> [`selector.unify()`][`selector.unify()` function] возвращает селектор,
> соответствующий пересечению двух селекторов, а функция
> [`selector.extend()`][`selector.extend()` function] работает точно так же,
> как `@extend`, но для одного селектора.

[selector functions]: https://sass-lang.com/documentation/modules/selector
[`selector.unify()` function]: https://sass-lang.com/documentation/modules/selector#unify
[`selector.extend()` function]: https://sass-lang.com/documentation/modules/selector#extend

> ⚠️ **Внимание**
>
> Поскольку `@extend` обновляет стилевые правила, содержащие расширяемый
> селектор, их стили имеют приоритет в [каскаде][the cascade] в зависимости от
> того, где расположены стилевые правила расширяемого селектора, а *не* от
> того, где расположен `@extend`. Это может сбивать с толку, но просто
> помните: это тот же самый приоритет, который эти правила имели бы, если бы
> вы добавили расширяемый класс в свой HTML!

[the cascade]: https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade

## Селекторы-заглушки

Иногда вам нужно написать стилевое правило, которое предназначено *только*
для расширения. В этом случае вы можете использовать
[селекторы-заглушки][placeholder selectors], которые выглядят как селекторы
классов, начинающиеся с `%` вместо `.`. Любые селекторы, включающие
заглушки, не попадают в результирующий CSS, а вот селекторы, которые их
расширяют, — попадают.

[placeholder selectors]: https://sass-lang.com/documentation/style-rules/placeholder-selectors

**SCSS**

```scss
.alert:hover, %strong-alert {
  font-weight: bold;
}

%strong-alert:hover {
  color: red;
}
```

**Sass**

```sass
.alert:hover, %strong-alert
  font-weight: bold


%strong-alert:hover
  color: red
```

**Результат CSS**

```css
.alert:hover {
  font-weight: bold;
}
```


### Приватные заглушки

Как и [члены модуля][module members], селектор-заглушка можно сделать
приватным, начав его имя с `-` или `_`. Приватный селектор-заглушку можно
расширять только внутри той таблицы стилей, в которой он определён. Для
любых других таблиц стилей будет выглядеть так, будто этот селектор не
существует.

[module members]: https://sass-lang.com/documentation/at-rules/use#private-members

## Область видимости расширения

Когда одна таблица стилей расширяет селектор, это расширение затронет только
стилевые правила, написанные в *вышестоящих* (upstream) модулях — то есть в
модулях, загружаемых этой таблицей стилей с помощью правила [`@use`][`@use` rule]
или правила [`@forward`][`@forward` rule], в модулях, загружаемых *этими*
модулями, и так далее. Это делает ваши правила `@extend` более
предсказуемыми, гарантируя, что они затрагивают только те стили, о которых вы
знали, когда их писали.

[`@use` rule]: https://sass-lang.com/documentation/at-rules/use
[`@forward` rule]: https://sass-lang.com/documentation/at-rules/forward

> ⚠️ **Внимание**
>
> При использовании правила [`@import`][`@import` rule] расширения вообще не
> имеют области видимости. Они не только повлияют на каждую таблицу стилей,
> которую вы импортируете, но и на каждую таблицу стилей, которая импортирует
> вашу, на всё, что импортируют эти таблицы стилей, и так далее. Без `@use`
> расширения являются *глобальными*.

[`@import` rule]: https://sass-lang.com/documentation/at-rules/import

## Обязательные и необязательные расширения

Обычно, если `@extend` не соответствует ни одному селектору в таблице стилей,
Sass выдаст ошибку. Это защищает от опечаток или от переименования селектора
без переименования селекторов, которые от него наследуют. Расширения,
требующие существования расширяемого селектора, называются *обязательными*.

Однако это не всегда то, что вам нужно. Если вы хотите, чтобы `@extend`
ничего не делал в случае, если расширяемый селектор не существует, просто
добавьте `!optional` в конец.

## Расширения или миксины?

Расширения и [миксины][mixins] — это два способа инкапсуляции и повторного
использования стилей в Sass, что закономерно поднимает вопрос о том, когда
какой из них использовать. Миксины, очевидно, необходимы, когда вам нужно
настраивать стили с помощью [аргументов][arguments], но что если это просто
кусок стилей?

[mixins]: https://sass-lang.com/documentation/at-rules/mixin
[arguments]: https://sass-lang.com/documentation/at-rules/mixin/#arguments

Как правило, расширения — лучший вариант, когда вы выражаете отношение между
семантическими классами (или другими семантическими селекторами). Поскольку
элемент с классом `.error--serious` *является* ошибкой, имеет смысл, чтобы он
расширял `.error`. Но для несемантических наборов стилей написание миксина
может избежать проблем с каскадом и облегчить настройку в дальнейшем.

> 💡 **Занятный факт**
>
> Большинство веб-серверов сжимают отдаваемый CSS с помощью алгоритма,
> который очень хорошо справляется с повторяющимися фрагментами одинакового
> текста. Это значит, что хотя миксины могут производить больше CSS, чем
> расширения, они, вероятно, не увеличат существенно объём данных, которые
> нужно скачать вашим пользователям. Поэтому выбирайте функциональность,
> которая имеет больше смысла для вашего случая использования, а не ту,
> которая генерирует наименьший объём CSS!

[gzip]: https://en.wikipedia.org/wiki/Gzip

## Ограничения

### Запрещённые селекторы

**Совместимость (без составных расширений):** Dart Sass: ✓ · LibSass: ✗ · Ruby Sass: ✗

LibSass и Ruby Sass в настоящее время позволяют расширять составные
  селекторы, такие как `.message.info`. Однако такое поведение не
  соответствует определению `@extend`: вместо того чтобы стилизовать
  элементы, соответствующие расширяющему селектору, так, как если бы у них
  был `class="message info"`, что было бы затронуто стилевыми правилами,
  включающими либо `.message`, *либо* `.info`, оно стилизовало их только
  правилами, включающими *и* `.message`, *и* `.info`.

  Чтобы сохранить определение `@extend` простым и понятным, а реализацию —
  чистой и эффективной, такое поведение теперь объявлено устаревшим и будет
  удалено в будущих версиях.

  См. подробности на [странице о ломающих изменениях][the breaking change page].

  [the breaking change page]: https://sass-lang.com/documentation/breaking-changes/extend-compound


Расширять можно только *простые селекторы* — отдельные селекторы вроде
`.info` или `a`. Если бы `.message.info` можно было расширить, определение
`@extend` означало бы, что элементы, соответствующие расширяющему селектору,
стилизовались бы так, как если бы они соответствовали `.message.info`. А это
то же самое, что соответствие и `.message`, и `.info`, поэтому не было бы
никакой пользы в том, чтобы писать так вместо `@extend .message, .info`.

Аналогично, если бы `.main .info` можно было расширить, это делало бы
(почти) то же самое, что и расширение `.info` само по себе. Тонкие различия
не стоят той путаницы, которую создаёт видимость выполнения чего-то
существенно иного, поэтому это тоже не разрешено.

**SCSS**

```scss
.alert {
  @extend .message.info;
  //      ^^^^^^^^^^^^^
  // Error: Write @extend .message, .info instead.

  @extend .main .info;
  //      ^^^^^^^^^^^
  // Error: write @extend .info instead.
}
```

**Sass**

```sass
.alert
  @extend .message.info
  //      ^^^^^^^^^^^^^
  // Error: Write @extend .message, .info instead.

  @extend .main .info
  //      ^^^^^^^^^^^
  // Error: write @extend .info instead.
```

### Эвристики HTML

Когда `@extend` [чередует сложные селекторы][interleaves complex selectors],
он не генерирует все возможные комбинации селекторов предков. Многие из
селекторов, которые он мог бы сгенерировать, вряд ли будут действительно
соответствовать реальному HTML, а генерация всех их сделала бы таблицы
стилей слишком большими ради очень небольшой реальной пользы. Вместо этого
используется [эвристика][heuristic]: предполагается, что предки каждого
селектора будут самодостаточными, не чередуясь с предками какого-либо
другого селектора.

[interleaves complex selectors]: #how-it-works
[heuristic]: https://en.wikipedia.org/wiki/Heuristic

**SCSS**

```scss
header .warning li {
  font-weight: bold;
}

aside .notice dd {
  // Sass doesn't generate CSS to match the <dd> in
  //
  // <header>
  //   <aside>
  //     <div class="warning">
  //       <div class="notice">
  //         <dd>...</dd>
  //       </div>
  //     </div>
  //   </aside>
  // </header>
  //
  // because matching all elements like that would require us to generate nine
  // new selectors instead of just two.
  @extend li;
}
```

**Sass**

```sass
header .warning li
  font-weight: bold


aside .notice dd
  // Sass doesn't generate CSS to match the <dd> in
  //
  // <header>
  //   <aside>
  //     <div class="warning">
  //       <div class="notice">
  //         <dd>...</dd>
  //       </div>
  //     </div>
  //   </aside>
  // </header>
  //
  // because matching all elements like that would require us to generate nine
  // new selectors instead of just two.
  @extend li
```

**Результат CSS**

```css
header .warning li, header .warning aside .notice dd, aside .notice header .warning dd {
  font-weight: bold;
}
```

### Расширение внутри `@media`

Хотя `@extend` разрешён внутри [`@media` и других at-правил CSS][`@media` and other CSS at-rules],
не разрешается расширять селекторы, которые появляются вне этого at-правила.
Это потому, что расширяющий селектор применяется только в рамках данного
медиа-контекста, и нет способа гарантировать сохранение этого ограничения в
сгенерированном селекторе без дублирования всего стилевого правила.

[`@media` and other CSS at-rules]: https://sass-lang.com/documentation/at-rules/css

**SCSS**

```scss
@media screen and (max-width: 600px) {
  .error--serious {
    @extend .error;
    //      ^^^^^^
    // Error: ".error" was extended in @media, but used outside it.
  }
}

.error {
  border: 1px #f00;
  background-color: #fdd;
}
```

**Sass**

```sass
@media screen and (max-width: 600px)
  .error--serious
    @extend .error
    //      ^^^^^^
    // Error: ".error" was extended in @media, but used outside it.



.error
  border: 1px #f00
  background-color: #fdd
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/extend/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

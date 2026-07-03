# Ломающее изменение: расширение составных селекторов

В настоящее время LibSass позволяет [расширять](https://sass-lang.com/documentation/at-rules/extend) составные селекторы, такие как `.message.info`, но способ, которым выполняется это расширение, не соответствует тому, как должен работать `@extend`.

**Совместимость:** Dart Sass: ✓ · LibSass: ✗ · Ruby Sass: ✗


Когда один селектор расширяет другой, Sass применяет стили ко всем элементам, соответствующим расширяющему селектору, так, как будто они также соответствуют расширяемому классу. Другими словами, если вы напишете `.heads-up {@extend .info}`, это будет работать так же, как если бы в вашем HTML вы заменили `class="heads-up"` на `class="heads-up info"`.

Следуя этой логике, можно было бы ожидать, что `.heads-up {@extend .message.info}` будет работать как замена `class="heads-up"` на `class="heads-up info message"`. Но сейчас в LibSass и Ruby Sass это работает иначе — вместо того чтобы добавлять `.heads-up` к каждому селектору, у которого есть *либо `.info`, либо `.message`*, он добавляется только к селекторам, у которых есть *`.info.message` вместе*.

**SCSS**

```scss
// These should both be extended, but they won't be.
.message {
  border: 1px solid black;
}
.info {
  font-size: 1.5rem;
}

.heads-up {
  @extend .message.info;
}
```

**Sass**

```sass
// These should both be extended, but they won't be.
.message
  border: 1px solid black

.info
  font-size: 1.5rem


.heads-up
  @extend .message.info
```

Чтобы исправить эту проблему, избежать дальнейшей путаницы и сохранить реализацию простой и эффективной, возможность расширять составные селекторы не поддерживается в Dart Sass и будет удалена в одной из будущих версий LibSass. Для совместимости вместо этого следует расширять каждый простой селектор отдельно:

**SCSS**

```scss
.message {
  border: 1px solid black;
}
.info {
  font-size: 1.5rem;
}

.heads-up {
  @extend .message, .info;
}
```

**Sass**

```sass
.message
  border: 1px solid black

.info
  font-size: 1.5rem


.heads-up
  @extend .message, .info
```

**CSS output**

```css
.message, .heads-up {
  border: 1px solid black;
}

.info, .heads-up {
  font-size: 1.5rem;
}
```

> ⚠️ **Внимание**
>
> Поскольку Sass не знает деталей HTML, который будет стилизоваться этим CSS, любому `@extend` может понадобиться сгенерировать дополнительные селекторы, которые не будут применяться конкретно к вашему HTML. Это особенно верно при отказе от расширения составных селекторов.
>
> В большинстве случаев эти дополнительные селекторы не вызовут никаких проблем и добавят к сжатому (gzip) CSS лишь пару лишних байт. Но некоторые таблицы стилей могут в большей степени полагаться на старое поведение. В этом случае мы рекомендуем заменить составной селектор на [селектор-заглушку][].
>
> [селектор-заглушку]: https://sass-lang.com/documentation/style-rules/placeholder-selectors
>
> **SCSS**
>
> ```scss
> // Instead of just `.message.info`.
> %message-info, .message.info {
>   border: 1px solid black;
>   font-size: 1.5rem;
> }
>
> .heads-up {
>   // Instead of `.message.info`.
>   @extend %message-info;
> }
> ```
>
> **Sass**
>
> ```sass
> // Instead of just `.message.info`.
> %message-info, .message.info
>   border: 1px solid black
>   font-size: 1.5rem
>
>
> .heads-up
>   // Instead of `.message.info`.
>   @extend %message-info
> ```
>
> **CSS output**
>
> ```css
> .heads-up, .message.info {
>   border: 1px solid black;
>   font-size: 1.5rem;
> }
> ```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/breaking-changes/extend-compound/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

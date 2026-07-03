# Функции как значения


**Совместимость (тип аргумента):** Dart Sass: ✓ · LibSass: начиная с 3.5.0 · Ruby Sass: начиная с 3.5.0

  В старых версиях LibSass и Ruby Sass [функция `call()`][] принимала
  строку, представляющую имя функции. Это было изменено, чтобы вместо этого
  принималось значение функции — в рамках подготовки к новой модульной
  системе, в которой функции больше не глобальны, и поэтому одно и то же имя
  не обязательно всегда ссылается на одну и ту же функцию.

  [функция `call()`]: https://sass-lang.com/documentation/modules/meta#call

  Передача строки в `call()` всё ещё работает во всех реализациях, но это
  устарело и будет запрещено в будущих версиях.



[Функции][] тоже могут быть значениями! Вы не можете напрямую записать
функцию как значение, но можете передать имя функции в
[функцию `meta.get-function()`][], чтобы получить её как значение. Когда у вас
есть значение функции, вы можете передать его в [функцию `meta.call()`][],
чтобы вызвать её. Это полезно для написания *функций высшего порядка*, которые
вызывают другие функции.

[Функции]: https://sass-lang.com/documentation/at-rules/function
[функцию `meta.get-function()`]: https://sass-lang.com/documentation/modules/meta#get-function
[функцию `meta.call()`]: https://sass-lang.com/documentation/modules/meta#call

**SCSS**

```scss
@use "sass:list";
@use "sass:meta";
@use "sass:string";

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition) {
  $new-list: ();
  $separator: list.separator($list);
  @each $element in $list {
    @if not meta.call($condition, $element) {
      $new-list: list.append($new-list, $element, $separator: $separator);
    }
  }
  @return $new-list;
}

$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;

.content {
  @function contains-helvetica($string) {
    @return string.index($string, "Helvetica");
  }
  font-family: remove-where($fonts, meta.get-function("contains-helvetica"));
}
```

**Sass**

```sass
@use "sass:list"
@use "sass:meta"
@use "sass:string"

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition)
  $new-list: ()
  $separator: list.separator($list)
  @each $element in $list
    @if not meta.call($condition, $element)
      $new-list: list.append($new-list, $element, $separator: $separator)


  @return $new-list


$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif

.content
  @function contains-helvetica($string)
    @return string.index($string, "Helvetica")

  font-family: remove-where($fonts, meta.get-function("contains-helvetica"))
```

**Результат CSS**

```css
.content {
  font-family: Tahoma, Geneva, Arial, sans-serif;
}
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/values/functions/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

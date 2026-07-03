# sass:list


**Совместимость:** Dart Sass: начиная с 1.23.0 · LibSass: ✗ · Ruby Sass: ✗

Только Dart Sass в настоящее время поддерживает загрузку встроенных модулей с помощью `@use`. Пользователям других реализаций вместо этого нужно вызывать функции по их глобальным именам.



> 💡 **Занятный факт**
>
> В Sass каждая [карта (map)][] считается списком, который содержит двухэлементный список для каждой пары ключ/значение. Например, `(1: 2, 3: 4)` считается как `(1 2, 3 4)`. Так что все эти функции работают и с картами тоже!
>
> [карта (map)]: https://sass-lang.com/documentation/values/maps
>
> Отдельные значения также считаются списками. Все эти функции трактуют `1px` как список, содержащий значение `1px`.


---


**`list.append($list, $val, $separator: auto)`**

**`append($list, $val, $separator: auto)`**

*Возвращает: `list`*

  Возвращает копию `$list` с добавленным в конец `$val`.

  Если `$separator` — `comma`, `space` или `slash`, результирующий список будет разделён запятыми, пробелами или слешем соответственно. Если это `auto` (значение по умолчанию), результирующий список будет использовать тот же разделитель, что и `$list` (или `space`, если у `$list` нет разделителя). Другие значения недопустимы.

  [separator]: https://sass-lang.com/documentation/values/lists

  Обратите внимание, что в отличие от [`list.join()`](#join), если `$val` — список, он будет вложен в результирующий список, а не все его элементы будут добавлены в результирующий список.

  **SCSS**

```scss
@use 'sass:list';

@debug list.append(10px 20px, 30px); // 10px 20px 30px
@debug list.append((blue, red), green); // blue, red, green
@debug list.append(10px 20px, 30px 40px); // 10px 20px (30px 40px)
@debug list.append(10px, 20px, $separator: comma); // 10px, 20px
@debug list.append((blue, red), green, $separator: space); // blue red green
```

**Sass**

```sass
@use 'sass:list'

@debug list.append(10px 20px, 30px)  // 10px 20px 30px
@debug list.append((blue, red), green)  // blue, red, green
@debug list.append(10px 20px, 30px 40px)  // 10px 20px (30px 40px)
@debug list.append(10px, 20px, $separator: comma)  // 10px, 20px
@debug list.append((blue, red), green, $separator: space)  // blue red green
```



---


**`list.index($list, $value)`**

**`index($list, $value)`**

*Возвращает: `number | null`*

  Возвращает [индекс][] значения `$value` в `$list`.

  [индекс]: https://sass-lang.com/documentation/values/lists#indexes

  Если `$value` не встречается в `$list`, возвращается [`null`][]. Если `$value` встречается в `$list` несколько раз, возвращается индекс первого вхождения.

  [`null`]: https://sass-lang.com/documentation/values/null

  **SCSS**

```scss
@use 'sass:list';

@debug list.index(1px solid red, 1px); // 1
@debug list.index(1px solid red, solid); // 2
@debug list.index(1px solid red, dashed); // null
```

**Sass**

```sass
@use 'sass:list'

@debug list.index(1px solid red, 1px)  // 1
@debug list.index(1px solid red, solid)  // 2
@debug list.index(1px solid red, dashed)  // null
```




---


**`list.is-bracketed($list)`**

**`is-bracketed($list)`**

*Возвращает: `boolean`*

  Возвращает, есть ли у `$list` квадратные скобки.

  **SCSS**

```scss
@use 'sass:list';

@debug list.is-bracketed(1px 2px 3px); // false
@debug list.is-bracketed([1px, 2px, 3px]); // true
```

**Sass**

```sass
@use 'sass:list'

@debug list.is-bracketed(1px 2px 3px)  // false
@debug list.is-bracketed([1px, 2px, 3px])  // true
```



---


**`list.join($list1, $list2, $separator: auto, $bracketed: auto)`**

**`join($list1, $list2, $separator: auto, $bracketed: auto)`**

*Возвращает: `list`*

  Возвращает новый список, содержащий элементы `$list1`, за которыми следуют элементы `$list2`.

  > ⚠️ **Внимание**
  >
  > Поскольку отдельные значения считаются одноэлементными списками, `list.join()` можно использовать, чтобы добавить значение в конец списка. Однако *это не рекомендуется*, так как если значение является списком, оно будет конкатенировано, что, вероятно, не то, чего вы ожидаете.
  >
  > Используйте вместо этого [`list.append()`](#append), чтобы добавить одно значение в список. `list.join()` используйте только для объединения двух списков в один.

  Если `$separator` — `comma`, `space` или `slash`, результирующий список будет разделён запятыми, пробелами или слешем соответственно. Если это `auto` (значение по умолчанию), результирующий список будет использовать тот же разделитель, что и `$list1`, если у него есть разделитель, иначе — что и `$list2`, если у него есть разделитель, иначе — `space`. Другие значения недопустимы.

  Если `$bracketed` — `auto` (значение по умолчанию), результирующий список будет в квадратных скобках, если в них был `$list1`. Иначе результирующий список будет в квадратных скобках, если `$bracketed` — [truthy], и без скобок, если `$bracketed` ложно (falsey).

  [truthy]: https://sass-lang.com/documentation/values/booleans#truthiness-and-falsiness

  **SCSS**

```scss
@use 'sass:list';

@debug list.join(10px 20px, 30px 40px); // 10px 20px 30px 40px
@debug list.join((blue, red), (#abc, #def)); // blue, red, #abc, #def
@debug list.join(10px, 20px); // 10px 20px
@debug list.join(10px, 20px, $separator: comma); // 10px, 20px
@debug list.join((blue, red), (#abc, #def), $separator: space); // blue red #abc #def
@debug list.join([10px], 20px); // [10px 20px]
@debug list.join(10px, 20px, $bracketed: true); // [10px 20px]
```

**Sass**

```sass
@use 'sass:list'

@debug list.join(10px 20px, 30px 40px)  // 10px 20px 30px 40px
@debug list.join((blue, red), (#abc, #def))  // blue, red, #abc, #def
@debug list.join(10px, 20px)  // 10px 20px
@debug list.join(10px, 20px, comma)  // 10px, 20px
@debug list.join((blue, red), (#abc, #def), space)  // blue red #abc #def
@debug list.join([10px], 20px)  // [10px 20px]
@debug list.join(10px, 20px, $bracketed: true)  // [10px 20px]
```



---


**`list.length($list)`**

**`length($list)`**

*Возвращает: `number`*

  Возвращает длину `$list`.

  Также может возвращать количество пар в карте.

  **SCSS**

```scss
@use 'sass:list';

@debug list.length(10px); // 1
@debug list.length(10px 20px 30px); // 3
@debug list.length((width: 10px, height: 20px)); // 2
```

**Sass**

```sass
@use 'sass:list'

@debug list.length(10px)  // 1
@debug list.length(10px 20px 30px)  // 3
@debug list.length((width: 10px, height: 20px))  // 2
```



---


**`list.separator($list)`**

**`list-separator($list)`**

*Возвращает: `unquoted string`*

  Возвращает имя разделителя, используемого `$list`: `space`, `comma` или `slash`.

  Если у `$list` нет разделителя, возвращается `space`.

  **SCSS**

```scss
@use 'sass:list';

@debug list.separator(1px 2px 3px); // space
@debug list.separator((1px, 2px, 3px)); // comma
@debug list.separator('Helvetica'); // space
@debug list.separator(()); // space
```

**Sass**

```sass
@use 'sass:list'

@debug list.separator(1px 2px 3px)  // space
@debug list.separator((1px, 2px, 3px))  // comma
@debug list.separator('Helvetica')  // space
@debug list.separator(())  // space
```



---


**`list.nth($list, $n)`**

**`nth($list, $n)`**

  Возвращает элемент `$list` по [индексу][] `$n`.

  [индексу]: https://sass-lang.com/documentation/values/lists#indexes

  Если `$n` отрицательный, отсчёт ведётся с конца `$list`. Если элемента с индексом `$n` не существует, выбрасывается ошибка.

  **SCSS**

```scss
@use 'sass:list';

@debug list.nth(10px 12px 16px, 2); // 12px
@debug list.nth([line1, line2, line3], -1); // line3
```

**Sass**

```sass
@use 'sass:list'

@debug list.nth(10px 12px 16px, 2)  // 12px
@debug list.nth([line1, line2, line3], -1)  // line3
```




---


**`list.set-nth($list, $n, $value)`**

**`set-nth($list, $n, $value)`**

*Возвращает: `list`*

  Возвращает копию `$list`, в которой элемент по [индексу][] `$n` заменён на `$value`.

  [индексу]: https://sass-lang.com/documentation/values/lists#indexes

  Если `$n` отрицательный, отсчёт ведётся с конца `$list`. Если элемента с индексом `$n` не существует, выбрасывается ошибка.

  **SCSS**

```scss
@use 'sass:list';

@debug list.set-nth(10px 20px 30px, 1, 2em); // 2em 20px 30px
@debug list.set-nth(10px 20px 30px, -1, 8em); // 10px, 20px, 8em
@debug list.set-nth((Helvetica, Arial, sans-serif), 3, Roboto); // Helvetica, Arial, Roboto
```

**Sass**

```sass
@use 'sass:list'

@debug list.set-nth(10px 20px 30px, 1, 2em); // 2em 20px 30px
@debug list.set-nth(10px 20px 30px, -1, 8em); // 10px, 20px, 8em
@debug list.set-nth((Helvetica, Arial, sans-serif), 3, Roboto); // Helvetica, Arial, Roboto
```



---


**`list.slash($elements...)`**

*Возвращает: `list`*

  Возвращает разделённый слешами список, содержащий `$elements`.

  > ⚠️ **Внимание**
  >
  > Эта функция — временное решение для создания списков, разделённых слешами. В будущем такие списки будут записываться буквально через слеш, как в `1px / 2px / solid`, но пока что [слеши используются для деления][], поэтому Sass не может использовать их для нового синтаксиса, пока не будет удалён старый синтаксис.
  >
  > [слеши используются для деления]: https://sass-lang.com/documentation/breaking-changes/slash-div

  **SCSS**

```scss
@use 'sass:list';

@debug list.slash(1px, 50px, 100px); // 1px / 50px / 100px
```

**Sass**

```sass
@use 'sass:list'

@debug list.slash(1px, 50px, 100px)  // 1px / 50px / 100px
```



---


**`list.zip($lists...)`**

**`zip($lists...)`**

*Возвращает: `list`*

  Объединяет все списки из `$lists` в один список подсписков.

  Каждый элемент результирующего списка содержит все элементы, находящиеся на этой позиции в `$lists`. Длина результирующего списка равна длине самого короткого списка в `$lists`.

  Результирующий список всегда разделён запятыми, а подсписки внутри него всегда разделены пробелами.

  **SCSS**

```scss
@use 'sass:list';

@debug list.zip(10px 50px 100px, short mid long); // 10px short, 50px mid, 100px long
@debug list.zip(10px 50px 100px, short mid); // 10px short, 50px mid
```

**Sass**

```sass
@use 'sass:list'

@debug list.zip(10px 50px 100px, short mid long)  // 10px short, 50px mid, 100px long
@debug list.zip(10px 50px 100px, short mid)  // 10px short, 50px mid
```

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/modules/list/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

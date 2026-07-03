# Правила управления потоком выполнения

Sass предоставляет ряд at-правил, которые позволяют управлять тем, выводятся ли
стили, а также выводить их несколько раз с небольшими вариациями. Их также можно
использовать в [миксинах](https://sass-lang.com/documentation/at-rules/mixin) и
[функциях](https://sass-lang.com/documentation/at-rules/function) для написания
небольших алгоритмов, упрощающих написание кода на Sass. Sass поддерживает четыре
правила управления потоком выполнения.

- [`@if`](https://sass-lang.com/documentation/at-rules/control/if) определяет, будет
  ли выполнен блок.

- [`@each`](https://sass-lang.com/documentation/at-rules/control/each) выполняет блок
  для каждого элемента [списка][] или каждой пары в [карте][].

- [`@for`](https://sass-lang.com/documentation/at-rules/control/for) выполняет блок
  определённое количество раз.

- [`@while`](https://sass-lang.com/documentation/at-rules/control/while) выполняет
  блок, пока не будет выполнено определённое условие.

[списка]: https://sass-lang.com/documentation/values/lists
[карте]: https://sass-lang.com/documentation/values/maps

---

> _Перевод официальной документации Sass. Источник: https://sass-lang.com/documentation/at-rules/control/. Оригинальный текст распространяется по лицензии MIT (© Sass team)._

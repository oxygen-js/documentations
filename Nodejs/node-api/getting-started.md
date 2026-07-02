# Начало работы с Node-API

Этот раздел проведёт вас через всё, что нужно, чтобы начать создавать нативные аддоны Node.js с помощью Node-API. Он рассчитан на разработчиков, которые впервые сталкиваются с разработкой нативных аддонов и хотят получить направляющий путь от нуля до работающего модуля.

## Что вы узнаете

  * [Предварительные требования](/learn/node-api/getting-started/prerequisites.html) \- знания C/C++ и JavaScript, которые вам понадобятся, прежде чем погрузиться в тему
  * [Инструменты, которые вам понадобятся](/learn/node-api/getting-started/tools.html) \- как установить Node, npm, Git и набор инструментов C/C++ на Windows, macOS и Linux
  * [Анатомия Node-API-проекта](/learn/node-api/getting-started/project-structure.html) \- типичная структура каталогов, записи `package.json`, `binding.gyp` и слой JavaScript-обёртки, общий для каждого проекта `node-addon-api`
  * [Ваш первый проект](/learn/node-api/getting-started/your-first-project.html) \- сборка и запуск вашего первого модуля Node-API
  * [ObjectWrap](/learn/node-api/getting-started/objectwrap.html) \- предоставление C++-объектов как JavaScript-объектов с помощью класса `ObjectWrap` из `node-addon-api`
  * [Миграция с NAN](/learn/node-api/getting-started/migration.html) \- преобразование существующего аддона на базе NAN в Node-API

## Какой уровень API мне использовать?

Node-API работает на двух уровнях:

  * **C API** \- встроен непосредственно в Node.js и задокументирован на [страницах Node.js API](https://nodejs.org/api/n-api.html). Даёт вам максимальный контроль и не добавляет лишних зависимостей.
  * **C++-обёртка (`node-addon-api`)** \- npm-пакет, который оборачивает C API в более дружелюбную C++-объектную модель. Рекомендуется для большинства проектов, поскольку значительно сокращает шаблонный код, сохраняя при этом полную стабильность ABI.

Примеры в этом разделе используют `node-addon-api`.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/node-api/getting-started. Оригинал распространяется по лицензии MIT._

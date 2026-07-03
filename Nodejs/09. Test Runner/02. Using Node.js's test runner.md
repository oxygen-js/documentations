# Использование средства запуска тестов в Node.js

В Node.js есть гибкое и надёжное встроенное средство запуска тестов. Это руководство покажет вам, как его настроить и использовать.
```text
example/
  ├ …
  ├ src/
    ├ app/…
    └ sw/…
  └ test/
    ├ globals/
      ├ …
      ├ IndexedDb.js
      └ ServiceWorkerGlobalScope.js
    ├ setup.mjs
    ├ setup.units.mjs
    └ setup.ui.mjs
```

> **Примечание**: glob-шаблоны требуют node v21+, и сами glob-шаблоны должны быть заключены в кавычки (без них вы получите поведение, отличное от ожидаемого, при котором может сначала показаться, что всё работает, но это не так).

Есть некоторые вещи, которые нужны всегда, поэтому поместите их в базовый файл настройки, подобный приведённому ниже. Этот файл будет импортироваться другими, более специализированными настройками.

## Общая настройка

`test/setup.mjs`
```js
import { register } from 'node:module';

register('some-typescript-loader');
// TypeScript поддерживается начиная с этого момента
// НО другие файлы test/setup.*.mjs всё равно должны быть на чистом JavaScript!
```

Затем для каждой настройки создайте отдельный файл `setup` (убедившись, что базовый файл `setup.mjs` импортируется в каждом из них). Есть несколько причин изолировать настройки, но самая очевидная — [YAGNI](https://en.wikipedia.org/wiki/You_aren't_gonna_need_it) \+ производительность: многое из того, что вы можете настраивать, — это специфичные для окружения моки/заглушки, которые могут быть весьма затратными и будут замедлять прогоны тестов. Вы хотите избежать этих затрат (буквально деньги, которые вы платите за CI, время ожидания завершения тестов и т. д.), когда они вам не нужны.

Каждый пример ниже взят из реальных проектов; они могут не подходить/не применяться к вашему, но каждый из них демонстрирует общие концепции, которые широко применимы.

## Динамическая генерация тестовых случаев

Иногда вам может понадобиться динамически генерировать тестовые случаи. Например, вы хотите протестировать одно и то же во множестве файлов. Это возможно, хотя и немного эзотерично. Вы должны использовать `test` (нельзя использовать `describe`) + `testContext.test`:

### Простой пример
```js
import assert from 'node:assert/strict';
import { test } from 'node:test';

import { detectOsInUserAgent } from '…';

const userAgents = [
  {
    ua: 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/134.0.0.0 Safari/537.3',
    os: 'WIN',
  },
  // …
];

test('Detect OS via user-agent', { concurrency: true }, t => {
  for (const { os, ua } of userAgents) {
    t.test(ua, () => assert.equal(detectOsInUserAgent(ua), os));
  }
});
```

### Продвинутый пример
```js
import assert from 'node:assert/strict';
import { test } from 'node:test';

import { getWorkspacePJSONs } from './getWorkspacePJSONs.mjs';

const requiredKeywords = ['node.js', 'sliced bread'];

test('Check package.jsons', { concurrency: true }, async t => {
  const pjsons = await getWorkspacePJSONs();

  for (const pjson of pjsons) {
    // ⚠️ `t.test`, НЕ `test`
    t.test(`Ensure fields are properly set: ${pjson.name}`, () => {
      assert.partialDeepStrictEqual(pjson.keywords, requiredKeywords);
    });
  }
});
```

> **Примечание**: До версии 23.8.0 настройка заметно отличается, потому что `testContext.test` не ожидался (await) автоматически.

## Тесты ServiceWorker

[`ServiceWorkerGlobalScope`](https://developer.mozilla.org/docs/Web/API/ServiceWorkerGlobalScope) содержит очень специфичные API, которых нет в других окружениях, и некоторые из его API внешне похожи на другие (например, `fetch`), но имеют расширенное поведение. Вы не хотите, чтобы это «просачивалось» в несвязанные тесты.

`test/setup.sw.mjs`
```js
import { beforeEach } from 'node:test';

import { ServiceWorkerGlobalScope } from './globals/ServiceWorkerGlobalScope.js';

import './setup.mjs'; // 💡

beforeEach(globalSWBeforeEach);
function globalSWBeforeEach() {
  globalThis.self = new ServiceWorkerGlobalScope();
}
```
```js
import assert from 'node:assert/strict';
import { describe, mock, it } from 'node:test';

import { onActivate } from './onActivate.js';

describe('ServiceWorker::onActivate()', () => {
  const globalSelf = globalThis.self;
  const claim = mock.fn(async function mock__claim() {});
  const matchAll = mock.fn(async function mock__matchAll() {});

  class ActivateEvent extends Event {
    constructor(...args) {
      super('activate', ...args);
    }
  }

  before(() => {
    globalThis.self = {
      clients: { claim, matchAll },
    };
  });
  after(() => {
    global.self = globalSelf;
  });

  it('should claim all clients', async () => {
    await onActivate(new ActivateEvent());

    assert.equal(claim.mock.callCount(), 1);
    assert.equal(matchAll.mock.callCount(), 1);
  });
});
```

## Снапшот-тесты

Их популяризировал Jest; теперь многие библиотеки реализуют подобную функциональность, включая Node.js начиная с v22.3.0. Существует несколько сценариев использования, таких как проверка вывода рендеринга компонентов и конфигурации [инфраструктуры как кода (Infrastructure as Code)](https://en.wikipedia.org/wiki/Infrastructure_as_code). Концепция одна и та же независимо от сценария использования.

Никакой особой конфигурации не _требуется_, кроме включения этой возможности через [`--experimental-test-snapshots`](https://nodejs.org/docs/latest/api/cli.html#--experimental-test-snapshots). Но чтобы продемонстрировать необязательную конфигурацию, вы, вероятно, добавили бы что-то вроде следующего в один из ваших существующих конфигурационных файлов тестов.

`test/setup.ui.mjs`

По умолчанию node генерирует имя файла, несовместимое с определением подсветки синтаксиса: `.js.snapshot`. Сгенерированный файл на самом деле является файлом CJS, поэтому более подходящее имя файла заканчивалось бы на `.snapshot.cjs` (или более кратко `.snap.cjs`, как ниже); это также лучше обрабатывается в проектах на ESM.
```js
import { basename, dirname, extname, join } from 'node:path';
import { snapshot } from 'node:test';

snapshot.setResolveSnapshotPath(generateSnapshotPath);
/**
 * @param {string} testFilePath '/tmp/foo.test.js'
 * @returns {string} '/tmp/foo.test.snap.cjs'
 */
function generateSnapshotPath(testFilePath) {
  const ext = extname(testFilePath);
  const filename = basename(testFilePath, ext);
  const base = dirname(testFilePath);

  return join(base, `${filename}.snap.cjs`);
}
```

Пример ниже демонстрирует снапшот-тестирование с [testing library](https://testing-library.com/) для UI-компонентов; обратите внимание на два разных способа доступа к `assert.snapshot`):
```
import { describe, it } from 'node:test';

import { prettyDOM } from '@testing-library/dom';
import { render } from '@testing-library/react'; // Любой фреймворк (например, svelte)

import { SomeComponent } from './SomeComponent.jsx';

describe('<SomeComponent>', () => {
  // Для тех, кто предпочитает синтаксис «стрелочных функций», следующее, вероятно, лучше для единообразия
  it('should render defaults when no props are provided', t => {
    const component = render(<SomeComponent />).container.firstChild;

    t.assert.snapshot(prettyDOM(component));
  });

  it('should consume `foo` when provided', function () {
    const component = render(<SomeComponent foo="bar" />).container.firstChild;

    this.assert.snapshot(prettyDOM(component));
    // `this` работает только тогда, когда используется `function` (а не «стрелочная функция»).
  });
});
```

JSX

> ⚠️ `assert.snapshot` берётся из контекста теста (`t` или `this`), **а не** из `node:assert`. Это необходимо, потому что контекст теста имеет доступ к области видимости, недоступной для `node:assert` (иначе вам пришлось бы вручную предоставлять её каждый раз при использовании `assert.snapshot`, например `snapshot(this, value)`, что было бы довольно утомительно).

## Модульные тесты

Модульные (unit) тесты — самые простые тесты и, как правило, не требуют практически ничего особенного. Подавляющее большинство ваших тестов, скорее всего, будут модульными, поэтому важно сохранять эту настройку минимальной, потому что небольшое снижение производительности настройки будет усиливаться и накапливаться каскадом.

`test/setup.units.mjs`
```js
import { register } from 'node:module';

import './setup.mjs'; // 💡

register('some-plaintext-loader');
// теперь можно импортировать файлы с обычным текстом, такие как graphql:
// import GET_ME from 'get-me.gql'; GET_ME = '
```
```js
import assert from 'node:assert/strict';
import { describe, it } from 'node:test';

import { Cat } from './Cat.js';
import { Fish } from './Fish.js';
import { Plastic } from './Plastic.js';

describe('Cat', () => {
  it('should eat fish', () => {
    const cat = new Cat();
    const fish = new Fish();

    assert.doesNotThrow(() => cat.eat(fish));
  });

  it('should NOT eat plastic', () => {
    const cat = new Cat();
    const plastic = new Plastic();

    assert.throws(() => cat.eat(plastic));
  });
});
```

## Тесты пользовательского интерфейса

Тесты пользовательского интерфейса (UI) обычно требуют DOM и, возможно, других специфичных для браузера API (таких как [`IndexedDb`](https://developer.mozilla.org/docs/Web/API/IndexedDB_API), используемый ниже). Их обычно очень сложно и дорого настраивать.

`test/setup.ui.mjs`

Если вы используете API вроде `IndexedDb`, но он очень изолирован, глобальный мок, подобный приведённому ниже, — возможно, не лучший путь. Вместо этого, возможно, стоит перенести этот `beforeEach` в конкретный тест, где будет осуществляться доступ к `IndexedDb`. Обратите внимание, что если модуль, обращающийся к `IndexedDb` (или чему-либо ещё), сам широко используется, то либо мокируйте этот модуль (вероятно, вариант получше), либо _всё же_ оставьте это здесь.
```js
import { register } from 'node:module';

// ⚠️ Убедитесь, что создаётся только 1 экземпляр JSDom; несколько приведут ко множеству 🤬
import jsdom from 'global-jsdom';

import './setup.units.mjs'; // 💡

import { IndexedDb } from './globals/IndexedDb.js';

register('some-css-modules-loader');

jsdom(undefined, {
  url: 'https://test.example.com', // ⚠️ Если это не указать, скорее всего, приведёт ко множеству 🤬
});

// Пример того, как декорировать глобальный объект.
// `history` в JSDOM не обрабатывает навигацию; следующее покрывает большинство случаев.
const pushState = globalThis.history.pushState.bind(globalThis.history);
globalThis.history.pushState = function mock_pushState(data, unused, url) {
  pushState(data, unused, url);
  globalThis.location.assign(url);
};

beforeEach(globalUIBeforeEach);
function globalUIBeforeEach() {
  globalThis.indexedDb = new IndexedDb();
}
```

У вас может быть 2 разных уровня UI-тестов: похожий на модульный (где внешние сущности и зависимости мокируются) и более сквозной (end-to-end) (где мокируются только внешние сущности, такие как IndexedDb, а остальная часть цепочки реальна). Первый обычно более «чистый» вариант, а второй обычно откладывается до полностью сквозного автоматизированного теста удобства использования с помощью чего-то вроде [Playwright](https://playwright.dev/) или [Puppeteer](https://pptr.dev/). Ниже приведён пример первого.
```
import { before, describe, mock, it } from 'node:test';

import { screen } from '@testing-library/dom';
import { render } from '@testing-library/react'; // Любой фреймворк (например, svelte)

// ⚠️ Обратите внимание, что SomeOtherComponent НЕ является статическим импортом;
// это необходимо для того, чтобы можно было мокировать его собственные импорты.

describe('<SomeOtherComponent>', () => {
  let SomeOtherComponent;
  let calcSomeValue;

  before(async () => {
    // ⚠️ Последовательность имеет значение: мок должен быть настроен ДО того, как импортируется его потребитель.

    // Требует, чтобы был установлен `--experimental-test-module-mocks`.
    calcSomeValue = mock.module('./calcSomeValue.js', {
      calcSomeValue: mock.fn(),
    });

    ({ SomeOtherComponent } = await import('./SomeOtherComponent.jsx'));
  });

  describe('when calcSomeValue fails', () => {
    // Это вы не захотели бы обрабатывать снапшотом, потому что он был бы хрупким:
    // когда в сообщение об ошибке вносятся несущественные изменения,
    // снапшот-тест ошибочно проваливался бы
    // (и снапшот пришлось бы обновлять без какой-либо реальной пользы).

    it('should fail gracefully by displaying a pretty error', () => {
      calcSomeValue.mockImplementation(function mock__calcSomeValue() {
        return null;
      });

      render(<SomeOtherComponent />);

      const errorMessage = screen.queryByText('unable');

      assert.ok(errorMessage);
    });
  });
});
```

JSX

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/test-runner/using-test-runner. Оригинал распространяется по лицензии MIT._

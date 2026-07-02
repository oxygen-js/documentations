# Публикация пакета на TypeScript

Эта статья охватывает вопросы, связанные именно с публикацией TypeScript. Публикация означает распространение в виде пакета через npm (или другой менеджер пакетов); речь идёт не о компиляции приложения / сервера для запуска в продакшене (такого как PWA и/или конечная точка сервера).

Несколько важных моментов, которые стоит отметить:

  * Всё из статьи [Публикация пакета](/learn/modules/publishing-a-package) применимо и здесь.

    * Поля вроде `main` работают с _опубликованным_ содержимым, поэтому, когда исходный код на TypeScript транспилируется в JavaScript, опубликованным содержимым становится JavaScript, и `main` будет указывать на файл JavaScript с расширением файла JavaScript (например, `main.ts` → `"main": "main.js"`).

    * Поля вроде `scripts.test` работают с исходным кодом, поэтому они будут использовать расширения файлов исходного кода (например, `"test": "node --test './src/**/*.test.ts'`).

  * Node запускает код на TypeScript через процесс под названием «[отбрасывание типов](https://nodejs.org/api/typescript.html#type-stripping)» (type stripping), при котором node (посредством [Amaro](https://github.com/nodejs/amaro)) удаляет специфичный для TypeScript синтаксис, оставляя чистый JavaScript (который node уже понимает). Это поведение включено по умолчанию, начиная с версии node 22.18.0.

    * Node **не** отбрасывает типы в `node_modules`, потому что это может вызвать значительные проблемы с производительностью для официального компилятора TypeScript (`tsc`) и частей VS Code, поэтому сопровождающие TypeScript хотели бы отговорить людей от публикации «сырого» TypeScript, по крайней мере пока.
  * Использование специфичных для TypeScript возможностей, таких как `enum`, в node всё ещё требует флага ([`--experimental-transform-types`](https://nodejs.org/api/typescript.html#typescript-features)). Для них, впрочем, часто есть более удачные альтернативы.

    * Чтобы гарантировать, что специфичные для TypeScript возможности _отсутствуют_ (и ваш код мог просто запускаться в node), установите опцию конфигурации [`erasableSyntaxOnly`](https://devblogs.microsoft.com/typescript/announcing-typescript-5-8-beta/#the---erasablesyntaxonly-option) в TypeScript версии 5.8+.
  * Используйте [dependabot](https://docs.github.com/en/code-security/dependabot), чтобы поддерживать ваши зависимости в актуальном состоянии, включая зависимости в github actions. Это очень простая конфигурация в духе «настроил и забыл».

  * `.nvmrc` берётся из [`nvm`](https://github.com/nvm-sh/nvm) — менеджера нескольких версий node. Он позволяет указать версию node, которую проекту следует использовать по умолчанию.

Обзор структуры каталогов репозитория выглядел бы примерно так:
```text
example-ts-pkg/
├ .github/
│ ├ workflows/
│ │ ├ ci.yml
│ │ └ publish.yml
│ └ dependabot.yml
├ src/
│ ├ foo.fixture.js
│ ├ main.ts
│ ├ main.test.ts
│ ├ some-util.ts
│ └ some-util.test.ts
├ LICENSE
├ package.json
├ README.md
└ tsconfig.json
```

А обзор структуры каталогов его опубликованного пакета выглядел бы примерно так:
```text
example-ts-pkg/
├ LICENSE
├ main.d.ts
├ main.d.ts.map
├ main.js
├ package.json
├ README.md
├ some-util.d.ts
├ some-util.d.ts.map
└ some-util.js
```

Замечание об организации каталогов: есть несколько распространённых практик размещения тестов. Принцип наименьшего знания подсказывает располагать их рядом (размещать вплотную к реализации). Иногда это тот же каталог, а иногда — отдельный «ящик» вроде `__test__` (также рядом с реализацией, «файлы совмещены, но обособлены»). Как альтернатива, некоторые предпочитают создавать каталог `test/` рядом с `src/` («'src' и 'test' полностью разделены») — либо с зеркальной структурой, либо в виде «свалки».

## Что делать с вашими типами

### Относитесь к типам как к тесту

Назначение типов — предупредить, что реализация не будет работать:
```ts
// @errors: 2322
const foo = 'a';
const bar: number = 1 + foo;
```

TypeScript предупредил, что приведённый выше код не будет вести себя так, как задумано, — точно так же, как модульный тест предупреждает, что код ведёт себя не так, как задумано. Они дополняют друг друга и проверяют разные вещи — вам стоит иметь и то, и другое.

Ваш редактор (например, VS Code), скорее всего, имеет встроенную поддержку TypeScript и отображает ошибки прямо по ходу работы. Если это не так и/или вы что-то упустили, вас подстрахует CI.

Следующее [GitHub Action](https://github.com/features/actions) настраивает задачу CI, которая автоматически проверяет (и требует), чтобы типы прошли инспекцию для PR в ветку `main`.
```yaml
# yaml-language-server: $schema=https://json.schemastore.org/github-workflow.json

name: Tests

on:
  pull_request:
    branches: ['*']

jobs:
  check-types:
    # Выделены отдельно от тестов, потому что
    # они не зависят от платформы и версии node
    # и должны выполняться лишь один раз.

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'
      - name: npm clean install
        run: npm ci
      # Здесь вы, возможно, захотите запустить и проверку линтером
      - run: node --run types:check

  get-matrix:
    # Автоматически выбирать активные LTS-версии
    runs-on: ubuntu-latest
    outputs:
      latest: ${{ steps.set-matrix.outputs.requireds }}
    steps:
      - uses: ljharb/actions/node/matrix@main
        id: set-matrix
        with:
          versionsAsRoot: true
          type: majors
          preset: '>= 22' # glob не портирован в версии ниже 22.x

  test:
    needs: [get-matrix]
    runs-on: ${{ matrix.os }}

    strategy:
      fail-fast: false
      matrix:
        node-version: ${{ fromJson(needs.get-matrix.outputs.latest) }}
        os:
          - macos-latest
          - ubuntu-latest
          - windows-latest

    steps:
      - uses: actions/checkout@v4
      - name: Use node ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - name: npm clean install
        run: npm ci
      - run: node --run test
```

Обратите внимание, что к тестовым файлам вполне может применяться другой `tsconfig.json` (именно поэтому они исключены в приведённом выше примере).

### Генерация деклараций типов

Декларации типов (`.d.ts` и им подобные) предоставляют информацию о типах в виде отдельного (sidecar) файла, что позволяет исполняемому коду оставаться чистым JavaScript, но при этом иметь типы.

Поскольку они генерируются на основе исходного кода, их можно собирать как часть процесса публикации, и их не нужно фиксировать в вашем репозитории.

Рассмотрим следующий пример, в котором декларации типов генерируются непосредственно перед публикацией в реестр npm.
```yaml
# yaml-language-server: $schema=https://json.schemastore.org/github-workflow.json

# Это по большей части шаблонный код.

name: Publish to npm
on:
  push:
    tags:
      - '**@*'

jobs:
  build:
    runs-on: ubuntu-latest

    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci

      # - name: Publish to npm
      #   run: … npm publish …
```

Вам стоит опубликовать пакет, скомпилированный для поддержки всех LTS-версий Node.js, поскольку вы не знаете, какую версию будет использовать потребитель; конфигурации `tsconfig` в этой статье поддерживают node 18.x и новее.

`npm publish` автоматически запустит [`prepack` заранее](https://docs.npmjs.com/cli/using-npm/scripts#npm-publish). `npm` также автоматически запускает `prepack` перед `npm pack --dry-run` (чтобы вы могли легко увидеть, каким будет ваш опубликованный пакет, фактически не публикуя его). **Осторожно**: [`node --run` этого _не_ делает](../command-line/run-nodejs-scripts-from-the-command-line.html#using-the---run-flag). Для этого шага вы не можете использовать `node --run`, так что данное предостережение здесь не применимо, но оно может касаться других шагов.

Шаги для фактической публикации в npm будут рассмотрены в отдельной статье (есть немало «за» и «против», выходящих за рамки этой статьи).

#### Разберём это подробнее

Генерация деклараций типов детерминирована: из одного и того же ввода вы каждый раз получите один и тот же вывод. Поэтому нет необходимости фиксировать их в git.

[`npm publish`](https://docs.npmjs.com/cli/commands/npm-publish) захватывает всё подходящее и доступное на момент запуска команды; поэтому генерация деклараций типов непосредственно перед этим означает, что они доступны и будут подхвачены.

По умолчанию `npm publish` захватывает (почти) всё (см. [Файлы, включаемые в пакет](https://docs.npmjs.com/cli/commands/npm-publish#files-included-in-package)). Чтобы ваш опубликованный пакет оставался минимальным (вспомните мем «Самые тяжёлые объекты во Вселенной» про `node_modules`), вам нужно исключить из упаковки определённые файлы (например, тесты и тестовые фикстуры). Добавьте их в список исключений, задаваемый в [`.npmignore`](https://docs.npmjs.com/cli/using-npm/developers#keeping-files-out-of-your-package); убедитесь, что в списке присутствует исключение `!*.d.ts`, иначе сгенерированные декларации типов не будут опубликованы! Как альтернатива, вы можете использовать [package.json «files»](https://docs.npmjs.com/cli/configuring-npm/package-json#files), чтобы задать список включения (если по ошибке случайно пропустить файл, ваш пакет может оказаться сломанным для тех, кто его использует, поэтому это менее безопасный вариант).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/typescript/publishing-a-ts-package. Оригинал распространяется по лицензии MIT._

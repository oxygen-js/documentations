# Библиотека промптов

> Готовые к копированию промпты для Claude Code, помеченные тегами по задаче и роли.

Это библиотека промптов для копирования в Claude Code. Используйте её, чтобы изучить способы работы, которые вы ещё не пробовали, или когда не уверены, с чего начать.

Промпты собраны из различных руководств Anthropic, включая [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows), [Лучшие практики](https://code.claude.com/docs/en/best-practices) и [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code). Это скорее отправные точки, чем скрипты. Откройте **Почему это работает** под любым промптом, чтобы увидеть закономерность, стоящую за ним, и написать собственный.

Ниже приведены все промпты из библиотеки, сгруппированные по этапу разработки и категории. На сайте документации они доступны в виде интерактивного виджета с поиском и фильтрами по тегам (задача и роль). Пять промптов помечены значком «★ Начните отсюда» — с них удобно начать, если вы не знаете, с чего стартовать.

## Исследование

### Онбординг

#### Освоиться в новом репозитории — ★ Начните отсюда · 1

```text
give me an overview of this codebase: architecture, key directories, and how the pieces connect
```

**Почему это работает.** Опишите, что вы хотите узнать, а не какие файлы читать. Claude сам исследует проект и возвращает сводку о том, как всё устроено.

**Закрепить.** [Запустите `/init`, чтобы настроить `CLAUDE.md` — тогда Claude будет помнить это в каждой сессии](https://code.claude.com/docs/en/memory)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

### Понимание

#### Объяснить незнакомый код

```text
explain what src/scheduler/queue.ts does and how data flows through it. write it up as an HTML page with a diagram, then open it in my browser
```

**Почему это работает.** Назовите файл и укажите, в каком формате хотите получить ответ. Замените HTML-страницу на диаграмму, маркированный список или что угодно, что подходит вашему стилю обучения.

**Закрепить.** [Задайте стиль вывода, чтобы Claude всегда объяснял в предпочитаемом вами формате](https://code.claude.com/docs/en/output-styles)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Найти, где что-то происходит — ★ Начните отсюда · 2

```text
where do we validate uploaded file types?
```

**Почему это работает.** Ищите по поведению, а не по имени файла. Поиск работает, даже когда вы не знаете, как называется файл и в какой директории он находится.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Проверить, что сломается, прежде чем удалять

```text
what would break if I deleted the retryWithBackoff helper?
```

**Почему это работает.** Спрашивайте, прежде чем что-либо удалять. Список вызывающих мест и последствий подскажет, идёт ли речь об однострочной чистке или об изменении, которое нужно согласовывать.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Проследить, как эволюционировал код

```text
look through the commit history of internal/auth/session.go and summarize how it evolved and why
```

**Почему это работает.** Указывайте на историю коммитов, когда вопрос в том, почему, а не что. Claude читает журнал и blame для используемой вами системы контроля версий и объясняет решения, стоящие за текущей реализацией.

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

#### Оценить объём изменения, прежде чем начать

```text
which files would I need to touch to add a dark mode toggle to settings?
```

**Почему это работает.** Оцените объём работы, прежде чем вносить её в дорожную карту. Список файлов подскажет, идёт ли речь об одном компоненте или о сквозном изменении.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Продукт, Дизайн

#### Задать кодовой базе продуктовый вопрос

```text
I am a PM. walk me through what happens when a user clicks Export to PDF, from the UI down to the result
```

**Почему это работает.** Укажите свою роль, чтобы ответ был подан на нужном уровне. Claude объясняет, что на самом деле делает продукт, по исходному коду — вам не нужно его читать.

**Закрепить.** [Задайте стиль вывода, чтобы Claude всегда подавал ответы на этом уровне](https://code.claude.com/docs/en/output-styles)

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Продукт

## Проектирование

### Планирование

#### Спланировать изменение нескольких файлов, прежде чем трогать код

```text
plan how to refactor the payment module to support multiple currencies. list the files you would change, but don't edit anything yet
```

**Почему это работает.** Добавление «пока ничего не редактируй» отделяет исследование от изменений, поэтому вы видите подход до того, как код будет тронут. Чтобы планирование по умолчанию срабатывало на каждом промпте, нажмите Shift+Tab для [режима планирования](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode).

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Продукт, Дизайн

#### Составить спецификацию через интервью

```text
I want to build per-workspace rate limits. interview me about implementation, UX, edge cases, and tradeoffs until we have covered everything, then write the spec to SPEC.md
```

**Почему это работает.** Попросите провести с вами интервью, вместо того чтобы писать спецификацию самостоятельно. Claude задаёт вам структурированные вопросы, пока требования не будут полными, а затем записывает результат в файл.

**Закрепить.** [Сохраните ваши вопросы для интервью как навык `/spec`, чтобы каждая спецификация начиналась одинаково](https://code.claude.com/docs/en/skills)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices) · **Роли:** Продукт

#### Превратить встречу в тикеты

**Нужно:** ваш трекер задач, добавленный как [коннектор claude.ai](https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai) или [сервер MCP](https://code.claude.com/docs/en/mcp).

```text
read @meeting-notes.md and write up the action items, then create a Linear ticket for each with acceptance criteria
```

**Почему это работает.** Пропустите этап расшифровки. Claude извлекает пункты действий из неструктурированного ввода и записывает их прямо в ваш трекер через [MCP](https://code.claude.com/docs/en/mcp), поэтому вы проверяете тикеты, а не транскрипт.

**Закрепить.** [Сохраните это как навык `/tickets`](https://code.claude.com/docs/en/skills)

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Продукт

#### Наметить крайние случаи, прежде чем строить

```text
list the error states, empty states, and edge cases for the file upload flow that the design needs to cover
```

**Почему это работает.** Спрашивайте о том, чего не хватает, а не о том, что уже есть. Claude перечисляет состояния ошибок, пустые состояния и крайние случаи, которые дизайн «счастливого пути» обычно пропускает.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Дизайн, Продукт

### Прототипирование

#### Превратить макет в рабочий прототип

_Вставьте, перетащите или упомяните через @ изображение вашего макета, затем отправьте это:_

```text
here is a mockup. build a working prototype I can click through, matching the layout and states shown
```

**Почему это работает.** Кликабельный прототип отвечает на вопросы, на которые статичный макет не способен. Передайте работающий код инженерам, вместо того чтобы описывать взаимодействия в документе.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Дизайн, Продукт, Маркетинг

#### Реализовать по скриншоту с самопроверкой

**Нужно:** способ для Claude отрисовывать результат и делать его скриншоты. В [настольном приложении](https://code.claude.com/docs/en/desktop#preview-your-app) это встроено. В терминале установите [расширение Chrome](https://code.claude.com/docs/en/chrome) или сервер Playwright [MCP](https://code.claude.com/docs/en/mcp).

_Вставьте, перетащите или упомяните через @ изображение вашего дизайна, затем отправьте это:_

```text
implement this design, then take a screenshot of the result, compare it to the original, and fix any differences
```

**Почему это работает.** Это даёт Claude цикл проверки: он отрисовывает результат, сравнивает его с исходным изображением и итерирует без того, чтобы вы указывали на каждое расхождение.

**Закрепить.** [Используйте `/goal`, чтобы Claude продолжал итерации, пока скриншоты не совпадут](https://code.claude.com/docs/en/goal)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices) · **Роли:** Дизайн

## Разработка

### Реализация

#### Следовать существующему образцу

```text
look at how the GitHub webhook handler is implemented to understand the pattern, then build a Stripe webhook handler the same way
```

**Почему это работает.** Указывайте на код, который вам уже нравится. Без образца Claude по умолчанию использует общие лучшие практики. С образцом он следует соглашениям, которые действительно приняты в вашей кодовой базе.

**Закрепить.** [Попросите Claude записать образец, которому он следовал, в `CLAUDE.md`, чтобы будущие сессии соответствовали ему без образца](https://code.claude.com/docs/en/memory)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

#### Сгенерировать документацию для недокументированного кода

```text
find the public functions in src/auth/ without JSDoc comments and add them, matching the style already used in the file
```

**Почему это работает.** Назовите область и формат. Claude находит то, чего не хватает, и соответствует стилю комментариев, уже принятому в файле, поэтому новая документация читается как остальная.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Документация

#### Добавить небольшую, чётко определённую функцию

```text
add a /health endpoint that returns the app version and uptime
```

**Почему это работает.** Укажите входные и выходные данные, а не то, как это построить. Claude находит, где расположен похожий код, и добавляет ваш рядом с ним.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Собрать небольшой внутренний инструмент с нуля

```text
create a drag-and-drop Kanban board with three columns using HTML, CSS, and vanilla JavaScript, then open it in my browser
```

**Почему это работает.** Вам не нужен ни проект, ни фреймворк, ни этап сборки. Опишите инструмент и попросите Claude открыть его, чтобы вы сразу увидели его в работе.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Продукт, Дизайн, Маркетинг, Документация

#### Проработать задачу от начала до конца

**Нужно:** аутентифицированный [gh CLI](https://cli.github.com) или GitHub, добавленный как [коннектор claude.ai](https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai).

```text
read issue #312, implement the fix, and run the tests
```

**Почему это работает.** Дайте номер задачи, а не краткое описание. Claude сам читает тикет целиком, поэтому требования, которые вы забыли бы упомянуть, доходят до него, а изменение он проверяет, прежде чем отчитаться.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Найти и обновить текст по всей кодовой базе

```text
find every place we say "Sign up free" or a close variant, show me each one in context, then update them all to "Start free trial". leave tests and the changelog alone
```

**Почему это работает.** Просите варианты и указывайте, что пропустить. Claude находит формулировки, которые буквальный поиск упустил бы, и оставляет тестовые фикстуры и историю нетронутыми, поэтому вы проверяете только тот текст, который действительно видят пользователи.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Дизайн, Документация, Маркетинг

#### Составить документ по прошлым примерам

```text
read the privacy impact assessments in legal/pia/ to learn the structure and voice, then draft a new one for the new analytics integration
```

**Почему это работает.** Указывайте на папку с готовыми работами вместо того, чтобы описывать свой стиль. Claude учится структуре и стилю по тому, что вы уже выпустили, поэтому первый черновик читается как один из ваших.

**Закрепить.** [Сохраните стиль как навык, чтобы каждый черновик начинался с него](https://code.claude.com/docs/en/skills)

**Источник.** [Как Anthropic использует Claude в юридическом отделе](https://claude.com/blog/how-anthropic-uses-claude-legal) · **Роли:** Документация, Маркетинг, Продукт

### Тестирование

#### Написать тесты, запустить их, исправить сбои — ★ Начните отсюда · 4

```text
write tests for app/parsers/feed.py, run them, and fix any failures
```

**Почему это работает.** Просите написать, запустить и исправить вместе, чтобы Claude итерировал, не останавливаясь за инструкциями.

**Закрепить.** [Запустите `/init`, чтобы Claude автоматически узнал вашу команду запуска тестов](https://code.claude.com/docs/en/memory)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Вести реализацию от тестов

```text
write tests for the password reset flow first, then implement it until they pass
```

**Почему это работает.** Разработка через тестирование: тесты определяют, когда работа завершена, а Claude итерирует над реализацией, пока они не пройдут.

**Источник.** [Руководство по масштабированию агентного программирования](https://resources.anthropic.com/hubfs/Scaling%20agentic%20coding%20across%20your%20organization.pdf)

#### Закрыть пробелы по отчёту о покрытии

```text
read coverage/coverage-summary.json and add tests for the lowest-covered files until each is above 80%
```

**Почему это работает.** Указывайте на отчёт о покрытии вместо того, чтобы угадывать, что не протестировано. Claude читает реальные цифры и пишет тесты для файлов, которым они нужнее всего.

**Закрепить.** [Задайте это как `/goal`, чтобы Claude продолжал писать тесты, пока покрытие не достигнет цели](https://code.claude.com/docs/en/goal)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

### Рефакторинг

#### Мигрировать образец по всей кодовой базе

```text
migrate everything from the old logging API to the structured logger: identify every place that needs to change, then make the changes
```

**Почему это работает.** Опишите старый образец и новый. Если попросить Claude сначала найти все места, то места вызова будут перечислены в ответе, и вы сможете проверить, что ни одно не пропущено.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Перенести код на другой язык

```text
port this Python module to Rust, keeping the same public API and test behavior
```

**Почему это работает.** Указывайте, что нужно сохранить, а не только целевой язык. Называя API или поведение, которое должно остаться неизменным, вы даёте Claude контракт, по которому он сверяет перенос.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code)

#### Оптимизировать под измеримую цель

```text
optimize the search query to bring p95 latency from 2s down to under 500ms
```

**Почему это работает.** Указание метрики и цели даёт Claude чёткое определение готовности.

**Закрепить.** [Задайте это как `/goal`, чтобы Claude продолжал измерять и итерировать, пока не достигнет нужного значения](https://code.claude.com/docs/en/goal)

**Источник.** [Руководство по масштабированию агентного программирования](https://resources.anthropic.com/hubfs/Scaling%20agentic%20coding%20across%20your%20organization.pdf) · **Роли:** Данные

#### Исправить точный визуальный баг

```text
the login button extends 20px beyond the card border on mobile. fix it.
```

**Почему это работает.** Точная визуальная обратная связь даёт точное исправление. Укажите конкретный элемент, размер и область просмотра.

**Закрепить.** [Добавьте инструмент предпросмотра, чтобы Claude сам делал скриншоты и проверял исправление](https://code.claude.com/docs/en/desktop#preview-your-app)

**Источник.** [Руководство по масштабированию агентного программирования](https://resources.anthropic.com/hubfs/Scaling%20agentic%20coding%20across%20your%20organization.pdf) · **Роли:** Дизайн

### Ревью

#### Просмотреть свои изменения перед коммитом — ★ Начните отсюда · 5

```text
review my uncommitted changes and flag anything that looks risky before I commit
```

**Почему это работает.** Находите проблемы, пока их ещё дёшево исправить. Claude читает изменённые файлы целиком, а не только строки diff, поэтому замечает проблемы, которые быстрая самопроверка упускает.

**Закрепить.** [Запустите `/code-review` для той же проверки одной командой](https://code.claude.com/docs/en/commands)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Провести ревью pull request

**Нужно:** аутентифицированный [gh CLI](https://cli.github.com) или GitHub, добавленный как [коннектор claude.ai](https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai).

```text
review PR #247 and summarize what changed, then list any concerns
```

**Почему это работает.** Claude проводит ревью, держа в контексте всю кодовую базу, а не только diff. Он читает изменённый код и то, что он вызывает, поэтому находит проблемы, которые ревью только по diff упустило бы.

**Закрепить.** [Включите это для каждого pull request с помощью Code Review](https://code.claude.com/docs/en/code-review)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Просмотреть изменения инфраструктуры перед применением

_Сначала вставьте вывод вашего плана в промпт, затем отправьте это:_

```text
here is my Terraform plan output. what is this going to do, and is anything here going to cause problems?
```

**Почему это работает.** Вывод плана плотный и его трудно просматривать. Вставив его, вы получите сводку простым языком о том, что действительно изменится, прежде чем вы это примените.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Безопасность, Дежурство

#### Провести ревью безопасности с помощью субагента

```text
use a subagent to review src/api/ for security issues and report what it finds
```

**Почему это работает.** [Субагент](https://code.claude.com/docs/en/sub-agents) проводит аудит в собственном окне контекста и возвращает сводку, поэтому длинное ревью безопасности не заполняет вашу основную сессию. Встроенный универсальный субагент справляется с этим без дополнительной настройки.

**Закрепить.** [Настройте отдельный субагент для ревью безопасности, которым сможет пользоваться вся ваша команда](https://code.claude.com/docs/en/sub-agents)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices) · **Роли:** Безопасность

#### Отловить проблемы до формального ревью

```text
review launch-post.md for unsupported claims, missing attributions, and brand-guideline issues and list anything I should fix before it goes to legal
```

**Почему это работает.** Получите первый проход, прежде чем человек потратит на это время. Назовите вопросы, которые хотите проверить, чтобы ревью было сфокусированным, затем исправьте найденное и отправьте более чистый черновик.

**Закрепить.** [Зафиксируйте ваш чек-лист ревью как навык, который сможет запускать вся ваша команда](https://code.claude.com/docs/en/skills)

**Источник.** [Как Anthropic использует Claude в юридическом отделе](https://claude.com/blog/how-anthropic-uses-claude-legal) · **Роли:** Маркетинг, Документация

### Корректировка

#### Скорректировать неверный подход

```text
that is not right: the function signature needs to stay backward-compatible. try a different approach
```

**Почему это работает.** Назовите ограничение, которое Claude упустил, а не просто скажите, что он ошибся. Конкретная причина даёт Claude конкретное ограничение, которое нужно удовлетворить при повторной попытке, вместо того чтобы снова угадывать.

**Закрепить.** [Нажмите `Esc` дважды, чтобы открыть меню отката и восстановить код и разговор — тогда повторная попытка начнётся с чистого листа](https://code.claude.com/docs/en/checkpointing)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

#### Сузить область изменения

```text
that is too much. keep only the changes to the validation logic in src/forms/ and undo your other edits
```

**Почему это работает.** Когда направление верное, но изменение вышло слишком широким, попросите Claude оставить его часть, вместо того чтобы откатывать всё. Обозначенная граница не даёт небольшому исправлению превратиться в рефакторинг.

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

#### Превратить исправление в правило

```text
you keep using default exports when this project uses named exports. add a rule to CLAUDE.md so this stops happening
```

**Почему это работает.** Исправление в чате не передаётся вашей команде. Правило в [CLAUDE.md](https://code.claude.com/docs/en/memory) проекта становится общим, как только вы его закоммитите, и Claude читает его в начале каждой сессии.

**Закрепить.** [Откройте `/memory`, чтобы просмотреть, что записал Claude](https://code.claude.com/docs/en/memory)

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

## Выпуск

### Git

#### Разрешить конфликты слияния

```text
resolve the merge conflicts in this branch and explain what you kept from each side
```

**Почему это работает.** Скажите, какое состояние вам нужно, а не какие маркеры оставить. Запрос обоснования делает слияние проверяемым, а не чёрным ящиком.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Закоммитить со сгенерированным сообщением

```text
commit these changes with a message that summarizes what I did
```

**Почему это работает.** Позвольте Claude вывести сообщение из diff. Оно соответствует стилю коммитов, уже принятому в вашем репозитории.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Открыть pull request из тикета

**Нужно:** ваш трекер задач, добавленный как [коннектор claude.ai](https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai) или [сервер MCP](https://code.claude.com/docs/en/mcp).

```text
find the Linear ticket about the login timeout and open a PR that implements it
```

**Почему это работает.** Пропустите переключение контекста между трекером, редактором и GitHub. Один промпт читает спецификацию, вносит изменение и открывает pull request.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

### Релиз

#### Составить примечания к релизу по истории git

```text
compare v2.3.0 to v2.4.0 and draft release notes grouped by feature, fix, and breaking change
```

**Почему это работает.** Дайте две опорные точки и нужную структуру. Claude читает журнал коммитов между ними и составляет черновик списка изменений, который вы можете отредактировать.

**Закрепить.** [Сохраните это как навык `/changelog`](https://code.claude.com/docs/en/skills)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Продукт, Документация, Маркетинг

#### Написать рабочий процесс CI

```text
write a GitHub Actions workflow that runs the tests and deploys to staging on every push to main
```

**Почему это работает.** Опишите, когда он должен запускаться и что делать; YAML будет сгенерирован за вас, в соответствии с командами сборки и тестирования вашего проекта.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Дежурство

## Эксплуатация

### Отладка

#### Найти и исправить падающий тест — ★ Начните отсюда · 3

```text
the UserAuth test is failing, find out why and fix it
```

**Почему это работает.** Опишите симптом; вам не нужно знать, какой файл сломан. Claude запускает тест, чтобы увидеть сбой, отслеживает его до исходного кода и исправляет.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Расследовать сообщённую ошибку

```text
users are seeing 500 errors on /api/settings. investigate and tell me what is going on
```

**Почему это работает.** Опишите симптом и место; Claude читает соответствующий путь в коде и отслеживает вероятные причины. Вставьте трассировки стека или логи, если они у вас есть.

**Закрепить.** [Поместите в свой runbook глубокую ссылку, которая открывает Claude с уже заполненным этим промптом](https://code.claude.com/docs/en/web-quickstart#pre-fill-sessions)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Дежурство

#### Исправить ошибку сборки в корне

_Сначала вставьте вывод ошибки в промпт, затем отправьте это:_

```text
here is a build error. fix the root cause and verify the build succeeds
```

**Почему это работает.** Запрос корневой причины и проверки предотвращает поверхностные заплатки, которые подавляют ошибку, не исправляя её.

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices) · **Роли:** Дежурство

### Инцидент

#### Расследовать инцидент в продакшене

```text
the checkout endpoint started returning 500s an hour ago. check the logs, recent deploys, and config changes, then tell me the most likely cause
```

**Почему это работает.** Перечислите источники данных для сопоставления, а не шаги, которые нужно предпринять. Claude читает логи, историю git и конфигурацию вместе, чтобы сузить причину.

**Закрепить.** [Подключите Sentry или ваше хранилище логов через MCP](https://code.claude.com/docs/en/mcp)

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) · **Роли:** Дежурство, Безопасность

#### Диагностировать по скриншоту консоли

_Вставьте, перетащите или упомяните через @ ваш скриншот, затем отправьте это:_

```text
here is a screenshot of the GCP Kubernetes dashboard. walk me through why this pod is failing and give me the exact commands to fix it
```

**Почему это работает.** Облачные консоли показывают проблему, но не команды для её устранения. Claude читает скриншот и переводит панель управления в команды kubectl, gcloud или aws, которые нужно выполнить.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Дежурство, Данные

#### Запрашивать логи на обычном языке

**Нужно:** ваше хранилище данных или логов, добавленное как [коннектор claude.ai](https://code.claude.com/docs/en/mcp#use-mcp-servers-from-claude-ai) или [сервер MCP](https://code.claude.com/docs/en/mcp).

```text
show me all failed logins for the auth service over the past 24 hours. write the query, run it, and tell me what stands out
```

**Почему это работает.** Задайте вопрос вместо того, чтобы писать SQL. Claude строит запрос, выполняет его по подключённым логам и показывает и запрос, и результат, чтобы вы могли проверить, что было выполнено.

**Источник.** [Как Anthropic использует Claude в кибербезопасности](https://claude.com/blog/how-anthropic-uses-claude-cybersecurity) · **Роли:** Безопасность, Дежурство, Данные

### Данные

#### Проанализировать файл данных

_Перетащите ваш файл в промпт или замените путь ниже собственным упоминанием через @:_

```text
read @reports/q1-signups.csv, summarize the key patterns, and write the results to an HTML page with charts, then open it in my browser
```

**Почему это работает.** Разовому вопросу не нужен разовый скрипт. Укажите на файл в папке вашего проекта, и Claude прочитает его напрямую, найдёт закономерности и запишет результат туда, куда вы попросите.

**Закрепить.** [Подключите источник данных через MCP вместо экспорта файлов](https://code.claude.com/docs/en/mcp)

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Данные, Продукт, Маркетинг

#### Сгенерировать варианты по данным о результативности

_Перетащите ваш файл в промпт или замените путь ниже собственным упоминанием через @:_

```text
read @ads-performance.csv, find the underperforming headlines, and generate 20 new variations that stay under 90 characters
```

**Почему это работает.** Укажите ограничение в самом начале, чтобы генерация оставалась в пределах лимита. Claude читает метрики, выбирает, что заменить, и создаёт подходящие альтернативы.

**Закрепить.** [Подключите рекламную платформу через MCP вместо экспорта файла](https://code.claude.com/docs/en/mcp)

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Маркетинг, Данные

### Автоматизация

#### Превратить повторяющуюся задачу в навык

```text
create a /ship skill for this project that runs the linter and tests, then drafts a commit message
```

**Почему это работает.** Назовите шаги один раз; используйте их повторно как команду. Claude пишет [навык](https://code.claude.com/docs/en/skills), который сможет запустить любой в вашей команде.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Добавить хук для повторяющегося поведения

```text
write a hook that runs prettier after every edit to a .ts or .tsx file
```

**Почему это работает.** Хуки делают поведение автоматическим, а не тем, о чём нужно помнить и просить. Опишите триггер и действие, и Claude напишет конфигурацию [хука](https://code.claude.com/docs/en/hooks).

**Источник.** [Лучшие практики](https://code.claude.com/docs/en/best-practices)

#### Подключить инструмент через MCP

```text
set up the Sentry MCP server so you can read my error reports directly
```

**Почему это работает.** Подключите источник один раз, вместо того чтобы вставлять данные в каждой сессии. После настройки [MCP](https://code.claude.com/docs/en/mcp) Claude читает данные из инструмента напрямую, когда вы о них спрашиваете.

**Источник.** [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows)

#### Зафиксировать то, что нужно запомнить на следующий раз

```text
summarize what we did this session and suggest what to add to CLAUDE.md
```

**Почему это работает.** Спросите, пока не забыли. Claude знает, что ему пришлось выяснить в этой сессии, и предлагает записи для [CLAUDE.md](https://code.claude.com/docs/en/memory), чтобы следующая сессия начиналась с этим контекстом.

**Источник.** [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code) · **Роли:** Продукт, Документация

## Что делает эти промпты рабочими

Промпты выше объединяет несколько закономерностей. Умение их распознавать помогает адаптировать любой промпт отсюда под собственную задачу.

**Описывайте результат, а не шаги.** Скажите, что вам нужно, и позвольте Claude найти файлы. Промпт ниже работает, не называя ни единого пути к файлу.

```text
add rate limiting to the public API and make sure existing tests still pass
```

**Дайте ему способ проверить свою работу.** Просите запустить, протестировать, сравнить или проверить в том же промпте, чтобы Claude итерировал, а не останавливался после одной попытки.

```text
write the migration, run it against the dev database, and confirm the schema matches
```

**Указывайте на образец.** Назовите существующий файл, тест или образец, которому нужно следовать, чтобы новый код был согласован с тем, что у вас уже есть.

```text
add a settings page that follows the same layout as the profile page
```

**Указывайте измеримую цель.** Когда цель — производительность или покрытие, задайте метрику и порог, чтобы завершённость была однозначной.

```text
get the bundle size under 200KB and show me what you removed
```

**Дайте ему артефакт.** Вставляйте ошибки, логи, скриншоты и вывод плана прямо в промпт или наберите `@`, чтобы сослаться на файл. Claude читает первоисточник, а не ваше описание его.

```text
why is the build failing? @build.log
```

**Скажите, каким хотите ответ.** Назовите формат, длину или аудиторию, чтобы объяснение подходило под то, как вы его будете использовать. Чтобы сделать формат стандартным для каждого ответа, задайте [стиль вывода](https://code.claude.com/docs/en/output-styles).

```text
explain how the payment retry logic works as an HTML page with a diagram, then open it in my browser
```

Подробнее о каждой закономерности см. в разделе [лучшие практики](https://code.claude.com/docs/en/best-practices).

## Откуда они взяты

Эти промпты основаны на закономерностях из опубликованных ресурсов Anthropic. Каждая карточка ссылается на свой источник:

* [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows): пошаговые руководства по основным задачам
* [Лучшие практики](https://code.claude.com/docs/en/best-practices): закономерности промптинга и настройка проекта
* [Как команды Anthropic используют Claude Code](https://claude.com/blog/how-anthropic-teams-use-claude-code): реальные рабочие процессы команд инженерии, продукта, дизайна и данных, с углублёнными разборами по [юридическому отделу](https://claude.com/blog/how-anthropic-uses-claude-legal), [маркетингу](https://claude.com/blog/how-anthropic-uses-claude-marketing) и [кибербезопасности](https://claude.com/blog/how-anthropic-uses-claude-cybersecurity)
* [Руководство по масштабированию агентного программирования](https://resources.anthropic.com/hubfs/Scaling%20agentic%20coding%20across%20your%20organization.pdf): руководство по внедрению в корпоративной среде

Видеоразборы этих закономерностей смотрите в бесплатном курсе [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action) на Anthropic Academy.

## Связанные ресурсы

Промпты на этой странице — отправные точки. Как только один из них заработает для вашего проекта, следующий шаг — сделать его воспроизводимым: сохраните его как [навык](https://code.claude.com/docs/en/skills), чтобы любой в вашей команде мог запустить его как `/command`, и зафиксируйте соглашения, которые усвоил Claude, в [CLAUDE.md](https://code.claude.com/docs/en/memory), чтобы каждая сессия начиналась с этим контекстом, а не с повторного изучения его Claude. Для более крупных или рискованных изменений [режим планирования](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode) показывает вам список файлов до того, как произойдут какие-либо правки.

Если вы внедряете Claude Code в команде, см. [администрирование](https://code.claude.com/docs/en/admin-setup) для управляемых настроек и политики, а также [затраты и использование](https://code.claude.com/docs/en/costs) для того, как эта работа тарифицируется по вашему плану.

---

> _Перевод официальной документации Claude Code (раздел «Использование Claude Code»). Источник: https://code.claude.com/docs/en/prompt-library. © Anthropic._

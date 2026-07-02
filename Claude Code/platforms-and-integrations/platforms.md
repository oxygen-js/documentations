# Обзор платформ и интеграций

> Выберите, где запускать Claude Code и что к нему подключить. Сравните CLI, Desktop, VS Code, JetBrains, веб, мобильные устройства и интеграции вроде Chrome, Slack и CI/CD.

Claude Code везде использует один и тот же движок в основе, но каждая среда настроена под свой способ работы. Эта страница поможет выбрать подходящую платформу для вашего рабочего процесса и подключить инструменты, которыми вы уже пользуетесь.

## Где запускать Claude Code

Выбирайте платформу исходя из того, как вам удобнее работать и где находится ваш проект.

| Платформа                          | Лучше всего подходит для                                                                                           | Что вы получаете                                                                                                                                                                              |
| :-------------------------------- | :------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [CLI](https://code.claude.com/docs/en/quickstart)             | Работы в терминале, скриптования, удалённых серверов                                                      | Полный набор возможностей, [Agent SDK](https://code.claude.com/docs/en/headless), [использование компьютера](https://code.claude.com/docs/en/computer-use) на macOS (Pro и Max), сторонние провайдеры                                                               |
| [Desktop](https://code.claude.com/docs/en/desktop)            | Визуального просмотра, параллельных сессий, управляемой настройки                                                    | Просмотр различий (diff), предпросмотр приложения, [использование компьютера](https://code.claude.com/docs/en/desktop#let-claude-use-your-computer) и [Dispatch](https://code.claude.com/docs/en/desktop#sessions-from-dispatch) на Pro и Max                                      |
| [VS Code](https://code.claude.com/docs/en/vs-code)            | Работы внутри VS Code без переключения на терминал                                             | Встроенные различия (diff), интегрированный терминал, контекст файлов                                                                                                                                           |
| [JetBrains](https://code.claude.com/docs/en/jetbrains)        | Работы внутри IntelliJ, PyCharm, WebStorm или других IDE от JetBrains                                | Просмотр различий (diff), передача выделенного текста, сессия в терминале                                                                                                                                         |
| [Веб](https://code.claude.com/docs/en/claude-code-on-the-web) | Долгих задач, не требующих активного управления, или работы, которая должна продолжаться, пока вы офлайн | Облако под управлением Anthropic, работа продолжается после вашего отключения                                                                                                                                   |
| Мобильные устройства                            | Запуска и мониторинга задач вдали от компьютера                                                    | Облачные сессии из приложения Claude для iOS и Android, [Remote Control](https://code.claude.com/docs/en/remote-control) для локальных сессий, [Dispatch](https://code.claude.com/docs/en/desktop#sessions-from-dispatch) в Desktop на Pro и Max |

CLI — самая полная среда для работы в терминале: скриптование и Agent SDK доступны только в CLI. Сторонние провайдеры также работают в [VS Code](https://code.claude.com/docs/en/vs-code#use-third-party-providers). Корпоративные развёртывания [Desktop](https://code.claude.com/docs/en/desktop) поддерживают Vertex AI и провайдеры-шлюзы; для Bedrock или Foundry используйте CLI или VS Code либо [Cowork on 3P research preview](https://claude.com/docs/cowork/3p/overview), где вкладка Code запускается на этих провайдерах. Desktop и расширения для IDE жертвуют некоторыми функциями, доступными только в CLI, ради визуального просмотра и более тесной интеграции с редактором. Веб работает в облаке Anthropic, поэтому задачи продолжают выполняться после вашего отключения. Мобильные устройства — это тонкий клиент к тем же облачным сессиям или к локальной сессии через Remote Control, а также могут отправлять задачи в Desktop с помощью Dispatch.

Вы можете сочетать разные среды в рамках одного проекта. Конфигурация, память проекта и серверы MCP используются совместно на всех локальных средах.

## Подключите свои инструменты

Интеграции позволяют Claude работать с сервисами за пределами вашей кодовой базы.

| Интеграция                          | Что она делает                                       | Для чего использовать                                                       |
| :----------------------------------- | :------------------------------------------------- | :--------------------------------------------------------------- |
| [Chrome](https://code.claude.com/docs/en/chrome)                 | Управляет вашим браузером с вашими активными сессиями (когда вы уже вошли в аккаунты) | Тестирования веб-приложений, заполнения форм, автоматизации сайтов без API |
| [GitHub Actions](https://code.claude.com/docs/en/github-actions) | Запускает Claude в вашем CI-конвейере                    | Автоматических проверок PR, сортировки issue, планового обслуживания        |
| [GitLab CI/CD](https://code.claude.com/docs/en/gitlab-ci-cd)     | То же, что GitHub Actions, но для GitLab                                  | Автоматизации на основе CI в GitLab                                            |
| [Code Review](https://code.claude.com/docs/en/code-review)       | Автоматически проверяет каждый PR                     | Выявления ошибок до проверки человеком                              |
| [Slack](https://code.claude.com/docs/en/slack)                   | Отвечает на упоминания `@Claude` в ваших каналах    | Превращения отчётов об ошибках в pull request прямо из командного чата            |

Для интеграций, не перечисленных здесь, [серверы MCP](https://code.claude.com/docs/en/mcp) и [коннекторы](https://code.claude.com/docs/en/desktop#connect-external-tools) позволяют подключить практически что угодно: Linear, Notion, Google Drive или ваши собственные внутренние API.

## Работайте вдали от терминала

Claude Code предлагает несколько способов работать, когда вы не за терминалом. Они различаются тем, что запускает работу, где выполняется Claude и сколько нужно настраивать.

|                                                | Триггер                                                                                        | Claude выполняется на                                                                               | Настройка                                                                                                                                | Лучше всего подходит для                                                      |
| :--------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| [Dispatch](https://code.claude.com/docs/en/desktop#sessions-from-dispatch) | Отправка задачи сообщением из мобильного приложения Claude                                      | Вашей машине (Desktop)                                                                       | [Свяжите мобильное приложение с Desktop](https://support.claude.com/en/articles/13947068)                                                  | Делегирования работы, пока вы в отъезде, при минимальной настройке              |
| [Remote Control](https://code.claude.com/docs/en/remote-control)           | Управление запущенной сессией из [claude.ai/code](https://claude.ai/code) или мобильного приложения Claude | Вашей машине (CLI или VS Code)                                                                | Запустите `claude remote-control`                                                                                                          | Управления выполняемой работой с другого устройства                 |
| [Channels](https://code.claude.com/docs/en/channels)                       | Отправка событий из чат-приложения вроде Telegram или Discord либо с вашего собственного сервера                       | Вашей машине (CLI)                                                                           | [Установите плагин канала](https://code.claude.com/docs/en/channels#quickstart) или [создайте свой](https://code.claude.com/docs/en/channels-reference)                                      | Реагирования на внешние события, например сбои CI или сообщения в чате |
| [Slack](https://code.claude.com/docs/en/slack)                             | Упоминание `@Claude` в командном канале                                                            | Облаке Anthropic                                                                              | [Установите приложение Slack](https://code.claude.com/docs/en/slack#setting-up-claude-code-in-slack) с включённым [Claude Code в вебе](https://code.claude.com/docs/en/claude-code-on-the-web) | PR и проверок из командного чата                                    |
| [Запланированные задачи](https://code.claude.com/docs/en/scheduled-tasks)         | Задание расписания                                                                                 | [CLI](https://code.claude.com/docs/en/scheduled-tasks), [Desktop](https://code.claude.com/docs/en/desktop-scheduled-tasks) или [облаке](https://code.claude.com/docs/en/routines) | Выберите частоту                                                                                                                         | Периодической автоматизации, например ежедневных проверок                          |

Если вы не уверены, с чего начать, [установите CLI](https://code.claude.com/docs/en/quickstart) и запустите его в каталоге проекта. Если вы предпочитаете не работать в терминале, [Desktop](https://code.claude.com/docs/en/desktop-quickstart) даёт тот же движок с графическим интерфейсом.

## Связанные ресурсы

### Платформы

* [Быстрый старт с CLI](https://code.claude.com/docs/en/quickstart): установите и запустите свою первую команду в терминале
* [Desktop](https://code.claude.com/docs/en/desktop): визуальный просмотр различий (diff), параллельные сессии, использование компьютера и Dispatch
* [VS Code](https://code.claude.com/docs/en/vs-code): расширение Claude Code внутри вашего редактора
* [JetBrains](https://code.claude.com/docs/en/jetbrains): расширение для IntelliJ, PyCharm и других IDE от JetBrains
* [Claude Code в вебе](https://code.claude.com/docs/en/claude-code-on-the-web): облачные сессии, которые продолжают работать после вашего отключения
* Мобильные устройства: приложение Claude для [iOS](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) и [Android](https://play.google.com/store/apps/details?id=com.anthropic.claude) для запуска и мониторинга задач вдали от компьютера

### Интеграции

* [Chrome](https://code.claude.com/docs/en/chrome): автоматизируйте задачи в браузере с вашими активными сессиями (когда вы уже вошли в аккаунты)
* [Использование компьютера](https://code.claude.com/docs/en/computer-use): позвольте Claude открывать приложения и управлять вашим экраном на macOS
* [GitHub Actions](https://code.claude.com/docs/en/github-actions): запускайте Claude в вашем CI-конвейере
* [GitLab CI/CD](https://code.claude.com/docs/en/gitlab-ci-cd): то же самое для GitLab
* [Code Review](https://code.claude.com/docs/en/code-review): автоматическая проверка каждого pull request
* [Slack](https://code.claude.com/docs/en/slack): отправляйте задачи из командного чата и получайте PR в ответ

### Удалённый доступ

* [Dispatch](https://code.claude.com/docs/en/desktop#sessions-from-dispatch): отправьте задачу сообщением с телефона, и она может породить сессию Desktop
* [Remote Control](https://code.claude.com/docs/en/remote-control): управляйте запущенной сессией с телефона или из браузера
* [Channels](https://code.claude.com/docs/en/channels): передавайте события из чат-приложений или ваших собственных серверов в сессию
* [Запланированные задачи](https://code.claude.com/docs/en/scheduled-tasks): запускайте промпты по повторяющемуся расписанию

---

> _Перевод официальной документации Claude Code (раздел «Платформы и интеграции»). Источник: https://code.claude.com/docs/en/platforms. © Anthropic._

# Обзор

> Claude Code — это агентный инструмент для программирования, который читает вашу кодовую базу, редактирует файлы, выполняет команды и интегрируется с вашими инструментами разработки. Доступен в терминале, IDE, настольном приложении и браузере.

Claude Code — это помощник по программированию на базе ИИ, который помогает вам создавать новую функциональность, исправлять ошибки и автоматизировать задачи разработки. Он понимает всю вашу кодовую базу целиком и может работать с несколькими файлами и инструментами, чтобы довести дело до конца.

## Начало работы

Выберите свою среду, чтобы начать. Большинство площадок требуют [подписки Claude](https://claude.com/pricing) или учётной записи [Anthropic Console](https://console.anthropic.com/). CLI для терминала и VS Code также поддерживают [сторонних провайдеров](https://code.claude.com/docs/en/third-party-integrations).

### Терминал

Полнофункциональный CLI для работы с Claude Code прямо в терминале. Редактируйте файлы, выполняйте команды и управляйте всем проектом из командной строки.

Чтобы установить Claude Code, используйте один из следующих способов:

#### Нативная установка (рекомендуется)

**macOS, Linux, WSL:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD:**

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

Если вы видите `The token '&&' is not a valid statement separator`, значит вы в PowerShell, а не в CMD. Если вы видите `'irm' is not recognized as an internal or external command`, значит вы в CMD, а не в PowerShell. Ваше приглашение показывает `PS C:\`, когда вы в PowerShell, и `C:\` без `PS`, когда вы в CMD.

Если команда установки завершается с ошибкой `syntax error near unexpected token '<'`, ошибкой `403` или другой ошибкой curl, обратитесь к разделу [Устранение неполадок установки](https://code.claude.com/docs/en/troubleshoot-install#find-your-error), чтобы сопоставить ошибку с решением и найти альтернативные способы установки.

На нативном Windows рекомендуется установить [Git для Windows](https://git-scm.com/downloads/win), чтобы Claude Code мог использовать инструмент Bash. Если Git для Windows не установлен, Claude Code вместо него использует PowerShell в качестве инструмента оболочки. Установкам на базе WSL Git для Windows не нужен.

> **Инфо.** Нативные установки автоматически обновляются в фоновом режиме, чтобы у вас всегда была последняя версия.

#### Homebrew

```bash
brew install --cask claude-code
```

Homebrew предлагает два cask-пакета. `claude-code` отслеживает стабильный канал выпуска, который обычно отстаёт примерно на неделю и пропускает релизы с серьёзными регрессиями. `claude-code@latest` отслеживает канал latest и получает новые версии сразу же после их выхода.

> **Инфо.** Установки через Homebrew не обновляются автоматически. Запускайте `brew upgrade claude-code` или `brew upgrade claude-code@latest` — в зависимости от того, какой cask вы установили, — чтобы получать последние возможности и исправления безопасности.

#### WinGet

```powershell
winget install Anthropic.ClaudeCode
```

> **Инфо.** Установки через WinGet не обновляются автоматически. Периодически запускайте `winget upgrade Anthropic.ClaudeCode`, чтобы получать последние возможности и исправления безопасности.

---

Вы также можете установить с помощью [apt, dnf или apk](https://code.claude.com/docs/en/setup#install-with-linux-package-managers) в Debian, Fedora, RHEL и Alpine.

Затем запустите Claude Code в любом проекте:

```bash
cd your-project
claude
```

При первом использовании вам будет предложено войти в систему. Вот и всё! [Продолжайте с Быстрого старта →](https://code.claude.com/docs/en/quickstart)

> **Совет.** См. [расширенную настройку](https://code.claude.com/docs/en/setup) для вариантов установки, ручных обновлений или инструкций по удалению. Посетите [устранение неполадок установки](https://code.claude.com/docs/en/troubleshoot-install), если столкнётесь с проблемами.

### VS Code

Расширение для VS Code предоставляет встроенные diff-сравнения, @-упоминания, обзор плана и историю переписки прямо в вашем редакторе.

* [Установить для VS Code](vscode:extension/anthropic.claude-code)
* [Установить для Cursor](cursor:extension/anthropic.claude-code)

Или найдите «Claude Code» в разделе Extensions (`Cmd+Shift+X` на Mac, `Ctrl+Shift+X` на Windows/Linux). После установки откройте палитру команд (`Cmd+Shift+P` / `Ctrl+Shift+P`), введите «Claude Code» и выберите **Open in New Tab**.

[Начать работу с VS Code →](https://code.claude.com/docs/en/vs-code#get-started)

### Настольное приложение

Отдельное приложение для запуска Claude Code вне вашей IDE или терминала. Просматривайте diff-сравнения визуально, запускайте несколько сессий бок о бок, планируйте повторяющиеся задачи и запускайте облачные сессии.

Скачайте и установите:

* [macOS](https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect) (Intel и Apple Silicon)
* [Windows](https://claude.ai/api/desktop/win32/x64/setup/latest/redirect) (x64)
* [Windows ARM64](https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect)

После установки запустите Claude, войдите в систему и нажмите вкладку **Code**, чтобы начать программировать. Требуется [платная подписка](https://claude.com/pricing).

[Узнать больше о настольном приложении →](https://code.claude.com/docs/en/desktop-quickstart)

### Веб

Запускайте Claude Code в браузере без локальной настройки. Запускайте длительные задачи и возвращайтесь, когда они будут готовы, работайте с репозиториями, которых у вас нет локально, или выполняйте несколько задач параллельно. Доступно в браузерах на настольных компьютерах и в приложении Claude для iOS.

Начните программировать на [claude.ai/code](https://claude.ai/code).

[Начать работу в вебе →](https://code.claude.com/docs/en/web-quickstart)

### JetBrains

Плагин для IntelliJ IDEA, PyCharm, WebStorm и других IDE от JetBrains с интерактивным просмотром diff-сравнений и передачей контекста выделенного фрагмента.

Установите [плагин Claude Code](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-) из JetBrains Marketplace и перезапустите свою IDE. Плагину требуется CLI Claude Code, устанавливаемый отдельно; см. [шаги настройки для JetBrains](https://code.claude.com/docs/en/jetbrains#installation).

[Начать работу с JetBrains →](https://code.claude.com/docs/en/jetbrains)

## Что вы можете делать

Вот некоторые из способов использования Claude Code:

### Автоматизируйте работу, которую вы постоянно откладываете

Claude Code берёт на себя рутинные задачи, съедающие ваш день: написание тестов для непокрытого кода, исправление ошибок линтера по всему проекту, разрешение конфликтов слияния, обновление зависимостей и составление примечаний к релизу.

```bash
claude "write tests for the auth module, run them, and fix any failures"
```

### Создавайте функциональность и исправляйте ошибки

Опишите, что вы хотите, простым языком. Claude Code планирует подход, пишет код в нескольких файлах и проверяет, что он работает.

Для ошибок вставьте сообщение об ошибке или опишите симптом. Claude Code прослеживает проблему через вашу кодовую базу, определяет первопричину и реализует исправление. См. [распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) для дополнительных примеров.

### Создавайте коммиты и pull request'ы

Claude Code работает напрямую с git. Он индексирует изменения, пишет сообщения коммитов, создаёт ветки и открывает pull request'ы.

```bash
claude "commit my changes with a descriptive message"
```

В CI вы можете автоматизировать обзор кода и сортировку задач с помощью [GitHub Actions](https://code.claude.com/docs/en/github-actions) или [GitLab CI/CD](https://code.claude.com/docs/en/gitlab-ci-cd).

### Подключайте свои инструменты с помощью MCP

[Model Context Protocol (MCP)](https://code.claude.com/docs/en/mcp) — это открытый стандарт для подключения инструментов ИИ к внешним источникам данных. С MCP Claude Code может читать ваши проектные документы в Google Drive, обновлять тикеты в Jira, извлекать данные из Slack или использовать ваш собственный инструментарий. [Быстрый старт MCP](https://code.claude.com/docs/en/mcp-quickstart) подключает ваш первый сервер от начала до конца.

### Настраивайте с помощью инструкций, навыков (skills) и хуков

[`CLAUDE.md`](https://code.claude.com/docs/en/memory) — это markdown-файл, который вы добавляете в корень проекта и который Claude Code читает в начале каждой сессии. Используйте его, чтобы задать стандарты кодирования, архитектурные решения, предпочитаемые библиотеки и чек-листы для обзора. Claude также формирует [автопамять](https://code.claude.com/docs/en/memory#auto-memory) в процессе работы, сохраняя выводы — например, команды сборки и находки при отладке — между сессиями, при этом вам ничего не нужно записывать.

Создавайте [навыки (skills)](https://code.claude.com/docs/en/skills), чтобы упаковывать повторяемые рабочие процессы, которыми может делиться ваша команда, например `/review-pr` или `/deploy-staging`.

[Хуки](https://code.claude.com/docs/en/hooks) позволяют выполнять команды оболочки до или после действий Claude Code — например, автоформатирование после каждого редактирования файла или запуск линтера перед коммитом.

### Запускайте команды агентов и создавайте собственных агентов

Порождайте [несколько агентов Claude Code](https://code.claude.com/docs/en/sub-agents), которые одновременно работают над разными частями задачи. Ведущий агент координирует работу, назначает подзадачи и объединяет результаты.

Чтобы запустить несколько полноценных сессий параллельно и наблюдать за ними с одного экрана, используйте [фоновых агентов](https://code.claude.com/docs/en/agent-view). Для полностью кастомных рабочих процессов [Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) позволяет создавать собственных агентов на основе инструментов и возможностей Claude Code с полным контролем над оркестрацией, доступом к инструментам и разрешениями.

### Направляйте вывод по конвейеру, пишите скрипты и автоматизируйте с помощью CLI

Claude Code компонуем и следует философии Unix. Направляйте в него логи по конвейеру, запускайте его в CI или объединяйте в цепочки с другими инструментами:

```bash
# Анализ недавнего вывода логов
tail -200 app.log | claude -p "Slack me if you see any anomalies"

# Автоматизация переводов в CI
claude -p "translate new strings into French and raise a PR for review"

# Массовые операции над файлами
git diff main --name-only | claude -p "review these changed files for security issues"
```

См. [справочник по CLI](https://code.claude.com/docs/en/cli-reference) для полного набора команд и флагов.

### Планируйте повторяющиеся задачи

Запускайте Claude по расписанию, чтобы автоматизировать повторяющуюся работу: утренние обзоры PR, ночной анализ сбоев CI, еженедельные аудиты зависимостей или синхронизацию документации после слияния PR.

* [Routines (рутины)](https://code.claude.com/docs/en/routines) выполняются на инфраструктуре, управляемой Anthropic, поэтому они продолжают работать, даже когда ваш компьютер выключен. Они также могут запускаться по вызовам API или событиям GitHub. Создавайте их из веба, настольного приложения или запуском `/schedule` в CLI.
* [Запланированные задачи настольного приложения](https://code.claude.com/docs/en/desktop-scheduled-tasks) выполняются на вашей машине, с прямым доступом к вашим локальным файлам и инструментам.
* [`/loop`](https://code.claude.com/docs/en/scheduled-tasks) повторяет запрос внутри сессии CLI для быстрого опроса.

### Работайте откуда угодно

Сессии не привязаны к одной площадке. Перемещайте работу между средами по мере изменения вашего контекста:

* Отойдите от рабочего стола и продолжайте работать с телефона или любого браузера с помощью [Remote Control (удалённого управления)](https://code.claude.com/docs/en/remote-control).
* Отправьте задачу в [Dispatch](https://code.claude.com/docs/en/desktop#sessions-from-dispatch) с телефона и откройте создаваемую им сессию настольного приложения.
* Запустите длительную задачу в [вебе](https://code.claude.com/docs/en/claude-code-on-the-web) или [приложении для iOS](https://apps.apple.com/app/claude-by-anthropic/id6473753684), затем перенесите её в терминал с помощью `claude --teleport`. Для Teleport требуется подписка claude.ai.
* Передайте сессию терминала в [настольное приложение](https://code.claude.com/docs/en/desktop) с помощью `/desktop` для визуального просмотра diff-сравнений.
* Маршрутизируйте задачи из командного чата: упомяните `@Claude` в [Slack](https://code.claude.com/docs/en/slack) с отчётом об ошибке и получите обратно pull request.

## Используйте Claude Code везде

Каждая площадка подключается к одному и тому же базовому движку Claude Code, поэтому ваши файлы CLAUDE.md, настройки и MCP-серверы работают во всех из них.

Помимо перечисленных выше сред — [Терминал](https://code.claude.com/docs/en/quickstart), [VS Code](https://code.claude.com/docs/en/vs-code), [JetBrains](https://code.claude.com/docs/en/jetbrains), [Настольное приложение](https://code.claude.com/docs/en/desktop) и [Веб](https://code.claude.com/docs/en/claude-code-on-the-web) — Claude Code интегрируется с CI/CD, чатами и браузерными рабочими процессами:

| Я хочу…                                                                                | Лучший вариант                                                                                                     |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Продолжить локальную сессию с телефона или другого устройства                          | [Remote Control](https://code.claude.com/docs/en/remote-control)                                                  |
| Отправлять события из Telegram, Discord, iMessage или собственных вебхуков в сессию    | [Channels (каналы)](https://code.claude.com/docs/en/channels)                                                     |
| Начать задачу локально, продолжить на мобильном                                        | [Веб](https://code.claude.com/docs/en/claude-code-on-the-web) или [приложение Claude для iOS](https://apps.apple.com/app/claude-by-anthropic/id6473753684) |
| Запускать Claude по повторяющемуся расписанию                                          | [Routines](https://code.claude.com/docs/en/routines) или [запланированные задачи настольного приложения](https://code.claude.com/docs/en/desktop-scheduled-tasks) |
| Автоматизировать обзоры PR и сортировку задач                                          | [GitHub Actions](https://code.claude.com/docs/en/github-actions) или [GitLab CI/CD](https://code.claude.com/docs/en/gitlab-ci-cd) |
| Получать автоматический обзор кода на каждый PR                                        | [GitHub Code Review](https://code.claude.com/docs/en/code-review)                                                 |
| Направлять отчёты об ошибках из Slack в pull request'ы                                 | [Slack](https://code.claude.com/docs/en/slack)                                                                    |
| Отлаживать «живые» веб-приложения                                                      | [Chrome](https://code.claude.com/docs/en/chrome)                                                                  |
| Создавать собственных агентов для своих рабочих процессов                              | [Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview)                                                   |

## Дальнейшие шаги

После установки Claude Code эти руководства помогут вам углубиться.

* [Быстрый старт](https://code.claude.com/docs/en/quickstart): пройдите свою первую настоящую задачу — от изучения кодовой базы до коммита исправления.
* [Храните инструкции и воспоминания](https://code.claude.com/docs/en/memory): дайте Claude постоянные инструкции с помощью файлов CLAUDE.md и автопамяти.
* [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) и [лучшие практики](https://code.claude.com/docs/en/best-practices): паттерны для максимальной отдачи от Claude Code.
* [Настройки](https://code.claude.com/docs/en/settings): настройте Claude Code под свой рабочий процесс.
* [Устранение неполадок](https://code.claude.com/docs/en/troubleshooting): решения распространённых проблем.
* [code.claude.com](https://code.claude.com/): демо, цены и сведения о продукте.

---

> _Перевод официальной документации Claude Code (раздел «Getting started»). Источник: https://code.claude.com/docs/en/overview. © Anthropic._

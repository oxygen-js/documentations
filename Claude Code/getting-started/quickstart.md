# Быстрый старт

> Добро пожаловать в Claude Code!

Это руководство по быстрому старту позволит вам начать пользоваться помощью в программировании на базе ИИ всего за несколько минут. К концу вы будете понимать, как использовать Claude Code для распространённых задач разработки.

## Прежде чем начать

Убедитесь, что у вас есть:

* Открытый терминал или командная строка.
  * Если вы никогда раньше не пользовались терминалом, ознакомьтесь с [руководством по терминалу](https://code.claude.com/docs/en/terminal-guide).
* Проект с кодом, с которым можно работать.
* [Подписка Claude](https://claude.com/pricing) (Pro, Max, Team или Enterprise), учётная запись [Claude Console](https://console.anthropic.com/) или доступ через [поддерживаемого облачного провайдера](https://code.claude.com/docs/en/third-party-integrations).

> **Примечание.** Это руководство охватывает CLI для терминала. Claude Code также доступен в [вебе](https://claude.ai/code), в виде [настольного приложения](https://code.claude.com/docs/en/desktop), в [VS Code](https://code.claude.com/docs/en/vs-code) и [IDE от JetBrains](https://code.claude.com/docs/en/jetbrains), в [Slack](https://code.claude.com/docs/en/slack), а также в CI/CD с [GitHub Actions](https://code.claude.com/docs/en/github-actions) и [GitLab](https://code.claude.com/docs/en/gitlab-ci-cd). См. [все интерфейсы](https://code.claude.com/docs/en/overview#use-claude-code-everywhere).

## Шаг 1: Установите Claude Code

Чтобы установить Claude Code, используйте один из следующих способов:

### Нативная установка (рекомендуется)

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

### Homebrew

```bash
brew install --cask claude-code
```

Homebrew предлагает два cask-пакета. `claude-code` отслеживает стабильный канал выпуска, который обычно отстаёт примерно на неделю и пропускает релизы с серьёзными регрессиями. `claude-code@latest` отслеживает канал latest и получает новые версии сразу же после их выхода.

> **Инфо.** Установки через Homebrew не обновляются автоматически. Запускайте `brew upgrade claude-code` или `brew upgrade claude-code@latest` — в зависимости от того, какой cask вы установили, — чтобы получать последние возможности и исправления безопасности.

### WinGet

```powershell
winget install Anthropic.ClaudeCode
```

> **Инфо.** Установки через WinGet не обновляются автоматически. Периодически запускайте `winget upgrade Anthropic.ClaudeCode`, чтобы получать последние возможности и исправления безопасности.

---

Вы также можете установить с помощью [apt, dnf или apk](https://code.claude.com/docs/en/setup#install-with-linux-package-managers) в Debian, Fedora, RHEL и Alpine.

## Шаг 2: Войдите в свою учётную запись

Для использования Claude Code требуется учётная запись. Запустите интерактивную сессию командой `claude`, и при первом использовании вам будет предложено войти в систему:

```bash
claude
```

Для учётных записей с подпиской Claude или Console следуйте подсказкам, чтобы завершить аутентификацию в браузере. Чтобы позже сменить учётную запись или пройти аутентификацию заново, введите `/login` внутри запущенной сессии:

```text
/login
```

Вы можете войти, используя любой из этих типов учётных записей:

* [Claude Pro, Max, Team или Enterprise](https://claude.com/pricing) (рекомендуется).
* [Claude Console](https://console.anthropic.com/) (доступ к API с предоплаченными кредитами). При первом входе в Console автоматически создаётся рабочее пространство «Claude Code» для централизованного учёта затрат.
* [Amazon Bedrock, Google Vertex AI или Microsoft Foundry](https://code.claude.com/docs/en/third-party-integrations) (корпоративные облачные провайдеры).
* Самостоятельно размещённый [Claude apps gateway](https://code.claude.com/docs/en/claude-apps-gateway), если ваша организация его использует: ваш администратор заранее настраивает URL шлюза, и `/login` открывается прямо на экране **Cloud gateway**, чтобы вы могли войти с помощью корпоративного SSO.

После входа ваши учётные данные сохраняются, и повторно входить не потребуется.

## Шаг 3: Начните свою первую сессию

Откройте терминал в любом каталоге проекта и запустите Claude Code:

```bash
cd /path/to/your/project
claude
```

Вы увидите приглашение Claude Code, а над ним — версию, текущую модель и рабочий каталог. Введите `/help`, чтобы увидеть доступные команды, или `/resume`, чтобы продолжить предыдущий разговор.

> **Совет.** После входа (шаг 2) ваши учётные данные хранятся в вашей системе. Подробнее — в разделе [Управление учётными данными](https://code.claude.com/docs/en/authentication#credential-management).

## Шаг 4: Задайте свой первый вопрос

Давайте начнём с понимания вашей кодовой базы. Попробуйте одну из этих команд:

```text
what does this project do?
```

Claude проанализирует ваши файлы и предоставит сводку. Вы также можете задавать более конкретные вопросы:

```text
what technologies does this project use?
```

```text
where is the main entry point?
```

```text
explain the folder structure
```

Вы также можете спросить Claude о его собственных возможностях:

```text
what can Claude Code do?
```

```text
how do I create custom skills in Claude Code?
```

```text
can Claude Code work with Docker?
```

> **Примечание.** Claude Code читает файлы вашего проекта по мере необходимости. Вам не нужно добавлять контекст вручную.

## Шаг 5: Внесите своё первое изменение в код

Теперь давайте заставим Claude Code выполнить настоящее программирование. Попробуйте простую задачу:

```text
add a hello world function to the main file
```

Claude Code:

1. Найдёт подходящий файл.
2. Покажет вам предлагаемые изменения.
3. Запросит ваше одобрение.
4. Внесёт правку.

> **Примечание.** Claude Code всегда запрашивает разрешение перед изменением файлов. Вы можете одобрять отдельные изменения или включить режим «Accept all» на сессию.

## Шаг 6: Используйте Git с Claude Code

Claude Code делает операции Git разговорными:

```text
what files have I changed?
```

```text
commit my changes with a descriptive message
```

Вы также можете запрашивать более сложные операции Git:

```text
create a new branch called feature/quickstart
```

```text
show me the last 5 commits
```

```text
help me resolve merge conflicts
```

## Шаг 7: Исправьте ошибку или добавьте функциональность

Claude умело справляется с отладкой и реализацией функциональности.

Опишите, что вы хотите, на естественном языке:

```text
add input validation to the user registration form
```

Или исправьте существующие проблемы:

```text
there's a bug where users can submit empty forms - fix it
```

Claude Code:

* Найдёт соответствующий код.
* Поймёт контекст.
* Реализует решение.
* Запустит тесты, если они есть.

## Шаг 8: Опробуйте другие распространённые рабочие процессы

Есть множество способов работать с Claude:

**Рефакторинг кода**

```text
refactor the authentication module to use async/await instead of callbacks
```

**Написание тестов**

```text
write unit tests for the calculator functions
```

**Обновление документации**

```text
update the README with installation instructions
```

**Обзор кода**

```text
review my changes and suggest improvements
```

> **Совет.** Общайтесь с Claude, как с полезным коллегой. Опишите, чего вы хотите достичь, и он поможет вам этого добиться.

## Основные команды

Вот важнейшие команды для повседневного использования. Команды оболочки выполняются из терминала, чтобы запустить или возобновить Claude Code. Команды сессии выполняются внутри Claude Code после его запуска.

**Команды оболочки**

| Команда             | Что она делает                                                | Пример                              |
| ------------------- | ------------------------------------------------------------- | ----------------------------------- |
| `claude`            | Запустить интерактивный режим                                 | `claude`                            |
| `claude "task"`     | Выполнить разовую задачу                                      | `claude "fix the build error"`      |
| `claude -p "query"` | Выполнить разовый запрос и выйти                              | `claude -p "explain this function"` |
| `claude -c`         | Продолжить самый недавний разговор в текущем каталоге         | `claude -c`                         |
| `claude -r`         | Возобновить предыдущий разговор                               | `claude -r`                         |

**Команды сессии**

| Команда           | Что она делает             | Пример   |
| ----------------- | -------------------------- | -------- |
| `/clear`          | Очистить историю разговора | `/clear` |
| `/help`           | Показать доступные команды | `/help`  |
| `/exit` или Ctrl+D | Выйти из Claude Code       | `/exit`  |

См. [справочник по CLI](https://code.claude.com/docs/en/cli-reference) для полного списка команд оболочки и [справочник по командам](https://code.claude.com/docs/en/commands) для полного списка команд сессии.

## Профессиональные советы для начинающих

Подробнее см. [лучшие практики](https://code.claude.com/docs/en/best-practices) и [распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows).

### Формулируйте запросы конкретно

Вместо: «fix the bug»

Попробуйте: «fix the login bug where users see a blank screen after entering wrong credentials»

### Используйте пошаговые инструкции

Разбивайте сложные задачи на шаги:

```text
1. create a new database table for user profiles
2. create an API endpoint to get and update user profiles
3. build a webpage that allows users to see and edit their information
```

### Дайте Claude сначала изучить код

Прежде чем вносить изменения, дайте Claude понять ваш код:

```text
analyze the database schema
```

```text
build a dashboard showing products that are most frequently returned by our UK customers
```

### Экономьте время с помощью горячих клавиш

* Введите `/`, чтобы увидеть все команды и навыки (skills).
* Используйте Tab для автодополнения команд.
* Нажмите ↑ для истории команд.
* Нажмите `Shift+Tab`, чтобы переключаться между режимами разрешений.

## Что дальше?

Теперь, когда вы освоили основы, изучите более продвинутые возможности:

* [Как работает Claude Code](https://code.claude.com/docs/en/how-claude-code-works) — разберитесь в агентном цикле, встроенных инструментах и в том, как Claude Code взаимодействует с вашим проектом.
* [Лучшие практики](https://code.claude.com/docs/en/best-practices) — получайте лучшие результаты с помощью эффективных запросов и настройки проекта.
* [Распространённые рабочие процессы](https://code.claude.com/docs/en/common-workflows) — пошаговые руководства для типичных задач.
* [Расширяйте Claude Code](https://code.claude.com/docs/en/features-overview) — настраивайте с помощью CLAUDE.md, навыков (skills), хуков, MCP и не только.

## Получение помощи

* **В Claude Code**: введите `/help` или спросите «how do I...».
* **Документация**: вы уже здесь! Просматривайте другие руководства.
* **Сообщество**: присоединяйтесь к нашему [Discord](https://www.anthropic.com/discord) за советами и поддержкой.

---

> _Перевод официальной документации Claude Code (раздел «Getting started»). Источник: https://code.claude.com/docs/en/quickstart. © Anthropic._

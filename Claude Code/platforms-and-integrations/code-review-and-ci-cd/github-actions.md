# GitHub Actions

> Узнайте об интеграции Claude Code в ваш процесс разработки с помощью Claude Code GitHub Actions

Claude Code GitHub Actions привносит автоматизацию на основе ИИ в ваш рабочий процесс GitHub. С помощью простого упоминания `@claude` в любом PR или issue Claude может анализировать ваш код, создавать pull request'ы, реализовывать функции и исправлять ошибки — и всё это с соблюдением стандартов вашего проекта. Об автоматических обзорах, публикуемых в каждом PR без триггера, см. [GitHub Code Review](https://code.claude.com/docs/en/code-review).

> **Примечание.** Claude Code GitHub Actions построен на основе [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview), который обеспечивает программную интеграцию Claude Code в ваши приложения. Вы можете использовать SDK для создания собственных рабочих процессов автоматизации, выходящих за рамки GitHub Actions.

## Зачем использовать Claude Code GitHub Actions?

* **Мгновенное создание PR**: опишите, что вам нужно, и Claude создаст полноценный PR со всеми необходимыми изменениями
* **Автоматическая реализация кода**: превращайте issues в рабочий код одной командой
* **Соблюдение ваших стандартов**: Claude учитывает рекомендации из вашего `CLAUDE.md` и существующие паттерны кода
* **Простая настройка**: начните работу за несколько минут с помощью нашего установщика и API-ключа
* **Безопасность по умолчанию**: ваш код остаётся на раннерах GitHub

## Что умеет Claude?

Claude Code предоставляет мощный GitHub Action, который преобразует то, как вы работаете с кодом:

### Claude Code Action

Этот GitHub Action позволяет запускать Claude Code внутри ваших рабочих процессов GitHub Actions. Вы можете использовать его для создания любого собственного рабочего процесса на основе Claude Code.

[Посмотреть репозиторий →](https://github.com/anthropics/claude-code-action)

## Настройка

## Быстрая настройка

Запустите `/install-github-app` в терминале Claude Code, чтобы настроить интеграцию в интерактивном режиме. Команда устанавливает Claude GitHub App в ваш репозиторий, а затем проводит вас через добавление рабочих процессов GitHub Actions и секрета с API-ключом.

После установки GitHub App команда спрашивает, продолжать ли настройку GitHub Actions. В Claude Code v2.1.187 и новее вы можете выбрать **Skip for now**, чтобы остановиться на установке только App и вернуться к шагам с рабочим процессом и секретом позже, снова запустив `/install-github-app`. Более ранние версии переходят сразу к выбору рабочего процесса.

> **Примечание.**
> * Вы должны быть администратором репозитория, чтобы установить GitHub App и добавить секреты
> * GitHub App запросит разрешения на чтение и запись для Contents, Issues и Pull requests
> * Этот метод быстрого старта доступен только для прямых пользователей Claude API. Если вы используете Amazon Bedrock или Google Vertex AI, см. раздел [Использование с Amazon Bedrock и Google Vertex AI](#using-with-amazon-bedrock-%26-google-vertex-ai).

## Ручная настройка

Если команда `/install-github-app` завершается ошибкой или вы предпочитаете ручную настройку, следуйте этим инструкциям по ручной настройке:

1. **Установите Claude GitHub app** в ваш репозиторий: [https://github.com/apps/claude](https://github.com/apps/claude)

   Claude GitHub app требует следующих разрешений репозитория:

   * **Contents**: Read & write (для изменения файлов репозитория)
   * **Issues**: Read & write (для ответа на issues)
   * **Pull requests**: Read & write (для создания PR и отправки изменений)

   Более подробную информацию о безопасности и разрешениях см. в [документации по безопасности](https://github.com/anthropics/claude-code-action/blob/main/docs/security.md).
2. **Добавьте ANTHROPIC\_API\_KEY** в секреты вашего репозитория ([Узнайте, как использовать секреты в GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions))
3. **Скопируйте файл рабочего процесса** из [examples/claude.yml](https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml) в каталог `.github/workflows/` вашего репозитория

> **Совет.** После завершения быстрого старта или ручной настройки протестируйте action, отметив `@claude` в комментарии к issue или PR.

## Обновление с бета-версии

> **Предупреждение.** Claude Code GitHub Actions v1.0 вносит несовместимые изменения, которые требуют обновления файлов ваших рабочих процессов для перехода на v1.0 с бета-версии.

Если вы в настоящее время используете бета-версию Claude Code GitHub Actions, мы рекомендуем обновить ваши рабочие процессы до версии GA. Новая версия упрощает конфигурацию, добавляя при этом мощные новые функции, такие как автоматическое определение режима.

### Основные изменения

Все пользователи бета-версии должны внести эти изменения в файлы своих рабочих процессов для перехода на новую версию:

1. **Обновите версию action**: измените `@beta` на `@v1`
2. **Удалите конфигурацию режима**: удалите `mode: "tag"` или `mode: "agent"` (теперь определяется автоматически)
3. **Обновите входные данные промпта**: замените `direct_prompt` на `prompt`
4. **Перенесите параметры CLI**: преобразуйте `max_turns`, `model`, `custom_instructions` и т. д. в `claude_args`

### Справочник по несовместимым изменениям

| Старый ввод бета-версии | Новый ввод v1.0                       |
| --------------------- | ------------------------------------- |
| `mode`                | *(Удалено — определяется автоматически)*           |
| `direct_prompt`       | `prompt`                              |
| `override_prompt`     | `prompt` с переменными GitHub        |
| `custom_instructions` | `claude_args: --append-system-prompt` |
| `max_turns`           | `claude_args: --max-turns`            |
| `model`               | `claude_args: --model`                |
| `allowed_tools`       | `claude_args: --allowedTools`         |
| `disallowed_tools`    | `claude_args: --disallowedTools`      |
| `claude_env`          | Формат JSON `settings`                |

### Пример «до и после»

**Бета-версия:**

```yaml
- uses: anthropics/claude-code-action@beta
  with:
    mode: "tag"
    direct_prompt: "Review this PR for security issues"
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    custom_instructions: "Follow our coding standards"
    max_turns: "10"
    model: "claude-sonnet-5"
```

**Версия GA (v1.0):**

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    prompt: "Review this PR for security issues"
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: |
      --append-system-prompt "Follow our coding standards"
      --max-turns 10
      --model claude-sonnet-5
```

> **Совет.** Action теперь автоматически определяет, запускаться ли в интерактивном режиме (отвечает на упоминания `@claude`) или в режиме автоматизации (запускается немедленно с промптом) на основе вашей конфигурации.

## Примеры сценариев использования

Claude Code GitHub Actions может помочь вам с самыми разными задачами. [Каталог examples](https://github.com/anthropics/claude-code-action/tree/main/examples) содержит готовые к использованию рабочие процессы для различных сценариев.

### Базовый рабочий процесс

```yaml
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Responds to @claude mentions in comments
```

### Использование навыков

Входной параметр `prompt` принимает как вызов [навыка (skill)](https://code.claude.com/docs/en/skills), так и обычный текст:

* Для навыка в каталоге `.claude/skills/` вашего репозитория запустите `actions/checkout` перед шагом action и передайте `/skill-name`.
* Для навыка, упакованного в плагин, установите плагин с помощью входных параметров `plugin_marketplaces` и `plugins` и передайте навык с пространством имён `/plugin-name:skill-name`.

Следующий рабочий процесс устанавливает плагин `code-review` и запускает его навык для каждого нового или обновлённого pull request:

```yaml
name: Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          plugin_marketplaces: "https://github.com/anthropics/claude-code.git"
          plugins: "code-review@claude-code-plugins"
          prompt: "/code-review:code-review ${{ github.repository }}/pull/${{ github.event.pull_request.number }}"
```

### Собственная автоматизация с промптами

```yaml
name: Daily Report
on:
  schedule:
    - cron: "0 9 * * *"
jobs:
  report:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Generate a summary of yesterday's commits and open issues"
          claude_args: "--model opus"
```

### Распространённые сценарии использования

В комментариях к issue или PR:

```text wrap
@claude implement this feature based on the issue description
@claude how should I implement user authentication for this endpoint?
@claude fix the TypeError in the user dashboard component
```

Claude автоматически проанализирует контекст и ответит соответствующим образом.

## Лучшие практики

### Конфигурация CLAUDE.md

Создайте файл `CLAUDE.md` в корне вашего репозитория, чтобы определить рекомендации по стилю кода, критерии обзора, специфичные для проекта правила и предпочтительные паттерны. Этот файл помогает Claude понимать стандарты вашего проекта.

### Соображения безопасности

> **Предупреждение.** Никогда не коммитьте API-ключи напрямую в ваш репозиторий.

Для всестороннего руководства по безопасности, включая разрешения, аутентификацию и лучшие практики, см. [документацию по безопасности Claude Code Action](https://github.com/anthropics/claude-code-action/blob/main/docs/security.md).

Всегда используйте GitHub Secrets для API-ключей:

* Добавьте ваш API-ключ как секрет репозитория с именем `ANTHROPIC_API_KEY`
* Ссылайтесь на него в рабочих процессах: `anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}`
* Ограничьте разрешения action только тем, что необходимо
* Просматривайте предложения Claude перед слиянием

Всегда используйте GitHub Secrets (например, `${{ secrets.ANTHROPIC_API_KEY }}`), а не жёстко прописывайте API-ключи прямо в файлах ваших рабочих процессов.

### Оптимизация производительности

Используйте шаблоны issue для предоставления контекста, держите ваш `CLAUDE.md` кратким и сфокусированным и настраивайте подходящие тайм-ауты для ваших рабочих процессов.

### Затраты на CI

При использовании Claude Code GitHub Actions учитывайте связанные с этим затраты:

**Затраты GitHub Actions:**

* Claude Code выполняется на раннерах, размещённых в GitHub, которые расходуют ваши минуты GitHub Actions
* Подробную информацию о ценах и лимитах минут см. в [документации GitHub по биллингу](https://docs.github.com/en/billing/managing-billing-for-your-products/managing-billing-for-github-actions/about-billing-for-github-actions)

**Затраты на API:**

* Каждое взаимодействие с Claude расходует токены API в зависимости от длины промптов и ответов
* Использование токенов варьируется в зависимости от сложности задачи и размера кодовой базы
* Актуальные ставки за токены см. на [странице цен Claude](https://claude.com/platform/api)

**Советы по оптимизации затрат:**

* Используйте конкретные команды `@claude`, чтобы уменьшить количество ненужных обращений к API
* Настройте подходящее значение `--max-turns` в `claude_args`, чтобы предотвратить избыточные итерации
* Устанавливайте тайм-ауты на уровне рабочего процесса, чтобы избежать вышедших из-под контроля заданий
* Рассмотрите использование средств управления параллелизмом GitHub для ограничения параллельных запусков

## Примеры конфигурации

Claude Code Action v1 упрощает конфигурацию с помощью унифицированных параметров:

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: "Your instructions here" # Optional
    claude_args: "--max-turns 5" # Optional CLI arguments
```

Ключевые особенности:

* **Унифицированный интерфейс промпта** — используйте `prompt` для всех инструкций
* **Навыки** — вызывайте установленные [навыки](https://code.claude.com/docs/en/skills) прямо из промпта
* **Проброс CLI** — любой аргумент CLI Claude Code через `claude_args`
* **Гибкие триггеры** — работает с любым событием GitHub

Полные файлы рабочих процессов см. в [каталоге examples](https://github.com/anthropics/claude-code-action/tree/main/examples).

> **Совет.** При ответе на комментарии к issue или PR Claude автоматически отвечает на упоминания @claude. Для других событий используйте параметр `prompt`, чтобы предоставить инструкции.

## Использование с Amazon Bedrock и Google Vertex AI

Для корпоративных окружений вы можете использовать Claude Code GitHub Actions со своей собственной облачной инфраструктурой. Такой подход даёт вам контроль над резидентностью данных и биллингом при сохранении той же функциональности.

### Предварительные требования

Перед настройкой Claude Code GitHub Actions с облачными провайдерами вам необходимо:

#### Для Google Cloud Vertex AI:

1. Проект Google Cloud с включённым Vertex AI
2. Настроенная Workload Identity Federation для GitHub Actions
3. Сервисный аккаунт с необходимыми разрешениями
4. GitHub App (рекомендуется) или используйте стандартный GITHUB\_TOKEN

#### Для Amazon Bedrock:

1. Учётная запись AWS с включённым Amazon Bedrock
2. Настроенный поставщик идентификаторов GitHub OIDC в AWS
3. Роль IAM с разрешениями Bedrock
4. GitHub App (рекомендуется) или используйте стандартный GITHUB\_TOKEN

1. **Создайте собственный GitHub App (рекомендуется для сторонних провайдеров)**

   Для лучшего контроля и безопасности при использовании сторонних провайдеров, таких как Vertex AI или Bedrock, мы рекомендуем создать свой собственный GitHub App:

   1. Перейдите на [https://github.com/settings/apps/new](https://github.com/settings/apps/new)
   2. Заполните основную информацию:
      * **GitHub App name**: выберите уникальное имя (например, "YourOrg Claude Assistant")
      * **Homepage URL**: веб-сайт вашей организации или URL репозитория
   3. Настройте параметры app:
      * **Webhooks**: снимите флажок "Active" (не требуется для этой интеграции)
   4. Установите необходимые разрешения:
      * **Repository permissions**:
        * Contents: Read & Write
        * Issues: Read & Write
        * Pull requests: Read & Write
   5. Нажмите "Create GitHub App"
   6. После создания нажмите "Generate a private key" и сохраните загруженный файл `.pem`
   7. Запишите ваш App ID со страницы настроек app
   8. Установите app в ваш репозиторий:
      * На странице настроек вашего app нажмите "Install App" в левой боковой панели
      * Выберите вашу учётную запись или организацию
      * Выберите "Only select repositories" и укажите конкретный репозиторий
      * Нажмите "Install"
   9. Добавьте закрытый ключ как секрет в ваш репозиторий:
      * Перейдите в Settings → Secrets and variables → Actions вашего репозитория
      * Создайте новый секрет с именем `APP_PRIVATE_KEY` с содержимым файла `.pem`
   10. Добавьте App ID как секрет:

   * Создайте новый секрет с именем `APP_ID` с ID вашего GitHub App

   > **Примечание.** Этот app будет использоваться с action [actions/create-github-app-token](https://github.com/actions/create-github-app-token) для генерации токенов аутентификации в ваших рабочих процессах.

   **Альтернатива для Claude API или если вы не хотите настраивать собственный GitHub app**: используйте официальный app от Anthropic:

   1. Установите с: [https://github.com/apps/claude](https://github.com/apps/claude)
   2. Дополнительная конфигурация для аутентификации не требуется

2. **Настройте аутентификацию облачного провайдера**

   Выберите вашего облачного провайдера и настройте безопасную аутентификацию:

   ### Amazon Bedrock

   **Настройте AWS, чтобы разрешить GitHub Actions безопасно аутентифицироваться без хранения учётных данных.**

   > **Примечание по безопасности**: используйте конфигурации, специфичные для репозитория, и предоставляйте только минимально необходимые разрешения.

   **Необходимая настройка**:

   1. **Включите Amazon Bedrock**:
      * Запросите доступ к моделям Claude в Amazon Bedrock
      * Для межрегиональных моделей запросите доступ во всех необходимых регионах

   2. **Настройте поставщик идентификаторов GitHub OIDC**:
      * URL провайдера: `https://token.actions.githubusercontent.com`
      * Audience: `sts.amazonaws.com`

   3. **Создайте роль IAM для GitHub Actions**:
      * Тип доверенной сущности: Web identity
      * Поставщик идентификаторов: `token.actions.githubusercontent.com`
      * Разрешения: политика `AmazonBedrockFullAccess`
      * Настройте политику доверия для вашего конкретного репозитория

   **Необходимые значения**:

   После настройки вам понадобятся:

   * **AWS\_ROLE\_TO\_ASSUME**: ARN созданной вами роли IAM

   > **Совет.** OIDC более безопасен, чем использование статических ключей доступа AWS, поскольку учётные данные являются временными и автоматически ротируются.

   Подробные инструкции по настройке OIDC см. в [документации AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html).

   ### Google Vertex AI

   **Настройте Google Cloud, чтобы разрешить GitHub Actions безопасно аутентифицироваться без хранения учётных данных.**

   > **Примечание по безопасности**: используйте конфигурации, специфичные для репозитория, и предоставляйте только минимально необходимые разрешения.

   **Необходимая настройка**:

   1. **Включите API** в вашем проекте Google Cloud:
      * IAM Credentials API
      * Security Token Service (STS) API
      * Vertex AI API

   2. **Создайте ресурсы Workload Identity Federation**:
      * Создайте Workload Identity Pool
      * Добавьте поставщик GitHub OIDC с:
        * Issuer: `https://token.actions.githubusercontent.com`
        * Сопоставлениями атрибутов для репозитория и владельца
        * **Рекомендация по безопасности**: используйте условия атрибутов, специфичные для репозитория

   3. **Создайте сервисный аккаунт**:
      * Предоставьте только роль `Vertex AI User`
      * **Рекомендация по безопасности**: создавайте отдельный сервисный аккаунт для каждого репозитория

   4. **Настройте привязки IAM**:
      * Разрешите Workload Identity Pool имперсонировать сервисный аккаунт
      * **Рекомендация по безопасности**: используйте наборы принципалов, специфичные для репозитория

   **Необходимые значения**:

   После настройки вам понадобятся:

   * **GCP\_WORKLOAD\_IDENTITY\_PROVIDER**: полное имя ресурса провайдера
   * **GCP\_SERVICE\_ACCOUNT**: адрес электронной почты сервисного аккаунта

   > **Совет.** Workload Identity Federation устраняет необходимость в загружаемых ключах сервисного аккаунта, повышая безопасность.

   Подробные инструкции по настройке см. в [документации Google Cloud по Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation).

3. **Добавьте необходимые секреты**

   Добавьте следующие секреты в ваш репозиторий (Settings → Secrets and variables → Actions):

   #### Для Claude API (прямой):

   1. **Для аутентификации API**:
      * `ANTHROPIC_API_KEY`: ваш API-ключ Claude с [console.anthropic.com](https://console.anthropic.com)

   2. **Для GitHub App (если используете собственный app)**:
      * `APP_ID`: ID вашего GitHub App
      * `APP_PRIVATE_KEY`: содержимое закрытого ключа (.pem)

   #### Для Google Cloud Vertex AI

   1. **Для аутентификации GCP**:
      * `GCP_WORKLOAD_IDENTITY_PROVIDER`
      * `GCP_SERVICE_ACCOUNT`

   2. **Для GitHub App (если используете собственный app)**:
      * `APP_ID`: ID вашего GitHub App
      * `APP_PRIVATE_KEY`: содержимое закрытого ключа (.pem)

   #### Для Amazon Bedrock

   1. **Для аутентификации AWS**:
      * `AWS_ROLE_TO_ASSUME`

   2. **Для GitHub App (если используете собственный app)**:
      * `APP_ID`: ID вашего GitHub App
      * `APP_PRIVATE_KEY`: содержимое закрытого ключа (.pem)

4. **Создайте файлы рабочих процессов**

   Создайте файлы рабочих процессов GitHub Actions, которые интегрируются с вашим облачным провайдером. Приведённые ниже примеры показывают полные конфигурации для Amazon Bedrock и Google Vertex AI:

   ### Рабочий процесс Amazon Bedrock

   **Предварительные требования:**

   * Включён доступ к Amazon Bedrock с разрешениями на модель Claude
   * GitHub настроен как поставщик идентификаторов OIDC в AWS
   * Роль IAM с разрешениями Bedrock, которая доверяет GitHub Actions

   **Необходимые секреты GitHub:**

   | Имя секрета          | Описание                                       |
   | -------------------- | ------------------------------------------------- |
   | `AWS_ROLE_TO_ASSUME` | ARN роли IAM для доступа к Bedrock            |
   | `APP_ID`             | ID вашего GitHub App (из настроек app)            |
   | `APP_PRIVATE_KEY`    | Закрытый ключ, который вы сгенерировали для вашего GitHub App |

   ```yaml
   name: Claude PR Action

   permissions:
     contents: write
     pull-requests: write
     issues: write
     id-token: write

   on:
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]
     issues:
       types: [opened, assigned]

   jobs:
     claude-pr:
       if: |
         (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'issues' && contains(github.event.issue.body, '@claude'))
       runs-on: ubuntu-latest
       env:
         AWS_REGION: us-west-2
       steps:
         - name: Checkout repository
           uses: actions/checkout@v4

         - name: Generate GitHub App token
           id: app-token
           uses: actions/create-github-app-token@v2
           with:
             app-id: ${{ secrets.APP_ID }}
             private-key: ${{ secrets.APP_PRIVATE_KEY }}

         - name: Configure AWS Credentials (OIDC)
           uses: aws-actions/configure-aws-credentials@v4
           with:
             role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
             aws-region: us-west-2

         - uses: anthropics/claude-code-action@v1
           with:
             github_token: ${{ steps.app-token.outputs.token }}
             use_bedrock: "true"
             claude_args: '--model us.anthropic.claude-sonnet-4-6 --max-turns 10'
   ```

   > **Совет.** Формат идентификатора модели для Bedrock включает префикс региона (например, `us.anthropic.claude-sonnet-4-6`).

   ### Рабочий процесс Google Vertex AI

   **Предварительные требования:**

   * Включён Vertex AI API в вашем проекте GCP
   * Настроена Workload Identity Federation для GitHub
   * Сервисный аккаунт с разрешениями Vertex AI

   **Необходимые секреты GitHub:**

   | Имя секрета                      | Описание                                       |
   | -------------------------------- | ------------------------------------------------- |
   | `GCP_WORKLOAD_IDENTITY_PROVIDER` | Имя ресурса поставщика workload identity          |
   | `GCP_SERVICE_ACCOUNT`            | Электронная почта сервисного аккаунта с доступом к Vertex AI       |
   | `APP_ID`                         | ID вашего GitHub App (из настроек app)            |
   | `APP_PRIVATE_KEY`                | Закрытый ключ, который вы сгенерировали для вашего GitHub App |

   ```yaml
   name: Claude PR Action

   permissions:
     contents: write
     pull-requests: write
     issues: write
     id-token: write

   on:
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]
     issues:
       types: [opened, assigned]

   jobs:
     claude-pr:
       if: |
         (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'issues' && contains(github.event.issue.body, '@claude'))
       runs-on: ubuntu-latest
       steps:
         - name: Checkout repository
           uses: actions/checkout@v4

         - name: Generate GitHub App token
           id: app-token
           uses: actions/create-github-app-token@v2
           with:
             app-id: ${{ secrets.APP_ID }}
             private-key: ${{ secrets.APP_PRIVATE_KEY }}

         - name: Authenticate to Google Cloud
           id: auth
           uses: google-github-actions/auth@v2
           with:
             workload_identity_provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
             service_account: ${{ secrets.GCP_SERVICE_ACCOUNT }}

         - uses: anthropics/claude-code-action@v1
           with:
             github_token: ${{ steps.app-token.outputs.token }}
             trigger_phrase: "@claude"
             use_vertex: "true"
             claude_args: '--model claude-sonnet-4-5@20250929 --max-turns 10'
           env:
             ANTHROPIC_VERTEX_PROJECT_ID: ${{ steps.auth.outputs.project_id }}
             CLOUD_ML_REGION: us-east5
             VERTEX_REGION_CLAUDE_4_5_SONNET: us-east5
   ```

   > **Совет.** ID проекта автоматически извлекается на шаге аутентификации Google Cloud, поэтому вам не нужно жёстко его прописывать.

## Устранение неполадок

### Claude не отвечает на команды @claude

Убедитесь, что GitHub App установлен правильно, проверьте, что рабочие процессы включены, убедитесь, что API-ключ задан в секретах репозитория, и подтвердите, что комментарий содержит `@claude` (а не `/claude`).

### CI не запускается на коммитах Claude

Убедитесь, что вы используете GitHub App или собственный app (а не пользователя Actions), проверьте, что триггеры рабочего процесса включают необходимые события, и подтвердите, что разрешения app включают триггеры CI.

### Ошибки аутентификации

Подтвердите, что API-ключ действителен и имеет достаточные разрешения. Для Bedrock/Vertex проверьте конфигурацию учётных данных и убедитесь, что секреты названы правильно в рабочих процессах.

## Расширенная конфигурация

### Параметры action

Claude Code Action v1 использует упрощённую конфигурацию:

| Параметр             | Описание                                                        | Обязательный |
| --------------------- | ------------------------------------------------------------------ | -------- |
| `prompt`              | Инструкции для Claude (обычный текст или имя [навыка](https://code.claude.com/docs/en/skills)) | Нет\*     |
| `claude_args`         | Аргументы CLI, передаваемые в Claude Code                                | Нет       |
| `plugin_marketplaces` | Список Git-URL маркетплейсов плагинов, разделённых переносами строк              | Нет       |
| `plugins`             | Список имён плагинов для установки перед выполнением, разделённых переносами строк | Нет       |
| `anthropic_api_key`   | API-ключ Claude                                                     | Да\*\*  |
| `github_token`        | Токен GitHub для доступа к API                                        | Нет       |
| `trigger_phrase`      | Пользовательская триггерная фраза (по умолчанию: "@claude")                         | Нет       |
| `use_bedrock`         | Использовать Amazon Bedrock вместо Claude API                           | Нет       |
| `use_vertex`          | Использовать Google Vertex AI вместо Claude API                          | Нет       |

\*Промпт необязателен — при пропуске для комментариев к issue/PR Claude отвечает на триггерную фразу\
\*\*Обязателен для прямого Claude API, не для Bedrock/Vertex

#### Передача аргументов CLI

Параметр `claude_args` принимает любые аргументы CLI Claude Code:

```yaml
claude_args: "--max-turns 5 --model claude-sonnet-5 --mcp-config /path/to/config.json"
```

Распространённые аргументы:

* `--max-turns`: максимальное количество ходов беседы (по умолчанию: 10)
* `--model`: используемая модель (например, `claude-sonnet-5`)
* `--mcp-config`: путь к конфигурации MCP
* `--allowedTools`: список разрешённых инструментов, разделённых запятыми. Псевдоним `--allowed-tools` также работает.
* `--debug`: включить отладочный вывод

### Альтернативные методы интеграции

Хотя команда `/install-github-app` является рекомендуемым подходом, вы также можете:

* **Собственный GitHub App**: для организаций, которым нужны фирменные имена пользователей или пользовательские процессы аутентификации. Создайте свой собственный GitHub App с необходимыми разрешениями (contents, issues, pull requests) и используйте action actions/create-github-app-token для генерации токенов в ваших рабочих процессах.
* **Ручная настройка GitHub Actions**: прямая настройка рабочего процесса для максимальной гибкости
* **Конфигурация MCP**: динамическая загрузка серверов Model Context Protocol

Подробные руководства по аутентификации, безопасности и расширенной конфигурации см. в [документации Claude Code Action](https://github.com/anthropics/claude-code-action/blob/main/docs).

### Настройка поведения Claude

Вы можете настроить поведение Claude двумя способами:

1. **CLAUDE.md**: определите стандарты кодирования, критерии обзора и специфичные для проекта правила в файле `CLAUDE.md` в корне вашего репозитория. Claude будет следовать этим рекомендациям при создании PR и ответе на запросы. Более подробную информацию см. в нашей [документации по памяти](https://code.claude.com/docs/en/memory).
2. **Пользовательские промпты**: используйте параметр `prompt` в файле рабочего процесса, чтобы предоставить инструкции, специфичные для рабочего процесса. Это позволяет настраивать поведение Claude для разных рабочих процессов или задач.

Claude будет следовать этим рекомендациям при создании PR и ответе на запросы.

---

> _Перевод официальной документации Claude Code (раздел «Обзор кода и CI/CD»). Источник: https://code.claude.com/docs/en/github-actions. © Anthropic._

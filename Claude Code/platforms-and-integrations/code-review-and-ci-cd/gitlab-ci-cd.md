# GitLab CI/CD

> Узнайте об интеграции Claude Code в ваш процесс разработки с помощью GitLab CI/CD

> **Инфо.** Claude Code для GitLab CI/CD в настоящее время находится в стадии бета-тестирования. Возможности и функциональность могут развиваться по мере того, как мы совершенствуем этот опыт.
>
> Данная интеграция поддерживается GitLab. За поддержкой обращайтесь к следующему [обсуждению (issue) GitLab](https://gitlab.com/gitlab-org/gitlab/-/issues/573776).

> **Примечание.** Эта интеграция построена на основе [Claude Code CLI и Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview), что позволяет программно использовать Claude в ваших задачах CI/CD и пользовательских автоматизированных рабочих процессах.

## Зачем использовать Claude Code с GitLab?

* **Мгновенное создание MR**: опишите, что вам нужно, и Claude предложит полный MR с изменениями и объяснением
* **Автоматизированная реализация**: превращайте задачи (issues) в работающий код одной командой или упоминанием
* **Осведомлённость о проекте**: Claude следует вашим рекомендациям из `CLAUDE.md` и существующим шаблонам кода
* **Простая настройка**: добавьте одну задачу в `.gitlab-ci.yml` и одну замаскированную переменную CI/CD
* **Готовность к корпоративному использованию**: выбирайте Claude API, Amazon Bedrock или Google Vertex AI, чтобы удовлетворить требования к резидентности данных и закупкам
* **Безопасность по умолчанию**: выполняется в ваших GitLab-раннерах с вашими правилами защиты веток и одобрений

## Как это работает

Claude Code использует GitLab CI/CD для выполнения задач ИИ в изолированных заданиях и фиксации результатов обратно через MR:

1. **Оркестрация на основе событий**: GitLab отслеживает выбранные вами триггеры (например, комментарий, упоминающий `@claude` в задаче, MR или ветке обсуждения ревью). Задание собирает контекст из обсуждения и репозитория, строит промпты на основе этих входных данных и запускает Claude Code.

2. **Абстракция провайдера**: используйте провайдер, который подходит вашей среде:
   * Claude API (SaaS)
   * Amazon Bedrock (доступ на основе IAM, варианты для разных регионов)
   * Google Vertex AI (нативный для GCP, Workload Identity Federation)

3. **Выполнение в песочнице**: каждое взаимодействие выполняется в контейнере со строгими правилами для сети и файловой системы. Claude Code применяет разрешения, ограниченные рабочим пространством, чтобы ограничить запись. Каждое изменение проходит через MR, поэтому рецензенты видят различия (diff), и одобрения по-прежнему действуют.

Выбирайте региональные конечные точки, чтобы снизить задержку и удовлетворить требования к суверенитету данных, используя при этом существующие соглашения с облачными провайдерами.

## Что может делать Claude?

Claude Code позволяет создавать мощные рабочие процессы CI/CD, которые преобразуют способ работы с кодом:

* Создание и обновление MR из описаний задач или комментариев
* Анализ регрессий производительности и предложение оптимизаций
* Реализация функций непосредственно в ветке с последующим открытием MR
* Исправление ошибок и регрессий, выявленных тестами или комментариями
* Ответы на дополнительные комментарии для доработки запрошенных изменений

## Настройка

### Быстрая настройка

Самый быстрый способ начать — добавить минимальное задание в ваш `.gitlab-ci.yml` и задать ваш API-ключ в качестве замаскированной переменной.

1. **Добавьте замаскированную переменную CI/CD**
   * Перейдите в **Settings** → **CI/CD** → **Variables**
   * Добавьте `ANTHROPIC_API_KEY` (замаскированную, защищённую по необходимости)

2. **Добавьте задание Claude в `.gitlab-ci.yml`**

```yaml
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  # Adjust rules to fit how you want to trigger the job:
  # - manual runs
  # - merge request events
  # - web/API triggers when a comment contains '@claude'
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
  script:
    # Optional: start a GitLab MCP server if your setup provides one
    - /bin/gitlab-mcp-server || true
    # Use AI_FLOW_* variables when invoking via web/API triggers with context payloads
    - echo "$AI_FLOW_INPUT for $AI_FLOW_CONTEXT on $AI_FLOW_EVENT"
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR and implement the requested changes'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
```

После добавления задания и вашей переменной `ANTHROPIC_API_KEY` протестируйте, запустив задание вручную из **CI/CD** → **Pipelines**, или запустите его из MR, чтобы Claude предложил обновления в ветке и при необходимости открыл MR.

> **Примечание.** Чтобы запускать на Amazon Bedrock или Google Vertex AI вместо Claude API, см. раздел [Использование с Amazon Bedrock и Google Vertex AI](#using-with-amazon-bedrock-%26-google-vertex-ai) ниже для настройки аутентификации и среды.

### Ручная настройка (рекомендуется для продакшена)

Если вы предпочитаете более контролируемую настройку или нуждаетесь в корпоративных провайдерах:

1. **Настройте доступ к провайдеру**:
   * **Claude API**: создайте и сохраните `ANTHROPIC_API_KEY` в качестве замаскированной переменной CI/CD
   * **Amazon Bedrock**: **Configure GitLab** → **AWS OIDC** и создайте IAM-роль для Bedrock
   * **Google Vertex AI**: **Configure Workload Identity Federation for GitLab** → **GCP**

2. **Добавьте учётные данные проекта для операций с GitLab API**:
   * По умолчанию используйте `CI_JOB_TOKEN` или создайте токен доступа к проекту (Project Access Token) с областью действия `api`
   * Сохраните как `GITLAB_ACCESS_TOKEN` (замаскированный), если используете PAT

3. **Добавьте задание Claude в `.gitlab-ci.yml`** (см. примеры ниже)

4. **(Необязательно) Включите триггеры на основе упоминаний**:
   * Добавьте webhook проекта для «Comments (notes)» в ваш прослушиватель событий (если вы его используете)
   * Настройте прослушиватель на вызов API триггера конвейера с переменными, такими как `AI_FLOW_INPUT` и `AI_FLOW_CONTEXT`, когда комментарий содержит `@claude`

## Примеры сценариев использования

### Превращение задач в MR

В комментарии к задаче:

```text
@claude implement this feature based on the issue description
```

Claude анализирует задачу и кодовую базу, вносит изменения в ветку и открывает MR для ревью.

### Получение помощи с реализацией

В обсуждении MR:

```text
@claude suggest a concrete approach to cache the results of this API call
```

Claude предлагает изменения, добавляет код с соответствующим кэшированием и обновляет MR.

### Быстрое исправление ошибок

В комментарии к задаче или MR:

```text
@claude fix the TypeError in the user dashboard component
```

Claude находит ошибку, реализует исправление и обновляет ветку или открывает новый MR.

## Использование с Amazon Bedrock и Google Vertex AI

Для корпоративных сред вы можете запускать Claude Code полностью на вашей облачной инфраструктуре с тем же удобством для разработчиков.

### Amazon Bedrock

#### Предварительные требования

Перед настройкой Claude Code с Amazon Bedrock вам потребуется:

1. Учётная запись AWS с доступом Amazon Bedrock к желаемым моделям Claude
2. GitLab, настроенный как OIDC-провайдер идентификации в AWS IAM
3. IAM-роль с разрешениями Bedrock и политикой доверия, ограниченной вашим проектом/ссылками (refs) GitLab
4. Переменные CI/CD GitLab для принятия роли:
   * `AWS_ROLE_TO_ASSUME` (ARN роли)
   * `AWS_REGION` (регион Bedrock)

#### Инструкции по настройке

Настройте AWS, чтобы разрешить заданиям GitLab CI принимать IAM-роль через OIDC (без статических ключей).

**Необходимая настройка:**

1. Включите Amazon Bedrock и запросите доступ к целевым моделям Claude
2. Создайте IAM OIDC-провайдер для GitLab, если он ещё не существует
3. Создайте IAM-роль, которой доверяет OIDC-провайдер GitLab, ограниченную вашим проектом и защищёнными ссылками (refs)
4. Присоедините разрешения с минимальными привилегиями для API вызова Bedrock

**Необходимые значения для сохранения в переменных CI/CD:**

* `AWS_ROLE_TO_ASSUME`
* `AWS_REGION`

Добавьте переменные в Settings → CI/CD → Variables:

```yaml
# For Amazon Bedrock:
- AWS_ROLE_TO_ASSUME
- AWS_REGION
```

Используйте приведённый выше пример задания Amazon Bedrock, чтобы обменять токен задания GitLab на временные учётные данные AWS во время выполнения.

### Google Vertex AI

#### Предварительные требования

Перед настройкой Claude Code с Google Vertex AI вам потребуется:

1. Проект Google Cloud с:
   * включённым API Vertex AI
   * настроенной Workload Identity Federation для доверия OIDC GitLab
2. Выделенная сервисная учётная запись только с необходимыми ролями Vertex AI
3. Переменные CI/CD GitLab для WIF:
   * `GCP_WORKLOAD_IDENTITY_PROVIDER` (полное имя ресурса)
   * `GCP_SERVICE_ACCOUNT` (email сервисной учётной записи)

#### Инструкции по настройке

Настройте Google Cloud, чтобы разрешить заданиям GitLab CI имперсонировать сервисную учётную запись через Workload Identity Federation.

**Необходимая настройка:**

1. Включите IAM Credentials API, STS API и Vertex AI API
2. Создайте Workload Identity Pool и провайдер для OIDC GitLab
3. Создайте выделенную сервисную учётную запись с ролями Vertex AI
4. Предоставьте принципалу WIF разрешение на имперсонацию сервисной учётной записи

**Необходимые значения для сохранения в переменных CI/CD:**

* `GCP_WORKLOAD_IDENTITY_PROVIDER`
* `GCP_SERVICE_ACCOUNT`

Добавьте переменные в Settings → CI/CD → Variables:

```yaml
# For Google Vertex AI:
- GCP_WORKLOAD_IDENTITY_PROVIDER
- GCP_SERVICE_ACCOUNT
- CLOUD_ML_REGION (for example, us-east5)
```

Используйте приведённый выше пример задания Google Vertex AI для аутентификации без хранения ключей.

## Примеры конфигурации

Ниже приведены готовые к использованию фрагменты, которые вы можете адаптировать под свой конвейер.

### Базовый .gitlab-ci.yml (Claude API)

```yaml
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Summarize recent changes and suggest improvements'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  # Claude Code will use ANTHROPIC_API_KEY from CI/CD variables
```

### Пример задания Amazon Bedrock (OIDC)

**Предварительные требования:**

* Amazon Bedrock включён с доступом к выбранным вами моделям Claude
* GitLab OIDC настроен в AWS с ролью, которая доверяет вашему проекту и ссылкам (refs) GitLab
* IAM-роль с разрешениями Bedrock (рекомендуются минимальные привилегии)

**Необходимые переменные CI/CD:**

* `AWS_ROLE_TO_ASSUME`: ARN IAM-роли для доступа к Bedrock
* `AWS_REGION`: регион Bedrock (например, `us-west-2`)

```yaml
claude-bedrock:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  before_script:
    - apk add --no-cache bash curl jq git python3 py3-pip
    - pip install --no-cache-dir awscli
    - curl -fsSL https://claude.ai/install.sh | bash
    # Exchange GitLab OIDC token for AWS credentials
    - export AWS_WEB_IDENTITY_TOKEN_FILE="${CI_JOB_JWT_FILE:-/tmp/oidc_token}"
    - if [ -n "${CI_JOB_JWT_V2}" ]; then printf "%s" "$CI_JOB_JWT_V2" > "$AWS_WEB_IDENTITY_TOKEN_FILE"; fi
    - >
      aws sts assume-role-with-web-identity
      --role-arn "$AWS_ROLE_TO_ASSUME"
      --role-session-name "gitlab-claude-$(date +%s)"
      --web-identity-token "file://$AWS_WEB_IDENTITY_TOKEN_FILE"
      --duration-seconds 3600 > /tmp/aws_creds.json
    - export AWS_ACCESS_KEY_ID="$(jq -r .Credentials.AccessKeyId /tmp/aws_creds.json)"
    - export AWS_SECRET_ACCESS_KEY="$(jq -r .Credentials.SecretAccessKey /tmp/aws_creds.json)"
    - export AWS_SESSION_TOKEN="$(jq -r .Credentials.SessionToken /tmp/aws_creds.json)"
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Implement the requested changes and open an MR'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    AWS_REGION: "us-west-2"
```

> **Примечание.** Идентификаторы моделей для Bedrock включают региональные префиксы (например, `us.anthropic.claude-sonnet-4-6`). Передавайте нужную модель через конфигурацию задания или промпт, если ваш рабочий процесс это поддерживает.

### Пример задания Google Vertex AI (Workload Identity Federation)

**Предварительные требования:**

* API Vertex AI включён в вашем проекте GCP
* Workload Identity Federation настроена для доверия OIDC GitLab
* Сервисная учётная запись с разрешениями Vertex AI

**Необходимые переменные CI/CD:**

* `GCP_WORKLOAD_IDENTITY_PROVIDER`: полное имя ресурса провайдера
* `GCP_SERVICE_ACCOUNT`: email сервисной учётной записи
* `CLOUD_ML_REGION`: регион Vertex (например, `us-east5`)

```yaml
claude-vertex:
  stage: ai
  image: gcr.io/google.com/cloudsdktool/google-cloud-cli:slim
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  before_script:
    - apt-get update && apt-get install -y git && apt-get clean
    - curl -fsSL https://claude.ai/install.sh | bash
    # Authenticate to Google Cloud via WIF (no downloaded keys)
    - >
      gcloud auth login --cred-file=<(cat <<EOF
      {
        "type": "external_account",
        "audience": "${GCP_WORKLOAD_IDENTITY_PROVIDER}",
        "subject_token_type": "urn:ietf:params:oauth:token-type:jwt",
        "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/${GCP_SERVICE_ACCOUNT}:generateAccessToken",
        "token_url": "https://sts.googleapis.com/v1/token"
      }
      EOF
      )
    - gcloud config set project "$(gcloud projects list --format='value(projectId)' --filter="name:${CI_PROJECT_NAMESPACE}" | head -n1)" || true
  script:
    - /bin/gitlab-mcp-server || true
    - >
      CLOUD_ML_REGION="${CLOUD_ML_REGION:-us-east5}"
      claude
      -p "${AI_FLOW_INPUT:-'Review and update code as requested'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    CLOUD_ML_REGION: "us-east5"
```

> **Примечание.** При использовании Workload Identity Federation вам не нужно хранить ключи сервисной учётной записи. Используйте условия доверия, специфичные для репозитория, и сервисные учётные записи с минимальными привилегиями.

## Лучшие практики

### Конфигурация CLAUDE.md

Создайте файл `CLAUDE.md` в корне репозитория, чтобы определить стандарты кодирования, критерии ревью и правила, специфичные для проекта. Claude читает этот файл во время выполнения и следует вашим соглашениям при предложении изменений.

### Соображения безопасности

**Никогда не фиксируйте API-ключи или облачные учётные данные в вашем репозитории**. Всегда используйте переменные CI/CD GitLab:

* Добавьте `ANTHROPIC_API_KEY` в качестве замаскированной переменной (и защитите её при необходимости)
* По возможности используйте OIDC, специфичный для провайдера (без долгоживущих ключей)
* Ограничивайте разрешения заданий и исходящий сетевой трафик
* Проверяйте MR от Claude, как и от любого другого участника

### Оптимизация производительности

* Держите `CLAUDE.md` сфокусированным и лаконичным
* Предоставляйте чёткие описания задач/MR, чтобы сократить количество итераций
* Настраивайте разумные тайм-ауты заданий, чтобы избежать неконтролируемых запусков
* По возможности кэшируйте установки npm и пакетов в раннерах

### Затраты на CI

При использовании Claude Code с GitLab CI/CD учитывайте связанные с этим затраты:

* **Время GitLab Runner**:
  * Claude выполняется на ваших GitLab-раннерах и потребляет вычислительные минуты
  * Подробности см. в биллинге раннеров вашего тарифного плана GitLab

* **Затраты на API**:
  * Каждое взаимодействие с Claude потребляет токены в зависимости от размера промпта и ответа
  * Использование токенов варьируется в зависимости от сложности задачи и размера кодовой базы
  * Подробности см. в [ценах Anthropic](https://platform.claude.com/docs/en/about-claude/pricing)

* **Советы по оптимизации затрат**:
  * Используйте конкретные команды `@claude`, чтобы сократить количество ненужных итераций
  * Устанавливайте подходящие значения `max_turns` и тайм-аута задания
  * Ограничивайте параллелизм для контроля одновременных запусков

## Безопасность и управление

* Каждое задание выполняется в изолированном контейнере с ограниченным доступом к сети
* Изменения Claude проходят через MR, поэтому рецензенты видят каждое различие (diff)
* Правила защиты веток и одобрения применяются к коду, сгенерированному ИИ
* Claude Code использует разрешения, ограниченные рабочим пространством, чтобы ограничить запись
* Затраты остаются под вашим контролем, поскольку вы используете собственные учётные данные провайдера

## Устранение неполадок

### Claude не отвечает на команды @claude

* Убедитесь, что ваш конвейер запускается (вручную, по событию MR или через прослушиватель/webhook событий заметок)
* Убедитесь, что переменные CI/CD (`ANTHROPIC_API_KEY` или настройки облачного провайдера) присутствуют и не замаскированы
* Проверьте, что комментарий содержит `@claude` (а не `/claude`) и что ваш триггер упоминания настроен

### Задание не может писать комментарии или открывать MR

* Убедитесь, что `CI_JOB_TOKEN` имеет достаточные разрешения для проекта, или используйте токен доступа к проекту (Project Access Token) с областью действия `api`
* Проверьте, что инструмент `mcp__gitlab` включён в `--allowedTools`
* Убедитесь, что задание выполняется в контексте MR или имеет достаточный контекст через переменные `AI_FLOW_*`

### Ошибки аутентификации

* **Для Claude API**: подтвердите, что `ANTHROPIC_API_KEY` действителен и не истёк
* **Для Bedrock/Vertex**: проверьте конфигурацию OIDC/WIF, имперсонацию роли и имена секретов; подтвердите регион и доступность модели

## Расширенная конфигурация

### Часто используемые параметры и переменные

Claude Code поддерживает эти часто используемые входные данные:

* `prompt` / `prompt_file`: предоставьте инструкции встроенно (`-p`) или через файл
* `max_turns`: ограничьте количество итераций обмена сообщениями
* `timeout_minutes`: ограничьте общее время выполнения
* `ANTHROPIC_API_KEY`: требуется для Claude API (не используется для Bedrock/Vertex)
* Среда, специфичная для провайдера: `AWS_REGION`, переменные проекта/региона для Vertex

> **Примечание.** Точные флаги и параметры могут различаться в зависимости от версии `@anthropic-ai/claude-code`. Запустите `claude --help` в вашем задании, чтобы увидеть поддерживаемые параметры.

### Настройка поведения Claude

Вы можете направлять Claude двумя основными способами:

1. **CLAUDE.md**: определите стандарты кодирования, требования к безопасности и соглашения проекта. Claude читает этот файл во время выполнения и следует вашим правилам.
2. **Пользовательские промпты**: передавайте инструкции для конкретных задач через `prompt`/`prompt_file` в задании. Используйте разные промпты для разных заданий (например, ревью, реализация, рефакторинг).

---

> _Перевод официальной документации Claude Code (раздел «Обзор кода и CI/CD»). Источник: https://code.claude.com/docs/en/gitlab-ci-cd. © Anthropic._

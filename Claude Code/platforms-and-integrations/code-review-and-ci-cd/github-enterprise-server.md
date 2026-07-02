# GitHub Enterprise Server

> Подключите Claude Code к своему self-hosted-экземпляру GitHub Enterprise Server для веб-сессий, обзора кода и маркетплейсов плагинов.

> **Примечание.** Поддержка GitHub Enterprise Server доступна для планов Team и Enterprise.

Поддержка GitHub Enterprise Server (GHES) позволяет вашей организации использовать Claude Code с репозиториями, размещёнными на вашем самоуправляемом экземпляре GitHub, вместо github.com. После того как Owner подключит ваш экземпляр GHES, разработчики смогут запускать веб-сессии, получать автоматические обзоры кода и устанавливать плагины из внутренних маркетплейсов без какой-либо настройки для каждого репозитория.

Для репозиториев на github.com см. [Claude Code в вебе](https://code.claude.com/docs/en/claude-code-on-the-web) и [Обзор кода](https://code.claude.com/docs/en/code-review). Чтобы запускать Claude в собственной CI-инфраструктуре, см. [GitHub Actions](https://code.claude.com/docs/en/github-actions).

## Что работает с GitHub Enterprise Server

В таблице ниже показано, какие функции Claude Code поддерживают GHES, и любые отличия от поведения github.com.

| Функция                | Поддержка GHES    | Примечания                                                                                                                           |
| :--------------------- | :-------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| Claude Code в вебе | ✅ Поддерживается     | Owner подключает экземпляр GHES один раз; разработчики используют `claude --remote` или [claude.ai/code](https://claude.ai/code) как обычно |
| Обзор кода            | ✅ Поддерживается     | Те же автоматические обзоры PR, что и на github.com                                                                                         |
| Claude Security        | ✅ Поддерживается     | Доступно в публичной бете для планов Enterprise на [claude.ai/security](https://claude.ai/security)                               |
| Сессии Teleport      | ✅ Поддерживается     | Перемещайте сессии между вебом и терминалом с помощью `--teleport`                                                                        |
| Маркетплейсы плагинов    | ✅ Поддерживается     | Используйте полные git-URL вместо сокращения `owner/repo`                                                                                 |
| Метрики вклада   | ✅ Поддерживается     | Доставляются через вебхуки на [панель аналитики](https://code.claude.com/docs/en/analytics)                                                              |
| GitHub Actions         | ✅ Поддерживается     | Требуется ручная настройка рабочего процесса; `/install-github-app` работает только с github.com                                                        |
| GitHub MCP server      | ❌ Не поддерживается | GitHub MCP server не работает с экземплярами GHES                                                                 |

## Настройка администратором

Owner подключает ваш экземпляр GHES к Claude Code один раз. После этого разработчики в вашей организации могут использовать репозитории GHES без какой-либо дополнительной настройки. Вам нужна роль Owner или Primary Owner в вашей организации Claude и разрешение на создание GitHub Apps в вашем экземпляре GHES.

Пошаговая настройка генерирует манифест GitHub App и перенаправляет вас на ваш экземпляр GHES для создания приложения в один клик. Если ваше окружение блокирует поток перенаправлений, доступна [альтернативная ручная настройка](#manual-setup).

1. **Откройте настройки администратора Claude Code**

   Перейдите на [claude.ai/admin-settings/claude-code](https://claude.ai/admin-settings/claude-code) и найдите раздел GitHub Enterprise Server.

2. **Начните пошаговую настройку**

   Нажмите **Connect**. Введите отображаемое имя для подключения и имя хоста вашего GHES, например `github.example.com`. Если ваш экземпляр GHES использует самоподписанный или частный удостоверяющий центр, вставьте CA-сертификат в необязательное поле.

3. **Создайте GitHub App**

   Нажмите **Continue to GitHub Enterprise**. Ваш браузер перенаправит вас на ваш экземпляр GHES с предзаполненным манифестом приложения. Проверьте конфигурацию и нажмите **Create GitHub App**. GHES перенаправит вас обратно в Claude с автоматически сохранёнными учётными данными приложения.

4. **Установите приложение на свои репозитории**

   На странице GitHub App в вашем экземпляре GHES установите приложение на репозитории или организации, к которым вы хотите дать доступ Claude. Вы можете начать с подмножества и добавить больше позже.

5. **Включите функции**

   Вернитесь на [claude.ai/admin-settings/claude-code](https://claude.ai/admin-settings/claude-code) и включите [Обзор кода](https://code.claude.com/docs/en/code-review#set-up-code-review), Claude Security и [метрики вклада](https://code.claude.com/docs/en/analytics#enable-contribution-metrics) для ваших репозиториев GHES, используя ту же конфигурацию, что и для github.com.

### Разрешения GitHub App

Манифест настраивает GitHub App с разрешениями и событиями вебхуков, которые нужны Claude для веб-сессий, Обзора кода, Claude Security и метрик вклада:

| Разрешение       | Доступ         | Используется для                                    |
| :--------------- | :------------- | :------------------------------------------ |
| Contents         | Чтение и запись | Клонирование репозиториев и push веток   |
| Pull requests    | Чтение и запись | Создание PR и публикация комментариев обзора    |
| Issues           | Чтение и запись | Ответы на упоминания в issue                |
| Checks           | Чтение и запись | Публикация запусков проверок Обзора кода              |
| Actions          | Чтение          | Чтение статуса CI для автоисправления              |
| Repository hooks | Чтение и запись | Получение вебхуков для метрик вклада |
| Metadata         | Чтение          | Требуется GitHub для всех приложений             |

Приложение подписывается на события `pull_request`, `issue_comment`, `pull_request_review_comment`, `pull_request_review` и `check_run`.

### Ручная настройка

Если пошаговый поток перенаправлений заблокирован вашей сетевой конфигурацией, нажмите **Add manually** вместо Connect. Создайте GitHub App на вашем экземпляре GHES с [разрешениями и событиями, указанными выше](#github-app-permissions), затем введите учётные данные приложения в форму: имя хоста, OAuth client ID и secret, GitHub App ID, client ID, client secret, webhook secret и private key.

### Сетевые требования

Ваш экземпляр GHES должен быть доступен из инфраструктуры Anthropic, чтобы Claude мог клонировать репозитории и публиковать комментарии обзора. Если ваш экземпляр GHES находится за файрволом, добавьте в список разрешённых [IP-адреса API Anthropic](https://platform.claude.com/docs/en/api/ip-addresses).

## Рабочий процесс разработчика

После того как Owner подключил экземпляр GHES, никакой настройки со стороны разработчика не требуется. Claude Code автоматически определяет имя хоста вашего GHES из git-remote в вашей рабочей директории.

Клонируйте репозиторий из вашего экземпляра GHES как обычно:

```bash
git clone git@github.example.com:platform/api-service.git
cd api-service
```

Затем запустите веб-сессию. Claude определяет хост GHES из вашего git-remote и маршрутизирует сессию через настроенный вашей организацией экземпляр:

```bash
claude --remote "Add retry logic to the payment webhook handler"
```

Сессия выполняется на инфраструктуре Anthropic, клонирует ваш репозиторий из GHES и отправляет изменения обратно в ветку. Отслеживайте прогресс с помощью `/tasks` или на [claude.ai/code](https://claude.ai/code). См. [Claude Code в вебе](https://code.claude.com/docs/en/claude-code-on-the-web) для полного рабочего процесса облачных сессий, включая обзор diff, автоисправление и рутины.

### Телепортация сессий в ваш терминал

Перетяните веб-сессию в ваш локальный терминал с помощью `claude --teleport`. Teleport проверяет, что вы находитесь в чекауте того же репозитория GHES, прежде чем получить ветку и загрузить историю сессии. Подробности см. в [требованиях teleport](https://code.claude.com/docs/en/claude-code-on-the-web#teleport-requirements).

## Маркетплейсы плагинов на GHES

Размещайте маркетплейсы плагинов на вашем экземпляре GHES, чтобы распространять внутренние инструменты по всей вашей организации. Структура маркетплейса идентична маркетплейсам, размещённым на github.com; единственное отличие — способ, которым вы на них ссылаетесь.

### Добавление маркетплейса GHES

Сокращение `owner/repo` всегда разрешается в github.com. Для маркетплейсов, размещённых на GHES, используйте полный git-URL:

```bash
/plugin marketplace add git@github.example.com:platform/claude-plugins.git
```

HTTPS-URL тоже работают:

```bash
/plugin marketplace add https://github.example.com/platform/claude-plugins.git
```

См. [Создание и распространение маркетплейса плагинов](https://code.claude.com/docs/en/plugin-marketplaces) для полного руководства по созданию маркетплейсов.

### Разрешение маркетплейсов GHES в управляемых настройках

Если ваша организация использует [управляемые настройки](https://code.claude.com/docs/en/settings) для ограничения того, какие маркетплейсы разработчики могут добавлять, используйте тип источника `hostPattern`, чтобы разрешить все маркетплейсы с вашего экземпляра GHES без перечисления каждого репозитория:

```json
{
  "strictKnownMarketplaces": [
    {
      "source": "hostPattern",
      "hostPattern": "^github\\.example\\.com$"
    }
  ]
}
```

Вы также можете предварительно зарегистрировать маркетплейсы для разработчиков, чтобы они появлялись без ручной настройки. В этом примере маркетплейс внутренних инструментов делается доступным для всей организации:

```json
{
  "extraKnownMarketplaces": {
    "internal-tools": {
      "source": {
        "source": "git",
        "url": "git@github.example.com:platform/claude-plugins.git"
      }
    }
  }
}
```

См. справочник по настройкам [strictKnownMarketplaces](https://code.claude.com/docs/en/settings#strictknownmarketplaces) и [extraKnownMarketplaces](https://code.claude.com/docs/en/settings#extraknownmarketplaces) для полной схемы.

## Ограничения

Несколько функций ведут себя на GHES иначе, чем на github.com. [Таблица функций](#what-works-with-github-enterprise-server) резюмирует поддержку; в этом разделе описаны обходные пути.

* **Команда `/install-github-app`**: вместо неё выполните поток [настройки администратором](#admin-setup) на claude.ai. Если вам также нужны рабочие процессы GitHub Actions на GHES, адаптируйте [пример рабочего процесса](https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml) вручную.
* **GitHub MCP server**: вместо него используйте CLI `gh`, настроенный для вашего хоста GHES. Выполните `gh auth login --hostname github.example.com` для аутентификации, после чего Claude сможет использовать команды `gh` в сессиях.

## Устранение неполадок

### Веб-сессия не может клонировать репозиторий

Если `claude --remote` завершается с ошибкой клонирования, убедитесь, что Owner завершил настройку для вашего экземпляра GHES и что GitHub App установлен на репозитории, в котором вы работаете. Попросите Owner, который подключил экземпляр, подтвердить, что имя хоста, зарегистрированное в настройках Claude, совпадает с именем хоста в вашем git-remote.

### Добавление маркетплейса завершается с ошибкой политики

Если `/plugin marketplace add` заблокирован для вашего URL GHES, ваша организация ограничила источники маркетплейсов. Попросите вашего администратора добавить запись `hostPattern` для имени хоста вашего GHES в [управляемых настройках](#allowlist-ghes-marketplaces-in-managed-settings).

### Экземпляр GHES недоступен

Если обзоры или веб-сессии завершаются по тайм-ауту, ваш экземпляр GHES может быть недоступен из инфраструктуры Anthropic. Убедитесь, что ваш файрвол разрешает входящие подключения с [IP-адресов API Anthropic](https://platform.claude.com/docs/en/api/ip-addresses).

## Связанные ресурсы

Эти страницы более подробно раскрывают функции, упомянутые в этом руководстве:

* [Claude Code в вебе](https://code.claude.com/docs/en/claude-code-on-the-web): запуск сессий Claude Code на облачной инфраструктуре
* [Обзор кода](https://code.claude.com/docs/en/code-review): автоматические обзоры PR
* [Маркетплейсы плагинов](https://code.claude.com/docs/en/plugin-marketplaces): создание и распространение каталогов плагинов
* [Аналитика](https://code.claude.com/docs/en/analytics): отслеживание использования и метрик вклада
* [Управляемые настройки](https://code.claude.com/docs/en/settings): конфигурация политик для всей организации
* [Сетевая конфигурация](https://code.claude.com/docs/en/network-config): требования к файрволу и списку разрешённых IP

---

> _Перевод официальной документации Claude Code (раздел «Обзор кода и CI/CD»). Источник: https://code.claude.com/docs/en/github-enterprise-server. © Anthropic._

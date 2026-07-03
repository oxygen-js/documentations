# Настройка корпоративной сети

## Обзор

Корпоративные среды часто требуют, чтобы приложения работали за корпоративными прокси и использовали пользовательские центры сертификации (CA) для проверки SSL/TLS. Node.js обеспечивает встроенную поддержку этих требований через переменные окружения и флаги командной строки, устраняя во многих случаях необходимость в сторонних библиотеках для работы с прокси.

Это руководство описывает, как настроить приложения Node.js для работы в корпоративных сетевых средах:

  * Настройка прокси через переменную окружения `NODE_USE_ENV_PROXY` или флаг `--use-env-proxy`
  * Добавление центров сертификации из системного хранилища через переменную окружения `NODE_USE_SYSTEM_CA` или флаг `--use-system-ca`.

## Настройка прокси

Во многих корпоративных средах доступ к внешним сервисам в интернете может требовать маршрутизации через HTTP/HTTPS-прокси в целях безопасности и мониторинга. Это требует, чтобы приложения знали об этих прокси и использовали их при выполнении сетевых запросов.

Настройки прокси часто передаются через переменные окружения, такие как `HTTP_PROXY`, `HTTPS_PROXY` и `NO_PROXY`. Node.js поддерживает их, когда включён `NODE_USE_ENV_PROXY` или `--use-env-proxy`. Это работает с методами `node:http` и `node:https` (v22.21.0 или v24.5.0+), а также с `fetch()` (v22.21.0 или v24.0.0+).

Пример (POSIX-совместимые оболочки):
```bash
# Настройки прокси могут быть заданы в системе вашим ИТ-отделом
# и совместно использоваться разными инструментами.
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080
export NO_PROXY=localhost,127.0.0.1,.company.com

# Чтобы включить это для приложений Node.js.
export NODE_USE_ENV_PROXY=1
node app.js
```

Как вариант, включите это через флаг командной строки `--use-env-proxy` в Node.js v22.21.0 или v24.5.0 и выше:
```bash
# Настройки прокси могут быть заданы в системе вашим ИТ-отделом
# и совместно использоваться разными инструментами.
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080
export NO_PROXY=localhost,127.0.0.1,.company.com

# Чтобы включить это для приложений Node.js.
node --use-env-proxy app.js
```

Или, если `--env-file` используется для загрузки переменных окружения из файла:
```text
# В файле .env
HTTP_PROXY=http://proxy.company.com:8080
HTTPS_PROXY=http://proxy.company.com:8080
NO_PROXY=localhost,127.0.0.1,.company.com
NODE_USE_ENV_PROXY=1
```

После включения запросы `http`, `https` и `fetch()` по умолчанию используют настроенные прокси, если только агент не переопределён или цель не совпадает с `NO_PROXY`.

### Настройка прокси программно

Чтобы настроить прокси программно, переопределите агенты. В настоящее время это поддерживается `https.request()` и другими методами, построенными на его основе, такими как `https.get()`.

Чтобы переопределить агент для отдельного запроса, используйте опцию `agent` для `http.request()`/`https.request()` и подобных методов:
```js
const https = require('node:https');

// Создание пользовательского агента с поддержкой пользовательского прокси.
const agent = new https.Agent({
  proxyEnv: { HTTPS_PROXY: 'http://proxy.company.com:8080' },
});

https.request(
  {
    hostname: 'www.external.com',
    port: 443,
    path: '/',
    agent,
  },
  res => {
    // Этот запрос будет проксирован через proxy.company.com:8080 по протоколу HTTP.
  }
);
```

Чтобы переопределить агент глобально, переприсвойте `http.globalAgent` и `https.globalAgent`:

**Примечание**: глобальные агенты не влияют на `fetch()`.
```js
const http = require('node:http');
const https = require('node:https');

http.globalAgent = new http.Agent({
  proxyEnv: { HTTP_PROXY: 'http://proxy.company.com:8080' },
});
https.globalAgent = new https.Agent({
  proxyEnv: { HTTPS_PROXY: 'http://proxy.company.com:8080' },
});

// Все последующие запросы будут использовать настроенные прокси, если только они не переопределяют опцию agent.
http.request('http://external.com', res => {
  /* ... */
});
https.request('https://external.com', res => {
  /* ... */
});
```

### Использование прокси с аутентификацией

Если прокси требует аутентификации, включите учётные данные в URL прокси:
```
export HTTPS_PROXY=http://username:password@proxy.company.com:8080
```

**Примечание по безопасности**: избегайте фиксации учётных данных в env-файлах. Отдавайте предпочтение менеджеру секретов и программной настройке.

### Настройка обхода прокси

Переменная `NO_PROXY` поддерживает:

  * `*` \- обход прокси для всех хостов
  * `company.com` \- точное совпадение имени хоста
  * `.company.com` \- совпадение по суффиксу домена (совпадает с `sub.company.com`)
  * `*.company.com` \- совпадение домена по шаблону
  * `192.168.1.100` \- точное совпадение IP-адреса
  * `192.168.1.1-192.168.1.100` \- диапазон IP-адресов
  * `company.com:8080` \- имя хоста с конкретным портом

Если цель совпадает с `NO_PROXY`, запрос обходит прокси.

## Настройка центров сертификации

По умолчанию Node.js использует поставляемые в комплекте с Mozilla корневые CA и не обращается к хранилищу ОС. Во многих корпоративных средах внутренние CA устанавливаются в хранилище ОС и должны считаться доверенными при подключении к внутренним сервисам; подключения к сертификатам, подписанным этими CA, могут не проходить проверку с ошибками, такими как:
```
Error: self signed certificate in certificate chain
```

Начиная с Node.js v22.19.0, v24.6.0 и выше, Node.js можно настроить на доверие этим пользовательским CA с использованием системного хранилища сертификатов.

### Добавление сертификатов CA из системного хранилища

  * Из переменной окружения: `NODE_USE_SYSTEM_CA=1 node app.js`
  * Из флага командной строки: `node --use-system-ca app.js`

Когда включено, Node.js загружает системные CA и использует их в дополнение к поставляемым в комплекте CA для проверки TLS.

Node.js читает сертификаты из разных мест в зависимости от платформы:

  * Windows: хранилище сертификатов Windows (через Windows Crypto API)
  * macOS: связка ключей macOS (Keychain)
  * Linux: значения по умолчанию OpenSSL, обычно через `SSL_CERT_FILE`/`SSL_CERT_DIR` или пути вроде `/etc/ssl/cert.pem` и `/etc/ssl/certs/` в зависимости от сборки OpenSSL

Node.js следует политике, схожей с политикой Chromium. Подробнее см. в [документации Node.js](https://nodejs.org/api/cli.html#--use-system-ca).

### Добавление дополнительных сертификатов CA

Чтобы добавить конкретные сертификаты CA, не полагаясь на системное хранилище:
```bash
export NODE_EXTRA_CA_CERTS=/path/to/company-ca-bundle.pem
node app.js
```

Файл должен содержать один или несколько сертификатов в кодировке PEM.

#### Сочетание опций

Вы можете сочетать `NODE_USE_SYSTEM_CA` с `NODE_EXTRA_CA_CERTS`:
```bash
export NODE_USE_SYSTEM_CA=1
export NODE_EXTRA_CA_CERTS=/path/to/additional-cas.pem
node app.js
```

Когда включены оба, Node.js доверяет поставляемым в комплекте CA, системным CA и дополнительным сертификатам, указанным в `NODE_EXTRA_CA_CERTS`.

### Настройка сертификатов CA программно

#### Настройка глобальных сертификатов CA

Используйте [`tls.getCACertificates()`](https://nodejs.org/api/tls.html#tlsgetcacertificatestype) и [`tls.setDefaultCACertificates()`](https://nodejs.org/api/tls.html#tlssetdefaultcacertificatescerts), чтобы настроить глобальные сертификаты CA. Например, чтобы добавить системные сертификаты в хранилище по умолчанию:
```js
const https = require('node:https');
const tls = require('node:tls');
const currentCerts = tls.getCACertificates('default');
const systemCerts = tls.getCACertificates('system');
tls.setDefaultCACertificates([...currentCerts, ...systemCerts]);

// Последующие запросы используют системные сертификаты при проверке.
https.get('https://internal.company.com', res => {
  /* ... */
});
fetch('https://internal.company.com').then(res => {
  /* ... */
});
```

#### Настройка сертификатов CA для отдельных запросов

Чтобы переопределить сертификаты CA для отдельного запроса, используйте опцию `ca`. В настоящее время это поддерживается только `tls.connect()`/`https.request()` и методами, построенными на их основе, такими как `https.get()`.
```js
const https = require('node:https');
const specialCerts = ['-----BEGIN CERTIFICATE-----\n...'];
https.get(
  {
    hostname: 'internal.company.com',
    port: 443,
    path: '/',
    method: 'GET',
    // Опция `ca` заменяет значения по умолчанию; при необходимости добавьте поставляемые в комплекте сертификаты.
    ca: specialCerts,
  },
  res => {
    /* ... */
  }
);
```

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/http/enterprise-network-configuration. Оригинал распространяется по лицензии MIT._

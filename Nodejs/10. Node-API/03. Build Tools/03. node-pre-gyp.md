# node-pre-gyp

Одно из ограничений нативных аддонов заключается в том, что их необходимо компилировать под каждую целевую платформу и архитектуру. Без предварительно собранных бинарных файлов каждый пользователь, устанавливающий ваш пакет, должен иметь на своей машине работающий инструментарий C/C++.

[node-pre-gyp](https://github.com/mapbox/node-pre-gyp) решает эту проблему, позволяя вам собирать бинарные файлы заранее, загружать их в удалённое хранилище и давать пользователям возможность загружать нужный бинарный файл во время установки, прибегая к компиляции из исходников только в том случае, если подходящий бинарный файл недоступен.

> Примечание: поддержка Node-API была добавлена в node-pre-gyp в версии 0.8.0.

На этой странице описаны изменения, необходимые для аддона на Node-API, чтобы обеспечить поддержку node-pre-gyp.

## Amazon S3

По умолчанию node-pre-gyp загружает бинарные файлы в [Amazon S3](https://aws.amazon.com/s3/).

> Модуль [node-pre-gyp-github](https://github.com/bchr02/node-pre-gyp-github) добавляет поддержку публикации в GitHub Releases вместо этого.

### Требования Amazon S3

Перед загрузкой вам понадобятся:

  1. Учётная запись Amazon Web Services.
  2. Пользователь или роль IAM с разрешением на загрузку в S3.
  3. [Бакет S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingBucket.html) для размещения бинарных файлов.

### Учётные данные AWS

Никогда не храните учётные данные в вашем репозитории. node-pre-gyp поддерживает два распространённых подхода к предоставлению учётных данных во время разработки:

  1. Файл `~/.node_pre_gyprc`:
```     {
      "accessKeyId": "xxx",
      "secretAccessKey": "xxx"
    }
```

JSON

  2. Переменные окружения:
```     export node_pre_gyp_accessKeyId=xxx
    export node_pre_gyp_secretAccessKey=xxx
```

Shell

Для CI-окружений предпочтительнее использовать роли IAM или короткоживущие учётные данные, а не долгоживущие ключи доступа. Дополнительные варианты смотрите в [документации по учётным данным node-pre-gyp](https://github.com/mapbox/node-pre-gyp#3-configure-aws-credentials).

## package.json

### Свойства `dependencies` и `devDependencies`

Теперь пакет публикуется под областью видимости `@mapbox`. Используйте `@aws-sdk/client-s3` как dev-зависимость для шага загрузки.
```json
"dependencies": {
  "@mapbox/node-pre-gyp": "^1.0.0"
},
"devDependencies": {
  "@aws-sdk/client-s3": "^3.0.0"
}
```

### Свойство `scripts`

Скрипт `install` должен вызывать node-pre-gyp с флагом `--fallback-to-build`, чтобы пользователи, у которых нет доступного предварительно собранного бинарного файла, всё же могли скомпилировать его локально:
```json
"scripts": {
  "install": "node-pre-gyp install --fallback-to-build"
}
```

### Свойство `binary`

Свойство `binary` сообщает node-pre-gyp, какие версии Node-API поддерживает ваш аддон и где найти/загрузить бинарные файлы:
```json
"binary": {
  "module_name": "your_module",
  "module_path": "./lib/binding/napi-v{napi_build_version}",
  "remote_path": "./{module_name}/v{version}/{configuration}/",
  "package_name": "{platform}-{arch}-napi-v{napi_build_version}.tar.gz",
  "host": "https://your_bucket.s3.us-west-1.amazonaws.com",
  "napi_versions": [3]
}
```

Задайте для `module_name` допустимый идентификатор C. Массив `napi_versions` перечисляет, под какие версии Node-API выполнять сборку; `3` — разумный минимум для большинства аддонов.

Полный справочник, включая [соображения по Node-API](https://github.com/mapbox/node-pre-gyp#n-api-considerations), смотрите в [документации node-pre-gyp](https://github.com/mapbox/node-pre-gyp#1-add-new-entries-to-your-packagejson).

## binding.gyp

### Новая цель

Добавьте цель, выполняемую после сборки (post-build), чтобы скопировать скомпилированный бинарный файл по пути, указанному в `module_path`:
```json
{
  "target_name": "action_after_build",
  "type": "none",
  "dependencies": ["<(module_name)"],
  "copies": [
    {
      "files": ["<(PRODUCT_DIR)/<(module_name).node"],
      "destination": "<(module_path)"
    }
  ]
}
```

### NAPI_VERSION

Включите версию Node-API в `defines` первой цели, чтобы заголовочные файлы настраивались корректно:
```json
"defines": [
  "NAPI_VERSION=<(napi_build_version)"
]
```

## Обновления JavaScript

JavaScript-код, загружающий нативный бинарный файл, должен динамически разрешать путь к корректному файлу `.node`:
```js
const binary = require('@mapbox/node-pre-gyp');
const path = require('path');
const bindingPath = binary.find(
  path.resolve(path.join(__dirname, './package.json'))
);
const binding = require(bindingPath);
```

## Сборка

Как только всё готово, выполните сборку из исходников:
```
npm install --build-from-source
```

## Упаковка и публикация
```bash
./node_modules/.bin/node-pre-gyp package
./node_modules/.bin/node-pre-gyp publish
```

## CI и автоматизированные сборки

Используйте [GitHub Actions](https://docs.github.com/en/actions) для сборки, тестирования и публикации бинарных файлов под несколько платформ и архитектур. Типичная матрица рабочего процесса покрывает `ubuntu-latest`, `macos-latest` и `windows-latest`, плюс любые нужные вам варианты архитектур (например, `x64`, `arm64`). Примеры конфигураций рабочих процессов смотрите в [репозитории node-pre-gyp](https://github.com/mapbox/node-pre-gyp).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: <https://nodejs.org/en/learn/node-api/build-tools/node-pre-gyp>. Оригинал распространяется по лицензии MIT._

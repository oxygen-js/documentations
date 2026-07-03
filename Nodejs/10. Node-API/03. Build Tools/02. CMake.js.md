# CMake.js

[CMake.js](https://github.com/cmake-js/cmake-js) — хорошая альтернатива инструменту сборки [node-gyp](/learn/node-api/build-tools/node-gyp.html). CMake.js основан на инструменте [CMake](https://cmake.org), который должен быть установлен.

### Плюсы

  * Использует инструмент CMake, широко распространённый в сообществе открытого исходного кода.
  * Идеально подходит для существующих библиотек C/C++, уже основанных на CMake.

### Минусы

  * Не получил широкого распространения в сообществе Node.

## Установка

Для работы CMake.js требуется, чтобы CMake был уже установлен. Установщики доступны на [сайте CMake](https://cmake.org).

> Разработчикам на macOS может быть удобнее установить CMake с помощью [Homebrew](https://brew.sh). Если Homebrew установлен, CMake можно установить командой `brew install cmake`.

Проверить установку CMake можно командой:
```
cmake --version
```

Как разработчику нативных модулей Node вам может быть удобно установить CMake.js в качестве глобального инструмента командной строки:
```
npm install cmake-js -g
```

Проверить установку CMake.js можно командой:
```
cmake-js --version
```

## package.json

В вашем файле `package.json` должно быть несколько записей, чтобы ваш нативный модуль работал с CMake.js.

Поскольку ваш нативный модуль нужно компилировать с помощью CMake.js при установке, свойство `scripts` вашего файла `package.json` должно содержать запись `install`, обеспечивающую это:
```json
"scripts": {
  "install": "cmake-js compile"
}
```

Маловероятно, что у пользователей вашего нативного модуля CMake.js будет установлен как глобальный инструмент командной строки. Поэтому вашему проекту нужно объявить CMake.js в качестве зависимости для разработки. Это можно сделать, выполнив команду:
```
npm install cmake-js --save-dev
```

Другой вариант — вручную добавить зависимость для разработки в файл `package.json`:
```json
"devDependencies": {
  "cmake-js": "^6.0.0"
}
```

Пример такого подхода [доступен здесь](https://github.com/nodejs/node-addon-examples/blob/main/src/8-tooling/build_with_cmake/node-addon-api/package.json).

## CMakeLists.txt

Нативные модули, собираемые на CMake.js, имеют файл `CMakeLists.txt`, который описывает, как должен собираться модуль. Этот файл выполняет ту же роль, что и `binding.gyp` для проектов, использующих `node-gyp`.

Помимо записей, необходимых для любой сборки CMake, при сборке нативных модулей требуются дополнительные записи.

### CMake.js

Вот строки, необходимые для всех нативных модулей, собираемых с помощью CMake.js:
```cpp
project(node-api-cmake-build-example)
include_directories(${CMAKE_JS_INC})
file(GLOB SOURCE_FILES "hello.cc")
add_library(${PROJECT_NAME} SHARED ${SOURCE_FILES} ${CMAKE_JS_SRC})
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")
target_link_libraries(${PROJECT_NAME} ${CMAKE_JS_LIB})
```

### NAPI_VERSION

При сборке нативного модуля на основе Node-API важно объявить минимальную версию Node-API, для работы с которой предназначен ваш модуль. Для CMake.js это делается добавлением в файл `CMakeLists.txt` строки вроде такой:
```cpp
# определение NAPI_VERSION
add_definitions(-DNAPI_VERSION=3)
```

> При отсутствии иных требований версия Node-API 3 — хороший выбор, поскольку именно эта версия Node-API была актуальной, когда Node-API покинул статус экспериментального.

### node-addon-api

Для модулей Node-API на основе `node-addon-api` требуются дополнительные значения конфигурации.

`node-addon-api` требует C++11. Эти строки конфигурации в начале файла `CMakeLists.txt` задают это требование:
```cpp
cmake_minimum_required(VERSION 3.9)
cmake_policy(SET CMP0042 NEW)
set (CMAKE_CXX_STANDARD 11)
```

Модули на основе `node-addon-api` включают дополнительные заголовочные файлы, которые не входят в состав самого Node. Эти строки указывают CMake.js, где найти эти файлы:
```cpp
# Подключение обёрток Node-API
execute_process(COMMAND node -p "require('node-addon-api').include"
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE NODE_ADDON_API_DIR
        )
string(REPLACE "\n" "" NODE_ADDON_API_DIR ${NODE_ADDON_API_DIR})
string(REPLACE "\"" "" NODE_ADDON_API_DIR ${NODE_ADDON_API_DIR})
target_include_directories(${PROJECT_NAME} PRIVATE ${NODE_ADDON_API_DIR})
```

Пример такого подхода [доступен здесь](https://github.com/nodejs/node-addon-examples/blob/main/src/8-tooling/build_with_cmake/node-addon-api/CMakeLists.txt).

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/node-api/build-tools/cmake-js. Оригинал распространяется по лицензии MIT._

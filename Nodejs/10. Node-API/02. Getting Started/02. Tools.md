# Инструменты

## Node.js

[Node.js](https://nodejs.org/) — это среда выполнения, которая исполняет JavaScript на вашей машине. Она объединяет [JavaScript-движок V8](https://developers.google.com/v8/) с набором встроенных модулей, позволяющих запускать JavaScript вне браузера.

Загрузите подходящий установщик для вашей платформы со [страницы загрузок Node.js](https://nodejs.org/en/download/). Релиз **LTS (Long Term Support, долгосрочная поддержка)** — самый стабильный выбор и рекомендуется для разработки аддонов. Установщик включает npm.

> Node-API стабилен во всех поддерживаемых на данный момент релизах Node.js. Подойдёт любой релиз Active LTS или Maintenance LTS.

## npm

[npm](https://www.npmjs.com) — это менеджер пакетов для Node.js. Он устанавливается вместе с Node.js. Для большинства Node-API-разработчиков цель — опубликовать npm-пакет, который оборачивает библиотеку на C/C++ и делает её доступной пользователям JavaScript.

npm поставляется вместе с Node.js. Вы можете поддерживать его в актуальном состоянии с помощью:
```
npm install -g npm@latest
```

## Git

[Git](https://git-scm.com) не является строго обязательным для работы с Node-API, но используется по всей экосистеме. Большинство примеров репозиториев и установок зависимостей полагаются на него.

## Компилятор C/C++ и Python

Помимо Node и npm, вам нужен набор инструментов компилятора C/C++ и Python (требуется для [node-gyp](/learn/node-api/build-tools/node-gyp.html)).

### Windows

Рекомендуемый подход — установить [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022) с выбранной рабочей нагрузкой **«Desktop development with C++»**. Это предоставляет компилятор MSVC, Windows SDK и инфраструктуру сборки, которую требует node-gyp.

В качестве альтернативы вы можете установить его через [winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/):
```
winget install Microsoft.VisualStudio.2022.BuildTools
```

Во время или после установки откройте Visual Studio Installer и убедитесь, что рабочая нагрузка **«Desktop development with C++»** отмечена.

Также требуется Python 3. Установите его с [python.org](https://www.python.org/downloads/windows/) или через winget:
```
winget install Python.Python.3
```

> Запускайте PowerShell или `cmd.exe` от имени **Администратора** при установке глобальных инструментов.

### macOS

Установите инструменты разработчика командной строки от Apple. Если вы ещё не установили Xcode, самый быстрый путь:
```
xcode-select --install
```

Если это не сработает, установите полную [IDE Xcode](https://developer.apple.com/xcode/) из Mac App Store, которая включает необходимый набор инструментов компилятора.

Python 3 **не** поставляется в комплекте с современной macOS. Установите его с помощью [Homebrew](https://brew.sh):
```
brew install python3
```

Или загрузите установщик с [python.org](https://www.python.org/downloads/macos/).

### Linux

В большинстве дистрибутивов Linux необходимый набор инструментов C/C++ и Python либо предустановлены, либо легко добавляются. Для систем на базе Debian/Ubuntu:
```bash
sudo apt-get update
sudo apt-get install -y build-essential python3
```

Для других дистрибутивов обратитесь к документации вашего менеджера пакетов или к [руководству по установке LLVM](https://llvm.org/docs/GettingStarted.html).

## Проверка ваших инструментов

После установки убедитесь, что каждый инструмент находится в вашем PATH.

### macOS и Linux
```bash
node --version
npm --version
python3 --version
git --version
cc --version
make --version
```

### Windows (PowerShell)
```
node --version
npm --version
python --version
git --version
```

PowerShell

Чтобы подтвердить доступность компилятора MSVC, откройте **Developer Command Prompt** (устанавливается вместе с Visual Studio Build Tools) и выполните:
```
cl
```

## Другие инструменты

Вам понадобится командная оболочка — Terminal на macOS/Linux, PowerShell или Windows Terminal на Windows.

Настоятельно рекомендуется хороший редактор кода. [Visual Studio Code](https://code.visualstudio.com) имеет отличную поддержку C/C++ через [расширение C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) и хорошо интегрируется со сборками node-gyp. [CLion](https://www.jetbrains.com/clion/) — ещё один популярный выбор для разработки на C++.

---

> _Перевод официальной документации Node.js (раздел Learn). Источник: https://nodejs.org/en/learn/node-api/getting-started/tools. Оригинал распространяется по лицензии MIT._

# Настольное приложение в Linux

> Установка и обновление настольного приложения Claude в Ubuntu и Debian

> **Примечание.** Поддержка настольного приложения Claude в Linux находится в стадии бета-тестирования. Доступны все вкладки: Chat, Cowork и Code.

Настольное приложение в Linux даёт вам тот же опыт работы с Chat, Cowork и Claude Code, что и в macOS и Windows: параллельные сессии, визуальный просмотр диффов, встроенные терминал и редактор, а также предпросмотр приложения в реальном времени. Полный справочник по функциям см. в разделе [Использование Claude Code Desktop](https://code.claude.com/docs/en/desktop).

## Требования

* Ubuntu 22.04 или новее, либо Debian 12 или новее
* x86\_64 или arm64

Другие дистрибутивы на основе Debian, отвечающие этим требованиям, могут работать, но официально не тестируются.

## Установка

Устанавливайте из apt-репозитория Anthropic, чтобы обновления приходили через обычные обновления пакетов вашей системы.

1. **Добавьте apt-репозиторий Anthropic**

   Скачайте ключ подписи Anthropic:

   ```bash
   sudo curl -fsSLo /usr/share/keyrings/claude-desktop-archive-keyring.asc https://downloads.claude.ai/claude-desktop/key.asc
   ```

   Зарегистрируйте репозиторий:

   ```bash
   echo "deb [arch=amd64,arm64 signed-by=/usr/share/keyrings/claude-desktop-archive-keyring.asc] https://downloads.claude.ai/claude-desktop/apt/stable stable main" | sudo tee /etc/apt/sources.list.d/claude-desktop.list
   ```

2. **Установите пакет**

   ```bash
   sudo apt update && sudo apt install claude-desktop
   ```

3. **Запустите и войдите в систему**

   Запустите **Claude** из меню запуска приложений или выполните `claude-desktop` в терминале и войдите с помощью вашей учётной записи Anthropic.

### Проверьте ключ подписи

Вы можете убедиться, что скачанный ключ подписи принадлежит Anthropic:

```bash
gpg --show-keys /usr/share/keyrings/claude-desktop-archive-keyring.asc
```

Отпечаток должен быть `31DD DE24 DDFA B679 F42D 7BD2 BAA9 29FF 1A7E CACE`.

### Установка из скачанного файла

Если вы не можете воспользоваться apt-репозиторием, скачайте пакет `.deb` для вашей архитектуры (x64 или arm64) с [claude.com/download](https://claude.com/download), затем либо откройте его в вашем установщике программного обеспечения, либо выполните из каталога загрузок:

```bash
sudo apt install ./claude-desktop_*.deb
```

Пакет `.deb`, установленный таким способом, не получает обновлений. Чтобы получать обновления через apt, добавьте репозиторий, как показано выше, или раскомментируйте строку `deb` в шаблонной записи, которую пакет записывает в `/etc/apt/sources.list.d/claude-desktop.list`.

## Обновление

Настольное приложение не обновляет себя само в Linux. Обновления приходят с обычными обновлениями пакетов вашей системы:

```bash
sudo apt update && sudo apt upgrade
```

Графический менеджер обновлений программного обеспечения вашего дистрибутива также подхватит новые версии.

## Удаление

```bash
sudo apt remove claude-desktop
```

Это удаляет ключ подписи вместе с приложением, поэтому, если при установке вы добавляли запись репозитория, удалите и её:

```bash
sudo rm /etc/apt/sources.list.d/claude-desktop.list
```

## Что пока недоступно в бете для Linux

* **Computer Use**: [управление приложениями и экраном](https://code.claude.com/docs/en/desktop#let-claude-use-your-computer) недоступно в Linux.
* **Диктовка**: голосовой ввод недоступен в настольном приложении для Linux. Вместо этого используйте [голосовую диктовку](https://code.claude.com/docs/en/voice-dictation) в CLI.
* **Глобальная горячая клавиша быстрого ввода (Quick Entry)**: работает в X11. В нативном Wayland требуется портал GlobalShortcuts вашей среды рабочего стола.
* **Fedora и RHEL**: на сегодняшний день поддерживаются только дистрибутивы на основе Debian. Поддержка дополнительных дистрибутивов появится в будущем.

Для всего, что пока недоступно в настольном приложении, [CLI](https://code.claude.com/docs/en/quickstart) запускает тот же движок Claude Code и поддерживает более широкий набор дистрибутивов Linux; см. [системные требования](https://code.claude.com/docs/en/setup#system-requirements).

---

> _Перевод официальной документации Claude Code (раздел «Claude Code на десктопе»). Источник: https://code.claude.com/docs/en/desktop-linux. © Anthropic._

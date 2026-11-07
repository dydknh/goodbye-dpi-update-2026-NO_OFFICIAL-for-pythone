# **<span style="color:red">THIS IS NOT THE OFFICIAL GOODBYEDPI, BUT A WORKING FORK OF IT. / ЭТО НЕ ОФИЦИАЛЬНЫЙ ГУДБАЙ ДЖПИАЙ, А ВСЕГО ЛИШЬ ЕГО РАБОЧИЙ ФОРК.</span>**

---

# 🚀 Обновленный GoodbyeDPI (Кастомная сборка)

Репозиторий содержит кастомную структуру скриптов на базе предварительного релиза **GoodbyeDPI v0.2.3rc3**, оптимизированную для обхода жестких настроек ТСПУ и глубокого анализа пакетов.

## ⚠️ Важное требование / Prerequisites
* **Для работы интерактивного меню, утилит диагностики, обновления списков и самого запуска обхода в этой сборке ОБЯЗАТЕЛЬНО должен быть установлен Python!** Убедитесь, что интерпретатор установлен и команда `python` доступна в вашей системе.
* **Python is REQUIRED to run the interactive menu, diagnostic tools, blacklist updater, and the bypass itself in this build.** Make sure Python is installed and added to your system PATH variable.

---

## ⚡ Быстрый запуск

1. **Самый простой способ:** Дважды нажмите **`all bypass.bat`**.
   * Скрипт автоматически запросит права администратора через стандартное окно UAC Windows.
   * Запустится GoodbyeDPI с наиболее эффективным профилем `strong`.
   * **Важно:** Это консольная утилита, графического интерфейса у нее нет. Фильтрация работает, пока открыто черное окно консоли. Для остановки нажмите `Ctrl + C` или просто закройте окно.

2. **Единое интерактивное меню:** Запустите **`START.cmd`** или выполните в консоли `all bypass.bat --menu`.
   * Откроется полноценное текстовое меню для управления всеми 10 доступными режимами, обновления списков заблокированных сайтов, запуска диагностики и быстрой установки службы.
   * *Внимание:* Одновременно можно запускать только один режим. Перед выбором нового обязательно закройте окно предыдущего!

## 🛠 Описание профилей и режимов

### Специальные профили
* **`strong`** — Официальный режим `-9` плюс дополнительные методы против пассивного DPI и модификации заголовков HTTP Host. Генерирует 5 поддельных пакетов с двукратной отправкой.
* **`strong-dns`** — Профиль `strong` с экспериментальным перенаправлением UDP DNS-запросов на сервера Яндекса (без изменения системных сетевых настроек Windows). Используйте, если у провайдера заблокированы или подменяются DNS.
* **`sni`** — Альтернативное разделение TLS-сессии перед отправкой имени сайта (SNI) + 5 фейковых пакетов. Другой технический подход, помогающий, если не работает стандартный метод.

### Базовые режимы

| № | Режим | Описание флагов и применение |
|---|---|---|
| **1** | **`standard`** | Официальный профиль `-9` без ограничения списком доменов. Рекомендуется начинать тестирование с него. Глобально блокирует QUIC/HTTP3 для принудительного перехода браузера на TCP. |
| **2** | **`standard-dns`** | Профиль `standard` с перенаправлением UDP DNS на безопасный порт Яндекса (`77.88.8.8:1253`). Не меняет DNS в свойствах адаптера Windows. |
| **3** | **`youtube`** | Кастомный режим для YouTube: официальный набор параметров `-9 --fake-gen 5 --fake-from-hex...`, работающий в режиме полного трафика (без фильтрации списком). |
| **4** | **`compatible`** | Мягкий режим `-6 -q` без отправки неверных контрольных сумм (Checksum). Используйте, если ваш домашний роутер зависает или сбрасывает соединение при режиме `standard`. |
| **5** | **`legacy`** | Запасной старый метод фрагментации `-1 -q` из ранних версий утилиты. Может снижать общую скорость загрузки. |
| **6/7** | **`russia` / `russia-dns`** | Модификация трафика применяется **строго по списку доменов** из файлов `russia-blacklist.txt` и `russia-youtube.txt`. Трафик к остальным сайтам идет без изменений. |

> ⚠️ **Примечание по спискам:** В версии 0.2.3rc3 присутствует известная проблема нестабильной работы списков при включенном в системе протоколе Kyber. Если сайты из списков не открываются, переключитесь на глобальные режимы (1, 3 или `strong`), работающие без привязки к файлам.

## 🔄 Что изменено и как обновляться

* **Движок:** Установлены официальные бинарники версии **0.2.3rc3** (сборка `goodbyedpi-0.2.3rc3-2.zip` от сентября 2024 года, оригинальный драйвер WinDivert в комплекте). Предыдущие исполняемые файлы автоматически сохранены в папку `backups/`.
* **Проверка целостности:** Зафиксированный SHA256 оригинального архива: `37f96b32d050dadcc930a639eba68e1ccd57ed5c04a5f77dfca908f01905a4c5`. Побайтовые хеши каждого бинарника проверяются через манифест `tools/official-manifest.json`.
* **Обновление списков:** Скрипт `0_russia_update_blacklist_file.cmd` скачивает свежий блеклист по HTTPS из официального репозитория, проверяет валидность структуры и делает бэкап старого файла (`.bak`). При желании можно указать свой источник через ключ `-ListSource`.

## 👥 Установка в качестве службы Windows

Для настройки фоновой работы без постоянно открытых окон консоли используйте специальные скрипты:
* `service_install_standard.cmd` — служба для глобального режима.
* `service_install_russia_blacklist.cmd` — служба для работы строго по спискам.

**Правила управления службой:**
1. Запуск инсталляторов требует явных прав Администратора.
2. Существующая служба автоматически не заменяется. Перед установкой нового режима обязательно запустите **`service_remove.cmd`**.
3. После установки службы **нельзя перемещать или переименовывать папку** с программой.
4. Поставить фоновую работу на паузу можно через классическую панель управления: `Win+R` -> `services.msc` -> find `GoodbyeDPI` -> Остановить.

## 🔍 Диагностика и требования

* **Системные требования:** Скрипты написаны на встроенном интерпретаторе **Python** (вызываются через команду `python`). Дополнительные внешние библиотеки устанавливать не нужно.
* **PowerShell:** Код полностью очищен от зависимостей PowerShell (старый скрипт `manage.ps1` удален). Это сделано для стабильной работы на ПК с жесткими системными политиками ограничения выполнения скриптов.
* **Тестирование:** Скрипт `diagnostics.cmd` проверяет целостность структуры файлов сборки и корректность вызова `.exe` (перехват трафика при этом не включается). Посмотреть параметры любого профиля без его применения можно командой:
  ```bash
  python tools\manage.py -Action Preview -Profile standard
  ```

## 🛑 Важные ограничения

* **Это не VPN:** Утилита работает локально на уровне пакетов, ваш реальный IP-адрес не изменяется, а заблокированные по IP ресурсы (или имеющие гео-ограничения) не откроются.
* **Архитектура:** Данный комплект драйверов WinDivert предназначен для систем x86/x64. Процессоры **ARM64 не поддерживаются**.
* **Рекомендация:** Не отключайте проверку подписей драйверов Windows или защиту TLS/Kyber в браузере глобально ради списков. Если возникают проблемы совместимости — просто используйте режимы без списков (сплошная фильтрация трафика).

---

# 🚀 Updated GoodbyeDPI (Custom Build)

This repository contains a custom script structure based on the **GoodbyeDPI v0.2.3rc3** pre-release, optimized to bypass strict TSPU censorship and deep packet inspection configurations.

## ⚡ Quick Start

1. **The easiest way:** Double-click **`all bypass.bat`**.
   * The script will automatically request administrator privileges via the standard Windows UAC prompt.
   * GoodbyeDPI will launch using the most effective profile: `strong`.
   * **Important:** This is a console utility; it does not have a separate graphical interface. Filtering works as long as the black console window remains open. To stop it, press `Ctrl + C` or simply close the window.

2. **Unified Interactive Menu:** Run **`START.cmd`** or execute `all bypass.bat --menu` in the console.
   * This opens a text-based menu to manage all 10 available modes, update blacklist files, run diagnostics, and easily install the service.
   * *Attention:* Only one mode can run at a time. Be sure to close the previous window before selecting a new one!

## 🛠 Profile and Mode Descriptions

### Special Profiles
* **`strong`** — Official `-9` mode plus additional techniques against passive DPI and HTTP Host header modifications. Generates 5 fake packets sent twice.
* **`strong-dns`** — The `strong` profile combined with experimental redirection of UDP DNS requests to Yandex servers (without changing global Windows network adapter properties). Use this if your ISP blocks or tampers with DNS.
* **`sni`** — An alternative approach that splits the TLS session right before sending the Server Name Indication (SNI) + 5 fake packets. A different technical method to try if standard splitting fails.

### Base Modes

| # | Mode | Flags Description & Use Case |
|---|---|---|
| **1** | **`standard`** | Official `-9` profile without any domain lists (global filtering). It is recommended to start testing with this mode. It globally blocks QUIC/HTTP3 to force browsers to switch to TCP. |
| **2** | **`standard-dns`** | The `standard` profile with UDP DNS redirection to a secure Yandex port (`77.88.8.8:1253`). Does not modify Windows network adapter settings. |
| **3** | **`youtube`** | A custom mode tailored for YouTube: uses official parameters `-9 --fake-gen 5 --fake-from-hex...` operating globally without a domain list constraint. |
| **4** | **`compatible`** | A lighter `-6 -q` mode without generating invalid checksums. Try this if your home router freezes or drops connections when running the `standard` mode. |
| **5** | **`legacy`** | A backup method using old fragmentation tricks (`-1 -q`) from earlier utility versions. May cause some speed drops. |
| **6/7** | **`russia` / `russia-dns`** | Traffic modifications are applied **strictly to domains** listed in `russia-blacklist.txt` and `russia-youtube.txt`. Regular traffic remains untouched. |

> ⚠️ **Note on Lists:** In version 0.2.3rc3, there is a known issue where domain lists work inconsistently if the Kyber protocol is enabled in your system. If blacklisted sites fail to open, switch to global modes (1, 3, or `strong`) that operate without relying on text lists.

## 🔄 Changes & Updates

Используйте код с осторожностью.
• Engine: Updated with official 0.2.3rc3 binaries (from the goodbyedpi-0.2.3rc3-2.zip archive released in September 2024, including the original WinDivert driver). Older execution files are backed up to the backups/ folder.
• Integrity Check: The fixed SHA256 of the downloaded archive is 37f96b32d050dadcc930a639eba68e1ccd57ed5c04a5f77dfca908f01905a4c5. Byte-by-byte hashes for individual binaries can be verified via tools/official-manifest.json.
• List Updates: The 0_russia_update_blacklist_file.cmd script downloads the latest blacklist via HTTPS from the official repository, verifies its structure, and creates a .bak backup of the old list. You can specify a custom source using the -ListSource argument.

👥 Windows Service Installation

To configure focus-free background operation without active console windows, use the dedicated setup scripts:
• service_install_standard.cmd — service for the global configuration.
• service_install_russia_blacklist.cmd — service restricted to domain list filtering.
Service Management Rules:
1. Running the installer scripts requires explicit Administrator privileges.
2. Existing services are not overwritten automatically. Run service_remove.cmd before changing your service mode.
3. Once the service is installed, do not move or rename the folder containing the files.
4. To temporarily pause the background filter, use the native Windows panel: Win+R -> services.msc -> find GoodbyeDPI -> Click Stop.

🔍 Diagnostics & Requirements

• System Requirements: Scripts run on the built-in Python interpreter (invoked via the python command). No external libraries or dependencies are required.
• PowerShell: Completely cleaned of PowerShell dependencies (the old manage.ps1 script has been removed). This ensures reliable execution even on systems with restrictive script execution policies (ExecutionPolicy).
• Testing: The diagnostics.cmd script checks folder integrity and verifies .exe execution capability ( it does not start packet interception). To preview any profile configuration without launching it, run:
  bash python tools\manage.py -Action Preview -Profile standard   

🛑 Important Limitations

• This is not a VPN: The utility operates locally at the packet level. It does not change your public IP address, and resources blocked entirely by IP or region-locked content will not open.
• Architecture: This specific WinDivert driver bundle is built for x86/x64 systems. ARM64 processors are not supported.
• Recommendation: Do not globally disable driver signature verification or browser TLS/Kyber protections just to make domain lists work. If compatibility issues persist, simply switch to global, listless filtration modes.

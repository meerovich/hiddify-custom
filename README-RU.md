# Hiddify Windows: autoconnect + `tunnel activate` fix

Этот мини-репозиторий предназначен только для воспроизводимой сборки Windows-версии Hiddify с двумя нужными изменениями:

1. восстановление предыдущего подключения при старте Windows (логика PR hiddify/hiddify-app #2321);
2. исправленный Hiddify Core, в котором адрес локального tunnel service формируется корректно и `HiddifyCli tunnel activate` не страдает от ошибки `tcp/18020`.

## Что именно зафиксировано

- App source: `LiuLin1220/hiddify-app`
- App commit: `72c1a3d8a2e7c6d10be99e84b02982da68809526`
- Core source: `hiddify/hiddify-core`
- Core commit: `db74dfc257d5becb4b4e9dbc7257a3dcdde20692`
- Flutter: `3.38.5`
- Сборка: **unsigned**.

Это не официальный релиз Hiddify. Используются публичные исходники Hiddify и зафиксированные SHA.

## Как собрать

1. Создайте любой пустой GitHub-репозиторий.
2. Скопируйте туда этот архив/каталог так, чтобы workflow лежал по пути:
   `.github/workflows/build-hiddify-windows.yml`
3. Сделайте commit/push.
4. Откройте вкладку **Actions**.
5. Выберите **Build Hiddify Windows - auto-connect + tunnel fix**.
6. Нажмите **Run workflow**.
7. После успешного завершения скачайте artifact:
   `Hiddify-Windows-auto-connect-tunnel-fix`.

В artifact будут:

- `Hiddify-Windows-Portable-x64.zip`
- `Hiddify-Windows-Setup-x64.exe`
- `SHA256SUMS.txt`
- `BUILD-PROVENANCE.txt`
- `CORE-BUILD-PROVENANCE.txt`

## Почему workflow сначала тестирует

Сборка Windows начинается только после:
- `flutter test` для зафиксированного app commit;
- успешной компиляции фиксированного core;
- проверки точных SHA core/sing-box/cronet;
- проверки наличия `HiddifyCli.exe`, `hiddify-core.dll`, `libcronet.dll`.

Portable ZIP после сборки дополнительно открывается как настоящий ZIP и проверяется наличие основных Windows-файлов.

## Важное про Windows Administrator

Эта сборка решает **автоподключение** и баг CLI, но не отключает UAC и не встраивает `requireAdministrator` в GUI.

Для вашего сценария надёжнее запускать Hiddify при входе через Windows Task Scheduler:
- `Run only when user is logged on`
- `Run with highest privileges`
- аргумент: `--autostart`
- разумная задержка после входа: 10–15 секунд.

Так Hiddify получает права для TUN, а исправленная логика startup restore восстанавливает подключение.

## Безопасность

Сборка не подписана сертификатом разработчиков Hiddify. Windows SmartScreen может показать `Unknown publisher`.
Проверяйте SHA256 из `SHA256SUMS.txt`, созданного в том же CI run.

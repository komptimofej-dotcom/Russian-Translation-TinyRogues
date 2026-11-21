# Tiny Rogues — офлайн русский перевод

Переносимый проект: плагин BepInEx 6 (IL2CPP), словарь на 8270 активных записей,
локальный шрифт и загрузчик. **Во время игры интернет не используется.**

Проект предназначен для копирования в уже установленную игру Tiny Rogues (Steam, 64-bit, Windows).
Оригинальные файлы игры (`GameAssembly.dll`, `UnityPlayer.dll`, `Tiny Rogues_Data\...`)
не изменяются и не входят в состав проекта.

---

## 1. Совместимость

| Что | Значение |
|---|---|
| Игра | Tiny Rogues, Steam, Windows x64 |
| Версия игры | сборка `20241007` (buildid в `steamapps\appmanifest_1337140.acf`) |
| Движок | Unity 2022.3.62f2, IL2CPP, .NET 6.0.7 |
| BepInEx | `6.0.0-be.759+9aedb90` (Unity IL2CPP win-x64) |
| Плагин | `community.tinyrogues.russian` 0.1.1 |
| ОС | Windows 10/11, нужна кириллическая TTF (по умолчанию `C:\Windows\Fonts\segoeui.ttf`) |

**Важно:** BepInEx `6.0.0-pre.2 / be.697` не подходит для этой игры — он падает с
`Unsupported metadata version found! We support 23-29, got 31`. В проекте лежит именно be.759.

Если игра обновилась и версия отличается, удалите `BepInEx\interop` и `BepInEx\cache`
в папке игры и запустите игру один раз: BepInEx создаст сборки заново (1–2 минуты, без интернета).
Если после этого плагин не загрузится — вернитесь к версии игры `20241007`.

---

## 2. Установка

1. **Закройте игру и Steam.** Это важно: игра держит файлы открытыми.
2. Распакуйте архив проекта в любую папку, например `C:\Downloads\TinyRogues-Russian-Offline`.
3. Запустите **`install.bat`** двойным щелчком.
   - Скрипт сам найдёт Steam через реестр и проверит путь `steamapps\common\Tiny Rogues`.
   - Если игра стоит в другой библиотеке Steam — скрипт спросит путь. Можно также
     перетащить папку игры на `install.bat`.
   - Если у вас уже стоит другой BepInEx-мод, скрипт предупредит: файлы `core`,
     `config` и `plugins` будут объединены, а не удалены.
   - **Несовместимость:** если в `BepInEx\plugins` есть `XUnity.AutoTranslator` или
     `tmpfallback`, установка прервётся. Удалите их вручную — плагин намеренно отключается,
     чтобы не смешивать два перевода.
4. Запустите игру обычным способом через Steam.
5. Готово. Перевод применяется автоматически.

### Что именно копируется

В корень игры копируются:

```
.doorstop_version
doorstop_config.ini
baselib.dll
winhttp.dll
dotnet\                              — локальная .NET 6 runtime
BepInEx\core\                        — BepInEx 6 be.759
BepInEx\interop\                     — готовые interop-сборки (чтобы не ждать первый запуск)
BepInEx\unity-libs\
BepInEx\config\                      — оптимизированные настройки
BepInEx\patchers\
BepInEx\plugins\TinyRogues.Russian\  — DLL + 47 JSON-паков + README
```

Ничего из `Tiny Rogues_Data\` не трогается.

---

## 3. Проверка успешной установки

Откройте `BepInEx\LogOutput.log` в папке игры. Должны присутствовать строки:

```
[Info   :   BepInEx] Loading [Tiny Rogues Russian 0.1.1]
[Info   :Tiny Rogues Russian] Catalog loaded: 8270 active entries.
[Info   :Tiny Rogues Russian] Adapters installed: three dialogue methods, ...
[Info   :Tiny Rogues Russian] Independent localization active: 8270 entries; ...
[Info   :Tiny Rogues Russian] Russian fallback font created from local file: segoeui.ttf.
```

**Не должно быть** строк `Localization disabled safely`, `Error`, `Pack reload rejected`.
Если они есть — см. раздел 7.

Дополнительный встроенный тест (не обязателен):
создайте ярлык игры и добавьте в конец поля «Объект»:
```
 -tinyrogues-russian-selftest
```
В логе появится `INDEPENDENT SELFTEST PASS`, а в папке плагина — `diagnostics\`.

---

## 4. Производительность

Игра начала подлагивать из-за четырёх вещей в плагине. Все они исправлены в этой версии:

| Было | Стало |
|---|---|
| `RecordMissing = true` — регулярное выражение и запись на диск при каждом непереведённом тексте | `false` по умолчанию; запись только по запросу |
| `WatchPacks` — опрос 47 JSON-файлов раз в секунду | `false` по умолчанию; словарь читается один раз при старте |
| Перевод заново вычислялся при каждой установке текста | кэш до 20 000 готовых переводов в памяти |
| TMP-меш: 3 `GetComponentInParent`, Regex и два вложенных цикла по всем символам на каждый кадр | кэш структуры, сканирование символов без Regex, один проход, ранний выход для неизменившегося текста |
| `Context()` — обход до 12 родителей + `string.Join` на каждую установку текста | результат кэшируется по объекту |
| `BepInEx.cfg → InstantFlushing = true` — форсированная запись лога | `false` |

Настройки находятся в `BepInEx\config\community.tinyrogues.russian.cfg`:

```ini
[Font]
Path = C:\Windows\Fonts\segoeui.ttf

[Translation]
RecordMissing = false

[Performance]
WatchPacks = false
```

Если вы правите словарь вручную и хотите видеть изменения без перезапуска — временно
включите `WatchPacks = true`, но верните `false` после правки: опрос файлов даёт
микрозадержки.

Если вы ищете пропущенные строки — временно включите `RecordMissing = true`,
запустите игру, посмотрите `BepInEx\plugins\TinyRogues.Russian\captures\missing.jsonl`
и снова выключите.

---

## 5. Включение / выключение перевода

- **Временно выключить, не удаляя:** переименуйте
  `BepInEx\plugins\TinyRogues.Russian\locales\ru` в `ru.off`. Плагин останется, но перевод
  применять не будет (в логе появится `Localization disabled safely: No translation packs found` — это нормально).
- **Вернуть оригинальную игру полностью:** запустите **`uninstall.bat`**.
  Он удалит только файлы этого проекта. Если вы ставили свои моды, не запускайте его —
  удалите `BepInEx\plugins\TinyRogues.Russian`, конфиги и загрузчик вручную.

---

## 6. Решение проблем

**Игра не запускается / чёрный экран.**
Убедитесь, что распаковали `payload` *в корень игры*, а не в подпапку. Проверьте,
что `winhttp.dll`, `doorstop_config.ini` и `BepInEx\core\BepInEx.Unity.IL2CPP.dll`
лежат рядом с `Tiny Rogues.exe`.

**В логе `Unsupported metadata version` / `Localization disabled safely`.**
Версия игры не совпадает. Удалите `BepInEx\interop` и `BepInEx\cache`, запустите игру
один раз и дайте ей 1–2 минуты. Если не помогло — нужна игра сборки `20241007`.

**В логе `another translator is installed`.**
Удалите `XUnity.AutoTranslator` и/или `tmpfallback` из `BepInEx\plugins`.

**Квадраты вместо букв.**
Откройте `BepInEx\config\community.tinyrogues.russian.cfg` и укажите `Path` на любой
установленный TTF с кириллицей, например `C:\Windows\Fonts\segoeui.ttf` или
`C:\Windows\Fonts\arial.ttf`. Файл шрифта в проект не входит (лицензия), берётся из системы.

**Остались английские слова.**
Проверьте, что вы используете словарь из этого архива (47 файлов в `locales\ru`),
и что в логе нет `Pack reload rejected`. Для поиска пропусков включите `RecordMissing`
временно, как описано выше.

**Опять начались просадки.**
Проверьте, что `RecordMissing = false` и `WatchPacks = false`, и что вы запускаете
игру не с флагом `-tinyrogues-russian-selftest`.

---

## 7. Состав проекта

```
TinyRogues-Russian-Offline\
├─ README.md                    — этот файл
├─ install.bat                  — установка
├─ uninstall.bat                — удаление
├─ THIRD_PARTY_NOTICES.md       — лицензии и благодарности
├─ build-info.txt               — версии, контрольные суммы
├─ payload\                     — копируется в корень игры
└─ source\                      — исходники плагина (C#, .NET Standard 2.1)
   ├─ Core\
   └─ Plugin\
```

Сборка из исходников (по желанию, нужен .NET SDK 6+):
```
dotnet build source\Plugin\TinyRogues.Russian.csproj -c Release -p:GameDir="ПУТЬКИГРЕ"
```
Готовый `TinyRogues.Russian.dll` уже лежит в `payload` — собирать ничего не обязательно.

---

## 8. Юридическое

- Перевод машинный, сделан для личного пользования; авторские права на игру и её тексты
  принадлежат владельцам Tiny Rogues.
- Исходная идея и часть кода плагина адаптированы из MIT-проекта
  `FIELD9z/TinyRogues-Chinese`. См. `THIRD_PARTY_NOTICES.md`.
- BepInEx распространяется по LGPL-2.1, Harmony — MIT.
- Файлы шрифта в архив **не входят**: используется системный шрифт Windows.
- Не распространяйте файлы самой игры (`GameAssembly.dll`, `UnityPlayer.dll`,
  `Tiny Rogues_Data\...`) вместе с этим проектом.

# Tiny Rogues — полный русский перевод (офлайн)


Полный русский перевод Tiny Rogues: 8474 активных записей — меню, классы, навыки, предметы, диалоги, подсказки и описания. Работает **без интернета**: все словари уже внутри архива. Оригинальные файлы игры не изменяются, сохранения не сбрасываются.


## Скачать


**[⬇ TinyRogues-Russian-Offline.zip (54 МБ)](https://github.com/komptimofej-dotcom/Russian-Translation-TinyRogues/releases/download/TinyRogues/TinyRogues-Russian-Offline.zip)**


[Страница релиза со всеми файлами](https://github.com/komptimofej-dotcom/Russian-Translation-TinyRogues/releases/tag/TinyRogues) · [Проверить контрольную сумму](#проверка-архива)


SHA-256 архива:


```
b4dbcc00c8320b07575de4323d11cf115fd0e6d3036b3ce663fa70b1afe79fc8
```


## Что понадобится


- Tiny Rogues из Steam, Windows 10/11 x64.
- Сборка игры **20241007**. Проверить: зайдите в `steamapps\`, откройте `appmanifest_2088570.acf` и найдите `"buildid"`.
- Системный шрифт с кириллицей — Segoe UI в Windows есть по умолчанию.
- Перед установкой закройте игру и Steam.


## Установка


1. Скачайте архив по ссылке выше и распакуйте его в любую папку, например в «Загрузки». **Не распаковывайте содержимое прямо в папку игры** — установщик сделает это сам.
2. Закройте игру и Steam полностью.
3. Запустите `install.bat` двойным щелчком. Если Windows покажет предупреждение — «Подробнее» → «Выполнить в любом случае».
   - Если появится ошибка `is not recognized as an internal or external command`, извлеките `install.bat` заново из архива: батник мог быть пересохранён с BOM или с переводами строк LF.
4. Скрипт сам найдёт Steam. Если игра стоит в другой библиотеке, введите путь вручную или перетащите папку игры на `install.bat`.
5. Если уже стоит другой BepInEx-мод — согласитесь на объединение папок. Если есть `XUnity.AutoTranslator` — удалите его: два переводчика одновременно не работают.
6. Дождитесь «ГОТОВО. Запустите игру через Steam.»
7. Запустите игру через Steam. Перевод включится автоматически; первый запуск может занять 1–2 минуты.


## Проверка


Откройте `BepInEx\LogOutput.log` в папке игры. Должны быть строки:


```text
Loading [Tiny Rogues Russian 0.1.1]
Catalog loaded: 8474 active entries.
Adapters installed: ...
Independent localization active: 8474 entries; ...
Russian fallback font created from local file: segoeui.ttf.

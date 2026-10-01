# Wonder Swine Flasher

`<img src="Assets/wonder_swine_icon.png" width="250" alt="Wonder Swine Flasher icon">`{=html}

**Wonder Swine Flasher** --- программа для чтения, записи, проверки и
обслуживания совместимых WonderSwan / WonderSwan Color flash-картриджей
на базе китайского программатора STM32H750 и CPLD Altera MAX II EPM240.

Программа предназначена прежде всего для **Wonder Swine multicart**:
Wonder Swine CPLD, Menu, нескольких ROM на одном картридже и независимых
резервных копий сохранений. Также предусмотрен **Standalone**-режим для
работы с обычным одиночным ROM.

> \[!WARNING\] **ВСЁ, ЧТО ВЫ ДЕЛАЕТЕ С ЭТОЙ ПРОГРАММОЙ, ВЫ ДЕЛАЕТЕ НА
> СВОЙ СТРАХ И РИСК!**
>
> На данный момент тестирование проводилось на крайне ограниченном
> количестве оборудования. Внешне одинаковые китайские картриджи могут
> иметь другую разводку платы. Ошибка при прошивке CPLD, Flash или
> save-data может привести к потере данных или неработоспособности
> картриджа.

> \[!IMPORTANT\] В первую очередь ищите картридж с дополнительными
> **ЗАДНИМИ** контактами. Однако одинаковый внешний вид не гарантирует
> совместимость.
>
> **Перед первой прошивкой Wonder Swine CPLD обязательно рекомендуется
> считать исходную CPLD через Quartus `Examine` и проверить полученный
> `.pof` кнопкой `Check POF...`.**
>
> Если программа показывает `UNKNOWN CPLD CONFIGURATION - DO NOT FLASH`,
> не прошивайте Wonder Swine CPLD до отдельной проверки совместимости
> платы.

------------------------------------------------------------------------

## Что понадобится

### Для обычной работы с ROM и save

-   совместимый WonderSwan flash-картридж (для перепрошивки он должен
    быть разобран);

`<img src="Assets/screenshots/china-cart.jpg" width="250">`{=html}

-   китайский **STM32H750 WonderSwan writer/dumper**
    (`USB H750 WSC flasher`);

`<img src="Assets/screenshots/STM32H750+cart.jpg" width="250">`{=html}

-   рабочий USB/Serial-драйвер --- программатор должен появиться в
    Windows как COM-порт;

`<img src="Assets/screenshots/COM-PORT.jpg">`{=html}

-   WonderSwan / WonderSwan Color / SwanCrystal для проверки результата
    на реальном железе.

`<img src="Assets/screenshots/about.jpg" width="250">`{=html}

> \[!IMPORTANT\] Картридж программируется через **ЗАДНИЕ** контакты
> слота. Передние edge-контакты картриджа при работе с этим
> программатором должны быть изолированы. Можно использовать небольшой
> кусочек бумаги или изоленты.

### Для первой установки / восстановления CPLD

Дополнительно нужны:

-   **USB Blaster**;
-   **Quartus II 13.0 SP1**;
-   JTAG-доступ к EPM240 на картридже;
-   файл **Wonder Swine CPLD POF** из релизного архива.

### Схема подключения USB Blaster

`<a href="Assets/screenshots/usbblasterpinout.jpg">`{=html}`<img src="Assets/screenshots/usbblasterpinout.jpg" width="200" alt="USB Blaster pinout">`{=html}`</a>`{=html}
`<a href="Assets/screenshots/usbblasterpinout3.jpg">`{=html}`<img src="Assets/screenshots/usbblasterpinout3.jpg" width="200" alt="USB Blaster pinout">`{=html}`</a>`{=html}

-   На экспериментальном картридже JTAG выполнен в виде отверстий с
    шагом 1,25 мм.
-   Для USB Blaster понадобится переходник с Dupont 2,54 мм на JST MX
    1,25 мм.
-   Разъём можно временно прижать к контактам: операция чтения/прошивки
    CPLD занимает недолго.
-   Во время работы CPLD картридж должен получать питание **только от
    одного источника**. Предпочтительно использовать программатор
    STM32H750. Не подключайте одновременно питание от программатора и
    консоли.

------------------------------------------------------------------------

# Проверка платы перед первой прошивкой CPLD

Для неизвестного экземпляра китайского картриджа этот шаг **настоятельно
рекомендуется**.

1.  Подключите картридж к USB Blaster.

`<a href="Assets/screenshots/usbblasterpinout2.jpg">`{=html}`<img src="Assets/screenshots/usbblasterpinout2.jpg" width="200" alt="USB Blaster connection">`{=html}`</a>`{=html}

2.  В Quartus Programmer выполните **Auto Detect** --- ожидается
    устройство семейства `EPM240`.

`<a href="Assets/screenshots/quartus1.jpg">`{=html}`<img src="Assets/screenshots/quartus1.jpg" width="200" alt="Quartus Auto Detect">`{=html}`</a>`{=html}

3.  Выполните **Examine** и сохраните считанный `.pof`. Для
    дополнительной уверенности можно повторить чтение несколько раз и
    убедиться, что полученные файлы одинаковы.

`<a href="Assets/screenshots/quartus2.jpg">`{=html}`<img src="Assets/screenshots/quartus2.jpg" width="200" alt="Quartus Examine">`{=html}`</a>`{=html}
`<a href="Assets/screenshots/quartus3.jpg">`{=html}`<img src="Assets/screenshots/quartus3.jpg" width="200" alt="Quartus save POF">`{=html}`</a>`{=html}

4.  Запустите Wonder Swine Flasher.
5.  Нажмите **Check POF...** и выберите считанный файл.

Возможные результаты:

### `COMPATIBLE REPLICA - SUPPORTED`

Обнаружена известная и проверенная конфигурация совместимого
flash-картриджа. Установка Wonder Swine CPLD для неё поддерживается.

### `WONDER SWINE CPLD vX.XX - UPDATE / RECOVERY ONLY`

Обнаружена Wonder Swine CPLD. Если этот POF только что считан через
`Examine` с картриджа, Wonder Swine CPLD уже установлена. Повторная
прошивка нужна только для обновления или восстановления.

### `UNKNOWN CPLD CONFIGURATION - DO NOT FLASH`

Конфигурация не известна проекту. Это **не доказывает**, что плата
несовместима, но прошивать Wonder Swine CPLD на неё не следует до
отдельной проверки разводки.

------------------------------------------------------------------------

# Быстрый старт

После установки Wonder Swine CPLD:

1.  Вставьте картридж в программатор STM32H750.
2.  Запустите `WonderSwineFlasher.exe`.
3.  Выберите COM-порт и нажмите **Connect**.
4.  Выберите `Multicart (Menu + Games)`.
5.  Дождитесь зелёного статуса:

`<img src="Assets/screenshots/wscompatible.jpg">`{=html}

6.  Нажмите **Scan cartridge**.

`<img src="Assets/screenshots/scan.jpg">`{=html}

7.  Если список выглядит явно неправильно, проверьте положение и
    контакты картриджа в программаторе: разъём PCI-E достаточно
    капризен.
8.  Двойной клик по найденной игре автоматически выбирает её основной
    Slot и ROM size.
9.  Перед заменой уже записанного ROM выполните **Erase Selected ROM**.
10. Нажмите **Flash ROM...**.
11. После записи обязательно выполните **Verify ROM...**.

------------------------------------------------------------------------

# Режимы

## Multicart (Menu + Games)

Основной режим Wonder Swine.

Доступны:

``` text
Menu
Game1
Game2
Game3
Game4
Game5
Game6
Game7
```

`Menu` всегда использует размер **512K**.

Поддерживаемые размеры ROM:

``` text
512K
1M
2M
4M
8M
```

`8M` предназначен для Standalone и не используется как обычный игровой
слот Multicart.

После подключения Flasher автоматически проверяет работу Wonder Swine
CPLD.

Статусы:

-   **зелёный** `CPLD: Wonder Swine compatible` --- Wonder Swine CPLD
    работает;
-   **красный** `CPLD: stock / unsupported` --- необходимая поддержка
    Multicart не обнаружена;
-   **оранжевый** `CPLD: check inconclusive` --- проверка не дала
    однозначного результата;
-   `CPLD: not checked` --- проверка ещё не выполнялась.

Если совместимость не подтверждена, опасные Multicart-операции
автоматически блокируются.

## Standalone ROM (Slot0)

Режим обычного single-ROM картриджа без Wonder Swine Multicart.

Используется для чтения и обслуживания одиночного ROM или исходной
single-game конфигурации. Перед разрушительными операциями программа
дополнительно предупреждает пользователя о выбранном размере области.

------------------------------------------------------------------------

# Описание функций

## Connection

### Refresh COM

Обновляет список доступных COM-портов.

### Connect / Disconnect

Открывает или закрывает соединение с STM32H750 writer.

### CPLD status

Показывает результат автоматической проверки Wonder Swine CPLD.

### About

Версия программы, автор и информация о проекте.

------------------------------------------------------------------------

## Cartridge / ROM

### Mode

Переключает между:

-   `Multicart (Menu + Games)`;
-   `Standalone ROM (Slot0)`.

### Slot

Выбор `Menu` / `Game1..Game7` в Multicart или `Slot0` в Standalone.

### Size

Выбор размера ROM: `512K`, `1M`, `2M`, `4M`, `8M`.

Для `Menu` размер автоматически фиксируется на `512K`.

### Switch

Переключает Wonder Swine CPLD на выбранный Slot. Содержимое Flash не
изменяется.

### ROM Info

Показывает доступную информацию о выбранном ROM:

-   название игры;
-   Product ID;
-   Header ID;
-   Developer;
-   WonderSwan / WonderSwan Color;
-   Game ID;
-   Revision;
-   ROM size;
-   save type;
-   checksum.

Название и Product ID берутся из внешнего `game_db.csv`.

### Check POF...

Офлайн проверяет файл Altera MAX II `.pof` и определяет, относится ли он
к:

-   известной **Compatible Replica**;
-   установленной **Wonder Swine CPLD**;
-   неизвестной конфигурации.

Кнопка **не требует подключения программатора и ничего не прошивает**.

### Read ROM...

Считывает выбранный ROM в `.ws` / `.wsc` файл и показывает итоговый
размер и SHA-256.

Операция read-only.

### Flash ROM...

Записывает ROM выбранного размера.

-   размер файла должен соответствовать выбранному ROM size;
-   после завершения программа предлагает выполнить Verify;
-   при обновлении Wonder Swine Menu необходимые пользовательские данные
    сохраняются автоматически.

### Verify ROM...

Считывает ROM с картриджа и сравнивает его с выбранным файлом.

### Erase Selected ROM

Стирает выбранную ROM-область и проверяет результат.

### ERASE ALL USED

Стирает ROM-данные стандартной Wonder Swine Multicart-разметки, сохраняя
резервные сохранения и служебные данные Wonder Swine.

### STOP / Disconnect

Аварийно прерывает текущую host-side операцию.

После STOP необходимо подключиться заново. Если операция была прервана
во время записи или стирания, соответствующую область следует считать
незавершённой до последующей проверки и, при необходимости, повторного
стирания/записи.

------------------------------------------------------------------------

# Save functions

Wonder Swine Menu автоматически обслуживает независимые сохранения игр и
хранит их резервные копии на картридже.

### Read Save (safe)...

Безопасно считывает текущее сохранение в `.sav`.

Программа выполняет дополнительную проверку чтения перед сохранением
файла и показывает SHA-256 результата.

### Flash Save (safe)...

Записывает `.sav` на картридж.

Перед записью Flasher автоматически создаёт резервную копию текущего
сохранения, запрашивает подтверждение и после записи выполняет проверку
результата.

### Export All Vault Saves...

Экспортирует все корректные резервные сохранения игр с картриджа в
отдельные `.sav` файлы и создаёт текстовый manifest с результатами.

Это **read-only** операция: содержимое картриджа не изменяется.

------------------------------------------------------------------------

# Cartridge contents / Scan cartridge

`Scan cartridge` сканирует содержимое картриджа и показывает найденные
игры:

-   Slot;
-   Game title;
-   ROM size;
-   Product ID;
-   Header ID;
-   Save type.

Большие ROM отображаются одной строкой для основной игры, а не как
несколько занятых слотов.

### Двойной клик по строке

Двойной клик:

-   выбирает соответствующий Slot;
-   выбирает ROM Size;
-   при активном соединении переключает CPLD на этот слот.

Сам по себе двойной клик **никогда не запускает Flash, Erase или другую
разрушительную операцию**.

------------------------------------------------------------------------

# game_db.csv

`game_db.csv` --- внешний редактируемый каталог игр. Он должен лежать
рядом с программой.

Пример:

``` csv
Developer,Color,GameID,Revision,ProductID,Title
01,00,08,00,SWJ-BAN008,Kaze no Klonoa - Moonlight Museum
01,00,13,00,SWJ-BAN013,Rockman & Forte - Mirai Kara no Chousen Sha
01,01,35,00,SWJ-BANC35,Rockman EXE WS
```

После изменения CSV программу необходимо перезапустить.

Если `game_db.csv` отсутствует или не читается, основные функции Flasher
продолжают работать, но **Scan cartridge** недоступен.

------------------------------------------------------------------------

# Diagnostics

### Raw protocol

Расширенная диагностическая функция для ручной отправки команд
программатору.

> \[!WARNING\] **Не используйте Raw protocol для обычной работы.**
> Неверная команда может изменить состояние картриджа или запустить
> операцию программатора.

### Clear log

Очищает окно текущего лога.

Также программа ведёт файл:

``` text
Wonder_Swine_Flasher_session.log
```

рядом с EXE.

------------------------------------------------------------------------

# Рекомендуемый рабочий порядок

## Запись / замена игры

``` text
Connect
→ CPLD: Wonder Swine compatible
→ Scan cartridge
→ выбрать Game slot / размер
→ Erase Selected ROM
→ Flash ROM
→ Verify ROM
→ проверить игру на консоли
```

## Обновление Wonder Swine Menu

``` text
Connect
→ Multicart / Menu / 512K
→ Erase Selected ROM
→ Flash ROM с новым Wonder Swine Menu
→ Verify ROM
→ проверить загрузку Menu на консоли
```

Пользовательские данные Wonder Swine при штатном обновлении Menu
сохраняются автоматически.

## Первая установка CPLD на Compatible Replica

``` text
Quartus Auto Detect
→ Examine исходной CPLD
→ сохранить POF
→ Wonder Swine Flasher / Check POF...
→ COMPATIBLE REPLICA - SUPPORTED
→ только после этого Program Wonder Swine CPLD через Quartus
→ подключить картридж к STM32 writer
→ Connect в Flasher
→ убедиться: CPLD: Wonder Swine compatible
```

------------------------------------------------------------------------

# Важные предупреждения

-   **Всегда сохраняйте исходный CPLD POF** перед первой перепрошивкой.
-   Не прошивайте Wonder Swine CPLD на неизвестную ревизию платы только
    из-за похожего внешнего вида.
-   Не подавайте питание на картридж одновременно от программатора и
    консоли.
-   Перед заменой ROM выполните erase выбранной области.
-   После Flash всегда выполняйте Verify.
-   `ERASE ALL USED` уничтожает ROM-данные стандартной
    Multicart-разметки, но сохраняет резервные сохранения и служебные
    данные Wonder Swine.
-   `STOP` во время записи или стирания может оставить незавершённое
    содержимое.
-   Не отключайте питание и не вытаскивайте картридж во время Flash /
    Erase / Save write.
-   `Raw protocol` предназначен только для диагностики.
-   `Check POF: UNKNOWN` означает **неизвестную и непроверенную**, а не
    обязательно несовместимую конфигурацию.

------------------------------------------------------------------------

# Автор

**Black Ace**\
© 2026\
GitHub: `github.com/cjblackace`

Wonder Swine Flasher --- часть проекта **Wonder Swine**: Multicart /
Menu / CPLD / PC-side tooling для WonderSwan.

[![latest](https://img.shields.io/github/v/release/GyverLibs/EncButton.svg?color=brightgreen)](https://github.com/GyverLibs/EncButton/releases/latest/download/EncButton.zip)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/EncButton?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# EncButton

|⚠️⚠️⚠️<br>**Новая версия v3 несовместима с предыдущими, смотри документацию и примеры!**<br>⚠️⚠️⚠️|
| --- |

Лёгкая и очень функциональная библиотека для энкодера с кнопкой, энкодера или кнопки с Arduino
- Кнопка
    - Обработка событий: нажатие, отпускание, клик, счётчик кликов, удержание, импульсное удержание + предварительные клики для всех перечисленных режимов
    - Программное подавление дребезга
    - Поддержка обработки двух одновременно нажимаемых кнопок как третьей кнопки
- Энкодер
    - Обработка событий: обычный поворот, нажатый поворот, быстрый поворот
    - Поддержка четырёх типов инкрементальных энкодеров
    - Высокоточный алгоритм определения позиции
    - Буферизация в прерывании
- Простота и универсальность API для разных сценариев использования
- Виртуальный режим (например для работы с расширителем пинов)
- Оптимизирована для работы в прерывании (энкодер)
- Максимально быстрое чтение пинов для AVR (ATmega328/ATmega168, ATtiny85/ATtiny13)
- Быстрые асинхронные алгоритмы опроса действий с кнопки и энкодера
- Жёсткая оптимизация и небольшой вес во Flash и SRAM памяти: 5 байт SRAM (на экземпляр) и ~350 байт Flash на обработку кнопки

Примеры сценариев использования:
- Несколько кликов - включение режима (по кол-ву кликов)
- Несколько кликов + короткое удержание - ещё вариант включения режима (по кол-ву кликов)
- Несколько кликов + удержание - постепенное изменение значения выбранной переменной (по кол-ву кликов)
- Несколько кликов выбирают переменную, энкодер её изменяет
- Изменение шага изменения переменной при вращении энкодера - например уменьшение при зажатой кнопке и увеличение при быстром вращении
- Навигация по меню при вращении энкодера, изменение переменной при вращении зажатого энкодера
- Полноценная навигация по меню при использовании двух кнопок (одновременное удержание для перехода на следующий уровень, одновременное нажатие для возврата на предыдущий)
- И так далее

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

## Содержание
- [Установка](#install)
- [Информация](#info)
- [Документация](#docs)
- [Примеры](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **EncButton** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/EncButton/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!

<a id="info"></a>

## Информация
### Энкодер
#### Тип энкодера
Библиотека поддерживает все 4 типа *инкрементальных* энкодеров, тип можно настроить при помощи `setEncType(тип)`:
- `EB_STEP4_LOW` - активный низкий сигнал (подтяжка к VCC). Полный период (4 фазы) за один щелчок. *Установлен по умолчанию*
- `EB_STEP4_HIGH` - активный высокий сигнал (подтяжка к GND). Полный период (4 фазы) за один щелчок
- `EB_STEP2` - половина периода (2 фазы) за один щелчок
- `EB_STEP1` - четверть периода (1 фаза) за один щелчок, а также энкодеры без фиксации

![diagram](/doc/enc_type.png)  

#### Рекомендации
Для работы по сценарию "энкодер с кнопкой" рекомендую вот такие ([ссылка](https://ali.ski/cmPI2), [ссылка](https://ali.ski/sZbTK)) круглые китайские модули с распаянными цепями антидребезга (имеют тип `EB_STEP4_LOW` по классификации выше):  
![scheme](/doc/encAli.png)  

Самостоятельно обвязать энкодер можно по следующей схеме (RC фильтры на каналы энкодера + подтяжка всех пинов к VCC):  
![scheme](/doc/enc_scheme.png)  

### Кнопка
#### Уровень кнопки
Кнопка может быть подключена к микроконтроллеру двумя способами и давать при нажатии высокий или низкий сигнал. В библиотеке предусмотрена настройка `setButtonLevel(уровень)`, где уровень - активный сигнал кнопки:
- `HIGH` - кнопка подключает VCC. Установлен по умолчанию в `Virt`-библиотеках
- `LOW` - кнопка подключает GND. Установлен по умолчанию в основных библиотеках
![scheme](/doc/btn_scheme.png)  

#### Подтяжка пина
В схемах с микроконтроллерами чаще всего используется подключение кнопки к GND с подтяжкой пина к VCC. Подтяжка может быть внешней (режим пина нужно поставить `INPUT`) или внутренней (режим пина `INPUT_PULLUP`). В "реальных" проектах рекомендуется внешняя подтяжка, т.к. она менее подвержена помехам - у внутренней слишком высокое сопротивление.

### Структура библиотеки
EncButton начиная с версии 3.0 представляет собой несколько библиотек (классов) для различных сценариев использования:
- Базовые классы:
  - `VirtButton` - базовый класс виртуальной кнопки, обеспечивает все возможности кнопки
  - `VirtEncoder` - базовый класс виртуального энкодера, определяет факт и направление вращения энкодера
  - `VirtEncButton` - базовый класс виртуального энкодера с кнопкой, обеспечивает опрос энкодера с учётом кнопки, *наследует VirtButton и VirtEncoder*
- Основные классы:
  - `Button` - класс кнопки, *наследует VirtButton*
  - `Encoder` - класс энкодера, *наследует VirtEncoder*
  - `EncButton` - класс энкодера с кнопкой, *наследует VirtEncButton*
  - Версии `ButtonT`, `EncoderT`, `EncButtonT` хранят номера пинов в шаблоне для максимальной оптимизации памяти и скорости работы

> *Виртуальный* - без указания пина микроконтроллера, работает напрямую с переданным значением, например для опроса кнопок-энкодеров через расширители пинов и сдвиговые регистры.

<a id="docs"></a>

## Документация
### Дефайны настроек
Объявлять до подключения библиотеки

```cpp
// отключить счётчик энкодера [VirtEncoder, Encoder, EncButton] (экономит 4 байта оперативки)
#define EB_NO_COUNTER

// отключить буферизацию энкодера (экономит 1 байт оперативки)
#define EB_NO_BUFFER

/*
  Настройка таймаутов для всех классов
  - Заменяет таймауты константами, изменить их из программы (SetXxxTimeout()) будет нельзя
  - Настройка влияет на все объявленные в программе кнопки/энкодеры
  - Экономит 1 байт оперативки на объект за каждый таймаут
  - Показаны значения по умолчанию в мс
  - Значения не ограничены 4000мс, как при установке из программы (SetXxxTimeout())
*/
#define EB_DEB_TIME 50      // таймаут гашения дребезга кнопки (кнопка)
#define EB_CLICK_TIME 500   // таймаут ожидания кликов (кнопка)
#define EB_HOLD_TIME 500    // таймаут удержания (кнопка)
#define EB_STEP_TIME 200    // таймаут импульсного удержания (кнопка)
#define EB_FAST_TIME 30     // таймаут быстрого поворота (энкодер)
```

### Классы
> Примечание: `#include <EncButton.h>` подключает все инструменты библиотеки!

<details>
<summary>VirtButton</summary>

```cpp
// ================ НАСТРОЙКИ ================
// установить таймаут удержания, умолч. 500 (макс. 4000 мс)
void setHoldTimeout(uint16_t tout);

// установить таймаут импульсного удержания, умолч. 200 (макс. 4000 мс)
void setStepTimeout(uint16_t tout);

// установить таймаут ожидания кликов, умолч. 500 (макс. 4000 мс)
void setClickTimeout(uint16_t tout);

// установить таймаут антидребезга, умолч. 50 (макс. 255 мс)
void setDebTimeout(uint8_t tout);

// установить уровень кнопки (HIGH - кнопка замыкает VCC, LOW - замыкает GND)
// умолч. HIGH, то есть true - кнопка нажата
void setButtonLevel(bool level);

// ================== СБРОС ==================
// сбросить системные флаги (принудительно закончить обработку)
void reset();

// принудительно сбросить флаги событий
void clear();

// ================ ОБРАБОТКА ================
// обработка кнопки значением
bool tick(bool s);

// обработка виртуальной кнопки как одновременное нажатие двух других кнопок
bool tick(VirtButton& b0, VirtButton& b1);

// ================== ОПРОС ==================
// кнопка нажата [событие]
bool press();

// кнопка отпущена (в любом случае) [событие]
bool release();

// клик по кнопке (отпущена без удержания) [событие]
bool click();

// кнопка зажата (между press() и release()) [состояние]
bool pressing();

// кнопка была удержана (больше таймаута) [событие]
bool hold();

// кнопка была удержана (больше таймаута) с предварительными кликами [событие]
bool hold(uint8_t num);

// кнопка удерживается (больше таймаута) [состояние]
bool holding();

// кнопка удерживается (больше таймаута) с предварительными кликами [состояние]
bool holding(uint8_t num);

// импульсное удержание [событие]
bool step();

// импульсное удержание с предварительными кликами [событие]
bool step(uint8_t num);

// зафиксировано несколько кликов [событие]
bool hasClicks();

// зафиксировано указанное количество кликов [событие]
bool hasClicks(uint8_t num);

// получить количество кликов
uint8_t getClicks();

// кнопка отпущена после удержания [событие]
bool releaseHold();

// кнопка отпущена после удержания с предварительными кликами [событие]
bool releaseHold(uint8_t num);

// кнопка отпущена после импульсного удержания [событие]
bool releaseStep();

// кнопка отпущена после импульсного удержания с предварительными кликами [событие]
bool releaseStep(uint8_t num);

// после взаимодействия с кнопкой (или энкодером EncButton) прошло указанное время, мс [событие]
bool timeout(uint16_t tout);

// кнопка ожидает повторных кликов (между click() и hasClicks()) [состояние]
bool waiting();

// идёт обработка (между первым нажатием и после ожидания кликов) [состояние]
bool busy();

// было действие с кнопки (или энкодером EncButton), вернёт код события [событие]
uint16_t action();

```
</details>
<details>
<summary>VirtEncoder</summary>

```cpp
// ==================== НАСТРОЙКИ ====================
// инвертировать направление энкодера (умолч. 0)
void setEncReverse(bool rev);

// установить тип энкодера (EB_STEP4_LOW, EB_STEP4_HIGH, EB_STEP2, EB_STEP1)
void setEncType(uint8_t type);

// инициализация энкодера
void initEnc(bool e0, bool e1);

// инициализация энкодера совмещённым значением
void initEnc(int8_t v);

// ====================== ОПРОС ======================
// был поворот [событие]
bool turn();

// направление энкодера (1 или -1) [состояние]
int8_t dir();

// счётчик
int32_t counter;

// ==================== ОБРАБОТКА ====================
// опросить энкодер в прерывании. Вернёт 1 или -1 при вращении, 0 при остановке
int8_t tickISR(bool e0, bool e1);
int8_t tickISR(int8_t state);

// опросить энкодер. Вернёт 1 или -1 при вращении, 0 при остановке
int8_t tick(bool e0, bool e1);
int8_t tick(int8_t state);

// опросить энкодер без установки флагов на поворот (быстрее). Вернёт 1 или -1 при вращении, 0 при остановке
int8_t tickRaw(bool e0, bool e1);
int8_t tickRaw(int8_t state);
```
</details>
<details>
<summary>VirtEncButton</summary>

- Доступны функции из `VirtButton`
- Доступны функции из `VirtEncoder`

```cpp
// ================== НАСТРОЙКИ ==================
// установить таймаут быстрого поворота, мс
void setFastTimeout(uint8_t tout);

// виртуально зажать кнопку энкодера
void holdEncButton(bool state);

// виртуально переключить кнопку энкодера
void toggleEncButton();

// ==================== ОПРОС ====================
// + все функции опроса из VirtButton и VirtEncoder

// поворот направо [событие]
bool right();

// поворот налево [событие]
bool left();

// нажатый поворот направо [событие]
bool rightH();

// нажатый поворот налево [событие]
bool leftH();

// нажата кнопка энкодера [состояние]
bool encHolding();

// быстрый поворот энкодера [состояние]
bool fast();

// ==================== ОБРАБОТКА ====================
// обработка в прерывании (только энкодер). Вернёт 0 в покое, 1 или -1 при повороте
int8_t tickISR(bool e0, bool e1);

// обработка в прерывании (только энкодер). Вернёт 0 в покое, 1 или -1 при повороте
int8_t tickISR(int8_t e01);

// обработка энкодера и кнопки
bool tick(bool e0, bool e1, bool btn);

// обработка энкодера и кнопки
bool tick(int8_t e01, bool btn);
```
</details>
<details>
<summary>Button</summary>

- Доступны функции из `VirtButton`
- Режим кнопки по умолчанию - `LOW`

```cpp
Button;
Button(uint8_t pin);                // с указанием пина
Button(uint8_t npin, uint8_t mode); // + режим работы (умолч. INPUT_PULLUP)

// указать пин и его режим работы
void init(uint8_t npin, uint8_t mode);

// + все функции опроса из VirtButton
// прочитать текущее значение кнопки (без дебаунса)
bool read();

// функция обработки, вызывать в loop
bool tick();
```
</details>
<details>
<summary>ButtonT</summary>

- Доступны функции из `VirtButton`
- Режим кнопки по умолчанию - `LOW`

```cpp
ButtonT<uint8_t pin>;                 // с указанием пина
ButtonT<uint8_t pin> (uint8_t mode);  // + режим работы (умолч. INPUT_PULLUP)

// указать режим работы
void init(uint8_t mode);

// + все функции опроса из VirtButton

// прочитать текущее значение кнопки (без дебаунса)
bool read();

// функция обработки, вызывать в loop
bool tick();
```
</details>
<details>
<summary>Encoder</summary>

- Доступны функции из `VirtEncoder`

```cpp
Encoder;
Encoder(uint8_t encA, uint8_t encB);                // с указанием пинов
Encoder(uint8_t encA, uint8_t encB, uint8_t mode);  // + режим работы (умолч. INPUT)

// указать пины и их режим работы
void init(uint8_t encA, uint8_t encB, uint8_t mode);

// функция обработки для вызова в прерывании энкодера
int8_t tickISR();

// функция обработки для вызова в loop
bool tick();
```
</details>
<details>
<summary>EncoderT</summary>

- Доступны функции из `VirtEncoder`

```cpp
EncoderT<uint8_t encA, uint8_t encB>;                 // с указанием пинов
EncoderT<uint8_t encA, uint8_t encB> (uint8_t mode);  // + режим работы (умолч. INPUT)

// указать режим работы пинов
void init(uint8_t mode);

// функция обработки для вызова в прерывании энкодера
int8_t tickISR();

// функция обработки для вызова в loop
bool tick();
```
</details>
<details>
<summary>EncButton</summary>

- Доступны функции из `VirtButton`
- Доступны функции из `VirtEncoder`
- Доступны функции из `VirtEncButton`

```cpp
EncButton;

// настроить пины (энк, энк, кнопка)
EncButton(uint8_t encA, uint8_t encB, uint8_t btn);

// настроить пины (энк, энк, кнопка, pinmode энк, pinmode кнопка)
EncButton(uint8_t encA, uint8_t encB, uint8_t btn, uint8_t modeEnc = INPUT, uint8_t modeBtn = INPUT_PULLUP);

// настроить пины (энк, энк, кнопка, pinmode энк, pinmode кнопка)
void init(uint8_t encA, uint8_t encB, uint8_t btn, uint8_t modeEnc = INPUT, uint8_t modeBtn = INPUT_PULLUP);

// + все функции опроса из VirtButton, VirtEncButton и VirtEncoder

// функция обработки для вызова в прерывании энкодера
int8_t tickISR();

// функция обработки, вызывать в loop
bool tick();

// прочитать значение кнопки
bool readBtn();

// прочитать значение энкодера
int8_t readEnc();
```
</details>
<details>
<summary>EncButtonT</summary>

- Доступны функции из `VirtButton`
- Доступны функции из `VirtEncoder`
- Доступны функции из `VirtEncButton`

```cpp
EncButtonT<uint8_t encA, uint8_t encB, uint8_t btn>;    // с указанием пинов
EncButtonT<uint8_t encA, uint8_t encB, uint8_t btn> (uint8_t modeEnc = INPUT, uint8_t modeBtn = INPUT_PULLUP);  // + режим работы пинов

// настроить режим работы пинов
void init(uint8_t modeEnc = INPUT, uint8_t modeBtn = INPUT_PULLUP);

// + все функции опроса из VirtButton, VirtEncButton и VirtEncoder

// функция обработки для вызова в прерывании энкодера
int8_t tickISR();

// функция обработки, вызывать в loop
bool tick();

// прочитать значение кнопки
bool readBtn();

// прочитать значение энкодера
int8_t readEnc();
```
</details>

### Обработка и опрос
Во всех библиотеках есть общая **функция обработки** (тикер `tick`), которая получает текущий сигнал с кнопки и энкодера
- Эту функцию нужно вызывать в основном цикле программы (для виртуальных - с передачей значения)
- Функция возвращает `true` при наступлении события (для энкодера - `1` или `-1` при повороте, `0` при его отсутствии. Таким образом поворот в любую сторону расценивается как `true`)
- Есть отдельные функции для вызова в прерывании, они имеют суффикс `ISR`, см. документацию ниже

Библиотека обрабатывает сигнал внутри этой функции, результат можно получить из **функций опроса** событий. Они бывают двух типов:
- `[событие]` - функция вернёт `true` однократно при наступлении события. Сбросится после следующего вызова функции обработки (например клик, поворот энкодера)
- `[состояние]` - функция возвращает `true`, пока активно это состояние (например кнопка удерживается)

Для простоты восприятия функцию обработки нужно размещать в начале цикла, а опросы делать ниже:
```cpp
void loop() {
  btn.tick();   // опрос

  if (btn.click()) Serial.println("click"); // однократно выведет при клике
  if (btn.click()) Serial.println("click"); // тот же клик!
}
```
> В отличие от предыдущих версий библиотеки, функции опроса сбрасываются не внутри себя, а *внутри функции обработки*. Таким образом в примере выше при клике по кнопке в порт дважды выведется сообщение `click`. Это позволяет использовать функции опроса по несколько раз за текущую итерацию цикла для создания сложной логики работы программы.

### Обработка кнопки
Библиотека обрабатывает кнопку следующим образом:
- Нажатие с программным подавлением дребезга (удержание дольше таймаута deb), результат - событие `press`, состояния `pressing` и `busy`
- Удержание дольше таймаута удержания hold - событие `hold`, состояние `holding`
- Удержание дольше таймаута удержания hold + таймаута степ - импульсное событие `step`, срабатывает с периодом step
- Отпускание кнопки, результат - событие `release`, снятие состояний `pressing` и `holding`
  - Отпускание до таймаута удержания - событие `click`
  - Отпускание после удержания - событие `releaseHold`
  - Отпускание после импульсного удержания - событие `releaseStep`
- Ожидание нового клика в течение таймаута click, состояние `waiting`
- Если нового клика нет - снятие состоятия `busy`, обработка закончена
- Если ожидается `timeout` - событие timeout с указанным периодом от текущего момента

> Лучше один раз увидеть, чем сто раз прочитать. Запусти пример demo и понажимай на кнопку

#### Click
![click](/doc/click.gif)  

#### Hold
![hold](/doc/hold.gif)  

#### Step
![step](/doc/step.gif)  

Онлайн-симуляция доступна [здесь](https://wokwi.com/projects/373591584298469377)

### Обработка энкодера
- "Быстрым" поворотом считается поворот, совершённый менее чем за настроенный таймаут от предыдущего поворота
- Обработанные в прерывании повороты становятся активными после вызова функции обработки
- Буферизация поворотов энкодера в прерывании не запоминает направление поворота, а только его факт

### Обработка энкодера с кнопкой
- Поворот энкодера при зажатой кнопке снимает и блокирует все последующие события и клики, за исключением события `release`. Состояния нажатой кнопки не изменяются
- Поворот энкодера также влияет на системный таймаут (функция `timeout()`) - сработает через указанное время после поворота энкодера

### Предварительные клики
Библиотека считает количество кликов по кнопке и некоторые функции опроса могут отдельно обрабатываться с *предварительными кликами*. Например 3 клика, затем удержание. Это очень сильно расширяет возможности одной кнопки. Есть два варианта работы с такими событиями:
```cpp
  // 1
  if (btn.hold()) {
    if (btn.getClicks() == 2) Serial.println("hold 2 clicks");
  }

  // 2
  if (btn.hold(2)) Serial.println("hold 2 clicks");
```

В первом варианте можно получить количество кликов для дальнейшей обработки вручную, а во втором - библиотека сделает это сама, если количество кликов для действия заранее известно.

### Получение события
Доступно во всех классах **с кнопкой**:
- `VirtButton`
- `Button`
- `VirtEncButton`
- `EncButton`

Функция `action()` при наступлении события возвращает код события (отличный от нуля, что само по себе является индикацией наличия события):
`EB_PRESS` - нажатие на кнопку
`EB_HOLD` - кнопка удержана
`EB_STEP` - импульсное удержание
`EB_RELEASE` - кнопка отпущена
`EB_CLICK` - одиночный клик
`EB_CLICKS` - сигнал о нескольких кликах
`EB_TURN` - поворот энкодера
`EB_REL_HOLD` - кнопка отпущена после удержания
`EB_REL_HOLD_C` - кнопка отпущена после удержания с предв. кликами
`EB_REL_STEP` - кнопка отпущена после степа
`EB_REL_STEP_C` - кнопка отпущена после степа с предв. кликами

> Результат функции `action()` сбрасывается после вызова функции обработки, то есть доступен на всей итерации цикла.

### Событийное программирование
В библиотеке не реализовано подключение функции-обработчика событий, но любители коллбэков могут организовать программу например следующим образом для любого из классов, т.к. функция обработки однократно возвращает `true` при наступлении события, а `action` возвращает код события:

```cpp
EncButton eb(2, 3, 4);

void callback(EncButton& eb) {
  switch (eb.action()) {
    case EB_PRESS:
      // ...
      break;
    case EB_HOLD:
      // ...
      break;
    // ...
  }
}

void loop() {
  if (eb.tick()) callback(eb);
}
```

### Одновременное нажатие
Библиотека нативно поддерживает работу с двумя одновременно нажатыми кнопками как с третьей кнопкой. Для этого нужно:
1. Cоздать виртуальную кнопку `VirtButton`
2. Вызвать обработку реальных кнопок
3. Передать виртуальной кнопке в обработку эти кнопки (это могут быть объекты классов `VirtButton`, `Button`, `EncButton` + их `T`-версии)
4. Далее опрашивать события

```cpp
Button b0(4);
Button b1(5);
VirtButton b2;  // 1

void loop() {
  b0.tick();  // 2
  b1.tick();  // 2
  b2.tick(b0, b1);  // 3

  // 4
  if (b0.click()) Serial.println("b0 click");
  if (b1.click()) Serial.println("b1 click");
  if (b2.click()) Serial.println("b0+b1 click");
}
```

Библиотека сама "сбросит" лишние события с реальных кнопок, если они были нажаты вместе, за исключением события `press`. Таким образом получается полноценная третья кнопка из двух других с удобным опросом.

### Прерывания
Для обработки энкодера в загруженной программе нужно подключать оба его пина на аппаратные прерывания по `CHANGE` и вызывать в обработчике специальный тикер для прерывания. Основной тикер также нужно вызывать в `loop` для корреткной работы (события генерируются в основном тикере):
```cpp
// пример для ATmega328 и EncButton
EncButton eb(2, 3, 4);

void isr() {
  eb.tickISR();
}
void setup() {
  attachInterrupt(0, isr, CHANGE);
  attachInterrupt(1, isr, CHANGE);
}
void loop() {
  eb.tick();
}
```

Примечание: использование работы в прерывании позволяет корректно обрабатывать позицию энкодера и не пропустить новый поворот. Событие с поворотом, полученное из прерывания, станет доступно *после* вызова `tick` в основном цикле программы:
- Если буферизация отключена: событие `turn` активируется только один раз, независимо от количества щелчков энкодера, совершённых между двумя вызовами `tick` (щелчки обработаны в прерывании)
- Если буферизация включена: событие `turn` будет вызвано столько раз, сколько реально было щелчков энкодера, это позволяет вообще не пропускать повороты и не нагружать систему в прерывании. Буферизация *не запоминает направление* вращения ради лёгкости библиотеки, направлением внутри опроса поворота всегда будет последнее направление щелчка!

> Примечание: счётчик энкодера всегда имеет актуальное значение и может опережать буферизированные повороты в программе с большими задержками в основном цикле!

> Примечание: на разных платформах прерывания могут работать по разному (например на ESPxx - нужно добавить функции аттрибут `IRAM_ATTR`, см. документацию на свою платформу!)

### Портирование на другие платформы
В файлах utils.h/utils.cpp можно заменить Arduino-зависимые функции на другие

<a id="example"></a>

## Примеры
Остальные примеры смотри в **examples**!
<details>
<summary>Полное демо EncButton</summary>

```cpp
// #define EB_NO_COUNTER       // отключить счётчик энкодера (экономит 4 байта оперативки)
// #define EB_NO_BUFFER        // отключить буферизацию энкодера (экономит 1 байт оперативки)

// #define EB_DEB_TIME 50      // таймаут гашения дребезга кнопки (кнопка)
// #define EB_CLICK_TIME 500   // таймаут ожидания кликов (кнопка)
// #define EB_HOLD_TIME 500    // таймаут удержания (кнопка)
// #define EB_STEP_TIME 200    // таймаут импульсного удержания (кнопка)
// #define EB_FAST_TIME 30     // таймаут быстрого поворота (энкодер)

#include <EncButton.h>
EncButton eb(2, 3, 4);

void setup() {
    Serial.begin(115200);

    // показаны значения по умолчанию
    eb.setButtonLevel(LOW);
    eb.setClickTimeout(500);
    eb.setDebTimeout(50);
    eb.setHoldTimeout(500);
    eb.setStepTimeout(200);

    eb.setEncReverse(0);
    eb.setEncType(EB_STEP4_LOW);
    eb.setFastTimeout(30);
}

void loop() {
    eb.tick();

    // обработка поворота общая
    if (eb.turn()) {
        Serial.print("turn: dir ");
        Serial.print(eb.dir());
        Serial.print(", fast ");
        Serial.print(eb.fast());
        Serial.print(", hold ");
        Serial.print(eb.encHolding());
        Serial.print(", counter ");
        Serial.println(eb.counter);
    }

    // обработка поворота раздельная
    if (eb.left()) Serial.println("left");
    if (eb.right()) Serial.println("right");
    if (eb.leftH()) Serial.println("leftH");
    if (eb.rightH()) Serial.println("rightH");

    // кнопка
    if (eb.press()) Serial.println("press");
    if (eb.release()) Serial.println("release");
    if (eb.click()) Serial.println("click");

    // состояния
    // Serial.println(eb.pressing());
    // Serial.println(eb.holding());
    // Serial.println(eb.busy());
    // Serial.println(eb.waiting());

    // таймаут
    if (eb.timeout(1000)) Serial.println("timeout!");

    // удержание
    if (eb.hold()) Serial.println("hold");
    // if (eb.hold()) {
    //     Serial.print("hold + ");
    //     Serial.print(eb.getClicks());
    //     Serial.println(" clicks");
    // }

    if (eb.hold(2)) Serial.println("hold 2");
    if (eb.hold(3)) Serial.println("hold 3");

    // импульсное удержание
    if (eb.step()) Serial.println("step");
    if (eb.step(2)) Serial.println("step 2");
    if (eb.step(3)) Serial.println("step 3");

    // отпущена после импульсного удержания
    if (eb.releaseStep()) Serial.println("release step");
    if (eb.releaseStep(2)) Serial.println("release step 2");
    if (eb.releaseStep(3)) Serial.println("release step 3");

    // отпущена после удержания
    if (eb.releaseHold()) Serial.println("release hold");
    if (eb.releaseHold(2)) Serial.println("release hold 2");

    // проверка на количество кликов
    if (eb.hasClicks(1)) Serial.println("has 1 clicks");
    if (eb.hasClicks(3)) Serial.println("has 3 clicks");

    // вывести количество кликов
    if (eb.hasClicks()) {
        Serial.print("has clicks: ");
        Serial.println(eb.clicks);
    }
}
```
</details>
<details>
<summary>Демо EncButton, общий обработчик</summary>

```cpp
#include <EncButton.h>
EncButton eb(2, 3, 4);

void setup() {
    Serial.begin(115200);
}

void cb() {
    Serial.print("callback: ");
    switch (eb.action()) {
        case EB_PRESS:
            Serial.println("press");
            break;
        case EB_HOLD:
            Serial.println("hold");
            break;
        case EB_STEP:
            Serial.println("step");
            break;
        case EB_RELEASE:
            Serial.println("release");
            break;
        case EB_CLICK:
            Serial.println("click");
            break;
        case EB_CLICKS:
            Serial.print("clicks ");
            Serial.println(eb.getClicks());
            break;
        case EB_TURN:
            Serial.print("turn ");
            Serial.print(eb.dir());
            Serial.print(" ");
            Serial.print(eb.fast());
            Serial.print(" ");
            Serial.println(eb.encHolding());
            break;
        case EB_REL_HOLD:
            Serial.println("release hold");
            break;
        case EB_REL_HOLD_C:
            Serial.print("release hold clicks ");
            Serial.println(eb.getClicks());
            break;
        case EB_REL_STEP:
            Serial.println("release step");
            break;
        case EB_REL_STEP_C:
            Serial.print("release step clicks ");
            Serial.println(eb.getClicks());
            break;
    }
}

void loop() {
    if (eb.tick()) cb();
}
```
</details>
<details>
<summary>Все типы кнопок</summary>

```cpp
#include <EncButton.h>

Button btn(4);
ButtonT<5> btnt;
VirtButton btnc;

void setup() {
    Serial.begin(115200);
}

void loop() {
    // Button
    btn.tick();
    if (btn.click()) Serial.println("btn click");

    // ButtonT
    btnt.tick();
    if (btnt.click()) Serial.println("btnt click");

    // VirtButton
    btnc.tick(!digitalRead(4));  // передать логическое значение
    if (btn.click()) Serial.println("btnc click");
}
```
</details>
<details>
<summary>Все типы энкодеров</summary>

```cpp
#include <EncButton.h>

Encoder enc(2, 3);
EncoderT<5, 6> enct;
VirtEncoder encc;

void setup() {
    Serial.begin(115200);
}

void loop() {
    // опрос одинаковый для всех, 3 способа:

    // 1
    // tick вернёт 1 или -1, значит это шаг
    if (enc.tick()) Serial.println(enc.counter);

    // 2
    // можно опросить через turn()
    enct.tick();
    if (enct.turn()) Serial.println(enct.dir());

    // 3
    // можно не использовать опросные функции, а получить направление напрямую
    int8_t v = encc.tick(digitalRead(2), digitalRead(3));
    if (v) Serial.println(v);  // выведет 1 или -1
}
```
</details>

<a id="versions"></a>
## Версии
<details>
<summary>Старые</summary>

- v1.1 - пуллап отдельныи методом
- v1.2 - можно передать конструктору параметр INPUT_PULLUP / INPUT(умолч)
- v1.3 - виртуальное зажатие кнопки энкодера вынесено в отдельную функцию + мелкие улучшения
- v1.4 - обработка нажатия и отпускания кнопки
- v1.5 - добавлен виртуальный режим
- v1.6 - оптимизация работы в прерывании
- v1.6.1 - подтяжка по умолчанию INPUT_PULLUP
- v1.7 - большая оптимизация памяти, переделан FastIO
- v1.8 - индивидуальная настройка таймаута удержания кнопки (была общая на всех)
- v1.8.1 - убран FastIO
- v1.9 - добавлена отдельная отработка нажатого поворота и запрос направления
- v1.10 - улучшил обработку released, облегчил вес в режиме callback и исправил баги
- v1.11 - ещё больше всякой оптимизации + настройка уровня кнопки
- v1.11.1 - совместимость Digispark
- v1.12 - добавил более точный алгоритм энкодера EB_BETTER_ENC
- v1.13 - добавлен экспериментальный EncButton2
- v1.14 - добавлена releaseStep(). Отпускание кнопки внесено в дебаунс
- v1.15 - добавлен setPins() для EncButton2
- v1.16 - добавлен режим EB_HALFSTEP_ENC для полушаговых энкодеров
- v1.17 - добавлен step с предварительными кликами
- v1.18 - не считаем клики после активации step. hold() и held() тоже могут принимать предварительные клики. Переделан и улучшен дебаунс
- v1.18.1 - исправлена ошибка в releaseStep() (не возвращала результат)
- v1.18.2 - fix compiler warnings
- v1.19 - оптимизация скорости, уменьшен вес в sram
- v1.19.1 - ещё чутка увеличена производительность
- v1.19.2 - ещё немного увеличена производительность, спасибо XRay3D
- v1.19.3 - сделал высокий уровень кнопки по умолчанию в виртуальном режиме
- v1.19.4 - фикс EncButton2
- v1.20 - исправлена критическая ошибка в EncButton2
- v1.21 - EB_HALFSTEP_ENC теперь работает для обычного режима
- v1.22 - улучшен EB_HALFSTEP_ENC для обычного режима
- v1.23 - getDir() заменил на dir()
- v2.0 
    - Алгоритм EB_BETTER_ENC оптимизирован и установлен по умолчанию, дефайн EB_BETTER_ENC упразднён
    - Добавлен setEncType() для настройки типа энкодера из программы, дефайн EB_HALFSTEP_ENC упразднён
    - Добавлен setEncReverse() для смены направления энкодера из программы
    - Добавлен setStepTimeout() для установки периода импульсного удержания, дефайн EB_STEP упразднён
    - Мелкие улучшения и оптимизация
</details>

- v3.0
  - Библиотека переписана с нуля, с предыдущими версиями несовместима!
    - Полностью другая инициализация объекта
    - Переименованы: hold()->holding(), held()->hold()
  - Оптимизация Flash памяти: библиотека весит меньше, в некоторых сценариях - на несколько килобайт
  - Оптимизация скорости выполнения кода, в том числе в прерывании
  - На несколько байт меньше оперативной памяти, несколько уровней оптимизации на выбор
  - Более простое, понятное и удобное использование
  - Более читаемый исходный код
  - Разбитие на классы для использования в разных сценариях
  - Новые функции, возможности и обработчики для кнопки и энкодера
  - Буферизация энкодера в прерывании
  - Нативная обработка двух одновременно нажимаемых кнопок как третьей кнопки
  - Поддержка 4-х типов энкодеров
  - Переписана документация
  - EncButton теперь заменяет GyverLibs/VirtualButton (архивирована)
    
<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!

При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
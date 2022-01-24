![](https://img.shields.io/badge/License-MIT-green.svg)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-red.svg)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/youtube/channel/subscribers/UCgtAOyEQdAyjvm9ATCi_Aig?style=social)](https://www.youtube.com/channel/UCgtAOyEQdAyjvm9ATCi_Aig)
# EncButton
Ультра лёгкая и быстрая библиотека для энкодера, энкодера с кнопкой или просто кнопки
- Максимально быстрое чтение пинов для AVR (ATmega328/ATmega168, ATtiny85/ATtiny13)
- Максимально лёгкий вес
- Быстрые и лёгкие алгоритмы опроса кнопки и энкодера
- Энкодер: обычный поворот, нажатый поворот, быстрый поворот, доступ к счётчику
- Кнопка: антидребезг, клик, несколько кликов, счётчик кликов, удержание, режим импульсного удержания
- Подключение - **только с подтяжкой к питанию** (внешней или внутренней)!
- Опциональный режим с обработчиками callback (+24 байта SRAM на каждый экземпляр)
- Виртуальный режим (кнопка, энк, энк с кнопкой)

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

## Содержание
- [Установка](#install)
- [Железо](#hardware)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
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

<a id="hardware"></a>
## Железо
Для работы по сценарию "энкодер с кнопкой" рекомендую вот такие ([ссылка](https://ali.ski/CYir4), [ссылка](https://ali.ski/49q5hy)) круглые китайские модули с распаянными цепями антидребезга:  
![scheme](/doc/encAli.png)

Самостоятельно обвязать энкодер можно по следующей схеме (RC фильтры на каналы энкодера + подтяжка всех пинов к VCC):  
![scheme](/doc/enc.png)

## Производительность
Время холостого выполнения функции tick() при реальном устройстве (кнопка/энкодер подключены к пинам МК) на ATmega328, библиотека EncButton:
| Режим | Время, мкс |
| ----- | ---------- |
| Энкодер + кнопка | 7.4 |
| Энкодер | 3.1 |
| Кнопка | 5.2 |

<a id="init"></a>
## Инициализация
**Если нужен массив кнопок/энкодеров, используй EncButton2!**
<details>
<summary>Инициализация EncButton</summary>

```cpp
// ============== БАЗОВАЯ =============
EncButton<MODE, A, B, KEY> enc;     // энкодер с кнопкой
EncButton<MODE, A, B> enc;          // просто энкодер
EncButton<MODE, KEY> btn;           // просто кнопка
// A, B, KEY: номера пинов
// MODE: EB_TICK или EB_CALLBACK - режим работы ручной или с обработчиками
// для изменения направления энкодера поменяй A и B при инициализации

// ============ ПОДКЛЮЧЕНИЕ ============
// По умолчанию пины настраиваются в INPUT_PULLUP
// Если используется внешняя подтяжка - лучше перевести в INPUT
EncButton<...> enc(INPUT);

// ========= ВИРТУАЛЬНЫЙ РЕЖИМ =========
EncButton<MODE, VIRT_BTN> enc;     // виртуальная кнопка
EncButton<MODE, VIRT_ENCBTN> enc;  // виртуальный энк с кнопкой
EncButton<MODE, VIRT_ENC> enc;     // виртуальный энк
// в tick нужно будет передавать виртуальное значение, см. пример
```
</details>
<details>
<summary>Инициализация EncButton2</summary>

Хранит пины НЕ в шаблоне, а как член класса. Всё кроме инициализации такое же как у EncButton!
```cpp
// ================ TICK ===============
EncButton2<EB_ENCBTN> enc(INPUT, A, B, KEY);    // энкодер с кнопкой
EncButton2<EB_ENC> enc(INPUT, A, B);            // просто энкодер
EncButton2<EB_BTN> enc(INPUT, KEY);             // просто кнопка
// режим пинов INPUT/INPUT_PULLUP

// ============== CALLBACK =============
EncButton2<EB_ENCBTN, EB_CALLBACK> enc(INPUT, A, B, KEY);    // энкодер с кнопкой
EncButton2<EB_ENC, EB_CALLBACK> enc(INPUT, A, B);            // просто энкодер
EncButton2<EB_BTN, EB_CALLBACK> enc(INPUT, KEY);             // просто кнопка
// режим пинов INPUT/INPUT_PULLUP

// ============== VIRT TICK ============
EncButton2<VIRT_ENCBTN> enc;        // энкодер с кнопкой
EncButton2<VIRT_ENC> enc;           // просто энкодер
EncButton2<VIRT_BTN> enc;           // просто кнопка

// ============ VIRT CALLBACK ==========
EncButton2<VIRT_ENCBTN, EB_CALLBACK> enc;   // энкодер с кнопкой
EncButton2<VIRT_ENC, EB_CALLBACK> enc;      // просто энкодер
EncButton2<VIRT_BTN, EB_CALLBACK> enc;      // просто кнопка
```
</details>
<details>
<summary>Массив экземпляров EncButton2</summary>

```cpp
EncButton2<EB_ENCBTN> enc[количество];
EncButton2<EB_ENC> enc[количество];
EncButton2<EB_BTN> enc[количество];

EncButton2<EB_ENCBTN, EB_CALLBACK> enc[количество];
EncButton2<EB_ENC, EB_CALLBACK> enc[количество];
EncButton2<EB_BTN, EB_CALLBACK> enc[количество];
// и так далее
// Задавать пины можно через setPins()
setPins(uint8_t mode, uint8_t P1, uint8_t P2, uint8_t P3);
// mode - INPUT/INPUT_PULLUP (для всех пинов)
// указываем только нужные для выбранного режима пины:
// EB_ENCBTN - A, B, KEY
// EB_ENC - A, B
// EB_BTN - KEY
// см. пример EucButton2_array
```
</details>

## Документация
<details>
<summary>ПОЛНОЕ ОПИСАНИЕ КЛАССА</summary>

```cpp
// =============== SETTINGS ==============
void pullUp();                      // подтянуть все пины внутренней подтяжкой
void holdEncButton(bool state);     // виртуально зажать кнопку энкодера
void setHoldTimeout(int tout);      // установить время удержания кнопки, мс (до 30 000)
void setButtonLevel(bool level);    // уровень кнопки: LOW - кнопка подключает GND (по умолч.), HIGH - кнопка подключает VCC

// ================= TICK ================
// тикер, вызывать как можно чаще или в прерывании
// вернёт отличное от нуля значение, если произошло какое то событие (см. пример optimisation)
uint8_t tick();

// tick(uint8_t s1 = 0, uint8_t s2 = 0, uint8_t key = 0)
// может принимать виртуальный сигнал при режиме VIRT_xxx:
// (сигнал кнопки)
// (сигнал энкодера А, сигнал энкодера B)
// (сигнал энкодера А, сигнал энкодера B, сигнал кнопки)

// Тикер для прерывания в режиме callback. Не вызывает подключенные функции!
// Требует наличие обычного tick() в loop() (см. примеры tickISR и callbackISR)
uint8_t tickISR();

// проверяет и вызывает подключенные функции для режима callback
// Встроено в tick(), но вынесено отдельной функцией для нестандартных сценариев работы
void checkCallback();

// =============== STATUS ================
uint8_t getState();     // получить статус кнопки/энкодера
void resetState();      // сбросить статус

// =============== ENCODER ===============
bool turn();        // поворот на один щелчок в любую сторону
bool turnH();       // поворот на один щелчок в любую сторону с зажатой кнопкой
bool fast();        // быстрый поворот на один щелчок в любую сторону
bool right();       // поворот на один щелчок направо
bool left();        // поворот на один щелчок налево
bool rightH();      // поворот на один щелчок направо с зажатой кнопкой
bool leftH();       // поворот на один щелчок налево с зажатой кнопкой
int8_t getDir();    // направление последнего поворота, 1 или -1
int counter;        // доступ к счётчику энкодера

// ================ BUTTON ================
bool state();       // текущее состояние кнопки (true нажата, false не нажата)
bool press();       // кнопка была нажата [однократное срабатывание]
bool release();     // кнопка была отпущена [однократное срабатывание]
bool click();       // клик (нажата и отпущена) [однократное срабатывание]
bool held();        // кнопка была удержана [однократное срабатывание]
bool hold();        // кнопка удерживается [постоянное срабатывание]
bool step();        // режим импульсного удержания
bool step(uint8_t clicks);    // режим импульсного удержания с предварительным накликиванием
bool releaseStep(); // отпущена после режима step
bool releaseStep(uint8_t clicks); // отпущена после режима step с предварительным накликиванием
uint8_t clicks;     // доступ к счётчику кликов
uint8_t hasClicks();            // вернёт количество кликов, если они есть
bool hasClicks(uint8_t num);    // проверка на наличие указанного количества кликов

// =============== CALLBACK ===============
void attach(eb_callback type, void (*handler)());       // подключить обработчик
void detach(eb_callback type);                          // отключить обработчик
void attachClicks(uint8_t amount, void (*handler)());   // подключить обработчик на количество кликов (может быть только один!)
void detachClicks();                                    // отключить обработчик на количество кликов

// eb_callback может быть:
TURN_HANDLER
TURN_H_HANDLER
RIGHT_HANDLER
LEFT_HANDLER
RIGHT_H_HANDLER
LEFT_H_HANDLER
CLICK_HANDLER
HOLDED_HANDLER
STEP_HANDLER
HOLD_HANDLER
CLICKS_HANDLER
PRESS_HANDLER
RELEASE_HANDLER
```

**Дополнительно у EncButton2**
```cpp
void pullUp();          // здесь не реализована!

void setPins(uint8_t mode, uint8_t P1, uint8_t P2, uint8_t P3);     // настроить пины
// mode - INPUT/INPUT_PULLUP (для всех пинов)
// указываем только нужные для выбранного режима пины:
// EB_ENCBTN - (A, B, KEY)
// EB_ENC - (A, B)
// EB_BTN - (KEY)
// см. пример EucButton2_array
```
</details>

### Заметки
- Библиотека универсальная, но сделана с упором на максимальную оптимизацию памяти при работе во всех режимах внутри одного класса, поэтому используется шаблон и дефайны
    - При создании объекта с разным количеством пинов (энкодер, кнопка, энкодер с кнопкой) библиотека будет компилироваться по разному, ненужный код будет вырезан. Это позволяет экономить Flash память.
    - То же самое касается режимов работы TICK/CALLBACK, при использовании TICK весь относящийся к CALLBACK код вырезается компилятором
- Два алгоритма опроса энкодера, обычный и точный. Точный использует на 16 байт больше SRAM памяти (на всю библиотеку), но позволяет работать даже с низкокачественными и убитыми энкодерами
    - Точный алгоритм активируется добавлением `#define EB_BETTER_ENC` перед подключением библиотеки
- Версия библиотеки *EncButton2.h* хранит номера пинов в классе. Используйте эту версию для создания массива объектов EncButton!

<a id="usage"></a>
## Особенности и сценарии использования
### Дефайны настроек
```cpp
// дефайнить ПЕРЕД ПОДКЛЮЧЕНИЕМ БИБЛИОТЕКИ, показаны значения по умолчанию (если они есть)
// энкодер
#define EB_FAST 30      // таймаут быстрого поворота, мс
#define EB_BETTER_ENC   // улучшенный алгоритм опроса энкодера. Добавит 16 байт SRAM при подключении библиотеки
#define EB_HALFSTEP_ENC // режим опроса полушагового энкодера (включи, если твой энкодер делает два тика за один)

// кнопка
#define EB_DEB 50       // дебаунс кнопки, мс
#define EB_STEP 500     // период срабатывания степ, мс
#define EB_CLICK 400    // таймаут накликивания, мс
#define EB_HOLD 1000    // таймаут удержания кнопки (можно переназначить setHoldTimeout() из программы), мс
```

### Режим tick
- Опрос пинов энкодера/кнопки и расчёт таймаутов осуществляется внутри функции `tick()`. Эту функцию нужно однократно вызывать в основном цикле программы.
- Для повышения качества обработки энкодера/кнопки в загруженной программе (чтобы не пропустить поворот или клик) рекомендуется продублировать опрос в прерывании по *CHANGE*: внутри обработчика прерывания вызываем специальный тикер `tickISR()`, и в основном цикле программы оставляем обычный `tick()`. Он нужен для того, чтобы корректно считались все таймауты.  
- `tick()` возвращает текущий статус энкодера/кнопки:
  - 0 - никаких действий не было
  - 1 - left + turn
  - 2 - right + turn
  - 3 - leftH + turnH
  - 4 - rightH + turnH
  - 5 - click
  - 6 - held
  - 7 - step
  - 8 - press  
Это позволяет например производить дальнейший опрос действий кнопки/энкодера только по факту их совершения: можно поместить весь опрос в блок `if (enc.tick()) {}`. В конце рекомендуется вызвать `resetState()` для сборса неопрошенных флагов, чтобы `tick()` перестал "сигналить" о действии. Подробнее смотри в примере *optimisation*.
- Основная идея работы: "тикнули", а затем вручную через условия опрашиваем нужные действия кнопки/энкодера. Почти все функции опроса имеют механизм "однократного срабатывания", то есть возвращают `true` и автоматически сбрасываются в `false` до наступления следующего события. Таким образом конструкция `if (btn.click())` позволяет выполнить какой-то блок кода однократно по клику. Подробнее разберём ниже.

#### Кнопка
- `press()` - кнопка была нажата. *[однократно вернёт true]*
- `release()` - кнопка была отпущена. *[однократно вернёт true]*
- `click()` - кнопка была кликнута, т.е. нажата и отпущена до таймаута удержания. *[однократно вернёт true]*
- `held()` - кнопка была удержана дольше таймаута удержания. *[однократно вернёт true]*
- `held(clicks)` - то же самое, но функция принимает количество кликов, сделанных до удержания. Примечание: held() без аргумента перехватит вызов! См. пример *preClicks*. *[однократно вернёт true]*
- `hold()` - кнопка была удержана дольше таймаута удержания. *[возвращает true, пока удерживается]*
- `hold(clicks)` - то же самое, но функция принимает количество кликов, сделанных до удержания. Примечание: hold() без аргумента перехватит вызов! См. пример *preClicks*. *[возвращает true, пока удерживается]*
- `step()` - режим "импульсного удержания": после удержания кнопки дольше таймаута данная функция *[возвращает true с периодом EB_STEP]*. Удобно использовать для пошагового изменения переменных: `if (btn.step()) val++;`.
- `step(clicks)` - то же самое, но функция принимает количество кликов, сделанных до удержания. Примечание: step() без аргумента перехватит вызов! См. пример *StepMode* и *preClicks*.
- `releaseStep()` - кнопка была отпущена после импульсного удержания. Может использоваться для изменения знака инкремента переменной. См. пример *StepMode*. *[однократно вернёт true]*
- `releaseStep(clicks)` - то же самое, но функция принимает количество кликов, сделанных до удержания. Примечание: releaseStep() без аргумента перехватит вызов! См. пример *StepMode* и *preClicks*. *[однократно вернёт true]*
- `hasClicks(clicks)` - было сделано указанное количество кликов с периодом менее *EB_CLICK*. *[однократно вернёт true]*
- `state()` - возвращает теукщее состояние кнопки (сигнал с пина, без антидребезга): `true` - нажата, `false` - не нажата.
- `hasClicks()` - вернёт количество кликов, сделанных с периодом менее *EB_CLICK*. В противном случае вернёт 0.
- `uint8_t clicks` - публичная переменная (член класса), хранит количество сделанных кликов с периодом менее *EB_CLICK*. Сбрасывается в 0 после нового клика.
![diagram](/doc/diagram.png)

#### Энкодер
- `turn()` - поворот на один щелчок в любую сторону. *[однократно вернёт true]*
- `turnH()` - поворот на один щелчок в любую сторону с зажатой кнопкой. *[однократно вернёт true]*
- `fast()` - был совершён быстрый поворот (с периодом менее *EB_FAST* мс) на один щелчок в любую сторону. *[возвращает true, пока энкодер крутится быстро]*
- `right()` - поворот на один щелчок направо. *[однократно вернёт true]*
- `left()` - поворот на один щелчок налево. *[однократно вернёт true]*
- `rightH()` - поворот на один щелчок направо с зажатой кнопкой. *[однократно вернёт true]*
- `leftH()` - поворот на один щелчок налево с зажатой кнопкой. *[однократно вернёт true]*
- `getDir()` - направление последнего поворота, 1 или -1.
- `int16_t counter` - публичная переменная (член класса), хранит счётчик энкодера.

### Режим callback
- В данном режиме можно подключить свою функцию-обработчик на любое действие кнопки/энкодера. Они будут автоматически вызываться при наступлении события.
- Для работы нужно вызывать `tick()` в основном цикле программы, а также можно продублировать `tickISR()` в прерывании по *CHANGE* для улучшения точности обработки энкодера.
- При работе в прерывании подключенные функции вызываются из `tick()`, т.е. из основного цикла программы. В `tickISR()` происходит только обработка алгоритмов библиотеки!
- Смотри пример *callbackMode*
```cpp
void attach(type, func);                   // подключить обработчик
void detach(type);                         // отключить обработчик
void attachClicks(uint8_t amount, func);   // подключить обработчик на количество кликов (может быть только один!)
void detachClicks();                       // отключить обработчик на количество кликов
```
Где `type` - тип события:
- *TURN_HANDLER* - поворот
- *TURN_H_HANDLER* - нажатый поворот
- *RIGHT_HANDLER* - поворот направо
- *LEFT_HANDLER* - поворот налево
- *RIGHT_H_HANDLER* - нажатый поворот направо
- *LEFT_H_HANDLER* - нажатый поворот налево
- *PRESS_HANDLER* - нажатие
- *RELEASE_HANDLER* - отпускание
- *CLICK_HANDLER* - клик
- *HOLDED_HANDLER* - удержание (однократное срабатывание)
- *HOLD_HANDLER* - удержание (постоянное срабатывание)
- *STEP_HANDLER* - импульсное удержание
- *CLICKS_HANDLER* - несколько кликов

### Виртуальный режим
Виртуальный режим позволяет получить все возможности библиотеки EncButton в ситуациях, когда кнопка не подключена напрямую к микроконтроллеру, либо для её опроса используется другая библиотека:
- Аналоговая клавиатура (например через библиотеку [AnalogKey](https://github.com/GyverLibs/AnalogKey))
- Матричная клавиатура (например через библиотеку [SimpleKeypad](https://github.com/maximebohrer/SimpleKeypad))
- Кнопки или энкодеры, подключенные через расширители пинов или сдвиговые регистры  

Для работы нужно передать в `tick()` текущие состояния "пинов" кнопки/энкодера: `tick(s1, s2, s3)` в следующем порядке
- Кнопка - (сигнал кнопки)
- Энкодер - (сигнал энкодера А, сигнал энкодера B)
- Энкодер с кнопкой - (сигнал энкодера А, сигнал энкодера B, сигнал кнопки)

### Настройки
```cpp
void pullUp();                      // подтянуть все пины внутренней подтяжкой
void holdEncButton(bool state);     // виртуально зажать кнопку энкодера
void setHoldTimeout(int tout);      // установить время удержания кнопки, мс (до 30 000)
void setButtonLevel(bool level);    // уровень кнопки: LOW - кнопка подключает GND (по умолч.), HIGH - кнопка подключает VCC
```

<a id="example"></a>
## Примеры
### Полное демо tick
Остальные примеры смотри в **examples**!
```cpp
// Пример с прямой работой библиотеки

#include <EncButton.h>
EncButton<EB_TICK, 2, 3, 4> enc;  // энкодер с кнопкой <A, B, KEY>
//EncButton<EB_TICK, 2, 3> enc;     // просто энкодер <A, B>
//EncButton<EB_TICK, 4> enc;        // просто кнопка <KEY>

void setup() {
  Serial.begin(9600);
  // ещё настройки
  //enc.counter = 100;        // изменение счётчика энкодера
  //enc.setHoldTimeout(500);  // установка таймаута удержания кнопки
  //enc.setButtonLevel(HIGH); // LOW - кнопка подключает GND (умолч.), HIGH - кнопка подключает VCC
}

void loop() {
  enc.tick();                       // опрос происходит здесь

  // =============== ЭНКОДЕР ===============
  // обычный поворот
  if (enc.turn()) {
    Serial.println("turn");

    // можно опросить ещё:
    //Serial.println(enc.counter);  // вывести счётчик
    //Serial.println(enc.fast());   // проверить быстрый поворот
    Serial.println(enc.getDir()); // направление поворота
  }

  // "нажатый поворот"
  if (enc.turnH()) {
    Serial.println("hold + turn");

    // можно опросить ещё:
    //Serial.println(enc.counter);  // вывести счётчик
    //Serial.println(enc.fast());   // проверить быстрый поворот
    Serial.println(enc.getDir()); // направление поворота
  }

  if (enc.left()) Serial.println("left");     // поворот налево
  if (enc.right()) Serial.println("right");   // поворот направо
  if (enc.leftH()) Serial.println("leftH");   // нажатый поворот налево
  if (enc.rightH()) Serial.println("rightH"); // нажатый поворот направо

  // =============== КНОПКА ===============
  if (enc.press()) Serial.println("press");
  if (enc.click()) Serial.println("click");
  if (enc.release()) Serial.println("release");

  if (enc.held()) Serial.println("held");     // однократно вернёт true при удержании
  //if (enc.hold()) Serial.println("hold");   // будет постоянно возвращать true после удержания
  if (enc.step()) Serial.println("step");     // импульсное удержание
  if (enc.releaseStep()) Serial.println("release step");  // отпущена после импульсного удержания
  
  // проверка на количество кликов
  if (enc.hasClicks(1)) Serial.println("action 1 clicks");
  if (enc.hasClicks(2)) Serial.println("action 2 clicks");
  if (enc.hasClicks(3)) Serial.println("action 3 clicks");
  if (enc.hasClicks(5)) Serial.println("action 5 clicks");

  // вывести количество кликов
  if (enc.hasClicks()) {
    Serial.print("has clicks ");
    Serial.println(enc.clicks);
  }
}
```

### Массив кнопок EncButton2
```cpp
// объявляем массив кнопок
#define BTN_AMOUNT 5
#include <EncButton2.h>
EncButton2<EB_BTN> btn[BTN_AMOUNT];

void setup() {
  Serial.begin(9600);
  btn[0].setPins(INPUT_PULLUP, D3);
  btn[1].setPins(INPUT_PULLUP, D2);
}

void loop() {
  for (int i = 0; i < BTN_AMOUNT; i++) btn[i].tick();
  for (int i = 0; i < BTN_AMOUNT; i++) {
    if (btn[i].click()) {
      Serial.print("click btn: ");
      Serial.println(i);
    }
  }
}
```

### Одна кнопка управляет несколькими переменными
```cpp
// используем одну КНОПКУ для удобного изменения трёх переменных
// первая - один клик, затем удержание (нажал-отпустил-нажал-держим)
// вторая - два клика, затем удержание
// третья - три клика, затем удержание
// смотри монитор порта

#include <EncButton.h>
EncButton<EB_TICK, 3> btn;

// переменные для изменения
int val_a, val_b, val_c;

// шаги изменения (signed)
int8_t step_a = 1;
int8_t step_b = 5;
int8_t step_c = 10;

void setup() {
  Serial.begin(9600);
}

void loop() {
  btn.tick();

  // передаём количество предварительных кликов
  if (btn.step(1)) {
    val_a += step_a;
    Serial.print("val_a: ");
    Serial.println(val_a);
  }
  if (btn.step(2)) {
    val_b += step_b;
    Serial.print("val_b: ");
    Serial.println(val_b);
  }
  if (btn.step(3)) {
    val_c += step_c;
    Serial.print("val_c: ");
    Serial.println(val_c);
  }

  // разворачиваем шаг для изменения в обратную сторону
  // передаём количество предварительных кликов
  if (btn.releaseStep(1)) step_a = -step_a;
  if (btn.releaseStep(2)) step_b = -step_b;
  if (btn.releaseStep(3)) step_c = -step_c;
}
```

<a id="versions"></a>
## Версии
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
- v1.18 - не считаем клики после активации step. held() и hold() тоже могут принимать предварительные клики. Переделан и улучшен дебаунс
- v1.18.1 - исправлена ошибка в releaseStep() (не возвращала результат)
- v1.18.2 - fix compiler warnings

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!

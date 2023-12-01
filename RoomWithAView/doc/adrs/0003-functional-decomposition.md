# 4. functional-decomposition

Date: 2023-11-30

## Status

Accepted

## Context

Предложите как минимум 2 способа функциональной декомпозиции системы.
Для каждого из решений проведите оценку модифицируемости, сравнительный анализ и вывод.

<!-- TOC -->
* [4. functional-decomposition](#4-functional-decomposition)
  * [Status](#status)
  * [Context](#context)
  * [Decision](#decision)
    * [Флоу:](#флоу)
    * [Сценарии и функции:](#сценарии-и-функции)
    * [Декомпозиция контекстами:](#декомпозиция-контекстами)
    * [Декомпозиция функциональными блоками:](#декомпозиция-функциональными-блоками)
  * [Consequences](#consequences)
    * [Сценарии изменений:](#сценарии-изменений)
    * [Оценка вероятности изменений и их влияние на существующую декомпозицию:](#оценка-вероятности-изменений-и-их-влияние-на-существующую-декомпозицию)
    * [Вывод:](#вывод)
<!-- TOC -->
## Decision

Выделим основные сценарии использования системы и их основе функционально декомпозируем нашу систему.

### Флоу:

**клиент** регистрируется -> выбирает номер -> бронирует -> оплачивает (или оплата по факту)

**клиент** заезжает в номер -> клиент использует возможности номера -> клиент выезжает -> клиент ставит оценку

**админ** бронирует номер по телефону -> отправляет ссылку на оплату -> клиент оплачивает (или оплата по факту)

**персонал** видит что номер нуждается в уборке -> убирает номер -> фиксирует уборку в системе

### Сценарии и функции:

1. Функции регистрации клиентов:

     Гости могут зарегистрироваться через Интернет, мобильные устройства, телефонный звонок или лично в отеле. 
2. Управление статусом номеров:

     Система поддерживает статус номера (забронирован, доступен, готов к уборке и т.д.). Сотрудники могут отмечать, когда номер будет доступен для следующего бронирования.
3. Функциональность управления уборкой:

     Персонал по уборке и техническому обслуживанию может использовать систему для направления в различные помещения в зависимости от приоритета и необходимости бронирования, используя запатентованные устройства на тележках для уборки.
4. Функции бронирования номеров:

     Система обеспечивает стандартные функции бронирования, включая обработку платежей, регистрационную информацию и другие сопутствующие аспекты через существующую систему бронирования. Гости могут выбрать тип номера (стандарт, делюкс, люкс) или конкретный номер, ознакомившись с фотографиями и местоположением каждого номера.
5. Интеграция с умными технологиями:

     Система интегрируется с умными замками в номерах, позволяя гостям открывать двери с помощью мобильных устройств.
6. Мобильное приложение для гостей:

     Мобильное приложение позволяет гостям бронировать и управлять номерами через мобильные устройства
7. Программа лояльности:

     Клиенты используют программу лояльности с бонусами и скидками что стимулирует повторные бронирования и предоставляет привилегии постоянным гостям.
8. Оценка номеров клиентами:

     Гости могут оценивать номера и делиться своим опытом, что помогает улучшать качество обслуживания и адаптировать предложение к предпочтениям гостей.
9. Виртуальные туры по номерам:

     Гости могут просматривать виртуальные туры по номерам, что помогает им более подробно ознакомиться с атмосферой и дизайном номеров.
10. Оплата номера:

    Оплатить номер можно через существующую систему платежей (необходима интеграция)

**На основе этих сценариев декомпозируем систему:**

### Декомпозиция контекстами:

* Контекст **Клиент** (регистрация , бронирование,  оплата, рейтинг)
* Контекст **Админ** (регистрация, управление бронированиями, прием оплаты, связь с персоналом, рейтинг)
* Контекст **Персонал** (регистрация, уборка, помощь клиентам, рейтинг)
* Контекст **Отель** (предоставление информации по номерам, рейтинг)
* Контекст **Комната** (все о номере: фото, оценка, состояние, датчики, замки, ключи, рейтинг)

### Декомпозиция функциональными блоками:
**Основной модуль**
*    Система регистрации (IAM подсистема)- можно разрабатывать отдельной командой и интегрировать с существующей системой (предоставляет API)
*    Система бронирования - предоставляет основные функции и управляет бронированиями (предоставляет API)
     * Подсистема управления номером (состояние/уборка)
     * Подсистема управления бронированием (создание отмена...)
*    Система оплаты - можно разрабатывать отдельной командой и интегрировать с существующей системой и платежками (предоставляет API)


**Внешний модуль** 
*    Мобильное приложение - можно разрабатывать отдельной командой на основе задокументированного API
*    Веб приложение - можно разрабатывать отдельной командой на основе задокументированного API
*    Админ-веб портал - можно разрабатывать отдельной командой на основе задокументированного API

**Доп модули (при реализации сценариев изменений)**

* Система лояльности - управление лояльностью клиента (интеграция с "Основным модулем")
* Система интеграции с умными устройствами - подсистема метрик, уведомлений и алертов о состоянии номера (интеграция с "_подситемой управления номером_") 
* Подсистема рейтинга номеров/отелей
 
## Consequences

### Сценарии изменений:
Предположим некоторые возможные изменения в системе в процессе ее реализации, а также после выпуска

1. Регистрация еще и через чат бота в телеграмм.
2. Добавление систему лояльности клиентов
3. Добавление систему оценки номеров клиентами в дополнение к остальным параметрам номера
4. Данные о клиенте возможно брать с госуслуг при регистрации в отеле
5. Возможно хостинг системы перенести в облачный провайдер вместо хостинга компании
6. Интеграция с местными клининговыми компаниями
7. Расширение функциональности системы: дополнительные возможности для гостей, такие как заказ услуг, ресторанов и бронирование спа-процедур.
8. Виртуальные туры по номерам
9. Интеграция с умными замками в комнатах и другими технологиями, такими как системы умного освещения и климат-контроля.


### Оценка вероятности изменений и их влияние на существующую декомпозицию:
A (добавление), С (изменение)

| № сценария     | вероятность % | влияние на контекстную декомпозицию      | влияние на функциональную декомпозицию                                                                                                      |
|----------------|---------------|------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 1 (бот)        | 50            | клиент (С)+админ(С)+персонал(С)+отель(С) | Система регистрации(С) + Внешний модуль (A-нового сервиса)                                                                                  |
| 2 (лояльность) | **80**        | клиент (С) + отель (C)                   | Система лояльности (A) + Подсистема бронирования (C)                                                                                        |
| 3 (рейтинг)    | **90**        | номер  (С)                               | Система регистрации(С) + подсистема рейтинга номеров/отелей   (A)                                                                           |
| 4 (госуслуги)  | 30            | клиент (С)+админ(С)+персонал(С)+отель(С) | Система регистрации (C)                                                                                                                     |
| 5 (облако)     | 50            | придется переносить целиком              | Легче выносить подсистемами (можно начать с дополнительных модулей выход из строя которых не окажет глобального влияния на систему в целом) |
| 6 (клининг)    | 60            | персонал(С) +отель(С) + номер (С)        | Подсистема управления номером (С)                                                                                                           |
| 7 (доб услуги) | **80**        | клиент (С) +   отель(С)                  | Подсистема управления бронированием (С)                                                                                                     |
| 8 (вирт туры)  | 50            | отель(С) + номер (С)                     | Подсистема управления номером (С)                                                                                                           |
| 9 (умн замки)  | **90**        | номер (С)                                | Система интеграции с умными устройствами (A)  + Подсистема управления номером (C)                                                           |

### Вывод:
На основе сценариев изменений и их вероятности можно предположить что при существенных изменениях **контекстная декомпозиция** претерпевает изменения в большем количестве элементов чем
**функциональная декомпозиция**

Однако **функциональная декомпозиция** при расширении потребует создания дополнительных функциональных блоков, 
что в контексте "расширяй, а не модифицируй" может быть даже лучше. Для того чтобы успеть к пику сезона можно начать работу с "подсистемы бронирования" 
использую функционал регистрации и оплаты от существующей системы и "веб приложением адаптированным под мобильные устройства" параллельно.

В функциональной декомпозииции можно ввести понятие "Бронирование" которе будет связывать контексты клиента, отеля, номера, администратора. 
Следует следить за тем чтобы флоу бронирования не было слишком громоздким и не смазывало границы ответственности между акторами. 
Например, термин "Номер забронирован" в разных контекстах звучит по разному:
* для клиента значит что этот номер на определнную дату пинадлежит ему
* для админа - нужно позаботиться о заезде клиента
* для персонала - нужно убрать номер к приезду гостя
# 1. Record architecture decisions

Date: 2024-01-05

## Status

Accepted

## Context

### Архитектурные Ката
https://nealford.com/katas/kata?id=BLT

Я возьму БЛТ (Сэндвич с беконом, салатом и томатами)
Национальная закусочная хочет включить функцию заказа их сендвичей через Интернет.
* Пользователи: тысячи, возможно, однажды миллионы
* Требования:
    * пользователям будет предоставлен заказ, затем им будет предоставлено время, чтобы забрать свой сэндвич и указать дорогу к магазину (который должен быть интегрирован с несколькими внешними картографическими сервисами, включающими информацию о дорожном движении).
    * если магазин предлагает услугу доставки, отправьте водителя с сэндвичем пользователю
    * доступность для мобильных устройств
    * предлагайте ежедневные национальные промо-акции / специальные предложения
    * предлагайте местные ежедневные промо-акции / специальные предложения
    * принимайте оплату онлайн или лично /при доставке
* Дополнительный контекст:
    * Закусочные работают по франшизе, у каждой свой владелец.
    * У материнской компании есть планы на ближайшее будущее по экспансии за рубеж.
    * Корпоративная цель - нанять недорогую рабочую силу для максимизации прибыли.

**ДЗ: Взаимодействие сервисов**

#### Цель:
В этом ДЗ вы научитесь проектировать взаимодействие и оценивать решение

Описание/Пошаговая инструкция выполнения домашнего задания:

Необходимо описать взаимодействие нескольких сервисов и оценить архитектурное решение. 

Для критичного сценария
1. Создать диаграмму последовательности (sequence diagram)
2. Сделать оценку атрибутов качества (например, надежность, производительность, модифицируемость и т.д) с помощью конкретных сценариев и характеристик ИТ системы.

attributes-esimate
## Decision

### Акторы:
* Пользователь
* Заказ
* Товар( BLT- сендвич)
* Закусочная(точка)
* Владелец закусочной
* Материнская компания
* Владелец материнской компании
* Рабочие закусочной
* Доставщики
* Система доставки
* Система сопровождения заказа
* Система оплаты
* Канал приема заказа
* Система регистрации(для акций и привилегий)

### Сценарии помогающие выделить подсистемы

- [ ] Пользователь хочет сделать заказ через интернет ( веб-сайт, телефон) для того чтобы получить свой сендвич на дом
- [ ] Пользователь хочет сделать заказ через интернет для того чтобы забрать свой сендвич самовывозом
- [ ] Пользователь отменяет свой заказ и хочет получить возврат средств
- [ ] В свой день рождения пользователь хочет получить скидку на заказ. Для этого он может зарегистрироваться в системе

### Функциональные требования
* По франшизе подключение новой Закусочной должно работать под ключ
* Должна быть единая система мониторинга закусочных и бух учета всех франчайзи.
* Франчайзи регистрирует свою закусочную в системе франшизы и получает доступ к управлению своей точкой. Данная точка появляется в общей системе закусочных и начинает получать заказы через интернет.
### Нефункциональные требования
* Единый сайт и моб приложение (может быть адаптив) для всех закусочных
* Отдельный сайт для франчайзи и система управления точкой (под ключ) статистика, мониторинг, контроль продуктов и финансов, система лояльности.
* Бекенд единый для всех франчайзи
* БД для хранения заказов и транзакционного перехода заказа по статусам
* Бд геораспределенная. Вся информация делится по регионам присутствия. Например по странам. Шардинг.
* Приложение для доставщиков (откуда забрать и куда отвезти)

## Consequences
Выделим сервисы согласно функциональной декомпозиции
### Сервисы:
* Система сопровождения заказов (ССЗ)- от регистрации заказа, оплаты, до доставки и завершения.
  * Бд (таблица) ССЗ. **order**
* Система управления меню (СУМ)
  * Бд (таблица) СУМ. **menu**
* Система регистрации и лояльности пользователя. (СРЛП)
  * Бд (таблица) СРЛП **user**
* Система управления франшизами. (СУФ) - объединяет все точки в единую международную сеть. Видит все шарды.
* Система регистрации франчайзи. (СРЛФ) - выдача прав, управление точками.
  * Бд (таблица) СРЛФ **franchisee**
* Система управления точкой (СУТ) - регистрация точки, учет заказов и ингредиентов, бух учет.
  * Бд (таблица) СУТ **food_point**
* Система регистрации доставщика СРД-учет доставщиков, передача заказа
  * Бд (таблица) СРД **delivery**
* Система очередей - переход заказа из состояния в состояние сопровождается евентом на которые подписываются закусочные, доставщики, система пользователей…

#### Ограничения:
* Инстансы сервисов:  ССЗ, СРЛП, СУТ, БД, геораспределены и работают в своем регионе.
* Инстансы подняты в кластере кубернетес.
* Кластер на каждый регион в своем датацентре или один кластер на 2 соседних региона: 2 датацентра по 1му в регионе, что лучше. (дубли, реплики, избыточные, сервисы в дублирующем датацентре соседнего региона).
* СУФ подключается из региона головного офиса и видит все шарды и инфо по всем регионам присутствия.

### Бд:
кластер MySQL. 

Таблицы: order, menu, user, franchisee, food_point, delivery.

#### Ограничения:
* Бд реплицируется полуасинхронно.
* Каждый регион это шард заказов, точек, меню, доставки и пользователей.
* Каждый шард реплиуируется.
* Распределенные транзакции отсутствуют. 
* Настроена система логирования, система мониторинга, алертинга и трассировки.
* Возможно применить паттерн SQRS для разделения сервисов на master ноды и info ноды.
 
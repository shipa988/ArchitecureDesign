# 1. Record architecture decisions

Date: 2024-01-09

## Status

Accepted

## Context

https://nealford.com/katas/kata?id=GoingGreen

### Сделаем мир чище

Крупный магазин электроники хочет заняться переработкой электроники и нуждается в новой системе для его поддержки.
Покупатели могут сдать свое небольшое личное электронное оборудование (или воспользоваться местными киосками в торговом
центре) и, возможно, получить деньги за использованное оборудование, если оно находится в рабочем состоянии.

#### Пользователи:

сотни, надеюсь, тысячи или миллионы.

#### Требования:

* Клиенты могут получить расценки на бывшее в употреблении личное электронное оборудование (телефоны, фотоаппараты и т.
  д.) либо через Интернет, либо в киоске в торговом центре.
* Клиенты получат коробку по почте, отправят свою электронную почту и, если она в хорошем рабочем состоянии, получат
  чек.
* После получения оборудования его оценивают (инспектируют), чтобы определить, можно ли его переработать (безопасно
  уничтожить) или продать (eBay и т. д.).
* Компания планирует добавлять 5-10 новых типов электроники, которые они будут принимать каждый месяц.
* Каждый тип электронной продукции имеет свой собственный набор правил описания и оценки.

#### Дополнительный контекст:

* Это высококонкурентный бизнес, и это новое направление деятельности для нас.
* Если мы не получили какой-либо тип электронного оборудования в течение года, мы удалим его из нашей системы.
* Нам необходимо вести список электронного оборудования, которое мы готовы принять, поскольку он часто меняется.
* Каждая единица оборудования имеет свои правила оценки (проверки)

#### ДЗ: Системное проектирование

#### Цель:

В этом ДЗ вы научитесь системному проектированию.

Описание/Пошаговая инструкция выполнения домашнего задания: Для выбранного кейса опишите слой решения:

1. Диаграмма контейнеров приложения на основе выбранной модели функциональной декомпозиции
2. Декомпозиция слоя данных: какие данные в каких БД хранятся
3. Деплоймент диаграмма

## Decision

#### Акторы:

1. магазин электроники
2. сдаваемая на переработку техника
3. клиенты
4. каталог сдаваемой электроники
5. заказ на передачу техники
6. канал связи с клиентом
7. оплата сданной техники
8. точка получения информации (Интернет, киоск, торговый центр)
9. точка отправки б/у техники (почта, служба доставки)
10. точка проверки оборудования (`сервисный центр`...)

#### сценарии:

**1. Запрос цены онлайн через веб-платформу:**

* Пользователь: Анна, владелица старого смартфона.

1. Анна открывает веб-сайт системы "Сделаем мир чище".
2. На главной странице она выбирает раздел "Оценить мое оборудование".
3. Анна вводит информацию о своем старом смартфоне, указывает модель и техническое состояние.
4. Система предоставляет Анне предварительную цену за ее устройство.
5. Удовлетворенная предложением, Анна заказывает коробку для отправки своего смартфона.

**2. Сдача устройства через киоск в торговом центре:**

* Пользователь: Иван, студент с неисправным ноутбуком.

1. Иван обнаруживает киоск системы в своем торговом центре.
2. Используя интерфейс киоска, Иван считывает штрих-код на своем ноутбуке для быстрого ввода информации.
3. Киоск запрашивает дополнительные детали о неисправности.
4. Иван получает уведомление о времени оценки и следит за статусом своего запроса через приложение.

3. Оценка и отправка оборудования:

* Пользователь: Мария, владелица старой камеры.

1. Мария отправляет свою камеру в предоставленной коробке.
2. После получения, система автоматически идентифицирует поступившее оборудование.
3. Специалисты проводят оценку состояния камеры и определяют, что она пригодна для переработки.
4. Мария получает уведомление о результатах оценки и решает продать оборудование.

**4. Добавление нового типа оборудования в систему:**

* Пользователь: Тимур, администратор системы.

1. Тимур получает информацию о новом типе электроники, который компания планирует принимать.
2. Он входит в административный интерфейс системы.
3. Тимур добавляет новый тип оборудования в базу данных с указанием правил описания и оценки.
4. Система автоматически обновляет список принимаемого оборудования на веб-сайте и в киосках.

**5. Управление списком принимаемого оборудования:**

* Пользователь: Елена, менеджер по переработке.

1. Елена просматривает статистику использования каждого типа оборудования за последний год.
2. Она решает удалить из списка те типы оборудования, которые не принимались более года.
3. Система автоматически удаляет устаревшие типы оборудования и обновляет информацию на всех платформах.

### Прохождение заказа сквозь сервисы

* `клиент` входит на `сайт`
* `клиент` регистрируется через `api-gateway` и `сервис пользователей`
* `сайт` запрашивает у `сервиса каталога` список товаров подлежащих утилизацции в данном регионе.
* `сайт` запрашивает у `сервиса пользователей` информацию по пользователю
* `сайт` запрашивает у `сервиса заказов` список заказов пользователя и их статусы
* `клиент` выбирает товар для утилизации
* `сайт` запрашивает информацию по данному товару у `сервиса оценки товаров` (подробно с ценами и необходимыми
  параметрами и состоянием)
* `клиент` соглашается и создает новый заказ
* `сайт` создает новый заказ в состоянии **created** в `сервисе заказов `
* `сайт` опрашивает `сервис доставки` для выбора ближайшей к клиенту точки отправки товара
* `сайт` опрашивает `сервис заказов` на вариант изменения статуса для отображения в личном кабинете пользователя
* `клиент` отправляет товар в данной `точке отправки (почта)`
* `точка отправки` взаимодействуя с `сервисом доставки` изменяет статус заказа на **sent**
* заказ прибывает в магазин (`сервисный центр`)
* `сервис доставки `изменяет статус заказа на **delivered**
* `сервис заказов` вычитывает заказы в статусах delivered и оповещает через **сервис уведомлений** сервисные центры
* `сервисный центр` через сервис заказов изменяет статус на **assessing**
* `сервисный центр` запрашивает у `сервиса оценки` данные для оценки товара
* `сервисный центр` проводит оценку и через `сервис заказов` проставляет для заказа цену и описание оценки и статус *
  *assessed**
* `сервис заказов` вычитывает заказы в статусах assessed оповещает через `сервис уведомлений` `клиент`ов о возможности
  получить выплату или о возврате товара
* `клиент` соглашается на выплату и передает в `сервис заказов` согласие
* `сервис заказов` перенаправляет `клиент` а на страницу ввода реквизитов и он взаимодействует с `сервисом оплаты `
  товаров (статус заказа **paying**)
* `клиент` вводит свои реквизиты и `сервис оплаты` взаимодействуя с `провайдером банка магазина` перечисляет `клиент`у
  средства за утилизированный товар
* `сервис заказов` переводит заказ в статус **payed**
* `сервис заказов` переводит заказ в статус **completed**

#### Функциональная декомпозиция:

1. **Система учета оборудования, сдаваемого на переработку**: ведет каталог оборудования, цен на него, кол-ва которое
   еще можно сдать и того что уже сдали, точки сбора, характеристики, описание проверки итп)
2. **api-gateway**: предоставляет авторизацию для клиентов и админов а также предоставляет услуги BFF и прокси сервера.
3. **фронт-енд**
    1. сайт
        1. публичный
        2. для киосков
    2. админка
4. **мобильное приложение**.
5. БД mysql для учета оборудования
    1. табл клиентов
    2. табл рабочих
    3. табл с каталогом оборудования на переработку
    4. табл справочника для оценки оборудования
    5. табл заказов
6. Система оценки оборудования (помогает специалистам оценить сданное оборуднование)
7. Система интеграции с почтой и доставкой (сервис взаимодействует с внешними службами доставки)
8. Система уведомлений клиентов/рабочих о новом статусе заказа (читает бд(реплику) и уведомляет клиентов и специалистов
   о новых/измененных заказах)
9. **Система учета заказов**
10. **Система учета пользователей**

## Consequences
разработана Диаграмма контейнеров приложения на основе выбранной модели функциональной декомпозиции
```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

skinparam wrapWidth 300
LAYOUT_WITH_LEGEND()
LAYOUT_LANDSCAPE()

title
  <b>RecyclingSystem v2024.01.16</b>
  <i>Система утилизации</i>
 end title

 Person(CLIENT, "Клиент")
  System_Ext(DELIVERY_POINT, "DELIVERY_POINT")
 System_Ext(BANK_PROVIDER, "BANK_PROVIDER")
 
 System_Boundary(recycling_system, "Система утилизации") {
 System(SERVICE_CENTER, "SERVICE_CENTER")
 Container(DELIVERY_SERVICE, "DELIVERY_SERVICE", "go", "интеграция с внешними сервисами доставки")
 Container(NOTIFICATION_SERVICE, "NOTIFICATION_SERVICE", "go", "система пуш смс email уведомлений")
 Container(PAYMENT_SERVICE, "PAYMENT_SERVICE", "go", "система оплаты и интеграции с платежками")
 
 
  System_Boundary(auth_system, "Система аутентификации") {
  Container(API_GATEWAY, "API_GATEWAY", "krakenD", "Aутентификация/сессии и API Gateway")
  ContainerDb(db, "Database", "Redis", "хранит данные по аутенификации/сессии", $sprite="Redis")
 }
 
 System_Boundary(equipment_accounting_system, "Система учета оборудования") {
  Container(CATALOG_SERVICE, "CATALOG_SERVICE", "go", "каталог сдаваемого на переработку оборрудования")
  ContainerDb(auth_db, "Database", "Mysql", "хранит актуальные и уже нет типы сдаваемого оборудования", $sprite="Mysql")
 
  Rel(CLIENT, CATALOG_SERVICE, "Запрос списка товаров")
 }
 
 System_Boundary(order_system, "Система учета заказов") {
  Container(ORDER_SERVICE_READ, "ORDER_SERVICE_READ", "go", "данные по заказам")
  Container(ORDER_SERVICE_WRITE, "ORDER_SERVICE_WRITE", "go", "управление заказами")

  ContainerDb(order_db, "Database", "Mysql", "хранит статусы и данные по каждому заказу", $sprite="Mysql")
  
  Rel(CLIENT, ORDER_SERVICE_READ, "Запрос информации о заказах")
  Rel(CLIENT, ORDER_SERVICE_WRITE, "Создание/изменение заказа")
 }
  
 System_Boundary(user_system, "Система учета пользователей") {
  Container(USER_SERVICE, "USER_SERVICE", "go", "данные о клиентах и работниках")
  ContainerDb(user_db, "Database", "Mysql", "бд пользователей", $sprite="Mysql")
  
  Rel(CLIENT, USER_SERVICE, "Запрос информации о пользователе")
  Rel(API_GATEWAY, USER_SERVICE, "Запрос информации о пользователе")
 }
   
 System_Boundary(assessment_system, "Система описания оборудования") {
 Container(ASSESSMENT_SERVICE, "ASSESSMENT_SERVICE", "go", "сервис по оценке товаров")
  ContainerDb(equipment_db, "Database", "Mysql", "хранит описания и систему оценки", $sprite="Mysql")
  
  Rel(CLIENT, ASSESSMENT_SERVICE, "Запрос информации о товаре")
 }
 }
 

 
 
   Rel(CLIENT, API_GATEWAY, "Aвторизуется")
   Rel(CLIENT, DELIVERY_SERVICE, "Запрос точки отправки")
   Rel(ORDER_SERVICE_WRITE, PAYMENT_SERVICE, "Выплата по завершенному заказу")
   Rel(BANK_PROVIDER, CLIENT, "Перечисление средств")
   Rel(PAYMENT_SERVICE, BANK_PROVIDER, "Запрос средств")
   Rel(SERVICE_CENTER, ORDER_SERVICE_WRITE, "Изменение заказа")
   Rel(SERVICE_CENTER, ASSESSMENT_SERVICE, "Запрос данных для оценки")
   Rel(SERVICE_CENTER, CATALOG_SERVICE, "Изменение списка товаров")
   BiRel(DELIVERY_POINT, DELIVERY_SERVICE, "Интеграция")
   Rel(ORDER_SERVICE_WRITE, NOTIFICATION_SERVICE, "Оповещение клиентов/работников")


 @enduml
```

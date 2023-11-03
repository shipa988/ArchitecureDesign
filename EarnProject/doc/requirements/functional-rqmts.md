В этом документе содержатся функциональные требования, которые мы выявили для системы "Earn" на основе первого ADR
0001-lets-earn.md. Документы предоставлены всем командам. Вы найдете _актеров_, которых мы определили, и то, как 
они взаимодействуют с системой в основных сценариях.

# ***Акторы***
- Клиент.
- Администратор.
- Подсистема управления контрактами клиентов.
- Подсистема распределения вознаграждений(процентов) по контрактам клиентам биржи.
- Подсистема мониторинга внешних застейканных средств биржи в блокчейне (proof of stake), балансов и выплат
вознаграждений

<br>

# ***Клиент на сайте или в приложении биржи***

## Управление аккаунтом
- Перейти на страницу с продуктом можно по ссылке (соцсети/реклама...) или с основной страницы из пункта меню "Earn"
- Незарегистрированный пользователь видит только список валют и проценты вознаграждений по ним без возможности открыть контракт.
- Зарегистрированный пользователь видит список валют с возможностью открыть новый контракт
и список своих контрактов и их статусов: открыт, отменен, исполнен.
- Зарегистрированный пользователь видит свой баланс в "Earn"

## ***Список доступных валют для открытия контрактов***
- Просмотр списка валют с отображением процентов по ним.
- В каждой валюте клиент может открыть новый контракт при нажатии на кнопку открыть.
- Для каждой валюты можно выбрать период контракта
- Для каждой валюты доступно увеличение процента вознаграждения за счет холда определенного количества монет биржи "X-COIN"
- Для каждой валюты отображается максимально и минимально доступная сумма для нового контракта исходя из настроек администратора и уже вложенных клиентом средств

# ***Контракты***

- В списке валют выбрать некую валюту и открыть пот ней контракт, выбрав условия: сумма, период, процент, автопродление,
капитализация процентов. При изменении условий ожидаемое вознаграждение динамически пересчитывается и показывается
пользователю.
- Если недостаточно средств должна быть возможность пополнить средства прямо в окне открытия контракта;
- Контракт можно отменить и отключить его автопродление;
- Показывать список контрактов с фильтрацией по статусам и валютам;
- При продлении/закрытии/выплате вознаграждения клиенту приходит пуш уведомление
<br>

# ***Администратор***
## Список Валют
- Через "Админку" добавляет/изменяет/удаляет/получает список валют
- Настраивает условия для каждой валюты: минмакс сумма,минмакс период,минмакс процент
## Список контрактов
- Видит все контракты всех пользователей с возможностью поиска/отмены/изменения условий
## Мониторинг
- Мониторит систему с помощью таблицы в которой указаны агрегированные данные по контрактам. Мониторит обеспечение
по выплатам вознаграждения по контрактам. Получает уведомления на телефон если есть проблемы.
- Добавляет средства на внешний блокчейн кошелек для стейкинга активов пользователей в системе "proof of stake";
<br>

# ***Подсистема управления контрактами клиентов.***
## Админы
- Обеспечивает CRUD операции по валютам:
  - Добавление
  - Удаление
  - Изменение
  - Список
- Обеспечивает CRUD операции по контрактам:
    - Отмена
    - Изменение
    - Список
- Отслеживает обеспечение контрактов полученное из "_Подсистема мониторинга внешних застейканных средств биржи в блокчейне (proof of stake), балансов и выплат вознаграждений_". Реагирует на критичные ситуации когда обеспечения может не хватить.
## Клиенты
- Обеспечивает операции по валютам:
    - Список
- Обеспечивает операции по контрактам:
    - Отмена
    - Изменение
    - Список
- Рассчитывает предполагаемое вознаграждение
  <br>

# ***Подсистема распределения вознаграждений(процентов) по контрактам клиентам биржи.***
- Отслеживает контракты клиентов на предмет окончания
- Для истекших контрактов запускает процедуру выплаты вознаграждения
  - Отправляет в "_сервис кошельков_" информацию о возврате средств пользователю по контракту
  - Если контракт автопродлеваемый
    - если контракт с капитализацией - отправляет в "_Подсистема управления контрактами клиентов_" уведомление об открытие нового контракта с суммой больше на вознаграждение
    - если юез капитализации - отправляет в "_Подсистема управления контрактами клиентов_" уведомление об открытие нового контракта с суммой равной сумме предыдущего контракта
  - Если контракт не автопродлеваемый
    - Отправляет в "_сервис кошельков_" информацию о выплате вознаграждения пользователю по контракту

<br>

# ***Подсистема мониторинга внешних застейканных средств биржи в блокчейне (proof of stake), балансов и выплат вознаграждений***
- Отслеживает публичную информацию (информация по всем кошелькам и транзакциям в среде блокчейн является открытой и доступной всем) о кошельках биржи с застейканными средствами
  - Отслеживает балансы и вознаграждения. 
  - Фиксирует все изменения внутри системы для передачи "_Подсистема управления контрактами клиентов_" и для возможности отображения админам.
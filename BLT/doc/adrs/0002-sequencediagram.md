# 2. sequenceDiagram

Date: 2024-01-05

## Status

Accepted

## Context

### Критический сценарий:

1. Юзер регистрирует новый заказ на доставку сендвичей и оплату через приложение.
2. Заказ переходит в состояние **_created_**.
3. Пользователь оплачивает заказ - состояние заказа - **_paid_**.
4. ССЗ выбирает ближайшую закусочную, принимающую заказы на доставку и в которой есть пул для приема заказов и доступны необходимые ингредиенты.
5. Заказ переходит в состояние **_assigned_**
6. После приготовления СУТ переводит заказ в состоянии **_ready_**.
7. ССЗ уведомляет доставщика. Доставщик берет заказ: состояние заказа **_in_delivery_**
8. Доставщик доставляет заказ до двери и переводит его в состояние **_completed_**.

## Decision

```mermaid
sequenceDiagram
    customer-->>+user app: get order page in some country
    user app-->>+geo haproxy: get order page with auth and geo
    geo haproxy-->>+istio ingress gateway: get order page with auth
    istio ingress gateway-->>+istio ingress gateway: authorize
    istio ingress gateway-->>+api gateway: get order page mtls
    api gateway-->>+order-info service: get my orders 
    order-info service-->>-api gateway: orders info
    api gateway-->>+basket-info service: get my bucket
    basket-info service-->>-api gateway: basket info
    api gateway-->>+menu-info service: get menu 
    menu-info service-->>-api gateway: menu info
    api gateway-->>+user-info service: get user info
    user-info service-->>-api gateway: user info
    api gateway-->>-istio ingress gateway: orders page info 
    istio ingress gateway-->>-geo haproxy: orders page info
    geo haproxy-->>-user app: orders page info
    user app-->>-customer: orders page info
    #
    customer-->>+user app: order the sandwich in some country
    user app-->>+geo haproxy: order the sandwich with auth and geo
    geo haproxy-->>+istio ingress gateway: order the sandwich with auth
    istio ingress gateway-->>+istio ingress gateway: authorize

    istio ingress gateway-->>+api gateway: order the sandwich mtls

    api gateway-->>+order-master service: order the sandwich
    order-master service-->>event bus: order status created
    order-master service-->>-api gateway: order №100500 created

    api gateway-->>+basket-master service: put order №100500 to basket
    basket-master service-->>event bus: order status paid
    basket-master service-->>-api gateway: basket filled and paid

    api gateway-->>-istio ingress gateway: order №100500 created
    istio ingress gateway-->>-geo haproxy: order №100500 created
    geo haproxy-->>-user app: order №100500 created
    user app-->>-customer: order №100500 created
#
    order-master service-->>event bus: order assigned
    event bus->>order-info service: order №100500 assigned
    
    event bus-->>food_point app: order for cooking
    food_point app-->>order-master service: order is ready
    order-master service-->>event bus: order ready
    event bus->>order-info service: order №100500 is ready for delivery
    
    event bus-->>delivery app: order for delivery
    delivery app-->>order-master service: order is delivered
    order-master service-->>event bus: order completed
    event bus->>order-info service: order №100500 completed

    customer-->>user app: get order №100500 status
    user app-->>geo haproxy: get order №100500 status
    geo haproxy-->>istio ingress gateway: get order №100500 status
    istio ingress gateway-->>istio ingress gateway: authorize
    istio ingress gateway-->>api gateway: get order №100500 status
    api gateway-->>order-info service: get order №100500 status
    order-info service-->>api gateway: order №100500 info
    api gateway-->>istio ingress gateway: order №100500 info
    istio ingress gateway-->>geo haproxy: order №100500 info
    geo haproxy-->>user app: order №100500 info
    user app-->>customer: order №100500 info
 ```
## Consequences

исполльзуем разделение на мастер и инфо ноды для реализации cqrs. приложение (user-app) ходит к **order-info** ноде для обновления статуса заказа.
**order-info** потребляет евенты из системы очередей.
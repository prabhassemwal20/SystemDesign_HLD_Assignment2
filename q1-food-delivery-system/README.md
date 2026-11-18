# Q1. Food Delivery System (HLD)

## 1. Introduction
A Food Delivery System is an online application that allows users to browse restaurants, view menus, order food, make online payments, and track deliveries. Examples include Swiggy and Zomato.

## 2. Features
- User registration and login
- Browse restaurants and view menus
- Add food items to cart and place orders
- Make online payments
- Track order status and delivery

## 3. High-Level Design (HLD)

The system follows a client-server architecture in which different services handle specific functionalities.

![HLD Diagram](./hld-diagram.png)

*Also available as [SVG](./hld-diagram.svg).*

<details>
<summary>Mermaid version of the HLD</summary>

```mermaid
flowchart TD
    A[User Application] --> B[API Gateway]
    B --> C[User Service]
    B --> D[Restaurant Service]
    B --> E[Order Service]
    B --> F[Payment Service]
    E --> G[Delivery Service]
    G --> H[Notification Service]
    C --> DB[(Database)]
    D --> DB
    E --> DB
    F --> DB
    G --> DB
    H --> DB
```

</details>

## 4. Main Components
| Component | Responsibility |
|-----------|----------------|
| **User Service** | User registration, login, authentication, profile management |
| **Restaurant Service** | Restaurants, menus, food items, prices, availability |
| **Order Service** | Shopping cart, order placement, order confirmation, order status |
| **Payment Service** | Online payments via payment gateways, transaction records |
| **Delivery Service** | Assigns delivery partners and tracks delivery status |
| **Notification Service** | Order confirmation, preparation, dispatch, and delivery alerts |

## 5. Working of the System
1. The user registers or logs into the application.
2. The user browses restaurants and selects food items.
3. Selected items are added to the cart.
4. The user places an order and completes the payment.
5. The restaurant receives and prepares the order.
6. A delivery partner is assigned to collect the order.
7. The user tracks the order until it is delivered.

```mermaid
sequenceDiagram
    actor U as User
    participant G as API Gateway
    participant O as Order Service
    participant P as Payment Service
    participant R as Restaurant Service
    participant D as Delivery Service
    participant N as Notification Service
    U->>G: Login, browse menu, add to cart
    G->>O: Place order
    O->>P: Request payment
    P-->>O: Payment status
    O->>R: Send order to restaurant
    O->>D: Assign delivery partner
    D->>N: Status updates
    N-->>U: Order confirmed / dispatched / delivered
```

## 6. Database Design
```mermaid
erDiagram
    USER ||--o{ ORDERS : places
    RESTAURANT ||--o{ MENU : offers
    ORDERS ||--|| PAYMENT : has
    ORDERS ||--|| DELIVERY : has
    USER { int User_ID PK
           string Name
           string Email
           string Password }
    RESTAURANT { int Restaurant_ID PK
                 string Name
                 string Location }
    MENU { int Item_ID PK
           int Restaurant_ID FK
           string Item_Name
           decimal Price }
    ORDERS { int Order_ID PK
             int User_ID FK
             decimal Total_Amount
             string Status }
    PAYMENT { int Payment_ID PK
              int Order_ID FK
              string Payment_Status }
    DELIVERY { int Delivery_ID PK
               int Order_ID FK
               int Partner_ID
               string Status }
```

## 7. Conclusion
The Food Delivery System provides a scalable and efficient platform for online food ordering. Using separate services for users, restaurants, orders, payments, and deliveries makes the system easier to maintain and expand.

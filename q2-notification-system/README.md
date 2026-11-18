# Q2. Notification System (HLD)

## 1. Introduction
A Notification System is a software system that sends important messages and updates to users through different communication channels such as Email, SMS, and Push Notifications.

## 2. Features
- User registration and notification preferences
- Send notifications through Email, SMS, and Push
- Support different types of notifications (order updates, alerts, reminders)
- Store notification history and delivery status
- Handle failed notifications through retry mechanisms

## 3. High-Level Design (HLD)

The system uses a service-based architecture to manage notification requests, user preferences, message processing, and delivery.

![HLD Diagram](./hld-diagram.png)

*Also available as [SVG](./hld-diagram.svg).*

<details>
<summary>Mermaid version of the HLD</summary>

```mermaid
flowchart TD
    A[User / Application] --> B[API Gateway]
    B --> C[Notification Service]
    C --> D[[Message Queue]]
    D --> E[Notification Worker]
    E --> F[Email Service]
    E --> G[SMS Service]
    E --> H[Push Service]
    F --> DB[(Database)]
    G --> DB
    H --> DB
    E -. failed delivery .-> D
```

</details>

## 4. Main Components
| Component | Responsibility |
|-----------|----------------|
| **User Service** | Registration, contact details, authentication |
| **Preference Service** | Stores user preferences, including enabled channels |
| **Notification Service** | Accepts requests, identifies notification type and recipient |
| **Message Queue** | Temporarily stores requests and distributes them for processing |
| **Notification Workers** | Process queued messages and send via the right channel |
| **Delivery Service** | Integrates with external email, SMS, and push providers |
| **Database** | Stores history, delivery status, preferences, and retry info |

## 5. Working of the System
1. The user registers and sets notification preferences.
2. An application generates a notification request (order update, alert, reminder).
3. The Notification Service receives and validates the request.
4. The request is placed in a message queue.
5. Notification workers process the request.
6. The appropriate channel (Email, SMS, or Push) is selected.
7. The delivery status is stored in the database.
8. If delivery fails, the system retries after a specified interval.

```mermaid
flowchart LR
    S([Start]) --> V[Validate request]
    V --> Q[Enqueue]
    Q --> W[Worker picks message]
    W --> P{Check user preferences}
    P --> C[Send via Email / SMS / Push]
    C --> R{Delivered?}
    R -- Yes --> OK[Status = SENT]
    R -- No --> RT[Retry after interval]
    RT --> W
    OK --> H[(Save history)]
```

## 6. Database Design
```mermaid
erDiagram
    USER ||--|| PREFERENCES : has
    USER ||--o{ NOTIFICATION : receives
    NOTIFICATION ||--o{ NOTIFICATION_HISTORY : logs
    NOTIFICATION ||--o{ RETRY_LOG : retries
    USER { int User_ID PK
           string Name
           string Email
           string Phone }
    PREFERENCES { int User_ID FK
                  bool Email_Enabled
                  bool SMS_Enabled
                  bool Push_Enabled }
    NOTIFICATION { int Notification_ID PK
                   int User_ID FK
                   string Type
                   string Message }
    NOTIFICATION_HISTORY { int Notification_ID FK
                           datetime Sent_Time
                           string Status }
    RETRY_LOG { int Retry_ID PK
                int Notification_ID FK
                int Retry_Count }
```

## 7. Conclusion
The Notification System provides reliable communication between applications and users. Message queues, background workers, and retry mechanisms improve scalability and help manage failed notifications efficiently.

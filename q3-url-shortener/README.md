# Q3. URL Shortener (HLD)

## 1. Introduction
A URL Shortener is a web-based application that converts long URLs into short and unique links. These short links redirect users to the original URL when accessed. Examples include Bitly and TinyURL.

## 2. Features
- Accept a long URL from the user
- Generate a unique short URL
- Redirect users to the original URL
- Store the mapping between short and long URLs
- Track the number of clicks on each shortened URL

## 3. High-Level Design (HLD)



![HLD Diagram](./hld-diagram.png)

*Also available as [SVG](./hld-diagram.svg).*

<details>
<summary>Mermaid version of the HLD</summary>

```mermaid
flowchart TD
    A[User / Client] --> B[API Gateway]
    B --> C[URL Shortener Service]
    C --> D[URL Generation Service]
    C --> E[URL Redirect Service]
    E <--> CA[(Cache)]
    D --> DB[(Database)]
    E --> DB
    DB --> F[Analytics Service]
    F --> G[Click Statistics]
```

</details>

## 4. Main Components
| Component | Responsibility |
|-----------|----------------|
| **API Gateway** | Receives requests and forwards them to the right services |
| **URL Generation Service** | Accepts long URLs and generates unique short codes |
| **URL Redirect Service** | Finds the original URL by short code and redirects |
| **Database** | Stores short URL to original URL mappings |
| **Analytics Service** | Tracks clicks on each shortened URL |
| **Cache** | Stores frequently accessed mappings for faster redirection |

## 5. Working of the System
1. The user enters a long URL into the application.
2. The URL Generation Service validates the URL.
3. A unique short code is generated.
4. The short code and original URL mapping are stored in the database.
5. The application generates the shortened URL and shows it to the user.
6. When someone opens the short URL, the Redirect Service looks up the original URL.
7. The user is redirected to the original website.
8. The Analytics Service updates the click count.

```mermaid
sequenceDiagram
    actor U as User
    participant G as API Gateway
    participant S as Generation Service
    participant R as Redirect Service
    participant C as Cache
    participant DB as Database
    participant A as Analytics
    U->>G: POST long URL
    G->>S: Validate and generate short code
    S->>DB: Store mapping
    S-->>U: Short URL
    U->>G: GET /short-code
    G->>R: Resolve
    R->>C: Lookup
    alt cache miss
        R->>DB: Lookup
        R->>C: Store mapping
    end
    R-->>U: 302 Redirect to original URL
    R->>A: Increment click count
```

## 6. Database Design
```mermaid
erDiagram
    USER ||--o{ URL : creates
    URL ||--o{ CLICK_STATISTICS : receives
    URL ||--|| URL_ANALYTICS : summarised_in
    URL { int URL_ID PK
          string Original_URL
          string Short_Code
          date Created_Date }
    USER { int User_ID PK
           string Name
           string Email }
    CLICK_STATISTICS { int Click_ID PK
                       string Short_Code FK
                       datetime Click_Time
                       string Device_Type }
    URL_ANALYTICS { string Short_Code FK
                    int Total_Clicks }
```

## 7. Conclusion
The URL Shortener System provides an efficient way to convert long URLs into short and shareable links. Unique code generation, database mapping, caching, and click tracking help improve system performance, reliability, and scalability.

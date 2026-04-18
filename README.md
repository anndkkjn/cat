# Диаграммы C4 для системы обработки заявок клиентов

## 1. Диаграмма контекста (Context Diagram)

```mermaid
graph TD
    A[Клиент] -->|HTTPS| S[Система обработки заявок]
    B[Оператор] -->|HTTPS| S
    C[Администратор] -->|HTTPS| S
    S -->|SMTP| D[Email-сервис]
    S -->|HTTP API| E[SMS-шлюз]
    S -->|LDAP| F[Внешняя аутентификация]
graph TD
    A[Клиент] --> W[Веб-приложение]
    A --> M[Мобильное приложение]
    B[Оператор] --> W
    C[Администратор] --> W
    
    W -->|REST API| API[Backend API]
    M -->|REST API| API
    
    API -->|JDBC| DB[(PostgreSQL)]
    API -->|Redis| R[(Redis)]
    API -->|Queue| WK[Worker]
    
    WK -->|SMTP| E[Email-сервис]
    WK -->|HTTP| S[SMS-шлюз]
graph TD
    subgraph Backend_API
        A[Auth Component]
        B[User Management]
        C[Ticket Management]
        D[Category Management]
        E[Notification Dispatcher]
        F[Reporting Component]
    end
    
    A -->|check| DB[(PostgreSQL)]
    A -->|proxy| EA[External Auth]
    B -->|CRUD| DB
    C -->|save| DB
    C -->|validate| D
    C -->|event| E
    D -->|cache| R[(Redis)]
    E -->|task| WK[Worker]
    F -->|read| DB
classDiagram
    class Ticket {
        -Long id
        -Long userId
        -String title
        -String description
        -Long categoryId
        -TicketStatus status
        +getters()
        +setters()
    }
    
    class TicketStatus {
        <<enum>>
        NEW
        IN_PROGRESS
        RESOLVED
        CLOSED
    }
    
    class TicketService {
        <<interface>>
        +createTicket(request)
        +assignTicket(id, assigneeId)
        +changeStatus(id, status)
    }
    
    class TicketServiceImpl {
        -TicketRepository repo
        -NotificationDispatcher nd
        -CategoryService cs
        +createTicket(request)
        +assignTicket(id, assigneeId)
    }
    
    class TicketRepository {
        <<interface>>
        +save(ticket)
        +findById(id)
        +findByUser(userId)
    }
    
    TicketServiceImpl ..|> TicketService
    TicketServiceImpl --> TicketRepository
    TicketServiceImpl --> NotificationDispatcher
    TicketServiceImpl --> CategoryService
    TicketRepository ..> Ticket

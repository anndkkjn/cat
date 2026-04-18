# C4 Diagrams for Ticket Processing System

## 1. Context Diagram

```mermaid
graph TD
    A[Client] -->|HTTPS| S[Ticket System]
    B[Operator] -->|HTTPS| S
    C[Admin] -->|HTTPS| S
    S -->|SMTP| D[Email Service]
    S -->|HTTP API| E[SMS Gateway]
    S -->|LDAP| F[External Auth]
2. Container Diagram
graph TD
    A[Client] --> W[Web App]
    A --> M[Mobile App]
    B[Operator] --> W
    C[Admin] --> W
    W -->|REST API| API[Backend API]
    M -->|REST API| API
    API -->|JDBC| DB[(PostgreSQL)]
    API -->|Redis| R[(Redis)]
    API -->|Queue| WK[Worker]
    WK -->|SMTP| E[Email Service]
    WK -->|HTTP| S[SMS Gateway]
3. Component Diagram
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
4. Code Diagram
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

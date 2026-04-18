# Диаграммы C4 для системы обработки заявок клиентов

## 1. Диаграмма контекста (Context Diagram)

```mermaid
graph TD
    client["Клиент<br>создаёт/отслеживает заявки"]
    operator["Оператор<br>назначает/меняет статус"]
    admin["Администратор<br>управляет справочниками"]
    
    system(("Система обработки заявок"))
    
    email["Email-сервис"]
    sms["SMS-шлюз"]
    auth["Внешняя аутентификация"]
    
    client -->|HTTPS| system
    operator -->|HTTPS| system
    admin -->|HTTPS| system
    
    system -->|SMTP| email
    system -->|HTTP API| sms
    system -->|LDAP/OAuth2| auth


graph TD
    subgraph "Система обработки заявок"
        web["Веб-приложение<br>React/Angular"]
        mobile["Мобильное приложение<br>Flutter"]
        api["Backend API<br>Spring Boot"]
        db[("PostgreSQL")]
        cache[("Redis")]
        worker["Worker<br>Celery"]
    end
    
    email["Email-сервис"]
    sms["SMS-шлюз"]
    
    client["Клиент"] --> web
    client --> mobile
    operator["Оператор"] --> web
    admin["Администратор"] --> web
    
    web -->|REST API| api
    mobile -->|REST API| api
    
    api -->|JDBC| db
    api -->|Redis| cache
    api -->|Queue| worker
    
    worker -->|SMTP| email
    worker -->|HTTP| sms

graph TD
    subgraph "Backend API"
        auth["Auth Component<br>аутентификация"]
        user["User Management<br>пользователи и роли"]
        ticket["Ticket Management<br>заявки"]
        category["Category Management<br>категории и SLA"]
        notif["Notification Dispatcher<br>уведомления"]
        report["Reporting Component<br>отчёты"]
    end
    
    db[("PostgreSQL")]
    cache[("Redis")]
    worker["Worker"]
    ext_auth["Внешняя аутентификация"]
    
    auth -->|проверка| db
    auth -->|прокси| ext_auth
    user -->|CRUD| db
    ticket -->|сохранение| db
    ticket -->|проверка| category
    ticket -->|событие| notif
    category -->|кеш| cache
    notif -->|задача| worker
    report -->|чтение| db

classDiagram
    class Ticket {
        - Long id
        - Long userId
        - String title
        - String description
        - Long categoryId
        - TicketStatus status
        - LocalDateTime createdAt
        - LocalDateTime updatedAt
        + getters/setters()
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
        + createTicket(request)
        + assignTicket(ticketId, assigneeId)
        + changeStatus(ticketId, newStatus)
        + getTicketsByUser(userId)
    }
    
    class TicketServiceImpl {
        - TicketRepository repository
        - NotificationDispatcher dispatcher
        - CategoryService categoryService
        + createTicket(request)
        + assignTicket(ticketId, assigneeId)
        + changeStatus(ticketId, newStatus)
    }
    
    class TicketRepository {
        <<interface>>
        + save(ticket)
        + findById(id)
        + findByUserId(userId)
        + findByStatus(status)
    }
    
    TicketServiceImpl ..|> TicketService
    TicketServiceImpl --> TicketRepository
    TicketServiceImpl --> NotificationDispatcher
    TicketServiceImpl --> CategoryService
    TicketRepository ..> Ticket

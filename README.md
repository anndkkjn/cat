graph TD
    client[Клиент<br>создаёт/отслеживает заявки]
    operator[Оператор<br>назначает/меняет статус]
    admin[Администратор<br>управляет справочниками]
    
    system((Система обработки заявок))
    
    email[Email-сервис]
    sms[SMS-шлюз]
    auth[Внешняя аутентификация]
    
    client -->|HTTPS| system
    operator -->|HTTPS| system
    admin -->|HTTPS| system
    
    system -->|SMTP| email
    system -->|HTTP API| sms
    system -->|LDAP/OAuth2| auth

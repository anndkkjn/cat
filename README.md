graph TB
    %% Стили
    classDef pool fill:#f5f5f5,stroke:#333,stroke-width:2px
    classDef task fill:#e1f5fe,stroke:#01579b,stroke-width:1px
    classDef event fill:#fff9c4,stroke:#fbc02d,stroke-width:2px
    classDef problem fill:#ffcdd2,stroke:#c62828,stroke-width:2px
    classDef note fill:#fff3e0,stroke:#ff9800,stroke-dasharray: 5 5

    %% Событие старта
    Start([Обращение клиента]) --> B1

    %% Менеджер
    subgraph Менеджер [Менеджер]
        B1[Принять обращение]:::task --> B2[Открыть Excel]:::task
        B2 --> B3[Внести данные]:::problem
        B3 --> B4[Выбрать мастера]:::task
        B4 --> B5[Передать заявку]:::problem
        B5 --> B6[Записать мастера]:::task
        B6 --> B7[Изменить статус]:::task
        B7 --> B8[Связаться с клиентом]:::task
    end

    %% Мастер
    subgraph Мастер [Мастер]
        C1[Выполнить работы]:::task --> C2[Сообщить о завершении]:::task
    end

    %% Клиент
    subgraph Клиент [Клиент]
        A2[/Подтверждение/]:::event
    end

    %% Руководитель (отдельный пул)
    subgraph Руководитель [Руководитель]
        D1[Запросить отчёт]:::task --> D2[Сформировать отчёт вручную]:::problem
    end

    %% Связи
    B5 --> C1
    C2 --> B7
    B8 --> A2
    
    %% Периодическая отчётность
    D2 -.-> B7

    %% Пояснения к проблемам
    P1[/"⚠️ Ручной ввод без блокировок"/]:::note -.-> B3
    P2[/"⚠️ Нет фиксации времени назначения"/]:::note -.-> B5
    P3[/"⚠️ 30+ минут на формирование"/]:::note -.-> D2

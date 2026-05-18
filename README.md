# 🐇 Event-Driven Architecture — RabbitMQ + STOMP

Проект демонструє **Event-Driven архітектуру** з використанням RabbitMQ як брокера повідомлень та двох веб-клієнтів для обміну повідомленнями в реальному часі через протокол STOMP/WebSocket.

---

## 📁 Структура проекту

```
exam/
├── src/
│   ├── web1/
│   │   └── index.html        # Publisher — відправляє повідомлення
│   └── web2/
│       └── index.html        # Subscriber — отримує повідомлення
├── rabbitmq.conf             # Конфігурація RabbitMQ
├── docker-compose.yml        # Docker-оркестрація
├── package.json
├── .editorconfig
├── .gitignore
└── README.md
```

---

## 🚀 Запуск

### Вимоги
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

### Команди

```bash
# Запустити всі сервіси
npm start
# або
docker compose up -d

# Зупинити
npm stop

# Переглянути логи
npm run logs

# Статус контейнерів
npm run status
```

---

## 🌐 Адреси сервісів

| Сервіс              | URL                          | Опис                        |
|---------------------|------------------------------|-----------------------------|
| **Web1** (Publisher) | http://localhost:8081        | Відправляє повідомлення      |
| **Web2** (Subscriber)| http://localhost:8082        | Отримує повідомлення         |
| **RabbitMQ UI**      | http://localhost:15672       | Панель управління (guest/guest) |
| **STOMP WebSocket**  | ws://localhost:15674/ws      | WebSocket endpoint           |
| **AMQP** (Postman)   | amqp://localhost:5672        | Для Postman / backend        |

---

## 🔄 Схема взаємодії

```
┌─────────────────────────────────────────────────────────┐
│                      RabbitMQ                           │
│                                                         │
│  Exchange: amq.fanout  ──►  Queue (auto)                │
│                                                         │
└─────────────────────────────────────────────────────────┘
        ▲                          │
        │ PUBLISH                  │ SUBSCRIBE
        │ (STOMP/WS)               │ (STOMP/WS)
        │                          ▼
┌──────────────┐          ┌──────────────┐
│    Web1      │          │    Web2      │
│  Publisher   │          │  Subscriber  │
│ :8081        │          │ :8082        │
└──────────────┘          └──────────────┘
        ▲
        │ PUBLISH
        │ (AMQP)
┌──────────────┐
│   Postman    │
│  (крос-      │
│  протокол)   │
└──────────────┘
```

---

## 📬 Тестування через Postman

### Надіслати повідомлення через AMQP (крос-протокольна взаємодія)

1. Відкрийте **Postman**
2. Виберіть **New Request → RabbitMQ**
3. Налаштуйте:
   - **URL**: `amqp://guest:guest@localhost:5672`
   - **Exchange**: `amq.fanout`
   - **Routing Key**: `` (порожньо для fanout)
   - **Body** (JSON):
     ```json
     {
       "from": "Postman",
       "text": "Привіт від Postman! 🚀",
       "timestamp": "2025-01-01T12:00:00.000Z"
     }
     ```
4. Натисніть **Publish**

Web2 отримає це повідомлення в реальному часі — це демонструє **крос-протокольну взаємодію** між AMQP (Postman) та STOMP/WebSocket (браузер).

---

## ⚙️ Конфігурація RabbitMQ

Файл `rabbitmq.conf` налаштовує:
- **Management UI** на порту `15672`
- **STOMP plugin** — протокол для браузерних клієнтів
- **WebSTOMP** на порту `15674` — WebSocket транспорт
- **AMQP** на порту `5672` — для Postman та backend сервісів
- Доступ для `guest` з будь-якого хоста (тільки для розробки)

---

## 🏗️ Event-Driven концепція

| Компонент      | Роль                                                      |
|----------------|-----------------------------------------------------------|
| **RabbitMQ**   | Message Broker — маршрутизує події між сервісами          |
| **amq.fanout** | Exchange — розсилає повідомлення всім підписникам         |
| **Web1**       | Producer — генерує події (повідомлення)                   |
| **Web2**       | Consumer — реагує на події в реальному часі               |
| **Postman**    | External Producer — демонструє крос-протокольну підтримку |
| **STOMP**      | Протокол для браузерів поверх WebSocket                   |
| **AMQP**       | Нативний протокол RabbitMQ для backend/Postman            |

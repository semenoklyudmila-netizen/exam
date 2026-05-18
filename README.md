# Exam – Event-Driven Architecture with RabbitMQ

## Task 2

Демонстрація Event-Driven архітектури з RabbitMQ та двома веб-сервісами для обміну повідомленнями в реальному часі.

## Запуск

```bash
brew install rabbitmq
brew services start rabbitmq
rabbitmq-plugins enable rabbitmq_stomp rabbitmq_web_stomp
brew services restart rabbitmq
```

Потім відкрити файли у браузері:
```bash
open src/web1/index.html
open src/web2/index.html
```

## Сервіси

| Сервіс | URL | Опис |
|--------|-----|------|
| RabbitMQ Management | http://localhost:15672 | Панель управління (guest/guest) |
| Web1 (Publisher) | src/web1/index.html | Надсилання повідомлень |
| Web2 (Consumer) | src/web2/index.html | Отримання повідомлень |

## Перевірка роботи

### Підготовка середовища
Встановлено RabbitMQ через Homebrew та увімкнено необхідні плагіни STOMP і WebSocket.

### Web1 — Publisher
Відкрито файл src/web1/index.html у браузері. Сервіс підключився до RabbitMQ через WebSocket (ws://localhost:15674/ws) з credentials guest/guest. Статус змінився на "Підключено до RabbitMQ" (зелений індикатор). У полі повідомлення введено текст "Привіт від Web1!" та натиснуто кнопку "Надіслати". Повідомлення опубліковано в exchange /exchange/amq.fanout.

### Web2 — Consumer
Відкрито файл src/web2/index.html у браузері. Сервіс підключився до RabbitMQ через WebSocket та підписався на /exchange/amq.fanout. Статус змінився на "Підключено — слухаю повідомлення" (зелений індикатор).

### Результат
Після надсилання повідомлення з Web1 воно миттєво з'явилось у розділі "ВХІДНІ ПОВІДОМЛЕННЯ" на Web2. Крос-протокольна взаємодія між двома браузерними клієнтами через RabbitMQ STOMP/WebSocket працює коректно.

## Postman – надсилання повідомлення





POST http://localhost:15672/api/exchanges/%2F/amq.default/publish
Authorization: Basic guest:guest
Content-Type: application/json
{
"properties": {},
"routing_key": "exam_queue",
"payload": "Hello from Postman!",
"payload_encoding": "string"
}

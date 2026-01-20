Методы REST API (HTTP Methods)

| Метод   | Описание                    | Идемпотентность* | Безопасность** | Пример использования   |
|---------|-----------------------------|------------------|----------------|------------------------|
| GET     | Получение ресурса           | Да               | Да             | GET /api/users/1       |
| POST    | Создание ресурса            | Нет              | Нет            | POST /api/users        |
| PUT     | Полная замена ресурса       | Да               | Нет            | PUT /api/users/1       |
| PATCH   | Частичное обновление        | Нет              | Нет            | PATCH /api/users/1     |
| DELETE  | Удаление ресурса            | Да               | Нет            | DELETE /api/users/1    |

Дополнительные методы:

| Метод    | Назначение                                   |
|----------|----------------------------------------------|
| HEAD     | Получение заголовков (без тела)              |
| OPTIONS  | Получение поддерживаемых методов             |
| TRACE    | Диагностика, получение обратно запроса       |
| CONNECT  | Установка туннеля (для прокси)               |



### Сравнительная таблица REST API

| Критерий | GitHub REST API v3 | Telegram Bot API |
|----------|--------------------|------------------|
| **Основные эндпоинты и методы** | `GET /users/{username}`, `GET /repos/{owner}/{repo}`, `POST /repos/{owner}/{repo}/issues` | `GET /bot{token}/getMe`, `POST /bot{token}/sendMessage`, `POST /bot{token}/sendPhoto` |
| **Форматы запроса/ответа** | JSON (по умолчанию), возможность запроса в XML через заголовок Accept | Только JSON. Для отправки файлов: multipart/form-data |
| **Авторизация** | OAuth2 токены, Personal Access Tokens в заголовке `Authorization: Bearer <token>` | Токен бота в URL: `/bot<token>/methodName`. Простая авторизация через секретный токен в пути |
| **Версионирование** | В заголовке: `Accept: application/vnd.github.v3+json` | Версия в URL: `/bot<token>/methodName`. Нет явного версионирования, обновления обратно совместимы |
| **Особенности дизайна** | RESTful, HATEOAS. Ссылки на связанные ресурсы в ответах. Стандартные HTTP-коды. | RPC-стиль (Remote Procedure Call). Каждый метод — отдельный endpoint. Все запросы POST. Возвращает всегда объект с ok boolean и result. |
| **Структура ошибок** | `json { "message": "Not Found", "documentation_url": "..." }` | `json { "ok": false, "error_code": 404, "description": "Not Found" }` |
| **Пагинация** | Заголовки Link с `rel="next"/"prev"`. Параметры `?page=2&per_page=100` | Своя пагинация через параметры offset и limit в теле запроса |
| **Rate Limiting** | 5000 запросов в час. Заголовки `X-RateLimit-*` | Ограничение: ~30 сообщений/сек на бота. Без явных заголовков |
| **Документация** | developer.github.com/v3 | core.telegram.org/bots/api |

Пример JSON-запроса к Telegram Bot API
Метод: sendMessage
Документация: https://core.telegram.org/bots/api#sendmessage

### Анализ структуры JSON-запроса:

| Ключ | Тип данных | Обязательный | Описание |
|------|------------|--------------|-----------|
| **chat_id** | integer или string | Да | Уникальный идентификатор чата или username |
| **text** | string | Да | Текст сообщения (1-4096 символов) |
| **parse_mode** | string | Нет | Форматирование: "MarkdownV2", "HTML", "Markdown" |
| **disable_notification** | boolean | Нет | Отправлять без звука |
| **reply_to_message_id** | integer | Нет | ID сообщения для ответа |
| **reply_markup** | object | Нет | Клавиатура или inline-кнопки |
| **reply_markup.inline_keyboard** | array of array | Нет | Массив рядов кнопок |
| **reply_markup.inline_keyboard[][]**.text | string | Да | Текст на кнопке |
| **reply_markup.inline_keyboard[][]**.callback_data | string | Нет* | Данные для callback (до 64 байт) |
| **reply_markup.inline_keyboard[][]**.url | string | Нет* | URL для кнопки-ссылки |





# **Пример JSON-запроса к Telegram Bot API**

Метод: sendMessage

Документация: https://core.telegram.org/bots/api#sendmessage

{
  "chat_id": 123456789,

  "text": "Hello, world!",
  "parse_mode": "HTML",

  "disable_notification": true,

  "reply_to_message_id": 123,

  "reply_markup": {

    "inline_keyboard": [
      [
        {
          "text": "Button 1",
          "callback_data": "action_1"
        },
        {
          "text": "Button 2",
          "url": "https://example.com"
        }
      ]
    ]
  }



# Критерии отбора:
- Доступность из РФ — документация и сам API должны быть доступны

- Разные архитектурные стили — чтобы показать контраст в подходах

- Качество документации — подробная, с примерами

- Популярность — широко используемые в разработке

Разная сложность — от учебного до Прода

**Выбранные API:**

- GitHub API v3 — классический RESTful API (учебный)

- Telegram Bot API — RPC-стиль, очень популярен в реальных проектах

**Альтернативы, которые рассматривались:**

- JSONPlaceholder — слишком простой для сравнения (только GET методы)
- Stripe - недоступен без ВПН


1. Преимущества JSON над XML и YAML:
JSON vs XML:
- Меньше кода - нет закрывающих тегов
- Быстрее парсится - особенно в JavaScript
- Легче читается человеком
- Меньший размер данных
- Нативная поддержка в JavaScript

2. JSON vs YAML:
- Строже - меньше неоднозначностей
- Безопаснее - YAML может выполнять код
- Стандарт для API - все REST API используют JSON
- Не зависит от пробелов - в YAML пробелы значимы
- Лучше для передачи данных между системами

3. JSON Schema:
JSON Schema — это стандарт для описания структуры JSON-документов.
Для чего:

- Валидация данных

- Документация формата

- Генерация кода и форм

- Тестирование API

Сериализация (Marshalling):
Преобразование объекта → JSON-строка

Десериализация (Unmarshalling):
Преобразование JSON-строки → объект
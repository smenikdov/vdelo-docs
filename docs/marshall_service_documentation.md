# Документация API
Версия: 1.0.0

**Данные для отправки запросов:**
*   **Рабочий URL:** `https://vdelo.pro`
*   **Тестовый URL:** `https://dev.vdelo.pro`
*   **Тестовый телефон для авторизации:** `70000000312`
*   **Тестовый пароль для авторизации:** `6WRk~bFMdY#L`

## 1. `POST /api/sign/login`

**Описание**: Авторизация пользователя в системе.

### Тело запроса

| Свойство | Тип     | Обязательно | Описание      |
| :------- | :------- | :------- | :------------ |
| `phone`  | `string` | Да       | Номер телефона |
| `password` | `string` | Да       | Пароль пользователя |

### Ответы

#### `200 OK`

```json
{
  "message": {
    "successful": true,
    "access": "string",
    "expired": "string",
    "userRole": "number",
    "userId": "number",
    "userName": "string"
  },
  "statusCode": 200
}
```

### Пример использования

```bash
curl https://dev.vdelo.pro/api/sign/login \
  -H 'Content-Type: application/json' \
  -d '{
    "phone":"70000000312",
    "password":"6WRk~bFMdY#L"
  }'
```

---

## 2. `POST /api/admin/marshall/requests`

**Описание**: Создает новую заявку в системе.

### Тело запроса

| Свойство             | Тип      | Обязательно | Описание                                  |
| :------------------- | :-------- | :------- | :------------------------------------------- |
| `userData`           | `object`  | Да       | Персональные данные пользователя             |
| `userData.lastName`  | `string`  | Да       | Фамилия                                      |
| `userData.firstName` | `string`  | Да       | Имя                                          |
| `userData.middleName`| `string`, `null` | Нет      | Отчество (необязательно)                     |
| `userData.birthDate` | `string`  | Да       | Дата рождения (ГГГГ-ММ-ДД)                   |
| `userData.inn`       | `string`  | Да       | ИНН физического лица (12 цифр)               |
| `userData.snils`     | `string`  | Да       | СНИЛС (11 цифр)                              |
| `userData.passportSeries` | `string`  | Да       | Серия паспорта (4 цифры, например: 4512)     |
| `userData.passportNumber` | `string`  | Да       | Номер паспорта (6 цифр)                      |
| `userData.passportIssuedBy` | `string`  | Да      | Кем выдан                                    |
| `userData.passportIssueDate` | `string`  | Да       | Дата выдачи паспорта (ГГГГ-ММ-ДД)            |
| `userData.passportDepartmentCode` | `string`  | Да      | Код подразделения (например: 770-001)        |
| `userData.registrationAddress` | `string`  | Да       | Адрес регистрации                            |
| `userData.phone`     | `string`  | Да       | Номер телефона (например: +79991234567)      |
| `userData.email`     | `string`  | Да       | Электронная почта                            |
| `userData.birthPlace`| `string`  | Да      | Место рождения                               |
| `companyInn`         | `string`, `null` | Нет      | ИНН компании (10 цифр, если ИП, то не передавать) |
| `requisites`         | `object`  | Да       | Банковские реквизиты                         |
| `requisites.accountNumber` | `string`  | Да       | Расчётный счёт (20 цифр)                     |
| `requisites.correspondentAccount` | `string`  | Да       | Корреспондентский счёт (20 цифр)             |
| `requisites.bankBik` | `string`  | Да       | БИК банка (9 цифр)                           |
| `requisites.bankName`| `string`  | Да       | Название банка                               |
| `requisites.bankKpp` | `string`  | Да       | КПП (9 цифр)                                 |

### Заголовки запроса

| Свойство | Тип     | Обязательно | Описание      |
| :------- | :------- | :------- | :------------ |
| `access` | `string` | Да       | Токен доступа |

### Ответы

#### `200 OK`

```json
{
  "status": "string",
  "requestUUID": "string",
  "debugRequestId": "string"
}
```

#### `400 Bad Request` (Неверный запрос)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `403 Forbidden` (Доступ запрещен)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `409 Conflict` (Конфликт)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

### Пример использования

```bash
curl https://dev.vdelo.pro/api/admin/marshall/requests \
  -H "Content-Type: application/json" \
  -H "access: $TOKEN" \
  -d '{
    "userData": {
        "lastName": "Иванов",
        "firstName": "Иван",
        "middleName": "Иванович",
        "birthDate": "1990-01-01",
        "inn": "772315938849",
        "snils": "00000000000",
        "passportSeries": "4577",
        "passportNumber": "785245",
        "passportIssuedBy": "ГУ МВД России по г. Москве",
        "passportIssueDate": "2010-03-15",
        "passportDepartmentCode": "770-001",
        "registrationAddress": "г. Москва, ул. Примерная, д. 1, кв. 1",
        "phone": "+79341284526",
        "email": "test@example.com",
        "placeOfBirth": "г. Москва"
    },
    "companyInn": "7730611598",
    "requisites": {
        "accountNumber": "12345678901234567890",
        "correspondentAccount": "09876543210987654321",
        "bankBik": "044525225",
        "bankName": "Сбербанк",
        "bankKpp": "770012345"
    }
  }'
```

---

## 3. `GET /api/admin/marshall/requests/:requestUUID`

**Описание**: Получает детали конкретной заявки по ее UUID.

### Параметры пути

| Свойство    | Тип     | Обязательно | Описание            |
| :---------- | :------- | :------- | :------------------ |
| `requestUUID` | `string` | Да       | UUID заявки (36 символов) |

### Заголовки запроса

| Свойство | Тип     | Обязательно | Описание      |
| :------- | :------- | :------- | :------------ |
| `access` | `string` | Да       | Токен доступа |

### Ответы

#### `200 OK`

```json
{
  "status": "string",
  "debugRequestId": "string",
  "request": "object"
}
```

#### `400 Bad Request` (Неверный запрос)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `403 Forbidden` (Доступ запрещен)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `404 Not Found` (Не найдено)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

### Пример использования

```bash
curl https://dev.vdelo.pro/api/admin/marshall/requests/47f3dc6d-2a53-408b-8133-03241d5e58ad \
  -H "access: $TOKEN"
```

---

## 4. `GET /api/admin/marshall/requests`

**Описание**: Получает список всех заявок.

### Заголовки запроса

| Свойство | Тип     | Обязательно | Описание      |
| :------- | :------- | :------- | :------------ |
| `access` | `string` | Да       | Токен доступа |

### Ответы

#### `200 OK`

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string",
  "requests": [
    "object"
  ]
}
```

#### `400 Bad Request` (Неверный запрос)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `403 Forbidden` (Доступ запрещен)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

### Пример использования

```bash
curl https://dev.vdelo.pro/api/admin/marshall/requests \
  -H "access: $TOKEN"
```

---

## 5. `POST /api/admin/marshall/extracts`

**Описание**: Загружает файл выписки для конкретной заявки.

### Тело запроса

| Свойство    | Тип     | Обязательно | Описание            |
| :---------- | :------- | :------- | :------------------ |
| `file`      | `array`  | Да       | Выписка в формате 1C |
| `requestUUID` | `string` | Да       | UUID заявки (36 символов) |

### Заголовки запроса

| Свойство | Тип     | Обязательно | Описание      |
| :------- | :------- | :------- | :------------ |
| `access` | `string` | Да       | Токен доступа |

### Ответы

#### `400 Bad Request` (Неверный запрос)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

#### `403 Forbidden` (Доступ запрещен)

```json
{
  "status": "string",
  "message": "string",
  "debugRequestId": "string"
}
```

### Пример использования

```bash
curl https://dev.vdelo.pro/api/admin/marshall/extracts \
  -H "access: $TOKEN" \
  -F "file=@extract.txt" \
  -F "requestUUID=47f3dc6d-2a53-408b-8133-03241d5e58ad"
```
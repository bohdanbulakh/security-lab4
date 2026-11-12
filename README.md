# Лабораторна робота 4

Модифікований застосунок [token_auth](https://github.com/Kreolwolf1/auth_examples/tree/main/token_auth):
перевірка користувача і отримання токена виконуються через Auth0.

- **Вхід**: `POST /api/login` -> Auth0 `/oauth/token` з `grant_type=password` (scope `openid profile email offline_access`).
  Токени зберігаються в серверній сесії, клієнт отримує лише id сесії (заголовок `Authorization`, як в оригіналі).
- **Реєстрація** (додаткове завдання): `POST /api/register` -> токен Management API через `client_credentials`
  (кешується до завершення життя) -> `POST /api/v2/users`.
- **Перевірка життя токена** (додаткове завдання): middleware `requireAuth` перед кожним захищеним запитом
  перевіряє `expires_at`; якщо лишилось менше `REFRESH_THRESHOLD_SECONDS`, токен оновлюється через `grant_type=refresh_token`.
  Сторінка показує зворотний відлік і сама звертається до сервера, коли настає час оновлення.
  `POST /api/refresh` примусово оновлює токен.

## Налаштування Auth0

У застосунку (Applications > *app* > Advanced Settings > Grant Types) мають бути увімкнені:
**Password**, **Refresh Token**, **Client Credentials**. У налаштуваннях тенанта Default Directory має бути
`Username-Password-Authentication` (як у лаб. 3). Застосунок має бути авторизований до Auth0 Management API
зі scope `create:users`.

## Запуск

```bash
cp .env.example .env   # вписати domain / client_id / client_secret
npm install
npm start              # http://localhost:3000
```

Щоб побачити автоматичне оновлення, не чекаючи добу (стандартний час життя токена - 86400 с), можна
поставити `REFRESH_THRESHOLD_SECONDS=86340`: токен оновиться приблизно через хвилину після входу.

## Postman

У `postman/` лежать колекція і environment "Security lab 4" (Import -> файли). `Login` сам записує
`session_token` в environment, решта запитів передають його в заголовку `Authorization` (без `Bearer`).

# Cyber Security

## My Information

- Puttakorn Somkana
- Tanatip Aiamongart
- Thanachot Najainuek

## เนเธเธฃเธเธชเธฃเนเธฒเธเนเธเธฃเน€เธเธเธ•เน

เธฃเธฐเธเธ **เธซเนเธญเธเธชเธกเธธเธ” (Library)** เธเธ Appwrite 1.5 เนเธเธ self-hosted เธ”เนเธงเธข Docker Compose
เธ—เธ”เธชเธญเธ REST API (Auth + Database CRUD + Queries) เธเนเธฒเธเนเธเธฅเน `api.http`

| Service | Container | เธเธณเธญเธเธดเธเธฒเธข |
| --- | --- | --- |
| `app` | `69-s1-appwrite` | API + Console (`http://localhost:9092`) |
| `appwrite-worker-*` | `69-s1-appwrite-worker-*` | Worker 10 เธ•เธฑเธง (databases, usage, audits, webhooks, certificates, functions, deletes, mails, messaging, migrations) |
| `mariadb` | `69-s1-mariadb` | เธเธฒเธเธเนเธญเธกเธนเธฅเธซเธฅเธฑเธ |
| `redis` | `69-s1-redis` | Cache + Queue |
| `mailpit` | `69-s1-mailpit` | SMTP เธเธณเธฅเธญเธเธชเธณเธซเธฃเธฑเธ dev (UI `http://localhost:8025`) |

**เธเธฒเธฃเนเธเนเธ config**

- เธเนเธฒ infrastructure (secret, DB, Redis, SMTP) เธญเธขเธนเนเนเธ `docker-compose.yaml`
- `.env` เน€เธเนเธเน€เธเธเธฒเธฐเธเนเธฒเธ—เธตเนเนเธเนเธ—เธ”เธชเธญเธ API (project id, endpoint, เธเนเธญเธกเธนเธฅเธ•เธฑเธงเธญเธขเนเธฒเธ) เธชเธณเธซเธฃเธฑเธ `api.http`
- **worker เธเธณเน€เธเนเธเธกเธฒเธ** โ€” เธเธฒเธฃเธชเธฃเนเธฒเธ attribute เธเธญเธ Appwrite เน€เธเนเธเนเธเธ asynchronous เธ–เนเธฒเนเธกเนเธกเธต worker `databases` attribute เธเธฐเธเนเธฒเธเธชเธ–เธฒเธเธฐ `processing` เนเธฅเธฐ insert เธเนเธญเธกเธนเธฅเนเธกเนเนเธ”เน

## เธชเธดเนเธเธ—เธตเนเธ•เนเธญเธเธกเธต

- Docker Desktop (เธซเธฃเธทเธญ Docker Engine + Compose v2)
- VS Code + เธชเนเธงเธเธเธขเธฒเธข [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

## เน€เธฃเธดเนเธกเนเธเนเธเธฒเธ

```powershell
# 1. เธชเธฃเนเธฒเธเนเธเธฅเน .env เธเธฒเธ template
Copy-Item .env.simple .env

# 2. เน€เธเธดเธ” Appwrite
docker compose up -d
```

3. เน€เธเธดเธ” `http://localhost:9092` โ’ เธชเธฃเนเธฒเธเธเธฑเธเธเธต Console เนเธฅเธฐ Project
4. เธเธฑเธ”เธฅเธญเธ **Project ID** เนเธฅเธฐเธชเธฃเนเธฒเธ **API key** (เนเธซเนเธชเธดเธ—เธเธดเน `databases`, `collections`, `attributes`, `documents`, `users`) เธกเธฒเนเธชเนเนเธ `.env`
5. เนเธชเนเธเนเธฒ `USER_LOGIN_*` / `USER_REGISTER_*` เนเธ `.env`
6. เน€เธเธดเธ” `api.http` โ’ เธฃเธฑเธ `1.1 Login`
7. เธเธฒเธ response เธเธญเธ `1.1 Login` เธเธฑเธ”เธฅเธญเธเธเนเธฒ token เนเธ header `Set-Cookie` (เธเนเธฒเธซเธฅเธฑเธ `a_session_<PROJECT_ID>=` เธเธเธ–เธถเธ `;`) เนเธเนเธชเนเนเธ `.env` เธ—เธตเน `APPWRITE_SESSION_TOKEN`
8. เธ—เธ”เธชเธญเธ section 2โ€“6 เนเธ”เนเน€เธฅเธข

> `{{$dotenv ...}}` เนเธ `api.http` เธญเนเธฒเธเธเนเธฒเธเธฒเธ `.env` เธ—เธตเนเธญเธขเธนเนเนเธเธฅเน€เธ”เธญเธฃเนเน€เธ”เธตเธขเธงเธเธฑเธเนเธเธฅเน `.http` เนเธ”เธขเธญเธฑเธ•เนเธเธกเธฑเธ•เธด

> **เธ—เธณเนเธกเธ•เนเธญเธ copy token เน€เธญเธ?** Appwrite 1.5 เน€เธเธฅเธตเนเธขเธเธฃเธนเธเนเธเธ session เน€เธเนเธ token เธ—เธตเนเธกเธต `secret` เธเธเธงเธเธญเธขเธนเนเนเธ base64 เนเธฅเธฐ**เนเธกเนเธชเนเธ response header `x-appwrite-session` เธเธฅเธฑเธเธกเธฒ** เนเธ•เนเธชเนเธเธเนเธฒเธ `Set-Cookie` เนเธ—เธ REST Client เธเธญเธ VS Code เธเธฑเธ”เธเธฒเธฃ cookie เนเธซเนเน€เธญเธเนเธกเนเนเธ”เน เธเธถเธเธ•เนเธญเธ copy เธเนเธฒเธเธฑเนเธเธกเธฒเธชเนเธเน€เธเนเธ `X-Appwrite-Session` เน€เธญเธ

## เนเธเธฃเธเธชเธฃเนเธฒเธเธเนเธญเธกเธนเธฅ (เธฃเธฐเธเธเธซเนเธญเธเธชเธกเธธเธ”)

Database `main` เธกเธต 5 collection โ€” collection เน€เธ”เธดเธก (`students`, `subjects`, `teachers`) เธ–เธนเธเธฅเธเนเธฅเนเธง
เธ—เธธเธ collection เนเธเน `documentSecurity = true` เนเธฅเธฐเธชเธดเธ—เธเธดเนเธฃเธฐเธ”เธฑเธ collection เน€เธเนเธ
`create("users"), read("users"), update("users"), delete("users")` โ’ เธ•เนเธญเธ Login เธเนเธญเธเธเธถเธเธเธฐเธญเนเธฒเธ/เน€เธเธตเธขเธเนเธ”เน

| Collection | Attribute | Type | Req | Default | เธซเธกเธฒเธขเน€เธซเธ•เธธ |
| --- | --- | --- | --- | --- | --- |
| `categories` | `name` | string(255) | yes | | |
| | `slug` | string(255) | yes | | unique |
| | `description` | string(1000) | no | | |
| `profiles` | `user_id` | string(255) | yes | | unique, เธเธนเธเธเธฑเธ Appwrite user |
| | `member_id` | string(50) | yes | | unique, เธฃเธซเธฑเธชเธชเธกเธฒเธเธดเธ |
| | `name` | string(255) | yes | | |
| | `email` | email | yes | | |
| | `phone` | string(20) | no | | |
| | `role` | enum | no | `member` | `member` / `librarian` / `admin` |
| | `joined_at` | datetime | yes | | |
| `books` | `title` | string(255) | yes | | |
| | `author` | string(255) | yes | | |
| | `isbn` | string(50) | no | | unique |
| | `publisher` | string(255) | no | | |
| | `publish_year` | integer | no | | 0โ€“3000 |
| | `category_id` | string(255) | yes | | เธญเนเธฒเธเธญเธดเธ `categories` |
| | `total_copies` | integer | no | `1` | 0โ€“100000 |
| | `available_copies` | integer | no | `1` | 0โ€“100000 |
| | `cover_url` | url | no | | |
| | `description` | string(2000) | no | | |
| `borrowings` | `user_id` | string(255) | yes | | เธญเนเธฒเธเธญเธดเธ `profiles.user_id` |
| | `book_id` | string(255) | yes | | เธญเนเธฒเธเธญเธดเธ `books` |
| | `borrow_date` | datetime | yes | | |
| | `due_date` | datetime | yes | | |
| | `return_date` | datetime | no | | เธงเนเธฒเธ = เธขเธฑเธเนเธกเนเธเธทเธ |
| | `status` | enum | no | `borrowed` | `borrowed` / `returned` / `overdue` / `lost` |
| | `fine` | float | no | | เธเนเธฒเธเธฃเธฑเธ |
| `reservations` | `user_id` | string(255) | yes | | เธญเนเธฒเธเธญเธดเธ `profiles.user_id` |
| | `book_id` | string(255) | yes | | เธญเนเธฒเธเธญเธดเธ `books` |
| | `reserved_at` | datetime | yes | | |
| | `fulfilled_at` | datetime | no | | เธงเนเธฒเธ = เธขเธฑเธเธฃเธญเธญเธขเธนเน |
| | `status` | enum | no | `waiting` | `waiting` / `fulfilled` / `cancelled` / `expired` |
| | `queue_position` | integer | no | | เธฅเธณเธ”เธฑเธเธเธดเธง 0โ€“100000 |
| | `note` | string(500) | no | | |

### Index

| Collection | Index | Type | Attribute |
| --- | --- | --- | --- |
| `categories` | `key_slug` | unique | `slug` |
| | `idx_name` | key | `name` |
| `profiles` | `key_user_id` | unique | `user_id` |
| | `key_member_id` | unique | `member_id` |
| | `idx_role` | key | `role` |
| `books` | `key_isbn` | unique | `isbn` |
| | `idx_category_id` | key | `category_id` |
| | `idx_title` | key | `title` |
| `borrowings` | `idx_user_id` | key | `user_id` |
| | `idx_book_id` | key | `book_id` |
| | `idx_status` | key | `status` |
| | `idx_due_date` | key | `due_date` |
| `reservations` | `idx_user_id` | key | `user_id` |
| | `idx_book_id` | key | `book_id` |
| | `idx_status` | key | `status` |

### เธฃเธนเธเนเธเธ `queries` เธเธญเธ Appwrite 1.5

`queries` เน€เธเนเธ **array param** เธ•เนเธญเธเธชเนเธเธเนเธณเน€เธเนเธ `queries[0]`, `queries[1]`, ... (เนเธกเนเนเธเน JSON array เน€เธ”เธตเธขเธง)

```text
?queries[0]={"method":"equal","attribute":"status","values":["waiting"]}&queries[1]={"method":"limit","values":[25]}
```

เธเนเธฒ JSON เธ•เนเธญเธ URL-encode เธ–เนเธฒเน€เธฃเธตเธขเธเธ”เนเธงเธข client เธ—เธตเน encode เนเธซเนเธญเธฑเธ•เนเธเธกเธฑเธ•เธด (เน€เธเนเธ Postman/SDK) เนเธกเนเธ•เนเธญเธเธ—เธณเน€เธญเธ

## เธเธณเธชเธฑเนเธเธ—เธตเนเนเธเนเธเนเธญเธข

```powershell
docker compose up -d            # เธชเธ•เธฒเธฃเนเธ— / เธญเธฑเธเน€เธ”เธ•
docker compose ps               # เธ”เธนเธชเธ–เธฒเธเธฐ
docker compose logs -f app      # เธ”เธน log
docker compose down             # เธซเธขเธธเธ” (เธเนเธญเธกเธนเธฅเธขเธฑเธเธญเธขเธนเน)
docker compose down -v          # เธซเธขเธธเธ” + เธฅเธเธเนเธญเธกเธนเธฅเธ—เธฑเนเธเธซเธกเธ”
```

เธ•เธฃเธงเธเธชเธธเธเธ เธฒเธเธฃเธฐเธเธเธ•เธฒเธกเธกเธฒเธ•เธฃเธเธฒเธ Appwrite:

```powershell
docker exec 69-s1-appwrite php app/cli.php doctor
```

## เนเธเนเธเธฑเธเธซเธฒ (Troubleshooting)

| เธญเธฒเธเธฒเธฃ | เธชเธฒเน€เธซเธ•เธธ | เธงเธดเธเธตเนเธเน |
| --- | --- | --- |
| `Unknown attribute` เธ•เธญเธ insert | attribute เธเนเธฒเธเธชเธ–เธฒเธเธฐ `processing` เน€เธเธฃเธฒเธฐเนเธกเนเธกเธต worker `databases` | `docker compose up -d` เนเธซเน worker เธเธฃเธ |
| เธ—เธธเธ request เนเธ”เน `500` / `getHeader(): Argument #2 must be of type string` | เนเธกเนเนเธ”เนเธ•เธฑเนเธ `_APP_SYSTEM_RESPONSE_FORMAT` | เธ•เนเธญเธเธกเธตเธ•เธฑเธงเนเธเธฃเธเธตเนเนเธ `docker-compose.yaml` (เธ•เธฑเนเธเน€เธเนเธเธเนเธฒเธงเนเธฒเธเนเธ”เน) |
| `401 User (role: guests) missing scope (...)` | เธขเธฑเธเนเธกเนเนเธ”เน Login เธซเธฃเธทเธญเธขเธฑเธเนเธกเนเนเธชเน `APPWRITE_SESSION_TOKEN` | เธฃเธฑเธ `1.1 Login` เนเธฅเนเธง copy token เธเธฒเธ `Set-Cookie` เนเธเนเธชเนเนเธ `.env` |
| `401` เธ•เธญเธ create/update/delete | collection เนเธเน `read("users")` / `create("users")` | guest เธ—เธณเนเธกเนเนเธ”เน เธ•เนเธญเธ login (list เธเธฐเนเธ”เน 200 เนเธ•เนเธงเนเธฒเธเน€เธเธฅเนเธฒ เนเธกเนเนเธเน 401) |
| list เนเธ”เน 200 เนเธ•เน `total = 0` เธ—เธฑเนเธเธ—เธตเนเธกเธตเธเนเธญเธกเธนเธฅ | เธขเธฑเธเนเธกเน Login เธซเธฃเธทเธญเธชเนเธ token เธเธดเธ” | เธ•เธฃเธงเธ `APPWRITE_SESSION_TOKEN` |
| `Cannot set default value for required attribute` | Appwrite 1.5 เธซเนเธฒเธกเธ•เธฑเนเธ default เนเธซเน attribute เธ—เธตเน `required` | เธ•เธฑเนเธ `required = false` เธเธนเนเธเธฑเธ `default` |
| `400 Invalid 'orders' param: must be one of (asc, desc)` | เธเนเธฒ order เธ•เนเธญเธเธเธดเธกเธเนเน€เธฅเนเธ | เนเธเน `asc` / `desc`; unique เนเธซเนเนเธเน `type: "unique"` เนเธ—เธ |
| `400 Invalid 'queries' param` | เธชเนเธ `queries` เน€เธเนเธ JSON array เน€เธ”เธตเธขเธง | เธ•เนเธญเธเธชเนเธเธเนเธณเน€เธเนเธ `queries[0]=...&queries[1]=...` |
| `400 Invalid query: Query value is invalid for attribute "..."` | เธเนเธฒเนเธ query เธกเธต `+` (เน€เธเนเธ `+00:00`) โ€” `+` เนเธ URL เธ–เธนเธเธญเนเธฒเธเน€เธเนเธเธเนเธญเธเธงเนเธฒเธ | เน€เธเธตเธขเธ datetime เนเธเธฃเธนเธเนเธเธ `Z` เน€เธเนเธ `2026-10-05T07:00:00.000Z` เธซเธฃเธทเธญ encode เน€เธเนเธ `%2B` |
| `401 User maximum sessions reached` | login เธชเธฐเธชเธกเน€เธเธดเธ 10 session (เธเนเธฒ default เธเธญเธเนเธเธฃเน€เธเธเธ•เน) | เธฅเธ session เน€เธเนเธฒเธเนเธฒเธ Console โ’ Users โ’ Sessions |
| register เนเธ”เน `409` | email เธ—เธตเนเธชเธกเธฑเธเธฃเธกเธตเธญเธขเธนเนเนเธฅเนเธง | เน€เธเธฅเธตเนเธขเธ `USER_REGISTER_EMAIL` |
| update เนเธ”เน `400 Unknown attribute: ...` | เธเธทเนเธญ field เนเธกเนเธ•เธฃเธเธเธฑเธ attribute เธเธญเธ collection | เธ•เธฃเธงเธเธเธทเนเธญ attribute เนเธ Console เนเธซเนเธ•เธฃเธเธเธฑเธ |
| Login เนเธกเนเนเธ”เนเธซเธฅเธฑเธเนเธเน secret | เน€เธเธฅเธตเนเธขเธ `_APP_OPENSSL_KEY_V1` (เนเธเนเธ–เธญเธ”เธฃเธซเธฑเธช password) | เธเธทเธเธเนเธฒเน€เธ”เธดเธก เธซเนเธฒเธกเน€เธเธฅเธตเนเธขเธเธซเธฅเธฑเธเน€เธฃเธดเนเธกเนเธเนเธเธฒเธ |

> เธซเธกเธฒเธขเน€เธซเธ•เธธ: เนเธ Appwrite 1.5.0 เธเธฒเธฃเน€เธเธฅเธตเนเธขเธ collection permission เนเธซเนเนเธเน `PUT /v1/databases/{db}/collections/{collectionId}`
> เธเธฃเนเธญเธกเธชเนเธ `name` เนเธเธ”เนเธงเธขเน€เธชเธกเธญ เนเธฅเธฐเธ–เนเธฒเนเธกเนเธชเนเธ `documentSecurity` เธเนเธฒเธเธฐเธ–เธนเธเธฃเธตเน€เธเนเธ•เน€เธเนเธ `false`

## เธซเธกเธฒเธขเน€เธซเธ•เธธเธ”เนเธฒเธเธเธงเธฒเธกเธเธฅเธญเธ”เธ เธฑเธข

- `.env` เนเธฅเธฐ `api.http` เธ–เธนเธ ignore เนเธกเน commit โ€” เนเธ•เน `docker-compose.yaml` **เธกเธต secret เธเธฃเธดเธเธญเธขเธนเน** เธ–เนเธฒเธเธฐเน€เธเธขเนเธเธฃเน repo เธ•เนเธญเธเธ—เธณเนเธซเนเน€เธเนเธ private เธซเธฃเธทเธญเธขเนเธฒเธข secret เธญเธญเธ
- `.env` เธกเธต `APPWRITE_SESSION_TOKEN` เธเธถเนเธเน€เธเนเธ session เธ—เธตเนเนเธเนเธเธฒเธเนเธ”เนเธเธฃเธดเธ โ€” เธญเธขเนเธฒ commit เธซเธฃเธทเธญเนเธเธฃเนเนเธเธฅเนเธเธตเน
- เธชเธดเธ—เธเธดเนเธฃเธฐเธ”เธฑเธ collection เน€เธเนเธ `("users")` เธ—เธฑเนเธเธซเธกเธ” = เธ—เธธเธเธเธเธ—เธตเน login เนเธฅเนเธงเธญเนเธฒเธ/เน€เธเธตเธขเธเนเธ”เนเธ—เธธเธ document (เธขเธฑเธเนเธกเนเธกเธตเธเธฒเธฃเนเธขเธเธชเธดเธ—เธเธดเนเธ•เธฒเธก `role`)
- เธเนเธฒ `_APP_OPTIONS_ABUSE=disabled` เนเธฅเธฐ `_APP_OPTIONS_FORCE_HTTPS=disabled` เน€เธซเธกเธฒเธฐเธเธฑเธ dev เน€เธ—เนเธฒเธเธฑเนเธ
- mariadb / redis เนเธกเนเน€เธเธดเธ” port เธญเธญเธเธ เธฒเธขเธเธญเธ เน€เธเนเธฒเธ–เธถเธเนเธ”เนเน€เธเธเธฒเธฐเนเธ Docker network


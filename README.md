# Cyber Security

## My Information

- Puttakorn Somkana
- Student ID 0568604050xx-x

## My Expectations for this Course

- To gain knowledge about cyber threats, attacks, and security risks.
- To learn how to protect data, computer systems, and networks from cyberattacks.
- To develop practical cybersecurity skills and hands-on experience with security tools.
- To apply cybersecurity knowledge in real-world situations and future careers.

## โครงสร้างโปรเจกต์

ระบบ **ห้องสมุด (Library)** บน Appwrite 1.5 แบบ self-hosted ด้วย Docker Compose
ทดสอบ REST API (Auth + Database CRUD + Queries) ผ่านไฟล์ `api.http`

| Service | Container | คำอธิบาย |
| --- | --- | --- |
| `app` | `69-s1-appwrite` | API + Console (`http://localhost:9092`) |
| `appwrite-worker-*` | `69-s1-appwrite-worker-*` | Worker 10 ตัว (databases, usage, audits, webhooks, certificates, functions, deletes, mails, messaging, migrations) |
| `mariadb` | `69-s1-mariadb` | ฐานข้อมูลหลัก |
| `redis` | `69-s1-redis` | Cache + Queue |
| `mailpit` | `69-s1-mailpit` | SMTP จำลองสำหรับ dev (UI `http://localhost:8025`) |

**การแบ่ง config**

- ค่า infrastructure (secret, DB, Redis, SMTP) อยู่ใน `docker-compose.yaml`
- `.env` เก็บเฉพาะค่าที่ใช้ทดสอบ API (project id, endpoint, ข้อมูลตัวอย่าง) สำหรับ `api.http`
- **worker จำเป็นมาก** — การสร้าง attribute ของ Appwrite เป็นแบบ asynchronous ถ้าไม่มี worker `databases` attribute จะค้างสถานะ `processing` และ insert ข้อมูลไม่ได้

## สิ่งที่ต้องมี

- Docker Desktop (หรือ Docker Engine + Compose v2)
- VS Code + ส่วนขยาย [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

## เริ่มใช้งาน

```powershell
# 1. สร้างไฟล์ .env จาก template
Copy-Item .env.simple .env

# 2. เปิด Appwrite
docker compose up -d
```

3. เปิด `http://localhost:9092` → สร้างบัญชี Console และ Project
4. คัดลอก **Project ID** และสร้าง **API key** (ให้สิทธิ์ `databases`, `collections`, `attributes`, `documents`, `users`) มาใส่ใน `.env`
5. ใส่ค่า `USER_LOGIN_*` / `USER_REGISTER_*` ใน `.env`
6. เปิด `api.http` → รัน `1.1 Login`
7. จาก response ของ `1.1 Login` คัดลอกค่า token ใน header `Set-Cookie` (ค่าหลัง `a_session_<PROJECT_ID>=` จนถึง `;`) ไปใส่ใน `.env` ที่ `APPWRITE_SESSION_TOKEN`
8. ทดสอบ section 2–6 ได้เลย

> `{{$dotenv ...}}` ใน `api.http` อ่านค่าจาก `.env` ที่อยู่โฟลเดอร์เดียวกับไฟล์ `.http` โดยอัตโนมัติ

> **ทำไมต้อง copy token เอง?** Appwrite 1.5 เปลี่ยนรูปแบบ session เป็น token ที่มี `secret` ผนวกอยู่ใน base64 และ**ไม่ส่ง response header `x-appwrite-session` กลับมา** แต่ส่งผ่าน `Set-Cookie` แทน REST Client ของ VS Code จัดการ cookie ให้เองไม่ได้ จึงต้อง copy ค่านั้นมาส่งเป็น `X-Appwrite-Session` เอง

## โครงสร้างข้อมูล (ระบบห้องสมุด)

Database `main` มี 5 collection — collection เดิม (`students`, `subjects`, `teachers`) ถูกลบแล้ว
ทุก collection ใช้ `documentSecurity = true` และสิทธิ์ระดับ collection เป็น
`create("users"), read("users"), update("users"), delete("users")` → ต้อง Login ก่อนจึงจะอ่าน/เขียนได้

| Collection | Attribute | Type | Req | Default | หมายเหตุ |
| --- | --- | --- | --- | --- | --- |
| `categories` | `name` | string(255) | yes | | |
| | `slug` | string(255) | yes | | unique |
| | `description` | string(1000) | no | | |
| `profiles` | `user_id` | string(255) | yes | | unique, ผูกกับ Appwrite user |
| | `member_id` | string(50) | yes | | unique, รหัสสมาชิก |
| | `name` | string(255) | yes | | |
| | `email` | email | yes | | |
| | `phone` | string(20) | no | | |
| | `role` | enum | no | `member` | `member` / `librarian` / `admin` |
| | `joined_at` | datetime | yes | | |
| `books` | `title` | string(255) | yes | | |
| | `author` | string(255) | yes | | |
| | `isbn` | string(50) | no | | unique |
| | `publisher` | string(255) | no | | |
| | `publish_year` | integer | no | | 0–3000 |
| | `category_id` | string(255) | yes | | อ้างอิง `categories` |
| | `total_copies` | integer | no | `1` | 0–100000 |
| | `available_copies` | integer | no | `1` | 0–100000 |
| | `cover_url` | url | no | | |
| | `description` | string(2000) | no | | |
| `borrowings` | `user_id` | string(255) | yes | | อ้างอิง `profiles.user_id` |
| | `book_id` | string(255) | yes | | อ้างอิง `books` |
| | `borrow_date` | datetime | yes | | |
| | `due_date` | datetime | yes | | |
| | `return_date` | datetime | no | | ว่าง = ยังไม่คืน |
| | `status` | enum | no | `borrowed` | `borrowed` / `returned` / `overdue` / `lost` |
| | `fine` | float | no | | ค่าปรับ |
| `reservations` | `user_id` | string(255) | yes | | อ้างอิง `profiles.user_id` |
| | `book_id` | string(255) | yes | | อ้างอิง `books` |
| | `reserved_at` | datetime | yes | | |
| | `fulfilled_at` | datetime | no | | ว่าง = ยังรออยู่ |
| | `status` | enum | no | `waiting` | `waiting` / `fulfilled` / `cancelled` / `expired` |
| | `queue_position` | integer | no | | ลำดับคิว 0–100000 |
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

### รูปแบบ `queries` ของ Appwrite 1.5

`queries` เป็น **array param** ต้องส่งซ้ำเป็น `queries[0]`, `queries[1]`, ... (ไม่ใช่ JSON array เดียว)

```text
?queries[0]={"method":"equal","attribute":"status","values":["waiting"]}&queries[1]={"method":"limit","values":[25]}
```

ค่า JSON ต้อง URL-encode ถ้าเรียกด้วย client ที่ encode ให้อัตโนมัติ (เช่น Postman/SDK) ไม่ต้องทำเอง

## คำสั่งที่ใช้บ่อย

```powershell
docker compose up -d            # สตาร์ท / อัปเดต
docker compose ps               # ดูสถานะ
docker compose logs -f app      # ดู log
docker compose down             # หยุด (ข้อมูลยังอยู่)
docker compose down -v          # หยุด + ลบข้อมูลทั้งหมด
```

ตรวจสุขภาพระบบตามมาตรฐาน Appwrite:

```powershell
docker exec 69-s1-appwrite php app/cli.php doctor
```

## แก้ปัญหา (Troubleshooting)

| อาการ | สาเหตุ | วิธีแก้ |
| --- | --- | --- |
| `Unknown attribute` ตอน insert | attribute ค้างสถานะ `processing` เพราะไม่มี worker `databases` | `docker compose up -d` ให้ worker ครบ |
| ทุก request ได้ `500` / `getHeader(): Argument #2 must be of type string` | ไม่ได้ตั้ง `_APP_SYSTEM_RESPONSE_FORMAT` | ต้องมีตัวแปรนี้ใน `docker-compose.yaml` (ตั้งเป็นค่าว่างได้) |
| `401 User (role: guests) missing scope (...)` | ยังไม่ได้ Login หรือยังไม่ใส่ `APPWRITE_SESSION_TOKEN` | รัน `1.1 Login` แล้ว copy token จาก `Set-Cookie` ไปใส่ใน `.env` |
| `401` ตอน create/update/delete | collection ใช้ `read("users")` / `create("users")` | guest ทำไม่ได้ ต้อง login (list จะได้ 200 แต่ว่างเปล่า ไม่ใช่ 401) |
| list ได้ 200 แต่ `total = 0` ทั้งที่มีข้อมูล | ยังไม่ Login หรือส่ง token ผิด | ตรวจ `APPWRITE_SESSION_TOKEN` |
| `Cannot set default value for required attribute` | Appwrite 1.5 ห้ามตั้ง default ให้ attribute ที่ `required` | ตั้ง `required = false` คู่กับ `default` |
| `400 Invalid 'orders' param: must be one of (asc, desc)` | ค่า order ต้องพิมพ์เล็ก | ใช้ `asc` / `desc`; unique ให้ใช้ `type: "unique"` แทน |
| `400 Invalid 'queries' param` | ส่ง `queries` เป็น JSON array เดียว | ต้องส่งซ้ำเป็น `queries[0]=...&queries[1]=...` |
| `400 Invalid query: Query value is invalid for attribute "..."` | ค่าใน query มี `+` (เช่น `+00:00`) — `+` ใน URL ถูกอ่านเป็นช่องว่าง | เขียน datetime ในรูปแบบ `Z` เช่น `2026-10-05T07:00:00.000Z` หรือ encode เป็น `%2B` |
| `401 User maximum sessions reached` | login สะสมเกิน 10 session (ค่า default ของโปรเจกต์) | ลบ session เก่าผ่าน Console → Users → Sessions |
| register ได้ `409` | email ที่สมัครมีอยู่แล้ว | เปลี่ยน `USER_REGISTER_EMAIL` |
| update ได้ `400 Unknown attribute: ...` | ชื่อ field ไม่ตรงกับ attribute ของ collection | ตรวจชื่อ attribute ใน Console ให้ตรงกัน |
| Login ไม่ได้หลังแก้ secret | เปลี่ยน `_APP_OPENSSL_KEY_V1` (ใช้ถอดรหัส password) | คืนค่าเดิม ห้ามเปลี่ยนหลังเริ่มใช้งาน |

> หมายเหตุ: ใน Appwrite 1.5.0 การเปลี่ยน collection permission ให้ใช้ `PUT /v1/databases/{db}/collections/{collectionId}`
> พร้อมส่ง `name` ไปด้วยเสมอ และถ้าไม่ส่ง `documentSecurity` ค่าจะถูกรีเซ็ตเป็น `false`

## หมายเหตุด้านความปลอดภัย

- `.env` และ `api.http` ถูก ignore ไม่ commit — แต่ `docker-compose.yaml` **มี secret จริงอยู่** ถ้าจะเผยแพร่ repo ต้องทำให้เป็น private หรือย้าย secret ออก
- `.env` มี `APPWRITE_SESSION_TOKEN` ซึ่งเป็น session ที่ใช้งานได้จริง — อย่า commit หรือแชร์ไฟล์นี้
- สิทธิ์ระดับ collection เป็น `("users")` ทั้งหมด = ทุกคนที่ login แล้วอ่าน/เขียนได้ทุก document (ยังไม่มีการแยกสิทธิ์ตาม `role`)
- ค่า `_APP_OPTIONS_ABUSE=disabled` และ `_APP_OPTIONS_FORCE_HTTPS=disabled` เหมาะกับ dev เท่านั้น
- mariadb / redis ไม่เปิด port ออกภายนอก เข้าถึงได้เฉพาะใน Docker network

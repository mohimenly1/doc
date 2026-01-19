# International Inquiry System - Backend API

> **ملاحظة مهمة:** هذا المشروع هو **Backend API فقط** (ASP.NET Core). يمكن ربط أي Frontend (React, Vue, Angular, Next.js, إلخ) بهذا API. المشروع يحتوي على مثال Frontend في مجلد `frontend/` لكنه اختياري ويمكن استبداله بأي تقنية أخرى.

---

## 📋 المحتويات

- [المتطلبات الأساسية](#المتطلبات-الأساسية)
- [إعداد البيئة التشغيلية](#إعداد-البيئة-التشغيلية)
- [تشغيل المشروع](#تشغيل-المشروع)
- [Swagger UI](#swagger-ui)
- [Authentication & Authorization](#authentication--authorization)
- [API Endpoints](#api-endpoints)
- [أمثلة على استخدام API](#أمثلة-على-استخدام-api)
- [CORS Configuration](#cors-configuration)
- [هيكل المشروع](#هيكل-المشروع)
- [التوثيق الكامل](#التوثيق-الكامل)

---

## 🔧 المتطلبات الأساسية

قبل البدء، تأكد من تثبيت البرامج التالية:

### 1. .NET SDK
- **الإصدار المطلوب:** .NET 10.0 SDK أو أحدث
- **التحميل:** [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)
- **التحقق من التثبيت:**
  ```bash
  dotnet --version
  # يجب أن يظهر: 10.0.x أو أحدث
  ```

### 2. Docker & Docker Compose
- **الغرض:** تشغيل SQL Server محلياً
- **التحميل:** [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
- **التحقق من التثبيت:**
  ```bash
  docker --version
  docker compose version
  ```

---

## إعداد البيئة التشغيلية

### الخطوة 1: تحميل المشروع

```bash
# إذا كان المشروع على Git
git clone <repository-url>
cd iis

# أو قم بتحميل المشروع كـ ZIP واستخرجه
```

### الخطوة 2: تشغيل SQL Server باستخدام Docker

```bash
# الانتقال إلى مجلد Docker
cd docker/

# تشغيل SQL Server
docker compose up -d

# التحقق من أن الحاوية تعمل
docker ps
# يجب أن ترى حاوية sqlserver تعمل

# عرض Logs (اختياري)
docker compose logs -f sqlserver
```

**معلومات الاتصال بقاعدة البيانات:**
- **Server:** `localhost,1433`
- **Database:** `InquirySystemDb` (سيتم إنشاؤها تلقائياً)
- **Username:** `sa`
- **Password:** `StrongPass123_Strong`
- **Connection String:** `Server=localhost,1433;Database=InquirySystemDb;User Id=sa;Password=StrongPass123_Strong;TrustServerCertificate=True;`

> **ملاحظة:** يمكنك تغيير كلمة المرور في `docker/docker-compose.yml` إذا أردت.

### الخطوة 3: تطبيق Migrations (إنشاء قاعدة البيانات)

```bash
# من جذر المشروع
cd src/Infrastructure/InquirySystem.Infrastructure

# تطبيق Migrations
dotnet ef database update --startup-project ../../Api/InquirySystem.Api.csproj

# أو من جذر المشروع مباشرة:
dotnet ef database update --project src/Infrastructure/InquirySystem.Infrastructure.csproj --startup-project src/Api/InquirySystem.Api.csproj
```

**ما الذي يحدث هنا؟**
- يتم إنشاء قاعدة البيانات `InquirySystemDb` تلقائياً
- يتم إنشاء جميع الجداول (Users, Organizations, Queries, إلخ)
- يتم إدراج البيانات الأولية (Seed Data) مثل الأدوار والصلاحيات

### الخطوة 4: تشغيل API

```bash
# من جذر المشروع
cd src/Api

# تشغيل API
dotnet run

# أو من جذر المشروع مباشرة:
dotnet run --project src/Api/InquirySystem.Api.csproj
```

**بعد التشغيل، سترى:**
```
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5234
      Now listening on: https://localhost:7067
```

**URLs المتاحة:**
- **HTTP:** `http://localhost:5234`
- **HTTPS:** `https://localhost:7067`
- **Swagger UI:** `http://localhost:5234/swagger` أو `https://localhost:7067/swagger`

---

## 📖 Swagger UI

### الوصول إلى Swagger

1. **افتح المتصفح وانتقل إلى:**
   ```
   http://localhost:5234/swagger
   ```
   أو
   ```
   https://localhost:7067/swagger
   ```

2. **سترى واجهة Swagger UI** تحتوي على جميع API Endpoints

### استخدام Swagger

#### 1. Authentication في Swagger

**للمسؤولين (System Admins):**
- بعض Endpoints تتطلب **Admin Key**
- اضغط على زر **"Authorize"** في أعلى الصفحة
- أدخل Admin Key: `DEV_ONLY_CHANGE_ME` (في بيئة التطوير)
- اضغط **"Authorize"** ثم **"Close"**

**للمستخدمين العاديين:**
- معظم Endpoints تتطلب **JWT Token**
- قم بتسجيل الدخول أولاً عبر `POST /api/v1/auth/login`
- انسخ `accessToken` من الـ Response
- اضغط على زر **"Authorize"** في Swagger
- أدخل: `Bearer <your-access-token>`
- مثال: `Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`

#### 2. تجربة Endpoints

1. **اختر Endpoint** من القائمة
2. اضغط **"Try it out"**
3. أدخل البيانات المطلوبة
4. اضغط **"Execute"**
5. شاهد الـ Response في الأسفل

#### 3. Schema Definitions

- في أسفل Swagger UI، ستجد **"Schemas"** section
- يحتوي على جميع Models/DTOs المستخدمة في API
- يمكنك رؤية بنية البيانات المطلوبة والمُرجعة

---

## 🔐 Authentication & Authorization

### 1. تسجيل الدخول (Login)

**Endpoint:** `POST /api/v1/auth/login`

**Request Body:**
```json
{
  "username": "admin",
  "password": "Admin123!"
}
```

**Response (Success):**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "abc123...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "refreshTokenExpiresAt": "2026-01-18T12:00:00Z",
  "requires2FA": false,
  "user": {
    "id": 1,
    "username": "admin",
    "fullName": "مدير النظام",
    "organizationId": 1,
    "organizationName": "الجهة الرئيسية"
  }
}
```

**Response (إذا كان 2FA مفعّل):**
```json
{
  "requires2FA": true,
  "message": "2FA code required. Call /api/v1/auth/2fa/login-verify with username and code."
}
```

### 2. استخدام JWT Token

**في جميع الطلبات المطلوبة للمصادقة:**
```
Authorization: Bearer <accessToken>
```

**مثال (cURL):**
```bash
curl -X GET "http://localhost:5234/api/v1/auth/me" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**مثال (JavaScript/Fetch):**
```javascript
const response = await fetch('http://localhost:5234/api/v1/auth/me', {
  headers: {
    'Authorization': `Bearer ${accessToken}`,
    'Content-Type': 'application/json'
  }
});
```

### 3. تحديث Token (Refresh Token)

**Endpoint:** `POST /api/v1/auth/refresh`

**Request Body:**
```json
{
  "refreshToken": "abc123..."
}
```

**Response:**
```json
{
  "accessToken": "new-access-token...",
  "refreshToken": "new-refresh-token...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "refreshTokenExpiresAt": "2026-01-18T12:00:00Z"
}
```

### 4. الحصول على معلومات المستخدم الحالي

**Endpoint:** `GET /api/v1/auth/me`

**Headers:**
```
Authorization: Bearer <accessToken>
```

**Response:**
```json
{
  "id": 1,
  "username": "admin",
  "fullName": "مدير النظام",
  "phoneNumber": "+966501234567",
  "isActive": true,
  "organizationId": 1,
  "organizationName": "الجهة الرئيسية",
  "twoFactorEnabled": false,
  "isSystemAdmin": true,
  "permissions": [
    {
      "id": 1,
      "name": "مسؤول النظام",
      "code": "SYSTEM_ADMIN"
    }
  ],
  "createdAt": "2026-01-01T00:00:00Z",
  "updatedAt": "2026-01-01T00:00:00Z"
}
```

### 5. تسجيل الخروج (Logout)

**Endpoint:** `POST /api/v1/auth/logout`

**Headers:**
```
Authorization: Bearer <accessToken>
```

**Request Body (اختياري):**
```json
{
  "refreshToken": "abc123..."
}
```

---

## 📡 API Endpoints

### Authentication
- `POST /api/v1/auth/login` - تسجيل الدخول
- `POST /api/v1/auth/refresh` - تحديث Token
- `GET /api/v1/auth/me` - معلومات المستخدم الحالي
- `POST /api/v1/auth/logout` - تسجيل الخروج
- `POST /api/v1/auth/2fa/enable` - تفعيل 2FA
- `POST /api/v1/auth/2fa/verify` - التحقق من 2FA
- `POST /api/v1/auth/2fa/disable` - تعطيل 2FA
- `POST /api/v1/auth/2fa/login-verify` - التحقق من 2FA أثناء تسجيل الدخول

### Dashboard
- `GET /api/v1/dashboard/stats` - إحصائيات Dashboard

### Queries
- `POST /api/v1/queries/person` - إنشاء استعلام عن شخص
- `POST /api/v1/queries/vehicle` - إنشاء استعلام عن مركبة
- `POST /api/v1/queries/document` - إنشاء استعلام عن وثيقة
- `GET /api/v1/queries` - قائمة الاستعلامات (مع Pagination)
- `GET /api/v1/queries/{id}` - تفاصيل استعلام

### Users
- `GET /api/v1/users` - قائمة المستخدمين (مع Pagination)
- `GET /api/v1/users/{id}` - تفاصيل مستخدم
- `POST /api/v1/users` - إنشاء مستخدم
- `PUT /api/v1/users/{id}` - تحديث مستخدم
- `DELETE /api/v1/users/{id}` - حذف مستخدم

### Organizations
- `GET /api/v1/organizations` - قائمة الجهات (مع Pagination)
- `GET /api/v1/organizations/{id}` - تفاصيل جهة
- `POST /api/v1/organizations` - إنشاء جهة
- `PUT /api/v1/organizations/{id}` - تحديث جهة
- `DELETE /api/v1/organizations/{id}` - حذف جهة

### Permissions & Roles
- `GET /api/v1/permissions` - قائمة الصلاحيات
- `POST /api/v1/permissions` - إنشاء صلاحية
- `GET /api/v1/roles` - قائمة الأدوار
- `POST /api/v1/roles` - إنشاء دور

### Follow-up Actions
- `GET /api/v1/actions` - قائمة جميع الإجراءات
- `GET /api/v1/actions/{id}` - تفاصيل إجراء
- `POST /api/v1/queries/{queryId}/actions` - إنشاء إجراء لاستعلام

### Transfers (Referrals)
- `GET /api/v1/transfers` - قائمة الإحالات (مع Pagination)
- `POST /api/v1/transfers` - إنشاء إحالة

### Reports
- `POST /api/v1/reports` - إنشاء تقرير
- `GET /api/v1/reports` - قائمة التقارير (مع Pagination)
- `GET /api/v1/reports/{id}/download` - تحميل تقرير (PDF)

### Activity Log
- `GET /api/v1/activity-logs` - سجل الأنشطة (مع Pagination)

---

## 💡 أمثلة على استخدام API

### مثال 1: تسجيل الدخول والحصول على Token

```javascript
// JavaScript/TypeScript
const loginResponse = await fetch('http://localhost:5234/api/v1/auth/login', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
      username: 'admin',
      password: 'Admin123!'
    })
});

const data = await loginResponse.json();
const accessToken = data.accessToken;

// حفظ Token في localStorage أو state management
localStorage.setItem('accessToken', accessToken);
localStorage.setItem('refreshToken', data.refreshToken);
```

### مثال 2: جلب قائمة الاستعلامات

```javascript
const accessToken = localStorage.getItem('accessToken');

const response = await fetch('http://localhost:5234/api/v1/queries?page=1&pageSize=20', {
  headers: {
    'Authorization': `Bearer ${accessToken}`,
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
// data.items: قائمة الاستعلامات
// data.total: العدد الإجمالي
// data.page: الصفحة الحالية
// data.pageSize: عدد العناصر في الصفحة
```

### مثال 3: إنشاء استعلام عن شخص

```javascript
const accessToken = localStorage.getItem('accessToken');

const response = await fetch('http://localhost:5234/api/v1/queries/person', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${accessToken}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    nationalId: '1234567890',
    organizationId: 1,
    notes: 'ملاحظات إضافية'
  })
});

const data = await response.json();
// data.id: معرف الاستعلام
// data.status: حالة الاستعلام
```

### مثال 4: Pagination

جميع Endpoints التي تعيد قوائم تدعم Pagination:

```
GET /api/v1/queries?page=1&pageSize=20&search=test
GET /api/v1/users?page=2&pageSize=10&status=active
GET /api/v1/organizations?page=1&pageSize=50
```

**Response Format:**
```json
{
  "items": [...],
  "total": 100,
  "page": 1,
  "pageSize": 20,
  "totalPages": 5
}
```

### مثال 5: Error Handling

```javascript
try {
  const response = await fetch('http://localhost:5234/api/v1/queries', {
    headers: {
      'Authorization': `Bearer ${accessToken}`
    }
  });

  if (!response.ok) {
    const errorData = await response.json();
    
    if (response.status === 401) {
      // Token منتهي أو غير صحيح
      // قم بإعادة تسجيل الدخول
    } else if (response.status === 403) {
      // ليس لديك صلاحية
      console.error('Forbidden:', errorData.message);
    } else if (response.status === 400) {
      // خطأ في البيانات المرسلة
      console.error('Bad Request:', errorData.message);
    }
    
    throw new Error(errorData.message || 'حدث خطأ');
  }

  const data = await response.json();
  // معالجة البيانات
} catch (error) {
  console.error('Error:', error);
}
```

---

## 🌐 CORS Configuration

### في بيئة التطوير (Development)

API يدعم **CORS** بشكل كامل في بيئة التطوير:
- **Allowed Origins:** جميع المصادر (`*`)
- **Allowed Methods:** جميع الطرق (GET, POST, PUT, DELETE, إلخ)
- **Allowed Headers:** جميع Headers

**لا حاجة لإعداد CORS إضافي في Frontend في بيئة التطوير.**

### في بيئة الإنتاج (Production)

يجب تكوين CORS في `appsettings.json`:

```json
{
  "Cors": {
    "AllowedOrigins": [
      "https://your-frontend-domain.com",
      "https://www.your-frontend-domain.com"
    ]
  }
}
```

---

## 🏗️ هيكل المشروع

```
iis/
├── src/
│   ├── Api/                          # API Layer
│   │   ├── Controllers/              # API Controllers
│   │   ├── Security/                 # JWT, AdminKey, PasswordHasher
│   │   ├── Services/                 # Business Logic Services
│   │   ├── Middleware/               # Custom Middleware
│   │   ├── Swagger/                  # Swagger Configuration
│   │   ├── Program.cs                # Application Entry Point
│   │   └── appsettings.json          # Configuration
│   │
│   ├── Domain/                       # Domain Layer
│   │   └── InquirySystem.Domain/
│   │       └── Entities/           # Domain Entities
│   │
│   ├── Infrastructure/               # Infrastructure Layer
│   │   └── InquirySystem.Infrastructure/
│   │       └── Data/
│   │           ├── AppDbContext.cs   # EF Core DbContext
│   │           ├── DbSeeder.cs       # Seed Data
│   │           └── Migrations/       # Database Migrations
│   │
│   └── Application/                  # Application Layer (Optional)
│
├── docker/
│   └── docker-compose.yml            # SQL Server Docker Configuration
│
├── docs/                             # Documentation
│   ├── api.md                        # Complete API Documentation
│   ├── architecture.md               # Architecture Documentation
│   ├── deployment.md                 # Deployment Guide
│   └── user-guide.md                 # User Guide
│
└── frontend/                         # Example Frontend (Optional)
    └── ...                           # Next.js Frontend Example
```

---

## 📚 التوثيق الكامل

### 1. API Documentation
- **الملف:** `docs/api.md`
- **المحتوى:** توثيق شامل لجميع Endpoints مع أمثلة Request/Response

### 2. Architecture Documentation
- **الملف:** `docs/architecture.md`
- **المحتوى:** شرح بنية المشروع، Clean Architecture، Database Schema

### 3. Deployment Guide
- **الملف:** `docs/deployment.md`
- **المحتوى:** دليل نشر المشروع في بيئة الإنتاج

### 4. User Guide
- **الملف:** `docs/user-guide.md`
- **المحتوى:** دليل المستخدم للواجهة الأمامية

### 5. Swagger UI
- **الوصول:** `http://localhost:5234/swagger`
- **المحتوى:** واجهة تفاعلية لجميع API Endpoints

---

## ⚙️ Configuration Files

### 1. appsettings.json
```json
{
  "Security": {
    "AdminKey": "",
    "Jwt": {
      "SecretKey": "CHANGE_THIS_IN_PRODUCTION...",
      "Issuer": "InquirySystem",
      "Audience": "InquirySystem",
      "ExpirationMinutes": 60,
      "RefreshExpirationDays": 7
    }
  },
  "ConnectionStrings": {
    "Default": "Server=localhost,1433;Database=InquirySystemDb;..."
  }
}
```

### 2. appsettings.Development.json
```json
{
  "Security": {
    "AdminKey": "DEV_ONLY_CHANGE_ME"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

---

## 🔧 أوامر مفيدة

### تشغيل API
```bash
dotnet run --project src/Api/InquirySystem.Api.csproj
```

### تطبيق Migrations
```bash
dotnet ef database update --project src/Infrastructure/InquirySystem.Infrastructure.csproj --startup-project src/Api/InquirySystem.Api.csproj
```

### إضافة Migration جديد
```bash
dotnet ef migrations add MigrationName --project src/Infrastructure/InquirySystem.Infrastructure.csproj --startup-project src/Api/InquirySystem.Api.csproj
```

### Build المشروع
```bash
dotnet build
```

### Clean Build Artifacts
```bash
dotnet clean
```

---

## 🐛 Troubleshooting

### المشكلة: SQL Server لا يعمل
```bash
# التحقق من حالة Docker
docker ps

# إعادة تشغيل SQL Server
cd docker/
docker compose restart

# عرض Logs
docker compose logs -f sqlserver
```

### المشكلة: Port 5234 أو 7067 مستخدم
```bash
# تغيير Port في launchSettings.json
# أو إيقاف التطبيق الذي يستخدم نفس Port
```

### المشكلة: Migration فشل
```bash
# حذف قاعدة البيانات وإعادة إنشائها
# (تحذير: سيتم حذف جميع البيانات)
dotnet ef database drop --project src/Infrastructure/InquirySystem.Infrastructure.csproj --startup-project src/Api/InquirySystem.Api.csproj
dotnet ef database update --project src/Infrastructure/InquirySystem.Infrastructure.csproj --startup-project src/Api/InquirySystem.Api.csproj
```

### المشكلة: CORS Error في Frontend
- تأكد من أن API يعمل على `http://localhost:5234`
- تأكد من إرسال `Authorization` header بشكل صحيح
- في بيئة التطوير، CORS مفتوح لجميع المصادر

---

## 📞 الدعم والمساعدة

- **Swagger UI:** `http://localhost:5234/swagger` - لرؤية جميع Endpoints
- **API Documentation:** `docs/api.md` - توثيق شامل
- **Architecture Docs:** `docs/architecture.md` - فهم بنية المشروع

---

## ✅ Checklist For Frontend

- [ ] تثبيت .NET 10 SDK
- [ ] تثبيت Docker & Docker Compose
- [ ] تشغيل SQL Server (`docker compose up -d`)
- [ ] تطبيق Migrations (`dotnet ef database update`)
- [ ] تشغيل API (`dotnet run`)
- [ ] فتح Swagger UI (`http://localhost:5234/swagger`)
- [ ] تجربة تسجيل الدخول في Swagger
- [ ] قراءة `docs/api.md` لفهم جميع Endpoints
- [ ] البدء في ربط Frontend بالـ API

---

**آخر تحديث:** 2026-01-11

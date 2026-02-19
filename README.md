<div align="center">

# 💼 Enterprise Expense Tracker

### A production-grade expense management platform with multi-level approvals, audit trails, and real-time analytics.

[![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://openjdk.org/projects/jdk/17/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.3.1-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)](https://spring.io/projects/spring-boot)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/JWT-Auth-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)](https://jwt.io/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Maven](https://img.shields.io/badge/Maven-Build-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white)](https://maven.apache.org/)

</div>

---

## 📖 Overview

The **Enterprise Expense Tracker** is a robust Spring Boot REST API built for organizations that need structured, transparent expense lifecycle management. From the moment an employee submits an expense to the final admin approval and invoice generation, every action is tracked, audited, and reportable.

> Designed following a layered architecture (Controller → Service → Repository), secured with stateless JWT authentication, and optimized for real-world enterprise workloads.

---

## ✨ Features

| Category | Capability |
|---|---|
| 🔐 **Security** | JWT-based stateless authentication, role-based access control (RBAC) |
| 👤 **User Management** | Registration, profile updates, role promotion/demotion, password reset via email |
| 💸 **Expense Lifecycle** | Submit, update, delete expenses with receipt attachments (Cloudinary) |
| ✅ **Approval Workflow** | Two-level approval chain — Manager review → Admin final sign-off |
| 📊 **Analytics** | Monthly, weekly, category, and status-based expense analytics per user |
| 📄 **Reporting** | On-demand PDF export of filtered expense lists |
| 📧 **Email Notifications** | Password reset links and invoice delivery via email |
| 🧾 **Invoice Generation** | Auto-generated invoices upon full approval, stored on Cloudinary |
| 🕵️ **Audit Logging** | Immutable audit trail for every create, update, delete, approve/reject action |
| 🔔 **Notifications** | In-app notifications for expense status changes and account events |
| 📦 **Pagination & Filtering** | All list endpoints support dynamic filtering, sorting, and pagination |

---

## 🏗️ Architecture

```
┌────────────────────────────────────────────────────────────────┐
│                        REST API Layer                          │
│  AuthController · ExpenseController · AdminController · ...    │
└──────────────────────────┬─────────────────────────────────────┘
                           │
┌──────────────────────────▼─────────────────────────────────────┐
│                      Service Layer                             │
│  UserService · ExpenseService · InvoiceService · ...           │
└──────────────────────────┬─────────────────────────────────────┘
                           │
┌──────────────────────────▼─────────────────────────────────────┐
│               Repository / Data Access Layer                    │
│  JPA Repositories + JPA Specifications (dynamic queries)       │
└──────────────────────────┬─────────────────────────────────────┘
                           │
            ┌──────────────▼──────────────┐
            │        PostgreSQL DB         │
            └─────────────────────────────┘

External Services:  Cloudinary (file storage) · Gmail SMTP (email)
Security:           JWT Filter · BCrypt · Spring Security
```

---

## 🗂️ Role Hierarchy

```
ADMIN
  └── Inherits MANAGER + EMPLOYEE permissions
      ├── Final expense approval / rejection
      ├── Invoice generation & delivery
      └── Full audit log access

MANAGER
  └── Inherits EMPLOYEE permissions
      └── First-level expense approval / rejection

EMPLOYEE
      ├── Submit & manage own expenses
      └── View own analytics & notifications
```

---

## 🚀 Quick Start

### Prerequisites

- **Java 17+**
- **Maven 3.8+** *(or use the included `./mvnw` wrapper)*
- **PostgreSQL** running locally or on a cloud instance
- **Docker** *(optional — for containerized setup)*

---

### 1 · Clone the repository

```bash
git clone https://github.com/sagarboyal/enterprise-expense-tracker-backend.git
cd enterprise-expense-tracker-backend
```

### 2 · Create your `.env` file

Create a `.env` file in the **project root** and fill in your values:

```dotenv
# ── Database ──────────────────────────────────────────────
DB_URL=jdbc:postgresql://localhost:5432/expense_tracker
DB_USERNAME=postgres
DB_PASSWORD=your_db_password
DRIVER_CLASS=org.postgresql.Driver
DB_DIALECT=org.hibernate.dialect.PostgreSQLDialect

# ── JWT ───────────────────────────────────────────────────
JWT_SECRET=your_strong_base64_encoded_secret_here
JWT_EXP_TIME=3600000

# ── Mail (Gmail SMTP) ─────────────────────────────────────
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_app_password

# ── Frontend ──────────────────────────────────────────────
FRONT_END_URL=http://localhost:5173

# ── Cloudinary ────────────────────────────────────────────
CLOUD_NAME=your_cloudinary_cloud_name
API_KEY=your_cloudinary_api_key
API_SECRET=your_cloudinary_api_secret
MAX_FILE_SIZE=2MB
```

> **Tip for Gmail:** Enable 2FA on your Google account and generate an [App Password](https://myaccount.google.com/apppasswords) to use as `MAIL_PASSWORD`.

---

### 3 · Load the `.env` into your run configuration

**IntelliJ IDEA**
1. Open **Run / Debug Configurations**
2. Select your Spring Boot run config → click **Modify options**
3. Enable **Environment variables** → point it to your `.env` file

**VS Code / Terminal**
```bash
export $(cat .env | xargs)
./mvnw spring-boot:run
```

**Docker**
```bash
docker build -t expense-tracker .
docker run --env-file .env -p 8080:8080 expense-tracker
```

---

### 4 · Run the application

```bash
./mvnw spring-boot:run
```

The API will be available at `http://localhost:8080`.

On first startup, three seed users are created automatically:

| Email | Password | Role |
|-------|----------|------|
| `admin@gmail.com` | `admin` | Admin + Manager + Employee |
| `manager@gmail.com` | `manager` | Manager + Employee |
| `user@gmail.com` | `user` | Employee |

> ⚠️ **Change these credentials immediately** before deploying to any shared or production environment.

---

## 📡 API Reference

### Authentication — `/api/auth`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/public/sign-in` | ❌ | Login — returns JWT token |
| `POST` | `/public/sign-up` | ❌ | Register a new employee account |
| `GET` | `/user` | ✅ | Get currently authenticated user info |
| `POST` | `/public/forgot-password` | ❌ | Request a password reset email |
| `POST` | `/public/reset-password` | ❌ | Reset password using token from email |

### Expenses — `/api/expenses`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/` | ✅ | List own expenses (filterable, paginated) |
| `GET` | `/{id}` | ✅ | Get a specific expense by ID |
| `GET` | `/request-list` | 🛡️ Manager/Admin | All users' expense requests |
| `POST` | `/` | ✅ | Submit a new expense |
| `PUT` | `/` | ✅ | Update an existing expense |
| `PUT` | `/approve/{id}` | 🛡️ Manager/Admin | Approve or reject an expense |
| `DELETE` | `/{id}` | ✅ | Delete an expense |
| `GET` | `/approval-stack/{id}` | ✅ | View full approval history for an expense |

### Documents — `/api/document`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/{expenseId}` | ✅ | Get attached document for an expense |
| `POST` | `/cloudinary/upload/{expenseId}` | ✅ | Upload a receipt/document (`multipart/form-data`) |
| `POST` | `/cloudinary/delete/{expenseId}` | ✅ | Remove the attached document |

### Analytics — `/api/analytics`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/monthly` | ✅ | Monthly expense totals |
| `GET` | `/weekly` | ✅ | Current-week daily breakdown |
| `GET` | `/category` | ✅ | Totals grouped by category |
| `GET` | `/status` | ✅ | Totals grouped by approval status |
| `GET` | `/summary` | ✅ | Approved/pending/rejected counts + totals |

### Admin — `/api/admin`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/audit-log` | 🔒 Admin | Filterable audit log |
| `GET` | `/users/invoice` | 🔒 Admin | All invoices |
| `GET` | `/invoice/mail` | 🔒 Admin | Email an invoice to the user |
| `GET` | `/invoice/generate/{userId}` | 🔒 Admin | Manually generate an invoice |
| `DELETE` | `/invoice/delete/{invoiceId}` | 🔒 Admin | Delete an invoice |

---

## ⚙️ Environment Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `DB_URL` | JDBC connection string | `jdbc:postgresql://localhost:5432/db` |
| `DB_USERNAME` | Database username | `postgres` |
| `DB_PASSWORD` | Database password | `secret` |
| `DRIVER_CLASS` | JDBC driver class | `org.postgresql.Driver` |
| `DB_DIALECT` | Hibernate dialect | `org.hibernate.dialect.PostgreSQLDialect` |
| `JWT_SECRET` | Base64-encoded HMAC secret (≥ 256 bit) | `base64encodedstring...` |
| `JWT_EXP_TIME` | Token expiry in milliseconds | `3600000` (1 hour) |
| `MAIL_USERNAME` | SMTP sender address | `noreply@example.com` |
| `MAIL_PASSWORD` | SMTP app password | `xxxx xxxx xxxx xxxx` |
| `FRONT_END_URL` | Allowed CORS origin | `http://localhost:5173` |
| `CLOUD_NAME` | Cloudinary cloud name | `myapp` |
| `API_KEY` | Cloudinary API key | `123456789012345` |
| `API_SECRET` | Cloudinary API secret | `abcdefghijklmnop` |
| `MAX_FILE_SIZE` | Max upload size | `2MB` |

---

## 🤝 Contributing

Contributions, bug reports, and feature suggestions are welcome!

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'feat: add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📜 License

This project is open-source. See the [LICENSE](LICENSE) file for details.

---

<div align="center">

Built with ❤️ by **Team 7**

</div>
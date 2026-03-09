<div align="center">

# 💰 Wallet API

**Concurrent-safe REST API for wallet management under high load**

[![Go](https://img.shields.io/badge/Go-1.24+-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://golang.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgresql.org)
[![Swagger](https://img.shields.io/badge/Swagger-85EA2D?style=for-the-badge&logo=swagger&logoColor=black)](https://swagger.io)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docker.com)

<br>

<table>
<tr>
<td align="center"><h3>⚡ 1000 RPS</h3><sub>sustained throughput</sub></td>
<td align="center"><h3>🔒 FOR UPDATE</h3><sub>row-level locking</sub></td>
<td align="center"><h3>📖 Swagger</h3><sub>interactive docs</sub></td>
<td align="center"><h3>0</h3><sub>race conditions</sub></td>
</tr>
</table>

</div>

---

## 💡 What It Does

A wallet service that handles **concurrent financial operations** safely:

- 💳 **Deposit** — add funds to any wallet
- 💸 **Withdraw** — deduct funds with balance validation
- 📊 **Balance** — instant balance lookup by UUID

The core challenge: **multiple simultaneous requests to the same wallet** must never corrupt the balance. Solved with PostgreSQL `SELECT ... FOR UPDATE` row-level locking inside transactions.

---

## 🏗️ Architecture

```
cmd/                → Entry point
config/             → Environment configuration
internal/
├── controllers/    → HTTP handlers (Gin)
├── service/        → Business logic + locking
├── repositories/   → PostgreSQL (FOR UPDATE)
├── models/         → Domain entities
└── middleware/      → Request logging
migrations/         → Goose SQL migrations
load_tests/         → wrk scripts + benchmark results
docs/               → Swagger API specs
```

---

## 🔌 API

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/wallet` | Deposit or withdraw funds |
| `GET` | `/api/v1/wallets/{uuid}` | Get wallet balance |

**Example:**

```json
POST /api/v1/wallet
{
  "walletId": "c3a8cb84-03f2-4fb9-982a-9ee2cfb50b9f",
  "operationType": "DEPOSIT",
  "amount": 1000
}

→ { "uuid": "c3a8cb84-...", "balance": 2000 }
```

---

## 🛠 Tech Stack

| | Technology | Purpose |
|-|------------|---------|
| 🔧 | **Go + Gin** | HTTP framework |
| 🗄 | **PostgreSQL** | Storage with row-level locking |
| 📖 | **Swagger** | Auto-generated API docs |
| 📦 | **Docker Compose** | One-command deployment |
| 🔄 | **Goose** | Database migrations |
| 📝 | **Logrus** | Structured logging |
| 🧪 | **SQLMock + Testify** | Unit testing |
| 🏋️ | **wrk** | Load testing (Lua scripts) |

---

## 🚀 Quick Start

```bash
git clone https://github.com/AlexMayka/wallet-api.git
cd wallet-api
docker compose --env-file config.env up -d
```

Swagger UI: `http://localhost:8080/swagger/index.html`

---

## 📈 Load Testing

Benchmarked with **wrk** using custom Lua scripts at 1000 RPS:

| Scenario | Result |
|----------|--------|
| GET balance | ✅ Stable at 1000 RPS |
| POST deposit/withdraw | ✅ No race conditions |
| Mixed workload | ✅ Consistent balances |

Results in `load_tests/`: `get_results.txt`, `post_results.txt`, `mixed_results.txt`

```bash
wrk -t4 -c10 -d30s -s ./load_tests/post.lua http://localhost:8080
```

---

## 🧪 Tests

```bash
go test ./...
```

## License

MIT

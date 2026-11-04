# Digital Inventory & Sales Management System

A production-ready, full-stack digital inventory, sales, billing, and CRM system designed for Karnataka Kirana & General Provision stores. Built with **React 19, TypeScript, Vite, Tailwind CSS v4, Express, and Prisma ORM**.

---

## 🚀 Features

- **Store Profile & Multi-Tenant Data Isolation**: Every transaction and inventory item strictly scoped to the store's business UUID.
- **Enterprise Security**:
  - Bcrypt password hashing (Salt rounds 10)
  - HTTP-Only SameSite JWT cookies + Bearer token support
  - Helmet security headers & granular CORS origin verification with credentials
  - Express Rate Limiting on authentication & API routes
  - Role-Based Access Control (`OWNER`, `STAFF`, `CUSTOMER`)
  - Zod validation and safe parameterized queries preventing SQL injection
- **Point of Sale (POS) & Order Management**:
  - Multi-item checkout with automatic stock deduction in Prisma ACID transactions
  - Instant barcode scan lookup (`/api/products/barcode/:barcode`)
  - Sequential invoice generation (`ORD-YYYYMMDD-XXXX`)
  - Order status lifecycle (`CONFIRMED`, `PROCESSING`, `SHIPPED`, `DELIVERED`, `CANCELLED`, `RETURNED`) with automatic inventory stock restoration on cancellation
  - Delivery deadline countdowns & overdue alerts
- **Catalog & Inventory Operations**:
  - Bilingual product catalog (English + Kannada ಕನ್ನಡ ಹೆಸರು)
  - Atomic stock-in replenishment & reason-tracked manual adjustments
  - Immutable inventory audit log
  - Automated stock status (`AVAILABLE`, `LOW_STOCK`, `OUT_OF_STOCK`)
  - Inventory Bin quarantine with **Accounting Protection Rule** (protects past sales records from deletion)
- **Financial Analytics & Reporting**:
  - Interactive revenue and order trends
  - Best-selling SKUs, category distribution, top spending customers
  - Exportable CSV financial reports (`/api/reports/export-csv`)
- **Promotions & Rule-Based Recommendation Engine**:
  - Store discount campaigns (Percentage / Flat discount)
  - Deterministic recommendation engine based on customer purchasing history and active festival deals

---

## 🛠️ Tech Stack

- **Frontend**: React 19, Vite, TypeScript, Tailwind CSS v4, Lucide Icons, Recharts, Canvas Confetti
- **Backend**: Node.js, Express, TypeScript, Prisma ORM, Bcryptjs, JWT, Helmet, Multer, Nodemailer
- **Database**:
  - **Local Development**: SQLite (instant zero-dependency setup via `dev.db`)
  - **Vercel Production**: PostgreSQL (Neon, Supabase, Vercel Postgres, AWS RDS)
- **Deployment**: Vercel Serverless Functions (`api/index.ts` + `vercel.json`)

---

## 🔑 Pre-Seeded Test Credentials

| Role | Email | Password | Access Level |
| :--- | :--- | :--- | :--- |
| **Store Owner** | `owner@kirana.com` | `Password@123` | Full administrative control, store settings, staff invitations, financial reports |
| **Store Staff** | `staff@kirana.com` | `Password@123` | POS terminal, barcode checkout, stock-in, order fulfillment |
| **Customer** | `customer@kirana.com` | `Password@123` | Order history, catalog browsing, active promotions |

---

## 💻 Running Locally in VS Code

### 1. Install Dependencies
```bash
npm install
npm --prefix server install
npm --prefix client install
```

### 2. Initialize Database & Seed
```bash
npm run seed
```

### 3. Start Development Server
```bash
npm run dev
```
- **Frontend**: [http://localhost:3000](http://localhost:3000)
- **Backend API**: [http://localhost:5000/api](http://localhost:5000/api)
- **Health Check**: [http://localhost:5000/api/health](http://localhost:5000/api/health)

### 4. Run Integration Tests
```bash
npm test
```
Runs the 31-point comprehensive integration test suite covering authentication, multi-tenancy, POS transactions, inventory audit, and security checks.

---

## ☁️ Deploying to Vercel

1. **Push your code to GitHub**:
   ```bash
   git add .
   git commit -m "feat: complete digital inventory application"
   git remote add origin https://github.com/<your-username>/<your-repo-name>.git
   git push -u origin main
   ```

2. **Import into Vercel**:
   - Go to [Vercel Dashboard](https://vercel.com/) and click **Add New > Project**.
   - Select your GitHub repository.
   - Set the root directory to `./`.
   - Vercel automatically detects `vercel.json`, building the client into `client/dist` and routing `/api/*` to `api/index.ts`.

3. **Set Environment Variables on Vercel**:
   - `DATABASE_URL`: Your PostgreSQL connection string (from [Neon](https://neon.tech/) or [Supabase](https://supabase.com/)):
     ```env
     DATABASE_URL="postgresql://postgres:password@ep-sample-12345.us-east-1.aws.neon.tech/digital_inventory?sslmode=require"
     ```
   - `JWT_SECRET`: A strong random string of at least 32 characters.
   - `JWT_EXPIRES_IN`: `7d`
   - `NODE_ENV`: `production`
   - `FRONTEND_URL`: Your Vercel production URL (e.g. `https://your-app.vercel.app`)

4. **Sync PostgreSQL Schema on Vercel**:
   Run database migration against your cloud Postgres:
   ```bash
   npx prisma db push --schema=server/prisma/schema.prisma
   npx tsx server/prisma/seed.ts
   ```

---

## 📁 Repository Architecture

```
├── api/
│   └── index.ts                 # Vercel Serverless Function entry point
├── client/                      # React 19 + Vite frontend application
│   ├── src/
│   │   ├── pages/               # POS, Products, Inventory, Reports, Settings, Auth
│   │   ├── layouts/             # Dashboard responsive layout with barcode lookup
│   │   ├── contexts/            # AuthContext & Theme provider
│   │   └── services/            # Axios API client (/api)
├── server/                      # Express + TypeScript enterprise backend
│   ├── prisma/
│   │   ├── schema.prisma        # PostgreSQL master schema (for Vercel & cloud DB)
│   │   ├── schema.sqlite.prisma # SQLite schema (for zero-dependency local dev)
│   │   └── seed.ts              # Kirana catalog & user seeder
│   ├── src/
│   │   ├── config/              # Environment parser and security settings
│   │   ├── controllers/         # Auth, Product, Inventory, Order, Report, Bin controllers
│   │   ├── middlewares/         # JWT Auth, RBAC, Rate-Limiting, Zod validation, Error handler
│   │   ├── routes/              # Express API route modules
│   │   ├── services/            # Email notifications & Recommendation engine
│   │   ├── app.ts               # Express configuration, Helmet & CORS
│   │   └── server.ts            # Local HTTP development server
│   └── scripts/
│       ├── db-sync.js           # Multi-database provider switcher
│       └── test-api.ts          # 31-point integration test runner
├── vercel.json                  # Vercel deployment rewrites and build config
├── package.json                 # Monorepo root scripts (dev, build, start, seed, test)
└── .env.example                 # Environment configuration template
```

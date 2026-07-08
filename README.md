# 🃏 CardVerse

Full-stack collectible-card e-commerce marketplace with Monorepo architecture.

---

## 📋 1. Analysis & Design (หัวข้อประเมินข้อ 4)

### 👥 Functional Requirements
- **Shop**: first-party card boxes/singles across 20 collection categories.
- **Marketplace**: user-to-user listings with escrow (Stripe Connect), a market-price chart driven by actual completed sales, and a live recent sales feed.
- **Shipping**: sellers update carrier + tracking number; escrow auto-releases after delivery.
- **Roles & Permissions**: Supports customer, manager, and admin roles with distinct access levels.

### 🛠️ Tech Stack & System Layers

| Layer | Technology | Description |
| --- | --- | --- |
| **Frontend** | Next.js 15 (App Router), TypeScript, Tailwind CSS, TanStack Query | Storefront, marketplace, dashboards, and next-intl (TH/EN) |
| **Backend** | NestJS (Node.js), REST API, Socket.IO | Core business logic and real-time communication gateway |
| **Database** | PostgreSQL + Prisma ORM | Relational database management and schema migrations |
| **Cache / Queue** | Redis + BullMQ | Background workers for price aggregation and escrow automation |
| **Auth** | Clerk | Secure user authentication and session management |
| **Payments** | Stripe + Stripe Connect | Escrow-based split payments and automated seller onboarding |
| **Storage** | Cloudflare R2 | S3-compatible object storage for high-performance image uploads |

---

## 🏗️ 2. System Architecture Diagram (หัวข้อประเมินข้อ 5)

โครงสร้างสถาปัตยกรรมระบบและการไหลเวียนของข้อมูล (Data Flow) ภายใน CardVerse:

```mermaid
graph TD
    User -->|Proxies /backend| WebServer[Next.js Apps/Web]
    WebServer -->|REST & Socket.IO| API[NestJS API Server]
    
    subgraph Local Infrastructure
        API -->|Prisma ORM| DB[(PostgreSQL)]
        API -->|Message Queue| Redis[(Redis + BullMQ)]
        Worker[BullMQ Worker] -->|Process Jobs| Redis
        Worker -->|Update Status| API
    end
    
    subgraph External Services
        API -->|Authentication| Clerk[Clerk Auth]
        API -->|Escrow & Transfers| Stripe[Stripe Connect]
        API -->|Presigned URLs| R2[Cloudflare R2]
    end

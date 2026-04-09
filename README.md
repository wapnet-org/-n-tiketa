<div align="center">

# Tiketa — Event Ticketing Platform

**Open public ticketing platform — microservices architecture**

[![Website](https://img.shields.io/badge/tiketa.app-Visit%20Website-blue?style=for-the-badge&logo=google-chrome&logoColor=white)](https://www.tiketa.app)
[![Facebook](https://img.shields.io/badge/Facebook-Follow%20Us-1877F2?style=for-the-badge&logo=facebook&logoColor=white)](https://www.facebook.com/Tiketa)
[![Email](https://img.shields.io/badge/Contact-info@tiketa.eu-red?style=for-the-badge&logo=gmail&logoColor=white)](mailto:info@tiketa.eu)

![Status](https://img.shields.io/badge/status-active-brightgreen?style=flat-square)
![Architecture](https://img.shields.io/badge/architecture-microservices-blueviolet?style=flat-square)
![Stack](https://img.shields.io/badge/stack-Node.js%20%2B%20React-blue?style=flat-square)

</div>

---

## About

Tiketa is an open public event ticketing platform — anyone can create an event (free, paid, or private) and sell tickets. Participants can discover events, purchase tickets, vote, and leave reviews.

The platform is organized as a **multi-repo** project: each service lives in its own GitHub repository and is referenced here as a Git submodule. The main repo (`-n`) acts as the umbrella — it holds the root configuration files (`docker-compose.yml`, `.env.example`, `docs/`) and references all service repos.

---

## Repositories

| Service | Repository | Description |
|---|---|---|
| **auth-service** | [wapnet-org/-n-auth-service](https://github.com/wapnet-org/-n-auth-service) | Authentication, sessions, user accounts, KYC |
| **bff-service** | [wapnet-org/-n-bff](https://github.com/wapnet-org/-n-bff) | API gateway — session management, request routing |
| **booking-service** | [wapnet-org/-n-booking](https://github.com/wapnet-org/-n-booking) | Ticket reservations, QR codes, order management |
| **engagement-service** | [wapnet-org/-n-engagement](https://github.com/wapnet-org/-n-engagement) | Likes, comments, saves, follows, votes, reviews, ads |
| **event-service** | [wapnet-org/-n-event-service](https://github.com/wapnet-org/-n-event-service) | Events, tickets, promo codes, affiliate links |
| **frontend** | [wapnet-org/-n-frontend](https://github.com/wapnet-org/-n-frontend) | React PWA (Vite + TanStack Query + Lucide React) |
| **notification-service** | [wapnet-org/-n-notification-service](https://github.com/wapnet-org/-n-notification-service) | Push & email notifications (RabbitMQ consumer) |
| **payment-service** | [wapnet-org/-n-payment](https://github.com/wapnet-org/-n-payment) | Stripe + Mobile Money, commissions, refunds |
| **reel-service** | [wapnet-org/-n-reels](https://github.com/wapnet-org/-n-reels) | Short video content (reels & stories) |
| **transcode-service** | [wapnet-org/-n-transcode](https://github.com/wapnet-org/-n-transcode) | Video transcoding pipeline |
| **main repo** | [wapnet-org/-n](https://github.com/wapnet-org/-n) | Root config, docker-compose, docs, submodules |

---

## Getting Started

### Clone everything

```bash
git clone --recurse-submodules https://github.com/wapnet-org/-n.git
```

### Update all submodules to latest

```bash
git submodule update --remote --merge
```

### Start all services

```bash
cp .env.example .env
# Fill in the required values in .env
docker-compose up -d
```

### Seed the databases

Run seeds in order — auth-service must run first since other services depend on its user IDs:

```bash
npm run prisma:seed --prefix auth-service
npm run prisma:seed --prefix event-service
npm run prisma:seed --prefix booking-service
npm run prisma:seed --prefix engagement-service
npm run prisma:seed --prefix payment-service
npm run prisma:seed --prefix notification-service
```

### Test accounts (password: `Password123!`)

| Email | Role | University |
|---|---|---|
| admin@buyin.cc | ADMIN | Buyin |
| alice@buyin.cc | STUDENT | Buyin |
| clara@buyin.cc | ORGANIZER | Buyin |
| hugo@buyin.cc | ORGANIZER | Buyin |
| david@wappnet.cc | STUDENT | Wappnet |
| emma@wappnet.cc | ORGANIZER | Wappnet |

---

## Architecture

### Communication flow

```
Browser
  └── cookie session-id (httpOnly)
        └── BFF Service (Express + Redis sessions)
              ├── x-user-id / x-user-role headers
              ├── auth-service      (PostgreSQL)
              ├── event-service     (PostgreSQL + Redis)
              ├── booking-service   (PostgreSQL + Redis locks)
              ├── payment-service   (PostgreSQL + Stripe)
              ├── engagement-service(PostgreSQL)
              └── notification-service (PostgreSQL)

Services → RabbitMQ (exchange: univent.notifications)
  ├── auth.otp              → notification-service sends OTP email
  ├── auth.approval         → notification-service sends approval email
  ├── auth.password-reset   → notification-service sends reset link
  ├── booking.confirmed     → notification-service sends ticket email
  ├── payment.succeeded     → booking-service confirms reservation
  ├── payment.refunded      → booking-service updates status
  └── xp.updated            → auth-service updates user XP/badge
```

The frontend communicates **only** with the BFF via `session-id` cookie — no JWT is exposed to the browser.

---

## Tech Stack

### Backend (all services)

| Layer | Technology |
|---|---|
| Runtime | Node.js + TypeScript |
| Framework | Express |
| ORM | Prisma 7 (PostgreSQL adapter) |
| Database | PostgreSQL (one DB per service) |
| Cache / Locks | Redis (ioredis) |
| Message broker | RabbitMQ (amqplib) — topic exchange |
| Storage | AWS S3 (presigned URLs) |
| Logging | Pino + pino-pretty |
| Auth | JWT (access 15min + refresh 7d in httpOnly cookie) |
| Payments | Stripe SDK + webhook verification |
| Validation | Zod |

### Frontend

| Layer | Technology |
|---|---|
| Framework | React 19 + TypeScript |
| Build tool | Vite |
| Data fetching | TanStack Query (React Query v5) |
| Routing | React Router v7 |
| Icons | Lucide React |
| Payments | Stripe.js |
| i18n | i18next + react-i18next (fr + en) |
| PWA | Service Worker + Web Manifest |
| Styling | CSS custom (mobile-first) |

### Infrastructure

| Layer | Technology |
|---|---|
| Containerization | Docker + Docker Compose |
| Reverse proxy | nginx |
| CI/CD | GitHub Actions |
| Cloud storage | AWS S3 |

---

## User Roles

| Role | Description |
|---|---|
| `STANDARD` | Public user — can browse, book, comment |
| `STUDENT` | Verified via university email — access to student pricing |
| `GROUP` | Collective account (BDE, association) — can organize events |
| `ORGANIZER` | KYC-approved organizer — can create paid events, access analytics |
| `ADMIN` | Platform admin — moderation, KYC review, user management |
| `SUPER_ADMIN` | Tiketa team — full access, can assign ADMIN role |

Role switching is supported — an `ORGANIZER` can switch to `STANDARD` view without losing their events or bookings.

---

## Key Features

| Feature | Service | Description |
|---|---|---|
| E-tickets & QR codes | booking-service | Digital tickets with unique QR codes, offline scan support |
| Event creation | event-service | Free, paid, or private events with multiple ticket categories |
| Promo codes | event-service | Percentage discounts with quota and expiry |
| Affiliate links | event-service | Trackable links with commission calculation |
| Stripe payments | payment-service | Card payments + Mobile Money (MTN, Orange, Wave, M-Pesa) |
| Variable commissions | payment-service | Commission rate varies by account type (3%–10%) |
| Vote system | engagement-service | Multi-phase voting with configurable electorate |
| Reviews & trust badge | engagement-service | 1–5 star reviews, auto-awarded trust badge at 10 reviews ≥ 4.0 |
| Social engagement | engagement-service | Likes, comments, saves, follows (users + events) |
| Notifications | notification-service | Email + push, retry with exponential backoff (max 3) |
| XP & badges | engagement-service | Gamification — XP earned per action, badge progression |
| Analytics dashboard | bff-service | Aggregated sales metrics, ticket breakdown, revenue charts |
| KYC verification | auth-service | ID document + bank info for organizer accounts |
| i18n | frontend | French and English, persisted in localStorage |
| PWA | frontend | Installable, offline fallback, service worker caching |

---

## Commission Rates

| Account type | Commission |
|---|---|
| STANDARD | 10% |
| STUDENT | 5% |
| GROUP | 7% |
| ORGANIZER | 3% |
| ADMIN / SUPER_ADMIN | 0% |

---

## Contact

| Channel | Info |
|---|---|
| Website | [tiketa.app](https://www.tiketa.app) |
| Email | [info@tiketa.eu](mailto:info@tiketa.eu) |
| Phone | +370 618 07405 |
| Facebook | [facebook.com/Tiketa](https://www.facebook.com/Tiketa) |

---

<div align="center">

© 2004–2026 Tiketa. All rights reserved.

</div>

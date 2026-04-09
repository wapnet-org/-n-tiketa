<div align="center">

# 🎟️ Tiketa — Event Platform

**Full-stack event ticketing platform — microservices architecture**

[![Website](https://img.shields.io/badge/tiketa.app-Visit%20Website-blue?style=for-the-badge&logo=google-chrome&logoColor=white)](https://www.tiketa.app)
[![Facebook](https://img.shields.io/badge/Facebook-Follow%20Us-1877F2?style=for-the-badge&logo=facebook&logoColor=white)](https://www.facebook.com/Tiketa)
[![Email](https://img.shields.io/badge/Contact-info@tiketa.eu-red?style=for-the-badge&logo=gmail&logoColor=white)](mailto:info@tiketa.eu)

![Status](https://img.shields.io/badge/status-active-brightgreen?style=flat-square)
![Architecture](https://img.shields.io/badge/architecture-microservices-blueviolet?style=flat-square)
![Region](https://img.shields.io/badge/region-Baltics-orange?style=flat-square)

</div>

---

## 📖 About

Tiketa is a comprehensive event ticketing platform connecting organizers with audiences across the Baltic region. This repository documents the technical architecture, design decisions, and development work done on the **Univent** platform — a modern rebuild of the ticketing system.

The platform handles thousands of events annually, processing millions of tickets through a secure, scalable microservices infrastructure.

---

## 🏗️ Architecture

The platform is built as a **microservices monorepo**, with each service owning its own database, business logic, and API.

```
├── auth-service          # JWT auth, sessions, user management
├── bff-service           # Backend-for-Frontend — API gateway & aggregation
├── booking-service       # Ticket reservations & order management
├── engagement-service    # Likes, comments, saves, follows, reports, ads
├── event-service         # Events, tickets, Redis cache, scheduler
├── frontend              # React PWA (React 19 + Vite + TanStack Query)
├── notification-service  # Push & email notifications
├── payment-service       # Stripe integration & webhooks
├── reel-service          # Short video content (reels/stories)
├── transcode-service     # Video transcoding pipeline
└── docs                  # Architecture docs & changelogs
```

---

## 🛠️ Tech Stack

### Backend
| Layer | Technology |
|---|---|
| Runtime | Node.js + TypeScript |
| Framework | Express |
| ORM | Prisma |
| Database | PostgreSQL (per service) |
| Cache | Redis |
| Message broker | RabbitMQ (amqplib) |
| Storage | AWS S3 |
| Logging | Pino |
| Security | Helmet, bcryptjs, JWT |

### Frontend
| Layer | Technology |
|---|---|
| Framework | React 19 + TypeScript |
| Build tool | Vite |
| Data fetching | TanStack Query |
| Routing | React Router v7 |
| Payments | Stripe.js |
| PWA | Service Worker + Web Manifest |
| Mock layer | MSW (Mock Service Worker) |

### Infrastructure
| Layer | Technology |
|---|---|
| Containerization | Docker + Docker Compose |
| Reverse proxy | nginx |
| CI/CD | GitHub Actions |
| Cloud | AWS (S3, presigned URLs) |

---

## ✨ Platform Features

| Feature | Description |
|---|---|
| 🎫 E-tickets & Mobile tickets | Digital ticket delivery, QR codes, mobile-first |
| 📊 Real-time analytics | Live sales tracking with Redis-cached event feeds |
| 💳 Payment integration | Stripe with webhook handling and fraud protection |
| 🎁 Loyalty & gamification | XP system, badges, rewards for returning users |
| 🎬 Reels & Stories | Short video content with transcoding pipeline |
| 💬 Social engagement | Likes, comments, saves, follows, reports |
| 🔔 Notifications | Push & email notifications via message broker |
| 🛎️ 24/7 support | Dedicated support throughout the event lifecycle |

---

## 📬 Contact

| Channel | Info |
|---|---|
| 🌐 Website | [tiketa.app](https://www.tiketa.app) |
| 📧 Email | [info@tiketa.eu](mailto:info@tiketa.eu) |
| 📞 Phone | +370 618 07405 |
| 📘 Facebook | [facebook.com/Tiketa](https://www.facebook.com/Tiketa) |

---

<div align="center">

© 2004–2026 Tiketa. All rights reserved.

</div>

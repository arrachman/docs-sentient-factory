# Changelog

All notable changes to the Sentient Factory project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Planned
- WhatsApp integration for Executive Hub
- Barcode/QR scanning for inventory
- Mobile responsive improvements
- Advanced AI analytics dashboard

---

## [1.0.0] - 2024-12-27

### Added

#### Documentation
- **README.md** - Project overview, quick start, tech stack
- **rules.md** - Business rules, AI persona, SDLC standards
- **system_analysis.md** - ERD, entity definitions, data model
- **architecture.md** - System architecture, tech stack, data flow

#### Business Process Analysis
- **bpa-1.md** - Core System (Auth, Users, Dashboard)
- **bpa-2.md** - Transaction Modules (SO, DO, PO, WO, Inventory)
- **bpa-3.md** - AI & Analytics (Chat, Alerts, Reports)

#### Design
- **ui-design.md** - UI/UX specification, wireframes, components
- **master_prompting.md** - AI prompt templates, persona

#### Development Specs
- **frontend.md** - React + Metronic implementation guide
- **backend.md** - NestJS API specification
- **api-contract.md** - API request/response schemas
- **database.md** - PostgreSQL DDL, functions, triggers, seed data
- **python-ai.md** - Python AI service with agentic framework

#### Infrastructure
- **deployment.md** - Docker, CI/CD, backup, monitoring
- **security.md** - Authentication, RBAC, OWASP compliance
- **ai-setup.md** - Local LLM setup guide (Mac M4)
- **ai-gemini.md** - Cloud AI (Gemini) integration guide

#### Quality
- **qa.md** - Test cases, E2E testing, performance testing

### Technical Stack
- Frontend: React 18, TypeScript, Metronic 9.3.7
- Backend: NestJS 10, TypeORM
- Database: PostgreSQL 15
- Cache: Redis 7
- Message Broker: NATS 2
- AI: Python 3.11, LangChain, Qwen-72B / Gemini
- Vector DB: ChromaDB

---

## Version History

| Version | Date       | Description                   |
| ------- | ---------- | ----------------------------- |
| 1.0.0   | 2024-12-27 | Initial documentation release |

---

## Contributors

- Product Owner
- Tech Lead
- Development Team

---

## Links

- [Documentation](README.md)
- [API Contract](api-contract.md)
- [Deployment Guide](deployment.md)
- [Security Spec](security.md)

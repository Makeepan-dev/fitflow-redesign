# Architecture Decision Record (ADR 001)

## Status
**Accepted**

## Context
FitFlow is transitioning from a legacy codebase to a scalable, cross-platform redesign. The application requires high UI rendering performance across mobile and web, real-time biometric and workout synchronization, compliance with health privacy standards (HIPAA/GDPR), and an AI inference pipeline for personalized workout generation.

## Decision
We have decided to adopt the following architectural stack:
1. **Frontend:** Flutter for multi-platform client distribution.
2. **Core API Gateway & Backend:** NestJS (Node.js/TypeScript) implementing a modular microservices architecture.
3. **AI Engine:** Standalone FastAPI (Python) microservice exposing endpoints for workout recommendations and personalized planning.
4. **Primary Persistence:** PostgreSQL with TimescaleDB extension for time-series telemetry.
5. **Authentication:** Supabase Auth utilizing Row-Level Security (RLS) and JWT verification.

## Consequences

### Positive
- Single unified UI codebase reducing engineering overhead and maintenance costs.
- Native compiled client-side rendering performance.
- Relational integrity combined with optimized time-series storage for health telemetry.
- Python ecosystem accessibility for ongoing ML/AI model updates.

### Negative
- Flutter web initial bundle size is larger than pure React/Next.js.
- Requires team cross-skilling in both Dart (frontend) and TypeScript/Python (backend).

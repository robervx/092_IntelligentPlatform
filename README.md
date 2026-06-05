# 092_IntelligentPlatform -  Cuadro de Mando Estratégico (CME)
Plataforma Inteligente para la gestión operativa de una Sala 092

Plataforma interna de inteligencia operativa. Acceso exclusivo desde red corporativa local.

---

## Arranque rápido (modo mock — sin BD)

```bash
cp .env.example .env.local   # DATA_SOURCE=mock ya configurado
make dev                      # levanta Next.js + FastAPI + Redis
# Abrir http://localhost:3000
```

## Con SQL Server real (red corporativa)

```bash
# En .env.local:
DATA_SOURCE=dev
MSSQL_HOST=<ip>
MSSQL_DATABASE=<nombre>
MSSQL_USER=<usuario>
MSSQL_PASSWORD=<contraseña>
make dev-real
```

---

## Stack

| Capa | Tecnología | Puerto |
|------|-----------|--------|
| Frontend | Next.js 14 (App Router, TypeScript) | 3000 |
| Backend | FastAPI (Python 3.11) | 8000 |
| BD | SQL Server (Microsoft) | 1433 |
| Cache | Redis | 6379 |

---

## Documentación (leer en este orden)

| Documento | Para qué |
|-----------|----------|
| **[CLAUDE.md](./CLAUDE.md)** | Instrucciones rápidas para Claude Code |
| **[docs/PROJECT_MASTER.md](./docs/PROJECT_MASTER.md)** | Contexto completo del proyecto — leer primero |
| **[docs/PRD.md](./docs/PRD.md)** | Visión de producto, fases, métricas de éxito |
| **[docs/SPRINTS.md](./docs/SPRINTS.md)** | Qué hay que hacer ahora y en qué orden |
| **[docs/AGENT_ROLES.md](./docs/AGENT_ROLES.md)** | Los 9 roles de agentes AI y sus territorios |
| **[docs/DECISION_LOG.md](./docs/DECISION_LOG.md)** | Decisiones tomadas — no reabrir sin nuevo ADR |
| **[docs/RISKS.md](./docs/RISKS.md)** | Riesgos activos con mitigaciones |
| **[docs/DEFINITION_OF_DONE.md](./docs/DEFINITION_OF_DONE.md)** | Criterios de calidad por tarea/sprint/módulo |
| **[docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)** | Stack, capas, decisiones técnicas |
| **[docs/MODULES_AND_ROUTING.md](./docs/MODULES_AND_ROUTING.md)** | Módulos 1A/1B/2/3, rutas web y API |
| **[docs/M1A_DATA_CONTRACTS.md](./docs/M1A_DATA_CONTRACTS.md)** | Contratos DTO de los 7 bloques del dashboard |
| **[docs/DATA_LAYER.md](./docs/DATA_LAYER.md)** | Capas SQL, vistas serving, convención de nombres |
| **[docs/MOCK_STRATEGY.md](./docs/MOCK_STRATEGY.md)** | Cómo funciona mock vs dev |
| **[docs/STAKEHOLDERS.md](./docs/STAKEHOLDERS.md)** | Mapa de partes interesadas |
| **[docs/DATA_QUALITY.md](./docs/DATA_QUALITY.md)** | Análisis de calidad de hist_AgentCallData (Sprint 2) |
| **[docs/IDEA_LOG.md](./docs/IDEA_LOG.md)** | Ideas capturadas — revisar los viernes |
| **[docs/kpi/INDEX.md](./docs/kpi/INDEX.md)** | Diccionario de KPIs gobernados |

---

## Módulos del producto

| Código | Nombre | Ruta | Sprint | Estado |
|--------|--------|------|--------|--------|
| **1A** | Tiempo real (dashboard) | `/situacion-operativa/tiempo-real` | S0–S2 | 🟢 Activo |
| **1A-K** | Kiosk (pantalla TV sala) | `/situacion-operativa/tiempo-real/kiosk` | S1 | 🟢 Activo |
| **1B** | Evolución y tendencias | `/situacion-operativa/evolucion-y-tendencias` | S4–S5 | 🔵 Siguiente |
| **2** | Anticipación operativa | `/anticipacion-operativa/...` | S6–S7 | ⚪ Futuro |
| **3** | Apoyo a la decisión | `/apoyo-a-la-decision/...` | S8 | ⚪ Futuro |

---

## Principios de arquitectura

1. Frontend = render only. Ningún KPI se calcula en JavaScript.
2. API = único lector de SQL. FastAPI es el único servicio con acceso a BD.
3. SQL = fuente de verdad de negocio.
4. Mock/real = solo variable de entorno (`DATA_SOURCE`).
5. Un KPI, un YAML aprobado en `docs/kpi/`.

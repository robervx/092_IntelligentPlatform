CME-CISE · Cuadro de Mando Estratégico
Plataforma interna de inteligencia operativa para la Policía Local de València.
Transforma datos operativos dispersos en conocimiento accionable para mandos que trabajan bajo presión, con datos en tiempo real, en sala de mando.

Acceso exclusivo desde red corporativa local. Sin exposición a internet.

Qué hace este producto
Los mandos de la sala CISE 092 toman decisiones críticas — refuerzo de turno, redistribución de recursos, priorización de atención — con información fragmentada y sin contexto predictivo. CME-CISE cierra ese gap con cuatro capas progresivas:

Capa	Pregunta	Módulo	Estado
Ver	¿Qué está pasando ahora mismo?	1A · Tiempo real	🟢 Activo
Entender	¿Qué ha pasado y cómo evolucionamos?	1B · Evolución y tendencias	🔵 Siguiente
Anticipar	¿Qué va a pasar en las próximas horas?	2 · Anticipación operativa	⚪ Futuro
Decidir	¿Qué debería hacer el mando?	3 · Apoyo a la decisión	⚪ Futuro
El diferenciador del producto es el bucle completo: datos → análisis → recomendación → decisión del mando → registro → retroalimentación. El Decision Log convierte las decisiones operativas en memoria institucional.

Stack técnico
Capa	Tecnología	Puerto
Frontend	Next.js 14 · App Router · TypeScript	3000
Backend	FastAPI · Python 3.11	8000
Base de datos	SQL Server (Microsoft) — sistema corporativo existente	1433
Cache	Redis — TTL por endpoint	6379
Proxy (prod)	Nginx — único puerto expuesto	—
Contenedores	Docker + Docker Compose	—
Arranque rápido
Modo mock (sin base de datos — desarrollo en casa)
cp .env.example .env.local   # DATA_SOURCE=mock ya configurado
make dev                      # levanta Next.js + FastAPI + Redis en Docker
Abrir http://localhost:3000 → redirige automáticamente al dashboard.

Modo nativo (sin Docker)
cp .env.example .env.local
make dev-native
Con SQL Server real (red corporativa)
# Editar .env.local:
DATA_SOURCE=dev
MSSQL_HOST=<ip>
MSSQL_DATABASE=<nombre>
MSSQL_USER=<usuario>
MSSQL_PASSWORD=<contraseña>

make dev-real
Cambiar de modo = solo editar .env.local. Sin cambiar código.

Comandos disponibles
make dev              # Arranca todo en modo mock (Docker)
make dev-native       # Arranca sin Docker
make dev-real         # Arranca con SQL Server real
make test             # Tests completos
make test-endpoints   # Verifica los 7 endpoints del módulo 1A
make logs-api         # Logs del API en tiempo real
make clean            # Limpia contenedores y volúmenes
Módulo 1A — Dashboard tiempo real (7 bloques)
El dashboard no es una colección de métricas. Cada bloque responde una pregunta operativa concreta:

#	Bloque	Pregunta
1	Cabecera operativa	¿Dónde estoy, qué turno es, están los datos al día?
2	Recorrido llamada→sala	¿Cómo está fluyendo el servicio ahora mismo?
3	Capacidad vs. demanda	¿Tenemos suficiente gente para lo que está entrando?
4	Tablero de puestos	¿Qué está haciendo cada puesto en este momento?
5	Comportamiento 24h	¿Es esto normal para esta hora del día?
6	Alertas operativas	¿Qué está fallando o en riesgo ahora mismo?
7	Insight operativo	En una frase: ¿qué está pasando y qué importa?
Rutas web:

/situacion-operativa/tiempo-real          → Dashboard (módulo 1A)
/situacion-operativa/tiempo-real/kiosk    → Pantalla TV sala (full-screen, auto-refresh 30s)
/situacion-operativa/evolucion-y-tendencias → Módulo 1B (placeholder)
/anticipacion-operativa/*                 → Módulo 2 (placeholder)
/apoyo-a-la-decision/*                    → Módulo 3 (placeholder)
Arquitectura — principios no negociables
Frontend (Next.js)  →  API (FastAPI)  →  Cache (Redis)  →  SQL Server
     ↑                     ↑
  render only          único lector de BD
  no calcula KPIs      lógica de negocio aquí
Frontend = render only. Ningún KPI se calcula en JavaScript. Solo renderiza DTOs.
API = único lector de SQL. El frontend nunca toca la base de datos.
SQL = fuente de verdad de negocio. Lógica de capacidad, alertas e insight vive en vistas SQL o en el API.
Mock/real = solo variable de entorno. DATA_SOURCE=mock|dev|prod. Sin código condicional en la app.
Un KPI, un YAML. Ningún KPI aparece en pantalla sin entrada aprobada en docs/kpi/.
Rutas en español, kebab-case. Sin /live, /dashboard ni rutas genéricas.
Estructura del repositorio
cme-cise/
├── apps/
│   ├── web/                    # Next.js 14 — frontend
│   │   └── src/
│   │       ├── app/            # App Router (páginas y layouts)
│   │       ├── components/m1a/ # Componentes del dashboard 1A
│   │       └── lib/m1a/        # API client, tipos
│   └── api/                    # FastAPI — backend
│       └── src/
│           ├── routers/m1a/    # 7 endpoints tiempo real
│           ├── repositories/   # Mock y SQL (switch por env)
│           └── schemas/m1a/    # DTOs Pydantic
├── packages/
│   └── contracts/src/m1a/      # Tipos TypeScript compartidos (sincronizados con dtos.py)
├── data/
│   └── sql/mssql/092/          # Scripts SQL en 6 capas (staging → serving)
├── docs/                       # Documentación completa del proyecto
│   ├── kpi/                    # Diccionario de KPIs gobernados (YAML)
│   └── PROJECT_MASTER.md       # Fuente de verdad — leer primero
├── infra/                      # Nginx, Docker configs producción
├── docker-compose.yml
├── Makefile
└── .env.example
Documentación
Documento	Para qué
docs/PROJECT_MASTER.md	Contexto completo — leer antes de cualquier acción
docs/PRD.md	Visión de producto, fases, métricas de éxito
docs/SPRINTS.md	Sprint activo y tareas pendientes
docs/ARCHITECTURE.md	Stack, capas, decisiones técnicas
docs/M1A_DATA_CONTRACTS.md	Contratos DTO de los 7 bloques
docs/DECISION_LOG.md	Decisiones tomadas — no reabrir sin nuevo ADR
docs/AGENT_ROLES.md	Los 9 roles de agentes AI y sus territorios
docs/RISKS.md	Riesgos activos con mitigaciones
docs/DEFINITION_OF_DONE.md	Criterios de calidad por tarea/sprint/módulo
docs/kpi/INDEX.md	Diccionario de KPIs gobernados
CLAUDE.md	Instrucciones para agentes Claude Code
Requisitos de rendimiento
Métrica	Objetivo
Carga inicial del dashboard	< 2 segundos
Refresco de datos	cada 10–30 segundos según bloque
Latencia API con cache activo	< 200 ms
Latencia API sin cache (cold)	< 1 segundo
Lag máximo de datos	< 30 segundos
Disponibilidad (horario operativo 24/7)	99.5%
Modelo de ejecución
El proyecto lo ejecuta Rober (Data Officer, CISE 092), asistido por un equipo de 9 agentes AI especializados: Product Manager, Project Manager, Software Architect, Full-Stack Developer, Backend & Integrations, Data Scientist, UX Designer, Security/Sysadmin, y OSINT.

Cada agente tiene un territorio exclusivo documentado en docs/AGENT_ROLES.md. La sincronización entre packages/contracts/src/m1a/types.ts y apps/api/src/schemas/m1a/dtos.py es obligatoria en la misma sesión.

Lo que este producto NO es
No es un sistema de gestión de incidencias (eso es SIGO/CAD, que ya existe)
No es una herramienta de vigilancia de empleados (solo puestos físicos, no personas)
No decide autónomamente — sugiere. El mando siempre tiene la última palabra
No se conecta a internet ni a servicios externos en producción
No es pública ni accesible desde fuera de la red corporativa
CME-CISE · Policía Local de València · Uso interno exclusivo

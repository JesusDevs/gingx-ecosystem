# Gingx Ecosystem v0.3.1

[🇬🇧 English](README.md) · 🇪🇸 **Español**

**Desarrollo de IA guiado por harness.** Spec → Plan → Código → Test → Seguridad → Memoria. Cada fase es un contrato. No hay código sin spec aprobado. Memoria vectorial persistente que viaja con el repo.

## En 30 segundos

```bash
# 1. Instala todo (un comando)
curl -fsSL https://raw.githubusercontent.com/JesusDevs/gingx-ecosystem/main/gingx-sdd/install.sh | bash

# 2. Inicializa cualquier proyecto con el ecosistema completo
cd mi-proyecto
gingx-sdd init

# 3. Tu proyecto ahora tiene: 9 agentes, 3 hooks, 19 skills, 8 perfiles
#    - memoria vectorial (mnemo) para búsqueda semántica entre sesiones
#    - grafo de conocimiento (graphify) de todo el codebase
#    - spec gate que bloquea escritura de código sin spec aprobado
#    - goal agent que ejecuta objetivos de forma autónoma (ej. durante la noche)

# 4. Crea un spec y delega al agente correcto
gingx-sdd hdu create "Mi feature" --question "¿Cuál es el stack correcto?"
gingx-sdd auto "implementar endpoint de health check"

# 5. Lanza un objetivo autónomo
gingx-sdd goal create map-architecture \
  --objective "Mapear y documentar toda la arquitectura" \
  --key-results "KR1: domain map completo, KR2: component index, KR3: decisions log"

# 6. Todo se autoguarda. La memoria viaja con el repo.
git add .gingx/memory/entries.jsonl && git commit -m "memoria del equipo"
```

## ¿Por qué Python para el harness SDD?

**Pragmático, no ideológico.** Cada capa usa la herramienta correcta:

| Capa | Lenguaje | Por qué |
|-------|----------|-----|
| **Memoria vectorial** (mnemo) | **Go** | Crítico en rendimiento: embeddings, similitud coseno, SQLite. Compila a un binario estático único. Cero dependencias. |
| **Harness SDD** (gingx-sdd) | **Python** | Orquestación de flujo: CLI (Typer), config YAML, sistema de goals, despacho multi-agente, renderizado de templates. Python es el mejor "pegamento" para conectar herramientas. |
| **Grafo de conocimiento** (graphify) | **Python** | Parsing AST con tree-sitter, detección de comunidades (Leiden), extracción con LLM. Ecosistema rico. |
| **Personas de agente** | **Markdown** | Portable entre herramientas de IA para código (Claude Code, Codex, Cursor, Kiro, Antigravity). |

El CLI migrará progresivamente a Go para distribución sin dependencias. Python sigue siendo la capa de orquestación por ahora — la lógica del harness es lo bastante compleja como para que iterar rápido en Python entregue más valor que un port a Go en este momento.

## Qué incluye

| Capa | Componente | Lenguaje | Qué hace |
|------|-----------|----------|--------------|
| **Memoria** | [gingx-mnemo](gingx-mnemo/) | Go | Memoria vectorial con SQLite + embeddings de Ollama. 12 herramientas MCP. Portable vía `.gingx/memory/entries.jsonl` |
| **Harness SDD** | [gingx-sdd](gingx-sdd/) | Python | Pipeline de 8 fases, 9 agentes, 30 contratos de harness, sistema de goals, auto-delegación |
| **Conocimiento** | Grafo de conocimiento | YAML + Obsidian + graphify | domain-map, component-index, decisions-log. Autogenerado, versionado, visualizado |
| **Agentes** | [.claude/agents/](.claude/agents/) | Markdown | 9 personas con triggers, reglas y acceso a herramientas |
| **Hooks** | [.claude/hooks/](.claude/hooks/) | Bash | SessionStart (carga contexto), PreToolUse (spec gate), Stop (persiste progreso) |
| **Skills** | [skills/](gingx-sdd/skills/) + [extras/](gingx-sdd/extras/skills/) | Markdown | 9 skills de equipo + 19 skills de tech stack en 6 categorías |
| **Perfiles** | [.gingx/profiles/](.gingx/profiles/) | YAML | 8 composiciones de equipo pre-armadas |

## Los 9 agentes

| # | Agente | Trigger | Qué hace |
|---|-------|---------|--------------|
| 1 | `supervisor` | Orquestar HDUs | Descompone, delega, trackea progreso entre fases |
| 2 | `explorer-agent` | SessionStart, preguntas "dónde/cómo" | Mapea el codebase, mantiene el grafo de conocimiento |
| 3 | `po-agent` | Definir features | Specs, escenarios Gherkin, negociación de alcance |
| 4 | `architect-agent` | Diseñar sistemas | Trade-offs, esquemas de BD, contratos de API, dependencias |
| 5 | `dev-agent` | Implementar | TDD, test-first, convenciones de código |
| 6 | `qa-agent` | Verificar | Testing adversarial, validación BDD, causa raíz |
| 7 | `ux-agent` | Revisar UI/UX | Accesibilidad (WCAG), usabilidad, patrones de diseño |
| 8 | `devops-agent` | Entregar | CI/CD, escaneos de seguridad, auditoría de dependencias |
| 9 | `goal-agent` | Objetivos autónomos | Ejecuta goals sin interacción humana (noche/fin de semana) |

## El ciclo de operación de 3 fases

```
SessionStart                  Sesión activa                    Stop
───────────                   ──────────────                   ────
Carga contexto         →   9 agentes trabajan       →   Persiste progreso
Carga grafo de conocim.    Spec gate activo             Refresca vault de Obsidian
Mnemo sync pull            Consultas a memoria vec.     Graphify AST update (código)
HDUs activos + bloqueos    Goal loops                   Exporta conocimiento
Importa memoria portable   Auto-delegación
```

## Instalación

### Automática (recomendada)

```bash
# Un comando. Instala Python, Go, Node, Ollama, mnemo, gingx-sdd, graphify, codegraph.
curl -fsSL https://raw.githubusercontent.com/JesusDevs/gingx-ecosystem/main/gingx-sdd/install.sh | bash
```

Qué hace:
1. Detecta el SO (macOS, Linux, Windows)
2. Instala prerrequisitos: Python 3.11+, Go, Node 20+
3. Instala Ollama + el modelo de embeddings BGE-M3 (local, sin costo de API)
4. Compila `mnemo` desde el código fuente → `/usr/local/bin/mnemo`
5. Instala el paquete Python `gingx-sdd` (`pip install -e`)
6. Crea la estructura `.gingx/` (config, perfiles, tracking)
7. Configura Claude Code: copia hooks, agentes, settings, servidores MCP
8. Registra el skill de graphify (`graphify claude install`)
9. Detecta el stack del proyecto e instala los skills correspondientes
10. Inicializa la estructura de OpenSpec

### Manual

```bash
git clone https://github.com/JesusDevs/gingx-ecosystem.git
cd gingx-ecosystem

# Instalar mnemo (Go) — no hay binarios precompilados publicados aún, se compila desde el código
cd gingx-mnemo && go build -o mnemo . && cp mnemo /usr/local/bin/

# Instalar OpenSpec (paquete npm público, no es parte de este repo)
npm install -g @fission-ai/openspec

# Instalar gingx-sdd (Python) — instalación editable, el código fuente queda en esta ruta
cd ../gingx-sdd && pip install -e .

# Instalar graphify
uv tool install graphifyy && graphify claude install

# Ollama + modelo de embeddings (mnemo lo necesita para embeddings locales, sin costo)
brew install ollama && ollama pull bge-m3   # o el método de instalación de Ollama de tu SO

# Inicializar un proyecto
cd ~/mi-proyecto && gingx-sdd init
openspec init --tools claude   # gingx-sdd init todavía no llama esto — ejecútalo una vez por proyecto
```

> **Fijar una versión**: `git clone` te trae `main` (lo último, posiblemente por delante del último
> release etiquetado). Para instalar una versión publicada específica: `git checkout v0.3.1` antes
> de correr los pasos de arriba, o `git clone --branch v0.3.1 --depth 1 ...`. Ver [Versionado](#versionado).

## Versionado

El ecosistema sigue [versionado semántico](https://semver.org/) (`vMAJOR.MINOR.PATCH`), registrado
como tags de git en este repo (`v0.2.0`, `v0.3.0`, `v0.3.1`, ...). [CHANGELOG.md](CHANGELOG.md) es
la fuente de verdad de qué se publicó en cada release.

```bash
gingx-sdd release v0.4.0 --all         # tag + snapshot de mnemo + entrada de changelog, todo el monorepo
gingx-sdd release v0.4.0 --all --dry-run   # preview sin crear tags
git tag -l                             # ver todas las versiones publicadas
```

`gingx-sdd release` **no** sube automáticamente la versión de `gingx-sdd/pyproject.toml` ni de
`gingx-sdd/gingx_sdd/__init__.py` — hay que actualizarlas a mano en el mismo commit del release
para que `pip show gingx-sdd` reporte la versión realmente instalada. Cada componente del monorepo
versiona de forma independiente (mnemo, gingx-sdd, graphify tienen su propio ritmo); el tag/versión
del README a nivel raíz refleja el ecosistema como conjunto.

## Usar el ecosistema en otro proyecto

```bash
cd ~/mi-proyecto-nuevo
gingx-sdd init                          # crea 23+ archivos

# Elige un stack (afecta qué skills se cargan)
gingx-sdd init --stack langgraph        # IA + LangGraph + FastAPI
gingx-sdd init --stack go               # Go + Fiber
gingx-sdd init --stack react            # React + Next.js
gingx-sdd init --stack minimal          # Solo lo esencial

# Preview sin escribir nada
gingx-sdd init --dry-run

# Sobrescribir config existente
gingx-sdd init --force
```

### Después de `init`, tu proyecto tiene:

```
mi-proyecto/
├── .claude/
│   ├── agents/           # 8 personas de agente
│   ├── hooks/            # 3 hooks: SessionStart, PreToolUse, Stop
│   └── settings.local.json
├── .gingx/
│   ├── config.yaml       # 30 contratos de harness
│   ├── profiles/         # 7 perfiles de equipo
│   ├── knowledge/        # domain-map, component-index (autogenerado)
│   ├── memory/           # entries.jsonl — memoria portable del equipo
│   ├── goals/            # definiciones de goals autónomos
│   └── current_task.yaml # tracking del HDU activo
├── openspec/
│   ├── AGENTS.md
│   └── changes/
└── .mcp.json             # servidores MCP de mnemo + codegraph
```

## Matriz de auto-actualización

Todo se mantiene al día sin intervención manual:

| Qué | Hook | Cuándo |
|------|------|---------|
| Grafo de conocimiento (domain-map) | SessionStart | Primera corrida (auto-descubrimiento) |
| Timestamps de conocimiento | Stop | Cada fin de sesión |
| Refresco del vault de Obsidian | Stop | Cada fin de sesión |
| Graphify AST update (solo código) | Stop | Cada fin de sesión |
| Exportación de conocimiento a mnemo | Stop | Cada fin de sesión |
| Mnemo sync push | Stop | Cada fin de sesión |
| Mnemo import (memoria portable) | SessionStart | Cada inicio de sesión |
| Mnemo sync pull | SessionStart | Cada inicio de sesión |

## Agregar más skills

Los skills son archivos Markdown con frontmatter YAML. Se auto-descubren con `gingx-sdd team list`.

### Agregar un skill de tech stack

```bash
cat > gingx-sdd/extras/skills/backend/rust.md << 'EOF'
---
name: rust
description: Convenciones de Rust — ownership, borrowing, async, tokio
category: backend
model: sonnet
effort: high
---

# Convenciones de Rust

## Reglas
- Clippy estricto. Sin warnings en CI.
- `cargo test` antes de cada commit.
- Usar `anyhow` para errores de aplicación, `thiserror` para librerías.

## Patrones
- Patrón repository con sqlx
- Modelo actor con actix para concurrencia
EOF
```

**Categorías:** `ai/`, `backend/`, `mobile/`, `web/`, `testing/`, `infra/`

### Agregar un skill de persona de agente

```bash
cat > gingx-sdd/skills/team/security-agent.md << 'EOF'
---
name: security-agent
description: Auditor de seguridad — OWASP, secretos, auditoría de dependencias
model: sonnet
effort: high
tools: Bash, Read, Grep, Glob
trigger: /security-audit
---

# Security Agent

## Tu trabajo
1. Escanear vulnerabilidades del OWASP Top 10
2. Auditar dependencias por CVEs conocidos
3. Revisar secretos hardcodeados

## Protocolo
- `gitleaks detect --no-git` primero
- Reportar: severidad, archivo, línea, remediación
EOF
```

Luego regístralo en un perfil:
```bash
gingx-sdd team spawn security-agent -t "auditar módulo de auth" --profile developer
```

## Agregar más hooks

Los hooks son scripts bash en `.claude/hooks/`. Claude Code los ejecuta automáticamente.

| Hook | Cuándo | Úsalo para |
|------|------|---------|
| `SessionStart` | Al iniciar la sesión | Cargar contexto, grafo de conocimiento, bloqueos |
| `PreToolUse` | Antes de cada llamada a herramienta | Bloquear herramientas sin spec aprobado |
| `PostToolUse` | Después de cada llamada a herramienta | Logging, métricas, auto-guardado |
| `PostToolBatch` | Después de un batch de herramientas | Validación post-cambio |
| `Stop` | Al terminar la sesión | Persistir progreso, sync mnemo, actualizar grafo |
| `PreCompact` | Antes de comprimir el contexto | Guardar decisiones antes de perder contexto |
| `Notification` | Notificaciones del sistema | Bloqueos, goals completados |

### Crear un hook nuevo

```bash
cat > .claude/hooks/PostToolUse.sh << 'EOF'
#!/usr/bin/env bash
set -euo pipefail

TOOL_NAME="${CLAUDE_TOOL_NAME:-unknown}"
DURATION_MS="${CLAUDE_TOOL_DURATION_MS:-0}"
PROJECT=$(basename "$(pwd)")

# Registra el uso de herramientas para métricas de costo/rendimiento
if command -v mnemo &>/dev/null; then
    mnemo save "Tool: $TOOL_NAME" \
        "Duration: ${DURATION_MS}ms" \
        --type metric --tags tool-usage,performance \
        2>/dev/null || true
fi

echo '{"continue": true}'
EOF

chmod +x .claude/hooks/PostToolUse.sh
```

### Variables de entorno de los hooks

- `$CLAUDE_TOOL_NAME` — nombre de la herramienta
- `$CLAUDE_TOOL_INPUT` — input de la herramienta (JSON)
- `$CLAUDE_PROJECT` — nombre del proyecto
- `$CLAUDE_SESSION_ID` — ID único de sesión

## Agregar más agentes

### 1. Crear el archivo de persona

```bash
cat > .claude/agents/data-engineer-agent.md << 'EOF'
---
name: data-engineer-agent
description: Ingeniero de pipelines de datos — ETL, esquemas, calidad de datos
tools: Bash, Read, Write, Grep, Glob
when_to_use: |
  Usar al diseñar pipelines de datos, esquemas, o flujos ETL.
  Se activa con: "pipeline", "ETL", "esquema", "calidad de datos".
---

# Data Engineer Agent

## Tu trabajo
1. Diseñar y revisar esquemas de base de datos
2. Construir definiciones de pipelines ETL
3. Reglas de validación de calidad de datos
EOF
```

### 2. Agregarlo a un perfil

```yaml
# En .gingx/profiles/developer.profile.yaml
agents:
  data-engineer-agent:
    model: sonnet
    tech_stack: [python-core, postgres]
```

### 3. Usarlo

```bash
gingx-sdd team spawn data-engineer-agent -t "diseñar ETL para eventos de usuario"
```

## Grafo de conocimiento

Un grafo de conocimiento vivo del codebase, mantenido por el explorer agent:

```bash
gingx-sdd knowledge status                  # estado de grafo + codegraph + mnemo
gingx-sdd knowledge explore GoalState       # callers, callees, impacto
gingx-sdd knowledge search "patrón de auth" # búsqueda cruzada en todas las capas
gingx-sdd knowledge save-decision "Usar Redis" --rationale "..." --trade-off "..."
gingx-sdd knowledge vault                   # genera vault de Obsidian con [[wikilinks]]
/graphify . --update                        # actualización incremental del grafo (solo código = gratis)
```

### Capas de conocimiento

| Archivo | Contenido | Autogenerado |
|------|----------|----------------|
| `domain-map.yaml` | Dominios, símbolos clave, dependencias, patrones | Hook SessionStart |
| `component-index.yaml` | Componentes con callers/callees, tipo, rol | Explorer agent |
| `decisions-log.yaml` | Decisiones de arquitectura con justificación | Manual (`knowledge save-decision`) |
| `vault/` | Vault de Obsidian con [[wikilinks]] + vista de grafo | Hook Stop (auto-refresh) |
| `graphify-out/` | Grafo completo: graph.json, GRAPH_REPORT.md, graph.html | `/graphify .` |

## Sistema de Goals v0.3.0

Objetivos autónomos ejecutados por el goal-agent sin interacción humana:

```bash
gingx-sdd goal create document-auth \
  --objective "Documentar todo el sistema de auth" \
  --key-results "KR1: diagrama de flujo OAuth2, KR2: docs de SessionStore, KR3: casos de test" \
  --max-iterations 30

gingx-sdd goal status document-auth
gingx-sdd goal list

# Lanzar el loop autónomo
gingx-sdd team spawn goal-agent \
  -t "Ejecutar el siguiente paso del goal document-auth" \
  --profile goal-autonomous

gingx-sdd goal complete document-auth
gingx-sdd goal complete refactor-db --blocked --reason "Esperando migración de prod"
```

**Patrón del goal loop**: `plan → act → observe → reflect` — Go puro, persistido vía YAML en `.gingx/goals/<id>.yaml`. Compatible con `/loop` de Claude Code para ejecución autónoma.

## Perfiles de equipo

8 perfiles pre-armados para distintos tipos de proyecto:

| Perfil | Agentes | Stack | Mejor para |
|---------|--------|-------|----------|
| `developer` | Los 9 agentes | python-core, fastapi | Desarrollo general |
| `fullstack` | 7 agentes | react, fastapi, postgres | Apps web full-stack |
| `fullstack-go` | 7 agentes | go-fiber, react | Backend Go + frontend |
| `fullstack-python-langgraph` | 9 agentes | langgraph-python, fastapi | Apps de IA/agentes |
| `goal-autonomous` | goal-agent | goal, langgraph | Autónomo overnight/fin de semana |
| `react-nextjs` | 7 agentes | nextjs, react, tailwind | Frontend-heavy |
| `minimal` | 4 agentes | python-core | Prototipos rápidos |
| `team` | 9 agentes | variable | Convenciones de equipo |

```bash
gingx-sdd team profile set fullstack-python-langgraph
gingx-sdd team profile show
gingx-sdd team profile list
```

## Modos de ejecución

```bash
gingx-sdd mode set interactive   # Pide confirmación en decisiones clave
gingx-sdd mode set automatic     # Avanza fase por fase, menos interrupciones
gingx-sdd mode set dry_run       # Solo checks de pre-release, sin ejecución real
gingx-sdd mode set off           # Harness desactivado, modo libre
gingx-sdd mode status
```

## Comandos esenciales

```bash
# Info del ecosistema
gingx-sdd status                     # HDU activo, modo, bloqueos
gingx-sdd knowledge status           # Grafo de conocimiento + codegraph + mnemo
gingx-sdd team list                  # Agentes y skills disponibles
gingx-sdd team profile show          # Perfil activo
gingx-sdd changelog                  # Generar CHANGELOG desde HDUs

# Memoria
mnemo search "patrón similar" --project $(basename $(pwd)) --limit 5
mnemo save "Decisión" "Qué y por qué" --type decision --outcome resolved
mnemo stats                          # Estadísticas del store
mnemo import                         # Importar desde .gingx/memory/
```

## Filosofía

> "Un harness transforma autonomía cruda en trabajo de ingeniería controlado." — Alan Buscaglia, Gentle AI

30 contratos de harness en `.gingx/config.yaml`. Cada uno es un **contrato operacional**, no una sugerencia.

**Principios:**
- **Sin spec, no hay código.** El spec gate bloquea Write/Edit sin un spec aprobado.
- **La memoria viaja con el repo.** `.gingx/memory/entries.jsonl` se commitea. Clonar = memoria del equipo.
- **Cada agente recibe solo su contexto.** Aislamiento de subagentes: el dev agent no ve todo el repo.
- **Autonomía con trazabilidad.** Cada goal deja historial en `.gingx/goals/<id>.yaml`.

---

**[CHANGELOG.md](CHANGELOG.md)** · **[gingx-mnemo](gingx-mnemo/)** · **[gingx-sdd](gingx-sdd/)**

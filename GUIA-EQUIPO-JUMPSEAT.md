# JumpSeat Signal Intelligence
## Guía de uso para el equipo
**Versión 1.0 | Junio 2026**

---

# ¿Qué es este sistema?

JumpSeat Signal Intelligence es un pipeline de 10 skills de Claude que, dado el nombre y website de una empresa, produce en ~30 minutos:

- Score de ICP (Tier 1 / Tier 2 / No ICP)
- Matriz de señales con evidencia real y fuentes verificables
- Score de convicción (HIGH / MEDIUM / PENDING / LOW)
- Dossier de inteligencia de 4–5 páginas
- Mapa de contactos priorizado
- Email 1 (≤75 palabras) + Email 2 (≤35 palabras)
- CSV listo para importar al CRM

**La regla de oro del sistema:** solo confirmamos lo que tiene fuente real. Si no hay URL, filing, o fuente con nombre, no cuenta como señal confirmada.

---

# El pipeline en cascada

```
STAGE 0 — Una vez por cliente nuevo
  ↓ skill-icp-onboarding    → construye el client-profile.yaml
  ↓ skill-signal-hunter     → define la librería de señales (workshop)

STAGE 1 — Por cada cuenta a analizar
  ↓ skill-deep-research     → research-brief.json (datos reales con fuentes)
  ↓ skill-icp-fit-scoring   → Tier 1 / Tier 2 / No ICP

STAGE 2 — Solo Tier 1 y Tier 2
  ↓ skill-signal-research   → señales C / I / N / U
  ↓ skill-conviction-algorithm → score ponderado

STAGE 3 — Solo HIGH conviction
  ↓ skill-dossier-builder   → dossier de inteligencia
  ↓ skill-contact-mapping   → quién llamar y cómo
  ↓ skill-email-personalization → Email 1 + Email 2

STAGE 4 — Todas las cuentas calificadas
  ↓ skill-salesforce-export → CSV para CRM
```

> **Nunca saltear el ICP scoring.** El gate existe para no gastar horas de research en cuentas que no califican.

---

---

# OPCIÓN 1 — Con GitHub + Claude Code
### (Recomendada a mediano plazo — requiere instalación inicial)

Esta opción usa GitHub para tener siempre la versión más actualizada de las skills, y Claude Code para ejecutarlas desde la terminal.

**Ventajas:**
- Siempre tenés la última versión de las skills
- Podés contribuir mejoras al repo
- Las skills se invocan con un comando simple
- El historial de cambios queda registrado

---

## Paso 1 — Instalar las herramientas (una sola vez)

### 1A — Instalar Claude Code

Claude Code es la app de escritorio de Claude que permite correr skills como comandos.

1. Ir a **claude.ai/download** en el browser
2. Descargar la app para Mac (o Windows)
3. Instalar y abrir
4. Iniciar sesión con tu cuenta de Anthropic / Claude

> Si ya usás Claude en el browser, Claude Code usa la misma cuenta.

### 1B — Instalar Git

Git es el programa que permite bajar y actualizar el repo desde GitHub.

**En Mac:**
1. Abrir la app **Terminal** (la encontrás en Aplicaciones → Utilidades)
2. Pegar este comando y presionar Enter:
```bash
git --version
```
3. Si ya está instalado, vas a ver algo como `git version 2.39.0` — listo.
4. Si no está instalado, Mac te va a pedir instalarlo automáticamente. Aceptar.

**En Windows:**
1. Ir a **git-scm.com/download/win**
2. Descargar e instalar con todas las opciones por defecto

### 1C — Crear cuenta en GitHub (si no tenés)

1. Ir a **github.com**
2. Click en "Sign up"
3. Crear cuenta con tu email de trabajo
4. Enviá tu usuario de GitHub a Hareli para que te dé acceso al repo privado

---

## Paso 2 — Bajar el repo (una sola vez)

Una vez que Hareli te dio acceso al repo en GitHub:

1. Abrir **Terminal** (Mac) o **Git Bash** (Windows)
2. Navegar a la carpeta donde querés guardar el proyecto. Por ejemplo:
```bash
cd ~/Documents
```
3. Clonar el repo (reemplazá `TU-USUARIO` con el usuario de GitHub de Hareli o JumpSeat):
```bash
git clone https://github.com/TU-USUARIO/jumpseat-skills.git
```
4. Entrar a la carpeta:
```bash
cd jumpseat-skills
```
5. Verificar que bajó todo:
```bash
ls skills/base/
```
Deberías ver los 10 archivos de skills.

---

## Paso 3 — Correr una skill

**Desde Claude Code:**

1. Abrir Claude Code (la app de escritorio)
2. En el chat, escribir el nombre de la skill precedido por `/`:

```
/skill-jumpseat-kickoff
```

Claude lee el archivo de la skill y empieza a ejecutarla.

**Flujo típico para analizar una cuenta nueva:**

```
Paso 1: /skill-jumpseat-kickoff
         → Claude te pregunta en qué estado estás y te rutea

Paso 2: /skill-deep-research
         → Tenés que darle: nombre de empresa + website URL
         → Claude produce: research-brief.json

Paso 3: /skill-icp-fit-scoring
         → Tenés que darle: research-brief.json + nombre del cliente (ej: [nombre-cliente])
         → Claude produce: score Tier 1 / 2 / No ICP

Paso 4: /skill-signal-research
         → Solo si pasó el gate de ICP
         → Claude produce: matriz de señales

Paso 5: /skill-conviction-algorithm
         → Claude produce: score de convicción

Paso 6 (solo HIGH): /skill-dossier-builder → /skill-contact-mapping → /skill-email-personalization

Paso 7: /skill-salesforce-export
         → Claude produce: CSV para el CRM
```

---

## Paso 4 — Actualizar las skills cuando hay cambios

Cuando Hareli publique una nueva versión de una skill:

1. Abrir Terminal
2. Ir a la carpeta del repo:
```bash
cd ~/Documents/jumpseat-skills
```
3. Bajar los cambios:
```bash
git pull
```
Listo. Las skills se actualizan en segundos.

---

## Paso 5 — Guardar los outputs de cada cuenta

Cuando terminés el pipeline para una cuenta, guardá los archivos en la carpeta `skills/test/`:

```
skills/test/
  └── nombre-de-la-empresa/
        ├── research-brief.json
        ├── icp-fit-scoring.json
        ├── signal-research.json
        ├── conviction-output.json
        ├── dossier-nombre-empresa.md
        ├── contact-map.md
        ├── emails-nombre-empresa.md
        └── crm-export.csv
```

Si querés compartir los resultados con el equipo, subí los archivos al repo:
```bash
git add skills/test/nombre-de-la-empresa/
git commit -m "Add: pipeline run for Nombre Empresa"
git push
```

---

---

# OPCIÓN 2 — Manual (sin GitHub, sin instalación)
### (Para usar de inmediato, sin setup técnico)

Esta opción usa Claude directamente en el browser. No requiere instalar nada ni saber usar Git.

**Ventajas:**
- Cero instalación — funciona hoy
- Cualquier persona del equipo puede usarlo

**Desventajas:**
- Tenés que copiar y pegar el contenido de las skills manualmente
- No recibís actualizaciones automáticas — Hareli tiene que enviarte la skill actualizada

---

## Paso 1 — Obtener los archivos de skills

Pedile a Hareli que te comparta:
- La carpeta `skills/base/` completa (los 10 archivos `.md`)
- El `client-profile.yaml` del cliente que vas a usar (Hareli te lo envía por separado — no está en el repo)

Podés recibirlos por email, Google Drive, o cualquier medio de archivo compartido.

---

## Paso 2 — Crear un Proyecto en Claude (una vez por cliente)

Claude Projects permite subir archivos que Claude tiene en contexto permanente — no tenés que pegar nada cada vez.

1. Ir a **claude.ai** en el browser
2. Iniciar sesión
3. En el panel izquierdo, click en **"New Project"** (o "Nuevo Proyecto")
4. Nombrarlo: `JumpSeat × [Nombre del cliente]`
5. En la sección **"Project Knowledge"** (Conocimiento del proyecto):
   - Click en "Add content" o "Upload files"
   - Subir **todos** los archivos `.md` de `skills/base/`
   - Subir el `client-profile.yaml` del cliente

> Desde este momento, cada conversación dentro de ese Proyecto ya tiene las skills y el perfil del cliente disponibles. No necesitás pegar nada.

---

## Paso 3 — Correr una skill

Dentro del Proyecto de Claude, simplemente escribí en el chat:

```
Corré skill-deep-research para la empresa [nombre-empresa], website [website.com]
```

O más corto:

```
/skill-deep-research — empresa: [nombre-empresa] / [website.com]
```

Claude lee el skill desde el conocimiento del Proyecto y lo ejecuta.

**La misma secuencia de pasos aplica que en la Opción 1** — la diferencia es cómo invocás la skill (escribiendo el nombre en lugar de usar el comando de terminal).

---

## Paso 4 — Guardar los outputs

Al terminar el pipeline de una cuenta, copiá los outputs que genera Claude y guardalos como archivos en tu computadora o Google Drive.

Estructura recomendada:
```
JumpSeat/
  └── cuentas/
        └── [nombre-empresa]/
              ├── research-brief.json
              ├── icp-scoring.json
              ├── signals.json
              ├── conviction.json
              ├── dossier.md
              ├── contactos.md
              └── emails.md
```

---

---

# Referencia rápida — skills y cuándo usarlas

| Skill | Cuándo correrla | Input | Output |
|-------|----------------|-------|--------|
| `skill-jumpseat-kickoff` | Siempre primero si no sabés por dónde empezar | Nada | Te rutea al skill correcto |
| `skill-icp-onboarding` | Cliente nuevo — una vez | Website del cliente | `client-profile.yaml` |
| `skill-signal-hunter` | Después del onboarding | `client-profile.yaml` | Lista de 30-50 señales candidatas |
| `skill-deep-research` | Por cada cuenta nueva | Nombre + website de la empresa | `research-brief.json` |
| `skill-icp-fit-scoring` | Después del research brief | `research-brief.json` + `client-profile.yaml` | Tier 1 / 2 / No ICP |
| `skill-signal-research` | Solo Tier 1 y 2 | `research-brief.json` + señales del cliente | Matriz C/I/N/U |
| `skill-conviction-algorithm` | Después del signal research | Matriz de señales + pesos | Score + nivel HIGH/MEDIUM/PENDING/LOW |
| `skill-dossier-builder` | Solo HIGH conviction | Todos los JSON anteriores | Dossier 4-5 páginas |
| `skill-contact-mapping` | Después del dossier | Dossier + `client-profile.yaml` | Mapa de contactos priorizados |
| `skill-email-personalization` | Después del contact map | Dossier + mapa de contactos | Email 1 + Email 2 |
| `skill-salesforce-export` | Al final, siempre | Todos los outputs | CSV para CRM |

---

# Clasificación de señales

| Código | Significado | ¿Cuenta para el score? |
|--------|-------------|----------------------|
| **C** | Confirmed — hay fuente real con URL o nombre de documento | ✅ Sí |
| **I** | Inferred — deducción lógica del modelo de negocio | ❌ No |
| **N** | Not Found — se buscó, no se encontró | ❌ No |
| **U** | Unknown — no cubierto en el research | ❌ No |

# Niveles de convicción

| Nivel | Condición | Acción |
|-------|-----------|--------|
| **HIGH** | ≥7 puntos ponderados confirmados | Dossier inmediato. Outreach esta semana. |
| **MEDIUM** | 3–6 puntos confirmados | Investigar gaps. Cola del próximo sprint. |
| **PENDING** | Fit fuerte, 0–2 señales confirmadas | Monitorear. Definir trigger de reactivación. |
| **LOW** | Fit débil + 0–2 señales confirmadas | No priorizar. |

> **PENDING ≠ LOW.** PENDING es "buen fit, mal timing." Siempre definir qué evento reactiva la cuenta.

---

# Reglas del sistema

1. **Nunca saltear el ICP gate.** Signal research sin fit score = tiempo perdido en cuentas malas.
2. **Sin fuente = no Confirmed.** Si el dato no tiene URL o documento, es Inferred (I), no Confirmed (C).
3. **Las skills base no tienen datos de clientes.** Todo el contexto del cliente vive en `client-profile.yaml`.
4. **Un dossier por cuenta HIGH.** No construir dossier para MEDIUM o PENDING — investigar los gaps primero.
5. **El Email 1 no puede superar 75 palabras. El Email 2 no puede superar 35 palabras.** Si supera → reescribir.

---

# Contacto y soporte

Dudas sobre el sistema → **Hareli Ortubia** — hareliortubia@gmail.com  
Bugs o mejoras → abrir un Issue en el repo de GitHub  
Actualizaciones de skills → se publican en el repo, hacer `git pull` para bajarlas

---

*JumpSeat Signal Intelligence — Built by Hareli Ortubia | June 2026*

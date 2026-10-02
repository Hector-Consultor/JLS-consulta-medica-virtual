---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-05T00:00:00Z
version: 2.0
author: Claude Code PM System
---

# Progress — JLS Consulta Médica Virtual (ahora: Plataforma Multi-Especialista)

## Estado actual del proyecto

**Fase:** EN PAUSA — Cambio de scope. Esperando validacion del cliente.
**Branch activo:** `main`
**Fecha de pausa:** 2026-03-05

> CAMBIO DE SCOPE MAYOR (05/03/2026)
> El proyecto paso de sistema unipersonal del Dr. Salazar a plataforma de telemedicina multi-especialista que opera en Colombia, Venezuela y Argentina. El PRD v2.0 fue enviado al cliente para validacion. Todas las tareas previas quedan en pausa hasta recibir respuestas.

## Trabajo completado

### Documentacion base (v1 — modelo unipersonal)
- [x] PRD v1.0: `.claude/prds/JLS-consulta-medica-virtual.md`
- [x] Epic tecnico v1: `.claude/epics/JLS-consulta-medica-virtual/epic.md`
- [x] README.md profesional en la raiz del proyecto
- [x] 8 tareas tecnicas descompuestas del epic (issues #2-#9)

### GitHub
- [x] Repositorio: `Hector-Consultor/JLS-consulta-medica-virtual`
- [x] Epic issue #1 y tareas #2-#9 creados con labels

### Hito 05/03/2026 — Cambio de scope
- [x] PRD v2.0 elaborado con nuevo modelo multi-especialista
- [x] PRD v2.0 enviado al cliente para validacion

## Esperando respuestas del cliente

El cliente debe definir:
1. **Nombre comercial** de la plataforma
2. **Pasarela de pago** y modelo de split entre especialistas y plataforma
3. **Cantidad de especialistas** en el MVP
4. **Fases de lanzamiento por pais** (Colombia / Venezuela / Argentina)
5. **Modelo de distribucion** de pagos (porcentaje plataforma / especialista)

## Proximos pasos (cuando el cliente valide el PRD v2.0)

```
/pm:prd-edit        -> ajustar PRD con respuestas del cliente
/pm:prd-parse       -> parsear PRD actualizado
/pm:epic-decompose  -> regenerar epic con nuevo scope
/pm:epic-sync       -> sincronizar con GitHub Issues
```

## Herramientas reservadas para cuando haya código

Evaluadas en `claude-config` y reservadas para este proyecto. Ninguna está instalada todavía.
Ficha completa en `hg-knowledge-base/03-proyectos/claude-config/stack-inventory.md`.

| Herramienta | Cuándo | Cómo | Antes de usarla |
|---|---|---|---|
| **claude-code-setup** (plugin oficial de Anthropic) | Al arrancar el desarrollo | `/plugin install claude-code-setup@claude-plugins-official` y pedir "recommend automations for this project" | Nada: es de solo lectura |
| **Strix** (`usestrix/strix`, pentesting con IA) | Antes de lanzar a producción (datos de salud y pagos) | `npx skills add usestrix/strix` + CLI `strix`. Probar contra el Web App desplegado (black-box) y el código `.gs` (white-box) | Necesita Docker (no instalado) y una API key de LLM que se paga por token. Confirmar el techo de gasto y probar primero con un modelo gratuito de NVIDIA vía LiteLLM. Correrlo sobre un checkout limpio: monta la carpeta con permiso de escritura |
| **ECC** (`affaan-m/ECC`): `healthcare-reviewer` + `security-review` + `tdd-workflow` | Si se construye con código | Piezas sueltas, siempre en scope de proyecto, nunca el plugin entero | Ver la memoria `ecc-cosecha-selectiva` |

Decisiones del 2026-09-25 (ECC) y del 2026-09-30 (Strix y claude-code-setup).

## Estado de issues en GitHub (todos EN PAUSA)

| Issue | Titulo | Estado | Nota |
|---|---|---|---|
| #1 | Epic: JLS Consulta Medica Virtual | OPEN | Sera reemplazado por nuevo epic multi-especialista |
| #2 | Setup base del proyecto Apps Script | OPEN | En pausa |
| #3 | Disponibilidad y formulario de agendamiento | OPEN | En pausa |
| #4 | Integracion con pasarela de pago | OPEN | En pausa |
| #5 | Flujo de cancelacion con validacion 24h | OPEN | En pausa |
| #6 | Recordatorios automaticos 24h antes | OPEN | En pausa |
| #7 | Landing page del Dr. Salazar | OPEN | En pausa (scope cambia radicalmente) |
| #8 | Template de informe post-consulta | OPEN | En pausa |
| #9 | QA, testing end-to-end y despliegue | OPEN | En pausa |

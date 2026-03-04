---
name: JLS-consulta-medica-virtual
status: backlog
created: 2026-03-04T20:09:29Z
progress: 0%
prd: .claude/prds/JLS-consulta-medica-virtual.md
github: [Will be updated when synced to GitHub]
---

# Epic: JLS Consulta Médica Virtual

## Overview

Implementar un sistema de consulta médica virtual autogestionado para el Dr. Juan Luis Salazar. El backend es 100% Google Apps Script actuando como API y orquestador; el frontend es una página web (plataforma a confirmar) que embebe o llama al Web App de Apps Script.

**Decisión arquitectónica clave de simplificación:** Apps Script puede servir directamente las páginas de agendamiento y cancelación como Web App (HTML Service), eliminando la dependencia del frontend externo para la lógica crítica. La página web externa (Sites/Wix/Carrd) solo necesita un botón que lleve al usuario al Web App de Apps Script.

---

## Architecture Decisions

| Decisión | Elección | Justificación |
|---|---|---|
| Backend / orquestador | Google Apps Script (único proyecto) | Ya disponible, sin costo adicional, integración nativa con todo el stack |
| UI de agendamiento y cancelación | Apps Script HTML Service (Web App) | Elimina dependencia del frontend externo para lógica transaccional; cualquier plataforma web puede simplemente enlazarlo |
| Base de datos | Google Sheets (una hoja maestra) | Sin costo, accesible por el médico, suficiente para volumen esperado (≤100 citas/mes) |
| Disponibilidad | Google Calendar API (leer eventos existentes) | El médico configura su disponibilidad directamente en Calendar; no hay UI adicional que mantener |
| Links de videollamada | Google Calendar con conferenceData (Meet) | Meet link se genera automáticamente al crear el evento de Calendar |
| Tokens de cancelación | `Utilities.getUuid()` de Apps Script | UUID v4 nativo, sin dependencias externas, suficientemente seguro |
| Emails transaccionales | `GmailApp.sendEmail()` | Nativo en Apps Script, enviado desde la cuenta del médico, sin costo |
| Recordatorios 24h | Apps Script time-based trigger (hourly) | Nativo, sin costo, sin servidor externo |
| Pagos | Pasarela externa (TBD: Wompi recomendado) | Apps Script recibe webhook de confirmación vía `doPost` |
| Detección de cancelación médico | Apps Script `onEventUpdated` trigger de Calendar | Evita panel admin adicional; el médico cancela desde Calendar como siempre |

---

## Technical Approach

### Estructura del proyecto Apps Script

```
JLS-Consulta-Medica/          ← Proyecto Apps Script único
├── Code.gs                   ← Router principal (doGet / doPost)
├── Agenda.gs                 ← Lógica de agendamiento y disponibilidad
├── Pagos.gs                  ← Webhook de pasarela + confirmación post-pago
├── Cancelaciones.gs          ← Cancelación paciente + detección cancelación médico
├── Recordatorios.gs          ← Time-based trigger, envío de recordatorios 24h
├── Emails.gs                 ← Templates de todos los emails transaccionales
├── SheetsService.gs          ← CRUD sobre Google Sheets (única fuente de verdad)
├── CalendarService.gs        ← Lectura de disponibilidad + creación de eventos Meet
├── pages/
│   ├── booking.html          ← Formulario de agendamiento (HTML Service)
│   └── cancelacion.html      ← Página de cancelación con validación 24h
└── Config.gs                 ← Variables de configuración (IDs, emails, políticas)
```

### Google Sheets — estructura de la hoja maestra

**Hoja: `consultas`**
| Columna | Descripción |
|---|---|
| id_turno | UUID generado al registrar |
| nombre | Nombre completo del paciente |
| email | Email del paciente |
| telefono | Teléfono |
| pais | Colombia / Venezuela / Otro |
| motivo | Motivo de consulta (texto libre) |
| fecha_hora | ISO 8601 UTC-5 |
| estado | pendiente_pago / confirmado / cancelado_paciente / cancelado_medico / realizado |
| id_evento_calendar | ID del evento en Google Calendar |
| link_meet | URL de Google Meet |
| token_cancelacion | UUID único para el link de cancelación |
| cancelado_por | paciente / medico / N/A |
| reembolso_solicitado | si / no / N/A |
| recordatorio_enviado | si / no + timestamp |
| fecha_registro | Timestamp de creación del registro |

### Flujo técnico por endpoint

**`GET /?page=agenda`** → Renderiza `booking.html` con slots disponibles del Calendar
**`POST /agenda`** → Registra en Sheets (estado: pendiente_pago) → redirige a pasarela
**`POST /webhook-pago`** → Valida firma pasarela → actualiza Sheets → crea evento Calendar con Meet → envía email confirmación
**`GET /?page=cancelar&token=UUID`** → Renderiza `cancelacion.html`, valida token + 24h
**`POST /cancelar`** → Valida token + 24h → actualiza Sheets + Calendar → envía emails
**Trigger: `recordatoriosHorario()`** → Cada hora, busca citas en ventana 24-25h → envía recordatorios
**Trigger: `onCalendarEventUpdated()`** → Detecta cancelaciones del médico en Calendar → notifica paciente

### Integración con pasarela de pago

Apps Script expone un endpoint `doPost` que recibe el webhook de confirmación de pago. La pasarela seleccionada (Wompi/PayU) debe:
1. Soportar webhook POST a URL del Web App de Apps Script
2. Incluir el `id_turno` como referencia de la transacción (pasado como parámetro en la URL de retorno)
3. Enviar firma de verificación para validar autenticidad del webhook

---

## Implementation Strategy

### Prerrequisitos (antes de comenzar el desarrollo)
- [ ] Cliente confirma pasarela de pago (bloqueante)
- [ ] Cliente confirma plataforma web (Sites / Wix / Carrd / Netlify)
- [ ] Cliente define tarifa de consulta en COP y/o USD
- [ ] Cliente tiene cuenta Google Workspace configurada (Calendar, Sheets, Gmail)
- [ ] Redacción de Términos y Condiciones + Política de Privacidad (responsabilidad del cliente)

### Estrategia de testing
- Cada Apps Script function tiene su propia función de test inline ejecutable desde el editor
- Testing end-to-end con citas de prueba antes del lanzamiento
- Verificar cuota de emails de la cuenta (100/día gratuita vs 1500/día Workspace)
- Probar webhook de pago en modo sandbox de la pasarela seleccionada

### Gestión del riesgo
| Riesgo | Mitigación |
|---|---|
| Webhook de pasarela no llega (falla de red) | Agregar endpoint de polling manual como fallback; el médico puede confirmar manualmente desde Sheets |
| Trigger de Calendar no detecta cancelación | Documentar proceso manual de backup; el médico puede ejecutar script manualmente |
| Cuota de emails agotada | Alertar si se acerca al límite; considerar upgrade a Workspace si el volumen crece |
| Tiempo de ejecución Apps Script > 6 min | Cada función es atómica y no supera 30s; sin riesgo en flujo normal |

---

## Task Breakdown Preview

- [ ] **TASK-1: Setup base** — Crear proyecto Apps Script, estructura de Sheets, Config.gs, permisos OAuth
- [ ] **TASK-2: Disponibilidad y formulario de agendamiento** — CalendarService.gs, booking.html, SheetsService.gs (registro inicial)
- [ ] **TASK-3: Integración pasarela de pago** — Pagos.gs, webhook doPost, creación evento Calendar con Meet, email de confirmación
- [ ] **TASK-4: Flujo de cancelación** — Cancelaciones.gs, cancelacion.html, validación 24h, distinción paciente/médico, emails
- [ ] **TASK-5: Recordatorios automáticos** — Recordatorios.gs, time-based trigger horario, emails 24h antes
- [ ] **TASK-6: Página web (landing)** — Configurar plataforma elegida, contenido, CTA que enlaza al Web App de agendamiento
- [ ] **TASK-7: Template informe post-consulta** — Google Docs template, carpeta Drive estructurada, instrucciones de uso
- [ ] **TASK-8: QA y despliegue** — Testing end-to-end, sandbox de pagos, documentación operativa para el médico

---

## Dependencies

| Dependencia | Estado | Bloqueante para |
|---|---|---|
| Pasarela de pago seleccionada | **Pendiente** — decisión del cliente | TASK-3 |
| Plataforma web seleccionada | **Pendiente** — decisión del cliente | TASK-6 |
| Tarifa de consulta definida | **Pendiente** — decisión del cliente | TASK-2, TASK-3 |
| Cuenta Google Workspace del médico | Asumida como disponible | TASK-1 |
| Términos y Condiciones redactados | **Pendiente** — responsabilidad del cliente | TASK-6 |
| Credenciales API de la pasarela (sandbox + prod) | **Pendiente** — tras selección de pasarela | TASK-3 |

---

## Success Criteria (Technical)

| Criterio | Cómo se verifica |
|---|---|
| Email de confirmación en < 2 min post-pago | Test cronometrado con pago sandbox |
| Cancelación bloqueada si < 24h de anticipación | Test con cita próxima creada manualmente |
| Recordatorio enviado exactamente en ventana 24-25h | Verificar con cita de prueba + logs de Sheets |
| Token de cancelación es único e ireutilizable | Verificar UUID en Sheets + test de reuso (debe fallar) |
| Slots reservados no aparecen como disponibles | Test creando cita y verificando que el slot desaparece del calendario |
| Médico no necesita intervención manual en flujo normal | Test completo sin acciones del médico |
| 0 errores en Apps Script logs en flujo crítico | Revisar Stackdriver Logging post-test |

## Estimated Effort

| Tarea | Estimación relativa |
|---|---|
| TASK-1: Setup base | S (pequeño) |
| TASK-2: Disponibilidad y formulario | M (mediano) |
| TASK-3: Integración pasarela | L (grande — depende de pasarela) |
| TASK-4: Flujo de cancelación | M (mediano) |
| TASK-5: Recordatorios | S (pequeño) |
| TASK-6: Página web landing | M (mediano — depende de plataforma) |
| TASK-7: Template informe | S (pequeño) |
| TASK-8: QA y despliegue | M (mediano) |

**Critical path:** TASK-1 → TASK-2 → TASK-3 → TASK-4 → TASK-5 → TASK-8
TASK-6 y TASK-7 pueden ejecutarse en paralelo una vez confirmadas las decisiones pendientes.

**Nota:** El mayor factor de variabilidad es la integración con la pasarela de pago. Wompi (Colombia) tiene documentación clara y sandbox funcional; se recomienda priorizar su selección para desbloquear el desarrollo.

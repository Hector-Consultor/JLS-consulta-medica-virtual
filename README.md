# JLS Consulta Médica Virtual

![Estado](https://img.shields.io/badge/estado-en%20desarrollo-yellow)
![Stack](https://img.shields.io/badge/stack-Google%20Workspace-blue)
![Mercado](https://img.shields.io/badge/mercado-Colombia%20%7C%20Venezuela-green)

Sistema de consulta médica virtual autogestionado para el **Dr. Juan Luis Salazar, Médico Internista Especialista**. Permite a pacientes agendar, pagar y gestionar citas de forma completamente autónoma, sin intermediarios ni staff administrativo.

---

## Problema que resuelve

El Dr. Salazar no dispone de consultorio físico, lo que imposibilita el modelo de atención presencial. Sin este sistema, la gestión de turnos se realiza de forma manual (WhatsApp, email), generando:

- Pérdida de tiempo del médico en coordinación administrativa
- Falta de visibilidad de disponibilidad para el paciente
- Ausencia de cobro previo formal
- Confirmaciones y links de videollamada enviados manualmente
- Sin proceso estandarizado para el informe post-consulta

---

## Stack tecnológico

| Componente | Tecnología |
|---|---|
| Orquestador / Backend | Google Apps Script |
| Base de datos | Google Sheets |
| Disponibilidad y agenda | Google Calendar |
| Videollamada | Google Meet (generado via Calendar API) |
| Emails transaccionales | Gmail via Apps Script (`GmailApp`) |
| UI de agendamiento y cancelación | Apps Script HTML Service (Web App) |
| Landing page | A definir: Google Sites / Wix / Carrd / Netlify |
| Pasarela de pago | **A definir** (candidatos: Wompi, PayU, MercadoPago) |

> El backend es 100% Google Workspace + Apps Script. Sin servidores externos, sin bases de datos adicionales, sin costo de infraestructura.

---

## Arquitectura general

```
[Paciente]
    │
    ▼
[Landing page] ──► [Apps Script Web App]
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
        Google        Google       Gmail
        Calendar      Sheets    (emails auto)
       (agenda)     (registro)
              │
              ▼
        [Pasarela de Pago]
              │  webhook POST
              ▼
        Apps Script
              │
    ┌─────────┼──────────┐
    ▼         ▼          ▼
  Evento    Email      Google
  Meet    confirmación  Drive
(Calendar)  (Gmail)   (informes)
```

**Apps Script** actúa como único orquestador: expone endpoints `doGet`/`doPost` para el formulario de agendamiento, el webhook de pago, y la página de cancelación. Los triggers nativos manejan los recordatorios automáticos y la detección de cancelaciones del médico.

---

## Flujo del paciente

```
1. AGENDA
   Paciente accede a la landing page
   → selecciona fecha y hora disponible (Google Calendar)
   → completa formulario (nombre, email, teléfono, país, motivo)
   → acepta política de no reembolso (checkbox obligatorio)

2. PAGA
   → redirigido a pasarela de pago (COP o USD)
   → pago aprobado → webhook notifica a Apps Script

3. CONFIRMACIÓN AUTOMÁTICA (< 2 minutos)
   → slot bloqueado en Google Calendar
   → evento con link de Google Meet generado
   → email automático al paciente con: fecha/hora, link Meet,
     instrucciones, link de cancelación único

4. RECORDATORIO AUTOMÁTICO
   → 24 horas antes: email al paciente y al médico
   → el email avisa que ya no es posible cancelar ni modificar

5. CONSULTA
   → ambos acceden al link de Google Meet a la hora pactada
   → el médico conduce la consulta

6. INFORME POST-CONSULTA
   → el médico completa el template de Google Docs
   → envía el informe de recomendaciones al email del paciente
   → copia guardada en Google Drive
```

---

## Políticas

### Cancelación
- El paciente puede cancelar su cita de forma autónoma usando el link único recibido por email.
- **No hay reembolso** si cancela el paciente (política visible y aceptada antes del pago).
- Solo se puede cancelar con **mínimo 24 horas de anticipación**. Con menos de 24 horas, la cita queda bloqueada.
- Si **el médico cancela**, el paciente recibe un email con dos opciones: reprogramar o solicitar reembolso.

### Modificación
- Las citas solo pueden modificarse (reprogramarse) con **mínimo 24 horas de anticipación**.
- No existe reprogramación autónoma en el MVP; el paciente debe cancelar y agendar una nueva cita.

---

## Mercado objetivo

| País | Regulación aplicable |
|---|---|
| 🇨🇴 Colombia | Resolución 2654 de 2019 (telemedicina) · Ley 1581 de 2012 (datos personales) |
| 🇻🇪 Venezuela | Ley del Ejercicio de la Medicina · Marco MPPS (validación pendiente) |

El médico cuenta con **licencia médica vigente en ambos países**. La plataforma actúa como canal de coordinación; el acto médico es responsabilidad exclusiva del Dr. Salazar.

---

## Estructura del proyecto Apps Script

```
JLS-Consulta-Medica/
├── Code.gs              ← Router principal (doGet / doPost)
├── Agenda.gs            ← Disponibilidad, registro de citas
├── Pagos.gs             ← Webhook de pasarela, confirmación post-pago
├── Cancelaciones.gs     ← Cancelación paciente + detección cancelación médico
├── Recordatorios.gs     ← Trigger horario, envío de recordatorios 24h
├── Emails.gs            ← Templates de todos los emails transaccionales
├── SheetsService.gs     ← CRUD sobre Google Sheets
├── CalendarService.gs   ← Lectura de disponibilidad + creación eventos con Meet
├── Config.gs            ← IDs, emails, parámetros de configuración
└── pages/
    ├── booking.html     ← Formulario de agendamiento (HTML Service)
    └── cancelacion.html ← Página de cancelación con validación 24h
```

### Hoja de cálculo (Google Sheets)

| Campo | Descripción |
|---|---|
| `id_turno` | UUID único por cita |
| `nombre`, `email`, `telefono`, `pais` | Datos del paciente |
| `motivo` | Motivo de consulta |
| `fecha_hora` | ISO 8601 UTC-5 |
| `estado` | pendiente_pago / confirmado / cancelado_paciente / cancelado_medico / realizado |
| `id_evento_calendar` | Referencia al evento en Google Calendar |
| `link_meet` | URL de Google Meet |
| `token_cancelacion` | UUID para el link de cancelación único |
| `cancelado_por` | paciente / medico / N/A |
| `reembolso_solicitado` | si / no / N/A |
| `recordatorio_enviado` | si / no + timestamp |
| `fecha_registro` | Timestamp de creación |

---

## Requisitos previos para implementar

- [ ] Cuenta **Google Workspace** activa (del Dr. Salazar)
- [ ] **Pasarela de pago** seleccionada y cuenta creada *(bloqueante)*
- [ ] **Plataforma web** seleccionada para la landing page *(bloqueante)*
- [ ] **Tarifa de consulta** definida en COP y/o USD *(bloqueante)*
- [ ] Dominio personalizado configurado (opcional pero recomendado)
- [ ] **Términos y Condiciones** redactados (responsabilidad del cliente)
- [ ] **Política de Privacidad** redactada (Habeas Data Colombia)
- [ ] Slots de disponibilidad cargados en Google Calendar

---

## Decisiones pendientes del cliente

| # | Decisión | Impacto | Estado |
|---|---|---|---|
| 1 | Pasarela de pago (Wompi recomendado) | Bloquea desarrollo del módulo de pagos | ⏳ Pendiente |
| 2 | Plataforma web (Sites / Wix / Carrd / Netlify) | Bloquea desarrollo de la landing | ⏳ Pendiente |
| 3 | Tarifa de consulta en COP y/o USD | Bloquea formulario y pasarela | ⏳ Pendiente |
| 4 | Dominio personalizado | Afecta URL del sistema | ⏳ Pendiente |
| 5 | Cuenta Workspace pago o gratuita | Afecta límite de emails/día | ⏳ Pendiente |

---

## Roadmap — MVP

| # | Tarea | Estado |
|---|---|---|
| TASK-1 | Setup base: Apps Script, Sheets, Config, permisos OAuth | ⏳ Pendiente |
| TASK-2 | Disponibilidad y formulario de agendamiento | ⏳ Pendiente |
| TASK-3 | Integración con pasarela de pago | 🔒 Bloqueada (decisión pasarela) |
| TASK-4 | Flujo de cancelación (24h, sin reembolso, detección médico) | ⏳ Pendiente |
| TASK-5 | Recordatorios automáticos 24h antes | ⏳ Pendiente |
| TASK-6 | Landing page (plataforma a definir) | 🔒 Bloqueada (decisión plataforma) |
| TASK-7 | Template informe post-consulta (Google Docs + Drive) | ⏳ Pendiente |
| TASK-8 | QA, testing end-to-end y despliegue | ⏳ Pendiente |

**Critical path:** TASK-1 → TASK-2 → TASK-3 → TASK-4 → TASK-5 → TASK-8
TASK-6 y TASK-7 se ejecutan en paralelo.

---

## Documentación técnica

- PRD completo: [`.claude/prds/JLS-consulta-medica-virtual.md`](.claude/prds/JLS-consulta-medica-virtual.md)
- Epic técnico: [`.claude/epics/JLS-consulta-medica-virtual/epic.md`](.claude/epics/JLS-consulta-medica-virtual/epic.md)

---

## Desarrollado por

**Héctor González**
Líder de Automatización Inteligente · Low-code / No-code · IA Aplicada
📧 hgonzalez.contador@gmail.com
🌐 [hgautomation.netlify.app](https://hgautomation.netlify.app)

---

*Proyecto en desarrollo activo — 2026*

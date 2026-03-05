---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Project Overview — JLS Consulta Médica Virtual

## Resumen ejecutivo

JLS Consulta Médica Virtual es una plataforma web de telemedicina autogestionada construida sobre Google Workspace + Apps Script. Permite al Dr. Juan Luis Salazar operar una práctica médica virtual sin consultorio físico ni personal administrativo, atendiendo pacientes de Colombia y Venezuela mediante videollamada.

## Capacidades actuales (documentación completada, desarrollo pendiente)

### Sistema de agendamiento
- Visualización de disponibilidad real (integrada con Google Calendar del médico)
- Formulario de registro de pacientes con validaciones
- Política de no reembolso presentada y aceptada antes del pago
- Registro en Google Sheets como base de datos central

### Sistema de pagos
- Integración con pasarela de pago (TBD: Wompi recomendado)
- Soporte para COP y USD
- Manejo de estados: pendiente / aprobado / rechazado
- Confirmación solo tras pago aprobado

### Sistema de confirmación automática
- Email al paciente (< 2 min post-pago): fecha/hora, link de Google Meet, link de cancelación
- Email al médico: nueva cita confirmada con datos del paciente
- Evento creado automáticamente en Google Calendar del médico con Meet link

### Sistema de recordatorios
- Trigger horario que detecta citas en ventana 24-25h
- Email de recordatorio al paciente con link Meet y aviso de no cancelación
- Email de recordatorio al médico con datos del paciente

### Sistema de cancelación
- Link único e irrepetible por cita (UUID)
- Validación server-side: solo permite cancelar con ≥ 24 horas de anticipación
- Sin reembolso si cancela el paciente (política visible y aceptada al pagar)
- Si cancela el médico: email al paciente con opción de reembolso o reprogramación
- Diferenciación en Sheets: cancelado_paciente vs cancelado_medico

### Sistema de informes post-consulta
- Template estandarizado de Google Docs
- Campos del paciente pre-llenados desde Sheets
- Carpeta de archivo organizada por año/mes en Google Drive
- Envío manual del informe por email al paciente (MVP)

## Integraciones

| Integración | Tipo | Estado |
|---|---|---|
| Google Calendar | Nativa (Apps Script) | A implementar |
| Google Sheets | Nativa (Apps Script) | A implementar |
| Gmail | Nativa (Apps Script) | A implementar |
| Google Meet | Via Calendar API | A implementar |
| Google Drive | Nativa (Apps Script) | A implementar |
| Google Docs | Nativa (Apps Script) | A implementar |
| Pasarela de pago | Webhook HTTP POST | TBD (Wompi) |
| Landing page | Link externo a Web App | TBD (plataforma) |

## Estado del proyecto por componente

| Componente | Documentación | Desarrollo |
|---|---|---|
| PRD | ✅ Completo | — |
| Epic técnico | ✅ Completo | — |
| GitHub Issues | ✅ Creados (#1-#9) | — |
| README.md | ✅ Completo | — |
| Apps Script - Setup base | 📋 Especificado (issue #2) | ⏳ Pendiente |
| Apps Script - Agendamiento | 📋 Especificado (issue #3) | ⏳ Pendiente |
| Apps Script - Pagos | 📋 Especificado (issue #4) | 🔒 Bloqueado |
| Apps Script - Cancelaciones | 📋 Especificado (issue #5) | ⏳ Pendiente |
| Apps Script - Recordatorios | 📋 Especificado (issue #6) | ⏳ Pendiente |
| Landing page | 📋 Especificado (issue #7) | 🔒 Bloqueado |
| Template informe | 📋 Especificado (issue #8) | ⏳ Pendiente |
| QA y despliegue | 📋 Especificado (issue #9) | 🔒 Bloqueado |

## Repositorio GitHub

- **URL:** https://github.com/Hector-Consultor/JLS-consulta-medica-virtual
- **Epic issue:** https://github.com/Hector-Consultor/JLS-consulta-medica-virtual/issues/1
- **Branch activo:** `main`
- **Branch de desarrollo:** `epic/JLS-consulta-medica-virtual`

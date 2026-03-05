---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-05T00:00:00Z
version: 2.0
author: Claude Code PM System
---

# Project Overview — Plataforma de Telemedicina Multi-Especialista

> CAMBIO DE SCOPE (05/03/2026): El proyecto evoluciono de sistema unipersonal (Dr. Salazar) a plataforma de telemedicina multi-especialista operando en Colombia, Venezuela y Argentina.

## Resumen ejecutivo

Plataforma web de telemedicina especializada construida sobre Google Workspace + Apps Script. Permite a multiples especialistas medicos ofrecer consultas virtuales a pacientes de Colombia, Venezuela y Argentina, con gestion automatizada de agenda, pagos con split automatico, confirmaciones y recordatorios.

## Modelo de negocio

- **Antes (v1):** Sistema unipersonal para el Dr. Salazar — $0 comision de plataforma
- **Ahora (v2):** Empresa/plataforma que conecta especialistas con pacientes — split de pagos entre especialista y plataforma (modelo TBD por el cliente)

## Roles del sistema

| Rol | Descripcion |
|---|---|
| Paciente | Agenda, paga y gestiona citas con cualquier especialista |
| Especialista | Ofrece disponibilidad, realiza consultas, emite informes |
| Administrador | Gestiona la plataforma, especialistas y configuracion |

## Paises y normativa

| Pais | Marco legal |
|---|---|
| Colombia | Ley 1419/2010 (telemedicina), Ley 1581/2012 (datos personales) |
| Venezuela | Ley del Ejercicio de la Medicina |
| Argentina | Ley 27.553/2020 (teleconsulta medica) |

## Capacidades del sistema (v2 — documentadas, desarrollo pendiente)

### Multi-especialista
- Multiples calendarios (uno por especialista)
- Perfil publico por especialista (especialidad, bio, tarifa, disponibilidad)
- Dashboard del especialista para ver su agenda

### Agendamiento
- Visualizacion de disponibilidad por especialista (integrada con Google Calendar)
- Formulario de registro de pacientes con validaciones
- Politica de no reembolso presentada antes del pago

### Pagos
- Integracion con pasarela (TBD: Wompi u otra)
- Soporte para COP y USD
- Split automatico de pagos entre especialista y plataforma
- Estados: pendiente / aprobado / rechazado

### Confirmaciones y recordatorios
- Email al paciente post-pago con link Meet y link de cancelacion
- Email al especialista con datos del paciente
- Recordatorio 24h antes a paciente y especialista
- Evento creado en Google Calendar del especialista con Meet link

### Cancelacion
- Link unico por cita (UUID)
- Cancelacion por paciente: solo con 24h+ de anticipacion, sin reembolso
- Cancelacion por especialista: notificacion al paciente con opciones de reembolso/reprogramacion

### Informes post-consulta
- Template estandarizado de Google Docs por especialidad
- Campos del paciente pre-llenados desde Sheets
- Carpeta de archivo organizada en Google Drive

## Integraciones

| Integracion | Tipo | Estado |
|---|---|---|
| Google Calendar | Nativa (Apps Script) | A implementar |
| Google Sheets | Nativa (Apps Script) | A implementar |
| Gmail | Nativa (Apps Script) | A implementar |
| Google Meet | Via Calendar API | A implementar |
| Google Drive | Nativa (Apps Script) | A implementar |
| Google Docs | Nativa (Apps Script) | A implementar |
| Pasarela de pago | Webhook HTTP POST + split | TBD |
| Landing page / plataforma web | Link externo a Web App | TBD |

## Estado del proyecto por componente

| Componente | Estado |
|---|---|
| PRD v1.0 (modelo unipersonal) | Completo — reemplazado por v2.0 |
| PRD v2.0 (multi-especialista) | Enviado al cliente para validacion (05/03/2026) |
| Epic v1 (issues #1-#9) | En pausa — seran reemplazados |
| README.md | Completo (desactualizado — refleja v1) |
| Desarrollo | No iniciado |

## Repositorio GitHub

- **URL:** https://github.com/Hector-Consultor/JLS-consulta-medica-virtual
- **Branch activo:** `main`
- **Issues actuales:** #1-#9 (en pausa, pendientes de redefinicion)

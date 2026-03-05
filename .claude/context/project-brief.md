---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Project Brief — JLS Consulta Médica Virtual

## ¿Qué es?

Sistema de consulta médica virtual autogestionado para el Dr. Juan Luis Salazar, Médico Internista Especialista con práctica privada unipersonal. Permite a pacientes de Colombia y Venezuela agendar, pagar y gestionar citas médicas por videollamada sin intermediarios.

## ¿Por qué existe?

El Dr. Salazar no dispone de consultorio físico ni staff administrativo. La gestión manual de turnos (WhatsApp, email) era ineficiente y no escalable. Se necesitaba un sistema que:

1. Funcionara 24/7 sin intervención del médico para confirmar turnos
2. Garantizara el cobro previo a la consulta
3. Integrara automáticamente la videollamada (Google Meet)
4. Aprovechara la infraestructura Google Workspace ya disponible (costo = $0)

## Alcance del MVP

### Incluido
- Página web pública con información del médico y CTA de agendamiento
- Calendario de disponibilidad en tiempo real (integrado con Google Calendar)
- Formulario de agendamiento con validaciones
- Aceptación obligatoria de política de no reembolso
- Pago online (COP/USD via pasarela a definir)
- Confirmación automática por email con link de Google Meet único
- Recordatorio automático 24h antes (paciente y médico)
- Cancelación autónoma por paciente (≥ 24h, sin reembolso)
- Cancelación por médico con oferta de reembolso/reprogramación
- Restricción: sin cancelación ni modificación con < 24h de anticipación
- Registro de citas en Google Sheets
- Template de informe de recomendaciones post-consulta (Google Docs)

### Excluido explícitamente
- Recetas digitales o prescripciones
- Historia clínica electrónica
- Portal del paciente (login, historial)
- App móvil nativa
- Múltiples médicos
- Integración con seguros médicos
- Facturación electrónica
- Recordatorios SMS

## Stack tecnológico

- **Backend:** Google Apps Script (único orquestador)
- **Base de datos:** Google Sheets
- **Agenda:** Google Calendar
- **Videollamada:** Google Meet (via Calendar API)
- **Emails:** Gmail via Apps Script
- **Documentos:** Google Docs + Google Drive
- **Frontend:** Plataforma web TBD + Apps Script HTML Service
- **Pagos:** Pasarela TBD (Wompi recomendado)

## Mercado objetivo

- 🇨🇴 Colombia — Resolución 2654/2019 (telemedicina), Ley 1581/2012 (datos personales)
- 🇻🇪 Venezuela — Ley del Ejercicio de la Medicina (validación pendiente con Colegio Médico)

## Criterios de éxito

| Criterio | Definición de éxito |
|---|---|
| Operativo | El médico no interviene manualmente en ningún turno confirmado |
| Técnico | 0 errores en flujo crítico (agendamiento + confirmación) |
| Negocio | ≥ 10 consultas pagadas en el primer mes |
| Calidad | Email de confirmación en < 2 minutos post-pago |

## Dependencias críticas (bloqueantes)

1. **Pasarela de pago seleccionada** — sin esto no se puede desarrollar el módulo de pagos
2. **Plataforma web seleccionada** — sin esto no se puede desarrollar la landing page
3. **Tarifa de consulta definida** — sin esto no se puede configurar la pasarela

## Stakeholders

| Rol | Persona | Responsabilidad |
|---|---|---|
| Cliente / Product Owner | Dr. Juan Luis Salazar | Decisiones de negocio, validación, operación |
| Desarrollador | Héctor González | Implementación, arquitectura |
| Usuarios finales | Pacientes Colombia / Venezuela | Uso del sistema |

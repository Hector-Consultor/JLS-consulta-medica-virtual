---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Product Context — JLS Consulta Médica Virtual

## Descripción del producto

Plataforma de consulta médica virtual completamente autogestionada para el Dr. Juan Luis Salazar (Médico Internista Especialista). Permite a pacientes de Colombia y Venezuela agendar, pagar y gestionar citas médicas virtuales sin intervención de staff administrativo.

## Usuarios objetivo

### Paciente Nuevo — Valentina Ríos (arquetipo)
- **Perfil:** 38 años, Bogotá, Colombia. Profesional con seguro médico privado.
- **Necesidad:** Segunda opinión sobre un diagnóstico médico reciente.
- **Comportamiento:** Prefiere gestionar todo online, sin llamadas. Busca médicos de confianza.
- **Pain point:** No encuentra internistas disponibles rápidamente para segunda opinión.
- **Criterio de éxito:** Agendar, pagar y recibir confirmación en menos de 5 minutos.

### Paciente Recurrente — Carlos Medina (arquetipo)
- **Perfil:** 55 años, Caracas, Venezuela. Paciente crónico (hipertensión, dislipemia).
- **Necesidad:** Seguimiento periódico con el médico de confianza.
- **Comportamiento:** Agenda consultas regulares, valora la continuidad del cuidado.
- **Pain point:** Acceso a especialistas de confianza desde Venezuela es muy limitado.
- **Criterio de éxito:** Proceso de re-agendamiento rápido sin necesidad de contacto directo.

### Médico — Dr. Juan Luis Salazar
- **Perfil:** Médico Internista Especialista. Práctica privada unipersonal. Sin staff.
- **Necesidad:** Sistema que gestione la agenda y cobros de forma autónoma.
- **Comportamiento:** Usa Google Workspace diariamente. No quiere aprender nuevas herramientas.
- **Pain point:** Tiempo perdido en coordinación manual de turnos via WhatsApp/email.
- **Criterio de éxito:** "No necesito hacer nada manualmente para confirmar un turno."

## Funcionalidades core (MVP)

| # | Funcionalidad | Actor | Prioridad |
|---|---|---|---|
| 1 | Ver disponibilidad en tiempo real | Paciente | Alta |
| 2 | Agendar cita con formulario | Paciente | Alta |
| 3 | Aceptar política de no reembolso antes de pagar | Paciente | Alta |
| 4 | Pagar online (COP/USD) | Paciente | Alta |
| 5 | Recibir email de confirmación automático con link Meet | Paciente | Alta |
| 6 | Cancelar cita (≥24h anticipación, sin reembolso) | Paciente | Alta |
| 7 | Recibir recordatorio 24h antes | Paciente + Médico | Alta |
| 8 | Realizar videollamada via Google Meet | Paciente + Médico | Alta |
| 9 | Recibir informe de recomendaciones post-consulta | Paciente | Alta |
| 10 | Ver agenda del día en Google Sheets | Médico | Media |
| 11 | Cancelar cita con oferta de reembolso o reprogramación | Médico | Media |
| 12 | Crear informe con template de Google Docs | Médico | Media |

## Casos de uso principales

### CU-01: Agendamiento completo
```
Pre: Paciente accede a la landing page
1. Ve disponibilidad (integrada con Google Calendar)
2. Selecciona slot
3. Completa formulario (nombre, email, teléfono, país, motivo)
4. Acepta política de no reembolso (checkbox obligatorio)
5. Paga online
6. Recibe email de confirmación con link de Meet y link de cancelación
Post: Cita registrada en Sheets y Calendar
```

### CU-02: Cancelación por paciente
```
Pre: Paciente tiene email de confirmación con link único
     Faltan ≥ 24 horas para la cita
1. Accede al link de cancelación
2. Ve datos de la cita y advertencia de no reembolso
3. Acepta checkbox de no reembolso
4. Confirma cancelación
5. Recibe email de confirmación de cancelación
Post: Slot liberado en Calendar; estado en Sheets = "cancelado_paciente"
```

### CU-03: Cancelación < 24h (bloqueada)
```
Pre: Paciente intenta cancelar con < 24 horas
1. Accede al link de cancelación
2. Ve mensaje: "Esta cita ya no puede cancelarse ni modificarse"
Post: Sin cambios en el sistema
```

### CU-04: Cancelación por médico
```
Pre: Médico elimina o cancela evento en Google Calendar
1. Apps Script detecta la cancelación (trigger)
2. Paciente recibe email con opciones: reprogramar o reembolso
Post: Slot liberado; estado en Sheets = "cancelado_medico"
```

## Políticas del producto

| Política | Regla |
|---|---|
| Reembolso | No hay reembolso si cancela el paciente |
| Plazo de cancelación | Mínimo 24 horas de anticipación |
| Modificación | Solo con ≥ 24h de anticipación |
| Cancelación por médico | Siempre implica oferta de reembolso o reprogramación |
| Informe post-consulta | No es una receta; es un informe de recomendaciones orientativo |

## Métricas de éxito

| Métrica | Objetivo (60 días post-lanzamiento) |
|---|---|
| Consultas agendadas y pagadas | ≥ 10 en el primer mes |
| Tasa de confirmación post-formulario | ≥ 60% |
| Tasa de cancelación | ≤ 20% |
| Confirmación email post-pago | < 2 minutos (100% casos) |
| Errores en flujo crítico | 0 |
| Pacientes recurrentes | ≥ 30% a los 3 meses |

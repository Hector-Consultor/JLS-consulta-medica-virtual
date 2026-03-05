---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-05T00:00:00Z
version: 2.0
author: Claude Code PM System
---

# Product Context — Plataforma de Telemedicina Multi-Especialista

> SCOPE v2.0 (05/03/2026): Actualizado de modelo unipersonal a plataforma multi-especialista.

## Descripcion del producto

Plataforma de telemedicina que conecta pacientes de Colombia, Venezuela y Argentina con especialistas medicos. Gestiona de forma automatizada el agendamiento, pago (con split entre plataforma y especialista), confirmacion y recordatorio de consultas virtuales via Google Meet.

## Usuarios objetivo

### Paciente — Valentina Rios (arquetipo, Colombia)
- **Perfil:** 38 anos, Bogota. Profesional con seguro medico privado.
- **Necesidad:** Segunda opinion con un especialista de confianza, rapido y sin tramites.
- **Pain point:** Turnos con especialistas presenciales tardan semanas.
- **Criterio de exito:** Agendar, pagar y recibir confirmacion con link Meet en menos de 5 minutos.

### Paciente — Carlos Medina (arquetipo, Venezuela)
- **Perfil:** 55 anos, Caracas. Paciente cronico (hipertension, dislipemia).
- **Necesidad:** Seguimiento periodico con especialistas de confianza accesibles desde Venezuela.
- **Pain point:** Acceso muy limitado a especialistas de calidad desde Venezuela.
- **Criterio de exito:** Re-agendamiento rapido y sin contacto directo con el medico.

### Especialista — Dr. Juan Luis Salazar (arquetipo, primer especialista)
- **Perfil:** Medico Internista. Practica privada. Sin staff administrativo.
- **Necesidad:** Sistema que gestione su agenda y cobros de forma autonoma.
- **Pain point:** Tiempo perdido coordinando turnos por WhatsApp/email.
- **Criterio de exito:** "No necesito intervenir manualmente para confirmar un turno."

### Administrador de plataforma
- **Perfil:** Hector Gonzalez (operador inicial) o persona designada por el cliente.
- **Necesidad:** Incorporar especialistas, supervisar citas, monitorear ingresos.
- **Criterio de exito:** Dashboard en Google Sheets con vision completa de la operacion.

## Funcionalidades core (MVP v2 — preliminar)

| # | Funcionalidad | Actor | Prioridad |
|---|---|---|---|
| 1 | Ver directorio de especialistas con perfiles | Paciente | Alta |
| 2 | Ver disponibilidad del especialista en tiempo real | Paciente | Alta |
| 3 | Agendar cita con formulario | Paciente | Alta |
| 4 | Aceptar politica de no reembolso antes de pagar | Paciente | Alta |
| 5 | Pagar online (COP/USD) con split automatico | Paciente | Alta |
| 6 | Recibir email de confirmacion con link Meet | Paciente | Alta |
| 7 | Cancelar cita (24h+, sin reembolso) | Paciente | Alta |
| 8 | Recibir recordatorio 24h antes | Paciente + Especialista | Alta |
| 9 | Realizar videollamada via Google Meet | Paciente + Especialista | Alta |
| 10 | Recibir informe de recomendaciones post-consulta | Paciente | Alta |
| 11 | Ver agenda del dia | Especialista | Alta |
| 12 | Cancelar cita con reembolso/reprogramacion | Especialista | Media |
| 13 | Crear informe con template de Google Docs | Especialista | Media |
| 14 | Gestionar especialistas (alta, baja, configuracion) | Administrador | Alta |
| 15 | Ver dashboard de citas e ingresos | Administrador | Media |

## Casos de uso principales

### CU-01: Agendamiento completo
```
Pre: Paciente accede a la plataforma
1. Ve directorio de especialistas
2. Selecciona especialista y ve su disponibilidad
3. Selecciona slot
4. Completa formulario (nombre, email, telefono, pais, motivo)
5. Acepta politica de no reembolso
6. Paga online (split automatico plataforma/especialista)
7. Recibe email con link Meet + link de cancelacion
Post: Cita en Sheets + Calendar del especialista
```

### CU-02: Cancelacion por paciente
```
Pre: Paciente tiene link unico de cancelacion
     Faltan 24h o mas para la cita
1. Accede al link
2. Ve datos de la cita y advertencia de no reembolso
3. Confirma cancelacion
4. Recibe confirmacion de cancelacion
Post: Slot liberado en Calendar; estado en Sheets = "cancelado_paciente"
```

### CU-03: Cancelacion menos de 24h (bloqueada)
```
Pre: Paciente intenta cancelar con menos de 24h
1. Ve mensaje: "Esta cita ya no puede cancelarse ni modificarse"
Post: Sin cambios
```

### CU-04: Cancelacion por especialista
```
Pre: Especialista cancela desde su Calendar o dashboard
1. Apps Script detecta la cancelacion
2. Paciente recibe email con opciones: reprogramar o reembolso
Post: Slot liberado; estado en Sheets = "cancelado_especialista"
```

## Politicas del producto

| Politica | Regla |
|---|---|
| Reembolso | Sin reembolso si cancela el paciente |
| Plazo de cancelacion | Minimo 24 horas de anticipacion |
| Cancelacion por especialista | Siempre implica oferta de reembolso o reprogramacion |
| Informe post-consulta | Informe de recomendaciones orientativo (no es receta) |
| Split de pagos | Porcentaje plataforma/especialista — pendiente definicion |

## Metricas de exito (MVP v2 — a refinar con el cliente)

| Metrica | Objetivo tentativo |
|---|---|
| Especialistas en la plataforma al mes 1 | A definir por el cliente |
| Consultas agendadas y pagadas en el primer mes | A definir por el cliente |
| Confirmacion email post-pago | Menos de 2 minutos (100% de casos) |
| Errores en flujo critico | 0 |
| Tasa de cancelacion | Menor o igual a 20% |

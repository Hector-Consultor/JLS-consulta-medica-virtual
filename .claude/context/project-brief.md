---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-05T00:00:00Z
version: 2.0
author: Claude Code PM System
---

# Project Brief — Plataforma de Telemedicina Multi-Especialista

> NOTA: Este brief refleja el scope v2.0 definido el 05/03/2026. La v1.0 era un sistema unipersonal para el Dr. Juan Luis Salazar. El cambio de scope fue solicitado por el cliente.

## Que es?

Plataforma de telemedicina especializada que conecta pacientes de Colombia, Venezuela y Argentina con multiples especialistas medicos. Permite agendar, pagar y gestionar consultas virtuales via Google Meet, con gestion automatizada end-to-end sin intervencion de staff administrativo.

## Por que existe?

El modelo unipersonal inicial (solo Dr. Salazar) no escalaba. El cliente identifico la oportunidad de construir una plataforma que:

1. Permita a multiples especialistas ofrecer sus servicios sin infraestructura propia
2. Automatice completamente la gestion de turnos, cobros y confirmaciones
3. Opere en 3 paises con sus respectivos marcos legales
4. Genere ingresos a la plataforma via split de pagos con especialistas

## Cambio de scope (v1 vs v2)

| Dimension | v1 (unipersonal) | v2 (plataforma) |
|---|---|---|
| Especialistas | 1 (Dr. Salazar) | Multiples |
| Paises | Colombia + Venezuela | + Argentina |
| Roles | Paciente + Medico | + Especialista + Administrador |
| Calendarios | 1 | Uno por especialista |
| Pagos | Tarifa fija al medico | Split plataforma/especialista |
| Normativa | 2 marcos legales | 3 marcos legales |
| Landing | Pagina del Dr. Salazar | Plataforma con directorio de especialistas |

## Stack tecnologico (sin cambios)

- **Backend:** Google Apps Script (orquestador)
- **Base de datos:** Google Sheets
- **Agenda:** Google Calendar (multi-calendario)
- **Videollamada:** Google Meet (via Calendar API)
- **Emails:** Gmail via Apps Script
- **Documentos:** Google Docs + Google Drive
- **Frontend:** Apps Script HTML Service + Plataforma web TBD
- **Pagos:** Pasarela TBD con soporte para split de pagos

## Mercado objetivo

- Colombia — Ley 1419/2010 (telemedicina), Ley 1581/2012 (datos personales)
- Venezuela — Ley del Ejercicio de la Medicina
- Argentina — Ley 27.553/2020 (teleconsulta medica)

## Alcance del MVP v2 (pendiente de definicion final con el cliente)

### Incluido (preliminar)
- Directorio de especialistas con perfiles publicos
- Agendamiento por especialista con calendario en tiempo real
- Pago online con split automatico plataforma/especialista
- Confirmacion automatica por email con link de Google Meet
- Recordatorio automatico 24h antes
- Cancelacion autonoma por paciente (24h+, sin reembolso)
- Cancelacion por especialista con reembolso/reprogramacion
- Dashboard de administrador (gestion de especialistas y citas)
- Template de informe post-consulta por especialidad

### Excluido explicitamente (heredado de v1)
- Recetas digitales o prescripciones
- Historia clinica electronica
- Portal del paciente con login e historial
- App movil nativa
- Integracion con seguros medicos
- Facturacion electronica
- Recordatorios SMS

## Decisiones pendientes del cliente (bloquean el desarrollo)

1. Nombre comercial de la plataforma
2. Pasarela de pago seleccionada
3. Modelo de distribucion de pagos (% plataforma / % especialista)
4. Cantidad de especialistas en MVP y especialidades
5. Fases de lanzamiento por pais (orden y calendario)

## Stakeholders

| Rol | Persona | Responsabilidad |
|---|---|---|
| Cliente / Product Owner | Dr. Juan Luis Salazar | Decisiones de negocio, validacion del PRD v2.0 |
| Desarrollador | Hector Gonzalez | Implementacion, arquitectura |
| Especialistas | A definir | Uso del sistema como proveedores de consultas |
| Pacientes | Colombia / Venezuela / Argentina | Uso del sistema como consumidores |

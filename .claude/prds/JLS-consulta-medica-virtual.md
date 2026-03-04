---
name: JLS-consulta-medica-virtual
description: Plataforma web para consulta médica virtual autogestionada del Dr. Juan Luis Salazar, con agendamiento, pago online y videollamada via Google Workspace.
status: backlog
created: 2026-03-04T19:26:53Z
---

# PRD: JLS Consulta Médica Virtual

## Executive Summary

Sistema de consulta médica virtual 100% autogestionado para la práctica privada unipersonal del Dr. Juan Luis Salazar, Médico Internista. Permite a pacientes de Colombia y Venezuela agendar, pagar y cancelar consultas de forma autónoma a través de una página web responsiva, con videollamada via Google Meet y envío automatizado de informe de recomendaciones post-consulta. El backend se apoya en Google Workspace (Calendar, Sheets, Gmail, Meet, Drive) + Google Apps Script; la plataforma de publicación web está por definir (ver sección de análisis de alternativas).

---

## Problem Statement

### Problema principal
El Dr. Salazar no dispone de consultorio físico, lo que imposibilita el modelo de atención presencial tradicional. Sin embargo, tiene licencia médica vigente en Colombia y Venezuela, y demanda real de pacientes que buscan segunda opinión o asesoría médica integral a distancia.

### Situación actual (pain points)
- Sin sistema de agendamiento propio, el médico depende de intermediarios o apps de terceros con altos costos/comisiones.
- La gestión manual de turnos (WhatsApp, email) consume tiempo del médico y genera errores de coordinación.
- Los pacientes no tienen visibilidad de disponibilidad en tiempo real.
- No existe un canal formal para recibir pago previo a la consulta.
- La confirmación del turno y el envío del link de Meet se realizan manualmente.
- No hay proceso estandarizado para el informe post-consulta.

### Por qué ahora
La telemedicina está en crecimiento acelerado en Colombia y Venezuela post-pandemia. El médico tiene la infraestructura Google Workspace ya disponible. Construir sobre ese stack elimina costos de infraestructura y permite lanzar un MVP funcional con inversión mínima.

---

## User Stories

### Personas

#### Persona 1 — Paciente Nuevo
**Nombre:** Valentina Ríos, 38 años, Bogotá, Colombia.
**Perfil:** Profesional con seguro médico privado. Recibió un diagnóstico y quiere una segunda opinión de un internista. Prefiere gestionar todo online sin llamar a nadie.
**Motivación:** Confirmar o descartar un diagnóstico sin esperar semanas por un turno presencial.

#### Persona 2 — Paciente Recurrente
**Nombre:** Carlos Medina, 55 años, Caracas, Venezuela.
**Perfil:** Paciente crónico con hipertensión y dislipemia. Consulta periódicamente para revisión de indicaciones y seguimiento.
**Motivación:** Acceso rápido al médico de confianza sin desplazarse.

#### Persona 3 — Médico (Dr. Juan Luis Salazar)
**Perfil:** Médico Internista con práctica privada. Maneja su propio Google Workspace. No tiene staff administrativo.
**Motivación:** Tener la agenda organizada automáticamente, cobrar antes de atender y enviar informes de forma estandarizada.

---

### User Journeys

#### Journey 1 — Agendar una consulta (Paciente Nuevo)

1. Paciente accede a Google Sites (página web del Dr. Salazar) desde mobile o desktop.
2. Ve la sección "Agendar consulta" con descripción del servicio y tarifa.
3. Hace clic en "Ver disponibilidad" → se muestra calendario con slots disponibles (integrado con Google Calendar).
4. Selecciona fecha y horario disponible.
5. Completa formulario: nombre, email, teléfono, motivo de consulta (campo libre) y país.
6. Es redirigido a pasarela de pago online → completa el pago.
7. Recibe email automático de confirmación con:
   - Datos del turno (fecha, hora, zona horaria)
   - Link de Google Meet
   - Instrucciones para la consulta
   - Link para cancelar el turno
8. El slot queda bloqueado en Google Calendar del médico.

**Criterios de aceptación:**
- El calendario muestra solo horarios disponibles (no los ya reservados).
- La confirmación llega al email del paciente en menos de 2 minutos de completado el pago.
- El link de Meet es único por consulta.
- El turno aparece en Google Calendar del médico con datos del paciente.

---

#### Journey 2 — Cancelar una consulta (Paciente)

1. Paciente accede al link de cancelación recibido en el email de confirmación.
2. Ve un formulario con sus datos y los datos del turno (pre-cargados).
3. Se muestra advertencia clara: **"Al cancelar esta cita no recibirá reembolso del pago realizado."**
4. El sistema valida que falten más de 24 horas para la cita. Si no, muestra: "Esta cita ya no puede cancelarse ni modificarse (menos de 24 horas de anticipación)."
5. Paciente confirma la cancelación aceptando la política de no reembolso (checkbox obligatorio).
6. Recibe email de confirmación de cancelación indicando que no habrá devolución.
7. El slot queda liberado en Google Calendar para nuevas reservas.
8. El médico recibe notificación de la cancelación.

**Criterios de aceptación:**
- Solo se puede cancelar con el link único recibido por email.
- La cancelación solo es posible con ≥ 24 horas de anticipación.
- Si el intento de cancelación ocurre con < 24 horas, el sistema bloquea la acción y muestra mensaje explicativo.
- La política de no reembolso se muestra antes de confirmar y requiere aceptación explícita.
- El slot se libera de forma inmediata tras confirmar.
- El médico recibe notificación por Gmail ante cada cancelación.

---

#### Journey 2B — Cancelación por parte del médico

1. El médico cancela el turno directamente desde Google Calendar o desde un panel admin básico.
2. El sistema detecta la cancelación (trigger en Apps Script).
3. Se envía email al paciente indicando la cancelación por parte del médico, ofreciendo dos opciones:
   - **Reprogramar:** link para seleccionar nuevo horario disponible.
   - **Solicitar reembolso:** instrucciones para el proceso de devolución del pago.
4. El médico recibe recordatorio de gestionar el reembolso si el paciente lo solicita.

**Criterios de aceptación:**
- El sistema distingue quién cancela (paciente o médico).
- Si cancela el médico, el email al paciente siempre ofrece reprogramación o reembolso.
- El proceso de reembolso es manual en MVP (el médico lo gestiona directamente con la pasarela), pero el sistema registra la solicitud en Google Sheets.
- El slot se libera en Google Calendar independientemente de quién cancele.

---

#### Journey 3 — Realizar la consulta (Médico + Paciente)

1. A la hora pactada, ambos acceden al link de Google Meet.
2. El médico conduce la consulta médica.
3. Al finalizar, el médico redacta el informe de recomendaciones en un template de Google Docs o formulario interno.
4. El sistema (o el médico manualmente en MVP) envía el informe al email del paciente.

**Criterios de aceptación:**
- El médico tiene acceso fácil a los datos del paciente antes y durante la consulta (desde Google Sheets o Google Calendar).
- El informe se envía al email registrado del paciente.
- El informe queda guardado en Google Drive del médico.

---

#### Journey 4 — Gestión de agenda (Médico)

1. El médico define sus slots disponibles directamente en Google Calendar.
2. Los slots bloqueados/reservados se actualizan automáticamente.
3. El médico puede ver en Google Sheets el listado de consultas del día/semana.

**Criterios de aceptación:**
- El médico no necesita intervención manual para confirmar turnos.
- Puede bloquear días festivos o vacaciones directamente en Google Calendar.

---

## Requirements

### Functional Requirements

#### FR-01: Página web pública (plataforma a definir — ver sección de análisis)
- Información del médico: especialidad, experiencia, países de licencia.
- Descripción del servicio de consulta virtual.
- Tarifa de la consulta visible antes de agendar.
- Sección "¿Cómo funciona?" con pasos del proceso.
- Política de cancelación visible: "No hay reembolso por cancelaciones del paciente."
- Botón de llamado a la acción "Agendar consulta".
- Diseño responsivo (desktop + mobile).
- Soporte de idioma español.
- La plataforma seleccionada debe permitir integrar el formulario de agendamiento y/o embeber el widget de calendario.

#### FR-02: Visualización de disponibilidad
- Mostrar calendario con slots disponibles en tiempo real.
- Integración con Google Calendar del médico para reflejar disponibilidad actualizada.
- Los slots ya reservados no deben ser seleccionables.
- Indicar zona horaria (el médico opera en zonas horarias de Colombia/Venezuela, ambas UTC-5).

#### FR-03: Formulario de agendamiento
- Campos: nombre completo, email, teléfono, país (Colombia / Venezuela / Otro), motivo de consulta (texto libre, obligatorio).
- Validaciones básicas (email válido, campos obligatorios).
- Almacenamiento del registro en Google Sheets.

#### FR-04: Pago online
- Integración con pasarela de pago compatible con Colombia y Venezuela.
- El turno solo se confirma una vez acreditado el pago.
- El sistema debe manejar el estado del pago (pendiente / aprobado / rechazado).
- Moneda: COP (pesos colombianos) y/o USD como alternativa.
- **Antes de proceder al pago**, el paciente debe aceptar (checkbox obligatorio) la política de cancelación: "Entiendo que si cancelo esta cita, no recibiré reembolso del pago realizado."
- La política debe estar redactada de forma visible en la pantalla de pago, no como texto oculto.

#### FR-05: Confirmación automática por email
- Trigger: pago aprobado.
- Contenido: nombre del médico, fecha/hora del turno, zona horaria, link de Google Meet único, instrucciones previas, link de cancelación personalizado.
- Enviado desde Gmail del médico o cuenta asociada vía Apps Script.

#### FR-06: Cancelación y modificación autónoma

**Cancelación por el paciente:**
- Link único e irrepetible por turno para cancelación (incluido en el email de confirmación).
- El sistema valida que falten **más de 24 horas** para la cita antes de permitir la cancelación.
  - Si faltan < 24 horas: mostrar mensaje bloqueante, sin opción de cancelar.
- Formulario de confirmación antes de cancelar con advertencia de no reembolso.
- El paciente debe aceptar un checkbox: "Entiendo que no recibiré reembolso."
- Liberación automática del slot en Google Calendar.
- Email de confirmación de cancelación al paciente (recordando la política de no reembolso).
- Notificación al médico por Gmail.
- Registro del evento en Google Sheets (estado: "cancelado por paciente").

**Restricción de modificación:**
- Las citas solo pueden modificarse (reprogramarse) con **mínimo 24 horas de anticipación**.
- Con < 24 horas de anticipación, la cita queda bloqueada: no puede cancelarse ni modificarse.
- El email de confirmación debe informar claramente esta restricción.

**Cancelación por el médico:**
- El médico puede cancelar desde Google Calendar o panel admin.
- Apps Script detecta la cancelación y notifica al paciente.
- El email al paciente ofrece: reprogramar (link de disponibilidad) o solicitar reembolso.
- El reembolso es gestionado manualmente por el médico en MVP; el sistema registra la solicitud en Sheets.
- Registro en Google Sheets (estado: "cancelado por médico").

#### FR-07: Registro de consultas (Google Sheets)
- Hoja de cálculo con columnas: ID turno, nombre paciente, email, teléfono, país, motivo, fecha/hora, estado (pendiente pago / confirmado / cancelado por paciente / cancelado por médico / realizado), link Meet, fecha de registro, cancelado por (paciente / médico / N/A), reembolso solicitado (sí / no / N/A).
- Actualización automática ante cada evento (reserva, pago, cancelación, realización).

#### FR-08: Informe de recomendaciones post-consulta
- Template de Google Docs estandarizado con datos del paciente y campos de recomendaciones.
- El médico completa el informe post-consulta.
- Envío del informe al email del paciente (manual por el médico en MVP, automatizable en fases futuras).
- Copia guardada en Google Drive organizada por fecha/paciente.

#### FR-09: Notificaciones al médico
- Email de notificación ante: nueva reserva confirmada, cancelación de turno (indicando si fue por paciente o médico).
- Recordatorio del turno próximo (24h antes), incluido en MVP.

#### FR-10: Recordatorio automático 24h antes de la cita
- El sistema envía automáticamente un email de recordatorio al paciente **24 horas antes** de la cita.
- Contenido del recordatorio: fecha/hora, link de Google Meet, instrucciones de conexión, y aviso de que ya no es posible cancelar ni modificar la cita.
- Trigger: Apps Script con time-based trigger que corra cada hora y detecte citas próximas.
- También se envía recordatorio al médico 24h antes con los datos del paciente y motivo de consulta.
- Registro en Google Sheets: columna "recordatorio enviado" (sí/no/fecha).

---

### Non-Functional Requirements

#### NFR-01: Rendimiento
- El calendario de disponibilidad debe cargar en menos de 3 segundos.
- La confirmación por email debe enviarse en menos de 2 minutos post-pago.
- Google Apps Script debe completar cada automatización en menos de 30 segundos (dentro del límite de ejecución de Apps Script).

#### NFR-02: Seguridad
- Links de cancelación con tokens únicos e impredecibles (UUID o similar).
- Datos de pacientes almacenados en Google Sheets con acceso restringido solo al médico.
- Pago procesado íntegramente por la pasarela (sin almacenar datos de tarjeta en Sheets ni Apps Script).
- Cumplimiento HTTPS en todo el sitio (Google Sites lo garantiza por defecto).

#### NFR-03: Privacidad y regulación
- **Colombia:** Cumplimiento con Resolución 2654 de 2019 (telemedicina), Ley 1581 de 2012 (protección de datos personales - Habeas Data), y normativas del MINSALUD. La plataforma actúa como canal de coordinación; el médico es responsable del acto médico.
- **Venezuela:** Ley del Ejercicio de la Medicina vigente. Considerar marco regulatorio de telemedicina del MPPS. Nota: el marco legal venezolano para telemedicina es menos específico; el médico debe validar con su colegio médico.
- Aviso de privacidad / términos y condiciones visible en el sitio.
- Consentimiento informado del paciente antes de agendar (checkbox obligatorio).

#### NFR-04: Escalabilidad
- El stack Google Workspace soporta la operación unipersonal sin modificaciones hasta aprox. 100 consultas/mes (límites de Apps Script y Sheets).
- Si el volumen supera ese umbral, se evaluará migración parcial o uso de Google Cloud.

#### NFR-05: Disponibilidad
- Google Sites y Google Workspace tienen SLA de 99.9% de uptime. Aceptable para práctica unipersonal.

#### NFR-06: Mantenibilidad
- Todo el código en Apps Script debe estar comentado y versionado (Google Apps Script tiene versionado nativo).
- El contenido de la página web (textos, tarifas, disponibilidad) debe poder ser actualizado por el médico sin intervención técnica.
- No depender de librerías externas fuera del ecosistema Google en el backend.

---

## Success Criteria

### Métricas de lanzamiento (primeros 60 días)
| Métrica | Objetivo |
|---|---|
| Consultas agendadas y pagadas | ≥ 10 en el primer mes |
| Tasa de confirmación (pago completado / formulario iniciado) | ≥ 60% |
| Tasa de cancelación | ≤ 20% |
| Tiempo de confirmación post-pago | < 2 minutos (100% de los casos) |
| Errores en automatizaciones Apps Script | 0 en flujo crítico (reserva + confirmación) |

### Métricas de calidad (post-lanzamiento)
| Métrica | Objetivo |
|---|---|
| Satisfacción del médico con la herramienta | "No necesito intervención manual para confirmar turnos" |
| Informes post-consulta enviados / consultas realizadas | 100% |
| Pacientes recurrentes (segunda consulta) | ≥ 30% a los 3 meses |

---

## Constraints & Assumptions

### Restricciones técnicas
- Backend obligatorio: Google Workspace (Sheets, Calendar, Gmail, Meet, Drive, Docs) + Google Apps Script. No se evalúan alternativas para el backend.
- La plataforma de publicación web (frontend) está abierta a evaluación (ver sección de análisis de alternativas).
- Apps Script tiene límite de ejecución de 6 minutos por script y cuotas de email (100 emails/día en cuenta gratuita, 1500/día en Workspace).
- No se pueden ejecutar servidores propios ni bases de datos relacionales en el stack definido.

### Restricciones de negocio
- Práctica unipersonal: el médico es el único usuario administrador.
- Sin staff: todas las configuraciones y ajustes los realiza el médico o el desarrollador contratado.
- La pasarela de pago debe operar en Colombia y/o Venezuela, aceptar tarjetas y transferencias locales.
- El médico no emite recetas (solo informes de recomendaciones), lo que simplifica el marco regulatorio.

### Supuestos
- El médico ya tiene cuenta de Google Workspace activa con dominio propio o cuenta profesional.
- El médico define y mantiene sus propios horarios de disponibilidad en Google Calendar.
- El precio de la consulta es fijo (no hay diferenciación por tipo de consulta en MVP).
- Las consultas son en español únicamente.
- Zona horaria de operación: UTC-5 (Colombia y Venezuela tienen la misma zona horaria).

---

## Out of Scope

Los siguientes elementos están **explícitamente excluidos** del MVP:

- Recetas digitales o prescripciones (el sistema emite informes de recomendaciones, no recetas).
- Historia clínica electrónica (HCE) o expediente médico del paciente.
- Mensajería o chat entre paciente y médico fuera del contexto del turno.
- Portal del paciente (login, historial de consultas, etc.).
- App móvil nativa (iOS/Android); solo web responsiva.
- Múltiples médicos o especialidades (es práctica unipersonal).
- Integración con seguros médicos o sistemas de salud públicos.
- Recordatorios SMS (solo email).
- Soporte multiidioma (solo español).
- Facturación electrónica o integración contable.
- Sistema de reseñas o valoraciones de pacientes.
- Módulo de analítica o reportes avanzados.
- Atención de emergencias o urgencias médicas.

---

## Dependencies

### Dependencias externas
| Dependencia | Descripción | Riesgo |
|---|---|---|
| Google Workspace | Infraestructura completa del sistema | Bajo (SLA alto, plataforma madura) |
| Pasarela de pago | Procesamiento de cobros online en Colombia/Venezuela | **Alto** — definir proveedor (Wompi, PayU, Stripe, MercadoPago) |
| Dominio web | Necesario para Google Sites con URL personalizada | Bajo |
| Cuenta de Google Meet | Incluida en Google Workspace | Bajo |

### Pasarelas de pago candidatas
- **Wompi** (Colombia, nativa, acepta PSE + tarjetas) — recomendada para mercado colombiano.
- **PayU** (Colombia y Venezuela) — cobertura regional.
- **MercadoPago** — amplio en Colombia, limitado en Venezuela.
- **Stripe** — requiere validar disponibilidad en Venezuela.

> **Decisión pendiente:** El médico debe seleccionar la pasarela de pago antes del inicio del desarrollo. Este es el único bloqueador crítico del proyecto.

### Dependencias internas
| Dependencia | Descripción |
|---|---|
| Definición de slots de disponibilidad | El médico debe configurar su Google Calendar con los horarios de trabajo antes del lanzamiento |
| Template de informe de recomendaciones | El médico debe aprobar el template de Google Docs |
| Precio de la consulta | El médico debe definir tarifa en COP y/o USD antes del desarrollo del formulario de pago |
| Política de cancelación | Definida: 24h mínimas para cancelar/modificar; sin reembolso si cancela el paciente; reembolso o reprogramación si cancela el médico |
| Términos y condiciones | Requiere redacción legal básica adaptada a Colombia y Venezuela |

---

## Análisis de Alternativas — Plataforma Web

El stack backend (Google Workspace + Apps Script) está definido. La elección de la plataforma de publicación web impacta en diseño, costo de mantenimiento e integración.

### Criterios de evaluación
- **Costo mensual** (en USD, sin IVA)
- **Facilidad de mantenimiento** (el médico debe poder actualizar textos sin ayuda técnica)
- **Integración con Google Workspace** (Calendar, Forms, Apps Script)
- **Diseño y personalización** (apariencia profesional)
- **Dominio personalizado** (necesario para credibilidad)

---

### Opción A — Google Sites

| Criterio | Evaluación |
|---|---|
| Costo | **Gratuito** (incluido en Google Workspace) |
| Mantenimiento | Muy fácil — el médico puede editar sin conocimientos técnicos |
| Integración Google | Nativa y directa (Calendar, Forms, Drive embebibles) |
| Diseño | Limitado — templates básicos, poca personalización visual |
| Dominio personalizado | Sí, con Google Workspace (requiere configuración DNS) |
| Responsivo | Sí, automático |

**Pros:** Sin costo adicional, integración perfecta con el backend, mantenimiento autónomo por el médico, tiempo de setup mínimo.
**Contras:** Diseño genérico, difícil diferenciarse visualmente, limitaciones en CSS/JS personalizado, no permite formularios complejos sin Apps Script.

**Recomendado si:** El presupuesto es la prioridad y la apariencia profesional es secundaria para el MVP.

---

### Opción B — Netlify (con sitio estático)

| Criterio | Evaluación |
|---|---|
| Costo | **Gratuito** (plan hobby) — sin límite de sitios estáticos |
| Mantenimiento | Requiere conocimiento técnico básico (HTML/CSS o generador de sitios) |
| Integración Google | Indirecta — via JavaScript + Apps Script Web App API |
| Diseño | Completo control — HTML/CSS/JS a medida |
| Dominio personalizado | Sí, gratuito con HTTPS automático |
| Responsivo | Depende del desarrollador |

**Pros:** Control total del diseño, HTTPS gratuito, despliegue desde GitHub (CI/CD), ideal para sitio estático personalizado, sin costo.
**Contras:** El médico NO puede editar solo — requiere intervención técnica para cada cambio de contenido. Integración con Google Calendar requiere desarrollo adicional (API o iframe).

**Recomendado si:** Se prioriza diseño profesional y hay disponibilidad de un desarrollador para mantenimiento.

---

### Opción C — Wix

| Criterio | Evaluación |
|---|---|
| Costo | **USD 17–29/mes** (plan con dominio y sin publicidad de Wix) |
| Mantenimiento | Muy fácil — editor drag & drop, el médico puede editar textos e imágenes |
| Integración Google | Parcial — Google Analytics y Calendar embebible via iframe; no integración nativa con Apps Script |
| Diseño | Templates profesionales, amplia galería médica/salud |
| Dominio personalizado | Incluido en planes pagos |
| Responsivo | Automático (con ajuste manual recomendado) |

**Pros:** Diseño profesional sin código, mantenimiento autónomo, templates del sector salud disponibles, incluye hosting.
**Contras:** Costo mensual, integración con Apps Script limitada (requiere iframes o Wix Velo para lógica), no se migra fácilmente a otro stack.

**Recomendado si:** Se prioriza apariencia profesional y autonomía del médico para editar, con presupuesto disponible.

---

### Opción D — Squarespace

| Criterio | Evaluación |
|---|---|
| Costo | **USD 23–33/mes** (plan Business o Commerce) |
| Mantenimiento | Fácil — editor visual, menos flexible que Wix pero más elegante |
| Integración Google | Google Analytics, Calendar embebible via iframe, sin integración Apps Script nativa |
| Diseño | Templates premium, estética más cuidada que Wix |
| Dominio personalizado | Incluido en el primer año |
| Responsivo | Automático y bien optimizado |

**Pros:** Diseño premium, aspecto muy profesional, buen manejo de imágenes, incluye SEO básico.
**Contras:** El más costoso, integración técnica con Google Workspace limitada (iframes), menos flexible que Wix para personalización avanzada.

**Recomendado si:** La imagen de marca premium es prioritaria y el presupuesto lo permite.

---

### Opción E — Carrd

| Criterio | Evaluación |
|---|---|
| Costo | **USD 19/año** (plan Pro Standard — dominio + formularios) |
| Mantenimiento | Fácil — editor simple, ideal para landing page de una sola página |
| Integración Google | Calendar embebible via iframe, formulario redirige a Apps Script |
| Diseño | Minimalista, moderno, limitado a una página |
| Dominio personalizado | Sí, en plan Pro |
| Responsivo | Automático |

**Pros:** Costo casi nulo, muy fácil de configurar, ideal para un MVP de landing page, aspecto limpio y moderno.
**Contras:** Limitado a una sola página (o pocas secciones), no escala bien si el médico quiere agregar blog o múltiples servicios.

**Recomendado si:** Se quiere una landing page simple y económica para el MVP, sin necesidad de múltiples páginas.

---

### Tabla comparativa resumen

| Plataforma | Costo/mes | Mantenimiento por médico | Integración GW | Diseño | Recomendación |
|---|---|---|---|---|---|
| Google Sites | Gratis | ✅ Muy fácil | ✅ Nativa | ⚠️ Básico | MVP económico |
| Netlify | Gratis | ❌ Técnico | ⚠️ API/iframe | ✅ Full control | Si hay dev disponible |
| Wix | ~USD 20 | ✅ Fácil | ⚠️ Parcial | ✅ Profesional | Balance costo/diseño |
| Squarespace | ~USD 28 | ✅ Fácil | ⚠️ Parcial | ✅ Premium | Imagen de marca alta |
| Carrd | ~USD 1.6 | ✅ Fácil | ⚠️ iframe | ✅ Moderno | MVP minimalista |

### Recomendación del equipo técnico

**Para el MVP:** **Google Sites** (costo cero, integración nativa con backend) o **Carrd** (diseño más moderno, costo simbólico).
**Si el médico quiere autonomía de edición + diseño profesional:** **Wix** en plan Business.
**Si hay presupuesto de desarrollo:** **Netlify con sitio estático** a medida ofrece el mayor control.

> **Decisión pendiente:** El Dr. Salazar debe seleccionar la plataforma antes del inicio del desarrollo. Se recomienda alinear esta decisión con el presupuesto mensual disponible y la frecuencia esperada de actualización de contenidos.

---

## Technical Architecture (alto nivel)

```
[Paciente] → [Plataforma Web - a definir]
             (Google Sites / Netlify / Wix / etc.)
                         ↓
               Apps Script (orquestador)
              /        |        \        \
   Google Calendar  Google Sheets  Gmail  Gmail
   (disponibilidad) (registro)  (confirm.) (recordat. 24h)
                         |
                   Pasarela de Pago
                   (webhook → Apps Script)
                         ↓
                   Google Meet (link generado)
                   Google Drive (informes)
```

### Flujo de datos clave

**Agendamiento:**
1. Apps Script expone disponibilidad de Google Calendar via Web App.
2. Formulario en la plataforma web (embebido o nativo) envía datos a Apps Script.
3. Apps Script crea registro en Google Sheets (estado: "pendiente pago") y redirige a pasarela.
4. Paciente acepta política de no reembolso en pantalla de pago antes de pagar.
5. Pasarela notifica a Apps Script (webhook o polling) con resultado del pago.
6. Apps Script actualiza Sheets → bloquea slot en Google Calendar → genera link Meet → envía email de confirmación con Gmail.

**Recordatorio (time-based trigger, cada hora):**
7. Apps Script revisa Sheets en busca de citas cuyo inicio sea en 24-25 horas.
8. Para cada una: envía email de recordatorio a paciente y médico. Registra en Sheets.

**Cancelación por paciente:**
9. Paciente usa link único del email → Apps Script valida token y tiempo (> 24h).
10. Si válido: paciente acepta no reembolso → Apps Script libera slot en Calendar → actualiza Sheets → envía emails de confirmación.
11. Si < 24h: Apps Script devuelve error bloqueante, sin cancelación posible.

**Cancelación por médico:**
12. Médico cancela en Calendar → trigger de Apps Script detecta la eliminación del evento.
13. Apps Script notifica al paciente ofreciendo reprogramación o reembolso. Registra en Sheets.

---

## Roadmap sugerido

### Fase 1 — MVP (alcance de este PRD)
- Página web (plataforma a definir según análisis de alternativas).
- Formulario de agendamiento con calendario de disponibilidad.
- Integración con pasarela de pago + aceptación de política de no reembolso.
- Confirmación automática por email con link de Meet.
- Recordatorio automático 24h antes de la cita (paciente y médico).
- Cancelación autónoma con link único (solo si faltan > 24h, sin reembolso).
- Cancelación por médico con oferta de reprogramación o reembolso.
- Restricción: no se puede cancelar ni modificar con < 24h de anticipación.
- Registro en Google Sheets con estados diferenciados.
- Template de informe de recomendaciones.

### Fase 2 — Post-MVP (fuera de scope)
- Envío automático del informe post-consulta (automatización con Apps Script).
- Módulo de seguimiento para pacientes recurrentes.
- Analítica básica en Google Data Studio / Looker Studio.

---

## Open Questions

| # | Pregunta | Responsable | Prioridad |
|---|---|---|---|
| 1 | ¿Qué pasarela de pago se usará? (bloqueante) | Dr. Salazar | Alta |
| 2 | ¿Cuál es la tarifa de la consulta en COP y/o USD? | Dr. Salazar | Alta |
| 3 | ¿Qué plataforma web se usará? (ver análisis de alternativas) | Dr. Salazar + Dev | Alta |
| 4 | ¿El sitio usará dominio personalizado? ¿Ya tiene uno? | Dr. Salazar | Media |
| 5 | ¿El médico tiene cuenta Google Workspace de pago o gratuita? (afecta límites de emails) | Dr. Salazar | Media |
| 6 | ¿Se requiere validación legal formal en Venezuela antes del lanzamiento? | Dr. Salazar | Media |
| 7 | ¿El template del informe de recomendaciones ya existe o debe crearse desde cero? | Dr. Salazar | Baja |

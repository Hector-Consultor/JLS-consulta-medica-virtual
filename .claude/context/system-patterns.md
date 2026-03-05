---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# System Patterns — JLS Consulta Médica Virtual

## Patrón arquitectónico general

**Serverless Single-Orchestrator**

Google Apps Script actúa como el único punto de orquestación. No hay servidor dedicado — todo corre en los servidores de Google bajo demanda.

```
[Paciente]
    │ HTTP GET/POST
    ▼
[Apps Script Web App]  ← único punto de entrada
    │
    ├─ doGet(e)  → sirve páginas HTML (booking, cancelacion)
    └─ doPost(e) → procesa webhooks (pago, cancelacion)
         │
         ▼ (orquesta)
    ┌────────────────────────────────────────┐
    │  Google Workspace                       │
    │  Calendar │ Sheets │ Gmail │ Drive      │
    └────────────────────────────────────────┘
```

## Patrones de diseño identificados

### 1. Router Pattern (Code.gs)
El archivo `Code.gs` actúa como router HTTP. Analiza el parámetro `page` del request y delega a la función correspondiente.

```javascript
function doGet(e) {
  const page = e.parameter.page;
  if (page === 'agenda') return servirAgenda();
  if (page === 'cancelar') return servirCancelacion(e.parameter.token);
  return servirHome();
}

function doPost(e) {
  const action = e.parameter.action;
  if (action === 'pago') return Pagos.procesarWebhook(e);
  if (action === 'cancelar') return Cancelaciones.procesarCancelacion(e);
}
```

### 2. Service Layer Pattern
Cada área de dominio tiene su propio archivo `.gs`:
- `SheetsService.gs` → acceso a datos (CRUD en Sheets)
- `CalendarService.gs` → disponibilidad y eventos
- `Emails.gs` → templates y envío de correos

Esta separación permite modificar el acceso a datos sin tocar la lógica de negocio.

### 3. Token-Based Authentication (cancelación)
Las cancelaciones usan un UUID único generado al confirmar el pago. El token se pasa en la URL y se valida contra Sheets. Después de usarse, se invalida.

```
Email confirmación → link /?page=cancelar&token=UUID
                          ↓
              Apps Script valida token en Sheets
                          ↓
              Procesa cancelación → invalida token
```

### 4. Event-Driven Automation (recordatorios)
Un time-based trigger corre cada hora y activa `Recordatorios.enviarRecordatoriosHorarios()`. La función consulta Sheets para encontrar citas en la ventana 24-25h y envía los recordatorios.

### 5. Webhook Pattern (pagos)
La pasarela de pago notifica a Apps Script via POST cuando un pago es aprobado o rechazado. Apps Script valida la firma del webhook antes de procesar.

```
[Pasarela] → POST /?action=pago
               ↓
         Valida firma HMAC
               ↓
         Busca id_turno en Sheets
               ↓
         Crea evento Calendar + Meet
               ↓
         Envía email confirmación
```

## Flujo de datos principal

```
1. REGISTRO (pendiente_pago)
   Formulario → SheetsService.crear() → Sheets[pendiente_pago]

2. PAGO APROBADO (confirmado)
   Webhook → Pagos.confirmar() → CalendarService.crearEvento()
                               → SheetsService.actualizar(confirmado, link_meet, token)
                               → Emails.enviarConfirmacion()

3. RECORDATORIO (24h antes)
   Trigger → Recordatorios.buscarProximas() → Emails.enviarRecordatorio()
                                             → SheetsService.marcarRecordatorio()

4. CANCELACIÓN PACIENTE (cancelado_paciente)
   Link token → Cancelaciones.validarToken() → CalendarService.eliminarEvento()
                                              → SheetsService.actualizar(cancelado_paciente)
                                              → Emails.enviarCancelacion()

5. CANCELACIÓN MÉDICO (cancelado_medico)
   Calendar delete → Trigger → Cancelaciones.detectarMedico()
                              → SheetsService.actualizar(cancelado_medico)
                              → Emails.enviarCancelacionMedico()
```

## Invariantes del sistema

1. **Un slot = un registro en Sheets**: No puede haber dos registros en estado `confirmado` para el mismo slot.
2. **Token de uso único**: Un token de cancelación solo puede usarse una vez. Después se invalida.
3. **Solo confirmar si pago aprobado**: El estado `confirmado` solo se asigna tras recibir webhook de pago aprobado con firma válida.
4. **Validación 24h en servidor**: La restricción de 24h se valida en Apps Script (servidor), no en el cliente (HTML).
5. **Zona horaria consistente**: Todas las fechas se almacenan y procesan en UTC-5.

## Consideraciones de seguridad

| Amenaza | Mitigación |
|---|---|
| Webhooks falsos de la pasarela | Validación de firma HMAC/secret en Apps Script |
| Uso de tokens de cancelación ajenos | Token UUID único por cita; no hay listado público |
| Acceso no autorizado a Sheets | Google Sheets con acceso restringido solo al médico |
| Inyección en campos del formulario | Sanitización en Apps Script antes de escribir en Sheets |
| Cancelación fuera de tiempo | Validación server-side de 24h; cliente no puede saltear |

---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-05T00:00:00Z
version: 2.0
author: Claude Code PM System
---

# Tech Context — Plataforma de Telemedicina Multi-Especialista

> SCOPE v2.0 (05/03/2026): Arquitectura actualizada para soporte multi-especialista, multi-calendario y split de pagos.

## Stack tecnológico

### Backend / Orquestador
| Tecnología | Versión | Rol |
|---|---|---|
| Google Apps Script | V8 runtime | Orquestador completo (API, automatizaciones, triggers) |
| Google Sheets API | v4 | Base de datos (lectura/escritura via SpreadsheetApp) |
| Google Calendar API | v3 | Gestión de disponibilidad y creación de eventos con Meet |
| Gmail API | via GmailApp | Envío de emails transaccionales |
| Google Drive API | via DriveApp | Almacenamiento de informes post-consulta |
| Google Meet | via Calendar API | Videollamada (links generados automáticamente) |

### Frontend / UI
| Tecnología | Rol |
|---|---|
| Apps Script HTML Service | Páginas de agendamiento y cancelación (Web App) |
| HTML5 + CSS3 | Markup y estilos de booking.html y cancelacion.html |
| JavaScript vanilla | Validaciones del lado cliente en las páginas HTML |
| Plataforma web TBD | Landing page pública (Google Sites / Wix / Carrd / Netlify) |

### Pagos
| Tecnología | Estado | Notas |
|---|---|---|
| Pasarela TBD | Pendiente decisión | Candidatos: Wompi (recomendado), PayU, MercadoPago, Stripe |
| Wompi | Preferida Colombia | Acepta PSE + tarjetas, webhook nativo |
| PayU | Alternativa regional | Colombia + Venezuela |

### Infraestructura
| Servicio | Proveedor | Costo |
|---|---|---|
| Hosting del sistema | Google Apps Script (Web App) | Incluido en Google Workspace |
| Base de datos | Google Sheets | Incluido en Google Workspace |
| Emails | Gmail via Apps Script | Incluido (cuota: 100/día gratis, 1500/día Workspace) |
| Videollamada | Google Meet | Incluido en Google Workspace |
| Almacenamiento | Google Drive | Incluido en Google Workspace |
| Landing page | A definir | Gratis (Sites/Netlify/Carrd) a ~USD 20/mes (Wix) |

## Limitaciones técnicas conocidas

| Limitación | Valor | Impacto |
|---|---|---|
| Tiempo ejecución Apps Script | 6 min/script | Aceptable, cada función < 30s |
| Emails por día (cuenta gratuita) | 100 | Suficiente para MVP; upgrade si supera 80 citas/mes |
| Emails por día (Workspace) | 1500 | Holgura suficiente |
| Registros en Google Sheets | ~5 millones celdas | Sin impacto en volumen esperado |
| Triggers simultáneos | 20 por proyecto | Sin impacto (solo se usa 1 time-based trigger) |
| URL del Web App | Larga por defecto | Resolver con dominio personalizado o redirección |

## Herramientas de desarrollo

| Herramienta | Uso |
|---|---|
| Google Apps Script Editor | IDE principal para desarrollo del backend |
| clasp (opcional) | CLI para desarrollo local de Apps Script con push/pull |
| Git + GitHub | Control de versiones del proyecto de documentación |
| Claude Code | Asistente de desarrollo y gestión del proyecto |

## Entorno de operación

- **Zona horaria:** UTC-5 (Colombia y Venezuela, misma zona)
- **Idioma:** Español (único, sin i18n)
- **Mercado:** Colombia (COP) y Venezuela (referencial USD)
- **Regulación:** Resolución 2654/2019 Colombia + Ley del Ejercicio de la Medicina Venezuela

## Cambios arquitecturales v2 (multi-especialista)

| Cambio | v1 (unipersonal) | v2 (plataforma) |
|---|---|---|
| Calendarios | 1 fijo | Uno por especialista (array de IDs) |
| Hojas de Sheets | Sheet unico | Sheet maestro + tab por especialista |
| Emails | Email fijo del medico | Email dinamico por especialista |
| Tarifas | Tarifa unica | Tarifa por especialista + porcentaje plataforma |
| Templates | Template unico | Template por especialidad |
| Configuracion | CONFIG global | CONFIG global + CONFIG por especialista |

## Variables de configuracion (Config.gs — v2)

```javascript
const CONFIG = {
  SHEET_ID: '',                  // ID del Google Sheet maestro de la plataforma
  HORAS_MIN_CANCELACION: 24,     // Horas minimas para cancelar/modificar
  URL_WEB_APP: '',               // URL del Web App desplegado
  DRIVE_FOLDER_ID: '',           // Carpeta raiz de informes en Drive
  PASARELA_SECRET: '',           // Secreto para validar webhooks de la pasarela
  PORCENTAJE_PLATAFORMA: 0,      // % de cada pago que retiene la plataforma (TBD)
  EMAIL_ADMIN: '',               // Email del administrador de la plataforma
};

// Configuracion por especialista (fila en Sheet "Especialistas")
// { id, nombre, email, calendar_id, tarifa_cop, tarifa_usd, template_informe_id, activo }
```

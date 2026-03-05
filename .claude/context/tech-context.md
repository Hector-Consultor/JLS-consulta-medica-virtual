---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Tech Context — JLS Consulta Médica Virtual

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

## Variables de configuración (Config.gs)

```javascript
const CONFIG = {
  SHEET_ID: '',              // ID del Google Sheet maestro
  CALENDAR_ID: '',           // ID del Google Calendar del médico
  EMAIL_MEDICO: '',          // Email del Dr. Salazar
  TARIFA_COP: 0,             // Tarifa en pesos colombianos (pendiente)
  TARIFA_USD: 0,             // Tarifa en USD (pendiente)
  HORAS_MIN_CANCELACION: 24, // Horas mínimas para cancelar/modificar
  URL_WEB_APP: '',           // URL del Web App desplegado
  TEMPLATE_INFORME_ID: '',   // ID del template de Google Docs
  DRIVE_FOLDER_ID: '',       // ID de la carpeta raíz de informes en Drive
  PASARELA_SECRET: '',       // Secreto para validar webhooks de la pasarela
};
```

---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Project Style Guide — JLS Consulta Médica Virtual

## Convenciones de código (Google Apps Script)

### Nomenclatura

| Elemento | Convención | Ejemplo |
|---|---|---|
| Archivos .gs | PascalCase | `SheetsService.gs`, `CalendarService.gs` |
| Funciones públicas | camelCase | `registrarCita()`, `procesarWebhook()` |
| Funciones privadas | _camelCase | `_validarToken()`, `_sanitizarInput()` |
| Constantes | SNAKE_UPPER_CASE | `HORAS_MIN_CANCELACION`, `EMAIL_MEDICO` |
| Variables locales | camelCase | `idTurno`, `fechaCita` |
| Parámetros de función | camelCase | `datosPaciente`, `tokenCancelacion` |

### Estructura de archivos .gs

```javascript
// ============================================================
// [NOMBRE_ARCHIVO].gs — [Descripción en una línea]
// JLS Consulta Médica Virtual
// ============================================================

/**
 * [Descripción de la función]
 * @param {Object} datos - Descripción del parámetro
 * @returns {string} Descripción del retorno
 */
function nombreFuncion(datos) {
  // Implementación
}
```

### Comentarios
- Comentar el propósito de cada función (JSDoc style)
- Comentar lógica no obvia (ej: cálculo de la ventana de 24-25h)
- Idioma de comentarios: **español**
- No comentar código obvio (no `// suma a + b`)

### Manejo de errores

```javascript
function operacionCritica(param) {
  try {
    // lógica
  } catch (error) {
    Logger.log('Error en operacionCritica: ' + error.toString());
    SheetsService.registrarError('operacionCritica', error.toString(), param);
    throw error; // re-lanzar si es flujo crítico
  }
}
```

### Acceso a Sheets
- Siempre usar `SheetsService.gs` como capa de acceso; nunca acceder directamente a `SpreadsheetApp` desde otros archivos
- Definir el ID del sheet en `Config.gs`, nunca hardcodeado

```javascript
// ✅ Correcto
const sheet = SheetsService.obtenerHojaConsultas();

// ❌ Incorrecto
const sheet = SpreadsheetApp.openById('1abc...').getSheetByName('consultas');
```

## Convenciones de HTML (booking.html, cancelacion.html)

### Estructura
```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JLS Consulta Médica</title>
  <style>/* estilos inline o en <style> */</style>
</head>
<body>
  <!-- contenido -->
  <script>/* scripts al final del body */</script>
</body>
</html>
```

### Estilos
- CSS en bloque `<style>` dentro del HTML (Apps Script HTML Service no soporta archivos CSS externos fácilmente)
- Variables CSS para colores corporativos
- Mobile-first: diseñar primero para mobile, luego ajustar para desktop

### Comunicación con Apps Script desde HTML
```javascript
// Usar google.script.run (si la página es servida por Apps Script)
google.script.run
  .withSuccessHandler(onSuccess)
  .withFailureHandler(onError)
  .nombreFuncion(datos);
```

## Convenciones de documentación

### Archivos Markdown (.md)
- Frontmatter YAML obligatorio en archivos de contexto y tareas
- Usar tablas para comparaciones
- Usar bloques de código con lenguaje especificado
- Títulos en español (excepto nombres técnicos)
- Listas de verificación con `- [ ]` para items pendientes

### Frontmatter de tareas
```yaml
---
name: Título descriptivo en imperativo
status: open | in_progress | completed
created: 2026-03-04T21:15:01Z
updated: 2026-03-04T21:15:01Z
github: https://github.com/Hector-Consultor/JLS-consulta-medica-virtual/issues/N
depends_on: [2, 3]  # números de issues GitHub
parallel: true | false
conflicts_with: []
---
```

## Convenciones de Git

### Commits
- Idioma: español
- Formato: `[tipo]: [descripción breve en minúsculas]`
- Tipos: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

```bash
# Ejemplos
feat: implementar webhook de confirmación de pago
docs: actualizar README con decisión de pasarela
fix: corregir cálculo de ventana de 24h en recordatorios
chore: actualizar mapeo de issues tras sync con GitHub
```

### Branches
- `main` — rama principal, siempre estable
- `epic/[nombre]` — branch de trabajo del epic
- `feature/[issue-number]-[descripción]` — branches de features específicas

## Variables de entorno y configuración

- Valores sensibles (tokens, secrets) siempre en **Script Properties** de Apps Script
- Nunca en el código fuente
- IDs de recursos (Sheets, Calendar, Drive) en `Config.gs` o Script Properties

```javascript
// ✅ Correcto: leer desde Script Properties
const secret = PropertiesService.getScriptProperties().getProperty('PASARELA_SECRET');

// ❌ Incorrecto: hardcodeado
const secret = 'sk_live_abc123...';
```

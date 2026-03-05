---
created: 2026-03-04T21:15:01Z
last_updated: 2026-03-04T21:15:01Z
version: 1.0
author: Claude Code PM System
---

# Project Structure — JLS Consulta Médica Virtual

## Estructura del repositorio (actual)

```
JLS-consulta-medica-virtual/           ← Raíz del proyecto (repo Git)
│
├── README.md                          ← Documentación pública del proyecto
│
└── .claude/                           ← Directorio de gestión del proyecto (PM system)
    ├── settings.local.json            ← Configuración local de Claude Code
    │
    ├── prds/                          ← Product Requirements Documents
    │   └── JLS-consulta-medica-virtual.md
    │
    ├── epics/
    │   └── JLS-consulta-medica-virtual/
    │       ├── epic.md                ← Documento técnico del epic
    │       ├── github-mapping.md      ← Mapeo issues GitHub ↔ archivos locales
    │       ├── 2.md                   ← Task #2: Setup base Apps Script
    │       ├── 3.md                   ← Task #3: Disponibilidad y formulario
    │       ├── 4.md                   ← Task #4: Pasarela de pago 🔒
    │       ├── 5.md                   ← Task #5: Cancelación 24h
    │       ├── 6.md                   ← Task #6: Recordatorios automáticos
    │       ├── 7.md                   ← Task #7: Landing page 🔒
    │       ├── 8.md                   ← Task #8: Template informe
    │       └── 9.md                   ← Task #9: QA y despliegue
    │
    └── context/                       ← Contexto del proyecto (este directorio)
        ├── progress.md
        ├── project-structure.md
        ├── tech-context.md
        ├── system-patterns.md
        ├── product-context.md
        ├── project-brief.md
        ├── project-overview.md
        ├── project-vision.md
        └── project-style-guide.md
```

## Estructura del proyecto Apps Script (a crear durante el desarrollo)

```
JLS-Consulta-Medica/                   ← Proyecto en Google Apps Script
├── Code.gs                            ← Router principal (doGet / doPost)
├── Agenda.gs                          ← Disponibilidad y registro de citas
├── Pagos.gs                           ← Webhook de pasarela, post-pago
├── Cancelaciones.gs                   ← Cancelación paciente + médico
├── Recordatorios.gs                   ← Trigger horario, emails 24h
├── Emails.gs                          ← Templates de emails transaccionales
├── SheetsService.gs                   ← CRUD sobre Google Sheets
├── CalendarService.gs                 ← Disponibilidad + eventos con Meet
├── Informes.gs                        ← Creación de informes post-consulta
├── Config.gs                          ← Constantes y configuración
└── pages/
    ├── booking.html                   ← Formulario de agendamiento
    └── cancelacion.html               ← Página de cancelación con validación 24h
```

## Convenciones de nombres

| Elemento | Convención | Ejemplo |
|---|---|---|
| Archivos de contexto | kebab-case | `tech-context.md` |
| Archivos de tareas | número de issue GitHub | `2.md`, `9.md` |
| Archivos Apps Script | PascalCase.gs | `SheetsService.gs` |
| Funciones Apps Script | camelCase | `registrarCita()` |
| Variables Config | SNAKE_UPPER_CASE | `HORAS_MIN_CANCELACION` |
| Templates HTML | kebab-case | `booking.html` |

## Archivos clave

| Archivo | Propósito |
|---|---|
| `README.md` | Documentación pública, punto de entrada del proyecto |
| `.claude/prds/JLS-consulta-medica-virtual.md` | Requisitos completos del producto |
| `.claude/epics/JLS-consulta-medica-virtual/epic.md` | Plan técnico de implementación |
| `.claude/epics/JLS-consulta-medica-virtual/github-mapping.md` | Mapeo issues ↔ archivos |

# Power Automate – CHR/CDR Boletines

Este directorio contiene los artefactos necesarios para desplegar el flujo **CHR_Boletines_AllES_v3** en Power Automate.

## Contenido
- `flow_CHR_Boletines_AllES_v3.json`: definición del flujo con captura multifuente, detección del PDF final, OCR y alertas en Teams.
- `boletines_fuentes_es.csv`: inventario de feeds RSS utilizados para BOE y boletines autonómicos.

## Pasos de importación
1. Crea (o abre) una solución en Power Automate.
2. Importa el archivo `flow_CHR_Boletines_AllES_v3.json`.
3. Una vez creado el flujo, edítalo en el diseñador y repara las conexiones solicitadas:
   - **SharePoint**
   - **Document Intelligence / AI Builder**
   - **Microsoft Teams** (para las alertas)
4. Ajusta los parámetros si es necesario:
   - `sharePointSiteAddress`: `https://magteles-my.sharepoint.com/personal/antonio_rueda_magtel_es`
   - `sharePointFolderPath`: `/Boletines`
   - `sharePointListName`: `Maestro_CHR_Boletines`

## Lista SharePoint
Antes de ejecutar el flujo, crea la lista `Maestro_CHR_Boletines` con las siguientes columnas:
- **Expediente** (Texto)
- **Proyecto** (Texto)
- **Boletín** (Elección)
- **Fecha publicación** (Fecha/Hora)
- **Tipo resolución** (Elección: AAP, AAC, AAE, DIA, Otro)
- **Organismo emisor** (Texto)
- **Informe / Aclaración** (Multilínea)
- **Alcance** (Elección múltiple: DPH, Embalse, Operatividad, Laderas, Limos, Ictiofauna, Caudales, Otro)
- **Fecha solicitud** (Fecha/Hora)
- **Fecha entrega** (Fecha/Hora)
- **Autor informe** (Texto)
- **URL boletín** (Hipervínculo)
- **Ruta SharePoint PDF** (Hipervínculo)
- **Estado procesamiento** (Elección: Pendiente OCR, Procesado, Error)

## Alertas
El flujo genera alertas en Teams cuando detecta:
- “Declaración de Impacto Ambiental” o “DIA”.
- Resultados desfavorables.
- Solicitudes de subsanación.
- Referencias a Confederaciones Hidrográficas.

 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a//dev/null b/power_automate/README.md
index 0000000000000000000000000000000000000000..8603780ffa5adcd263265a5c222cdb63979b8c5b 100644
--- a//dev/null
+++ b/power_automate/README.md
@@ -0,0 +1,48 @@
+# Power Automate – CHR/CDR Boletines
+
+Este directorio contiene los artefactos necesarios para desplegar el flujo "CHR_Boletines_AllES_v3" en Power Automate.
+
+## Contenido
+
+- `flow_CHR_Boletines_AllES_v3.json`: definición del flujo con captura multifuente, detección del PDF final, OCR y alertas en Teams.
+- `boletines_fuentes_es.csv`: inventario de feeds RSS utilizados para BOE y boletines autonómicos.
+
+## Pasos de importación
+
+1. Crea (o abre) una solución en Power Automate.
+2. Usa el CLI oficial `pac` para cargar la definición JSON en tu entorno (ver sección **Importación con Power Platform CLI**).
+3. Una vez creado el flujo, edítalo en el diseñador y repara las conexiones solicitadas: SharePoint, Document Intelligence/AI Builder y Microsoft Teams.
+4. Ajusta los parámetros si es necesario:
+   - `sharePointSiteAddress`: `https://magteles-my.sharepoint.com/personal/antonio_rueda_magtel_es`
+   - `sharePointFolderPath`: `/Boletines`
+   - `sharePointListName`: `Maestro_CHR_Boletines`
+   - `teamsTeamId` y `teamsChannelId`: usa los identificadores reales de tu equipo/canal.
+5. Publica el flujo y comprueba que la lista de SharePoint contiene las columnas indicadas en la conversación.
+
+### Importación con Power Platform CLI
+
+Si la opción de **Importar desde archivo ZIP** no está disponible o necesitas cargar el JSON directamente, puedes usar el [Power Platform CLI](https://learn.microsoft.com/power-platform/developer/cli/introduction) desde cualquier equipo con acceso a Power Automate (no requiere privilegios de administrador si ya tienes Microsoft 365 instalado):
+
+```bash
+pac auth create --url https://make.powerautomate.com --name chr-ingestor
+pac flow push --path power_automate/flow_CHR_Boletines_AllES_v3.json --environment <ENVIRONMENT_ID>
+```
+
+Sustituye `<ENVIRONMENT_ID>` por el identificador del entorno donde quieras desplegar el flujo. Tras la publicación, accede a Power Automate → **Soluciones** → busca `CHR_Boletines_AllES_v3` y completa las conexiones pendientes.
+
+## Funcionalidades destacadas
+
+- Consulta horaria de BOE y todos los boletines autonómicos mediante RSS.
+- Filtro semántico por autorizaciones administrativas y expedientes CHidr.
+- Descarga robusta del PDF final con búsqueda en páginas intermedias.
+- Desduplicación por URL, guardado en SharePoint y registro en lista maestra.
+- OCR (modelo prebuilt-layout) para extraer texto completo y campos hidráulicos clave.
+- Alertas en Microsoft Teams cuando se detectan DIAs desfavorables, subsanaciones o menciones a Confederaciones Hidrográficas.
+
+> **Nota:** el paso de OCR usa el conector premium **Document Intelligence**; si tu licencia no lo cubre, deshabilita la acción `Analyze_Document` antes de ejecutar el flujo.
+
+## Personalización adicional
+
+- Actualiza el CSV para añadir o desactivar fuentes concretas.
+- Amplía el bloque de análisis (`Compose_Alcance`) para detectar más términos técnicos.
+- Añade condiciones de alerta adicionales en `Alert_Check`.
 
EOF
)

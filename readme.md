 (cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF' 
diff --git a/power_automate/README.md b/power_automate/README.md
new file mode 100644
index 0000000000000000000000000000000000000000..8603780ffa5adcd263265a5c222cdb63979b8c5b
--- /dev/null
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
diff --git a/power_automate/boletines_fuentes_es.csv b/power_automate/boletines_fuentes_es.csv
new file mode 100644
index 0000000000000000000000000000000000000000..23d7d4c005358387831939a3ff331c10a4a876be
--- /dev/null
+++ b/power_automate/boletines_fuentes_es.csv
@@ -0,0 +1,23 @@
+Nombre,URL_RSS,Notas
+BOE,https://www.boe.es/diario_boe/rss.php,Boletín Oficial del Estado
+BOJA,https://www.juntadeandalucia.es/eboja/sumario.do?formato=rss,Boletín Oficial de la Junta de Andalucía
+BOA,https://www.boa.aragon.es/cgi-bin/EBOA/BRSCGI?CMD=VERLST&BASE=RSS_LISTA,Boletín Oficial de Aragón
+BOPA,https://sede.asturias.es/rss,Boletín Oficial del Principado de Asturias
+BOIB,https://www.caib.es/pidip2front/rss.do,Boletín Oficial de las Illes Balears
+BOC-Canarias,https://www.gobiernodecanarias.org/boc/boletines/rss.do,Boletín Oficial de Canarias
+BOC-Cantabria,https://boc.cantabria.es/boces/rss,Boletín Oficial de Cantabria
+DOCM,https://docm.castillalamancha.es/rss,Diario Oficial de Castilla-La Mancha
+BOCYL,https://bocyl.jcyl.es/boletines/rss.do,Boletín Oficial de Castilla y León
+DOGC,https://dogc.gencat.cat/es/el-dogc/rss/,Diari Oficial de la Generalitat de Catalunya
+DOGV,https://dogv.gva.es/datos/rss/es,Diari Oficial de la Generalitat Valenciana
+DOE,https://doe.juntaex.es/rss,Diario Oficial de Extremadura
+DOG,https://www.xunta.gal/dog/Publicados/rss.do,Diario Oficial de Galicia
+BOCM,https://www.bocm.es/boletin/rss,Boletín Oficial de la Comunidad de Madrid
+BORM,https://www.borm.es/borm/rss,Boletín Oficial de la Región de Murcia
+BON,https://bon.navarra.es/es/rss,Boletín Oficial de Navarra
+BOR,https://ias1.larioja.org/boletin/rss,Boletín Oficial de La Rioja
+BOPV-EHAA,https://www.euskadi.eus/bopv/rss,Boletín Oficial del País Vasco / Euskal Herriko Agintaritzaren Aldizkaria
+BOCCE,https://www.ceuta.es/ceuta/rss/boletin,Boletín Oficial de la Ciudad de Ceuta
+BOME,https://www.melilla.es/melillaPortal/recursos/rss/boletinOficial.rss,Boletín Oficial de la Ciudad de Melilla
+BOPA-Oviedo,https://sedemovil.asturias.es/rss,Fuente alternativa móvil Principado de Asturias
+BOCAZ,https://www.zaragoza.es/sede/servicio/boletin/rss.xml,Boletín Oficial de la Provincia de Zaragoza (complementario)
diff --git a/power_automate/flow_CHR_Boletines_AllES_v3.json b/power_automate/flow_CHR_Boletines_AllES_v3.json
new file mode 100644
index 0000000000000000000000000000000000000000..5eba99c419464ba92073cb3e4c845744cafd4948
--- /dev/null
+++ b/power_automate/flow_CHR_Boletines_AllES_v3.json
@@ -0,0 +1,448 @@
+{
+  "name": "CHR_Boletines_AllES_v3",
+  "type": "Microsoft.Logic/workflows",
+  "location": "westeurope",
+  "properties": {
+    "state": "NotSpecified",
+    "displayName": "CHR_Boletines_AllES_v3",
+    "definition": {
+      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
+      "contentVersion": "1.0.0.0",
+      "parameters": {
+        "sharePointSiteAddress": {
+          "type": "String",
+          "defaultValue": "https://magteles-my.sharepoint.com/personal/antonio_rueda_magtel_es"
+        },
+        "sharePointFolderPath": {
+          "type": "String",
+          "defaultValue": "/Boletines"
+        },
+        "sharePointListName": {
+          "type": "String",
+          "defaultValue": "Maestro_CHR_Boletines"
+        },
+        "teamsAlertEnabled": {
+          "type": "Bool",
+          "defaultValue": true
+        },
+        "teamsTeamId": {
+          "type": "String",
+          "defaultValue": "00000000-0000-0000-0000-000000000000"
+        },
+        "teamsChannelId": {
+          "type": "String",
+          "defaultValue": "19:00000000000000000000000000000000@thread.tacv2"
+        }
+      },
+      "triggers": {
+        "Recurrence": {
+          "type": "Recurrence",
+          "recurrence": {
+            "frequency": "Hour",
+            "interval": 1
+          }
+        }
+      },
+      "actions": {
+        "Initialize_Source_List": {
+          "type": "InitializeVariable",
+          "inputs": {
+            "variables": [
+              {
+                "name": "SourceList",
+                "type": "Array",
+                "value": [
+                  {"name": "BOE", "rss": "https://www.boe.es/diario_boe/rss.php"},
+                  {"name": "BOJA", "rss": "https://www.juntadeandalucia.es/eboja/sumario.do?formato=rss"},
+                  {"name": "BOA", "rss": "https://www.boa.aragon.es/cgi-bin/EBOA/BRSCGI?CMD=VERLST&BASE=RSS_LISTA"},
+                  {"name": "BOPA", "rss": "https://sede.asturias.es/rss"},
+                  {"name": "BOIB", "rss": "https://www.caib.es/pidip2front/rss.do"},
+                  {"name": "BOC-Canarias", "rss": "https://www.gobiernodecanarias.org/boc/boletines/rss.do"},
+                  {"name": "BOC-Cantabria", "rss": "https://boc.cantabria.es/boces/rss"},
+                  {"name": "DOCM", "rss": "https://docm.castillalamancha.es/rss"},
+                  {"name": "BOCYL", "rss": "https://bocyl.jcyl.es/boletines/rss.do"},
+                  {"name": "DOGC", "rss": "https://dogc.gencat.cat/es/el-dogc/rss/"},
+                  {"name": "DOGV", "rss": "https://dogv.gva.es/datos/rss/es"},
+                  {"name": "DOE", "rss": "https://doe.juntaex.es/rss"},
+                  {"name": "DOG", "rss": "https://www.xunta.gal/dog/Publicados/rss.do"},
+                  {"name": "BOCM", "rss": "https://www.bocm.es/boletin/rss"},
+                  {"name": "BORM", "rss": "https://www.borm.es/borm/rss"},
+                  {"name": "BON", "rss": "https://bon.navarra.es/es/rss"},
+                  {"name": "BOR", "rss": "https://ias1.larioja.org/boletin/rss"},
+                  {"name": "BOPV-EHAA", "rss": "https://www.euskadi.eus/bopv/rss"},
+                  {"name": "BOCCE", "rss": "https://www.ceuta.es/ceuta/rss/boletin"},
+                  {"name": "BOME", "rss": "https://www.melilla.es/melillaPortal/recursos/rss/boletinOficial.rss"}
+                ]
+              }
+            ]
+          }
+        },
+        "Initialize_CurrentFileUrl": {
+          "type": "InitializeVariable",
+          "inputs": {
+            "variables": [
+              {
+                "name": "CurrentFileUrl",
+                "type": "String",
+                "value": ""
+              }
+            ]
+          },
+          "runAfter": {
+            "Initialize_Source_List": [
+              "Succeeded"
+            ]
+          }
+        },
+        "For_Each_Source": {
+          "type": "Foreach",
+          "foreach": "@variables('SourceList')",
+          "actions": {
+            "HTTP_Get_Feed": {
+              "type": "Http",
+              "inputs": {
+                "method": "GET",
+                "uri": "@item()?['rss']"
+              }
+            },
+            "Compose_Feed_XML": {
+              "type": "Compose",
+              "inputs": "@xml(body('HTTP_Get_Feed'))",
+              "runAfter": {
+                "HTTP_Get_Feed": [
+                  "Succeeded"
+                ]
+              }
+            },
+            "Compose_Items": {
+              "type": "Compose",
+              "inputs": "@xpath(outputs('Compose_Feed_XML'), '/rss/channel/item')",
+              "runAfter": {
+                "Compose_Feed_XML": [
+                  "Succeeded"
+                ]
+              }
+            },
+            "For_Each_Item": {
+              "type": "Foreach",
+              "foreach": "@outputs('Compose_Items')",
+              "actions": {
+                "Reset_File_Url": {
+                  "type": "SetVariable",
+                  "inputs": {
+                    "name": "CurrentFileUrl",
+                    "value": ""
+                  }
+                },
+                "Compose_Link": {
+                  "type": "Compose",
+                  "inputs": "@xpath(item(), 'string(link)')",
+                  "runAfter": {
+                    "Reset_File_Url": [
+                      "Succeeded"
+                    ]
+                  }
+                },
+                "Compose_Title": {
+                  "type": "Compose",
+                  "inputs": "@xpath(item(), 'string(title)')",
+                  "runAfter": {
+                    "Compose_Link": [
+                      "Succeeded"
+                    ]
+                  }
+                },
+                "Compose_PubDate": {
+                  "type": "Compose",
+                  "inputs": "@xpath(item(), 'string(pubDate)')",
+                  "runAfter": {
+                    "Compose_Title": [
+                      "Succeeded"
+                    ]
+                  }
+                },
+                "Compose_Description": {
+                  "type": "Compose",
+                  "inputs": "@xpath(item(), 'string(description)')",
+                  "runAfter": {
+                    "Compose_PubDate": [
+                      "Succeeded"
+                    ]
+                  }
+                },
+                "Compose_Text": {
+                  "type": "Compose",
+                  "inputs": "@concat(outputs('Compose_Title'), ' ', outputs('Compose_Description'))",
+                  "runAfter": {
+                    "Compose_Description": [
+                      "Succeeded"
+                    ]
+                  }
+                },
+                "Filter_By_Keywords": {
+                  "type": "If",
+                  "expression": "@or(contains(toLower(outputs('Compose_Text')), 'autorización administrativa previa'), contains(toLower(outputs('Compose_Text')), 'autorización administrativa de construcción'), contains(toLower(outputs('Compose_Text')), 'autorización administrativa de explotación'), contains(toLower(outputs('Compose_Text')), 'declaración de impacto ambiental'), contains(toLower(outputs('Compose_Text')), ' dia '), contains(toLower(outputs('Compose_Text')), ' central hidroeléctrica reversible'), contains(toLower(outputs('Compose_Text')), ' central reversible'), contains(toLower(outputs('Compose_Text')), ' bombeo reversible'), contains(toLower(outputs('Compose_Text')), ' hidroeléctrica reversible'), contains(toLower(outputs('Compose_Text')), ' chr'), contains(toLower(outputs('Compose_Text')), ' cdr'), contains(toLower(outputs('Compose_Text')), 'chidr-'))",
+                  "actions": {
+                    "Check_Duplicates": {
+                      "type": "OpenApiConnection",
+                      "inputs": {
+                        "host": {
+                          "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline",
+                          "connectionName": "shared_sharepointonline",
+                          "operationId": "GetItems"
+                        },
+                        "parameters": {
+                          "dataset": "@parameters('sharePointSiteAddress')",
+                          "table": "@concat('/lists/', parameters('sharePointListName'))",
+                          "$filter": "@{concat('URL_x0020_boletín eq ''', outputs('Compose_Link'), '''')}"
+                        }
+                      }
+                    },
+                    "If_New": {
+                      "type": "If",
+                      "expression": "@equals(length(body('Check_Duplicates')?['value']), 0)",
+                      "actions": {
+                        "HTTP_Get_Page": {
+                          "type": "Http",
+                          "inputs": {
+                            "method": "GET",
+                            "uri": "@outputs('Compose_Link')"
+                          }
+                        },
+                        "Compose_PDF_Match": {
+                          "type": "Compose",
+                          "inputs": "@match(string(body('HTTP_Get_Page')), '(https?:\/\/[^\"<> ]+?[.]pdf)')",
+                          "runAfter": {
+                            "HTTP_Get_Page": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Select_Download_URL": {
+                          "type": "If",
+                          "expression": "@greater(length(outputs('Compose_PDF_Match')), 0)",
+                          "actions": {
+                            "Set_File_URL": {
+                              "type": "SetVariable",
+                              "inputs": {
+                                "name": "CurrentFileUrl",
+                                "value": "@first(outputs('Compose_PDF_Match'))"
+                              }
+                            }
+                          },
+                          "else": {
+                            "actions": {
+                              "Set_File_URL_Fallback": {
+                                "type": "SetVariable",
+                                "inputs": {
+                                  "name": "CurrentFileUrl",
+                                  "value": "@outputs('Compose_Link')"
+                                }
+                              }
+                            }
+                          }
+                        },
+                        "Download_File": {
+                          "type": "Http",
+                          "inputs": {
+                            "method": "GET",
+                            "uri": "@variables('CurrentFileUrl')"
+                          },
+                          "runAfter": {
+                            "Select_Download_URL": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Compose_File_Name": {
+                          "type": "Compose",
+                          "inputs": "@concat(formatDateTime(outputs('Compose_PubDate'), 'yyyyMMdd'), '_', items('For_Each_Source')?['name'], '_', guid(), '.pdf')",
+                          "runAfter": {
+                            "Download_File": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Create_File": {
+                          "type": "OpenApiConnection",
+                          "inputs": {
+                            "host": {
+                              "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline",
+                              "connectionName": "shared_sharepointonline",
+                              "operationId": "CreateFile"
+                            },
+                            "parameters": {
+                              "dataset": "@parameters('sharePointSiteAddress')",
+                              "folderPath": "@concat(parameters('sharePointFolderPath'), '/', formatDateTime(outputs('Compose_PubDate'), 'yyyy'), '/', items('For_Each_Source')?['name'])",
+                              "name": "@outputs('Compose_File_Name')"
+                            },
+                            "body": "@body('Download_File')"
+                          },
+                          "runAfter": {
+                            "Compose_File_Name": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Get_File_Content": {
+                          "type": "OpenApiConnection",
+                          "inputs": {
+                            "host": {
+                              "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline",
+                              "connectionName": "shared_sharepointonline",
+                              "operationId": "GetFileContent"
+                            },
+                            "parameters": {
+                              "dataset": "@parameters('sharePointSiteAddress')",
+                              "id": "@body('Create_File')?['ItemId']"
+                            }
+                          },
+                          "runAfter": {
+                            "Create_File": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Analyze_Document": {
+                          "type": "OpenApiConnection",
+                          "inputs": {
+                            "host": {
+                              "apiId": "/providers/Microsoft.PowerApps/apis/shared_documentintelligence",
+                              "connectionName": "shared_documentintelligence",
+                              "operationId": "AnalyzeDocument"
+                            },
+                            "parameters": {
+                              "modelId": "prebuilt-layout",
+                              "document": "@base64(body('Get_File_Content'))"
+                            }
+                          },
+                          "runAfter": {
+                            "Get_File_Content": [
+                              "Succeeded"
+                            ]
+                          }
+                        },
+                        "Compose_AI_Text": {
+                          "type": "Compose",
+                          "inputs": "@coalesce(string(body('Analyze_Document')?['content']), '')",
+                          "runAfter": {
+                            "Analyze_Document": [
+                              "Succeeded",
+                              "Failed"
+                            ]
+                          }
+                        },
+                        "Compose_Alcance": {
+                          "type": "Compose",
+                          "inputs": "@trim(join(union(createArray(), if(contains(toLower(outputs('Compose_AI_Text')), 'dominio público hidráulico'), createArray('DPH'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'embalse'), createArray('Embalse'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'operatividad'), createArray('Operatividad'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'ladera'), createArray('Laderas'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'limo'), createArray('Limos'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'ictiofauna'), createArray('Ictiofauna'), createArray()), if(contains(toLower(outputs('Compose_AI_Text')), 'caudal ecológico'), createArray('Caudales ecológicos'), createArray())), '; '))",
+                          "runAfter": {
+                            "Compose_AI_Text": [
+                              "Succeeded",
+                              "Failed"
+                            ]
+                          }
+                        },
+                        "Create_List_Item": {
+                          "type": "OpenApiConnection",
+                          "inputs": {
+                            "host": {
+                              "apiId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline",
+                              "connectionName": "shared_sharepointonline",
+                              "operationId": "PostItem"
+                            },
+                            "parameters": {
+                              "dataset": "@parameters('sharePointSiteAddress')",
+                              "table": "@concat('/lists/', parameters('sharePointListName'))"
+                            },
+                            "body": {
+                              "Title": "@outputs('Compose_Title')",
+                              "Expediente": "@if(greater(length(match(outputs('Compose_Text'), '(CHidr-[0-9]{3})')), 0), first(match(outputs('Compose_Text'), '(CHidr-[0-9]{3})')), '')",
+                              "Proyecto": "@if(greater(length(match(outputs('Compose_Text'), 'Central [^.,;]+')), 0), first(match(outputs('Compose_Text'), 'Central [^.,;]+')), '')",
+                              "Boletín": "@items('For_Each_Source')?['name']",
+                              "Fecha_x0020_publicación": "@outputs('Compose_PubDate')",
+                              "Tipo_x0020_resolución": "@if(greater(length(match(outputs('Compose_Text'), '(AAP|AAC|AAE|DIA)')), 0), first(match(outputs('Compose_Text'), '(AAP|AAC|AAE|DIA)')), '')",
+                              "Organismo_x0020_emisor": "@if(greater(length(match(outputs('Compose_Text'), '(Confederación [^.,;]+|MITECO|Ministerio [^.,;]+)')), 0), first(match(outputs('Compose_Text'), '(Confederación [^.,;]+|MITECO|Ministerio [^.,;]+)')), '')",
+                              "Informe_x0020__x002f__x0020_Aclaración": "@outputs('Compose_AI_Text')",
+                              "Alcance": "@outputs('Compose_Alcance')",
+                              "Fecha_x0020_solicitud": "@if(greater(length(match(outputs('Compose_AI_Text'), 'solicitad[oa] el ([0-9]{1,2}\\/[0-9]{1,2}\\/[0-9]{2,4})')), 0), first(match(outputs('Compose_AI_Text'), 'solicitad[oa] el ([0-9]{1,2}\\/[0-9]{1,2}\\/[0-9]{2,4})')), '')",
+                              "Fecha_x0020_entrega": "@if(greater(length(match(outputs('Compose_AI_Text'), 'presentad[oa] el ([0-9]{1,2}\\/[0-9]{1,2}\\/[0-9]{2,4})')), 0), first(match(outputs('Compose_AI_Text'), 'presentad[oa] el ([0-9]{1,2}\\/[0-9]{1,2}\\/[0-9]{2,4})')), '')",
+                              "Autor_x0020_informe": "@if(greater(length(match(outputs('Compose_AI_Text'), '(Ingeniería [^.,;]+|Universidad [^.,;]+|Consultor[íi]a [^.,;]+)')), 0), first(match(outputs('Compose_AI_Text'), '(Ingeniería [^.,;]+|Universidad [^.,;]+|Consultor[íi]a [^.,;]+)')), '')",
+                              "URL_x0020_boletín": "@outputs('Compose_Link')",
+                              "Ruta_x0020_SharePoint_x0020_PDF": "@body('Create_File')?['Path']",
+                              "Estado_x0020_procesamiento": "Procesado"
+                            }
+                          },
+                          "runAfter": {
+                            "Compose_Alcance": [
+                              "Succeeded",
+                              "Failed"
+                            ]
+                          }
+                        },
+                        "Alert_Check": {
+                          "type": "If",
+                          "expression": "@and(equals(parameters('teamsAlertEnabled'), true), or(contains(toLower(outputs('Compose_AI_Text')), 'dia desfavorable'), contains(toLower(outputs('Compose_AI_Text')), 'subsanación'), contains(toLower(outputs('Compose_AI_Text')), 'confederación hidrográfica')))",
+                          "actions": {
+                            "Send_Teams_Message": {
+                              "type": "OpenApiConnection",
+                              "inputs": {
+                                "host": {
+                                  "apiId": "/providers/Microsoft.PowerApps/apis/shared_microsoftteams",
+                                  "connectionName": "shared_microsoftteams",
+                                  "operationId": "PostMessageToChannel"
+                                },
+                                "parameters": {
+                                  "teamId": "@parameters('teamsTeamId')",
+                                  "channelId": "@parameters('teamsChannelId')"
+                                },
+                                "body": {
+                                  "rootMessage": {
+                                    "subject": "Nueva alerta CHR/CDR",
+                                    "body": {
+                                      "contentType": "html",
+                                      "content": "@concat('<p><strong>', outputs('Compose_Title'), '</strong></p><p><a href=\"', outputs('Compose_Link'), '\">Enlace al bolet\u00edn</a></p><p>', substring(outputs('Compose_AI_Text'), 0, min(1000, length(outputs('Compose_AI_Text')))), '...</p>')"
+                                    }
+                                  }
+                                }
+                              }
+                            }
+                          }
+                        }
+                      }
+                    }
+                  }
+                }
+              }
+            }
+          },
+          "runAfter": {
+            "Initialize_CurrentFileUrl": [
+              "Succeeded"
+            ]
+          }
+        }
+      }
+    },
+    "parameters": {
+      "$connections": {
+        "value": {
+          "shared_sharepointonline": {
+            "connectionId": "/providers/Microsoft.PowerApps/apis/shared_sharepointonline",
+            "connectionName": "shared_sharepointonline",
+            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.PowerApps/apis/shared_sharepointonline"
+          },
+          "shared_documentintelligence": {
+            "connectionId": "/providers/Microsoft.PowerApps/apis/shared_documentintelligence",
+            "connectionName": "shared_documentintelligence",
+            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.PowerApps/apis/shared_documentintelligence"
+          },
+          "shared_microsoftteams": {
+            "connectionId": "/providers/Microsoft.PowerApps/apis/shared_microsoftteams",
+            "connectionName": "shared_microsoftteams",
+            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.PowerApps/apis/shared_microsoftteams"
+          }
+        }
+      }
+    }
+  }
+}
 
EOF
)

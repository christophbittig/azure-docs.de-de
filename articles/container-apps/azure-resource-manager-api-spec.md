---
title: API-Spezifikation der ARM-Vorlage für Container Apps (Vorschau)
description: Sehen Sie sich die verfügbaren Eigenschaften in der ARM-Vorlage für Container Apps an.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: reference
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0f0c4c360b8b9b3cdbe78c833df2a76c2b27d84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323522"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>API-Spezifikation der ARM-Vorlage für Container Apps (Vorschau)

Azure Container Apps-Bereitstellungen basieren auf einer Azure Resource Manager-Vorlage (ARM). In den folgenden Tabellen werden die Eigenschaften beschrieben, die in der ARM-Vorlage der Container-App verfügbar sind.

Die [ARM-Beispielvorlage für Verwendungsbeispiele](#example).

## <a name="resources"></a>Ressourcen

Einträge im `resources`-Array der ARM-Vorlage besitzen die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Datentyp |
|---|---|--|
| `name` | Name der Container Apps-Anwendung | Zeichenfolge |
| `location` | Die Azure-Region, in der die Container Apps-Instanz bereitgestellt wird | Zeichenfolge |
| `tags` | Sammlung von Azure-Tags, die der Container-App zugeordnet sind | array |
| `type` | ARM-Endpunkt `Microsoft.Web/containerApps` bestimmt immer, an welche API weitergeleitet werden soll.  | Zeichenfolge |

In diesem Beispiel geben Sie Ihre Werte an Stelle der Platzhaltertoken ein, die von `<>`-Klammern umgeben sind.

## <a name="properties"></a>properties

Das `properties`-Objekt einer Ressource weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG | Datentyp | Schreibgeschützt |
|---|---|---|---|
| `provisioningState` | Der Status eines Vorgangs mit langer Ausführung, z. B. wenn eine neue Containerrevision erstellt wird. Mögliche Werte sind: Bereitstellung, bereitgestellt, Fehler. Überprüfen Sie, ob die App ausgeführt wird. | Zeichenfolge | Ja |
| `environmentId` | Die Umgebungs-ID für Ihre Container-App **Dies ist eine erforderliche Eigenschaft.** | Zeichenfolge | Nein |
| `latestRevisionName` | Der Name der neuesten Revision | Zeichenfolge | Ja |
| `latestRevisionFqdn` | Die URL der neuesten Revision | Zeichenfolge | Ja |

Der `environmentId`-Wert nimmt die folgende Form an:

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

In diesem Beispiel geben Sie Ihre Werte an Stelle der Platzhaltertoken ein, die von `<>`-Klammern umgeben sind.

## <a name="propertiesconfiguration"></a>properties.configuration

Das `properties.configuration`-Objekt einer Ressource weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG | Datentyp |
|---|---|---|
| `activeRevisionsMode` | Wenn Sie auf `multiple` festlegen, können Sie mehrere Revisionen verwalten. Wenn Sie auf `single` festlegen, werden alte Revisionen automatisch deaktiviert, und nur die neueste Revision bleibt aktiv. | Zeichenfolge |
| `secrets` | Definiert Geheimniswerte in Ihrer Container-App | Objekt |
| `ingress` | Ein Objekt, das die öffentliche Zugriffskonfiguration einer Container-App definiert | Objekt |
| `registries` | Konfigurationsobjekt, das auf Anmeldeinformationen für private Containerregister verweist Mit `secretref` definierte Einträge verweisen auf das Geheimniskonfigurationsobjekt. | Objekt |

Änderungen am Abschnitt `configuration` sind [Änderungen am Anwendungsbereich](revisions.md#application-scope-changes), die keine neue Revision auslösen.

## <a name="propertiestemplate"></a>properties.template

Das `properties.template`-Objekt einer Ressource weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG | Datentyp |
|---|---|---|
| `revisionSuffix` | Ein Anzeigename für eine Revision Dieser Wert muss eindeutig sein, da die Runtime Konflikte mit vorhandenen Suffixwerten von Revisionsnamen ablehnt. | Zeichenfolge |
| `containers` | Konfigurationsobjekt, das definiert, welche Containerimages in der Container-App enthalten sind | Objekt |
| `scale` | Konfigurationsobjekt, das Skalierungsregeln für die Container-App definiert | Objekt |
| `dapr` | Konfigurationsobjekt, das Dapr-Einstellungen für die Container-App definiert | Objekt  |

Änderungen am Abschnitt `template` sind [Änderungen am Revisionsbereich](revisions.md#revision-scope-changes), die keine neue Revision auslösen.

## <a name="example"></a>Beispiel

Im Folgenden finden Sie eine ARM-Beispielvorlage zum Bereitstellen einer Container-App.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "containerappName": {
            "defaultValue": "mycontainerapp",
            "type": "String"
        },
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "myenvironment",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2021-03-01",
            "type": "Microsoft.Web/containerApps",
            "name": "[parameters('containerappName')]",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "secrets": [
                        {
                            "name": "mysecret",
                            "value": "thisismysecret"
                        }
                    ],
                    "ingress": {
                        "external": true,
                        "targetPort": 80,
                        "allowInsecure": false,
                        "traffic": [
                            {
                                "latestRevision": true,
                                "weight": 100
                            }
                        ]
                    }
                },
                "template": {
                    "revisionSuffix": "myrevision",
                    "containers": [
                        {
                            "name": "nginx",
                            "image": "nginx",
                            "env": [
                                {
                                    "name": "HTTP_PORT",
                                    "value": "80"
                                },
                                {
                                    "name": "SECRET_VAL",
                                    "secretRef": "mysecret"
                                }
                            ],
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 3
                    }
                }
            }
        }
    ]
}
```

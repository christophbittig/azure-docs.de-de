---
title: Programmgesteuertes Erstellen von Azure-Dashboards
description: Verwenden eines Dashboards im Azure-Portal als Vorlage, um Azure-Dashboards programmgesteuert zu erstellen. Der Artikel enthält eine JSON-Referenz.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 15d94cf5efc857adf9bbc0cf98c9e3d34e5bda7d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239803"
---
# <a name="programmatically-create-azure-dashboards"></a>Programmgesteuertes Erstellen von Azure-Dashboards

In diesem Artikel wird der Vorgang der programmgesteuerten Erstellung und Veröffentlichung von Azure-Dashboards Schritt für Schritt beschrieben. Auf das unten gezeigte Beispieldashboard wird im gesamten Dokument verwiesen.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/sample-dashboard.png" alt-text="Screenshot: Beispieldashboard im Azure-Portal.":::

## <a name="overview"></a>Übersicht

Freigegebene Dashboards im [Azure-Portal](https://portal.azure.com) sind [Ressourcen](../azure-resource-manager/management/overview.md) genau wie virtuelle Computer und Speicherkonten. Sie können Ressourcen programmgesteuert mithilfe der [Azure Resource Manager-REST-APIs](/rest/api/), [der Azure CLI](/cli/azure) und mit [Azure PowerShell](/powershell/azure/get-started-azureps)-Befehlen verwalten.

Viele Features basieren auf diesen APIs, um die Ressourcenverwaltung zu vereinfachen. Alle diese APIs und Tools bieten Möglichkeiten zum Erstellen, Auflisten, Abrufen, Ändern und Löschen von Ressourcen. Da Dashboards Ressourcen sind, können Sie Ihre bevorzugte API bzw. Ihr bevorzugtes Tool auswählen.

Unabhängig davon, welche Tools Sie zum programmgesteuerten Erstellen eines Dashboards verwenden, erstellen Sie eine JSON-Darstellung des Dashboardobjekts. Dieses Objekt enthält Informationen zu den Kacheln im Dashboard. Es umfasst Größen- und Positionsangaben, Ressourcen, an die Kacheln gebunden sind, sowie alle Benutzeranpassungen.

Der praktischste Weg, dieses JSON-Dokument zu erstellen, ist die Verwendung des Azure-Portals, um ein erstes Dashboard mit den gewünschten Kacheln zu erstellen. Anschließend exportieren Sie das JSON-Dokument und erstellen aus dem Ergebnis eine Vorlage, die Sie weiter bearbeiten und in Skripts, Programmen und Bereitstellungstools verwenden können.

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Um ein Dashboard zu erstellen, wählen Sie im Menü [Azure-Portal](https://portal.azure.com) die Option **Dashboard** aus, und wählen Sie dann **Neues Dashboard** aus.

Ausführliche Anleitungen finden Sie unter [Erstellen eines Dashboards im Azure-Portal](azure-portal-dashboards.md).

## <a name="share-the-dashboard"></a>Freigeben des Dashboards

Nach dem Konfigurieren des Dashboards muss es im nächsten Schritt mithilfe des Befehls **Freigeben** veröffentlicht werden.

Wenn Sie ein Dashboard freigeben, müssen Sie auswählen, in welchem Abonnement und welcher Ressourcengruppe die Veröffentlichung stattfindet. Sie müssen für das Abonnement und die Ressourcengruppe, die Sie auswählen, über Schreibzugriff verfügen. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

Ausführliche Anweisungen finden Sie unter [Freigeben von Azure-Dashboards mithilfe rollenbasierten Zugriffssteuerung von Azure](azure-portal-dashboard-share-access.md).

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Abrufen der JSON-Darstellung des Dashboards

Die Freigabe des Dashboards dauert nur wenige Sekunden. Anschließend exportieren Sie im nächsten Schritt mithilfe des Befehls **Herunterladen** die JSON-Darstellung.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/download-command.png" alt-text="Screenshot: Befehl zum Exportieren der JSON-Darstellung einer Vorlage im Azure-Portal.":::

## <a name="create-a-template-from-the-json"></a>Erstellen einer Vorlage aus der JSON-Darstellung

Der nächste Schritt besteht darin, eine Vorlage aus dieser JSON-Ressource zu erstellen. Sie können diese Vorlage programmgesteuert mit den entsprechenden Ressourcenverwaltungs-APIs, Befehlszeilentools oder innerhalb des Portals verwenden.

Zum Erstellen einer Vorlage ist es nicht erforderlich, dass Sie bis ins kleinste Detail mit der JSON-Struktur des Dashboards vertraut sind. In den meisten Fällen möchten Sie die Struktur und Konfiguration der einzelnen Kacheln beibehalten. Parametrisieren Sie dann die Gruppe der Azure-Ressourcen, auf die die Kacheln verweisen.

Sehen Sie sich das exportierte JSON-Dashboard an, und suchen Sie alle Vorkommen von Azure-Ressourcen-IDs. Das Beispieldashboard umfasst mehrere Kacheln, die alle auf einen einzelnen virtuellen Azure-Computer verweisen. Dies liegt daran, dass das Dashboard nur diese einzelne Ressource umfasst. Wenn Sie den JSON-Beispielcode (am Ende des Dokuments eingefügt) nach „/subscriptions“ durchsuchen, finden Sie mehrere Vorkommen dieser ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Um dieses Dashboard für alle künftig verwendeten virtuellen Computer zu veröffentlichen, parametrisieren Sie jedes Vorkommen dieser Zeichenfolge im JSON-Code.

Es gibt zwei Ansätze für APIs, die Ressourcen in Azure erstellen:

- Imperative APIs erstellen jeweils nur eine Ressource. Weitere Informationen finden Sie unter [Ressourcen](/rest/api/resources/resources).
- Ein auf Vorlagen basierendes Bereitstellungssystem, mit dem mehrere abhängige Ressourcen mit einem einzigen API-Befehl erstellt werden. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Bei einer vorlagenbasierten Bereitstellung werden Parametrisierung und Vorlagenerstellung unterstützt. Wir verwenden diesen Ansatz in diesem Artikel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmgesteuertes Erstellen eines Dashboards über die Vorlage mithilfe einer Vorlagenbereitstellung

Azure bietet die Möglichkeit, die Bereitstellung von mehreren Ressourcen zu orchestrieren. Sie erstellen eine Bereitstellungsvorlage, die die Gruppe der bereitzustellenden Ressourcen sowie die Beziehungen zwischen ihnen darstellt.  Das JSON-Format der einzelnen Ressourcen entspricht jeweils dem Format beim Erstellen der Ressourcen nacheinander. Der Unterschied besteht darin, dass mit der Vorlagensprache einige Konzepte wie Variablen, Parameter, allgemeine Funktionen usw. hinzugefügt werden. Diese erweiterte Syntax wird nur im Kontext einer Vorlagenbereitstellung unterstützt. Sie funktioniert nicht, wenn Sie mit den zuvor erwähnten imperativen APIs verwendet wird. Weitere Informationen finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md).

Die Parametrisierung muss über die Parametersyntax der Vorlage erfolgen.  Sie ersetzen alle zuvor gefundenen Instanzen der Ressourcen-ID wie im Folgenden gezeigt.

JSON-Beispieleigenschaft mit hartcodierter Ressourcen-ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

JSON-Beispieleigenschaft basierend auf Vorlagenparametern in eine parametrisierte Version konvertiert

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarieren Sie erforderliche Vorlagenmetadaten und die Parameter oben in der JSON-Vorlage wie folgt:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

Nachdem Sie die Vorlage konfiguriert haben, stellen Sie sie mit einer der folgenden Methoden bereit:

- [REST-APIs](/rest/api/resources/deployments)
- [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/group/deployment#az_group_deployment_create)
- [Vorlagenbereitstellungsseite im Azure-Portal](https://portal.azure.com/#create/Microsoft.Template)

Es folgen zwei Versionen der JSON-Darstellung für das Beispieldashboard. Die erste ist die Version, die wir im Portal erstellt haben und die bereits an eine Ressource gebunden war. Die zweite ist die Vorlagenversion, die programmgesteuert an jeden virtuellen Computer gebunden und mithilfe von Azure Resource Manager bereitgestellt werden kann.

### <a name="json-representation-of-our-example-dashboard"></a>JSON-Darstellung des Beispieldashboards

Dieses Beispiel ähnelt dem Ergebnis, das Sie sehen werden, wenn Sie diesen Artikel durchgearbeitet haben. Die Anleitungen haben die JSON-Darstellung eines Dashboards exportiert, das bereits bereitgestellt wurde. Die hartcodierten Ressourcen-IDs geben an, dass dieses Dashboard auf einen bestimmten virtuellen Azure-Computer verweist.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Vorlagendarstellung des Beispieldashboards

Die Vorlagenversion des Dashboards hat drei Parameter namens `virtualMachineName`, `virtualMachineResourceGroup` und `dashboardName` definiert.  Mit den Parametern können Sie festlegen, dass dieses Dashboard bei jeder Bereitstellung auf einen unterschiedlichen virtuellen Azure-Computer verweist. Dieses Dashboard kann programmgesteuert so konfiguriert und bereitgestellt werden, dass es auf einen beliebigen virtuellen Azure-Computer verweist. Diese Funktion können Sie testen, indem Sie die folgende Vorlage kopieren und in die [Vorlagenbereitstellungsseite des Azure-Portals](https://portal.azure.com/#create/Microsoft.Template) kopieren.

In diesem Beispiel wird ein Dashboard bereitgestellt. Über die Vorlagensprache können Sie jedoch mehrere Ressourcen bereitstellen und ein oder mehrere Dashboards neben ihnen bündeln.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Da Sie sich nun ein Beispiel für die Verwendung einer parametrisierten Vorlage zum Bereitstellen eines Dashboards angesehen haben, können Sie versuchen, die Vorlage mithilfe der [Azure Resource Manager-REST-APIs](/rest/api/), über die [Azure-Befehlszeilenschnittstelle](/cli/azure) oder mithilfe von [Azure PowerShell-Befehlen](/powershell/azure/get-started-azureps) bereitzustellen.

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Programmgesteuertes Erstellen eines Dashboards mit der Azure-Befehlszeilenschnittstelle

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- In diesen Beispielen wird das Dashboard [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json) verwendet. Achten Sie darauf, dass Sie den gesamten Inhalt in eckigen Klammern durch Ihre Werte ersetzen.

Führen Sie den Befehl [az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) aus, um basierend auf Ihrer Vorlage ein Dashboard zu erstellen:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Mit dem Befehl [az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) können Sie ein Dashboard aktualisieren:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Durch Ausführen des Befehls [az portal dashboard show](/cli/azure/portal/dashboard#az_portal_dashboard_show) können Sie die Details eines Dashboards anzeigen:

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Wenn Sie alle Dashboards für das aktuelle Abonnement anzeigen möchten, verwenden Sie den Befehl [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Sie können auch alle Dashboards für eine Ressourcengruppe anzeigen:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie [Markdown-Kacheln in Azure-Dashboards zum Anzeigen von benutzerdefinierten Inhalten verwendet werden](azure-portal-markdown-tile.md).
- Informationen zu allen Azure CLI-Befehlen für Dashboards finden Sie unter [az portal dashboard](/cli/azure/portal/dashboard).
- Erfahren Sie, wie [Einstellungen und Voreinstellungen im Azure-Portal verwaltet](set-preferences.md) werden.

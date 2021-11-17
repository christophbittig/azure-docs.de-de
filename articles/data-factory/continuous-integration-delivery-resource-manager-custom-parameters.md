---
title: Benutzerdefinierte Parameter in einer Resource Manager-Vorlage
description: Erfahren Sie, wie Sie benutzerdefinierte Parameter in einer Resource Manager-Vorlage mit Continuous Integration und Continuous Delivery in Azure Data Factory verwenden.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6584120a0a66fd1d913fdee86a24ce3d91b2555f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519665"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>Verwenden benutzerdefinierter Parameter mit der Resource Manager-Vorlage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Ihre Entwicklungsinstanz ein zugeordnetes Git-Repository aufweist, können Sie die standardmäßigen Parameter der Resource Manager-Vorlage überschreiben, die durch deren Veröffentlichung oder Export generiert wurden. Die Überschreibung der Standardparameterkonfiguration von Resource Manager kann beispielsweise in folgenden Szenarien erforderlich sein:

* Sie verwenden automatisierte CI/CD und möchten einige Eigenschaften während der Resource Manager-Bereitstellung ändern, die Eigenschaften sind standardmäßig aber nicht parametrisiert.
* Die Resource Manager-Standardvorlage ist aufgrund der Größe Ihrer Factory ungültig, da sie mehr als die maximal zulässige Parameteranzahl (256) enthält.

    In Bezug auf das Limit von 256 für benutzerdefinierte Parameter gibt es drei Optionen:    
  
    * Verwenden Sie die benutzerdefinierte Parameterdatei, und entfernen Sie Eigenschaften, die keine Parametrisierung erfordern, d. h. Eigenschaften, die einen Standardwert beibehalten und somit die Parameteranzahl verringern können.
    * Gestalten Sie die Logik im Datenfluss zur Reduzierung von Parametern um. Wenn z. B. alle Pipelineparameter denselben Wert aufweisen, können Sie stattdessen globale Parameter verwenden.
    * Teilen Sie eine Data Factory in mehrere Datenflüsse auf.

Um die Standardparameterkonfiguration von Resource Manager zu überschreiben, wechseln Sie zum Hub **Verwalten**, und wählen Sie im Abschnitt „Quellcodeverwaltung“ **ARM-Vorlage** aus. Klicken Sie im Abschnitt **ARM-Parameterkonfiguration** in „Parameterkonfiguration bearbeiten“ auf das Symbol **Bearbeiten**, um den Code-Editor für die Resource Manager-Parameterkonfiguration zu öffnen.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="Verwalten von benutzerdefinierten Parametern":::

> [!NOTE]
> Die **ARM-Parameterkonfiguration** ist nur im Git-Modus aktiviert. Im Livemodus und Data Factory-Modus ist sie derzeit deaktiviert.

Beim Erstellen einer benutzerdefinierten Resource Manager-Parameterkonfiguration wird im Stammordner Ihres Git-Branches eine Datei mit dem Namen **arm-template-parameters-definition.json** erstellt. Sie müssen exakt diesen Dateinamen verwenden.

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="Benutzerdefinierte Parameterdatei":::

Beim Veröffentlichen aus dem Kollaborationsbranch liest Data Factory diese Datei und verwendet deren Konfiguration zum Generieren der Eigenschaften, die parametrisiert werden sollen. Sollte keine Datei gefunden werden, wird die Standardvorlage verwendet.

Beim Exportieren einer Resource Manager-Vorlage liest Data Factory diese Datei aus dem Branch, an dem Sie gerade arbeiten, und nicht aus dem Kollaborationsbranch. Sie können die Datei in einem privaten Branch erstellen oder bearbeiten und Ihre Änderungen testen, indem Sie auf der Benutzeroberfläche die Option **Export ARM Template** (ARM-Vorlage exportieren) auswählen. Anschließend können Sie die Datei mit dem Kollaborationsbranch zusammenführen.

> [!NOTE]
> Durch eine benutzerdefinierte Resource Manager-Parameterkonfiguration wird das ARM-Vorlagenparameterlimit von 256 nicht geändert. Sie können in ihr die parametrisierten Eigenschaften auswählen und ihre Anzahl verringern.

## <a name="custom-parameter-syntax"></a>Benutzerdefinierte Parametersyntax

Nachstehend finden Sie einige Richtlinien, die beim Erstellen der benutzerdefinierten Parameterdatei, **arm-template-parameters-definition.json**, befolgt werden müssen. Die Datei enthält jeweils einen eigenen Abschnitt für die Entitätstypen „trigger“, „pipeline“, „linked service“, „dataset“, „integration runtime“ und „data flow“.

* Geben Sie den Eigenschaftenpfad unter dem relevanten Entitätstyp ein.
* Durch das Festlegen eines Eigenschaftennamens auf `*` geben Sie an, dass alle untergeordneten Eigenschaften parametrisiert werden sollen (nicht rekursiv, sondern nur bis zur ersten Ebene). Sie können auch Ausnahmen für diese Konfiguration angeben.
* Wenn Sie den Wert einer Eigenschaft als Zeichenfolge festlegen, geben Sie damit an, dass die Eigenschaft parametrisiert werden soll. Verwenden Sie das Format `<action>:<name>:<stype>`.
   *  `<action>` kann für eines dieser Zeichen durchgeführt werden:
      * `=` bedeutet, dass der aktuelle Wert als Standardwert für den Parameter beibehalten werden soll.
      * `-` bedeutet, dass der Standardwert für den Parameter nicht beibehalten werden soll.
      * `|` ist ein Sonderfall für Geheimnisse aus Azure Key Vault für Verbindungszeichenfolgen oder Schlüssel.
   * `<name>` ist der Name des Parameters. Wenn dieser Wert leer ist, wird der Name der Eigenschaft verwendet. Beginnt der Wert mit dem Zeichen `-`, wird der Name gekürzt. `AzureStorage1_properties_typeProperties_connectionString` wird beispielsweise in `AzureStorage1_connectionString` gekürzt.
   * `<stype>` ist der Typ des Parameters. Wenn `<stype>` leer ist, wird standardmäßig der Typ `string` verwendet. Unterstützte Werte: `string`, `securestring`, `int`, `bool`, `object`, `secureobject` und `array`.
* Wenn Sie ein Array in der Definitionsdatei angeben, bedeutet dies, dass die entsprechende Eigenschaft in der Vorlage ein Array ist. Data Factory durchläuft alle Objekte im Array anhand der Definition, die im Integration Runtime-Objekt des Arrays angegeben ist. Das zweite Objekt (eine Zeichenfolge) wird zum Namen der Eigenschaft, der bei jeder Iteration als Name für den Parameter verwendet wird.
* Eine Definition kann nicht spezifisch für eine Ressourceninstanz sein. Jede Definition gilt für alle Ressourcen dieses Typs.
* Standardmäßig werden alle sicheren Zeichenfolgen parametrisiert, z. B. Key Vault-Geheimnisse, Verbindungszeichenfolgen, Schlüssel und Token.
 
## <a name="sample-parameterization-template"></a>Exemplarische Parametrisierungsvorlage

Im folgenden Beispiel wird gezeigt, wie eine Resource Manager-Parameterkonfiguration aussehen kann:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/credentials" : {
        "properties": {
            "typeProperties": {
                "resourceId": "="
            }
        }
    }
}
```
Im Folgenden wird das Erstellen der obigen Vorlage mit einer Aufschlüsselung nach Ressourcentypen beschrieben.

### <a name="pipelines"></a>Pipelines
    
* Jede Eigenschaft im Pfad `activities/typeProperties/waitTimeInSeconds` wird parametrisiert. Jede Aktivität in einer Pipeline, die eine Eigenschaft auf Codeebene mit dem Namen `waitTimeInSeconds` enthält (z. B. die Aktivität `Wait`), wird als Zahl mit einem Standardnamen parametrisiert. Die Aktivität verfügt jedoch nicht über einen Standardwert in der Resource Manager-Vorlage. Hierbei handelt es sich um eine erforderliche Eingabe bei der Resource Manager-Bereitstellung.
* Analog dazu wird eine Eigenschaft namens `headers` (etwa in einer Aktivität vom Typ `Web`) mit dem Typ `object` (JObject) parametrisiert. Sie verfügt über einen Standardwert (gleicher Wert wie für die Quellfactory).

### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle Eigenschaften unter dem Pfad `typeProperties` werden mit ihren jeweiligen Standardwerten parametrisiert. Beispielsweise sind unter Eigenschaften vom Typ `IntegrationRuntimes` zwei Eigenschaften vorhanden: `computeProperties` und `ssisProperties`. Beide Eigenschaftentypen werden mit ihren jeweiligen Standardwerten und -typen (Objekt) erstellt.

### <a name="triggers"></a>Trigger

* Unter `typeProperties` werden zwei Eigenschaften parametrisiert. Die erste ist `maxConcurrency`. Diese Eigenschaft besitzt einen Standardwert und ist vom Typ `string`. Der Standardparametername lautet `<entityName>_properties_typeProperties_maxConcurrency`.
* Die Eigenschaft `recurrence` wird ebenfalls parametrisiert. Darunter werden alle Eigenschaften auf dieser Ebene gemäß Angabe als Zeichenfolgen mit Standardwerten und Parameternamen parametrisiert. Eine Ausnahme ist die `interval`-Eigenschaft, für die beim Parametrisieren der Typ `int` verwendet wird. An den Parameternamen ist das Suffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` angehängt. Analog dazu ist die Eigenschaft `freq` eine Zeichenfolge und wird als Zeichenfolge parametrisiert. Die Eigenschaft `freq` wird jedoch ohne Standardwert parametrisiert. Der Name wird verkürzt und mit einem Suffix versehen. Beispiel: `<entityName>_freq`.

### <a name="linkedservices"></a>LinkedServices

* Verknüpfte Dienste sind ein Sonderfall. Da verknüpfte Dienste und Datasets eine breite Palette von Typen umfassen, können Sie eine typspezifische Anpassung vornehmen. In diesem Beispiel wird für alle verknüpften Dienste vom Typ `AzureDataLakeStore` eine bestimmte Vorlage angewendet. Für alle anderen Dienste wird eine andere Vorlage angewendet (per `*`).
* Die `connectionString`-Eigenschaft wird als `securestring`-Wert parametrisiert. Sie hat keinen Standardwert. Sie weist einen verkürzten Parameternamen auf, an den das Suffix `connectionString` angehängt ist.
* Die `secretAccessKey`-Eigenschaft ist eine Eigenschaft vom Typ `AzureKeyVaultSecret` (beispielsweise in einem verknüpften Amazon S3-Dienst). Sie wird automatisch als Azure Key Vault-Geheimnis parametrisiert und aus dem konfigurierten Schlüsseltresor abgerufen. Auch der Schlüsseltresor kann parametrisiert werden.

### <a name="datasets"></a>Datasets

* Für Datasets steht zwar eine typspezifische Anpassung zur Verfügung, aber Sie können die Konfiguration durchführen, ohne dass eine explizite Konfiguration auf der Ebene \* vorhanden sein muss. Im vorherigen Beispiel werden alle Dataseteigenschaften unter `typeProperties` parametrisiert.

> [!NOTE]
> Wenn **Azure-Warnungen und -Matrizen** für eine Pipeline konfiguriert sind, werden diese derzeit nicht als Parameter für ARM-Bereitstellungen unterstützt. Befolgen Sie [Überwachen von Data Factory, Warnungen und Matrizen](./monitor-metrics-alerts.md), um die Warnungen und Matrizen in der neuen Umgebung erneut anzuwenden.
> 

## <a name="default-parameterization-template"></a>Standardvorlage für die Parametrisierung

Nachfolgend ist die aktuelle Standardvorlage für die Parametrisierung dargestellt. Falls Sie nur wenige Parameter hinzufügen müssen, ist die direkte Bearbeitung dieser Vorlage ggf. eine gute Idee, weil die vorhandene Parametrisierungsstruktur nicht verloren geht.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Beispiel: Parametrisieren der ID eines vorhandenen interaktiven Azure Databricks-Clusters

Im folgenden Beispiel wird das Hinzufügen eines einzelnen Werts zur Standardvorlage für die Parametrisierung veranschaulicht. Der Parameterdatei soll lediglich die ID eines vorhandenen interaktiven Azure Databricks-Clusters für einen verknüpften Databricks-Dienst hinzugefügt werden. Beachten Sie, dass diese Datei mit der vorherigen Datei nahezu identisch ist. Die einzige Ausnahme ist, dass unter dem Eigenschaftenfeld von `Microsoft.DataFactory/factories/linkedServices` das `existingClusterId`-Element hinzugefügt wurde.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Continuous Integration und Continuous Delivery (CI/CD) – Übersicht](continuous-integration-delivery.md)
- [Automatisieren von Continuous Integration mit Azure Pipelines-Releases](continuous-integration-delivery-automate-azure-pipelines.md)
- [Manuelles Höherstufen einer Resource Manager-Vorlage für jede Umgebung](continuous-integration-delivery-manual-promotion.md)
- [Verknüpfte Resource Manager-Vorlagen](continuous-integration-delivery-linked-templates.md)
- [Verwenden einer Hotfix-Produktionsumgebung](continuous-integration-delivery-hotfix-environment.md)
- [Beispielskript für vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md)
---
title: Erstellen Sie eine Simulation, die einen Service-Direktfehler unter Verwendung von Azure Chaos Studio mit der Azure CLI nutzt
description: Erstellen Sie eine Simulation, die einen Service-Direktfehler mit der Azure CLI nutzt
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7366cf2a8452b40830a9b73f314caeaf63368c64
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373477"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-with-the-azure-cli"></a>Erstellen Sie eine Chaos-Simulation, die einen Service-Direktfehler mit der Azure CLI verwendet

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie ein Multi-Read, Single-Write Azure Comos DB Failover mit Hilfe eines Chaos-Experiments und Azure Chaos Studio durchführen. Die Durchführung dieses Experiments kann Ihnen helfen, sich vor Datenverlusten zu schützen, wenn ein Failover-Ereignis eintritt.

Dieselben Schritte können auch für die Einrichtung und Durchführung eines Experiments für einen beliebigen dienstbedingten Fehler verwendet werden. Ein **service-direct**-Fehler wird direkt gegen eine Azure-Ressource ausgeführt, ohne dass eine Instrumentierung erforderlich ist, im Gegensatz zu agentenbasierten Fehlern, die die Installation des Chaosagenten erfordern.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Ein Azure Cosmos DB-Konto. Wenn Sie noch kein Azure Cosmos DB-Konto haben, können Sie [die folgenden Schritte ausführen, um eines zu erstellen](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Mindestens eine Lese- und eine Schreibregion für Ihr Azure Cosmos DB-Konto eingerichtet.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie die Befehlszeilenschnittstelle lieber lokal installieren und verwenden möchten, benötigen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.30. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Aktivieren Sie Chaos Studio auf Ihrem Azure Cosmos DB-Konto

Chaos Studio kann keine Fehler gegen eine Ressource injizieren, wenn diese Ressource nicht zuerst in Chaos Studio eingebunden wurde. Sie binden eine Ressource in Chaos Studio ein, indem Sie ein [Ziel und Fähigkeiten](chaos-studio-targets-capabilities.md) für die Ressource erstellen. Azure Cosmos DB-Konten haben nur einen Zieltyp (service-direkt) und eine Fähigkeit (Failover), aber andere Ressourcen können bis zu zwei Zieltypen - einen für service-direkte Fehler und einen für agentenbasierte Fehler - und viele Fähigkeiten haben.

1. Erstellen Sie ein Ziel, indem Sie `$RESOURCE_ID` die Ressourcen-ID der Ressource, die Sie einbinden möchten, durch `$TARGET_TYPE`  [ den Zieltyp, den Sie einbinden möchten ](chaos-studio-fault-providers.md) ersetzen:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Zum Beispiel, wenn Sie eine virtuelle Maschine als direktes Ziel für einen Dienst einrichten:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Erstellen Sie die Funktionen auf dem Ziel, indem Sie `$RESOURCE_ID` die Ressourcen-ID der Ressource, die Sie einbinden möchten, `$TARGET_TYPE` durch den [Zieltyp, den Sie einbinden möchten, ](chaos-studio-fault-providers.md) `$CAPABILITY` durch den [Namen der Fehlerfunktion, die Sie aktivieren möchten, ](chaos-studio-fault-library.md) ersetzen.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Zum Beispiel, wenn Sie die Fähigkeit zum Herunterfahren der virtuellen Maschine aktivieren:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

Sie haben nun Ihr Azure Cosmos DB-Konto erfolgreich in Chaos Studio eingebunden.

## <a name="create-an-experiment"></a>Erstellen eines Experiments
Wenn Ihr Azure Cosmos DB-Konto nun eingebunden ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Erstellen Sie Ihren JSON-Experimentcode, beginnend mit dem folgenden JSON-Beispiel. Ändern Sie das JSON so, dass es der Simulation entspricht, die Sie mit Hilfe der [Create Experiment API](/rest/api/chaosstudio/experiments/create-or-update) und der [Fault Library](chaos-studio-fault-library.md) durchführen möchten.

    ```json
    {
      "location": "eastus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                        "key": "readRegion",
                        "value": "East US 2"
                      }
                    ],
                    "name": "urn:csci:microsoft:cosmosDB:failover/1.0"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Erstellen Sie die Simulation mit Hilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihre Simulation. Make sure you have saved and uploaded your Simulation JSON and updated `experiment.json` it with your JSON filename.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Jede Simulation erstellt eine entsprechende systemseitig zugewiesene verwaltete Identität. Notieren Sie sich die `principalId` diese Identität in der Antwort für den nächsten Schritt.

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Erteilen Sie Ihrem Azure Cosmos DB-Konto eine Experimentierberechtigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann.

Gewähren Sie der Simulation mithilfe des folgenden Befehls Zugriff auf Ihre Ressourcen, und ersetzen Sie dabei `$EXPERIMENT_PRINCIPAL_ID` durch die principalId aus dem vorherigen Schritt und `$RESOURCE_ID` durch die Ressourcen-ID der Zielressource (in diesem Fall die  Cosmos DB instance resource ID). Ändern Sie die Rolle in die entsprechende [ integrierte Rolle für diesen Ressourcentyp](chaos-studio-fault-providers.md). Führen Sie diesen Befehl für jede Ressource aus, die in Ihrer Simulation als Ziel verwendet wird. 

```azurecli-interactive
az role assignment create --role "Cosmos DB Operator" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir, die Übersicht Ihres Azure Cosmos DB-Kontos zu öffnen und **Daten global replizieren** in einem separaten Browser-Tab aufzurufen. Durch regelmäßiges Aktualisieren während des Experiments wird der Regionstausch angezeigt.

1. Starten Sie die Simulation mithilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihre Simulation.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. Die Antwort enthält eine Status-URL, mit der Sie den Simulationsstatus während der Ausführung der Simulation abfragen können.

## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie eine Azure Cosmos DB Service-Direkt-Simulation durchgeführt haben, können Sie loslegen:
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)

---
title: Erstellen Sie ein Experiment, das einen Agent-basierten Fehler nutzt mit Azure Chaos Studio mit dem Azure CLI
description: Erstellen Sie eine Simulation, die einen agentenbasierten Fehler verwendet, und konfigurieren Sie den Chaos-Agenten mit der Azure CLI
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e2f605e06cd7d57acbe9fe421f4c1b883cc4d193
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373249"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>Erstellen Sie eine Chaos-Simulation, die einen agentenbasierten Fehler auf einer virtuellen Maschine oder eine VM-Skalierungsgruppe mit der Azure CLI verwendet

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie ein Ereignis mit hoher CPU-Leistung auf einer virtuellen Linux-Maschine mithilfe eines Chaos-Experiments und Azure Chaos Studio auslösen. Die Durchführung dieses Experiments kann Ihnen helfen, eine Anwendung vor Ressourcenmangel zu schützen.

Dieselben Schritte können für die Einrichtung und Durchführung eines Experiments für jeden agentenbasierten Fehler verwendet werden. Ein **agentbasierter** Fault erfordert die Einrichtung und Installation des Chaos-Agenten, im Gegensatz zu einem service-direkten Fault, der direkt gegen eine Azure-Ressource läuft, ohne dass eine Instrumentierung erforderlich ist.


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Einen virtuellen Computer. Wenn Sie noch keine virtuelle Maschine haben, können Sie [mit den folgenden Schritten eine erstellen](../virtual-machines/linux/quick-create-portal.md).
- Eine Netzwerkeinrichtung, die es Ihnen ermöglicht, [SSH in Ihre virtuelle Maschine](../virtual-machines/ssh-keys-portal.md)
- Eine vom Benutzer zugewiesene verwaltete Identität. Wenn Sie keine benutzerzugewiesene verwaltete Identität haben, können Sie [mit den folgenden Schritten eine erstellen](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie die Befehlszeilenschnittstelle lieber lokal installieren und verwenden möchten, benötigen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.30. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="assign-managed-identity-to-the-virtual-machine"></a>Weisen Sie der virtuellen Maschine eine Identität zu

Bevor Sie Chaos Studio auf der virtuellen Maschine einrichten, müssen Sie jeder virtuellen Maschine und/oder jede VM-Skalierungsgruppe, auf die Sie den Agenten installieren möchten, eine vom Benutzer zugewiesene verwaltete Identität `az vm identity assign` oder `az vmss identity assign`zuweisen. Ersetzen Sie `$VM_RESOURCE_ID`/`$VMSS_RESOURCE_ID` durch die Ressourcen-ID der VM, die Sie als Chaos-Ziel hinzufügen, und `$MANAGED_IDENTITY_RESOURCE_ID` durch die Ressourcen-ID der dem Benutzer zugewiesenen verwalteten Identität.

**Virtueller Computer**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**VM-Skalierungsgruppe**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Aktivieren Sie Chaos Studio auf Ihrem virtuellen Rechner

Chaos Studio kann keine Fehler gegen eine virtuelle Maschine injizieren, wenn diese virtuelle Maschine nicht zuvor in Chaos Studio eingebunden wurde. Sie binden eine virtuelle Maschine in Chaos Studio ein, indem Sie ein [Target und capabilities](chaos-studio-targets-capabilities.md) auf der Ressource erstellen und dann den Chaos Agent installieren. Virtuelle Maschinen haben zwei Zieltypen - einen, der service-direkte Fehler ermöglicht (für den kein Agent erforderlich ist), und einen, der agentenbasierte Fehler ermöglicht (für den die Installation eines Agenten erforderlich ist). Der Chaos-Agent ist eine Anwendung, die auf Ihrer virtuellen Maschine als [Erweiterung der virtuellen Maschine](../virtual-machines/extensions/overview.md) installiert wird und es Ihnen ermöglicht, Fehler in das Gastbetriebssystem zu injizieren.

### <a name="install-stress-ng-linux-only"></a>Installieren Sie stress-ng (nur Linux)

Der Chaos Studio-Agent für Linux benötigt stress-ng, eine Open-Source-Anwendung, die verschiedene Stressereignisse auf einer virtuellen Maschine auslösen kann. Sie können stress-ng installieren, indem Sie [eine Verbindung zu Ihrer virtuellen Linux-Maschine](../virtual-machines/ssh-keys-portal.md) herstellen und den entsprechenden Installationsbefehl z.B. für Ihren Paketmanager ausführen:

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

Oder:

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>Chaos-Ziel und -Fähigkeiten freischalten

Als Nächstes richten Sie auf jeder virtuellen Maschine oder jedem virtuellen Maschinen-Skalierungssatz ein Microsoft-Agentenziel ein, das die dem Benutzer zugewiesene verwaltete Identität angibt, die der Agent für die Verbindung mit Chaos Studio benötigt. In diesem Beispiel verwenden wir eine verwaltete Identität für alle VMs. Ein Ziel muss über die REST-API erstellt werden. In diesem Beispiel verwenden wir den `az rest` CLI-Befehl, um die REST-API-Aufrufe auszuführen.

1. Ändern Sie den folgenden JSON-Code, indem Sie `$USER_IDENTITY_CLIENT_ID`  durch die clientID Ihrer verwalteten Identität ersetzen, die Sie in der Azure-Portal Übersicht über die vom Benutzer zugewiesene verwaltete Identität finden, die Sie erstellt haben, und `$USER_IDENTITY_TENANT_ID` durch Ihre Azure-Mandanten-ID, die Sie im Azure-Portal unter **Azure Active Directory** unter **Mandanteninformationen** finden. Speichern Sie den JSON-Code als Datei an dem Speicherort, an dem Sie die Azure CLI ausführen (in Cloud Shell können Sie die JSON-Datei per Drag and Drop hochladen).

    ```json
    {
      "properties": {
        "identities": [
          {
            "clientId": "$USER_IDENTITY_CLIENT_ID",
            "tenantId": "$USER_IDENTITY_TENANT_ID",
            "type": "AzureManagedIdentity"
          }
        ]
      }
    }
    ```

2. Erstellen Sie das Ziel, indem Sie`$RESOURCE_ID` durch die Ressourcen-ID des virtuellen Zielcomputers oder der VM-Skalierungsgruppe ersetzen. Ersetzen Sie `target.json` durch den Namen der im vorherigen Schritt erstellten JSON Datei.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. Kopieren Sie die GUID für die **agentProfileId,** die von diesem Befehl zur Verwendung in einem späteren Schritt zurückgegeben wird.

4. Erstellen Sie die Funktionen auf dem Ziel, indem Sie wie unten gezeigt eine Datei mit dem Namen capability.json erstellen. Speichern Sie die JSON-Datei an dem Speicherort, an dem Sie die Azure CLI ausführen. Ersetzen Sie `$RESOURCE_ID` durch die Ressourcen-ID des virtuellen Zielcomputers oder der VM-Skalierungsgruppe und `$CAPABILITY` durch den Namen der [Fehlerfunktion,](chaos-studio-fault-library.md)die Sie aktivieren.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Wenn Sie z. B. die CPU-Auslastungsfunktion aktivieren:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>Installieren der Chaos Studio-VM-Erweiterung

Der Chaos-Agent ist eine Anwendung, die in Ihren VM- oder VMSS ausgeführt wird, um Agent-basierte Fehler auszuführen. Während der Installation konfigurieren Sie den Agent mit der verwalteten Identität, die der Agent für die Authentifizierung bei Chaos Studio verwenden soll, der Profil-ID des von Ihnen erstellten Microsoft-Agent Ziels und optional einem Application Insights Instrumentationsschlüssel, mit dem der Agent Diagnoseereignisse an Azure-Anwendung Insights senden kann.

1. Bevor Sie beginnen, stellen Sie sicher, dass Sie über die folgenden Details verfügen:
    * **agentProfileId:** Die Eigenschaft, die beim Erstellen des Ziels zurückgegeben wird. Wenn Sie nicht über diese Eigenschaft verfügen, können Sie die Eigenschaft ausführen `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` und `agentProfileId` kopieren.
    * Notieren Sie sich auf jeden Fall die **Client-ID** der benutzerseitig zugewiesenen verwalteten Identität. Wenn Sie nicht über diese Eigenschaft verfügen, können Sie die Eigenschaft ausführen `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` und `clientId` kopieren
    * (optional) **AppInsightsKey:** Der Instrumentierungsschlüssel für Ihre Application Insights-Komponente, den Sie im Portal auf der Seite Application Insights unter **Essentials** finden.

2. Installieren Sie die Chaos Studio-VM-Erweiterung. Ersetzen Sie `$VM_RESOURCE_ID` durch die Ressourcen-ID Ihres virtuellen Computers, oder ersetzen Sie `$SUBSCRIPTION_ID` ,`$RESOURCE_GROUP`, und `$VMSS_NAME`durch diese Eigenschaften für Ihre VM-Skalierungsgruppe. Ersetzen Sie `$AGENT_PROFILE_ID` durch die agentProfileId, `$USER_IDENTITY_CLIENT_ID` und die clientID Ihrer verwalteten Identität und `$APP_INSIGHTS_KEY` durch Ihren Application Insights Instrumentationsschlüssel. Wenn Sie Application Insights nicht verwenden, entfernen Sie dieses Schlüssel-Wert-Paar.

    #### <a name="install-the-agent-on-a-virtual-machine"></a>Installieren Sie den Agent auf der virtuellen Maschine

    **Windows**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    #### <a name="install-the-agent-on-a-virtual-machine-scale-set"></a>Installieren Sie den Agent auf der VMSS

    **Windows**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```
3. Wenn Sie eine VMSS einrichten, überprüfen Sie, ob die Instanzen auf das neueste Modell aktualisiert wurden. 

## <a name="create-an-experiment"></a>Erstellen eines Experiments

Wenn Ihre virtuelle Maschine nun an Bord ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Erstellen Sie Ihren JSON-Experimentcode, beginnend mit dem folgenden JSON-Beispiel. Ändern Sie das JSON so, dass es der Simulation entspricht, die Sie mit Hilfe der [Create Experiment API](/rest/api/chaosstudio/experiments/create-or-update) und der [Fault Library](chaos-studio-fault-library.md) durchführen möchten.

    ```json
    {
      "identity": {
        "type": "SystemAssigned"
      },
      "location": "centralus",
      "properties": {
        "selectors": [
          {
            "id": "Selector1",
            "targets": [
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myWindowsVM/providers/Microsoft.Chaos/targets/microsoft-agent",
                "type": "ChaosTarget"
              },
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myLinuxVM/providers/Microsoft.Chaos/targets/micosoft-agent",
                "type": "ChaosTarget"
              }
            ],
            "type": "List"
          }
        ],
        "steps": [
          {
            "branches": [
              {
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ],
                "name": "Branch 1"
              }
            ],
            "name": "Step 1"
          }
        ]
      }
    }
    ```

    Wenn Sie mit einem VMSS arbeiten, ändern Sie die Fehlerparameter so, dass sie die Nummer(n) der Instanz(en) enthalten, auf die Sie abzielen:

    ```json
    "parameters": [
      {
        "key": "pressureLevel",
        "value": "95"
      },
      {
        "key": "virtualMachineScaleSetInstances",
        "value": "[0,1,2]"
      }
    ]
    ```

    Sie können Skalierungssatzinstanznummern im Azure-Portal identifizieren, indem Sie zu Ihrem VM-Skalierungssatz navigieren und auf **Instanzen** klicken. Der Instanzname endet mit der Instanznummer.
    
2. Erstellen Sie die Simulation mit Hilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihre Simulation. Make sure you have saved and uploaded your Simulation JSON and updated `experiment.json` it with your JSON filename.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Jede Simulation erstellt eine entsprechende systemseitig zugewiesene verwaltete Identität. Notieren Sie sich die `principalId` diese Identität in der Antwort für den nächsten Schritt.

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Erteilen Sie Ihrer virtuellen Maschine eine Experimentiergenehmigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann. Die Leserrolle ist für Agent-basierte Fehler erforderlich. Andere Rollen, die nicht über die Berechtigung "*/Read" verfügen, z. B. "Mitwirkender für virtuelle Computer", erhalten keine entsprechende Berechtigung für Agent-basierte Fehler.

Gewähren Sie dem Experiment mithilfe des folgenden Befehls Zugriff auf Ihren virtuellen Computer oder Ihre VMSS. Ersetzen Sie dabei `$EXPERIMENT_PRINCIPAL_ID`durch die principalId aus dem vorherigen Schritt und `$RESOURCE_ID`durch die Ressourcen-ID des virtuellen Zielcomputers oder der VMSS (die Ressourcen-ID des virtuellen Computers, nicht die Ressourcen-ID des Chaos-Agents, der in der Simulationsdefinition verwendet wird). Führen Sie diesen Befehl für jeden virtuellen Computer oder jeden VM-Skalierungssatz aus, der bzw. die in Ihrer Simulation als Ziel verwendet wird.

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir, [ein Azure Monitor-Metriken-Diagramm](../azure-monitor/essentials/tutorial-metrics.md) mit der CPU-Auslastung Ihrer virtuellen Maschine in einem separaten Browser-Tab zu öffnen.

1. Starten Sie die Simulation mithilfe der Azure CLI, und ersetzen Sie `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` und `$EXPERIMENT_NAME` durch die Eigenschaften für Ihre Simulation.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. Die Antwort enthält eine Status-URL, mit der Sie den Simulationsstatus während der Ausführung der Simulation abfragen können.

## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie ein agentenbasiertes Experiment durchgeführt haben, können Sie loslegen:
- [Erstellen Sie ein Experiment, das dienstbezogene Fehler verwendet](chaos-studio-tutorial-service-direct-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)
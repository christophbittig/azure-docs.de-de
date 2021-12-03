---
title: Verwenden von REST zum Verwalten von ML-Ressourcen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie REST-APIs zum Erstellen, Ausführen und Löschen von Azure Machine Learning-Ressourcen, z. B. einen Arbeitsbereich, oder zum Registrieren von Modellen verwenden.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 9465a8c45dd44eca4fcd67a8603e60a2061f2609
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518183"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Erstellen, Ausführen und Löschen von Azure ML-Ressourcen mithilfe von REST



Es gibt verschiedene Möglichkeiten zur Verwaltung Ihrer Azure ML-Ressourcen. Sie können das [-Portal](https://portal.azure.com/), die [Befehlszeilenschnittstelle (CLI)](/cli/azure) oder das [Python SDK](/python/api/overview/azure/ml/intro) verwenden. Sie können auch die REST-API verwenden. Die REST-API verwendet HTTP-Verben in einer Standardmethode zum Erstellen, Abrufen, Aktualisieren und Löschen von Ressourcen. Die REST-API funktioniert mit jeder Sprache oder jedem Tool, die bzw. das HTTP-Anforderungen ausführen kann. Durch die unkomplizierte Struktur von REST sind diese APIs häufig eine gute Wahl in Skriptumgebungen sowie für MLOps-Automatisierung. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Abrufen eines Autorisierungstokens
> * Erstellen einer ordnungsgemäß formatierten REST-Anforderung mithilfe der Dienstprinzipalauthentifizierung
> * Verwenden von GET-Anforderungen zum Abrufen von Informationen zu den hierarchischen Ressourcen von Azure ML
> * Verwenden von PUT- und POST-Anforderungen zum Erstellen und Ändern von Ressourcen
> * Verwenden von PUT-Anforderungen zum Erstellen von Azure ML-Arbeitsbereichen
> * Verwenden von DELETE-Anforderungen zum Bereinigen von Ressourcen 

## <a name="prerequisites"></a>Voraussetzungen

- Ein **Azure-Abonnement**, für das Sie über Administratorrechte verfügen. Wenn Sie nicht über ein solches Abonnement verfügen, testen Sie das [kostenlose oder kostenpflichtige persönliche Abonnement](https://azure.microsoft.com/free/).
- Ein [Azure Machine Learning-Arbeitsbereich](./how-to-manage-workspace.md).
- Administrative REST-Anforderungen verwenden Dienstprinzipalauthentifizierung. Führen Sie die Schritte in [Einrichten von Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](./how-to-setup-authentication.md#service-principal-authentication) zum Erstellen eines Dienstprinzipals in Ihrem Arbeitsbereich aus.
- Das **curl**-Hilfsprogramm. Das **curl**-Programm ist im [Windows-Subsystem für Linux](/windows/wsl/install-win10) oder jeder beliebigen UNIX-Distribution verfügbar. In PowerShell ist **curl** ein Alias für **Invoke-WebRequest**, und `curl -d "key=val" -X POST uri` wird zu `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Abrufen eines Tokens für die Dienstprinzipalauthentifizierung

Administrative REST-Anforderungen werden mit einem impliziten OAuth2-Flow authentifiziert. Dieser Authentifizierungsflow verwendet ein Token, das vom Dienstprinzipal Ihres Abonnements bereitgestellt wird. Um dieses Token abzurufen, benötigen Sie Folgendes:

- Ihre Mandanten-ID (identifiziert die Organisation, zu der Ihr Abonnement gehört)
- Ihre Client-ID (die dem erstellten Token zugeordnet wird)
- Ihr geheimer Clientschlüssel (den Sie schützen sollten)

Sie sollten diese Werte von der Antwort auf die Erstellung Ihres Dienstprinzipals erhalten. Das Abrufen dieser Werte wird in [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](./how-to-setup-authentication.md#service-principal-authentication) erläutert. Wenn Sie das Abonnement Ihres Unternehmens verwenden, verfügen Sie möglicherweise nicht über die Berechtigung zum Erstellen eines Dienstprinzipals. In diesem Fall sollten Sie ein [kostenloses oder kostenpflichtiges persönliches Abonnement](https://azure.microsoft.com/free/) verwenden.

So rufen Sie ein Token ab:

1. Öffnen Sie ein Terminalfenster.
1. Geben Sie den folgenden Code in der Befehlszeile ein:
1. Ersetzen Sie `<YOUR-TENANT-ID>`, `<YOUR-CLIENT-ID>`, und `<YOUR-CLIENT-SECRET>` durch Ihre eigenen Werte. In diesem Artikel sind Zeichenfolgen, die von spitzen Klammern umgeben sind, Variablen, die Sie durch Ihre entsprechenden eigenen Werte ersetzen müssen.
1. Führen Sie den folgenden Befehl aus:

```bash
curl -X POST https://login.microsoftonline.com/<YOUR-TENANT-ID>/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=<YOUR-CLIENT-ID>&client_secret=<YOUR-CLIENT-SECRET>" \
```

Die Antwort sollte ein Zugriffstoken bereitstellen, das eine Stunde lang gültig ist:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "YOUR-ACCESS-TOKEN"
}
```

Notieren Sie sich das Token, da Sie es verwenden, um alle zusätzlichen administrativen Anforderungen zu authentifizieren. Hierzu legen Sie einen Autorisierungsheader in allen Anforderungen fest:

```bash
curl -h "Authorization:Bearer <YOUR-ACCESS-TOKEN>" ...more args...
```

> [!NOTE]
> Der Wert beginnt mit der Zeichenfolge „Bearer“, einschließlich eines einzelnen Leerzeichens, bevor Sie das Token hinzufügen.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Abrufen einer Liste aller Ressourcengruppen, die Ihrem Abonnement zugeordnet sind

Um eine Liste aller Ressourcengruppen abzurufen, die Ihrem Abonnement zugeordnet sind, führen Sie Folgendes aus:

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups?api-version=2021-03-01-preview -H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

In Azure werden viele REST-APIs veröffentlicht. Jeder Dienstanbieter aktualisiert seine API in seinem eigenen Rhythmus, ohne jedoch vorhandene Programme funktionsunfähig zu machen. Der Dienstanbieter verwendet das `api-version`-Argument, um Kompatibilität zu gewährleisten. Das `api-version`-Argument variiert von Dienst zu Dienst. Für Machine Learning Service ist die aktuelle API-Version beispielsweise `2021-03-01-preview`. Für Speicherkonten ist dies `2019-08-01`. Für Schlüsseltresore ist dies `2019-09-01`. Alle REST-Aufrufe sollten das `api-version`-Argument auf den erwarteten Wert festlegen. Sie können sich auf die Syntax und Semantik der angegebenen Version verlassen, auch wenn die API weiterentwickelt wird. Wenn Sie eine Anforderung ohne das `api-version`-Argument an einen Anbieter senden, enthält die Antwort eine von Menschen lesbare Liste unterstützter Werte. 

Der Aufruf oben führt zu einer komprimierten JSON-Antwort der folgenden Form: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Drilldown in Arbeitsbereiche und deren Ressourcen

Um den Satz von Arbeitsbereichen in einer Ressourcengruppe abzurufen, führen Sie Folgendes aus, und ersetzen dabei `<YOUR-SUBSCRIPTION-ID>`, `<YOUR-RESOURCE-GROUP>` und `<YOUR-ACCESS-TOKEN>`: 

```
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Sie erhalten erneut eine JSON-Liste, die dieses Mal eine Liste enthält, in der jedes Element einen Arbeitsbereich beschreibt:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Zum Arbeiten mit Ressourcen in einem Arbeitsbereich wechseln Sie vom allgemeinen **management.azure.com**-Server zu einem REST-API-Server, der für den Speicherort des Arbeitsbereichs spezifisch ist. Beachten Sie den Wert des `discoveryUrl`-Schlüssels in der JSON-Antwort oben. Wenn Sie diese URL mit GET abrufen, erhalten Sie eine Antwort ähnlich der folgenden:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Der Wert der `api`-Antwort ist die URL des Servers, den Sie für weitere Anforderungen verwenden. Zum Auflisten von Experimenten senden Sie beispielsweise den folgenden Befehl. Ersetzen Sie `REGIONAL-API-SERVER` durch den Wert der `api`-Antwort (z. B. `centralus.api.azureml.ms`). Ersetzen Sie außerdem `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` und `YOUR-ACCESS-TOKEN` wie üblich:

```bash
curl https://<REGIONAL-API-SERVER>/history/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/experiments?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Zum Abrufen registrierter Modelle in Ihrem Arbeitsbereich senden Sie auf ähnliche Weise Folgendes:

```bash
curl https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Beachten Sie, dass der Pfad zum Auflisten von Experimenten mit `history/v1.0` beginnt, während der Pfad mit `modelmanagement/v1.0` zum Auflisten von Modellen beginnt. Die REST-API ist in verschiedene Betriebsgruppen unterteilt, die jeweils über einen eindeutigen Pfad verfügen. 

|Bereich|`Path`|
|-|-|
|Artifacts|/rest/api/azureml|
|Datenspeicher|/azure/machine-learning/how-to-access-data|
|Hyperparameteroptimierung|hyperdrive/v1.0/|
|Modelle|modelmanagement/v1.0/|
|Ausführungsverlauf|execution/v1.0/ and history/v1.0/|

Sie können die REST-API unter Verwendung des allgemeinen Musters untersuchen:

|URL-Komponente|Beispiel|
|-|-|
| https://| |
| REGIONAL-API-SERVER/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/IHRE-ABONNEMENT-ID/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/IHRE-RESSOURCENGRUPPE/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Erstellen und Ändern von Ressourcen mit PUT- und POST-Anforderungen

Zusätzlich zum Abrufen von Ressourcen mit dem GET-Verb unterstützt die REST-API die Erstellung aller Ressourcen, die zum Trainieren, Bereitstellen und Überwachen von ML-Lösungen erforderlich sind. 

Zum Trainieren und Ausführen von ML-Modellen sind Computeressourcen erforderlich. Sie können die Computeressourcen eines Arbeitsbereichs folgendermaßen auflisten: 

```bash
curl https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes?api-version=2021-03-01-preview \
-H "Authorization:Bearer <YOUR-ACCESS-TOKEN>"
```

Um eine benannte Computeressource zu erstellen oder zu überschreiben, verwenden Sie eine PUT-Anforderung. Im Folgenden werden zusätzlich zu den jetzt bekannten Ersetzungen von `YOUR-SUBSCRIPTION-ID`, `YOUR-RESOURCE-GROUP`, `YOUR-WORKSPACE-NAME` und `YOUR-ACCESS-TOKEN` auch `YOUR-COMPUTE-NAME` und die Werte für `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName` und `adminUserPassword` ersetzt. Der folgende Befehl erstellt eine dedizierte Standard_D1 mit einem Knoten (eine grundlegende CPU-Computeressource), die nach 30 Minuten herunterskaliert wird, wie in der Referenz unter [Machine Learning Compute: Erstellen oder Aktualisieren des SDK-Verweises](/rest/api/azureml/workspaces/createorupdate) angegeben:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/computes/<YOUR-COMPUTE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "<ADMIN_USERNAME>",
        "adminUserPassword": "<ADMIN_PASSWORD>"
    }
}'
```

> [!Note]
> In Windows-Terminals müssen Sie die doppelten Anführungszeichen beim Senden von JSON-Daten möglicherweise mit Escapezeichen versehen. Das heißt, Text wie `"location"` wird zu `\"location\"`. 

Eine erfolgreiche Anforderung erhält eine `201 Created`-Antwort, aber beachten Sie, dass diese Antwort einfach nur bedeutet, dass der Bereitstellungsvorgang begonnen hat. Sie müssen eine Abfrage (oder das Portal) verwenden, um den erfolgreichen Abschluss zu bestätigen.

## <a name="create-a-workspace-using-rest"></a>Erstellen eines Arbeitsbereichs mithilfe von REST 

Jeder Azure ML-Arbeitsbereich ist von vier anderen Azure-Ressourcen abhängig: einer Azure Container Registry-Ressource, Azure Key Vault, Azure Application Insights und einem Azure Storage-Konto. Sie können erst einen Arbeitsbereich erstellen, wenn diese Ressourcen vorhanden sind. Weitere Informationen zum Erstellen dieser Ressourcen finden Sie in der REST-API-Referenz.

Um einen Arbeitsbereich zu erstellen, senden Sie einen ähnlichen Befehl wie den folgenden mit PUT an `management.azure.com`. Auch wenn dieser Aufruf erfordert, dass Sie eine große Anzahl von Variablen festlegen, ist er strukturell identisch mit anderen Aufrufen, die in diesem Artikel erläutert wurden. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity" : {
        "type" : "systemAssigned"
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

Sie sollten eine `202 Accepted`-Antwort erhalten und in den zurückgegebenen Headern einen `Location`-URI. Sie können diesen URI für Informationen zur Bereitstellung mit GET abrufen, einschließlich hilfreicher Debuginformationen, wenn ein Problem mit einer ihrer abhängigen Ressourcen vorliegt (wenn Sie beispielsweise vergessen haben, Administratorzugriff für Ihre Containerregistrierung zu aktivieren). 

## <a name="create-a-workspace-using-a-user-assigned-managed-identity"></a>Erstellen eines Arbeitsbereichs mit einer benutzerseitig zugewiesenen verwalteten Identität 

Beim Erstellen eines Arbeitsbereichs können Sie eine benutzerseitig zugewiesene verwaltete Identität angeben, die für den Zugriff auf die zugehörigen Ressourcen verwendet werden soll: ACR, KeyVault, Storage und App Insights. Verwenden Sie den folgenden Anforderungstext, um einen Arbeitsbereich mit einer benutzerseitig zugewiesenen verwalteten Identität zu erstellen. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "AZURE-LOCATION>",
    "identity": {
      "type": "SystemAssigned,UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ManagedIdentity/userAssignedIdentities/<YOUR-MANAGED-IDENTITY>": {}
      }
    },
    "properties": {
        "friendlyName" : "<YOUR-WORKSPACE-FRIENDLY-NAME>",
        "description" : "<YOUR-WORKSPACE-DESCRIPTION>",
        "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
        keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
        "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
        "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>"
    }
}'
```

## <a name="create-a-workspace-using-customer-managed-encryption-keys"></a>Erstellen eines Arbeitsbereichs mit kundenseitig verwalteten Verschlüsselungsschlüsseln

Standardmäßig werden Metadaten für den Arbeitsbereich auf einer Azure Cosmos DB-Instanz gespeichert, die von Microsoft verwaltet wird. Diese Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt. Anstatt den von Microsoft verwalteten Schlüssel zu verwenden, können Sie auch Ihren eigenen Schlüssel bereitstellen. Dadurch wird ein [zusätzlicher Satz von Ressourcen](/azure/machine-learning/concept-data-encryption#azure-cosmos-db) in Ihrem Azure-Abonnement erstellt, um Ihre Daten zu speichern.

Zum Erstellen eines Arbeitsbereichs, der Ihre Schlüssel zur Verschlüsselung verwendet, müssen Sie die folgenden Voraussetzungen erfüllen:

* Der Azure Machine Learning-Dienstprinzipal muss über Zugriff als Mitwirkender auf Ihr Azure-Abonnement verfügen.
* Sie müssen über einen vorhandenen Azure Key Vault verfügen, der einen Verschlüsselungsschlüssel enthält.
* Der Azure Key Vault muss sich in derselben Azure-Region befinden, in der Sie den Azure Machine Learning-Arbeitsbereich erstellen möchten.
* Für den Azure Key Vault muss das vorläufige Löschen und der Bereinigungsschutz aktiviert sein, um vor Datenverlusten im Falle eines versehentlichen Löschvorgangs zu schützen.
* Sie müssen über eine Zugriffsrichtlinie im Azure Key Vault verfügen, die get-, wrap- und unwrap-Zugriff auf die Azure Cosmos DB-Anwendung gewährt.

Verwenden Sie den folgenden Anforderungstext, um Arbeitsbereiche zu erstellen, die eine benutzerseitig zugewiesene verwaltete Identität und kundenseitig verwaltete Schlüssel für die Verschlüsselung verwenden. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität für den Arbeitsbereich verwenden, legen Sie auch die `userAssignedIdentity`-Eigenschaft auf die Ressourcen-ID der verwalteten Identität fest.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-NEW-WORKSPACE-NAME>?api-version=2021-03-01-preview' \
  -H 'Authorization: Bearer <YOUR-ACCESS-TOKEN>' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "eastus2euap",
    "identity": {
      "type": "SystemAssigned"
    },
    "properties": {
      "friendlyName": "<YOUR-WORKSPACE-FRIENDLY-NAME>",
      "description": "<YOUR-WORKSPACE-DESCRIPTION>",
      "containerRegistry" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.ContainerRegistry/registries/<YOUR-REGISTRY-NAME>",
      "keyVault" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>\
/providers/Microsoft.Keyvault/vaults/<YOUR-KEYVAULT-NAME>",
      "applicationInsights" : "subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.insights/components/<YOUR-APPLICATION-INSIGHTS-NAME>",
      "storageAccount" : "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.Storage/storageAccounts/<YOUR-STORAGE-ACCOUNT-NAME>",
      "encryption": {
        "status": "Enabled",
        "identity": {
          "userAssignedIdentity": null
        },      
        "keyVaultProperties": {
           "keyVaultArmId": "/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/\
providers/Microsoft.KeyVault/vaults/<YOUR-VAULT>",
           "keyIdentifier": "https://<YOUR-VAULT>.vault.azure.net/keys/<YOUR-KEY>/<YOUR-KEY-VERSION>",
           "identityClientId": ""
        }
      },
      "hbiWorkspace": false
    }
}'
```

### <a name="delete-resources-you-no-longer-need"></a>Löschen von Ressourcen, die nicht mehr benötigt werden

Einige, aber nicht alle Ressourcen unterstützen das DELETE-Verb. Überprüfen Sie die [API-Referenz](/rest/api/azureml/), bevor Sie für Löschanwendungsfälle die REST-API verwenden. Zum Löschen eines Modells können Sie beispielsweise Folgendes verwenden:

```bash
curl
  -X DELETE \
'https://<REGIONAL-API-SERVER>/modelmanagement/v1.0/subscriptions/<YOUR-SUBSCRIPTION-ID>/resourceGroups/<YOUR-RESOURCE-GROUP>/providers/Microsoft.MachineLearningServices/workspaces/<YOUR-WORKSPACE-NAME>/models/<YOUR-MODEL-ID>?api-version=2021-03-01-preview' \
  -H 'Authorization:Bearer <YOUR-ACCESS-TOKEN>' 
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Verschieben des Arbeitsbereichs

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

### <a name="deleting-the-azure-container-registry"></a>Löschen der Azure Container Registry

Der Azure Machine Learning-Arbeitsbereich verwendet für einige Operationen die Azure Container Registry (ACR). Es wird automatisch eine ACR-Instanz erstellt, wenn erstmals eine erforderlich ist.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erkunden der vollständigen [AzureML REST-API-Referenz](/rest/api/azureml/).
- Lernen Sie, wie Sie den Designer zum [Prognostizieren von Automobilpreisen mit dem Designer](./tutorial-designer-automobile-price-train-score.md) verwenden.
- Erkunden von [Azure Machine Learning mit Jupyter-Notebooks](..//machine-learning/samples-notebooks.md).

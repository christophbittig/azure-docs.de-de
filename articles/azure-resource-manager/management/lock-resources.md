---
title: Sperren von Ressourcen, um Änderungen zu verhindern
description: Verhindern Sie, dass Benutzer Azure-Ressourcen aktualisieren oder löschen, indem Sie eine Sperre für alle Benutzer und Rollen anwenden.
ms.topic: conceptual
ms.date: 07/01/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 324aed15446e83e0853f4b590c7d679a7f598abe
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491391"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Sperren von Ressourcen, um unerwartete Änderungen zu verhindern

Als Administrator können Sie ein Abonnement, eine Ressourcengruppe oder eine Ressource sperren, um zu verhindern, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Die Sperre setzt alle Berechtigungen außer Kraft, die dem Benutzer möglicherweise erteilt wurden.

Sie können die Sperrebene auf **CanNotDelete** oder **ReadOnly** festlegen. Im Portal heißen die Sperren **Löschen** und **Schreibgeschützt**.

- **CanNotDelete** bedeutet, dass autorisierte Benutzer weiterhin eine Ressource lesen und ändern, aber nicht löschen können.
- **ReadOnly** bedeutet, dass autorisierte Benutzer eine Ressource zwar lesen, aber nicht löschen oder aktualisieren können. Mit dieser Sperre erzielen Sie einen ähnlichen Effekt wie durch die Beschränkung sämtlicher autorisierter Benutzer auf die Berechtigungen der **Leserolle**.

Im Gegensatz zur rollenbasierten Zugriffssteuerung verwenden Sie Verwaltungssperren, um eine Einschränkung für alle Benutzer und Rollen zu aktivieren. Informationen zum Festlegen von Benutzer- und Rollenberechtigungen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

## <a name="lock-inheritance"></a>Vererbung von Sperren

Wenn Sie eine Sperre in einem übergeordneten Bereich anwenden, erben alle Ressourcen in diesem Bereich die entsprechende Sperre. Auch Ressourcen, die Sie später hinzufügen, erben die Sperre aus dem übergeordneten Element. Die restriktivste Sperre in der Vererbung hat Vorrang.

## <a name="understand-scope-of-locks"></a>Grundlegendes zum Umfang von Sperren

> [!NOTE]
> Ein grundlegender Aspekt von Sperren besteht darin, dass sie nicht für alle Arten von Vorgängen gelten. Azure-Vorgänge können in zwei Kategorien unterteilt werden: auf Steuerungsebene und auf Datenebene. **Sperren gelten nur für Vorgänge der Steuerungsebene**.

Vorgänge der Steuerungsebene werden an `https://management.azure.com` übermittelt. Vorgänge der Datenebene sind Vorgänge, die an Ihre Instanz eines Dienst gesendet werden, z. B. an `https://myaccount.blob.core.windows.net/`. Weitere Informationen finden Sie unter [Steuerungsebene und Datenebene von Azure](control-plane-and-data-plane.md). Informationen dazu, welche Vorgänge die URL der Steuerungsebene verwenden, finden Sie unter [Azure REST-API](/rest/api/azure/).

Dieser Unterschied bedeutet, dass Sperren Änderungen an einer Ressource verhindern, aber nicht einschränken, wie Ressourcen ihre eigenen Funktionen ausführen.  Beispielsweise verhindert eine ReadOnly-Sperre auf einem logischen SQL-Datenbankserver das Löschen oder Ändern des Servers. Sie verhindert jedoch nicht das Erstellen, Aktualisieren oder Löschen von Daten in den Datenbanken auf diesem Server. Datentransaktionen sind zulässig, da diese Vorgänge nicht an `https://management.azure.com` gesendet werden.

Weitere Beispiele zu den Unterschieden zwischen Vorgängen der Steuerungs- und Datenebenen finden Sie im nächsten Abschnitt.

## <a name="considerations-before-applying-locks"></a>Überlegungen vor der Anwendung von Sperren

Das Anwenden von Sperren kann zu unerwarteten Ergebnissen führen, da einige Vorgänge, die die Ressource nicht zu ändern scheinen, tatsächlich Aktionen erfordern, die von der Sperre blockiert werden. Sperren verhindern alle Vorgänge, für die eine POST-Anforderung an die Azure Resource Manager-API erforderlich ist. Einige gängige Beispiele für die Vorgänge, die durch Sperren blockiert werden, sind:

- Eine Schreibschutzsperre für ein **Speicherkonto** hindert Benutzer am Auflisten der Kontoschlüssel. Der Vorgang Azure Storage [List Keys](/rest/api/storagerp/storageaccounts/listkeys) wird durch eine Post-Anforderung verarbeitet, um den Zugriff auf die Kontoschlüssel zu schützen, die den gesamten Zugriff auf die Daten im Speicherkonto ermöglichen. Wenn eine Schreibschutzsperre für ein Speicherkonto konfiguriert ist, müssen Benutzer, die nicht über die Kontoschlüssel verfügen, Azure AD-Anmeldeinformationen verwenden, um auf Blob- oder Warteschlangendaten zuzugreifen. Eine Schreibschutzsperre verhindert auch die Zuweisung von Azure RBAC-Rollen, die auf das Speicherkonto oder einen Datencontainer (Blobcontainer oder Warteschlange) beschränkt sind.

- Eine Löschschutzsperre für ein **Speicherkonto** verhindert nicht, dass Daten innerhalb dieses Kontos gelöscht oder geändert werden. Diese Art von Sperre verhindert nur, dass das Konto selbst gelöscht wird. Wenn eine Anforderung [Vorgänge auf Datenebene](control-plane-and-data-plane.md#data-plane) verwendet, schützt die Sperre des Speicherkontos keine Blob-, Warteschlangen-, Tabellen- oder Dateidaten innerhalb dieses Speicherkontos. Wenn die Anforderung jedoch [Vorgänge auf Steuerungsebene](control-plane-and-data-plane.md#control-plane) verwendet, schützt die Sperre diese Ressourcen.

  Wenn eine Anforderung z. B. [Dateifreigaben – Löschen](/rest/api/storagerp/file-shares/delete) verwendet, was ein Vorgang auf Steuerungsebene ist, wird der Löschvorgang verweigert. Wenn die Anforderung [Freigabe löschen](/rest/api/storageservices/delete-share) verwendet, was ein Vorgang auf Datenebene ist, ist der Löschvorgang erfolgreich. Es wird empfohlen, die Vorgänge auf Steuerungsebene zu verwenden.

- Eine Schreibschutzsperre für ein **Speicherkonto** verhindert nicht, dass Daten innerhalb dieses Kontos gelöscht oder geändert werden. Durch diese Art von Schutz wird nur das Speicherkonto selbst vor dem Löschen oder Ändern geschützt. Blob-, Warteschlangen-, Tabellen- oder Dateidaten in diesem Speicherkonto werden nicht geschützt.

- Das Festlegen einer Schreibschutzsperre für eine **App Service**-Ressource verhindert, dass der Server-Explorer von Visual Studio Dateien für die Ressource anzeigen kann, da für diese Interaktion Schreibzugriff erforderlich ist.

- Durch eine Schreibschutzsperre für eine **Ressourcengruppe**, die einen **App Service-Plan** enthält, werden Sie am [Hochskalieren Ihres Tarifs](../../app-service/manage-scale-up.md) gehindert.

- Eine Schreibschutzsperre für eine **Ressourcengruppe**, die einen **virtuellen Computer** enthält, hindert alle Benutzer am Starten bzw. Neustarten des virtuellen Computers. Diese Vorgänge erfordern eine POST-Anforderung.

- Eine Löschschutzsperre für eine **Ressourcengruppe** verhindert, dass mit Azure Resource Manager automatisch Bereitstellungen aus dem Verlauf [gelöscht](../templates/deployment-history-deletions.md) werden. Wenn im Verlauf 800 Bereitstellungen erreicht werden, treten bei weiteren Bereitstellungen Fehler auf.

- Eine vom **Azure Backup-Dienst** erstellte Löschschutzsperre für die **Ressourcengruppe** führt dazu, dass Sicherungen fehlschlagen. Der Dienst unterstützt maximal 18 Wiederherstellungspunkte. Bei einer Sperrung kann der Sicherungsdienst Wiederherstellungspunkte nicht bereinigen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Sichern von Azure-VMs](../../backup/backup-azure-vm-backup-faq.yml).

- Eine Nichtlöschsperre für eine **Resourcengruppe** verhindert, dass **Azure Machine Learning** [Azure Machine Learning-Computeclusters](../../machine-learning/concept-compute-target.md#azure-machine-learning-compute-managed) automatisch skaliert, um nicht verwendete Knoten zu entfernen.

- Eine Schreibschutzsperre für ein **Abonnement** verhindert, dass **Azure Advisor** ordnungsgemäß funktioniert. Advisor kann die Ergebnisse seiner Abfragen nicht speichern.

- Eine Schreibschutzsperre für ein **Application Gateway** verhindert, dass Sie die Back-End-Integrität des Anwendungsgateways abrufen können. Dieser [Vorgang verwendet POST](/rest/api/application-gateway/application-gateways/backend-health), das durch die Schreibschutzsperre blockiert wird.

- Eine Schreibschutzsperre für einen **AKS-Cluster** hindert alle Benutzer daran, auf Clusterressourcen im Abschnitt **Kubernetes-Ressourcen** des linken Blatts des AKS-Clusters im Azure-Portal zuzugreifen. Diese Vorgänge erfordern eine POST-Anforderung für die Authentifizierung.

## <a name="who-can-create-or-delete-locks"></a>Voraussetzungen für das Erstellen oder Löschen von Sperren

Zum Erstellen oder Löschen von Verwaltungssperren benötigen Sie Zugriff auf `Microsoft.Authorization/*`- oder `Microsoft.Authorization/locks/*`-Aktionen. Unter den integrierten Rollen können nur **Besitzer** und **Benutzerzugriffsadministrator** diese Aktionen ausführen.

## <a name="managed-applications-and-locks"></a>Verwaltete Anwendungen und Sperren

Einige Azure-Dienste wie Azure Databricks verwenden für die Implementierung des Diensts [verwaltete Anwendungen](../managed-applications/overview.md). In diesem Fall erstellt der Dienst zwei Ressourcengruppen. Eine Ressourcengruppe enthält eine Übersicht über den Dienst und ist nicht gesperrt. Die andere Ressourcengruppe enthält die Infrastruktur für den Dienst und ist gesperrt.

Wenn Sie versuchen, die Infrastrukturressourcengruppe zu löschen, wird eine Fehlermeldung ausgegeben, dass die Ressourcengruppe gesperrt ist. Wenn Sie versuchen, die Sperre für die Infrastrukturressourcengruppe aufzuheben, wird eine Fehlermeldung ausgegeben, dass die Sperre nicht gelöscht werden kann, da sie einer Systemanwendung angehört.

Löschen Sie stattdessen den Dienst, dann wird auch die Infrastrukturressourcengruppe gelöscht.

Wählen Sie für verwaltete Anwendungen den Dienst, den Sie bereitgestellt haben, aus.

![Auswählen des Diensts](./media/lock-resources/select-service.png)

Beachten Sie, dass der Dienst einen Link für eine **verwaltete Ressourcengruppe** aufweist. In dieser Ressourcengruppe befindet sich die Infrastruktur, und sie ist gesperrt. Sie kann nicht direkt gelöscht werden.

![Anzeigen der verwalteten Gruppe](./media/lock-resources/show-managed-group.png)

Um alle Elemente für den Dienst zu löschen, einschließlich der gesperrten Infrastrukturressourcengruppe, wählen Sie **Löschen** für den Dienst aus.

![Suchdienst löschen](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Konfigurieren von Sperren

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="template"></a>Vorlage

Wenn Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) oder Bicep-Datei zum Bereitstellen einer Sperre verwenden, müssen Sie den Bereich der Sperre und den Bereich der Bereitstellung beachten. Um eine Sperre auf den Bereitstellungsbereich anzuwenden, z. B. das Sperren einer Ressourcengruppe oder eines Abonnements, legen Sie die Eigenschaft „scope“ nicht fest. Wenn Sie eine Ressource innerhalb des Bereitstellungsbereichs sperren, legen Sie den Bereich ordnungsgemäß fest.

Die folgende Vorlage wendet eine Sperre auf die Ressourcengruppe an, in der sie bereitgestellt ist. Beachten Sie, dass für die Sperrressource keine Eigenschaft „scope“vorhanden ist, weil der Bereich der Sperre mit dem Bereich der Bereitstellung übereinstimmt. Diese Vorlage wird auf Ressourcengruppenebene bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "rgLock",
      "properties": {
        "level": "CanNotDelete",
        "notes": "Resource group should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

---

Um eine Ressourcengruppe zu erstellen und zu sperren, stellen Sie die folgende Vorlage auf Abonnementebene bereit.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2021-04-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "lockDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Authorization/locks",
              "apiVersion": "2016-09-01",
              "name": "rgLock",
              "properties": {
                "level": "CanNotDelete",
                "notes": "Resource group and its resources should not be deleted."
              }
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Die Bicep-Hauptdatei erstellt eine Ressourcengruppe und verwendet ein [Modul](../bicep/modules.md), um die Sperre zu erstellen.

```Bicep
targetScope = 'subscription'

param rgName string
param rgLocation string

resource createRg 'Microsoft.Resources/resourceGroups@2021-04-01' = {
  name: rgName
  location: rgLocation
}

module deployRgLock './lockRg.bicep' = {
  name: 'lockDeployment'
  scope: resourceGroup(createRg.name)
}
```

Das Modul verwendet eine Bicep-Datei namens _lockRg.bicep_, die die Ressourcengruppensperre hinzufügt.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group and its resources should not be deleted.'
  }
}
```

---

Wenn Sie eine Sperre auf eine **Ressource** in der Ressourcengruppe anwenden, fügen Sie die Eigenschaft „scope“ hinzu. Legen Sie „scope“ auf den Namen der zu sperrenden Ressource fest.

Im folgenden Beispiel wird eine Vorlage gezeigt, die einen App Service-Plan, eine Website und eine Sperre für die Website erstellt. Der Umfang der Sperre wird auf die Website festgelegt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-12-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-12-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param hostingPlanName string
param location string = resourceGroup().location

var siteName = concat('ExampleSite', uniqueString(resourceGroup().id))

resource serverFarm 'Microsoft.Web/serverfarms@2020-12-01' = {
  name: hostingPlanName
  location: location
  sku: {
    tier: 'Free'
    name: 'f1'
    capacity: 0
  }
  properties: {
    targetWorkerCount: 1
  }
}

resource webSite 'Microsoft.Web/sites@2020-12-01' = {
  name: siteName
  location: location
  properties: {
    serverFarmId: serverFarm.name
  }
}

resource siteLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'siteLock'
  scope: webSite
  properties:{
    level: 'CanNotDelete'
    notes: 'Site should not be deleted.'
  }
}
```

---

### <a name="azure-powershell"></a>Azure PowerShell

Sie sperren bereitgestellte Ressourcen mit Azure PowerShell über den Befehl [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Geben Sie zum Sperren einer Ressource den Namen der Ressource, ihren Ressourcentyp und ihren Ressourcengruppennamen an.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Geben Sie zum Sperren einer Ressourcengruppe ihren Namen an.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Rufen Sie Informationen zu einer Sperre mithilfe von [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) ab. Rufen Sie alle Sperren im Abonnement mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock
```

Rufen Sie alle Sperren für eine Ressource mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Rufen Sie alle Sperren für eine Ressourcengruppe mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Mit dem folgenden Befehl können Sie eine Sperre für eine Ressource löschen:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Mit dem folgenden Befehl können Sie eine Sperre für eine Ressourcengruppe löschen:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

Sperren Sie bereitgestellte Ressourcen mit der Azure CLI, indem Sie den Befehl [az lock create](/cli/azure/lock#az_lock_create) verwenden.

Geben Sie zum Sperren einer Ressource den Namen der Ressource, ihren Ressourcentyp und ihren Ressourcengruppennamen an.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Geben Sie zum Sperren einer Ressourcengruppe ihren Namen an.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Verwenden Sie zum Abrufen von Informationen zu einer Sperre [az lock list](/cli/azure/lock#az_lock_list). Rufen Sie alle Sperren im Abonnement mit dem folgenden Befehl ab:

```azurecli
az lock list
```

Rufen Sie alle Sperren für eine Ressource mit dem folgenden Befehl ab:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Rufen Sie alle Sperren für eine Ressourcengruppe mit dem folgenden Befehl ab:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Mit dem folgenden Befehl können Sie eine Sperre für eine Ressource löschen:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Mit dem folgenden Befehl können Sie eine Sperre für eine Ressourcengruppe löschen:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST-API

Sie können bereitgestellte Ressourcen mit der [REST-API für Verwaltungssperren](/rest/api/resources/managementlocks) sperren. Die REST-API ermöglicht es Ihnen, Sperren zu erstellen und zu löschen sowie Informationen zu vorhandenen Sperren abzurufen.

Führen Sie zum Erstellen einer Sperre Folgendes durch:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Bei dem Bereich kann es sich um ein Abonnement, die Ressourcengruppe oder die Ressource handeln. Geben Sie für "lock-name" den jeweiligen Namen der Sperre ein. Verwenden Sie als „api-version“ die Einstellung **2016-09-01**.

Schließen Sie in die Anforderung ein JSON-Objekt ein, das die Eigenschaften für die Sperre angibt.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum logischen Organisieren von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Ressourcen](tag-resources.md).
- Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).
- Anleitungen dazu, wie Unternehmen Abonnements mit Resource Manager effektiv verwalten können, finden Sie unter [Azure-Unternehmensgerüst - Präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).

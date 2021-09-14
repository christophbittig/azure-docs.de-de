---
title: Verschieben der Containerregistrierung in eine andere Region
description: Manuelles Verschieben von Azure-Containerregistrierungseinstellungen und -daten in eine andere Azure-Region.
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: e2bc00287923a95e2e4d3698b22c4c2ca65bebc6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835889"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>Manuelles Verschieben der Containerregistrierung in eine andere Region

Möglicherweise müssen Sie eine Azure-Containerregistrierung von einer Azure-Region in eine andere verschieben. Wenn Sie beispielsweise eine Entwicklungspipeline ausführen oder ein neues Bereitstellungsziel in einer anderen Region hosten, möchten Sie vielleicht eine Registrierung in der Nähe bereitstellen.

Obwohl [Azure Resource Mover](../resource-mover/overview.md) das Verschieben für eine Azure-Containerregistrierung derzeit nicht automatisieren kann, können Sie eine Containerregistrierung manuell in eine andere Region verschieben:

* Exportieren der Registrierungseinstellungen in eine Resource Manager-Vorlage
* Verwenden der Vorlage zum Bereitstellen einer Registrierung in einer anderen Azure-Region
* Importieren des Registrierungsinhalts aus der Quellregistrierung in die Zielregistrierung

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>Voraussetzungen

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>Weitere Überlegungen

* Verwenden Sie die Schritte in diesem Artikel, um die Registrierung in eine andere Region im selben Abonnement zu verschieben. Es sind möglicherweise weitere Konfigurationsarbeiten erforderlich, um eine Registrierung in ein anderes Azure-Abonnement im selben Active Directory-Mandanten zu verschieben.
* Das Exportieren und Verwenden einer Resource Manager Vorlage kann dabei helfen, viele Registrierungseinstellungen wieder zu erstellen. Sie können die Vorlage bearbeiten, um mehr Einstellungen zu konfigurieren oder die Zielregistrierung nach der Erstellung zu aktualisieren.
* Derzeit unterstützt Azure Container Registry das Verschieben einer Registrierung in einen anderen Active Directory-Mandanten nicht. Diese Einschränkung gilt sowohl für Registrierungen, die mit einem [kundenseitig verwalteten Schlüssel](container-registry-customer-managed-keys.md) verschlüsselt sind, als auch für unverschlüsselte Registrierungen.
* Wenn Sie eine Registrierung nicht verschieben können, erstellen Sie, wie in diesem Artikel beschrieben, eine neue Registrierung, erstellen Sie die Einstellungen manuell neu, und [importieren Sie Registrierungsinhalt in die Zielregistrierung](#import-registry-content-in-target-registry).

## <a name="export-template-from-source-registry"></a>Exportieren der Vorlage aus der Quellregistrierung 

Verwenden Sie das Azure-Portal, die Azure CLI, Azure PowerShell oder andere Azure-Tools, um eine Resource Manager-Vorlage zu exportieren. So verwenden Sie das Azure-Portal:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Quellregistrierung.
1. Wählen Sie im Menü unter **Automatisierung** die Option **Vorlage exportieren** > **Herunterladen** aus.

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="Exportieren einer Vorlage für eine Containerregistrierung":::

## <a name="redeploy-target-registry-in-new-region"></a>Erneutes Bereitstellen der Zielregistrierung in der neuen Region

### <a name="modify-template"></a>Ändern der Vorlage

Untersuchen Sie die Registrierungseigenschaften in der heruntergeladenen JSON-Vorlagendatei, und nehmen Sie die erforderlichen Änderungen vor. Mindestangaben:

* Ändern Sie den Registrierungsnamen `defaultValue` in den gewünschten Namen der Zielregistrierung.
* Aktualisieren sie den `location` auf die gewünschte Azure-Region für die Zielregistrierung.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

Weitere Informationen finden Sie unter [Verwenden einer exportierten Vorlage aus dem Azure-Portal](../azure-resource-manager/templates/template-tutorial-export-template.md) und in der [Vorlagenreferenz](/azure/templates/microsoft.containerregistry/registries).

> [!IMPORTANT]
> Wenn Sie die Zielregistrierung mit einem kundenseitig verwalteten Schlüssel verschlüsseln möchten, aktualisieren Sie die Vorlage mit den Einstellungen für die erforderliche verwaltete Identität, den Schlüsseltresor und den Schlüssel. Sie können den kundenseitig verwalteten Schlüssel nur aktivieren, wenn Sie die Registrierung bereitstellen.
> 
> Weitere Informationen finden Sie unter [Verschlüsseln der Registrierung mithilfe eines kundenseitig verwalteten Schlüssels](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template).

### <a name="create-resource-group"></a>Ressourcengruppe erstellen 

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe für die Zielregistrierung. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*. 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>Erneutes Bereitstellen der Zielregistrierung in der neuen Region

Verwenden Sie den Befehl [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create), um die Zielregistrierung mithilfe der Vorlage bereitzustellen:

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> Wenn während der Bereitstellung Fehler auftreten, müssen Sie möglicherweise bestimmte Konfigurationen in der Vorlagendatei aktualisieren und den Befehl wiederholen.

## <a name="import-registry-content-in-target-registry"></a>Importieren des Registrierungsinhalts in die Zielregistrierung

Nachdem Sie die Registrierung in der Zielregion erstellt haben, verwenden Sie den Befehl [az acr import](/cli/azure/acr#az_acr_import) oder den entsprechenden PowerShell-Befehl `Import-AzContainerImage`, um Images und andere Artefakte, die Sie erhalten möchten, aus der Quellregistrierung in die Zielregistrierung zu importieren. Befehlsbeispiele finden Sie unter [Importieren von Containerimages in eine Containerregistrierung](container-registry-import-images.md).

* Verwenden Sie die Azure CLI-Befehle [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) und [az acr repository show-tags](/cli/azure/acr/repository#az_acr_repository_show_tags) oder entsprechende Azure PowerShell-Befehle, um den Inhalt Ihrer Quellregistrierung aufzählen zu können.
* Führen Sie den Importbefehl für einzelne Artefakte aus, oder erstellen Sie ein Skript damit, um es über eine Liste von Artefakten auszuführen.

Das folgende Azure CLI-Beispielskript zählt die Quellrepositorys und Tags auf und importiert dann die Artefakte in eine Zielregistrierung im selben Azure-Abonnement. Ändern Sie es nach Bedarf, um bestimmte Repositorys oder Tags zu importieren. Informationen zum Importieren aus einer Registrierung in einem anderen Abonnement oder Mandanten finden Sie in den Beispielen unter [Importieren von Containerimages in eine Containerregistrierung](container-registry-import-images.md).

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```



## <a name="verify-target-registry"></a>Überprüfen der Zielregistrierung

Bestätigen Sie die folgenden Informationen in Ihrer Zielregistrierung:

* Registrierungseinstellungen wie Registrierungsname, Dienstebene, öffentlicher Zugriff und Replikationen
* Repositorys und Tags für Inhalte, die Sie erhalten möchten.


### <a name="additional-configuration"></a>Zusätzliche Konfiguration

* Konfigurieren Sie bei Bedarf manuell Einstellungen in der Zielregistrierung, z. B. private Endpunkte, IP-Zugriffsregeln und verwaltete Identitäten.

* Aktualisieren Sie Entwicklungs- und Bereitstellungssysteme, damit diese die Zielregistrierung anstelle der Quellregistrierung verwenden.

* Aktualisieren Sie alle Clientfirewallregeln, sodass der Zugriff auf die Zielregistrierung zugelassen wird.

## <a name="delete-original-registry"></a>Löschen der ursprünglichen Registrierung

Nachdem Sie die Zielregistrierung erfolgreich bereitgestellt, Inhalt migriert und Registrierungseinstellungen überprüft haben, können Sie die Quellregistrierung löschen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Importieren von Containerimages](container-registry-import-images.md) in eine Azure-Containerregistrierung aus einer öffentlichen Registrierung oder einer anderen privaten Registrierung. 
* Siehe die [Azure Resource Manager-Vorlagenreferenz](/azure/templates/microsoft.containerregistry/registries) für Azure Container Registry.
---
title: Erstellen einer privaten Registrierung für das Bicep-Modul
description: Erfahren Sie, wie Sie eine Azure-Containerregistrierung für private Bicep-Module einrichten.
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: b667a80a155355ea206c4804d6eb98200491e3f8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551366"
---
# <a name="create-private-registry-for-bicep-modules-preview"></a>Erstellen einer privaten Registrierung für Bicep-Module (Vorschau)

Um [Module](modules.md) innerhalb Ihrer Organisation freizugeben, können Sie eine private Modulregistrierung erstellen. Sie veröffentlichen Module in dieser Registrierung und gewähren Benutzern, die die Module bereitstellen müssen, Lesezugriff. Nachdem die Module in den Registrierungen freigegeben wurden, können Sie in Ihren Bicep-Dateien darauf verweisen.

Für die Arbeit mit Modulregistrierungen benötigen Sie die Bicep CLI-Version **0.4.1008 oder höher**.

## <a name="configure-private-registry"></a>Konfigurieren einer privaten Registrierung

Eine Bicep-Registrierung wird auf [Azure Container Registry (ACR)](../../container-registry/container-registry-intro.md) gehostet. Verwenden Sie die folgenden Schritte, um Ihre Registrierung für Module zu konfigurieren.

1. Wenn Sie bereits über eine Containerregistrierung verfügen, können Sie sie verwenden. Wenn Sie eine Containerregistrierung erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Erstellen einer Containerregistrierung mithilfe einer Bicep-Datei](../../container-registry/container-registry-get-started-bicep.md). 

   Sie können jede der verfügbaren Registrierungs-SKUs für die Modulregistrierung verwenden. Die [Georeplikation](../../container-registry/container-registry-geo-replication.md) der Registrierung bietet Benutzern eine lokale Präsenz oder eine Sicherung auf heißer Speicherebene.

1. Rufen Sie den Namen des Anmeldeservers ab. Sie benötigen diesen Namen, wenn Sie aus Ihren Bicep-Dateien eine Verknüpfung mit der Registrierung herstellen. 

   Um den Namen des Anmeldeservers abzurufen, verwenden Sie [Get-AzContainerRegistry](/powershell/module/az.containerregistry/get-azcontainerregistry).

   ```azurepowershell
   Get-AzContainerRegistry -ResourceGroupName "<resource-group-name>" -Name "<registry-name>"  | Select-Object LoginServer
   ```

   Oder verwenden Sie [az acr show](/cli/azure/acr#az_acr_show).

   ```azurecli
   az acr show --resource-group <resource-group-name> --name <registry-name> --query loginServer
   ```

   Das Format des Anmeldeservernamens lautet `<registry-name>.azurecr.io`.

- Um Module in einer Registrierung zu veröffentlichen, benötigen Sie die Berechtigung zum **Pushen** eines Images. Um Module aus einer Registrierung bereitzustellen, benötigen Sie die Berechtigung zum **Pullen** des Images. Weitere Informationen zu den Rollen, die angemessenen Zugriff gewähren, finden Sie unter [Azure Container Registry-Rollen und -Berechtigungen](../../container-registry/container-registry-roles.md).

- Abhängig vom Typ des Kontos, das Sie zum Bereitstellen des Moduls verwenden, müssen Sie möglicherweise anpassen, welche Anmeldeinformationen verwendet werden. Diese Anmeldeinformationen werden benötigt, um die Module aus der Registrierung abzurufen. Standardmäßig werden Anmeldeinformationen aus Azure CLI oder Azure PowerShell abgerufen. Sie können die Rangfolge zum Abrufen der Anmeldeinformationen in der Datei **bicepconfig.json** anpassen. Weitere Informationen finden Sie unter [Anmeldeinformationen zum Wiederherstellen von Modulen](bicep-config-modules.md#credentials-for-restoring-modules).

- Um die Sicherheit zu erhöhen, können Sie Zugriff über einen privaten Endpunkt anfordern. Weitere Informationen finden Sie unter [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](../../container-registry/container-registry-private-link.md).

## <a name="publish-files-to-registry"></a>Veröffentlichen von Dateien in der Registrierung

Nachdem Sie die Containerregistrierung eingerichtet haben, können Sie darin Dateien veröffentlichen. Verwenden Sie den Befehl [publish](bicep-cli.md#publish), und geben Sie alle Bicep-Dateien an, die Sie als Module verwenden möchten. Geben Sie den Zielspeicherort für das Modul in Ihrer Registrierung an.

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

## <a name="view-files-in-registry"></a>Anzeigen von Dateien in der Registrierung

So zeigen Sie das veröffentlichte Modul im Portal an

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Containerregistrierungen**.
1. Wählen Sie Ihre Registrierung aus.
1. Wählen Sie im linken Menü **Repositorys** aus.
1. Wählen Sie den Modulpfad (Repository) aus.  Im vorherigen Beispiel lautet der Name des Modulpfads **bicep/modules/storage**.
1. Wählen Sie das Tag aus. Im vorherigen Beispiel lautet das Tag **v1**.
1. Der **Artefaktverweis** entspricht dem Verweis, den Sie in der Bicep-Datei verwenden.

   ![Artefaktverweis der Bicep-Modulregistrierung](./media/private-module-registry/bicep-module-registry-artifact-reference.png)

Sie können nun aus einer Bicep-Datei auf die Datei in der Registrierung verweisen. Beispiele für die Syntax, die zum Verweisen auf ein externes Modul verwendet wird, finden Sie unter [Bicep-Module](modules.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Modulen finden Sie unter [Bicep-Module](modules.md).
* Informationen zum Konfigurieren von Aliasen für eine Modulregistrierung finden Sie unter [Hinzufügen von Moduleinstellungen in der Bicep-Konfigurationsdatei](bicep-config-modules.md).
* Weitere Informationen zum Veröffentlichen und Wiederherstellen von Modulen finden Sie unter [Bicep CLI-Befehle](bicep-cli.md).

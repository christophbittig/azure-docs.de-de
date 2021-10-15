---
title: Bereitstellen von Ressourcen mit PowerShell und Bicep
description: Verwenden Sie Azure Resource Manager und Azure PowerShell, um Ressourcen in Azure bereitzustellen. Die Ressourcen sind in einer Bicep-Datei definiert.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: cc6c8e05f5e6f37a8ac832ac5ee8fae386a627f1
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387713"
---
# <a name="deploy-resources-with-bicep-and-azure-powershell"></a>Bereitstellen von Ressourcen mit Bicep und Azure PowerShell

In diesem Artikel wird erläutert, wie Ihre Ressourcen mithilfe von Azure PowerShell und Bicep-Dateien in Azure bereitgestellt werden. Wenn Sie nicht mit den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen vertraut sind, informieren Sie sich in der [Bicep-Übersicht](overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine einfache Bicep-Datei zum Bereitstellen. Die Datei muss lokal vorliegen.

Sie müssen über Azure PowerShell verfügen und mit Azure verbunden sein:

- **Installieren Sie Azure PowerShell-Cmdlets auf Ihrem lokalen Computer.** Zum Bereitstellen von Bicep-Dateien benötigen Sie Version **5.6.0 oder höher** von [Azure PowerShell](/powershell/azure/install-az-ps). Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
- **Stellen Sie mithilfe von [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Azure her**. Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie möglicherweise auch [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) ausführen. Weitere Informationen finden Sie unter [Verwenden mehrerer Azure-Abonnements](/powershell/azure/manage-subscriptions-azureps).

Wenn PowerShell nicht installiert ist, können Sie Azure Cloud Shell verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Bicep-Dateien über Azure Cloud Shell](./deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Bereitstellungsumfang

Sie können als Ziel für Ihre Bereitstellung eine Ressourcengruppe, ein Abonnement, eine Verwaltungsgruppe oder einen Mandanten verwenden. Abhängig vom Umfang der Bereitstellung verwenden Sie unterschiedliche Befehle.

- Verwenden Sie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um eine **Ressourcengruppe** bereitzustellen:

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-bicep>
  ```

- Zur Bereitstellung in einem **Abonnement** verwenden Sie [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment), ein Alias des Cmdlets `New-AzDeployment`:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

- Verwenden Sie zum Bereitstellen in einer **Verwaltungsgruppe** das Cmdlet [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -ManagementGroupId <management-group-id> -Location <location> -TemplateFile <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Verwaltungsgruppenebene finden Sie unter [Erstellen von Ressourcen auf der Verwaltungsgruppenebene](deploy-to-management-group.md).

- Für die Bereitstellung in einem **Mandanten** verwenden Sie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Mandantenebene finden Sie unter [Erstellen von Ressourcen auf der Mandantenebene](deploy-to-tenant.md).

Der Benutzer, der die Vorlage bereitstellt, muss für jeden Bereich über die erforderlichen Berechtigungen zum Erstellen von Ressourcen verfügen.

## <a name="deploy-local-bicep-file"></a>Bereitstellen einer lokalen Bicep-Datei

Sie können eine Bicep-Datei bereitstellen, die auf Ihrem lokalen Computer oder extern gespeichert ist. In diesem Abschnitt wird die Bereitstellung einer lokalen Bicep-Datei beschrieben.

Wenn eine Bereitstellung in einer Ressourcengruppe erfolgen soll, die nicht vorhanden ist, erstellen Sie zunächst die Ressourcengruppe. Der Name einer Ressourcengruppe darf nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Der Name kann bis zu 90 Zeichen umfassen. Der Name darf nicht mit einem Punkt enden.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Verwenden Sie zum Bereitstellen einer lokalen Bicep-Datei im Bereitstellungsbefehl den Parameter `-TemplateFile`.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-bicep>
```

Die Bereitstellung kann mehrere Minuten dauern.

## <a name="deploy-remote-bicep-file"></a>Bereitstellen einer Bicep-Remotedatei

Derzeit wird die Bereitstellung von BICEP-Remotedateien von Azure PowerShell nicht unterstützt. Kompilieren Sie die Bicep-Datei mithilfe der [Bicep-Befehlszeilenschnittstelle](./install.md#vs-code-and-bicep-extension) in eine JSON-Vorlage, und laden Sie dann die JSON-Datei an den Remotespeicherort.

## <a name="parameters"></a>Parameter

Zum Übergeben von Parameterwerten können Sie entweder Inlineparameter oder eine Parameterdatei verwenden.

### <a name="inline-parameters"></a>Inlineparameter

Geben Sie zum Übergeben von Inlineparametern die Parameternamen mit dem Befehl `New-AzResourceGroupDeployment` an. Wenn Sie beispielsweise eine Zeichenfolge und ein Array an eine Bicep-Datei übergeben möchten, verwenden Sie Folgendes:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Sie können auch den Inhalt einer Datei abrufen und als Inlineparameter übergeben.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Das Abrufen eines Parameterwerts aus einer Datei ist praktisch, wenn Sie Konfigurationswerte angeben müssen. Sie können beispielsweise [cloud-init-Werte für einen virtuellen Linux-Computer](../../virtual-machines/linux/using-cloud-init.md) angeben.

Wenn Sie ein Array von Objekten übergeben müssen, erstellen Sie Hashtabellen in PowerShell, und fügen Sie sie einem Array hinzu. Übergeben Sie dieses Array während der Bereitstellung als Parameter.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameterdateien

Anstatt Parameter als Inlinewerte in Ihrem Skript zu übergeben, ist es wohl einfacher, eine JSON-Datei zu verwenden, die die Parameterwerte enthält. Bei der Parameterdatei kann es sich um eine lokale Datei oder eine externe Datei mit einem erreichbaren URI handeln. Die Bicep-Datei verwendet JSON-Parameterdateien.

Weitere Informationen zur Parameterdatei finden Sie unter [Erstellen einer Resource Manager-Parameterdatei](./parameter-files.md).

Um eine lokale Parameterdatei zu übergeben, verwenden Sie den Parameter `TemplateParameterFile`:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\BicepFiles\storage.bicep `
  -TemplateParameterFile c:\BicepFiles\storage.parameters.json
```

Um eine externe Parameterdatei zu übergeben, verwenden Sie den `TemplateParameterUri`-Parameter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\BicepFiles\storage.bicep `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.parameters.json
```

## <a name="preview-changes"></a>Vorschau der Änderungen

Vor dem Bereitstellen der Bicep-Datei können Sie die Änderungen, die von der Bicep-Datei an Ihrer Umgebung vorgenommen werden, in der Vorschau anzeigen. Überprüfen Sie anhand des [Was-wäre-wenn-Vorgangs](./deploy-what-if.md), ob die Bicep-Datei die erwarteten Änderungen vornimmt. Mit „Was-wäre-wenn“ wird die Bicep-Datei auch auf Fehler überprüft.

## <a name="deploy-template-specs"></a>Bereitstellen von Vorlagenspezifikationen

Azure PowerShell unterstützt das Erstellen von Vorlagenspezifikationen durch Bereitstellen von BICEP-Dateien derzeit nicht. Sie können jedoch eine Bicep-Datei mit der [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs)-Ressource erstellen, um eine Vorlagenspezifikation bereitzustellen. Das [Beispiel für das Erstellen einer Vorlagenspezifikation](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep) zeigt, wie Sie eine Vorlagenspezifikation in einer Bicep-Datei erstellen. Sie können Ihre Bicep-Datei auch über die Bicep-Befehlszeilenschnittstelle im JSON-Format erstellen und dann eine Vorlagenspezifikation mit der JSON-Vorlage erstellen.

## <a name="deployment-name"></a>„Deployment name“ (Bereitstellungsname)

Wenn Sie eine Bicep-Datei bereitstellen, können Sie der Bereitstellung einen Namen geben. Dieser Name kann das Abrufen der Bereitstellung aus dem Bereitstellungsverlauf vereinfachen. Wenn Sie keinen Namen für die Bereitstellung angeben, wird der Name der Bicep-Datei verwendet. Wenn Sie beispielsweise eine Bicep-Datei mit dem Namen `main.bicep` bereitstellen und keinen Bereitstellungsnamen angeben, erhält die Bereitstellung den Namen `main`.

Bei jedem Ausführen einer Bereitstellung wird dem Bereitstellungsverlauf der Ressourcengruppe ein Eintrag mit dem Bereitstellungsnamen hinzugefügt. Wenn Sie eine andere Bereitstellung ausführen und denselben Namen vergeben, wird der vorherige Eintrag durch die aktuelle Bereitstellung ersetzt. Wenn Sie eindeutige Einträge im Bereitstellungsverlauf beibehalten möchten, müssen Sie jeder Bereitstellung einen eindeutigen Namen geben.

Um einen eindeutigen Namen zu erstellen, können Sie eine Zufallszahl zuweisen.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Sie können auch einen Datumswert hinzufügen.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Wenn Sie gleichzeitige Bereitstellungen in derselben Ressourcengruppe mit dem gleichen Bereitstellungsnamen ausführen, wird nur die letzte Bereitstellung abgeschlossen. Alle Bereitstellungen mit dem gleichen Namen, die noch nicht abgeschlossen wurden, werden durch die letzte Bereitstellung ersetzt. Wenn Sie z. B. eine Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, wird nur ein Speicherkonto bereitgestellt. Das resultierende Speicherkonto hat den Namen `storage2`.

Führen Sie jedoch eine Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und führen Sie direkt nach dem Abschluss eine andere Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, erhalten Sie zwei Speicherkonten. Eines hat den Namen `storage1` und das andere den Namen `storage2`. Es ist jedoch nur ein Eintrag im Bereitstellungsverlauf vorhanden.

Wenn Sie für jede Bereitstellung einen eindeutigen Namen angeben, können Sie diese ohne Konflikt gleichzeitig ausführen. Wenn Sie eine Bereitstellung namens `newStorage1` ausführen, die ein Speicherkonto namens `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung namens `newStorage2` ausführen, die ein Speicherkonto namens `storage2` bereitstellt, erhalten Sie zwei Speicherkonten und zwei Einträge im Bereitstellungsverlauf.

Geben Sie jeder Bereitstellung einen eindeutigen Namen, um Konflikte mit gleichzeitigen Bereitstellungen zu vermeiden und eindeutige Einträge im Bereitstellungsverlauf zu gewährleisten.

## <a name="next-steps"></a>Nächste Schritte

- Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von ARM-Vorlagen](file.md).

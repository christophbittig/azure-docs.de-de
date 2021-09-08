---
title: Bereitstellen von Ressourcen mit Azure CLI und Bicep-Dateien
description: Verwenden Sie Azure Resource Manager und Azure CLI, um Ressourcen in Azure bereitzustellen. Die Ressourcen sind in einer Bicep-Datei definiert.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 5e46b920359615c34864d670363b11c451b416b3
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295117"
---
# <a name="deploy-resources-with-bicep-and-azure-cli"></a>Bereitstellen von Ressourcen mit Bicep und Azure CLI

In diesem Artikel wird erläutert, wie Ihre Ressourcen mithilfe der Azure CLI und Bicep-Dateien in Azure bereitgestellt werden. Wenn Sie nicht mit den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen vertraut sind, informieren Sie sich in der [Bicep-Übersicht](./overview.md).

Zum Bereitstellen von Bicep-Dateien benötigen Sie [Version 2.20.0 oder höher der Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Wenn die Azure-Befehlszeilenschnittstelle nicht installiert ist, können Sie Azure Cloud Shell verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Bicep-Dateien über Azure Cloud Shell](./deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Bereitstellungsumfang

Sie können als Ziel für Ihre Bereitstellung eine Ressourcengruppe, ein Abonnement, eine Verwaltungsgruppe oder einen Mandanten verwenden. Abhängig vom Umfang der Bereitstellung verwenden Sie unterschiedliche Befehle.

* Für die Bereitstellung in einer **Ressourcengruppe** verwenden Sie [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* Für die Bereitstellung in einem **Abonnement** verwenden Sie [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

* Für die Bereitstellung in einer **Verwaltungsgruppe** verwenden Sie [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Verwaltungsgruppenebene finden Sie unter [Erstellen von Ressourcen auf der Verwaltungsgruppenebene](deploy-to-management-group.md).

* Für die Bereitstellung in einem **Mandanten** verwenden Sie [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  Weitere Informationen zu Bereitstellungen auf Mandantenebene finden Sie unter [Erstellen von Ressourcen auf der Mandantenebene](deploy-to-tenant.md).

Der Benutzer, der die Bicep-Datei bereitstellt, muss für jeden Bereich über die erforderlichen Berechtigungen zum Erstellen von Ressourcen verfügen.

## <a name="deploy-local-bicep-file"></a>Bereitstellen einer lokalen Bicep-Datei

Sie können eine Bicep-Datei bereitstellen, die auf Ihrem lokalen Computer oder extern gespeichert ist. In diesem Abschnitt wird die Bereitstellung einer lokalen Bicep-Datei beschrieben.

Wenn eine Bereitstellung in einer Ressourcengruppe erfolgen soll, die nicht vorhanden ist, erstellen Sie zunächst die Ressourcengruppe. Der Name einer Ressourcengruppe darf nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Der Name kann bis zu 90 Zeichen umfassen. Der Name darf nicht mit einem Punkt enden.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Verwenden Sie zum Bereitstellen einer lokalen Bicep-Datei im Bereitstellungsbefehl den Parameter `--template-file`. Das folgende Beispiel zeigt auch, wie ein Parameterwert festgelegt wird.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Die Bereitstellung kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Nachricht mit dem Ergebnis angezeigt:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>Bereitstellen einer Bicep-Remotedatei

Derzeit wird die Bereitstellung von Bicep-Remotedateien von Azure CLI nicht unterstützt. Kompilieren Sie die Bicep-Datei mithilfe der [Bicep-Befehlszeilenschnittstelle](./install.md#development-environment) in eine JSON-Vorlage, und laden Sie dann die JSON-Datei an den Remotespeicherort.

## <a name="parameters"></a>Parameter

Zum Übergeben von Parameterwerten können Sie entweder Inlineparameter oder eine Parameterdatei verwenden.

### <a name="inline-parameters"></a>Inlineparameter

Wenn Sie Inlineparameter übergeben möchten, geben Sie die Werte in `parameters` an. Wenn Sie beispielsweise eine Zeichenfolge und ein Array einer Bicep-Datei in einer Bash-Shell übergeben möchten, verwenden Sie Folgendes:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Wenn Sie die Azure-Befehlszeilenschnittstelle mit der Windows-Eingabeaufforderung (CMD) oder PowerShell verwenden, übergeben Sie das Array in folgendem Format: `exampleArray="['value1','value2']"`.

Sie können auch den Inhalt einer Datei abrufen und als Inlineparameter übergeben.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Das Abrufen eines Parameterwerts aus einer Datei ist praktisch, wenn Sie Konfigurationswerte angeben müssen. Sie können beispielsweise [cloud-init-Werte für einen virtuellen Linux-Computer](../../virtual-machines/linux/using-cloud-init.md) angeben.

Das Format von _arrayContent.json_ lautet:

```json
[
    "value1",
    "value2"
]
```

Verwenden Sie JSON, um ein Objekt zu übergeben, z. B. zum Festlegen von Tags. Ihre Bicep-Datei kann z. B. einen Parameter wie den folgenden enthalten:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

In diesem Fall können Sie eine JSON-Zeichenfolge übergeben, um den Parameter festzulegen, wie im folgenden Bash-Skript gezeigt:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Schließen Sie den JSON-Code, der an das Objekt übergeben werden soll, in doppelte Anführungszeichen ein.

Sie können eine Variable verwenden, die die Parameterwerte enthalten kann. Legen Sie in Bash die Variable auf alle Parameterwerte fest, und fügen Sie sie dem Bereitstellungsbefehl hinzu.

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
``` 

Wenn Sie jedoch die Azure-Befehlszeilenschnittstelle mit der Windows-Eingabeaufforderung (CMD) oder PowerShell verwenden, legen Sie die Variable auf eine JSON-Zeichenfolge fest. Versehen Sie Anführungszeichen mit einem Escapezeichen: `$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`.

### <a name="parameter-files"></a>Parameterdateien

Anstatt Parameter als Inlinewerte in Ihrem Skript zu übergeben, ist es wohl einfacher, eine JSON-Datei zu verwenden, die die Parameterwerte enthält. Die Parameterdatei muss eine lokale Datei sein. Externe Parameterdateien werden mit der Azure-Befehlszeilenschnittstelle nicht unterstützt. Die Bicep-Datei verwendet JSON-Parameterdateien.

Weitere Informationen zur Parameterdatei finden Sie unter [Erstellen einer Resource Manager-Parameterdatei](./parameter-files.md).

Um eine lokale Parameterdatei zu übergeben, verwenden Sie `@` zur Angabe einer lokalen Datei mit dem Namen _storage.parameters.json_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>Vorschau der Änderungen

Vor dem Bereitstellen der Bicep-Datei können Sie die Änderungen, die von der Bicep-Datei an Ihrer Umgebung vorgenommen werden, in der Vorschau anzeigen. Überprüfen Sie anhand des [Was-wäre-wenn-Vorgangs](./deploy-what-if.md), ob die Bicep-Datei die erwarteten Änderungen vornimmt. Mit „Was-wäre-wenn“ wird die Bicep-Datei auch auf Fehler überprüft.

## <a name="deploy-template-specs"></a>Bereitstellen von Vorlagenspezifikationen

Azure CLI unterstützt das Erstellen von Vorlagenspezifikationen durch Bereitstellen von Bicep-Dateien derzeit nicht. Sie können jedoch eine BICEP-Datei mit der [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs)-Ressource erstellen, um eine Vorlagenspezifikation bereitzustellen. Hier sehen Sie ein [Beispiel](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep). Sie können Ihre Bicep-Datei auch mithilfe der Bicep-Befehlszeilenschnittstelle in Form einer ARM-Vorlagen-JSON-Datei erstellen und dann eine Vorlagenspezifikation mit der JSON-Vorlage erstellen.

## <a name="deployment-name"></a>„Deployment name“ (Bereitstellungsname)

Wenn Sie eine Bicep-Datei bereitstellen, können Sie der Bereitstellung einen Namen geben. Dieser Name kann das Abrufen der Bereitstellung aus dem Bereitstellungsverlauf vereinfachen. Wenn Sie keinen Namen für die Bereitstellung angeben, wird der Name der Bicep-Datei verwendet. Wenn Sie beispielsweise eine Bicep-Datei mit dem Namen `azuredeploy.bicep` bereitstellen und keinen Bereitstellungsnamen angeben, erhält die Bereitstellung den Namen `azuredeploy`.

Bei jedem Ausführen einer Bereitstellung wird dem Bereitstellungsverlauf der Ressourcengruppe ein Eintrag mit dem Bereitstellungsnamen hinzugefügt. Wenn Sie eine andere Bereitstellung ausführen und denselben Namen vergeben, wird der vorherige Eintrag durch die aktuelle Bereitstellung ersetzt. Wenn Sie eindeutige Einträge im Bereitstellungsverlauf beibehalten möchten, müssen Sie jeder Bereitstellung einen eindeutigen Namen geben.

Um einen eindeutigen Namen zu erstellen, können Sie eine Zufallszahl zuweisen.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Sie können auch einen Datumswert hinzufügen.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Wenn Sie gleichzeitige Bereitstellungen in derselben Ressourcengruppe mit dem gleichen Bereitstellungsnamen ausführen, wird nur die letzte Bereitstellung abgeschlossen. Alle Bereitstellungen mit dem gleichen Namen, die noch nicht abgeschlossen wurden, werden durch die letzte Bereitstellung ersetzt. Wenn Sie z. B. eine Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, wird nur ein Speicherkonto bereitgestellt. Das resultierende Speicherkonto hat den Namen `storage2`.

Führen Sie jedoch eine Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und führen Sie direkt nach dem Abschluss eine andere Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, erhalten Sie zwei Speicherkonten. Eines hat den Namen `storage1` und das andere den Namen `storage2`. Es ist jedoch nur ein Eintrag im Bereitstellungsverlauf vorhanden.

Wenn Sie für jede Bereitstellung einen eindeutigen Namen angeben, können Sie diese ohne Konflikt gleichzeitig ausführen. Wenn Sie eine Bereitstellung namens `newStorage1` ausführen, die ein Speicherkonto namens `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung namens `newStorage2` ausführen, die ein Speicherkonto namens `storage2` bereitstellt, erhalten Sie zwei Speicherkonten und zwei Einträge im Bereitstellungsverlauf.

Geben Sie jeder Bereitstellung einen eindeutigen Namen, um Konflikte mit gleichzeitigen Bereitstellungen zu vermeiden und eindeutige Einträge im Bereitstellungsverlauf zu gewährleisten.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Rollback zu einer erfolgreiche Bereitstellung, wenn ein Fehler auftritt, finden Sie unter [Rollback bei Fehler zu erfolgreicher Bereitstellung](../templates/rollback-on-error.md).
- Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von ARM-Vorlagen](file.md).
* Tipps zum Beheben gängiger Azure-Bereitstellungsfehler finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../templates/common-deployment-errors.md).
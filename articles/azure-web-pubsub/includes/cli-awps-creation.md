---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783501"
---
Verwenden Sie den Azure CLI-Befehl [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create), um eine Web PubSub-Instanz in der Ressourcengruppe aus dem vorherigen Schritt zu erstellen. Geben Sie dabei die folgenden Informationen an:

- Ressourcenname: eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf

  > [!Important]
  > Jede Web PubSub-Ressource muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen &lt;your-unique-resource-name&gt; durch den Namen Ihrer Web PubSub-Instanz.

- Ressourcengruppennamen: **myResourceGroup**
- Standort: **EastUS**
- SKU: **Free_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

> [!Note]
> Der Web PubSub-Dienst befindet sich noch in der Vorschauphase, daher wird in der CLI-Konsole möglicherweise die folgende Warnung angezeigt: „The command requires the extension webpubsub. Do you want to install it now? The command will continue to run after the extension is installed. (Y/n)“ (Für den Befehl ist die Erweiterung „webpubsub“ erforderlich. Möchten Sie sie jetzt installieren? Der Befehl wird nach der Installation der Erweiterung weiter ausgeführt. (J/N)). Drücken Sie J, um die Erweiterung zu installieren.

In der Ausgabe dieses Befehls werden die Eigenschaften der neu erstellten Ressource angezeigt. Beachten Sie die beiden folgenden Eigenschaften:

- **Resource Name**: Der Name, den Sie oben für den Parameter `--name` angegeben haben.
- **hostName**: Im Beispiel lautet der Hostname `<your-unique-resource-name>.webpubsub.azure.com/`.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für die neue Ressource autorisiert.

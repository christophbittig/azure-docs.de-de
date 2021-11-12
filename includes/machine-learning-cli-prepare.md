---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 2bd402b9905772b7a45be5ea433a214b6e5f76cf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553442"
---
Die Informationen in diesem Artikel basieren auf Codebeispielen, die im Repository [azureml-examples ](https://github.com/azure/azureml-examples) enthalten sind. Um die Befehle lokal auszuführen, ohne YAML und andere Dateien kopieren/einfügen zu müssen, klonen Sie das Repository, und wechseln Sie dann in das Verzeichnis `cli` im Repository:

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Wenn Sie die Standardeinstellungen für die Azure-Befehlszeilenschnittstelle noch nicht festgelegt haben, speichern Sie Ihre Standardeinstellungen. Um zu vermeiden, dass Sie die Werte für Ihr Abonnement, Ihren Arbeitsbereich und Ihre Ressourcengruppe mehrfach übergeben müssen, verwenden Sie die folgenden Befehle. Ersetzen Sie die folgenden Parameter durch Werte für Ihre spezifische Konfiguration:

* Ersetzen Sie `<subscription>` durch Ihre Azure-Abonnement-ID.
* Ersetzen Sie `<workspace>` durch den Namen Ihres Azure Machine Learning-Arbeitsbereichs.
* Ersetzen Sie `<resource-group>` durch die Azure-Ressourcengruppe, die Ihren Arbeitsbereich enthält.
* Ersetzen Sie `<location>` durch die Azure-Region, die Ihren Arbeitsbereich enthält.

> [!TIP]
> Mit dem Befehl `az configure -l` können Sie die aktuellen Standardwerte anzeigen.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```


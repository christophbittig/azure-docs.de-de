---
title: Authentifizierungsgeheimnisse im Training
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der Azure Key Vault-Instanz für Ihren Arbeitsbereich auf sichere Weise Geheimnisse an Trainingsausführungen weitergeben können.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: af61ce1fbea14b6c872916ecf9b501ad074bda9b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556994"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Verwenden von Geheimnissen als Anmeldeinformationen für die Authentifizierung in Azure Machine Learning-Trainingsausführungen

In diesem Artikel erfahren Sie, wie Sie Geheimnisse sicher in Trainingsausführungen verwenden. Authentifizierungsinformationen (wie etwa Ihr Benutzername und Ihr Kennwort) sind Geheimnisse. Wenn Sie beispielsweise eine Verbindung mit einer externen Datenbank herstellen möchten, um Trainingsdaten abzufragen, müssen Sie Ihren Benutzernamen und Ihr Kennwort an den Remoteausführungskontext übergeben. Das Codieren solcher Werte in Trainingsskripts in Klartext ist unsicher, da so das Geheimnis verfügbar gemacht wird. 

Daher ist Ihrem Azure Machine Learning-Arbeitsbereich eine Ressource namens [Azure Key Vault](../key-vault/general/overview.md) zugeordnet. Verwenden Sie diese Key Vault-Instanz, um Geheimnisse über eine Gruppe von APIs aus dem Azure Machine Learning Python SDK sicher an Remoteausführungen zu übergeben.

Der Standardablauf für das Verwenden von Geheimnissen sieht wie folgt aus:
 1. Melden Sie sich auf einem lokalen Computer bei Azure an, und stellen Sie eine Verbindung mit Ihrem Arbeitsbereich her.
 2. Legen Sie auf dem lokalen Computer ein Geheimnis in der Key Vault-Instanz des Arbeitsbereichs fest.
 3. Starten Sie eine Remoteausführung.
 4. Rufen Sie innerhalb der Remoteausführung das Geheimnis aus der Key Vault-Instanz ab, und verwenden Sie es.

## <a name="set-secrets"></a>Festlegen von Geheimnissen

In Azure Machine Learning enthält die Klasse [Keyvault](/python/api/azureml-core/azureml.core.keyvault.keyvault) Methoden zum Festlegen von Geheimnissen. Rufen Sie in Ihrer lokalen Python-Sitzung zunächst einen Verweis auf die Key Vault-Instanz Ihres Arbeitsbereichs ab, und verwenden Sie dann die Methode [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-), um ein Geheimnis mit Name und Wert festzulegen. Die __set_secret__-Methode aktualisiert den Geheimniswert, wenn der Name bereits vorhanden ist.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Schreiben Sie den Wert des Geheimnisses nicht in Python-Code, weil es unsicher ist, den Wert als Klartext in einer Datei zu speichern. Rufen Sie den Wert des Geheimnisses stattdessen aus einer Umgebungsvariablen, z. B. Azure DevOps-Buildgeheimnis, oder aus einer interaktiven Benutzereingabe ab.

Geheimnisnamen können mithilfe der Methode [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) aufgelistet werden. Mit [set_secrets()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) steht außerdem eine Batchversion zur Verfügung, mit der Sie mehrere Geheimnisse gleichzeitig festlegen können.

> [!IMPORTANT]
> Mit `list_secrets()`werden nur Geheimnisse aufgelistet, die über `set_secret()` oder `set_secrets()`mit dem Azure ML SDK erstellt wurden. Es werden keine Geheimnisse aufgelistet, die von einem anderen als diesem SDK erstellt wurden. Beispielsweise wird ein Geheimnis, das mit dem Azure-Portal oder der Azure PowerShell erstellt wurde, nicht aufgeführt.
> 
> Sie können [`get_secret()`](#get-secrets) verwenden, um einen Geheimniswert aus dem Schlüsseltresor abzurufen, unabhängig davon, wie er erstellt wurde. Sie können also Geheimnisse abrufen, die nicht von `list_secrets()` aufgelistet werden.

## <a name="get-secrets"></a>Abrufen geheimer Schlüssel

In Ihrem lokalen Code können Sie die Methode [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) verwenden, um den Geheimniswert anhand des Namens abzurufen.

Verwenden Sie für Ausführungen mit Übermittlung von [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-) die Methode [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) mit der Klasse [`Run`](/python/api/azureml-core/azureml.core.run%28class%29). Da die übermittelte Ausführung ihren Arbeitsbereich kennt, umgeht diese Methode die Arbeitsbereichsinstanziierung und gibt den Geheimniswert direkt zurück.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Achten Sie darauf, dass Sie den Geheimniswert nicht dadurch verfügbar machen, dass Sie ihn schreiben oder drucken.

Mit [get_secrets()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) steht auch eine Batchversion zur Verfügung, mit der Sie gleichzeitig auf mehrere Geheimnisse zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

 * [Beispielnotebook anzeigen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Informationen über Unternehmenssicherheit mit Azure Machine Learning](concept-enterprise-security.md)
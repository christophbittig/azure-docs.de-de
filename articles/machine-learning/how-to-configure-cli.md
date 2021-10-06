---
title: Installieren und Einrichten der CLI (v2)
titleSuffix: Azure Machine Learning
description: Erhalten Sie Informationen zum Installieren und Einrichten der Azure CLI-Erweiterung für Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: d3b21575b1e0e7e8e5c049cdf692ea261b8173c6
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423894"
---
# <a name="install-and-set-up-the-cli-v2"></a>Installieren und Einrichten der CLI (v2)

Die Erweiterung `ml` (Vorschau) für die [Azure CLI](/cli/azure/) ist die erweiterte Schnittstelle für Azure Machine Learning. Hiermit können Sie Modelle über die Befehlszeile trainieren und bereitstellen und Features nutzen, mit denen beim Nachverfolgen des Modelllebenszyklus das Hoch- und Aufskalieren von Data Science-Einheiten beschleunigt werden kann.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Für die Verwendung der CLI benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
- Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli).

## <a name="installation"></a>Installation

Für die neue Machine Learning-Erweiterung **ist die Azure CLI-Version `>=2.15.0` erforderlich**. Stellen Sie sicher, dass diese Anforderung erfüllt ist:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Ist dies nicht der Fall, [aktualisieren Sie Ihre Azure CLI](/cli/azure/update-azure-cli).

Überprüfen Sie die installierten Azure CLI-Erweiterungen:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

Stellen Sie sicher, dass keine in Konflikt stehende Erweiterung mit dem Namespace `ml` installiert ist, einschließlich der Erweiterung `azure-cli-ml`:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

Installieren Sie nun die Erweiterung `ml`:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

Führen Sie den Hilfebefehl aus, um Ihre Installation zu überprüfen und verfügbare Unterbefehle anzuzeigen:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

Sie können die Erweiterung auf die neueste Version aktualisieren:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>Installation unter Linux

Wenn Sie Linux verwenden, ist die schnellste Möglichkeit, die erforderliche CLI-Version und die Machine Learning-Erweiterung zu installieren:

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

Weitere Informationen finden Sie unter [Installieren der Azure CLI unter Linux](/cli/azure/install-azure-cli-linux).

## <a name="set-up"></a>Einrichten

Anmeldung:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Wenn Sie Zugriff auf mehrere Azure-Abonnements haben, können Sie Ihr aktives Abonnement festlegen:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Ist noch keine Azure-Ressourcengruppe vorhanden, können Sie sie erstellen:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

Für Machine Learning-Unterbefehle sind die Parameter `--workspace/-w` und `--resource-group/-g` erforderlich. Konfigurieren Sie Standardwerte, um diese nicht wiederholt eingeben zu müssen:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> Bei den meisten Codebeispielen wird davon ausgegangen, dass Sie einen Standardarbeitsbereich und eine Standardressourcengruppe festgelegt haben. Sie können diese über die Befehlszeile überschreiben.

Erstellen Sie nun den Arbeitsbereich für maschinelles Lernen:

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren von Modellen mithilfe der Machine Learning-CLI-Erweiterung (Vorschau)](how-to-train-cli.md)
- [Einrichten der Azure Machine Learning-Erweiterung für Visual Studio Code](how-to-setup-vs-code.md)
- [Trainieren eines TensorFlow-Modells für die Bildklassifizierung mit der Azure Machine Learning-Erweiterung für Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md)

---
title: 'Tutorial: Verwaltete Onlineendpunkte für den Zugriff auf Ressourcen'
description: Hier finden Sie Informationen zum sicheren Zugreifen auf Azure-Ressourcen für Ihre Machine Learning-Modellimplementierung mit einem verwalteten Onlineendpunkt und einer systemseitig zugewiesenen verwalteten Identität.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: tutorial
ms.custom: tutorial, devplatv2
ms.openlocfilehash: 18e8c09f1d1a734d2e68e656658c3017a115f9a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079337"
---
# <a name="tutorial-access-resources-with-managed-online-endpoints-and-identity-preview"></a>Tutorial: Zugreifen auf Ressourcen mit verwalteten Onlineendpunkten und Identität (Vorschau)

In diesem Tutorial erfahren Sie, wie Sie über Ihr Bewertungsskript mit einem verwalteten Onlineendpunkt und einer systemseitig zugewiesenen verwalteten Identität sicher auf Azure-Ressourcen zugreifen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]
Es wird gezeigt, wie Sie die folgenden Aktionen mit der Azure CLI und der zugehörigen ML-Erweiterung ausführen:

> [!div class="checklist"]
> * Festlegen der zu verwendenden Standardwerte für die Azure CLI
> * Konfigurieren der zu verwendenden Variablen für Ihren verwalteten Onlineendpunkt
> * Erstellen eines Blobspeicherkontos und eines Blobcontainers 
> * Erstellen eines verwalteten Onlineendpunkts
> * Erteilen von Speicherzugriffsberechtigungen für die systemseitig zugewiesene verwaltete Identität
> * Erstellen einer dem verwalteten Endpunkt zugeordneten Bereitstellung


## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung von Azure Machine Learning ist ein Azure-Abonnement erforderlich. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Sie müssen die Azure CLI und die ML-Erweiterung installieren und konfigurieren. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschauversion)](how-to-configure-cli.md). 

* Sie müssen über eine Azure-Ressourcengruppe verfügen, für die Ihnen (oder dem von Ihnen genutzten Dienstprinzipal) die Zugriffsberechtigung `Contributor` (Mitwirkender) zugeordnet ist. Sie verfügen über eine Ressourcengruppe dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben. 

* Sie müssen über einen Azure Machine Learning-Arbeitsbereich verfügen. Sie verfügen über einen Arbeitsbereich dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben.

* Ein trainiertes, für die Bewertung und Bereitstellung bereites Machine Learning-Modell.


## <a name="set-the-defaults-for-azure-cli"></a>Festlegen der Standardwerte für die Azure CLI

Legen Sie die gewünschten Standardwerte für die Azure-Abonnement-ID, den Azure Machine Learning-Arbeitsbereich und die Ressourcengruppe fest, um sicherzustellen, dass in diesem Tutorial die richtigen Ressourcen verwendet werden. Dadurch müssen die Werte nicht bei jedem Aufruf eines Azure CLI-Befehls erneut übergeben werden. 

> [!IMPORTANT]
> Achten Sie darauf, dass Ihrem Benutzerkonto die Rolle „Benutzerzugriffsadministrator“ für die Ressourcengruppe zugewiesen ist. 

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

## <a name="define-the-configuration-yaml-file-for-your-deployment"></a>Definieren der YAML-Konfigurationsdatei für Ihre Bereitstellung

Wenn Sie einen verwalteten Endpunkt bereitstellen möchten, müssen Sie zunächst die Konfiguration für Ihren Endpunkt in einer YAML-Datei definieren.

Im folgenden Codebeispiel wird ein verwalteter Endpunkt mit folgenden Eigenschaften erstellt:  
* Anzeige der YAML-Dateien aus dem Verzeichnis `endpoints/online/managed/managed-identities/`
* Definition des Namens, unter dem Sie auf den Endpunkt verweisen möchten: `my-sai-endpoint`
* Angabe des Autorisierungstyps, der für den Zugriff auf den Endpunkt verwendet werden soll: `auth-mode: key`
* Angabe, dass Sie einen Endpunkt vom Typ `online` erstellen möchten 
* Angabe, dass dem Endpunkt eine Bereitstellung namens `blue` zugeordnet ist
* Konfiguration der Bereitstellungsdetails (beispielsweise, welches Modell bereitgestellt und welche Umgebung und welches Bewertungsskript verwendet werden soll)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

Informationen zu YAML finden Sie in der [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md).

## <a name="configure-variables-for-your-deployment"></a>Konfigurieren von Variablen für Ihre Bereitstellung

Konfigurieren Sie die Variablennamen für den Arbeitsbereich, den Standort des Arbeitsbereichs und den Endpunkt, den Sie erstellen möchten. Durch den folgenden Code werden diese Werte als Umgebungsvariablen in Ihren Endpunkt exportiert:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

Geben Sie als Nächstes an, wie Sie Ihr Blobspeicherkonto, Ihren Blobcontainer und Ihre Datei nennen möchten. Diese Variablennamen werden hier definiert und im nächsten Abschnitt in den Befehlen `az storage account create` und `az storage container create` verwendet.

Durch den folgenden Code werden diese Werte als Umgebungsvariablen exportiert:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::


Erstellen Sie nach dem Exportieren dieser Variablen eine lokale Textdatei. Wenn der Endpunkt bereitgestellt wird, wird vom Bewertungsskript mithilfe der systemseitig zugewiesenen verwalteten Identität, die bei der Erstellung des Endpunkts generiert wird, auf diese Textdatei zugegriffen.

## <a name="create-blob-storage-and-container"></a>Erstellen von Blobspeicher und Blobcontainer

In diesem Beispiel erstellen Sie ein Blobspeicherkonto und einen Blobcontainer und laden dann die zuvor erstellte Textdatei in den Blobcontainer hoch. 

Erstellen Sie zuerst ein Speicherkonto. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

Erstellen Sie dann den Blobcontainer im Speicherkonto. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

Laden Sie anschließend Ihre Textdatei in den Blobcontainer hoch. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

## <a name="create-a-managed-online-endpoint"></a>Erstellen eines verwalteten Onlineendpunkts

Durch den folgenden Code wird ein verwalteter Onlineendpunkt ohne Angabe einer Bereitstellung erstellt. Die Bereitstellung wird später im Tutorial erstellt.

Wenn Sie einen verwalteten Endpunkt erstellen, wird standardmäßig eine systemseitig zugewiesene verwaltete Identität für den Endpunkt erstellt.

>[!IMPORTANT]
> Systemseitig zugewiesene verwaltete Identitäten sind unveränderlich und können nach der Erstellung nicht mehr geändert werden.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

Überprüfen Sie den Status des Endpunkts wie folgt: 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

Falls Probleme auftreten, helfen Ihnen die Informationen unter [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](./how-to-troubleshoot-online-endpoints.md) weiter.

## <a name="give-storage-permission-to-system-assigned-managed-identity"></a>Erteilen von Speicherberechtigungen für die systemseitig zugewiesene verwaltete Identität

Sie können dem verwalteten Endpunkt Speicherzugriffsberechtigungen über die systemseitig zugewiesene verwaltete Identität erteilen. 

Rufen Sie die systemseitig zugewiesene verwaltete Identität ab, die für Ihren Endpunkt erstellt wurde. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

Anschließend können Sie der systemseitig zugewiesenen verwalteten Identität Zugriff auf Ihren Speicher gewähren.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

## <a name="scoring-script-to-access-azure-resource"></a>Bewertungsskript für den Zugriff auf Azure-Ressourcen

Das folgende Bewertungsskript zeigt, wie Sie das Token der systemseitig zugewiesenen verwalteten Identität für den Zugriff auf Azure-Ressourcen verwenden. In diesem Szenario handelt es sich bei der Azure-Ressource um das Speicherkonto, das in den vorherigen Abschnitten erstellt wurde. 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-using-your-configuration"></a>Erstellen einer Bereitstellung mit Ihrer Konfiguration

Erstellen Sie eine Bereitstellung, die dem verwalteten Endpunkt zugeordnet ist.

Diese Bereitstellung dauert etwa acht bis 14 Minuten – je nachdem, ob die zugrunde liegende Umgebung bzw. das zugrunde liegende Image zum ersten Mal erstellt wird. Nachfolgende Bereitstellungen mit der gleichen Umgebung sind schneller.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

Überprüfen Sie den Status der Bereitstellung. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

> [!NOTE]
> Von der init-Methode im Bewertungsskript wird die Datei aus Ihrem Speicherkonto unter Verwendung des Tokens der systemseitig zugewiesenen verwalteten Identität gelesen. 

Die Ausgabe der init-Methode kann im Bereitstellungsprotokoll mit dem folgenden Code überprüft werden. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::

Nach Abschluss dieses Befehls haben Sie das Modell, die Umgebung und den Endpunkt in Ihrem Azure Machine Learning-Arbeitsbereich registriert.

### <a name="confirm-your-endpoint-deployed-successfully"></a>Vergewissern, dass der Endpunkt erfolgreich bereitgestellt wurde

Vergewissern Sie sich nach dem Bereitstellen des Endpunkts, dass er funktioniert. Details zum Rückschluss variieren von Modell zu Modell. In diesem Tutorial sehen die JSON-Abfrageparameter wie folgt aus: 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

Führen Sie zum Aufrufen Ihres Endpunkts Folgendes aus: 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::


## <a name="delete-the-endpoint-and-storage-account"></a>Löschen des Endpunkts und des Speicherkontos

Wenn Sie den bereitgestellten Endpunkt und den Speicher nicht mehr benötigen, löschen Sie sie, um Kosten zu sparen. Durch das Löschen des Endpunkts werden auch alle zugeordneten Bereitstellungen gelöscht. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

## <a name="next-steps"></a>Nächste Schritte

In diesem Azure Machine Learning-Tutorial haben Sie die Machine Learning-CLI für die folgenden Aufgaben verwendet:

> [!div class="checklist"]
> * Festlegen der zu verwendenden Standardwerte für die Azure CLI
> * Konfigurieren der zu verwendenden Variablen für Ihren Endpunkt
> * Erstellen eines Blobspeicherkontos und eines Blobcontainers 
> * Erstellen eines verwalteten Endpunkts
> * Erteilen von Speicherzugriffsberechtigungen für die systemseitig zugewiesene verwaltete Identität
> * Erstellen einer dem verwalteten Endpunkt zugeordneten Bereitstellung

* Weitere Informationen zum Verwenden der Befehlszeilenschnittstelle (CLI) finden Sie unter [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Informationen zum Einschränken von JSON-Abfragen, sodass nur bestimmte Daten zurückgegeben werden, finden Sie unter [Abfragen der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli).
* Weitere Informationen zum YAML-Schema finden Sie im Dokument [YAML-Referenz für verwalteten Onlineendpunkt](reference-yaml-endpoint-managed-online.md).
* Informationen dazu, welche Computeressourcen Sie verwenden können, finden Sie unter [SKU-Liste für verwaltete Onlineendpunkte (Vorschau)](reference-managed-online-endpoints-vm-sku-list.md).
* Weitere Informationen zu Kosten finden Sie unter [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md).
* Weitere Informationen zur Bereitstellung finden Sie unter [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md).
* Informationen zur Überwachung von Endpunkten finden Sie unter [Überwachen verwalteter Onlineendpunkte (Vorschau)](how-to-monitor-online-endpoints.md).
* Informationen zu Einschränkungen für verwaltete Endpunkte finden Sie unter [Verwalten und Erhöhen von Kontingenten für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).
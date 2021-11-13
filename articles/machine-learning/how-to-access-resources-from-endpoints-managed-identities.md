---
title: Zugriff auf Azure-Ressourcen vom verwalteten Endpunkt aus
titleSuffix: Azure Machine Learning
description: Greifen Sie über einen verwalteten Online-Endpunkt mit einer system- oder benutzerzugeordneten verwalteten Identität sicher auf Azure-Ressourcen für die Bereitstellung Ihres Modells für maschinelles Lernen zu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 8969ca4b9ca24e3cd864075d2d99492db74762bb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056150"
---
# <a name="access-azure-resources-from-a-managed-online-endpoint-preview-with-a-managed-identity"></a>Zugriff auf Azure-Ressourcen von einem verwalteten Online-Endpunkt (Vorschau) mit einer verwalteten Identität 

Erfahren Sie, wie Sie von Ihrem Scoring-Skript aus mit einem verwalteten Online-Endpunkt und entweder einer vom System zugewiesenen verwalteten Identität oder einer vom Benutzer zugewiesenen verwalteten Identität auf Azure-Ressourcen zugreifen können. 

Verwaltete Endpunkte (Vorschau) ermöglichen es Azure Machine Learning, den Aufwand für die Bereitstellung Ihrer Rechenressourcen und die Bereitstellung Ihres Machine Learning-Modells zu verwalten. In der Regel muss Ihr Modell auf Azure-Ressourcen wie die Azure Container Registry oder Ihren Blob-Speicher für die Rückschließen zugreifen; mit einer verwalteten Identität können Sie auf diese Ressourcen zugreifen, ohne dass Sie Anmeldeinformationen in Ihrem Code verwalten müssen. [Erfahren Sie mehr über verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

In dieser Anleitung wird davon ausgegangen, dass Sie keine verwaltete Identität, kein Speicherkonto und keinen verwalteten Online-Endpunkt haben. Wenn Sie bereits über diese Komponenten verfügen, fahren Sie mit dem Abschnitt [Zugriffsberechtigung für die verwaltete Identität erteilen](#give-access-permission-to-the-managed-identity) fort. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung von Azure Machine Learning ist ein Azure-Abonnement erforderlich. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Sie müssen die Azure CLI- und die ML-Erweiterung installieren und konfigurieren. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der 2.0 CLI (Vorschau)](how-to-configure-cli.md).

* Eine Azure-Ressourcengruppe, auf die Sie (oder der von Ihnen verwendete Dienstprinzipal) Zugriff haben müssen `User Access Administrator` und `Contributor`. Sie verfügen über eine Ressourcengruppe dieser Art, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben.

* Ein Azure Machine Learning-Arbeitsbereich. Sie haben einen Arbeitsbereich, wenn Sie Ihre ML-Erweiterung gemäß dem obigen Artikel konfiguriert haben.

* Ein trainiertes, für die Bewertung und Bereitstellung bereites Machine Learning-Modell. Wenn Sie dem Beispiel folgen, wird ein Modell bereitgestellt.

* Wenn Sie die Standardeinstellungen für die Azure-Befehlszeilenschnittstelle noch nicht festgelegt haben, speichern Sie Ihre Standardeinstellungen. Um zu vermeiden, dass Sie die Werte für Ihr Abonnement, Ihren Arbeitsbereich und Ihre Ressourcengruppe mehrfach eingeben müssen, führen Sie den folgenden Code aus:

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* Um dem Beispiel zu folgen, klonen Sie das Beispiel-Repository

    ```azurecli
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="define-configuration-yaml-file-for-deployment"></a>Konfigurations-YAML-Datei für die Bereitstellung definieren

Konfigurations-YAML-Datei für die Bereitstellung definieren Weitere Informationen zum YAML-Schema finden Sie im Dokument [YAML-Referenz für verwalteten Onlineendpunkt](reference-yaml-endpoint-managed-online.md).

Die YAML-Dateien in den folgenden Beispielen werden zur Erstellung von Online-Endpunkten verwendet. 

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

Das folgende YAML-Beispiel befindet sich unter `endpoints/online/managed/managed-identities/1-sai-create-endpoint`. Die Datei, 

* Legt den Namen fest, mit dem Sie auf den Endpunkt `my-sai-endpoint` verweisen wollen.
* Gibt die Art der Berechtigung an, die für den Zugriff auf den Endpunkt `auth-mode: key` verwendet werden soll.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

Dieses YAML-Beispiel, `2-sai-deployment.yml`,

* Angabe, dass Sie einen Endpunkt vom Typ `online` erstellen möchten
* Zeigt an, dass der Endpunkt eine zugehörige Bereitstellung mit dem Namen `blue` hat.
* Konfiguration der Bereitstellungsdetails (beispielsweise, welches Modell bereitgestellt und welche Umgebung und welches Bewertungsskript verwendet werden soll)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

Das folgende YAML-Beispiel befindet sich unter `endpoints/online/managed/managed-identities/1-uai-create-endpoint`. Die Datei, 

* Legt den Namen fest, mit dem Sie auf den Endpunkt `my-uai-endpoint` verweisen wollen.
* Gibt die Art der Berechtigung an, die für den Zugriff auf den Endpunkt `auth-mode: key` verwendet werden soll.
* Gibt den zu verwendenden Identitätstyp an, `type: user_assigned`

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

Dieses YAML-Beispiel, `2-sai-deployment.yml`,

* Angabe, dass Sie einen Endpunkt vom Typ `online` erstellen möchten
* Zeigt an, dass der Endpunkt eine zugehörige Bereitstellung mit dem Namen `blue` hat.
* Konfiguration der Bereitstellungsdetails (beispielsweise, welches Modell bereitgestellt und welche Umgebung und welches Bewertungsskript verwendet werden soll)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

---

## <a name="configure-variables-for-deployment"></a>Konfigurieren Sie Variablen für die Bereitstellung

Konfigurieren Sie die Variablennamen für den Arbeitsbereich, den Arbeitsbereichsspeicherort und den Endpunkt, den Sie für die Verwendung mit Ihrer Bereitstellung erstellen möchten.

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

Durch den folgenden Code werden diese Werte als Umgebungsvariablen in Ihren Endpunkt exportiert:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

Geben Sie als Nächstes an, wie Sie Ihr Blobspeicherkonto, Ihren Blobcontainer und Ihre Datei nennen möchten. Diese Variablennamen werden hier definiert und im nächsten Abschnitt in den Befehlen `az storage account create` und `az storage container create` verwendet.

Durch den folgenden Code werden diese Werte als Umgebungsvariablen exportiert:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::

Erstellen Sie nach dem Exportieren dieser Variablen eine lokale Textdatei. Wenn der Endpunkt bereitgestellt wird, greift das Bewertungsskript auf diese Textdatei zu, indem es die vom System zugewiesene verwaltete Identität verwendet, die bei der Erstellung des Endpunkts generiert wurde.

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

Legen Sie den Namen Ihres Endpunkts, Arbeitsbereichs und Arbeitsbereichs fest und exportieren Sie diesen Wert als Umgebungsvariable:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_variables" :::

Geben Sie als Nächstes an, wie Sie Ihr Blobspeicherkonto, Ihren Blobcontainer und Ihre Datei nennen möchten. Diese Variablennamen werden hier definiert und im nächsten Abschnitt in den Befehlen `az storage account create` und `az storage container create` verwendet.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="configure_storage_names" :::

Erstellen Sie nach dem Exportieren dieser Variablen eine lokale Textdatei. Wenn der Endpunkt bereitgestellt wird, greift das Bewertungsskript auf diese Textdatei zu, indem es die dem Benutzer zugewiesene verwaltete Identität verwendet, die im Endpunkt verwendet wird. 

Legen Sie den Namen Ihrer Benutzeridentität fest, und exportieren Sie diesen Wert als Umgebungsvariable:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_user_identity_name" :::

---

## <a name="create-the-managed-identity"></a>Erstellen Sie die verwaltete Identität 
Um auf Azure-Ressourcen zuzugreifen, erstellen Sie eine dem System oder dem Benutzer zugewiesene verwaltete Identität für Ihren Endpunkt. 

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

Wenn Sie [einen Online-Endpunkt](#create-a-managed-online-endpoint) erstellen, wird automatisch eine vom System zugewiesene verwaltete Identität für Sie generiert, so dass Sie keine separate Identität erstellen müssen. 

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

Um eine benutzerzugeordnete verwaltete Identität zu erstellen, gehen Sie wie folgt vor:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_user_identity" :::

---

## <a name="create-storage-account-and-container"></a>Speicherkonto und Container erstellen

Für dieses Beispiel erstellen Sie ein Blob-Speicherkonto und einen Blob-Container und laden dann die zuvor erstellte Textdatei in den Blob-Container hoch. Dies ist das Speicherkonto und der Blob-Container, auf den Sie dem Endpunkt und der verwalteten Identität Zugriff geben. 

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

Erstellen Sie zuerst ein Speicherkonto.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

Als nächstes erstellen Sie den Blob-Container im Speicherkonto.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

Laden Sie anschließend Ihre Textdatei in den Blobcontainer hoch.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

Erstellen Sie zuerst ein Speicherkonto.  

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_account" :::

Sie können eine vorhandene Speicherkonto-ID auch wie folgt abrufen. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_storage_account_id" :::

Als nächstes erstellen Sie den Blob-Container im Speicherkonto. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_container" :::

Dann laden Sie die Datei in den Container hoch. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="upload_file_to_storage" :::

---

## <a name="create-a-managed-online-endpoint"></a>Erstellen eines verwalteten Onlineendpunkts

Durch den folgenden Code wird ein verwalteter Onlineendpunkt ohne Angabe einer Bereitstellung erstellt. 

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)
Wenn Sie einen verwalteten Endpunkt erstellen, wird standardmäßig eine systemseitig zugewiesene verwaltete Identität für den Endpunkt erstellt.

>[!IMPORTANT]
> Systemseitig zugewiesene verwaltete Identitäten sind unveränderlich und können nach der Erstellung nicht mehr geändert werden.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

Überprüfen Sie den Status des Endpunkts wie folgt:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

Falls Probleme auftreten, helfen Ihnen die Informationen unter [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](how-to-troubleshoot-managed-online-endpoints.md) weiter.

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

Überprüfen Sie den Status des Endpunkts wie folgt:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

Falls Probleme auftreten, helfen Ihnen die Informationen unter [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](how-to-troubleshoot-managed-online-endpoints.md) weiter.

---

## <a name="give-access-permission-to-the-managed-identity"></a>Zugriffsberechtigung für die verwaltete Identität erteilen

>[!IMPORTANT] 
> Online-Endpunkte benötigen die Azure Container Registry Pull-Berechtigung, die AcrPull-Berechtigung, für die Container Registry und die Storage Blob Data Reader-Berechtigung für den Standard-Datenspeicher des Arbeitsbereich.

Sie können dem verwalteten Endpunkt die Erlaubnis erteilen, über seine vom System zugewiesene verwaltete Identität auf Ihren Speicher zuzugreifen, oder der vom Benutzer zugewiesenen verwalteten Identität die Erlaubnis erteilen, auf das im vorherigen Abschnitt erstellte Speicherkonto zuzugreifen.

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

Rufen Sie die dem System zugewiesene verwaltete Identität ab, die für Ihren Endpunkt erstellt wurde.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

Anschließend können Sie der systemseitig zugewiesenen verwalteten Identität Zugriff auf Ihren Speicher gewähren.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

Abrufen der dem Benutzer zugewiesenen verwalteten Identitäts-Client-ID.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_client_id" :::

Abrufen der dem Benutzer zugewiesenen verwalteten Identitäts-ID.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_id" :::

Abrufen der mit dem Arbeitsbereich verbundenen Container-Registrierung.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_container_registry_id" :::

Abrufen des Standardspeichers des Arbeitsbereichs.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_workspace_storage_id" :::

Erteilen Sie der dem Benutzer zugewiesenen verwalteten Identität die Genehmigung für ein Speicherkonto.  

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_user_storage_account" :::

Erteilen Sie dem Benutzer, dem eine verwaltete Identität zugewiesen wurde, die Berechtigung zur Containerregistrierung.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_container_registry" :::

Erteilen Sie der dem Benutzer zugewiesenen verwalteten Identität die Berechtigung zur Speicherung im Standardarbeitsbereich.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_workspace_storage_account" :::

---

## <a name="scoring-script-to-access-azure-resource"></a>Bewertungsskript für den Zugriff auf Azure-Ressourcen

Anhand des folgenden Skripts können Sie nachvollziehen, wie Sie mit Ihrem Identitäts-Token auf Azure-Ressourcen zugreifen, in diesem Szenario auf das in den vorherigen Abschnitten erstellte Speicherkonto. 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-with-your-configuration"></a>Erstellen Sie eine Bereitstellung mit Ihrer Konfiguration

Erstellen Sie eine Bereitstellung, die dem verwalteten Endpunkt zugeordnet ist. [Erfahren Sie mehr über die Bereitstellung auf verwalteten Online-Endpunkten](how-to-deploy-managed-online-endpoints.md).

>[!WARNING]
> Diese Bereitstellung dauert etwa acht bis 14 Minuten – je nachdem, ob die zugrunde liegende Umgebung bzw. das zugrunde liegende Image zum ersten Mal erstellt wird. Nachfolgende Bereitstellungen mit der gleichen Umgebung sind schneller.

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

>[!NOTE]
> Der Wert des `--name`-Arguments kann den `name`-Schlüssel in der YAML-Datei außer Kraft setzen.

Überprüfen Sie den Status der Bereitstellung.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

Um die obige Abfrage so zu verfeinern, dass nur bestimmte Daten zurückgegeben werden, siehe [Abfrage der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli).

> [!NOTE]
> Von der init-Methode im Bewertungsskript wird die Datei aus Ihrem Speicherkonto unter Verwendung des Tokens der systemseitig zugewiesenen verwalteten Identität gelesen.

Die Ausgabe der init-Methode kann im Bereitstellungsprotokoll mit dem folgenden Code überprüft werden. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::


# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

>[!Note]
> Der Wert des `--name`-Arguments kann den `name`-Schlüssel in der YAML-Datei außer Kraft setzen.

Sobald der Befehl ausgeführt wurde, können Sie den Status der Bereitstellung überprüfen.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

Um die obige Abfrage so zu verfeinern, dass nur bestimmte Daten zurückgegeben werden, siehe [Abfrage der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli).

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

> [!NOTE]
> Von der init-Methode im Bewertungsskript wird die Datei aus Ihrem Speicherkonto unter Verwendung des Tokens der systemseitig zugewiesenen verwalteten Identität gelesen.

Die Ausgabe der init-Methode kann im Bereitstellungsprotokoll mit dem folgenden Code überprüft werden. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

---

Wenn Ihre Bereitstellung abgeschlossen ist, werden das Modell, die Umgebung und der Endpunkt in Ihrem Azure Machine Learning-Arbeitsbereich registriert.

## <a name="confirm-your-endpoint-deployed-successfully"></a>Vergewissern, dass der Endpunkt erfolgreich bereitgestellt wurde

Vergewissern Sie sich nach dem Bereitstellen des Endpunkts, dass er funktioniert. Details zum Rückschluss variieren von Modell zu Modell. Für diesen Leitfaden sehen die JSON-Abfrageparameter wie folgt aus: 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

Führen Sie zum Aufrufen Ihres Endpunkts Folgendes aus:

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="test_endpoint" :::

---

## <a name="delete-the-endpoint-and-storage-account"></a>Löschen des Endpunkts und des Speicherkontos

Wenn Sie den bereitgestellten Endpunkt und den Speicher nicht mehr benötigen, löschen Sie sie, um Kosten zu sparen. Durch das Löschen des Endpunkts werden auch alle zugeordneten Bereitstellungen gelöscht.

# <a name="system-assigned-managed-identity"></a>[Systemseitig zugewiesene verwaltete Identität](#tab/system-identity)
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[Benutzerseitig zugewiesene verwaltete Identität](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_storage_account" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_user_identity" :::

---

## <a name="next-steps"></a>Nächste Schritte

* [Einsatz und Bewertung eines Modells für maschinelles Lernen mithilfe eines verwalteten Online-Endpunkts (Vorschau)](how-to-deploy-managed-online-endpoints.md).
* Weitere Informationen zur Bereitstellung finden Sie unter [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md).
* Weitere Informationen zum Verwenden der Befehlszeilenschnittstelle (CLI) finden Sie unter [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Informationen dazu, welche Computeressourcen Sie verwenden können, finden Sie unter [SKU-Liste für verwaltete Onlineendpunkte (Vorschau)](reference-managed-online-endpoints-vm-sku-list.md).
* Weitere Informationen zu Kosten finden Sie unter [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md).
* Informationen zur Überwachung von Endpunkten finden Sie unter [Überwachen verwalteter Onlineendpunkte (Vorschau)](how-to-monitor-online-endpoints.md).
* Einschränkungen für verwaltete Endpunkte finden Sie unter [Verwaltung und Erhöhung von Quoten für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

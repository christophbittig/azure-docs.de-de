---
title: Bereitstellen von Modellen mithilfe verwalteter Onlineendpunkte mit REST-APIs (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Modelle mithilfe verwalteter Onlineendpunkte mit REST-APIs bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 724c7a3108f1b0bb699c81444a76e34d795f1705
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056131"
---
# <a name="deploy-models-with-rest-preview"></a>Bereitstellen von Modellen per REST (Vorschau)

Erfahren Sie, wie Sie die Azure Machine Learning-REST-API zum Bereitstellen von Modellen verwenden (Vorschau).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Von der REST-API werden HTTP-Standardverben genutzt, um Ressourcen zu erstellen, abzurufen, zu aktualisieren und zu löschen. Die REST-API funktioniert mit jeder Sprache oder jedem Tool, die bzw. das HTTP-Anforderungen ausführen kann. Durch die unkomplizierte Struktur von REST sind diese APIs eine gute Wahl in Skriptumgebungen und für die MLOps-Automatisierung.

In diesem Artikel wird beschrieben, wie Sie die neuen REST-APIs für folgende Zwecke verwenden:

> [!div class="checklist"]
> * Erstellen von Machine Learning-Ressourcen
> * Erstellen eines einfachen Trainingsauftrags 
> * Erstellen eines Sweep-Auftrags für die Hyperparameteroptimierung

## <a name="prerequisites"></a>Voraussetzungen

- Ein **Azure-Abonnement**, für das Sie über Administratorrechte verfügen. Testen Sie das [kostenlose oder kostenpflichtige persönliche Abonnement](https://azure.microsoft.com/free/), falls Sie nicht über ein Abonnement dieser Art verfügen.
- Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).
- Ein Dienstprinzipal in Ihrem Arbeitsbereich. Für administrative REST-Anforderungen wird die [Dienstprinzipalauthentifizierung](how-to-setup-authentication.md#use-service-principal-authentication) verwendet.
- Ein Token für die Dienstprinzipalauthentifizierung. Führen Sie die Schritte unter [Abrufen eines Tokens für die Dienstprinzipalauthentifizierung](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) aus, um dieses Token abzurufen. 
- Das **curl**-Hilfsprogramm. Das **curl**-Programm ist im [Windows-Subsystem für Linux](/windows/wsl/install-win10) oder jeder beliebigen UNIX-Distribution verfügbar. In PowerShell ist **curl** ein Alias für **Invoke-WebRequest**, und `curl -d "key=val" -X POST uri` wird zu `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="set-endpoint-name"></a>Festlegen von Endpunktnamen

> [!NOTE]
> Endpunktnamen müssen auf Azure-Regionsebene eindeutig sein. Beispielsweise kann es nur einen Endpunkt namens „my-endpoint“ in „westus2“ geben.

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-managed-online-endpoints"></a>Verwaltete Azure Machine Learning-Onlineendpunkte
Verwaltete Onlineendpunkte (Vorschau) gestatten Ihnen die Bereitstellung Ihres Modells, ohne dass Sie die zugrunde liegende Infrastruktur erstellen und verwalten müssen. In diesem Artikel erstellen Sie einen Onlineendpunkt und eine Bereitstellung und überprüfen ihn, indem Sie ihn aufrufen. Zunächst müssen Sie jedoch die für die Bereitstellung erforderlichen Ressourcen registrieren, einschließlich Modell, Code und Umgebung.

Es gibt viele Möglichkeiten, einen Azure Machine Learning-Onlineendpunkt zu erstellen, [einschließlich der Azure-Befehlszeilenschnittstelle](how-to-deploy-managed-online-endpoints.md) und visuell über [Studio](how-to-use-managed-online-endpoint-studio.md). Im folgenden Beispiel wird ein verwalteter Onlineendpunkt mit der REST-API verwendet.

## <a name="create-machine-learning-assets"></a>Erstellen von Machine Learning-Ressourcen

Richten Sie zunächst Ihre Azure Machine Learning-Ressourcen ein, um Ihren Auftrag zu konfigurieren.

In den folgenden REST-API-Aufrufen verwenden wir `SUBSCRIPTION_ID`, `RESOURCE_GROUP`, `LOCATION` und `WORKSPACE` als Platzhalter. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte. 

Für administrative REST-Anforderungen wird ein [Token für die Dienstprinzipalauthentifizierung](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token) angefordert. Ersetzen Sie `TOKEN` durch Ihren eigenen Wert. Sie können dieses Token mit dem folgenden Befehl abrufen:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

Der Dienstanbieter verwendet das `api-version`-Argument, um Kompatibilität zu gewährleisten. Das `api-version`-Argument variiert von Dienst zu Dienst. Legen Sie die API-Version zur Vorbereitung für zukünftige Versionen als Variable fest:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="api_version":::

### <a name="get-storage-account-details"></a>Abrufen von Speicherkontodetails

Um das Modell und den Code zu registrieren, müssen sie zuerst in ein Speicherkonto hochgeladen werden. Die Details des Speicherkontos sind im Datenspeicher verfügbar. In diesem Beispiel rufen Sie den Standarddatenspeicher und das Azure Storage-Konto für Ihren Arbeitsbereich ab. Fragen Sie Ihren Arbeitsbereich mit einer GET-Anforderung ab, um eine JSON-Datei mit den Informationen abzurufen.

Sie können das Tool [jq](https://stedolan.github.io/jq/) verwenden, um das JSON-Ergebnis zu analysieren und die erforderlichen Werte zu ermitteln. Sie können diese Informationen auch über das Azure-Portal ermitteln:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>Hochladen und Registrieren des Codes

Nachdem Sie über den Datenspeicher verfügen, können Sie das Bewertungsskript hochladen. Verwenden Sie die Azure Storage-CLI, um ein Blob in Ihren Standardcontainer hochzuladen:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_code":::

> [!TIP]
> Sie können auch andere Uploadmethoden verwenden, z. B. das Azure-Portal oder [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

Nachdem Sie Ihren Code hochgeladen haben, können Sie ihn mit einer PUT-Anforderung angeben und mit `datastoreId` auf den Datenspeicher verweisen:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>Hochladen und Registrieren des Modells

Laden Sie die Modelldateien ähnlich wie den Code hoch:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_model":::

Registrieren Sie jetzt das Modell:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_model":::

### <a name="create-environment"></a>Erstellen der Umgebung
Die Bereitstellung muss in einer Umgebung ausgeführt werden, die über die erforderlichen Abhängigkeiten verfügt. Erstellen Sie die Umgebung mit einer PUT-Anforderung. Verwenden Sie ein Docker-Image aus Microsoft Container Registry. Sie können das Docker-Image mit `Docker` konfigurieren und Conda-Abhängigkeiten mit `condaFile` hinzufügen.

Im folgenden Codeausschnitt wurde der Inhalt einer Conda-Umgebung (YAML-Datei) in eine Umgebungsvariable gelesen:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_environment":::

### <a name="create-endpoint"></a>Endpunkt erstellen

Erstellen Sie den Onlineendpunkt:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_endpoint":::

### <a name="create-deployment"></a>Bereitstellung erstellen

Erstellen Sie eine Bereitstellung unter dem Endpunkt:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_deployment":::

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>Aufrufen des Endpunkts zum Bewerten von Daten mit Ihrem Modell

Wir benötigen den Bewertungs-URI und das Zugriffstoken, um den Endpunkt aufzurufen. Rufen Sie zunächst den Bewertungs-URI ab:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_endpoint":::

Rufen Sie das Zugriffstoken des Endpunkts ab:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

Rufen Sie jetzt den Endpunkt mit curl auf:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="score_endpoint":::

### <a name="check-the-logs"></a>Überprüfen der Protokolle

Überprüfen Sie die Bereitstellungsprotokolle:

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_deployment_logs":::

### <a name="delete-the-endpoint"></a>Löschen des Endpunkts

Wenn Sie die Bereitstellung nicht verwenden, sollten Sie sie mit dem folgenden Befehl löschen (der Endpunkt und alle zugrunde liegenden Bereitstellungen werden gelöscht):

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie Ihr Modell [mithilfe der Azure-Befehlszeilenschnittstelle](how-to-deploy-managed-online-endpoints.md) bereitstellen.
* Erfahren Sie, wie Sie Ihr Modell [über das Studio](how-to-use-managed-online-endpoint-studio.md) bereitstellen.
* Erfahren Sie mehr über die [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](how-to-troubleshoot-managed-online-endpoints.md).
* Erfahren Sie, wie Sie [mit einem verwalteten Online-Endpunkt und einer verwalteten Identität auf Azure-Ressourcen zugreifen (Vorschau)](how-to-access-resources-from-endpoints-managed-identities.md)
* Erfahren Sie, wie Sie [Onlineendpunkte überwachen](how-to-monitor-online-endpoints.md).
* Erfahren Sie mehr über den [sicheren Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md).
* [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
* [SKU-Liste für verwaltete Onlineendpunkte (Vorschau)](reference-managed-online-endpoints-vm-sku-list.md)
* Weitere Informationen zu Grenzwerten bei verwalteten Onlineendpunkten finden Sie unter [Verwalten und Erhöhen von Kontingenten für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).
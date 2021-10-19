---
title: 'Abrufen des Zugriffstokens mit Azure CLI: Azure Healthcare-APIs für den DICOM-Dienst'
description: In diesem Artikel wird erläutert, wie Sie mithilfe des Azure CLI ein Zugriffstoken für den DICOM-Dienst abrufen.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: b48a36a5f9549fef2d484dd79b938c1a8e833e8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339663"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>Abrufen des Zugriffstokens für den DICOM-Dienst mit Azure CLI

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI ein Zugriffstoken für den DICOM-Dienst abrufen. Wenn Sie [den DICOM-Dienst bereitstellen,](deploy-dicom-services-in-azure.md)konfigurieren Sie eine Gruppe von Benutzern oder Dienstprinzipalen, die Zugriff auf den Dienst haben. Wenn sich Ihre Benutzerobjekt-ID in der Liste der zulässigen Objekt-IDs befindet, können Sie auf den Dienst über ein Token zugreifen, das Sie über die Azure CLI erhalten haben.

## <a name="prerequisites"></a>Voraussetzungen

Verwenden Sie die Bash-Umgebung in Azure Cloud Shell.


[![Starten Sie Azure Cloud Shell. ](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

Wenn Sie möchten, können Sie auch die Azure CLI [installieren](/cli/azure/install-azure-cli), um CLI-Verweisbefehle auszuführen.

* Wenn Sie eine lokale Installation verwenden, melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az_login) bei der Azure CLI an. Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen. Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).
* Installieren Sie die Azure CLI-Erweiterungen bei der ersten Verwendung, wenn Sie dazu aufgefordert werden. Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).
* Führen Sie [az version](/cli/azure/reference-index#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="obtain-a-token"></a>Erhalten eines Tokens

Der DICOM-Dienst verwendet oder `resource` `Audience` mit einem URI, der dem URI des DICOM-Servers  `https://dicom.healthcareapis.azure.com` entspricht. Sie können ein Token erhalten und mit dem folgenden Befehl in einer Variablen (namens `$token`) speichern:


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>Verwenden mit dem DICOM-Dienst

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mithilfe der Azure CLI ein Zugriffstoken für den DICOM-Dienst abrufen. 

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
---
title: Zugreifen auf die Azure Healthcare-APIs mit cURL
description: In diesem Artikel wird erläutert, wie Sie mit cURL auf die ApIs für das Gesundheitswesen zugreifen.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784946"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>Zugreifen auf die ApIs für das Gesundheitswesen (Vorschau) mit cURL 

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie mit cURL auf die Azure Healthcare-APIs zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="powershell"></a>PowerShell

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, installieren Sie [PowerShell](/powershell/module/powershellget/) und [Azure Az PowerShell.](/powershell/azure/install-az-ps)
* Optional können Sie die Skripts in Visual Studio Code mit der Rest-Clienterweiterung ausführen. Weitere Informationen finden Sie unter [Erstellen eines Links zum Restclientdokument](using-rest-client.md).
* Laden Sie [cURL](https://curl.se/download.html)herunter, und installieren Sie es.

### <a name="cli"></a>CLI

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, installieren Sie [Azure CLI](/cli/azure/install-azure-cli). 
* Installieren Sie optional eine Bash-Shell wie Git Bash, die in [Git für Windows](https://gitforwindows.org/)enthalten ist.
* Führen Sie optional die Skripts in Visual Studio Code mit der Rest-Clienterweiterung aus. Weitere Informationen finden Sie unter [Erstellen eines Links zum Restclientdokument](using-rest-client.md).
* Laden Sie [cURL](https://curl.se/download.html)herunter, und installieren Sie es.

## <a name="obtain-azure-access-token"></a>Abrufen des Azure-Zugriffstokens

Vor dem Zugriff auf die Healthcare-APIs müssen Sie dem Benutzer oder der Client-App die richtigen Berechtigungen erteilen. Weitere Informationen zum Erteilen von Berechtigungen finden Sie unter Autorisierung von ApIs im [Gesundheitswesen.](authentication-authorization.md)

Es gibt verschiedene Möglichkeiten, ein Azure-Zugriffstoken für die Healthcare-APIs abzurufen. 

> [!NOTE]
> Stellen Sie sicher, dass Sie sich bei Azure angemeldet haben und sich im Azure-Abonnement und -Mandanten befinden, in dem Sie die Healthcare-APIs-Instanz bereitgestellt haben.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>Zugreifen auf Daten im FHIR-Dienst

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![Zugreifen auf Daten im FHIR-Dienst mit curl-Skript. ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>Zugreifen auf Daten im DICOM-Dienst

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![Zugreifen auf Daten im DICOM-Dienst mit curl-Skript. ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mit cURL auf die Daten der Gesundheits-APIs zugreifen.

Informationen zum Zugreifen auf die Daten der Gesundheits-APIs mithilfe der REST-Clienterweiterung in Visual Studio Code finden Sie unter 

>[!div class="nextstepaction"]
>[Zugreifen auf die ApIs für das Gesundheitswesen mithilfe des REST-Clients](using-rest-client.md)
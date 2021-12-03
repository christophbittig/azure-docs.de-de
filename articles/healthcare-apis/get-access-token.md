---
title: Abrufen des Zugriffstokens mit Azure CLI oder Azure PowerShell
description: In diesem Artikel wird erläutert, wie Sie ein Zugriffstoken für die Healthcare-APIs mithilfe des Azure CLI oder Azure PowerShell.
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d350669abca87156cc5e735151f4a905d98745a8
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814586"
---
# <a name="get-access-token-using-azure-cli-or-azure-powershell"></a>Abrufen des Zugriffstokens mit Azure CLI oder Azure PowerShell

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie ein Zugriffstoken für den FHIR-Dienst und den DICOM-Dienst mithilfe von PowerShell und dem Azure CLI. Weitere Informationen zu den ersten Schritte mit den Gesundheits-APIs finden Sie unter Erste Schritte mit [FHIR]() oder Erste Schritte [mit DICOM.]() 

---
## <a name="obtain-a-token-for-the-fhir-service"></a>Abrufen eines Tokens für den FHIR-Dienst

Der FHIR-Dienst verwendet oder `resource`  mit einem URI, der dem `Audience` URI des FHIR-Servers `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` entspricht. Sie können ein Token erhalten und mit dem folgenden Befehl in einer Variablen (namens `$token`) speichern:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-the-token-with-the-fhir-service"></a>Verwenden des Tokens mit dem FHIR-Dienst

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

## <a name="obtain-a-token-for-the-dicom-service"></a>Abrufen eines Tokens für den DICOM-Dienst

Der DICOM-Dienst verwendet oder `resource` mit einem URI, der dem `Audience` URI des DICOM-Servers  `https://dicom.healthcareapis.azure.com` entspricht. Sie können ein Token erhalten und mit dem folgenden Befehl in einer Variablen (namens `$token`) speichern:


```Azure CLICopy
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-the-tokenb-with-the-dicom-service"></a>Verwenden von tokenb mit dem DICOM-Dienst

```Azure CLICopy
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Nächste Schritte

- [Zugreifen auf FHIR mitHilfe von Postman](use-postman.md)
- [Zugreifen auf den FHIR-Sing-REST-Client](using-rest-client.md)
- [Zugreifen auf DICOM mit cUrl](dicom/dicomweb-standard-apis-curl.md)


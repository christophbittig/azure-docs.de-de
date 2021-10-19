---
title: Abrufen des Zugriffstokens mit Azure CLI oder Azure PowerShell – FHIR-Dienst
description: In diesem Artikel wird erläutert, wie Sie ein Zugriffstoken für den FHIR-Dienst mithilfe Azure CLI oder Azure PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619429"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>Abrufen des Zugriffstokens für den FHIR-Dienst Azure CLI oder Azure PowerShell

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

In diesem Artikel erfahren Sie, wie Sie ein Zugriffstoken für den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) mithilfe der Azure CLI. Wenn Sie [den FHIR-Dienst bereitstellen,](fhir-portal-quickstart.md)konfigurieren Sie eine Gruppe von Benutzern oder Dienstprinzipals, die Zugriff auf den Dienst haben. Wenn sich Ihre Benutzerobjekt-ID in der Liste der zulässigen Objekt-IDs befindet, können Sie auf den Dienst über ein Token zugreifen, das Sie über die Azure CLI erhalten haben.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

In diesem Artikel erfahren Sie, wie Sie ein Zugriffstoken für den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) mithilfe von Azure PowerShell. Wenn Sie [den FHIR-Dienst bereitstellen,](fhir-portal-quickstart.md)konfigurieren Sie eine Gruppe von Benutzern oder Dienstprinzipals, die Zugriff auf den Dienst haben. Wenn ihre Benutzerobjekt-ID in der Liste der zulässigen Objekt-IDs enthalten ist, können Sie auf den Dienst zugreifen, indem Sie ein Token verwenden, das mithilfe von Azure PowerShell.

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>Erhalten eines Tokens

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

## <a name="use-with-fhir-service"></a>Verwenden mit dem FHIR-Dienst

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[Zugriff auf die FHIR-API mit Postman](../use-postman.md)

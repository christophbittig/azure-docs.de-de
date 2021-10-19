---
title: 'Abrufen von Zugriffstoken mithilfe der Azure CLI: Azure API for FHIR'
description: In diesem Artikel wird erläutert, wie Sie ein Zugriffstoken für die Azure API for FHIR über die Azure CLI erhalten.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: b955fd521224197a3505bd24f7ff0d02ec95fb5d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748956"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Abrufen von Zugriffstoken für die Azure API for FHIR mit Azure CLI

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI ein Zugriffstoken für die Azure API for FHIR erhalten. Wenn Sie [die Azure API for FHIR bereitstellen](fhir-paas-portal-quickstart.md), konfigurieren Sie eine Reihe von Benutzern oder Dienstprinzipalen mit Zugriff auf den Dienst. Wenn sich Ihre Benutzerobjekt-ID in der Liste der zulässigen Objekt-IDs befindet, können Sie auf den Dienst über ein Token zugreifen, das Sie über die Azure CLI erhalten haben.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Erhalten eines Tokens

Die Azure API for FHIR verwendet `resource` oder `Audience` mit einer URI, die der URI des FHIR-Servers entspricht: `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`. Sie können ein Token erhalten und mit dem folgenden Befehl in einer Variablen (namens `$token`) speichern:

```azurecli-interactive
$token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Verwenden mit Azure API for FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mithilfe der Azure CLI ein Zugriffstoken für die Azure API for FHIR erhalten. Informationen zum Zugriff auf die FHIR-API mit Postman finden Sie im Tutorial zu Postman.

>[!div class="nextstepaction"]
>[Zugreifen auf den FHIR-Dienst mithilfe von Postman](./../use-postman.md)

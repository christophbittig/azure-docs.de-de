---
title: Azure API Management-Back-Ends | Microsoft-Dokumentation
description: Informationen zu benutzerdefinierten Back-Ends in API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647117"
---
# <a name="backends-in-api-management"></a>Back-Ends in API Management

Ein *Back-End* (oder *API-Back-End*) in API Management ist ein HTTP-Dienst, der Ihre Front-End-API und die zugehörigen Vorgänge implementiert.

Beim Importieren bestimmter APIs wird das API-Back-End in API Management automatisch konfiguriert. Das API Management konfiguriert zum Beispiel das Back-End beim Importieren:
* An [OpenAPI Spezifikation](import-api-from-oas.md).
* Eine [SOAP API](import-soap-api.md).
* Azure-Ressourcen, wie z.B. eine HTTP-ausgelöste [Azure Funktions-App](import-function-app-as-api.md) oder [Logik-App](import-logic-app-as-api.md).

API Management unterstützt auch die Verwendung anderer Azure-Ressourcen als API-Back-End, wie z.B.:
* Ein [Service Fabric cluster](how-to-configure-service-fabric-backend.md).
* Ein benutzerdefinierter Dienst. 

Benutzerdefinierte Back-Ends erfordern eine zusätzliche Konfiguration, um die Anmeldeinformationen für Anforderungen an den Back-End-Dienst zu autorisieren und API-Vorgänge zu definieren. Konfigurieren und verwalten Sie benutzerdefinierte Back-Ends im Azure-Portal: oder mithilfe von Azure-APIs oder -Tools.

Nach dem Erstellen eines Back-Ends können Sie in Ihren APIs auf die Back-End-URL verweisen. Verwenden Sie die Richtlinie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService), um eine eingehende API-Anforderung an das benutzerdefinierte Back-End anstatt an das Standard-Back-End der entsprechenden API umzuleiten.

## <a name="benefits-of-backends"></a>Vorteile von Back-Ends

Ein benutzerdefiniertes Back-End bietet verschiedene Vorteile:

* Fasst Informationen über den Back-End-Dienst zusammen, fördert die Wiederverwendbarkeit über APIs hinweg und verbessert die Governance.  
* Einfache Verwendung durch Konfigurieren einer Transformationsrichtlinie für eine vorhandene API.
* Nutzt die API Management-Funktionalität, um Geheimnisse in Azure Key Vault zu verwalten, wenn [benannte Werte](api-management-howto-properties.md) für die Header- oder Query-Parameter-Authentifizierung konfiguriert sind.

## <a name="limitation"></a>Einschränkung

Für die Ebenen **Developer** und **Premium** Ebenen kann eine API Management-Instanz, die in einem [internen virtuellen Netz](api-management-using-with-internal-vnet.md) bereitgestellt wird, einen HTTP 500 `BackendConnectionFailure`-Fehler auslösen, wenn die Gateway-Endpunkt-URL und die Back-End-URL identisch sind. Wenn Sie auf diese Einschränkung stoßen, befolgen Sie die Anweisungen im Artikel [Self-Chained API Management request limitation in internal virtual network mode](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417) im Tech Community Blog. 

## <a name="next-steps"></a>Nächste Schritte

* Einrichten eines [Service Fabric-Back-Ends](how-to-configure-service-fabric-backend.md) über das Azure-Portal
* Back-Ends können auch über die [REST-API](/rest/api/apimanagement), mit [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) oder [Azure Resource Manager-Vorlagen](../service-fabric/service-fabric-tutorial-deploy-api-management.md) in API Management konfiguriert werden.


---
title: Registrieren einer Ressourcen-App in Azure AD – Azure API for FHIR
description: Registrieren Sie eine Ressourcen-App (oder API-App) in Azure Active Directory, damit Clientanwendungen bei der Authentifizierung Zugriff auf die Ressource anfordern können.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: e8453a2b82051dc7e63d1193947747d839478427
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355727"
---
# <a name="register-a-resource-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registrieren einer Ressourcenanwendung in Azure Active Directory für Azure API for FHIR

In diesem Artikel erfahren Sie, wie Sie eine Ressourcenanwendung (oder API-Anwendung) in Azure Active Directory registrieren. Eine Ressourcenanwendung ist eine Azure Active Directory-Darstellung der FHIR-Server-API selbst, und Clientanwendungen können bei der Authentifizierung den Zugriff auf die Ressource anfordern. Die Ressourcenanwendung wird im OAuth-Kontext auch als *Zielgruppe* bezeichnet.

## <a name="azure-api-for-fhir"></a>Azure-API für FHIR

Wenn Sie Azure API for FHIR verwenden, wird automatisch eine Ressourcenanwendung erstellt, wenn Sie den Dienst bereitstellen. Verwenden Sie Azure API for FHIR in dem Azure Active Directory-Mandanten, in dem Sie auch Ihre Anwendung bereitstellen, so können Sie diese Anleitung überspringen und stattdessen mit der Bereitstellung von Azure API for FHIR beginnen.

Wenn Sie einen anderen Azure Active Directory-Mandanten verwenden (der nicht Ihrem Abonnement zugeordnet ist), können Sie die Azure API for FHIR-Ressourcenanwendung mit PowerShell in Ihren Mandanten importieren:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

Alternativ können Sie die Azure-Befehlszeilenschnittstelle verwenden:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR-Server für Azure

Wenn Sie den Open-Source-FHIR-Server für Azure verwenden, führen Sie die folgenden Schritte im [GitHub-Repository](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) aus, um eine Ressourcenanwendung zu registrieren. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Ressourcenanwendung in Azure Active Directory registrieren. Als Nächstes registrieren Sie Ihre vertrauliche Clientanwendung.
 
>[!div class="nextstepaction"]
>[Registrieren einer vertraulichen Clientanwendung](register-confidential-azure-ad-client-app.md)
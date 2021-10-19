---
title: 'Ermitteln von Identitätsobjekt-IDs für die Authentifizierung: Azure API for FHIR'
description: In diesem Artikel wird erläutert, wie Sie die Identitätsobjekt-IDs ausfindig machen, die zum Konfigurieren der Authentifizierung für Azure API for FHIR erforderlich sind.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: zxue
ms.openlocfilehash: 96dfc3b10a1a4a9b4f12e8d9decb5d01446385f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355816"
---
# <a name="find-identity-object-ids-for-authentication-configuration-for-azure-api-for-fhir"></a>Suchen von Identitätsobjekt-IDs für die Authentifizierungskonfiguration Azure API for FHIR

In diesem Artikel erfahren Sie, wie Sie Identitätsobjekt-IDs ermitteln, die beim Konfigurieren von Azure API for FHIR erforderlich sind, um [einen externen oder sekundären Active Directory-Mandanten](configure-local-rbac.md) für die Datenebene zu verwenden.

## <a name="find-user-object-id"></a>Ermitteln einer Benutzerobjekt-ID

Ist ein Benutzer mit dem Benutzernamen `myuser@contoso.com` vorhanden, können Sie das `ObjectId`-Element des Benutzers mithilfe des folgenden PowerShell-Befehls ermitteln:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

Alternativ können Sie die Azure-Befehlszeilenschnittstelle verwenden:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Ermitteln einer Dienstprinzipalobjekt-ID

Wenn Sie eine [Dienstclient-App](register-service-azure-ad-client-app.md) registriert haben und diesem Dienstclient den Zugriff auf Azure API for FHIR erlauben möchten, können Sie die Objekt-ID für den Clientdienstprinzipal mit dem folgenden PowerShell-Befehl ermitteln:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

Dabei steht `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` für die Anwendungs-ID des Dienstclients. Alternativ können Sie das `DisplayName`-Element des Dienstclients verwenden:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Bei Nutzung der Azure-Befehlszeilenschnittstelle können Sie Folgendes verwenden:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Ermitteln einer Sicherheitsgruppenobjekt-ID

Wenn Sie die Objekt-ID einer Sicherheitsgruppe ermitteln möchten, können Sie den folgenden PowerShell-Befehl verwenden:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Dabei ist `mygroup` der Name der Gruppe, an der Sie interessiert sind.

Bei Nutzung der Azure-Befehlszeilenschnittstelle können Sie Folgendes verwenden:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Identitätsobjekt-IDs ermitteln, die zum Konfigurieren von Azure API for FHIR erforderlich sind, um einen externen oder sekundären Azure Active Directory-Mandanten zu verwenden. Im nächsten Artikel erfahren Sie, wie Sie die Objekt-IDs zum Konfigurieren lokaler RBAC-Einstellungen verwenden:
 
>[!div class="nextstepaction"]
>[Konfigurieren lokaler RBAC-Einstellungen](configure-local-rbac.md)

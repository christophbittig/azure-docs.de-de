---
title: Ändern des Authentifizierungstyps der Unterdomäne mithilfe von PowerShell und Graph – Azure Active Directory | Microsoft-Dokumentation
description: Ändern Sie die Standardauthentifizierungseinstellungen der Unterdomäne, die von den Einstellungen der Stammdomäne in Azure Active Directory geerbt werden.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 11/05/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8a0ab89e8437edec176e7033665b627df6cd493
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866485"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Ändern des Authentifizierungstyps der Unterdomäne in Azure Active Directory

Nachdem Azure Active Directory (Azure AD) eine Stammdomäne hinzugefügt wurde, erben alle nachfolgenden untergeordneten Domänen, die diesem Stamm in Ihrer Azure AD-Organisation hinzugefügt werden, automatisch die Authentifizierungseinstellung von der Stammdomäne. Wenn Sie jedoch Domänenauthentifizierungseinstellungen unabhängig von den Einstellungen der Stammdomäne verwalten möchten, können Sie dies jetzt über die Microsoft Graph-API ausführen. Wenn Sie z. B. eine Verbundstammdomäne wie contoso.com haben, können Sie anhand dieses Artikels eine Unterdomäne wie child.contoso.com als verwaltet anstatt als Verbund bestätigen.

Wenn die übergeordnete Domäne im Azure AD-Portal eine Verbunddomäne ist und der Administrator versucht, auf der Seite **Benutzerdefinierte Domänennamen** eine verwaltete Unterdomäne zu überprüfen, wird die Fehlermeldung „Fehler beim Hinzufügen der Domäne“ mit der Begründung „Mindestens eine Eigenschaft enthält ungültige Werte“ angezeigt. Wenn Sie versuchen, diese Unterdomäne aus dem Microsoft 365 Admin Center hinzuzufügen, erhalten Sie einen ähnlichen Fehler. Weitere Informationen zu diesem Fehler finden Sie unter [Eine untergeordnete Domäne erbt keine Änderungen der übergeordneten Domäne in Office 365, Azure oder Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Überprüfen einer benutzerdefinierten Unterdomäne

Da Unterdomänen standardmäßig den Authentifizierungstyp der Stammdomäne erben, müssen Sie die Unterdomäne mithilfe Microsoft Graph auf eine Azure AD-Stammdomäne höher stufen, damit Sie den Authentifizierungstyp auf den gewünschten Typ festlegen können.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Hinzufügen der Unterdomäne und Anzeigen ihres Authentifizierungstyps

1. Verwenden Sie PowerShell, um die neue Unterdomäne hinzuzufügen, die den Standardauthentifizierungstyp der Stammdomäne besitzt. Azure AD Admin Center und Microsoft 365 Admin Center unterstützen diesen Vorgang noch nicht.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Verwenden Sie das folgende Beispiel, um GET für die Domäne auszuführen. Da die Domäne keine Stammdomäne ist, erbt sie den Authentifizierungstyp der Stammdomäne. Der Befehl und die Ergebnisse können wie folgt aussehen, wobei Sie Ihre eigene Mandanten-ID verwenden:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-microsoft-graph-api-to-make-this-a-root-domain"></a>Verwenden der Microsoft Graph-API, um diese Domäne zu einer Stammdomäne zu machen

Verwenden Sie den folgenden Befehl zum Höherstufen der Unterdomäne:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Ändern des Authentifizierungstyps der Unterdomäne

1. Verwenden Sie den folgenden Befehl, um den Authentifizierungstyp der Unterdomäne zu ändern:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Überprüfen Sie über die Microsoft Graph-API mit GET, ob der Authentifizierungstyp der Unterdomäne jetzt verwaltet ist:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen benutzerdefinierter Domänennamen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Verwalten von Domänennamen](domains-manage.md)
- [ForceDelete eines benutzerdefinierten Domänennamens mit der Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
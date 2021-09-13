---
title: Authentifizierung
description: Informationen zur Funktionsweise der Authentifizierung
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 01b2dbaa8ed318f08fd68660078ae6fb9923221d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969737"
---
# <a name="configure-authentication"></a>Konfigurieren der Authentifizierung

Azure Remote Rendering verwendet denselben Authentifizierungsmechanismus wie [Azure Spatial Anchors (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Clients müssen *eins* der folgenden Elemente festlegen, um die REST-APIs erfolgreich aufzurufen:

* **AccountKey** kann auf der Registerkarte „Schlüssel“ für das Remote Rendering-Konto im Azure-Portal abgerufen werden. Kontoschlüssel werden nur für Entwicklung/Prototypen empfohlen.
    ![Account ID](./media/azure-account-primary-key.png)

* **AccountDomain** kann auf der Registerkarte „Übersicht“ für das Remote Rendering-Konto im Azure-Portal abgerufen werden.
    ![Kontodomäne](./media/azure-account-domain.png)

* **AuthenticationToken** ist ein Azure AD-Token, das mithilfe der [MSAL-Bibliothek](../../active-directory/develop/msal-overview.md) abgerufen werden kann. Es sind mehrere verschiedene Abläufe zum Akzeptieren von Benutzeranmeldeinformationen und zum Verwenden dieser Anmeldeinformationen zum Abrufen eines Zugriffstokens verfügbar.

* **MRAccessToken** ist ein MR-Token, das beim Sicherheitstokendienst (Security Token Service, STS) von Azure Mixed Reality abgerufen werden kann. Wird beim `https://sts.<accountDomain>`-Endpunkt mithilfe eines ähnlichen wie dem unten dargestellten REST-Aufruf abgerufen:

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Dabei weist der Autorisierungsheader das folgende Format auf : `Bearer <Azure_AD_token>` oder `Bearer <accoundId>:<accountKey>`. Aus Sicherheitsgründen ist das erste Format vorzuziehen. Das durch diesen REST-Aufruf zurückgegebene Token ist das MR-Zugriffstoken.

## <a name="authentication-for-deployed-applications"></a>Authentifizierung für bereitgestellte Anwendungen

Kontoschlüssel werden nur während der Entwicklung für die schnelle Prototyperstellung empfohlen. Es wird empfohlen, Anwendungen, die einen eingebetteten Kontoschlüssel enthalten, nicht an die Produktion auszuliefern. Stattdessen wird empfohlen, eine benutzerbasierte oder dienstbasierte Azure AD-Authentifizierung zu verwenden.

### <a name="azure-ad-user-authentication"></a>Azure AD-Benutzerauthentifizierung

Die Azure AD-Authentifizierung wird in der [Dokumentation zu Azure Spatial Anchors](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) beschrieben.

Führen Sie die Schritte zum Konfigurieren der Azure Active Directory-Benutzerauthentifizierung im Azure-Portal aus.

1. Registrieren Sie Ihre Anwendung in Azure Active Directory. Im Rahmen der Registrierung muss festgelegt werden, ob Ihre Anwendung mehrinstanzenfähig sein soll. Außerdem müssen auf dem Blatt „Authentifizierung“ die Umleitungs-URLs angegeben werden, die für Ihre Anwendung zulässig sind.
:::image type="content" source="./media/azure-active-directory-app-setup.png" alt-text="Einrichtung der Authentifizierung":::

1. Fordern Sie auf der Registerkarte **API-Berechtigungen** unter **mixedreality** delegierte Berechtigungen für den Bereich **mixedreality.signin** an.
:::image type="content" source="./media/azure-active-directory-app-api-permissions.png" alt-text="API-Berechtigungen":::

1. Erteilen Sie unter „Sicherheit“ auf der Registerkarte „Berechtigungen“ die Administratoreinwilligung. :::image type="content" source="./media/azure-active-directory-grant-admin-consent.png" alt-text="Administratoreinwilligung":::

1. Navigieren Sie anschließend zu Ihrer Azure Remote Rendering-Ressource. Erteilen Sie im Bereich für die Zugriffssteuerung die gewünschten [Rollen](#azure-role-based-access-control) für Ihre Anwendungen und Benutzer, in deren Namen Sie delegierte Zugriffsberechtigungen für Ihre Azure Remote Rendering-Ressource verwenden möchten.
:::image type="content" source="./media/azure-remote-rendering-add-role-assignment.png" alt-text="Hinzufügen von Berechtigungen":::
:::image type="content" source="./media/azure-remote-rendering-role-assignments.png" alt-text="Rollenzuweisungen":::

Informationen zur Verwendung der Azure AD-Benutzerauthentifizierung in Ihrem Anwendungscode finden Sie im Tutorial „Schützen von Azure Remote Rendering und Modellspeicher“ unter [Azure Active Directory-Authentifizierung (Azure AD)](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication).

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Zum Steuern der Zugriffsebene, die Ihrem Dienst erteilt wird, verwenden Sie beim Erteilen von rollenbasiertem Zugriff die folgenden Rollen:

* **Remote Rendering-Administrator**: Bietet dem Benutzer Konvertierungs-, Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering.
* **Remote Rendering-Client**: Bietet dem Benutzer Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Kontos](create-an-account.md)
* [Verwenden der Azure-Front-End-APIs für die Authentifizierung](frontend-apis.md)
* [PowerShell-Beispielskripts](../samples/powershell-example-scripts.md)

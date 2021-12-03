---
title: Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory
titleSuffix: Azure API Management
description: Erfahren Sie, wie Benutzer mithilfe von Azure Active Directory in Azure API Management autorisiert werden.
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: fd45ad3a77ddfe9bea46c9c816b67be0c2cf1fac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579158"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management

In diesem Artikel lernen Sie Folgendes:
> [!div class="checklist"]
> * Aktivieren des Zugriffs auf das Entwicklerportal für Benutzer aus Azure Active Directory (Azure AD)
> * Verwalten von Azure AD-Benutzergruppen durch Hinzufügen externer Gruppen, die die Benutzer enthalten

## <a name="prerequisites"></a>Voraussetzungen

- Absolvieren Sie den Schnellstart unter [Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe des Azure-Portals](get-started-create-service-instance.md).
- [Importieren und veröffentlichen](import-and-publish.md) Sie eine Azure API Management-Instanz.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorisieren von Entwicklerkonten mithilfe von Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Select ![Pfeilsymbol](./media/api-management-howto-aad/arrow.png).
1. Suchen Sie die **API Management-Dienste**, und wählen Sie sie aus.
1. Wählen Sie Ihre API Management-Dienstinstanz aus.
1. Wählen Sie unter **Entwicklerportal** die Option **Identitäten** aus.
1. Wählen Sie oben **+Hinzufügen** aus, um den Bereich **Identitätsanbieter hinzufügen** auf der rechten Seite zu öffnen.
1. Wählen Sie im Dropdownmenü unter **Typ** Ihre neue **Azure Active Directory**-Instanz aus.
    * Nach der Auswahl können Sie weitere erforderliche Informationen eingeben. 
    * Zu den Informationen gehören die **Client-ID** und **der geheime Clientschlüssel**. 
    * Informationen zu diesen Steuerelementen finden Sie weiter unten in diesem Artikel.
1. Speichern Sie die **Umleitungs-URL**, da Sie sie später benötigen.
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Identitätsanbieter im Azure-Portal hinzufügen":::

    > [!NOTE]
    > Es gibt zwei Umleitungs-URLs:<br/>
    > * Die **Umleitungs-URL** verweist auf das neueste Entwicklerportal von API Management.
    > * Die **Umleitungs-URL (veraltetes Portal)** verweist auf das als veraltet markierte Entwicklerportal von API Management.
    >
    > Es empfiehlt sich, die Umleitungs-URL zum neuesten Entwicklerportal zu verwenden.
   
1. Öffnen Sie das Azure-Portal in Ihrem Browser auf einer neuen Registerkarte. 
1. Navigieren Sie zu [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908), um eine App in Active Directory zu registrieren.
1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** folgende Werte fest:
    
    * Legen Sie für **Name** einen aussagekräftigen Namen fest, z. B. *Entwicklerportal*.
    * Legen Sie **Unterstützte Kontotypen** auf **Nur Konten in diesem Organisationsverzeichnis** fest. 
    * Legen Sie den **Umleitungs-URI** auf den Wert fest, den Sie in Schritt 9 gespeichert haben. 
    * Wählen Sie **Registrieren**. 

1.  Kopieren Sie nach der Registrierung der Anwendung die **Anwendungs-ID (Client)** von der Seite **Übersicht**. 
1. Wechseln Sie zur Browserregisterkarte mit Ihrer API Management-Instanz. 
1. Fügen Sie den Wert **Anwendungs-ID (Client)** im Fenster **Identitätsanbieter hinzufügen** in das Feld **Client-ID** ein.
1. Wechseln Sie zur Browserregisterkarte mit der App-Registrierung.
1. Wählen Sie die entsprechende App-Registrierung aus.
1. Wählen Sie im Abschnitt **Verwalten** des Seitenmenüs **Zertifikate und Geheimnisse** aus. 
1. Wählen Sie auf der Seite **Zertifikate und Geheimnisse** unter **Geheime Clientschlüssel** die Schaltfläche **Neuer geheimer Clientschlüssel** aus. 
    * Geben Sie eine **Beschreibung** ein.
    * Wählen Sie eine beliebige Option für **Gültig bis** aus.
    * Wählen Sie **Hinzufügen** aus. 
1. Kopieren Sie den Client **Geheimniswert**, bevor Sie die Seite verlassen. Sie benötigen sie später. 
1. Wählen Sie im Seitenmenü unter **Verwalten** die Option **Authentifizierung** aus.
1. Aktivieren Sie in den Abschnitten **Implizite Genehmigung und Hybridflows** das Kontrollkästchen **ID-Token**.
1. Wechseln Sie zur Browserregisterkarte mit Ihrer API Management-Instanz. 
1. Fügen Sie das Geheimnis im Bereich **Identitätsanbieter hinzufügen** in das Feld **Geheimer Clientschlüssel** ein.

    > [!IMPORTANT]
    > Aktualisieren Sie den **geheimen Clientschlüssel**, bevor er abläuft. 

1. Geben Sie im Bereich **Identitätsanbieter hinzufügen** im Feld **Zulässige Mandanten** die Domänen der Azure AD-Instanzen an, denen Sie Zugriff auf die APIs der API Management-Dienstinstanz gewähren möchten. 
    * Sie können mehrere Domänen durch neue Zeilen, Leerzeichen oder Kommas trennen.

    > [!NOTE]
    > Sie können im Abschnitt **Zulässige Mandanten** mehrere Domänen angeben. Ein globaler Administrator muss der Anwendung Zugriff auf Verzeichnisdaten gewähren, bevor Benutzer sich aus einer anderen Domäne als der ursprünglichen App-Registrierungsdomäne anmelden können. Um Berechtigungen zu erteilen, müssen Sie als globaler Administrator folgendermaßen vorgehen:
    > 1. Wechseln Sie zu `https://<URL of your developer portal>/aadadminconsent` (z. B. `https://contoso.portal.azure-api.net/aadadminconsent`).
    > 1. Geben Sie den Domänennamen des Azure AD-Mandanten ein, auf den Zugriff gewährt werden soll.
    > 1. Klicken Sie auf **Submit** (Senden). 

1.  Nachdem Sie die gewünschte Konfiguration angegeben haben, wählen Sie **Hinzufügen** aus.

Nach dem Speichern der Änderungen können sich Benutzer in der angegebenen Azure AD-Instanz [mit einem Azure AD-Konto beim Entwicklerportal anmelden](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Hinzufügen einer externen Azure AD-Gruppe

Nachdem Sie den Zugriff für Benutzer in einem Azure AD-Mandanten aktiviert haben, können Sie folgende Aktionen ausführen:
* Hinzufügen von Azure AD-Gruppen zu API Management 
* Steuern der Sichtbarkeit von Produkten mit Azure AD-Gruppen

Befolgen Sie diese Schritte zur Erteilung:
* `Directory.Read.All` Anwendungserlaubnis für Microsoft Graph API und Azure Active Directory Graph API.
* `User.Read` delegierte Berechtigung für Microsoft Graph API. 

1. Passen Sie die ersten 3 Zeilen des folgenden PowerShell-Skripts an Ihre Umgebung an und führen Sie es aus. 
   ```powershell
   $subId = "Your Azure subscription ID" #e.g. "1fb8fadf-03a3-4253-8993-65391f432d3a"
   $tenantId = "Your Azure AD Tenant or Organization ID" #e.g. 0e054eb4-e5d0-43b8-ba1e-d7b5156f6da8"
   $appObjectID = "Application Object ID that has been registered in AAD" #e.g. "2215b54a-df84-453f-b4db-ae079c0d2619"
   #Login and Set the Subscription
   az login
   az account set --subscription $subId
   #Assign the following permissions: Microsoft Graph Delegated Permission: User.Read, Microsoft Graph Application Permission: Directory.ReadAll,  Azure Active Directory Graph Application Permission: Directory.ReadAll (legacy)
   az rest --method PATCH --uri "https://graph.microsoft.com/v1.0/$($tenantId)/applications/$($appObjectID)" --body "{'requiredResourceAccess':[{'resourceAccess': [{'id': 'e1fe6dd8-ba31-4d61-89e7-88639da4683d','type': 'Scope'},{'id': '7ab1d382-f21e-4acd-a863-ba3e13f7da61','type': 'Role'}],'resourceAppId': '00000003-0000-0000-c000-000000000000'},{'resourceAccess': [{'id': '5778995a-e1bf-45b8-affa-663a9f3f4d04','type': 'Role'}], 'resourceAppId': '00000002-0000-0000-c000-000000000000'}]}"
   ```
2. Melden Sie sich ab und melden Sie sich wieder am Azure-Portal an.
3. Navigieren Sie zur Seite App-Registrierung für die Anwendung, die Sie im vorherigen Abschnitt [ registriert haben](#authorize-developer-accounts-by-using-azure-ad). 
4. Klicken Sie auf **API-Berechtigungen**. Sie sollten die vom PowerShell-Skript in Schritt 1 gewährten Berechtigungen sehen. 
5. Wählen Sie **Administratoeinwilligung für {tenantname} erteilen** aus, damit Sie allen Benutzern in diesem Verzeichnis den Zugriff gewähren. 

Jetzt können Sie externe Azure AD-Gruppen über die Registerkarte **Gruppen** Ihrer API Management-Instanz hinzufügen.

1. Wählen Sie im Seitenmenü unter **Entwicklerportal** die Option **Gruppen** aus.
2. Wählen Sie die Schaltfläche **Azure AD-Gruppe hinzufügen** aus.

   ![Schaltfläche „AAD-Gruppe hinzufügen“](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Wählen Sie in der Dropdownliste den **Mandanten** aus. 
2. Suchen Sie nach der Gruppe, die Sie hinzufügen möchten, und wählen Sie sie aus.
3. Klicken Sie auf die Schaltfläche **Auswählen**.

Nachdem Sie eine externe Azure AD-Gruppe hinzugefügt haben, können Sie ihre Eigenschaften überprüfen und konfigurieren: 
1. Wählen Sie den Namen der Gruppe auf der Registerkarte **Gruppen** aus. 
2. Bearbeiten Sie den **Namen** und die **Beschreibung** für die Gruppe.
 
Benutzer aus der konfigurierten Azure AD-Instanz können jetzt folgende Aktionen ausführen:
* Sie können sich beim Entwicklerportal anmelden. 
* Sie können beliebige Gruppen anzeigen und abonnieren, die für sie sichtbar sind.

> [!NOTE]
> Weitere Informationen zum Unterschied zwischen den Berechtigungstypen **Delegiert** und **Anwendung** finden Sie im Artikel [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../active-directory/develop/v2-permissions-and-consent.md#permission-types).

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Entwicklerportal: Hinzufügen der Azure AD-Kontoauthentifizierung

Im Entwicklerportal können Sie sich mit Azure AD anmelden, indem Sie das Widget **Anmeldeschaltfläche: OAuth** verwenden, das sich auf der Anmeldeseite des standardmäßigen Entwicklerportals befindet.

Es wird zwar automatisch ein neues Konto erstellt, wenn sich ein neuer Benutzer mit Azure AD anmeldet, aber Sie können trotzdem erwägen, dieses Widget auch auf der Registrierungsseite hinzuzufügen. Das Widget **Registrierungsformular: OAuth** stellt ein Formular dar, das zum Registrieren bei OAuth verwendet wird.

> [!IMPORTANT]
> Sie müssen [das Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Azure AD-Änderungen wirksam werden.

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>Als veraltet markiertes Entwicklerportal: Anmelden mit Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

So melden Sie sich mit einem in den vorherigen Abschnitten konfigurierten Azure AD-Konto beim Entwicklerportal an:

1. Öffnen Sie ein neues Browserfenster, und geben Sie die Anmelde-URL aus der Active Directory-Anwendungskonfiguration ein. 
2. Wählen Sie **Azure Active Directory** aus.

   ![Anmeldeseite][api-management-dev-portal-signin]

1. Geben Sie die Anmeldeinformationen eines Benutzers in Azure AD ein.
2. Wählen Sie **Anmelden**.

   ![Anmeldung mit Benutzername und Kennwort][api-management-aad-signin]

1. Wenn Sie zur Eingabe eines Registrierungsformulars aufgefordert werden, geben Sie alle erforderlichen zusätzlichen Informationen an. 
2. Wählen Sie **Registrieren** aus.

   ![Schaltfläche „Anmelden“ auf dem Registrierungsformular][api-management-complete-registration]

Sie sind jetzt beim Entwicklerportal für Ihre API Management-Dienstinstanz angemeldet.

![Entwicklerportal nach Abschluss der Registrierung][api-management-registration-complete]

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie ein [Web-API-Back-End in API Management mithilfe der OAuth 2.0-Autorisierung mit Azure AD schützen](./api-management-howto-protect-backend-with-aad.md).
- Weitere Informationen zu [Azure Active Directory und OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md)
- Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
- Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](./api-management-howto-mutual-certificates.md).
- [Erstellen einer API Management-Dienstinstanz](./get-started-create-service-instance.md)
- [Verwalten Ihrer ersten API](./import-and-publish.md)

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account

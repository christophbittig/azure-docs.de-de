---
title: Konfigurieren von Azure AD-Authentifizierung
description: Erfahren Sie, wie Sie die Azure Active Directory-Authentifizierung als Identitätsanbieter für Ihre App Services- oder Azure Functions-App konfigurieren.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 10/26/2021
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: cc5761afe295bd9ef71c86cf6ffdafb8c0e847ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435831"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dieser Artikel zeigt Ihnen, wie Sie die Authentifizierung für Azure App Service oder Azure Functions so konfigurieren, dass Ihre App Benutzer mit der [Microsoft Identity Platform](../active-directory/develop/v2-overview.md) (Azure AD) als Authentifizierungsanbieter anmeldet.

Mit der APP Service-Authentifizierungsfunktion kann automatisch eine APP-Registrierung mit der Microsoft Identity-Plattform erstellt werden. Sie können auch eine Registrierung verwenden, die Sie oder ein Verzeichnis-Administrator separat erstellen.

- [Das automatische Erstellen einer neuen App-Registrierung](#express)
- [Verwenden Sie eine vorhandene, separat erstellte Registrierung](#advanced)

> [!NOTE]
> Die Option zum Erstellen einer neuen Registrierung ist für Regierungs-Clouds nicht verfügbar. Definieren Sie stattdessen [eine Registrierung separat](#advanced).

## <a name="option-1-create-a-new-app-registration-automatically"></a><a name="express"> </a> Option 1: Automatisches Erstellen einer neuen App-Registrierung

Diese Option ist so konzipiert, dass die Authentifizierung ganz einfach und mit nur wenigen Klicks aktiviert werden kann.

1. Melden Sie sich am [Azure-Portal] an und navigieren Sie zu Ihrer App.
1. Wählen Sie **Authentifizierung** im Menü auf der linken Seite. Klicken Sie auf **Identitätsanbieter hinzufügen**.
1. Wählen Sie **Microsoft** in der Dropdown-Liste der Identitätsanbieter aus. Die Option zum Erstellen einer neuen Registrierung ist standardmäßig ausgewählt. Sie können den Namen der Registrierung oder die unterstützten Kontotypen ändern.

    Ein Client-Geheimnis wird erstellt und als eine Slot-Sticky-[Anwendungseinstellung](./configure-common.md#configure-app-settings) namens `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` gespeichert. Sie können diese Einstellung später aktualisieren, um [Key Vault Verweise](./app-service-key-vault-references.md) zu verwenden, wenn Sie den geheimen Schlüssel in Azure Key Vault verwalten möchten.

1. Wenn dies der erste Identitätsanbieter ist, der für die Anwendung konfiguriert wurde, wird auch ein Abschnitt mit den **Einstellungen für die App-Dienst-Authentifizierung** angezeigt. Andernfalls können Sie mit dem nächsten Schritt fortfahren.
    
    Diese Optionen bestimmen, wie Ihre Anwendung auf nicht authentifizierte Anfragen reagiert, und die Standardeinstellungen leiten alle Anfragen zur Anmeldung mit diesem neuen Anbieter um. Sie können dieses Verhalten jetzt anpassen oder diese Einstellungen später über den Hauptbildschirm der **Authentifizierung** anpassen, indem Sie neben den **Authentifizierungs-Einstellungen** die Option **Bearbeiten** auswählen. Weitere Informationen zu diesen Optionen finden Sie unter [Authentifizierungs-Fluss](overview-authentication-authorization.md#authentication-flow).

1. (Fakultativ) Klicken Sie auf **Fortfahren: Berechtigungen** und fügen Sie alle von der Anwendung benötigten Umfänge hinzu. Diese werden der APP-Registrierung hinzugefügt, Sie können Sie jedoch später auch ändern.
1. Klicken Sie auf **Hinzufügen**.

Sie sind nun bereit, die Microsoft Identity Platform für die Authentifizierung in Ihrer App zu verwenden. Der Anbieter wird auf dem Bildschirm **Authentifizierung** aufgeführt. Von dort aus können Sie diese Anbieterkonfiguration bearbeiten oder löschen.

Ein Beispiel für die Konfiguration der Azure AD-Anmeldung für eine Web-App, die auf Azure Storage und Microsoft Graph zugreift, finden Sie in [diesem Tutorial](scenario-secure-app-authentication-app-service.md).

## <a name="option-2-use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Option 2: Verwenden einer vorhandenen, separat erstellten Registrierung

Sie können Ihre Anwendung auch manuell für die Microsoft Identity-Plattform registrieren, die Registrierung anpassen und die App Service Authentifizierung mit den Registrierungsdetails konfigurieren. Dies ist beispielsweise hilfreich, wenn Sie eine App-Registrierung von einem anderen Azure AD-Mandanten als dem verwenden möchten, auf dem sich Ihre App Service-App befindet.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Erstellen einer App-Registrierung in Azure AD für Ihre App Service-App

Erstellen Sie zuerst Ihre App-Registrierung. Halten Sie dabei die folgenden Informationen fest, die Sie später beim Konfigurieren der Authentifizierung in der App Service-App benötigen:

- Client-ID
- Mandanten-ID
- Geheimer Clientschlüssel (optional)
- Anwendungs-ID-URI

Gehen Sie wie folgt vor, um die App zu registrieren:

1. Melden Sie sich beim [Azure portal] an, suchen Sie die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. Notieren Sie sich die **URL** Ihrer App. Sie verwenden diese, um die Registrierung Ihrer Azure Active Directory-App zu konfigurieren.
1. Klicken Sie im Portalmenü auf die Option **Azure Active Directory**, navigieren Sie zur Registerkarte **App-Registrierungen** und klicken Sie auf **Neue Registrierung**.
1. Geben Sie auf der Seite **Anwendung registrieren** einen **Namen** für Ihre App-Registrierung ein.
1. Wählen Sie unter **Umleitungs-URIs** die Option **Web** aus, und geben Sie `<app-url>/.auth/login/aad/callback` ein. Beispiel: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Wählen Sie **Registrieren**.
1. Nachdem die App-Registrierung erstellt wurde, kopieren Sie die **Anwendungs-ID (Client)** und die **Verzeichnis-ID (Mandant)** , damit Sie diese später verwenden können.
1. Wählen Sie **Authentifizierung** aus. Aktivieren Sie unter **Implizite Genehmigung und Hybridflows** die Option **ID-Token**, um OpenID Connect-Benutzeranmeldungen von App Service zuzulassen.  Wählen Sie **Speichern** aus.
1. (Optional) Wählen Sie **Branding** aus. Geben Sie in **URL der Startseite** die URL Ihrer App Service-App ein, und wählen Sie **Speichern** aus.
1. Wählen Sie **Eine API verfügbar machen** aus, und klicken Sie neben „Anwendungs-ID-URI“ auf **Festlegen**. Dieser Wert identifiziert die Anwendung eindeutig, wenn sie als Ressource verwendet wird, sodass Token angefordert werden können, die Zugriff gewähren. Er wird als Präfix für Bereiche verwendet, die Sie erstellen.

    Für eine App mit nur einem Mandanten können Sie den Standardwert verwenden, der die Form `api://<application-client-id>` aufweist. Sie können auch einen besser lesbaren URI wie `https://contoso.com/api` basierend auf einer der überprüften Domänen für Ihren Mandanten angeben. Für eine mehrinstanzenfähige App müssen Sie einen benutzerdefinierten URI angeben. Weitere Informationen zu akzeptierten Formaten für App-ID-URIs finden Sie in der [Referenz zu bewährten Methoden für App-Registrierungen](../active-directory/develop/security-best-practices-for-app-registration.md#appid-uri-configuration).

    Nachdem Sie den Wert eingegeben haben, klicken Sie auf **Speichern**.

1. Wählen Sie **Bereich hinzufügen**.
   1. Unter **Bereich hinzufügen** ist der **Anwendungs-ID-URI** der Wert, den Sie in einem vorherigen Schritt festgelegt haben.  Wählen Sie **Speichern und fortfahren** aus.
   1. Geben Sie in **Bereichsname** den Namen *user_impersonation* ein.
   1. Geben Sie in die Textfelder den Namen und die Beschreibung für den Einwilligungsbereich ein, die Benutzern auf der Einwilligungsseite angezeigt werden sollen. Geben Sie z. B. *Zugriff auf &lt;Anwendungsname&gt;* ein.
   1. Wählen Sie **Bereich hinzufügen** aus.
1. (Optional) Um einen geheimen Clientschlüssel zu erstellen, wählen Sie **Zertifikate und Geheimnisse** > **Geheime Clientschlüssel** > **Neuer geheimer Clientschlüssel** aus.  Geben Sie eine Beschreibung und den Ablauf ein, und wählen Sie **Hinzufügen** aus. Kopieren Sie den Wert des geheimen Clientschlüssels, der auf der Seite angezeigt wird. Er wird nicht noch einmal angezeigt.
1. (Optional) Wenn Sie mehrere **Antwort-URLs** hinzufügen möchten, wählen Sie **Authentifizierung** aus.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Aktivieren von Azure Active Directory in Ihrer App Service-App

1. Melden Sie sich am [Azure-Portal] an und navigieren Sie zu Ihrer App.
1. Wählen Sie **Authentifizierung** im Menü auf der linken Seite. Klicken Sie auf **Identitätsanbieter hinzufügen**.
1. Wählen Sie **Microsoft** in der Dropdown-Liste der Identitätsanbieter aus.
1. Für den **App-Registrierungstyp** können Sie **eine vorhandene App-Registrierung in diesem Verzeichnis** auswählen, die automatisch die erforderlichen App-Informationen erfasst. Wenn Ihre Registrierung von einem anderen Mandanten aus erfolgt oder Sie nicht über die Berechtigung zum Anzeigen des Registrierungs-Objekts verfügen, wählen Sie **Details zu einer vorhandenen APP-Registrierung angeben** aus. Für diese Option müssen Sie die folgenden Konfigurationsdetails ausfüllen:

    |Feld|BESCHREIBUNG|
    |-|-|
    |Anwendungs-ID (Client)| Verwenden Sie die **Anwendungs-ID (Client)** der App-Registrierung. |
    |Geheimer Clientschlüssel| Verwenden Sie den geheimen Clientschlüssel, den Sie in der App-Registrierung generiert haben. Bei einem geheimen Clientschlüssel wird der Hybridflow verwendet, und der App Service gibt Zugriffs- und Aktualisierungstoken zurück. Wenn der geheime Clientschlüssel nicht festgelegt ist, wird impliziter Flow verwendet und nur ein ID-Token zurückgegeben. Diese Token werden vom Anbieter gesendet und im EasyAuth-Tokenspeicher gespeichert.|
    |Aussteller-URL| Verwenden Sie `<authentication-endpoint>/<tenant-id>/v2.0`, und ersetzen Sie *\<authentication-endpoint>* durch den [Authentifizierungsendpunkt für Ihre Cloudumgebung](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (z. B. „https://login.microsoftonline.com“ für globales Azure), und ersetzen Sie *\<tenant-id>* durch die **Verzeichnis (Mandanten)-ID**, in der die App-Registrierung erstellt wurde. Dieser Wert wird verwendet, um Benutzer an den richtigen Azure AD-Mandanten umzuleiten sowie um die entsprechenden Metadaten herunterzuladen, um die entsprechenden Tokensignaturschlüssel und den Anspruchswert des Tokenausstellers zu ermitteln. Lassen Sie für Anwendungen, die Azure AD v1 verwenden, und für Azure Functions-Apps die Zeichenfolge `/v2.0` in der URL aus.|
    |Zulässige Tokenzielgruppen| Wenn dies eine Cloud- oder Server-App ist und Sie Authentifizierungstoken von einer Web-App zulassen möchten, fügen Sie hier den **Anwendungs-ID-URI** der Web-App hinzu. Die konfigurierte **Client-ID** wird *immer* implizit als zulässige Zielgruppe angesehen.|

    Der geheime Schlüssel wird als Slot-sticky [Anwendungs-Einstellung](./configure-common.md#configure-app-settings) mit dem Namen`MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` gespeichert. Sie können diese Einstellung später aktualisieren, um [Key Vault Verweise](./app-service-key-vault-references.md) zu verwenden, wenn Sie den geheimen Schlüssel in Azure Key Vault verwalten möchten.

1. Wenn dies der erste Identitätsanbieter ist, der für die Anwendung konfiguriert wurde, wird auch ein Abschnitt mit den **Einstellungen für die App-Dienst-Authentifizierung** angezeigt. Andernfalls können Sie mit dem nächsten Schritt fortfahren.
    
    Diese Optionen bestimmen, wie Ihre Anwendung auf nicht authentifizierte Anfragen reagiert, und die Standardeinstellungen leiten alle Anfragen zur Anmeldung mit diesem neuen Anbieter um. Sie können dieses Verhalten jetzt anpassen oder diese Einstellungen später über den Hauptbildschirm der **Authentifizierung** anpassen, indem Sie neben den **Authentifizierungs-Einstellungen** die Option **Bearbeiten** auswählen. Weitere Informationen zu diesen Optionen finden Sie unter [Authentifizierungs-Fluss](overview-authentication-authorization.md#authentication-flow).

1. Klicken Sie auf **Hinzufügen**.

Sie sind nun bereit, die Microsoft Identity Platform für die Authentifizierung in Ihrer App zu verwenden. Der Anbieter wird auf dem Bildschirm **Authentifizierung** aufgeführt. Von dort aus können Sie diese Anbieterkonfiguration bearbeiten oder löschen.

## <a name="configure-client-apps-to-access-your-app-service"></a>Konfigurieren von Client-Apps für den Zugriff auf Ihre App Service-App

Im letzten Abschnitt haben Sie Ihre App Service- oder Azure Functions-App registriert, um Benutzer zu authentifizieren. In diesem Abschnitt wird erläutert, wie Sie native Client- oder Daemon-Apps registrieren können, damit diese im Namen von Benutzern oder im eigenen Namen Zugriff auf APIs beantragen können, die von Ihrer App Service-App zur Verfügung gestellt werden. Sie müssen die in diesem Abschnitt beschriebenen Schritte nicht ausführen, wenn Sie nur Benutzer authentifizieren möchten.

### <a name="native-client-application"></a>Native Clientanwendung

Sie können native Clients registrieren, um im Namen eines angemeldeten Benutzers Zugriff auf die APIs Ihrer App Servic-App zu beantragen.

1. Wählen Sie im [Azure portal] nacheinander **Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.
1. Geben Sie auf der Seite **Anwendung registrieren** einen **Namen** für Ihre App-Registrierung ein.
1. Wählen Sie unter **Umleitungs-URI** die Option **Öffentlicher Client (Mobilgerät und Desktop)** aus, und geben Sie die URL `<app-url>/.auth/login/aad/callback` ein. Beispiel: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Für eine Microsoft Store-Anwendung verwenden Sie stattdessen die [Paket-SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) als URI.
1. Klicken Sie auf **Erstellen**.
1. Sobald die App-Registrierung erstellt wurde, kopieren Sie den Wert von **Anwendungs-ID (Client)** .
1. Wählen Sie über die Option **API-Berechtigungen** > **Berechtigung hinzufügen** > **Meine APIs** aus.
1. Wählen Sie die App-Registrierung aus, die Sie zuvor für Ihre App Service-App erstellt haben. Wenn die App-Registrierung nicht angezeigt wird, stellen Sie sicher, dass Sie den Bereich **user_impersonation** in [Erstellen einer App-Registrierung in Azure AD für Ihre App Service-App](#register) hinzugefügt haben.
1. Wählen Sie unter **Delegierte Berechtigungen** den Eintrag **user_impersonation** aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.

Sie haben nun eine native Clientanwendung konfiguriert, die im Namen eines Benutzers Zugriff auf Ihre App Service-App beantragen kann.

### <a name="daemon-client-application-service-to-service-calls"></a>Daemon-Clientanwendung (Dienst-zu-Dienst-Aufrufe)

Ihre Anwendung kann ein Token abrufen, um eine Web-API, die in Ihrer App Service- oder Funktions-App gehostet wird, in deren eigenen Namen (nicht im Namen eines Benutzers) aufzurufen. Dieses Szenario ist nützlich für nicht interaktive Daemon-Anwendungen, die Aufgaben ohne angemeldeten Benutzer ausführen. Es verwendet die Standardzuweisung für [Clientanmeldeinformationen](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) von OAuth 2.0.

1. Wählen Sie im [Azure portal] nacheinander **Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.
1. Geben Sie auf der Seite **Anwendung registrieren** einen **Namen** für Ihre Daemon-App-Registrierung ein.
1. Für eine Daemon-Anwendung benötigen Sie keinen Umleitungs-URI, sodass Sie diesen Wert leer lassen können.
1. Klicken Sie auf **Erstellen**.
1. Sobald die App-Registrierung erstellt wurde, kopieren Sie den Wert von **Anwendungs-ID (Client)** .
1. Wählen Sie **Zertifikate und Geheimnisse** > **Neuer geheimer Clientschlüssel** > **Hinzufügen** aus. Kopieren Sie den Wert des geheimen Clientschlüssels, der auf der Seite angezeigt wird. Er wird nicht noch einmal angezeigt.

Sie können jetzt [ein Zugriffstoken mithilfe der Client-ID und des geheimen Clientschlüssels anfordern](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret), indem Sie den Parameter `resource` auf den **Anwendungs-ID-URI** der Ziel-App festlegen. Das resultierende Zugriffstoken kann dann der Ziel-App mithilfe des standardmäßigen [OAuth 2.0-Autorisierungsheaders](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource) angezeigt werden, woraufhin die App Service-Authentifizierung/-Autorisierung das Token überprüft und wie üblich verwendet, um jetzt anzuzeigen, dass der Aufrufer (in diesem Fall eine Anwendung, kein Benutzer) authentifiziert ist.

Im Moment ermöglicht dies _jeder_ Clientanwendung in Ihrem Azure AD-Mandanten, ein Zugriffstoken anzufordern und sich bei der Ziel-App zu authentifizieren. Wenn Sie außerdem bei der _Autorisierung_ erzwingen möchten, dass nur bestimmte Clientanwendungen zugelassen werden, müssen Sie einige zusätzliche Konfigurationen vornehmen.

1. [Definieren Sie eine App-Rolle](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) im Manifest der App-Registrierung, die die App Service- oder Funktions-App darstellt, die Sie schützen möchten.
1. Wählen Sie in der App-Registrierung, die den Client darstellt, der autorisiert werden muss, **API-Berechtigungen** > **Berechtigung hinzufügen** > **Meine APIs** aus.
1. Wählen Sie die App-Registrierung aus, die Sie zuvor erstellt haben. Wenn die App-Registrierung nicht angezeigt wird, stellen Sie sicher, dass Sie [eine App-Rolle hinzugefügt haben](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Wählen Sie unter **Anwendungsberechtigungen** die zuvor erstellte App-Rolle aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.
1. Stellen Sie sicher, dass Sie auf **Administratoreinwilligung erteilen**, um die Clientanwendung zum Anfordern der Berechtigung zu autorisieren.
1. Ähnlich wie im vorherigen Szenario (vor dem Hinzufügen jeglicher Rollen) können Sie jetzt [ein Zugriffstoken anfordern](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) für dieselbe Ziel-`resource`, woraufhin das Zugriffstoken einen `roles`-Anspruch aufnimmt, der die App-Rollen enthält, die für die Clientanwendung autorisiert wurden.
1. Im Code der App Service- oder Funktions-Ziel-App können Sie jetzt überprüfen, ob die erwarteten Rollen im Token vorhanden sind (dies wird nicht von der App Service-Authentifizierung/-Autorisierung durchgeführt). Weitere Informationen finden Sie unter [Zugriff auf Benutzeransprüche](configure-authentication-user-identities.md#access-user-claims-in-app-code).

Sie haben nun eine Daemon-Clientanwendung konfiguriert, die unter Verwendung der eigenen Identität auf Ihre App Service-App zugreifen kann.

## <a name="best-practices"></a>Bewährte Methoden

Unabhängig von der Konfiguration, die Sie zum Einrichten der Authentifizierung verwenden, werden Ihnen die folgenden bewährten Methoden dabei helfen, Ihren Mandanten und Ihre Anwendungen besser zu schützen:

- Erteilen Sie jeder App Service-App eigene Berechtigungen und Einwilligung.
- Konfigurieren Sie jede App Service-App mit eigener Registrierung.
- Vermeiden Sie das Teilen von Berechtigungen zwischen Umgebungen, indem Sie separate App-Registrierungen für gesonderte Bereitstellungsslots verwenden. Wenn Sie neuen Code testen, kann diese Vorgehensweise dabei helfen, Probleme zu verhindern, die sich auf die Produktions-App auswirken können.

## <a name="next-steps"></a><a name="related-content"> </a>Nächste Schritte

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Tutorial: Hinzufügen der Authentifizierung zu Ihrer Web-App in Azure App Service](scenario-secure-app-authentication-app-service.md)
* [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure portal]: https://portal.azure.com/

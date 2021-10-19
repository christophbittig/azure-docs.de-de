---
title: Schützen des API-Back-Ends in API Management mithilfe von OAuth 2.0 und Azure Active Directory
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie den Benutzerzugriff auf ein Web-API-Back-End in Azure API Management und dem Entwicklerportal mit der OAuth 2.0-Benutzerautorisierung und Azure Active Directory sichern.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7739411364b187e437e9dda4c72dff85de26d741
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669566"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>Schützen eines Web-API-Back-Ends in Azure API Management mithilfe der OAuth 2.0-Autorisierung mit Azure Active Directory 

In diesem Artikel erfahren Sie, wie Sie Ihre [Azure API Management-Instanz](api-management-key-concepts.md) so konfigurieren, dass eine API über das [OAuth 2.0-Protokoll mit Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md) geschützt ist. 

> [!NOTE]
> Dieses Feature ist in den Tarifen **Developer**, **Basic**, **Standard** und **Premium** von API Management verfügbar.  
> 
> Mit Ausnahme des Aufrufs der API über das Entwicklerportal können Sie jeden Schritt unten im **Verbrauch** starif ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Schritte in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Eine API Management-Instanz
- Eine veröffentlichte API, die die API Management-Instanz verwendet
- Einen Azure AD-Mandanten

## <a name="overview"></a>Übersicht

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="Übersichtsgrafik, um das Konzept des folgenden Flows zu veranschaulichen":::

1. Registrieren einer Anwendung (Back-End-App) in Azure AD, die die API darstellt

1. Registrieren einer anderen Anwendung (Client-App) in Azure AD, die eine Clientanwendung darstellt, aus der die API aufgerufen werden muss

1. Gewähren von Berechtigungen in Azure AD, damit die Client-App die Back-End-App aufrufen kann

1. Konfigurieren der Entwicklerkonsole im Entwicklerportal zum Aufrufen der API unter Verwendung der OAuth 2.0-Benutzerautorisierung

1. Hinzufügen der **validate-jwt**-Richtlinie, um das OAuth-Token für jede eingehende Anforderung zu überprüfen

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. Registrieren einer Anwendung in Azure AD, die die API darstellt

Schützen Sie eine API mit Azure AD unter Verwendung des Azure-Portals, indem Sie eine Anwendung registrieren, die die API in Azure AD darstellt. 

Ausführliche Informationen zur App-Registrierung finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App-Registrierungen**, und wählen Sie den Eintrag aus.

1. Wählen Sie **Neue Registrierung** aus. 

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:

   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird, beispielsweise *Back-End-App*. 
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** eine Option aus,die Ihrem Szenario entspricht. 

1. Lassen Sie den Abschnitt [**Umleitungs-URI**](../active-directory/develop/reply-url.md) leer.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

1. Wählen Sie im Abschnitt **Verwalten** des seitlichen Menüs **Eine API verfügbar machen** aus, und legen Sie den **Anwendungs-ID-URI** auf den Standardwert fest. Notieren Sie sich diesen Wert zur späteren Verwendung.

1. Wählen Sie die Schaltfläche **Bereich hinzufügen** aus, um die Seite **Bereich hinzufügen** anzuzeigen:
    1. Geben Sie einen neuen **Bereichsnamen** ein, einen **Anzeigenamen der Administratoreinwilligung** und eine **Beschreibung der Administratoreinwilligung**.
    1. Stellen Sie sicher, dass der Bereichsstatus **Aktiviert** ausgewählt ist.

1. Wählen Sie die Schaltfläche **Bereich hinzufügen** aus, um den Bereich zu erstellen. 

1. Wiederholen Sie die Schritte 8 und 9, um alle Bereiche hinzuzufügen, die von Ihrer API unterstützt werden.

1. Nachdem Sie die Bereiche erstellt haben, notieren Sie diese, um sie in einem nachfolgenden Schritt zu verwenden. 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. Registrieren einer anderen Anwendung in Azure AD, die eine Clientanwendung darstellt

Registrieren Sie jede Clientanwendung, aus der die API aufgerufen wird, als Anwendung in Azure AD. In diesem Beispiel wird die **Entwicklerkonsole** im API Management-Entwicklerportal als Clientanwendung verwendet. 

So registrieren Sie eine weitere Anwendung in Azure AD, die die Entwicklerkonsole darstellt

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App-Registrierungen**, und wählen Sie den Eintrag aus.

1. Wählen Sie **Neue Registrierung** aus.

1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen Ihrer Anwendung ein:

   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird, beispielsweise *Client-App*. 
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis (Beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)** aus. 

1. Wählen Sie im Abschnitt **Umleitungs-URI** die Option `Web` aus, und lassen Sie das URL-Feld vorerst leer.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen. 

1. Suchen Sie auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID** und notieren Sie ihn zur späteren Verwendung.

1. Erstellen Sie einen geheimen Clientschlüssel für diese Anwendung, der in einem späteren Schritt von ihr verwendet wird.

   1. Wählen Sie im Abschnitt **Verwalten** des seitlichen Menüs **Zertifikate und Geheimnisse** aus.
   1. Wählen Sie unter **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel**.
   1. Geben Sie unter **Geheimen Clientschlüssel hinzufügen** eine **Beschreibung** ein, und wählen Sie aus, wann der Schlüssel ablaufen soll.
   1. Wählen Sie **Hinzufügen**.

Nachdem Sie das Geheimnis erstellt haben, notieren Sie sich den Schlüsselwert, um ihn in einem nachfolgenden Schritt zu verwenden. 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Erteilen von Berechtigungen in Azure AD

Nachdem nun zwei Anwendungen registriert sind, die die API und die Entwicklerkonsole darstellen, gewähren Sie Berechtigungen, damit die Client-App die Back-End-App aufrufen kann.  

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App-Registrierungen**, und wählen Sie den Eintrag aus.

1. Wählen Sie Ihre Client-App aus. Wählen Sie dann in der Liste der Seiten für die App **API-Berechtigungen** aus.

1. Wählen Sie **Berechtigung hinzufügen** aus.

1. Wählen Sie unter **API auswählen** die Option **Meine APIs** aus, suchen Sie dann die Back-End-App, und wählen Sie diese aus.

1. Wählen Sie **Delegierte Berechtigungen** aus, und wählen Sie dann die entsprechenden Berechtigungen für Ihre Back-End-App aus.

1. Wählen Sie **Berechtigungen hinzufügen** aus.

Optional:
1. Navigieren Sie zur Seite **API-Berechtigungen** Ihrer Client-App.

1. Wählen Sie **Administratorzustimmung für \<your-tenant-name> erteilen** aus, um die Zustimmung im Auftrag aller Benutzer in diesem Verzeichnis zu erteilen. 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. Aktivieren der OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole

An diesem Punkt haben Sie die Anwendungen in Azure AD erstellt und die erforderlichen Berechtigungen erteilt, damit die Client-App die Back-End-App aufrufen kann. 

In diesem Beispiel aktivieren Sie die OAuth 2.0-Benutzerautorisierung in der Entwicklerkonsole (der Client-App).

1. Suchen Sie im Azure-Portal die **Autorisierungsendpunkt-URL** und die **Token-Endpunkt-URL**, und speichern Sie diese zur späteren Verwendung. 
    1. Öffnen Sie die Seite **App-Registrierungen**. 
    1. Wählen Sie **Endpunkte**.
    1. Kopieren Sie den **OAuth 2.0-Autorisierungsendpunkt** und den **OAuth 2.0-Token-Endpunkt**. 

1. Navigieren Sie zu Ihrer API Management-Instanz.

1. Wählen Sie im Abschnitt **Entwicklerportal** des seitlichen Menüs **OAuth 2.0 + OpenID Connect** aus. 

1. Wählen Sie auf der Registerkarte **OAuth 2.0** die Option **Hinzufügen** aus.

1. Geben Sie den **Anzeigenamen** und eine **Beschreibung** an.

1. Geben Sie als **URL für Clientregistrierungsseite** einen Platzhalterwert ein, beispielsweise `http://localhost`. 
    * **URL für Clientregistrierungsseite** verweist auf eine Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten nutzen können, die von OAuth 2.0-Anbietern unterstützt werden. 
    * Es wird ein Platzhalter verwendet, da in diesem Beispiel Benutzer keine eigenen Konten erstellen und konfigurieren.

1. Wählen Sie unter **Autorisierungszuweisungstypen** die Option **Autorisierungscode**.

1. Geben Sie die **Autorisierungsendpunkt-URL** und die **Token-Endpunkt-URL** ein, die Sie vorher gespeichert haben: 
    1. Kopieren Sie den **OAuth 2.0-Autorisierungsendpunkt**, und fügen Sie ihn in das Textfeld **URL für Autorisierungsendpunkt** ein. 
    1. Wählen Sie unter „Autorisierungsanforderungsmethode“ die Option **POST** aus.
    1. Kopieren Sie den **OAuth 2.0-Token-Endpunkt**, und fügen Sie ihn in das Textfeld **URL für Tokenendpunkt** ein. 
        * Wenn Sie den **v1**-Endpunkt verwenden:
          * Fügen Sie einen body-Parameter mit dem Namen **resource** hinzu.
          * Geben Sie als Wert die **Anwendungs-ID** der Back-End-App ein.
        * Wenn Sie den **v2**-Endpunkt verwenden:
          * Verwenden Sie den Back-End-App-Bereich, den Sie im Feld **Standardbereich** erstellt haben.
          * Legen Sie den Wert für die [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute)-Eigenschaft in Ihrem [Anwendungsmanifest](../active-directory/develop/reference-app-manifest.md) auf `2` fest.
          

   >[!IMPORTANT]
   > Sie können entweder **v1**- oder **v2**-Endpunkte verwenden; es wird jedoch empfohlen, v2-Endpunkte zu verwenden. 

1. Geben Sie die Client-App-Anmeldeinformationen an:
    * Verwenden Sie für **Client-ID** die **Anwendungs-ID** der Client-App.
    * Verwenden Sie für **Clientgeheimnis** den Schlüssel, den Sie zuvor für die Client-App erstellt haben. 

1. Notieren Sie sich den **Umleitungs-URI** für den Autorisierungscode-Gewährungstyp.

1. Klicken Sie auf **Erstellen**.

1. Kehren Sie zu Ihrer Client-App-Registrierung zurück. 
 
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.

1. Unter **Plattformkonfigurationen**:
    * Klicken Sie auf **Plattform hinzufügen**.
    * Wählen Sie den Typ **Web** aus. 
    * Fügen Sie den Umleitungs-URI, den Sie zuvor gespeichert haben, unter **Umleitungs-URIs** ein.
    * Klicken Sie zum Speichern auf die Schaltfläche **Konfigurieren**.

   Nachdem nun die Entwicklerkonsole Zugriffstoken von Azure AD über Ihren OAuth 2.0-Autorisierungsserver abrufen kann, aktivieren Sie die OAuth 2.0-Benutzerautorisierung für Ihre API. Dadurch weiß die Entwicklerkonsole, dass sie im Namen des Benutzers ein Zugriffstoken abrufen muss, bevor Aufrufe an die API erfolgen.

15. Navigieren Sie zu Ihrer API Management-Instanz und dann zu **APIs**.

1. Wählen Sie die API aus, die Sie schützen möchten. Beispiel: `Echo API`.

1. Wechseln Sie zu **Einstellungen**.

1. Unter **Sicherheit**:
    1. Wählen Sie **OAuth 2.0** aus.
    1. Wählen Sie den OAuth 2.0-Server aus, den Sie zuvor konfiguriert haben. 

1. Wählen Sie **Speichern** aus.

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. Erfolgreiches Aufrufen der API aus dem Entwicklerportal

> [!NOTE]
> Dieser Abschnitt gilt nicht für den **Verbrauchstarif**, da er keine Unterstützung für das Entwicklerportal bietet.

Nachdem nun die OAuth 2.0-Benutzerautorisierung für Ihre API aktiviert ist, ruft die Entwicklerkonsole im Namen des Benutzers ein Zugriffstoken ab, bevor die API aufgerufen wird.

1. Navigieren Sie im Entwicklerportal zu einem beliebigen Vorgang unter der API. 
1. Wählen Sie **Ausprobieren** aus, um zur Entwicklerkonsole zu gelangen.

1. Beachten Sie ein neues Element im Abschnitt **Autorisierung**, das dem soeben hinzugefügten Autorisierungsserver entspricht.

1. Wählen Sie in der Dropdownliste „Autorisierung“ die Option **Autorisierungscode** aus. 
1. Sobald Sie aufgefordert werden, melden Sie sich bei Azure AD an. 
    * Wenn Sie bereits beim Konto angemeldet sind, werden Sie möglicherweise nicht dazu aufgefordert.

1. Nach der erfolgreichen Anmeldung wird der Anforderung ein `Authorization`-Header mit einem Zugriffstoken von Azure AD hinzugefügt. Hier sehen Sie ein Beispieltoken (Base64-codiert):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Wählen Sie **Senden** aus, um die API erfolgreich aufzurufen.

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. Konfigurieren einer JWT-Überprüfungsrichtlinie zur Vorautorisierung von Anforderungen

Die bisherigen Schritte:
* Sie haben versucht, einen Aufruf über die Entwicklerkonsole zu starten.
* Sie haben sich nach entsprechender Aufforderung bei Azure AD angemeldet. 
* Die Entwicklerkonsole ruft in Ihrem Namen ein Zugriffstoken ab und nimmt das Token in die Anforderung an die API auf.

Was aber passiert, wenn jemand Ihre API ohne Token oder mit einem ungültigen Token aufruft? Wenn Sie beispielsweise die API ohne den `Authorization`-Header aufrufen, wird der Aufruf dennoch durchgeleitet, da API Management das Zugriffstoken nicht überprüft. API Management übergibt den `Authorization`-Header einfach an die Back-End-API.

Autorisieren Sie Anforderungen in API Management vorab mithilfe der Richtlinie [JWT überprüfen](./api-management-access-restriction-policies.md#ValidateJWT). Dadurch werden Zugriffstoken für jede eingehende Anforderung überprüft. Wenn für eine Anforderung kein gültiges Token vorliegt, wird sie von API Management blockiert. 

Wenn die folgende Beispielrichtlinie dem Richtlinienabschnitt `<inbound>` hinzugefügt wird, überprüft sie den Wert des Zielgruppenanspruchs in einem Zugriffstoken, das von Azure AD erhalten wurde, und gibt eine Fehlermeldung zurück, wenn das Token ungültig ist. 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> Die obige `openid-config`-URL entspricht dem v2-Endpunkt. Verwenden Sie für den v1-`openid-config`-Endpunkt `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration`.

> [!TIP] 
> Suchen Sie den Wert **{aad-tenant}** als Azure AD-Mandanten-ID im Azure-Portal, entweder auf
> * der Übersichtsseite Ihrer Azure AD Ressource oder
> * der Seite **Verwalten > Eigenschaften** Ihrer Azure AD-Ressource.

Informationen zum Konfigurieren von Richtlinien finden Sie unter [How to set or edit Azure API Management policies](./set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien).

## <a name="build-an-application-to-call-the-api"></a>Erstellen einer Anwendung zum Aufrufen der API

In diesem Handbuch wird die Entwicklerkonsole in API Management als Beispielclientanwendung verwendet, um die durch OAuth 2.0 geschützte `Echo API` aufzurufen. Weitere Informationen zum Erstellen einer Anwendung und zum Implementieren von OAuth 2.0 finden Sie unter [Azure AD-Codebeispiele](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure AD und OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
- Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
- Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](./api-management-howto-mutual-certificates.md).
- [Erstellen einer API Management-Dienstinstanz](./get-started-create-service-instance.md)
- [Verwalten Ihrer ersten API](./import-and-publish.md)

---
title: Sichern einer Azure API Management-API mithilfe von Azure Active Directory B2C
description: Erfahren Sie, wie Sie von Azure Active Directory B2C ausgestellte Zugriffstoken zum Sichern eines Azure API Management-API-Endpunkts verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4d1aab07514f4a42d011cf75a9a22b34246a5653
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355264"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Sichern einer Azure API Management-API mit Azure AD B2C

Erfahren Sie, wie Sie den Zugriff auf Ihre Azure API Management-API auf Clients beschränken, die sich mit Azure Active Directory B2C (Azure AD B2C) authentifiziert haben. Befolgen Sie die Anweisungen in diesem Artikel, um eine Eingangsrichtlinie in Azure API Management zu erstellen und zu testen, mit der ausschließlich Anforderungen Zugriff erhalten, die ein gültiges, über Azure AD B2C ausgestelltes Zugriffstoken umfassen.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vorab sicher, dass die folgenden Ressourcen vorhanden sind:

* Ein [Azure AD B2C-Mandant](tutorial-create-tenant.md)
* Eine [in Ihrem Mandanten registrierte Anwendung](tutorial-register-applications.md)
* [In Ihrem Mandanten erstellte Benutzerflows](tutorial-create-user-flows.md)
* Eine in Azure API Management [veröffentlichte API](../api-management/import-and-publish.md)
* (Optional) Eine [Postman-Plattform](https://www.getpostman.com/) zum Testen des gesicherten Zugriffs

## <a name="get-azure-ad-b2c-application-id"></a>Abrufen einer Azure AD B2C-Anwendungs-ID

Wenn Sie eine API in Azure API Management mit Azure AD B2C schützen, benötigen Sie verschiedene Werte für die [Eingangsrichtlinie](../api-management/api-management-howto-policies.md), die Sie in Azure API Management erstellen. Notieren Sie sich zuerst die Anwendungs-ID einer Anwendung, die Sie zuvor in Ihrem Azure AD B2C-Mandanten registriert haben. Wenn Sie die Anwendung verwenden, die Sie zum Erfüllen der Voraussetzungen erstellt haben, verwenden Sie die Anwendungs-ID für *webapp1*.

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche *App-Registrierungen* oder die alte Benutzeroberfläche *Anwendungen (Legacy)* verwenden. Erfahren Sie mehr über die [neue Benutzeroberfläche für die Registrierung](./app-registrations-training-guide.md).

# <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Bereich die Option **Azure AD B2C** aus. Alternativ können Sie **Alle Dienste** auswählen, nach dem Eintrag **Azure AD B2C** suchen und diesen auswählen.
1. Klicken Sie auf **App-Registrierungen** und dann auf die Registerkarte **Anwendungen mit Besitzer**.
1. Notieren Sie den Wert in der Spalte **Anwendungs-ID (Client)** für *webapp1* oder eine andere Anwendung, die Sie zuvor erstellt haben.

# <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Bereich die Option **Azure AD B2C** aus. Alternativ können Sie **Alle Dienste** auswählen, nach dem Eintrag **Azure AD B2C** suchen und diesen auswählen.
1. Klicken Sie unter **Verwalten** auf die Option **Applications (Legacy)** (Anwendungen (Legacy)).
1. Notieren Sie den Wert in der Spalte **Anwendungs-ID** für *webapp1* oder eine andere Anwendung, die Sie zuvor erstellt haben.

* * *

## <a name="get-a-token-issuer-endpoint"></a>Abrufen des Tokenausstellerendpunkts

Rufen Sie als Nächstes die bekannte Konfigurations-URL für einen Ihrer Azure AD B2C-Benutzerflows ab. Sie benötigen außerdem den Tokenausstellerendpunkt-URI, der in Azure API Management unterstützt werden soll.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure AD B2C-Mandanten.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie eine vorhandene Richtlinie (beispielsweise *B2C_1_signupsignin1*) und dann **Benutzerflow ausführen** aus.
1. Notieren Sie sich die URL des Links, der unter der Überschrift **Benutzerflow ausführen** oben auf der Seite angezeigt wird. Diese URL ist der bekannte OpenID Connect-Ermittlungsendpunkt für den Benutzerflow. Sie verwenden diesen im nächsten Abschnitt beim Konfigurieren der Eingangsrichtlinie in Azure API Management.

    ![Screenshot: Bekannter URI-Link auf der Seite „Benutzerflow ausführen“ im Azure-Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Wählen Sie den Link aus, um zur Seite für die bekannte OpenID Connect-Konfiguration zu navigieren.
1. Notieren Sie sich den Wert `issuer`, der auf der im Browser geöffneten Seite angezeigt wird. Zum Beispiel:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Verwenden Sie diesen Wert im nächsten Abschnitt, wenn Sie Ihre API in Azure API Management konfigurieren.

Sie haben sich nun zwei URLs für die Verwendung im nächsten Abschnitt notiert: die bekannte OpenID Connect-Konfigurationsendpunkt-URL und den Aussteller-URI. Beispiel:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-the-inbound-policy-in-azure-api-management"></a>Konfigurieren der Eingangsrichtlinie in Azure API Management

Nun können Sie die eingehende Richtlinie, mit der API-Aufrufe überprüft werden, in Azure API Management hinzufügen. Durch Hinzufügen einer Richtlinie für die [JWT-Überprüfung (JSON Web Token)](../api-management/api-management-access-restriction-policies.md#ValidateJWT), mit der Zielgruppe und Aussteller in einem Zugriffstoken überprüft werden, können Sie sicherstellen, dass nur API-Aufrufe mit einem gültigen Token akzeptiert werden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure API Management-Instanz.
1. Klicken Sie auf **APIs**.
1. Wählen Sie die API aus, die Sie mit Azure AD B2C sichern möchten.
1. Klicken Sie auf die Registerkarte **Entwurf**.
1. Wählen Sie unter **Inbound processing** (Eingehende Verarbeitung) **\</\>** aus, um den Richtliniencode-Editor zu öffnen.
1. Platzieren Sie das folgende `<validate-jwt>`-Tag in der `<inbound>`-Richtlinie, und gehen Sie dann folgendermaßen vor:

    a. Aktualisieren Sie den `url`-Wert im `<openid-config>`-Element durch die bekannte Konfigurations-URL Ihrer Richtlinie.  
    b. Aktualisieren Sie das `<audience>`-Element mit der Anwendungs-ID der zuvor im B2C-Mandanten erstellten Anwendung (z. B. *webapp1*).  
    c. Aktualisieren Sie das `<issuer>`-Element durch den zuvor aufgezeichneten Tokenaussteller-Endpunkt.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Überprüfen des sicheren API-Zugriffs

Um sicherzustellen, dass nur authentifizierte Aufrufer auf Ihre API zugreifen können, können Sie Ihre Azure API Management-Konfiguration überprüfen, indem Sie die API mit [Postman](https://www.getpostman.com/) aufrufen.

Zum Aufrufen der API benötigen Sie sowohl ein von Azure AD B2C ausgestelltes Zugriffstoken als auch einen Azure API Management-Abonnementschlüssel.

### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

Sie benötigen zunächst ein Token, das von Azure AD B2C zur Verwendung im `Authorization`-Header in Postman ausgestellt wird. Dieses können Sie mithilfe der Funktion *Jetzt ausführen* Ihres Benutzerflows für die Registrierung bzw. Anmeldung abrufen, den Sie zur Erfüllung der Voraussetzungen erstellt haben.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure AD B2C-Mandanten.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie eine vorhandene Richtlinie für die Registrierung/Anmeldung, wie z. B. *B2C_1_signupsignin1*.
1. Wählen Sie für **Anwendung** die Option *webapp1* aus.
1. Wählen Sie für **Antwort-URL** die URL `https://jwt.ms` aus.
1. Wählen Sie **Benutzerflow ausführen** aus.

    ![Screenshot: Bereich „Benutzerflow ausführen“ für den Benutzerflow zur Registrierung/Anmeldung im Azure-Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Schließen Sie den Anmeldevorgang ab. Sie werden zu `https://jwt.ms` weitergeleitet.
1. Notieren Sie sich den verschlüsselten Tokenwert, der im Browser angezeigt wird. Sie verwenden dieses Token für den Autorisierungsheader in Postman.

    ![Screenshot: In „jwt.ms“ angezeigter verschlüsselter Tokenwert](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-an-api-subscription-key"></a>Abrufen eines API-Abonnementschlüssels

Eine Clientanwendung (in diesem Fall Postman), die eine veröffentlichte API aufruft, muss in Ihren HTTP-Anforderungen an die API einen gültigen API Management-Abonnementschlüssel enthalten. So rufen Sie einen Abonnementschlüssel ab, den Sie in Ihre Postman-HTTP-Anforderung einbeziehen können:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure API Management-Dienstinstanz.
1. Wählen Sie **Abonnements**.
1. Wählen Sie neben dem Zertifikat die Auslassungspunkte ( **...** ) neben **Produkt: Unbegrenzt** und anschließend **Geheimnisse anzeigen/ausblenden** aus.
1. Notieren Sie sich den **Primärschlüssel** für das Produkt. Verwenden Sie diesen Schlüssel für den `Ocp-Apim-Subscription-Key`-Header in Ihrer HTTP-Anforderung in Postman.

![Screenshot: Seite „Abonnementschlüssel“ mit ausgewählter Option „Geheimnisse anzeigen/ausblenden“ im Azure-Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testen eines sicheren API-Aufrufs

Nachdem Sie sich das Zugriffstoken und den Azure API Management-Abonnementschlüssel notiert haben, können Sie nun testen, ob Sie den sicheren Zugriff auf die API ordnungsgemäß konfiguriert haben.

1. Erstellen Sie in [Postman](https://www.getpostman.com/) eine neue `GET`-Anforderung. Geben Sie für die Anforderungs-URL den Sprecherliste-Endpunkt der API an, den Sie als eine der Voraussetzungen veröffentlicht haben. Beispiel:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Fügen Sie als Nächstes die folgenden Header hinzu:

    | Schlüssel | Wert |
    | --- | ----- |
    | `Authorization` | Der verschlüsselte Tokenwert, den Sie zuvor notiert haben, mit dem Präfix `Bearer ` (mit einem Leerzeichen nach „Bearer“) |
    | `Ocp-Apim-Subscription-Key` | Der Azure API Management-Abonnementschlüssel, den Sie zuvor notiert haben |
    | | |

    Ihre **GET**-Anforderungs-URL und **Headers** (Header) sollten in etwa wie folgt angezeigt werden:

    ![Screenshot: Postman-Benutzeroberfläche mit GET-Anforderungs-URL und Headern](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Klicken Sie in Postman auf die Schaltfläche **Send** (Senden) aus, um die Anforderung auszuführen. Wenn Sie alles korrekt konfiguriert haben, wird Ihnen eine JSON-Antwort mit einer Sammlung von Konferenzrednern angezeigt werden (hier abgeschnitten):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testen eines unsicheren API-Aufrufs

Nachdem Sie nun eine erfolgreiche Anforderung durchgeführt haben, testen Sie den Fehlerfall, um sicherzustellen, dass Aufrufe Ihrer API mit einem *ungültigen* Token wie erwartet abgelehnt werden. Eine Möglichkeit zum Durchführen des Tests besteht darin, im Tokenwert einige Zeichen hinzuzufügen oder zu ändern, und anschließend die gleiche `GET`-Anforderung wie zuvor auszuführen.

1. Fügen Sie dem Tokenwert mehrere Zeichen hinzu, um ein ungültiges Token zu simulieren. Fügen Sie beispielsweise „INVALID“ zum Tokenwert hinzu, wie hier gezeigt:

    ![Screenshot: Abschnitt „Headers“ (Header) der Postman-Benutzeroberfläche, bei dem die Zeichenfolge INVALID in das Token eingefügt wurde](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Wählen Sie die Schaltfläche **Send** (Senden) aus, um die Anforderung auszuführen. Bei einem ungültigen Token ist das erwartete Ergebnis der nicht autorisierte Statuscode `401`:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Wenn Ihnen der Statuscode `401` angezeigt wird, haben Sie sichergestellt, dass nur Aufrufer mit einem gültigen, von Azure AD B2C ausgestellten Zugriffstoken erfolgreich Anforderungen an Ihre Azure API Management-API senden können.

## <a name="support-multiple-applications-and-issuers"></a>Unterstützen mehrerer Anwendungen und Aussteller

Mehrere Anwendungen interagieren in der Regel mit einer einzigen REST-API. Damit Ihre API Token für mehrere Apps akzeptieren kann, fügen Sie die entsprechenden App-Kennungen dem Element `<audiences>` in der Azure API Management-Eingangsrichtlinie zu.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Wenn Sie mehrere Tokenaussteller unterstützen möchten, fügen Sie deren Endpunkt-URIs dem `<issuers>`-Element in der Azure API Management-Eingangsrichtlinie hinzu.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrieren zu b2clogin.com

Wenn Sie über eine Azure API Management-API verfügen, die vom `login.microsoftonline.com`-Legacyendpunkt ausgestellte Token überprüft, sollten Sie die API und die aufgerufenen Anwendungen migrieren, damit von [b2clogin.com](b2clogin.md) ausgestellte Token verwendet werden.

Gehen Sie wie folgt vor, um eine gestaffelte Migration durchzuführen:

1. Fügen Sie in Ihrer Azure API Management-Eingangsrichtlinie Unterstützung für Token hinzu, die sowohl von b2clogin.com als auch von login.microsoftonline.com ausgestellt wurden.
1. Aktualisieren Sie Ihre Anwendungen nacheinander, um Token vom b2clogin.com-Endpunkt abzurufen.
1. Nachdem alle Anwendungen ordnungsgemäß Token von b2clogin.com abrufen, entfernen Sie die Unterstützung für von login.microsoftonline.com ausgestellte Token aus der API.

Die folgende beispielhafte Azure API Management-Eingangsrichtlinie veranschaulicht, wie Token akzeptiert werden, die sowohl von b2clogin.com als auch von login.microsoftonline.com ausgestellt wurden. Darüber hinaus unterstützt die Richtlinie API-Anforderungen von zwei Anwendungen.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure API Management-Richtlinien finden Sie unter [Richtlinien für die API-Verwaltung](../api-management/api-management-policies.md).

Informationen zur Migration von OWIN-basierten Web-APIs und den zugehörigen Anwendungen zu b2clogin.com finden Sie unter [Migrieren einer OWIN-basierten Web-API zu b2clogin.com](multiple-token-endpoints.md).

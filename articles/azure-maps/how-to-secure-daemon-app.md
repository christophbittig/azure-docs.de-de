---
title: Schützen einer Daemonanwendung in Microsoft Azure Maps
titleSuffix: Azure Maps
description: In diesem Artikel wird beschrieben, wie Sie Daemonanwendungen wie Hintergrundprozesse, Timer und Aufträge in einer vertrauenswürdigen und sicheren Umgebung in Microsoft Azure Maps hosten.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
custom.ms: subject-rbac-steps
ms.openlocfilehash: e64645f7bdcbfb40cbee0fd29d1df2464a5d4f3e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450854"
---
# <a name="secure-a-daemon-application"></a>Schützen einer Daemon-Anwendung

In diesem Artikel wird beschrieben, wie Sie Daemonanwendungen in einer vertrauenswürdigen und sicheren Umgebung in Microsoft Azure Maps hosten.

Beispiele für Daemonanwendungen:

- Azure-Webaufträge
- Azure-Funktionen-App
- Windows-Dienst
- Ein laufender und zuverlässiger Hintergrunddienst

## <a name="view-azure-maps-authentication-details"></a>Anzeigen von Azure Maps-Authentifizierungsdetails

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>Für Produktionsanwendungen sollten Azure AD und die rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) implementiert werden. Eine Übersicht über die Azure AD-Konzepte finden Sie unter [Authentifizierung bei Azure Maps](azure-maps-authentication.md).

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>Szenario: Authentifizierung mit gemeinsam genutzten Schlüsseln mit Azure Key Vault

Anwendungen, die die Authentifizierung mit gemeinsam genutzten Schlüsseln einsetzen, sollten die Schlüssel in einem sicheren Speicher speichern. In diesem Szenario wird beschrieben, wie Sie Ihren Anwendungsschlüssel sicher als Geheimnis in Azure Key Vault speichern. Anstatt den gemeinsam genutzten Schlüssel in der Anwendungskonfiguration zu speichern, kann ihn die Anwendung als Azure Key Vault-Geheimnis abrufen. Damit Schlüssel einfacher neu generiert werden können, wird empfohlen, dass Anwendungen nur jeweils einen Schlüssel verwenden. Anwendungen können den nicht genutzten Schlüssel dann neu generieren und den neu generierten Schlüssel in Azure Key Vault bereitstellen, während die aktuellen Verbindungen mit einem Schlüssel aufrechterhalten werden. Weitere Informationen zum Konfigurieren von Azure Key Vault finden Sie im [Azure Key Vault-Entwicklerleitfaden](../key-vault/general/developers-guide.md).

>[!IMPORTANT]
>In diesem Szenario wird indirekt über Azure Key Vault auf Azure Active Directory zugegriffen. Es wird jedoch empfohlen, die Azure AD-Authentifizierung direkt zu verwenden. Die direkte Authentifizierung mit Azure AD vermeidet zusätzliche Komplexität und umgeht die Notwendigkeit, die Authentifizierung mit gemeinsam genutzten Schlüsseln zu verwenden und Key Vault einzurichten.

Dieser Vorgang wird in den folgenden Schritten beschrieben:

1. [Erstellen Sie eine Azure Key Vault-Instanz.](../key-vault/general/quick-create-portal.md)
2. Erstellen Sie einen [Azure AD-Dienstprinzipal](../active-directory/fundamentals/service-accounts-principal.md), indem Sie eine App-Registrierung oder verwaltete Identität erstellen. Der erstellte Dienstprinzipal ist für den Zugriff auf Azure Key Vault verantwortlich.
3. Weisen Sie dem Dienstprinzipalzugriff auf Azure Key Vault-Geheimnisse die `get`-Berechtigung zu. Ausführliche Anweisungen zum Festlegen von Berechtigungen finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie über das Azure-Portal](../key-vault/general/assign-access-policy-portal.md).
4. Weisen Sie den Zugriff auf die `set`-Berechtigung für Geheimnisse sich selbst als Entwickler zu.
5. Legen Sie den gemeinsam genutzten Schlüssel in den Key Vault-Geheimnissen fest, und referenzieren Sie die Geheimnis-ID als Konfiguration für die Daemonanwendung.
6. Entfernen Sie die `set`-Berechtigung Ihrer Geheimnisse.
7. Implementieren Sie zum Abrufen des gemeinsam genutzten Schlüsselgeheimnisses aus Azure Key Vault die Azure Active Directory-Authentifizierung in der Daemonanwendung.
8. Erstellen Sie eine Azure Maps-REST-API-Anforderung mit dem gemeinsam genutzten Schlüssel.
Jetzt kann die Daemonanwendung den gemeinsam genutzten Schlüssel aus der Key Vault-Instanz abrufen.

> [!TIP]
> Wenn die App in der Azure-Umgebung gehostet wird, wird empfohlen, eine verwaltete Identität zu verwenden, um die Kosten und die Komplexität der Verwaltung eines Authentifizierungsgeheimnisses zu verringern. Informationen zum Einrichten einer verwalteten Identität finden Sie unter [Tutorial: Verbinden von Key Vault mit einer Azure-Web-App in .NET mithilfe einer verwalteten Identität](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

## <a name="scenario-azure-ad-role-based-access-control"></a>Szenario: Rollenbasierte Zugriffssteuerung von Azure AD

Nach der Erstellung eines Azure Maps-Kontos wird der `Client ID`-Wert von Azure Maps auf der Authentifizierungsdetailseite des Azure-Portals angezeigt. Dieser Wert steht für das Konto, das für REST-API-Anforderungen verwendet werden soll. Dieser Wert sollte in der Anwendungskonfiguration gespeichert und abgerufen werden, bevor HTTP-Anforderungen ausgeführt werden. Das Ziel des Szenarios besteht darin, die Daemonanwendung für die Authentifizierung bei Azure AD zu aktivieren und Azure Maps-REST-APIs aufzurufen.

> [!TIP]
>Es wird empfohlen, Azure Virtual Machines, Virtual Machine Scale Sets oder App Services als Host zu verwenden, damit Sie die Vorteile der Komponenten von verwalteten Identitäten nutzen können.

### <a name="host-a-daemon-on-azure-resources"></a>Hosten eines Daemon in Azure-Ressourcen

Wenn die Daemonanwendung in Azure-Ressourcen ausgeführt wird, können Sie verwaltete Azure-Identitäten konfigurieren, um von niedrigen Kosten und minimalem Verwaltungsaufwand für Anmeldeinformationen zu profitieren.

Informationen zum Aktivieren des Anwendungszugriffs auf eine verwaltete Identität finden Sie in der [Übersicht über verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Beispiele für Vorteile verwalteter Identitäten:

- Kryptografieauthentifizierung mit einem öffentlichen Schlüssel des vom Azure-System verwalteten X509-Zertifikats
- Azure AD-Sicherheit mit X509-Zertifikaten anstelle von geheimen Clientschlüsseln
- Azure verwaltet und erneuert alle Zertifikate, die der verwalteten Identitätsressource zugeordnet sind.
- Einfachere Verwaltung von Anmeldeinformationen, da eine verwaltete Identität keinen geschützten Geheimnisspeicherdienst wie Azure Key Vault erfordert

### <a name="host-a-daemon-on-non-azure-resources"></a>Hosten eines Daemon in Azure-externen Ressourcen

Für eine Ausführung in einer Azure-externen Umgebung sind verwaltete Identitäten nicht verfügbar. Daher müssen Sie einen Dienstprinzipal über eine Azure AD-Anwendungsregistrierung für die Daemonanwendung konfigurieren.

#### <a name="create-new-application-registration"></a>Erstellen einer neuen Anwendungsregistrierung

Wenn Sie Ihre Anwendungsregistrierung bereits erstellt haben, navigieren Sie zu [Zuweisen delegierter API-Berechtigungen](#assign-delegated-api-permissions).

So fügen Sie eine neue Anwendungsregistrierung hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Azure Active Directory** aus.

3. Klicken Sie im linken Bereich unter **Verwalten** auf **App-Registrierungen**.

4. Wählen Sie die Registerkarte **+ Neue Registrierung** aus.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="Anzeigen von App-Registrierungen":::

5. Geben Sie einen **Namen** ein, und wählen Sie einen **Supportkontotyp** aus.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="Erstellen einer App-Registrierung":::

6. Wählen Sie **Registrieren**.  

#### <a name="assign-delegated-api-permissions"></a>Zuweisen delegierter API-Berechtigungen

So weisen Sie Azure Maps delegierte API-Berechtigungen zu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, falls Sie dies noch nicht getan haben.

2. Wählen Sie **Azure Active Directory** aus.

3. Klicken Sie im linken Bereich unter **Verwalten** auf **App-Registrierungen**.

4. Wählen Sie Ihre Anwendung aus.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="Auswählen von App-Registrierungen":::

5. Klicken Sie im linken Bereich unter **Verwalten** auf **API-Berechtigungen**.

6. Wählen Sie **Berechtigung hinzufügen** aus.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="Hinzufügen einer App-Berechtigung":::

7. Wählen Sie die Registerkarte **Von meiner Organisation verwendete APIs** aus.

8. Geben Sie **Azure Maps** in das Suchfeld ein.

9. Wählen Sie **Azure Maps** aus.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="Anfordern einer App-Berechtigung":::

10. Aktivieren Sie das Kontrollkästchen **Access Azure Maps** (Auf Azure Maps zugreifen).

11. Wählen Sie **Berechtigungen hinzufügen** aus.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="Auswählen von App-API-Berechtigungen":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>Erstellen eines geheimen Clientschlüssels oder Konfigurieren eines Zertifikats

Für die Implementierung der server- oder anwendungsbasierten Authentifizierung in Ihrer Anwendung können Sie eine von zwei Optionen auswählen:

- Hochladen eines öffentlichen Schlüsselzertifikats
- Erstellen Sie einen geheimen Clientschlüssel.

##### <a name="upload-a-public-key-certificate"></a>Hochladen eines öffentlichen Schlüsselzertifikats

So laden Sie ein öffentliches Schlüsselzertifikat hoch:

1. Klicken Sie im linken Bereich unter **Verwalten** auf **Zertifikate und Geheimnisse**.

2. Wählen Sie **Zertifikat hochladen**.
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="Hochladen eines Zertifikats":::

3. Klicken Sie rechts neben dem Textfeld auf das Dateisymbol.

4. Wählen Sie eine *CRT*-, *CER*- oder *PEM*-Datei aus, und klicken Sie auf **Hinzufügen**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="Hochladen einer Zertifikatdatei":::

##### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

So erstellen Sie einen geheimen Clientschlüssel:

1. Klicken Sie im linken Bereich unter **Verwalten** auf **Zertifikate und Geheimnisse**.

2. Wählen Sie **+ Neuer geheimer Clientschlüssel** aus.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="Neuer geheimer Clientschlüssel":::

3. Geben Sie eine Beschreibung für den geheimen Clientschlüssel ein.

4. Wählen Sie **Hinzufügen**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="Hinzufügen eines neuen geheimen Clientschlüssels":::

5. Kopieren Sie das Geheimnis, und speichern Sie es sicher in einem Dienst wie Azure Key Vault. Das Geheimnis wird außerdem im Abschnitt [Anfordern eines Tokens mit einer verwalteten Identität](#request-a-token-with-managed-identity) dieses Artikels verwendet.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="Kopieren des geheimen Clientschlüssels":::

     >[!IMPORTANT]
     >Anweisungen zum sicheren Speichern eines Zertifikats oder Geheimnisses finden Sie im [Azure Key Vault-Entwicklerleitfaden](../key-vault/general/developers-guide.md). Dieses Geheimnis verwenden Sie zum Abrufen von Tokens aus Azure AD.

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>Anfordern eines Tokens mit einer verwalteten Identität

Nachdem eine verwaltete Identität für die Hostressource konfiguriert wurde, können Sie mit dem Azure SDK oder der REST-API ein Token für Azure Maps abrufen. Weitere Informationen hierzu finden Sie unter [Abrufen eines Zugriffstokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

### <a name="request-token-with-application-registration"></a>Anforderungstoken mit Anwendungsregistrierung

Nachdem Sie Ihre App registriert und mit Azure Maps verknüpft haben, müssen Sie ein Zugriffstoken anfordern.

So fordern Sie das Zugriffstoken an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, falls Sie dies noch nicht getan haben.

2. Wählen Sie **Azure Active Directory** aus.

3. Klicken Sie im linken Bereich unter **Verwalten** auf **App-Registrierungen**.

4. Wählen Sie Ihre Anwendung aus.

5. Daraufhin sollte die Übersicht angezeigt werden. Kopieren Sie die Anwendungs-ID (Client) und die Verzeichnis-ID (Mandant).

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="Kopieren von Tokenparametern":::

Hier wird die Anwendung [Postman](https://www.postman.com/) für die Tokenanforderung verwendet. Sie können jedoch auch eine andere API-Entwicklungsumgebung einsetzen.

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung).

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein, z. B. *POST Token Request* (POST-Tokenanforderung).

4. Wählen Sie als HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL in die Adressleiste ein (ersetzen Sie `{Tenant-ID}` durch die Verzeichnis-ID (Mandant), `{Client-ID}` durch die Anwendungs-ID (Client) und `{Client-Secret}` durch Ihren geheimen Clientschlüssel):

    ```http
    https://login.microsoftonline.com/{Tenant-ID}/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id={Client-ID}&client_secret={Client-Secret}%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. Wählen Sie **Senden** aus.

7. Die folgende JSON-Antwort sollte angezeigt werden:

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

Weitere Informationen zum Authentifizierungsflow finden Sie unter [OAuth 2.0-Flow für Clientanmeldeinformationen auf Microsoft Identity Platform](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret).

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Beispiele:
> [!div class="nextstepaction"]
> [Authentifizierungsszenarien für Azure AD](../active-directory/develop/authentication-vs-authorization.md)

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

---
title: Erstellen eines SCIM-Endpunkts für die Benutzerbereitstellung in Apps aus Azure Active Directory
description: Hier erfahren Sie, wie Sie einen SCIM-Endpunkt entwickeln, Ihre SCIM-API mit Azure Active Directory integrieren und mit der Automatisierung der Bereitstellung von Benutzern und Gruppen in Ihren Cloudanwendungen beginnen.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5f1799b6a8fe0ff4feb69797deec11c29c69a81c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990594"
---
# <a name="tutorial-develop-a-sample-scim-endpoint-in-azure-active-directory"></a>Tutorial: Entwickeln eines SCIM-Beispielendpunkts in Azure Active Directory

Niemand möchte einen neuen Endpunkt von Grund auf neu erstellen. Daher haben wir [Referenzcode](https://aka.ms/scimreferencecode) erstellt, der Sie bei den ersten Schritten mit [System for Cross-domain Identity Management (SCIM)](https://aka.ms/scimoverview) unterstützen soll. Sie können den SCIM-Endpunkt ohne Code in nur fünf Minuten einrichten und ausführen.

In diesem Tutorial wird beschrieben, wie Sie den SCIM-Referenzcode in Azure bereitstellen und mithilfe von Postman oder durch Integration in den Azure AD-SCIM-Client (Azure Active Directory) testen. Dieses Tutorial richtet sich an Entwickler, die mit SCIM beginnen möchten, und an andere Benutzer, die einen SCIM-Endpunkt testen möchten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Bereitstellen Ihres SCIM-Endpunkts in Azure.
> * Testen Ihres SCIM-Endpunkts.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Bereitstellen Ihres SCIM-Endpunkts in Azure

In den folgenden Schritten wird der SCIM-Endpunkt mithilfe von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) und [Azure App Service](../../app-service/index.yml) für einen Dienst bereitgestellt. Der SCIM-Referenzcode kann auch lokal ausgeführt, von einem lokalen Server gehostet oder unter einem anderen externen Dienst bereitgestellt werden.

1. Navigieren Sie in GitHub zum [Referenzcode](https://github.com/AzureAD/SCIMReferenceCode), und wählen Sie **Klonen oder herunterladen** aus.

1. Wählen Sie **Auf Desktop öffnen** aus, oder kopieren Sie den Link, öffnen Sie Visual Studio, und wählen Sie **Code klonen oder auschecken** aus, um den kopierten Link einzugeben und eine lokale Kopie zu erstellen.

1. Stellen Sie sicher, dass Sie sich in Visual Studio bei dem Konto anmelden, das Zugriff auf Ihre Hostingressourcen hat.

1. Öffnen Sie im Projektmappen-Explorer *Microsoft.SCIM.sln*, und klicken Sie mit der rechten Maustaste auf die Datei *Microsoft.SCIM.WebHostSample*. Klicken Sie auf **Veröffentlichen**.

    ![Screenshot mit der Beispieldatei.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Um diese Projektmappe lokal auszuführen, doppelklicken Sie auf das Projekt, und wählen Sie **IIS Express** aus, um das Projekt als Webseite mit einer URL für einen lokalen Host zu starten.

1. Wählen Sie **Profil erstellen** aus, und stellen Sie sicher, dass **App Service** und **Neu erstellen** ausgewählt sind.

    ![Screenshot mit dem Fenster „Veröffentlichen“.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Durchlaufen Sie die Dialogfeldoptionen, und benennen Sie die App nach Wunsch. Dieser Name wird sowohl in der App als auch in der SCIM-Endpunkt-URL verwendet.

    ![Screenshot, der das Erstellen eines neuen App-Diensts zeigt.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Wählen Sie die zu verwendende Ressourcengruppe und dann **Veröffentlichen** aus.

    ![Screenshot, der das Veröffentlichen eines neuen App-Diensts zeigt.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Navigieren Sie in **Azure App Service** > **Konfiguration** zu Ihrer Anwendung, und wählen Sie **Neue Anwendungseinstellung** aus, um die Einstellung *Token__TokenIssuer* mit dem Wert `https://sts.windows.net/<tenant_id>/` hinzuzufügen. Ersetzen Sie `<tenant_id>` durch die Azure AD-Mandanten-ID. Wenn Sie den SCIM-Endpunkt mithilfe von [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) testen möchten, fügen Sie eine *ASPNETCORE_ENVIRONMENT*-Einstellung mit dem Wert `Development` hinzu.

   ![Screenshot, der das Fenster „Anwendungseinstellungen“ zeigt.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Wenn Sie den Endpunkt mit einer Unternehmensanwendung im [Azure-Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client) testen, stehen Ihnen zwei Optionen zur Verfügung. Sie können die Umgebung in beibehalten `Development` und das Testtoken vom `/scim/token`-Endpunkt bereitstellen oder die Umgebung in `Production` ändern und das Tokenfeld leer lassen.

Das ist alles! Der SCIM-Endpunkt ist nun veröffentlicht und ermöglicht Ihnen die Verwendung der Azure App Service-URL zum Testen des SCIM-Endpunkts.

## <a name="test-your-scim-endpoint"></a>Testen Ihres SCIM-Endpunkts

Anforderungen an einen SCIM-Endpunkt erfordern eine Autorisierung. Der SCIM-Standard bietet mehrere Authentifizierungs- und Autorisierungsoptionen, z. B. Cookies, Standardauthentifizierung, TLS-Clientauthentifizierung oder eine der Methoden in [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Vermeiden Sie unbedingt unsichere Methoden wie Benutzername und Kennwort, sondern verwenden Sie eine sichere Methode wie OAuth. Azure AD unterstützt Bearertoken mit langer Gültigkeitsdauer (für Kataloganwendungen und andere Anwendungen) und die OAuth-Autorisierungsgewährung (für Kataloganwendungen).

> [!NOTE]
> Die im Repository bereitgestellten Autorisierungsmethoden dienen nur zu Testzwecken. Bei der Integration in Azure AD können Sie die Autorisierungsanleitung lesen. Weitere Informationen finden Sie unter [Planen der Bereitstellung für einen SCIM-Endpunkt](use-scim-to-provision-users-and-groups.md).

Die Entwicklungsumgebung ermöglicht Features, die in einer Produktionsumgebung zu unsicher wären, z. B. Referenzcode zum Steuern der Überprüfung von Sicherheitstoken. Der Code zur Überprüfung von Token verwendet ein selbstsigniertes Token, und der Signierungsschlüssel ist in der Konfigurationsdatei gespeichert. Beispiel: **Token:IssuerSigningKey**-Parameter in der Datei *appsettings.Development.js*.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Wenn Sie eine **GET**-Anforderung an den `/scim/token`-Endpunkt senden, wird ein Token mithilfe des konfigurierten Schlüssels ausgegeben. Dieses Token kann als Bearertoken für die nachfolgende Autorisierung verwendet werden.

Der Standardcode zur Tokenvalidierung ist für die Verwendung eines von Azure AD ausgestellten Tokens konfiguriert. Außerdem muss der ausstellende Mandant mithilfe des **Token:TokenIssuer**-Parameters in der Datei *appsettings.json* konfiguriert sein.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Testen der Endpunkte mithilfe von Postman

Nachdem Sie den SCIM-Endpunkt bereitgestellt haben, können Sie testen, ob er mit SCIM RFC kompatibel ist. Dieses Beispiel enthält eine Reihe von Tests in Postman, die CRUD-Vorgänge (erstellen, lesen, aktualisieren und löschen) für Benutzer und Gruppen, Filtern, das Aktualisieren der Gruppenmitgliedschaft und das Deaktivieren von Benutzern überprüfen.

Die Endpunkte befinden sich im `{host}/scim/`-Verzeichnis, und Sie können HTTP-Standardanforderungen verwenden, um mit Ihnen zu interagieren. Informationen zum Ändern der Route von `/scim/` finden Sie unter *ControllerConstant.cs* in **AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers**.

> [!NOTE]
> Sie können nur HTTP-Endpunkte für lokale Tests verwenden. Der Azure AD-Bereitstellungdienst erfordert, dass der Endpunkt HTTPS unterstützt.

1. Laden Sie [Postman](https://www.getpostman.com/downloads/) herunter, und starten Sie die Anwendung.
1. Kopieren Sie diesen Link, und fügen Sie ihn in Postman ein, um die Testsammlung zu importieren: `https://aka.ms/ProvisioningPostman`.

    ![Screenshot, der das Importieren der Testsammlung in Postman zeigt.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Erstellen Sie eine Testumgebung mit den folgenden Variablen:

   |Umgebung|Variable|Wert|
   |-|-|-|
   |Projekt lokal mit IIS Express ausführen|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359` *( **`:`** nicht vergessen)*|
   ||**API**|`scim`|
   |Projekt lokal mit Kestrel ausführen|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001` *( **`:`** nicht vergessen)*|
   ||**API**|`scim`|
   |Endpunkt in Azure hosten|||
   ||**Server**|*(Geben Sie Ihre SCIM-URL ein.)*|
   ||**Port**|*(Lassen Sie dieses Feld leer.)*|
   ||**API**|`scim`|

1. Verwenden Sie **Get Key** (Schlüssel abrufen) aus der Postman-Sammlung, um eine **GET**-Anforderung an den Tokenendpunkt zu senden und ein Sicherheitstoken abzurufen, das für nachfolgende Anforderungen in der Variable **token** gespeichert werden soll.

   ![Screenshot, der den „Get Key“-Ordner von Postman zeigt.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Um einen SCIM-Endpunkt sicher zu machen, benötigen Sie ein Sicherheitstoken, bevor Sie eine Verbindung herstellen. Das Tutorial verwendet den `{host}/scim/token`-Endpunkt, um ein selbstsigniertes Token zu generieren.

Das ist alles! Sie können jetzt die **Postman**-Sammlung ausführen, um die Funktion des SCIM-Endpunkts zu testen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie einen SCIM-konformen Benutzer- und Gruppenendpunkt mit Interoperabilität für einen Client entwickeln möchten, informieren Sie sich hier über die [SCIM-Clientimplementierung](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutorial: Entwickeln eines SCIM-Endpunkts und Planen seiner Bereitstellung](use-scim-to-provision-users-and-groups.md)
> [Tutorial: Konfigurieren der Bereitstellung für eine Katalog-App](configure-automatic-user-provisioning-portal.md)
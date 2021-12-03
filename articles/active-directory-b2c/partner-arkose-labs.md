---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Arkose Labs
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Arkose Labs zum Identifizieren von riskanten und betrügerischen Benutzern
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c6e31a9026e4598d12367d623cfb166663c6fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044815"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Arkose Labs mit Azure Active Directory B2C

In diesem Beispieltutorial wird beschrieben, wie Sie die Azure Active Directory (AD) B2C-Authentifizierung in [Arkose Labs](https://www.arkoselabs.com/) integrieren. Arkose Labs unterstützt Organisationen vor Botangriffen, Angriffen mit Kontoübernahmen und betrügerischen Kontoeröffnungen.  

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein [Arkose Labs](https://www.arkoselabs.com/book-a-demo/)-Konto.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Arkose Labs-Integration umfasst die folgenden Komponenten:

- **Arkose Labs**: Ein Betrugs- und Missbrauchsdienst zum Schutz vor Bots und anderem automatisierten Missbrauch.

- **Benutzerflow für die Azure AD B2C-Registrierung**: Die Registrierungsoberfläche, die den Arkose Labs-Dienst verwendet. Für die Integration in den Arkose Labs-Dienst werden benutzerdefinierter HTML- und JavaScript-Code und API-Connectors verwendet.

- **Azure Functions**: Der von Ihnen gehostete API-Endpunkt, der mit dem Feature „API-Connectors“ arbeitet. Diese API ist für die Ausführung der serverseitigen Überprüfung des Arkose Labs-Sitzungstokens verantwortlich.

Im folgenden Diagramm ist beschrieben, wie Arkose Labs mit Azure AD B2C integriert wird.

![Abbildung des Arkose Labs-Architekturdiagramms](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Schritt  | BESCHREIBUNG |
|---|---|
|1     | Ein Benutzer registriert sich und erstellt ein Konto. Wenn der Benutzer die Option „Senden“ auswählt, wird eine Arkose Labs-Abfrage angezeigt.         |
|2     |  Nachdem der Benutzer die Abfrage abgeschlossen hat, sendet Azure AD B2C den Status zwecks Generierung eines Tokens an Arkose Labs. |
|3     |  Arkose Labs generiert ein Token und sendet es an Azure AD B2C zurück.   |
|4     |  Azure AD B2C ruft eine zwischengeschaltete Web-API auf, um das Registrierungsformular zu übergeben.      |
|5     |  Die zwischengeschaltete Web-API sendet das Registrierungsformular zwecks Tokenüberprüfung an Arkose Labs.    |
|6     | Die Überprüfungsergebnisse werden von Arkose Labs verarbeitet und an die zwischengeschaltete Web-API zurückgesendet.|
|7     | Die zwischengeschaltete Web-API sendet das Erfolgs- oder Fehlerergebnis der Abfrage an Azure AD B2C. |
|8     | Bei erfolgreichem Abschluss der Abfrage wird ein Registrierungsformular an Azure AD B2C gesendet, und Azure AD B2C schließt die Authentifizierung ab.|

## <a name="onboard-with-arkose-labs"></a>Durchführen des Onboardings für Arkose Labs

1. Wenden Sie sich an [Arkose](https://www.arkoselabs.com/book-a-demo/), und erstellen Sie ein Konto.

2. Navigieren Sie nach der Erstellung des Kontos zu https://dashboard.arkoselabs.com/login.  

3. Navigieren Sie im Dashboard zu den Websiteeinstellungen, um Ihren öffentlichen und privaten Schlüssel zu ermitteln. Sie benötigen diese Informationen später, um Azure AD B2C zu konfigurieren. Im [Beispielcode](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) werden die Werte von öffentlichen und privaten Schlüsseln als `ARKOSE_PUBLIC_KEY` und `ARKOSE_PRIVATE_KEY` bezeichnet.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Teil 1: Erstellen eines benutzerdefinierten ArkoseSessionToken-Attributs

Führen Sie die folgenden Schritte aus, um ein benutzerdefiniertes Attribut zu erstellen:  

1. Navigieren Sie zu **Azure-Portal** > **Azure AD B2C**.

2. Wählen Sie **Benutzerattribute** aus.

3. Wählen Sie **Hinzufügen** aus.

4. Geben Sie **ArkoseSessionToken** als Attributnamen ein.

5. Klicken Sie auf **Erstellen**.

Erfahren Sie mehr über [benutzerdefinierte Attribute](./user-flow-custom-attributes.md?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Teil 2: Erstellen eines Benutzerflows

Der Benutzerflow kann entweder für die **Registrierung** und **Anmeldung** oder nur für die **Registrierung** erstellt werden. Der Arkose Labs-Benutzerflow wird nur während der Registrierung angezeigt.

1. Folgen Sie den [Anweisungen](./tutorial-create-user-flows.md) zum Erstellen eines Benutzerflows. Wenn Sie einen vorhandenen Benutzerflow verwenden möchten, muss es sich dabei um einen Benutzerflow des Versionstyps **Empfohlen** handeln.

2. Wechseln Sie in den Einstellungen des Benutzerflows zu **Benutzerattribute**, und wählen Sie den Anspruch **ArkoseSessionToken** aus.

![Screenshot: Auswählen von benutzerdefinierten Attributen](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Teil 3: Konfigurieren von benutzerdefiniertem HTML- und JavaScript-Code und Seitenlayouts

Navigieren Sie zum bereitgestellten [HTML-Skript](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html). Die Datei enthält eine HTML-Vorlage mit JavaScript-Tags (`<script>`-Tags), die drei Aufgaben ausführen:

1. Laden des Arkose Labs-Skripts, mit dem das Arkose Labs-Widget gerendert und die clientseitige Arkose Labs-Überprüfung durchgeführt wird

2. Ausblenden des dem benutzerdefinierten `ArkoseSessionToken`-Attribut entsprechenden `extension_ArkoseSessionToken`-Eingabeelements und der Bezeichnung aus der Benutzeroberfläche, die dem Benutzer angezeigt wird.

3. Wenn ein Benutzer die Arkose Labs-Abfrage abschließt, überprüft Arkose Labs die Antwort des Benutzers und generiert ein Token. Der Rückruf `arkoseCallback` im benutzerdefinierten JavaScript-Code legt den Wert von `extension_ArkoseSessionToken` auf den Wert des generierten Tokens fest. Dieser Wert wird an den API-Endpunkt übermittelt, wie im nächsten Abschnitt beschrieben.

    Weitere Informationen zur clientseitigen Arkose Labs-Überprüfung finden Sie in [diesem Artikel](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup).

Führen Sie die aufgeführten Schritte aus, um den benutzerdefinierten HTML- und JavaScript-Code für Ihren Benutzerflow zu verwenden.

1. Ändern Sie die Datei [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) so, dass `<ARKOSE_PUBLIC_KEY>` mit dem Wert übereinstimmt, den Sie für die clientseitige Überprüfung generiert und zum Laden des Arkose Labs-Skripts für Ihr Konto verwendet haben.

2. Hosten Sie die HTML-Seite auf einem Webendpunkt, für den die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) aktiviert ist. [Erstellen Sie ein Azure Blob Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) und [konfigurieren Sie CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Wenn Sie Ihren eigenen benutzerdefinierten HTML-Code verwenden, kopieren Sie die `<script>`-Elemente, und fügen Sie diese auf Ihrer HTML-Seite ein.

3. Führen Sie die folgenden Schritte aus, um die Seitenlayouts zu konfigurieren:

   a. Navigieren Sie im Azure-Portal zu **Azure AD B2C**.

   b. Navigieren Sie zu **Benutzerflows**, und wählen Sie Ihren Benutzerflow aus.

   c. Wählen Sie **Seitenlayouts** aus.

   d. Wählen Sie das Layout **Seite für Registrierung mit lokalem Konto** aus.

   e. Legen Sie **Benutzerdefinierten Seiteninhalt verwenden** auf **Ja** fest.

   f. Fügen Sie den URI Ihrer benutzerdefinierten HTML-Seite in **Benutzerdefinierter Seiten-URI** ein.

   g. Wenn Sie ein soziales Netzwerk als Identitätsanbieter verwenden, wiederholen Sie die Schritte **e** und **f** für das Layout **Seite zur Registrierung über Konto für soziales Netzwerk**.

   ![Screenshot: Seitenlayouts](media/partner-arkose-labs/page-layouts.png)

4. Wechseln Sie in Ihrem Benutzerflow zu **Eigenschaften**, und wählen Sie **JavaScript zur Erzwingung des Seitenlayouts aktivieren (Vorschau)** aus. Weitere Informationen hierzu finden Sie in [diesem Artikel](./javascript-and-page-layout.md?pivots=b2c-user-flow).

### <a name="part-4---create-and-deploy-your-api"></a>Teil 4: Erstellen und Bereitstellen Ihrer API

Installieren Sie die [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) für Visual Studio Code.

>[!Note]
>In den in diesem Abschnitt beschriebenen Schritten wird davon ausgegangen, dass Sie Visual Studio Code zum Bereitstellen der Azure-Funktion verwenden. Sie können auch das Azure-Portal, ein Terminal oder eine Eingabeaufforderung oder einen anderen Code-Editor für die Bereitstellung verwenden.

#### <a name="run-the-api-locally"></a>Lokales Ausführen der API

1. Navigieren Sie in Visual Studio Code auf der linken Navigationsleiste zur Azure-Erweiterung. Wählen Sie den Ordner **Lokales Projekt** aus, der Ihre lokale Azure-Funktion darstellt.

2. Drücken Sie **F5**, oder verwenden Sie das Menü **Debuggen** > **Debuggen starten**, um den Debugger zu starten und an den Azure Functions-Host anzufügen. Dieser Befehl verwendet automatisch die einzige von Azure Functions erstellte Debugkonfiguration.

3. Die Azure Functions-Erweiterung generiert automatisch einige Dateien für die lokale Entwicklung, installiert Abhängigkeiten und installiert Azure Functions Core Tools, sofern nicht bereits vorhanden. Diese Tools unterstützen Sie beim Debuggen.

4. Die Ausgabe von Functions Core Tools wird im Visual Studio Code-Bereich **Terminal** angezeigt. Nachdem der Host gestartet wurde, drücken Sie die **ALT-TASTE**, und klicken Sie gleichzeitig auf die lokale URL in der Ausgabe, um den Browser zu öffnen und die Funktion auszuführen. Klicken Sie im Azure Functions-Explorer mit der rechten Maustaste auf die Funktion, um die URL der lokal gehosteten Funktion anzuzeigen.

Wenn Sie die lokale Instanz während des Testens erneut bereitstellen möchten, wiederholen Sie die Schritte 1 bis 4.

#### <a name="add-environment-variables"></a>Hinzufügen von Umgebungsvariablen

In diesem Beispiel wird der Web-API-Endpunkt mithilfe der [HTTP-Standardauthentifizierung](https://tools.ietf.org/html/rfc7617) geschützt.

Benutzername und Kennwort werden als Umgebungsvariablen und nicht als Teil des Repositorys gespeichert. Weitere Informationen finden Sie im Abschnitt [Datei für lokale Einstellungen: „local.settings.json“](../azure-functions/functions-develop-local.md#local-settings-file).

1. Erstellen Sie die Datei „local.settings.json“ in Ihrem Stammverzeichnis.

2. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei ein:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Bei den Werten für **BASIC_AUTH_USERNAME** und **BASIC_AUTH_PASSWORD** handelt es sich um die Anmeldeinformationen, die zum Authentifizieren des API-Aufrufs Ihrer Azure Functions-Instanz verwendet werden. Wählen Sie die gewünschten Werte aus.

`<ARKOSE_PRIVATE_KEY>` ist der serverseitige geheime Schlüssel, den Sie im Arkose Labs-Dienst generiert haben. Er wird zum Aufrufen der [serverseitigen Arkose Labs-Überprüfungs-API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) verwendet, um den Wert des vom Front-End generierten Tokens (`ArkoseSessionToken`) zu überprüfen.

`<B2C_EXTENSIONS_APP_ID>` ist die Anwendungs-ID der App, die von Azure AD B2C zum Speichern benutzerdefinierter Attribute im Verzeichnis verwendet wird. Diese Anwendungs-ID finden Sie, indem Sie im Bereich **Übersicht** zu „App-Registrierungen“ navigieren, nach „b2c-extensions-app“ suchen und die Anwendungs-ID (Client-ID) kopieren. Entfernen Sie die `-`-Zeichen.

![Screenshot: Suchen nach der App-ID](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Bereitstellen der Anwendung im Web

1. Führen Sie die in [diesem](/azure/javascript/tutorial-vscode-serverless-node-04) Leitfaden beschriebenen Schritte aus, um Ihre Azure Functions-App in der Cloud bereitzustellen. Kopieren Sie die Webendpunkt-URL Ihrer Azure Functions-App.

2. Wählen Sie nach der Bereitstellung die Option **Einstellungen hochladen** aus. Die Umgebungsvariablen werden in die [Anwendungseinstellungen](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) des App-Diensts hochgeladen. Diese Anwendungseinstellungen können auch [im Azure-Portal konfiguriert oder verwaltet werden](../azure-functions/functions-how-to-use-azure-function-app-settings.md).

Weitere Informationen zur Visual Studio Code-Entwicklung für Azure Functions finden Sie in [diesem Artikel](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files).

#### <a name="configure-and-enable-the-api-connector"></a>Konfigurieren und Aktivieren des API-Connectors

[Erstellen Sie einen API-Connector](./add-api-connector.md), und aktivieren Sie ihn für Ihren Benutzerflow. Die Konfiguration des API-Connectors sollte wie folgt aussehen:

![Screenshot: Konfigurieren des API-Connectors](media/partner-arkose-labs/configure-api-connector.png)

- **Endpunkt-URL**: Dies ist die Azure Functions-URL, die Sie zuvor beim Bereitstellen von Azure Function kopiert haben.

- **Benutzername und Kennwort**: Die Angaben (Benutzername und Kennwort), die Sie zuvor als Umgebungsvariablen definiert haben.

Um den API-Connector zu aktivieren, wählen Sie in den Einstellungen der **API-Connectors** für Ihren Benutzerflow den API-Connector aus, der im Schritt **Vor dem Erstellen des Benutzers** aufgerufen werden soll. Dadurch wird die API aufgerufen, wenn ein Benutzer im Registrierungsflow die Option **Erstellen** auswählt. Die API führt eine serverseitige Überprüfung des `ArkoseSessionToken`-Werts durch, der durch den Rückruf des Arkose-Widgets `arkoseCallback` festgelegt wurde.

![Screenshot: Aktivieren des API-Connectors](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Benutzerflows** aus.

2. Wählen Sie den zuvor erstellten Benutzerflow aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. Anwendung: Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. Antwort-URL: Wählen Sie die Umleitungs-URL aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Abmelden

6. Führen Sie den Anmeldeflow aus.  

7. Nachdem Sie **Weiter** ausgewählt haben, wird ein Arkose-Puzzle angezeigt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Beispielcodes](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) für den Benutzerflow für die Azure AD B2C-Registrierung

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

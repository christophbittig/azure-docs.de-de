---
title: 'Schnellstart: Client Bibliothek zur optischen Zeichenerkennung für Node.js'
description: Erste Schritte mit der optischen Zeichen Erkennungs Client-Bibliothek für Node.js mit dieser Schnellstartanleitung
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 2260df8403a27d57f7bd5b9fe7612147c935f7d7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520055"
---
<a name="HOLTop"></a>

Verwenden Sie die Client Bibliothek zur optischen Zeichenerkennung, um gedruckten und handschriftlichen Text mit der Read-API zu lesen.

[Referenzdokumentation](/javascript/api/@azure/cognitiveservices-computervision/) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Paket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Beispiele](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf.

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen.

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie die NPM-Pakete `ms-rest-azure` und `@azure/cognitiveservices-computervision`.

```console
npm install @azure/cognitiveservices-computervision
```

Installieren Sie außerdem das asynchrone Modul:

```console
npm install async
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie die neue Datei *index.js*, und öffnen Sie sie in einem Text-Editor.

### <a name="find-the-subscription-key-and-endpoint"></a>Ermitteln des Abonnementschlüssels und Endpunkts

Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Ressource für maschinelles Sehen erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihren Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Erstellen Sie Variablen für den Schlüssel und Endpunkt Ihres Maschinelles Sehen-Abonnements. Fügen Sie Ihren Abonnementschlüssel und -endpunkt an der angegeben Stelle in den folgenden Code ein. Der Maschinelles Sehen-Endpunkt hat das Format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Denken Sie daran, den Abonnementschlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Ich habe die Variablen eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client&product=computer-vision&page=node-sdk)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen des OCR Node.js SDK.

|name|BESCHREIBUNG|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Diese Klasse wird für alle Funktionen der Maschinelles Sehen-API benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der OCR Clientbibliothek für Node.js ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Lesen von gedrucktem und handschriftlichem Text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients


Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials)-Objekt mit Ihrem Schlüssel und Endpunkt, und verwenden Sie es zum Erstellen eines [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-Objekts.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definieren Sie anschließend die Funktion `computerVision`, und deklarieren Sie eine asynchrone Serie mit einer primären Funktion und einer Rückruffunktion. Am Ende des Skripts vervollständigen Sie diese Funktionsdefinition und rufen sie anschließend auf.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#read-printed-and-handwritten-text) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client&product=computer-vision&page=node-sdk)



## <a name="read-printed-and-handwritten-text"></a>Lesen von gedrucktem und handschriftlichem Text

Der OCR-Dienst kann sichtbaren Text in einem Bild extrahieren und in eine Zeichenfolge konvertieren. In diesem Beispiel werden die Lesevorgänge verwendet.

### <a name="set-up-test-images"></a>Einrichten von Testbildern

Speichern Sie einen Verweis auf die URL der Bilder, aus denen Sie Text extrahieren möchten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Sie können auch Text aus einem lokalen Bild auslesen. Sehen Sie sich die [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-Methoden an, etwa **readInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) Szenarien zu lokalen Bildern.

### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Definieren Sie die folgenden Felder in Ihrer Funktion, um die Statuswerte der Read-Aufrufe anzugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Fügen Sie den unten angegebenen Code hinzu, mit dem die Funktion `readTextFromURL` für die jeweiligen Bilder aufgerufen wird.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definieren Sie die Funktion `readTextFromURL`. Hiermit wird die **read**-Methode für das Clientobjekt aufgerufen, mit der eine Vorgangs-ID zurückgegeben und ein asynchroner Prozess zum Auslesen des Bildinhalts gestartet wird. Anschließend wird die Vorgangs-ID verwendet, um den Vorgangsstatus zu überprüfen, bis die Ergebnisse zurückgegeben werden. Danach werden die extrahierten Ergebnisse zurückgegeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definieren Sie als Nächstes die Hilfsfunktion `printRecText`, mit der die Ergebnisse der Lesevorgänge in der Konsole ausgegeben werden.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Ich habe Text gelesen.](?success=read-printed-handwritten-text#run-the-application) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text&product=computer-vision&page=node-sdk)

## <a name="close-the-function"></a>Schließen der Funktion

Schließen Sie die Funktion `computerVision`, und rufen Sie sie anschließend auf.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Ich habe die Anwendung ausgeführt.](?success=run-the-application#clean-up-resources) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application&product=computer-vision&page=node-sdk)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ich habe die Ressourcen bereinigt.](?success=clean-up-resources#next-steps) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources&product=computer-vision&page=node-sdk)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie die OCR-Clientbibliothek installieren und die Lese-API verwenden. Informieren Sie sich als Nächstes ausführlicher über die Funktionen der Lese-API.

> [!div class="nextstepaction"]
>[Aufrufen der Lese-API](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR-Übersicht](../../overview-ocr.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

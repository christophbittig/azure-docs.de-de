---
title: 'Schnellstart: Client Bibliothek zur optischen Zeichenerkennung für GO'
titleSuffix: Azure Cognitive Services
description: Erste Schritte mit der optischen Zeichen Erkennungs Client-Bibliothek für Go mit dieser Schnellstartanleitung.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: a6dbc2b54c8fac9f2b1bdb219784f873d99432f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520559"
---
<a name="HOLTop"></a>

Verwenden Sie die OCR-Client Bibliothek, um gedruckten und handschriftlichen Text aus Bildern zu lesen.

[Referenzdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Paket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version von [Go](https://golang.org/dl/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen </a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung Ihrer Anwendung mit dem Dienst für maschinelles Sehen herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-go-project-directory"></a>Erstellen eines Go-Projektverzeichnisses

Erstellen Sie in einem Konsolenfenster (cmd, PowerShell, Terminal, Bash) einen neuen Arbeitsbereich für Ihr Go-Projekt namens `my-app`, und navigieren Sie dorthin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Ihr Arbeitsbereich enthält drei Ordner:

* **src**: Das Verzeichnis für Quellcode und Pakete. Alle mit dem Befehl `go get` installierten Pakete werden in diesem Verzeichnis platziert.
* **pkg**: Das Verzeichnis für die kompilierten Go-Paketobjekte. Diese Dateien haben alle die Erweiterung `.a`.
* **bin**: Das Verzeichnis für die ausführbaren Binärdateien, die bei der Ausführung von `go install` erstellt werden.

> [!TIP]
> Weitere Informationen zur Struktur eines Go-Arbeitsbereichs finden Sie in der [Dokumentation zur Go-Sprache](https://golang.org/doc/code.html#Workspaces). Dieses Handbuch enthält Informationen zum Festlegen von `$GOPATH` und `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installieren der Clientbibliothek für Go

Installieren Sie als Nächstes die Clientbibliothek für Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

Bei Verwendung von DEP können Sie alternativ Folgendes in Ihrem Repository ausführen:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Erstellen einer Go-Anwendung 

Erstellen Sie als Nächstes im Verzeichnis **src** eine Datei mit dem Namen `sample-app.go`:

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Öffnen Sie `sample-app.go` in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Text-Editor.

Deklarieren Sie einen Kontext am Skriptstamm. Dieses Objekt wird zum Ausführen der meisten Funktionsaufrufe für maschinelles Sehen benötigt.

### <a name="find-the-subscription-key-and-endpoint"></a>Ermitteln des Abonnementschlüssels und Endpunkts

Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Ressource für maschinelles Sehen erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihren Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 

Erstellen Sie Variablen für den Schlüssel und Endpunkt Ihres Maschinelles Sehen-Abonnements. Fügen Sie Ihren Abonnementschlüssel und -endpunkt an der angegeben Stelle in den folgenden Code ein. Der Maschinelles Sehen-Endpunkt hat das Format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Denken Sie daran, den Abonnementschlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise [Azure Key Vault](../../../../key-vault/general/overview.md).

Beginnen Sie nun damit, Code für verschiedene Vorgänge der OCR Vorgänge hinzuzufügen.

> [!div class="nextstepaction"]
> [Ich habe die Variablen eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client&product=computer-vision&page=go-sdk)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen des OCR Go SDK.

|name|BESCHREIBUNG|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Diese Klasse wird für alle Funktionen für maschinelles Sehen benötigt, also beispielsweise für die Bildanalyse und das Lesen von Text. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dieser Typ enthält die Ergebnisse eines Batchlesevorgangs. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der OCR Clientbibliothek für Go ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Lesen von gedrucktem und handschriftlichem Text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE]
> In diesem Schritt wird vorausgesetzt, dass Sie für den Schlüssel und den Endpunkt für maschinelles Sehen (`COMPUTER_VISION_SUBSCRIPTION_KEY` bzw. `COMPUTER_VISION_ENDPOINT`) [Umgebungsvariablen erstellt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) haben.

Erstellen Sie eine Funktion vom Typ `main`, und fügen Sie ihr den folgenden Code hinzu, um einen Client mit Ihrem Endpunkt und Schlüssel zu instanziieren:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#read-printed-and-handwritten-text) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client&product=computer-vision&page=go-sdk)



## <a name="read-printed-and-handwritten-text"></a>Lesen von gedrucktem und handschriftlichem Text

Der OCR-Dienst kann sichtbaren Text in einem Bild lesen und in eine Zeichenfolge konvertieren. Der Code in diesem Abschnitt definiert die Funktion `RecognizeTextReadAPIRemoteImage`. Diese verwendet das Clientobjekt, um gedruckten oder handgeschriebenen Text im Bild zu erkennen und zu extrahieren.

Fügen Sie der Funktion `main` den Beispielbildverweis und den Funktionsaufruf hinzu.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Sie können auch Text aus einem lokalen Bild extrahieren. Sehen Sie sich die [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient)-Methoden an, etwa **BatchReadFileInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) Szenarien zu lokalen Bildern.

### <a name="call-the-read-api"></a>Aufrufen der Lese-API

Definieren Sie die neue Funktion zum Lesen von Text (`RecognizeTextReadAPIRemoteImage`). Fügen Sie den folgenden Code hinzu. Der Code ruft die Methode **BatchReadFile** für das angegebene Bild auf. Diese Methode gibt eine Vorgangs-ID zurück und startet einen asynchronen Prozess, um den Inhalt des Bilds zu lesen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Abrufen der Leseergebnisse

Rufen Sie als Nächstes die Vorgangs-ID ab, die durch Aufrufen von **BatchReadFile** zurückgegeben wurde, und verwenden Sie sie mit der Methode **GetReadOperationResult**, um die Vorgangsergebnisse vom Dienst abzufragen. Der folgende Code überprüft den Vorgang in Intervallen von einer Sekunde, bis die Ergebnisse zurückgegeben werden. Anschließend werden die extrahierten Textdaten in der Konsole ausgegeben.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Anzeigen der Leseergebnisse

Fügen Sie den folgenden Code hinzu, um die abgerufenen Textdaten zu analysieren und anzuzeigen, und schließen Sie die Funktionsdefinition ab:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Ich habe Text gelesen.](?success=read-printed-handwritten-text#run-the-application) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text&product=computer-vision&page=go-sdk)

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `go run` aus dem Anwendungsverzeichnis aus.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Ich habe die Anwendung ausgeführt.](?success=run-the-application#clean-up-resources) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application&product=computer-vision&page=go-sdk)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ich habe die Ressourcen bereinigt.](?success=clean-up-resources#next-steps) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources&product=computer-vision&page=go-sdk)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie die OCR-Clientbibliothek installieren und die Lese-API verwenden. Informieren Sie sich als Nächstes ausführlicher über die Funktionen der Lese-API.

> [!div class="nextstepaction"]
>[Aufrufen der Lese-API](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR-Übersicht](../../overview-ocr.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

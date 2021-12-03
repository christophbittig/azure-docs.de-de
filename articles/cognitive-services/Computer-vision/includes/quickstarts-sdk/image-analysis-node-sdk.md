---
title: 'Schnellstart: Bildanalyse-Client Bibliothek für Node.js'
description: Beginnen Sie mit dieser Schnellstartanleitung mit dem Einsatz der Bild-Analyse Clientbibliothek für Node.js
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: b3a20beb6e4df422e071a2035e642d26d08dfea3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520851"
---
<a name="HOLTop"></a>

Verwenden Sie die Client Bibliothek für die Bildanalyse, um ein Bild für Tags, Textbeschreibungen, Gesichter, nicht jugendfreie Inhalte und vieles mehr zu analysieren.

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

Erstellen Sie die neue Datei *index.js*, und öffnen Sie sie in einem Text-Editor. Fügen Sie die folgenden import-Anweisungen hinzu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imports)]

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Ressource für maschinelles Sehen erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

> [!div class="nextstepaction"]
> [Ich habe die Variablen eingerichtet.](?success=set-up-client#object-model) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client&product=computer-vision&page=image-analysis-node-sdk)

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen des Node.js SDK zur Bild-Analyse.

|name|BESCHREIBUNG|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Diese Klasse wird für alle Funktionen der Maschinelles Sehen-API benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie für die meisten Bildvorgänge.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Diese Enumeration definiert die verschiedenen Typen der Bildanalyse, die bei einem standardmäßigen Analysevorgang ausgeführt werden können. Sie geben abhängig von Ihren Anforderungen verschiedene **VisualFeatureTypes**-Werte an. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeschnipsel zeigen Ihnen, wie Sie die folgenden Aufgaben mit der Bild-Analyse Client-Bibliothek für Node.js durchführen können:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Analysieren eines Bilds](#analyze-an-image)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients


Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials)-Objekt mit Ihrem Schlüssel und Endpunkt, und verwenden Sie es zum Erstellen eines [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-Objekts.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_client)]

Definieren Sie anschließend die Funktion `computerVision`, und deklarieren Sie eine asynchrone Serie mit einer primären Funktion und einer Rückruffunktion. Sie fügen Ihren Schnellstartcode der primären Funktion hinzu und rufen unten im Skript `computerVision` auf. Der restliche Code in dieser Schnellstartanleitung wird in die `computerVision`-Funktion eingefügt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Ich habe den Client authentifiziert.](?success=authenticate-client#analyze-an-image) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client&product=computer-vision&page=image-analysis-node-sdk)

## <a name="analyze-an-image"></a>Analysieren von Bildern

Mit dem Code in diesem Abschnitt werden Remotebilder analysiert, um verschiedene visuelle Merkmale zu extrahieren. Sie können diese Vorgänge im Rahmen der **analyzeImage**-Methode des Clientobjekts durchführen, oder Sie können sie mit individuellen Methoden aufrufen. Ausführlichere Informationen finden Sie in der [Referenzdokumentation](/javascript/api/@azure/cognitiveservices-computervision/).

> [!NOTE]
> Sie können auch ein lokales Bild analysieren. Sehen Sie sich die [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-Methoden an, etwa **describeImageInStream**. Alternativ finden Sie im Beispielcode auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js) Szenarien zu lokalen Bildern.

### <a name="get-image-description"></a>Abrufen der Bildbeschreibung

Mit dem folgenden Code wird die Liste der generierten Beschriftungen für das Bild abgerufen. Weitere Informationen finden Sie unter [Beschreiben von Bildern in lesbarer Sprache](../../concept-describing-images.md).

Definieren Sie zuerst die URL eines zu analysierenden Images:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_describe_image)]

Fügen Sie anschließend den folgenden Code hinzu, um die Bildbeschreibung abzurufen und in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Abrufen der Bildkategorie

Mit dem folgenden Code wird die erkannte Kategorie des Bilds abgerufen. Weitere Informationen finden Sie unter [Kategorisieren von Bildern nach Schlüsselinhalt](../../concept-categorizing-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_categories)]

Definieren Sie die Hilfsfunktion `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Abrufen von Bildtags

Mit dem folgenden Code werden die erkannten Tags im Bild abgerufen. Weitere Informationen finden Sie unter [Inhaltstags](../../concept-tagging-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_tags)]

Definieren Sie die Hilfsfunktion `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Erkennen von Objekten

Der folgende Code erkennt alltägliche Objekte im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Objekterkennung](../../concept-object-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_objects)]

Definieren Sie die Hilfsfunktion `formatRectObjects`, um die oberen, linken, unteren und rechten Koordinaten zusammen mit der Breite und Höhe zurückzugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Erkennen von Marken

Der folgende Code erkennt Marken und Firmenlogos im Bild und gibt sie in der Konsole aus. Weitere Informationen finden Sie unter [Markenerkennung](../../concept-brand-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Erkennen von Gesichtern

Mit dem folgenden Code werden die erkannten Gesichter im Bild mit den Rechteckkoordinaten und ausgewählten Gesichtsattributen zurückgegeben. Weitere Informationen finden Sie unter [Gesichtserkennung mit maschinellem Sehen](../../concept-detecting-faces.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_faces)]

Definieren Sie die Hilfsfunktion `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Erkennen von nicht jugendfreien, freizügigen oder gewaltverherrlichenden Inhalten

Der folgende Code gibt an, ob nicht jugendfreie Inhalte im Bild erkannt wurden. Weitere Informationen finden Sie unter [Erkennen von nicht jugendfreien und freizügigen Inhalten](../../concept-detecting-adult-content.md).

Definieren Sie die URL des zu verwendenden Bilds:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_adult_image)]

Fügen Sie anschließend den folgenden Code hinzu, um die Erkennung von nicht jugendfreien Inhalten zu aktivieren und die Ergebnisse in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Abrufen des Farbschemas für ein Bild

Mit dem folgenden Code werden die die erkannten Farbattribute im Bild ausgegeben, etwa die vorherrschenden Farben und die Akzentfarbe. Weitere Informationen finden Sie unter [Erkennen von Farbschemas auf Bildern](../../concept-detecting-color-schemes.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_colors)]

Definieren Sie die Hilfsfunktion `printColorScheme`, um die Details des Farbschemas in der Konsole auszugeben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Abrufen des domänenspezifischen Inhalts

Bild-Analyse kann spezialisierte Modelle zur weiteren Analyse von Bildern verwenden. Weitere Informationen finden Sie unter [Erkennen domänenspezifischer Inhalte](../../concept-detecting-domain-content.md).

Definieren Sie zuerst die URL eines zu analysierenden Images:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_domain_image)]

Der folgende Code analysiert Daten zu erkannten Sehenswürdigkeiten im Bild:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_landmarks)]

Definieren Sie die Hilfsfunktion `formatRectDomain`, um die Standortdaten der erkannten Orientierungspunkte zu analysieren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Abrufen des Bildtyps

Der folgende Code gibt Informationen über den Typ des Bilds aus, und zwar unabhängig davon, ob es sich um ClipArt oder eine linienbasierte Zeichnung handelt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imagetype)]

Definieren Sie die Hilfsfunktion `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ImageAnalysisQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Ich habe ein Bild analysiert.](?success=analyze-image#run-the-application) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image&product=computer-vision&page=image-analysis-node-sdk)



## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Ich habe die Anwendung ausgeführt.](?success=run-the-application#clean-up-resources) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application&product=computer-vision&page=image-analysis-node-sdk)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ich habe die Ressourcen bereinigt.](?success=clean-up-resources#next-steps) [Bei mir ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources&product=computer-vision&page=image-analysis-node-sdk)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Bildanalyse-Clientbibliothek installieren und grundlegende Bildanalyseaufrufe durchführen. Als Nächstes erfahren Sie mehr über die Funktionen der Analyse-API.

> [!div class="nextstepaction"]
>[Aufrufen der Bildanalyse-API](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Übersicht über die Bildanalyse](../../overview-image-analysis.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ImageAnalysisQuickstart.js).


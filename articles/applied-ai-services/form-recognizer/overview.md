---
title: Was ist die Azure-Formularerkennung? (aktualisiert)
titleSuffix: Azure Applied AI Services
description: Der Azure-Formularerkennungsdienst ermöglicht das Erkennen und Extrahieren von Schlüssel-Wert-Paaren und Tabellendaten aus Ihren Formulardokumenten sowie das Extrahieren wichtiger Informationen aus Verkaufsbelegen und Visitenkarten.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
keywords: automatisierte Datenverarbeitung, Dokumentverarbeitung, automatisierte Dateneingabe, Formularverarbeitung
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9c22bef6236008c772c39a9fea4f4673923b070
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843098"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>Was ist die Azure-Formularerkennung?

Die Azure-Formularerkennung ist ein cloudbasierter [Azure Applied AI-Dienst](../../applied-ai-services/index.yml), der Machine Learning-Modelle verwendet, um Formularfelder, Text und Tabellen aus Ihren Dokumenten zu extrahieren und zu analysieren. Die Formularerkennung analysiert Ihre Formulare und Dokumente, extrahiert Text und Daten, ordnet Feldbeziehungen als Schlüssel-Wert-Paare zu und gibt eine strukturierte JSON-Ausgabe zurück. Sie können schnell präzise und auf Ihre spezifischen Inhalte zugeschnittene Ergebnisse erzielen, ohne dass komplizierte manuelle Eingriffe oder umfangreiche Data Science-Kenntnisse erforderlich sind. Verwenden Sie die Formularerkennung, um Ihre Datenverarbeitung in Anwendungen und Workflows zu automatisieren, datengesteuerte Strategien zu verbessern und die Suchfunktionen für Dokumente zu erweitern.

Die Formularerkennung kann folgende Dokumentdaten leicht identifizieren, extrahieren und analysieren:

* Tabellenstruktur und Inhalt
* Formularelemente und Feldwerte
* Getippten und handschriftlichen alphanumerischen Text
* Beziehungen zwischen Elementen
* Schlüssel-Werte-Paare
* Elementposition mit Begrenzungsrahmenkoordinaten

## <a name="form-recognizer-features-and-development-options"></a>Funktionen und Entwicklungsoptionen der Formularerkennung

### <a name="form-recognizer-ga-v21"></a>[Formularerkennung GA (v2.1)](#tab/v2-1)

Von der Azure-Formularerkennung v2.1 werden die unten angegebenen Features unterstützt. Verwenden Sie die Links in der Tabelle, um mehr über die einzelnen Funktionen zu erfahren und die API-Referenzen zu durchsuchen.

| Funktion | BESCHREIBUNG | Entwicklungsoptionen |
|----------|--------------|-------------------------|
|[**Layout-API**](concept-layout.md) | Extrahieren und Analysieren von Text, Auswahlzeichen und Tabellenstrukturen sowie deren Begrenzungsrahmenkoordinaten aus Formularen und Dokumenten | <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#analyze-layout)</li><li>[**REST-API**](quickstarts/get-started-sdk-rest-api.md#try-it-layout-model)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Benutzerdefiniertes Modell**](concept-custom.md) | Extrahieren und Analysieren von Daten aus spezifischen Formularen und Dokumenten für unterschiedliche Geschäftsdaten und Anwendungsfälle| <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#train-a-custom-form-model)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md)</li><li>[**Clientbibliothek/SDK**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Rechnungsmodell**](concept-invoice.md) | Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Verkaufsrechnungen | <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**REST-API**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Belegmodell**](concept-receipt.md) | Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Verkaufsbelegen| <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**REST-API**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**Clientbibliothek/SDK**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Ausweisdokumentmodell**](concept-id-document.md) | Automatisierte Datenverarbeitung und Extraktion wichtiger Informationen aus US-Führerscheinen und internationalen Pässen| <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**REST-API**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**Clientbibliothek/SDK**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modell für Visitenkarten**](concept-business-card.md) | Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Visitenkarten| <ul><li>[**Formularerkennungstool für die Bezeichnung**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**REST-API**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**Clientbibliothek/SDK**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[Formularerkennung: Vorschauversion (v3.0)](#tab/v3-0)

Die folgenden Funktionen und Entwicklungsoptionen werden vom Formularerkennungsdienst v3.0 unterstützt. Verwenden Sie die Links in der Tabelle, um mehr über die einzelnen Funktionen zu erfahren und die API-Referenzen zu durchsuchen.

| Funktion | BESCHREIBUNG | Entwicklungsoptionen |
|----------|--------------|-------------------------|
|[🆕 **Allgemeines Dokumentmodell**](concept-general-document.md)|Extrahieren von Text, Tabellen, Strukturen, Schlüssel-Wert-Paaren und benannten Entitäten.|<ul ><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md#try-it-general-document-model)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-general-document-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-general-document-model)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md#try-it-general-document-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-general-document-model)</li></ul> |
|[**Layoutmodell**](concept-layout.md) | Extrahieren von Text, Auswahlmarkierungen und Tabellenstrukturen sowie der zugehörigen Begrenzungsrahmenkoordinaten aus Formularen und Dokumenten.</br></br> Die Layout-API wurde auf ein vordefiniertes Modell aktualisiert. | <ul><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#layout)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md#try-it-layout-model)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-layout-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-layout-model)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md#try-it-layout-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-layout-model)</li></ul>|
|[**Benutzerdefiniertes Modell (aktualisiert)**](concept-custom.md) | Extrahieren und Analysieren von Daten aus spezifischen Formularen und Dokumenten für unterschiedliche Geschäftsdaten und Anwendungsfälle.</br></br>Die benutzerdefinierte Modell-API v3.0 unterstützt die **Erkennung von Signaturen für benutzerdefinierte Formulare**.</li></ul>| <ul><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|
|[**Rechnungsmodell**](concept-invoice.md) | Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Verkaufsrechnungen | <ul><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**Belegmodell (aktualisiert)**](concept-receipt.md) | Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Verkaufsbelegen</br></br>Das Modell „Beleg“ v3.0 unterstützt die Verarbeitung von **einseitigen Hotelbelegen**.| <ul><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**ID-Dokumentmodell (aktualisiert)**](concept-id-document.md) |Automatisierte Datenverarbeitung und Extraktion wichtiger Informationen aus US-Führerscheinen und internationalen Pässen</br></br>Die vordefinierte API für ID-Dokumente unterstützt das **Extrahieren von Vermerken, Einschränkungen und Fahrzeugklassifizierungen aus US-Führerscheinen**. |<ul><li> [**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**Modell für Visitenkarten**](concept-business-card.md) |Automatisierte Datenverarbeitung und Extraktion von Schlüsselinformationen aus Visitenkarten| <ul><li>[**Formularerkennung Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**REST-API**](quickstarts/try-v3-rest-api.md)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|

---

## <a name="which-form-recognizer-feature-should-i-use"></a>Welches Feature der Formularerkennung sollte ich verwenden?

Anhand der Informationen in diesem Abschnitt können Sie entscheiden, welches Features der Formularerkennung Sie für Ihre Anwendung verwenden sollten.

| Welche Art von Dokument möchten Sie analysieren?| Wie ist das Dokument formatiert? | Für Sie am besten geeignete Lösung |
| -----------------|-------------------| ----------|
|<ul><li>**Rechnung**</li><li>**Rechnung**</li><li>**Visitenkarte**</li></ul>| Ist der Text Ihrer Rechnung, Quittung oder Visitenkarte englisch? | <ul><li>Verwenden Sie bei **Ja** das Modell [**Rechnung**](concept-invoice.md), [**Beleg**](concept-receipt.md), oder [**Visitenkarte**](concept-business-card.md).</li><li>Verwenden Sie bei **Nein** das Modell [**Layout**](concept-layout.md) oder [**Allgemeines Dokument (Vorschau)**](concept-general-document.md).</li></ul>|
|<ul><li>**ID-Dokument**</li></ul>| Ist Ihr ID-Dokument ein US-Führerschein oder ein internationaler Pass?| <ul><li>Verwenden Sie bei **Ja** das Modell [**Ausweisdokument**](concept-id-document.md).</li><li>Verwenden Sie bei **Nein** das Modell [**Layout**](concept-layout.md) oder [**Allgemeines Dokument (Vorschau)**](concept-general-document.md).</li></ul>|
 |<ul><li>**Formular** oder **Dokument**</li></ul>| Ist Ihr Formular oder Dokument ein Branchenstandardformat, das häufig in Ihrem Unternehmen oder Ihrer Branche verwendet wird?| <ul><li>Verwenden Sie bei **Ja** das Modell [**Layout**](concept-layout.md) oder [**Allgemeines Dokument (Vorschau)**](concept-general-document.md).</li><li>Bei **Nein** können Sie mit [**Trainieren und Erstellen eines benutzerdefinierten Modells**](quickstarts/try-sample-label-tool.md#train-a-custom-form-model) fortfahren.

## <a name="how-to-use-form-recognizer-documentation"></a>Verwenden der Dokumentation zur Azure-Formularerkennung

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* Die Artikel zu [**Konzepten**](concept-layout.md) enthalten ausführliche Erläuterungen der Dienstfunktionen und -features.
* [**Schnellstarts**](quickstarts/try-sdk-rest-api.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](how-to-guides/try-sdk-rest-api.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* [**Tutorials**](tutorial-ai-builder.md) sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

 Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Formularerkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie auf unserer Seite [Daten, Datenschutz und Sicherheit für die Formularerkennung](/legal/cognitive-services/form-recognizer/fr-data-privacy-security).

## <a name="next-steps"></a>Nächste Schritte

### <a name="form-recognizer-v21"></a>[Formularerkennung v2.1](#tab/v2-1)

> [!div class="checklist"]
>
> * Testen Sie unser [**Onlinetool für die Beschriftung von Beispielen**](https://aka.ms/fott-2.1-ga/).
> * Führen Sie unseren [**Schnellstart zur Clientbibliothek/REST-API**](./quickstarts/try-sdk-rest-api.md) durch, um mit dem Extrahieren von Daten aus Dokumenten zu beginnen. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.
> * Sehen Sie sich die [**Referenzdokumentation zur REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) an, um mehr zu erfahren.
> * Wenn Sie mit einer früheren Version der API vertraut sind, finden Sie im Artikel [**Neuerungen**](./whats-new.md) weitere Informationen zu den aktuellen Änderungen.

### <a name="form-recognizer-v30"></a>[Formularerkennung v3.0](#tab/v3-0)

> [!div class="checklist"]
>
> * [**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com) ausprobieren
> * Sehen Sie sich die [**Referenzdokumentation zur REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) an, um mehr zu erfahren.
> * Wenn Sie mit einer früheren Version der API vertraut sind, finden Sie im Artikel [**Neuerungen**](./whats-new.md) weitere Informationen zu den aktuellen Änderungen.

---

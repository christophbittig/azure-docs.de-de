---
title: Was ist die Azure-Formularerkennung?
titleSuffix: Azure Applied AI Services
description: Der Azure-Formularerkennungsdienst ermöglicht das Erkennen und Extrahieren von Schlüssel-Wert-Paaren und Tabellendaten aus Ihren Formulardokumenten sowie das Extrahieren wichtiger Informationen aus Verkaufsbelegen und Visitenkarten.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatisierte Datenverarbeitung, Dokumentverarbeitung, automatisierte Dateneingabe, Formularverarbeitung
ms.openlocfilehash: 2231832f87b2caf085f5d5278a2b291cda25b606
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327223"
---
# <a name="what-is-azure-form-recognizer"></a>Was ist die Azure-Formularerkennung?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Die Azure-Formularerkennung ist Teil der [Azure Applied AI Services](../../applied-ai-services/index.yml) und erlaubt Ihnen, unter Verwendung der Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung zu entwickeln. Identifizieren und extrahieren Sie Text, Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellen und die Struktur aus Ihren Dokumenten. Der Dienst gibt strukturierte Daten aus, die unter anderem die Beziehungen in der ursprünglichen Datei sowie Begrenzungsrahmen und Konfidenz enthalten. Sie können schnell präzise und auf Ihre spezifischen Inhalte zugeschnittene Ergebnisse erzielen, ohne dass komplizierte manuelle Eingriffe oder umfangreiche Data Science-Kenntnisse erforderlich sind. Verwenden Sie die Formularerkennung, um Dateneingaben in Ihren Anwendungen zu automatisieren und Ihre Dokumentsuchfunktionen zu erweitern.

Die Formularerkennung setzt sich aus benutzerdefinierten Dokumentverarbeitungsmodellen, vordefinierten Modellen für Rechnungen, Belege, Ausweise und Visitenkarten und dem Layoutmodell zusammen. Sie können Formularerkennungsmodelle mithilfe einer REST-API oder über Clientbibliothek-SDKs aufrufen, um die Komplexität zu reduzieren und sie in Ihren Workflow oder Ihre Anwendung zu integrieren.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* Die Artikel zu [**Konzepten**](concept-layout.md) enthalten ausführliche Erläuterungen der Dienstfunktionen und -features.
* [**Schnellstarts**](quickstarts/client-library.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](build-training-data-set.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* [**Tutorials**](tutorial-ai-builder.md) sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="form-recognizer-features"></a>Funktionen der Formularerkennung

Mit der Formularerkennung können Sie mithilfe der folgenden Features problemlos Dokumentdaten extrahieren und analysieren:

### <a name="layout"></a>[Layout](concept-layout.md)

Dient zum Extrahieren von Text, Auswahlmarkierungen und Tabellenstrukturen sowie der zugehörigen Begrenzungsrahmenkoordinaten aus Dokumenten.

Die Formularerkennung kann Text, Auswahlmarkierungen und die Tabellenstruktur (die Zeilen- und Spaltennummern, die dem Text zugeordnet sind) aus Dokumenten extrahieren. Hierzu kommen eine hochauflösende optische Zeichenerkennung (Optical Character Recognition, OCR) und ein erweitertes Deep Learning-Modell zum Einsatz.

:::image type="content" source="./media/tables-example.jpg" alt-text="Beispiel für Tabellen" lightbox="./media/tables-example.jpg":::

### <a name="custom-models"></a>[Benutzerdefinierte Modelle](concept-custom.md)

Dienen zum Extrahieren von Text, Schlüssel-Wert-Paaren, Auswahlmarkierungen und Tabellendaten aus Formularen. Diese Modelle werden mit Ihren eigenen Daten trainiert, sodass Sie auf Ihre Formulare zugeschnitten sind.

Benutzerdefinierte Formularerkennungsmodelle werden mit Ihren eigenen Daten trainiert, und Sie benötigen zu Beginn nur fünf Beispieleingabeformulare. Ein trainiertes Dokumentverarbeitungsmodell kann strukturierte Daten ausgeben, die die Beziehungen in der Originaldatei enthalten. Nachdem das Modell trainiert wurde, können Sie es testen, neu trainieren und schließlich verwenden, um Daten aus weiteren Formularen zuverlässig nach Ihren Bedürfnissen zu extrahieren.

Beim Trainieren von benutzerdefinierten Modellen haben Sie die folgenden Optionen: Training mit beschrifteten Daten und ohne beschriftete Daten.

#### <a name="train-without-labels"></a>Trainieren ohne Beschriftungen

Die Formularerkennung verwendet nicht überwachtes Lernen, um das Layout und die Beziehungen zwischen Feldern und Einträgen in Ihren Formularen nachzuvollziehen. Anhand der übermittelten Eingabeformulare gruppiert der Algorithmus die Formulare nach Typ, erkennt, welche Schlüssel und Tabellen vorhanden sind, und ordnet Schlüsseln Werte und Tabellen Einträge zu. Für das Trainieren ohne Beschriftungen ist keine manuelle Datenbeschriftung oder intensive Codierung und Wartung erforderlich, und Sie sollten diese Methode zuerst testen.

Tipps zum Sammeln von Trainingsdokumenten finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](./build-training-data-set.md).

#### <a name="train-with-labels"></a>Trainieren mit Beschriftungen

Wenn Sie mit beschrifteten Daten trainieren, extrahiert das Modell relevante Werte mit überwachtem Lernen, wobei die von Ihnen bereitgestellten beschrifteten Formulare verwendet werden. Beschriftete Daten führen zu Modellen mit besserer Leistung und können Modelle hervorbringen, die mit komplexen Formularen oder Formularen arbeiten, die Werte ohne Schlüssel enthalten.

Die Formularerkennung verwendet die [Layout](#layout)-API, um die erwarteten Größen und Positionen von gedruckten und handschriftlichen Textelementen zu erlernen und Tabellen zu extrahieren. Anschließend werden benutzerdefinierte Beschriftungen verwendet, um die Schlüssel-Wert-Zuordnungen und Tabellen in den Dokumenten zu erlernen. Wir empfehlen, fünf manuell beschriftete Formulare gleichen Typs (gleiche Struktur) zu verwenden, um mit dem Trainieren eines neuen Modells zu beginnen, und weitere beschriftete Daten nach Bedarf hinzuzufügen, um die Modellgenauigkeit zu verbessern. Die Formularerkennung ermöglicht das Trainieren eines Modells zum Extrahieren von Schlüssel-Wert-Paaren und Tabellen mithilfe der Funktionen für beaufsichtigtes Lernen.

[Trainieren eines Formularerkennungsmodells mit Beschriftungen mithilfe des Tools für die Beschriftung von Beispielen](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


### <a name="prebuilt-models"></a>Vordefinierte Modelle

 Die Formularerkennung umfasst auch vordefinierte Modelle für die automatisierte Datenverarbeitung von Belegen, Visitenkarten, Rechnungen und Ausweisdokumenten.

### <a name="receipts"></a>[Receipts](concept-receipts.md)

Das vordefinierte Belegmodell wird zum Lesen englischsprachiger Verkaufsbelege aus Australien, Kanada, dem Vereinigten Königreich, Indien und den USA verwendet – des Typs, der von Restaurants, Tankstellen, Einzelhandel usw. verwendet wird. Dieses Modell extrahiert wichtige Informationen wie Zeitpunkt und Datum der Transaktion, Händlerinformationen, Steuer- und Summenbeträge, Einzelposten und mehr. Darüber hinaus wird das vorgefertigte Belegmodell dazu trainiert, den gesamten Text eines Belegs zu analysieren und zurückzugeben.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Beispielbeleg" lightbox="./media/overview-receipt.jpg":::

### <a name="business-cards"></a>[Visitenkarten](concept-business-cards.md)

Mit dem Visitenkartenmodell können Sie Informationen wie Name, Position, Adresse, E-Mail-Adresse, Unternehmen und Telefonnummern einer Person aus englischsprachigen Visitenkarten extrahieren.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Beispielvisitenkarte" lightbox="./media/overview-business-card.jpg":::

### <a name="invoices"></a>[Invoices](concept-invoices.md)

Das vordefinierte Rechnungsmodell extrahiert Daten aus Rechnungen in verschiedenen Formaten und gibt strukturierte Daten zurück. Dieses Modell extrahiert wichtige Informationen wie Rechnungs-ID, Kundendetails, Anbieterdetails, Lieferadresse, Rechnungsadresse, Summe, Steuer, Zwischensumme, Positionen und vieles mehr. Darüber hinaus wird das vordefinierte Rechnungsmodell darauf trainiert, den gesamten Text und sämtliche Tabellen auf der Rechnung zu analysieren und zurückzugeben.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Beispielrechnung" lightbox="./media/overview-invoices.jpg":::

### <a name="identity-documents"></a>[Identitätsdokumente](concept-identification-cards.md)

Das Modell für Ausweisdokumente ermöglicht es Ihnen, wichtige Informationen aus internationalen Ausweisen und US-amerikanischen Führerscheinen zu extrahieren. Es extrahiert Daten wie Dokument-ID, Geburtsdatum, Ablaufdatum, Name, Land, Region, die maschinenlesbare Zone usw.

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="Beispielausweis" lightbox="./media/overview-id.jpg":::

## <a name="get-started"></a>Erste Schritte

Verwenden Sie das Beispieltool für die Formularerkennung, um die vordefinierten Modelle für das Layout auszuprobieren, und trainieren Sie ein benutzerdefiniertes Modell für Ihre Dokumente: Zum Ausprobieren des Formularerkennungsdiensts benötigen Sie ein Azure-Abonnement (kann [**hier**](https://azure.microsoft.com/free/cognitive-services) kostenlos erstellt werden), einen Endpunkt für eine [**Formularerkennungsressource**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel.

>
> [!div class="nextstepaction"]
> [Formularerkennung ausprobieren](https://aka.ms/fott-2.1-ga/)
>

Befolgen Sie die Anweisungen im [Schnellstart zur Clientbibliothek/REST-API ](./quickstarts/client-library.md), um mit dem Extrahieren von Daten aus Dokumenten zu beginnen. Sie sollten den kostenlosen Dienst nutzen, wenn Sie die Technologie erlernen. Bedenken Sie, dass die Anzahl der kostenlosen Seiten auf 500 pro Monat beschränkt ist.

Sehen Sie sich die [Referenzdokumentation zur REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) an, um mehr zu erfahren. Wenn Sie mit einer früheren Version der API vertraut sind, finden Sie im Artikel [Neuerungen](./whats-new.md) weitere Informationen zu den aktuellen Änderungen.

## <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="service-availability-and-redundancy"></a>Dienstverfügbarkeit und Redundanz

### <a name="is-form-recognizer-service-zone-resilient"></a>Ist der Formularerkennungsdienst zonenresilient?

Ja. Der Formularerkennungsdienst ist standardmäßig zonenresilient.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Wie konfiguriere ich den Formularerkennungsdienst für Zonenresilienz?

Es ist keine Kundenkonfiguration erforderlich, um Zonenresilienz zu ermöglichen. Zonenresilienz für Formularerkennungsressourcen ist standardmäßig verfügbar und wird vom Dienst selbst verwaltet.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

* Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Formularerkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

* Die Formularerkennung speichert und verarbeitet keine Kundendaten außerhalb der Region, in der der Kunde die Dienstinstanz bereitstellt.

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie unser Onlinetool und den Schnellstart aus, um mehr über den Formularerkennungsdienst zu erfahren.

* [**Tool für die Formularerkennung**](https://aka.ms/fott-2.1-ga)
* [**Schnellstart: Verwenden der Clientbibliothek oder REST-API für die Formularerkennung**](quickstarts/client-library.md)
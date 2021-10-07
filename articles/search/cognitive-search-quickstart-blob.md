---
title: 'Schnellstart: Erstellen eines Skillsets im Azure-Portal'
titleSuffix: Azure Cognitive Search
description: In diesem Schnellstart über das Portal verwenden Sie den Datenimport-Assistenten, um einer Indizierungspipeline in Azure Cognitive Search kognitive Qualifikationen hinzuzufügen. Zu diesen Qualifikationen zählen die optische Zeichenerkennung (Optical Character Recognition, OCR) und die Verarbeitung natürlicher Sprache.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/17/2021
ms.openlocfilehash: d39ee872024cc3eb301e59d45e276843bb886340
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210552"
---
# <a name="quickstart-create-an-azure-cognitive-search-skillset-in-the-azure-portal"></a>Schnellstart: Erstellen eines Skillsets für Azure Cognitive Search über das Azure-Portal

In dieser Schnellstartanleitung wird die Skillset-Unterstützung im Portal veranschaulicht und gezeigt, wie mithilfe der optischen Zeichenerkennung (Optical Character Recognition, OCR) und der Entitätserkennung durchsuchbare Textinhalte aus Bildern und Anwendungsdateien erstellt werden können.

Zur Vorbereitung erstellen Sie einige Ressourcen und laden Beispielbilder und Anwendungsinhaltsdateien hoch. Nach Abschluss der Einrichtung wird im Azure-Portal der **Datenimport-Assistent** ausgeführt, um alles miteinander zu verknüpfen. Am Ende verfügen Sie über einen durchsuchbaren Index mit Daten, die mittels KI-Verarbeitung erstellt wurden und im Portal mithilfe des [Suchexplorers](search-explorer.md) abgefragt werden können.

Beginnen Sie lieber mit Code? Lesen Sie stattdessen die Informationen unter [Tutorial: Verwenden von REST und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob.md) oder [Tutorial: Durch KI generierter durchsuchbarer Inhalt aus Azure-Blobs mit dem .NET SDK](cognitive-search-tutorial-blob-dotnet.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen können, müssen die folgenden Voraussetzungen erfüllt werden:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Ein Azure Storage-Konto mit [Blob Storage](../storage/blobs/index.yml).

> [!NOTE]
> In diesem Schnellstart wird außerdem [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="set-up-your-data"></a>Einrichten Ihrer Daten

In den folgenden Schritten richten Sie einen Blobcontainer in Azure Storage ein, um heterogene Inhaltsdateien zu speichern.

1. [Laden Sie die Beispieldaten herunter](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), die aus einem kleinen Satz Dateien verschiedener Typen bestehen. Entzippen Sie die Dateien.

1. [Erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Es muss sich in der gleichen Region wie Azure Cognitive Search befinden, um Bandbreitengebühren zu vermeiden. 

   + Wählen Sie „StorageV2 (universell V2)“ aus.

1. Öffnen Sie die Seiten für Blobdienste, und erstellen Sie einen Container. Sie können die standardmäßige öffentliche Zugriffsebene verwenden. 

1. Klicken Sie im Container auf **Hochladen**, um die im ersten Schritt heruntergeladenen Beispieldateien hochzuladen. Ihnen steht ein breites Spektrum an Inhaltstypen zur Verfügung – einschließlich Bild- und Anwendungsdateien, die in ihrem nativen Format nicht für die Volltextsuche geeignet sind.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Quelldateien in Azure Blob Storage" border="false":::

Nun können Sie zum Datenimport-Assistenten wechseln.

## <a name="run-the-import-data-wizard"></a>Ausführen des Datenimport-Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Navigieren Sie zu Ihrem Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Seite „Übersicht“ auf der Befehlsleiste auf **Daten importieren**, um die kognitive Anreicherung in vier Schritten einzurichten.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

### <a name="step-1---create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

1. Wählen Sie unter **Verbindung mit Ihren Daten herstellen** die Option **Azure Blob Storage** sowie das erstellte Storage-Konto und den erstellten Container aus. Geben Sie der Datenquelle einen Namen, und verwenden Sie für alles andere die Standardwerte. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Azure-Blobkonfiguration" border="false":::

    Wechseln Sie zur nächsten Seite.

### <a name="step-2---add-cognitive-skills"></a>Schritt 2: Hinzufügen von kognitiven Qualifikationen

Konfigurieren Sie als nächstes die KI-Anreicherung, um OCR, Bildanalyse und Verarbeitung in natürlicher Sprache aufzurufen. 

1. In dieser Schnellstartanleitung verwenden wir die Cognitive Services-Ressource vom Typ **Free**. Die Beispieldaten umfassen 14 Dateien. Das kostenlose Kontingent von 20 Transaktionen für Cognitive Services ist somit für diese Schnellstartanleitung ausreichend. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Anfügen von Cognitive Services: Anfügen eines Basisdiensts" border="false":::

1. Erweitern Sie **Anreicherungen hinzufügen**, und wählen Sie vier Auswahlmöglichkeiten. 

   Aktivieren Sie OCR, um der Assistentenseite Bildanalysequalifikationen hinzuzufügen.

   Legen Sie die Granularität auf Seiten fest, um Text in kleinere Blöcke aufzuteilen. Mehrere Textqualifikationen sind auf Eingaben von 5 KB beschränkt.

   Wählen Sie die Entitätserkennung (Personen, Organisationen, Orte) und Bildanalysequalifikationen aus.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Anfügen von Cognitive Services: Auswählen von Diensten für das Skillset" border="false":::

   Wechseln Sie zur nächsten Seite.

### <a name="step-3---configure-the-index"></a>Schritt 3: Konfigurieren des Indexes

Ein Index enthält Ihre durchsuchbaren Inhalte, und der **Datenimport-Assistent** kann in der Regel die Datenquelle untersuchen und das Schema für Sie erstellen. Überprüfen Sie in diesem Schritt das generierte Schema, und überarbeiten Sie ggf. die Einstellungen. Im Anschluss sehen Sie das für das Blobdataset der Demo erstellte Standardschema.

Für diesen Schnellstart legt der Assistent sinnvolle Standardwerte fest:  

+ Standardfelder basieren auf Eigenschaften für vorhandene Blobs und neue Felder, um die Anreicherungsausgabe enthalten zu können (z. B. `people`, `organizations`, `locations`). Datentypen werden aus Metadaten und Datenstichproben abgeleitet.

+ Der Standarddokumentschlüssel ist *metadata_storage_path* (da dieses Feld eindeutige Werte enthält).

+ Standardattribute sind **Abrufbar** und **Durchsuchbar**. **Durchsuchbar** ermöglicht die Volltextsuche in einem Feld. **Abrufbar** bedeutet, dass Feldwerte in Ergebnissen zurückgegeben werden können. Der Assistent geht davon aus, dass diese Felder abrufbar und durchsuchbar sein sollen, da Sie sie über eine Qualifikationsgruppe erstellt haben.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Indexfelder" border="false":::

Beachten Sie, dass das Attribut **Abrufbar** für das Feld `content` durchgestrichen und mit einem Fragezeichen versehen ist. Bei textlastigen Blobdokumenten enthält das Feld `content` den Großteil der Datei, der mehrere tausende Zeilen umfassen kann. Ein solches Feld ist in den Suchergebnissen unpraktisch und sollte für diese Demo ausgeschlossen werden. 

Wenn Sie jedoch Dateiinhalte an Clientcode übergeben müssen, stellen Sie sicher, dass **Abrufbar** ausgewählt bleibt, damit die Suchmaschine dieses Feld zurückgeben kann.

Die Markierung eines Felds als **abrufbar** bedeutet nicht, dass das Feld in den Suchergebnissen vorhanden sein *muss*. Sie können die Zusammenstellung der Suchergebnisse präzise steuern und mit dem Abfrageparameter **$select** angeben, welche Felder enthalten sein sollen. Bei textlastigen Feldern wie `content` können Sie mit dem Parameter **$select** verwaltbare Suchergebnisse für die menschlichen Benutzer Ihrer Anwendung bereitstellen und gleichzeitig sicherstellen, dass Clientcode über das Attribut **Abrufbar** Zugriff auf alle erforderlichen Informationen hat.
  
Wechseln Sie zur nächsten Seite.

### <a name="step-4---configure-the-indexer"></a>Schritt 4: Konfigurieren des Indexers

Der Indexer ist eine allgemeine Ressource, die den Indizierungsvorgang antreibt. Er gibt den Datenquellennamen, einen Zielindex und die Häufigkeit der Ausführung an. Der **Datenimport-Assistent** erstellt mehrere Objekte, und eines davon ist immer ein Indexer, den Sie wiederholt ausführen können.

1. Auf der Seite **Indexer** können Sie den Standardnamen übernehmen und auf die Zeitplanoption **Einmal** klicken, um ihn sofort auszuführen. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Indexerdefinition" border="false":::

1. Klicken Sie auf **Senden**, um den Indexer zu erstellen und gleichzeitig auszuführen.

## <a name="monitor-status"></a>Überwachen des Status

Die Indizierung kognitiver Qualifikationen dauert länger als die übliche textbasierte Indizierung. Dies gilt insbesondere für OCR und Bildanalyse. Navigieren Sie zum Überwachen des Fortschritts zur Übersichtsseite, und klicken Sie in der Mitte der Seite auf **Indexer**.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Azure Cognitive Search-Benachrichtigung" border="false":::

Warnungen sind hinsichtlich der umfangreichen Spanne von Inhaltstypen normal. Einige Inhaltstypen sind für bestimmte Skills ungültig, und bei niedrigeren Tarifen gelten üblicherweise [Grenzwerte für Indexer](search-limits-quotas-capacity.md#indexer-limits). Beispielsweise treten im Free-Tarif Benachrichtigungen über Kürzungen auf den Indexergrenzwert von 32.000 Zeichen auf. Bei Ausführung dieser Demo auf einer höheren Ebene würden viele Kürzungswarnungen wegfallen.

Um Warnungen oder Fehler zu überprüfen, klicken Sie in der Liste „Indexer“ auf den Status „Warnung“, um die Seite „Ausführungsverlauf“ zu öffnen.

Klicken Sie auf dieser Seite erneut auf den Status „Warnung“, um die Liste der Warnungen anzuzeigen, die der unten gezeigten ähneln. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Indexerwarnungsliste" border="false":::

Wenn Sie auf eine bestimmte Statuszeile klicken, werden Details angezeigt. Diese Warnung besagt, dass die Zusammenführung nach Erreichen eines maximalen Schwellenwerts beendet wurde (diese spezielle PDF-Datei ist groß).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Warnungsdetails" border="false":::

## <a name="query-in-search-explorer"></a>Abfragen im Suchexplorer

Nachdem ein Index erstellt wurde, können Sie Abfragen ausführen, um Ergebnisse zu erhalten. Verwenden Sie im Portal den **Suchexplorer** für diese Aufgabe. 

1. Klicken Sie auf der Dashboardseite des Suchdiensts auf der Befehlsleiste auf **Suchexplorer**.

1. Wählen Sie oben **Index ändern** aus, um den von Ihnen erstellten Index auszuwählen.

1. Geben Sie eine Suchzeichenfolge ein, um den Index abzufragen, z.B. `search=Microsoft&$select=people,organizations,locations,imageTags`.

Die Ergebnisse werden im JSON-Format zurückgegeben, was sehr ausführlich und schwierig zu lesen sein kann, insbesondere bei langen Dokumenten, die aus Azure-Blobs stammen. Einige Tipps für die Suche in diesem Tool umfassen die folgenden Techniken:

+ Fügen Sie `$select` an, um festzulegen, welche Felder in die Ergebnisse aufgenommen werden sollen. 
+ Suchen Sie mit STRG+F im JSON-Code nach bestimmten Eigenschaften oder Begriffen.

Bei Abfragezeichenfolgen wird die Groß-/Kleinschreibung beachtet. Wenn Sie also eine Meldung „Unbekanntes Feld“ erhalten, überprüfen Sie **Felder** oder **Indexdefinition (JSON)** , um Name und Schreibweise zu überprüfen. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Suchexplorer-Beispiel" border="false":::

## <a name="takeaways"></a>Wesentliche Punkte

Sie haben nun Ihr erstes Skillset erstellt und sich mit wichtigen Konzepten vertraut gemacht, die für die Erstellung von Prototypen für eine angereicherte Suchlösung mit Ihren eigenen Daten hilfreich sind.

Einige wichtige Konzepte, von denen wir hoffen, dass Sie sie verinnerlicht haben, schließen die Abhängigkeit von Azure-Datenquellen ein. Ein Skillset ist an einen Indexer gebunden, und Indexer sind Azure- und quellenspezifisch. In dieser Schnellstartanleitung wird Azure Blob Storage verwendet. Es können aber auch andere Azure-Datenquellen verwendet werden. Weitere Informationen finden Sie unter [Indexer in Azure Cognitive Search](search-indexer-overview.md). 

Ein weiteres wichtiges Konzept ist, dass Skills mit Inhaltstypen arbeiten und bei der Arbeit mit heterogenen Inhalten einige Eingaben übersprungen werden. Außerdem können große Dateien oder Felder die Indexergrenzwerte ihrer Dienstebene überschreiten. Es ist normal, dass Warnungen angezeigt werden, wenn diese Ereignisse auftreten. 

Die Ausgabe wird an einen Suchindex weitergeleitet, und es gibt eine Zuordnung zwischen Name-Wert-Paaren, die im Zuge der Indizierung erstellt wurden, und einzelnen Feldern in Ihrem Index. Intern richtet das Portal [Anmerkungen](cognitive-search-concept-annotations-syntax.md) ein und definiert eine [Qualifikationsgruppe](cognitive-search-defining-skillset.md), um die Reihenfolge der Vorgänge und den allgemeinen Ablauf festzulegen. Diese Schritte sind im Portal ausgeblendet, werden aber wichtig, wenn Sie selbst mit der Erstellung von Code beginnen.

Außerdem haben Sie gelernt, dass Sie Inhalte durch Abfragen des Index überprüfen können. Azure Cognitive Search stellt letztendlich einen durchsuchbaren Index bereit, den Sie entweder mit der [einfachen](/rest/api/searchservice/simple-query-syntax-in-azure-search) oder mit der [vollständig erweiterten Abfragesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search) abfragen können. Ein Index, der angereicherte Felder enthält, ist wie jeder andere. Wenn Sie standardmäßige oder [benutzerdefinierte Analysetools](search-analyzers.md), [Bewertungsprofile](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [Synonyme](search-synonyms.md), [Facettennavigation](search-faceted-navigation.md), die geografische Suche oder andere Azure Cognitive Search-Features einbeziehen möchten, stehen Ihnen alle Wege offen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Skillsets können über das Portal, per .NET SDK oder per REST-API erstellt werden. Probieren Sie bei Interesse die REST-API mit Postman und weiteren Beispieldaten aus.

> [!div class="nextstepaction"]
> [Tutorial: Extrahieren von Text und Struktur aus JSON-Blobs in Azure mit REST-APIs (Azure Cognitive Search)](cognitive-search-tutorial-blob.md)

> [!Tip]
> Wenn Sie diese Übung wiederholen oder eine andere exemplarische Vorgehensweise für die KI-Anreicherung ausprobieren möchten, löschen Sie den Indexer im Portal. Durch Löschen des Indexers wird der Zähler für kostenlose Transaktionen pro Tag für die Cognitive Services-Verarbeitung auf Null zurückgesetzt.
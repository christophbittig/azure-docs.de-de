---
title: 'Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal'
titleSuffix: Azure Cognitive Search
description: Verwenden Sie den Datenimport-Assistenten, um einen Wissensspeicher zum Speichern angereicherter Inhalte zu erstellen. Stellen Sie von anderen Apps aus eine Verbindung mit einem Wissensspeicher her, um Analysen durchzuführen, oder senden Sie angereicherte Inhalte an Downstreamprozesse.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/02/2021
ms.openlocfilehash: f80a4a5961c0506f423da4d4f1578b8cf8999b51
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755228"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal

Der [Wissensspeicher](knowledge-store-concept-intro.md) ist ein Feature von Azure Cognitive Search, das Ausgaben aus einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) an Azure Storage sendet. Von der Pipeline erstellte Anreicherungen – z. B. übersetzter Text, OCR-Text, erkannte Entitäten und andere Anreicherungen – werden in Tabellen oder Blobs projiziert, wo von allen Apps oder Workloads, die eine Verbindung mit Azure Storage herstellen, darauf zugegriffen werden kann.

In diesem Schnellstart richten Sie Ihre Daten ein und führen dann den **Datenimport-Assistenten** aus, um eine Anreicherungspipeline zu erstellen, die auch einen Wissensspeicher generiert. Der Wissensspeicher enthält originalen Textinhalt, der aus der Quelle (Kundenbewertungen eines Hotels) stammt, sowie mittels KI generierte Inhalte, die eine Stimmungsbezeichnung, Schlüsselbegriffsextraktion und Textübersetzung nicht englischsprachiger Kundenkommentare enthalten.

> [!NOTE]
> Dieser Schnellstart zeigt Ihnen den schnellsten Weg zu einem fertigen Wissensspeicher in Azure Storage. Ausführlichere Erläuterungen zu jedem Schritt finden Sie hingegen unter [Erstellen eines Wissensspeichers in REST](knowledge-store-create-rest.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die folgenden Dienste verwendet:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder [suchen Sie in Ihrem Konto nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Ein Azure Storage-Konto mit [Blob Storage](../storage/blobs/index.yml).

In diesem Schnellstart wird außerdem [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="set-up-your-data"></a>Einrichten Ihrer Daten

In den folgenden Schritten richten Sie einen Blobcontainer in Azure Storage ein, um heterogene Inhaltsdateien zu speichern.

1. [Laden Sie „HotelReviews_Free.csv“ herunter.](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Bei diesen Daten handelt es sich um gespeicherte Hotelrezensionen (von Kaggle.com) in einer CSV-Datei. Die Daten umfassen 19 Kundenfeedbacks für ein einzelnes Hotel. 

1. [Erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Azure Storage wird sowohl für die zu importierenden Rohinhalte als auch für den resultierenden Wissensspeicher verwendet.

   Wählen Sie den Kontotyp **StorageV2 (allgemein, Version 2)** aus.

1. Erstellen Sie in der Azure Storage-Ressource in **Storage-Explorer** einen Blobcontainer mit dem Namen **hotel-reviews**.

1. Wählen Sie oben auf der Seite **Hochladen** aus, um die Datei **HotelReviews-Free.csv** zu laden, die Sie im vorherigen Schritt heruntergeladen haben.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="Screenshot: Storage-Explorer mit hochgeladener Datei und linkem Navigationsbereich" border="true":::

1. Sie sind fast fertig mit dieser Ressource, aber bevor Sie diese Seiten verlassen, wählen Sie im linken Navigationsbereich **Zugriffsschlüssel** aus, um eine Verbindungszeichenfolge abzurufen, damit Sie diese Daten mit dem Indexer abrufen können.

1. Wählen Sie in **Zugriffsschlüssel** oben auf der Seite **Schlüssel anzeigen** aus, um die Verbindungszeichenfolgen einzublenden, und kopieren Sie dann die Verbindungszeichenfolge entweder für Schlüssel1 oder Schlüssel2.

   Die Verbindungszeichenfolge weist das folgende Format auf: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Nun können Sie zum **Datenimport**-Assistenten wechseln.

## <a name="start-the-wizard"></a>Starten des Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Suchen Sie Ihren Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Übersichtsseite auf der Befehlsleiste auf **Daten importieren**, um in vier Schritten einen Wissensspeicher zu erstellen.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Da die Daten aus mehreren Zeilen in einer CSV-Datei bestehen, legen Sie den *Analysemodus* fest, um ein Suchdokument für jede Zeile zu erhalten.

1. Wählen Sie unter **Verbindung mit Ihren Daten herstellen** die Option **Azure Blob Storage** sowie das erstellte Konto und den erstellten Container aus. 

1. Geben Sie unter **Name** den Namen `hotel-reviews-ds` ein.

1. Wählen Sie unter **Analysemodus** die Option **Durch Trennzeichen getrennter Text** aus, und aktivieren Sie anschließend das Kontrollkästchen **Erste Zeile enthält Überschrift**. Vergewissern Sie sich, dass unter **Trennzeichen** ein Komma (,) angegeben ist.

1. Fügen Sie in **Verbindungszeichenfolge** die Verbindungszeichenfolge ein, die Sie aus Azure Storage kopiert haben.

1. Geben Sie in **Container** den Namen des Blobcontainers ein, der die Daten enthält (`hotel-reviews`).

    Ihre Seite sollte in etwa dem folgenden Screenshot entsprechen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="Screenshot der Datenquellendefinition" border="true":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-2-add-skills"></a>Schritt 2: Hinzufügen von Qualifikationen

Fügen Sie in diesem Schritt des Assistenten Qualifikationen für die KI-Anreicherung hinzu. Die Quelldaten bestehen aus Kundenbewertungen in englischer und französischer Sprache. Zu den Qualifikationen, die für dieses Dataset relevant sind, gehören die Schlüsselwortextraktion, die Stimmungserkennung und die Textübersetzung. Diese Anreicherungen werden in einem späteren Schritt als Azure-Tabellen in einen Wissensspeicher „projiziert“.

1. Erweitern Sie **Cognitive Services-Instanz anfügen**. Standardmäßig ist **Free (begrenzte Anreicherung)** ausgewählt. Da die Datei „HotelReviews-Free.csv“ 19 Datensätze enthält und mit der kostenlosen Ressource 20 Transaktionen pro Tag möglich sind, können Sie diese Ressource verwenden.

1. Erweitern Sie **Anreicherungen hinzufügen**.

1. Geben Sie unter **Name des Skillsets** den Namen `hotel-reviews-ss` ein.

1. Wählen Sie unter **Quelldatenfeld** die Option **reviews_text** aus.

1. Wählen Sie unter **Granularitätsebene für Anreicherung** die Option **Seiten (5.000 Zeichenblöcke)** aus.

1. Wählen Sie für **Kognitive Fähigkeiten für Text** die folgenden Qualifikationen aus:

    + **How to extract key phrases in Text Analytics** (Extrahieren von Schlüsselbegriffen mithilfe der Textanalyse)
    + **Übersetzen von Text**
    + **Sprachenerkennung**
    + **Stimmung erkennen**

   Ihre Seite sollte wie im folgenden Screenshot aussehen:

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="Screenshot der Skillsetdefinition" border="true":::

1. Scrollen Sie nach unten, und erweitern Sie **Anreicherungen in einem Wissensspeicher speichern**.

1. Wählen Sie die folgenden **Azure-Tabellenprojektionen** aus. Der Assistent bietet stets die Projektion **Dokumente**. Je nach den von Ihnen ausgewählten Qualifikationen (z. B. **Schlüsselbegriffe**) oder Granularität der Anreicherung (**Seiten**) werden andere Projektionen angeboten:

    + **Dokumente**
    + **Seiten**
    + **Schlüsselbegriffe**

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="Screenshot der Definition des Wissensspeichers" border="true":::

1. Geben Sie unter **Verbindungszeichenfolge für Speicherkonto** die Verbindungszeichenfolge ein, die Sie in einem früheren Schritt gespeichert haben.

1. Wechseln Sie zur nächsten Seite.

### <a name="step-3-configure-the-index"></a>Schritt 3: Konfigurieren des Index

In diesem Schritt des Assistenten konfigurieren Sie einen Index für optionale Volltextsuchabfragen. Der Assistent analysiert Ihre Datenquelle anhand einer Stichprobe, um Felder und Datentypen abzuleiten. Sie müssen lediglich die Attribute für das gewünschte Verhalten auswählen. Mit dem Attribut **Abrufbar** kann der Suchdienst beispielsweise einen Feldwert zurückgeben. Das Attribut **Suchbar** ermöglicht dagegen eine Volltextsuche für das Feld.

1. Geben Sie unter **Indexname** den Namen `hotel-reviews-idx` ein.

1. Übernehmen Sie für Attribute die Standardauswahl: **Abrufbar** und **Durchsuchbar** für die neuen Felder, die von der Pipeline erstellt werden.

    Ihr Index sollte in etwa wie in der folgenden Abbildung aussehen: Aufgrund der Länger der Liste sind in der Abbildung nicht alle Felder zu sehen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="Screenshot der Indexdefinition" border="true":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-4-configure-and-run-the-indexer"></a>Schritt 4: Konfigurieren und Ausführen des Indexers

In diesem Schritt des Assistenten konfigurieren Sie einen Indexer, der die Datenquelle, das Skillset und den Index aus den vorherigen Schritten zusammenführt.

1. Geben Sie unter **Name**`hotel-reviews-idxr` ein.

1. Behalten Sie unter **Zeitplan** den Standardwert **Einmalig** bei.

1. Wählen Sie **Senden** aus, um den Indexer auszuführen. In diesem Schritt werden die Datenextraktion und die Indizierung durchgeführt und die kognitiven Qualifikationen angewendet.

## <a name="check-status"></a>Status überprüfen

Öffnen Sie in der Mitte der Seite **Übersicht** die Registerkarte **Indexer**, und wählen Sie dann **hotels-reviews-ixr** aus. Innerhalb von ein oder zwei Minuten sollte sich der Status von „In Bearbeitung“ ohne Fehler und Warnungen in „Erfolgreich“ ändern.

## <a name="check-tables-in-storage-explorer"></a>Überprüfen von Tabellen in Storage-Explorer

Wechseln Sie im Azure-Portal zu Ihrem Azure Storage-Konto, und zeigen Sie in **Storage-Explorer** die neuen Tabellen an. Sie sollten drei Tabellen sehen, und zwar eine für jede Projektion, die im Abschnitt „Anreicherungen speichern“ auf der Seite „Anreicherungen hinzufügen“ angeboten wurde.

+ `hotelReviewssDocument` enthält alle Knoten der ersten Ebene der Anreicherungsstruktur eines Dokuments, die keine Sammlungen sind.

+ `hotelReviewssPages` enthält angereicherte Felder, die auf jeder Seite erstellt wurden, die vom Dokument getrennt wurde. Anreicherungen auf Seitenebene bestehen aus einer Stimmungsbezeichnung und übersetztem Text. Eine Seitentabelle (oder eine Sätzetabelle, wenn Sie diese spezielle Granularitätsstufe angeben) wird erstellt, wenn Sie die Granularität „Seiten“ in der Skillsetdefinition auswählen. Für Qualifikationen, die auf Seiten- oder Satzebene ausgeführt werden, wird die Ausgabe in diese Tabelle projiziert.

+ `hotelReviewssKeyPhrases` enthält eine lange Liste mit nur den Schlüsselbegriffen, die aus allen Bewertungen extrahiert wurden. Qualifikationen, die Sammlungen (Arrays) ausgeben, z. B. Schlüsselbegriffe und Entitäten, erzeugen Ausgaben, die an eine eigenständige Tabelle gesendet werden.

Alle diese Tabellen enthalten ID-Spalten, um Tabellenbeziehungen in anderen Tools und Apps zu unterstützen. Scrollen Sie beim Öffnen einer Tabelle über diese Felder, um die von der Pipeline hinzugefügten Inhaltsfelder anzuzeigen.

In diesem Schnellstart sollte Ihre Tabelle in etwa wie im folgenden Screenshot aussehen:

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Screenshot der generierten Tabellen in Storage-Explorer" border="true":::

## <a name="clean-up"></a>Bereinigung

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie am Ende eines Projekts prüfen, ob Sie die Ressourcen, die Sie erstellt haben, noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben.

> [!TIP]
> Wenn Sie diese Übung wiederholen oder eine andere exemplarische Vorgehensweise für die KI-Anreicherung ausprobieren möchten, löschen Sie den Indexer **hotel-reviews-idxr** und die zugehörigen Objekte, um sie neu zu erstellen. Durch das Löschen des Indexers wird der Zähler für kostenlose Transaktionen pro Tag auf Null zurückgesetzt.

## <a name="next-steps"></a>Nächste Schritte

Nach der Einführung in Wissensspeicher können Sie sich die einzelnen Schritte genauer ansehen, indem Sie zur exemplarischen Vorgehensweise für die REST-API wechseln. Aufgaben, die der Assistent intern verarbeitet hat, werden in der exemplarischen Vorgehensweise zu REST erläutert.

> [!div class="nextstepaction"]
> [Erstellen eines Wissensspeichers mithilfe von REST und Postman](knowledge-store-create-rest.md)

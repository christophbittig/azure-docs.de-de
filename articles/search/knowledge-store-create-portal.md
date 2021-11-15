---
title: 'Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal'
titleSuffix: Azure Cognitive Search
description: Verwenden Sie den Datenimport-Assistenten, um einen Wissensspeicher zum Speichern angereicherter Inhalte zu erstellen. Stellen Sie von anderen Apps aus eine Verbindung mit einem Wissensspeicher her, um Analysen durchzuführen, oder senden Sie angereicherte Inhalte an Downstreamprozesse.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/28/2021
ms.openlocfilehash: 6e9c09cb5407747c325b696570cd3eabdeeb4ed7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563718"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal

Der [Wissensspeicher](knowledge-store-concept-intro.md) ist ein Feature von Azure Cognitive Search, das Ausgaben aus einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) akzeptiert und in Azure Storage für nachgeschaltete Apps und Workloads verfügbar macht. Von der Pipeline erstellte Anreicherungen – z. B. übersetzter Text, OCR-Text, Bilder mit Tags und erkannte Entitäten – werden in Tabellen oder Blobs projiziert, wo von allen Apps oder Workloads, die eine Verbindung mit Azure Storage herstellen, darauf zugegriffen werden kann.

In diesem Schnellstart richten Sie Ihre Daten ein und führen dann den **Datenimport-Assistenten** aus, um eine Anreicherungspipeline zu erstellen, die auch einen Wissensspeicher generiert. Der Wissensspeicher enthält originalen Textinhalt, der aus der Quelle (Kundenbewertungen eines Hotels) stammt, sowie mittels KI generierte Inhalte, die eine Stimmungsbezeichnung, Schlüsselbegriffsextraktion und Textübersetzung nicht englischsprachiger Kundenkommentare enthalten.

> [!NOTE]
> Dieser Schnellstart zeigt Ihnen den schnellsten Weg zu einem fertigen Wissensspeicher in Azure Storage. Ausführlichere Erläuterungen zu jedem Schritt finden Sie hingegen unter [Erstellen eines Wissensspeichers in REST](knowledge-store-create-rest.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die folgenden Dienste verwendet:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Azure Cognitive Search. [Erstellen Sie einen Dienst](search-create-service-portal.md), oder [suchen Sie in Ihrem Konto nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Azure Storage. [Erstellen Sie ein Konto](../storage/common/storage-account-create.md), oder [suchen Sie ein vorhandenes Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Der Kontotyp muss **StorageV2 (universell, V2)** lauten.

+ Beispieldaten. In dieser Schnellstartanleitung werden in einer CSV-Datei gespeicherte Hotelrezensionen (von Kaggle.com) verwendet. Die Daten umfassen 19 Kundenfeedbacks für ein einzelnes Hotel.

  [Laden Sie „HotelReviews_Free.csv“ herunter](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D), und laden Sie dann [die Datei in einen Blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) in Azure Storage hoch.

In dieser Schnellstartanleitung wird außerdem [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI-Anreicherung verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="start-the-wizard"></a>Starten des Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Suchen Sie Ihren Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Übersichtsseite auf der Befehlsleiste auf **Daten importieren**, um in vier Schritten einen Wissensspeicher zu erstellen.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Da die Daten aus mehreren Zeilen in einer CSV-Datei bestehen, legen Sie den *Analysemodus* fest, um ein Suchdokument für jede Zeile zu erhalten.

1. Wählen Sie unter **Verbindung mit Ihren Daten herstellen** die Option **Azure Blob Storage** sowie das erstellte Konto und den erstellten Container aus. 

1. Geben Sie unter **Name** den Namen „hotel-reviews-ds“ ein.

1. Wählen Sie unter **Analysemodus** die Option **Durch Trennzeichen getrennter Text** aus, und aktivieren Sie anschließend das Kontrollkästchen **Erste Zeile enthält Überschrift**. Vergewissern Sie sich, dass unter **Trennzeichen** ein Komma (,) angegeben ist.

1. Fügen Sie unter **Verbindungszeichenfolge** eine Verbindungszeichenfolge für Ihr Azure Storage-Konto ein. 

   Die Verbindungszeichenfolge weist das folgende Format auf: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Geben Sie unter **Container** den Namen des Blobcontainers ein, der die Daten enthält (hotel-reviews).

    Ihre Seite sollte in etwa dem folgenden Screenshot entsprechen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="Screenshot der Datenquellendefinition" border="true":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-2-add-skills"></a>Schritt 2: Hinzufügen von Qualifikationen

Fügen Sie in diesem Schritt des Assistenten Qualifikationen für die KI-Anreicherung hinzu. Die Quelldaten bestehen aus Kundenbewertungen in englischer und französischer Sprache. Zu den Qualifikationen, die für dieses Dataset relevant sind, gehören die Schlüsselwortextraktion, die Stimmungserkennung und die Textübersetzung. Diese Anreicherungen werden in einem späteren Schritt als Azure-Tabellen in einen Wissensspeicher „projiziert“.

1. Erweitern Sie **Cognitive Services-Instanz anfügen**. Standardmäßig ist **Free (begrenzte Anreicherung)** ausgewählt. Da die Datei „HotelReviews-Free.csv“ 19 Datensätze enthält und mit der kostenlosen Ressource 20 Transaktionen pro Tag möglich sind, können Sie diese Ressource verwenden.

1. Erweitern Sie **Anreicherungen hinzufügen**.

1. Geben Sie unter **Skillsetname** den Namen „hotel-reviews-ss“ ein.

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

1. Wählen Sie **Vorhandene Verbindung auswählen** und anschließend ein Azure Storage-Konto aus. Die Seite „Container“ wird angezeigt, damit Sie einen Container für Projektionen erstellen können. Es wird empfohlen, eine Präfixnamenskonvention wie „kstore-hotel-reviews“ einzuführen, um zwischen Quell- und Wissensspeicherinhalten zu unterscheiden.

1. Kehren Sie zum Datenimport-Assistenten zurück, und wählen Sie die folgenden **Azure-Tabellenprojektionen** aus. Der Assistent bietet stets die Projektion **Dokumente**. Je nach den von Ihnen ausgewählten Qualifikationen (z. B. **Schlüsselbegriffe**) oder Granularität der Anreicherung (**Seiten**) werden andere Projektionen angeboten:

    + **Dokumente**
    + **Seiten**
    + **Schlüsselbegriffe**

   Der folgende Screenshot zeigt die Auswahl der Tabellenprojektion im Assistenten:

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="Screenshot der Definition des Wissensspeichers" border="true":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-3-configure-the-index"></a>Schritt 3: Konfigurieren des Index

In diesem Schritt des Assistenten konfigurieren Sie einen Index für optionale Volltextsuchabfragen. Der Assistent analysiert Ihre Datenquelle anhand einer Stichprobe, um Felder und Datentypen abzuleiten. Sie müssen lediglich die Attribute für das gewünschte Verhalten auswählen. Mit dem Attribut **Abrufbar** kann der Suchdienst beispielsweise einen Feldwert zurückgeben. Das Attribut **Suchbar** ermöglicht dagegen eine Volltextsuche für das Feld.

1. Geben Sie unter **Indexname** den Namen „hotel-reviews-idx“ ein.

1. Übernehmen Sie für Attribute die Standardauswahl: **Abrufbar** und **Durchsuchbar** für die neuen Felder, die von der Pipeline erstellt werden.

    Ihr Index sollte in etwa wie in der folgenden Abbildung aussehen: Aufgrund der Länger der Liste sind in der Abbildung nicht alle Felder zu sehen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="Screenshot der Indexdefinition" border="true":::

1. Wechseln Sie zur nächsten Seite.

### <a name="step-4-configure-and-run-the-indexer"></a>Schritt 4: Konfigurieren und Ausführen des Indexers

In diesem Schritt des Assistenten konfigurieren Sie einen Indexer, der die Datenquelle, das Skillset und den Index aus den vorherigen Schritten zusammenführt.

1. Geben Sie unter **Name** den Namen „hotel-reviews-idxr“ ein.

1. Behalten Sie unter **Zeitplan** den Standardwert **Einmalig** bei.

1. Wählen Sie **Senden** aus, um den Indexer auszuführen. In diesem Schritt werden die Datenextraktion und die Indizierung durchgeführt und die kognitiven Qualifikationen angewendet.

### <a name="step-5-check-status"></a>Schritt 5: Überprüfen des Status

Öffnen Sie in der Mitte der Seite **Übersicht** die Registerkarte **Indexer**, und wählen Sie dann **hotels-reviews-idxr** aus. Innerhalb von ein oder zwei Minuten sollte sich der Status von „In Bearbeitung“ ohne Fehler und Warnungen in „Erfolgreich“ ändern.

## <a name="check-tables-in-storage-browser"></a>Überprüfen von Tabellen im Speicherbrowser

Wechseln Sie im Azure-Portal zu Ihrem Azure Storage-Konto, und zeigen Sie im **Speicherbrowser** die neuen Tabellen an. Sie sollten drei Tabellen sehen, und zwar eine für jede Projektion, die im Abschnitt „Anreicherungen speichern“ auf der Seite „Anreicherungen hinzufügen“ angeboten wurde.

+ „hotelReviewssDocuments“ enthält alle Knoten der ersten Ebene der Anreicherungsstruktur eines Dokuments, die keine Sammlungen sind. 

+ „hotelReviewssKeyPhrases“ enthält eine lange Liste mit nur den Schlüsselbegriffen, die aus allen Bewertungen extrahiert wurden. Qualifikationen, die Sammlungen (Arrays) ausgeben, z. B. Schlüsselbegriffe und Entitäten, erzeugen Ausgaben, die an eine eigenständige Tabelle gesendet werden.

+ „hotelReviewssPages“ enthält angereicherte Felder, die auf jeder Seite erstellt wurden, die vom Dokument getrennt wurde. In diesem Skillset und dieser Datenquelle bestehen die Anreicherungen auf Seitenebene aus Stimmungsbezeichnungen und übersetztem Text. Eine Seitentabelle (oder eine Sätzetabelle, wenn Sie diese spezielle Granularitätsstufe angeben) wird erstellt, wenn Sie die Granularität „Seiten“ in der Skillsetdefinition auswählen. 

Alle diese Tabellen enthalten ID-Spalten, um Tabellenbeziehungen in anderen Tools und Apps zu unterstützen. Scrollen Sie beim Öffnen einer Tabelle über diese Felder, um die von der Pipeline hinzugefügten Inhaltsfelder anzuzeigen.

In dieser Schnellstartanleitung sollte die Tabelle für „hotelReviewssPages“ etwa wie auf dem folgenden Screenshot aussehen:

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Screenshot: Generierte Tabellen im Speicherbrowser" border="true":::

## <a name="clean-up"></a>Bereinigen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie am Ende eines Projekts prüfen, ob Sie die Ressourcen, die Sie erstellt haben, noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben.

> [!TIP]
> Wenn Sie diese Übung wiederholen oder eine andere exemplarische Vorgehensweise für die KI-Anreicherung ausprobieren möchten, löschen Sie den Indexer **hotel-reviews-idxr** und die zugehörigen Objekte, um sie neu zu erstellen. Durch das Löschen des Indexers wird der Zähler für kostenlose Transaktionen pro Tag auf Null zurückgesetzt.

## <a name="next-steps"></a>Nächste Schritte

Nach der Einführung in Wissensspeicher können Sie sich die einzelnen Schritte genauer ansehen, indem Sie zur exemplarischen Vorgehensweise für die REST-API wechseln. Aufgaben, die der Assistent intern verarbeitet hat, werden in der exemplarischen Vorgehensweise zu REST erläutert.

> [!div class="nextstepaction"]
> [Erstellen eines Wissensspeichers mithilfe von REST und Postman](knowledge-store-create-rest.md)

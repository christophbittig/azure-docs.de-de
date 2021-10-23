---
title: 'Schnellstart: Textübersetzung und Entitätserkennung'
titleSuffix: Azure Cognitive Search
description: Verwenden Sie den Datenimport-Assistenten und KI-basierte kognitive Skills, um Sprache und Entitäten zu erkennen und Text zu übersetzen. Die durch KI erstellten neuen Felder werden in einem Azure Cognitive Search-Index zu durchsuchbarem Text.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 01024a829c4e4447cfa7dc642469384170bc99f4
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708126"
---
# <a name="quickstart-translate-text-and-recognize-entities-using-the-import-data-wizard"></a>Schnellstart: Übersetzen von Text und Erkennen von Entitäten mithilfe des Datenimport-Assistenten

Hier erfahren Sie, wie die KI-Anreicherung in Azure Cognitive Search Sprachenerkennung, Textübersetzung und Entitätserkennung hinzufügt, um durchsuchbare Inhalte in einem Suchindex zu erstellen. 

In dieser Schnellstartanleitung führen Sie den **Datenimport-Assistenten** aus, um französische und spanische Beschreibungen von verschiedenen Nationalmuseen in Spanien zu analysieren. Die Ausgabe ist ein durchsuchbarer Index, der übersetzten Text und Entitäten enthält und im Portal mithilfe des [Suchexplorers](search-explorer.md) abgefragt werden kann. 

Zur Vorbereitung erstellen Sie einige Ressourcen und laden Beispieldateien hoch, bevor Sie den Assistenten ausführen.

Beginnen Sie lieber mit Code? Probieren Sie stattdessen das [.NET-Tutorial](cognitive-search-tutorial-blob-dotnet.md), das [Python-Tutorial](cognitive-search-tutorial-blob-python.md) oder das [REST-Tutorial](cognitive-search-tutorial-blob-dotnet.md) aus.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen können, müssen die folgenden Voraussetzungen erfüllt werden:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Azure Cognitive Search-Dienst. [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Azure Storage-Konto mit Blob Storage. [Erstellen Sie ein Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie ein vorhandenes Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

  + Wählen Sie dasselbe Abonnement aus, wenn der Assistent Ihr Speicherkonto suchen und die Verbindung einrichten soll.
  + Es muss sich in der gleichen Region wie Azure Cognitive Search befinden, um Bandbreitengebühren zu vermeiden.
  + Wählen Sie „StorageV2 (universell V2)“ aus.

> [!NOTE]
> In diesem Schnellstart wird außerdem [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="set-up-your-data"></a>Einrichten Ihrer Daten

In den folgenden Schritten richten Sie einen Blobcontainer in Azure Storage ein, um heterogene Inhaltsdateien zu speichern.

1. Laden Sie [Beispieldaten](https://github.com/Azure-Samples/azure-search-sample-data) von GitHub herunter. Es gibt mehrere Datasets. Verwenden Sie für diese Schnellstartanleitung die Dateien im Ordner **spanish-museums**.

1. Laden Sie die Beispieldaten in einen Blobcontainer hoch.

   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und suchen Sie Ihr Speicherkonto.
   1. Wählen Sie im linken Navigationsbereich die Option **Container** aus.
   1. [Erstellen Sie einen Container](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) mit dem Namen „spanish-museums“. Verwenden Sie die standardmäßige öffentliche Zugriffsebene.
   1. Wählen Sie im Container „spanish-museums“ die Option **Hochladen** aus, um die Dateien aus dem lokalen Ordner **spanish-museums** hochzuladen.

Sie sollten über zehn Dateien mit französischen und spanischen Beschreibungen der Nationalmuseen in Spanien verfügen.

   :::image type="content" source="media/cognitive-search-quickstart-blob/museums-container.png" alt-text="Liste der DOCX-Dateien in einem Blobcontainer" border="true":::

Nun können Sie zum Datenimport-Assistenten wechseln.

## <a name="run-the-import-data-wizard"></a>Ausführen des Datenimport-Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Navigieren Sie zu Ihrem Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Seite „Übersicht“ auf der Befehlsleiste auf **Daten importieren**, um die kognitive Anreicherung in vier Schritten einzurichten.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

### <a name="step-1---create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

1. Wählen Sie unter **Mit Ihren Daten verbinden** die Option **Azure Blob Storage** aus. Wählen Sie eine vorhandene Verbindung mit dem von Ihnen erstellten Speicherkonto und Container aus. Geben Sie der Datenquelle einen Namen, und verwenden Sie für alles andere die Standardwerte. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-spanish-museums.png" alt-text="Azure-Blobkonfiguration" border="true":::

### <a name="step-2---add-cognitive-skills"></a>Schritt 2: Hinzufügen von kognitiven Qualifikationen

Konfigurieren Sie als Nächstes die KI-Anreicherung, um Sprachenerkennung, Textübersetzung und Entitätserkennung aufzurufen. 

1. In dieser Schnellstartanleitung verwenden wir die Cognitive Services-Ressource vom Typ **Free**. Die Beispieldaten umfassen zehn Dateien. Das Kontingent von 20 kostenlosen Transaktionen pro Tag und Indexer für Cognitive Services ist somit für diese Schnellstartanleitung ausreichend. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="Anfügen der kostenlosen Cognitive Services-Verarbeitung" border="true":::

1. Erweitern Sie auf der gleichen Seite die Option **Anreicherungen hinzufügen**, und wählen Sie die folgenden fünf Optionen aus:

   Wählen Sie die Entitätserkennung (Personen, Organisationen, Standorte) aus.

   Wählen Sie die Sprachenerkennung und die Textübersetzung aus.

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-entity-lang-enrichments.png" alt-text="Anfügen von Cognitive Services: Auswählen von Diensten für das Skillset" border="true":::

   In Blobs enthält das Feld „Inhalt“ den Inhalt der Datei. In den Beispieldaten handelt es sich beim Inhalt um mehrere Absätze zu einem bestimmten Museum (entweder in französischer oder in spanischer Sprache). Die Granularität ist auf das Feld festgelegt. Einige Skills funktionieren bei kleineren Textblöcken besser. Für die Skills in dieser Schnellstartanleitung ist die Granularität auf Feldebene jedoch ausreichend.

### <a name="step-3---configure-the-index"></a>Schritt 3: Konfigurieren des Indexes

Ein Index enthält Ihre durchsuchbaren Inhalte, und der **Datenimport-Assistent** kann in der Regel die Daten untersuchen und das Schema für Sie ableiten. Überprüfen Sie in diesem Schritt das generierte Schema, und überarbeiten Sie ggf. die Einstellungen. Unten sehen Sie das für das Demodataset erstellte Standardschema.

Für diesen Schnellstart legt der Assistent sinnvolle Standardwerte fest:  

+ Standardfelder basieren auf Eigenschaften für vorhandene Blobs und neue Felder, um die Anreicherungsausgabe enthalten zu können (z. B. `people`, `organizations`, `locations`). Datentypen werden aus Metadaten und Datenstichproben abgeleitet.

+ Der Standarddokumentschlüssel ist *metadata_storage_path* (da dieses Feld eindeutige Werte enthält).

+ Standardattribute sind **Abrufbar** und **Durchsuchbar**. **Durchsuchbar** ermöglicht die Volltextsuche in einem Feld. **Abrufbar** bedeutet, dass Feldwerte in Ergebnissen zurückgegeben werden können. Der Assistent geht davon aus, dass diese Felder abrufbar und durchsuchbar sein sollen, da Sie sie über eine Qualifikationsgruppe erstellt haben.

+ Aktivieren Sie das filterbare Kontrollkästchen für „Sprache“. Der Assistent legt den Ordner nicht für Sie fest, aber die Möglichkeit zur Filterung nach Sprache ist in dieser Demo nützlich, da es mehrere Sprachen gibt.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-lang-entities.png" alt-text="Indexfelder" border="true":::

Die Markierung eines Felds als **abrufbar** bedeutet nicht, dass das Feld in den Suchergebnissen vorhanden sein *muss*. Sie können die Zusammenstellung der Suchergebnisse präzise steuern und mit dem Abfrageparameter **$select** angeben, welche Felder enthalten sein sollen. Bei textlastigen Feldern wie `content` können Sie mit dem Parameter **$select** verwaltbare Suchergebnisse für die menschlichen Benutzer Ihrer Anwendung bereitstellen und gleichzeitig sicherstellen, dass Clientcode über das Attribut **Abrufbar** Zugriff auf alle erforderlichen Informationen hat.

### <a name="step-4---configure-the-indexer"></a>Schritt 4: Konfigurieren des Indexers

Der Indexer ist eine allgemeine Ressource, die den Indizierungsvorgang antreibt. Er gibt den Datenquellennamen, einen Zielindex und die Häufigkeit der Ausführung an. Der **Datenimport-Assistent** erstellt mehrere Objekte, und eines davon ist immer ein Indexer, den Sie wiederholt ausführen können.

1. Auf der Seite **Indexer** können Sie den Standardnamen übernehmen und auf die Zeitplanoption **Einmal** klicken, um ihn sofort auszuführen. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-spanish-museum.png" alt-text="Indexerdefinition" border="true":::

1. Klicken Sie auf **Senden**, um den Indexer zu erstellen und gleichzeitig auszuführen.

## <a name="monitor-status"></a>Überwachen des Status

Die Indizierung kognitiver Qualifikationen dauert länger als die übliche textbasierte Indizierung. Navigieren Sie zum Überwachen des Fortschritts zur Übersichtsseite, und wählen Sie in der Mitte der Seite die Registerkarte **Indexer** aus.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-spanish-museums.png" alt-text="Indexerstatus" border="true":::

Wählen Sie einen Indexer aus der Liste aus, um Details zum Ausführungsstatus anzuzeigen.

## <a name="query-in-search-explorer"></a>Abfragen im Suchexplorer

Nachdem ein Index erstellt wurde, können Sie Abfragen ausführen, um Ergebnisse zu erhalten. Verwenden Sie im Portal den **Suchexplorer** für diese Aufgabe. 

1. Klicken Sie auf der Dashboardseite des Suchdiensts auf der Befehlsleiste auf **Suchexplorer**.

1. Wählen Sie oben **Index ändern** aus, um den von Ihnen erstellten Index auszuwählen.

1. Geben Sie unter „Abfragezeichenfolge“ eine Suchzeichenfolge ein, um den Index abzufragen (etwa `search="picasso museum" &$select=people,organizations,locations,language,translated_text &$count=true &$filter=language eq 'fr'`), und wählen Sie dann **Suchen** aus.

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-spanish-museums.png" alt-text="Abfragezeichenfolge im Suchexplorer" border="true":::

Die Ergebnisse werden im JSON-Format zurückgegeben, was sehr ausführlich und schwierig zu lesen sein kann, insbesondere bei langen Dokumenten, die aus Azure-Blobs stammen. Einige Tipps für die Suche in diesem Tool umfassen die folgenden Techniken:

+ Fügen Sie `$select` an, um festzulegen, welche Felder in die Ergebnisse aufgenommen werden sollen. 
+ Suchen Sie mit STRG+F im JSON-Code nach bestimmten Eigenschaften oder Begriffen.

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-spanish-museums.png" alt-text="Suchexplorer-Beispiel" border="true":::

Bei Abfragezeichenfolgen wird die Groß-/Kleinschreibung beachtet. Wenn Sie also eine Meldung „Unbekanntes Feld“ erhalten, überprüfen Sie **Felder** oder **Indexdefinition (JSON)** , um Name und Schreibweise zu überprüfen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Cognitive Search verfügt über andere integrierte Skills, die im Datenimport-Assistenten ausgeführt werden können. Probieren Sie im nächsten Schritt die Skills für die OCR und Bildanalyse aus, um für die Textsuche geeignete Inhalte aus Bilddateien zu erstellen.

> [!div class="nextstepaction"]
> [Schnellstart: Anwenden von OCR und Bildanalyse mithilfe des Datenimport-Assistenten](cognitive-search-quickstart-ocr.md)
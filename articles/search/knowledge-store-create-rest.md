---
title: Erstellen eines Wissensspeichers mithilfe von REST
titleSuffix: Azure Cognitive Search
description: Verwenden Sie die REST-API und Postman, um einen Azure Cognitive Search-Wissensspeicher zum Speichern von KI-Anreicherungen aus einem Skillset zu erstellen.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: ad30dc4f59816f286f6ffe40909b76410e963c16
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555756"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Erstellen eines Wissensspeichers mithilfe von REST und Postman

Der Wissensspeicher ist ein Feature von Azure Cognitive Search, das Skillsetausgaben aus einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) zur nachfolgenden Wissensgewinnung, Datenanalyse oder nachgelagerten Verarbeitung an Azure Storage sendet. Nachdem der Wissensspeicher aufgefüllt wurde, können Sie den Inhalt mit Tools wie dem [Speicherbrowser](knowledge-store-view-storage-explorer.md) oder [Power BI](knowledge-store-connect-power-bi.md) erkunden.

In diesem Artikel verwenden Sie die REST-API zum Erfassen, Anreichern und Erkunden einer Reihe von Kundenbewertungen von Hotelaufenthalten in einem Wissensspeicher in Azure Storage. Das Endergebnis ist ein Wissensspeicher mit originalem Textinhalt, der aus der Quelle stammt, sowie mittels KI generierter Inhalte, die eine Stimmungsbewertung, Schlüsselbegriffsextraktion, Spracherkennung und Textübersetzung von nicht englischsprachigen Kundenkommentaren enthalten.

Um das anfängliche DataSet verfügbar zu machen, werden die Hotelrezensionen zuerst in Azure Blob Storage importiert. Bei der Nachbearbeitung werden die Ergebnisse als Wissensspeicher in Azure Table Storage gespeichert.

> [!NOTE]
> Dieser Artikel setzt die [Desktop-App Postman](https://www.getpostman.com/) voraus. Der [Quellcode](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) für diesen Artikel enthält eine Postman-Auflistung aller Anforderungen. 

## <a name="create-services-and-load-data"></a>Erstellen von Diensten und Laden von Daten

In dieser Übung werden Azure Cognitive Search, Azure Blob Storage und [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. 

Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund genutzt und bietet eine kostenlose Verarbeitung von bis zu 20 Transaktionen pro Tag. Bei einer kleinen Workload können Sie das Erstellen oder Anfügen einer Cognitive Services-Ressource überspringen.

1. [Laden Sie „HotelReviews_Free.csv“ herunter.](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Bei diesen Daten handelt es sich um gespeicherte Hotelrezensionen (von Kaggle.com) in einer CSV-Datei. Die Daten umfassen 19 Kundenfeedbacks für ein einzelnes Hotel. 

1. [Erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Azure Storage wird sowohl für die zu importierenden Rohinhalte als auch für den resultierenden Wissensspeicher verwendet.

   Wählen Sie den Kontotyp **StorageV2 (allgemein, Version 2)** aus.

1. Erstellen Sie in der Azure Storage-Ressource im **Speicherbrowser** einen Blobcontainer mit dem Namen **hotel-reviews**.

1. Wählen Sie oben auf der Seite **Hochladen** aus, um die Datei **HotelReviews-Free.csv** zu laden, die Sie im vorherigen Schritt heruntergeladen haben.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="Screenshot: Speicherbrowser mit hochgeladener Datei und linkem Navigationsbereich" border="true":::

1. Sie sind fast fertig mit dieser Ressource, aber bevor Sie diese Seiten verlassen, wählen Sie im linken Navigationsbereich **Zugriffsschlüssel** aus, um eine Verbindungszeichenfolge abzurufen, damit Sie diese Daten mit dem Indexer abrufen können.

1. Wählen Sie in **Zugriffsschlüssel** oben auf der Seite **Schlüssel anzeigen** aus, um die Verbindungszeichenfolgen einzublenden, und kopieren Sie dann die Verbindungszeichenfolge entweder für Schlüssel1 oder Schlüssel2.

   Die Verbindungszeichenfolge weist das folgende Format auf: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

## <a name="configure-requests"></a>Konfigurieren von Anforderungen

1. Laden Sie [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) von GitHub herunter, und dekomprimieren Sie die Datei. Das Repository enthält mehrere Sammlungen. Sie verwenden die Sammlung im Ordner „knowledge-store“.

1. Wählen Sie in Postman **File** > **Import** aus, um die Datei „KnowledgeStore.postman_collection.json“ zu importieren.

1. Wählen Sie die Registerkarte **Sammlungen** und dann die Schaltfläche **...** (Auslassungszeichen) aus.

1. Wählen Sie **Bearbeiten** aus.

   ![Postman-App mit Navigation](media/knowledge-store-create-rest/postman-edit-menu.png "Navigieren zum Bearbeitungsmenü in Postman")

1. Wählen Sie im Dialogfeld **Edit** (Bearbeiten) die Registerkarte **Variables** (Variablen) aus. 

Auf der Registerkarte **Variables** (Variablen) können Sie Werte hinzufügen, die von Postman jeweils eingefügt werden, wenn eine bestimmte Variable in doppelten Klammern erkannt wird. Beispielsweise ersetzt Postman das Symbol `{{admin-key}}` durch den aktuellen Wert, den Sie für `admin-key` festlegen. Postman führt die Ersetzung in URLs, Headern, im Anforderungstext usw. durch. 

Variablen werden für Azure-Dienste, Dienstverbindungen und Objektnamen definiert. Ersetzen Sie die Platzhalterwerte für Dienst und Verbindung durch die tatsächlichen Werte für Ihren Suchdienst und Ihr Speicherkonto. Sie finden diese Werte im Azure-Portal.

+ Um die Werte für `search-service-name` und `search-service-admin-key` zu ermitteln, wechseln Sie im Portal zum Dienst Azure Cognitive Search, und kopieren Sie die Werte von den Seiten **Übersicht** und **Schlüssel**.

+ Um die Werte für `storage-account-name` und `storage-account-connection-string` zu erhalten, überprüfen Sie die Seite **Zugriffsschlüssel**.

![Postman-App, Registerkarte mit den Variablen](media/knowledge-store-create-rest/postman-variables-window.png "Fenster mit den Postman-Variablen")

| Variable    | Ursprung |
|-------------|-----------------|
| `admin-key` | Auf der Seite **Schlüssel** des Azure Cognitive Search-Diensts  |
| `api-version` | Übernehmen Sie **2020-06-30**. |
| `datasource-name` | Übernehmen Sie **hotel-reviews-ds**. | 
| `indexer-name` | Übernehmen Sie **hotel-reviews-ixr**. | 
| `index-name` | Übernehmen Sie **hotel-reviews-ix**. | 
| `search-service-name` | Der Name des Azure Cognitive Search-Diensts. Wenn die URL `https://mySearchService.search.windows.net` ist, geben Sie den Wert `mySearchService` ein. | 
| `skillset-name` | Übernehmen Sie **hotel-reviews-ss**. | 
| `storage-account-name` | Azure-Speicherkontoname | 
| `storage-connection-string` | Wählen Sie im Speicherkonto auf der Registerkarte **Zugriffsschlüssel** oben auf der Seite **Schlüssel anzeigen** aus, und kopieren Sie dann **Schlüssel1** > **Verbindungszeichenfolge**. | 
| `storage-container-name` | Übernehmen Sie **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Überprüfen der Auflistung von Anforderungen in Postman

Wissensspeicher werden in Skillsets definiert, die wiederum an Indexer angefügt sind. Zum Erstellen eines Wissensspeichers müssen Sie alle Upstreamobjekte erstellen, einschließlich eines Index, einer Datenquelle, eines Skillsets und eines Indexers. Obwohl ein Index nicht mit einem Wissensspeicher verknüpft ist, benötigt ein Indexer ihn für die Ausführung. Daher erstellen Sie einen Index als Voraussetzung für den Indexer.

Beim Erstellen eines Wissensspeichers müssen Sie vier HTTP-Anforderungen stellen: 

+ **PUT-Anforderung zum Erstellen des Indexes**: Dieser Index enthält durchsuchbare Daten, die bei Abfragen an Ihren Suchdienst zurückgegeben werden.

+ **POST-Anforderung zum Erstellen der Datenquelle**: Diese Datenquelle stellt Verbindungsinformationen für den Indexer bereit. Der Indexer stellt eine Verbindung mit Ihrem Azure Storage-Konto her, um die Beispieldaten abzurufen.

+ **PUT-Anforderung zum Erstellen des Skillsets**: Das Skillset gibt die Anreicherungen an, die auf Ihre Daten angewendet werden. Außerdem gibt es die Struktur des Wissensspeichers an.

+ **PUT-Anforderung zum Erstellen des Indexers**: Beim Ausführen des Indexers werden die Daten gelesen, das Skillset angewendet, der Wissensspeicher in Azure Storage erstellt und die Ergebnisse gespeichert. Diese Anforderung muss zuletzt ausgeführt werden.

Die zuvor eingerichteten Variablen werden in den Headern und der URL verwendet. Der folgende Screenshot für die Anforderung „Index erstellen“ zeigt, wo diese Variablen in der Anforderung angezeigt werden.

![Screenshot: Postman-Schnittstelle für Header](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Alle Anforderungen in der Sammlung legen Header vom Typ `api-key` und `Content-type` fest. Diese sind erforderlich. Wenn von Postman eine Variable erkannt wird, wird sie als orangefarbener Text angezeigt (wie `{{admin-key}}` im obigen Screenshot). Falls die Variable falsch geschrieben ist, wird sie als roter Text dargestellt.
>

## <a name="create-an-index"></a>Erstellen eines Index

Verwenden Sie die [„Create Index“ (REST-API)](/rest/api/searchservice/create-index), um einen Suchindex für den Suchdienst zu erstellen. Ein Suchindex hat nichts mit einem Wissensspeicher zu tun, aber der Indexer verlangt, dass Sie einen erstellen. Der Suchindex enthält denselben Inhalt wie der Wissensspeicher. Wenn Sie Ihre Inhalte auf andere Weise erkunden möchten, können Sie diesen Index abfragen, indem Sie Abfragen an Ihren Suchdienst senden. 

Erstellen Sie den Index, indem Sie eine PUT-Anforderung an `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` senden. Das Indexschema wird im Textkörper der Abfrage bereitgestellt.

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

Diese Indexdefinition ist eine Kombination von Daten, die Sie dem Benutzer präsentieren möchten. Sie enthält Felder, die direkt der CSV-Datei zugeordnet sind (Name des Hotels, Bewertungsdatum usw.) und Felder, die vom Skillset erstellt werden (Stimmung, Schlüsselbegriffe, Sprache und übersetzter Text).

Wählen Sie **Send** (Senden) aus, um die PUT-Anforderung zu senden.

Als Status sollte `201 - Created` angezeigt werden. Wenn ein anderer Status angezeigt wird, sollten Sie im Bereich **Body** (Text) nach einer JSON-Antwort suchen, die eine Fehlermeldung enthält. Wenn Sie einen kostenlosen Suchdienst verwenden, stellen Sie sicher, dass Platz für die neuen Objekte vorhanden ist (der kostenlose Dienst erlaubt maximal drei).

An diesem Punkt wird der Index erstellt, aber nicht geladen. Das Importieren von Dokumenten erfolgt später, wenn Sie den Indexer ausführen. 

## <a name="create-a-data-source"></a>Erstellen einer Datenquelle

Verbinden Sie als Nächstes Azure Cognitive Search mit den Hoteldaten, die Sie im Blobspeicher gespeichert haben. Um die Datenquelle zu erstellen, senden Sie die POST-Anforderung [Datenquelle erstellen](/rest/api/searchservice/create-data-source) an `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. 

Navigieren Sie in Postman zur Anforderung **Create Datasource** (Datenquelle erstellen) und dann zum Bereich **Body** (Text). Der folgende Code sollte angezeigt werden:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Wählen Sie **Send** (Senden) aus, um die POST-Anforderung zu senden. 

## <a name="create-a-skillset"></a>Erstellen eines Skillsets 

Im nächsten Schritt wird das Skillset erstellt. Hiermit werden sowohl die anzuwendenden Erweiterungen als auch der Wissensspeicher angegeben, in dem die Ergebnisse gespeichert werden. Diese Anforderung sendet die PUT-Anforderung [Skillset erstellen](/rest/api/searchservice/create-skillset) an `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.

Es gibt zwei große Objekte der obersten Ebene: `skills` und `knowledgeStore`.

+ „skills“ ist das Skillset. Jedes Objekt innerhalb des `skills`-Objekts ist ein Anreicherungsdienst. Jeder Anreicherungsdienst verfügt über `inputs` und `outputs`. Für `LanguageDetectionSkill` wird für `targetName` das Ergebnis `Language` ausgegeben. Der Wert dieses Knotens wird von den meisten anderen Skills als Eingabe verwendet. Die Quelle ist `document/Language`. Die Fähigkeit, die Ausgabe eines Knotens als Eingabe für einen anderen zu nutzen, wird mit `ShaperSkill` noch besser ersichtlich. Darin wird angegeben, wie die Daten in die Tabellen im Wissensspeicher fließen.

+ „knowledgeStore“ enthält die Verbindungszeichenfolge für das Speicherkonto und eine Reihe von Projektionen. Jedes Projektionselement im Projektionsarray resultiert in einer Tabelle in Azure Storage. Spalten in der Tabelle enthalten generierte Spalten, die für Kreuzverknüpfungs- und Inhaltsfelder verwendet werden. Sie enthalten auch Inhaltsfelder, die während der Anreicherung erstellt wurden.

  Projektionen akzeptieren Datenformen als Eingabe. Sie können die Formen über einen Skill für Shaper bereitstellen, die Skillausgabe einer Projektion zuordnen oder Inlineformen in jedem Projektionselement definieren. 

Wählen Sie zum Generieren des Skillsets in Postman die Schaltfläche **Send** (Senden) aus, um die PUT-Anforderung zu senden:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>Erstellen eines Indexers

Der letzte Schritt ist die Anforderung [Indexer erstellen](/rest/api/searchservice/create-indexer). Der Indexer liest die Daten und aktiviert das Skillset. Die Definition des Indexers verweist auf verschiedene andere Ressourcen, die Sie bereits erstellt haben: die Datenquelle, den Index und das Skillset. 

+ Das `parameters/configuration`-Objekt steuert die Erfassung der Daten durch den Indexer. In diesem Fall befinden sich die Eingabedaten in einer einzigen CSV-Datei, die über eine Kopfzeile und durch Trennzeichen getrennte Werte verfügt. 

+ Feldzuordnungen erstellen „AzureSearch_DocumentKey“, einen eindeutigen Bezeichner jedes Dokuments, der (basierend auf dem Speicherpfad der Metadaten) vom Blobindexer generiert wird. 

+ Ausgabefeldzuordnungen geben an, wie angereicherte Felder Feldern in einem Suchindex zugeordnet werden. Ausgabefeldzuordnungen werden nicht in Wissensspeichern verwendet (denn Wissensspeicher nutzen Formen und Projektionen, um die physischen Datenstrukturen auszudrücken).

Wählen Sie in Postman **Send** (Senden) aus, um den Indexer zu erstellen und auszuführen. Datenimport, Skillsetausführung und Wissensspeichererstellung erfolgen in diesem Schritt.

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>Status überprüfen

Nach dem Senden der einzelnen Anforderungen sollte der Dienst mit der Erfolgsmeldung 201 antworten. Wenn Sie Fehlermeldungen erhalten, überprüfen Sie Ihre Variablen, und vergewissern Sie sich, dass der Dienst Platz für den neuen Index, den Indexer, die Datenquelle und das Skillset bereithält (der Free-Tarif ist auf jeweils drei davon beschränkt).

Navigieren Sie im Azure-Portal zur Seite **Übersicht** des Azure Cognitive Search-Diensts. Wählen Sie die Registerkarte **Indexer** und dann **hotels-reviews-ixr** aus. Innerhalb von ein oder zwei Minuten sollte sich der Status von „In Bearbeitung“ ohne Fehler und Warnungen in „Erfolgreich“ ändern.

## <a name="check-tables-in-storage-browser"></a>Überprüfen von Tabellen im Speicherbrowser

Wechseln Sie im Azure-Portal zu Ihrem Azure Storage-Konto, und zeigen Sie im **Speicherbrowser** die neuen Tabellen an. Es sollten sechs Tabellen angezeigt werden, und zwar eine für jede im Skillset definierte Projektion.

Jede Tabelle wird mit den IDs generiert, die für die Kreuzverknüpfung der Tabellen in Abfragen erforderlich sind. Scrollen Sie beim Öffnen einer Tabelle über diese Felder, um die von der Pipeline hinzugefügten Inhaltsfelder anzuzeigen.

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Screenshot: Wissensspeichertabellen im Speicherbrowser" border="true":::

In dieser exemplarischen Vorgehensweise besteht der Wissensspeicher aus einer Reihe von Tabellen, die verschiedene Möglichkeiten zur Gestaltung und Strukturierung einer Tabelle aufzeigen. Die erste Gruppe mit drei Tabellen zeigt, wie die Ausgabe eines Skills für Shaper die Spalten und Zeilen festlegt. Die Tabellen 4 bis 6 werden aus Anweisungen zur Inline-Gestaltung erstellt, die in die Projektion selbst eingebettet sind. Bei beiden Ansätzen erzielen Sie das gleiche Ergebnis.

| Tabelle | BESCHREIBUNG |
|-------|-------------|
| hotelReviews1Document | Enthält Felder, die der CSV-Datei entnommen werden, z. B. reviews_date und reviews_text. |
| hotelReviews2Pages | Enthält angereicherte Felder, die vom Skillset erstellt wurden, z. B. Stimmungsbewertung und übersetzter Text. |
| hotelReviews3KeyPhrases | Enthält eine lange Liste nur der Schlüsselbegriffe. |
| hotelReviews4InlineProjectionDocument | Alternative zur ersten Tabelle, wobei die Inlinegestaltung anstelle des Skills für Shaper verwendet wird, um Daten für die Projektion zu strukturieren. |
| hotelReviews5InlineProjectionPages | Alternative zur zweiten Tabelle mit Inlinegestaltung. |
| hotelreviews6InlineProjectionKeyPhrases | Alternative zur dritten Tabelle mit Inlinegestaltung. |

## <a name="clean-up"></a>Bereinigen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie am Ende eines Projekts prüfen, ob Sie die Ressourcen, die Sie erstellt haben, noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben.

> [!TIP]
> Wenn Sie diese Übung wiederholen oder eine andere exemplarische Vorgehensweise für die KI-Anreicherung ausprobieren möchten, löschen Sie den Indexer **hotel-reviews-idxr** und die zugehörigen Objekte, um sie neu zu erstellen. Durch das Löschen des Indexers wird der Zähler für kostenlose Transaktionen pro Tag auf Null zurückgesetzt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Daten mit Cognitive Services angereichert und die Ergebnisse in einen Wissensspeicher projiziert haben, können Sie mit dem Speicherbrowser oder anderen Apps Ihr angereichertes Dataset erkunden.

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie diesen Wissensspeicher mit dem Speicherbrowser untersuchen:

> [!div class="nextstepaction"]
> [Anzeige mit dem Speicherbrowser](knowledge-store-view-storage-explorer.md)
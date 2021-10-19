---
title: Importieren von Daten in einen Suchindex über das Azure-Portal
titleSuffix: Azure Cognitive Search
description: Erfahren Sie mehr über den Datenimport-Assistenten im Azure-Portal, der zum Erstellen und Laden eines Index verwendet wird, und rufen Sie optional die KI-Anreicherung auf, indem Sie integrierte Qualifikationen zur Verarbeitung natürlicher Sprache, Übersetzung, OCR und Bildanalyse verwenden.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 9fe443422bac06ccb934a34799bc29be29c45cb5
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714682"
---
# <a name="import-data-wizard-in-azure-cognitive-search"></a>Datenimport-Assistent in Azure Cognitive Search

Der **Datenimport-Assistent** in Azure-Portal erstellt mehrere Objekte, die für die Indizierung und KI-Anreicherung in einem Suchdienst verwendet werden. Wenn Sie Azure Cognitive Search noch nicht kennen, ist dies eine der leistungsstärksten Funktionen, die Ihnen zur Verfügung stehen. Mit minimalem Aufwand können Sie eine Indizierungs- oder Anreicherungspipeline erstellen, die den Großteil der Funktionen von Azure Cognitive Search nutzt.

Falls Sie den Assistenten für Proof of Concept-Tests verwenden, wird in diesem Artikel auch die interne Arbeit des Assistenten erläutert, damit Sie ihn effektiver verwenden können.

Es handelt sich nicht um eine Schritt-für-Schritt-Anleitung. Hilfe zur Verwendung des Assistenten mit integrierten Beispieldaten finden Sie unter [Schnellstart: Erstellen eines Suchindexes](search-get-started-portal.md) oder [Schnellstart: Erstellen einer Textübersetzung und eines Entitäts-Skillsets](cognitive-search-quickstart-blob.md).

## <a name="starting-the-wizard"></a>Starten des Assistenten

Öffnen Sie im [Azure-Portal](https://portal.azure.com) im Dashboard die Seite mit dem Suchdienst, oder [suchen Sie Ihren Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in der Liste mit den Diensten. Klicken Sie am oberen Rand der Übersichtsseite des Diensts auf **Daten importieren**.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

Der Assistent wird vollständig erweitert im Browserfenster geöffnet, damit Sie mehr Platz zum Arbeiten haben. Mehrere Seiten sind recht dicht bepackt.

Sie können den **Import-Assistenten** auch über andere Azure-Dienste wie etwa Azure Cosmos DB, Azure SQL-Datenbank, SQL Managed Instance und Azure Blob Storage starten. Suchen Sie auf der Übersichtsseite des Diensts im linken Navigationsbereich nach **Add Azure Cognitive Search** (Kognitive Azure-Suche hinzufügen).

## <a name="objects-created-by-the-wizard"></a>Vom Assistenten erstellte Objekte

Der Assistent gibt die Objekte in der folgenden Tabelle aus. Nachdem die Objekte erstellt wurden, können Sie ihre JSON-Definitionen im Portal überprüfen oder über Code aufrufen.

| Object | BESCHREIBUNG | 
|--------|-------------|
| [Indexer](/rest/api/searchservice/create-indexer)  | Ein Konfigurationsobjekt, das eine Datenquelle, einen Zielindex, eine optionale Qualifikationsgruppe, einen optionalen Zeitplan und optionale Konfigurationseinstellungen für die Fehlerbehandlung und Base-64-Codierung festlegt. |
| [Data Source](/rest/api/searchservice/create-data-source)  | Speichert die Verbindungsinformationen zu den Quelldaten, einschließlich der Anmeldeinformationen. Ein Datenquellenobjekt wird ausschließlich mit Indexern verwendet. | 
| [Index](/rest/api/searchservice/create-index) | Physische Datenstruktur, die für die Volltextsuche und andere Abfragen verwendet wird. | 
| [Qualifikationsgruppe](/rest/api/searchservice/create-skillset) | Optional. Ein kompletter Satz von Anweisungen zum Bearbeiten, Transformieren und Strukturieren von Inhalten, einschließlich der Analyse und Extraktion von Informationen aus Bilddateien. Mit Ausnahme sehr einfacher und eingeschränkter Strukturen umfasst eine Qualifikationsgruppe einen Verweis auf eine Cognitive Services-Ressource, die Anreicherungen bereitstellt. | 
| [Wissensspeicher](knowledge-store-concept-intro.md) | Optional. Speichert Ausgaben aus einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) in Tabellen und Blobs in Azure Storage zur unabhängigen Analyse oder Downstreamverarbeitung. |

## <a name="benefits-and-limitations"></a>Vorteile und Einschränkungen

Bevor Sie Code schreiben, können Sie den Assistenten für Prototyping und Proof of Concept-Tests verwenden. Der Assistent stellt eine Verbindung mit externen Datenquellen her, führt ein Sampling der Daten aus, um einen ersten Index zu erstellen, und importiert die Daten dann als JSON-Dokumente in einen Index in der kognitiven Azure-Suche. 

Wenn Sie Skillsets auswerten, verarbeitet der Assistent alle Ausgabefeldzuordnungen und fügt Hilfsfunktionen hinzu, um nutzbare Objekte zu erstellen. Die Textaufteilung wird hinzugefügt, wenn Sie einen Analysemodus angeben. Textzusammenführung wird hinzugefügt, wenn Sie Bildanalyse ausgewählt haben, damit der Assistent Textbeschreibungen mit Bildinhalten wieder zusammenführen kann. Shaper-Skills wurden hinzugefügt, um gültige Projektionen zu unterstützen, wenn Sie die Option Wissensspeicher ausgewählt haben. Alle oben genannten Aufgaben haben eine Lernkurve. Wenn Sie mit der Anreicherung noch nicht fertig sind, können Sie mit der Möglichkeit, diese Schritte für Sie zu verarbeiten, den Wert eines Skills messen, ohne viel Zeit und Aufwand investieren zu müssen.

Beim Sampling handelt es sich um den Prozess, mit dem ein Indexschema abgeleitet wird. Es gelten einige Einschränkungen. Wenn die Datenquelle erstellt wurde, entscheidet der Assistent anhand einer Stichprobe von Dokumenten, welche Spalten Teil der Datenquelle sind. Nicht alle Dateien werden gelesen, da dies bei sehr großen Datenquellen möglicherweise Stunden dauern könnte. Bei einer Auswahl von Dokumenten werden Quellmetadaten verwendet, z.B. Feldname oder Typ, um eine Feldsammlung in einem Indexschema zu erstellen. Abhängig von der Komplexität der Quelldaten müssen Sie ggf. das ursprüngliche Schema zum Zwecke der Genauigkeit überarbeiten oder zur Vollständigkeit erweitern. Sie können die Änderungen auf der Indexdefinitionsseite inline vornehmen.

Insgesamt sind die Vorteile der Verwendung des Assistenten klar: Sofern die Anforderungen erfüllt sind, können Sie innerhalb weniger Minuten das Prototyping für einen abfragbaren Index vornehmen. Einige komplexe Schritte der Indizierung, z.B. das Serialisieren von Daten als JSON-Dokumente, werden vom Assistenten erledigt.

Der Assistent ist nicht ohne Einschränkungen. Die Constraints sind wie folgt zusammengefasst:

+ Der Assistent unterstützt keine Iteration oder Wiederverwendung. Bei jedem Pass-Through erstellt der Assistent einen neue Index, eine neue Qualifikationsgruppe und eine neue Indexer-Konfiguration. Nur Datenquellen können persistent gespeichert und innerhalb des Assistenten wieder verwendet werden. Um andere Objekte zu bearbeiten oder zu verfeinern, löschen Sie entweder die Objekte, und starten Sie von vorn, oder verwenden Sie die REST-APIs oder das .NET SDK, um die Strukturen zu ändern.

+ Der Quellinhalt muss sich in einer [unterstützten Datenquelle](search-indexer-overview.md#supported-data-sources) befinden.

+ Das Sampling erfolgt über eine Teilmenge der Quelldaten. Bei großen Datenquellen ist es möglich, dass der Assistent Felder auslässt. Sie müssen das Schema möglicherweise erweitern oder die abgeleiteten Datentypen korrigieren, wenn das Sampling unzureichend ist.

+ Die KI-Anreicherung, wie Sie im Portal verfügbar gemacht wird, ist auf eine Teilmenge integrierter Qualifikationen beschränkt. 

+ Ein [Wissensspeicher](knowledge-store-concept-intro.md), der vom Assistenten erstellt werden kann, ist auf einige Standardprojektionen beschränkt und verwendet eine Standardnamenskonvention. Wenn Sie Namen oder Projektionen anpassen möchten, müssen Sie den Wissensspeicher über REST oder die SDKs erstellen.

## <a name="workflow"></a>Workflow

Der Assistent ist in vier Hauptschritte organisiert:

1. Herstellen einer Verbindung mit einer unterstützten Azure-Datenquelle.

1. Erstellen eines Indexschemas, das durch Sampling von Quelldaten abgeleitet wird.

1. Optional: Hinzufügen von KI-Anreicherungen, um Inhalte und Strukturen zu extrahieren oder zu generieren. Eingaben zum Erstellen eines Wissensspeichers werden in diesem Schritt gesammelt.

1. Führen Sie den Assistenten aus, um Objekte zu erstellen, Daten zu laden, einen Zeitplan und andere Konfigurationsoptionen festzulegen.

<a name="data-source-inputs"></a>

### <a name="data-source-configuration-in-the-wizard"></a>Datenquellenkonfiguration im Assistenten

Der **Datenimport**-Assistent stellt eine Verbindung mit einer externen [unterstützten Datenquelle](search-indexer-overview.md#supported-data-sources) mithilfe der von Indexern der kognitiven Azure-Suche bereitgestellten internen Logik her. Diese sind für das Sampling der Quelle, zum Lesen von Metadaten, zum Entschlüsseln von Dokumenten, um Inhalt und Struktur zu lesen, sowie zum Serialisieren von Inhalten als JSON für den nachfolgenden Import in die kognitive Azure-Suche ausgestattet.

Nicht alle Datenquellen der Vorschauversion sind im Assistenten garantiert verfügbar. Da jede Datenquelle das Potenzial hat, nachgelagerte Änderungen vorzunehmen, wird der Datenquellenliste nur dann eine Vorschaudatenquelle hinzugefügt, wenn sie alle Erfahrungen im Assistenten vollständig unterstützt, z. B. Skillsetdefinition und Indexschemarückschluss.

Sie können Daten nur aus einer einzelnen Tabelle, Datenbanksicht oder entsprechenden Datenstruktur importieren, die Struktur kann jedoch hierarchische oder geschachtelte Unterstrukturen enthalten. Weitere Informationen finden Sie unter [Modellieren komplexer Datentypen in Azure Search](search-howto-complex-data-types.md).

### <a name="skillset-configuration-in-the-wizard"></a>Skillsetkonfiguration im Assistenten

Die Skillsetkonfiguration erfolgt nach der Datenquellendefinition, da der Typ der Datenquelle die Verfügbarkeit bestimmter integrierter Qualifikationen informiert. Insbesondere wenn Sie Dateien aus Blob Storage indizieren, bestimmt Ihre Wahl des Analysemodus dieser Dateien, ob die Stimmungsanalyse verfügbar ist.

Der Assistent fügt die Qualifikationen hinzu, die Sie auswählen, aber er fügt auch andere Qualifikationen hinzu, die zum Erzielen eines erfolgreichen Ergebnisses erforderlich sind. Wenn Sie beispielsweise einen Wissensspeicher angeben, fügt der Assistent eine Shaper-Qualifikation hinzu, um Projektionen (oder physische Datenstrukturen) zu unterstützen.

Skillsets sind optional, und unten auf der Seite befindet sich eine Schaltfläche zum Überspringen, wenn Sie keine KI-Anreicherung wünschen.

<a name="index-definition"></a>

### <a name="index-schema-configuration-in-the-wizard"></a>Indexschemakonfiguration im Assistenten

Der Assistent verwendet Stichproben ihrer Datenquelle, um die Felder und den Feldtyp zu erkennen. Je nach Datenquelle können auch Felder für die Indizierung von Metadaten angeboten werden.

Da die Stichprobenentnahme unpräzise ist, überprüfen Sie den Index auf Folgendes:

1. Ist die Feldliste vollständig? Wenn Ihre Datenquelle Felder enthält, die bei der Stichprobenentnahme nicht verwendet wurden, können Sie manuell alle neuen Felder hinzufügen, die bei der Stichprobenentnahme übersehen wurden, und alle Felder entfernen, die keinen Mehrwert zu einer Suchfunktion hinzufügen oder nicht in einem [Filterausdruck](search-query-odata-filter.md) oder [Bewertungsprofil](index-add-scoring-profiles.md) verwendet werden.

1. Ist der Datentyp für die eingehenden Daten geeignet? Die kognitive Azure-Suche unterstützt die [Datentypen des Entity Data Model (EDM)](/rest/api/searchservice/supported-data-types). Für Azure SQL-Daten gibt es ein [Zuordnungsdiagramm](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping), in dem äquivalente Werte aufgeführt sind. Weitere Hintergrundinformationen finden Sie unter [Feldzuordnungen und Transformationen](search-indexer-field-mappings.md).

1. Verfügen Sie über ein Feld, das als *Schlüssel* fungieren kann? Dieses Feld muss vom Typ „Edm.string“ sein und ein Dokument eindeutig identifizieren. Bei relationalen Daten kann dieses einem Primärschlüssel zugeordnet werden. Für Blobs kann es `metadata-storage-path` sein. Wenn Feldwerte Leerzeichen oder Bindestriche enthalten, muss im Schritt **Indexer erstellen** unter **Erweiterte Optionen** die Option **Base64-codierte Schlüssel** festgelegt werden, um die Überprüfung für diese Zeichen zu unterdrücken.

1. Legen Sie Attribute fest, um zu bestimmen, wie das Feld in einem Index verwendet wird. 

   Nehmen Sie sich bei diesem Schritt Zeit, da Attribute den physischen Ausdruck von Feldern im Index bestimmen. Wenn Sie Attribute später ändern möchten, selbst programmgesteuert, müssen Sie fast immer den Index löschen und neu erstellen. Die [Auswirkungen](search-what-is-an-index.md#index-size) von Kernattributen wie **Searchable** und **Retrievable** auf den Speicher sind vernachlässigbar. Die Aktivierung von Filtern und die Verwendung von Vorschlagsfunktionen erhöht die Speicheranforderungen. 

   + **Durchsuchbar** ermöglicht eine Volltextsuche. Jedes Feld, das in Freiformabfragen oder in Abfrageausdrücken verwendet wird, muss über dieses Attribut verfügen. Für jedes als **Durchsuchbar** markierte Feld werden invertierte Indizes erstellt.

   + **Abrufbar** gibt das Feld in Suchergebnissen zurück. Jedes Feld, das Inhalt für Suchergebnisse bereitstellt, muss über dieses Attribut verfügen. Das Festlegen dieses Felds wirkt sich nicht nennenswert auf die Indexgröße aus.

   + **Filterbar** ermöglicht die Verwendung von Verweisen auf das Feld in Filterausdrücken. Jedes Feld, das in einem Ausdruck vom Typ **$filter** verwendet wird, muss über dieses Attribut verfügen. Filterausdrücke werden für exakte Übereinstimmungen verwendet. Da Textzeichenfolgen intakt bleiben, ist zusätzlicher Speicher für die ausführlichen Inhalte erforderlich.

   + **Facettierbar** ermöglicht die Verwendung des Felds in einer Facettennavigation. Als **Facettierbar** können nur Felder markiert werden, die auch als **Filterbar** markiert sind.

   + **Sortierbar** ermöglicht die Verwendung des Felds in einer Sortierung. Jedes Feld, das in einem Ausdruck vom Typ **$Orderby** verwendet wird, muss über dieses Attribut verfügen.

1. Benötigen Sie [lexikalische Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)? Für Felder vom Typ „Edm.string“, die **Searchable** sind, können Sie ein **Analysetool** festlegen, wenn Sie spracherweiterte Indizierung und Abfrage benötigen. 

   Standardmäßig wird die *Standardanalyse von Lucene* verwendet. Sie können aber auch *Englisch - Microsoft* auswählen, wenn Sie das Analysetool von Microsoft für eine erweiterte lexikalische Verarbeitung (etwa zur Auflösung unregelmäßiger Nomen oder Verben) verwenden möchten. Im Portal können nur Sprachanalysen angegeben werden. Die Verwendung benutzerdefinierter oder sprachfremder Analysetools wie Schlüsselwort, Muster usw. muss programmgesteuert erfolgen. Weitere Informationen zu Analysetools finden Sie unter [Hinzufügen von Sprachanalysen](search-language-support.md).

1. Benötigen Sie Vorschlagsfunktionen in Form von AutoVervollständigen oder vorgeschlagenen Ergebnissen? Aktivieren Sie das Kontrollkästchen **Vorschlagsfunktion**, um für ausgewählte Felder [Eingabevorschläge für Abfragen und AutoVervollständigen](index-add-suggesters.md) zu aktivieren. Vorschlagsfunktionen erhöhen die Anzahl der tokenisierten Begriffe in Ihrem Index und verbrauchen somit mehr Speicher.

### <a name="indexer-configuration-in-the-wizard"></a>Indexerkonfiguration im Assistenten

Auf der letzten Seite des Assistenten werden Benutzereingaben für die Indexerkonfiguration gesammelt. Sie können [einen Zeitplan angeben](search-howto-schedule-indexers.md) und andere Optionen festlegen, die je nach Datenquellentyp variieren.

Intern richtet der Assistent auch Folgendes ein, was im Indexer erst nach Erstellen sichtbar ist:

+ [Feldzuordnungen](search-indexer-field-mappings.md) zwischen Datenquelle und Index
+ [Ausgabefeldzuordnungen](cognitive-search-output-field-mapping.md) zwischen der Qualifikationsausgabe und einem Index

## <a name="next-steps"></a>Nächste Schritte

Am besten können Sie sich mit den Vorteilen und Einschränkungen des Assistenten vertraut machen, indem Sie ihn schrittweise zu durchlaufen. Der folgende Schnellstart führt Sie durch die einzelnen Schritte.

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Suchindexes über das Azure-Portal](search-get-started-portal.md)
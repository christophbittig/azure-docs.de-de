---
title: Importieren von Daten in einen Suchindex über das Azure-Portal
titleSuffix: Azure Cognitive Search
description: In diesem Artikel erfahren Sie, wie Sie den Datenimport-Assistenten im Azure-Portal verwenden, um Azure-Daten aus Azure Cosmos DB, Blob Storage, Table Storage, SQL-Datenbank, SQL Managed Instance und SQL Server auf Azure-VMs zu durchforsten.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed3f02f6bf4c9a7b53a63f31163663c59c0edc88
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451023"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Datenimport-Assistent für die kognitive Azure-Suche

Im Azure-Portal steht im Dashboard der kognitiven Azure-Suche der **Datenimport**-Assistent für Prototyping und Laden eines Index zur Verfügung. In diesem Artikel werden die Vorteile und Einschränkungen der Verwendung des Assistenten, Eingaben und Ausgaben sowie einige Verwendungsinformationen behandelt. Praktische Anleitungen zum Durchlaufen des Assistenten in Schritten mithilfe integrierter Beispieldaten finden Sie im Schnellstart [Erstellen eines Index der kognitiven Azure-Suche im Azure-Portal](search-get-started-portal.md).

Folgende Vorgänge werden von diesem Assistenten ausgeführt:

1 – Herstellen einer Verbindung mit einer unterstützten Azure-Datenquelle.

2 – Erstellen eines Indexschemas, das durch Sampling von Quelldaten abgeleitet wird.

3 – Optional: Hinzufügen von KI-Anreicherungen, um Inhalte und Strukturen zu extrahieren oder zu generieren.

4 – Führen Sie den Assistenten aus, um Objekte zu erstellen, Daten zu importieren, einen Zeitplan und andere Konfigurationsoptionen festzulegen.

Der Assistent gibt eine Reihe von Objekten aus, die in Ihrem Suchdienst gespeichert werden, und auf die Sie programmatisch oder in anderen Tools zugreifen können.

## <a name="advantages-and-limitations"></a>Vorteile und Einschränkungen

Bevor Sie Code schreiben, können Sie den Assistenten für Prototyping und Proof of Concept-Tests verwenden. Der Assistent stellt eine Verbindung mit externen Datenquellen her, führt ein Sampling der Daten aus, um einen ersten Index zu erstellen, und importiert die Daten dann als JSON-Dokumente in einen Index in der kognitiven Azure-Suche. 

Beim Sampling handelt es sich um den Prozess, mit dem ein Indexschema abgeleitet wird. Es gelten einige Einschränkungen. Wenn die Datenquelle erstellt wurde, entscheidet der Assistent anhand einer Stichprobe von Dokumenten, welche Spalten Teil der Datenquelle sind. Nicht alle Dateien werden gelesen, da dies bei sehr großen Datenquellen möglicherweise Stunden dauern könnte. Bei einer Auswahl von Dokumenten werden Quellmetadaten verwendet, z.B. Feldname oder Typ, um eine Feldsammlung in einem Indexschema zu erstellen. Abhängig von der Komplexität der Quelldaten müssen Sie ggf. das ursprüngliche Schema zum Zwecke der Genauigkeit überarbeiten oder zur Vollständigkeit erweitern. Sie können die Änderungen auf der Indexdefinitionsseite inline vornehmen.

Insgesamt sind die Vorteile der Verwendung des Assistenten klar: Sofern die Anforderungen erfüllt sind, können Sie innerhalb weniger Minuten das Prototyping für einen abfragbaren Index vornehmen. Einige komplexe Schritte der Indizierung, z.B. das Bereitstellen von Daten als JSON-Dokumente, werden vom Assistenten erledigt.

Die bekannten Einschränkungen lassen sich wie folgt zusammenfassen:

+ Der Assistent unterstützt keine Iteration oder Wiederverwendung. Bei jedem Pass-Through erstellt der Assistent einen neue Index, eine neue Qualifikationsgruppe und eine neue Indexer-Konfiguration. Nur Datenquellen können persistent gespeichert und innerhalb des Assistenten wieder verwendet werden. Zum Bearbeiten oder Verfeinern anderer Objekte müssen Sie die REST-APIs oder das .NET SDK verwenden, um die Strukturen abzurufen und zu ändern.

+ Der Quellinhalt muss sich in einer unterstützten Azure-Datenquelle befinden.

+ Das Sampling erfolgt über eine Teilmenge der Quelldaten. Bei großen Datenquellen ist es möglich, dass der Assistent Felder auslässt. Sie müssen das Schema möglicherweise erweitern oder die abgeleiteten Datentypen korrigieren, wenn das Sampling unzureichend ist.

+ Die KI-Anreicherung, wie Sie im Portal verfügbar gemacht wird, ist auf einige integrierte Qualifikationen beschränkt. 

+ Ein [Wissensspeicher](knowledge-store-concept-intro.md), der vom Assistenten erstellt werden kann, ist auf einige Standardprojektionen beschränkt. Wenn Sie vom Assistenten erstellte angereicherte Dokumente speichern möchten, weisen der Blob-Container und die Tabellen Standardnamen und -strukturen auf.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Datenquelleneingabe

Der **Datenimport**-Assistent stellt eine Verbindung mit einer externen Datenquelle mithilfe der von Indexern der kognitiven Azure-Suche bereitgestellten internen Logik her. Diese sind für das Sampling der Quelle, zum Lesen von Metadaten, zum Entschlüsseln von Dokumenten, um Inhalt und Struktur zu lesen, sowie zum Serialisieren von Inhalten als JSON für den nachfolgenden Import in die kognitive Azure-Suche ausgestattet.

Sie können Daten nur aus einer einzelnen Tabelle, Datenbanksicht oder entsprechenden Datenstruktur importieren, die Struktur kann jedoch hierarchische oder geschachtelte Unterstrukturen enthalten. Weitere Informationen finden Sie unter [Modellieren komplexer Datentypen in Azure Search](search-howto-complex-data-types.md).

Diese einzelne Tabelle oder Sicht muss vor dem Ausführen des Assistenten erstellt werden und über Inhalt verfügen. Aus offensichtlichen Gründen ist es nicht sinnvoll, den **Datenimport**-Assistenten für eine leere Datenquelle auszuführen.

|  Auswahl | BESCHREIBUNG |
| ---------- | ----------- |
| **Vorhandene Datenquelle** |Wenn Sie in Ihrem Suchdienst bereits Indexer definiert haben, verfügen Sie möglicherweise über eine vorhandene Datenquellendefinition, die Sie wiederverwenden können. In der kognitiven Azure-Suche werden Datenquellenobjekte nur von Indexern verwendet. Sie können Datenquellenobjekte programmgesteuert oder über den **Datenimport**-Assistenten erstellen und bei Bedarf wiederverwenden.|
| **Beispiele**| Die kognitive Azure-Suche umfasst zwei integrierte Beispieldatenquellen, die in Tutorials und Schnellstarts verwendet werden: eine SQL-Immobiliendatenbank und eine auf Cosmos DB gehostete Hoteldatenbank. Eine exemplarische Vorgehensweise, die auf dem Hotels-Beispiel basiert, finden Sie im Schnellstart [Erstellen eines Indexes im Azure-Portal](search-get-started-portal.md). |
| [**Azure SQL-Datenbank oder SQL Managed Instance**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Dienstname, Anmeldeinformationen für einen Datenbankbenutzer mit Leseberechtigung und ein Datenbankname können entweder auf der Seite oder über eine ADO.NET-Verbindungszeichenfolge angegeben werden. Wenn Sie Eigenschaften anzeigen oder anpassen möchten, verwenden Sie die Verbindungszeichenfolgen-Option. <br/><br/>Die Tabelle oder Sicht, die das Rowset bereitstellt, muss auf der Seite angegeben werden. Diese Option wird nach erfolgreicher Verbindungsherstellung in einer Dropdownliste angezeigt.|
| **SQL Server auf virtuellen Azure-Computern** |Geben Sie einen vollqualifizierten Dienstnamen, eine Benutzer-ID, ein Kennwort und eine Datenbank als Verbindungszeichenfolge an. Um diese Datenquelle verwenden zu können, müssen Sie zuvor ein Zertifikat im lokalen Speicher installieren haben, das die Verbindung verschlüsselt. Eine Anleitung finden Sie unter [SQL-VM-Verbindung mit der kognitiven Azure-Suche](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Die Tabelle oder Sicht, die das Rowset bereitstellt, muss auf der Seite angegeben werden. Diese Option wird nach erfolgreicher Verbindungsherstellung in einer Dropdownliste angezeigt. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Zu den erforderlichen Angaben gehören das Konto, die Datenbank und die Sammlung. Alle Dokumente in der Sammlung werden in den Index aufgenommen. Sie können eine Abfrage zum Vereinfachen oder Filtern des Rowsets definieren oder die Abfrage leer lassen. Eine Abfrage ist in diesem Assistenten nicht erforderlich.|
| [**Azure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |Zu den erforderlichen Angaben gehören das Speicherkonto und ein Container. Optional: Wenn zu Gruppierungszwecken für Blobnamen eine virtuelle Benennungskonvention verwendet wird, können Sie den virtuellen Verzeichnisteil des Namens als Ordner unter dem Container angeben. Weitere Informationen finden Sie unter [Indizieren von Dokumenten in Azure Blob Storage mit Azure Search](search-howto-indexing-azure-blob-storage.md). |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |Zu den erforderlichen Angaben gehören das Speicherkonto und ein Tabellenname. Optional können Sie eine Abfrage zum Abrufen einer Teilmenge der Tabellen angeben. Weitere Informationen finden Sie unter [Indizieren von Azure Table Storage mit Azure Search](search-howto-indexing-azure-tables.md). |

## <a name="wizard-output"></a>Ausgabe des Assistenten

Im Hintergrund erstellt und konfiguriert der Assistent die folgenden Objekte, und ruft sie auf. Nachdem der Assistent ausgeführt wurde, finden Sie seine Ausgabe auf den Portalseiten. Die Übersichtsseite Ihres Diensts enthält Listen mit Indizes, Indexern, Datenquellen und Qualifikationsgruppen. Indexdefinitionen können mit vollständigem JSON-Code im Portal angezeigt werden. Für andere Definitionen können Sie die [REST-API](/rest/api/searchservice/) verwenden, um bestimmte Objekte mit GET abzurufen.

| Object | BESCHREIBUNG | 
|--------|-------------|
| [Data Source](/rest/api/searchservice/create-data-source)  | Speichert die Verbindungsinformationen zu den Quelldaten, einschließlich der Anmeldeinformationen. Ein Datenquellenobjekt wird ausschließlich mit Indexern verwendet. | 
| [Index](/rest/api/searchservice/create-index) | Physische Datenstruktur, die für die Volltextsuche und andere Abfragen verwendet wird. | 
| [Qualifikationsgruppe](/rest/api/searchservice/create-skillset) | Ein kompletter Satz von Anweisungen zum Bearbeiten, Transformieren und Strukturieren von Inhalten, einschließlich der Analyse und Extraktion von Informationen aus Bilddateien. Mit Ausnahme sehr einfacher und eingeschränkter Strukturen umfasst eine Qualifikationsgruppe einen Verweis auf eine Cognitive Services-Ressource, die Anreicherungen bereitstellt. Optional kann sie auch eine Wissensspeicherdefinition enthalten.  | 
| [Indexer](/rest/api/searchservice/create-indexer)  | Ein Konfigurationsobjekt, das eine Datenquelle, einen Zielindex, eine optionale Qualifikationsgruppe, einen optionalen Zeitplan und optionale Konfigurationseinstellungen für die Fehlerbehandlung und Base-64-Codierung festlegt. |

## <a name="how-to-start-the-wizard"></a>Starten des Assistenten

Der Datenimport-Assistent wird über die Befehlsleiste auf der Seite „Übersicht“ des Diensts gestartet.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) im Dashboard die Seite mit dem Suchdienst, oder [suchen Sie Ihren Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in der Liste mit den Diensten.

1. Klicken Sie am oberen Rand der Übersichtsseite des Diensts auf **Daten importieren**.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

Sie können den **Import-Assistenten** auch über andere Azure-Dienste wie etwa Azure Cosmos DB, Azure SQL-Datenbank, SQL Managed Instance und Azure Blob Storage starten. Suchen Sie auf der Übersichtsseite des Diensts im linken Navigationsbereich nach **Add Azure Cognitive Search** (Kognitive Azure-Suche hinzufügen).

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Bearbeiten oder Beenden eines Indexschemas im Assistenten

Der Assistent generiert einen unvollständigen Index, der mit Dokumenten aus der Eingabedatenquelle aufgefüllt wird. Für einen funktionsfähigen Index müssen die folgenden Elemente definiert sein:

1. Ist die Feldliste vollständig? Fügen Sie neue Felder hinzu, die beim Sampling ausgelassen wurden, und entfernen Sie Felder, die keinen Wert für die Sucherfahrung bieten oder in keinem [Filterausdruck](search-query-odata-filter.md) oder [Bewertungsprofil](index-add-scoring-profiles.md) verwendet werden.

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


## <a name="next-steps"></a>Nächste Schritte

Am besten können Sie sich mit den Vorteilen und Einschränkungen des Assistenten vertraut machen, indem Sie ihn schrittweise zu durchlaufen. Der folgende Schnellstart führt Sie durch die einzelnen Schritte.

> [!div class="nextstepaction"]
> [Erstellen eines Index für die kognitive Azure-Suche im Azure-Portal](search-get-started-portal.md)
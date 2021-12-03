---
title: Indizieren von Daten mit Power Query-Connectors (Vorschau)
titleSuffix: Azure Cognitive Search
description: Importieren Sie Daten aus verschiedenen Datenquellen mithilfe der Power Query-Connectors.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 5ff733cca02d8c4ca9d3014296cffd8db87c3c3a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892815"
---
# <a name="index-data-using-power-query-connectors-preview"></a>Indizieren von Daten mit Power Query-Connectors (Vorschau)

> [!IMPORTANT] 
> Die Unterstützung für Power Query-Connectors befindet sich derzeit in einer **geschlossenen öffentlichen Vorschau**. [Registrieren Sie sich](https://aka.ms/azure-cognitive-search/indexer-preview), um Zugriff anzufordern.

Wenn Sie einen Indexer verwenden, um externe Datenquellen für die Indizierung zu crawlen, können Sie jetzt ausgewählte [Power Query](/power-query/power-query-what-is-power-query)-Connectors für Ihre Datenquellenverbindung in Azure Cognitive Search verwenden.

Power Query-Connectors können eine größere Bandbreite von Datenquellen erreichen, einschließlich der Datenquellen anderer Cloudanbieter. Neue Datenquellen, die in dieser Vorschau unterstützt werden:

+ Amazon Redshift
+ Elasticsearch
+ PostgreSQL
+ Salesforce-Objekte
+ Salesforce-Berichte
+ Smartsheet
+ Snowflake

In diesem Artikel wird ein Azure-Portal-basierter Ansatz zum Einrichten eines Indexers mit Power Query-Connectors erläutert. Derzeit gibt es keine SDK-Unterstützung.

> [!NOTE]
> Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-functionality"></a>Unterstützte Funktionen
Power Query-Connectors werden in Indexern verwendet. Ein Indexer in Azure Cognitive Search ist ein Crawler, mit dem durchsuchbare Daten und Metadaten aus einer externen Datenquelle extrahiert werden und ein Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle aufgefüllt wird. Dieser Ansatz wird auch als „Pullmodell“ bezeichnet, weil der Dienst Daten abruft, ohne dass Sie Code schreiben müssen, der einem Index Daten hinzufügt. Indexer bieten Benutzern eine bequeme Möglichkeit, Inhalte aus ihrer Datenquelle zu indizieren, ohne ein eigenes Crawler- oder Pushmodell schreiben zu müssen.

Indexer, die auf Power Query-Datenquellen verweisen, verfügen über dasselbe Maß an Unterstützung für Skillsets, Zeitpläne, Erkennungslogik für Änderungen in der Obergrenzenmarkierung und die meisten Parameter, die von anderen Indexern unterstützt werden.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit dem Abrufen von Daten aus einer der unterstützten Datenquellen beginnen, müssen Sie sich vergewissern, dass Sie all Ihre Ressourcen eingerichtet haben.
+ Dienst für die kognitive Azure-Suche
    + Der Azure Cognitive Search-Dienst ist in einer [unterstützten Region](search-how-to-index-power-query-data-sources.md#regional-availability) eingerichtet.
    + Vergewissern Sie sich, dass das Azure Cognitive Search-Team Ihren Suchdienst für die Vorschau aktiviert hat. Sie können sich für die Vorschau registrieren, indem Sie [dieses Formular](https://aka.ms/azure-cognitive-search/indexer-preview) ausfüllen. 
+ Azure Blob Storage-Konto
    + Ein Blob Storage-Konto ist erforderlich, damit die Vorschau als Vermittler für Ihre Daten verwendet werden kann. Die Daten werden von Ihrer Datenquelle an Blob Storage und dann zum Index fließen. Diese Anforderung ist nur bei der anfänglichen geschlossenen Vorschau vorhanden.

## <a name="getting-started-using-the-azure-portal"></a>Erste Schritte mit dem Azure-Portal
Das Azure-Portal bietet Unterstützung für die Power Query-Connectors. Der Datenimport-Assistent in der kognitiven Azure-Suche kann durch die Entnahme von Stichproben und das Lesen von Metadaten im Container einen Standardindex erstellen, Quellfelder Zielindexfeldern zuordnen und den Index in einem einzigen Vorgang laden. Je nach Größe und Komplexität der Quelldaten können Sie auch innerhalb von Minuten einen funktionsfähigen Volltextsuchindex erstellen.

 Im folgenden Video wird gezeigt, wie Sie einen Power Query-Connector in Azure Cognitive Search einrichten.
 
> [!VIDEO https://www.youtube.com/embed/uy-l4xFX1EE]

### <a name="step-1--prepare-source-data"></a>Schritt 1: Aufbereiten von Quelldaten
Vergewissern Sie sich, dass Ihre Datenquelle Daten enthält. Zur Ableitung eines Indexschemas werden vom Datenimport-Assistenten Metadaten gelesen und Stichproben entnommen, aber es werden auch Daten aus Ihren Datenquellen geladen. Wenn die Daten fehlen, wird der Assistent beendet, und es wird ein Fehler zurückgegeben. 

### <a name="step-2--start-import-data-wizard"></a>Schritt 2: Starten des Datenimport-Assistenten
Nachdem Sie für die Vorschau genehmigt wurden, stellt Ihnen das Azure Cognitive Search-Team einen Azure-Portal-Link bereit, der ein Featureflag verwendet, damit Sie auf die Power Query-Connectors zugreifen können. Öffnen Sie diese Seite, und starten Sie den Assistenten über die Befehlsleiste auf der Seite des Azure Cognitive Search-Diensts, indem Sie **Daten importieren** auswählen.

:::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Screenshot des Befehls „Daten importieren“" border="true":::

### <a name="step-3--select-your-data-source"></a>Schritt 3: Auswählen der Datenquelle
Es gibt einige Datenquellen, aus denen Sie Daten mithilfe dieser Vorschau abrufen können. Alle Datenquellen, für die Power Query verwendet wird, enthalten den Hinweis „Powered By Power Query“ auf ihrer Kachel. Wählen Sie Ihre Datenquelle aus. 
 
![Auswählen einer Datenquelle](./media/search-power-query-connectors/power-query-import-data.png "Auswählen einer Datenquelle")

Nachdem Sie Ihre Datenquelle ausgewählt haben, wählen Sie **Next: Configure your data** (Weiter: Konfigurieren Sie Ihre Daten) aus, um zum nächsten Abschnitt zu wechseln.

### <a name="step-4--configure-your-data"></a>Schritt 4: Konfigurieren Ihrer Daten
Nachdem Sie Ihre Datenquelle ausgewählt haben, konfigurieren Sie die Verbindung. Die einzelnen Datenquellen erfordern jeweils unterschiedliche Informationen. Für einige Datenquellen enthält die Power Query-Dokumentation zusätzliche Details zum Herstellen einer Verbindung mit den Daten. 

+ [PostgreSQL](/power-query/connectors/postgresql)
+ [Salesforce-Objekte](/power-query/connectors/salesforceobjects)
+ [Salesforce-Berichte](/power-query/connectors/salesforcereports)

Nachdem Sie Ihre Anmeldeinformationen für die Verbindung angegeben haben, wählen Sie **Weiter** aus.

### <a name="step-5--select-your-data"></a>Schritt 5: Auswählen Ihrer Daten
Im Import-Assistenten wird eine Vorschau verschiedener Tabellen angezeigt, die in Ihrer Datenquelle verfügbar sind. In diesem Schritt überprüfen Sie eine Tabelle, die die Daten enthält, die Sie in den Index importieren möchten.
 
![Anzeigen einer Vorschau Ihrer Daten](./media/search-power-query-connectors/power-query-preview-data.png "Anzeigen einer Vorschau Ihrer Daten")

Nachdem Sie die Tabelle ausgewählt haben, wählen Sie **Weiter** aus.

### <a name="step-6--transform-your-data-optional"></a>Schritt 6: Transformieren Ihrer Daten (optional)
Power Query-Connectors bieten Ihnen eine umfassende Benutzeroberfläche, mit der Sie die Daten bearbeiten können, damit Sie die richtigen Daten an den Index senden können. Sie können Spalten entfernen, Zeilen filtern und vieles mehr. 

Es ist nicht erforderlich, dass Sie die Daten transformieren, bevor Sie sie in Azure Cognitive Search importieren.

![Transformieren Ihrer Daten](./media/search-power-query-connectors/power-query-transform-your-data.png "Transformieren Ihrer Daten") 

Weitere Informationen zum Transformieren von Daten mit Power Query finden Sie unter [Verwenden von Power Query in Power BI Desktop](/power-query/power-query-quickstart-using-power-bi). 

Nachdem Sie die Transformation der Daten abgeschlossen haben, wählen Sie **Weiter** aus.

### <a name="step-7--add-azure-blob-storage"></a>Schritt 7: Hinzufügen von Azure Blob Storage
Für die Vorschauversion der Power Query-Connectors müssen Sie derzeit ein Blob-Speicherkonto angeben. Dieser Schritt ist nur bei der anfänglichen geschlossenen Vorschau vorhanden. Dieses Blob-Speicherkonto dient als temporärer Speicher für Daten, die von Ihrer Datenquelle in einen Azure Cognitive Search-Index verschoben werden.

Es wird empfohlen, eine Verbindungszeichenfolge für das Speicherkonto mit Vollzugriff bereitzustellen: 
```
{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }
```

Sie können die Verbindungszeichenfolge über das Azure-Portal abrufen, indem Sie auf dem Blatt des Speicherkontos zu „Einstellungen“ > „Schlüssel“ (für klassische Speicherkonten) oder zu „Einstellungen“ > „Zugriffsschlüssel“ (für Azure Resource Manager-Speicherkonten) navigieren.

Nachdem Sie einen Datenquellennamen und eine Verbindungszeichenfolge angegeben haben, wählen Sie „Weiter: Kognitive Qualifikationen hinzufügen (Optional)“ aus. 

### <a name="step-8--add-cognitive-skills-optional"></a>Schritt 8: Kognitive Qualifikationen hinzufügen (Optional)
Die [KI-Anreicherung](cognitive-search-concept-intro.md) ist eine Erweiterung von Indexern, die dazu verwendet werden kann, Ihre Inhalte leichter durchsuchbar zu machen.

Dies ist ein optionaler Schritt für diese Vorschau. Wählen Sie nach Abschluss des Vorgangs **Weiter: Zielindex anpassen** aus.

### <a name="step-9--customize-target-index"></a>Schritt 9: Anpassen des Zielindex
Auf der Indexseite sollte eine Liste von Feldern mit einem Datentyp sowie mehrere Kontrollkästchen zum Festlegen von Indexattributen aufgeführt sein. Der Assistent kann basierend auf Metadaten und durch Sampling der Quelldaten eine Felderliste erstellen.

Sie können durch Klicken auf das Kontrollkästchen am Kopf einer Attributspalte mehrere Attribute gleichzeitig auswählen. Wählen Sie Abrufbar und Durchsuchbar für jedes Feld aus, das an eine Client-App zurückgegeben werden sollte und der Volltextsuche unterliegt. Sie werden feststellen, dass Ganzzahlen nicht mit der Volltext- oder Fuzzysuche durchsuchbar sind (Zahlen werden wörtlich ausgewertet und eignen sich häufig in Filtern).

Weitere Informationen finden Sie in den Beschreibungen der Indexattribute und Sprachanalysetools.

Nehmen Sie sich einen Moment Zeit, um Ihre Auswahl zu überprüfen. Wenn Sie den Assistenten ausführen, werden physische Datenstrukturen erstellt, und Sie können den Großteil der Eigenschaften für diese Felder nicht bearbeiten, ohne alle Objekte zu löschen und neu zu erstellen.

![Erstellen des Index](./media/search-power-query-connectors/power-query-index.png "Erstellen des Index")

Wählen Sie nach Abschluss des Vorgangs **Weiter: Indexer erstellen** aus.

### <a name="step-10--create-an-indexer"></a>Schritt 10: Erstellen eines Indexers
Mit dem letzten Schritt wird der Indexer erstellt. Wenn der Indexer benannt wird, kann er als eigenständige Ressource existieren. Diese können Sie unabhängig vom Index und Datenquellenobjekt, die im selben Durchlauf des Assistenten erstellt wurden, planen und verwalten.

Die Ausgabe des Datenimport-Assistenten ist ein Indexer, der Ihre Datenquelle crawlt und die von Ihnen ausgewählten Daten in einen Index in Azure Cognitive Search importiert.

Beim Erstellen des Indexers können Sie optional den Indexer nach einem Zeitplan ausführen und die Änderungserkennung hinzufügen. Legen Sie eine Spalte mit oberem Grenzwert fest, um die Änderungserkennung hinzuzufügen.

![Erstellen des Indexers](./media/search-power-query-connectors/power-query-indexer-configuration.png "Erstellen des Indexers")

Nachdem Sie diese Seite ausgefüllt haben, wählen Sie die Option zum **Übermitteln** aus.

## <a name="high-water-mark-change-detection-policy"></a>Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert
Diese Richtlinie zur Erkennung von Änderungen basiert auf einer Spalte mit oberem Grenzwert, die Version oder Uhrzeit der letzten Aktualisierung einer Zeile erfasst.

### <a name="requirements"></a>Requirements (Anforderungen)
+ Alle Einfügungen geben einen Wert für die Spalte an.
+ Alle Updates für ein Element ändern auch den Wert der Spalte.
+ Der Wert dieser Spalte wird bei jeder Einfügung oder Aktualisierung erhöht.

## <a name="unsupported-column-names"></a>Nicht unterstützte Spaltennamen
Feldnamen in einem Azure Cognitive Search-Index müssen bestimmte Anforderungen erfüllen. Eine dieser Anforderungen ist, dass einige Zeichen wie „/“ nicht zulässig sind. Wenn ein Spaltenname in Ihrer Datenbank diese Anforderungen nicht erfüllt, wird die Spalte von der Indexschemaerkennung nicht als gültiger Feldname erkannt, und die Spalte wird nicht als vorgeschlagenes Feld für Ihren Index aufgeführt. Normalerweise würde das Problem durch die Verwendung von [Feldzuordnungen](search-indexer-field-mappings.md) gelöst, aber Feldzuordnungen werden im Portal nicht unterstützt.

Benennen Sie die Spalte während der Phase „Transform your data“ (Daten transformieren) des Datenimportprozesses um, um Inhalte aus einer Tabellenspalte mit einem nicht unterstützten Feldnamen zu indizieren. Beispielsweise können Sie eine Spalte mit der Bezeichnung „Abrechnungscode/Postleitzahl“ in „Postleitzahl“ umbenennen. Durch Umbenennen der Spalte wird die Spalte von der Indexschemaerkennung als gültiger Feldname erkannt und der Indexdefinition als Vorschlag hinzugefügt.

## <a name="regional-availability"></a>Regionale Verfügbarkeit
Die Vorschau ist nur für Kunden mit Suchdiensten in den folgenden Regionen verfügbar:
+ USA (Mitte)
+ East US
+ USA (Ost) 2
+ USA Nord Mitte
+ Nordeuropa
+ USA Süd Mitte
+ USA, Westen-Mitte
+ Europa, Westen
+ USA (Westen)
+ USA, Westen 2

## <a name="preview-limitations"></a>Einschränkungen der Vorschau
Diese Vorschau beinhaltet viele tolle Features, aber es gibt einige Einschränkungen. In diesem Abschnitt werden die Einschränkungen beschrieben, die konkret für die aktuelle Version der Vorschau gelten.
+ Das Pullen von Binärdaten aus Ihrer Datenquelle wird in dieser Version der Vorschau nicht unterstützt. 
+ [Debugsitzungen](cognitive-search-debug-session.md) werden zu diesem Zeitpunkt nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Sie haben gelernt, wie Daten mithilfe der Power Query-Connectors aus neuen Datenquellen abgerufen werden. Weitere Informationen zu Indexern finden Sie unter [Indexer in Azure Cognitive Search](search-indexer-overview.md).
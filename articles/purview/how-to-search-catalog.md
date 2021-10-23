---
title: 'Gewusst wie: Suchen im Datenkatalog'
description: Dieser Artikel enthält eine Übersicht über das Suchen im Azure Purview-Datenkatalog.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 329213fc37edae93d3871c1a52b6d5b73f8f76ed
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659562"
---
# <a name="search-the-azure-purview-data-catalog"></a>Suchen im Azure Purview-Datenkatalog

Nachdem Daten überprüft und in der Azure Purview Data Map erfasst wurden, müssen Datenconsumer die für ihre Analyse- oder Governanceworkloads erforderlichen Daten leicht finden können. Die Datenermittlung kann zeitaufwändig sein, wenn Sie möglicherweise nicht wissen, wo Sie die gewünschten Daten suchen sollen. Doch selbst nachdem Sie die Daten gefunden haben, haben Sie möglicherweise Zweifel, ob Sie diese als vertrauenswürdig einstufen und eine Abhängigkeit davon akzeptieren können.

Die Suche in Azure Purview soll den Datenermittlungsprozess beschleunigen, damit Sie die relevanten Daten schnell finden. In diesem Artikel wird beschrieben, wie Sie mithilfe der Suche im Azure Purview-Datenkatalog die gewünschten Daten schnell finden.

## <a name="search-the-catalog-for-assets"></a>Durchsuchen des Katalogs nach Objekten (Assets)

Auf die Suchleiste können Sie schnell über die obere Leiste der Purview Studio-Benutzeroberfläche zugreifen. Auf der Startseite des Datenkatalogs befindet sich die Suchleiste in der Mitte des Bildschirms.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Screenshot der Position der Suchleiste in Azure Purview" border="true":::

Nachdem Sie auf die Suchleiste geklickt haben, werden Ihr Suchverlauf und die Ressourcen angezeigt, auf die Sie kürzlich im Datenkatalog zugegriffen haben. Dadurch können Sie frühere Suchvorgänge im Handumdrehen fortsetzen.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Screenshot der Suchleiste vor Eingabe eines Schlüsselworts" border="true":::

Geben Sie in die Suchleiste Schlüsselwörter zur Identifikation der gewünschten Ressource ein, wie z. B. Name, Datentyp, Klassifizierung und Glossarbegriffe. Wenn Sie Suchbegriffe eingeben, schlägt Purview dynamisch Ressourcen und Suchen vor, die Ihren Anforderungen entsprechen können. Klicken Sie zum Ausführen der Suche auf „Suchergebnisse anzeigen“, oder drücken Sie die EINGABETASTE.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Screenshot der Suchleiste während der Eingabe von Schlüsselwörtern durch den Benutzer" border="true":::

Sobald Sie Ihre Suchbegriffe eingeben, gibt Purview eine Liste von Datenressourcen zurück, für die ein Benutzer ein Datenleser ist und die mit den eingegebenen Schlüsselwörtern übereinstimmen.

Die Relevanz-Engine von Purview sortiert alle Übereinstimmungen und ordnet sie danach, wie hilfreich die Ergebnisse schätzungsweise für den Benutzer sind. So ist z. B. eine Tabelle, die mit mehreren Schlüsselwörtern übereinstimmt und der ein Data Steward Glossarbegriffe und eine Beschreibung zugewiesen hat, für einen Datenconsumer wahrscheinlich interessanter als ein Ordner, der nicht kommentiert wurde. Ein umfassender Satz an Faktoren bestimmt die Relevanzbewertung einer Ressource. Das Purview-Suchteam verbessert die Relevanz-Engine kontinuierlich, um sicherzustellen, dass die ersten Suchergebnisse wertvoll für Sie sind.

Wenn die besten Ergebnisse nicht die gesuchten Ressourcen enthalten, können Sie über die links angezeigten Facets die Ergebnisse nach Geschäftsmetadaten filtern, beispielsweise nach Glossarbegriffen, Klassifizierungen und der enthaltenen Sammlung. Wenn Sie nach einem bestimmten Datenquellentyp wie Azure Data Lake Storage Gen2 oder Azure SQL-Datenbank suchen, können Sie die Suchergebnisse mit dem Quelltyp-Filter eingrenzen.

> [!NOTE]
> Die Suche gibt nur Ressourcen in Sammlungen zurück, für die Sie Datenleser oder Datenkurator sind. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md).

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot der Suchergebnisse" border="true":::

Bei bestimmten Anmerkungen können Sie auf die Ellipsen klicken, um zwischen einer UND-Bedingung oder einer ODER-Bedingung auszuwählen. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Screenshot der Auswahl zwischen UND- und ODER-Bedingung" border="true":::

Wenn Sie die gesuchte Ressource gefunden haben, können Sie sie auswählen, um Details wie Schema, Herkunft und eine detaillierte Klassifizierungsliste anzuzeigen. Weitere Informationen zur Seite mit den Ressourcendetails finden Sie unter [Verwalten von Katalogressourcen](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot der Seite mit den Ressourcendetails" border="true":::

## <a name="bulk-edit-search-results"></a>Massenbearbeitung von Suchergebnissen

Wenn Sie Änderungen an mehreren Ressourcen aus den Sucheregebnissen vornehmen möchten, können Sie mit Azure Purview Glossarbegriffe, Klassifizierungen und Kontakte per Massenvorgang ändern. Weitere Informationen finden Sie im Leitfaden zur [Massenbearbeitung von Ressourcen](how-to-bulk-edit-assets.md).

## <a name="browse-the-data-catalog"></a>Browsen im Datenkatalog

Die Suchfunktion ist praktisch, wenn Sie gezielt nach etwas suchen möchten. Manchmal möchten Datenconsumer aber auch einfach die verfügbaren Daten durchstöbern. Im Azure Purview-Datenkatalog können Benutzer die für sie verfügbaren Daten durchsuchen – entweder nach Sammlung oder über die Hierarchie jeder Datenquelle im Katalog. Weitere Informationen finden Sie unter [Browsen im Datenkatalog](how-to-browse-catalog.md).

## <a name="search-query-syntax"></a>Suchabfragesyntax

Alle Suchabfragen bestehen aus Schlüsselwörtern und Operatoren. Ein Schlüsselwort ist ein Element, das zu den Eigenschaften einer Ressource gehört. Zu den möglichen Schlüsselwörtern zählen die Klassifizierung, ein Glossarbegriff, die Beschreibung einer Ressource oder deren Name. Ein Schlüsselwort kann auch nur einen Teil der Eigenschaft darstellen, die Sie suchen. Mithilfe von Schlüsselwörtern und der unten in der Tabelle aufgelisteten Operatoren gibt Azure Purview die von Ihnen gesuchten Ressourcen zurück.

Bestimmte Zeichen einschließlich Leerzeichen, Bindestriche und Kommas werden als Trennzeichen interpretiert. Das Durchsuchen einer Zeichenfolge wie `hive-database` entspricht der Suche nach zwei Schlüsselwörtern `hive database`. 

Die folgenden Operatoren können Sie in Ihrer Suchabfrage verwenden. In einer einzelnen Abfrage können Operatoren beliebig miteinander kombiniert werden.

| Operator | Definition | Beispiel |
| -------- | ---------- | ------- |
| oder | Gibt an, dass eine Ressource mindestens eines von zwei Schlüsselwörtern enthalten muss. Muss vollständig in Großbuchstaben geschrieben werden. Ein Leerzeichen stellt auch einen OR-Operator dar.  | Durch die Abfrage `hive OR database` werden Ressourcen zurückgegeben, die „hive“, „database“ oder beide Begriffe enthalten. |
| AND | Gibt an, dass eine Ressource beide Schlüsselwörter enthalten muss. Muss vollständig in Großbuchstaben geschrieben werden. | Durch die Abfrage `hive AND database` werden Ressourcen zurückgegeben, die sowohl „hive“ als auch „database“ enthalten. |
| NICHT | Gibt an, dass eine Ressource das Schlüsselwort hinter der NOT-Klausel nicht enthalten darf. | Durch die Abfrage `hive NOT database` werden Ressourcen zurückgegeben, die „hive“, aber nicht „database“ enthalten. |
| () | Gruppiert mehrere Schlüsselwörter und Operatoren. Werden mehrere Operatoren miteinander kombiniert, geben die Klammern die Reihenfolge der Vorgänge an. | Durch die Abfrage `hive AND (database OR warehouse)` werden Ressourcen zurückgegeben, die „hive“ und entweder „database“ oder „warehouse“ oder beides enthalten. |
| "" | Gibt den exakten Inhalt eines Ausdrucks an, mit dem die Abfrage übereinstimmen muss. | Durch die Abfrage `"hive database"` werden Ressourcen zurückgegeben, deren Eigenschaften den Ausdruck „hive database“ enthalten. |
| * | Platzhalter für ein oder mehrere Zeichen. Darf nicht das erste Zeichen eines Schlüsselworts darstellen. | Durch die Abfrage `dat*` werden Ressourcen zurückgegeben, deren Eigenschaften mit „dat“ beginnen, wie z. B. „data“ oder „database“. |
| ? | Platzhalter für ein einzelnes Zeichen. Darf nicht das erste Zeichen eines Schlüsselworts darstellen. | Durch die Abfrage `dat?` werden Ressourcen zurückgegeben, deren Eigenschaften mit „dat“ beginnen und die aus vier Zeichen bestehen, wie z. B. „date“ oder „data“. |

> [!Note]
> Geben Sie boolesche Operatoren (**AND**, **OR**, **NOT**) immer vollständig in Großbuchstaben an. Ansonsten spielen weder die Groß-/Kleinschreibung noch zusätzliche Leerzeichen eine Rolle.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen, Importieren und Exportieren von Glossarbegriffen](how-to-create-import-export-glossary.md)
- [Verwalten von Begriffsvorlagen für ein Unternehmensglossar](how-to-manage-term-templates.md)

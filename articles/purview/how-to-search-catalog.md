---
title: 'Gewusst wie: Suchen im Datenkatalog'
description: Dieser Artikel enthält eine Übersicht dazu, wie in einem Datenkatalog gesucht wird.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 5581a19f001b4caee8ef0e0d93908bd6d98471a0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213598"
---
# <a name="search-the-azure-purview-data-catalog"></a>Suchen im Azure Purview-Datenkatalog

Die Datenermittlung ist der erste Schritt einer Datenanalyse- oder Datengovernanceworkload für Datenconsumer. Die Datenermittlung kann zeitaufwändig sein, wenn Sie möglicherweise nicht wissen, wo Sie die gewünschten Daten suchen sollen. Doch selbst nachdem Sie die Daten gefunden haben, haben Sie möglicherweise Zweifel, ob Sie diese als vertrauenswürdig einstufen und eine Abhängigkeit davon akzeptieren können.

Die Suche in Azure Purview soll den Datenermittlungsprozess beschleunigen, damit Sie die relevanten Daten schnell finden. In diesem Artikel wird beschrieben, wie Sie mithilfe der Suche im Azure Purview-Datenkatalog die gewünschten Daten schnell finden.

## <a name="search-the-catalog-for-assets"></a>Durchsuchen des Katalogs nach Objekten (Assets)

In Azure Purview befindet sich die Suchleiste oben in der Purview Studio-Benutzeroberfläche.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Screenshot der Position der Suchleiste in Azure Purview" border="true":::

Wenn Sie in die Suchleiste klicken, werden der aktuelle Suchverlauf und die zuletzt verwendeten Ressourcen angezeigt. Klicken Sie auf **Alle anzeigen**, um alle zuletzt angezeigten Ressourcen anzuzeigen.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Screenshot der Suchleiste vor Eingabe eines Schlüsselworts" border="true":::

Geben Sie in die Suchleiste Schlüsselwörter zur Identifikation der gewünschten Ressource ein, wie z. B. Name, Datentyp, Klassifizierung und Glossarbegriffe. Während der Eingabe von Schlüsselwörtern zur gesuchten Ressource zeigt Azure Purview Vorschläge zu Suchbegriffen sowie mögliche Ressourcenübereinstimmungen an. Klicken Sie zum Starten der Suche auf **Suchergebnisse anzeigen**, oder drücken Sie die EINGABETASTE.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Screenshot der Suchleiste während der Eingabe von Schlüsselwörtern durch den Benutzer" border="true":::

Auf der Seite der Suchergebnisse wird in der Reihenfolge ihrer Relevanz eine Liste der Ressourcen angezeigt, die mit den Schlüsselwörtern übereinstimmen. Die Relevanzbewertung einer Ressource wird von unterschiedlichen Faktoren beeinflusst. Sie können die Liste weiter nach bestimmten Datenspeichern, Klassifizierungen, Kontakten, Bezeichnungen und Glossarbegriffe filtern, die auf die gesuchte Ressource zutreffen.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot der Suchergebnisse" border="true":::

 Klicken Sie auf die gewünschte Ressource, um die Seite mit den Ressourcendetails anzuzeigen, darunter z. B. Eigenschaften wie Schema, Herkunft und Ressourcenbesitzer.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot der Seite mit den Ressourcendetails" border="true":::

## <a name="search-query-syntax"></a>Suchabfragesyntax

Alle Suchabfragen bestehen aus Schlüsselwörtern und Operatoren. Ein Schlüsselwort ist ein Element, das zu den Eigenschaften einer Ressource gehört. Zu den möglichen Schlüsselwörtern zählen die Klassifizierung, ein Glossarbegriff, die Beschreibung einer Ressource oder deren Name. Ein Schlüsselwort kann auch nur einen Teil der Eigenschaft darstellen, die Sie suchen. Mithilfe von Schlüsselwörtern und der unten in der Tabelle aufgelisteten Operatoren gibt Azure Purview die von Ihnen gesuchten Ressourcen zurück. 

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

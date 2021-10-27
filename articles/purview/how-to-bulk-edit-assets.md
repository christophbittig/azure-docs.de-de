---
title: Massenbearbeitung von Ressourcen zur Kennzeichnung von Klassifizierungen, Glossarbegriffen und zum Ändern von Kontakten
description: Erfahren Sie mehr über die Massenbearbeitung von Ressourcen in Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 511c104080acf27e233fecfa3fae7efa814b9bba
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133396"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Massenbearbeitung von Ressourcen zur Kommentierung von Klassifizierungen, Glossarbegriffen und zum Ändern von Kontakten

In diesem Artikel wird beschrieben, wie Sie eine Liste ausgewählter Objekte in einer einzigen Aktion mit mehreren Glossarbegriffen, Klassifizierungen, Besitzern und Experten markieren.

## <a name="add-assets-to-view-selected-list-using-search"></a>Hinzufügen von Ressourcen zur Liste „Ausgewählte anzeigen“ mithilfe der Suche

1. Suchen Sie nach der Datenressource, die Sie der Liste für die Massenbearbeitung hinzufügen möchten.

1. Zeigen Sie auf der Suchergebnisseite auf die Ressource, die Sie der Liste **Ausgewählte anzeigen** für die Massenbearbeitung hinzufügen möchten, um ein Kontrollkästchen anzuzeigen.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Screenshot des Kontrollkästchens":::

1. Aktivieren Sie das Kontrollkästchen, um es der Liste **Ausgewählte anzeigen** für die Massenbearbeitung hinzuzufügen. Nach dem Hinzufügen wird unten auf der Seite das Symbol „Ausgewählte Elemente“ angezeigt.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Screenshot der Liste":::

1. Wiederholen Sie die Schritte oben, um der Liste alle Datenressourcen hinzuzufügen.

## <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Hinzufügen von Ressourcen zur Liste „Ausgewählte anzeigen“ von der Detailseite dieser Ressourcen

Sie können ein Asset auch auf der Asset-Detailseite zur Massenbearbeitungsliste hinzufügen. Aktivieren Sie das Kontrollkästchen in der oberen rechten Ecke, um das Asset der Liste für die Massenbearbeitung hinzuzufügen.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Screenshot der Ressource":::

## <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Massenbearbeitung von Ressourcen in der Liste „Ausgewählte anzeigen“ zum Hinzufügen, Ersetzen oder Entfernen von Glossarbegriffen

1. Wenn Sie mit der Identifizierung aller zu bearbeitenden Datenbestände fertig sind, wählen Sie **Ausgewählte** Liste anzeigen auf der Seite mit den Suchergebnissen oder den Bestandsdetails.

    :::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Screenshot der Ansicht":::

1. Überprüfen Sie die Liste und wählen Sie **Abwählen**, wenn Sie bestimmte Assets aus der Liste entfernen möchten.

    :::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Bildschirmfoto mit hervorgehobener Schaltfläche &quot;Abwählen&quot; ":::

1. Wählen Sie **Sammelbearbeitung**, um Glossarbegriffe für alle ausgewählten Assets hinzuzufügen, zu entfernen oder zu ersetzen.

    :::image type="content" source="media/how-to-bulk-edit-assets/bulk-edit.png" alt-text="Screenshot mit hervorgehobener Schaltfläche &quot;Massenbearbeitung&quot;.":::

1. Zum Hinzufügen von Glossarbegriffen wählen Sie als Vorgang **Hinzufügen** aus. Wählen Sie alle Glossarbegriffe aus, die Sie als neuen Wert hinzufügen möchten. Wählen Sie „Anwenden“ aus, wenn Sie fertig sind.
    - Durch den Vorgang „Hinzufügen“ wird ein neuer Wert an die Liste der Glossarbegriffe angefügt, mit denen die Datenressourcen bereits gekennzeichnet sind.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Screenshot des Hinzufügens":::

1. Wenn Sie Glossarbegriffe ersetzen möchten, wählen Sie den Vorgang **Ersetzen durch** aus. Wählen Sie alle Glossarbegriffe aus, die Sie durch einen neuen Wert ersetzen möchten. Wählen Sie „Anwenden“ aus, wenn Sie fertig sind.
    - Durch den Ersetzungsvorgang werden alle Glossarbegriffe für die ausgewählte Datenressourcen durch die in „Neuer Wert“ angegebenen Begriffe ersetzt.
   
1. Zum Entfernen von Glossarbegriffen wählen Sie als Vorgang **Entfernen** aus. Wählen Sie „Anwenden“ aus, wenn Sie fertig sind.
    - Durch das Entfernen werden alle Glossarbegriffe aus den ausgewählten Datenressourcen entfernt.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Screenshot des Entfernens von Begriffen":::

1. Wiederholen Sie die obigen Schritte für Klassifizierungen, Besitzer und Experten.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Screenshot der Klassifizierungen und Kontakte.":::

1. Schließen Sie nach Abschluss des Vorgangs das Blatt für die Massenbearbeitung, indem Sie **Schließen** oder **Remove all and close** (Alle entfernen und schließen) auswählen. Durch „Schließen“ werden die ausgewählten Ressourcen nicht entfernt, mit „Remove all and close“ (Alle entfernen und schließen) hingegen schon.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Screenshot des Schließens":::

   > [!Important]
   > Die empfohlene Anzahl von Ressourcen für die Massenbearbeitung beträgt 25. Wenn Sie mehr als 25 auswählen, kann dies die Leistung beeinträchtigen.
   > Das Feld **Ausgewählte anzeigen** wird nur angezeigt, wenn mindestens eine Ressource ausgewählt ist.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)](how-to-create-import-export-glossary.md). Hier finden Sie weitere Informationen.

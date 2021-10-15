---
title: 'Gewusst wie: Browsen im Datenkatalog'
description: Dieser Artikel enthält eine Übersicht über das Browsen im Azure Purview-Datenkatalog nach Assettyp.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456819"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Durchsuchen des Azure Purview-Datenkatalogs

Das Durchsuchen eines Datenkatalogs eignet sich hervorragend für die Datenermittlung, wenn der Datenconsumer weiß, wonach er sucht. Häufig wissen Benutzer*innen jedoch nicht genau, wie ihre Daten strukturiert sind. Der Azure Purview-Datenkatalog bietet eine Suchfunktion, mit der Benutzer*innen untersuchen können, welche Daten für sie verfügbar sind. Dies erfolgt nach Sammlung oder durch Durchlaufen der Hierarchie jeder Datenquelle im Katalog.

Um die Suchfunktion zu nutzen, wählen Sie auf der Startseite des Datenkatalogs die Option „Ressourcen durchsuchen“ aus.

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview-Startseite" border="true":::

## <a name="browse-by-collection"></a>Durchsuchen nach Sammlung

Das Durchsuchen nach Sammlungen ermöglicht Ihnen, die verschiedenen Sammlungen zu untersuchen, für die Sie Datenleser oder -kurator sind.

> [!NOTE]
> Es werden nur Sammlungen angezeigt, auf die Sie Zugriff haben. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md).

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="Screenshot der Seite „Nach Sammlung durchsuchen“" border="true":::

Nachdem eine Sammlung ausgewählt wurde, erhalten Sie eine Liste der Ressourcen in dieser Sammlung mit den Facetten und Filtern, die in der Suche verfügbar sind. Da eine Sammlung Tausende von Ressourcen enthalten kann, wird beim Durchsuchen die Relevanz-Engine für die Purview-Suche angewandt, um die wichtigsten Ressourcen hervorzuheben.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="Screenshot der Ergebnisse der Suche nach Sammlungen" border="true":::

Bei bestimmten Anmerkungen können Sie auf die Ellipsen klicken, um zwischen einer UND-Bedingung oder einer ODER-Bedingung auszuwählen. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Screenshot der Auswahl zwischen UND- und ODER-Bedingung" border="true":::

Wenn die ausgewählte Sammlung die gesuchten Daten nicht enthält, können Sie ganz einfach zu verwandten Sammlungen navigieren oder wieder die gesamte Sammlungsstruktur anzeigen.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="Screenshot der Navigation zwischen Sammlungen" border="true":::

Wenn Sie die gesuchte Ressource gefunden haben, können Sie sie auswählen, um weitere Details wie Schema, Herkunft und eine detaillierte Klassifizierungsliste anzuzeigen. Weitere Informationen zur Seite mit den Ressourcendetails finden Sie unter [Verwalten von Katalogressourcen](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot der Seite mit den Ressourcendetails" border="true":::

## <a name="browse-by-source-type"></a>Nach Quelltyp durchsuchen

Mit „Nach Quelltyp durchsuchen“ können Datenconsumer die Hierarchien von Datenquellen mithilfe einer Explorer-Ansicht untersuchen. Wählen Sie einen Quelltyp aus, um die Liste der überprüften Quellen anzuzeigen.

Beispielsweise können Sie in Azure Data Lake Storage Gen 2 ein Dataset mit dem Namen *DateDimension* im Ordner *Dimensions* leicht finden. Sie können die Option „Nach Quelltyp durchsuchen“ verwenden, um zum ADLS Gen 2-Speicherkonto zu navigieren. Greifen Sie anschließend auf „Dienst“ > „Container“ > „Ordner“ zu, um zum entsprechenden Ordner *Dimensions* zu navigieren, unter dem die Tabelle *DateDimension* angezeigt wird.

Für jede entsprechende Datenquelle ist eine native Suchfunktion mit einem hierarchischen Namespace vorhanden.

> [!NOTE]
> Nach einer erfolgreichen begrenzten Überprüfung kann es zu einer Verzögerung kommen, bevor neu überprüfte Ressourcen in der Suchfunktion angezeigt werden.


1. Auf der Seite **Nach Quelltyp durchsuchen** sind die Kacheln nach Datenquelle kategorisiert. Wählen Sie die entsprechende Kachel aus, um die Ressourcen der einzelnen Datenquellen weiter zu erkunden.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="Seite „Browse asset types“ (Ressourcentypen durchsuchen)" border="true":::

   > [!TIP]
   > Bestimmte Kacheln sind Gruppierungen einer Sammlung von Datenquellen. Beispielsweise enthält die Kachel „Azure Storage-Konto“ alle Azure Blob Storage- und Azure Data Lake Storage Gen2-Konten. Auf der Azure SQL Server-Kachel werden die Azure SQL Server-Assets angezeigt, die Azure SQL-Datenbank- und dedizierte Azure SQL-Poolinstanzen enthalten, die in den Katalog aufgenommen wurden. 

1. Auf der nächsten Seite sind unter dem von Ihnen ausgewählten Datentyp die Ressourcen der obersten Ebene aufgelistet. Wählen Sie eine der Ressourcen aus, um ihren Inhalt eingehender zu untersuchen. Nachdem Sie beispielsweise „Azure SQL-Datenbank“ ausgewählt haben, wird eine Liste der Datenbanken mit Ressourcen im Datenkatalog angezeigt.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Suchseite für Azure SQL-Datenbank" border="true":::

1. Die Explorer-Ansicht wird geöffnet. Wählen Sie im linken Bereich die Ressource aus, um die Suche zu starten. Untergeordnete Ressourcen werden im rechten Bereich der Seite aufgelistet.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="Explorer-Ansicht" border="true":::

1. Wählen Sie ganz rechts den Namen oder die Schaltfläche mit den Auslassungszeichen aus, um die Details einer Ressource anzuzeigen.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="Auswählen der Auslassungszeichen-Schaltfläche zum Anzeigen der Seite mit den Ressourcendetails" border="true":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen, Importieren und Exportieren von Glossarbegriffen](how-to-create-import-export-glossary.md)
- [Verwalten von Begriffsvorlagen für ein Unternehmensglossar](how-to-manage-term-templates.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

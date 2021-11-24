---
title: Anzeigen, Bearbeiten und Löschen von Ressourcen
description: In dieser Anleitung wird beschrieben, wie Sie Details zu Ressourcen anzeigen und bearbeiten können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6013811622492f476e65c91d3c028007e2802c27
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485195"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>Anzeigen, Bearbeiten und Löschen von Ressourcen im Purview-Katalog

In diesem Artikel wird erläutert, wie Sie Ihre Ressourcen und deren relevante Details anzeigen können. Außerdem wird beschrieben, wie Sie Ressourcen in Ihrem Katalog bearbeiten und löschen können.

## <a name="prerequisites"></a>Voraussetzungen

- Richten Sie Ihre Datenquellen ein, und überprüfen Sie die Ressourcen in Ihrem Katalog.
- *Oder* verwenden Sie die Atlas-APIs von Purview, um Ressourcen im Katalog zu erfassen. 

## <a name="viewing-asset-details"></a>Anzeigen von Ressourcendetails

Sie können Ihre Ressourcen in Purview wie folgt ermitteln:
- [Durchsuchen des Azure Purview-Datenkatalogs](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

Sobald Sie die gesuchte Ressource gefunden haben, können Sie, wie in den folgenden Abschnitten beschrieben, alle Details anzeigen, bearbeiten oder löschen.

## <a name="asset-details-tabs-explained"></a>Erläuterung der Registerkarten mit Ressourcendetails

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="Registerkarten mit Ressourcendetails":::

- **Übersicht**: Diese Registerkarte enthält die grundlegenden Details einer Ressource wie Beschreibung, Klassifizierung, Hierarchie und Glossarbegriffe.
- **Eigenschaften**: Diese Registerkarte enthält sowohl grundlegende als auch erweiterte Eigenschaften einer Ressource.
- **Schema**: Das Schema der Ressource, einschließlich Spaltennamen, Datentypen, Klassifizierungen auf Spaltenebene, Begriffe und Beschreibungen, werden auf dieser Registerkarte dargestellt.
- **Datenherkunft**: Diese Registerkarte enthält Details im Graphen zur Datenherkunft von Ressourcen, sofern verfügbar.
- **Kontakte**: Jeder Ressource kann ein Besitzer und Experte zugewiesen werden, der auf dieser Registerkarte angezeigt und verwaltet werden kann.
- **Zugehörig**: Auf dieser Registerkarte können Sie zu Ressourcen navigieren, die mit der aktuell angezeigten Ressource zusammenhängen. 

## <a name="asset-overview"></a>Ressourcenübersicht
Der Abschnitt „Übersicht“ der Ressourcendetails bietet eine zusammengefasste Ansicht einer Ressource. In den folgenden Abschnitten werden die verschiedenen Teile der Übersichtsseite erläutert.

### <a name="asset-hierarchy"></a>Ressourcenhierarchie

Auf der Registerkarte „Übersicht“ können Sie die vollständige Ressourcenhierarchie einsehen. Beispiel: Wenn Sie zu einer SQL-Tabelle navigieren, können Sie das Schema, die Datenbank und den Server erkennen, zu dem die Tabelle gehört.

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="Ressourcenhierarchie":::

### <a name="asset-classifications"></a>Ressourcenklassifizierungen

Ressourcenklassifizierungen dienen zur Bestimmung der Art der dargestellten Daten und werden manuell oder während einer Überprüfung angewendet. Beispiel: Eine Personalausweis- oder Reisepassnummer sind unterstützte Klassifizierungen. (Eine vollständige Liste der Klassifizierungen finden Sie auf der Seite [Unterstützte Klassifizierungen](supported-classifications.md).) Die Registerkarte „Übersicht“ spiegelt sowohl die Klassifizierungen auf Ressourcen- als auch auf Spaltenebene wider, die Sie auch als Teil des Schemas einsehen können.

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="Ressourcenklassifizierungen":::

### <a name="asset-description"></a>Ressourcenbeschreibung

Sie können die Beschreibung einer Ressource im Abschnitt „Übersicht“ einsehen. Sie können eine Beschreibung der Ressource hinzufügen, indem Sie [die Ressource bearbeiten](#editing-assets)

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="Ressourcenbeschreibung":::

### <a name="asset-glossary-terms"></a>Ressourcenspezifische Glossarbegriffe

Ressourcenspezifische Glossarbegriffe sind ein verwaltetes Vokabular für Geschäftsbegriffe, die zum Kategorisieren und Verknüpfen von Ressourcen in Ihrer Umgebung verwendet werden können. Beispiele hierfür sind Begriffe wie „Kunde“, „Käufer“, „Kostenstelle“ oder Begriffe, die für Ihre Daten Benutzerkontext bieten. Weitere Informationen finden Sie auf der Seite mit dem [Glossar mit geschäftlichen Begriffen](concept-business-glossary.md). Sie können die Glossarbegriffe für eine Ressource im Abschnitt „Übersicht“ einsehen und einen Glossarbegriff zu einer Ressource hinzufügen, indem Sie die Ressource [bearbeiten](#editing-assets).

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="Ressourcenspezifische Glossarbegriffe":::

## <a name="editing-assets"></a>Bearbeiten von Ressourcen

Sie können ein Asset bearbeiten, indem Sie auf das Bearbeitungssymbol in der oberen linken Ecke des Assets klicken.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="Schaltflächen zum Bearbeiten und Löschen von Ressourcen":::

Auf Ressourcenebene können Sie eine Beschreibung, Klassifizierung oder einen Glossarbegriff bearbeiten oder hinzufügen, indem Sie auf der Registerkarte „Übersicht“ des Bearbeitungsbildschirms bleiben.

Sie können auf dem Bearbeitungsbildschirm zur Registerkarte „Schema“ navigieren, um den Spaltennamen, Datentyp, die Klassifizierung auf Spaltenebene, Begriffe oder die Ressourcenbeschreibung zu aktualisieren.

Sie können auf dem Bearbeitungsbildschirm zur Registerkarte „Kontakt“ navigieren, um Besitzer und Experten für die Ressource zu aktualisieren. Sie können in Ihrer Azure Active Directory-Instanz nach vollständigem Namen, E-Mail-Adresse oder Alias der Person suchen.

### <a name="scans-on-edited-assets"></a>Überprüfen auf bearbeitete Ressourcen

Wenn Sie eine Ressource bearbeiten, indem Sie eine Beschreibung, eine Klassifizierung auf Ressourcenebene, einen Glossarbegriff oder einen Kontakt hinzufügen, wird das Ressourcenschema auch bei späteren Überprüfungen aktualisiert (neue Spalten und Klassifizierungen, die bei späteren Überprüfungen erkannt wurden).

Wenn Sie einige Aktualisierungen auf Spaltenebene vornehmen, z. B. das Hinzufügen einer Beschreibung, einer Klassifizierung auf Spaltenebene oder eines Glossarbegriffs, dann wird bei nachfolgenden Überprüfungen auch das Ressourcenschema aktualisiert (neue Spalten und Klassifizierungen werden bei nachfolgenden Überprüfungen erkannt). 

Selbst bei bearbeiteten Ressourcen spiegelt Azure Purview nach einer Überprüfung die Tatsachen des Quellsystems wider. Beispiel: Wenn Sie eine Spalte bearbeiten und sie aus der Quelle gelöscht wird, wird sie aus Ihrer Ressource in Purview gelöscht. 

>[!NOTE]
> Wenn Sie den **Namen oder den Datentyp einer Spalte** in einer Azure Purview-Ressource aktualisieren, wird das Schema der Ressource bei späteren Überprüfungen **nicht** mehr aktualisiert. Neue Spalten und Klassifizierungen **werden nicht** erkannt.

## <a name="deleting-assets"></a>Löschen von Ressourcen

Sie können ein Asset löschen, indem Sie auf das Löschsymbol unter dem Namen des Assets klicken.

### <a name="delete-behavior-explained"></a>Erläuterung des Löschverhaltens

Alle Ressourcen, die Sie mithilfe der Schaltfläche „Löschen“ löschen, werden in Azure Purview endgültig gelöscht. Wenn Sie jedoch eine **vollständige Überprüfung** der Quelle durchführen, aus der die Ressource im Katalog erfasst wurde, wird die Ressource erneut erfasst, sodass Sie sie mithilfe des Purview-Katalogs finden können.

Wenn Sie für die Quelle eine geplante Überprüfung (wöchentlich oder monatlich) durchführen lassen, wird die **gelöschte Ressource nicht erneut im Katalog erfasst**, es sei denn, die Ressource wurde seit dem letzten Überprüfungslauf von einem Benutzer geändert.   Wenn z. B. eine SQL-Tabelle aus Purview gelöscht wurde, aber ein Benutzer nach der Löschung der Tabelle eine neue Spalte in SQL hinzugefügt hat, wird die Ressource bei der nächsten Überprüfung erneut geprüft und in den Katalog aufgenommen.

Wenn Sie eine Ressource löschen, wird nur diese Ressource gelöscht. Purview unterstützt derzeit keine kaskadierenden Löschvorgänge. Wenn Sie beispielsweise ein Speicherkontoobjekt aus Ihrem Katalog löschen, werden die darin enthaltenen Container, Ordner und Dateien nicht gelöscht. 


## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

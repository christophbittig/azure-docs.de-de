---
title: Anzeigen, Bearbeiten und Löschen von Ressourcen
description: In dieser Anleitung wird beschrieben, wie Sie Details zu Ressourcen anzeigen und bearbeiten können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/10/2021
ms.openlocfilehash: 8bd100826564ac865a17bf869ad54ce3f9ceef5d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340144"
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

Sie können eine Ressource bearbeiten, indem Sie links oben in der Ressource auf das Bearbeitungssymbol klicken.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="Schaltflächen zum Bearbeiten und Löschen von Ressourcen":::

Auf Ressourcenebene können Sie eine Beschreibung, Klassifizierung oder einen Glossarbegriff bearbeiten oder hinzufügen, indem Sie auf der Registerkarte „Übersicht“ des Bearbeitungsbildschirms bleiben.

Sie können auf dem Bearbeitungsbildschirm zur Registerkarte „Schema“ navigieren, um den Spaltennamen, Datentyp, die Klassifizierung auf Spaltenebene, Begriffe oder die Ressourcenbeschreibung zu aktualisieren.

Sie können auf dem Bearbeitungsbildschirm zur Registerkarte „Kontakt“ navigieren, um Besitzer und Experten für die Ressource zu aktualisieren. Sie können in Ihrer Azure Active Directory-Instanz nach vollständigem Namen, E-Mail-Adresse oder Alias der Person suchen.

### <a name="edit-behavior-explained"></a>Erläuterung des Bearbeitungsverhaltens

Wenn Sie eine Aktualisierung auf Ressourcenebene vornehmen, wie z. B. Hinzufügen einer Beschreibung, einer Klassifizierung auf Ressourcenebene, eines Glossarbegriffs oder eines Kontakts zu einer Ressource, wird bei nachfolgenden Überprüfungen das Schema der Ressource aktualisiert (mit neuen Spalten und Klassifizierungen, die bei nachfolgenden Überprüfungsläufen erkannt wurden).

Wenn Sie eine Aktualisierung auf Spaltenebene vornehmen, wie z. B. Hinzufügen einer Beschreibung, einer Klassifizierung auf Spaltenebene, eines Glossarbegriffs oder Aktualisieren des Datentyps oder Spaltennamens, wird bei nachfolgenden Überprüfungen das Schema der Ressource **nicht** aktualisiert (neue Spalten und Klassifizierungen werden bei nachfolgenden Überprüfungsläufen **nicht erkannt**).

## <a name="deleting-assets"></a>Löschen von Ressourcen

Sie können eine Ressource löschen, indem Sie unter dem Namen der Ressource auf das Löschsymbol klicken.

### <a name="delete-behavior-explained"></a>Erläuterung des Löschverhaltens

Alle Ressourcen, die Sie mithilfe der Schaltfläche „Löschen“ löschen, werden endgültig gelöscht. Wenn Sie jedoch eine **vollständige Überprüfung** der Quelle durchführen, aus der die Ressource im Katalog erfasst wurde, wird die Ressource erneut erfasst, sodass Sie sie mithilfe des Purview-Katalogs finden können.

Wenn Sie für die Quelle eine geplante Überprüfung (wöchentlich oder monatlich) durchführen lassen, wird die **gelöschte Ressource nicht erneut im Katalog erfasst**, es sei denn, die Ressource wurde seit dem letzten Überprüfungslauf von einem Benutzer geändert.   Wenn z. B. eine SQL-Tabelle aus Purview gelöscht wurde, aber ein Benutzer nach der Löschung der Tabelle eine neue Spalte in SQL hinzugefügt hat, wird die Ressource bei der nächsten Überprüfung erneut geprüft und in den Katalog aufgenommen.

Wenn Sie eine Ressource löschen, wird nur diese Ressource gelöscht. Purview unterstützt derzeit keine kaskadierenden Löschvorgänge. Wenn Sie beispielsweise ein Speicherkontoobjekt aus Ihrem Katalog löschen, werden die darin enthaltenen Container, Ordner und Dateien nicht gelöscht. 

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

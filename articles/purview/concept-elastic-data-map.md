---
title: Elastische Data Map
description: In diesem Artikel werden die Konzepte der elastischen Data Map in Azure Purview erläutert.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: template-concept
ms.openlocfilehash: 80706316490cde805ef19b0f1c90f2870dc195ab
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445790"
---
# <a name="elastic-data-map-in-azure-purview"></a>Elastische Data Map in Azure Purview

Azure Purview Data Map bildet die Grundlage für die Datenermittlung und Datengovernance. Es werden Metadaten zu Unternehmensdaten in Analysen, Software-as-a-Service (SaaS) und Betriebssystemen in Hybridumgebungen, lokalen Umgebungen und Multicloudumgebungen erfasst. Durch das integrierte Überprüfungs- und Klassifizierungssystem bleibt Purview Data Map automatisch auf dem neuesten Stand. Mit der Benutzeroberfläche können Entwickler zudem mithilfe Open-Source-basierter Apache Atlas-APIs programmgesteuert mit Data Map interagieren.

## <a name="elastic-data-map"></a>Elastische Data Map

Alle Azure Purview-Konten verfügen über eine Data Map, die ab einer Kapazitätseinheit elastisch wachsen kann. Sie skalieren basierend auf der Anforderungslast innerhalb des Elastizitätsfensters hoch und herunter ([überprüfen Sie aktuelle Einschränkungen](how-to-manage-quotas.md)). Wenn Sie mit sehr großen Datasets oder einer großen Umgebung arbeiten und eine höhere Kapazität benötigen, können Sie eine größere Kapazität des Elastizitätsfensters anfordern, indem Sie die [Umfrage hier](https://aka.ms/PurviewProdSurvey) abschließen.

## <a name="data-map-capacity-unit"></a>Data Map-Kapazitätseinheit

Die elastische Data Map umfasst Operationsdurchsatz- und Speicherkomponenten, die als Kapazitätseinheit (Capacity Unit, CU) angegeben werden. Alle Azure Purview-Konten verfügen standardmäßig über eine Kapazitätseinheit und können basierend auf der Nutzung elastisch wachsen. Jede Data Map-Kapazitätseinheit umfasst einen Durchsatz von 25 Operationen pro Sekunde und 2 GB an Metadatenspeichergrenze.  

### <a name="operations"></a>Operationen (Operations)

Operationen sind das Durchsatzmaß von Purview Data Map. Dazu gehören die Operationen Erstellen, Lesen, Schreiben, Aktualisieren und Löschen für Metadaten, die in der Data Map gespeichert sind. Einige Beispiele für Operationen sind unten aufgeführt:

- Erstellen eines Objekts in Data Map
- Hinzufügen einer Beziehung zu einem Objekt, z. B. Besitzer, Steward, übergeordnetes Element, Herkunft usw.
- Bearbeiten eines Objekts, um Geschäftsmetadaten wie Beschreibung, Glossarbegriff usw. hinzuzufügen
- Schlüsselwortsuche, die Ergebnisse an die Suchergebnisseite zurückgibt

### <a name="storage"></a>Speicher

Der Speicher ist die zweite Komponente von Data Map und enthält technische, geschäftliche, operative und semantische Metadaten.

Zu den technischen Metadaten gehören Schema, Datentyp, Spalten usw., die bei der Purview-[Überprüfung](concept-scans-and-ingestion.md) ermittelt werden. Die Geschäftsmetadaten umfassen automatisiertes (z. B. von Power BI heraufgestufte Datasets oder Beschreibungen aus SQL-Tabellen) und manuelles Tagging von Beschreibungen, Glossarbegriffen usw. Zu den Beispielen für semantische Metadaten gehören die Sammlungszuordnung zu Datenquellen oder Klassifizierungen. Die operativen Metadaten umfassen den Ausführungsstatus der Data Factory-Kopier- und Datenflussaktivität sowie die Ausführungszeit.

## <a name="work-with-elastic-data-map"></a>Arbeiten mit einer elastischen Data Map

- **Elastische Data Map mit automatischer Skalierung** – Sie beginnen mit einer Data Map, die nicht größer als eine Kapazitätseinheit ist und nach Auslastung automatisch skalieren kann. Für die meisten Organisationen führt dieses Feature zu vermehrten Einsparungen und zu einem niedrigeren Preispunkt für den Start von Datengovernanceprojekten. Dieses Feature wirkt sich auf den Preis aus.

- **Erweiterte Überprüfung & Erfassung** – Sie können die Auffüllung der Datenobjekte sowie die Klassifizierung und Herkunft während des gesamten Erfassungs- und Überprüfungsprozesses nachverfolgen und steuern. Dieses Feature wirkt sich auf den Preis aus.

- **Erweiterter Ressourcensatz** – Sie können die Größe der Data Map verringern, indem Sie partitionierte Dateien in einem Data Lake verarbeiten, so dass die Verarbeitung als einzelnes Datenobjekt, genannt [Resource Set](concept-resource-sets.md) (Ressourcensatz), erfolgt. Dies ist ein optionales Feature, das sich auf den Preis auswirkt.

## <a name="scenario"></a>Szenario

Claudia ist ein Azure-Administrator bei Contoso und möchte ein neues Azure Purview-Konto über das Azure-Portal bereitstellen. Während der Bereitstellung kennt sie die für die Unterstützung des zukünftigen Status der Plattform erforderliche Größe von Purview Data Map nicht. Sie weiß jedoch, dass Purview Data Map nach Kapazitätseinheiten abgerechnet wird, die vom Speicher und Operationsdurchsatz beeinflusst werden. Sie möchte die kleinste Data Map bereitstellen, um die Kosten niedrig zu halten und die Data Map basierend auf dem Verbrauch elastisch zu vergrößern.  

Claudia kann ein Purview-Konto mit der Data Map-Standardgröße von einer Kapazitätseinheit erstellen, das automatisch hoch- und herunterskalieren kann. Das Feature der automatischen Skalierung ermöglicht zudem die Optimierung der Kapazität basierend auf zeitweiligen oder geplanten Datenbursts in bestimmten Zeiträumen. Claudia führt die nächsten Schritte der Bereitstellung zur Einrichtung der Netzwerkkonfiguration aus und schließt die Bereitstellung ab.  

Auf der Seite für die Azure Monitor-Metriken kann Claudia den Verbrauch des Data Map-Speichers und -Operationsdurchsatzes sehen. Sie kann darüber hinaus eine Warnung für den Fall einrichten, dass der Speicher oder Operationsdurchsatz einen bestimmten Grenzwert erreicht, um den Verbrauch und die Abrechnung des neuen Purview-Kontos zu überwachen.  

## <a name="data-map-billing"></a>Data Map-Abrechnung

Kunden wird eine Kapazitätseinheit (25 Operationen pro Sekunde und 2 GB) in Rechnung gestellt, und die zusätzliche Abrechnung basiert auf dem Verbrauch jeder zusätzlichen Kapazitätseinheit pro Stunde. Die Data Map-Operationen werden in Schritten von 25 Operationen pro Sekunde skaliert, und der Metadatenspeicher wird in Schritten von 2 GB skaliert. Purview Data Map kann innerhalb des Elastizitätsfensters automatisch hoch- und herunterskalieren ([überprüfen Sie aktuelle Einschränkungen](how-to-manage-quotas.md)). Um jedoch die nächste Elastizitätsfensterebene zu erhalten, muss ein Supportticket erstellt werden.

Die Data Map-Kapazitätseinheiten verfügen über eine Obergrenze für Operationsdurchsatz und Speicher. Wenn der Speicher die aktuelle Kapazitätseinheit überschreitet, wird Kunden die nächste Kapazitätseinheit in Rechnung gestellt, auch wenn der Operationsdurchsatz nicht verwendet wird. Die folgende Tabelle zeigt die Data Map-Kapazitätseinheitenbereiche. Wenden Sie sich an den Support, wenn die Data Map-Kapazitätseinheit über 100 Kapazitätseinheiten hinausgeht.

|Data Map-Kapazitätseinheit  |Operationen pro Sekunde (Durchsatz)   |Speicherkapazität in GB|
|----------|-----------|------------|
|1    |25      |2     |
|2    |50      |4     |
|3    |75      |6     |
|4    |100     |8     |
|5    |125     |10    |
|6    |150     |12   |
|7    |175      |14     |
|8    |200     |16    |
|9    |225      |18    |
|10    |250     |20    |
|..   |..      |..     |
|100    |2500     |200   |

### <a name="billing-examples"></a>Abrechnungsbeispiele

- Der Purview Data Map-Operationsdurchsatz für die angegebene Stunde beträgt 25 Operationen pro Sekunde oder weniger und die Speichergröße beträgt 1 GB. Kunden wird eine Kapazitätseinheit in Rechnung gestellt.

- Der Purview Data Map-Operationsdurchsatz für die angegebene Stunde beträgt 25 Operationen pro Sekunde oder weniger und die Speichergröße beträgt 3 GB. Kunden werden zwei Kapazitätseinheiten in Rechnung gestellt.

- Der Purview Data Map-Operationsdurchsatz für die angegebene Stunde beträgt 50 Operationen pro Sekunde und die Speichergröße beträgt 1 GB. Kunden werden zwei Kapazitätseinheiten in Rechnung gestellt.

- Der Purview Data Map-Operationsdurchsatz für die angegebene Stunde beträgt 50 Operationen pro Sekunde und die Speichergröße beträgt 5 GB. Kunden werden drei Kapazitätseinheiten in Rechnung gestellt.

- Der Purview Data Map-Operationsdurchsatz für die angegebene Stunde beträgt 250 Operationen pro Sekunde und die Speichergröße beträgt 5 GB. Kunden werden zehn Kapazitätseinheiten in Rechnung gestellt.

### <a name="detailed-billing-example"></a>Detailliertes Abrechnungsbeispiel

Das folgende Data Map-Abrechnungsbeispiel zeigt eine Data Map mit wachsendem Metadatenspeicher und variablen Operationen pro Sekunde in einem Sechs-Stunden-Fenster von 12:00 bis 18:00 Uhr. Die rote Linie im Diagramm ist der Verbrauch von Operationen pro Sekunde, und die blaue gepunktete Linie ist der Metadatenspeicherverbrauch in diesem sechsstündigen Fenster:

:::image type="content" source="./media/concept-elastic-data-map/operations-and-metadata.png" alt-text="Diagramm, das die Anzahl von Operationen und das Wachstum von Metadaten im Zeitverlauf darstellt.":::

Jede Data Map-Kapazitätseinheit unterstützt 25 Operationen pro Sekunde und 2 GB Metadatenspeicher. Die Data Map wird auf Stundenbasis abgerechnet. Ihnen wird die maximale Data Map-Kapazitätseinheit in Rechnung gestellt, die innerhalb einer Stunde benötigt wird. Manchmal benötigen Sie möglicherweise mehr Operationen pro Sekunde innerhalb der Stunde, und dies erhöht die Anzahl der innerhalb dieser Stunde benötigten Kapazitätseinheiten. In anderen Fällen nutzen Sie möglicherweise nur wenige Operationen pro Sekunde, benötigen jedoch möglicherweise trotzdem ein großes Volumen an Metadatenspeicher. Der Metadatenspeicher bestimmt, wie viele Kapazitätseinheiten Sie innerhalb der Stunde benötigen.

Die folgende Tabelle zeigt die maximale Anzahl von Operationen pro Sekunde und den Metadatenspeicher, die pro Stunde für dieses Abrechnungsbeispiel verwendet werden: 

:::image type="content" source="./media/concept-elastic-data-map/billing-table.png" alt-text="Tabelle, die die maximale Anzahl von Operationen und das Wachstum von Metadaten im Zeitverlauf darstellt.":::

Basierend auf dem Verbrauch der Operationen pro Sekunde und des Metadatenspeichers der Data Map in diesem Zeitraum würden für diese Data Map 20 Kapazitätseinheitenstunden über diesen Sechs-Stunden-Zeitraum (1 + 3 + 4 + 3 + 6 + 3) in Rechnung gestellt:

:::image type="content" source="./media/concept-elastic-data-map/billing-capacity-hours.png" alt-text="Tabelle mit der Anzahl der Kapazitätseinheitenstunden im Zeitverlauf":::

>[!Important]
>Purview Data Map kann innerhalb des Elastizitätsfensters automatisch hoch- und herunterskalieren ([überprüfen Sie aktuelle Einschränkungen](how-to-manage-quotas.md)). Um die nächste Elastizitätsfensterebene zu erhalten, muss ein Supportticket erstellt werden.

## <a name="summary"></a>Zusammenfassung

Mit einer elastischen Data Map bietet Purview niedrige Kostenschwellen für Kunden, um ihre Datengovernance-Journey zu starten.
Purview Data Map kann mit einem Modell für die nutzungsbasierte Bezahlung bereits ab einer Kapazitätseinheit elastisch wachsen.
Kunden müssen sich bei der Bereitstellung keine Gedanken über die Auswahl der richtigen Data Map-Größe für ihren Datenbestand machen und sich zukünftig nicht mit Plattformmigrationen aufgrund von Größenbeschränkungen befassen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure Purview-Kontos](create-catalog-portal.md)
- [Purview Pricing](https://azure.microsoft.com/pricing/details/azure-purview/) (Purview – Preise)

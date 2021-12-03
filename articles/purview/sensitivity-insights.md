---
title: Berichterstellung zu Vertraulichkeitsbezeichnungen für Ihre Daten in Azure Purview mit Purview-Erkenntnissen
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Berichterstellung zu Vertraulichkeitsbezeichnungen für Ihre Daten in Purview anzeigen und verwenden.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2c030d419349275204e662d78396543f5517ceca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847221"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Vertraulichkeitsbezeichnungs-Erkenntnisse zu Ihren Daten in Azure Purview

In diesem Leitfaden ist beschrieben, wie Sie auf Sicherheitserkenntnisse zugreifen, die von Vertraulichkeitsbezeichnungen bereitgestellt werden, die auf Ihre Daten angewendet wurden, und wie Sie diese Sicherheitserkenntnisse anzeigen und filtern.

> [!IMPORTANT]
> Erkenntnisse für Azure Purview-Vertraulichkeitsbezeichnungen befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Folgende Datenquellen werden unterstützt: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance, Amazon S3 Buckets, Amazon RDS-Datenbanken (öffentliche Vorschau), Power BI.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> - Starten Ihres Purview-Kontos über Azure
> - Anzeigen von Vertraulichkeitsbezeichnungs-Erkenntnissen zu Ihren Daten
> - Ausführen von Drilldowns zu weiteren Vertraulichkeitsbezeichnungsdetails zu Ihren Daten

 
## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Füllen der entsprechenden Konten mit Testdaten

- [Erweitern der Microsoft 365-Vertraulichkeitsbezeichnungen auf Objekte (Assets) in Azure Purview](create-sensitivity-label.md) und Erstellen oder Auswählen der Bezeichnungen, die Sie auf Ihre Daten anwenden möchten

- Einrichten und Abschließen einer Überprüfung der Testdaten in den einzelnen Datenquellen. Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md) und [Erstellen eines Scan-Regelsatzes](create-a-scan-rule-set.md).

- Anmeldung bei Purview mit einem Konto mit der Rolle [„Datenleser“ oder „Datenkurator“](catalog-permissions.md#roles)

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md) und [Automatisches Bezeichnen Ihrer Daten in Azure Purview](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Verwenden von Purview-Vertraulichkeitsbezeichnungs-Erkenntnissen

In Purview ähneln Klassifizierungen Betrefftags und werden zum Kennzeichnen und Identifizieren von Daten eines bestimmten Typs verwendet, die während der Überprüfung in Ihrer Datenumgebung gefunden werden.

Vertraulichkeitsbezeichnungen ermöglichen es Ihnen anzugeben, wie vertraulich bestimmte Daten in Ihrer Organisation sind. Beispielsweise kann ein bestimmter Projektname in Ihrer Organisation streng vertraulich sein, während derselbe Begriff in anderen Organisationen nicht vertraulich ist. 

Klassifizierungen werden direkt abgeglichen, z. B. eine Sozialversicherungsnummer, die über die Klassifizierung **Sozialversicherungsnummer** verfügt. 

Im Gegensatz dazu werden Vertraulichkeitsbezeichnungen angewendet, wenn eine oder mehrere Klassifizierungen und Bedingungen gleichzeitig gefunden werden. In diesem Kontext beziehen sich [Bedingungen](/microsoft-365/compliance/apply-sensitivity-label-automatically) auf alle Parameter, die Sie für unstrukturierte Daten definieren können, z. B. **Nähe zu einer anderen Klassifizierung** und **% Konfidenz**. 

In Purview werden die gleichen Klassifizierungen (auch als [vertrauliche Informationstypen](/microsoft-365/compliance/sensitive-information-type-entity-definitions) bezeichnet) wie in Microsoft 365 verwendet. Dies ermöglicht Ihnen, Ihre vorhandenen Vertraulichkeitsbezeichnungen auf Ihre Azure Purview-Ressourcen auszuweiten.

> [!NOTE]
> Nachdem Sie Ihre Quelltypen überprüft haben, geben Sie den Erkenntnissen zur **Vertraulichkeitsbezeichnung** einige Stunden Zeit, um die neuen Ressourcen zu berücksichtigen.

**So zeigen Sie Vertraulichkeitsbezeichnungs-Erkenntnisse an:**

1. Navigieren Sie zur **Azure Purview**-Startseite.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kontokachel **Purview starten** aus.

1. Wählen Sie in Purview das Menüelement **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: auf der linken Seite aus, um auf Ihren **Erkenntnisse**-Bereich zuzugreifen.

1. Wählen Sie im Bereich **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: die Option **Vertraulichkeitsbezeichnungen** aus, um den Purview-Bericht **Vertraulichkeitsbezeichnungs-Erkenntnisse** anzuzeigen.

    > [!NOTE]
    > Ist dieser Bericht leer, haben Sie Ihre Vertraulichkeitsbezeichnungen möglicherweise nicht auf Azure Purview erweitert. Weitere Informationen finden Sie unter [Automatisches Bezeichnen Ihrer Daten in Azure Purview](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Vertraulichkeitsbezeichnungs-Erkenntnisse":::

   Die Hauptseite für **Vertraulichkeitsbezeichnungs-Erkenntnisse** zeigt die folgenden Bereiche an:

   |Bereich  |BESCHREIBUNG  |
   |---------|---------|
   |**Übersicht der Quellen mit Vertraulichkeitsbezeichnungen**     |Zeigt Kacheln mit folgenden Informationen an: <br>- Die Anzahl der in Ihren Daten gefundenen Abonnements <br>- Die Anzahl eindeutiger Vertraulichkeitsbezeichnungen, die auf Ihre Daten angewendet werden <br>- Die Anzahl der Quellen mit angewendeten Vertraulichkeitsbezeichnungen <br>- Die Anzahl der gefundenen Dateien und Tabellen mit angewendeten Vertraulichkeitsbezeichnungen|
   |**Hauptquellen mit bezeichneten Daten (letzte 30 Tage)**     | Zeigt den Trend über die letzten 30 Tage für die Anzahl der Quellen mit angewendeten Vertraulichkeitsbezeichnungen.       |
   |**Hauptbezeichnungen, die auf alle Quellen angewendet werden**     |Zeigt die Hauptbezeichnungen an, die auf alle Ihrer Purview-Datenressourcen angewendet werden. |
   |**Hauptbezeichnungen, die auf Dateien angewendet werden**     |Zeigt die Hauptvertraulichkeitsbezeichnungen an, die auf Dateien in Ihren Daten angewendet werden.          |
   |**Hauptbezeichnungen, die auf Tabellen angewendet werden**     | Zeigt die Hauptvertraulichkeitsbezeichnungen an, die auf Datenbanktabellen in Ihren Daten angewendet werden. |   
   |  **Bezeichnungsaktivität**  |  Zeigt separate Graphen für Dateien und Tabellen an, die jeweils die Anzahl der bezeichneten Dateien oder Tabellen über den ausgewählten Zeitraum anzeigen. <br>**Standard:** 30 Tage<br>Wählen Sie den Filter **Zeit** oberhalb der Graphen aus, um einen anderen Zeitrahmen für die Anzeige auszuwählen.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Drilldown für Vertraulichkeitsbezeichnungs-Erkenntnisse

Wählen Sie in einem der folgenden **Vertraulichkeitsbezeichnungs-Erkenntnisse**-Graphen den Link **Mehr anzeigen** aus, um weitere Details zu erhalten:

- **Hauptbezeichnungen, die auf alle Quellen angewendet werden**
- **Hauptbezeichnungen, die auf Dateien angewendet werden**
- **Hauptbezeichnungen, die auf Tabellen angewendet werden**
- **Bezeichnungsaktivität > Bezeichnete Daten**

Beispiel:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Drilldown zu Vertraulichkeitsbezeichnungen":::

Führen Sie einen der folgenden Schritte aus, um weitere Informationen zu erhalten:

|Option  |BESCHREIBUNG  |
|---------|---------|
|**Filtern der Daten**     |  Verwenden Sie die Filter oberhalb des Rasters, um die angezeigten Daten zu filtern, einschließlich des Bezeichnungsnamens, des Abonnementnamens oder des Quellentyps. <br><br>Wenn Sie sich bezüglich des genauen Bezeichnungsnamens nicht sicher sind, können Sie einen Teil oder den ganzen Namen in das Feld **Nach Schlüsselwort filtern** eingeben.       |
|**Sortieren des Rasters** |Wählen Sie einen Spaltenheader aus, um das Raster nach dieser Spalte zu sortieren. | 
|**Spalten bearbeiten**     |  Wenn Sie mehr oder weniger Spalten in Ihrem Raster anzeigen möchten, wählen Sie **Spalten bearbeiten** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: und dann die Spalten aus, die Sie anzeigen oder deren Reihenfolge Sie ändern möchten.    <br><br>Wählen Sie einen Spaltenheader aus, um das Raster nach dieser Spalte zu sortieren.   |
|**Weitere Detailinformationen anzeigen**     | Um einen Drilldown zu einer bestimmten Bezeichnung auszuführen, wählen Sie in der Spalte **Vertraulichkeitsbezeichnung** einen Namen aus, um den **Bezeichnung nach Quelle**-Bericht anzuzeigen. <br><br>In diesem Bericht werden Daten für die ausgewählte Bezeichnung angezeigt, einschließlich Quellenname, Quellentyp, Abonnement-ID und der Anzahl der klassifizierten Dateien und Tabellen.      |
|**Ressourcen durchsuchen**     |  Um die mit einer bestimmten Bezeichnung oder Quelle gefundenen Ressourcen (Assets) zu durchsuchen, wählen Sie, je nach angezeigtem Bericht, Bezeichnungen oder Quellen und dann **Ressourcen durchsuchen** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: oberhalb der Filter aus. <br><br>Die Suchergebnisse zeigen alle bezeichneten Objekte (Assets) an, die für den ausgewählten Filter gefunden wurden.  Weitere Informationen finden Sie unter [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Vertraulichkeitsbezeichnungs-Integration mit Microsoft 365-Konformität

Enge Integration in das [Microsoft Information Protection](/microsoft-365/compliance/information-protection)-Paket, das mit Microsoft 365 bereitgestellt wird, bedeutet, dass Purview direkte Möglichkeiten bietet, die Sichtbarkeit in ihren Datenbestand zu erweitern und Ihre Daten zu klassifizieren und zu bezeichnen.

Damit Ihre Microsoft 365-Vertraulichkeitsbezeichnungen auf Ihre Assets in Azure Purview erweitert werden können, müssen Sie Information Protection für Azure Purview explizit im Microsoft 365 Compliance Center aktivieren.

Weitere Informationen finden Sie unter [Automatisches Bezeichnen Ihrer Daten in Azure Purview](create-sensitivity-label.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über diese Azure Purview-Erkenntnisberichte:

- [Glossarerkenntnisse](glossary-insights.md)
- [Überprüfungserkenntnisse](scan-insights.md)
- [Klassifizierungs-Erkenntnisse](./classification-insights.md)

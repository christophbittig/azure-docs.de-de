---
title: Überwachen von Azure Cosmos DB mit Cosmos DB-Erkenntnissen von Azure Monitor | Microsoft-Dokumentation
description: In diesem Artikel wird das Feature für Cosmos DB-Erkenntnisse von Azure Monitor beschrieben, das Cosmos DB-Besitzern ein schnelles Verständnis der Leistungs- und Nutzungsprobleme bei ihren CosmosDB-Konten ermöglicht.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3a3a87a3d639c2f5d0211e488340ab918c339ba0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131921"
---
# <a name="explore-azure-monitor-cosmos-db-insights"></a>Informationen zu Cosmos DB-Erkenntnissen von Azure Monitor

Das Feature für Cosmos DB-Erkenntnisse bietet eine Übersicht über Gesamtleistung, Fehler, Kapazität und Betriebsintegrität aller Ihrer Azure Cosmos DB-Ressourcen auf einer vereinheitlichten interaktiven Benutzeroberfläche. Anhand dieses Artikels können Sie sich mit den Vorteilen dieser neuen Überwachungsoberfläche vertraut machen, und Sie erfahren, wie Sie diese entsprechend den konkreten Anforderungen Ihrer Organisation ändern und anpassen können.   

## <a name="introduction"></a>Einführung

Bevor Sie sich mit der Oberfläche genauer befassen, sollten Sie nachvollziehen können, wie sie Informationen darstellt und visualisiert. 

Vorteile:

* **Sicht im gewünschten Umfang** auf Ihre Azure Cosmos DB-Ressourcen in all Ihren Abonnements an einem zentralen Ort, wobei Sie den Bereich selektiv auf die Abonnements und Ressourcen festlegen können, die Sie bewerten möchten.

* **Drilldownanalyse** einer bestimmten Azure CosmosDB-Ressource, um Probleme zu diagnostizieren oder eine detaillierte Analyse nach Kategorien durchzuführen – Nutzung, Fehler, Kapazität und Vorgänge. Wenn Sie eine dieser Optionen auswählen, erhalten Sie eine detaillierte Übersicht über die relevanten Azure Cosmos DB-Metriken.  

* **Anpassbar**: Diese Oberfläche baut auf Azure Monitor-Arbeitsmappenvorlagen auf, sodass Sie ändern können, welche Metriken angezeigt werden, Schwellenwerte entsprechend Ihren Grenzwerten ändern oder festlegen können und die Änderungen in einer benutzerdefinierten Arbeitsmappe speichern können. Diagramme in der Arbeitsmappe können an Azure-Dashboards angeheftet werden.  

Bei diesem Feature müssen Sie nichts aktivieren oder konfigurieren, da diese Azure Cosmos DB-Metriken standardmäßig erfasst werden.

>[!NOTE]
>Für den Zugriff auf dieses Feature fallen keine Gebühren an. Ihnen werden nur die grundlegenden Features von Azure Monitor in Rechnung gestellt, die Sie entsprechend der Beschreibung auf der Seite [Azure Monitor-Preisdetails](https://azure.microsoft.com/pricing/details/monitor/) konfigurieren oder aktivieren.

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Anzeigen von Nutzungs- und Leistungsmetriken für Azure Cosmos DB

Führen Sie die folgenden Schritte aus, um die Nutzung und Leistung Ihrer Speicherkonten in allen ihren Abonnements anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Monitor**, und wählen Sie **Monitor** aus.

    ![Suchfeld mit dem Wort „Monitor“, und eine Dropdownliste „Dienste“ mit dem Eintrag „Monitor“ mit einem Tachometersymbol](./media/cosmosdb-insights-overview/search-monitor.png)

3. Wählen Sie **Cosmos DB**.

    ![Screenshot der Arbeitsmappe mit Cosmos DB-Übersicht](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Übersicht

In der **Übersicht** werden in der Tabelle interaktive Azure Cosmos DB-Metriken aufgeführt. Sie können die Ergebnisse anhand der Optionen filtern, die Sie in den folgenden Dropdownlisten auswählen:

* **Abonnements**: Es werden nur Abonnements aufgelistet, die über eine Azure Cosmos DB-Ressource verfügen.  

* **Cosmos DB**: Sie können alle oder eine Teilmenge von Azure Cosmos DB-Ressourcen oder eine einzelne Azure Cosmos DB-Ressource auswählen.

* **Zeitbereich** – Standardmäßig werden die Informationen aus den letzten 4 Stunden basierend auf der entsprechenden Auswahl angezeigt.

In der Zähler-Kachel unter den Dropdownlisten wird die Gesamtzahl der Azure Cosmos DB-Ressourcen in den ausgewählten Abonnements angezeigt. Für Spalten in der Arbeitsmappe, die Transaktionsmetriken melden, gibt es eine bedingte Farbcodierung oder Wärmebilder. Die dunkelste Farbe hat den höchsten Wert, und eine hellere Farbe basiert auf den niedrigsten Werten. 

Wenn Sie einen Dropdownpfeil neben einer der Azure Cosmos DB-Ressourcen auswählen, wird eine Aufschlüsselung der Leistungsmetriken auf der jeweiligen Datenbankcontainerebene eingeblendet:

![Erweiterte Dropdownliste, in der einzelne Datenbankcontainer und die zugehörige Leistungsaufschlüsselung angezeigt werden](./media/cosmosdb-insights-overview/container-view.png)

Wenn Sie den blau hervorgehobenen Azure Cosmos DB-Ressourcennamen auswählen, rufen Sie die standardmäßige **Übersicht** für das zugehörige Azure Cosmos DB-Konto auf. 

### <a name="failures"></a>Fehler

Wählen Sie oben auf der Seite **Fehler** aus, und der Bereich **Fehler** der Arbeitsmappenvorlage wird geöffnet. Hier wird die Gesamtzahl der Anforderungen mit der Verteilung der Antworten angezeigt, die für die Anforderungen vorhanden sind:

![Screenshot von Fehlern mit Aufschlüsselung nach HTTP-Anforderungstyp](./media/cosmosdb-insights-overview/failures.png)

| Code |  BESCHREIBUNG       | 
|-----------|:--------------------|
| `200 OK`  | Einer der folgenden REST-Vorgänge war erfolgreich: </br>- GET für eine Ressource. </br> - PUT für eine Ressource. </br> - POST für eine Ressource. </br> - POST für eine gespeicherte Prozedur zum Ausführen der gespeicherten Prozedur.|
| `201 Created` | Ein POST-Vorgang zum Erstellen einer Ressource war erfolgreich. |
| `404 Not Found` | Es wird versucht, eine nicht mehr vorhandene Ressource zu verarbeiten. Beispiel: Die Ressource wurde möglicherweise bereits gelöscht. |

Eine vollständige Liste der Statuscodes finden Sie im Artikel zu [Azure Cosmos DB HTTP-Statuscodes](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacity

Wählen Sie oben auf der Seite **Kapazität** aus, und der Bereich **Kapazität** der Arbeitsmappenvorlage wird geöffnet. Hier werden die Anzahl der vorhandenen Dokumente, der Dokumentzuwachs über die Zeit, die Datennutzung sowie die Gesamtmenge des verbleibenden verfügbaren Speichers angezeigt.  Anhand dieser Angaben können Sie potenzielle Speicher- und Datennutzungsprobleme ermitteln.

![Arbeitsmappe „Kapazität“](./media/cosmosdb-insights-overview/capacity.png) 

Wie bei der Übersichtsarbeitsmappe wird beim Auswählen der Dropdownliste neben einer Azure Cosmos DB-Ressource in der Spalte **Abonnement** eine Aufschlüsselung nach den einzelnen Containern der Datenbank angezeigt.

### <a name="operations"></a>Operationen (Operations)

Wählen Sie oben auf der Seite **Vorgänge** aus, und der Bereich **Vorgänge** der Arbeitsmappenvorlage wird geöffnet. Hier können Sie Ihre Anforderungen überprüfen, aufgeschlüsselt nach dem Typ der getätigten Anforderungen.

Im folgenden Beispiel ist ersichtlich, dass `eastus-billingint` hauptsächlich Leseanforderungen empfängt, jedoch auch eine kleine Anzahl von Upsert- und Erstellungsanforderungen vorhanden ist. `westeurope-billingint` hingegen ist in Bezug auf Anforderungen schreibgeschützt, zumindest über die letzten vier Stunden, auf die der Bereich der Arbeitsmappe derzeit über ihren Zeitbereichsparameter beschränkt ist.

![Bereich „Vorgänge“ der Arbeitsmappe](./media/cosmosdb-insights-overview/operation.png)

## <a name="view-from-an-azure-cosmos-db-resource"></a>Anzeigen von einer Azure Cosmos DB-Ressource aus

1. Suchen Sie nach Ihren vorhandenen Azure Cosmos DB-Konten, oder wählen Sie sie aus.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-search.png" alt-text="Suche nach Azure Cosmos DB." border="true":::

2. Wählen Sie nach der Navigation zu Ihrem Azure Cosmos DB-Konto im Abschnitt „Überwachung“ die Option **Erkenntnisse (Vorschau)** oder **Arbeitsmappen** aus, um weitere Analysen zu Durchsatz, Anforderungen, Speicherung, Verfügbarkeit, Latenz, System und Kontoverwaltung durchzuführen.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-overview.png" alt-text="Übersicht über Cosmos DB-Erkenntnisse." border="true":::

### <a name="time-range"></a>Uhrzeitbereich

Standardmäßig werden im Feld **Zeitbereich** Daten der **letzten 24 Stunden** angezeigt. Sie können den Zeitbereich so ändern, dass Daten von den letzten 5 Minuten bis zu den letzten sieben Tagen beliebig angezeigt werden. Die Zeitbereichsauswahl umfasst auch einen **benutzerdefinierten** Modus, in dem Sie die Start-/Enddaten eingeben können, um einen benutzerdefinierten Zeitrahmen auf der Grundlage der verfügbaren Daten für das ausgewählte Konto anzuzeigen.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-time-range.png" alt-text="Cosmos DB-Zeitbereich." border="true":::

### <a name="insights-overview"></a>Übersicht zu Erkenntnissen

Die Registerkarte **Übersicht** enthält die gängigsten Metriken für das ausgewählte Azure Cosmos DB-Konto, einschließlich:

* Anzahl von Anforderungen
* Fehlerhafte Anforderungen (429s)
* Normalisierter RU-Verbrauch (Höchstwert)
* Daten- & Indexnutzung
* Cosmos DB-Kontometriken nach Sammlung

**Anforderungen insgesamt:** Dieses Diagramm enthält eine Übersicht über die Gesamtanzahl der Anforderungen für das Konto, die nach Statuscode aufgeschlüsselt sind. Die Einheiten am unteren Rand des Diagramms stellen eine Summe der Gesamtanforderungen für den Zeitraum dar.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-total-requests.png" alt-text="Cosmos DB-Diagramm für Gesamtanforderungen." border="true":::

**Fehlerhafte Anforderungen (429s)** : Dieses Diagramm enthält eine Übersicht über fehlerhafte Anforderungen mit dem Statuscode 429. Die Einheiten am unteren Rand des Diagramms stellen eine Summe der gesamten fehlerhaften Anforderungen für den Zeitraum dar.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-429.png" alt-text="Cosmos DB-Diagramm für fehlerhafte Anforderungen." border="true":::

**Normalisierter RU-Verbrauch (Höchstwert)** : Dieses Diagramm gibt den maximalen Prozentsatz zwischen 0-100 % der normalisierten RU-Verbrauchseinheiten für den angegebenen Zeitraum an.

:::image type="content" source="./media/cosmosdb-insights-overview/cosmosdb-normalized-ru.png" alt-text="Normalisierter RU-Verbrauch in Cosmos DB." border="true":::

## <a name="pin-export-and-expand"></a>Anheften, Exportieren und Erweitern

Sie können einen beliebigen Metrikabschnitt an ein [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md) anheften, indem Sie oben rechts im Abschnitt das Stecknadelsymbol auswählen.

![Beispiel für das Anheften eines Metrikabschnitts an das Dashboard](./media/cosmosdb-insights-overview/pin.png)

Wenn Sie die Daten im Excel-Format exportieren möchten, wählen Sie das Abwärtspfeil-Symbol links neben dem Stecknadelsymbol aus.

![Symbol „Arbeitsmappe exportieren“](./media/cosmosdb-insights-overview/export.png)

Wenn Sie alle Dropdownansichten in der Arbeitsmappe erweitern oder reduzieren möchten, wählen Sie links vom Exportsymbol das Erweiterungssymbol aus:

![Symbol „Arbeitsmappe erweitern“](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-cosmos-db-insights"></a>Anpassen von Cosmos DB-Erkenntnissen

Da diese Oberfläche auf Azure Monitor-Arbeitsmappenvorlagen aufbaut, stehen Ihnen die Optionen **Anpassen** > **Bearbeiten** zur Verfügung, und mit **Speichern** können Sie eine Kopie der geänderten Version in einer benutzerdefinierten Arbeitsmappe speichern. 

![Symbolleiste zum Anpassen](./media/cosmosdb-insights-overview/customize.png)

Arbeitsmappen werden in einer Ressourcengruppe gespeichert, und zwar entweder im für Sie privaten Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte**, der für alle Benutzer mit Zugriff auf die Ressourcengruppe zugänglich ist. Nachdem Sie die benutzerdefinierte Arbeitsmappe gespeichert haben, müssen Sie zum Arbeitsmappenkatalog wechseln, um die Mappe zu starten.

![Starten des Arbeitsmappenkatalogs über die Befehlsleiste](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Problembehandlung

Anleitungen zur Problembehandlung finden Sie im Artikel zur [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](troubleshoot-workbooks.md).

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren Sie [Metrikwarnungen](../alerts/alerts-metric.md) und [Dienstintegritätsbenachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md), um automatisierte Warnungen einzurichten, die beim Erkennen von Problemen hilfreich sein können.

* Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können, und vieles mehr.

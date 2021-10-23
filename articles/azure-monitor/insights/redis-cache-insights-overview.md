---
title: Azure Monitor für Azure Cache for Redis | Microsoft-Dokumentation
description: In diesem Artikel wird das Azure Monitor für Azure Redis Cache-Feature beschrieben, das Cachebesitzern ein schnelles Verständnis der Leistungs- und Nutzungsprobleme ermöglicht.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 922909d3c66d53168188257063f943385db0a3d0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132130"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Informationen zu Azure Monitor für Azure Cache for Redis

Azure Monitor für Azure Cache for Redis bietet für alle Ihre Azure Cache for Redis-Ressourcen eine einheitliche, interaktive Ansicht folgender Aspekte:

- Gesamtleistung
- Fehler
- Capacity
- Betriebsintegrität

Dieser Artikel verdeutlicht die Vorteile dieser neuen Überwachungsoberfläche. Außerdem wird gezeigt, wie Sie die Oberfläche entsprechend den besonderen Anforderungen Ihrer Organisation ändern und anpassen können.

## <a name="introduction"></a>Einführung

Bevor Sie die Oberfläche aufrufen, sollten Sie nachvollziehen können, wie Informationen von Azure Monitor für Azure Cache for Redis visuell dargestellt werden.

Vorteile:

- **Sicht im gewünschten Umfang** auf Ihre Azure Cache for Redis-Ressourcen in all Ihren Abonnements an einem zentralen Ort. Sie können den Bereich selektiv auf die Abonnements und Ressourcen festlegen, die Sie bewerten möchten.

- **Drilldownanalyse** einer bestimmten Azure Cache for Redis-Ressource. Sie können Probleme diagnostizieren und detaillierte Analysen von Nutzung, Fehlern, Kapazität und Vorgängen anzeigen. Wählen Sie eine dieser Kategorien aus, um eine detaillierte Ansicht der relevanten Informationen anzuzeigen.  

- **Anpassung** dieser Oberfläche, die auf Azure Monitor-Arbeitsmappenvorlagen basiert. Auf der Oberfläche können Sie ändern, welche Metriken angezeigt werden, sowie Schwellenwerte entsprechend Ihren Grenzwerten ändern oder festlegen. Sie können die Änderungen in einer benutzerdefinierten Arbeitsmappe speichern und dann Arbeitsmappendiagramme an Azure-Dashboards anheften.

Bei diesem Feature müssen Sie nichts aktivieren oder konfigurieren. Azure Cache for Redis-Informationen werden standardmäßig erfasst.

>[!NOTE]
>Für den Zugriff auf dieses Feature fallen keine Gebühren an. Ihnen werden nur die grundlegenden Features von Azure Monitor in Rechnung gestellt, die Sie entsprechend der Beschreibung auf der Seite mit den [Azure Monitor-Preisdetails](https://azure.microsoft.com/pricing/details/monitor/) konfigurieren oder aktivieren.

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Anzeigen von Nutzungs- und Leistungsmetriken für Azure Cache for Redis

Führen Sie die folgenden Schritte aus, um die Nutzung und Leistung Ihrer Speicherkonten in allen Ihren Abonnements anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie nach **Monitor**, und wählen Sie **Monitor** aus.

    ![Suchfeld mit dem Wort „Monitor“ und das Ergebnis der Suche unter „Dienste“ mit dem Eintrag „Monitor“ mit einem Tachometersymbol](./media/cosmosdb-insights-overview/search-monitor.png)

1. Wählen Sie **Azure Cache for Redis** aus. Wenn diese Option nicht vorhanden ist, wählen Sie **Mehr** > **Azure Cache for Redis** aus.

### <a name="overview"></a>Übersicht

In der **Übersicht** werden in der Tabelle interaktive Azure Cache for Redis-Metriken aufgeführt. Sie können die Ergebnisse anhand der Optionen filtern, die Sie in den folgenden Dropdownlisten auswählen:

- **Abonnements**: Es werden nur Abonnements aufgelistet, die über eine Azure Cache for Redis-Ressource verfügen.  

- **Azure Cache for Redis:** Sie können alle oder eine Teilmenge von Azure Cache for Redis-Ressourcen oder eine einzelne solche Ressource auswählen.

- **Zeitbereich**: Standardmäßig werden in der Tabelle die Informationen der letzten vier Stunden basierend auf der entsprechenden Auswahl angezeigt.

Unter den Dropdownlisten befindet sich eine Zählerkachel. Die Kachel zeigt die Gesamtzahl der Azure Cache for Redis-Ressourcen in den ausgewählten Abonnements. Bedingte Farbcodes oder Wärmebilder für Spalten in der Arbeitsmappe melden Transaktionsmetriken. Die dunkelste Farbe stellt den höchsten Wert dar. Hellere Farben stellen niedrigere Werte dar.

Wenn Sie einen Dropdownlistenpfeil neben einer der Azure Cache for Redis-Ressourcen auswählen, wird eine Aufschlüsselung der Leistungsmetriken auf der jeweiligen Ressourcenebene eingeblendet.

![Screenshot der Übersichtsanzeige](./media/redis-cache-insights-overview/overview.png)

Wenn Sie den blau hervorgehobenen Azure Cache for Redis-Ressourcennamen auswählen, wird die Standardtabelle **Übersicht** für das zugehörige Konto angezeigt. Darin sind folgende Spalten enthalten:

- **Verwendeter Arbeitsspeicher**
- **Prozentsatz der Arbeitsspeicherverwendung**
- **Serverauslastung**
- **Serverlast: Zeitachse**
- **CPU**
- **Verbundene Clients**
- **Cachefehler**
- **Fehler (max.)**

### <a name="operations"></a>Operationen (Operations)

Wenn Sie oben auf der Seite **Vorgänge** auswählen, wird die Tabelle **Vorgänge** der Arbeitsmappenvorlage geöffnet. Darin sind folgende Spalten enthalten:

- **Vorgänge gesamt**
- **Vorgänge gesamt: Zeitachse**
- **Vorgänge pro Sekunde**
- **Get-Vorgänge**
- **Set-Vorgänge**

![Screenshot der Vorgangsanzeige](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Verwendung

Wenn Sie oben auf der Seite **Nutzung** auswählen, wird die Tabelle **Nutzung** der Arbeitsmappenvorlage geöffnet. Darin sind folgende Spalten enthalten:

- **Cache-Lesevorgänge**
- **Cache-Lesevorgänge: Zeitachse**
- **Cache-Schreibvorgänge**
- **Cachetreffer**
- **Cachefehler**

![Screenshot der Nutzungsanzeige](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Fehler

Wenn Sie oben auf der Seite **Fehler** auswählen, wird die Tabelle **Fehler** der Arbeitsmappenvorlage geöffnet. Darin sind folgende Spalten enthalten:

- **Fehler gesamt**
- **Failover/Fehler**
- **Client reagiert nicht/Fehler**
- **RDB/Fehler**
- **AOF/Fehler**
- **Export/Fehler**
- **Datenverlust/Fehler**
- **Import/Fehler**

![Screenshot von Fehlern mit Aufschlüsselung nach HTTP-Anforderungstyp](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Metrikdefinitionen

Eine vollständige Liste der Metrikdefinitionen, die diese Arbeitsmappen bilden, finden Sie im [Artikel zu verfügbaren Metriken und Berichtsintervallen](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Anzeigen von einer Azure Cache for Redis-Ressource aus

Führen Sie die folgenden Schritte aus, um von einer einzelnen Ressource aus direkt auf Azure Monitor für Azure Cache for Redis zuzugreifen:

1. Wählen Sie im Azure-Portal die Option „Azure Cache for Redis“ aus.

2. Wählen Sie in der Liste eine einzelne Azure Cache for Redis-Ressource aus. Wählen Sie im Abschnitt „Überwachung“ die Option „Insights“ aus.

    ![Screenshot der Menüoptionen mit dem Wort „Insights “ in einem roten Kasten](./media/redis-cache-insights-overview/insights.png)

Alternativ können Sie auch in der Arbeitsmappe auf Azure Monitor-Ebene den Ressourcennamen einer Azure Cache for Redis-Ressource auswählen, um auf diese Ansichten zuzugreifen.

### <a name="resource-level-overview"></a>Übersicht auf Ressourcenebene

In der Arbeitsmappe **Übersicht** für Azure Redis Cache werden mehrere Leistungsmetriken angezeigt, die Ihnen den Zugriff auf Folgendes ermöglichen:

- Interaktive Leistungsdiagramme mit den wichtigsten Details zur Leistung von Azure Cache for Redis.

- Metriken und Statuskacheln zur Hervorhebung der Shardleistung, der Gesamtzahl verbundener Clients und der Gesamtwartezeit.

![Screenshot des Übersichtsdashboards mit Informationen zu CPU-Leistung, verwendetem Arbeitsspeicher, verbundenen Clients, Fehlern, abgelaufenen Schlüsseln und entfernten Schlüsseln](./media/redis-cache-insights-overview/resource-overview.png)

Durch Auswählen einer der anderen Registerkarten für **Leistung** oder **Vorgänge** gelangen Sie zu den entsprechenden Arbeitsmappen.

### <a name="resource-level-performance"></a>Leistung auf Ressourcenebene

![Screenshot der Diagramme zur Ressourcenleistung](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Vorgänge auf Ressourcenebene

![Screenshot der Diagramme zu Ressourcenvorgängen](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Anheften, Exportieren und Erweitern

Zum Anheften eines Metrikabschnitts an ein [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md) wählen Sie das Reißzweckensymbol in der oberen rechten Ecke des Abschnitts aus.

![Metrikabschnitt mit hervorgehobenem Reißzweckensymbol](./media/cosmosdb-insights-overview/pin.png)

Wenn Sie die Daten in einem Excel-Format exportieren möchten, wählen Sie das Abwärtspfeilsymbol links neben dem Reißzweckensymbol aus.

![Hervorgehobenes Symbol zum Exportieren einer Arbeitsmappe](./media/cosmosdb-insights-overview/export.png)

Wenn Sie alle Ansichten in einer Arbeitsmappe erweitern oder reduzieren möchten, wählen Sie links vom Exportsymbol das Erweiterungssymbol aus.

![Hervorgehobenes Symbol zum Erweitern einer Arbeitsmappe](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Anpassen von Azure Monitor für Azure Cache for Redis

Da diese Oberfläche auf Azure Monitor-Arbeitsmappenvorlagen basiert, können Sie **Anpassen** > **Bearbeiten** > **Speichern** auswählen, um eine Kopie der geänderten Version in einer benutzerdefinierten Arbeitsmappe zu speichern.

![Befehlsleiste mit hervorgehobener Option „Anpassen“](./media/cosmosdb-insights-overview/customize.png)

Arbeitsmappen werden innerhalb einer Ressourcengruppe entweder im Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte** gespeichert. **Meine Berichte** steht nur Ihnen zur Verfügung. **Freigegebene Berichte** steht allen Benutzern mit Zugriff auf die Ressourcengruppe zur Verfügung.

Nachdem Sie eine benutzerdefinierte Arbeitsmappe gespeichert haben, wechseln Sie zum Arbeitsmappenkatalog, um sie zu öffnen.

![Befehlsleiste mit hervorgehobener Option „Katalog“](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Problembehandlung

Anleitungen zur Problembehandlung finden Sie im Artikel zur [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](troubleshoot-workbooks.md).

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren Sie [Metrikwarnungen](../alerts/alerts-metric.md) und [Dienstintegritätsbenachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md), um automatisierte Warnungen einzurichten, die beim Erkennen von Problemen hilfreich sind.

* Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) über die von Arbeitsmappen unterstützten Szenarien, wie Sie Berichte erstellen und anpassen können, und vieles mehr.

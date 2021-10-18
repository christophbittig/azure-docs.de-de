---
title: 'Überwachen von Azure Database for MySQL: Flexible Server mit Azure Monitor-Arbeitsmappen'
description: 'Beschreibt, wie Sie Azure Database for MySQL: Flexible Server mit Azure Monitor-Arbeitsmappen überwachen.'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621023"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>Überwachen von Azure Database for MySQL: Flexible Server mit Azure Monitor-Arbeitsmappen

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL: Flexible Server ist jetzt in Azure Monitor Arbeitsmappen integriert. Arbeitsmappen bieten einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals. Mit ihnen können Sie mehrere Datenquellen in Azure nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren. Arbeitsmappenvorlagen dienen als zusammengestellte Berichte, die von mehreren Benutzern und Teams flexibel wiederverwendet werden können. Beim Öffnen einer Vorlage wird eine temporäre Arbeitsmappe mit dem Inhalt der Vorlage erstellt. Mit dieser Integration verfügt der Server über einen Link zu Arbeitsmappen und einige Beispielvorlagen, mit denen der Dienst im großen Stil überwacht werden kann. Diese Vorlagen können bearbeitet, an die Kundenanforderungen angepasst und an das Dashboard angeheftet werden, um eine fokussierte und geordnete Ansicht von Azure-Ressourcen zu erstellen.
 
In diesem Artikel erfahren Sie mehr über die verschiedenen Arbeitsmappenvorlagen, die für Ihren flexiblen Server verfügbar sind

Für Azure Database for MySQL: Flexible Server stehen drei Standardvorlagen zur Verfügung.
 
- **Übersicht:** Zeigt die Instanzzusammenfassung und Top-Level-Metriken an, um die Ressourcennutzung auf Ihrem Server zu verstehen. Die folgenden Details werden für Azure Database for MySQL: Flexible Server angezeigt.

    * Serverübersicht 
    * Datenbankzusammenfassung
    * Verbindungsmetriken 
    * Leistungsmetriken 
    * Speichermetriken 

* **Überwachung:** Zusammenfassung und Details zu den für den Server gesammelten Überwachungsereignissen. Die folgenden Ansichten sind mit dieser Vorlage für Azure Database for MySQL: Flexible Server verfügbar.

    * Administrative Aktionen für den Dienst
    * Zusammenfassung der Überwachung
    * Zusammenfassung der Überwachungsverbindungsereignisse
    * Überwachungsverbindungsereignisse
    * Zusammenfassung des Tabellenzugriffs
    * Identifizierte Fehler

* **Einblicke in die Abfrageleistung:** Zusammenfassung und Details zur Abfrageworkload für die Instanz, zu zeitintensivewn Abfragen, zu langsamen Abfrageanalysen und zu Verbindungsmetriken. Die folgende Ansicht ist mit dieser Vorlage für Azure Database for MySQL: Flexible Server verfügbar.

    * Abfragelast
    * Aktive Verbindungen gesamt
    * Trend langsamer Abfragen (Abfragezeit > 10 Sekunden)
    * Details zu langsamen Abfragen
    * Auflisten der fünf Abfragen mit der längsten Dauer
    * Zusammenfassen langsamer Abfragen nach minimaler, maximaler und durchschnittlicher Abfragezeit sowie der Abfragezeit mit Standardabweichung

Sie können diese Vorlagen auch bearbeiten und entsprechend Ihren Anforderungen anpassen. Weitere Informationen finden Sie unter [Übersicht über Azure Monitor-Arbeitsmappen: Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).

 ## <a name="how-to-access-workbook-templates"></a>Zugreifen auf Arbeitsmappenvorlagen

Zum Anzeigen der Vorlagen navigieren Sie im Azure-Portal zum Blatt **Überwachung** für Azure Database for MySQL: Flexible Server, und wählen Sie **Arbeitsmappen** aus.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Diagramm: Darstellung der Arbeitsmappen.":::

Sie können auch die Liste der Vorlagen anzeigen, indem Sie zu **Öffentliche Vorlagen** navigieren.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Diagramm: Vorlagen von Arbeitsmappen":::


## <a name="next-steps"></a>Nächste Schritte
- [Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-access-control.md) Erfahren Sie mehr über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
- [Erste Schritte mit Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md#visualizations) Erfahren Sie mehr über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.

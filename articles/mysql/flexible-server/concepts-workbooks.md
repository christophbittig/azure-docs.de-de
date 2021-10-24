---
title: Überwachen flexibler Server von Azure Database for MySQL mit Azure Monitor-Arbeitsmappen
description: In diesem Artikel wird beschrieben, wie Sie flexible Server von Azure Database for MySQL mit Azure Monitor-Arbeitsmappen überwachen.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: aa26531dd9f644b4dc5b3343674abd7920fe57dd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064588"
---
# <a name="monitor-azure-database-for-mysql-flexible-server-by-using-azure-monitor-workbooks"></a>Überwachen flexibler Server von Azure Database for MySQL mit Azure Monitor-Arbeitsmappen

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Flexible Server von Azure Database for MySQL sind jetzt in Azure Monitor-Arbeitsmappen integriert. Mit Arbeitsmappen erhalten Sie einen flexiblen Canvas zum Analysieren von Daten und Erstellen umfangreicher visueller Berichte innerhalb des Azure-Portals. Mit Arbeitsmappen können Sie mehrere Datenquellen in Azure nutzen und sie zu vereinheitlichten interaktiven Benutzeroberflächen kombinieren. Arbeitsmappenvorlagen dienen als zusammengestellte Berichte, die von mehreren Benutzern und Teams flexibel wiederverwendet werden können. 

Wenn Sie eine Vorlage öffnen, erstellen Sie eine vorübergehende Arbeitsmappe, die mit dem Inhalt der Vorlage aufgefüllt wird. Mit dieser Integration verfügt der Server über Links zu Arbeitsmappen und einigen Beispielvorlagen, mit denen der Dienst im großen Stil überwacht werden kann. Sie können diese Vorlagen bearbeiten, an Ihre Anforderungen anpassen und an das Dashboard anheften, um eine fokussierte und organisierten Ansicht der Azure-Ressourcen zu erstellen.
 
In diesem Artikel erfahren Sie mehr über die verschiedenen Arbeitsmappenvorlagen, die für Ihren flexiblen Server verfügbar sind.

Flexible Server von Azure Database for MySQL bieten drei verfügbare Vorlagen:
 
- **Übersicht**: Zeigt eine Instanzzusammenfassung und Top-Level-Metriken an, um die Ressourcennutzung auf Ihrem Server zu visualisieren und zu verstehen. Diese Vorlage zeigt die folgenden Ansichten an:

    * Serverübersicht 
    * Datenbankzusammenfassung
    * Verbindungsmetriken 
    * Leistungsmetriken 
    * Speichermetriken 

* **Überwachung**: Zeigt eine Zusammenfassung und Details zu den für den Server erfassten Überwachungsereignissen an. Diese Vorlage zeigt die folgenden Ansichten an:

    * Administrative Aktionen für den Dienst
    * Zusammenfassung der Überwachung
    * Zusammenfassung der Überwachungsverbindungsereignisse
    * Überwachungsverbindungsereignisse
    * Zusammenfassung des Tabellenzugriffs
    * Identifizierte Fehler

* **Statistik zur Abfrageleistung**: Zeigt eine Zusammenfassung und Details zur Abfrageworkload für die Instanz, zu zeitintensiven Abfragen, zu langsamen Abfrageanalysen und zu Verbindungsmetriken an. Diese Vorlage zeigt die folgenden Ansichten an:

    * Abfragelast
    * Aktive Verbindungen gesamt
    * Trend langsamer Abfragen (Abfragezeit > 10 Sekunden)
    * Details zu langsamen Abfragen
    * 5 längste Abfragen auflisten
    * Zusammenfassen langsamer Abfragen nach minimaler, maximaler und durchschnittlicher Abfragezeit sowie der Abfragezeit mit Standardabweichung

Sie können diese Vorlagen auch entsprechend Ihren Anforderungen bearbeiten und anpassen. Weitere Informationen finden Sie unter [Azure Monitor-Arbeitsmappen: Übersicht](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).

 ## <a name="access-the-workbook-templates"></a>Zugreifen auf die Arbeitsmappenvorlagen

Zum Anzeigen der Vorlagen navigieren Sie im Azure-Portal zum Bereich **Überwachung** für flexible Server von Azure Database for MySQL, und wählen Sie dann **Arbeitsmappen** aus.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Screenshot der Vorlagen „Übersicht“, „Überwachung“ und „Statistik zur Abfrageleistung“ im Bereich „Arbeitsmappen“.":::

Sie können die Liste der Vorlagen auch anzeigen, indem Sie zum Bereich **Öffentliche Vorlagen** navigieren.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Abbildung, die die Vorlagen „Übersicht“, „Überwachung“ und „Statistik zur Abfrageleistung“ im Bereich „Öffentliche Vorlagen“ zeigt.":::


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über die [Zugriffssteuerung](../../azure-monitor/visualize/workbooks-access-control.md) in Azure Monitor-Arbeitsmappen.
- Erfahren Sie mehr über [Visualisierungsoptionen](../../azure-monitor/visualize/workbooks-overview.md#visualizations) in Azure Monitor Arbeitsmappen. 

---
title: Azure Advisor für MySQL
description: Hier lernen Sie Azure Advisor-Empfehlungen für MySQL kennen.
author: alau-ms
ms.author: alau
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 97f4e55b1d1888de00502d70c911f0bf24d31782
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639773"
---
# <a name="azure-advisor-for-mysql"></a>Azure Advisor für MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Hier erfahren Sie, wie Azure Advisor auf Azure Database for MySQL angewendet wird, und Sie erhalten Antworten auf häufig gestellte Fragen.
## <a name="what-is-azure-advisor-for-mysql"></a>Was ist Azure Advisor für MySQL?
Das Azure Advisor-System verwendet Telemetriedaten, um Empfehlungen zur Leistung und Zuverlässigkeit für Ihre MySQL-Datenbank auszugeben. Advisor-Empfehlungen sind auf unsere MySQL-Datenbankangebote aufgeteilt:
* Azure Database for MySQL Single Server
* Azure Database for MySQL: Flexibler Server

Einige Empfehlungen gelten für mehrere Produktangebote, während andere Empfehlungen auf produktspezifischen Optimierungen basieren.
## <a name="where-can-i-view-my-recommendations"></a>Wo kann ich die Empfehlungen anzeigen?
Empfehlungen finden Sie im Azure-Portal in der Navigationsleiste **Übersicht**. Eine Vorschau wird als Bannerbenachrichtigung eingeblendet, und Details können im Abschnitt **Benachrichtigungen** direkt unterhalb der Diagrammen zur Ressourcennutzung angezeigt werden.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Screenshot des Azure-Portals mit einer Azure Advisor-Empfehlung":::

## <a name="recommendation-types"></a>Empfehlungstypen
In Azure Database for MySQL werden die folgenden Arten von Empfehlungen priorisiert:
* **Leistung**: Hilft Ihnen, die Geschwindigkeit Ihres MySQL-Servers zu verbessern. Dazu gehören CPU-Auslastung, Arbeitsspeicherauslastung, Verbindungspooling, Datenträgerauslastung und produktspezifische Serverparameter. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zur Leistung](../advisor/advisor-performance-recommendations.md).
* **Zuverlässigkeit**: Hilft Ihnen, die ununterbrochene Verfügbarkeit Ihrer unternehmenskritischen Datenbanken zu gewährleisten und zu verbessern. Dies schließt Empfehlungen zu Speicher- und Verbindungslimits ein. Weitere Informationen finden Sie unter [Verbessern der Zuverlässigkeit Ihrer Anwendung mit dem Azure Advisor](../advisor/advisor-high-availability-recommendations.md).
* **Kosten**: Mit dem Ratgeber können Sie Ihre Gesamtausgaben für Azure senken und optimieren. Dies schließt Empfehlungen für die richtige Dimensionierung des Servers ein. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zu Kosten](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Grundlegendes zu Ihren Empfehlungen
* **Täglicher Zeitplan:** Bei Azure MySQL-Datenbanken überprüfen wir die Servertelemetriedaten und geben Empfehlungen nach einem täglichen Zeitplan aus. Wenn Sie an Ihrer Serverkonfiguration eine Änderung vornehmen, bleiben vorhandene Empfehlungen so lange sichtbar, bis wir die Telemetriedaten am nächsten Tag erneut untersuchen. 
* **Leistungsverlauf:** Einige unserer Empfehlungen basieren auf dem Leistungsverlauf. Diese Empfehlungen werden erst angezeigt, nachdem ein Server 7 Tage lang mit derselben Konfiguration ausgeführt wurde. Nur so lassen sich Muster mit hoher Auslastung (z. B. hohe CPU-Aktivität oder hohes Verbindungsvolumen) über einen längeren Zeitraum erkennen. Wenn Sie einen neuen Server bereitstellen oder zu einer neuen Konfiguration der virtuellen Kerne wechseln, werden diese Empfehlungen vorübergehend ausgesetzt. Dadurch wird verhindert, dass veraltete Telemetriedaten Empfehlungen für einen neu konfigurierten Server auslösen. Dies bedeutet aber auch, dass auf dem Leistungsverlauf basierte Empfehlungen möglicherweise nicht sofort angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Azure Advisor – Übersicht](../advisor/advisor-overview.md).

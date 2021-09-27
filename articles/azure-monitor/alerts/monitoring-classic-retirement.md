---
title: Update der klassischen Warnungen und Überwachung in Azure Monitor
description: Beschreibung der Einstellung der klassischen Überwachungsdienste und -funktionen, die bisher im Azure-Portal unter Benachrichtigungen (klassisch) angezeigt wurden.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: 64e9c454bfe8df9b65176fe344999e88204720d0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539117"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Einheitliche Benachrichtigung und Überwachung in Azure Monitor tritt an die Stelle von klassischer Benachrichtigung und Überwachung

Bei Azure Monitor handelt es sich um einen einheitlichen Überwachungsstapel, der eine Lösung für Metriken und eine Lösung für Warnungen für alle Azure-Ressourcen bietet. Weitere Informationen finden Sie in diesem [Blogbeitrag](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Die neuen Plattformen für Überwachung und Warnungen wurde so entwickelt, dass sie schneller und intelligenter sind und einfacher erweitert werden können. So können Sie dafür sorgen, dass Sie mit dem steigenden Anstieg des Cloud Computings Schritt halten und mit der Philosophie einer intelligenten Cloud von Microsoft konform bleiben.

Mit der neuen Azure-Plattform für Überwachung und Warnungen werden klassische Warnungen in Azure Monitor für Benutzer der öffentlichen Cloud eingestellt. Klassische Warnungen für die Azure Government-Cloud und Azure China 21Vianet werden am **29. Februar 2024** eingestellt.

 ![Klassische Warnung im Azure-Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Wir empfehlen Ihnen, sich mit der neuen Plattform vertraut zu machen und damit zu beginnen, Warnungen auf dieser neu zu erstellen.

> [!IMPORTANT]
> Im Aktivitätsprotokoll erstellte klassische Warnungsregeln werden nicht als veraltet eingestuft und nicht migriert. Alle im Aktivitätsprotokoll erstellten klassischen Warnungsregeln können nun über „Azure Monitor – Warnungen“ unverändert aufgerufen und verwendet werden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](./alerts-activity-log.md). Warnungen zur Dienstintegrität können im neuen Abschnitt „Dienstintegrität“ unverändert aufgerufen und verwendet werden. Weitere Informationen finden Sie unter [Warnungen zu Dienstintegritätsbenachrichtigungen](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Einheitliche Metriken und Warnungen für Azure-Ressourcen

Im März 2018 wurde eine neue Generation von Warnungen für Azure-Ressourcen angekündigt. Die neuere Plattform für Metriken und Warnungen ist schneller und bietet eine höhere Granularität mithilfe von Dimensionen. Dimensionen ermöglichen es Ihnen, zu segmentieren und entsprechend bestimmter Wertkombinationen, Bedingungen oder Vorgänge zu filtern. Für die neueren Metrikwarnungen werden Aktionsgruppen verwendet. Dies ermöglicht mehr Benachrichtigungen und die Automatisierung von Aktionen. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](./alerts-metric.md).

Die neueren Metriken für Azure-Ressourcen sind in diesen Formen verfügbar:

- **Azure Monitor-Standard-Plattformmetriken**: beliebte, vorkonfigurierte Metriken aus verschiedenen Azure-Diensten und Produkten. Weitere Informationen finden Sie in diesen Artikeln über [Unterstützte Metriken in Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) und [Unterstützte Metrikwarnungen in Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor benutzerdefinierte Metriken**: Metriken aus benutzerdefinierten Quellen, einschließlich des Azure-Diagnose-Agents. Weitere Informationen finden Sie in diesem Artikel über [Benutzerdefinierte Metriken in Azure Monitor](../essentials/metrics-custom-overview.md). Mithilfe benutzerdefinierter Metriken können Sie auch vom [Microsoft Azure-Diagnose-Agent](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) und vom [InfluxData Telegraf-Agent](../essentials/collect-custom-metrics-linux-telegraf.md) erfasste Metriken veröffentlichen.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Einstellung der klassischen Überwachungs- und Benachrichtigungsplattform

Wie bereits erwähnt werden die klassische Überwachung und klassische Warnungen für Benutzer der öffentlichen Cloud eingestellt. Dazu gehört die Einstellung der dazugehörigen APIs, der entsprechenden Benutzeroberfläche im Azure-Portal und der darin enthaltenen Dienste. Bis zum **31. Mai 2021** ist die Verwendung jedoch noch eingeschränkt möglich. Klassische Warnungen für die Azure Government-Cloud und Azure China 21Vianet werden am **29. Februar 2024** eingestellt.

Genau genommen sind von der Einstellung die klassischen Metriken betroffen, die aktuell im [Abschnitt „Warnungen (klassisch)“](./alerts-classic.overview.md) im Azure-Portal verfügbar sind und auf die als [microsoft.insights/alertrules](/rest/api/monitor/alertrules)-Ressourcen zugegriffen werden kann.

Das bedeutet:

- Klassische Überwachungs- und Warnungsdienste werden außer Betrieb genommen und sind für die Erstellung neuer Warnungsregeln nicht mehr verfügbar.
- Alle Warnungsregeln, die in „Warnungen (klassisch)“ noch vorhanden sind, werden auch weiterhin Benachrichtigungen ausführen und auslösen.
- Die meisten klassischen Warnungsregeln werden migriert. Dabei handelt es sich um einen nahtlosen Prozess ohne Ausfallzeiten, bei dem für Kunden keine Überwachungslücken auftreten.
- Ausgelöste Benachrichtigung enthalten eine neue Nutzlaststruktur. Das Ziel muss angepasst werden, damit es mit der neuen Struktur verwendet werden kann.
- Einige [klassische Warnungsregeln, die nicht automatisch migriert werden können](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) und manuelle Aktionen von Benutzern erfordern, werden weiterhin ausgeführt.

> [!IMPORTANT]
> Azure Monitor hat in Phasen ein [Tool zum freiwilligen Migrieren](alerts-using-migration-tool.md) veröffentlicht, mit dem Kunden ihre klassischen Warnungsregeln zur neuen Plattform migrieren können. Die verbleibenden Regeln werden automatisch migriert, sobald der Dienst eingestellt wurde. Kunden müssen sicherstellen, dass die Automatisierung der Nutzung der Nutzlast klassischer Warnungsregeln für die Verarbeitung der neuen Nutzlast unter [Einheitliche Metriken und Warnungen für Azure-Ressourcen](#unified-metrics-and-alerts-for-azure-resources) nach der Migration der klassischen Warnungsregeln angepasst wird. Weitere Informationen finden Sie unter [Vorbereiten Ihrer Logik-Apps und Runbooks für die Migration von klassischen Warnungsregeln](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Preise für migrierte Warnungsregeln

Wir führen ein Migrationstool ein, das Sie beim Migrieren der [klassischen Warnungen](./alerts-classic.overview.md) von Azure Monitor zur neuen Benutzeroberfläche für Warnungen unterstützt. Die migrierten Warnungsregeln und die zugehörigen migrierten Aktionsgruppen (E-Mail, Webhook oder LogicApp) bleiben gebührenfrei. Die Funktionen, die Sie bei klassischen Warnungen nutzen konnten – Bearbeiten von Schwellenwerten, Aggregationstyp und Detailgenauigkeit einer Aggregation – bleiben auch bei der migrierten Warnungsregel weiterhin kostenlos verfügbar. Wenn Sie jedoch eine migrierte Warnungsregel bearbeiten, sodass diese ein Feature der neuen Warnungsplattform, eine Benachrichtigung oder einen Aktionstyp verwendet, wird die entsprechende Gebühr berechnet. Weitere Informationen zu den Preisen für Warnungsregeln und Benachrichtigungen finden Sie unter [Preise für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Im Folgenden finden Sie einige Beispiele für Fälle, in denen für Ihre Warnungsregel eine Gebühr berechnet wird:

- Alle neuen (nicht migrierten) Warnungsregeln, die über die kostenlosen Einheiten auf der neuen Azure Monitor-Plattform erstellt werden
- Alle über die in Azure Monitor inbegriffenen kostenlosen Einheiten hinaus erfassten und aufbewahrten Daten
- Alle mehrfachen Web-Tests, die von Application Insights ausgeführt werden
- Alle benutzerdefinierten Metriken, die über die inbegriffenen kostenlosen Einheiten hinaus in Azure Monitor gespeichert werden
- Alle migrierten Warnungsregeln, die bearbeitet wurden und neuere Metrikwarnungsfeatures nutzen – z. B. Häufigkeit, mehrere Ressourcen/Dimensionen, [dynamischer Schwellwert](../alerts/alerts-dynamic-thresholds.md), Ändern von Ressourcen/Signalen usw.
- Alle migrierten Aktionsgruppen, die bearbeitet wurden und neuere Benachrichtigungen oder Aktionstypen wie SMS, Sprachanruf und/oder ITSM-Integration verwenden

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [neuen einheitlichen Azure Monitor](../overview.md).
* Weitere Informationen über die neuen [Azure-Warnungen](./alerts-overview.md).

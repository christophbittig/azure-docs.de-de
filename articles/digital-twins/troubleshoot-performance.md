---
title: 'Problembehandlung: Leistung'
titleSuffix: Azure Digital Twins
description: Tipps zur Behandlung von Leistungsproblemen einer Azure Digital Twins Instanz.
author: baanders
ms.author: baanders
ms.date: 10/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 67f182ffbaf439d2c6b592854156073d6104efa7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131427"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>Problembehandlung von Azure Digital Twins: Leistung

Wenn bei der Arbeit mit Azure Digital Twins Verzögerungen oder andere Leistungsprobleme auftreten, können Ihnen die Tipps in diesem Artikel bei der Problembehandlung weiterhelfen.

## <a name="isolate-the-source-of-the-delay"></a>Isolieren der Quelle der Verzögerung

Bestimmen Sie, ob die Verzögerung von Azure Digital Twins oder einem anderen Dienst in Ihrer Lösung stammt. Um diese Verzögerung zu untersuchen, können Sie die Metrik **API-Wartezeit** in [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md) über das Azure-Portal verwenden. Anweisungen zum Anzeigen von Azure Monitor-Metriken für eine Azure Digital Twins-Instanz finden Sie unter [Problembehandlung: Metriken](troubleshoot-metrics.md).

## <a name="check-regions"></a>Überprüfen von Regionen

Wenn Ihre Lösung Azure Digital Twins in Kombination mit anderen Azure-Diensten (wie Azure Functions) verwendet, überprüfen Sie die Region für die Bereitstellung der einzelnen Dienste. Dienste, die in verschiedenen Regionen bereitgestellt werden, können in Ihrer Lösung zu Verzögerungen führen. Sofern Sie nicht absichtlich eine verteilte Lösung erstellen, sollten Sie erwägen, alle Dienstinstanzen in derselben Region bereitzustellen, um die versehentliche Einführung von Verzögerungen zu vermeiden.

## <a name="check-logs"></a>Überprüfen der Protokolle

Azure Digital Twins kann Protokolle für Ihre Dienstinstanz zur Überwachung von Leistung, Zugriff und anderen Daten erfassen. Protokolle können an [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) oder Ihren benutzerdefinierten Speichermechanismus gesendet werden. Um die Protokollierung in Ihrer Instanz zu aktivieren, befolgen Sie die Anweisungen unter [Problembehandlung: Diagnoseprotokolle](troubleshoot-diagnostics.md). Anhand einer Analyse der Zeitstempel in den Protokollen können Sie Wartezeiten messen, bewerten, ob sie konsistent sind, und ihre Quelle ermitteln.

## <a name="check-api-frequency"></a>Überprüfen der API-Häufigkeit

Ein weiterer Faktor, der sich auf die Leistung auswirken kann, ist die Zeit, die zum erneuten Autorisieren von API-Aufrufen erforderlich ist. Berücksichtigen Sie die Häufigkeit Ihrer API-Aufrufe. Wenn zwischen den Aufrufen eine Lücke von mehr als 15 Minuten besteht, führt das System möglicherweise bei jedem Aufruf eine erneute Autorisierung durch, was zusätzliche Zeit in Anspruch nimmt. Sie können dieses Problem verhindern, indem Sie Ihrem Code einen Timer oder ähnliches hinzufügen, um sicherzustellen, dass Azure Digital Twins mindestens alle 15 Minuten einmal aufgerufen wird.

## <a name="contact-support"></a>Support kontaktieren

Wenn nach der Problembehandlung mit den oben genannten Schritten weiterhin Leistungsprobleme auftreten, können Sie über „Hilfe + Support“ für Azure eine Supportanfrage erstellen, um zusätzliche Unterstützung bei der Problembehandlung zu erhalten. 

Folgen Sie diesen Schritten:

1. Sammeln Sie [Metriken](troubleshoot-metrics.md) und [Protokolle](troubleshoot-diagnostics.md) für Ihre Instanz.
2. Navigieren Sie zu [Azure-Hilfe + Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) im Azure-Portal. Verwenden Sie die Eingabeaufforderungen, um Details zu Ihrem Problem anzugeben, empfohlene Lösungen anzuzeigen, Ihre Metriken/Protokolldateien zu teilen und alle anderen Informationen zu übermitteln, die das Supportteam verwenden kann, um Ihr Problem zu untersuchen. Weitere Informationen zum Erstellen von Supportanfragen finden Sie unter [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie in den folgenden Artikeln über andere Möglichkeiten zur Problembehandlung Ihrer Azure Digital Twins-Instanz:
* [Problembehandlung: Metriken](troubleshoot-metrics.md)
* [Problembehandlung: Diagnoseprotokolle](troubleshoot-diagnostics.md)
* [Problembehandlung: Warnungen](troubleshoot-alerts.md)
* [Problembehandlung: Ressourcenintegrität](troubleshoot-resource-health.md)

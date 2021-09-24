---
title: Grundlegendes zur Ressourcenintegrität
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Azure Resource Health verwenden können, um die Integrität Ihrer Azure Digital Twins-Instanz zu überprüfen.
author: baanders
ms.author: baanders
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 551193ebcddb023010f1cea1029571c99176afb9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113090"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Problembehandlung von Azure Digital Twins: Ressourcenintegrität

[Azure Service Health](../service-health/index.yml) umfasst eine Reihe von Erfahrungen, die Ihnen bei der Diagnose von Dienstproblemen, die sich auf Ihre Azure-Ressourcen auswirken, helfen und Ihnen Unterstützung bieten können. Es enthält Informationen zu **Ressourcenintegrität**, **Dienstintegrität** und **Status** sowie Berichte über aktuelle und frühere Integritätsinformationen.

## <a name="use-azure-resource-health"></a>Verwenden von Azure Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) kann Ihnen bei der Überwachung helfen, ob Ihre Azure Digital Twins-Instanz ordnungsgemäß ausgeführt wird. Sie können darüber auch erfahren, ob ein regionaler Ausfall Auswirkungen auf die Integrität Ihrer Instanz hat.

Um die Integrität Ihrer Instanz zu überprüfen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü Ihrer Instanz die Option **Ressourcenintegrität** unter „Support und Problembehandlung“ aus. Dadurch gelangen Sie auf die Seite zur Ansicht des Verlaufs der Ressourcenintegrität. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Screenshot, der die Seite „Ressourcenintegrität“ zeigt. Es gibt einen Abschnitt „Integritätsverlauf“ mit einem täglichen Bericht der letzten neun Tage.":::

In der obigen Abbildung ist dieser Fall als **Verfügbar** dargestellt, und zwar seit neun Tagen. Weitere Informationen über den Status „Verfügbar“ und die anderen Statustypen, die auftreten können, finden Sie unter [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).

Weitere Informationen zu den verschiedenen unterschiedlichen Azure-Ressourcentypen finden Sie unter [Ressourcentypen und Integritätsprüfungen in Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

## <a name="use-azure-service-health"></a>Verwenden von Azure Service Health

Mit [Azure Service Health](../service-health/service-health-overview.md) können Sie die Integrität des gesamten Azure Digital Twins-Diensts in einer bestimmten Region überprüfen und sich über Ereignisse wie laufende Dienstprobleme und anstehende geplante Wartungsarbeiten informieren.

Um den Dienststatus zu überprüfen, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zum **Service Health**-Dienst. Sie finden sie, indem Sie „Service Health“ auf der Suchleiste des Portals eingeben. 

Anschließend können Sie Dienstprobleme nach Abonnement, Region und Dienst filtern.

Weitere Informationen zur Verwendung von Azure Service Health finden Sie unter [Service Health-Übersicht](../service-health/service-health-overview.md).

## <a name="use-azure-status"></a>Verwenden von Azure-Status

Die Seite [Azure-Status](../service-health/azure-status-overview.md) bietet eine globale Ansicht der Integrität von Azure-Diensten und -Regionen. Sie ist nicht so personalisiert wie Azure Service Health oder Azure Resource Health und kann nützlich sein, um Vorfälle mit weitreichenden Auswirkungen zu verstehen.

Navigieren Sie zur Seite [Azure-Status](https://status.azure.com/status/), um den Azure-Status zu überprüfen. Auf der Seite wird eine Tabelle mit Azure-Diensten zusammen mit Integritätsindikatoren pro Region angezeigt. Sie können Azure Digital Twins anzeigen, indem Sie nach dem entsprechenden Tabelleneintrag auf der Seite suchen.

Weitere Informationen zur Verwendung der Azure-Statusseite finden Sie unter [Azure-Statusübersicht](../service-health/azure-status-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie in den folgenden Artikeln über andere Möglichkeiten, Ihre Azure Digital Twins-Instanz zu überwachen:
* [Problembehandlung: Anzeigen von Metriken mit Azure Monitor](troubleshoot-metrics.md)
* [Problembehandlung: Einrichten der Diagnose](troubleshoot-diagnostics.md).
* [Problembehandlung: Einrichten von Warnungen](troubleshoot-alerts.md).

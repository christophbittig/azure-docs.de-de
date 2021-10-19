---
title: Nutzen von Azure Advisor für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über Azure Advisor-Angebote für Azure Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 3c3b995cdf4b27056b0714ae6f9f8bc29fad6302
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713102"
---
# <a name="azure-advisor-for-azure-communication-services"></a>Azure Advisor für Azure Communication Services

Bei [Azure Advisor](../../advisor/advisor-overview.md) handelt es sich um einen personalisierten Cloudberater, der Sie mit bewährten Methoden zum Optimieren von Azure-Bereitstellungen unterstützt. Azure Communication Services ist in Azure Advisor integriert und gibt Empfehlungen für Möglichkeiten zum Optimieren Ihrer Kommunikationsressourcen. Sie können diese Empfehlungen im [Azure-Portal](https://portal.azure.com) auf dem [Advisor-Blatt](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) anzeigen. Empfehlungen werden in Ihrem [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/platform-logs-overview.md)gespeichert, und Sie können Warnungen für diese Empfehlungen über [ARM-Vorlagen](../../advisor/advisor-alerts-arm.md) oder das [Portal](../../advisor/advisor-alerts-portal.md) konfigurieren. 

## <a name="install-the-latest-sdks"></a>Installieren der neuesten SDKs

Um sicherzustellen, dass alle aktuellen Fixes und Updates vorgenommen werden, wird empfohlen, im Hinblick auf die verfügbaren SDKs immer auf dem neuesten Stand zu bleiben. Wenn eine neuere Version der verwendeten SDKs verfügbar ist, wird in der Kategorie **Leistung** die Empfehlung angezeigt, auf das neueste SDK zu aktualisieren.

![Azure Advisor-Beispiel mit der Empfehlung zum Aktualisieren des Chat-SDK](./media/advisor-chat-sdk-update-example.png)

Für dieses Feature werden die folgenden SDKs zusammen mit allen von ihnen unterstützten Sprachen unterstützt. Weitere Informationen zu den verfügbaren SDK-Optionen finden Sie [hier](./sdk-options.md).

* Anruf (Client)
* Chat
* sms
* Identität
* Telefonnummern
* Verwaltung
* Network Traversal
* Anrufautomatisierung

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Dokumente könnten Sie auch interessieren:

- [Protokollierung und Diagnose](./logging-and-diagnostics.md)
- [Metriken](./metrics.md)

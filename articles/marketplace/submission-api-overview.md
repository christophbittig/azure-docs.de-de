---
title: Überblick über die APIs für die kommerzielle Marketplace-Übermittlung im Partner Center
description: Verschaffen Sie sich einen Überblick über die APIs für die kommerzielle Marketplace-Übermittlung.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056640"
---
# <a name="commercial-marketplace-submission-api-overview"></a>Überblick über die API für die kommerzielle Marketplace-Übermittlung

Verwenden Sie die API, um programmatisch Übermittlungen für Angebote abzufragen, zu erstellen und zu veröffentlichen. Die API ist nützlich, wenn Ihr Konto viele Angebote verwaltet und Sie den Übermittlungsprozess für diese Angebote automatisieren und optimieren möchten.

## <a name="types-of-apis"></a>Arten von APIs

Es sind zwei Arten von Übermittlungs-APIs verfügbar:

- **Partner Center-Übermittlungs-API** – Der gewöhnliche Satz von APIs, die für Verbraucher- und kommerzielle Produkte zur Veröffentlichung über das Partner Center funktionieren. Dieser Satz von APIs wird kontinuierlich um neue Funktionen erweitert. Weitere Informationen über die Integration mit dieser API finden Sie unter [ Partner Center Übermittlungs-API Onboarding](submission-api-onboard.md). 
- **Legacy Cloud Partner Portal API** – Die APIs, die vom veralteten Cloud Partner Portal übernommen wurden; sie sind in das Partner Center integriert und funktionieren weiterhin. Dieser Satz von APIs befindet sich nur im Wartungsmodus; neue Funktionen, die im Partner Center eingeführt wurden, werden möglicherweise nicht unterstützt, und er sollte nur für bestehende Produkte verwendet werden, die bereits vor dem Übergang zum Partner Center integriert waren. Weitere Informationen zur weiteren Verwendung der Cloud Partner Portal-APIs finden Sie unter [Cloud Partner Portal API Verweis](cloud-partner-portal-api-overview.md).

In der folgenden Tabelle finden Sie die unterstützten Übermittlungs-APIs für jeden Angebotstyp.

| Angebotstyp | Unterstützung von Legacy Cloud Partner Portal-API | Unterstützung von Partner Center Übermittlungs-API |
| --- | :---: | :---: |
| Azure-Anwendung |  | &#x2714; |
| Azure-Container | &#x2714; |  |
| Virtueller Azure-Computer | &#x2714; |  |
| Beratungsdienst | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| IoT Edge-Modul | &#x2714; |  |
| Verwalteter Dienst | &#x2714; |  |
| Power BI-App | &#x2714; |  |
| Software-as-a-Service |  | &#x2714; |
|

Microsoft 365 Office-Add-ins: Microsoft 365 SharePoint-Lösungen, Microsoft 365 Teams-Anwendungen und Power BI visuelle Objekte haben keine Unterstützung für die Übermittlungs-API.

## <a name="next-steps"></a>Nächste Schritte

- Besuchen Sie bei Bedarf den API-Link, der für Ihren Angebotstyp geeignet ist

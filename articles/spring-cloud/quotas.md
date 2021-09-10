---
title: Servicepläne und Dienstkontingente für Azure Spring Cloud
description: Erfahren Sie mehr über Dienstkontingente und Servicepläne für Azure Spring Cloud.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 1bd1ed797ce737260289ab40ed6a16c4ce4581e7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356181"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kontingente und Servicepläne für Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Für die Ressourcen und Funktionen aller Azure-Dienste gelten festgelegte Standardlimits und -kontingente.   Azure Spring Cloud bietet zwei Tarife: Basic und Standard. In diesem Artikel werden die Grenzwerte für beide Tarife erläutert.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Dienstebenen und Grenzwerte für Azure Spring Cloud

| Resource | `Scope` | Basic | Standard
------- | ------- | -------
vCPU | pro App-Instanz | 1 | 4
Arbeitsspeicher | pro App-Instanz | 2 GB | 8 GB
Azure Spring Cloud-Dienstinstanzen | pro Region und Abonnement | 10 | 10
Gesamte App-Instanzen | pro Azure Spring Cloud-Dienstinstanz | 25 | 500
Benutzerdefinierte Domänen | pro Azure Spring Cloud-Dienstinstanz | 0 | 25
Persistente Volumes | pro Azure Spring Cloud-Dienstinstanz | 1 GB/App × 10 Apps | 50 GB/App × 10 Apps
Eingehende öffentliche Endpunkte | pro Azure Spring Cloud-Dienstinstanz| 10 <sup>1</sup> | 10 <sup>1</sup>
Öffentliche IP-Adressen für ausgehenden Datenverkehr | pro Azure Spring Cloud-Dienstinstanz| 1<sup>2</sup> | 2 <sup>2</sup> <br> 1 bei Verwendung von VNET<sup>2</sup>

<sup>1</sup> Sie können diesen Grenzwert über eine Supportanfrage auf maximal 1 pro App erhöhen.

<sup>2</sup> Sie können diesen Grenzwert über eine Supportanfrage auf maximal 10 erhöhen.

> [!TIP]
> Die für die Gesamtzahl der App-Instanzen pro Dienstinstanz aufgeführten Grenzwerte gelten für Apps und Bereitstellungen in beliebigem Zustand, einschließlich des beendeten Zustands. Löschen Sie Apps oder Bereitstellungen, die nicht verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Einige Standardgrenzwerte können erhöht werden. Sollte für Ihr Setup eine Erhöhung erforderlich sein, [erstellen Sie eine Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md).

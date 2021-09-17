---
title: 'SQL Managed Instance mit Azure Arc-Unterstützung: Dienstebenen'
description: Erläutert die für SQL Managed Instance-Bereitstellungen mit Azure Arc-Unterstützung verfügbaren Dienstebenen.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783999"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>SQL Managed Instance mit Azure Arc-Unterstützung: Dienstebenen

Als Teil der Familie der Azure SQL-Produkte ist SQL Managed Instance mit Azure Arc-Unterstützung in zwei Dienstebenen für [virtuelle Kerne](../../azure-sql/database/service-tiers-vcore.md) verfügbar.

- **Universell** ist eine budgetfreundliche Ebene, die für die meisten Workloads mit gängigen Leistungs- und Verfügbarkeitsanforderungen konzipiert ist.
- Die Dienstebene **Unternehmenskritisch** ist für leistungsabhängige Workloads mit höheren Verfügbarkeitsfeatures konzipiert.

Die Dienstebene Unternehmenskritisch befindet sich derzeit in der [öffentlichen Vorschauphase](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die Dienstebene „Universell“ ist allgemein verfügbar. 

In Azure werden Speicher und Computeressourcen von Microsoft mit garantierten Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) für Leistung, Durchsatz, Verfügbarkeit usw. für jede Dienstebene bereitgestellt. Bei Datendiensten mit Azure Arc-Unterstützung stellen Kunden den Speicher und die Computeressourcen zur Verfügung. Daher gibt es keine garantierten SLAs, die Kunden mit Datendiensten mit Azure Arc-Unterstützung zur Verfügung gestellt werden. Kunden wird jedoch die Flexibilität geboten, unabhängig von der Dienstebene ihre eigene leistungsfähige Hardware zu nutzen. 

## <a name="service-tier-comparison"></a>Vergleich der Dienstebenen

Im Folgenden finden Sie eine Beschreibung der verschiedenen Funktionen, die auf den beiden Dienstebenen von Datendiensten mit Azure Arc-Unterstützung zur Verfügung stehen:


Bereich | Unternehmenskritisch (Vorschau)* | Allgemeiner Zweck
----------|-----------------|------------------
Funktionsumfang | Identisch mit SQL Server Enterprise Edition | Identisch mit SQL Server Standard Edition
CPU-Grenzwert/-Instanz | Unbegrenzt  | 24 Kerne
Arbeitsspeicherlimit/-Instanz | Unbegrenzt | 128 GB
Hochverfügbarkeit | Verfügbarkeitsgruppe | Einzelne Instanz mit Kubernetes-Neubereitstellung und freigegebenem Speicher.
Horizontale Leseskalierung | Verfügbarkeitsgruppe | Keine
AHB-Austauschkurse für IP-Komponente des Preises | 1:1 Enterprise Edition <br> 4:1 Standard Edition | 1:4 Enterprise Edition <br> 1:1 Standard Edition 
Preise für Dev/Test | Kostenlos | Kostenlos

\* Derzeit befindet sich die Dienstebene „Unternehmenskritisch“ in der Vorschauphase und es entstehen keine Gebühren für die Nutzung während dieser Vorschauphase. Einige der Features können sich noch ändern, wenn die allgemeine Verfügbarkeit näherrückt.

## <a name="how-to-choose-between-the-service-tiers"></a>Auswählen zwischen den Dienstebenen

Da Kunden ihre eigene Hardware mit Leistungs- und Verfügbarkeitsanforderungen gemäß ihren Geschäftsanforderungen verwenden, sind die wichtigsten Unterschiede zwischen den Dienstebenen die Leistungen, die auf Softwareebene bereitgestellt werden. 

### <a name="choose-general-purpose-if"></a>Wählen Sie „Universell“:

- Wenn CPU-/Arbeitsspeicheranforderungen die Grenzwerte der Dienstebene „Universell“ erfüllen oder innerhalb dieser liegen.
- Wenn die von Kubernetes bereitgestellten Hochverfügbarkeitsoptionen, z. B. erneute Bereitstellung von Pods, für die Workload ausreichend sind.
- Wenn die Anwendung keine horizontale Leseskalierung benötigt.
- Wenn die Anwendung keins der Features der Dienstebene „Unternehmenskritisch“ erfordert (wie SQL Server Enterprise Edition).

### <a name="choose-business-critical-if"></a>Wählen Sie „Unternehmenskritisch“:

- Wenn CPU-/Arbeitsspeicheranforderungen die Grenzwerte der Dienstebene „Universell“ überschreiten.
- Wenn die Anwendung ein höheres Maß an Hochverfügbarkeit erfordert, z. B. integrierte Verfügbarkeitsgruppen, um Anwendungsfailover zu verarbeiten, als von Kubernetes geboten wird. 
- Wenn die Anwendung horizontale Leseskalierung nutzen kann, um Leseworkloads an die sekundären Replikate abzuladen.
- Wenn die Anwendung Features erfordert, die nur von der Dienstebene „Unternehmenskritisch“ geboten werden (wie SQL Server Enterprise Edition).

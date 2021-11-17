---
title: Erstellen von Azure Confidential-Lösungen
description: Erfahren Sie, wie Sie Lösungen für Azure Confidential Computing erstellen.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d29f214f4ee50f672b47fe0b346f13e8b8fd49c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290246"
---
# <a name="building-confidential-computing-solutions"></a>Erstellen von Confidential Computing-Lösungen

Azure Confidential Computing bietet verschiedene Optionen zum Erstellen vertraulicher Lösungen. Das Spektrum der Optionen reicht von der Aktivierung von „Lift and Shift“-Szenarien von vorhandenen Anwendungen bis hin zu einer vollständigen Kontrolle der verschiedenen Sicherheitsfeatures. Zu diesen Features gehört die Steuerung der Zugriffsebene. Sie legen die Zugriffsebenen des Hostanbieters oder Gastoperators auf Daten und Code fest. Sie können auch den Zugriff auf andere Rootkits oder Schadsoftware steuern, die die Integrität von Workloads gefährden könnten, die in der Cloud ausgeführt werden.

## <a name="solutions"></a>Lösungen

Technologien wie sichere Enklaven oder vertrauliche virtuelle Computer ermöglichen es Kunden, genau den Ansatz zu wählen, den sie beim Erstellen vertraulicher Lösungen verwenden möchten.

- Bestehende Anwendungen ohne Zugriff auf Quellcode können von vertraulichen VMs auf Basis der AMD SEV-SNP-Technologie profitieren, um das Onboarding auf die Azure Confidential Computing-Plattform problemlos zu bewerkstelligen.
- Komplexe Workloads, die proprietären Code zum Schutz vor vertraulichen Vektoren enthalten, können von sicheren Anwendungsenklaven profitieren. Azure bietet derzeit Anwendungsenklaven auf VMs, die auf Intel SGX basieren. Intel SGX bietet Schutz von Daten und Code, die in einem hardwareverschlüsselten Speicherplatz ausgeführt werden. Diese Anwendungen erfordern in der Regel die Kommunikation mit einer bestätigten sicheren Enklave, die mithilfe von Open-Source-Frameworks bezogen wird.
- Containerisierte Lösungen, die in [vertraulichen Containern](confidential-containers.md) ausgeführt werden, die in Azure Kubernetes Service aktiviert sind, sind möglicherweise für Kunden geeignet, die nach einem ausgewogenen Vertraulichkeitsansatz suchen. In diesen Szenarien können vorhandene Apps mit begrenzten Änderungen gepackt und in Containern bereitgestellt werden; allerdings bieten diese noch immer eine vollständige Sicherheitsisolation vom Clouddienstanbieter und den Administratoren.

![Screenshot: Confidential Computing-Spektrum mit einfacheren Optionen bis hin zu Optionen mit den meisten Sicherheitskontrollen.](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>Weitere Informationen

Um die Leistungsfähigkeit von Enklaven und isolierten Umgebungen ausschöpfen zu können, müssen Sie Tools verwenden, die Confidential Computing unterstützen. Es gibt unterschiedliche Tools, die die Entwicklung von Enklavenanwendungen unterstützen. Weitere Informationen finden Sie im Kapitel [Entwicklung von Enklavenanwendungen](application-development.md). 

Erfahren Sie mehr über [Open-Source-Tools](enclave-development-oss.md) zum Erstellen von Lösungen für Intel SGX-Enklaveanwendungen für virtuelle Computer.

Verwenden Sie [Partner und Open-Source-Tools für vertrauliche Container](confidential-containers.md). Einige dieser Tools können Sie auch für Azure Kubernetes-Workloads verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über die Entwicklung von Anwendungsenclaves](application-development.md).

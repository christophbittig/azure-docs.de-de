---
title: Von Microsoft Defender für Cloud unterstützte Plattformen und Features | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Liste der Plattformen, die von Microsoft Defender für Cloud unterstützt werden.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cf49562119503e7b0c54cb0691cbb520c6a0f9e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428347"
---
# <a name="supported-platforms"></a>Unterstützte Plattformen 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite finden Sie die Plattformen und Umgebungen, die von Microsoft Defender für Cloud unterstützt werden.

## <a name="combinations-of-environments"></a>Kombinationen von Umgebungen <a name="vm-server"></a>

Microsoft Defender für Cloud unterstützt virtuelle Computer und Server in unterschiedlichen Hybridumgebungen:

* Nur Azure
* Azure und lokal
* Azure und andere Clouds
* Azure, andere Clouds und lokal

Für eine unter einem Azure-Abonnement aktivierte Azure-Umgebung werden von Microsoft Defender für Cloud automatisch IaaS-Ressourcen ermittelt, die dem Abonnement bereitgestellt werden.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Defender für Cloud ist vom [Log Analytics-Agent](../azure-monitor/agents/agents-overview.md#log-analytics-agent) abhängig. Stellen Sie sicher, dass auf ihren Computern eines der unterstützten Betriebssysteme für diesen Agent ausgeführt wird, wie auf den folgenden Seiten beschrieben:

* [Log Analytics-Agent für von Windows unterstützte Betriebssysteme](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Log Analytics-Agent für von Linux unterstützte Betriebssysteme](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Stellen Sie außerdem sicher, dass [der Log Analytics-Agent ordnungsgemäß für das Senden von Daten an Defender für Cloud konfiguriert ist](enable-data-collection.md#manual-agent).

Weitere Informationen zu den spezifischen Defender für Cloud-Features, die unter Windows und Linux verfügbar sind, finden Sie unter [Featureabdeckung für Computer](supported-machines-endpoint-solutions-clouds.md).

> [!NOTE]
> Obwohl **Microsoft Defender für Server** zum Schutz von Servern konzipiert ist, werden die meisten seiner Features von Computern mit Windows 10 unterstützt. Ein Feature, das derzeit nicht unterstützt wird, ist [die integrierte EDR-Lösung von Defender für Cloud: Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md).

## <a name="managed-virtual-machine-services"></a>Verwaltete Dienste für virtuelle Computer <a name="virtual-machine"></a>

Unter einem Kundenabonnement werden virtuelle Computer auch im Rahmen einiger verwalteter Azure-Dienste erstellt, z. B. Azure Kubernetes (AKS), Azure Databricks usw. Defender für Cloud ermittelt auch diese virtuellen Computer, und der Log Analytics-Agent kann installiert und konfiguriert werden, wenn ein unterstütztes Betriebssystem verfügbar ist.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuelle Computer, die in einem Clouddienst ausgeführt werden, werden ebenfalls unterstützt. Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Übersicht zu Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Der Schutz für VMs, die sich in Azure Stack Hub befinden, wird ebenfalls unterstützt. Weitere Informationen zur Integration von Defender für Cloud in Azure Stack Hub finden Sie unter [Integrieren Ihrer Azure Stack Hub-VMs in Defender für Cloud](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie [Defender für Cloud mit dem Log Analytics-Agent Daten sammelt](enable-data-collection.md).
- Erfahren Sie, wie [Defender für Cloud Daten verwaltet und schützt](data-security.md).
- Erfahren Sie, wie Sie [die Entwurfsaspekte in Bezug auf die Einführung von Microsoft Defender für Cloud planen und verstehen](security-center-planning-and-operations-guide.md).
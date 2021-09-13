---
title: Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure Site Recovery.
description: In diesem Artikel werden die Unterstützung und die Anforderungen für das Bereitstellen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure mit Azure Site Recovery beschrieben.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: 71861ec8f3b1e10f0052fe46705b726f06e717dd
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444303"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure

>[!NOTE]
> Die Informationen in diesem Artikel gelten für die Releases von Azure Site Recovery (klassisch). In der Vorschau müssen Sie für die Replikation von VMs eine Azure Site Recovery-Replikationsappliance erstellen und verwenden. Ausführliche Informationen zu den Anforderungen der Replikationsappliance und zur Bereitstellung [finden Sie in diesem Artikel](deploy-vmware-azure-replication-appliance-preview.md).

Sie stellen einen lokalen Konfigurationsserver bereit, wenn Sie [Azure Site Recovery](site-recovery-overview.md) für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure verwenden.

- Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen VMware-Umgebung und Azure. Darüber hinaus verwaltet er die Datenreplikation.
- [Erfahren Sie mehr](vmware-azure-architecture.md) zu den Komponenten und Prozessen von Konfigurationsservern.

## <a name="configuration-server-deployment"></a>Bereitstellung des Konfigurationsservers

Für die Notfallwiederherstellung von VMware-VMs in Azure stellen Sie den Konfigurationsserver als VMware-VM bereit.

- Site Recovery stellt eine OVA-Vorlage bereit, die Sie aus dem Azure-Portal herunterladen und in vCenter Server importieren, um die Konfigurationsserver-VM einzurichten.
- Wenn Sie den Konfigurationsserver mit der OVA-Vorlage bereitstellen, erfüllt die VM automatisch die Anforderungen, die in diesem Artikel aufgeführt sind.
- Es wird dringend empfohlen, den Konfigurationsserver mit der OVA-Vorlage einzurichten. Aber wenn Sie die Notfallwiederherstellung für VMware-VMs einrichten und die OVA-Vorlage nicht verwenden können, können Sie den Konfigurationsserver anhand [dieser Anleitung](physical-azure-set-up-source.md) bereitstellen.
- Falls Sie den Konfigurationsserver für die Notfallwiederherstellung von lokalen physischen Computern in Azure bereitstellen, hilft Ihnen die Anleitung in [diesem Artikel](physical-azure-set-up-source.md) weiter.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Nächste Schritte
[Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)

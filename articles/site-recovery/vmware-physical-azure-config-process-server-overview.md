---
title: Informationen zu Azure Site Recovery-Konfiguration/Prozess/Masterzielservern
description: Dieser Artikel enthält eine Übersicht über die Konfiguration, Prozesse und Masterzielserver, die beim Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit Azure Site Recovery verwendet werden.
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ad8cdbdf9d051ac9b0e5a7fd47660811e4a285e3
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446426"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informationen zu Site Recovery-Komponenten (Konfiguration, Prozess und Masterziel)

Dieser Artikel beschreibt die Konfiguration, Prozesse und Masterzielserver, die vom [Site Recovery](site-recovery-overview.md)-Dienst zum Replizieren von virtuellen VMware-Computern und physischen Servern in Azure verwendet werden. Dieser Artikel gilt für klassische Releases.

In der Vorschau müssen Sie einen Azure Site Recovery-Replikationsserver erstellen und verwenden, um virtuelle Computer zu replizieren. Weitere Informationen zu Azure Site Recovery-Replikationsserver und seinen Komponenten finden Sie in [diesem Artikel](vmware-azure-architecture-preview.md).

## <a name="configuration-server"></a>Konfigurationsserver

Für die Notfallwiederherstellung von lokalen virtuellen VMware-Computern und physischen Servern stellen Sie einen lokalen Site Recovery-Konfigurationsserver bereit.

**Einstellung** | **Details** | **Links**
--- | --- | ---
**Komponenten**  | Auf dem Konfigurationsservercomputer werden alle lokalen Site Recovery-Komponenten ausgeführt, einschließlich Konfigurationsserver, Prozessserver und Masterzielserver.<br/><br/> Wenn Sie den Konfigurationsserver einrichten, werden alle Komponenten automatisch installiert. | [Häufig gestellte Fragen](vmware-azure-common-questions.md#configuration-server) zum Konfigurationsserver
**Rolle** | Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation. | Weitere Informationen zur Architektur für die Notfallwiederherstellung von [VMware](vmware-azure-architecture.md) und [physischen Servern](physical-azure-architecture.md) in Azure
**VMware-Anforderungen** | Für die Notfallwiederherstellung von lokalen virtuellen VMware-Computern müssen Sie den Konfigurationsserver als lokalen und hochverfügbaren virtuellen VMware-Computer installieren und ausführen. | [Weitere Informationen](vmware-azure-deploy-configuration-server.md#prerequisites) zu den Voraussetzungen
**VMware-Bereitstellung** | Es wird empfohlen, den Konfigurationsserver mit einer OVA-Vorlage bereitzustellen. Diese Methode bietet eine einfache Möglichkeit, den Konfigurationsserver so einzurichten, dass er alle Anforderungen und Voraussetzungen erfüllt.<br/><br/> Falls Sie aus irgendeinem Grund keinen virtuellen VMware-Computer mit einer OVA-Vorlage bereitstellen können, können Sie die Konfigurationsservercomputer manuell einrichten. Dies wird nachfolgend für die Notfallwiederherstellung von physischen Computern beschrieben. | [Bereitstellen](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) mit einer OVA-Vorlage
**Anforderungen physischer Server** | Für die Notfallwiederherstellung auf lokalen physischen Servern stellen Sie den Konfigurationsserver manuell bereit. | [Weitere Informationen](physical-azure-set-up-source.md#prerequisites) zu den Voraussetzungen
**Physische Serverbereitstellung** | Wenn das Installieren des Konfigurationsservers als virtueller VMware-Computer nicht möglich ist, können Sie ihn auf einem physischen Server installieren. | [Manuelles Bereitstellen](physical-azure-set-up-source.md#set-up-the-source-environment) des Konfigurationsservers

## <a name="process-server"></a>Prozessserver

Ein Prozessserver verarbeitet Replikationsdaten während des Failovers und Failbacks und installiert den Mobilitätsdienst für lokale virtuelle VMware-Computer und physische Server.

**Einstellung** | **Details** | **Links**
--- | --- | ---
**Bereitstellung**  | Der Prozessserver wird bei der Bereitstellung des Konfigurationsservers standardmäßig installiert. <br/><br/> Für die Notfallwiederherstellung und Replikation von lokalen virtuellen VMware-Computern und physischen Servern ist ein lokaler Prozessserver erforderlich. | [Weitere Informationen](vmware-azure-architecture.md#architectural-components)
**Rolle (lokal)** | Empfängt Replikationsdaten von für die Replikation aktivierten Computern. <br/><br/> Optimiert Replikationsdaten durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage. <br/><br/> Führt eine Pushinstallation des Site Recovery-Mobilitätsdiensts auf lokalen virtuellen VMware-Computern und physischen Servern aus. <br/><br/> Führt die automatische Ermittlung von lokalen Computern durch. | [Weitere Informationen](vmware-azure-enable-replication.md)
**Rolle (Failback von Azure)** | Nach dem Failover Ihrer lokalen Website richten Sie in Azure einen Prozessserver als virtuellen Azure-Computer ein, um ein Failback zu Ihrem lokalen Speicherort zu ermöglichen.<br/><br/> Der Prozessserver in Azure ist temporär. Der virtuelle Azure-Computer kann nach Abschluss des Failbacks gelöscht werden. | [Weitere Informationen](vmware-azure-set-up-process-server-azure.md)
**Skalieren** | Für größere Bereitstellungen können Sie lokal zusätzliche horizontal skalierbare Prozessserver einrichten. Zusätzliche Server skalieren die Kapazität auf, indem eine größere Anzahl von replizierenden Computern und größere Mengen von Replikationsdatenverkehr verarbeitet werden.<br/><br/> Sie können Computer zwischen zwei Prozessservern verschieben, um einen Lastenausgleich für Replikationsdatenverkehr vorzunehmen. | [Weitere Informationen](vmware-azure-set-up-process-server-scale.md)

## <a name="master-target-server"></a>Masterzielserver

Der Masterzielserver verarbeitet die Replikationsdaten während des Failbacks von Azure.

- Der Masterzielserver wird standardmäßig auf dem Konfigurationsserver installiert.
- Bei größeren Bereitstellungen können Sie einen zusätzlichen, separaten Masterzielserver für das Failback hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie die [Architektur](vmware-azure-architecture.md) für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern.
- Sehen Sie sich die [Anforderungen und Voraussetzungen](vmware-physical-azure-support-matrix.md) für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure an.

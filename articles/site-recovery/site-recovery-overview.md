---
title: Informationen zu Azure Site Recovery
description: Hier erhalten Sie eine Übersicht über den Azure Site Recovery-Dienst und eine Zusammenfassung der Bereitstellungsszenarien für Notfallwiederherstellung und Migration.
ms.topic: overview
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: d5558930c77c115ba25cb4b35e88d470afc38a23
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445689"
---
# <a name="about-site-recovery"></a>Informationen zu Azure Site Recovery

Willkommen beim Azure Site Recovery-Dienst! Dieser Artikel enthält eine kurze Dienstübersicht.

Organisationen benötigen eine BCDR-Strategie (Business Continuity and Disaster Recovery, Geschäftskontinuität und Notfallwiederherstellung), die den Schutz ihrer Daten sowie den Onlinezustand ihrer Apps und Workloads gewährleistet, wenn geplante oder ungeplante Ausfälle auftreten.

Azure Recovery Services unterstützt Sie bei Ihrer BCDR-Strategie:

- **Site Recovery-Dienst:** Site Recovery sorgt dafür, dass geschäftliche Apps und Workloads während Ausfällen weiter ausgeführt werden, und trägt so zur Aufrechterhaltung der Geschäftskontinuität bei. Site Recovery repliziert Workloads, die auf physischen und virtuellen Computern ausgeführt werden, von einem primären Standort an einen sekundären Standort. Im Falle eines Ausfalls am primären Standort können Sie ein Failover auf den sekundären Standort ausführen und von dort aus auf die Apps zugreifen. Wenn der primäre Standort dann wieder verfügbar ist, können Sie ein Failback ausführen.
- **Backup-Dienst:** Mit dem [Azure Backup](../backup/index.yml)-Dienst sind Ihre Daten geschützt und wiederherstellbar.

Site Recovery kann die Replikation für folgende Bereiche verwalten:

- Replikation von virtuellen Azure-Computern zwischen Azure-Regionen
- Lokale VMs, Azure Stack-VMs und physische Server.

## <a name="what-does-site-recovery-provide"></a>Was bietet Site Recovery?

**Feature** | **Details**
--- | ---
**Einfache BCDR-Lösung** | Mit Site Recovery können Sie Replikation, Failover und Failback von einem zentralen Ort im Azure-Portal aus einrichten und verwalten.
**Replikation virtueller Azure-Computer** | Sie können die Notfallwiederherstellung virtueller Azure-Computer aus einer primären Region in einer sekundären Region einrichten.
**Replikation virtueller VMware-Computer** | Sie können VMware-VMs in Azure replizieren, indem Sie die verbesserte Azure Site Recovery-Replikationsappliance verwenden, die höhere Sicherheit und Resilienz als der Konfigurationsserver bietet. Weitere Informationen finden Sie unter [Informationen zur Notfallwiederherstellung von virtuellen VMware-Computern in Azure](vmware-azure-about-disaster-recovery.md).
**Replikation lokaler virtueller Computer** | Sie können lokale virtuelle Computer und physische Server in Azure oder in einem sekundären Datencenter replizieren. Mit der Replikation in Azure entfallen die Kosten und die Komplexität, die mit der Verwaltung eines sekundären Datencenters verbunden sind.
**Workloadreplikation** | Sie können beliebige Workloads replizieren, die auf unterstützten virtuellen Azure-Computern, auf lokalen virtuellen Hyper-V- und VMware-Computern und auf physischen Windows-/Linux-Servern ausgeführt werden.
**Datenresilienz** | Site Recovery orchestriert die Replikation, ohne dass dies Auswirkungen auf die Anwendungsdaten hat. Wenn Sie Daten in Azure replizieren, werden diese in Azure Storage gespeichert, und Sie profitieren von der entsprechenden Resilienz. Bei einem Failover werden virtuelle Azure-Computer auf der Grundlage der replizierten Daten erstellt.
**RTO- und RPO-Ziele** | Halten Sie die Grenzwerte Ihrer Organisation für Recovery Time Objectives (RTO) und Recovery Point Objectives (RPO) ein. Site Recovery ermöglicht eine fortlaufende Replikation für virtuelle Azure-Computer und virtuelle VMware-Computer sowie eine Replikationsfrequenz von nur 30 Sekunden für Hyper-V. Mittels Integration in [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) können Sie RTO weiter reduzieren.
**Beibehalten der Konsistenz für Apps bei einem Failover** | Bei der Replizierung können Sie Wiederherstellungspunkte mit anwendungskonsistenten Momentaufnahmen verwenden. Diese Momentaufnahmen umfassen Datenträgerdaten, sämtliche Daten im Arbeitsspeicher sowie alle aktiven Transaktionen.
**Testen ohne Unterbrechungen** | Sie können komfortabel Übungen zur Notfallwiederherstellung durchführen, ohne die laufende Replikation zu beeinflussen.
**Flexible Failover** | Sie können geplante Failover ohne Datenverlust für erwartete Ausfälle ausführen. Oder Sie können ungeplante Failover mit minimalem Datenverlust (je nach Replikationshäufigkeit) für unerwartete Notfälle ausführen. Wenn der primäre Standort wieder verfügbar ist, kann problemlos ein Failback ausgeführt werden.
**Angepasste Wiederherstellungspläne** | Mit Wiederherstellungsplänen können Sie Failover und Wiederherstellungen von Anwendungen mit mehreren Ebenen anpassen und sequenzieren, die auf mehreren virtuellen Computern ausgeführt werden. In einem Wiederherstellungsplan können Sie Computer zusammenfassen und bei Bedarf Skripts und manuelle Aktionen hinzufügen. Wiederherstellungspläne können in Azure Automation-Runbooks integriert werden.
**BCDR-Integration** | Site Recovery ist in andere BCDR-Technologien integriert. Beispielsweise können Sie Site Recovery verwenden, um das SQL Server-Back-End von geschäftlichen Workloads zu schützen – mit nativer Unterstützung für SQL Server Always On zum Verwalten des Failovers von Verfügbarkeitsgruppen.
**Azure Automation-Integration** | Eine umfassende Azure Automation-Bibliothek bietet produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Site Recovery integriert werden können.
**Netzwerkintegration** | Site Recovery ist für die Anwendungsnetzwerkverwaltung in Azure integriert. Beispiele hierfür sind das Reservieren von IP-Adressen, Konfigurieren des Lastenausgleichs und Verwenden von Azure Traffic Manager für effiziente Netzwerkwechsel.

## <a name="what-can-i-replicate"></a>Was kann ich replizieren?

**Unterstützt** | **Details**
--- | ---
**Replikationsszenarien** | Replizieren Sie virtuelle Azure-Computer aus einer Azure-Region in eine andere.<br/><br/>  Replizieren Sie lokale virtuelle VMware-Computer, virtuelle Hyper-V-Computer, physische Server (Windows und Linux) und virtuelle Azure Stack-Computer in Azure.<br/><br/> Replizieren Sie AWS-Windows-Instanzen nach Azure.<br/><br/> Replizieren Sie lokale virtuelle VMware-Computer, von System Center VMM verwaltete virtuelle Hyper-V-Computer und physische Server an einem sekundären Standort.
**Regionen** | Informationen zu unterstützten Regionen für Site Recovery finden Sie [hier](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery). |
**Replizierte Computer** | Überprüfen Sie die Replikationsanforderungen für die Replikation von [Azure-VMs](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [lokale VMware-VMs und physische Server](vmware-physical-azure-support-matrix.md#replicated-machines) sowie [lokale Hyper-V-VMs](hyper-v-azure-support-matrix.md#replicated-vms).
**Workloads (Arbeitslasten)** | Sie können jede Workload replizieren, die auf einem für die Replikation geeigneten Computer ausgeführt wird. Darüber hinaus hat das Site Recovery-Team App-spezifische Tests für [einige Apps](site-recovery-workload.md#workload-summary) durchgeführt.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich ausführlicher über [Workloadunterstützung](site-recovery-workload.md).
- [Erste Schritte](azure-to-azure-quickstart.md) mit der Replikation virtueller Azure-Computer zwischen Regionen
- Erste Schritte mit der [Replikation virtueller VMware-Computer](vmware-azure-enable-replication.md)

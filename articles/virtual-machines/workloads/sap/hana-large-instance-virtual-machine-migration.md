---
title: Migrieren von SAP HANA in Azure (große Instanzen) zu virtuellen Azure-Computern | Microsoft-Dokumentation
description: Migrieren von SAP HANA in Azure (große Instanzen) zu virtuellen Azure-Computern
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2021
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e401ee0a8760033bfada5f054ad4c61904a62a6a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136847"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA in Azure (große Instanzen) – Migration zu Azure Virtual Machines
In diesem Artikel werden mögliche Szenarien für die Bereitstellung von Azure (große Instanzen) beschrieben, und es werden Planungs- und Migrationskonzepte mit minimierten Ausfallzeiten beim Übergang angeboten.

## <a name="overview"></a>Übersicht
Azure Large Instances für SAP HANA (HLI) wurde erstmals im September 2016 angekündigt. Seitdem haben viele diese Hardware als Dienst für ihre In-Memory-Computeplattform übernommen. Gerade in den letzten Jahren haben die Azure-VM-Größenerweiterung und die Unterstützung der HANA-Bereitstellung mit horizontaler Skalierung die Kapazitätsanforderungen der ERP-Datenbanken der meisten Unternehmenskunden übertroffen. Viele zeigen ihr Interesse an der Migration ihrer SAP HANA-Workload von physischen Servern zu Azure-VMs.

Dieser Leitfaden ist keine Schritt-für-Schritt-Anleitung zur Konfiguration. Er beschreibt die allgemeinen Bereitstellungsmodelle und gibt Ratschläge zu Planung und Migration. Dies geschieht in der Absicht, die erforderlichen vorbereitenden Überlegungen bewusst zu machen, um die Ausfallzeit bei der Umstellung zu minimieren.

## <a name="assumptions"></a>Annahmen
Dieser Artikel geht von den folgenden Annahmen aus:
- Es wird ausschließlich eine homogene Migration von HANA-Datenbank-Computediensten von HANA (große Instanzen; HANA Large Instance, HLI) zu einem virtuellen Azure-Computer ohne größere Softwareupgrades oder -patches berücksichtigt. Geringfügige Updates schließen die Verwendung einer neueren Betriebssystemversion (Operating system, OS) oder HANA-Version ein, wenn diese ausdrücklich in den relevanten SAP-Hinweisen als unterstützt angegeben ist. 
- Alle Update-/Upgradeaktivitäten müssen vor oder nach der Migration erfolgen.  Beispiel: Umwandlung von SAP HANA MCOS- in MDC-Bereitstellung. 
- Der Migrationsansatz mit den geringsten Ausfallzeiten ist die SAP HANA-Systemreplikation. Andere Migrationsmethoden werden in diesem Dokument nicht behandelt.
- Dieser Leitfaden bezieht sich auf die SKUs Rev3 und Rev4 von HLI.
- Die HANA-Bereitstellungsarchitektur bleibt während der Migration größtenteils unverändert.  Das heißt, ein System mit Notfallwiederherstellung (Disaster Recovery, DR) auf einer einzelnen Instanz bleibt auf dem Ziel auf gleiche Weise erhalten.
- Sie haben die Vereinbarung zum Servicelevel (SLA) der Zielarchitektur gelesen und verstanden. 
- Die gewerblichen Nutzungsbedingungen von HLIs und VMs unterscheiden sich. Überwachen Sie die Nutzung Ihrer VMs für die Kostenverwaltung.
- Ihnen sollte bewusst sein, dass HLI eine dedizierte Computeplattform ist, während VMs von einander isoliert auf einer gemeinsamen Infrastruktur ausgeführt werden.
- Sie haben bestätigt, dass die Ziel-VMs Ihre beabsichtigte Architektur unterstützen. Eine Liste aller unterstützten VM-SKUs, die für SAP HANA-Bereitstellungen zertifiziert sind, finden Sie im [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Sie haben den Entwurf und den Migrationsplan überprüft.
- Planen Sie VMs für die Notfallwiederherstellung zusammen mit dem primären Standort.  Sie können den HLI-Knoten nicht für die Notfallwiederherstellung am primären Standort verwenden, der nach der Migration auf VMs ausgeführt wird.
- Sie haben die erforderlichen Sicherungsdateien entsprechend den Anforderungen für Geschäftswiederherstellung und Compliance auf die Ziel-VMs kopiert. Dies ermöglicht bei von den virtuellen Computern aus zugänglichen Sicherungen die Zeitpunktwiederherstellung während des Übergangszeitraums.
- Für die SAP HANA-Systemreplikation (HSR) mit Hochverfügbarkeit (High Availability, HA) müssen Sie das STONITH-Gerät gemäß den SAP HANA-Anweisungen zur Hochverfügbarkeit für [SLES](./high-availability-guide-suse-pacemaker.md) und [RHEL](./high-availability-guide-rhel-pacemaker.md) einrichten und konfigurieren.  Dies ist nicht wie HLI vorkonfiguriert.
- Dieser Migrationsansatz ist nicht für HLI-SKUs mit Optane-Konfiguration ausgelegt.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien
Sie können für alle HLI-Szenarien zu Azure-VMs migrieren. In der folgenden Tabelle sind allgemeine Bereitstellungsmodelle für HLI zusammengefasst. Um die verfügbaren ergänzenden Azure-Dienste nutzen zu können, müssen Sie möglicherweise kleinere Änderungen an der Architektur vornehmen.

| Szenario-ID | HLI-Szenario | Unverändert zu VM migrieren? | Anmerkung |
| --- | --- | --- | --- |
| 1 | [Einzelner Knoten mit einer SID](./hana-supported-scenario.md#single-node-with-one-sid) | Ja | - |
| 2 | [Einzelner Knoten mit mehreren Komponenten in einem System (MCOS)](./hana-supported-scenario.md#single-node-mcos) | Ja | - |
| 3 | [Einzelner Knoten mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Nein | Die Speicherreplikation ist für die virtuelle Azure-Plattform nicht verfügbar. Ändern Sie die aktuelle Notfallwiederherstellungslösung in HSR oder Sicherung/Wiederherstellung. |
| 4 | [Einzelner Knoten mit DR (Mehrzweck) mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Nein | Die Speicherreplikation ist für die virtuelle Azure-Plattform nicht verfügbar. Ändern Sie die aktuelle Notfallwiederherstellungslösung in HSR oder Sicherung/Wiederherstellung. |
| 5 | [HSR mit STONITH für Hochverfügbarkeit](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Ja | Keine vorkonfigurierte SBD für Ziel-VMs.  Auswählen und Bereitstellen einer STONITH-Lösung.  Mögliche Optionen: Azure Fencing-Agent (unterstützt sowohl für [RHEL](./high-availability-guide-rhel-pacemaker.md) als auch für [SLES](./high-availability-guide-suse-pacemaker.md)) und STONITH-Blockgerät (SBD). |
| 6 | [Hochverfügbarkeit mit HSR, Notfallwiederherstellung mit Speicherreplikation](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Nein | Ersetzen der Speicherreplikation für Notfallwiederherstellungs-Anforderungen durch HSR oder Sicherung/Wiederherstellung. |
| 7 | [Automatisches Hostfailover (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | Ja | Verwenden von Azure NetApp Files (ANF) für freigegebenen Speicher mit Azure-VMs. |
| 8 | [Horizontales Hochskalieren mit Standby](./hana-supported-scenario.md#scale-out-with-standby) | Ja | BW/4HANA mit VMs der Typen M128s, M416s, M416ms nur mit ANF für Speicherung. |
| 9 | [Horizontales Hochskalieren ohne Standby](./hana-supported-scenario.md#scale-out-without-standby) | Ja | BW/4HANA mit VMs der Typen M128s, M416s, M416ms (mit oder ohne ANF für Speicherung). |
| 10 | [Horizontale Skalierung mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Nein | Ersetzen der Speicherreplikation für Notfallwiederherstellungs-Anforderungen durch HSR oder Sicherung/Wiederherstellung. |
| 11 | [Einzelner Knoten mit DR über HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [HSR in einem einzelnen Knoten für Notfallwiederherstellung (kostenoptimiert)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [Hochverfügbarkeit und Notfallwiederherstellung mit HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [Hochverfügbarkeit und Notfallwiederherstellung mit HSR (kostenoptimiert)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Horizontale Skalierung mit DR über HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Ja | BW/4HANA mit VMs der Typen M128s. M416s, M416ms (mit oder ohne ANF für Speicherung). |


## <a name="source-hli-planning"></a>Quellplanung (HLI)
Beim Onboarding Ihres HLI-Servers mussten Sie und das Microsoft Service Management Compute, Netzwerk, Speicher und betriebssystemspezifische Einstellungen planen, um die SAP HANA-Datenbank auszuführen. Eine ähnliche Planung muss für die Migration zur Azure-VM durchgeführt werden.

### <a name="sap-hana-housekeeping"></a>SAP HANA-Housekeeping 
Es ist eine bewährte Betriebspraxis, den Datenbankinhalt zu bereinigen, sodass unerwünschte oder veraltete Daten sowie veraltete Protokolle nicht zur neuen Datenbank migriert werden.  Das Housekeeping umfasst im Allgemeinen das Löschen oder Archivieren alter, abgelaufener oder inaktiver Daten.  Diese „Datenhygiene“ sollte in Nicht-Produktionssystemen getestet werden, um die Gültigkeit der Datenbereinigung vor dem Einsatz in einer Produktionsumgebung zu überprüfen.

### <a name="allow-network-connectivity-for-new-vms-and-virtual-network"></a>Zulassen der Netzwerkkonnektivität für neue VMs und virtuelle Netzwerke 
In Ihrer HLI-Bereitstellung wurde das Netzwerk entsprechend den Informationen im Artikel [SAP HANA-Netzwerkarchitektur (große Instanzen)](./hana-network-architecture.md) eingerichtet. Das Routing von Netzwerkdatenverkehr erfolgt außerdem wie im Abschnitt [Routing in Azure](./hana-network-architecture.md#routing-in-azure) beschrieben.
- Befindet sich das neue VM-Migrationsziel in dem bestehenden virtuellen Netzwerk mit IP-Adressbereichen, die bereits für die Verbindung mit HLI zugelassen sind? Dann ist kein weiteres Konnektivitätsupdate erforderlich.
- Wird der neue virtuelle Azure-Computer in einem neuen Microsoft Azure Virtual Network untergebracht, möglicherweise in einer anderen Region, und wird ein Peering mit dem vorhandenen virtuellen Netzwerk hergestellt? Dann können Sie den ExpressRoute-Dienstschlüssel und die Ressourcen-ID aus der ursprünglichen HLI-Bereitstellung verwenden, um den Zugriff auf diesen neuen IP-Adressbereich des virtuellen Netzwerks zuzulassen. Sprechen Sie sich mit Microsoft Service Management ab, um die Verbindung des virtuellen Netzwerks mit HLI zuzulassen.  
    > [!NOTE]
    > Um die Netzwerklatenz zwischen der Anwendungs- und der Datenbankschicht zu minimieren, müssen sich die Anwendungs- und die Datenbankschichten im selben virtuellen Netzwerk befinden.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Vorhandene Verfügbarkeitsgruppe, Verfügbarkeitszonen und Näherungsplatzierungsgruppe (Proximity Placement Group, PPG) der Anwendungsschicht
Das aktuelle Bereitstellungsmodell soll bestimmte Servicelevelziele erfüllen. Stellen Sie bei dieser Verschiebung sicher, dass die Zielinfrastruktur Ihre festgelegten Ziele erfüllt oder überschreitet.  
Höchstwahrscheinlich werden Ihre SAP-Anwendungsserver in einer Verfügbarkeitsgruppe platziert. Wenn der Servicelevel der aktuellen Bereitstellung zufriedenstellend ist und die Ziel-VM den logischen HLI-Namen als Hostnamen übernimmt, würde die Aktualisierung der DNS-Adressauflösung (Domain Name Service), die auf die IP der VM verweist, ohne Aktualisierung der SAP-Profile funktionieren.
- Wenn Sie keine PPG verwenden, platzieren Sie alle Anwendungs- und DB-Server in derselben Zone, um die Netzwerklatenz zu minimieren.
- Wenn Sie PPG verwenden, lesen Sie einen späteren Abschnitt dieses Artikels, [Verfügbarkeitsgruppen, Verfügbarkeitszonen und Näherungsplatzierungsgruppen](#availability-sets-availability-zones-and-proximity-placement-groups).

### <a name="storage-replication-discontinuance-process-if-used"></a>Verfahren zur Beendigung der Speicherreplikation (sofern verwendet)
Wenn Sie die Speicherreplikation als DR-Lösung verwendet haben, beenden Sie sie, nachdem die SAP-Anwendung heruntergefahren wurde. Vergewissern Sie sich zuvor, dass der letzte SAP HANA-Katalog, die Protokolldatei und Datensicherungen auf die DR HLI-Remotespeichervolumes repliziert werden. Diese Replikation ist wichtig für den Fall, dass während des Übergangs vom physischen Server zur Azure-VM ein Notfall auftritt.

### <a name="data-backups-preservation-consideration"></a>Überlegungen zur Beibehaltung von Datensicherungen
Nach dem Übergang zu SAP HANA auf Ihrer Azure-VM sind die Momentaufnahmedaten und Protokollsicherungen unter HLI nicht mehr einfach zugänglich oder auf einer VM wiederherstellbar. Es wird empfohlen, Sicherungen auf Dateiebene und Momentaufnahmen noch Wochen vor der Übernahme auf der HLI zu erstellen. Kopieren Sie diese Sicherungen in ein Azure Storage-Konto, auf das die neue SAP HANA-VM zugreifen kann. Nehmen Sie ebenfalls im frühen Übergangszeitraum, bevor die Azure-basierte Sicherung einen genügenden Verlauf erstellt, um die Anforderungen an die Point-in-Time-Wiederherstellung zu erfüllen, Sicherungen auf Dateiebene vor. 

Das Sichern des HLI-Inhalts ist von entscheidender Bedeutung. Außerdem ist es ratsam, die vollständigen Sicherungen der SAP-Landschaft zugänglich zu halten, falls ein Rollback erforderlich ist.

### <a name="adjusting-system-monitoring"></a>Anpassen der Systemüberwachung 
Sie können viele verschiedene Tools verwenden, um Warnbenachrichtigungen für Systeme in ihrer SAP-Landschaft zu überwachen und zu senden. Führen Sie eine geeignete Aktion durch, um Änderungen in die Überwachung einzubeziehen und ggf. die Empfänger von Warnbenachrichtigungen zu aktualisieren.

### <a name="microsoft-operations-team-involvement"></a>Beteiligung des Microsoft Operations-Teams 
Öffnen Sie über das Azure-Portal ein Ticket für die vorhandene HLI-Instanz.  Nach dem Erstellen des Supporttickets werden Sie per E-Mail von einem Supporttechniker kontaktiert.  

### <a name="engage-microsoft-account-team"></a>Einbeziehung des Microsoft-Kontoteams
Planen Sie die Migration in der Nähe des Verlängerungszeitpunkts für den HLI-Vertrag am Jahrestag, um unnötige Zusatzkosten für Computeressourcen zu vermeiden. Um die HLI-Instanz außer Betrieb zu nehmen, koordinieren Sie die Vertragsbeendigung und das eigentliche Herunterfahren der Einheit.

## <a name="destination-planning"></a>Zielplanung
Eine sorgfältige Planung ist bei der Bereitstellung einer neuen Infrastruktur, die eine vorhandene ersetzen soll, von entscheidender Bedeutung. Stellen Sie sicher, dass die neue Ergänzung Ihre Anforderungen im größeren Stil erfüllt. Beachten Sie vor allem die folgenden Punkte.

### <a name="resource-availability-in-the-target-region"></a>Verfügbarkeit von Ressourcen in der Zielregion 
Die Bereitstellungsregion des aktuellen SAP-Anwendungsservers befindet sich normalerweise nahe bei den zugeordneten HLIs. HLIs werden jedoch an weniger Standorten angeboten, als Azure-Regionen verfügbar sind. Die Migration der physischen HLI zu einer Azure-VM ist außerdem ein guter Anlass, die Nähe aller beteiligten Dienste zwecks Leistungsoptimierung anzupassen.  Dabei sollte sichergestellt werden, dass in der ausgewählten Region alle erforderlichen Ressourcen vorhanden sind. Beispielsweise können Sie die Verfügbarkeit einer bestimmten VM-Familie oder der Azure-Zonen überprüfen, die eine Hochverfügbarkeitseinrichtung bieten.

### <a name="virtual-network"></a>Virtuelles Netzwerk 
Möchten Sie die neue HANA-Datenbank in einem vorhandenen virtuellen Netzwerk betreiben oder ein neues erstellen? Der wichtigste Faktor für diese Entscheidung ist das aktuelle Netzwerklayout für die SAP-Landschaft. Außerdem bringt es Änderungen der Architektur mit sich, wenn die Infrastruktur von einer Bereitstellung in einer Zone zu einer in zwei Zonen mit PPG wechselt. Weitere Informationen finden Sie im Artikel [Azure-PPG für optimale Netzwerklatenz bei SAP-Anwendungen](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Sicherheit
Unabhängig davon, ob die neue SAP HANA-VM in einem neuen oder vorhandenen VNET/Subnetz ausgeführt wird, ist dies ein neuer Dienst, der für Ihr Unternehmen von entscheidender Bedeutung ist. Dies erfordert Schutzmaßnahmen. Stellen Sie sicher, dass die Zugriffssteuerung mit der Sicherheitsrichtlinie Ihres Unternehmens konform ist.

### <a name="vm-sizing-recommendation"></a>Empfehlungen für die VM-Größe
Die Migration stellt außerdem eine gute Gelegenheit dar, die Größe Ihrer HANA-Compute-Engine zu optimieren. Sie können HANA-[Systemansichten](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) mit HANA Studio verwenden, um den Verbrauch von Systemressourcen zu verstehen und damit die richtige Größe für eine höhere Ausgabeneffizienz zu ermitteln.

### <a name="storage"></a>Speicher 
Die Speicherleistung ist einer der Faktoren, die sich auf Ihre Benutzererfahrung mit SAP-Anwendungen auswirken. Für bestimmte VM-SKUs werden Mindestspeicherlayouts veröffentlicht. Weitere Informationen finden Sie unter [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md). Sie sollten diese Spezifikationen überprüfen und mit Ihren vorhandenen HLI-Systemstatistiken vergleichen, um eine angemessene E/A-Kapazität und Leistung für Ihre neue HANA-VM sicherzustellen.

Konfigurieren Sie PPG für den neuen virtuellen HANA-Computer und die zugehörigen Server? Reichen Sie dann ein Supportticket ein, um die gemeinsame Unterbringung des Speichers und der VM zu überprüfen und sicherzustellen. Da Ihre Sicherungslösung möglicherweise geändert werden muss, untersuchen ebenfalls die Speicherkosten erneut, um Überraschungen bei den Betriebsausgaben zu vermeiden.

### <a name="storage-replication-for-disaster-recovery"></a>Speicherreplikation für die Notfallwiederherstellung
Mit der Einführung von HLI wurde Speicherreplikation zur Standardoption für die Notfallwiederherstellung. Dieses Feature ist nicht die Standardoption für SAP HANA auf Azure-VMs. Ziehen Sie HSR, Sicherung/Wiederherstellung oder andere unterstützte Lösungen gemäß Ihren geschäftlichen Anforderungen in Betracht.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Verfügbarkeitsgruppen, Verfügbarkeitszonen und Näherungsplatzierungsgruppen 
Sie können den Abstand zwischen der Anwendungsschicht und SAP HANA verkürzen, um die Netzwerklatenz möglichst gering zu halten. Platzieren Sie die neue Datenbank-VM und die aktuellen SAP-Anwendungsserver in einer PPG. Informationen zur Zusammenarbeit von Verfügbarkeitsgruppen und Verfügbarkeitszonen in Azure mit PPG für SAP-Bereitstellungen finden Sie unter [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](./sap-proximity-placement-scenarios.md).

Wenn Mitglieder Ihres HANA-Systems in mehreren Azure-Zonen bereitgestellt werden, sollten Sie das Latenzprofil der ausgewählten Zonen kennen. Platzieren Sie SAP-Systemkomponenten, um den Abstand zwischen der SAP-Anwendung und der Datenbank zu verkürzen. Mit dem [Testtool für die Verfügbarkeitszonenlatenz](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) (Public Domain) können Sie die Messung vereinfachen.  


### <a name="backup-strategy"></a>Sicherungsstrategie
Viele unserer Kunden verwenden bereits Sicherungslösungen von Drittanbietern für SAP HANA in HLI.  Wenn das auch auf Sie zutrifft, müssen nur die hinzugefügten geschützten VMs und HANA-Datenbanken konfiguriert werden. Laufende HLI-Sicherungsaufträge können aus der Planung entfernt werden, wenn der Computer nach der Migration außer Betrieb gesetzt wird.

Azure Backup für SAP HANA auf VMs ist jetzt allgemein verfügbar.  Weitere Informationen zur SAP HANA-Sicherung auf Azure-VMs finden Sie unter [Sichern](../../../backup/backup-azure-sap-hana-database.md), [Wiederherstellen](../../../backup/sap-hana-db-restore.md) und [Verwalten](../../../backup/sap-hana-db-manage.md).

### <a name="dr-strategy"></a>Strategie zur Notfallwiederherstellung
Wenn Ihre Servicelevelziele eine längere Wiederherstellungszeit berücksichtigen, kann die Sicherung einfach sein. Eine Sicherung auf Blobspeicher und eine Wiederherstellung an Ort und Stelle oder eine Wiederherstellung auf einer neuen VM ist die einfachste und kostengünstigste DR-Strategie.
 
Auf der Plattform für große Instanzen erfolgt die HANA-DR in der Regel mit HSR. Auf einer Azure-VM ist HSR auch die natürlichste und nativste SAP HANA-DR-Lösung. Unabhängig davon, ob Ihre Quellbereitstellung aus einer Einzelinstanz oder einem Cluster besteht, ist ein Replikat der Quellinfrastruktur in der Region für die Notfallwiederherstellung erforderlich.
Dieses Replikat zur Notfallwiederherstellung wird nach Abschluss der Migration der primären HLI-Umgebung zur VM konfiguriert.  Die HANA-Datenbankinstanz zur Notfallwiederherstellung wird bei der primären SAP HANA-Instanz auf der VM als sekundärer Replikationsstandort registriert.  

### <a name="sap-application-server-connectivity-destination-change"></a>Änderung der Verbindungsziele für SAP-Anwendungsserver
Die HSR-Migration führt zu einem neuen HANA-Datenbank-Host und auch zu einem neuen Datenbankhostnamen für die Anwendungsschicht. Ändern Sie SAP-Profile, um den neuen Hostnamen widerzuspiegeln. Wenn der Wechsel durch Namensauflösung unter Beibehaltung des Hostnamens erfolgt, ist keine Profiländerung erforderlich.

### <a name="operating-system-os"></a>Betriebssystem
Die OS-Images für HLI und VM sind nicht identisch, obwohl sie dieselbe Releaseversion aufweisen, z. B. SLES 12 SP4. Validieren Sie die erforderlichen Pakete, Hotfixes, Patches, Kernel und Sicherheitsfixes auf der HLI. Installieren Sie dann die gleichen Pakete auf dem Ziel.  Sie können HSR zum Replizieren von einem älteren Betriebssystem auf eine VM mit einer neueren Betriebssystemversion verwenden.  Die unterstützten Versionen können Sie im [SAP-Hinweis 2763388](https://launchpad.support.sap.com/#/notes/2763388) überprüfen.

### <a name="new-sap-license-request"></a>Neue SAP-Lizenzanforderung
Eine einfache Anforderung einer neuen SAP-Lizenz für das neue HANA-System nach der Migration zu VMs.

### <a name="service-level-agreement-sla-differences"></a>Unterschiede in den Vereinbarungen zum Servicelevel (SLA)
Die Autoren möchten auf den Unterschied der Verfügbarkeits-SLA zwischen HLI und Azure-VM hinweisen.  Beispielsweise bieten Hochverfügbarkeitspaare geclusterter HLIs eine Verfügbarkeit von 99,99 %. Um die gleiche SLA zu erreichen, müssen Sie VMs in Verfügbarkeitszonen bereitstellen. [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) beschreibt die Verfügbarkeit für verschiedene VM-Konfigurationen, damit Kunden ihre Zielinfrastruktur planen können.


## <a name="migration-strategy"></a>Migrationsstrategie
In diesem Dokument wird nur der HANA-Systemreplikationsansatz für die Migration von HLI zu Azure-VMs behandelt.  Abhängig von der bereitgestellten Zielspeicherlösung unterscheidet sich das Verfahren geringfügig. Die allgemeinen Schritte sind nachfolgend beschrieben.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM mit Datenträgern in den Tarifen Premium/Ultra für Daten
Bei VMs, die mit Datenträgern in den Tarifen Premium oder Ultra bereitgestellt werden, gilt die Standardkonfiguration für die SAP HANA-Systemreplikation für die Einrichtung von HSR. Eine Übersicht über die Schritte zum Einrichten der Systemreplikation finden Sie im [SAP-Hilfeartikel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html). Der Artikel behandelt auch die Übernahme eines sekundären Systems, ein Failback zum primären System und die Deaktivierung der Systemreplikation. Für die Migration benötigen wir nur die Schritte zur Einrichtung, zum Übergeben und zum Deaktivieren der Replikation.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM mit ANF für Daten- und Protokollvolumes
Auf allgemeiner Ebene müssen die neuesten HLI-Speichermomentaufnahmen der vollständigen Daten- und Protokollvolumes in Azure Storage kopiert werden. Von dort aus sind sie für die HANA-Ziel-VM zugänglich und wiederherstellbar.  Der Kopiervorgang kann mit beliebigen nativen Linux-Kopiertools durchgeführt werden.  

> [!IMPORTANT]
> Das Kopieren und die Datenübertragung können abhängig von der HANA-Datenbankgröße und der Netzwerkbandbreite mehrere Stunden dauern. Der Großteil des Kopiervorgangs sollte vor der Ausfallzeit der primären HANA-Datenbank erfolgen.

### <a name="mcos-to-mdc-conversion"></a>Wechsel von MCOS zu MDC
Einige unserer HLI-Kunden haben das Bereitstellungsmodell mit mehreren Komponenten in einem System (Multiple Components in One System, MCOS) verwendet. Die Motivation bestand darin, die Einschränkungen bei MDC-Speichermomentaufnahmen (Multiple Databases Container) früherer SAP HANA-Versionen zu umgehen.  Im MCOS-Modell werden mehrere unabhängige SAP HANA-Instanzen auf einer HLI gestapelt. HSR kann problemlos für die Migration verwendet werden, führt aber zu mehreren HANA-VMs mit jeweils einer Mandantendatenbank. Dieses Modell sorgt für eine viel belebtere Landschaft, als Ihnen vielleicht lieb ist. Die Standardbereitstellung für SAP HANA 2.0 ist MDC. Eine Alternative besteht darin, nach der HSR-Migration den [HANA-Mandanten zu verschieben](https://launchpad.support.sap.com/#/notes/2472478). Bei der Verschiebung von HANA-Mandanten werden diese unabhängigen HANA-Datenbanken in Co-Mandanten in einem einzelnen HANA-Container kombiniert.  

### <a name="application-layer-consideration"></a>Überlegungen zur Anwendungsschicht
Der Datenbankserver gilt als Mittelpunkt eines SAP-Systems.  Alle Anwendungsserver sollten sich in der Nähe der SAP HANA-Datenbank befinden. Wenn Sie eine neue PPG verwenden möchten, müssen Sie möglicherweise vorhandene Anwendungsserver auf die PPG verschieben, in der sich die HANA-VM befindet.  Das Erstellen neuer Anwendungsserver ist möglicherweise einfacher, wenn Sie bereits über Bereitstellungsvorlagen verfügen.

Suchen Sie vorhandene Anwendungsserver und die neue HANA-VM auf optimale Weise. Dann müssen Sie keine neuen Anwendungsserver erstellen, es sei denn, Sie wünschen mehr Kapazität.

Wenn Sie eine neue Infrastruktur erstellen, um die Dienstverfügbarkeit zu verbessern, werden Ihre vorhandenen Anwendungsserver möglicherweise nicht mehr benötigt. Sie können heruntergefahren und gelöscht werden.
Wenn der Hostname der Ziel-VM geändert wurde und sich vom HLI-Hostnamen unterscheidet, passen Sie die SAP-Anwendungsserverprofile an, sodass sie auf den neuen Host verweisen. Wenn sich nur die IP-Adresse der HANA-Datenbank geändert hat, aktualisieren Sie den DNS-Eintrag, um eingehende Verbindungen zur neuen HANA-VM zu leiten.

### <a name="acceptance-test"></a>Akzeptanztest
Bei der Migration von HLI zu VM wird der Inhalt der Datenbank im Vergleich zu einer heterogenen Migration nicht wesentlich verändert. Dennoch wird empfohlen, die Leistung des neuen Setups zu überprüfen.  

### <a name="cutover-plan"></a>Umstellungsplan
Diese Migration ist zwar sehr direkt, schließt jedoch auch die Außerbetriebnahme einer vorhandenen Datenbank ein.  Eine sorgfältige Planung ist von zentraler Bedeutung, um das Quellsystem mit seinen Inhalten und Sicherungsimages für den Fall beizubehalten, dass ein Fallback erforderlich ist. Eine gute Planung ermöglicht eine schnellere Umkehrung.   

## <a name="post-migration"></a>Nach der Migration
Der Migrationsauftrag wird erst ausgeführt, wenn alle HLI-abhängigen Dienste und Verbindungen sicher entkoppelt wurden, um die Datenintegrität sicherzustellen. Außerdem wird empfohlen, unnötige Dienste herunterzufahren. In diesem Abschnitt werden einige der wichtigeren Elemente erläutert.

### <a name="decommissioning-the-hli"></a>Außerbetriebsetzung der HLI-Umgebung
Stellen Sie nach der erfolgreichen Migration der HANA-Datenbank zu einer Azure-VM sicher, dass keine Geschäftstransaktionen in der HLI-Datenbank ausgeführt werden.  Wenn Sie die HLI jedoch für die Dauer ihres lokalen Aufbewahrungszeitfensters für Sicherungen ausführen, wird bei Bedarf eine schnellere Wiederherstellung sichergestellt. Nur wenn das Aufbewahrungszeitfenster für lokale Sicherungen abgelaufen ist, sollten Sie die HLI außer Betrieb setzen. Sie sollten dann die HLI-Verpflichtungen mit Microsoft in vertraglicher Form festhalten und sich dazu an ihren Microsoft-Vertriebspartner wenden.

### <a name="remove-any-proxy-for-example-iptables-bigip-configured-for-hli"></a>Entfernen aller für HLI konfigurierten Proxys (z. B. IPTables, BIGIP) 
Wenn ein Proxydienst wie IPTables zum Weiterleiten von lokalem Datenverkehr an und von HLI verwendet wird, ist er nach der erfolgreichen Migration zum virtuellen Computer nicht mehr erforderlich.  Dieser Konnektivitätsdienst sollte jedoch so lange beibehalten werden, wie die HLI noch bereitsteht.  Fahren Sie den Dienst erst herunter, sobald die HLI vollständig außer Betrieb genommen ist.

### <a name="remove-global-reach-for-hli"></a>Entfernen von Global Reach für HLI 
Global Reach wird verwendet, um das ExpressRoute-Gateway von Kunden mit dem HLI ExpressRoute-Gateway zu verbinden.  Es macht es möglich, dass der lokale Datenverkehr von Kunden den HLI-Mandanten direkt erreicht, ohne Nutzung eines Proxydiensts. Diese Verbindung wird nach der Migration und in der Abwesenheit der HLI-Einheit nicht mehr benötigt. Wie im Fall des IPTables-Proxydiensts sollte auch GlobalReach aufrechterhalten werden, bis die HLI vollständig außer Betrieb genommen ist.

### <a name="operating-system-subscription--movereuse"></a>Betriebssystemabonnement: Verschieben/Wiederverwenden
Wenn die VM-Server bereitgestellt und die HLIs außer Betrieb genommen sind, können die Betriebssystemabonnements ersetzt oder wiederverwendet werden. Es ist nicht erforderlich, doppelt für Betriebssystemlizenzen zu bezahlen.

## <a name="next-steps"></a>Nächste Schritte

Planen Sie Ihre SAP-Bereitstellung.

> [!div class="nextstepaction"]
> [Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure](sap-deployment-checklist.md)

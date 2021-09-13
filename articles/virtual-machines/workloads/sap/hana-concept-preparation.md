---
title: Prinzipien der Notfallwiederherstellung und Vorbereitung darauf unter SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Machen Sie sich vertraut mit Prinzipien der Notfallwiederherstellung und Vorbereitung darauf unter SAP HANA in Azure (große Instanzen).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79991bcdd5174d2655f3c917d0898b0b87b0ca50
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217069"
---
# <a name="disaster-recovery-principles-and-preparation"></a>Prinzipien der Notfallwiederherstellung und Vorbereitung darauf

In diesem Artikel werden wichtige Prinzipien für die Notfallwiederherstellung (Disaster Recovery, DR) für große HANA-Instanzen (auch als BareMetal-Infrastruktur bezeichnet) erläutert. Wir führen Sie durch die Schritte, die Sie zur Vorbereitung der Notfallwiederherstellung ausführen müssen. Außerdem erfahren Sie, wie Sie Ihr RTO (Recovery Time Objective) und Recovery Point Objective (RPO) in einem Notfall erreichen. 

## <a name="dr-principles-for-hana-large-instances"></a>DR-Prinzipien für HANA (große Instanzen)

Für HANA (große Instanzen) ist eine Notfallwiederherstellungsfunktion zwischen Stempeln für HANA (große Instanzen) in verschiedenen Azure-Regionen verfügbar. Angenommen, Sie stellen HANA (große Instanzen) in der Azure-Region „USA, Westen“ bereit. Dann können Sie HANA (große Instanzen) in der Region „USA, Osten“ als DR-Einheiten verwenden. Die Notfallwiederherstellung wird nicht automatisch konfiguriert, da sie eine weitere kostenpflichtige Instanz von HANA (große Instanzen) in der DR-Region erfordert. Die Notfallwiederherstellungseinrichtung kann mit zentralem und horizontalem Hochskalieren verwendet werden. 

Die meisten Kunden verwenden die Einheit in der DR-Region zum Ausführen nicht zur Produktion verwendeter Systeme mit einer installierten HANA-Instanz. Die Instanz von HANA (große Instanzen) muss der gleichen SKU angehören wie die für die Produktion verwendete SKU. In der folgenden Abbildung ist dargestellt, wie die Datenträgerkonfiguration zwischen der Servereinheit in der Azure-Produktionsregion und der Region für die Notfallwiederherstellung aussieht:

![Konfiguration mit Notfallwiederherstellungseinrichtung aus Datenträgersicht](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Wie in dieser Übersichtsgrafik dargestellt, müssen Sie eine zweite Gruppe von Datenträgervolumes anfordern. Die Zieldatenträgervolumes, die dem Server von HANA (große Instanzen) am DR-Standort zugeordnet sind, haben die gleiche Größe wie die Produktionsvolumes. 

Die folgenden Volumes werden von der Produktionsregion auf den Standort für die Notfallwiederherstellung repliziert:

- /hana/data
- /hana/logbackups 
- /hana/shared (einschließlich „/usr/sap“)

Das Volume „/hana/log“ wird nicht repliziert. Das SAP HANA-Transaktionsprotokoll wird bei der Wiederherstellung von diesen Volumes nicht benötigt.

## <a name="hana-large-instance-storage-replication"></a>Speicherreplikation von HANA (große Instanzen) 

Die Grundlage der DR-Funktionalität in der Infrastruktur von HANA (große Instanzen) ist die Speicherreplikation. Die speicherseitig verwendete Funktion ist kein konstanter Datenstrom mit Änderungen, die asynchron repliziert werden, wenn Änderungen am Speichervolume auftreten. Stattdessen basiert der Mechanismus darauf, dass in regelmäßigen Abständen Momentaufnahmen dieser Volumes erstellt werden. Anschließend werden die Veränderungen zwischen einer bereits replizierten und einer neuen, noch nicht replizierten Momentaufnahme auf Zieldatenträgervolumes am DR-Standort übertragen. Diese Momentaufnahmen werden auf den Volumes gespeichert. Im Falle eines DR-Failovers müssen sie auf diesen Volumes wiederhergestellt werden.  

Die erste Übertragung der gesamten Daten des Volumes sollte durchgeführt werden, bevor die Menge der Daten die Veränderungen zwischen den Momentaufnahmen unterschreitet. Dann enthalten die Volumes am DR-Standort sämtliche Volumemomentaufnahmen, die am Produktionsstandort erstellt wurden. Sie können das Notfallwiederherstellungssystem dann verwenden, um zu einem früheren Status zurückzukehren und verloren gegangene Daten wiederherzustellen, ohne dass ein Rollback des Produktionssystems erforderlich ist.

Bei MCOD-Bereitstellungen mit mehreren unabhängigen SAP HANA-Instanzen auf einer HANA-Einheit (große Instanzen) sollte der Speicher aller SAP HANA-Instanzen auf die DR-Seite repliziert werden.

Wenn Sie die HANA-Systemreplikation für Hochverfügbarkeit für Ihren Produktionsstandort und die speicherbasierte Replikation für den DR-Standort verwenden, werden die Volumes beider Knoten vom primären Standort auf die DR-Instanz repliziert. Erwerben Sie zusätzlichen Speicher (mit derselben Größe wie der primäre Knoten) am DR-Standort, um die Replikation sowohl des primären als auch sekundären Knotens für die DR zu ermöglichen. 

>[!NOTE]
>Die Speicherreplikationsfunktion von HANA (große Instanzen) spiegelt und repliziert Speichermomentaufnahmen. Wenn Sie keine Speichermomentaufnahmen erstellen, wie unter [Sichern und Wiederherstellen](hana-backup-restore.md) beschrieben, kann keine Replikation auf den DR-Standort stattfinden. Die Erstellung von Speichermomentaufnahmen ist eine Voraussetzung für die Speicherreplikation am Standort für die Notfallwiederherstellung.

## <a name="preparation-of-the-disaster-recovery-scenario"></a>Vorbereitung für das Notfallwiederherstellungsszenario
In diesem DR-Szenario wird davon ausgegangen, dass in der Azure-Produktionsregion ein Produktionssystem in HANA (große Instanzen) ausgeführt wird. Für die folgenden Schritte wird vorausgesetzt, die SID des HANA-Systems sei „PRD“. Außerdem wird vorausgesetzt, dass Sie über ein nicht zur Produktion verwendetes System verfügen, das auf HANA (große Instanzen) in der DR-Azure-Region ausgeführt wird. Die SID ist „TST“. Diese Konfiguration ist in der folgenden Abbildung dargestellt:

![Starten der Notfallwiederherstellungseinrichtung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Angenommen, die Serverinstanz wurde noch nicht mit dem zusätzlichen Speichervolume bestellt. Dann fügt SAP HANA unter der Azure-Dienstverwaltung die hinzugefügten Volumes an. Sie sind ein Ziel für das Produktionsreplikat auf der Instanz von HANA (große Instanzen), auf der Sie die HANA-Instanz „TST“ ausführen. Sie müssen die SID Ihrer HANA-Produktionsinstanz angeben. Nachdem SAP HANA auf der Azure-Dienstverwaltung sichergestellt hat, dass diese Volumes angefügt wurden, müssen Sie diese Volumes in die Instanz von HANA (große Instanzen) einbinden.

![Nächster Schritt: Notfallwiederherstellungseinrichtung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Als Nächstes installieren Sie in der Azure-Region für die Notfallwiederherstellung die zweite SAP HANA-Instanz auf der Instanz von HANA (große Instanzen), wo Sie die HANA-Instanz „TST“ ausführen. Die neu installierte SAP HANA-Instanz muss dieselbe SID aufweisen. Die erstellten Benutzer müssen dieselbe UID und Gruppen-ID wie die Produktionsinstanz aufweisen. Ausführliche Informationen finden Sie unter [Sichern und Wiederherstellen](hana-backup-restore.md). Wenn die Installation erfolgreich war, müssen Sie Folgendes tun:

- Führen Sie Schritt 2 der Vorbereitung der Speichermomentaufnahme durch, wie unter [Sichern und Wiederherstellen](hana-backup-restore.md) beschrieben.
- Erstellen Sie einen öffentlichen Schlüssel für die DR-Einheit der Instanz von HANA (große Instanzen), falls Sie dies noch nicht durchgeführt haben. Weitere Informationen finden Sie in Schritt 3 der unter [Sichern und Wiederherstellen](hana-backup-restore.md) beschriebenen Vorbereitung der Speichermomentaufnahme.
- Behalten Sie die Datei *HANABackupCustomerDetails.txt* für die neue HANA-Instanz bei, und testen Sie, ob Verbindungen mit dem Speicher ordnungsgemäß funktionieren.  
- Beenden Sie die neu installierte SAP HANA-Instanz auf der Instanz von HANA (große Instanzen) in der Azure-Region für die Notfallwiederherstellung.
- Heben Sie die Bereitstellung dieser PRD-Volumes auf, und wenden Sie sich an das Dienstverwaltungsteam für SAP HANA in Azure. Die Volumes können nicht mehr für die Einheit bereitgestellt werden, da auf die Volumes nicht zugegriffen werden kann, wenn sie als Speicherreplikationsziel fungieren.  

![Diagramm: Replikationsbeziehung zwischen den PRD-Volumes in der Azure-Produktionsregion und den PRD-Volumes in der Azure-Region für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Das Betriebsteam stellt die Replikationsbeziehung zwischen den PRD-Volumes in der Produktionsregion und den PRD-Volumes in der DR-Region her.

>[!IMPORTANT]
>Das Volume „/hana/log“ wird nicht repliziert, da es nicht erforderlich ist, die replizierte SAP HANA-Datenbank am Notfallwiederherstellungsstandort in einem konsistenten Zustand wiederherzustellen.

Legen Sie als Nächstes den Sicherungszeitplan für Speichermomentaufnahmen fest, um Ihr RTO und RPO bei einem Notfall zu erreichen. Legen Sie zur Minimierung des RPO folgende Replikationsintervalle im Dienst HANA (große Instanzen) fest:
- Legen Sie für die durch die kombinierte Momentaufnahme abgedeckten Volumes (Momentaufnahmetyp **hana**) fest, dass sie alle 15 Minuten an die entsprechenden Speichervolumeziele am Standort für die Notfallwiederherstellung repliziert werden.
- Legen Sie für das Volume für die Transaktionsprotokollsicherung (Momentaufnahmetyp **logs**) fest, dass es alle drei Minuten an den entsprechenden Speichervolumezielen am Standort für die Notfallwiederherstellung repliziert wird.

So minimieren Sie das RPO:
- Führen Sie alle 30 Minuten bis 1 Stunde Speichermomentaufnahmen vom Typ **hana** durch. Weitere Informationen finden Sie unter [Sichern mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md).
- Führen Sie alle fünf Minuten SAP HANA-Transaktionsprotokollsicherungen durch.
- Führen Sie alle 5 bis 15 Minuten Speichermomentaufnahmen vom Typ **logs** durch. Mit diesem Intervall erzielen Sie einen RPO-Wert von etwa 15 bis 25 Minuten.

Die folgende Grafik zeigt eine mögliche Sequenz der Transaktionsprotokollsicherungen und Speichermomentaufnahmen sowie der Replikation des Volumes mit der HANA-Transaktionsprotokollsicherung und von „/hana/data“ und „/hana/shared“ (einschließlich „/usr/sap“) bei Verwendung dieser Einstellungen:

 ![Beziehung zwischen Momentaufnahme der Transaktionsprotokollsicherung und Momentaufnahmespiegelung im Zeitverlauf](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Um bei der Notfallwiederherstellung einen noch besseren RPO-Wert zu erzielen, können Sie die HANA-Transaktionsprotokollsicherungen aus SAP HANA in Azure (große Instanzen) in die andere Azure-Region kopieren. Führen Sie für diese weitere RPO-Reduzierung die folgenden Schritte aus:

1. Sichern Sie das HANA-Transaktionsprotokoll so häufig wie möglich unter „/hana/logbackups“.
1. Kopieren Sie mithilfe von rsync die Transaktionsprotokollsicherungen auf die virtuellen Azure-Computer, die auf der NFS-Freigabe gehostet werden. Die VMs befinden sich in virtuellen Azure-Netzwerken in der Azure-Produktionsregion und der DR-Region. Stellen Sie eine Verbindung beider virtueller Azure-Netzwerke mit den großen Produktionsinstanzen von HANA (große Instanzen) in Azure her. Weitere Informationen finden Sie unter [Überlegungen zu Netzwerken für die Notfallwiederherstellung mit HANA (große Instanzen)](hana-overview-high-availability-disaster-recovery.md#network-considerations-for-disaster-recovery-with-hana-large-instances). 
1. Belassen Sie die Transaktionsprotokollsicherungen in der Region der VM, die dem exportierten NFS-Speicher angefügt ist.
1. Ergänzen Sie bei einem Notfallfailover die Transaktionsprotokollsicherungen aus dem Volume „/hana/logbackups“ mit neueren Transaktionsprotokollsicherungen auf der NFS-Freigabe am DR-Standort. 
1. Starten Sie eine Wiederherstellung der Transaktionsprotokollsicherung auf die neueste Sicherung, die in der Region für die Notfallwiederherstellung gespeichert werden konnte.

Wenn das Betriebsteam für HANA (große Instanzen) bestätigt, dass die Replikationsbeziehung eingerichtet ist, und Sie die Speichermomentaufnahme-Sicherungen für die Ausführung starten, beginnt die Replikation der Daten.

![Schritt zur Notfallwiederherstellungseinrichtung vor der Einrichtung der Replikation](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Bei der Replikation werden die Momentaufnahmen nicht auf den PRD-Volumes in den DR-Azure-Regionen für die Notfallwiederherstellung. Die Momentaufnahmen werden nur gespeichert. Wenn die Volumes in einem solchen Zustand eingebunden werden, stellen sie den Status dar, in dem Sie die Bereitstellung dieser Volumes aufgehoben haben, nachdem die SAP HANA-Instanz „PRD“ auf dem Server in der DR-Azure-Region installiert wurde. Darüber hinaus stellen sie die Speichersicherungen dar, die noch nicht wiederhergestellt wurden.

Bei einem Failover können Sie anstelle der neuesten Speichermomentaufnahme auch eine ältere Speichermomentaufnahme wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über den Failoverprozess bei der Notfallwiederherstellung.

> [!div class="nextstepaction"]
> [Failoverprozedur für die Notfallwiederherstellung](hana-failover-procedure.md)

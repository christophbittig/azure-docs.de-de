---
title: HANA-Failover zu einem Standort für die Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie ein Failover zu einem Standort für die Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) ausführen.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5720d1ce2281e8984db98b0dc900c4b9de8a52a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284867"
---
# <a name="disaster-recovery-failover-procedure"></a>Failoverprozedur für die Notfallwiederherstellung

>[!IMPORTANT]
>Dieser Artikel ist kein Ersatz für die Verwaltungsdokumentation zu SAP HANA oder für SAP Notes. Es wird davon ausgegangen, dass Sie über solide Kenntnisse in Bezug auf die SAP HANA-Verwaltung und den Betrieb verfügen, insbesondere in den Bereichen Sicherung, Wiederherstellung, Hochverfügbarkeit und Notfallwiederherstellung (DR). In diesem Artikel werden Screenshots von SAP HANA Studio gezeigt. Inhalt, Struktur und grundlegender Aufbau der Bildschirme in den SAP-Verwaltungstools sowie auch die Tools selbst können sich von Release zu Release von SAP HANA ändern.

In diesem Artikel werden die einzelnen Schritte zum Ausführen eines Failovers zu einem Standort für die Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben. 

## <a name="failover-scenarios-and-options"></a>Failoverszenarien und -optionen

Beim Failover auf einen Standort für die Notfallwiederherstellung müssen zwei Fälle berücksichtigt werden:

- Die SAP HANA-Datenbank muss zum aktuellen Status der Daten zurückgeführt werden. Für diesen Fall steht ein Self-Service-Skript zur Verfügung, mit dem Sie das Failover ausführen können, ohne sich an Microsoft zu wenden. Für das Failback müssen Sie jedoch mit Microsoft zusammenarbeiten.
- Sie möchten eine Speichermomentaufnahme wiederherstellen, bei der es sich nicht um die letzte replizierte Momentaufnahme handelt. In diesem Fall müssen Sie mit Microsoft zusammenarbeiten. 

>[!NOTE]
>Sie müssen auf der HANA-Instanz (große Instanzen) am Standort für die Notfallwiederherstellung die folgenden Schritte ausführen. 
 
Um die letzte replizierte Speichermomentaufnahme wiederherzustellen, befolgen Sie die Schritte unter „Perform full DR failover – azure_hana_dr_failover“ (Ausführen eines vollständigen Failovers für die Notfallwiederherstellung – azure_hana_dr_failover) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 

Wenn Sie für mehrere SAP HANA-Instanzen ein Failover ausführen möchten, führen Sie den Befehl azure_hana_dr_failover mehrfach aus. Geben Sie bei entsprechender Aufforderung die SAP HANA-SID ein, für die Sie das Failover und die Wiederherstellung ausführen möchten. 


Sie können das Notfallwiederherstellungs-Failover ohne Auswirkungen auf die tatsächliche Replikationsbeziehung testen. Um ein Testfailover auszuführen, befolgen Sie die Schritte unter „Ausführen eines Testfailovers für die Notfallwiederherstellung – azure_hana_test_dr_failover“ auf der Seite zu den [Microsoft-Momentaufnahmetools für SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>Führen Sie auf der Instanz, die Sie am Standort der Notfallwiederherstellung beim **Testen eines Failovers** erstellt haben, *keine* Produktionstransaktionen durch. Der Befehl azure_hana_test_dr_failover erstellt einen Satz von Volumes, die keine Beziehung zum primären Standort haben. Eine Synchronisierung zurück an den primären Standort ist daher *nicht* möglich. 

Führen Sie das Skript mehrfach aus, wenn Sie Tests für mehrere SAP HANA-Instanzen durchführen möchten. Geben Sie bei entsprechender Aufforderung die SAP HANA-SID der Instanz ein, die Sie für das Failover testen möchten. 

## <a name="set-dr-volumes-to-an-earlier-snapshot"></a>Festlegen von Notfallwiederherstellungsvolumes auf eine frühere Momentaufnahme

Angenommen, Sie müssen ein Failover zum Standort für die Notfallwiederherstellung ausführen, um Daten zu retten, die vor mehreren Stunden gelöscht wurden. Hierfür müssen Sie die Notfallwiederherstellungsvolumes auf eine frühere Momentaufnahme festlegen. Anschließend gilt die folgende Vorgehensweise: 

1. Fahren Sie die nicht für die Produktion bestimmte Instanz von HANA auf der von Ihnen ausgeführten HANA-Instanz (große Instanzen) für die Notfallwiederherstellung herunter. Eine ruhende HANA-Produktionsinstanz ist vorinstalliert.
1. Stellen Sie sicher, dass keine SAP HANA-Prozesse ausgeführt werden. Verwenden Sie für diese Überprüfung den folgenden Befehl:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      In der Ausgabe muss der Prozess **hdbdaemon** den Zustand „Beendet“ aufweisen, und es dürfen keine anderen HANA-Prozesse mit dem Zustand „Gestartet“ vorhanden sein.
1. Überprüfen Sie, unter welchen Momentaufnahmenamen bzw. welcher SAP HANA-Sicherungs-ID der Standort für die Notfallwiederherstellung wiederhergestellt werden soll. In der Praxis handelt es sich dabei in der Regel um die neueste Momentaufnahme. Wenn Sie verlorene Daten wiederherstellen müssen, wählen Sie eine ältere Momentaufnahme.
1. Wenden Sie sich mit einer Supportanfrage mit hoher Priorität an den Azure-Support. Bitten Sie um die Wiederherstellung dieser Momentaufnahme, indem Sie den Namen und das Datum der Momentaufnahme angeben. Sie können zur Identifizierung auch die HANA-Sicherungs-ID am Standort für die Notfallwiederherstellung angeben. Standardmäßig wird auf Operations-Seite nur das Volume „/hana/data“ wiederhergestellt. Wenn auch die Volumes für „/hana/logbackups“ wiederhergestellt werden sollen, müssen Sie dies ausdrücklich angeben. *Stellen Sie das Volume „/hana/shared“ nicht wieder her.* Wählen Sie stattdessen bestimmte Dateien wie etwa „global.ini“ aus dem Verzeichnis **.snapshot** und den zugehörigen Unterverzeichnissen aus, nachdem Sie das Volume „/hana/shared“ für das PRD-Volume erneut eingebunden haben. 

   Von Microsoft Operations werden die folgenden Schritte ausgeführt:

   a. Beenden Sie die Replikation von Momentaufnahmen aus dem Produktionsvolume auf den Notfallwiederherstellungsvolumes. Diese Störung ist unter Umständen bereits eingetreten, falls der Notfall durch einen Ausfall am Produktionsstandort verursacht wurde.
   
   b. Stellen Sie den Namen der Speichermomentaufnahme oder die Momentaufnahme mit der Sicherungs-ID, die Sie auf den Notfallwiederherstellungsvolumes ausgewählt haben, wieder her.
   
Nach der Wiederherstellung können die Notfallwiederherstellungsvolumes in die HANA-Instanzen (große Instanzen) in der Notfallwiederherstellungsregion eingebunden werden.
      
1. Binden Sie die Notfallwiederherstellungsvolumes in die Einheit von HANA (große Instanzen) am Standort für die Notfallwiederherstellung ein. 
1. Starten Sie die ruhende SAP HANA-Produktionsinstanz.
1. Angenommen, Sie entscheiden sich für das Kopieren der Transaktionsprotokollsicherungen, um den RPO-Wert (Recovery Point Objective) zu verringern. Fügen Sie die Transaktionsprotokollsicherungen anschließend in das neu eingebundene Verzeichnis „/hana/logbackups“ für die Notfallwiederherstellung ein. Überschreiben Sie keine vorhandenen Sicherungen. Kopieren Sie neuere Sicherungen, die nicht mit der neuesten Replikation einer Speichermomentaufnahme repliziert wurden.
1. Sie können auch einzelne Dateien über die Momentaufnahmen wiederherstellen, die nicht auf dem Volume „/hana/shared/PRD“ in der Azure-Region für die Notfallwiederherstellung repliziert wurden.

## <a name="recover-the-sap-hana-production-instance"></a>Wiederherstellen der SAP HANA-Produktionsinstanz

Mit den folgenden Schritten wird die Wiederherstellung der SAP HANA-Produktionsinstanz aus der wiederhergestellten Speichermomentaufnahme und den verfügbaren Transaktionsprotokollsicherungen veranschaulicht.

1. Ändern Sie mithilfe von SAP HANA Studio das Sicherungsverzeichnis in **/hana/logbackups**.

   ![Ändern des Sicherungsspeicherorts für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA überprüft alle Sicherungsdateispeicherorte und schlägt für die Wiederherstellung die neueste Transaktionsprotokollsicherung vor. Die Überprüfung kann einige Minuten dauern. Danach wird ein Bildschirm wie der folgende angezeigt:

   ![Liste mit Transaktionsprotokollsicherungen für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Passen Sie einige Standardeinstellungen an:

      - Deaktivieren Sie **Deltasicherungen verwenden**.
      - Wählen Sie **Protokollbereich initialisieren**.

   ![Aktivieren der Option „Protokollbereich initialisieren“](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wählen Sie **Fertig stellen** aus.

   ![Fertigstellen der Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Daraufhin sollte ein Statusfenster wie das folgende erscheinen. Beachten Sie, dass es sich bei diesem Beispiel um eine Notfallwiederherstellung einer SAP HANA-Konfiguration mit drei Knoten für horizontales Hochskalieren handelt.

![Status der Wiederherstellung](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Falls die Wiederherstellung auf dem Bildschirm **Fertig stellen** nicht mehr reagiert und der Statusbildschirm nicht angezeigt wird, überprüfen Sie, ob alle SAP HANA-Instanzen auf den Workerknoten ausgeführt werden. Starten Sie die SAP HANA-Instanzen bei Bedarf manuell.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback von einem Standort für die Notfallwiederherstellung auf den Produktionsstandort

Sie können ein Failback von einem Notfallwiederherstellungsstandort auf einen Produktionsstandort durchführen. Wir sehen uns nun ein Szenario an, bei dem das Failover auf den Notfallwiederherstellungsstandort auf Probleme in der Azure-Produktionsregion zurückzuführen ist (und nicht darauf, dass Sie verloren gegangene Daten wiederherstellen müssen). 

Sie haben Ihre SAP-Produktionsworkload für einen gewissen Zeitraum am Standort für die Notfallwiederherstellung ausgeführt. Nachdem die Probleme am Produktionsstandort behoben wurden, sollten Sie ein Failback auf Ihren Produktionsstandort durchführen. Da hierbei keine Daten verloren gehen dürfen, umfasst der Wechsel zum Produktionsstandort mehrere Schritte und eine enge Zusammenarbeit mit dem Betriebsteam für SAP HANA in Azure. Nach der Behebung der Probleme müssen Sie sich mit dem Betriebsteam in Verbindung setzen, um die Rücksynchronisierung mit dem Produktionsstandort anhand eines Triggers zu initiieren.

Folgen Sie diesen Schritten:

1. Das Betriebsteam für SAP HANA in Azure ruft den Trigger zum Synchronisieren der Produktionsspeichervolumes aus den Speichervolumes für die Notfallwiederherstellung ab, die jetzt den Produktionszustand darstellen. In diesem Zustand wird die HANA-Instanz (große Instanzen) am Produktionsstandort heruntergefahren.
1. Das Betriebsteam für SAP HANA in Azure überwacht die Replikation und stellt sicher, dass die Daten auf dem neuesten Stand sind, bevor es Sie informiert.
1. Sie fahren die Anwendungen herunter, die die HANA-Produktionsinstanz am Standort für die Notfallwiederherstellung verwenden. Führen Sie anschließend eine HANA-Transaktionsprotokollsicherung durch. Beenden Sie als Nächstes die HANA-Instanz, die am Standort für die Notfallwiederherstellung auf HANA-Instanzen (große Instanzen) ausgeführt wird.
1. Nun werden die Datenträgervolumes vom Betriebsteam manuell neu synchronisiert.
1. Das Betriebsteam für SAP HANA in Azure startet die HANA-Instanz (große Instanzen) am Produktionsstandort wieder. Dann übergibt es sie an Sie. Sie stellen sicher, dass die SAP HANA-Instanz beim Starten der HANA-Instanz (große Instanzen) heruntergefahren ist.
1. Sie führen die gleichen Schritte für die Datenbankwiederherstellung aus, die Sie beim vorherigen Failover zum Notfallwiederherstellungsstandort ausgeführt haben.

## <a name="monitor-disaster-recovery-replication"></a>Überwachen der Replikation für die Notfallwiederherstellung

Um den Status der Speicherreplikation zu überwachen, führen Sie das Skript `azure_hana_replication_status` aus. Dieser Befehl muss über eine Einheit ausgeführt werden, die am Standort für die Notfallwiederherstellung ausgeführt wird, damit es erwartungsgemäß funktioniert. Der Befehl funktioniert unabhängig davon, ob die Replikation aktiv ist. Der Befehl kann für jede HANA-Instanz (große Instanzen) Ihres Mandanten am Standort für die Notfallwiederherstellung ausgeführt werden. Er kann nicht zum Abrufen von Details über das Startvolume verwendet werden. 

Weitere Informationen zu dem Befehl und seiner Ausgabe finden Sie unter „Get DR replication status – azure_hana_replication_status“ (Abrufen des Status der Notfallwiederherstellungsreplikation – azure_hana_replication_status) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die Überwachung von SAP HANA in Azure (große Instanzen).

> [!div class="nextstepaction"]
> [Überwachen von SAP HANA in Azure (große Instanzen)](troubleshooting-monitoring.md)

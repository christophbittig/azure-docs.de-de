---
title: HANA-Sicherung und -Wiederherstellung in SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SAP HANA in Azure (große Instanzen) sichern und wiederherstellen.
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
ms.date: 7/02/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b60ba5c169f660cd01f95f53fbc292a768f1b5e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358937"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Sichern und Wiederherstellen von SAP HANA in HANA (große Instanzen)

>[!IMPORTANT]
>Dieser Artikel ersetzt nicht die SAP HANA-Verwaltungsdokumentation oder SAP-Hinweise. Es wird vorausgesetzt, dass Sie über Fachkenntnisse bezüglich der Administration und des Betriebs von SAP HANA verfügen, insbesondere in den Bereichen Sicherung, Wiederherstellung, Hochverfügbarkeit und Notfallwiederherstellung. In diesem Artikel werden Screenshots von SAP HANA Studio gezeigt. Inhalt, Struktur und grundlegender Aufbau der Bildschirme in den SAP-Verwaltungstools sowie auch die Tools selbst können sich von Release zu Release von SAP HANA ändern.

In diesem Artikel werden die Schritte zum Sichern und Wiederherstellen von SAP HANA in Azure (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben. 

Einige der in diesem Artikel beschriebenen Prozesse sind vereinfacht. Sie sind nicht als detaillierte Schritte vorgesehen, die in Betriebshandbücher aufgenommen werden sollen. Führen Sie Ihre Betriebsabläufe testweise mit Ihren spezifischen HANA-Versionen und -Releases aus, um Betriebshandbücher für Ihre Konfigurationen zu erstellen. Anschließend können Sie die Prozesse für Ihre Konfigurationen dokumentieren.

Einer der wichtigsten Aspekte beim Betrieb von Datenbanken ist der Schutz vor Katastrophenfällen. Solche Ereignisse können sowohl durch Naturkatastrophen als auch einfache Benutzerfehler verursacht werden. Durch das Sichern einer Datenbank mit der Möglichkeit einer Wiederherstellung zu einem beliebigen Zeitpunkt. z. B. bevor jemand kritische Daten gelöscht hat, bietet einen entscheidenden Schutz. Sie können Ihre Datenbank in einem Zustand wiederherstellen, der dem Zustand vor der Funktionsunterbrechung so nahe wie möglich kommt.

Um die Möglichkeit zum Wiederherstellen zu erreichen, müssen zwei Arten von Sicherungen ausgeführt werden:

- Datenbanksicherungen: vollständig, inkrementell oder differenziell
- Transaktionsprotokollsicherungen

Sie können vollständige Datenbanksicherungen auf Anwendungsebene oder Sicherungen mit Speichermomentaufnahmen durchführen. Speichermomentaufnahmen sind jedoch kein Ersatz für Transaktionsprotokollsicherungen. Transaktionsprotokollsicherungen werden auch weiterhin für Point-in-Time-Datenbankwiederherstellungen sowie zum Leeren der Protokolle von Transaktionen, für die bereits ein Commit ausgeführt wurde, benötigt. Speichermomentaufnahmen können die Wiederherstellung durch schnelles Bereitstellen eines Rollforwardimages der Datenbank beschleunigen. 

Es gibt zwei Sicherungs- und Wiederherstellungsoptionen für SAP HANA in Azure (große Instanzen):

- Sie können Sicherungen mit einem Drittanbietertool zum Schutz von Daten erstellen. Dieses Tool sollte in der Lage sein, konsistente Momentaufnahmen von Anwendungen zu erstellen, oder es muss die Sicherungsschnittstelle nutzen können, um mehrere Sitzungen an einen geeigneten Sicherungsspeicherort zu streamen. Es sind mehrere unterstützte Tools verfügbar. Die Wahl des Tools sollte mit dem Projektteam besprochen und so gestaltet werden, dass die Anforderungen des Kunden an Sicherungsfenster erfüllt werden. Und ganz wichtig ist es, das Verfahren zur Sicherung und Wiederherstellung in der Projektphase zu testen.
- Sie können Sicherungen von Speichermomentaufnahmen mit einem von Microsoft bereitgestellten Hilfsprogramm einsetzen, wie im nächsten Kapitel beschrieben.

>[!NOTE]
>Vor HANA2.0 SPS4 wurde das Erstellen von Datenbankmomentaufnahmen von mehrinstanzenfähigen Datenbankcontainer-Datenbanken (für mehrere Mandanten) nicht unterstützt.
Ab SPS4 unterstützt SAP diese Momentaufnahmenerstellung vollständig.



## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Verwenden von Speichermomentaufnahmen von SAP HANA in Azure (große Instanzen)

Die zugrunde liegende Speicherinfrastruktur von SAP HANA in Azure (große Instanzen) unterstützt Speichermomentaufnahmen von Volumes. Sicherung und Wiederherstellung von Volumes werden unterstützt, dabei ist jedoch Folgendes zu beachten:

- Anstelle von vollständigen Datenbanksicherungen werden in regelmäßigen Abständen Speichervolume-Momentaufnahmen erstellt.
- Bevor eine Speichermomentaufnahme über Volumes des Typs „/hana/data“ ausgelöst wird, startet das Momentaufnahmetool (azacsnap) eine SAP HANA-Momentaufnahme. Diese SAP HANA-Momentaufnahme ist der Konsistenzpunkt für nachfolgende Wiederherstellungen von Protokollen, nachdem die Speichermomentaufnahme wiederhergestellt wurde. 
- Für erfolgreiche HANA-Momentaufnahmen benötigen Sie eine aktive HANA-Instanz. In einem Szenario mit HANA System Replication (HSR) werden Speichermomentaufnahmen auf aktuellen sekundären Knoten, auf denen keine HANA-Momentaufnahmen ausgeführt werden können, nicht unterstützt.
- Nach erfolgreichem Ausführen der Speichermomentaufnahme wird die SAP HANA-Momentaufnahme gelöscht.
- Für andere Volumes wie „/hana/shared“ (einschließlich „/usr/sap“) kann jederzeit ohne Datenbankinteraktion eine Momentaufnahme erstellt werden.

Transaktionsprotokollsicherungen werden in regelmäßigen Abständen erstellt und auf dem Volume „/hana/logbackups“ oder in Azure gespeichert. Sie können für das Volume „/hana/logbackups“ mit den Transaktionsprotokollsicherungen eine separate Momentaufnahmenerstellung auslösen. In diesem Fall müssen Sie keine HANA-Datenmomentaufnahme ausführen. Da alle Dateien in „/hana/logbackup“ konsistent sind (weil sie „offline“ sind), können Sie sie auch jederzeit an einem anderen Sicherungsspeicherort sichern, um sie zu archivieren.
Wenn Sie bei einem Produktionsausfall eine Datenbank zu einem bestimmten Zeitpunkt wiederherstellen müssen, kann das Tool azacsnap entweder eine Momentaufnahme auf ein neues Volume klonen, um die Datenbank wiederherzustellen (bevorzugte Wiederherstellungsmethode), oder eine Momentaufnahme auf demselben Datenvolume wiederherstellen, auf dem sich die Datenbank befindet.

>[!NOTE]
> Wenn Sie eine ältere Momentaufnahme (SnapRevert) auf dem ursprünglichen Datenvolume wiederherstellen, werden alle neueren erstellten Momentaufnahmen gelöscht. Das Speichersystem geht so vor, da die Datenpunkte für die neueren erstellten Momentaufnahmen ungültig sind. Beginnen Sie stets damit, die letzte Momentaufnahme wiederherzustellen oder noch besser, die Momentaufnahme auf ein neues Volume zu klonen. Beim Klonvorgang wird nichts gelöscht.


## <a name="storage-snapshot-considerations"></a>Aspekte von Speichermomentaufnahmen

>[!NOTE]
>Speichermomentaufnahmen beanspruchen Speicherplatz, der den Einheiten von HANA (große Instanzen) zugeteilt ist. Berücksichtigen Sie bei der Planung von Speichermomentaufnahmen und der Festlegung der Anzahl von Speichermomentaufnahmen die folgenden Aspekte. 

Zur besonderen Funktionsweise von Speichermomentaufnahmen von SAP HANA in Azure (große Instanzen) gehört Folgendes:

- Eine individuelle Speichermomentaufnahme beansprucht zum Zeitpunkt der Erstellung nur wenig Speicherplatz.
- Wenn sich Dateninhalte und der Inhalt in SAP HANA-Datendateien auf dem Speichervolume ändern, müssen der ursprüngliche Blockinhalt und die Datenänderungen in der Momentaufnahme gespeichert werden.
- Die Speichermomentaufnahme vergrößert sich infolgedessen. Je länger die Momentaufnahme vorhanden ist, desto größer wird die Speichermomentaufnahme.
- Je mehr Änderungen am SAP HANA-Datenbankvolume während der Lebensdauer eines Speicher-Snapshots erfolgen, desto mehr Speicherplatz belegt der Speicher-Snapshot.

SAP HANA in Azure (große Instanzen) bietet standardmäßig feste Volumegrößen für die SAP HANA-Daten- und Protokollvolumes. Das Erstellen von Momentaufnahmen dieser Volumes belegt Volumespeicherplatz. Sie müssen folgende Schritte durchführen:

- Legen Sie fest, wann geplante Speichermomentaufnahmen erstellt werden sollen.
- Überwachen Sie den Speicherplatzverbrauch der Speichervolumes. 
- Verwalten Sie die Anzahl der gespeicherten Momentaufnahmen. 

Sie können Speichermomentaufnahmen deaktivieren, wenn Sie Unmengen von Daten importieren oder andere wichtige Änderungen an der HANA-Datenbank vornehmen. 


Die folgenden Abschnitte enthalten Informationen zum Erstellen dieser Momentaufnahmen sowie allgemeine Empfehlungen:

- Obwohl die Hardware 255 Momentaufnahmen pro Volume unterstützen kann, ist es ratsam, unter diesem Wert zu bleiben. Die Empfehlung liegt bei höchstens 250.
- Überwachen und verfolgen Sie freien Speicherplatz nach, bevor Sie Speichermomentaufnahmen erstellen.
- Verringern Sie die Anzahl von Speichermomentaufnahmen basierend auf dem freien Speicherplatz. Sie können die Anzahl der gespeicherten Momentaufnahmen verringern oder die Volumes erweitern. Sie können zusätzlichen Speicher in 1-TB-Schritten buchen.
- Deaktivieren Sie Speichermomentaufnahmen für das Volume „/hana/data“ bei Aktivitäten wie dem Verschieben von Daten in SAP HANA mit SAP-Plattformmigrationstools (R3load) oder dem Wiederherstellen von SAP HANA-Datenbanken aus Sicherungen. 
- Vermeiden Sie bei größeren Neuorganisationen von SAP HANA-Tabellen nach Möglichkeit Speichermomentaufnahmen.
- Speichermomentaufnahmen sind eine Voraussetzung, um von den Notfallwiederherstellungsfunktionen von SAP HANA in Azure (große Instanzen) profitieren zu können.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Voraussetzungen für die Nutzung von Self-Service-Speichermomentaufnahmen

Lesen Sie die Dokumentation [Worum handelt es sich beim Tool für konsistente Momentaufnahmen in Azure-Anwendungen?](../../../azure-netapp-files/azacsnap-introduction.md)

Für die Implementierung dieses Tools gibt es zwei Möglichkeiten. 
1)  Lokal auf dem Datenbankserver
2)  Remote auf einer Sicherungs-VM

Wenn Sie eine Sicherungs-VM erstellen, stellen Sie sicher, dass der neueste HANA-Client auf dieser VM installiert ist. Bei dieser Methode muss azacsnap eine Remotedatenbankverbindung mit einer HANA-Instanz herstellen können, die auf einer anderen VM ausgeführt wird.
Sie müssen beim Microsoft-Support-Team einen SSH-Schlüssel und Speicherbenutzer beantragen, um auf den Speicher zugreifen zu können. Ohne diesen SSH-Schlüssel und Benutzer ist es nicht möglich, Momentaufnahmen zu erstellen.

## <a name="download-and-set-up-azacsnap"></a>Herunterladen und Einrichten von azacsnap

Um Speichermomentaufnahmen mit SAP HANA (großen Instanzen) einzurichten, beginnen Sie mit dem Herunterladen und Installieren des Tools azacsnap, wie unter [Erste Schritte mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-get-started.md) beschrieben.

Azacsnap erstellt standardmäßig einen Benutzer namens „azacsnap“. Wenn Sie einen anderen Namen bevorzugen, können Sie ihn während der Installation angeben. Weitere Informationen finden Sie in der Dokumentation weiter oben.

## <a name="subsequent-next-steps"></a>Die nächsten Schritte
Befolgen Sie die Dokumentation zu azacsnap für folgende Aufgaben:

- [Installieren des Tools für konsistente Momentaufnahmen](../../../azure-netapp-files/azacsnap-installation.md)
- [Konfigurieren des Tools für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Testen des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Sichern mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Abrufen von Details mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Löschen mit dem Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Durchführen der Wiederherstellung mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Notfallwiederherstellung mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Tipps und Tricks zur Verwendung des Tools für konsistente Momentaufnahmen in Azure-Anwendungen für Azure NetApp Files](../../../azure-netapp-files/azacsnap-tips.md)


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel [Worum handelt es sich beim Tool für konsistente Momentaufnahmen in Azure-Anwendungen?](../../../azure-netapp-files/azacsnap-introduction.md)

  




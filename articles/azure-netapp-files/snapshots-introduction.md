---
title: Funktionsweise von Azure NetApp Files-Momentaufnahmen | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure NetApp Files-Momentaufnahmen funktionieren, welche Möglichkeiten es zum Erstellen von Momentaufnahmen gibt, wie Momentaufnahmen wiederhergestellt werden und wie Sie Momentaufnahmen in regionsübergreifenden Replikationseinstellungen verwenden.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 15216e29c20503babfd0b03b6a4223281ec038f8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092920"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Funktionsweise von Azure NetApp Files-Momentaufnahmen

In diesem Artikel wird die Funktionsweise von Azure NetApp Files-Momentaufnahmen erläutert. Die Momentaufnahmetechnologie von Azure NetApp Files bietet Stabilität, Skalierbarkeit und eine schnellere Wiederherstellbarkeit, ohne dass sich dies auf die Leistung auswirkt. Sie bildet die Grundlage für Datensicherungslösungen, einschließlich der Wiederherstellung einzelner Dateien, der Wiederherstellung von Volumes und Klonen, der regionsübergreifenden Replikation und der langfristigen Speicherung. 

Die Schritte zum Verwenden von Volumemomentaufnahmen finden Sie unter [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Überlegungen zur Verwaltung von Momentaufnahmen bei der regionsübergreifenden Replikation finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Definition von Volumemomentaufnahmen  

Eine Azure NetApp Files-Momentaufnahme ist ein Image eines Dateisystems (Volumes) zu einem bestimmten Zeitpunkt. Er ist ideal als Online-Backup geeignet. Sie können einen Snapshot verwenden, um [ einen neuen Datenträger zu erstellen](snapshots-restore-new-volume.md) (Klonen), [ Wiederherstellen einer Datei](snapshots-restore-file-client.md) oder [ Rückgängigmachen eines Datenträgers](snapshots-revert-volume.md). Bestimmte Anwendungsdaten, die auf Azure NetApp Files-Volumes gespeichert sind, erfordern möglicherweise zusätzliche Schritte, um die Anwendungskonsistenz sicherzustellen.  

Der geringe Mehraufwand für die Momentaufnahmen wird durch die einzigartigen Funktionen der zugrunde liegenden Volumevirtualisierungstechnologie ermöglicht, die Teil von Azure NetApp Files ist. Wie bei einer Datenbank nutzt diese Schicht Zeiger auf die eigentlichen Datenblöcke auf dem Datenträger. Im Gegensatz zu einer Datenbank werden jedoch keine vorhandenen Blöcke neu geschrieben, sondern aktualisierte Daten in neue Blöcke geschrieben und die Zeiger geändert, so dass die neuen und die alten Daten erhalten bleiben. Eine Azure NetApp Files-Momentaufnahme passt also nur die Blockzeiger an. Dadurch entsteht eine „fixierte“, schreibgeschützte Ansicht eines Volumes, über die Anwendungen ohne spezielle Programmierung auf ältere Versionen von Dateien und Verzeichnishierarchien zugreifen können. Es werden keine Datenblöcke kopiert. Aus diesem Grund sind Momentaufnahmen beim Erstellen so effizient. Ihre Generierung erfolgt unabhängig von der Volumegröße nahezu unmittelbar. Snapshots sind außerdem sehr speichereffizient; es werden nur Delta-Blöcke zwischen Snapshots und dem aktiven Volume aufbewahrt.

Die folgenden Diagramme veranschaulichen die Konzepte:  

![Diagramme der wichtigsten Konzepte von Momentaufnahmen](../media/azure-netapp-files/snapshot-concepts.png)

In den Diagrammen wird in Abbildung 1a eine Momentaufnahme erstellt. In Abbildung 1b werden die geänderten Daten in einen *neuen Block* B1 geschrieben und der Zeiger wird aktualisiert. Der Snapshot-Zeiger zeigt jedoch immer noch auf den *zuvor geschriebenen Block* B, so dass Sie eine Live- und eine historische Ansicht der Daten erhalten. In Abbildung 1c wird eine weitere Momentaufnahme erstellt. Damit haben Sie Zugriff auf drei Generationen der Daten (die Livedaten, Momentaufnahme 2 und Momentaufnahme 1, in der Reihenfolge ihres Alters), ohne den Volumespeicherplatz in Anspruch zu nehmen, der für drei vollständige Kopien nötig wäre. 

Für eine Momentaufnahme wird nur eine Kopie der Volumemetadaten (*I-Knoten-Tabelle*) erstellt. Die Erstellung dauert nur wenige Sekunden, und zwar unabhängig von der Volumegröße, der genutzten Kapazität und dem Grad an Aktivität auf dem Volume. Die Erstellung eines Snapshots von einem 100-TiB-Volume nimmt also genauso viel Zeit in Anspruch wie die Erstellung eines Snapshots von einem 100-GiB-Volume. Nachdem eine Momentaufnahme erstellt wurde, spiegeln sich Änderungen an den Datendateien wie üblich in der aktiven Version der Dateien wider.

In der Zwischenzeit bleiben die Datenblöcke, auf die von Snapshots verwiesen wird, stabil und unveränderlich. Aufgrund der "Redirect on Write"-Charakteristik von Azure NetApp Files Volumes verursachen Snapshots keinen Performance-Overhead und verbrauchen selbst keinen Speicherplatz. Sie können im Lauf der Zeit bis zu 255 Momentaufnahmen pro Volume speichern, auf die jeweils als schreibgeschützte und Onlineversion der Daten zugegriffen werden kann. Sie belegen aber nur so viel Kapazität, wie Blöcke zwischen den einzelnen Momentaufnahmen geändert wurden. Geänderte Blöcke werden im aktiven Volume gespeichert. Blöcke, auf die in Momentaufnahmen verwiesen wird, werden (schreibgeschützt) auf dem Volume aufbewahrt und nur dann erneut verwendet, wenn alle Zeiger im aktiven Volume und in den Momentaufnahmen gelöscht wurden. Daher steigt die Volumeauslastung im Lauf der Zeit durch neue Datenblöcke oder (geänderte) Datenblöcke, die in Momentaufnahmen aufbewahrt werden.

 Im folgenden Diagramm werden die Momentaufnahmen eines Volumes und der verwendete Speicherplatz im Lauf der Zeit gezeigt: 

![Diagramm der Momentaufnahmen eines Volumes und der verwendete Speicherplatz im Zeitverlauf](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Da in einer Volumemomentaufnahme nur die Blockänderungen seit der letzten Momentaufnahme aufgezeichnet werden, bietet sie die folgenden wichtigen Vorteile:

* Momentaufnahmen sind ***speichereffizient***.   
    Snapshots verbrauchen nur wenig Speicherplatz, da sie keine Datenblöcke des gesamten Volumes kopieren. Zwei nacheinander erstellte Momentaufnahmen unterscheiden sich nur in den Blöcken, die im Zeitraum zwischen den beiden hinzugefügt oder geändert wurden. Dieses blockweise Verhalten minimiert den damit verbundenen Verbrauch an Speicherkapazität. Viele alternative Implementierungen für Momentaufnahmen verbrauchen Speichervolumes, die dem aktiven Dateisystem entsprechen, und erhöhen so die Speicherkapazitätsanforderungen. Abhängig von der täglichen Änderungsrate auf *Blockebene* von Anwendungen verbrauchen Azure NetApp Files-Momentaufnahmen mehr oder weniger Kapazität, jedoch nur für die geänderten Daten. Der durchschnittliche Verbrauch liegt bei täglichen Momentaufnahmen auf vielen Anwendungsvolumes bei 1–5 % der genutzten Volumekapazität bzw. bei SAP HANA-Datenbankvolumes bei 20–30 %. [Überwachen Sie den Volume- und Momentaufnahmeverbrauch](azure-netapp-files-metrics.md#volumes) auf den Kapazitätsverbrauch von Momentaufnahmen im Verhältnis zur Anzahl der erstellten und verwalteten Momentaufnahmen.   

* Momentaufnahmen können ***schnell erstellt, repliziert, wiederhergestellt oder geklont*** werden.   
    Es dauert nur wenige Sekunden, einen Snapshot zu erstellen, zu replizieren, wiederherzustellen oder zu klonen, unabhängig von der Größe des Volumes und der Aktivität auf dem Volume. Sie können [einen Volume-Snapshot bei Bedarf erstellen](azure-netapp-files-manage-snapshots.md). Sie können auch [Momentaufnahmerichtlinien](snapshots-manage-policy.md) verwenden, um anzugeben, wann Azure NetApp Files automatisch eine Momentaufnahme erstellen soll und wie viele Momentaufnahmen für ein Volume aufbewahrt werden sollen.  Anwendungskonsistenz erreichen Sie durch die Orchestrierung von Momentaufnahmen mit der Anwendungsschicht, beispielsweise durch die Verwendung des Tools [AzAcSnap](azacsnap-introduction.md) für SAP HANA.

* Momentaufnahmen haben keine Auswirkung auf die ***Leistung*** des Volumes.   
    Da die zugrunde liegende Technologie eine „Umleitung beim Schreiben“ nutzt, hat das Speichern oder Beibehalten von Azure NetApp Files-Momentaufnahmen keinerlei Auswirkungen auf die Leistung – selbst bei einer hohen Datenaktivität. Auch das Löschen einer Momentaufnahme hat in den meisten Fällen nur geringe oder gar keine Auswirkungen auf die Leistung. 

* Snapshots bieten ***Skalierbarkeit***, da sie häufig erstellt werden können und viele davon beibehalten werden können.   
    Azure NetApp Files Volumes unterstützen bis zu 255 Snapshots pro Volume. Die Möglichkeit, viele häufig erstellte Snapshots mit geringen Auswirkungen zu speichern, erhöht die Wahrscheinlichkeit, dass die gewünschte Version der Daten erfolgreich wiederhergestellt werden kann.

* Snapshots können ***auf Azure-Speicher ausgelagert*** werden.   
    Für Compliance- und langfristige Datenaufbewahrungsanforderungen verwenden Sie die [Azure NetApp Files Backup](backup-introduction.md)-Funktionalität, um Snapshots auf kosteneffizientem, ZRS-aktiviertem Azure Storage außerhalb des zu schützenden Volumes zu sichern.  

* Momentaufnahmen bieten ***Sichtbarkeit für Benutzer** _ und ermöglichen das _*_Wiederherstellen von Dateien_**.   

Die hohe Leistung, Skalierbarkeit und Stabilität der Azure NetApp Files-Momentaufnahmetechnologie macht sie zur idealen Option für die Onlinesicherung zur benutzergesteuerten Wiederherstellung. Momentaufnahmen können Benutzern für das Wiederherstellen von Dateien, Verzeichnissen oder Volumes verfügbar gemacht werden. Zusätzliche Lösungen ermöglichen das Kopieren von Backups auf Offline-Speicher oder [die regionsübergreifende Replikation](cross-region-replication-introduction.md) für Aufbewahrungs- oder Notfallwiederherstellungszwecke.

## <a name="ways-to-create-snapshots"></a>Optionen zum Erstellen von Momentaufnahmen   

Sie können verschiedene Methoden zum Erstellen und Verwalten von Momentaufnahmen verwenden:

* Manuell (bedarfsgesteuert) mit:   
    * [Azure-Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST-API](/rest/api/netapp/snapshots), [Azure-Befehlszeilenschnittstelle](/cli/azure/netappfiles/snapshot) oder [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)-Tools
    * Skripts (siehe [Beispiele](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatisiert mit:
    * Momentaufnahmerichtlinien mithilfe von [Azure-Portal](snapshots-manage-policy.md), [REST-API](/rest/api/netapp/snapshotpolicies), [Azure-Befehlszeilenschnittstelle](/cli/azure/netappfiles/snapshot/policy) oder [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)-Tools
    * Tool für anwendungskonsistente Momentaufnahmen wie [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Regionsübergreifendes Replizieren von Volumes und Momentaufnahmen für die Notfallwiederherstellung  

Azure NetApp Files unterstützt [regionsübergreifende Replikation](cross-region-replication-introduction.md) für die Notfallwiederherstellung (DR). Für die regionsübergreifende Replikation nutzt Azure NetApp Files die SnapMirror-Technologie. Dabei werden nur die geänderten Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet. Nachdem eine regionsübergreifende Replikation zwischen Volumes initiiert wurde, wird nur einmal der gesamte Volumeinhalt (d. h. die tatsächlich gespeicherten Datenblöcke) übertragen. Dieser Vorgang wird als *Baselinetransfer* bezeichnet. Nach der ersten Übertragung werden nur noch geänderte Blöcke (die in Momentaufnahmen aufgezeichnet werden) übertragen. Das Ergebnis ist ein asynchrones 1:1-Replikat des Quellvolumes einschließlich aller Momentaufnahmen. Dieses Verhalten folgt einem vollständigen und dauerhaft inkrementellen Replikationsmechanismus. Diese Technologie minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Außerdem verkürzt sie die Replikationszeit. Sie können ein kleineres Recovery Point Objective (RPO) erreichen, da mehr Snapshots erstellt und häufiger mit minimalen Datenübertragungen übertragen werden können. Außerdem müssen keine hostbasierten Replikationsmechanismen mehr angewandt werden, sodass auch die Kosten für VMs und Softwarelizenzen wegfallen.

Das folgende Diagramm zeigt den Datenverkehr für Momentaufnahmen in regionsübergreifenden Replikationsszenarien: 

![Diagramm des Datenverkehrs für Momentaufnahmen in regionsübergreifenden Replikationsszenarien](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="how-snapshots-can-be-vaulted-for-long-term-retention-and-cost-savings"></a>Wie Snapshots für eine langfristige Aufbewahrung und Kosteneinsparungen im Tresor gespeichert werden können

Wie bereits beschrieben, werden Snapshots verwendet, um schnelle, platzsparende Backups von Azure NetApp Files Volumes zu erstellen, und zwar effizient und schnell, und sie bieten eine Möglichkeit, Datendateien oder komplette Volumes sehr effektiv wiederherzustellen. Diese Online-Snapshots dienen als erste Verteidigungslinie und decken die meisten Datenwiederherstellungsvorgänge ab.   

Um Snapshots über einen längeren Zeitraum aufzubewahren oder mehr Snapshots als die maximale Anzahl an Online-Snapshots aufzubewahren, können Sie Snapshots von Azure NetApp Files-Volumes in ZRS-aktivierten Azure-Storage auslagern.  Dies wird durch die [*Azure NetApp Files Backup*](backup-introduction.md) Funktionalität erleichtert.  Diese Funktion bewahrt Snapshots über einen längeren Zeitraum auf (bis zu einem Jahr oder sogar länger). Die Backups werden auf Azure-Storage gespeichert, was einen Kostenvorteil gegenüber den Kosten für den Azure NetApp Files-Kapazitätspool bietet, und es wird eine andere Storage-Plattform verwendet, um Abhängigkeiten zu beseitigen und die Aufbewahrungsanforderungen zu erfüllen.

Um Snapshot Vaulting auf Ihrem Azure NetApp Files-Volume zu aktivieren, [konfigurieren Sie eine Backup-Richtlinie](backup-configure-policy-based.md) im Azure NetApp Files-Abonnement (unter dem Abschnitt Data Protection) und geben Sie die Anzahl der täglichen, wöchentlichen und monatlichen Backups an, die Sie aufbewahren möchten. Das ist alles, was Sie tun müssen, um Ihre Datensicherung mit kosteneffizientem Langzeitspeicher zu erweitern.  

Das folgende Diagramm zeigt, wie Snapshot-Daten vom Azure NetApp Files-Volume zum Azure NetApp Files-Sicherungsspeicher übertragen werden, der auf Azure Storage gehostet wird.

![Diagramm, das die vom Azure NetApp Files-Volume an den Azure NetApp Files-Sicherungsspeicher übertragenen Snapshot-Daten zeigt](../media/azure-netapp-files/snapshot-data-transfer-backup-storage.png)

Die Backup-Funktionalität von Azure NetApp Files ist so konzipiert, dass eine längere Historie von Backups aufbewahrt wird, wie in diesem vereinfachten Beispiel dargestellt. Beachten Sie, dass das Backup-Repository auf der rechten Seite mehr und ältere Snapshots enthält als das geschützte Volume und die Snapshots auf der linken Seite. 

In den meisten Anwendungsfällen ist es erforderlich, dass Sie Online-Snapshots auf dem Azure NetApp Files-Volume für einen relativ kurzen Zeitraum (in der Regel mehrere Monate) aufbewahren, um die häufigsten Wiederherstellungen verlorener Daten aufgrund von Anwendungs- oder Benutzerfehlern zu ermöglichen. Die Azure NetApp Files Backup-Funktionalität wird verwendet, um den Zeitraum des Datenschutzes auf ein Jahr oder länger zu verlängern, indem die Snapshots auf kosteneffizienten Azure Storage übertragen werden. Wie durch die blaue Farbe im Diagramm angedeutet, ist die allererste Übertragung die Baseline, die alle verbrauchten Datenblöcke im Quellvolume von Azure NetApp Files und Snapshots kopiert. Bei nachfolgenden Backups wird der Snapshot-Mechanismus verwendet, um das Backup-Repository nur mit blockweisen Updates zu aktualisieren.

## <a name="ways-to-restore-data-from-snapshots"></a>Verfahren zum Wiederherstellen von Daten aus Momentaufnahmen  

Die Azure NetApp Files-Momentaufnahmetechnologie steigert die Häufigkeit und Zuverlässigkeit von Sicherungen erheblich. Gleichzeitig verursacht sie nur einen minimalen Leistungsmehraufwand und kann sicher auf einem aktiven Volume erstellt werden. Azure NetApp Files-Momentaufnahmen ermöglichen nahezu sofortige, sichere und optional vom Benutzer verwaltete Wiederherstellungen. In diesem Abschnitt werden verschiedene Methoden beschrieben, mit denen Daten aus Azure NetApp Files-Momentaufnahmen abgerufen oder wiederhergestellt werden können.

### <a name="restoring-files-or-directories-from-online-snapshots"></a>Wiederherstellung von Dateien oder Verzeichnissen aus Online-Snapshots

Wenn die Sichtbarkeit des [Snapshot-Pfads](snapshots-edit-hide-path.md) nicht auf `hidden` eingestellt ist, können Sie direkt auf Snapshots zugreifen, um versehentlich gelöschte, beschädigte oder veränderte Daten wiederherzustellen. Die Sicherheit von Dateien und Verzeichnissen wird in der Momentaufnahme beibehalten. Außerdem sind Momentaufnahmen immer schreibgeschützt. Daher ist die Wiederherstellung sicher und einfach. Wenn die Sichtbarkeit des Snapshot-Pfads auf `hidden` eingestellt ist, können Sie ein Support-Ticket öffnen, damit ein Backup-Administrator oder Systemadministrator Ihre Dateien aus einem Snapshot wiederherstellt.

Das folgende Diagramm zeigt den Zugriff auf Dateien oder Verzeichnisse in einer Momentaufnahme: 

![Diagramm des Zugriffs auf Dateien oder Verzeichnisse in einer Momentaufnahme](../media/azure-netapp-files/snapshot-file-directory-access.png)

Im Diagramm verwendet „Snapshot 1“ nur die Deltablöcke zwischen dem aktiven Volume und dem Zeitpunkt der Erstellung der Momentaufnahme. Wenn Sie jedoch auf die Volumemomentaufnahme über ihren Pfad zugreifen, werden die Daten so dargestellt, *als ob* es sich um die vollständige Volumekapazität zum Zeitpunkt der Erstellung der Momentaufnahme handelt. Durch Zugriff auf die Snapshot-Ordner können Sie Daten wiederherstellen, indem Sie Dateien und Verzeichnisse aus einem Snapshot Ihrer Wahl kopieren.

Ähnlich kann bei einer regionsübergreifenden Replikation zur Datenwiederherstellung in der Notfallwiederherstellungsregion schreibgeschützt auf Momentaufnahmen auf Zielvolumes zugegriffen werden.  

Das folgende Diagramm zeigt den Zugriff auf Momentaufnahmen in Szenarien mit regionsübergreifender Replikation: 

![Diagramm des Zugriffs auf Momentaufnahmen in Szenarien mit regionsübergreifender Replikation](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Informationen zum Wiederherstellen einzelner Dateien oder Verzeichnisse aus Momentaufnahmen finden Sie unter [Wiederherstellen einer Datei aus einer Momentaufnahme mithilfe eines Clients](snapshots-restore-file-client.md).

### <a name="restoring-cloning-an-online-snapshot-to-a-new-volume"></a>Wiederherstellung (Klonen) eines Online-Snapshots auf einem neuen Volume

Sie können Azure NetApp Files Snapshots auf separaten, unabhängigen Volumes (Klonen) wiederherstellen. Dieser Vorgang erfolgt unabhängig von der Volumegröße und der verbrauchten Kapazität nahezu unmittelbar. Auf das neu erstellte Volume kann fast sofort zugegriffen werden, während das eigentlichen Volume und die Datenblöcke aus der Momentaufnahme kopiert werden. Abhängig von der Volumegröße und der Kapazität kann dieser Vorgang eine beträchtliche Zeit in Anspruch nehmen, während der das übergeordnete Volume und die Momentaufnahme nicht gelöscht werden können. Auf das Volume kann jedoch bereits nach der anfänglichen Erstellung zugegriffen werden, während der Kopiervorgang im Hintergrund ausgeführt wird. Diese Funktion ermöglicht das schnelle Erstellen von Volumes für die Datenwiederherstellung sowie das Klonen von Volumes für Tests und die Entwicklung. Aufgrund der Art des Datenkopiervorgangs verdoppelt sich der Verbrauch des Speicherkapazitätspools nach Abschluss der Wiederherstellung, und das neue Volume hat die gesamte aktive Kapazität der ursprünglichen Momentaufnahme. Nach Abschluss dieses Vorgangs ist das Volume unabhängig und nicht mehr mit dem ursprünglichen Volume verbunden, und Quellvolumes und Snapshots können unabhängig vom neuen Volume verwaltet oder entfernt werden.

Das folgende Diagramm zeigt ein neues Volume, das durch Wiederherstellen einer Momentaufnahme (Klonen) erstellt wurde:   

![Diagramm eines neuen, durch Wiederherstellen einer Momentaufnahme erstellten Volumes](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Der gleiche Vorgang kann für replizierte Snapshots auf einem Disaster-Recovery (DR)-Volume durchgeführt werden. Jede Momentaufnahme kann auf einem neuen Volume wiederhergestellt werden, selbst wenn die regionsübergreifende Replikation aktiv ist oder fortgesetzt wird. Diese Funktion ermöglicht die unterbrechungsfreie Erstellung von Test- und Entwicklungsumgebungen in einer DR-Region, wobei die Daten genutzt werden, während die replizierten Volumes sonst nur für DR-Zwecke verwendet würden. Mit diesem Anwendungsfall können Test- und Entwicklungsumgebungen von der Produktion isoliert werden, wodurch mögliche Auswirkungen auf die Produktionsumgebungen vermieden werden.  

Das folgende Diagramm zeigt die Volumewiederherstellung (Klonen) mithilfe der Volumemomentaufnahme am Notfallwiederherstellungsziel, während eine regionsübergreifende Replikation stattfindet:  

![Diagramm der Volumewiederherstellung mithilfe der Volumemomentaufnahme am Notfallwiederherstellungsziel](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Weitere Informationen zu Wiederherstellungsvorgängen für Volumes finden Sie unter [Wiederherstellen einer Momentaufnahme auf einem neuen Volume](snapshots-restore-new-volume.md).

### <a name="restoring-reverting-an-online-snapshot-in-place"></a>Wiederherstellen eines Online-Snapshots (Rückgängigmachen)

Da ein neues Volume Speicherkapazität beansprucht, ist das Erstellen eines neuen Volumes aus einer Momentaufnahme in einigen Fällen möglicherweise nicht erforderlich oder nicht angemessen. Zur schnellen Wiederherstellung von beschädigten Daten (z. B. Datenbankbeschädigung oder Ransomware-Angriffe) kann es sinnvoller sein, einen Snapshot auf dem Volume selbst wiederherzustellen. Dieser Vorgang kann mit der Funktion Azure NetApp Files [snapshot revert](snapshots-revert-volume.md) durchgeführt werden. Mit dieser Funktion können Sie schnell ein Volume wieder in den Zustand zurückversetzen, in dem es sich befand, als eine bestimmte Momentaufnahme erstellt wurde. In den meisten Fällen erfolgt das Wiederherstellen eines Volumes wesentlich schneller als das Wiederherstellen einzelner Dateien aus einer Momentaufnahme in das aktive Dateisystem. Dies gilt insbesondere bei großen Volumes mit vielen TiB an Daten. 

Das Wiederherstellen einer Volumemomentaufnahme erfolgt nahezu unmittelbar und dauert selbst bei den größten Volumes nur wenige Sekunden. Die Metadaten des aktiven Volumes (*I-Knoten-Table*) werden durch die Metadaten der Momentaufnahme zum Zeitpunkt der Erstellung der Momentaufnahme ersetzt. Dadurch wird das Volume auf diesen Zeitpunkt zurückgesetzt. Für diese Wiederherstellung müssen keine Datenblöcke kopiert werden. Dies ist platzsparender und schneller als die Wiederherstellung eines Snapshots auf einem neuen Datenträger.

Das folgende Diagramm zeigt ein Volume, das auf eine ältere Momentaufnahme wiederhergestellt wird:  

![Diagramm der Wiederherstellung eines Volumes auf eine ältere Momentaufnahme](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Aktive Dateisystemdaten, die geschrieben wurden, und Snapshots, die nach dem ausgewählten Snapshot erstellt wurden, gehen verloren. Der Vorgang "Snapshot zurücksetzen" ersetzt alle Daten auf dem Zielvolume durch die Daten im ausgewählten Snapshot. Bei der Auswahl der Momentaufnahme sollten Sie auf die Inhalte und das Erstellungsdatum achten. Sie können den Vorgang zum Wiederherstellen der Momentaufnahme nicht zurücksetzen.  

Weitere Informationen zur Verwendung dieses Features finden Sie unter [Wiederherstellen eines Volumes mit „Momentaufnahme wiederherstellen“](snapshots-revert-volume.md).

### <a name="restoring-volume-backups-from-vaulted-snapshots"></a>Wiederherstellen von Volume-Backups aus ausgelagerten Snapshots

Sie können [auf Volume-Ebene oder auf NetApp Konto-Ebene nach Backups](backup-search.md) suchen. Die für Snapshots verwendeten Namen bleiben beim Sichern der Snapshots erhalten und enthalten das Präfix "täglich", "wöchentlich" oder "monatlich". Außerdem enthalten sie den Zeitstempel der Snapshot-Erstellung und das Datum. Der erste Snapshot, der bei Aktivierung der Sicherungsfunktion erstellt wird, wird als Basis-Snapshot bezeichnet. Der Baseline-Snapshot enthält alle Daten auf dem geschützten Volume und die Snapshots. Aufeinanderfolgende gesicherte Snapshots sind blockweise inkrementelle Updates, während Snapshots immer eine vollständige Darstellung des Datenträgers zum Zeitpunkt der Erstellung des gesicherten Snapshots sind und direkt wiederhergestellt werden können *ohne* die Notwendigkeit, die Baseline mit inkrementellen Updates zu stapeln. 

Das folgende Diagramm veranschaulicht den Vorgang der Wiederherstellung eines ausgewählten gesicherten Snapshots auf einem neuen Datenträger:  

![Diagramm, das die Wiederherstellung eines ausgewählten gesicherten Snapshots auf einem neuen Datenträger zeigt](../media/azure-netapp-files/snapshot-restore-vaulted-new-volume.png)

### <a name="restoring-individual-files-or-directories-from-vaulted-snapshots"></a>Wiederherstellen einzelner Dateien oder Verzeichnisse aus gesicherten Snapshots  

Um einzelne Dateien oder Verzeichnisse wiederherzustellen, wird der gesamte gesicherte Snapshot auf einem neuen Datenträger wiederhergestellt, der dann gemountet werden kann, um nach den wiederherzustellenden Dateien oder Verzeichnissen zu suchen. Die Wiederherstellung erfolgt durch Kopieren der erforderlichen Dateien oder Verzeichnisse von dem neu wiederhergestellten Volume an den Zielspeicherort. Wenn die Wiederherstellung abgeschlossen ist, kann das wiederhergestellte Volume gelöscht werden.  

Wenn ein Volume gelöscht wird, bleiben die im Tresor gespeicherten Snapshots (Backups) erhalten, im Gegensatz zu den Online-Snapshots, die Teil des Volumes sind und mit dem Löschen des Volumes gelöscht werden. Sie können komplette Volumes und anschließend einzelne Verzeichnisse aus gesicherten Backups wiederherstellen, selbst wenn das übergeordnete Volume aufgrund von Anwendungs- oder Benutzerfehlern gelöscht oder verloren wurde. Dazu wählen Sie den entsprechenden gesicherten Snapshot aus der Backup-Liste aus und stellen ihn auf einem neuen Datenträger wieder her. Siehe [Wiederherstellung eines Backups auf einem neuen Datenträger](backup-restore-new-volume.md) für weitere Informationen.


## <a name="how-snapshots-are-deleted"></a>Löschen von Momentaufnahmen  

In diesem Abschnitt wird erläutert, wie Online-Snapshots und gespeicherte Snapshots gelöscht werden.

### <a name="deleting-online-snapshots"></a>Löschen von Online-Snapshots 

Snapshots verbrauchen Speicherkapazität. Daher werden sie in der Regel nicht unbegrenzt aufbewahrt. Für den Schutz von Daten, die Aufbewahrung und die Wiederherstellbarkeit wird in der Regel eine bestimmte Anzahl von Momentaufnahmen (zu unterschiedlichen Zeitpunkten) für eine bestimmte Dauer online gehalten. Diese Dauer richtet sich nach den Anforderungen an RPO, RTO und Beibehaltung in der Vereinbarung zum Servicelevel (SLA). Ältere Momentaufnahmen müssen jedoch häufig nicht im Speicherdienst aufbewahrt werden und können gelöscht werden, um Speicherplatz freizugeben. Alle Momentaufnahmen können jederzeit von einem Administrator gelöscht werden (nicht notwendigerweise in der Reihenfolge ihrer Erstellung). 

> [!IMPORTANT]
> Das Löschen von Momentaufnahmen kann nicht rückgängig gemacht werden. Sie sollten Offline-Kopien (gesicherte Snapshots) des Volumes zu Zwecken des Datenschutzes und der Datenaufbewahrung aufbewahren. 

Beim Löschen einer Momentaufnahme werden alle Zeiger aus dieser Momentaufnahme auf vorhandene Datenblöcke entfernt. Erst wenn auf einen Datenblock keine Zeiger mehr zeigen (durch den aktiven Datenträger oder andere Snapshots im Datenträger), wird der Datenblock in den freien Speicher des Datenträgers zur zukünftigen Verwendung zurückgegeben. Daher wird durch das Entfernen von Momentaufnahmen in der Regel mehr Kapazität in einem Volume freigegeben als durch das Löschen von Daten aus dem aktiven Volume, da Datenblöcke häufig in zuvor erstellten Momentaufnahmen erfasst werden. 

Das folgende Diagramm zeigt die Auswirkung auf den Speicherverbrauch durch das Löschen von „Snapshot 3“ auf einem Volume:  

![Diagramm der Auswirkungen des Löschens einer Momentaufnahme auf den Speicherverbrauch](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Sie sollten den [Verbrauch durch Volumes und Momentaufnahmen überwachen](azure-netapp-files-metrics.md#volumes) und analysieren, um besser zu verstehen, wie Anwendung, aktives Volume und Momentaufnahmeverbrauch zusammenhängen. 

Weitere Informationen zum Verwalten der Löschung von Momentaufnahmen finden Sie unter [Löschen von Momentaufnahmen](snapshots-delete.md). Weitere Informationen zum Automatisieren dieses Vorgangs finden Sie unter [Verwalten von Momentaufnahmerichtlinien](snapshots-manage-policy.md).

### <a name="deleting-vaulted-snapshots"></a>Löschen von gesicherten Snapshots

Wenn Sie Backups für ein Volume deaktivieren, werden alle im Azure-Speicher für dieses Volume gespeicherten Vaulted Snapshots (Backups) gelöscht.

Wenn ein Volume gelöscht wird, aber die Backup-Richtlinie vor der Löschung des Volumes nicht deaktiviert wurde, bleiben alle Backups, die sich auf das Volume beziehen, im Azure-Storage erhalten und werden unter dem zugehörigen NetApp-Konto aufgeführt.

Weitere Informationen finden Sie unter [Deaktivieren der Backup-Funktionalität für ein Azure NetApp Files Volume](backup-disable.md). 

Die Historie von Vaulted Snapshots wird automatisch durch die angewandte Snapshot-Richtlinie verwaltet, wobei der älteste Snapshot gelöscht wird, wenn ein neuer durch den Vaulted Snapshot (Backup)-Scheduler hinzugefügt wird. Sie können gesicherte Snapshots auch manuell entfernen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Überwachen von Volume- und Momentaufnahmemetriken](azure-netapp-files-metrics.md#volumes)
* [Problembehandlung für Momentaufnahmenrichtlinien](troubleshoot-snapshot-policies.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen eines Backups auf einem neuen Datenträger](backup-restore-new-volume.md)
* [Sicherungsfunktionalität für einen Datenträger deaktivieren](backup-disable.md)
* [Sicherungen eines Datenträgers löschen](backup-delete.md)


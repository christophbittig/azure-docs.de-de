---
title: Konfigurieren des Clusterquorums
description: 'Hier erfahren Sie, wie Sie einen Datenträgerzeugen, Cloudzeugen oder Dateifreigabenzeugen als Quorum für einen Windows Server-Failovercluster auf SQL Server Azure-VMs konfigurieren. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 575e8a5ab43e38b723bd0b2084709a1216b477fe
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163436"
---
# <a name="configure-cluster-quorum-for-sql-server-on-azure-vms"></a>Konfigurieren des Clusterquorums für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie eine der drei Quorumoptionen für einen Windows Server-Failovercluster konfigurieren, der auf einem SQL Server auf Azure-VMs ausgeführt wird – ein Datenträgerzeuge, ein Cloudzeuge und ein Dateifreigabenzeuge.


## <a name="overview"></a>Übersicht

Das Quorum für einen Cluster wird durch die Anzahl der Abstimmungselemente (Stimmen) bestimmt, die Teil der aktiven Clustermitgliedschaft sein müssen, damit der Cluster ordnungsgemäß gestartet oder weiterhin ausgeführt werden kann. Durch das Konfigurieren einer Quorumressource kann ein Zwei-Knoten-Cluster mit nur einem Knoten online bleiben. Der Windows Server-Failovercluster ist die zugrunde liegende Technologie für die SQL Server auf Azure-VMs mit Hochverfügbarkeitsoptionen: [Failoverclusterinstanzen (FCIs)](failover-cluster-instance-overview.md) und [Verfügbarkeitsgruppen](availability-group-overview.md). 

Der Datenträgerzeuge ist die resilienteste Quorumoption. Um jedoch einen Datenträgerzeugen auf einem SQL Server auf einer Azure-VM zu verwenden, müssen Sie einen freigegebenen Azure-Datenträger verwenden, der einige Einschränkungen für die Hochverfügbarkeitslösung erzwingt. Verwenden Sie daher einen Datenträgerzeugen, wenn Sie Ihre Failoverclusterinstanz mit freigegebenen Azure-Datenträgern konfigurieren. Verwenden Sie andernfalls nach Möglichkeit einen Cloudzeugen. Wenn Sie Windows Server 2012 R2 oder eine ältere Version verwenden, die keinen Cloudzeugen unterstützt, können Sie einen Dateifreigabenzeugen verwenden. 

Die folgenden Quorumoptionen können für SQL Server auf Azure-VMs verwendet werden: 

|  |[Cloudzeuge](/windows-server/failover-clustering/deploy-cloud-witness) |[Datenträgerzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[Dateifreigabenzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Unterstütztes Betriebssystem**| Windows Server 2016+ |All | Alle|

Weitere Informationen zum Quorum finden Sie in der [Übersicht über Windows Server-Failovercluster](hadr-windows-server-failover-cluster-overview.md). 

## <a name="cloud-witness"></a>Cloudzeuge

Ein Cloudzeuge ist ein Quorumzeugentyp für Failovercluster, der Microsoft Azure zum Bereitstellen einer Stimme für das Clusterquorum verwendet. 


Die folgende Tabelle enthält zusätzliche Informationen und Überlegungen zu den Cloudzeugentypen. 

| Zeugentyp  | BESCHREIBUNG  | Anforderungen und Empfehlungen  |
| ---------    |---------        |---------                        |
| Cloudzeuge     |  <ul><li> Verwendet Azure Storage als Cloudzeugen und enthält nur den Zeitstempel. </li><li> Ideal für Bereitstellungen an mehreren Standorten, in mehreren Zonen und in mehreren Regionen.</li> <li> Erstellt unter dem Microsoft-Speicherkonto einen bekannten Container `msft-cloud-witness`. </li> <li> Schreibt eine einzelne Blobdatei und verwendet dabei die eindeutige ID des entsprechenden Clusters als Dateiname der Blobdatei im Container. </li>      |  <ul><li>Die Standardgröße beträgt 1 MB.</li><li> Verwenden Sie **Universell** als Kontoart. Blobspeicher wird nicht unterstützt. </li><li> Verwenden Sie Standardspeicher. Azure Storage Premium wird nicht unterstützt. </li><li> Beim Failoverclustering wird die Blobdatei als Vermittlungspunkt verwendet. Dies erfordert einige Konsistenzgarantien beim Lesen von Daten. Wählen Sie daher unter **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.</li><li> Sollte von Sicherungen und Antivirenscans ausgeschlossen werden</li><li> Ein Datenträgerzeuge wird von „Direkte Speicherplätze“ nicht unterstützt.</li> <li> Der Cloudzeuge verwendet HTTPS (Standardport 443) für die Kommunikation mit dem Azure-Blobdienst. Stellen Sie sicher, dass per Netzwerkproxy auf den HTTPS-Port zugegriffen werden kann. </li>|

Berücksichtigen Sie Folgendes, wenn Sie eine Quorumressource für Cloudzeugen für Ihren Failovercluster konfigurieren:
- Anstatt den Zugriffsschlüssel zu speichern, generiert Ihr Failovercluster ein SAS-Token (Shared Access Security) und speichert es sicher.
- Das generierte SAS-Token ist gültig, solange der Zugriffsschlüssel gültig bleibt. Beim Rotieren des primären Zugriffsschlüssels ist es wichtig, zuerst den Cloudzeugen (in allen Clustern, die dieses Speicherkonto verwenden) mit dem sekundären Zugriffsschlüssel zu aktualisieren, bevor der primäre Zugriffsschlüssel neu generiert wird.
- Der Cloudzeuge verwendet die HTTPS-REST-Schnittstelle des Azure Storage-Kontodiensts. Dies bedeutet, dass der HTTPS-Port auf allen Clusterknoten geöffnet sein muss.


Ein Cloudzeuge benötigt ein Azure Storage-Konto. Führen Sie diese Schritte aus, um ein Speicherkonto zu erstellen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Hubmenu Neu -> Daten und Speicher -> Speicherkonto.
3. Gehen Sie auf der Seite „Speicherkonto erstellen“ wie folgt vor:
    1. Geben Sie einen Namen für Ihr Speicherkonto ein. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Außerdem muss der Name des Speicherkontos innerhalb von Azure eindeutig sein.
    2. Wählen Sie unter **Kontoart** die Option **Universell** aus.
    3. Wählen Sie für **Leistung** die Option **Standard** aus.
    2. Wählen Sie unter **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.


Nachdem Ihr Speicherkonto erstellt wurde, führen Sie die folgenden Schritte aus, um die Quorumressource Ihres Cloudzeugen für Ihren Failovercluster zu konfigurieren: 


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Der vorhandene PowerShell-Befehl „Set-ClusterQuorum“ verfügt über neue Parameter, die dem Cloudzeugen entsprechen.

Sie können den Cloudzeugen mit dem Cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) konfigurieren, indem Sie den folgenden PowerShell-Befehl verwenden:

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey>
```

In seltenen Fällen müssen Sie einen anderen Endpunkt verwenden; nutzen Sie hierzu den folgenden PowerShell-Befehl: 

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey> -Endpoint <servername>
```

Weitere Informationen, wie Sie den Zugangsschlüssel des Speicherkontos finden, lesen Sie in der [Dokumentation zum Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness). 


# <a name="failover-cluster-manager"></a>[Failovercluster-Manager](#tab/fcm-gui)

Verwenden Sie den Quorumkonfigurations-Assistenten im Failovercluster-Manager, um Ihren Cloudzeugen zu konfigurieren. Gehen Sie dazu folgendermaßen vor: 

1. Öffne den Failovercluster-Manager

2. Klicken Sie mit der rechten Maustaste auf den Cluster -> **Weitere Aktionen** -> **Einstellungen für Clusterquorum konfigurieren**. Dies startet den Assistenten zum Konfigurieren des Clusterquorums.

    ![Momentaufnahme des Menüpfads zum Konfigurieren von Clusterquorumeinstellungen auf der Benutzeroberfläche des Failovercluster-Managers](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-7.png)
    
3. Wählen Sie auf der Seite **Quorumkonfiguration auswählen** die Option **Quorumzeugen auswählen**.

    ![Momentaufnahme des Optionsfelds „Quorumzeugen auswählen“ im Clusterquorum-Assistenten](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-8.png)
   
4. Wählen Sie auf der Seite **Quorumzeuge auswählen** die Option **Cloudzeugen konfigurieren**.

    ![Momentaufnahme des entsprechenden Optionsfelds zum Auswählen eines Cloudzeugen](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-9.png)
    
5. Geben Sie auf der Seite **Cloudzeugen konfigurieren** die Information für das Azure-Speicherkonto ein. Für Tipps, wo Sie diese Informationen finden, lesen Sie in der [Dokumentation zu Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness). 
   1. (Erforderlicher Parameter) Azure Storage Kontoname.
   2. (Erforderlicher Parameter) Zugriffsschlüssel, der dem Speicherkonto entspricht.
       1. Verwenden Sie beim erstmaligen Erstellen den primären Zugriffsschlüssel. 
       2. Verwenden Sie beim Rotieren des primären Zugriffsschlüssels den sekundären Zugriffsschlüssel.
   3. (Optionaler Parameter) Wenn Sie beabsichtigen, einen anderen Azure-Dienstendpunkt (z. B. den Microsoft Azure-Dienst in China) zu verwenden, aktualisieren Sie den Namen des Endpunktservers.

    ![Momentaufnahme des Konfigurationsbereichs des Cloudzeugen im Clusterquorum-Assistenten](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-10.png)
      

6. Nach erfolgreicher Konfiguration des Cloudzeugen können Sie die neu erstellte Zeugenressource im Failovercluster-Manager-Snap-In sehen.

    ![Erfolgreiche Konfiguration des Cloudzeugen](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-11.png)
    


---


## <a name="disk-witness"></a>Datenträgerzeuge

Ein Datenträgerzeuge ist ein kleiner Clusterdatenträger in der Gruppe „Verfügbarer Clusterspeicher“. Dieser Datenträger ist hochverfügbar und unterstützt ein Failover zwischen Knoten. 

Der Datenträgerzeuge ist die empfohlene Quorumoption, wenn er mit einer Hochverfügbarkeitslösung für freigegebenen Speicher verwendet wird, z. B. die Failoverclusterinstanz mit freigegebenen Azure-Datenträgern. 

Die folgende Tabelle enthält zusätzliche Informationen und Überlegungen zum Quorumdatenträgerzeugen. 

| Zeugentyp  | BESCHREIBUNG  | Anforderungen und Empfehlungen  |
| ---------    |---------        |---------                        |
| Datenträgerzeuge     |  <ul><li> Dedizierte LUN, die eine Kopie der Clusterdatenbank speichert</li><li> Besonders für Cluster mit freigegebenem (nicht repliziertem) Speicher geeignet</li>       |  <ul><li>Die Größe der LUN muss mindestens 512 MB betragen</li><li> Muss für die Clusterverwendung dediziert sein und darf keiner Clusterrolle zugewiesen werden</li><li> Muss in den Clusterspeicher einbezogen werden und die Speichervalidierungstests bestehen</li><li> Darf kein Datenträger sein, der ein CSV (freigegebenes Clustervolume) ist</li><li> Einfacher Datenträger mit einzelnem Volume</li><li> Laufwerksbuchstabe ist nicht erforderlich</li><li> Formatierung mit NTFS oder ReFS zulässig</li><li> Kann für die Fehlertoleranz optional mit Hardware-RAID konfiguriert werden</li><li> Sollte von Sicherungen und Antivirenscans ausgeschlossen werden</li><li> Ein Datenträgerzeuge wird von „Direkte Speicherplätze“ nicht unterstützt.</li>|

Um einen freigegebenen Azure-Datenträger für den Datenträgerzeugen zu verwenden, müssen Sie zuerst den Datenträger erstellen und einbinden. Führen Sie dazu die Schritte im Abschnitt [Datenträger einbinden](failover-cluster-instance-azure-shared-disks-manually-configure.md#add-azure-shared-disk) des Leitfadens „Failoverclusterinstanz für freigegebene Azure-Datenträger“ aus. Der Datenträger muss kein Premium-Datenträger sein. 

Nachdem Ihr Datenträger eingebunden wurde, fügen Sie ihn dem Clusterspeicher wie folgt hinzu: 

1. Öffne den Failovercluster-Manager 
1. Wählen Sie im linken Navigationsbereich unter **Speicher** die Option **Datenträger** aus. 
1. Wählen Sie im rechten Navigationsbereich unter **Aktionen** die Option **Datenträger hinzufügen** aus. 
1. Wählen Sie das freigegebene Azure-Laufwerk aus, das Sie gerade eingebunden haben, und notieren Sie sich den Namen, z. B. `Cluster Disk 3`. 

Nachdem Ihr Datenträger als Clusterspeicher hinzugefügt wurde, konfigurieren Sie ihn mithilfe von PowerShell als Datenträgerzeuge:  


Der vorhandene PowerShell-Befehl „Set-ClusterQuorum“ verfügt über neue Parameter, die dem Cloudzeugen entsprechen.

Verwenden Sie den Pfad für die Dateifreigabe als Parameter für den Datenträgerzeugen, wenn Sie das PowerShell-Cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) verwenden:

```PowerShell
Set-ClusterQuorum -NodeAndDiskMajority "Cluster Disk 3"
```

Sie können auch den Failovercluster-Manager verwenden. Führen Sie die gleichen Schritte wie für den Cloudzeugen aus, wählen Sie aber stattdessen den Datenträgerzeugen als Quorumoption aus. 


## <a name="file-share-witness"></a>Dateifreigabenzeuge

Ein Dateifreigabezeuge ist eine SMB-Dateifreigabe, die in der Regel auf einem Dateiserver mit Windows Server konfiguriert ist. Dieser Zeuge verwaltet Clusteringinformationen in einer Datei „witness.log“, speichert aber keine Kopie der Clusterdatenbank. In Azure können Sie eine Azure-Dateifreigabe auf einem separaten virtuellen Computer konfigurieren. 

Konfigurieren Sie einen Dateifreigabenzeugen, wenn ein Datenträgerzeuge oder Cloudzeuge in Ihrer Umgebung nicht verfügbar oder nicht unterstützt wird. 

Die folgende Tabelle enthält zusätzliche Informationen und Überlegungen zu den Quorumdateifreigabenzeugen. 

| Zeugentyp  | BESCHREIBUNG  | Anforderungen und Empfehlungen  |
| ---------    |---------        |---------                        |
| Dateifreigabenzeuge     | <ul><li>SMB-Dateifreigabe, die auf einem Dateiserver konfiguriert ist, der Windows Server ausführt</li><li> Speichert keine Kopie der Clusterdatenbank</li><li> Verwaltet Clusterinformationen nur in der Datei "witness.log"</li><li> Besonders für Cluster mit repliziertem Speicher für mehrere Standorte geeignet </li>       |  <ul><li>Es sind mindestens 5 MB freier Speicherplatz erforderlich</li><li> Muss dem einzelnen Cluster zugeteilt und nicht zum Speichern von Benutzer- oder Anwendungsdaten verwendet werden</li><li> Schreibberechtigungen müssen für das Computerobjekt für den Clusternamen aktiviert sein</li></ul><br>Nachfolgend sind weitere Überlegungen zu einem Dateiserver aufgeführt, der den Dateifreigabezeugen hostet:<ul><li>Ein einzelner Dateiserver mit Dateifreigabezeugen kann für mehrere Cluster konfiguriert werden.</li><li> Der Dateiserver muss sich an einem Standort befinden, der von der Clusterarbeitsauslastung getrennt ist. Dadurch haben alle Clusterstandorte bei einer Unterbrechung der Netzwerkkommunikation zwischen den Standorten dieselbe Chance, diesen Zwischenfall zu überstehen. Wenn sich der Dateiserver am gleichen Standort befindet, wird der Standort zum primären Standort. Dies ist dann der einzige Standort, der auf die Dateifreigabe zugreifen kann.</li><li> Der Dateiserver kann auf einem virtuellen Computer ausgeführt werden, wenn der virtuelle Computer nicht auf demselben Cluster gehostet wird, der den Dateifreigabezeugen verwendet.</li><li> Der Dateiserver kann auf einem separaten Failovercluster konfiguriert werden, um eine hohe Verfügbarkeit zu bieten. </li>      |

Nachdem Sie Ihre Dateifreigabe erstellt und die Berechtigungen ordnungsgemäß konfiguriert haben, müssen Sie die Dateifreigabe auf Ihren Clusterknoten einbinden. Zum Einbinden der Datenfreigabe führen Sie die gleichen allgemeinen Schritte aus, die in der Schrittanleitung zur Failoverclusterinstanz für Premium-Dateifreigaben im Abschnitt [Dateifreigabe einbinden](failover-cluster-instance-premium-file-share-manually-configure.md) beschrieben sind. 

Nachdem Ihre Dateifreigabe ordnungsgemäß konfiguriert und eingebunden wurde, verwenden Sie PowerShell, um die Dateifreigabe als Quorumzeugenressource hinzuzufügen: 

```powershell
Set-ClusterQuorum -FileShareWitness <UNC path to file share> -Credential $(Get-Credential)
```

Sie werden zur Eingabe eines Kontos und Kennworts für ein lokales Konto (für die Dateifreigabe) aufgefordert, das kein Administratorkonto ist und über vollständige Administratorrechte für die Freigabe verfügt.  Der Cluster hält den Namen und das Kennwort verschlüsselt und es kann niemand darauf zugreifen.

Sie können auch den Failovercluster-Manager verwenden. Führen Sie die gleichen Schritte wie für den Cloudzeugen aus, wählen Sie aber stattdessen den Dateifreigabenzeugen als Quorumoption aus. 

## <a name="change-quorum-voting"></a>Ändern der Quorumabstimmung


Es ist möglich, die Quorumabstimmung (Stimme) eines Knotens zu ändern, der an einem Windows Server-Failovercluster beteiligt ist. 

Wenn Sie die Einstellungen für die Knotenabstimmung (Stimme) ändern, befolgen Sie diese Richtlinien: 

| Richtlinien zur Quorumabstimmung |
|-|
| Beginnen Sie damit, dass jeder Knoten standardmäßig keine Stimme besitzt. Jeder Knoten sollte nur über eine Stimme verfügen, wenn es dafür eine explizite Begründung gibt.|
| Aktivieren Sie Stimmen für Clusterknoten, die das primäre Replikat einer Verfügbarkeitsgruppe hosten, oder für die bevorzugten Besitzer einer Failoverclusterinstanz. |
| Aktivieren Sie Stimmen für Besitzer des automatischen Failovers. Jeder Knoten, der nach einem automatischen Failover zu einem Host eines primären Replikats oder einer FCI werden kann, sollte eine Stimme haben. | 
| Wenn eine Verfügbarkeitsgruppe über mehrere sekundäre Replikate verfügt, aktivieren Sie nur Stimmen für die Replikate, die über ein automatisches Failover verfügen. | 
| Deaktivieren Sie die Stimmen für Knoten, die sich an sekundären Standorten für die Notfallwiederherstellung befinden. Knoten an sekundären Standorten sollten nicht zur Entscheidung beitragen, einen Cluster offline zu schalten, wenn mit dem primären Standort alles in Ordnung ist. | 
| Sie verfügen über eine ungerade Anzahl von Stimmen mit mindestens drei Quorumstimmen. Fügen Sie bei Bedarf einen [Quorumzeugen](hadr-cluster-quorum-configure-how-to.md) für eine zusätzliche Stimme in einem Cluster mit zwei Knoten hinzu. | 
| Bewerten Sie die Stimmenzuweisungen nach einem Failover neu. Es ist nicht wünschenswert, ein Failover in eine Clusterkonfiguration auszuführen, die kein fehlerfreies Quorum unterstützt. |




## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Always On-Verfügbarkeitsgruppen mit SQL Server auf Azure-VMs](availability-group-overview.md)
- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

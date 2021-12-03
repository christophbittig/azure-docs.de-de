---
title: Konfigurieren von Azure Load Balancer für einen VNN einer Failoverclusterinstanz
description: Erfahren Sie, wie Sie einen Azure Load Balancer zum Weiterleiten von Datenverkehr an den virtuellen Netzwerknamen (VNN) für Ihre Failoverclusterinstanz (FCI) mit SQL Server auf Azure-VMs für Hochverfügbarkeit und Notfallwiederherstellung (High Availability und Disaster Recovery, HADR) konfigurieren.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 0eea2233a2b6b59c92b49ca325378a95301fc962
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158850"
---
# <a name="configure-azure-load-balancer-for-an-fci-vnn"></a>Konfigurieren von Azure Load Balancer für einen FCI-VNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Erstellen Sie Ihre SQL Server-VMs in mehreren Subnetzen innerhalb des gleichen virtuellen Azure-Netzwerks, um keine Azure Load Balancer-Instanz für die Failover-Cluster-Instanz zu benötigen.

In Azure Virtual Machines wird für Cluster ein Lastenausgleich für eine IP-Adresse verwendet, die zu einem bestimmten Zeitpunkt auf einem Clusterknoten vorhanden sein muss. In dieser Lösung enthält der Load Balancer die IP-Adresse für den Namen des virtuellen Netzwerks (VNN), der von der Clusterressource in Azure verwendet wird. 

In diesem Artikel erfahren Sie, wie Sie Lastenausgleich mit dem Azure Load Balancer-Dienst konfigurieren. Der Lastenausgleich leitet Datenverkehr an Ihre [Failoverclusterinstanz (FCI)](failover-cluster-instance-overview.md) mit SQL Server auf Azure-VMs weiter und sorgt für Hochverfügbarkeit und Notfallwiederherstellung (High Availability und Disaster Recovery, HADR). 

Wenn Sie eine andere Konnektivitätsoption für SQL Server 2019 CU2 und höher verwenden möchten, sollten Sie stattdessen einen [verteilten Netzwerknamen](failover-cluster-instance-distributed-network-name-dnn-configure.md) in Erwägung ziehen. Dieser bietet eine einfachere Konfiguration und besseres Failover. 


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Schritte ausführen, sollten Sie über Folgendes verfügen:

- Sie sollten festgelegt haben, dass Azure Load Balancer die geeignete [Konnektivitätsoption für Ihre Failoverclusterinstanz](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) ist.
- Konfigurierte [Failoverclusterinstanzen](failover-cluster-instance-overview.md). 
- Sie müssen die neueste Version von [PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows) installiert haben. 

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Sie können entweder einen internen Lastenausgleich oder einen externen Lastenausgleich erstellen. Ein interner Lastenausgleich kann nur von privaten Ressourcen aus erfolgen, die sich innerhalb des Netzwerks befinden.  Ein externer Lastenausgleich kann den Datenverkehr von öffentlichen Ressourcen zu internen Ressourcen leiten. Wenn Sie einen internen Lastenausgleich konfigurieren, verwenden Sie bei der Konfiguration der Regeln für den Lastenausgleich dieselbe IP-Adresse wie die FCI-Ressource für die Front-End-IP-Adresse. Wenn Sie einen externen Lastenausgleich konfigurieren, können Sie nicht dieselbe IP-Adresse verwenden, da es sich bei der FCI-IP-Adresse nicht um eine öffentliche IP-Adresse handeln darf. Um also einen externen Lastenausgleich zu verwenden, weisen Sie logischerweise eine IP-Adresse im gleichen Subnetz wie die FCI zu, die mit keiner anderen IP-Adresse in Konflikt steht, und verwenden Sie diese Adresse als Front-End-IP-Adresse für die Lastenausgleichsregeln. 


Verwenden Sie das [Azure-Portal](https://portal.azure.com) zum Erstellen des Lastenausgleichs:

1. Navigieren Sie im Azure-Portal zu der Ressourcengruppe mit den virtuellen Computern.

1. Wählen Sie **Hinzufügen**. Durchsuchen Sie Azure Marketplace nach **Load Balancer**. Wählen Sie **Load Balancer** aus.

1. Klicken Sie auf **Erstellen**.

1. Richten Sie den Lastenausgleich mit den folgenden Werten ein:

   - **Abonnement**: Ihr Azure-Abonnement.
   - **Ressourcengruppe**: Die Ressourcengruppe, die Ihre virtuellen Computer enthält.
   - **Name**: Ein Name, mit dem der Lastenausgleich identifiziert wird.
   - **Region**: Der Azure-Standort, der Ihre virtuellen Computer enthält.
   - **Typ:** Öffentlich oder privat. Der Zugriff auf einen privaten Lastenausgleich ist innerhalb des virtuellen Netzwerks möglich. Für die meisten Azure-Anwendungen kann ein privater Lastenausgleich verwendet werden. Verwenden Sie einen öffentlichen Lastenausgleich, wenn Ihre Anwendung direkten Zugriff auf SQL Server über das Internet benötigt.
   - **SKU**: Standard.
   - **Virtuelles Netzwerk:** Dies ist dasselbe Netzwerk wie für die virtuellen Computer.
   - **IP-Adresszuweisung**: Statisch. 
   - **Private IP-Adresse**: Die IP-Adresse, die Sie der Clusternetzwerkressource zugewiesen haben.

   Die folgende Abbildung zeigt die Benutzeroberfläche zum **Erstellen des Lastenausgleichs**:

   ![Einrichten des Lastenausgleichs](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Konfigurieren des Back-End-Pools

1. Wechseln Sie zurück zur Azure-Ressourcengruppe mit den virtuellen Computern, und suchen Sie nach dem neuen Lastenausgleich. Möglicherweise müssen Sie die Ansicht in der Ressourcengruppe aktualisieren. Wählen Sie den Load Balancer aus.

1. Wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.

1. Ordnen Sie den Back-End-Pool der Verfügbarkeitsgruppe mit den virtuellen Computern zu.

1. Aktivieren Sie unter **Zielnetzwerk-IP-Konfigurationen** die Option **VIRTUELLER COMPUTER**, und wählen Sie die virtuellen Computer aus, die als Clusterknoten eingeschlossen werden. Schließen Sie dabei alle virtuellen Computer ein, die die FCI hosten. Fügen Sie nur die primäre IP-Adresse der einzelnen VMs hinzu, aber keine sekundären IP-Adressen. 

1. Wählen Sie **OK** aus, um den Back-End-Pool zu erstellen.

## <a name="configure-health-probe"></a>Konfigurieren des Integritätstests

1. Wählen Sie im Bereich für den Lastenausgleich **Integritätstests** aus.

1. Wählen Sie **Hinzufügen**.

1. Legen Sie im Bereich **Integritätstest hinzufügen** <span id="probe"></span>die folgenden Parameter für den Integritätstest fest:

   - **Name**: Ein Name für den Integritätstest.
   - **Protokoll:** TCP.
   - **Port:** Der Port, den Sie [beim Vorbereiten der VM](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1) in der Firewall für den Integritätstest erstellt haben. Im Beispiel dieses Artikels wird der TCP-Port `59999` verwendet.
   - **Intervall**: 5 Sekunden.
   - **Fehlerschwellenwert**: Zwei aufeinanderfolgende Fehler.

1. Klicken Sie auf **OK**.

## <a name="set-load-balancing-rules"></a>Festlegen von Lastenausgleichsregeln

Legen Sie die Lastenausgleichsregeln für den Lastenausgleich fest. 


# <a name="private-load-balancer"></a>[Privater Lastenausgleich](#tab/ilb)

Legen Sie die Lastenausgleichsregeln für den privaten Lastenausgleich wie folgt fest: 

1. Wählen Sie im Bereich für den Lastenausgleich **Lastenausgleichsregeln** aus.
1. Wählen Sie **Hinzufügen**.
1. Legen Sie die Parameter für die Lastenausgleichsregeln fest:

   - **Name**: Ein Name für die Lastenausgleichsregeln.
   - **Front-End-IP-Adresse**: Die IP-Adresse für die Clusternetzwerkressource der SQL Server-FCI.
   - **Port:** Der SQL Server-TCP-Port. Der Standardport der Instanz lautet 1433.
   - **Back-End-Port**: Der gleiche Port, den Sie als Wert für **Port** angeben, wenn Sie **Floating IP (Direct Server Return)** aktivieren.
   - **Back-End-Pool**: Der Name des Back-End-Pools, den Sie zuvor konfiguriert haben.
   - **Integritätstest**: Der Integritätstest, den Sie zuvor konfiguriert haben.
   - **Sitzungspersistenz**: Keine.
   - **Leerlaufzeitüberschreitung (Minuten)** : 4.
   - **Floating IP (Direct Server Return)** : Aktiviert.

1. Klicken Sie auf **OK**.

# <a name="public-load-balancer"></a>[Öffentlicher Lastenausgleich](#tab/elb)

Legen Sie die Lastenausgleichsregeln für den öffentlichen Lastenausgleich wie folgt fest: 

1. Wählen Sie im Bereich für den Lastenausgleich **Lastenausgleichsregeln** aus.
1. Wählen Sie **Hinzufügen**.
1. Legen Sie die Parameter für die Lastenausgleichsregeln fest:

   - **Name**: Ein Name für die Lastenausgleichsregeln.
   - **Front-End-IP-Adresse**: Die öffentliche IP-Adresse, die Clients zum Herstellen einer Verbindung mit dem öffentlichen Endpunkt verwenden. 
   - **Port:** Der SQL Server-TCP-Port. Der Standardport der Instanz lautet 1433.
   - **Back-End-Port**: Der von der FCI-Instanz verwendete Port. Der Standardwert ist "1433". 
   - **Back-End-Pool**: Der Name des Back-End-Pools, den Sie zuvor konfiguriert haben.
   - **Integritätstest**: Der Integritätstest, den Sie zuvor konfiguriert haben.
   - **Sitzungspersistenz**: Keine.
   - **Leerlaufzeitüberschreitung (Minuten)** : 4.
   - **Floating IP (Direct Server Return)** : Deaktiviert.

1. Klicken Sie auf **OK**.

---



## <a name="configure-cluster-probe"></a>Konfigurieren des Clustertests

Legen Sie den Parameter für den Clustertestport in PowerShell fest.

# <a name="private-load-balancer"></a>[Privater Lastenausgleich](#tab/ilb)

Aktualisieren Sie die Variablen im folgenden Skript mit Werten aus Ihrer Umgebung, um den Parameter für den Clustertestport festzulegen. Entfernen Sie die spitzen Klammern (`<` und `>`) aus dem Skript.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

Die Werte, die Sie aktualisieren können, werden in der folgenden Tabelle beschrieben:


|**Wert**|**Beschreibung**|
|---------|---------|
|`Cluster Network Name`| Der Name des Windows Server-Failoverclusters für das Netzwerk. Klicken Sie in **Failovercluster-Manager** > **Netzwerke** mit der rechten Maustaste auf das Netzwerk, und wählen Sie **Eigenschaften** aus. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**.|
|`SQL Server FCI IP Address Resource Name`|Der Ressourcenname für die IP-Adresse der SQL Server-FCI. Klicken Sie in **Failovercluster-Manager** > **Rollen** unter der Rolle „SQL Server-FCI“ unter **Servername** mit der rechten Maustaste auf die IP-Adressressource, und wählen Sie dann **Eigenschaften** aus. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**.|
|`ILBIP`|Die IP-Adresse des internen Load Balancers (ILB). Diese Adresse wird als Front-End-Adresse des ILB im Azure-Portal konfiguriert. Dies ist auch die FCI-IP-Adresse von SQL Server. Sie finden sie im **Failovercluster-Manager** auf der gleichen Eigenschaftenseite, auf der sich der `<SQL Server FCI/AG listener IP Address Resource Name>` befindet.|
|`nnnnn`|Der Testport, den Sie im Integritätstest des Lastenausgleichs konfiguriert haben. Alle nicht verwendeten TCP-Ports sind zulässig.|
|„SubnetMask“| Die Subnetzmaske für den Clusterparameter. Dabei muss es sich um die TCP/IP-Broadcastadresse handeln: `255.255.255.255`.| 


Nach dem Festlegen des Clustertests werden in PowerShell alle Clusterparameter angezeigt. Führen Sie dieses Skript aus:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

# <a name="public-load-balancer"></a>[Öffentlicher Lastenausgleich](#tab/elb)

Aktualisieren Sie die Variablen im folgenden Skript mit Werten aus Ihrer Umgebung, um den Parameter für den Clustertestport festzulegen. Entfernen Sie die spitzen Klammern (`<` und `>`) aus dem Skript.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
$ELBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ELBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

Die Werte, die Sie aktualisieren können, werden in der folgenden Tabelle beschrieben:


|**Wert**|**Beschreibung**|
|---------|---------|
|`Cluster Network Name`| Der Name des Windows Server-Failoverclusters für das Netzwerk. Klicken Sie in **Failovercluster-Manager** > **Netzwerke** mit der rechten Maustaste auf das Netzwerk, und wählen Sie **Eigenschaften** aus. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**.|
|`SQL Server FCI IP Address Resource Name`|Der Ressourcenname für die IP-Adresse der SQL Server-FCI. Klicken Sie in **Failovercluster-Manager** > **Rollen** unter der Rolle „SQL Server-FCI“ unter **Servername** mit der rechten Maustaste auf die IP-Adressressource, und wählen Sie dann **Eigenschaften** aus. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**.|
|`ELBIP`|Die IP-Adresse des externen Lastenausgleichs (ELB). Diese Adresse wird im Azure-Portal als Front-End-Adresse des externen Lastenausgleichs konfiguriert und wird verwendet, um eine Verbindung mit dem öffentlichen Lastenausgleich von externen Ressourcen aus herzustellen. |
|`nnnnn`|Der Testport, den Sie im Integritätstest des Lastenausgleichs konfiguriert haben. Alle nicht verwendeten TCP-Ports sind zulässig.|
|„SubnetMask“| Die Subnetzmaske für den Clusterparameter. Dabei muss es sich um die TCP/IP-Broadcastadresse handeln: `255.255.255.255`.| 

Nach dem Festlegen des Clustertests werden in PowerShell alle Clusterparameter angezeigt. Führen Sie dieses Skript aus:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

> [!NOTE]
> Da es keine private IP-Adresse für den externen Lastenausgleich gibt, können die Benutzer den VNN-DNS-Namen nicht direkt verwenden, da er die IP-Adresse innerhalb des Subnetzes auflöst. Verwenden Sie entweder die öffentliche IP-Adresse des öffentlichen Lastenausgleichs, oder konfigurieren Sie eine andere DNS-Zuordnung auf dem DNS-Server. 

---

## <a name="modify-connection-string"></a>Ändern der Verbindungszeichenfolge 

Fügen Sie für Clients, die dies unterstützen, der Verbindungszeichenfolge `MultiSubnetFailover=True` hinzu.  Die MultiSubnetFailover-Verbindungsoption ist zwar nicht erforderlich, bietet jedoch den Vorteil eines schnelleren Subnetzfailovers. Das liegt daran, dass der Clienttreiber versucht, parallel ein TCP-Socket für alle IP-Adressen zu öffnen. Der Clienttreiber wartet, bis die erste IP erfolgreich antwortet, und verwendet diese dann für die Verbindung.

Wenn Ihr Client den MultiSubnetFailover-Parameter nicht unterstützt, können Sie die Einstellungen RegisterAllProvidersIP und HostRecordTTL ändern, um Konnektivitätsverzögerungen bei einem Failover zu vermeiden. 

Verwenden Sie PowerShell, um die Einstellungen RegisterAllProvidersIp und HostRecordTTL zu ändern: 

```powershell
Get-ClusterResource yourFCIname | Set-ClusterParameter RegisterAllProvidersIP 0  
Get-ClusterResource yourFCIname | Set-ClusterParameter HostRecordTTL 300 
```

Weitere Informationen finden Sie in der SQL Server-Dokumentation [Listenerverbindungstimeout](/troubleshoot/sql/availability-groups/listener-connection-times-out). 

> [!TIP]
> - Legen Sie den MultiSubnetFailover-Parameter in der Verbindungszeichenfolge auf true fest, auch für HADR-Lösungen, die sich über ein einzelnes Subnetz erstrecken, um die zukünftige Spanne von Subnetzen zu unterstützen, ohne dass Verbindungszeichenfolgen aktualisiert werden müssen.  
> - Standardmäßig werden von Clients DNS-Clustereinträge 20 Minuten zwischengespeichert. Durch das Reduzieren von HostRecordTTL reduzieren Sie die Gültigkeitsdauer (Time to Live, TTL) für den zwischengespeicherten Eintrag. Legacyclients können schneller wieder Verbindungen herstellen. Das Reduzieren der Einstellung HostRecordTTL kann zu größerem Datenverkehr an die DNS-Server führen.


## <a name="test-failover"></a>Testfailover


Testen Sie das Failover der Clusterressource, um die Clusterfunktionalität zu validieren. 

Führen Sie die folgenden Schritte aus:

1. Stellen Sie mithilfe von RDP eine Verbindung mit einem der SQL Server-Clusterknoten her.
1. Öffnen Sie den **Failovercluster-Manager**. Wählen Sie **Rollen** aus. Achten Sie darauf, welcher Knoten im Besitz der SQL Server-FCI-Rolle ist.
1. Klicken Sie mit der rechten Maustaste auf die SQL Server-FCI-Rolle. 
1. Wählen Sie **Verschieben** aus, und wählen Sie dann **Bestmöglicher Knoten** aus.

Unter **Failovercluster-Manager** wird die Rolle angezeigt, und die Ressourcen werden in den Offlinezustand versetzt. Die Ressourcen werden dann verschoben und auf dem anderen Knoten dann wieder in den Onlinezustand versetzt.


## <a name="test-connectivity"></a>Testen der Konnektivität

Melden Sie sich zum Testen der Konnektivität an einem anderen virtuellen Computer in demselben virtuellen Netzwerk an. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit dem SQL Server-FCI-Namen her. 

> [!NOTE]
> Bei Bedarf können Sie [SQL Server Management Studio herunterladen](/sql/ssms/download-sql-server-management-studio-ssms).





## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Failoverclusterinstanzen mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md)
- [AlwaysOn-Failoverclusterinstanzen (SQL Server)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)





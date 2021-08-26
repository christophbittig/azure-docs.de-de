---
title: Einrichten einer Always On-Verfügbarkeitsgruppe mit DH2i DxEnterprise auf Linux-basierten virtuellen Azure-Computern
description: Hier erfahren Sie, wie Sie DH2i DxEnterprise als Cluster-Manager verwenden, um Hochverfügbarkeit mit einer Verfügbarkeitsgruppe auf Linux-basierten virtuellen Azure-Computern zu erzielen.
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 8791d845b822f365e47482b0215f1ecac9fa0da9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732017"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Tutorial: Einrichten einer Always On-Verfügbarkeitsgruppe mit drei Knoten unter Verwendung von DH2i DxEnterprise auf Linux-basierten virtuellen Azure-Computern

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Tutorial erfahren Sie, wie Sie eine SQL Server-Always On-Verfügbarkeitsgruppe mit DH2i DxEnterprise auf Linux-basierten virtuellen Azure-Computern (virtual machines, VMs) konfigurieren. 

Weitere Informationen zu DH2i DxEnterprise finden Sie unter [DxEnterprise für Verfügbarkeitsgruppen](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft unterstützt die Datenverschiebung, die Verfügbarkeitsgruppe und die SQL Server-Komponente. Wenden Sie sich an DH2i, um Unterstützung im Zusammenhang mit der Dokumentation des DH2i DxEnterprise-Clusters für die Cluster- und Quorumverwaltung zu erhalten.
 

Dieses Tutorial besteht aus den folgenden Schritten:

> [!div class="checklist"]
> * Installieren von SQL Server auf allen virtuellen Azure-Computern, die Teil der Verfügbarkeitsgruppe sein sollen
> * Installieren von DxEnterprise auf allen virtuellen Computern und Konfigurieren des DxEnterprise-Clusters
> * Erstellen der virtuellen Hosts für Failoverunterstützung und Hochverfügbarkeit, Hinzufügen einer Verfügbarkeitsgruppe und Hinzufügen einer Datenbank zur Verfügbarkeitsgruppe
> * Erstellen der internen Azure Load Balancer-Instanz für den Verfügbarkeitsgruppenlistener (optional)
> * Durchführen eines manuellen oder automatischen Failovers

In diesem Tutorial wird ein DxEnterprise-Cluster über die [DxAdmin-Clientbenutzeroberfläche](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/) eingerichtet. Der Cluster kann optional auch über die Befehlszeilenschnittstelle [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) eingerichtet werden. In diesem Beispiel werden vier virtuelle Computer verwendet. Drei dieser virtuellen Computer verfügen über Ubuntu 18.04 und sind Teil des Clusters mit drei Knoten. Der vierte virtuelle Computer verfügt über Windows 10 mit dem DxAdmin-Tool zum Verwalten und Konfigurieren des Clusters.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vier virtuelle Computer in Azure. Gehen Sie zum Erstellen der Linux-basierten virtuellen Computer wie unter [Schnellstart: Erstellen eines virtuellen Linux-Computers im Azure-Portal](../../../virtual-machines/linux/quick-create-portal.md) beschrieben vor. Verwenden Sie zum Erstellen des Windows-basierten virtuellen Computers den Artikel [Schnellstart: Erstellen einer virtuellen Windows-Maschine im Azure-Portal](../../../virtual-machines/windows/quick-create-portal.md).
- Installieren Sie .NET 3.1 auf allen Linux-basierten virtuellen Computern, die Teil des Clusters sein sollen. Führen Sie dazu die [hier](/dotnet/core/install/linux) beschriebenen Schritte für das von Ihnen verwendete Linux-Betriebssystem aus.
- Sie benötigen eine gültige DxEnterprise-Lizenz mit aktivierten Features für die Verfügbarkeitsgruppenverwaltung. Weitere Informationen zur kostenlosen Testversion von DxEnterprise finden Sie [hier](https://dh2i.com/trial/).

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Installieren von SQL Server auf allen virtuellen Azure-Computern, die Teil der Verfügbarkeitsgruppe sein sollen

In diesem Tutorial wird ein Linux-basierter Cluster mit drei Knoten zum Ausführen der Verfügbarkeitsgruppe eingerichtet. Führen Sie die unter [Installieren](/sql/linux/sql-server-linux-overview#install) beschriebenen Schritte für die von Ihnen verwendete Linux-Plattform aus. Darüber hinaus empfiehlt es sich, für dieses Tutorial die [SQL Server-Tools](/sql/linux/sql-server-linux-setup-tools) zu installieren.
 
> [!NOTE]
> Bei dem von Ihnen verwendeten Linux-Betriebssystem muss es sich um eine gängige Distribution handeln, die sowohl von [DH2i DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) (siehe Abschnitt mit den Mindestanforderungen) als auch von [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms) unterstützt wird.
>
> In diesem Beispiel wird Ubuntu 18.04 verwendet. Diese Distribution wird sowohl von DH2i DxEnterprise als auch von Microsoft SQL Server unterstützt.

SQL Server wird im Rahmen dieses Tutorials nicht auf dem virtuellen Windows-Computer installiert, da dieser Knoten nicht Teil des Clusters ist und nur für die Clusterverwaltung mithilfe von DxAdmin verwendet wird.

Nach diesem Schritt sollten SQL Server und optional auch die [SQL Server-Tools](/sql/linux/sql-server-linux-setup-tools) auf allen drei Linux-basierten virtuellen Computern installiert sein, die der Verfügbarkeitsgruppe angehören sollen.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Installieren von DxEnterprise auf allen virtuellen Computern und Konfigurieren des Clusters

In diesem Schritt wird DH2i DxEnterprise für Linux auf den drei virtuellen Linux-Computern installiert. In der folgenden Tabelle werden die Rollen der einzelnen Server im Cluster beschrieben:

| Number of VMs (Anzahl von VMs) | DH2i DxEnterprise-Rolle | Replikatrolle der Microsoft SQL Server-Verfügbarkeitsgruppe |
|--|--|--|
| 1 | Clusterknoten: Linux-basiert | Primär |
| 1 | Clusterknoten: Linux-basiert | Sekundär: Synchroner Commit |
| 1 | Clusterknoten: Linux-basiert | Sekundär: Synchroner Commit |
| 1 | DxAdmin-Client | Nicht verfügbar |


Installieren Sie DxEnterprise auf den drei Linux-basierten Knoten. Verwenden Sie dazu die in der DH2i DxEnterprise-Dokumentation beschriebene Vorgehensweise für das von Ihnen verwendete Linux-Betriebssystem. Für die Installation von DxEnterprise stehen folgende Methoden zur Verfügung:

- **Ubuntu**
    - [Schnellstarthandbuch für die Repository-Installation](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Schnellstarthandbuch für die Erweiterung](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Schnellstarthandbuch für das Marketplace-Image](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Schnellstarthandbuch für die Repository-Installation](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Schnellstarthandbuch für die Erweiterung](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Schnellstarthandbuch für das Marketplace-Image](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Informationen zum Installieren des DxAdmin-Clienttools auf dem virtuellen Windows-Computer finden Sie im [Schnellstarthandbuch für die DxAdmin-Clientbenutzeroberfläche](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Nach diesem Schritt sollte der DxEnterprise-Cluster auf den virtuellen Linux-Computern erstellt und der DxAdmin-Client auf dem Windows-Clientcomputer installiert sein. 

> [!NOTE]
> Sie können auch einen Cluster mit drei Knoten erstellen, bei dem einer der Knoten wie [hier](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) beschrieben im *reinen Konfigurationsmodus* hinzugefügt wird, um automatische Failover zu ermöglichen. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Erstellen der virtuellen Hosts für Failoverunterstützung und Hochverfügbarkeit

In diesem Schritt wird die DxAdmin-Benutzeroberfläche verwendet, um einen virtuellen Host und eine Verfügbarkeitsgruppe zu erstellen und eine Datenbank hinzuzufügen.   

> [!NOTE]
> Im Rahmen dieses Schritts werden die SQL Server Instanzen neu gestartet, um Always On zu aktivieren. 

Stellen Sie eine Verbindung mit dem Windows-Clientcomputer her, auf dem DxAdmin ausgeführt wird, um eine Verbindung mit dem im obigen Schritt erstellten Cluster herzustellen. Gehen Sie wie unter [MSSQL-Verfügbarkeitsgruppen mit DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) beschrieben vor, um Always On zu aktivieren und den virtuellen Host und die Verfügbarkeitsgruppe zu erstellen. 

> [!TIP]
> Vergewissern Sie sich vor dem Hinzufügen der Datenbanken, dass die Datenbank in der primären Instanz von SQL Server erstellt und gesichert wird.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Erstellen der internen Azure Load Balancer-Instanz für den Listener (optional)

In diesem optionalen Schritt können Sie die Azure Load Balancer-Instanz erstellen und konfigurieren, die die IP-Adressen für die Verfügbarkeitsgruppenlistener enthält. Weitere Informationen zu Azure Load Balancer finden Sie unter [Was versteht man unter Azure Load Balancer?](../../../load-balancer/load-balancer-overview.md). Eine Anleitung zum Konfigurieren der Azure Load Balancer-Instanz und des Verfügbarkeitsgruppenlisteners mithilfe von DxAdmin finden Sie im [Azure Load Balancer-Schnellstarthandbuch](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/) für DxEnterprise.

Nach diesem Schritt sollten Sie einen Verfügbarkeitsgruppenlistener erstellt und der internen Azure Load Balancer-Instanz zugeordnet haben.

## <a name="test-manual-or-automatic-failover"></a>Testen des manuellen oder automatischen Failovers

Zum Testen des automatischen Failovers können Sie das primäre Replikat herunterfahren, indem Sie den virtuellen Computer über das Azure-Portal ausschalten. Dadurch wird die plötzliche Nichtverfügbarkeit des primären Knotens simuliert. Das erwartete Verhalten sieht wie folgt aus:
- Der Cluster-Manager stuft eines der sekundären Replikate in der Verfügbarkeitsgruppe auf „Primär“ hoch.
- Das fehlgeschlagene primäre Replikat wird automatisch dem Cluster beitreten, nachdem es gesichert wurde. Der Cluster-Manager stuft ihn auf das sekundäre Replikat hoch.

 
Alternativ können Sie auch wie folgt ein manuelles Failover durchführen:

1. Stellen Sie über DxAdmin eine Verbindung mit dem Cluster her.   
1. Erweitern Sie den virtuellen Host für die Verfügbarkeitsgruppe.
1. Klicken Sie mit der rechten Maustaste auf den Zielknoten/das sekundäre Replikat, und wählen Sie **Start Hosting on Member** (Hosten auf Mitglied starten), um das Failover zu initiieren. 

Informationen zu weiteren Vorgängen in DxEnterprise finden Sie im [Administratorhandbuch für DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) sowie im [DxCLI-Handbuch für DxEnterprise](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/).

## <a name="next-steps"></a>Nächste Schritte

- [Always On-Verfügbarkeitsgruppen unter Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Schnellstart: Erstellen eines virtuellen Linux-Computers im Azure-Portal](../../../virtual-machines/linux/quick-create-portal.md)
- [Schnellstart: Erstellen einer virtuellen Windows-Maschine im Azure-Portal](../../../virtual-machines/windows/quick-create-portal.md)
- [Unterstützte Plattformen](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)
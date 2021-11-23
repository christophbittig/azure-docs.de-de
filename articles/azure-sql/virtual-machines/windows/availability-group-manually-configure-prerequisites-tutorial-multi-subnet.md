---
title: 'Tutorial: Voraussetzungen für Verfügbarkeitsgruppen in mehreren Subnetzen'
description: 'In diesem Tutorial erfahren Sie, wie Sie die Voraussetzungen für die Erstellung einer Always On-Verfügbarkeitsgruppe in mehreren Subnetzen für SQL Server auf virtuellen Azure-Computern (Virtual Machines, VMs) konfigurieren. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 4d1ec223a745f49518a7936bc85d40f470739c80
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159908"
---
# <a name="tutorial-prerequisites-for-availability-groups-in-multiple-subnets-sql-server-on-azure-vms"></a>Tutorial: Voraussetzungen für Verfügbarkeitsgruppen in mehreren Subnetzen (SQL Server auf virtuellen Azure-Computern) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Erstellen Sie Ihre SQL Server-VMs in mehreren Subnetzen innerhalb des gleichen virtuellen Azure-Netzwerks, um weder eine Azure Load Balancer-Instanz noch einen DNN (Distributed Network Name, Name des verteilten Netzwerks) für Ihre Always On-Verfügbarkeitsgruppe zu benötigen.

In diesem Tutorial werden die erforderlichen Komponenten für die Erstellung einer [Always On-Verfügbarkeitsgruppe für SQL Server auf virtuellen Azure-Computern (Virtual Machines, VMs) in mehreren Subnetzen](availability-group-manually-configure-tutorial-multi-subnet.md) eingerichtet. Am Ende dieses Tutorials verfügen Sie über einen Domänencontroller auf zwei virtuellen Azure-Computern, zwei SQL Server-VMs in mehreren Subnetzen und ein Speicherkonto in einer einzelnen Ressourcengruppe. 

**Voraussichtliche Dauer:** In diesem Tutorial werden mehrere Ressourcen in Azure erstellt. Dies kann bis zu 30 Minuten dauern. 

Das folgende Diagramm veranschaulicht die Ressourcen, die in diesem Tutorial bereitgestellt werden: 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/multi-subnet-availability-group-diagram.png" alt-text="Diagramm: In diesem Tutorial bereitgestellte Ressourcen":::


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Abonnement. Sie können entweder ein [kostenloses Azure-Konto erstellen](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) oder [Visual Studio-Abonnementvorteile aktivieren](/visualstudio/subscriptions/subscriber-benefits).
- Grundkenntnisse hinsichtlich [Always On-Verfügbarkeitsgruppen in SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) sowie grundlegende Vertrautheit mit diesen Gruppen. 


## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Führen Sie die folgenden Schritte aus, um die Ressourcengruppe über das Azure-Portal zu erstellen: 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Portal die Option **+ Ressource erstellen** aus, um eine neue Ressource zu erstellen.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-portal-plus.png" alt-text="Auswählen von „+ Ressource erstellen“ im Portal, um eine neue Ressource zu erstellen":::

1. Suchen Sie im Suchfeld von **Marketplace** nach **Ressourcengruppe**, und wählen Sie die Kachel **Ressourcengruppe** von Microsoft aus. Wählen Sie auf der Seite **Ressourcengruppe** die Option **Erstellen** aus. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-search.png" alt-text="Suchen nach „Ressourcengruppe“ im Marketplace und Auswählen der Option zum Erstellen der Ressourcengruppe":::

1. Füllen Sie die Werte auf der Seite **Ressourcengruppe erstellen** aus, um die Ressourcengruppe zu erstellen:
   1. Wählen Sie in der Dropdownliste das entsprechende Azure-Abonnement aus. 
   1. Geben Sie einen Namen für Ihre Ressourcengruppe an (beispielsweise **SQL-HA-RG**).
   1. Wählen Sie in der Dropdownliste eine Region aus (beispielsweise **USA, Westen 2**). Alle weiteren Ressourcen müssen ebenfalls an diesem Standort bereitgestellt werden. 
   1. Wählen Sie **Überprüfen und erstellen** aus, um die Ressourcenparameter zu überprüfen, und wählen Sie dann **Erstellen** aus, um Ihre Ressourcengruppe zu erstellen.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-create-complete.png" alt-text="Ausfüllen der Werte im Azure-Portal, um Ihre Ressourcengruppe zu erstellen":::


## <a name="create-network-and-subnets"></a>Erstellen von Netzwerk und Subnetzen

Erstellen Sie als Nächstes das virtuelle Netzwerk und drei Subnetze. Weitere Informationen finden Sie unter [Was ist Azure Virtual Network?](../../../virtual-network/virtual-networks-overview.md). 

Führen Sie die folgenden Schritte aus, um das virtuelle Netzwerk über das Azure-Portal zu erstellen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie **+ Erstellen** aus.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Erstellen einer neuen Ressource in Ihrer Ressourcengruppe":::

1. Suchen Sie im Suchfeld von **Marketplace** nach **Virtuelles Netzwerk**, und wählen Sie die Kachel **Virtuelles Netzwerk** von Microsoft aus. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Erstellen** aus.  
1. Geben Sie auf der Seite **Virtuelles Netzwerk erstellen** auf der Registerkarte **Allgemeine Informationen** die folgenden Informationen ein: 
   1. Wählen Sie unter **Details zum Projekt** in der Dropdownliste **Abonnement** das entsprechende Azure-Abonnement und unter **Ressourcengruppe** die zuvor von Ihnen erstellte Ressourcengruppe aus (beispielsweise **SQL-HA-RG**). 
   1. Geben Sie unter **Details zur Instanz** einen Namen für Ihr virtuelles Netzwerk an (beispielsweise **SQLHAVNET**), und wählen Sie in der Dropdownliste die gleiche Region aus wie für Ihre Ressourcengruppe.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Auswählen der zuvor erstellten Ressourcengruppe und Angeben eines Namens für das virtuelle Netzwerk (beispielsweise „SQLHAVNET“)":::

1. Wählen Sie auf der Registerkarte **IP-Adressen** das Subnetz **Standard** aus, um die Seite **Subnetz bearbeiten** zu öffnen. Ändern Sie den Namen in **DC-subnet**, um es für das Domänencontrollersubnetz zu verwenden. Wählen Sie **Speichern** aus.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/04-create-vnet-ip-address-rename-default-subnet.png" alt-text="Wählen Sie auf der Registerkarte „IP-Adressen“ das Standardsubnetz aus, um die Seite „Subnetz bearbeiten“ zu öffnen. Ändern Sie den Namen in „DC-subnet“, um es für das Domänencontrollersubnetz zu verwenden. Wählen Sie „Speichern“ aus.":::

1. Wählen Sie **+ Subnetz hinzufügen** aus, um ein Subnetz für Ihre erste SQL Server-VM hinzuzufügen, und geben Sie die folgenden Werte an: 
   1. Geben Sie unter **Name des Subnetzes** einen Subnetznamen an (beispielsweise **SQL-subnet-1**). 
   1. Geben Sie einen eindeutigen Subnetzadressbereich innerhalb des Adressraums des virtuellen Netzwerks an. Hierzu können Sie beispielsweise das dritte Oktett des Adressbereichs von „DC-subnet“ um den Wert 1 erhöhen. 
      - Wenn der Adressbereich von **DC-subnet** also beispielsweise *10.38.0.0/24* ist, geben Sie für **SQL-subnet-1** den IP-Adressbereich `10.38.1.0/24` ein. 
      - Anderes Beispiel: Wenn **DC-subnet** den IP-Adressbereich *10.5.0.0/24* hat, geben Sie für das neue Subnetz den IP-Adressbereich `10.5.1.0/24` ein. 
   1. Wählen Sie **Hinzufügen** aus, um Ihr neues Subnetz hinzuzufügen. 

     :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Nennen Sie Ihr erstes Subnetz beispielsweise „sql-subnet-1“, und erhöhen Sie das dritte Oktett um den Wert 1. Wenn Ihr DC-Subnetz also beispielsweise die IP-Adresse 10.5.0.0 hat, sollte die IP-Adresse Ihres neuen Subnetzes 10.5.1.0 sein.":::

1. Wiederholen Sie den vorherigen Schritt, um einen weiteren eindeutigen Subnetzbereich für Ihre zweite SQL Server-VM hinzuzufügen. Als Name können Sie beispielsweise **SQL-subnet-2** verwenden. Sie können das dritte Oktett erneut um den Wert 1 erhöhen. 
   - Hat **DC-subnet** also beispielsweise den IP-Adressbereich *10.38.0.0/24* und **SQL-subnet-1** den Bereich *10.38.1.0/24*, geben Sie für das neue Subnetz `10.38.2.0/24` ein.
   - Anderes Beispiel: Wenn **DC-subnet** den IP-Adressbereich *10.5.0.0/24* und **SQL-subnet-1** den Bereich *10.5.1.0/24* hat, geben Sie für **SQL-subnet-2** den IP-Adressbereich `10.5.2.0/24` ein. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Nennen Sie Ihr zweites Subnetz beispielsweise „sql-subnet-2“, und erhöhen Sie das dritte Oktett um den Wert 2. Wenn Ihr DC-Subnetz also beispielsweise die IP-Adresse 10.38.0.0/24 hat, sollte die IP-Adresse Ihres neuen Subnetzes 10.38.2.0/24 sein.":::

1. Überprüfen Sie nach dem Hinzufügen des zweiten Subnetzes die Subnetznamen und -bereiche. (Ihre IP-Adressbereiche können von der Abbildung abweichen.) Ist alles korrekt, wählen Sie **Überprüfen und erstellen** > **Erstellen** aus, um Ihr neues virtuelles Netzwerk zu erstellen. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/07-create-vnet-ip-address.png" alt-text="Überprüfen Sie nach dem Hinzufügen des zweiten Subnetzes die Subnetznamen und -bereiche, wie auf dem Beispielbild zu sehen. (Ihre IP-Adressen können allerdings abweichen.) Ist alles korrekt, wählen Sie „Überprüfen und erstellen“ > „Erstellen“ aus, um Ihr neues virtuelles Netzwerk zu erstellen.":::

   Azure zeigt wieder das Portaldashboard an und benachrichtigt Sie, wenn das neue Netzwerk erstellt wurde.


## <a name="create-domain-controllers"></a>Erstellen von Domänencontrollern

Wenn das Netzwerk und die Subnetze bereit sind, erstellen Sie einen virtuellen Computer (oder optional zwei für Hochverfügbarkeit), und konfigurieren Sie ihn als Domänencontroller. 

### <a name="create-dc-virtual-machines"></a>Erstellen virtueller DC-Computer

Führen Sie die folgenden Schritte aus, um Ihre virtuellen DC-Computer (Domänencontroller) über das Azure-Portal zu erstellen: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie **+ Erstellen** aus.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Erstellen einer neuen Ressource in Ihrer Ressourcengruppe":::

1. Suchen Sie im Suchfeld von **Marketplace** nach **Windows Server**. 
1. Wählen Sie auf der Kachel **Windows Server** von Microsoft das Dropdownelement **Erstellen** und anschließend das Image **Windows Server 2016 Datacenter** aus. 
1. Füllen Sie die Werte auf der Seite **Virtuellen Computer erstellen** aus, um Ihren virtuellen Domänencontrollercomputer zu erstellen (beispielsweise **DC-VM-1**). Erstellen Sie optional einen weiteren virtuellen Computer (beispielsweise **DC-VM-2**), um Hochverfügbarkeit für die Active Directory Domain Services zu erhalten. Verwenden Sie die Werte aus der folgenden Tabelle, um Ihre virtuellen Computer zu erstellen:

    | **Feld** | Wert |
    | --- | --- |
    | **Abonnement** |*Ihr Abonnement* |
    | **Ressourcengruppe** |SQL-HA-RG |
    | **Name des virtuellen Computers** |Erster Domänencontroller: *DC-VM-1*</br>Zweiter Domänencontroller: *DC-VM-2* |
    | **Region** |*Der Standort, an dem Sie Ihre Ressourcengruppe und Ihr virtuelles Netzwerk bereitgestellt haben.* |
    | **Verfügbarkeitsoptionen** |Verfügbarkeitszone </br> *Für Azure-Regionen, die keine Verfügbarkeitszonen unterstützen, können stattdessen Verfügbarkeitsgruppen verwendet werden. Erstellen Sie eine neue Verfügbarkeitsgruppe, und platzieren Sie alle in diesem Tutorial erstellten virtuellen Computer in dieser Verfügbarkeitsgruppe.* |
    | **Verfügbarkeitszone** |Geben Sie „1“ für „DC-VM-1“ an. </br> Geben Sie „2“ für „DC-VM-2“ an. |
    | **Größe** |D2s_v3 (zwei vCPUs, 8 GB RAM) |
    | **Benutzername** |DomainAdmin |
    | **Kennwort** |Contoso!0000 |
    | **Öffentliche Eingangsports** | *Ausgewählte Ports zulassen* |
    | **Eingangsports auswählen** | *RDP (3389)* |
    | **Typ des Betriebssystemdatenträgers** | SSD Premium (lokal redundanter Speicher) |
    | **Virtuelles Netzwerk** |SQLHAVNET |
    | **Subnetz** |DC-subnet |
    | **Öffentliche IP-Adresse** |*Gleicher Name wie für den virtuellen Computer (beispielsweise „DC-VM-1“ oder „DC-VM-2“)* |
    | **NIC-Netzwerksicherheitsgruppe**| Basic |
    | **Öffentliche Eingangsports**| Ausgewählte Ports zulassen |
    | **Eingangsports auswählen**| RDP (3389) |
    | **Startdiagnose** |Mit verwaltetem Speicherkonto aktivieren (empfohlen) |

    Azure benachrichtigt Sie, wenn Ihre virtuellen Computer erstellt wurden und einsatzbereit sind. 


### <a name="configure-the-domain-controller"></a>Konfigurieren des Domänencontrollers

Wenn die virtuellen DC-Computer bereit sind, konfigurieren Sie den Domänencontroller für „corp.contoso.com“.

Führen Sie die folgenden Schritte aus, um **DC-VM-1** als Domänencontroller zu konfigurieren: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie den Computer **DC-VM-1** aus. 
1. Wählen Sie auf der Seite **DC-VM-1** die Option **Verbinden** aus, um eine RDP-Datei für Remotedesktopzugriff herunterzuladen, und öffnen Sie anschließend die Datei.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/08-dc-vm-1-rdp-connect.png" alt-text="Herstellen einer Verbindung mit einem virtuellen Computer":::

1. Stellen Sie eine Verbindung mit der RDP-Sitzung her. Verwenden Sie dabei Ihr konfiguriertes Administratorkonto (**DomainAdmin**) und Kennwort (**Contoso!0000**).
1. Öffnen Sie das Dashboard **Server-Manager** (wird ggf. standardmäßig geöffnet), und wählen Sie **Rollen und Features hinzufügen** aus. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Server Manager – Hinzufügen von Rollen":::

1. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverrollen** gelangen.
1. Wählen Sie die Rollen **Active Directory Domain Services** und **DNS-Server** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle für diese Rollen erforderlichen zusätzlichen Funktionen hinzu.

   > [!NOTE]
   > In Windows wird die Warnung angezeigt, dass keine statische IP-Adresse vorhanden ist. Wenn Sie die Konfiguration testen, wählen Sie **Weiter** aus. Legen Sie in Produktionsszenarien die IP-Adresse über das Azure-Portal als statische Adresse fest, oder [verwenden Sie PowerShell, um die statische IP-Adresse des Domänencontrollercomputers festzulegen](/azure/virtual-network/virtual-networks-static-private-ip-arm-ps).
   >

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/10-add-roles.png" alt-text="Dialogfeld „Rollen hinzufügen“":::

1. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Bestätigung** gelangen. Aktivieren Sie das Kontrollkästchen **Zielserver bei Bedarf automatisch neu starten**.
1. Wählen Sie **Installieren** aus.
1. Nachdem die Installation der Funktionen abgeschlossen ist, kehren Sie zum Dashboard **Server-Manager** zurück.
1. Wählen Sie die neue Option **AD DS** im linken Bereich aus.
1. Wählen Sie auf der gelben Warnleiste den Link **Mehr** aus.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/11-ad-ds-more.png" alt-text="AD DS-Dialogfeld auf dem virtuellen DNS-Servercomputer":::
    
1. Wählen Sie im Dialogfeld **Alle Serveraufgabendetails** in der Spalte **Aktion** den Eintrag **Server zu einem Domänencontroller heraufstufen** aus.
1. Verwenden Sie im **Konfigurations-Assistenten für Active Directory-Domänendienste** die folgenden Werte:

    | **Seite** | Einstellung |
    | --- | --- |
    | **Bereitstellungskonfiguration** |**Neue Gesamtstruktur hinzufügen**<br/> **Rootdomänenname** = corp.contoso.com |
    | **Domänencontrolleroptionen** |**DSRM-Kennwort** = Contoso!0000<br/>**Kennwort bestätigen** = Contoso!0000 |

1. Wählen Sie **Weiter** aus, um die anderen Seiten des Assistenten zu durchlaufen. Vergewissern Sie sich, dass auf der Seite **Prüfung der erforderlichen Komponenten** die folgende Meldung angezeigt wird: **Alle erforderlichen Komponenten wurden erfolgreich überprüft.** Sie können alle zutreffenden Warnmeldungen überprüfen, aber es ist möglich, die Installation fortzusetzen.
1. Wählen Sie **Installieren** aus. Der virtuelle Computer **DC-VM-1** wird automatisch neu gestartet.

### <a name="identify-dns-ip-address"></a>Ermitteln der DNS-IP-Adresse

Verwenden Sie den primären Domänencontroller für den DNS. Ermitteln Sie hierzu die private IP-Adresse des virtuellen Computers, der für den primären Domänencontroller verwendet wird. 

Führen Sie die folgenden Schritte aus, um im Azure-Portal die private IP-Adresse des virtuellen Computers zu ermitteln: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie den primären Domänencontroller **DC-VM-1** aus. 
1. Wählen Sie auf der Seite **DC-VM-1** im Bereich **Einstellungen** die Option **Netzwerk** aus. 
1. Notieren Sie sich die unter **Private IP-Adresse der NIC** angegebene Adresse. Verwenden Sie diese IP-Adresse als DNS-Server für die anderen virtuellen Computer.  Auf dem Beispielbild lautet die private IP-Adresse **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="Wählen Sie auf der Seite „DC-VM-1“ im Bereich „Einstellungen“ die Option „Netzwerk“ aus, und notieren Sie sich die private IP-Adresse der NIC. Verwenden Sie diese IP-Adresse als DNS-Server.":::

### <a name="configure-virtual-network-dns"></a>Konfigurieren des DNS für das virtuelle Netzwerk

Nachdem Sie den ersten Domänencontroller erstellt und das DNS aktiviert haben, konfigurieren Sie das virtuelle Netzwerk so, dass dieser virtuelle Computer für das DNS verwendet wird.

Führen Sie die folgenden Schritte aus, um Ihr virtuelles Netzwerk für das DNS zu konfigurieren: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie Ihr virtuelles Netzwerk aus (beispielsweise **SQLHAVNET**). 
1. Wählen Sie im Bereich **Einstellungen** die Option **DNS-Server** und dann **Benutzerdefiniert** aus. 
1. Geben Sie die zuvor ermittelte private IP-Adresse (beispielsweise `10.38.0.4`) in das Feld **IP-Adresse** ein. 
1. Wählen Sie **Speichern** aus. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="Wählen Sie im Bereich „Einstellungen“ die Option „DNS-Server“ und dann „Benutzerdefiniert“ aus. Geben Sie die zuvor ermittelte private IP-Adresse (beispielsweise 10.38.0.4) in das Feld „IP-Adresse“ ein.":::


### <a name="configure-second-domain-controller"></a>Konfigurieren des zweiten Domänencontrollers

Nach dem Neustart des primären Domänencontrollers können Sie optional den zweiten Domänencontroller konfigurieren, um Hochverfügbarkeit zu erreichen. Falls Sie keinen zweiten Domänencontroller konfigurieren möchten, überspringen Sie diesen Schritt. Beachten Sie jedoch, dass in Produktionsumgebungen ein zweiter Domänencontroller empfohlen wird. 

Legen Sie die bevorzugte DNS-Serveradresse fest, treten Sie der Domäne bei, und konfigurieren Sie dann den sekundären Domänencontroller. 

#### <a name="set-preferred-dns-server-address"></a>Festlegen der bevorzugten DNS-Serveradresse

Ändern Sie zunächst die Adresse des bevorzugte DNS-Servers. Gehen Sie dazu folgendermaßen vor: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie den sekundären Domänencontrollercomputer aus (beispielsweise **DC-VM-2**). 
1. Wählen Sie auf der Seite **DC-VM-2** die Option **Verbinden** aus, um die RDP-Datei für Remotedesktopzugriff herunterzuladen, und öffnen Sie anschließend die Datei. 
1. Stellen Sie eine Verbindung mit der RDP-Sitzung her. Verwenden Sie dabei Ihr konfiguriertes Administratorkonto (**DomainAdmin**) und Kennwort (**Contoso!0000**).
1. Öffnen Sie **Netzwerk- und Freigabecenter**, und wählen Sie die Netzwerkschnittstelle aus. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/13-network-interface.png" alt-text="Netzwerkschnittstelle":::

1. Öffnen Sie die Seite **Eigenschaften**. 
1. Wählen Sie **Internetprotokoll Version 4 (TCP/IPv4)** und dann **Eigenschaften** aus.
1. Wählen Sie **Folgende DNS-Serveradressen verwenden** aus, und geben Sie unter **Bevorzugter DNS-Server** die private IP-Adresse des primären Domänencontrollers an (beispielsweise `10.38.0.4`). 
1. Wählen Sie **OK** und anschließend **Schließen** aus, um die Änderungen zu übernehmen. Sollte die Remotedesktopverbindung nach dem Ändern der DNS-IP-Adresse verloren gehen, navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem virtuellen Computer, und starten Sie ihn neu. 

### <a name="join-the-domain"></a>Beitreten zur Domäne 

Treten Sie als Nächstes der Domäne **corp.contoso.com** bei. Gehen Sie dazu folgendermaßen vor: 

1. Stellen Sie unter Verwendung des Kontos **BUILTIN\DomainAdmin** eine Remoteverbindung mit dem virtuellen Computer her.
1. Öffnen Sie **Server-Manager**, und wählen Sie **Lokaler Server** aus.
1. Wählen Sie **WORKGROUP** aus.
1. Wählen Sie im Abschnitt **Computername** die Option **Ändern** aus.
1. Aktivieren Sie das Kontrollkästchen **Domäne**, und geben Sie **corp.contoso.com** in das Textfeld ein. Klicken Sie auf **OK**.
1. Geben Sie im Popupdialogfeld **Windows-Sicherheit** die Anmeldeinformationen für das standardmäßige Domänenadministratorkonto (**CORP\DomainAdmin**) und das Kennwort (**Contoso!0000**) an.
1. Wenn die Meldung „Willkommen in der Domäne ‚corp.contoso.com‘“ angezeigt wird, wählen Sie **OK** aus.
1. Wählen Sie **Schließen** und dann im Popupdialogfeld **Jetzt neu starten** aus.


#### <a name="configure-domain-controller"></a>Konfigurieren des Domänencontrollers 

Nachdem Ihr Server der Domäne hinzugefügt wurde, können Sie ihn als zweiten Domänencontroller konfigurieren. Gehen Sie dazu folgendermaßen vor: 

1. Sollte noch keine Verbindung bestehen, öffnen Sie eine RDP-Sitzung mit Ihrem sekundären Domänencontroller, und öffnen Sie das Dashboard **Server-Manager** (ggf. standardmäßig geöffnet).
1. Wählen Sie im Dashboard den Link **Rollen und Features hinzufügen** aus.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Server Manager – Hinzufügen von Rollen":::

1. Wählen Sie **Weiter** aus, bis Sie zum Abschnitt **Serverrollen** gelangen.
1. Wählen Sie die Rollen **Active Directory Domain Services** und **DNS-Server** aus. Wenn Sie dazu aufgefordert werden, fügen Sie alle für diese Rollen erforderlichen zusätzlichen Funktionen hinzu.
1. Nachdem die Installation der Funktionen abgeschlossen ist, kehren Sie zum Dashboard **Server-Manager** zurück.
1. Wählen Sie die neue Option **AD DS** im linken Bereich aus.
1. Wählen Sie auf der gelben Warnleiste den Link **Mehr** aus.
1. Wählen Sie im Dialogfeld **Alle Serveraufgabendetails** in der Spalte **Aktion** den Eintrag **Server zu einem Domänencontroller heraufstufen** aus.
1. Wählen Sie unter **Bereitstellungskonfiguration** die Option **Domänencontroller vorhandener Domäne hinzufügen**.
1. Klicken Sie auf **Auswählen**.
1. Verwenden Sie das Administratorkonto (**CORP.CONTOSO.COM\domainadmin**) und Kennwort (**Contoso!0000**), um eine Verbindung herzustellen.
1. Wählen Sie unter **Domäne aus der Gesamtstruktur auswählen** Ihre Domäne und dann **OK** aus.
1. Verwenden Sie unter **Domänencontrolleroptionen** die Standardwerte, und legen Sie ein DSRM-Kennwort fest.

    >[!NOTE]
    >Auf der Seite **DNS-Optionen** wird möglicherweise eine Warnung angezeigt, dass für diesen DNS-Server keine Delegierung erstellt werden kann. Sie können diese Warnung in nicht produktiven Umgebungen ignorieren.
    >

1. Wählen Sie **Weiter** aus, bis das Dialogfeld die **Voraussetzungsprüfung** erreicht. Wählen Sie dann **Installieren** aus.

Nachdem der Server die Änderungen an der Konfiguration abgeschlossen hat, starten Sie den Server neu.

### <a name="add-second-dc-ip-address-to-dns"></a>Hinzufügen der IP-Adresse des zweiten Domänencontrollers zum DNS

Führen Sie nach der Konfiguration Ihres zweiten Domänencontrollers erneut die zuvor beschriebenen Schritte aus, um die [private IP-Adresse des virtuellen Computers zu ermitteln](#identify-dns-ip-address) und die [private IP-Adresse als sekundären benutzerdefinierten DNS-Server hinzuzufügen](#configure-virtual-network-dns) (im virtuellen Netzwerk Ihrer Ressourcengruppe). Durch das Hinzufügen des sekundären DNS-Servers im Azure-Portal wird Redundanz für den DNS-Dienst erreicht. 



## <a name="configure-domain-accounts"></a>Konfigurieren von Domänenkonten

Nachdem Sie Ihre(n) Domänencontroller konfiguriert und Ihre(n) DNS-Server im Azure-Portal festgelegt haben, können Sie Domänenkonten für den Benutzer, der SQL Server installiert, und für das SQL Server-Dienstkonto erstellen. 

Konfigurieren Sie insgesamt drei Konten: ein Installationskonto für beide SQL Server-VMs und jeweils ein Dienstkonto für die beiden SQL Server-VMs. Verwenden Sie für die Konten beispielsweise die Werte in der folgenden Tabelle:

|Konto  | VM  |Vollständiger Domänenname  |BESCHREIBUNG   |
|---------|---------|---------|---------|
|Installieren    |Beide| Corp\Install        |Melden Sie sich mit diesem Konto bei einem der virtuellen Computer an, um den Cluster und die Verfügbarkeitsgruppe zu konfigurieren. |
|SQLSvc1     |SQL-VM-1 |Corp\SQLSvc1 | Verwenden Sie dieses Konto für den SQL Server-Dienst auf der ersten SQL Server-VM. |
|SQLSvc2     |SQL-VM2 |Corp\SQLSvc2| Verwenden Sie dieses Konto für den SQL Server-Dienst auf der zweiten SQL Server-VM.|

Führen Sie die folgenden Schritte aus, um die Konten zu erstellen: 

1. Stellen Sie eine Verbindung mit Ihrem primären Domänencontrollercomputer (beispielsweise **DC-VM-1**) her. .
1. Wählen Sie im **Server-Manager** die Option **Tools** und dann **Active Directory-Verwaltungscenter** aus.
1. Wählen Sie **corp (local)** im linken Bereich.
1. Wählen Sie rechts im Bereich **Aufgaben** die Option **Neu** und dann **Benutzer** aus. 
1. Geben Sie das neue Benutzerkonto ein, und legen Sie ein komplexes Kennwort fest. Legen Sie für nicht produktive Umgebungen fest, dass das Benutzerkonto nie abläuft.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/14-ad-dc-new-user.png" alt-text="Active Directory-Verwaltungscenter":::

1. Wählen Sie **OK** aus, um den Benutzer zu erstellen.
1. Wiederholen Sie diese Schritte, um alle drei Konten zu erstellen. 

### <a name="grant-installation-account-permissions"></a>Erteilen von Berechtigungen für das Installationskonto

Erteilen Sie im Anschluss an die Kontoerstellung dem Installationskonto die erforderlichen Domänenberechtigungen, um die Erstellung von Objekten in AD zu ermöglichen. 

Führen Sie die folgenden Schritte aus, um dem Installationskonto die erforderlichen Berechtigungen zu erteilen: 

1. Öffnen Sie über den **Server-Manager** das **Active Directory-Verwaltungscenter**, sofern es noch nicht geöffnet ist. 
1. Wählen Sie im linken Bereich die Option **corp (local)** aus. 
1. Vergewissern Sie sich rechts im Bereich **Aufgaben**, dass **corp (local)** in der Dropdownliste angezeigt wird, und wählen Sie darunter **Eigenschaften** aus.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/15-ad-dc-properties.png" alt-text="CORP-Benutzereigenschaften":::

1. Wählen Sie **Erweiterungen** und dann auf der Registerkarte **Sicherheit** die Schaltfläche **Erweitert** aus.
1. Wählen Sie im Dialogfeld **Erweiterte Sicherheitseinstellungen für corp** die Option **Hinzufügen** aus.
1. Wählen Sie **Prinzipal auswählen** aus, suchen Sie nach **CORP\Install**, und wählen Sie anschließend **OK** aus.
1. Aktivieren Sie das Kontrollkästchen neben **Alle Eigenschaften lesen** und **Computerobjekte erstellen**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/16-add-permissions.png" alt-text="Corp-Benutzerberechtigungen":::

1. Klicken Sie auf **OK** und anschließend erneut auf **OK**. Schließen Sie das Eigenschaftenfenster von **corp**.

Nachdem Sie nun Active Directory und die Benutzerobjekte konfiguriert haben, können Sie Ihre SQL Server-VMs erstellen. 

## <a name="create-sql-server-vms"></a>Erstellen der virtuellen SQL Server-Computer

Wenn AD, DNS und Ihre Benutzerkonten konfiguriert sind, können Sie Ihre SQL Server-VMs erstellen. Verwenden Sie der Einfachheit halber die SQL Server VM-Images aus dem Marketplace. 

Setzen Sie sich jedoch vor dem Erstellen Ihrer SQL Server-VMs zunächst mit den folgenden Entwurfsentscheidungen auseinander: 

**Verfügbarkeit: Verfügbarkeitszonen**   

Stellen Sie die virtuellen Computer in separaten Verfügbarkeitszonen bereit, um ein Höchstmaß an Redundanz, Resilienz und Verfügbarkeit zu erreichen. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig voneinander funktionieren. Verwenden Sie für Azure-Regionen, die noch keine Verfügbarkeitszonen unterstützen, Verfügbarkeitsgruppen. Platzieren Sie alle virtuellen Computer in der gleichen Verfügbarkeitsgruppe. 

**Speicher – Azure Managed Disks**   

Verwenden Sie als Speicher für virtuelle Computer Azure Managed Disks. Microsoft empfiehlt Managed Disks für SQL Server-VMs, da sie den Speicher im Hintergrund verwalten. Weitere Informationen finden Sie unter [Azure Managed Disks – Übersicht](../../../virtual-machines/managed-disks-overview.md). 

**Netzwerk – private IP-Adressen in der Produktion**   

Für die virtuellen Computer werden in diesem Tutorial öffentliche IP-Adressen verwendet. Eine öffentliche IP-Adresse ermöglicht eine direkte Remoteverbindung mit dem virtuellen Computer über das Internet. Die Konfigurationsschritte werden somit vereinfacht. In Produktionsumgebungen empfiehlt Microsoft private IP-Adressen, um die Sicherheitsrisiken der VM-Ressource für die SQL Server-Instanz zu verringern.

**Netzwerk: Einzelne NIC pro Server**   

Verwenden Sie eine einzelne NIC pro Server (Clusterknoten). Azure-Netzwerktechnologie verfügt über physische Redundanz, was zusätzliche NICs in einem Failovercluster, der auf einem virtuellen Azure-Computer bereitgestellt ist, überflüssig macht. Der Clusterüberprüfungsbericht weist Sie darauf hin, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind. Diese Warnung kann ignoriert werden, wenn sich Ihr Failovercluster auf virtuellen Azure-Computern befindet. 

Führen Sie die folgenden Schritte aus, um Ihre virtuellen Computer zu erstellen: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie **+ Erstellen** aus.
1. Suchen Sie nach **Azure SQL**, und wählen Sie die Kachel **Azure SQL** von Microsoft aus. 
1. Wählen Sie auf der Seite **Azure SQL** die Option **Erstellen** und anschließend in der Dropdownliste das Image **SQL Server 2016 SP2 Enterprise unter Windows Server 2016** aus. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/18-select-sql-vm-image.png" alt-text="Wählen Sie im Portal auf der Seite „Azure SQL“ die Option „Erstellen“ und anschließend in der Dropdownliste das Image „SQL Server 2016 SP2 Enterprise unter Windows Server 2016“ aus.":::

Füllen Sie die Werte auf der Seite **Virtuellen Computer erstellen** aus. Orientieren Sie sich dabei an der folgenden Tabelle, um beide SQL Server-VMs (beispielsweise **SQL-VM-1** und **SQL-VM-2**) zu erstellen. (Ihre IP-Adressen können von den Beispielen in der Tabelle abweichen.) 

| Konfiguration | SQL-VM-1 | SQL-VM-2 |
| --- | --- | --- |
| Katalogimage |**SQL Server 2016 SP2 Enterprise unter Windows Server 2016** |**SQL Server 2016 SP2 Enterprise unter Windows Server 2016** |
| **VM-Grundeinstellungen** |**Name** = SQL-VM-1<br/>**Benutzername** = DomainAdmin<br/>**Kennwort** = Contoso!0000<br/>**Abonnement** = Ihr Abonnement<br/>**Ressourcengruppe** = SQL-HA-RG<br/>**Standort** = Ihr Azure-Standort |**Name** = SQL-VM-2<br/>**Benutzername** = DomainAdmin<br/>**Kennwort** = Contoso!0000<br/>**Abonnement** = Ihr Abonnement<br/>**Ressourcengruppe** = SQL-HA-RG<br/>**Standort** = Ihr Azure-Standort |
| **VM-Größe** |**Größe** = E2ds_v4 (zwei vCPUs, 16 GB RAM)</br> |**Größe** = E2ds_v4 (zwei vCPUs, 16 GB RAM) |
| **VM-Einstellungen** |**Verfügbarkeitsoptionen** = Verfügbarkeitszone<br/>**Verfügbarkeitszone** = 1<br/>**Öffentliche Eingangsports** = Ausgewählte Ports zulassen<br/>**Eingangsports auswählen** = RDP (3389)<br/>**Typ des Betriebssystemdatenträgers** = SSD Premium (lokal redundanter Speicher)<br/>**Virtuelles Netzwerk** = SQLHAVNET<br/>**Subnetz** = SQL-subnet-1(10.38.1.0/24)<br/>**Öffentliche IP-Adresse** = Automatisch generiert<br/>**NIC-Netzwerksicherheitsgruppe** = Standard<br/>**Öffentliche Eingangsports** = Ausgewählte Ports zulassen <br/> **Eingangsports auswählen** = RDP (3389)<br/>**Startdiagnose** = Mit verwaltetem Speicherkonto aktivieren (empfohlen)<br/>|**Verfügbarkeitsoptionen** = Verfügbarkeitszone<br/>**Verfügbarkeitszone** = 2<br/>**Öffentliche Eingangsports** = Ausgewählte Ports zulassen<br/>**Eingangsports auswählen** = RDP (3389)<br/>**Typ des Betriebssystemdatenträgers** = SSD Premium (lokal redundanter Speicher)<br/>**Virtuelles Netzwerk** = SQLHAVNET<br/>**Subnetz** = SQL-subnet-2(10.38.2.0/24)<br/>**Öffentliche IP-Adresse** = Automatisch generiert<br/>**NIC-Netzwerksicherheitsgruppe** = Standard<br/>**Öffentliche Eingangsports** = Ausgewählte Ports zulassen <br/> **Eingangsports auswählen** = RDP (3389)<br/>**Startdiagnose** = Mit verwaltetem Speicherkonto aktivieren (empfohlen)<br/> |
| **SQL Server-Einstellungen** |**SQL-Konnektivität** = Privat (innerhalb von Virtual Network)<br/>**Port** = 1433<br/>**SQL-Authentifizierung** = Deaktiviert<br/>**Azure Key Vault-Integration** = Deaktivieren <br/>**Speicheroptimierung** = Transaktionsverarbeitung<br/>**SQL-Daten** = 1.024 GiB, 5.000 IOPS, 200 MB/s<br/>**SQL-Protokoll** = 1.024 GiB, 5.000 IOPS, 200 MB/s<br/>**SQL TempDb** = Lokales SSD-Laufwerk verwenden<br/>**Automatisiertes Patchen** = Sonntags, 2:00 Uhr<br/>**Automatisierte Sicherung** = Deaktivieren |**SQL-Konnektivität** = Privat (innerhalb von Virtual Network)<br/>**Port** = 1433<br/>**SQL-Authentifizierung** = Deaktiviert<br/>**Azure Key Vault-Integration** = Deaktivieren <br/>**Speicheroptimierung** = Transaktionsverarbeitung<br/>**SQL-Daten** = 1.024 GiB, 5.000 IOPS, 200 MB/s<br/>**SQL-Protokoll** = 1.024 GiB, 5.000 IOPS, 200 MB/s<br/>**SQL TempDb** = Lokales SSD-Laufwerk verwenden<br/>**Automatisiertes Patchen** = Sonntags, 2:00 Uhr<br/>**Automatisierte Sicherung** = Deaktivieren |

<br/>

> [!NOTE]
> Diese vorgeschlagenen Computergrößen sind nur zum Testen von Verfügbarkeitsgruppen in Azure Virtual Machines vorgesehen. Größenempfehlungen für optimierte Produktionsworkloads finden Sie unter [Checkliste: Bewährte Methoden für SQL Server auf Azure-VMs](./performance-guidelines-best-practices-checklist.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

## <a name="configure-sql-server-vms"></a>Konfigurieren der SQL Server-VMs

Konfigurieren Sie nach Abschluss der VM-Erstellung Ihre SQL Server-VMs, indem Sie jedem virtuellen Computer eine sekundäre IP-Adresse hinzufügen und sie in die Domäne einbinden. 


### <a name="add-secondary-ips-to-sql-server-vms"></a>Hinzufügen sekundärer IP-Adressen zu den SQL Server-VMs

Weisen Sie in der Umgebung mit mehreren Subnetzen jeder SQL Server-VM eine sekundäre IP-Adresse zur Verwendung für den Verfügbarkeitsgruppenlistener zu. Für Windows Server 2016 und ältere Versionen muss außerdem jeder SQL Server-VM eine sekundäre IP-Adresse für die Cluster-IP-Adresse zugewiesen werden. Dadurch wird keine Azure Load Balancer-Instanz benötigt, was in einer Umgebung mit einzelnem Subnetz der Fall wäre.  

Unter Windows Server 2016 und älteren Versionen muss jeder SQL Server-VM eine zusätzliche sekundäre IP-Adresse für die Windows-Cluster-IP-Adresse zugewiesen werden, da der Cluster anstelle des in Windows Server 2019 eingeführten Standardnamens des verteilten Netzwerks (Distributed Network Name, DNN) den **Clusternetzwerknamen** verwendet. Mit einem DNN wird das Clusternamenobjekt (CNO) automatisch mit den IP-Adressen für alle Knoten des Clusters registriert, wodurch keine dedizierte Windows-Cluster-IP-Adresse benötigt wird.

Wenn Sie Windows Server 2016 oder eine ältere Version verwenden, müssen Sie mithilfe der in diesem Abschnitt beschriebenen Schritte jeder SQL Server-VM eine sekundäre IP-Adresse zuweisen, und zwar *sowohl* für den Verfügbarkeitsgruppenlistener *als auch* für den Cluster. 

Ab Windows Server 2019 müssen Sie lediglich eine sekundäre IP-Adresse für den Verfügbarkeitsgruppenlistener zuweisen und können die Schritte zum Zuweisen einer Windows-Cluster-IP-Adresse überspringen – es sei denn, Sie planen, Ihren Cluster mit einem VNN (Virtual Network Name, Name des virtuellen Netzwerks) zu konfigurieren. Weisen Sie in diesem Fall jeder SQL Server-VM beide IP-Adressen zu (wie bei Verwendung von Windows Server 2016). 

Führen Sie die folgenden Schritte aus, um den virtuellen Computern zusätzliche sekundäre IP-Adressen zuzuweisen: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Ressourcengruppe, und wählen Sie die erste SQL Server-VM (beispielsweise **SQL-VM-1**) aus. 
1. Wählen Sie im Bereich **Einstellungen** die Option **Netzwerk** und anschließend die **Netzwerkschnittstelle** aus: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Auswählen von „Netzwerk“ im Bereich „Einstellungen“ und anschließendes Auswählen der Netzwerkschnittstelle":::

1. Wählen Sie auf der Seite **Netzwerkschnittstelle** im Bereich **Einstellungen** die Option **IP-Konfigurationen** und anschließend **+ Hinzufügen** aus, um eine zusätzliche IP-Adresse hinzuzufügen: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP-Konfigurationen":::

1. Führen Sie auf der Seite **IP-Konfiguration hinzufügen** die folgenden Schritte aus: 
   1. Geben Sie unter **Name** einen Wert als Windows-Cluster-IP-Adresse an – etwa **windows-cluster-ip** für Windows 2016 und ältere Versionen. Überspringen Sie diesen Schritt, wenn Sie mindestens Windows Server 2019 verwenden.  
   1. Legen Sie **Zuordnung** auf **Statisch** fest.
   1. Geben Sie eine noch nicht verwendete **IP-Adresse** aus dem Subnetz **SQL-subnet-1** ein, in dem sich auch die SQL Server-VM **SQL-VM-1** befindet – beispielsweise `10.38.1.10`. 
   1. Behalten Sie unter **Öffentliche IP-Adresse** die Standardeinstellung **Zuordnung aufheben** bei. 
   1. Wählen Sie **OK** aus, um das Hinzufügen der IP-Konfiguration abzuschließen. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="Hinzufügen einer Cluster-IP-Adresse durch Eingeben einer nicht verwendeten IP-Adresse aus dem Subnetz der ersten SQL Server-VM":::

1. Wählen Sie erneut **+ Hinzufügen** aus, um eine zusätzliche IP-Adresse für den Verfügbarkeitsgruppenlistener (mit einem Namen wie **availability-group-listener**) zu konfigurieren, und geben Sie erneut eine nicht verwendete IP-Adresse aus dem Subnetz **SQL-subnet-1** an (beispielsweise `10.38.1.11`): 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/22-add-ip-ag-listener.png" alt-text="Wählen Sie erneut „+ Hinzufügen“ aus, um eine zusätzliche IP-Adresse für den Verfügbarkeitsgruppenlistener (mit einem Namen wie „availability-group-listener“) zu konfigurieren, und geben Sie erneut eine nicht verwendete IP-Adresse aus dem Subnetz „SQL-subnet-1“ an (beispielsweise 10.31.1.11).":::

1. Wiederholen Sie diese Schritte für die zweite SQL Server-VM (beispielsweise **SQL-VM-2**). Weisen Sie zwei nicht verwendete sekundäre IP-Adressen aus dem Subnetz **SQL-subnet-2** zu. Verwenden Sie die Werte aus der folgenden Tabelle, um die IP-Konfiguration hinzuzufügen: 

   
    | **Feld** | Eingabe | Eingabe | 
    | --- | --- | --- |
    | **Name** |windows-cluster-ip | availability-group-listener |
    | **Speicherbelegung** | statischen | statischen |
    | **IP-Adresse** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |

Nun können Sie der Domäne **corp.contoso.com** beitreten. 

### <a name="join-the-servers-to-the-domain"></a>Einbinden der Server in die Domäne

Nachdem beiden SQL Server-VMs die beiden sekundären IP-Adressen zugewiesen wurden, können Sie die SQL Server-VMs in die Domäne **corp.contoso.com** einbinden. 

Führen Sie die gleichen Schritte aus wie beim [Einbinden des sekundären Domänencontrollers in die Domäne](#join-the-domain), um die SQL Server-VMs in die Domäne „corp.contoso.com“ einzubinden. 

Warten Sie jeweils, bis die SQL Server-VM neu gestartet wurde, und fügen Sie dann Ihre Konten hinzu. 


## <a name="add-accounts"></a>Hinzufügen von Konten

Fügen Sie das Installationskonto als Administrator auf allen virtuellen Computern hinzu, erteilen Sie innerhalb von SQL Server die Berechtigung für das Installationskonto und die lokalen Konten, und aktualisieren Sie das SQL Server-Dienstkonto. 

### <a name="add-install-account"></a>Hinzufügen des Installationskontos

Nachdem beide SQL Server-VMs in die Domäne eingebunden wurden, können Sie **CORP\Install** als Mitglied der lokalen Administratorgruppe hinzufügen.

>[!TIP]
> Achten Sie darauf, dass Sie sich mit dem *Domänen* administratorkonto anmelden. In den vorherigen Schritten wurde das Administratorkonto **BUILTIN** verwendet. Nachdem der Server nun in die Domäne eingebunden ist, wird das Domänenkonto verwendet. Geben Sie in Ihrer RDP-Sitzung die Domäne und den Benutzernamen im Format *DOMÄNE*\\*Benutzername* an (also beispielsweise **CORP\DomainAdmin**).

Führen Sie die folgenden Schritte aus, um das Konto als Administrator hinzuzufügen: 

1. Warten Sie, bis der virtuelle Computer neu gestartet wurde, und starten Sie die RDP-Datei erneut über die erste SQL Server-VM, um sich bei **SQL-VM-1** mit dem Konto **CORP\DomainAdmin** anzumelden.
1. Wählen Sie in **Server-Manager** die Option **Tools** und anschließend **Computerverwaltung** aus.
1. Erweitern Sie im Fenster **Computerverwaltung** die Option **Lokale Benutzer und Gruppen**, und wählen Sie dann **Gruppen** aus.
1. Doppelklicken Sie auf die Gruppe **Administratoren** .
1. Wählen Sie im Dialogfeld **Administratoreigenschaften** die Schaltfläche **Hinzufügen** aus.
1. Geben Sie den Benutzer **CORP\Install** ein, und wählen Sie dann **OK** aus.
1. Wählen Sie **OK** aus, um das Dialogfeld **Administratoreigenschaften** zu schließen.
1. Wiederholen Sie diese Schritte für **SQL-VM-2**. 

### <a name="add-account-to-sysadmin"></a>Hinzufügen eines Kontos zu „sysadmin“ 

Das Installationskonto (CORP\Install), das zum Konfigurieren der Verfügbarkeitsgruppe verwendet wird, muss auf jeder SQL Server-VM Teil der festen Serverrolle **sysadmin** sein. 

Führen Sie die folgenden Schritte aus, um **sysadmin** die erforderlichen Berechtigungen zu erteilen: 

1. Stellen Sie unter Verwendung des Kontos *\<MachineName\>\DomainAdmin* (beispielsweise `SQL-VM-1\DomainAdmin`) eine RDP-Verbindung mit dem Server her.
1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der lokalen SQL Server-Instanz her.
1. Wählen Sie im **Objekt-Explorer** die Option **Sicherheit** aus.
1. Klicken Sie mit der rechten Maustaste auf **Anmeldungen**. Wählen Sie **Neue Anmeldung** aus.
1. Wählen Sie in **Anmeldung – Neu** die Option **Suche** aus.
1. Wählen Sie **Standorte** aus.
1. Geben Sie die Netzwerkanmeldeinformationen des Domänenadministrators ein.
1. Verwenden Sie das Installationskonto (CORP\install).
1. Legen Sie die Anmeldung als Mitglied der festen Serverrolle **sysadmin** fest.
1. Klicken Sie auf **OK**.
1. Wiederholen Sie diese Schritte auf der zweiten SQL Server-VM (beispielsweise **SQL-VM-2**), und stellen Sie eine Verbindung mit dem relevanten Computernamenkonto (beispielsweise `SQL-VM-2\DomainAdmin`) her. 


### <a name="add-system-account"></a>Hinzufügen des Systemkontos 

In höheren Versionen von SQL Server verfügt das Konto „[NT AUTHORITY\SYSTEM]“ standardmäßig nicht über Berechtigungen für SQL Server, sodass die Berechtigungen manuell erteilt werden müssen. 

Führen Sie die folgenden Schritte aus, um „[NT AUTHORITY\SYSTEM]“ hinzuzufügen und entsprechende Berechtigungen zu erteilen: 

1. Stellen Sie unter Verwendung des Kontos *\<MachineName\>\DomainAdmin* (beispielsweise `SQL-VM-1\DomainAdmin`) eine RDP-Verbindung mit der ersten SQL Server-VM her.
1. Öffnen Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit der lokalen SQL Server-Instanz her.
1. Erstellen Sie mithilfe des folgenden T-SQL-Befehls (Transact-SQL) in jeder SQL Server-Instanz ein Konto für `[NT AUTHORITY\SYSTEM]`: 

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Erteilen Sie `[NT AUTHORITY\SYSTEM]` die folgenden Berechtigungen für jede SQL Server-Instanz:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Verwenden Sie zum Erteilen dieser Berechtigungen den folgenden T-SQL-Befehl (Transact-SQL): 

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

1. Wiederholen Sie diese Schritte auf der zweiten SQL Server-VM (beispielsweise **SQL-VM-2**), und stellen Sie eine Verbindung mit dem relevanten Computernamenkonto (beispielsweise `SQL-VM-2\DomainAdmin`) her. 

### <a name="set-the-sql-server-service-accounts"></a>Festlegen der SQL Server-Dienstkonten

Vom SQL Server-Dienst auf dem jeweiligen virtuellen Computer muss ein dediziertes Domänenkonto verwendet werden.  Verwenden Sie die zuvor erstellten Domänenkonten: **Corp\SQLSvc1** für **SQL-VM-1** und **Corp\SQLSvc2** für **SQL-VM-2**. 

Führen Sie zum Festlegen des Dienstkontos die folgenden Schritte aus: 

1. Stellen Sie unter Verwendung des Kontos *\<MachineName\>\DomainAdmin* (beispielsweise `SQL-VM-1\DomainAdmin`) eine RDP-Verbindung mit der ersten SQL Server-VM her.
1. Öffnen Sie den **SQL Server-Konfigurations-Manager**.
1. Klicken Sie mit der rechten Maustaste auf den SQL Server-Dienst, und wählen Sie **Eigenschaften** aus.
1. Geben Sie das Konto (**Corp\SQLSvc1**) und das Kennwort an.
1. Wählen Sie **Anwenden** aus, um die Änderung zu übernehmen und den SQL Server-Dienst neu zu starten. 
1. Wiederholen Sie diese Schritte für die andere SQL Server-VM (SQL-VM-2). Melden Sie sich dabei mit dem Computerdomänenkonto (beispielsweise `SQL-VM-2\DomainAdmin`) an, und geben Sie das zweite Dienstkonto (**Corp\SQLSvc2**) an. 


## <a name="create-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Wenn Sie einen Windows Server-Failovercluster mit zwei Knoten bereitstellen möchten, ist ein drittes Mitglied erforderlich, um ein Quorum zu etablieren. Auf virtuellen Azure-Computern ist der Cloudzeuge die empfohlene Quorumoption. Zum Konfigurieren eines Cloudzeugen benötigen Sie ein Azure Storage-Konto. Weitere Informationen finden Sie unter [Bereitstellen eines Cloudzeugen für einen Failovercluster](/windows-server/failover-clustering/deploy-cloud-witness).

So erstellen Sie das Azure Storage-Konto über das Portal:

1. Öffnen Sie im Portal die Ressourcengruppe **SQL-HA-RG**, und wählen Sie **+ Erstellen** aus.
1. Suchen Sie nach **Speicherkonto**.
1. Wählen **Speicherkonto** > **Erstellen** aus, und konfigurieren Sie es mit den folgenden Werten: 

    1. Wählen Sie Ihr Abonnement und anschließend die Ressourcengruppe **SQL-HA-RG** aus.
    1. Geben Sie unter **Name des Speicherkontos** einen Namen für Ihr Speicherkonto ein.
       Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Außerdem muss der Name des Speicherkontos innerhalb von Azure eindeutig sein.    
    1. Wählen Sie Ihre **Region** aus.
    1. Wählen Sie unter **Leistung** die Option **Standard: empfohlen für die meisten Szenarios** (universelles v2-Konto) aus. Azure Storage Premium wird für einen Cloudzeugen nicht unterstützt.
    1. Wählen Sie unter **Redundanz** die Option **Lokal redundanter Speicher (LRS)** aus.
       Beim Failoverclustering wird die Blobdatei als Vermittlungspunkt verwendet. Dies erfordert einige Konsistenzgarantien beim Lesen von Daten. Wählen Sie daher als Replikationstyp die Option „Lokal redundanter Speicher (LRS)“ aus. 
    1. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="configure-the-firewall"></a>Konfigurieren der Firewall

Für das Verfügbarkeitsgruppenfeature ist Datenverkehr über die folgenden TCP-Ports erforderlich: 

- **SQL Server-VM**: Port 1433 für eine Standardinstanz von SQL Server
- **Datenbankspiegelungs-Endpunkt**: Ein beliebiger verfügbarer Port. Für Beispiele wird häufig 5022 verwendet.

Öffnen Sie diese Firewallports auf beiden SQL Server-VMs. Die Methode zum Öffnen der Ports hängt von der verwendeten Firewalllösung ab und kann sich vom Windows Firewall-Beispiel in diesem Abschnitt unterscheiden. 

Führen Sie die folgenden Schritte aus, um diese Ports in einer Windows Firewall-Instanz zu öffnen: 

1. Starten Sie auf dem **Startbildschirm** der ersten SQL Server-Instanz die **Windows-Firewall mit erweiterter Sicherheit**.
1. Klicken Sie im linken Bereich auf **Eingehende Regeln**. Wählen Sie im rechten Bereich **Neue Regel** aus.
1. Wählen Sie Für **Regeltyp** die Option **Port**.
1. Geben Sie für den Port **TCP** an, und geben Sie die entsprechenden Portnummern ein. Sehen Sie sich folgendes Beispiel an:

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/17-firewall-tcp-ports.png" alt-text="SQL-Firewall":::

1. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** und anschließend **Weiter** aus.
1. Akzeptieren Sie auf der Seite **Profil** die Standardeinstellungen, und wählen Sie dann **Weiter** aus.
1. Geben Sie auf der Seite **Name** im Textfeld **Name** einen Regelnamen an (beispielsweise **SQL Inbound**), und wählen Sie dann **Fertig stellen** aus.
1. Wiederholen Sie diese Schritte auf dem zweiten virtuellen SQL Server-Computer.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Voraussetzungen konfiguriert haben, können Sie mit dem [Konfigurieren Ihrer Verfügbarkeitsgruppe](availability-group-manually-configure-tutorial-multi-subnet.md) in mehreren Subnetzen beginnen. 

Weitere Informationen finden Sie unter:

- [Windows Server-Failovercluster mit SQL Server auf Azure-VMs](hadr-windows-server-failover-cluster-overview.md)
- [Always On-Verfügbarkeitsgruppen mit SQL Server auf Azure-VMs](availability-group-overview.md)
- [Übersicht über Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [HADR-Einstellungen für SQL Server auf Azure-VMs](hadr-cluster-best-practices.md)
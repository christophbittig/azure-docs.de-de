---
title: Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter SLES | Microsoft-Dokumentation
description: Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter SLES
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/26/2021
ms.author: radeltch
ms.openlocfilehash: ab66bcdc834fd894d7d258ab9c7c08a0049b07ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673139"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Hochverfügbarkeit für horizontal skalierte SAP HANA-Systeme mit HSR unter SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:../../../azure-netapp-files/index.yml
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


In diesem Artikel wird beschrieben, wie Sie ein hochverfügbares SAP HANA-System in einer Konfiguration mit horizontaler Skalierung mit HANA-Systemreplikation (HANA System Replication, HSR) und Pacemaker auf virtuellen Azure SUSE Linux Enterprise Server-Computern (Virtual Machines, VMs) bereitstellen. Die freigegebenen Dateisysteme in der vorgestellten Architektur werden von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) bereitgestellt und über NFS eingebunden.  

In den Beispielkonfigurationen, Installationsbefehlen usw. werden die HANA-Instanz **03** und HANA-System-ID **HN1** verwendet. Die Beispiele basieren auf HANA 2.0 SP4 und SUSE Linux Enterprise Server 12 SP5. 

Bevor Sie beginnen, lesen Sie die folgenden SAP-Hinweise und Dokumente:

* [Azure NetApp Files-Dokumentation][anf-azure-doc] 
* SAP-Hinweis [1928533] enthält:  
  * Liste der für die Bereitstellung von SAP-Software unterstützten Azure-VM-Größen
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Die erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* SAP-Hinweis [2015553]: Enthält eine Liste der Voraussetzungen für Bereitstellungen von SAP-Software in Azure, die von SAP unterstützt werden
* SAP-Hinweis [2205917]: Enthält empfohlene Betriebssystemeinstellungen für SUSE Linux Enterprise Server für SAP-Anwendungen
* SAP-Hinweis [1944799]: Enthält SAP-Richtlinien für SUSE Linux Enterprise Server für SAP-Anwendungen
* SAP-Hinweis [2178632]: Enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden
* SAP-Hinweis [2191498]: Enthält die erforderliche SAP Host Agent-Version für Linux in Azure
* SAP-Hinweis [2243692]: Enthält Informationen zur SAP-Lizenzierung unter Linux in Azure
* SAP-Hinweis [1984787]: Enthält allgemeine Informationen zu SUSE Linux Enterprise Server 12
* SAP-Hinweis [1999351]: Enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP
* SAP-Hinweis [1900823]: Enthält Informationen zu SAP HANA-Speicheranforderungen
* [Wiki der SAP Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [Best Practices für SUSE SAP HA][suse-ha-guide]: Enthält alle erforderlichen Informationen zum lokalen Einrichten von NetWeaver-Hochverfügbarkeit und SAP Hana System Replication (als allgemeine Basis zu verwenden, sie bieten jedoch weitaus ausführlichere Informationen)
* [Versionshinweise zur SUSE Linux Enterprise-Hochverfügbarkeitserweiterung 12 SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Behandeln fehlerhafter NFS-Freigaben im SUSE-Hochverfügbarkeitscluster für die HANA-Systemreplikation](https://www.suse.com/support/kb/doc/?id=000019904)
* [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]
* [NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Übersicht

Eine Methode zum Erreichen von HANA-Hochverfügbarkeit für Installationen horizontaler HANA-Skalierungen besteht darin, die HANA-Systemreplikation zu konfigurieren und die Lösung mit einem Pacemaker-Cluster zu schützen, um ein automatisches Failover zu ermöglichen. Wenn ein aktiver Knoten ausfällt, führt der Clusterknoten einen Failover zu den HANA-Ressourcen am anderen Standort aus.  
Die vorgestellte Konfiguration zeigt drei HANA-Knoten an jedem Standort sowie einen Majority Maker-Knoten, um ein Split-Brain-Szenario zu verhindern. Die Anweisungen können angepasst werden, um mehr virtuelle Computer als HANA DB-Knoten einzubeziehen.  

Das freigegebene HANA-Dateisystem `/hana/shared` in der vorgestellten Architektur wird von [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) bereitgestellt. Es wird über NFSv4.1 auf jedem HANA-Knoten an demselben Replikationsstandort des HANA-Systems eingebunden. Die Dateisysteme `/hana/data` und `/hana/log` sind lokale Dateisysteme und werden nicht von den HANA DB-Knoten freigegeben. SAP HANA wird im nicht freigegebenen Modus installiert. 

> [!TIP]
> Informationen zu empfohlenen SAP HANA-Speicherkonfigurationen finden Sie unter [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md).   

[![Horizontale SAP HANA-Skalierung mit HSR und Pacemaker-Cluster unter SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

Im vorangehenden Diagramm werden drei Subnetze in einem virtuellen Azure-Netzwerk dargestellt, das den Empfehlungen für SAP HANA-Netzwerke entspricht: 
* Für die Clientkommunikation – `client` 10.23.0.0/24  
* Für die interne Kommunikation zwischen SAP HANA-Knoten – `inter` 10.23.1.128/26  
* Für die HANA-Systemreplikation – `hsr` 10.23.1.192/26  

Da `/hana/data` und `/hana/log` auf lokalen Datenträgern bereitgestellt werden, ist es nicht erforderlich, separate Subnetze und separate virtuelle Netzwerkkarten für die Kommunikation mit dem Speicher bereitzustellen.  

Die Azure NetApp-Volumes werden in einem separaten Subnetz bereitgestellt, das an [Azure NetApp Files delegiert wurde](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> Die Systemreplikation an einem dritten Standort wird nicht unterstützt. Weitere Informationen finden Sie im Abschnitt „Wichtige Voraussetzungen“ unter [SLES SAP HANA-Systemreplikation (horizontale Skalierung) – leistungsoptimiertes Szenario](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Einrichten der Infrastruktur

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits die Ressourcengruppe und das Azure Virtual Network mit drei Azure Virtual Network-Subnetzen erstellt haben: `client`, `inter` und `hsr`.

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal
1. Stellen Sie die virtuellen Azure-Computer bereit.  
Stellen Sie für die in diesem Dokument vorgestellte Konfiguration sieben virtuelle Computer bereit: 
   - Drei virtuelle Computer, die als HANA DB-Knoten für HANA-Replikationsstandort 1 dienen: **hana-s1-db1**, **hana-s1-db2** und **hana-s1-db3**  
   - Drei virtuelle Computer, die als HANA DB-Knoten für HANA-Replikationsstandort 2 dienen: **hana-s2-db1**, **hana-s2-db2** and **hana-s2-db3**  
   - Ein kleiner virtueller Computer, der als *Majority Maker* dient: **hana-s-mm**

   Die virtuellen Computer, die als SAP DB HANA-Knoten bereitgestellt werden, sollten von SAP für HANA zertifiziert sein, wie im [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) veröffentlicht. Stellen Sie bei der Bereitstellung der HANA DB-Knoten sicher, dass [Beschleunigtes Netzwerk](../../../virtual-network/create-vm-accelerated-networking-cli.md) aktiviert ist.  
  
   Für den Majority Maker-Knoten können Sie einen kleinen virtuellen Computer bereitstellen, da auf diesem keine der SAP HANA-Ressourcen ausgeführt wird. Der virtuelle Majority Maker-Computer wird in der Clusterkonfiguration verwendet, um eine ungerade Anzahl von Clusterknoten in einem Split-Brain-Szenario zu erreichen. Der virtuelle Majority Maker-Computer benötigt in diesem Beispiel nur eine virtuelle Netzwerkschnittstelle im Subnetz `client`.        

   Stellen Sie lokal verwaltete Datenträger für `/hana/data` und `/hana/log` bereit. Die empfohlene Mindestspeicherkonfiguration für `/hana/data` und `/hana/log` ist in der [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md) beschrieben.

   Stellen Sie die primäre Netzwerkschnittstelle für die einzelnen virtuellen Computer im `client`-Subnetz des virtuellen Netzwerks bereit.  
   Wenn der virtuelle Computer über das Azure-Portal bereitgestellt wird, erfolgt die automatische Generierung des Namens der Netzwerkschnittstelle. In diesen Anweisungen werden die automatisch generierten primären Netzwerkschnittstellen, die mit dem `client`-Subnetz des virtuellen Azure-Netzwerks verbunden sind, der Einfachheit halber als **hana-s1-db1-client**, **hana-s1-db2-client**, **hana-s1-db3-client** usw. bezeichnet.  


   > [!IMPORTANT]
   > Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem SAP-zertifiziert ist für SAP HANA auf den spezifischen VM-Typen, die Sie verwenden. Eine Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese finden Sie auf der Website [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klicken Sie in die Details des jeweils aufgeführten VM-Typs, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für diesen Typ anzuzeigen.  
  

2. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `inter`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-inter**, **hana-s1-db2-inter**, **hana-s1-db3-inter**, **hana-s2-db1-inter**, **hana-s2-db2-inter** und **hana-s2-db3-inter**).  

3. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `hsr`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-hsr**, **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr** und **hana-s2-db3-hsr**).  

4. Fügen Sie die neu erstellten virtuellen Netzwerkschnittstellen an die entsprechenden virtuellen Computer an:  

    a. Navigieren Sie im [Azure-Portal](https://portal.azure.com/#home) zum virtuellen Computer.  

    b. Wählen Sie im linken Bereich **Virtuelle Computer** aus. Filtern Sie nach dem Namen des virtuellen Computers (z. B. **hana-s1-db1**), und wählen Sie dann den virtuellen Computer aus.  

    c. Klicken Sie im Bereich **Übersicht** auf **Beenden**, um die Zuordnung des virtuellen Computers aufzuheben.  

    d. Wählen Sie **Netzwerk** aus, und fügen Sie dann die Netzwerkschnittstelle an. Wählen Sie in der Dropdownliste **Netzwerkschnittstelle anfügen** die bereits erstellten Netzwerkschnittstellen für die Subnetze `inter` und `hsr` aus.  
    
    e. Wählen Sie **Speichern** aus. 
 
    f. Wiederholen Sie die Schritte b bis e für die übrigen virtuellen Computer (in unserem Beispiel **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2** und **hana-s2-db3**).
 
    g. Belassen Sie die virtuelle Computer für den Moment im Status „Beendet“. Als Nächstes aktivieren wir den [beschleunigten Netzwerkbetrieb](../../../virtual-network/create-vm-accelerated-networking-cli.md) für alle neu angefügten Netzwerkschnittstellen.  

5. Aktivieren Sie den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen für die Subnetze `inter` und `hsr` mit den folgenden Schritten:  

    a. Öffnen Sie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) im [Azure-Portal](https://portal.azure.com/#home).  

    b. Führen Sie die folgenden Befehle aus, um den beschleunigten Netzwerkbetrieb für die zusätzlichen Netzwerkschnittstellen zu aktivieren, die wir an die Subnetze `inter` und `hsr` angefügt haben.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Starten der virtuellen HANA DB-Computer

### <a name="deploy-azure-load-balancer"></a>Bereitstellen von Azure Load Balancer

1. Es wird empfohlen, Load Balancer Standard zu verwenden. Befolgen Sie die folgenden Konfigurationsschritte zur Bereitstellung von Load Balancer Standard:
   1. Erstellen Sie zunächst einen Front-End-IP-Pool:

      1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. **hana-frontend**).
      1. Legen Sie die **Zuweisung** auf **Statisch** fest, und geben Sie die IP-Adresse (z. B. **10.23.0.27**) ein.
      1. Klicken Sie auf **OK**.
      1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.

   1. Erstellen Sie dann einen Back-End-Pool, und fügen Sie alle virtuellen Clustercomputer zum Back-End-Pool hinzu:

      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. **hana-backend**).
      1. Wählen Sie **Virtuellen Computer hinzufügen** aus.
      1. Klicken Sie auf **Virtuelle Computer**.
      1. Wählen Sie die virtuellen Computer des SAP HANA-Clusters und deren IP-Adressen für das `client`-Subnetz aus.
      1. Wählen Sie **Hinzufügen**.

   1. Erstellen Sie als Nächstes einen Integritätstest:

      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen des neuen Integritätstests ein (z.B. **hana-hp**).
      1. Wählen Sie als Protokoll **TCP** und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
      1. Klicken Sie auf **OK**.

   1. Erstellen Sie als Nächstes die Lastenausgleichsregeln:
   
      1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
      1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **hana-lb**).
      1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **hana-frontend**, **hana-backend** und **hana-hp**).
      1. Wählen Sie **HA-Ports** aus.
      1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
      1. Klicken Sie auf **OK**.

   > [!IMPORTANT]
   > Floating IP-Adressen werden für sekundäre NIC-IP-Konfigurationen in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.    
   
   > [!Note]
   > Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  


   > [!IMPORTANT]
   > Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Siehe auch SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Bereitstellen der Infrastruktur für Azure NetApp Files 

Stellen Sie die ANF-Volumes für das `/hana/shared`-Dateisystem bereit. Sie benötigen ein separates `/hana/shared`-Volume für jeden Replikationsstandort des HANA-Systems. Weitere Informationen finden Sie unter [Einrichten der Infrastruktur für Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

In diesem Beispiel wurden die folgenden Azure NetApp Files-Volumes verwendet: 

* Volume „**HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)“
* Volume „**HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)“


## <a name="operating-system-configuration-and-preparation"></a>Konfiguration und Vorbereitung des Betriebssystems

Die Anweisungen in den nächsten Abschnitten weisen eine der folgenden Abkürzungen auf:
* **[A]** :      Gilt für alle Knoten
* **[AH]** :     Gilt für alle HANA DB-Knoten
* **[M]** :      Gilt für den Majority Maker-Knoten
* **[AH1]** :    Gilt für alle HANA DB-Knoten an STANDORT 1
* **[AH2]** :    Gilt für alle HANA DB-Knoten an STANDORT 2
* **[1]** :      Gilt nur für HANA DB-Knoten 1, STANDORT 1
* **[2]** :      Gilt nur für HANA DB-Knoten 1, STANDORT 2

Führen Sie die folgenden Schritte aus, um das Betriebssystem zu konfigurieren und vorzubereiten:

1. **[A]** Verwalten Sie die Hostdateien auf den virtuellen Computern. Schließen Sie Einträge für alle Subnetze ein. Die folgenden Einträge wurden für dieses Beispiel zu `/etc/hosts` hinzugefügt.  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

3. **[A]** Bereiten Sie das Betriebssystem für die Ausführung von SAP HANA auf NetApp-Systemen mit NFS vor, wie unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure] beschrieben. Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/netapp-hana.conf* für die NetApp-Konfigurationseinstellungen.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/ms-az.conf* mit Microsoft für Azure-Konfigurationseinstellungen.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

    > [!TIP]
    > Legen Sie „net.IPv4.ip_local_port_range“ und „net.IPv4.ip_local_reserved_ports“ nicht explizit in den sysctl-Konfigurationsdateien fest, damit der SAP-Host-Agent die Portbereiche verwalten kann. Weitere Informationen finden Sie im SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

4. **[A]** Passen Sie die sunrpc-Einstellungen entsprechend den Empfehlungen unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure] an.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

2. **[A]** SUSE stellt spezielle Ressourcen-Agents für SAP HANA bereit, und Agents für die vertikale SAP HANA-Skalierung sind standardmäßig installiert. Deinstallieren Sie die Pakete für die vertikale Skalierung (sofern installiert), und installieren Sie die Pakete für das Szenario der horizontalen SAP HANA-Skalierung. Der Schritt muss auf allen Cluster-VMs einschließlich des Majority Maker-VMs ausgeführt werden.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[AH]** Vorbereiten der VMs: Wenden Sie die empfohlenen Einstellungen gemäß SAP-Hinweis [2205917] für SUSE Linux Enterprise Server für SAP-Anwendungen an.  

## <a name="prepare-the-file-systems"></a>Vorbereiten der Dateisysteme
### <a name="mount-the-shared-file-systems"></a>Einbinden der freigegebenen Dateisysteme

In diesem Beispiel werden die freigegebenen HANA-Dateisysteme auf Azure NetApp Files bereitgestellt und über NFSv4 eingebunden.  

1. **[AH]** Erstellen Sie Bereitstellungspunkte für die HANA-Datenbankvolumes.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **`defaultv4iddomain.com`** ) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.  
   Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFSv4.1 verwenden.  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass die NFS-Domäne in `/etc/idmapd.conf` auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **`defaultv4iddomain.com`** . Wenn es einen Konflikt zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) gibt, werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als `nobody` angezeigt.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** Überprüfen Sie `nfs4_disable_idmapping`. Diese Angabe sollte auf **Y** (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.  
   Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFSv4.1 verwenden.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 1 ein.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 2 ein.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[AH]** Überprüfen Sie, ob die entsprechenden `/hana/shared/`-Dateisysteme auf allen virtuellen HANA DB-Computern mit NFS-Protokollversion **NFSv4** eingebunden sind.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Vorbereiten der Daten und Protokollieren lokaler Dateisysteme
In der dargestellten Konfiguration werden die Dateisysteme `/hana/data` und `/hana/log` auf einem verwalteten Datenträger bereitgestellt und lokal an die einzelnen virtuellen HANA DB-Computer angefügt. Sie müssen die Schritte zur Erstellung der lokalen Daten- und Protokollvolumes auf jedem virtuellen HANA DB-Computer ausführen. 

Richten Sie das Datenträgerlayout mit **Logical Volume Manager (LVM)** ein. Im folgenden Beispiel wird davon ausgegangen, dass jeder virtuelle HANA-Computer über drei Datenträger verfügen, die zum Erstellen von zwei Volumes verwendet werden.

1. **[AH]** Auflisten aller verfügbaren Datenträger:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Beispielausgabe:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** Erstellen Sie physische Volumes für alle Datenträger, die Sie verwenden möchten:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** Erstellen Sie eine Volumegruppe für die Datendateien. Erstellen Sie eine Volumegruppe für Protokolldateien und eine für das freigegebene Verzeichnis von SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** Erstellen Sie die logischen Volumes. 
   Ein lineares Volume wird erstellt, wenn Sie `lvcreate` ohne den Schalter `-i` verwenden. Es wird empfohlen, ein Stripesetvolume für eine bessere E/A-Leistung zu erstellen und die Stripegrößen an die in [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md) dokumentierten Werte anzupassen. Das `-i`-Argument sollte die Anzahl der zugrunde liegenden physischen Volumes und das `-I`-Argument die Stripegröße sein. In diesem Dokument werden zwei physische Volumes für das Datenvolume verwendet, daher wird das Argument für den Schalter `-i` auf **2** festgelegt. Die Stripegröße für das Datenvolume beträgt **256 KiB**. Für das Protokollvolume wird ein physisches Volume verwendet, sodass keine `-i`- oder `-I`-Schalter explizit für die Protokollvolumebefehle verwendet werden.  

   > [!IMPORTANT]
   > Verwenden Sie den Schalter `-i`, und ändern Sie die Zahl in die Anzahl der zugrunde liegenden physischen Volumes, wenn Sie für die einzelnen Daten- oder Protokollvolumes mehrere physische Datenträger verwenden. Verwenden Sie den Schalter `-I`, um die Stripegröße festzulegen, wenn Sie ein Stripesetvolume erstellen.  
   > Informationen zu empfohlenen Speicherkonfigurationen, einschließlich Stripegrößen und Anzahl der Datenträger, finden Sie unter [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md).  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** Erstellen Sie die Bereitstellungsverzeichnisse, und kopieren Sie die UUID aller logischen Volumes:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** Erstellen Sie `fstab`-Einträge für die logischen Volumes, und binden Sie Folgendes an:
    ```bash
    sudo vi /etc/fstab
    ```

   Fügen Sie die folgende Zeile in die Datei `/etc/fstab` ein:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Stellen Sie die neuen Volumes bereit:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker auf SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) zum Erstellen eines grundlegenden Pacemaker-Clusters für diesen HANA-Server aus.
Beziehen Sie alle virtuellen Computer ein, einschließlich Majority Maker im Cluster.  

> [!IMPORTANT]
> Legen Sie `quorum expected-votes` nicht auf 2 fest, da es sich nicht um einen Cluster mit zwei Knoten handelt.  
> Stellen Sie sicher, dass die Clustereigenschaft `concurrent-fencing` aktiviert ist, damit das Knotenfencing deserialisiert wird.   

## <a name="installation"></a>Installation  

In diesem Beispiel für die Bereitstellung von SAP HANA in einer Konfiguration mit horizontaler Skalierung mit HSR auf virtuellen Azure-Computern haben wir HANA 2.0 SP4 verwendet.  

### <a name="prepare-for-hana-installation"></a>Vorbereiten der HANA-Installation

1. **[AH]** Legen Sie vor der HANA-Installation das Stammkennwort fest. Nachdem die Installation abgeschlossen wurde, können Sie das Stammkennwort deaktivieren. Führen Sie den Befehl `passwd` als `root` aus.  

2. **[1,2]** Ändern der Berechtigungen für `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Überprüfen Sie, ob Sie sich über SSH bei den virtuellen HANA DB-Computern an diesem Standort, **hana-s1-db2** und **hana-s1-db3**, anmelden können, ohne nach einem Kennwort gefragt zu werden. Wenn dies nicht der Fall ist, tauschen Sie die SSH-Schlüssel (wie unter [Aktivieren des SSH-Zugriffs über den öffentlichen Schlüssel](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional) beschrieben) aus.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Überprüfen Sie, ob Sie sich über SSH bei den virtuellen HANA DB-Computern an diesem Standort, **hana-s2-db2** und **hana-s2-db3**, anmelden können, ohne nach einem Kennwort gefragt zu werden.  
   Wenn dies nicht der Fall ist, tauschen Sie die SSH-Schlüssel aus.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** Installieren Sie zusätzliche Pakete, die für HANA 2.0 SP4 erforderlich sind. Weitere Informationen finden Sie im SAP-Hinweis [2593824](https://launchpad.support.sap.com/#/notes/2593824) für Ihre SLES-Version. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-Installation auf dem ersten Knoten an jedem Standort

1. **[1]** Installieren Sie SAP HANA gemäß den Anweisungen im [SAP HANA 2.0 Installation and Update Guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) (Installations- und Updateleitfaden für SAP HANA 2.0). In den folgenden Anweisungen wird die SAP HANA-Installation auf dem ersten Knoten an STANDORT 1 veranschaulicht.   

   a. Starten Sie das Programm **hdblcm** als `root` über das HANA-Installationssoftwareverzeichnis. Verwenden Sie den `internal_network`-Parameter, und übergeben Sie den Adressraum für das Subnetz, das für die interne Kommunikation zwischen HANA-Knoten verwendet wird.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Aktion auswählen**: Geben Sie **1** ein (für „installieren“).
     * Für **Additional components for installation** (Zusätzliche Komponenten für die Installation): Geben Sie **2, 3** ein.
     * Für den Installationspfad: Drücken Sie die EINGABETASTE (Standardwert „/hana/shared“).
     * Für **Local Host Name** (Name des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Do you want to add hosts to the system?** (Möchten Sie dem System Hosts hinzufügen?): Geben Sie **n** ein.
     * Für **SAP HANA System ID** (SAP HANA-System-ID): Geben Sie **HN1** ein.
     * Für **Instance number** (Instanznummer) [00]: Geben Sie **03** ein.
     * Für **Local Host Worker Group** (Workergruppe des lokalen Hosts): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select System Usage / Enter index [4]** (Systemnutzung auswählen/Index eingeben): Geben Sie **4** (für benutzerdefiniert) ein.
     * Für **Location of Data Volumes** (Speicherort der Datenvolumes) [/Hana/Data/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Location of Log Volumes** (Speicherort der Protokollvolumes) [/Hana/log/HN1]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie die maximale Speicherbelegung beschränken?** [n] Geben Sie **n** ein.
     * Für **Certificate Host Name For Host hana-s1-db1** [hana-s1-db1]: (Zertifikathostname für Host „hana-s1-db1“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer): Geben Sie das Kennwort ein.
     * Für **Confirm SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer bestätigen): Geben Sie das Kennwort ein.
     * Für **System Administrator (hn1adm) Password** (Kennwort für den Systemadministrator (hn1adm)): Geben Sie das Kennwort ein.
     * Für **System Administrator Home Directory** (Stammverzeichnis des Systemadministrators) [/usr/sap/HN1/home]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator Login Shell** (Anmelde-Shell für den Systemadministrator) [/bin/sh]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator User ID** (Benutzer-ID des Systemadministrators) [1001]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter ID of User Group (sapsys)** (ID der Benutzergruppe (sapsys) eingeben) [79]: Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * For **System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system)): Geben Sie das Kennwort für das System ein.
     * For **Confirm System Database User (system) Password** (Kennwort für den Systemdatenbankbenutzer (system) bestätigen): Geben Sie das Kennwort für das System ein.
     * Für **Soll das System nach dem Neustart des Computers neu starten?** [n] Geben Sie **n** ein. 
     * Für **Möchten Sie fortfahren (y/n)?** : Überprüfen Sie die Zusammenfassung. Wenn alle Werte korrekt sind, geben Sie **y** ein.

2. **[2]** Wiederholen Sie den vorhergehenden Schritt, um SAP HANA auf dem ersten Knoten an STANDORT 2 zu installieren.   

3. **[1,2]** Überprüfen Sie die „global.ini“.  

   Zeigen Sie die „global. ini“ an, und stellen Sie sicher, dass die Konfiguration für die interne SAP HANA-Kommunikation zwischen Knoten eingerichtet ist. Überprüfen Sie den Abschnitt **communication**. Dieser sollte den Adressraum für das `inter`-Subnetz enthalten, und `listeninterface` sollte auf `.internal` festgelegt sein. Überprüfen Sie den Abschnitt **internal_hostname_resolution**. Er sollte die IP-Adressen für die virtuellen HANA-Computer enthalten, die zum `inter`-Subnetz gehören.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1,2]** Bereiten Sie `global.ini` für die Installation in einer nicht freigegebenen Umgebung vor, wie in SAP-Hinweis [2080991](https://launchpad.support.sap.com/#/notes/0002080991) beschrieben.  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** Starten Sie SAP HANA neu, um die Änderungen zu aktivieren.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** Stellen Sie sicher, dass die Clientschnittstelle zur Kommunikation die IP-Adressen aus dem `client`-Subnetz verwendet.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Weitere Informationen zum Überprüfen der Konfiguration finden Sie im SAP-Hinweis [2183363 – Konfiguration des internen SAP HANA-Netzwerks](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[AH]** Ändern Sie die Berechtigungen für die Daten- und Protokollverzeichnisse, um einen HANA-Installationsfehler zu vermeiden.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** Installieren Sie die sekundären HANA-Knoten. Die Beispielanweisungen in diesem Schritt beziehen sich auf STANDORT 1.  
   a. Starten Sie das Programm **hdblcm** als `root`.    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Aktion auswählen**: Geben Sie **2** ein (für „Hosts installieren“).
     * Für **Enter comma separated host names to add** (Durch Komma getrennte hinzuzufügende Hostnamen eingeben): hana-s1-db2, hana-s1-db3
     * Für **Additional components for installation** (Zusätzliche Komponenten für die Installation): Geben Sie **2, 3** ein.
     * Für **Enter Root User Name [root]** (Root-Benutzername [root] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select roles for host 'hana-s1-db2' [1]** (Rollen für Host 'hana-s1-db2' [1] eingeben): 1 (für Worker)
     * Für **Enter Host Failover Group for host 'hana-s1-db2' [default]** (Hostfailovergruppe für Host 'hana-s1-db2' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Storage Partition Number for host 'hana-s1-db2' [\<\<assign automatically\>\>]** (Speicherpartitionsnummer für Host 'hana-s1-db2' eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Worker Group for host 'hana-s1-db2' [default]** (Workergruppe für Host 'hana-s1-db2' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Select roles for host 'hana-s1-db3' [1]** (Rollen für Host 'hana-s1-db3' [1] eingeben): 1 (für Worker)
     * Für **Enter Host Failover Group for host 'hana-s1-db3' [default]** (Hostfailovergruppe für Host 'hana-s1-db3' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Storage Partition Number for host 'hana-s1-db3' [\<\<assign automatically\>\>]** (Speicherpartitionsnummer für Host 'hana-s1-db3' eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Enter Worker Group for host 'hana-s1-db3' [default]** (Workergruppe für Host 'hana-s1-db3' [Standard] eingeben): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **System Administrator (hn1adm) Password** (Kennwort für den Systemadministrator (hn1adm)): Geben Sie das Kennwort ein.
     * Für **Enter SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer (sapadm) eingeben): Geben Sie das Kennwort ein.
     * Für **Confirm SAP Host Agent User (sapadm) Password** (Kennwort für SAP-Host-Agent-Benutzer bestätigen): Geben Sie das Kennwort ein.
     * Für **Certificate Host Name For Host hana-s1-db2** [hana-s1-db2]: (Zertifikathostname für Host „hana-s1-db2“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Certificate Host Name For Host hana-s1-db3** [hana-s1-db3]: (Zertifikathostname für Host „hana-s1-db3“): Drücken Sie die EINGABETASTE, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie fortfahren (y/n)?** : Überprüfen Sie die Zusammenfassung. Wenn alle Werte korrekt sind, geben Sie **y** ein.

9. **[2]** Wiederholen Sie den vorhergehenden Schritt, um die sekundären SAP HANA-Knoten auf STANDORT 2 zu installieren.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurieren der SAP HANA 2.0-Systemreplikation

1. **[1]**  Konfigurieren Sie die Systemreplikation auf STANDORT 1.

   Sichern Sie die Datenbanken als **hn1** adm:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Kopieren Sie die PKI-Systemdateien auf den sekundären Standort:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Erstellen Sie den primären Standort:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Konfigurieren Sie die Systemreplikation auf STANDORT 2.
    
   Registrieren Sie den zweiten Standort zum Starten der Replikation. Führen Sie den folgenden Befehl als „<hanasid\>adm“ aus:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Überprüfen Sie den Replikationsstatus.

   Überprüfen Sie den Replikationsstatus, und warten Sie, bis alle Datenbanken synchronisiert wurden.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** Ändern Sie die HANA-Konfiguration, sodass die Kommunikation für die HANA-Systemreplikation über die virtuellen Netzwerkschnittstellen der HANA-Systemreplikation geleitet wird.   
   - HANA an beiden Standorten beenden
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Bearbeiten Sie „global.ini“, um die Hostzuordnung für die HANA-Systemreplikation hinzuzufügen: Verwenden Sie die IP-Adressen aus dem Subnetz `hsr`.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - HANA an beiden Standorten starten
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Weitere Informationen finden Sie unter [Hostnamensauflösung für die Systemreplikation](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Erstellen von Dateisystemressourcen

Erstellen Sie eine Dummy-Dateisystemclusterressource, die Fehler überwacht und meldet, falls beim Zugreifen auf das über NFS eingebundene Dateisystem `/hana/shared` ein Problem auftritt. Dann kann der Cluster ein Failover auslösen, falls beim Zugreifen auf `/hana/shared` ein Problem auftritt. Weitere Informationen hierzu finden Sie unter [Behandeln fehlerhafter NFS-Freigaben im SUSE-Hochverfügbarkeitscluster für die HANA-Systemreplikation](https://www.suse.com/support/kb/doc/?id=000019904). 

1. **[1]** Versetzen Sie Pacemaker in den Wartungsmodus, als Vorbereitung für die Erstellung der HANA-Clusterressourcen.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1,2]** Erstellen Sie das Verzeichnis auf dem ANF-Volume „/hana/shared“, das in der speziellen Überwachungsressource des Dateisystems verwendet wird. Die Verzeichnisse müssen an beiden Standorten erstellt werden.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[AH]** Erstellen Sie das Verzeichnis, das zum Einbinden der speziellen Überwachungsressource des Dateisystems verwendet wird. Das Verzeichnis muss auf allen HANA-Clusterknoten erstellt werden.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** Erstellen Sie die Clusterressourcen des Dateisystems.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   Das Attribut `OCF_CHECK_LEVEL=20` wird dem Überwachungsvorgang hinzugefügt, sodass Überwachungsvorgänge einen Lese-/Schreibtest auf dem Dateisystem ausführen. Ohne dieses Attribut überprüft der Überwachungsvorgang nur, ob das Dateisystem eingebunden ist. Dies kann ein Problem sein, denn im Fall eines Konnektivitätsverlusts bleibt das Dateisystem möglicherweise eingebunden, es kann aber nicht darauf zugegriffen werden.  

   Das Attribut `on-fail=fence` wird dem Überwachungsvorgang ebenfalls hinzugefügt. Durch diese Option wird ein Knoten sofort eingegrenzt, wenn beim Überwachungsvorgang für diesen Knoten ein Fehler auftritt.   

## <a name="create-sap-hana-cluster-resources"></a>Erstellen von SAP HANA-Clusterressourcen

1. **[1,2]** Installieren Sie den „Systemreplikationshook“ für HANA. Der Hook muss auf einem HANA DB-Knoten an jedem Systemreplikationsstandort installiert werden.         

   1. Bereiten Sie den Hook als `root` vor. 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Beenden Sie HANA an beiden Systemreplikationsstandorten. Führen Sie den Vorgang als „<sid\>adm“ aus:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Passen Sie `global.ini` an.
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[AH]** Der Cluster erfordert sudoers-Konfiguration auf dem Clusterknoten für <sid\>adm. In diesem Beispiel wird dies durch das Erstellen einer neuen Datei erreicht. Führen Sie die Befehle als `root` aus.    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1,2]** Starten Sie SAP HANA an beiden Replikationsstandorten. Führen Sie als <sid\>adm aus.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Überprüfen Sie die Installation des Hooks. Nehmen Sie die Ausführung als <sid\>adm auf dem aktiven Replikationsstandort des HANA-Systems vor.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** Erstellen Sie die HANA-Clusterressourcen. Führen Sie die folgenden Befehle als `root` aus.    
   1. Stellen Sie sicher, dass sich der Cluster bereits im Wartungsmodus befindet.  
    
   2. Als nächstes erstellen Sie die HANA-Topologieressource.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Als nächstes erstellen Sie die HANA-Instanzressource.  
      > [!NOTE] 
      > Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Es wird als bewährte Methode empfohlen, AUTOMATED_REGISTER nur auf **no** festzulegen, während gründliche Failovertests durchgeführt werden, um zu verhindern, dass sich eine fehlerhafte primäre Instanz automatisch als sekundäre Instanz registriert. Nachdem die Failover-Tests erfolgreich abgeschlossen sind, legen Sie AUTOMATED_REGISTER auf **yes** fest, sodass nach der Übernahme die Systemreplikation automatisch wieder aufgenommen werden kann. 

   4. Erstellen Sie die virtuelle IP-Adresse und zugehörige Ressourcen.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Erstellen der Clustereinschränkungen  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** Konfigurieren Sie zusätzliche Clustereigenschaften.   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** Überprüfen Sie die Kommunikation zwischen dem HOOK und dem Cluster.
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** Beenden Sie für den Cluster den Wartungsmodus. Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische HANA-Setup angepasst werden. Beispielsweise müssen Sie ggf. das Starttimeout erhöhen, wenn es länger dauert, bis die SAP HANA-Datenbank gestartet wird.
  

## <a name="test-sap-hana-failover"></a>Testen des SAP HANA-Failovers 

> [!NOTE]
> Dieser Artikel enthält Verweise auf die Begriffe *Master* und *Slave*, die von Microsoft nicht mehr verwendet werden. Sobald diese Begriffe aus der Software entfernt wurden, werden sie auch aus diesem Artikel gelöscht.

1. Bevor Sie einen Test starten, überprüfen Sie den Cluster und den Replikationsstatus des SAP HANA-Systems.    

   a. Überprüfen Sie, ob es keine fehlerhaften Clusteraktionen gibt.  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Überprüfen Sie, ob die SAP HANA-Systemreplikation synchronisiert ist.

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Es wird empfohlen, die SAP HANA-Clusterkonfiguration gründlich zu überprüfen, indem Sie die Tests durchführen, die unter [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter SUSE Linux Enterprise Server](./sap-hana-high-availability.md#test-the-cluster-setup) und [SLES SAP HANA-Systemreplikation (horizontale Skalierung) – leistungsoptimiertes Szenario](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster) dokumentiert sind.

3. Überprüfen Sie die Clusterkonfiguration auf ein Fehlerszenario, bei dem ein Knoten den Zugriff auf die NFS-Freigabe (`/hana/shared`) verliert.  

   Die SAP HANA-Ressourcen-Agents benötigen in `/hana/shared` gespeicherte Binärdateien, um während eines Failovers Vorgänge auszuführen. Das Dateisystem `/hana/shared` ist in der dargestellten Konfiguration über NFS eingebunden. Ein Test, der durchgeführt werden kann, besteht darin, eine temporäre Firewallregel zu erstellen, um den Zugriff auf das ANF-Volume `/hana/shared` auf einem der virtuellen Computer des primären Standorts zu blockieren. Auf diese Weise lässt sich überprüfen, ob der Cluster ein Failover ausführen wird, wenn der Zugriff auf `/hana/shared` am aktiven Systemreplikationsstandort verloren geht.  

   **Erwartetes Ergebnis**: Wenn Sie den Zugriff auf das ANF-Volume `/hana/shared` auf einem der virtuellen Computer des primären Standorts blockieren, schlägt der Überwachungsvorgang fehl, der Lese-/Schreibvorgänge für das Dateisystem ausführt, weil er nicht auf das Dateisystem zugreifen kann und ein HANA-Ressourcenfailover auslöst. Dasselbe Ergebnis ist zu erwarten, wenn der HANA-Knoten den Zugriff auf die NFS-Freigabe verliert.  
     
   Sie können den Zustand der Clusterressourcen prüfen, indem Sie `crm_mon` oder `crm status` ausführen. Zustand der Ressource vor dem Starten des Tests:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Um einen Fehler für `/hana/shared` zu simulieren, bestätigen Sie zunächst die IP-Adresse für das ANF-Volume `/hana/shared` am primären Standort. Dazu können Sie den Befehl `df -kh|grep /hana/shared` ausführen. 
   Richten Sie dann eine temporäre Firewallregel ein, um den Zugriff auf die IP-Adresse des ANF-Volumes `/hana/shared` zu blockieren, indem Sie auf einem der virtuellen Computer des primären HANA-Systemreplikationsstandorts den folgenden Befehl ausführen.
   In diesem Beispiel wurde der Befehl auf „hana-s1-db1“ ausgeführt.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Die Clusterressourcen werden zum anderen HANA-Systemreplikationsstandort migriert.    
              
   Wenn Sie AUTOMATED_REGISTER="false" festlegen, müssen Sie die SAP HANA-Systemreplikation am sekundären Standort konfigurieren. In diesem Fall können Sie diese Befehle ausführen, um SAP HANA als sekundär neu zu konfigurieren.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Der Zustand der Ressourcen nach dem Test: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* [NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA](./hana-vm-operations-netapp.md)
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
---
title: Horizontale SAP HANA-Skalierung mit HSR und Pacemaker unter RHEL | Microsoft-Dokumentation
description: SAP HANA Scale-Out mit HANA-Systemreplikation (HSR) und Pacemaker auf Red Hat Enterprise Linux (RHEL)
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/12/2021
ms.author: radeltch
ms.openlocfilehash: 74f9fef91149a34c189c696e1791ad6c035e963e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493645"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Hochverfügbarkeit der horizontalen SAP HANA-Skalierung unter Red Hat Enterprise Linux 

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Dieser Artikel beschreibt, wie man ein hochverfügbares SAP HANA-System in einer horizontale Skalierung-Konfiguration einsetzt. Konkret nutzt die Konfiguration die HANA-Systemreplikation (HSR) und Pacemaker auf Azure Red Hat Enterprise Linux Virtual Machines (VMs). [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) stellt in der vorgestellten Architektur die gemeinsam genutzten Dateisysteme zur Verfügung, die über Network File System (NFS) gemountet werden.  

In den Beispielkonfigurationen und Installationsbefehlen ist die HANA-Instanz `03` und die HANA-System-ID `HN1`. Die Beispiele basieren auf HANA 2.0 SP4 und Red Hat Enterprise Linux (RHEL) für SAP 7.6. 

## <a name="prerequisites"></a>Voraussetzungen

Einige Leser werden davon profitieren, eine Reihe von SAP-Hinweisen und -Ressourcen zu konsultieren, bevor sie mit den Themen in diesem Artikel weitermachen:

* SAP-Hinweis [1928533] enthält:  
  * Eine Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden.
  * Wichtige Kapazitätsinformationen für Azure-VM-Größen
  * Unterstützte SAP-Software, Betriebssystem- und Datenbankkombinationen.
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* SAP-Hinweis [2015553]: Listet die Voraussetzungen für SAP-unterstützte SAP-Softwareimplementierungen in Azure auf.
* SAP-Hinweis [2002167]: Enthält empfohlene Betriebssystemeinstellungen für RHEL.
* SAP-Hinweis [2009879]: Hat SAP HANA Richtlinien für RHEL.
* SAP-Hinweis [2178632]: Enthält detaillierte Informationen über alle Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498]: Enthält die erforderliche SAP-Host-Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692]: Enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1999351]: Enthält zusätzliche Informationen zur Fehlerbehebung für die erweiterte Azure-Überwachungserweiterung für SAP.
* SAP-Hinweis [1900823]: Enthält Informationen zu den Speicheranforderungen von SAP HANA.
* [SAP-Community-Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines Planung und Implementierung für SAP auf Linux][planning-guide].
* [Azure Virtual Machines Bereitstellung für SAP auf Linux][deployment-guide].
* [Azure Virtual Machines DBMS-Bereitstellung für SAP auf Linux][dbms-guide].
* [SAP HANA Netzwerkanforderungen](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html).
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über die hohe Verfügbarkeit der Zusatzmodule](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index).
  * [Hochverfügbare Add-on-Verwaltung](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index).
  * [Hochverfügbarkeits-Zusatzreferenz](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index).
  * [Red Hat Enterprise Linux Netzwerkhandbuch](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide).
  * [Wie konfiguriere ich SAP HANA horizontale Skalierung-Systemreplikation in einem Pacemaker-Cluster mit HANA-Dateisystemen auf NFS-Freigaben](https://access.redhat.com/solutions/5423971).
  * Aktiv/Aktiv (leseaktiviert) [: RHEL Hochverfügbarkeit-Lösung für SAP HANA Scale-out und Systemreplikation](https://access.redhat.com/sites/default/files/attachments/v8_ha_solution_for_sap_hana_scale_out_system_replication_1.pdf).
* Azure-spezifische RHEL-Dokumentation:
  * [SAP HANA auf Red Hat Enterprise Linux für die Verwendung in Microsoft Azure installieren](https://access.redhat.com/public-cloud/microsoft-azure).
  * [Red Hat Enterprise Linux Lösung für SAP HANA horizontale Skalierung und Systemreplikation](https://access.redhat.com/solutions/4386601).
* [NetApp SAP-Applikationen auf Microsoft Azure mit Azure NetApp Files][anf-sap-applications-azure].
* [Azure NetApp Files Dokumentation][anf-azure-doc]. 
* [NFS v4.1-Volumes auf Azure NetApp Files für SAP HANA](./hana-vm-operations-netapp.md).

## <a name="overview"></a>Übersicht

Um HANA-Hochverfügbarkeit für HANA horizontale Skalierung -Installationen zu erreichen, können Sie die HANA-Systemreplikation konfigurieren und die Lösung mit einem Pacemaker-Cluster schützen, um ein automatisches Failover zu ermöglichen. Wenn ein aktiver Knoten ausfällt, führt der Clusterknoten einen Failover zu den HANA-Ressourcen am anderen Standort aus.  

Im folgenden Diagramm gibt es drei HANA-Knoten an jedem Standort und einen Majority-Maker-Knoten, um ein "Split-Brain"-Szenario zu verhindern. Die Anweisungen können angepasst werden, um mehr VMs als HANA DB-Knoten einzubeziehen.  

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) stellt das gemeinsame HANA-Dateisystem zur Verfügung, `/hana/shared`. Es wird über NFS v4.1 auf jedem HANA-Knoten in derselben HANA-Systemreplikations-Site eingehängt. Die Dateisysteme `/hana/data` und `/hana/log` sind lokale Dateisysteme und werden von den HANA DB-Knoten nicht gemeinsam genutzt. SAP HANA wird im nicht freigegebenen Modus installiert. 

> [!TIP]
> Informationen zu empfohlenen SAP HANA-Speicherkonfigurationen finden Sie unter [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md).   

[![Diagramm von SAP HANA horizontale Skalierung mit HSR und Pacemaker-Cluster ](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Das vorstehende Diagramm zeigt drei Subnetze, die innerhalb eines virtuellen Azure-Netzwerks dargestellt werden, entsprechend den SAP HANA-Netzwerkempfehlungen: 

* Für die Kundenkommunikation: `client` 10.23.0.0/24  
* Für die interne HANA-Internode-Kommunikation: `inter` 10.23.1.128/26  
* Für die HANA-Systemreplikation: `hsr` 10.23.1.192/26  

Da `/hana/data` und `/hana/log` auf lokalen Datenträgern eingesetzt werden, ist es nicht erforderlich, ein separates Subnetz und separate virtuelle Netzwerkkarten für die Kommunikation mit dem Speicher einzusetzen.  

Die Azure NetApp-Volumes werden in einem separaten Subnetz bereitgestellt, das an [Azure NetApp Files delegiert wurde](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md): `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Einrichten der Infrastruktur

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits die Ressourcengruppe und das Azure Virtual Network mit drei Azure Virtual Network-Subnetzen erstellt haben: `client`, `inter` und `hsr`.

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal

1. Stellen Sie die virtuellen Azure-Computer bereit. Für diese Konfiguration stellen Sie sieben virtuelle Maschinen bereit: 
   
   - Drei virtuelle Maschinen, die als HANA-DB-Knoten für HANA-Replikationsstandort 1 dienen: **hana-s1-db1**, **hana-s1-db2** und **hana-s1-db3**.  
   - Drei virtuelle Maschinen, die als HANA-DB-Knoten für HANA-Replikationsstandort 2 dienen: **hana-s2-db1**, **hana-s2-db2** und **hana-s2-db3**.  
   - Eine kleine virtuelle Maschine, die als Mehrheitsbeschaffer dient: **hana-s-mm**.

   Die VMs, die als SAP DB HANA-Knoten eingesetzt werden, sollten von SAP für HANA zertifiziert sein, wie im [SAP HANA Hardware-Verzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) veröffentlicht. Wenn Sie die HANA DB-Knoten bereitstellen, stellen Sie sicher, dass Sie [beschleunigtes Netzwerk](../../../virtual-network/create-vm-accelerated-networking-cli.md) wählen.  
  
   Für den Major Maker Node können Sie eine kleine VM einsetzen, da diese VM keine SAP HANA-Ressourcen ausführt. Der Majority Maker VM wird in der Clusterkonfiguration verwendet, um eine ungerade Anzahl von Clusterknoten in einem Split-Brain-Szenario zu erreichen. Der virtuelle Majority Maker-Computer benötigt in diesem Beispiel nur eine virtuelle Netzwerkschnittstelle im Subnetz `client`.        

   Stellen Sie lokal verwaltete Datenträger für `/hana/data` und `/hana/log` bereit. Die empfohlene Mindestspeicherkonfiguration für `/hana/data` und `/hana/log` ist in der [Speicherkonfigurationen für SAP HANA Azure VMs](./hana-vm-operations-storage.md) beschrieben.

   Stellen Sie die primäre Netzwerkschnittstelle für die einzelnen virtuellen Computer im `client`-Subnetz des virtuellen Netzwerks bereit. Wenn der virtuelle Computer über das Azure-Portal bereitgestellt wird, erfolgt die automatische Generierung des Namens der Netzwerkschnittstelle. In diesem Artikel werden die automatisch generierten primären Netzwerkschnittstellen als **hana-s1-db1-client**, **hana-s1-db2-client**, **hana-s1-db3-client** und so weiter bezeichnet. Diese Netzwerkschnittstellen sind mit dem `client` Azure-Subnetz für virtuelle Netzwerke angeschlossen.  

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass das von Ihnen gewählte Betriebssystem für SAP HANA auf den von Ihnen verwendeten VM-Typen SAP-zertifiziert ist. Eine Liste der von SAP HANA zertifizierten VM-Typen und Betriebssystemversionen für diese Typen finden Sie unter [SAP HANA zertifizierte IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Gehen Sie in die Details des aufgeführten VM-Typs, um die vollständige Liste der von SAP HANA unterstützten Betriebssystemversionen für diesen Typ zu erhalten.  
  
1. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `inter`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-inter**, **hana-s1-db2-inter**, **hana-s1-db3-inter**, **hana-s2-db1-inter**, **hana-s2-db2-inter** und **hana-s2-db3-inter**).  

1. Erstellen Sie sechs Netzwerkschnittstellen, eine für jeden virtuellen HANA DB-Computer im `hsr`-Subnetz des virtuellen Netzwerks (in diesem Beispiel **hana-s1-db1-hsr**, **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr** und **hana-s2-db3-hsr**).  

1. Fügen Sie die neu erstellten virtuellen Netzwerkschnittstellen an die entsprechenden virtuellen Computer an:  

    1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/#home) zum virtuellen Computer.  

    1. Wählen Sie im linken Fensterbereich die Optionen **Virtuelle Maschinen**. Filtern Sie nach dem Namen des virtuellen Computers (z. B. **hana-s1-db1**), und wählen Sie dann den virtuellen Computer aus.  

    1. Wählen Sie im Fenster **Übersicht** die Option **Anhalten**, um die Zuordnung der virtuellen Maschine aufzuheben.  

    1. Wählen Sie **Netzwerk** aus, und fügen Sie dann die Netzwerkschnittstelle an. Wählen Sie in der Dropdown-Liste **Netzwerkschnittstelle anschließen** die bereits erstellten Netzwerkschnittstellen für die Subnetze `inter` und `hsr`.  
    
    1. Wählen Sie **Speichern** aus. 
 
    1. Wiederholen Sie die Schritte b bis e für die übrigen virtuellen Computer (in unserem Beispiel **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2** und **hana-s2-db3**).
 
    1. Lassen Sie die virtuellen Maschinen vorerst im gestoppten Zustand.   

1. Aktivieren Sie [beschleunigte Vernetzung](../../../virtual-network/create-vm-accelerated-networking-cli.md) für die zusätzlichen Netzwerkschnittstellen für die Subnetze `inter` und `hsr`, indem Sie wie folgt vorgehen:  

    1. Öffnen Sie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) im [Azure-Portal](https://portal.azure.com/#home).  

    1. Führen Sie die folgenden Befehle aus, um die beschleunigte Vernetzung für die zusätzlichen Netzwerkschnittstellen zu aktivieren, die mit den Subnetzen `inter` und `hsr` verbunden sind.  

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

1. Starten Sie die virtuellen HANA DB-Maschinen.

### <a name="deploy-azure-load-balancer"></a>Bereitstellen von Azure Load Balancer

Am besten ist es, den Standard-Loadbalancer zu verwenden. Gehen Sie dazu wie folgt vor:

1. Erstellen eines Front-End-IP-Pools:

    1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
    1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z.B. *hana-frontend*).
    1. Setzen Sie **Zuweisung** auf **Statisch**, und geben Sie die IP-Adresse ein (zum Beispiel *10.23.0.18*).
    1. Klicken Sie auf **OK**.
    1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.

1. Erstellen Sie einen Backend-Pool und fügen Sie alle Cluster-VMs zum Backend-Pool hinzu:

    1. Öffnen Sie den Lastenausgleich, und wählen Sie **Back-End-Pools** und dann **Hinzufügen** aus.
    1. Geben Sie den Namen des neuen Back-End-Pools ein (z.B. *hana-backend*).
    1. Wählen Sie **Eine virtuelle Maschine hinzufügen** > **Virtuelle Maschine**.
    1. Wählen Sie die virtuellen Computer des SAP HANA-Clusters und deren IP-Adressen für das `client`-Subnetz aus.
    1. Wählen Sie **Hinzufügen**.

1. Erstellen eines Integritätstests:

    1. Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
    1. Geben Sie den Namen des neuen Integritätstests ein (z.B. *hana-hp*).
    1. Wählen Sie als Protokoll **TCP** und als Port 625 **03** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
    1. Klicken Sie auf **OK**.

1. Erstellen Sie die Lastenausgleichsregeln:
   
    1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
    1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. *hana-lb*).
    1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **hana-frontend**, **hana-backend** und **hana-hp**).
    1. Wählen Sie **HA-Ports** aus.
    1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
    1. Klicken Sie auf **OK**.

   > [!IMPORTANT]
   > Floating IP wird bei einer NIC-Sekundär-IP-Konfiguration in Load-Balancing-Szenarien nicht unterstützt. Einzelheiten finden Sie unter [Azure Load Balancer Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie eine zusätzliche IP-Adresse für die VM benötigen, setzen Sie eine zweite Netzwerkkarte ein.    
   
Wenn Sie den Standard-Load-Balancer verwenden, sollten Sie sich der folgenden Einschränkung bewusst sein. Wenn Sie VMs ohne öffentliche IP-Adressen im Backend-Pool eines internen Load Balancers platzieren, gibt es keine ausgehende Internet-Konnektivität. Um das Routing zu öffentlichen Endpunkten zu ermöglichen, müssen Sie eine zusätzliche Konfiguration vornehmen. Weitere Informationen finden Sie unter [Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarios verwenden](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

   > [!IMPORTANT]
   > Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs, die sich hinter Azure Load Balancer befinden. Die Aktivierung von TCP-Zeitstempeln führt dazu, dass die Health Probes fehlschlagen. Setzen Sie den Parameter `net.ipv4.tcp_timestamps` auf `0`. Details finden Sie unter [Load Balancer Health Probes](../../../load-balancer/load-balancer-custom-probe-overview.md) und SAP Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Bereitstellen der Infrastruktur für Azure NetApp Files 

Stellen Sie die Azure NetApp Files Volumes für das Dateisystem `/hana/shared` bereit. Sie benötigen ein separates `/hana/shared` Volume für jeden HANA-Systemreplikationsstandort. Weitere Informationen finden Sie unter [Einrichten der Infrastruktur für Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

In diesem Beispiel verwenden Sie die folgenden Azure NetApp Files Volumes: 

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

Konfigurieren Sie Ihr Betriebssystem wie folgt und bereiten Sie es vor:

1. **[A]** Verwalten Sie die Hostdateien auf den virtuellen Computern. Schließen Sie Einträge für alle Subnetze ein. Für dieses Beispiel werden die folgenden Einträge zu `/etc/hosts` hinzugefügt.  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```

1. **[A]** Bereiten Sie das Betriebssystem für den Betrieb von SAP HANA vor. Weitere Informationen finden Sie unter [NetApp-SAP-Anwendungen in Microsoft Azure mithilfe von Azure NetApp Files][anf-sap-applications-azure]. Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/netapp-hana.conf* für die Konfigurationseinstellungen von Azure NetApp Files.  

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

1. **[A]** Erstellen Sie die Konfigurationsdatei */etc/sysctl.d/ms-az.conf* mit weiteren Optimierungseinstellungen.  

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
    > Vermeiden Sie es, `net.ipv4.ip_local_port_range` und `net.ipv4.ip_local_reserved_ports` explizit in den `sysctl` Konfigurationsdateien zu setzen, damit der SAP-Host-Agent die Portbereiche verwalten kann. Weitere Einzelheiten finden Sie im SAP-Hinweis [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

1. **[A]** Passen Sie die `sunrpc` Einstellungen an, wie in [NetApp SAP-Anwendungen auf Microsoft Azure mit Azure NetApp Files][anf-sap-applications-azure] empfohlen.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

1. **[A]** Installieren Sie das NFS-Clientpaket.  

   `yum install nfs-utils`


1. **[AH]** Red Hat für HANA-Konfiguration.  

   Konfigurieren Sie RHEL, wie im [Red Hat Kundenportal](https://access.redhat.com/solutions/2447641) und in den folgenden SAP-Hinweisen beschrieben:

   - [2292690 – SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 7)
   - [2777782 - SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: Running SAP applications compiled with GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582) (Linux – Ausführen von mit GCC 6.x kompilierten SAP-Anwendungen)
   - [2593824 – Linux: Ausführen von mit GCC 7.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: Ausführen von mit GCC 9.x kompilierten SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Vorbereiten der Dateisysteme

Die folgenden Abschnitte enthalten Schritte zur Vorbereitung Ihrer Dateisysteme.

### <a name="mount-the-shared-file-systems"></a>Einbinden der freigegebenen Dateisysteme

In diesem Beispiel werden die freigegebenen HANA-Dateisysteme auf Azure NetApp Files bereitgestellt und über NFS v4 eingehängt.  

1. **[AH]** Erstellen Sie Bereitstellungspunkte für die HANA-Datenbankvolumes.

    ```bash
    mkdir -p /hana/shared
    ```

1. **[AH]** Überprüfen Sie die Einstellung für die NFS-Domäne. Stellen Sie sicher, dass die Domäne als Standarddomäne von Azure NetApp Files konfiguriert ist: `defaultv4iddomain.com`. Stellen Sie sicher, dass die Zuordnung auf `nobody` eingestellt ist.  
   (Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFS v4.1 verwenden).  

    > [!IMPORTANT]
    > Stellen Sie sicher, dass die NFS-Domäne in `/etc/idmapd.conf` auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: `defaultv4iddomain.com` . Wenn die Domänenkonfiguration auf dem NFS-Client und dem NFS-Server nicht übereinstimmt, werden die Berechtigungen für Dateien auf Azure NetApp-Volumes, die auf den VMs eingehängt sind, als `nobody` angezeigt.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

1. **[AH]** Überprüfen Sie `nfs4_disable_idmapping`. Diese Angabe sollte auf `Y` (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter */sys/modules* nicht manuell erstellen, da der Zugriff für den Kernel oder die Treiber reserviert ist.  
   Dieser Schritt ist nur erforderlich, wenn Sie Azure NetAppFiles NFSv4.1 verwenden.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Weitere Informationen zur Änderung des `nfs4_disable_idmapping` Parameters finden Sie im [Red Hat Kundenportal](https://access.redhat.com/solutions/1749883).

1. **[AH1]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 1 ein.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

1. **[AH2]** Binden Sie die freigegebenen Azure NetApp Files-Volumes auf den virtuellen HANA DB-Computern von STANDORT 2 ein.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


1. **[AH]** Überprüfen Sie, ob die entsprechenden `/hana/shared/` Dateisysteme auf allen HANA DB VMs mit der NFS-Protokollversion **NFSv4** eingehängt sind.  

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

In der vorgestellten Konfiguration stellen Sie die Dateisysteme `/hana/data` und `/hana/log` auf einem verwalteten Datenträger bereit und verbinden diese Dateisysteme lokal mit jeder HANA DB VM. Führen Sie die folgenden Schritte aus, um die lokalen Daten- und Protokollvolumes auf jeder virtuellen HANA DB-Maschine zu erstellen. 

Richten Sie das Layout des Datenträgers mit **Logical Volume Manager (LVM)** ein. Im folgenden Beispiel wird davon ausgegangen, dass an jede virtuelle HANA-Maschine drei Datenträger angeschlossen sind und dass diese Datenträger zur Erstellung von zwei Volumes verwendet werden.

1. **[AH]** Auflisten aller verfügbaren Datenträger:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Beispielausgabe:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

1. **[AH]** Erstellen Sie physische Volumes für alle Datenträger, die Sie verwenden möchten:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** Erstellen Sie eine Volumegruppe für die Datendateien. Erstellen Sie eine Volumegruppe für Protokolldateien und eine für das freigegebene Verzeichnis von SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** Erstellen Sie die logischen Volumes. Ein *lineares* Volumen wird erstellt, wenn Sie `lvcreate` ohne den `-i` Schalter verwenden. Für eine bessere E/A-Leistung wird empfohlen, ein *Striped*-Volume zu erstellen. Passen Sie die Stripe-Größen an die in [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md) dokumentierten Werte an. Das `-i`-Argument sollte die Anzahl der zugrunde liegenden physischen Volumes und das `-I`-Argument die Stripegröße sein. In diesem Artikel werden zwei physische Volumes für das Data-Volume verwendet, daher wird das Schalterargument `-i` auf `2` gesetzt. Die Stripe-Größe für das Datenvolumen ist `256 KiB`. Für das Log-Volume wird ein physisches Volume verwendet, so dass Sie keine expliziten `-i`- oder `-I`-Schalter für die Log-Volume-Befehle verwenden müssen.  

   > [!IMPORTANT]
   > Verwenden Sie den Schalter `-i` und setzen Sie ihn auf die Nummer des zugrunde liegenden physischen Volumes, wenn Sie mehr als ein physisches Volume für jedes Daten- oder Log-Volume verwenden. Verwenden Sie den Schalter `-I`, um die Stripe-Größe anzugeben, wenn Sie ein Striping-Volume erstellen. Informationen zu empfohlenen Speicherkonfigurationen, einschließlich Stripegrößen und Anzahl der Datenträger, finden Sie unter [SAP HANA VM-Speicherkonfigurationen](./hana-vm-operations-storage.md).  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

1. **[AH]** Erstellen Sie die Bereitstellungsverzeichnisse, und kopieren Sie die UUID aller logischen Volumes:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

1. **[AH]** Erstellen Sie `fstab`-Einträge für die logischen Volumes, und binden Sie Folgendes an:
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

## <a name="installation"></a>Installation  

In diesem Beispiel für die Bereitstellung von SAP HANA in einer horizontale Skalierung-Konfiguration mit HSR auf Azure-VMs verwenden Sie HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Vorbereiten der HANA-Installation

1. **[AH]** Legen Sie vor der HANA-Installation das Stammkennwort fest. Nachdem die Installation abgeschlossen wurde, können Sie das Stammkennwort deaktivieren. Führen Sie als `root` den Befehl `passwd` aus, um das Passwort festzulegen.  

1. **[1,2]** Ändern Sie die Berechtigungen für `/hana/shared`. 
    ```bash
    chmod 775 /hana/shared
    ```

1. **[1]** Überprüfen Sie, ob Sie sich über eine sichere Shell (SSH) bei **hana-s1-db2** und **hana-s1-db3** anmelden können, ohne nach einem Passwort gefragt zu werden. Wenn das nicht der Fall ist, tauschen Sie `ssh` Schlüssel aus, wie in [Verwendung der schlüsselbasierten Authentifizierung](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs) dokumentiert.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

1. **[2]** Überprüfen Sie, ob Sie sich bei **hana-s2-db2** und **hana-s2-db3** über SSH anmelden können, ohne zur Eingabe eines Passworts aufgefordert zu werden. Wenn das nicht der Fall ist, tauschen Sie `ssh` Schlüssel aus, wie in [Verwendung der schlüsselbasierten Authentifizierung](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs) dokumentiert.  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

1. **[AH]** Installieren Sie zusätzliche Pakete, die für HANA 2.0 SP4 erforderlich sind. Weitere Informationen finden Sie im SAP-Hinweis [2593824](https://launchpad.support.sap.com/#/notes/2593824) für RHEL 7. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


1. **[A]** Deaktivieren Sie die Firewall vorübergehend, damit sie die HANA-Installation nicht stört. Sie können sie nach der HANA-Installation wieder aktivieren. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-Installation auf dem ersten Knoten an jedem Standort

1. **[1]** Installieren Sie SAP HANA, indem Sie den Anweisungen im [SAP HANA 2.0 Installations- und Update-Leitfaden](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) folgen. Die folgenden Anweisungen zeigen die SAP HANA-Installation auf dem ersten Knoten auf SITE 1.   

   1. Starten Sie das Programm `hdblcm` als `root` aus dem Verzeichnis der HANA-Installationssoftware. Verwenden Sie den Parameter `internal_network` und übergeben Sie den Adressraum für das Subnetz, der für die interne HANA-Internode-Kommunikation verwendet wird.  

      ```bash
      ./hdblcm --internal_network=10.23.1.128/26
      ```
   1. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Wählen Sie eine Aktion**, geben Sie **1** (für installieren).
     * Für **Zusätzliche Komponenten für die Installation**, geben Sie **2, 3** ein.
     * Drücken Sie für den Installationspfad die Eingabetaste (die Standardeinstellung ist */hana/shared*).
     * Drücken Sie für **Lokaler Hostname** die Eingabetaste, um die Standardeinstellung zu übernehmen.
     * Für **Möchten Sie dem System Hosts hinzufügen?** , geben Sie **n** ein.
     * Für **SAP HANA System ID**, geben Sie **HN1** ein.
     * Für **Instanznummer** [00], geben Sie **03** ein.
     * Für **Lokale Host-Arbeitergruppe** [Standard] drücken Sie die Eingabetaste, um den Standard zu übernehmen.
     * Für **Systemnutzung wählen / Index [4]** eingeben, **4** (für benutzerdefiniert).
     * Für **Speicherort der Datenvolumes** [/hana/data/HN1] drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Location of Log Volumes** [/hana/log/HN1] drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Möchten Sie die maximale Speicherbelegung beschränken?** [n], geben Sie **n** ein.
     * Für **Zertifikat Hostname Für Host hana-s1-db1** [hana-s1-db1], drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Geben Sie für **SAP Host Agent User (sapadm) Passwort** das Passwort ein.
     * Geben Sie für **Bestätigen Sie das SAP Host Agent User (sapadm) Passwort** das Passwort ein.
     * Geben Sie für **Systemadministrator (hn1adm) Passwort** das Passwort ein.
     * Für **Systemadministrator-Home-Verzeichnis** [/usr/sap/HN1/home] drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Systemadministrator-Anmeldeshell** [/bin/sh] drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Systemadministrator-Benutzer-ID** [1001], drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Eingabe ID der Benutzergruppe (sapsys)** [79], drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Geben Sie für **Systemdatenbank-Benutzer (System) Passwort** das Passwort des Systems ein.
     * Geben Sie für **Bestätigen Sie das Passwort des Systemdatenbankbenutzers (System)** das Passwort des Systems ein.
     * Für **Soll das System nach dem Neustart des Computers neu starten?** [n], geben Sie **n** ein. 
     * Für **Möchten Sie fortfahren (j/n)** , bestätigen Sie die Zusammenfassung und wenn alles gut aussieht, geben Sie **j** ein.

1. **[2]** Wiederholen Sie den vorhergehenden Schritt, um SAP HANA auf dem ersten Knoten an STANDORT 2 zu installieren.   

1. **[1,2]** Überprüfen Sie *global.ini*.  

   Zeigen Sie *global.ini* an und stellen Sie sicher, dass die Konfiguration für die interne SAP HANA-Internode-Kommunikation vorhanden ist. Überprüfen Sie den Abschnitt `communication`. Dieser sollte den Adressraum für das `inter`-Subnetz enthalten, und `listeninterface` sollte auf `.internal` festgelegt sein. Überprüfen Sie den Abschnitt `internal_hostname_resolution`. Er sollte die IP-Adressen für die virtuellen HANA-Computer enthalten, die zum `inter`-Subnetz gehören.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

1. **[1,2]** Bereiten Sie *global.ini* für die Installation in einer nicht gemeinsam genutzten Umgebung vor, wie in SAP-Hinweis [2080991](https://launchpad.support.sap.com/#/notes/0002080991) beschrieben.  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

1. **[1,2]** Starten Sie SAP HANA neu, um die Änderungen zu aktivieren.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

1. **[1,2]** Vergewissern Sie sich, dass die Client-Schnittstelle die IP-Adressen aus dem Subnetz `client` für die Kommunikation verwendet.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Informationen zur Verifizierung der Konfiguration finden Sie im SAP-Hinweis [2183363 - Konfiguration des internen SAP HANA-Netzwerks](https://launchpad.support.sap.com/#/notes/2183363).  

1. **[AH]** Ändern Sie die Berechtigungen für die Daten- und Protokollverzeichnisse, um einen HANA-Installationsfehler zu vermeiden.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

1. **[1]** Installieren Sie die sekundären HANA-Knoten. Die Beispielanweisungen in diesem Schritt beziehen sich auf STANDORT 1.  
   1. Starten Sie das residente Programm `hdblcm` als `root`.    
      ```bash
       cd /hana/shared/HN1/hdblcm
       ./hdblcm 
      ```

   1. Geben Sie an der Eingabeaufforderung folgende Werte ein:

     * Für **Wählen Sie eine Aktion**, geben Sie **2** (für Hosts hinzufügen).
     * Für **Geben Sie durch Komma getrennte Hostnamen ein, die Sie hinzufügen möchten**, geben Sie hana-s1-db2, hana-s1-db3 ein.
     * Für **Zusätzliche Komponenten für die Installation**, geben Sie **2, 3** ein.
     * Geben Sie für **den Root-Benutzernamen [root]** ein und drücken Sie die Eingabetaste, um die Standardeinstellung zu übernehmen.
     * Für **Wählen Sie Rollen für den Host 'hana-s1-db2' [1]** , wählen Sie 1 (für Arbeiter).
     * Für **Geben Sie die Host-Ausfallsicherungsgruppe für den Host 'hana-s1-db2' [Standard]** ein, drücken Sie die Eingabetaste, um den Standard zu akzeptieren.
     * Für **Geben Sie die Nummer der Speicherpartition für den Host 'hana-s1-db2' [\<\<assign automatically\>\>]** ein, drücken Sie die Eingabetaste, um den Standard zu übernehmen.
     * Geben Sie für **Arbeitergruppe für Host 'hana-s1-db2' [Standard]** ein und drücken Sie die Eingabetaste, um den Standard zu akzeptieren.
     * Für **Wählen Sie Rollen für den Host 'hana-s1-db3' [1]** , wählen Sie 1 (für Arbeiter).
     * Für **Geben Sie die Host-Failover-Gruppe für den Host 'hana-s1-db3' [Standard]** ein und drücken Sie die Eingabetaste, um den Standard zu akzeptieren.
     * Für **Geben Sie die Nummer der Speicherpartition für den Host 'hana-s1-db3' [\<\<assign automatically\>\>]** ein, drücken Sie die Eingabetaste, um den Standard zu übernehmen.
     * Geben Sie für **Arbeitergruppe für Host 'hana-s1-db3' [Standard]** ein und drücken Sie die Eingabetaste, um den Standard zu akzeptieren.
     * Geben Sie für **Systemadministrator (hn1adm) Passwort** das Passwort ein.
     * Geben Sie für **Enter SAP Host Agent User (sapadm) Passwort** das Passwort ein.
     * Geben Sie für **Bestätigen Sie das SAP Host Agent User (sapadm) Passwort** das Passwort ein.
     * Für **Zertifikat Hostname Für Host hana-s1-db2** [hana-s1-db2], drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Zertifikat Hostname Für Host hana-s1-db3** [hana-s1-db3], drücken Sie die Eingabetaste, um die Vorgabe zu übernehmen.
     * Für **Möchten Sie fortfahren (j/n)** , bestätigen Sie die Zusammenfassung und wenn alles gut aussieht, geben Sie **j** ein.

1. **[2]** Wiederholen Sie den vorhergehenden Schritt, um die sekundären SAP HANA-Knoten auf STANDORT 2 zu installieren.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurieren Sie die SAP HANA 2.0 Systemreplikation

Mit den folgenden Schritten können Sie die Systemreplikation einrichten:

1. **[1]** Konfigurieren Sie die Systemreplikation auf SITE 1:

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

1. **[2]** Konfigurieren Sie die Systemreplikation auf SITE 2:
    
   Registrieren Sie den zweiten Standort zum Starten der Replikation. Führen Sie den folgenden Befehl als „<hanasid\>adm“ aus:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

1. **[1]** Überprüfen Sie den Replikationsstatus und warten Sie, bis alle Datenbanken synchronisiert sind.

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

1. **[1,2]** Ändern Sie die HANA-Konfiguration so, dass die Kommunikation für die HANA-Systemreplikation über die virtuellen Netzwerkschnittstellen der HANA-Systemreplikation geleitet wird.   
   1. Stoppen Sie HANA an beiden Standorten.
      ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
      ```

   1. Bearbeiten Sie *global.ini*, um die Hostzuordnung für die HANA-Systemreplikation hinzuzufügen. Verwenden Sie die IP-Adressen aus dem Subnetz `hsr`.  
      ```bash
      sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
      #Add the section
      [system_replication_hostname_resolution]
      10.23.1.202 = hana-s1-db1
      10.23.1.203 = hana-s1-db2
      10.23.1.204 = hana-s1-db3
      10.23.1.205 = hana-s2-db1
      10.23.1.206 = hana-s2-db2
      10.23.1.207 = hana-s2-db3
      ```

   1. Starten Sie HANA an beiden Standorten.
     ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
     ```

   Weitere Informationen finden Sie unter [Hostnamenauflösung für Systemreplikation](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

1. **[AH]** Aktivieren Sie die Firewall wieder und öffnen Sie die erforderlichen Ports.  
   1. Aktivieren Sie die Firewall wieder.
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   1. Öffnen Sie die erforderlichen Firewallports. Sie müssen die Ports für Ihre HANA-Instanznummer anpassen.  

       > [!IMPORTANT]
       > Erstellen Sie Firewall-Regeln, um die HANA-Internode-Kommunikation und den Client-Datenverkehr zuzulassen. Die erforderlichen Ports sind auf [TCP/IP-Ports aller SAP-Produkte](https://help.sap.com/viewer/ports) aufgeführt. Die folgenden Befehle dienen lediglich als Beispiel. In diesem Szenario verwenden Sie die Systemnummer 03.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Erstellen eines Pacemaker-Clusters

Um einen einfachen Pacemaker-Cluster für diesen HANA-Server zu erstellen, folgen Sie den Schritten in [Einrichten von Pacemaker auf Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md). Beziehen Sie alle virtuellen Computer ein, einschließlich Majority Maker im Cluster.  

> [!IMPORTANT]
> Setzen Sie `quorum expected-votes` nicht auf 2. Dies ist kein Zwei-Knoten-Cluster. Vergewissern Sie sich, dass die Clustereigenschaft `concurrent-fencing` aktiviert ist, damit das Knotenfencing deserialisiert wird.   

## <a name="create-file-system-resources"></a>Erstellen von Dateisystemressourcen

Für den nächsten Teil dieses Prozesses müssen Sie Dateisystemressourcen erstellen. Gehen Sie dazu wie folgt vor:

1. **[1,2]** Beenden Sie SAP HANA an beiden Replikationsstandorten. Als <sid\>adm ausführen.  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

1. **[AH]** Trennen Sie das Dateisystem `/hana/shared`, das für die Installation auf allen HANA DB VMs vorübergehend eingehängt wurde. Bevor Sie es aushängen können, müssen Sie alle Prozesse und Sitzungen stoppen, die das Dateisystem verwenden. 
 
    ```bash
    umount /hana/shared 
    ```

1. **[1]** Erstellen Sie die Dateisystem-Cluster-Ressourcen für `/hana/shared` im deaktivierten Zustand. Sie verwenden `--disabled`, weil Sie die Ortsbeschränkungen definieren müssen, bevor die Montierungen aktiviert werden.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   Das Attribut `OCF_CHECK_LEVEL=20` wird der Überwachungsoperation hinzugefügt, so dass die Überwachungsoperationen einen Lese-/Schreibtest des Dateisystems durchführen. Ohne dieses Attribut überprüft der Überwachungsvorgang nur, ob das Dateisystem eingebunden ist. Dies kann ein Problem darstellen, denn wenn die Verbindung unterbrochen wird, kann das Dateisystem eingehängt bleiben, obwohl es nicht zugänglich ist.  

   Das Attribut `on-fail=fence` wird auch dem Überwachungsvorgang hinzugefügt. Durch diese Option wird ein Knoten sofort eingegrenzt, wenn beim Überwachungsvorgang für diesen Knoten ein Fehler auftritt. Ohne diese Option werden standardmäßig alle von der ausgefallenen Ressource abhängigen Ressourcen gestoppt, dann die ausgefallene Ressource neu gestartet und anschließend alle von der ausgefallenen Ressource abhängigen Ressourcen gestartet. Dieses Verhalten kann nicht nur sehr lange dauern, wenn eine SAP HANA-Ressource von der ausgefallenen Ressource abhängt, sondern es kann auch ganz ausfallen. Die SAP HANA-Ressource kann nicht erfolgreich gestoppt werden, wenn die NFS-Freigabe mit den HANA-Binärdateien unzugänglich ist.  

1. **[1]** Konfigurieren und überprüfen Sie die Knotenattribute. Allen SAP HANA DB-Knoten am Replikationsstandort 1 wird das Attribut `S1` zugewiesen, und allen SAP HANA DB-Knoten am Replikationsstandort 2 wird das Attribut `S2` zugewiesen.  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

1. **[1]** Konfigurieren Sie die Beschränkungen, die bestimmen, wo die NFS-Dateisysteme eingehängt werden, und aktivieren Sie die Dateisystemressourcen.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Wenn Sie die Dateisystemressourcen aktivieren, wird der Cluster die `/hana/shared`-Dateisysteme einbinden.
 
1. **[AH]** Stellen Sie sicher, dass die Azure NetApp Files Volumes unter `/hana/shared` auf allen HANA DB VMs an beiden Standorten eingehängt sind.

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

1. **[1]** Konfigurieren und klonen Sie die Attribut-Ressourcen und konfigurieren Sie die Constraints wie folgt:  

    ```bash
    # Configure the attribute resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribute resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Wenn Ihre Konfiguration andere Dateisysteme als /`hana/shared` enthält und diese Dateisysteme per NFS eingebunden sind, fügen Sie die Option `sequential=false` hinzu. Diese Option stellt sicher, dass es keine Ordnungsabhängigkeiten zwischen den Dateisystemen gibt. Alle per NFS eingehängten Dateisysteme müssen vor der entsprechenden Attributressource beginnen, aber sie müssen nicht in einer bestimmten Reihenfolge relativ zueinander beginnen. Weitere Informationen finden Sie unter [Wie konfiguriere ich SAP HANA horizontale Skalierung HSR in einem Pacemaker-Cluster, wenn die HANA-Dateisysteme NFS-Freigaben sind](https://access.redhat.com/solutions/5423971).  

1. **[1]** Versetzen Sie Pacemaker in den Wartungsmodus, um die Erstellung der HANA-Cluster-Ressourcen vorzubereiten.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Erstellen von SAP HANA-Clusterressourcen

Jetzt können Sie die Cluster-Ressourcen erstellen:

1. **[A]** Installieren Sie den HANA-Ressourcen-Agent für die horizontale Skalierung auf allen Clusterknoten, einschließlich Majority Maker.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Die minimal unterstützte Version des Pakets `resource-agents-sap-hana-scaleout` für Ihre Betriebssystemversion finden Sie unter [Support-Richtlinien für RHEL Hochverfügbarkeit-Cluster - Verwaltung von SAP HANA in einem Cluster](https://access.redhat.com/articles/3397471) .  

1. **[1,2]** Installieren Sie den HANA-Systemreplikations-Hook auf einem HANA-DB-Knoten an jedem Systemreplikationsstandort. SAP HANA sollte immer noch nicht verfügbar sein.        

   1. Bereiten Sie den Hook als `root` vor. 
      ```bash
       mkdir -p /hana/shared/myHooks
       cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
       chown -R hn1adm:sapsys /hana/shared/myHooks
      ```

   1. Einstellen `global.ini`.
      ```bash
      # add to global.ini
      [ha_dr_provider_SAPHanaSR]
      provider = SAPHanaSR
      path = /hana/shared/myHooks
      execution_order = 1
    
      [trace]
      ha_dr_saphanasr = info
      ```

1. **[AH]** Der Cluster erfordert sudoers-Konfiguration auf dem Clusterknoten für <sid\>adm. In diesem Beispiel erreichen Sie dies, indem Sie eine neue Datei anlegen. Führen Sie die Befehle als `root` aus.    
    ```bash
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    Defaults!SOK, SFAIL !requiretty
    ```

1. **[1,2]** Starten Sie SAP HANA an beiden Replikationsstandorten. Als <sid\>adm ausführen.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

1. **[1]** Überprüfen Sie die Installation des Hooks. Führen Sie als <sid\>adm auf dem aktiven HANA-Systemreplikationsstandort aus.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

1. **[1]** Erstellen Sie die HANA-Clusterressourcen. Führen Sie die folgenden Befehle als `root` aus.    
   1. Stellen Sie sicher, dass sich der Cluster bereits im Wartungsmodus befindet.  
    
   1. Als nächstes erstellen Sie die HANA-Topologieressource.  
      Wenn Sie einen RHEL **7.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Wenn Sie einen RHEL **8.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   1. Erstellen Sie die HANA-Instanzressource.  
      > [!NOTE]
      > Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.  
 
      Wenn Sie einen RHEL **7.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Wenn Sie einen RHEL **8.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Es ist eine gute Idee, `AUTOMATED_REGISTER` auf `false` zu setzen, während Sie Failover-Tests durchführen, um zu verhindern, dass sich eine ausgefallene primäre Instanz automatisch als sekundäre registriert. Setzen Sie nach dem Test am besten `AUTOMATED_REGISTER` auf `true`, damit die Systemreplikation nach der Übernahme automatisch fortgesetzt werden kann. 

   1. Erstellen Sie die virtuelle IP und die zugehörigen Ressourcen.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   1. Erstellen Sie die Cluster-Constraints.  
      Wenn Sie einen RHEL **7.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Wenn Sie einen RHEL **8.x**-Cluster erstellen, verwenden Sie die folgenden Befehle:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

1. **[1]** Beenden Sie für den Cluster den Wartungsmodus. Vergewissern Sie sich, dass der Cluster-Status `ok` ist und dass alle Ressourcen gestartet sind.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Die Zeitüberschreitungen in der vorstehenden Konfiguration sind nur Beispiele und müssen möglicherweise an die spezifische HANA-Einrichtung angepasst werden. Sie müssen beispielsweise den Start-Timeout erhöhen, wenn es länger dauert, die SAP HANA-Datenbank zu starten.
  
## <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurieren Sie die aktive/lesefähige Systemreplikation von HANA

Ab SAP HANA 2.0 SPS 01 erlaubt SAP aktive/lesefähige Setups für die SAP HANA-Systemreplikation. Mit dieser Fähigkeit können Sie die sekundären Systeme der SAP HANA-Systemreplikation aktiv für leseintensive Workloads nutzen. Um ein solches Setup in einem Cluster zu unterstützen, benötigen Sie eine zweite virtuelle IP-Adresse, über die Clients auf die sekundäre, lesefähige SAP HANA-Datenbank zugreifen können. Um sicherzustellen, dass der sekundäre Replikationsstandort auch nach einer Übernahme noch erreichbar ist, muss der Cluster die virtuelle IP-Adresse mit der sekundären der SAP HANA-Ressource verschieben.

Dieser Abschnitt beschreibt die zusätzlichen Schritte, die Sie unternehmen müssen, um diese Art der Systemreplikation in einem Red Hat Hochverfügbarkeitscluster mit einer zweiten virtuellen IP-Adresse zu verwalten.  

Bevor Sie fortfahren, stellen Sie sicher, dass Sie einen Red Hat-Hochverfügbarkeitscluster, der eine SAP HANA-Datenbank verwaltet, vollständig konfiguriert haben, wie weiter oben in diesem Artikel beschrieben.  

![SAP HANA Scale-Out-Hochverfügbarkeit mit lesefähigem Sekundärteil](./media/sap-hana-high-availability-rhel/sap-hana-high-avalability-scale-out-hsr-rhel-read-enabled.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Zusätzliche Einrichtung in Azure Load Balancer für aktive/lesefähige Einrichtung

Um mit der Bereitstellung Ihrer zweiten virtuellen IP fortzufahren, stellen Sie sicher, dass Sie Azure Load Balancer wie in [Einrichten von Azure Load Balancer](#deploy-azure-load-balancer) beschrieben konfiguriert haben.

Für den *Standard*-Loadbalancer führen Sie die folgenden zusätzlichen Schritte auf demselben Loadbalancer aus, den Sie im vorherigen Abschnitt erstellt haben.

1. Erstellen eines zweiten Front-End-IP-Pools: 

   1. Öffnen Sie den Lastenausgleich, und wählen Sie den **Front-End-IP-Pool** und dann **Hinzufügen** aus.
   1. Geben Sie den Namen des zweiten Front-End-IP-Pools ein (z. B. *hana-secondaryIP*).
   1. Setzen Sie **Zuweisung** auf **Statisch**, und geben Sie die IP-Adresse ein (zum Beispiel **10.23.0.19**).
   1. Klicken Sie auf **OK**.
   1. Notieren Sie nach Erstellen des neuen Front-End-IP-Pools dessen IP-Adresse.

1. Erstellen Sie als Nächstes einen Integritätstest:

   1. Öffnen Sie den Lastenausgleich, und wählen Sie **Integritätstests** und dann **Hinzufügen** aus.
   1. Geben Sie den Namen des neuen Integritätstests ein (z. B. *hana-secondaryhp*).
   1. Wählen Sie **TCP** als Protokoll und den Port **62603** aus. Behalten Sie für das **Intervall** den Wert „5“ und als **Fehlerschwellenwert** „2“ bei.
   1. Klicken Sie auf **OK**.

1. Erstellen Sie als Nächstes die Lastenausgleichsregeln:

   1. Öffnen Sie den Lastenausgleich, und wählen Sie **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
   1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. *hana-secondarylb*).
   1. Wählen Sie die Front-End-IP-Adresse, den Back-End-Pool und die zuvor erstellte Integritätsprüfung aus (zum Beispiel **hana-secondaryIP**, **hana-backend** und **hana-secondaryhp**).
   1. Wählen Sie **HA-Ports** aus.
   1. Achten Sie darauf, dass Sie **„Floating IP“ aktivieren**.
   1. Klicken Sie auf **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurieren Sie die aktive/lesefähige Systemreplikation von HANA

Die Schritte zur Konfiguration der HANA-Systemreplikation sind im Abschnitt [Konfiguration der SAP HANA 2.0-Systemreplikation](#configure-sap-hana-20-system-replication) beschrieben. Wenn Sie ein lesefähiges sekundäres Szenario einrichten, führen Sie während der Konfiguration der Systemreplikation auf dem zweiten Knoten folgenden Befehl als **hanasid** adm aus:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2 --operationMode=logreplay_readaccess 
```

### <a name="add-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Hinzufügen einer sekundären virtuellen IP-Adressressressressource für eine aktive/leseaktivierte Einrichtung

Sie können die zweite virtuelle IP und die zusätzlichen Beschränkungen mit den folgenden Befehlen konfigurieren. Wenn die sekundäre Instanz ausgefallen ist, wird die sekundäre virtuelle IP auf die primäre umgestellt.   

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.23.0.19"
pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603
pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

# RHEL 8.x: 
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  SAPHana_HN1_HDB03-clone then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave SAPHana_HN1_HDB03-clone 5

# RHEL 7.x:
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  msl_SAPHana_HN1_HDB03 then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave msl_SAPHana_HN1_HDB03 5

pcs property set maintenance-mode=false
```
Vergewissern Sie sich, dass der Cluster-Status `ok` ist und dass alle Ressourcen gestartet sind. Die zweite virtuelle IP wird auf dem sekundären Standort zusammen mit der sekundären SAP HANA-Ressource ausgeführt.

```
# Example output from crm_mon
#Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#
#Active resources:
#
#rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
#Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
#    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
#    Masters: [ hana-s1-db1 ]
#    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Resource Group: g_ip_HN1_03
#    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
#    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
#Resource Group: g_secip_HN1_03
#    secnc_HN1_03       (ocf::heartbeat:azure-lb):      Started hana-s2-db1
#    secvip_HN1_03      (ocf::heartbeat:IPaddr2):       Started hana-s2-db1

```

Im nächsten Abschnitt finden Sie eine Reihe typischer Failover-Tests, die Sie durchführen können.

Wenn Sie einen HANA-Cluster testen, der mit einer leseaktivierten sekundären IP konfiguriert ist, sollten Sie das folgende Verhalten der zweiten virtuellen IP beachten:

- Wenn die Clusterressource **SAPHANA_HN1_HDB03** zum sekundären Standort (**S2**) wechselt, wechselt die zweite virtuelle IP zum anderen Standort, **hana-s1-db1**. Wenn Sie `AUTOMATED_REGISTER="false"` konfiguriert haben und die HANA-Systemreplikation nicht automatisch registriert wird, dann läuft die zweite virtuelle IP auf **hana-s2-db1**.  

- Wenn Sie einen Serverabsturz testen, laufen die zweite virtuelle IP-Ressource (**secvip_HN1_03**) und die Azure Load Balancer Port-Ressource (**secnc_HN1_03**) auf dem primären Server neben den primären virtuellen IP-Ressourcen. Während der sekundäre Server ausgefallen ist, stellen die Anwendungen, die mit einer HANA-Datenbank mit Lesezugriff verbunden sind, eine Verbindung mit der primären HANA-Datenbank her. Dies ist das erwartete Verhalten. Sie ermöglicht den Betrieb von Anwendungen, die mit der lesefähigen HANA-Datenbank verbunden sind, während ein sekundärer Server nicht verfügbar ist.   
  
- Während Failover und Fallback können die bestehenden Verbindungen für Anwendungen, die die zweite virtuelle IP zur Verbindung mit der HANA-Datenbank verwenden, unterbrochen werden.  

## <a name="test-sap-hana-failover"></a>Testen des SAP HANA-Failovers 

1. Bevor Sie einen Test starten, überprüfen Sie den Cluster und den Replikationsstatus des SAP HANA-Systems.  

   1. Überprüfen Sie, ob es keine fehlgeschlagenen Cluster-Aktionen gibt.  
       ```bash
       #Verify that there are no failed cluster actions
       pcs status
       # Example
       #Stack: corosync
       #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
       #Last updated: Thu Sep 24 06:00:20 2020
       #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
       #
       #7 nodes configured
       #45 resources configured
       #
       #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #
       #Active resources:
       #
       #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
       #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
       #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
       #    Masters: [ hana-s1-db1 ]
       #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Resource Group: g_ip_HN1_03
       #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
       #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
       ```

   1. Überprüfen Sie, ob die SAP HANA-Systemreplikation synchron ist.

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

1. Überprüfen Sie die Clusterkonfiguration auf ein Fehlerszenario, bei dem ein Knoten den Zugriff auf die NFS-Freigabe (`/hana/shared`) verliert.  

   Die SAP HANA-Ressourcenagenten hängen von Binärdateien ab, die auf `/hana/shared` gespeichert sind, um Operationen während des Failover durchzuführen. Das Dateisystem `/hana/shared` ist in der dargestellten Konfiguration über NFS eingebunden. Ein Test, den Sie durchführen können, besteht darin, das `/hana/shared` Dateisystem als *Nur-Lesen* neu einzuhängen. Auf diese Weise lässt sich überprüfen, ob der Cluster ein Failover ausführen wird, wenn der Zugriff auf `/hana/shared` am aktiven Systemreplikationsstandort verloren geht.  

   **Erwartetes Ergebnis**: Wenn Sie `/hana/shared` als *Nur-Lesen* neu einhängen, schlägt die Überwachungsoperation, die eine Lese-/Schreiboperation auf dem Dateisystem durchführt, fehl. Der Grund dafür ist, dass er nicht in das Dateisystem schreiben kann und eine Ausfallsicherung der HANA-Ressourcen auslöst. Dasselbe Ergebnis ist zu erwarten, wenn der HANA-Knoten den Zugriff auf die NFS-Freigabe verliert.  
     
   Sie können den Zustand der Cluster-Ressourcen überprüfen, indem Sie `crm_mon` oder `pcs status` ausführen. Zustand der Ressource vor dem Starten des Tests:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Um den Ausfall von `/hana/shared` auf einer der primären Replikations-Site-VMs zu simulieren, führen Sie den folgenden Befehl aus:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the preceding command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   Die HANA-VM, die den Zugriff auf `/hana/shared` verloren hat, sollte neu gestartet oder gestoppt werden, je nach Clusterkonfiguration. Die Clusterressourcen werden zum anderen Replikationsstandort des HANA-Systems migriert.  
         
   Wenn der Cluster auf der neu gestarteten VM noch nicht gestartet wurde, starten Sie den Cluster mit folgendem Befehl: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   Beim Start des Clusters wird das Dateisystem `/hana/shared` automatisch eingehängt. Wenn Sie `AUTOMATED_REGISTER="false"` einstellen, müssen Sie die SAP HANA-Systemreplikation auf dem sekundären Standort konfigurieren. In diesem Fall können Sie diese Befehle ausführen, um SAP HANA als sekundär zu rekonfigurieren.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and clean up failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Der Zustand der Ressourcen nach dem Test: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Es ist ratsam, die SAP HANA-Cluster-Konfiguration gründlich zu testen, indem Sie auch die in [Hochverfügbarkeit für SAP HANA auf Azure VMs auf RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup) dokumentierten Tests durchführen.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* [NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA](./hana-vm-operations-netapp.md)
* Wie Sie Hochverfügbarkeit einrichten und die Wiederherstellung von SAP HANA auf Azure VMs planen können, erfahren Sie unter [Hochverfügbarkeit von SAP HANA auf Azure VMs][sap-hana-ha].

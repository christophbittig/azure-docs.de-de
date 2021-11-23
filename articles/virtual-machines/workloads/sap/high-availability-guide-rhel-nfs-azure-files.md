---
title: Hochverfügbarkeit virtueller Azure-Computer für SAP NW unter RHEL mit NFS in Azure Files | Microsoft-Dokumentation
description: Richten Sie Hochverfügbarkeit für SAP NW auf virtuellen Azure-Computern (VMs) unter RHEL mit NFS in Azure Files ein.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: badc6696936b3cdf99b91a5abf0d95217ec9b2e7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557508"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-with-nfs-on-azure-files"></a>Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux mit NFS in Azure Files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2772999]:https://launchpad.support.sap.com/#/notes/2772999
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html


[sap-hana-ha]:sap-hana-high-availability-rhel.md


In diesem Artikel erfahren Sie, wie Sie virtuelle Computer bereitstellen und konfigurieren, das Clusterframework installieren und ein SAP NetWeaver-System mit Hochverfügbarkeit unter Verwendung von [NFS in Azure Files](../../../storage/files/files-nfs-protocol.md) installieren. In den Beispielkonfigurationen werden virtuelle Computer unter Red Hat Enterprise Linux (SLES) verwendet.  

## <a name="prerequisites"></a>Voraussetzungen

* [Dokumentation zu Azure Files][afs-azure-doc] 
* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure
* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2002167] enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux 7.x.
* SAP-Hinweis [2772999] enthält empfohlene Betriebssystemeinstellungen für Red Hat Enterprise Linux 8.x.
* SAP-Hinweis [2009879] enthält SAP HANA-Richtlinien für Red Hat Enterprise Linux.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung zur verbesserten Überwachung für SAP.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* [Azure Virtual Machines – Planung und Implementierung für SAP unter Linux][planning-guide]
* [Bereitstellung von Azure Virtual Machines für SAP unter Linux][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP unter Linux][dbms-guide]
* [SAP NetWeaver im Pacemaker-Cluster](https://access.redhat.com/articles/3150081)
* Allgemeine RHEL-Dokumentation:
  * [Übersicht über das Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Verwaltung des Hochverfügbarkeits-Add-Ons](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenz zum Hochverfügbarkeits-Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurieren von ASCS/ERS für SAP NetWeaver mit eigenständigen Ressourcen in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurieren von SAP S/4HANA ASCS/ERS mit eigenständigem Enqueue-Server 2 (ENSA2) in Pacemaker unter RHEL](https://access.redhat.com/articles/3974941)
* Azure-spezifische RHEL-Dokumentation:
  * [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Virtuelle Microsoft Azure-Computer als Clustermitglieder](https://access.redhat.com/articles/3131341)
  * [Installieren und Konfigurieren eines Red Hat Enterprise Linux 7.4-Hochverfügbarkeitclusters (und höher) in Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Übersicht

Zum Bereitstellen der SAP NetWeaver-Anwendungsschicht müssen in der Umgebung freigegebene Verzeichnisse wie `/sapmnt/SID` und `/usr/sap/trans` vorhanden sein. Beim Bereitstellen eines SAP-Systems mit Hochverfügbarkeit müssen außerdem Dateisysteme wie `/sapmnt/SID` und `/usr/sap/SID/ASCS` geschützt und hochverfügbar gemacht werden.

Diese Dateisysteme können jetzt in [NFS in Azure Files](../../../storage/files/files-nfs-protocol.md) platziert werden. NFS in Azure Files ist eine Speicherlösung mit Hochverfügbarkeit. Diese Lösung bietet synchronen zonenredundanten Speicher (ZRS) und eignet sich für SAP ASCS/ERS-Instanzen, die über mehrere Verfügbarkeitszonen hinweg bereitgestellt werden.  Sie benötigen allerdings weiterhin einen Pacemaker-Cluster, um Single Point of Failure-Komponenten wie die zentralen Dienste von SAP NetWeaver (ASCS/SCS) zu schützen.  

In den Beispielkonfigurationen und Installationsbefehlen werden die folgenden Instanznummern verwendet:

| Instanzname | Instanznummer |
| ---------------- | ------------------ |
| ABAP SAP Central Services (ASCS) | 00 |
| ERS | 01 |
| Primärer Anwendungsserver (Primary Application Server, PAS) | 02 |
| Zusätzlicher Anwendungsserver (Additional Application Server, AAS) | 03 |
| SAP-Systembezeichner | NW1 |

:::image type="complex" source="./media/high-availability-guide-rhel/high-availability-guide-rhel-nfs-azure-files.png" alt-text="Hochverfügbarkeit von SAP NetWeaver mit NFS in Azure Files":::
   Dieses Diagramm zeigt eine typische SAP NetWeaver-Architektur mit Hochverfügbarkeit. Die Dateisysteme „sapmnt“ und „saptrans“ werden auf NFS-Freigaben in Azure Files bereitgestellt. Die zentralen SAP-Dienste werden durch einen Pacemaker-Cluster geschützt. Die gruppierten virtuellen Computer befinden sich hinter einer Azure Load Balancer-Instanz. Die NFS-Freigaben werden über einen privaten Endpunkt eingebunden.
:::image-end:::

## <a name="prepare-infrastructure"></a>Infrastruktur vorbereiten

In diesem Dokument wird davon ausgegangen, dass Sie bereits ein [virtuelles Azure-Netzwerk](../../../virtual-network/virtual-networks-overview.md), ein Subnetz und eine Ressourcengruppe bereitgestellt haben.

1. Stellen Sie Ihre virtuellen Computer bereit. Sie können virtuelle Computer in Verfügbarkeitsgruppen oder in Verfügbarkeitszonen bereitstellen, sofern die Azure-Region diese Optionen unterstützt. Wenn Sie zusätzliche IP-Adressen für Ihre virtuellen Computer benötigen, können Sie eine zweite NIC bereitstellen und anfügen. Fügen Sie der primären NIC keine sekundären IP-Adressen hinzu. [Dieses Szenario wird vom Floating IP-Feature von Azure Load Balancer nicht unterstützt.](../../../load-balancer/load-balancer-multivip-overview.md#limitations)  
 
2. Stellen Sie für Ihre virtuellen IP-Adressen eine Instanz von [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) bereit, und konfigurieren Sie sie. Es wird empfohlen, einen [Lastenausgleich im Tarif „Standard“](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. 

    1. Konfigurieren Sie zwei Front-End-IP-Adressen: eine für ASCS (`10.90.90.10`) und eine für ERS (`10.90.90.9`).
    2. Erstellen Sie einen Back-End-Pool, und fügen Sie die beiden virtuellen Computer hinzu, die dem Cluster angehören sollen.
    3. Erstellen Sie den Integritätstest für ASCS. Der Testport ist `62000`. Erstellen Sie den Testport für ERS. Der ERS-Testport ist `62101`. Beim späteren Konfigurieren der Pacemaker-Ressourcen müssen entsprechende Testports verwendet werden.
    4. Konfigurieren Sie die Lastenausgleichsregeln für ASCS und ERS. Wählen Sie die entsprechenden Front-End-IP-Adressen und Integritätstests sowie den Back-End-Pool aus. Wählen Sie Hochverfügbarkeitsports aus, erhöhen Sie das Leerlauftimeout auf 30 Minuten, und aktivieren Sie Floating IP.

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Bereitstellen von Azure Files-Speicherkonto und NFS-Freigaben 

NFS in Azure Files basiert auf [Premium-Speicher von Azure Files][afs-azure-doc]. Lesen Sie vor dem Einrichten von NFS in Azure Files die [Anleitung zum Erstellen einer NFS-Freigabe](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal).    

Es gibt zwei Optionen für Redundanz innerhalb einer Azure-Region:

- [Lokal redundanter Speicher (LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) bietet eine lokale synchrone Datenreplikation innerhalb einer Zone.
- [Zonenredundanter Speicher (ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) repliziert Ihre Daten synchron in allen drei [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) der primären Region.

Überprüfen Sie, ob in der von Ihnen gewählten Azure-Region NFS 4.1 in Azure Files mit der entsprechenden Redundanz zur Verfügung steht. Überprüfen Sie die [Verfügbarkeit von Azure Files nach Azure-Region][afs-avail-matrix] unter **File Storage Premium**. Wenn Ihr Szenario von ZRS profitiert, [vergewissern Sie sich, dass Premium-Dateifreigaben mit ZRS in Ihrer Azure-Region unterstützt werden](../../../storage/common/storage-redundancy.md#zone-redundant-storage).

Es wird empfohlen, über einen [privaten Azure-Endpunkt](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal) auf Ihr Azure Storage-Konto zuzugreifen. Achten Sie darauf, dass Sie den Azure Files-Speicherkontoendpunkt und die virtuellen Computer, auf denen Sie die NFS-Freigaben bereitstellen müssen, im gleichen virtuellen Azure-Netzwerk (VNet) oder in mittels Peering verknüpften Azure-VNets bereitstellen.

1. Stellen Sie ein File Storage-Konto mit dem Namen `sapafsnfs` bereit. In diesem Beispiel wird ZRS verwendet. Sollten Sie mit dem Prozess nicht vertraut sein, finden Sie weitere Informationen unter [Speicherkonto erstellen](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account) für das Azure-Portal.
1. Verwenden Sie auf der Registerkarte **Allgemeine Informationen** die folgenden Einstellungen:

    1. Geben Sie unter **Name des Speicherkontos** den Namen `sapafsnfs` ein.
    
    1. Wählen Sie unter **Leistung** den Wert **Premium** aus.
    
    1. Wählen Sie unter **Premium account type** (Premium-Kontotyp) die Option **FileStorage** aus.
    
    1. Wählen Sie unter **Replikation** die Zonenredundanz (ZRS) aus.

1. Wählen Sie **Weiter** aus.

1. Deaktivieren Sie auf der Registerkarte **Erweitert** die Option **Sichere Übertragung für REST-API-Vorgänge erforderlich**. Wenn Sie diese Option nicht deaktivieren, kann die NFS-Freigabe nicht auf Ihrem virtuellen Computer eingebunden werden. Bei der Einbindung tritt ein Timeout auf.

1. Wählen Sie **Weiter** aus.

1. Konfigurieren Sie im Abschnitt **Netzwerk** die folgenden Einstellungen: 

    1. Wählen Sie unter **Netzwerkkonnektivität** für **Konnektivitätsmethode** die Option **Privater Endpunkt** aus.  
    
    1. Wählen Sie unter **Privater Endpunkt** die Option **Privaten Endpunkt hinzufügen** aus.
    
1. Wählen Sie im Bereich **Privaten Endpunkt erstellen** Ihr **Abonnement**, Ihre **Ressourcengruppe** und Ihren **Standort** aus. 
    
    Geben Sie unter **Name**`sapafsnfs_pe` ein.
        
    Wählen Sie unter **Untergeordnete Speicherressource** die Option **Datei** aus.
        
    Wählen Sie unter **Netzwerk** für **Virtuelles Netzwerk** das zu verwendende VNet und das entsprechende Subnetz aus. Auch hier können Sie das VNet, in dem sich Ihre virtuellen SAP-Computer befinden, oder ein mittels Peering verknüpftes Vnet verwenden.
       
    Übernehmen Sie unter **Integration von privatem DNS** für **In private DNS-Zone integrieren** die Standardoption **Ja**. Wählen Sie Ihre **private DNS-Zone** aus.
        
    Klicken Sie auf **OK**.
        
1. Wählen Sie auf der Registerkarte **Netzwerk** die Option **Weiter** aus.

1. Übernehmen Sie auf der Registerkarte **Datenschutz** alle Standardeinstellungen. 

1. Wählen Sie **Überprüfen und erstellen** aus, um Ihre Konfiguration zu überprüfen.

1. Warten Sie, bis die Überprüfung abgeschlossen ist. Beheben Sie ggf. vorhandene Probleme, bevor Sie fortfahren.

1. Wählen Sie auf der Registerkarte **Überprüfen + erstellen** die Option **Erstellen** aus.


Stellen Sie als Nächstes die NFS-Freigaben im von Ihnen erstellten Speicherkonto bereit. In diesem Beispiel gibt es zwei NFS-Freigaben: `sapnw1` und `saptrans`.    
1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Speicherkonten** aus, oder suchen Sie danach. 

1. Wählen Sie auf der Seite **Speicherkonten** die Option **sapafsnfs** aus.

1.  Wählen Sie im Ressourcenmenü für **sapafsnfs** unter **Datenspeicher** die Option **Dateifreigaben** aus.

1. Wählen Sie auf der Seite **Dateifreigaben** die Option **Dateifreigabe** aus.

   1. Geben Sie unter **Name** Folgendes ein: `sapnw1`, `saptrans`.
   1.  Wählen Sie eine geeignete Freigabegröße aus – beispielsweise **128 GB**.  Berücksichtigen Sie die Größe der auf der Freigabe gespeicherten Daten sowie die IOPS- und Durchsatzanforderungen.  Weitere Informationen finden Sie unter [Skalierbarkeitsziele für Azure-Dateifreigaben](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
   1. Wählen Sie als Protokoll **NFS** aus.
   1. Wählen Sie **Kein Root-Squash** aus.  Andernfalls wird beim Einbinden der Freigaben auf Ihren virtuellen Computern der Dateibesitzer bzw. die Gruppe nicht angezeigt.

   > [!IMPORTANT]
   > Die oben angegebene Freigabegröße ist nur ein Beispiel. Dimensionieren Sie Ihre Freigaben angemessen. Orientieren Sie sich dabei nicht nur an der Größe der auf der Freigabe gespeicherten Daten, sondern auch an den Anforderungen für IOPS und Durchsatz. Ausführliche Informationen finden Sie unter [Skalierbarkeitsziele für Azure-Dateifreigaben](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).  

   Die SAP-Dateisysteme, die nicht über NFS eingebunden werden müssen, können auch in [Azure Disk Storage](../../disks-types.md#premium-ssds) bereitgestellt werden. In diesem Beispiel können `/usr/sap/NW1/D02` und `/usr/sap/NW1/D03` in Azure Disk Storage bereitgestellt werden. 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Wichtige Überlegungen im Zusammenhang mit Freigaben vom Typ „NFS in Azure Files“

Berücksichtigen Sie die folgenden wichtigen Punkte, wenn Sie eine Bereitstellung mit NFS in Azure Files planen:  

- Die Mindestgröße für eine Freigabe beträgt 100 GiB. Sie bezahlen nur für die [Kapazität der bereitgestellten Freigaben](../../../storage/files/understanding-billing.md#provisioned-model). 
- Berücksichtigen Sie beim Dimensionieren Ihrer NFS-Freigaben nicht nur die Kapazitätsanforderungen, sondern auch die Anforderungen hinsichtlich IOPS und Durchsatz. Ausführliche Informationen finden Sie unter [Skalierbarkeitsziele für Azure-Dateifreigaben](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets).
- Testen Sie die Workload, um die gewählte Dimensionierung zu überprüfen und sicherzustellen, dass sie Ihre Leistungsziele erfüllt. Informationen zur Behandlung von Leistungsproblemen mit NFS in Azure Files finden Sie unter [Problembehandlung bei Leistungsproblemen mit Azure-Dateifreigaben](../../../storage/files/storage-troubleshooting-files-performance.md).
- Bei SAP J2EE-Systemen wird die Platzierung von `/usr/sap/<SID>` in NFS in Azure Files nicht unterstützt.
- Wenn in Ihrem SAP-System sehr viele Batchaufträge ausgeführt werden, verfügen Sie möglicherweise über Millionen von Auftragsprotokollen. Falls die SAP-Batchauftragsprotokolle im Dateisystem gespeichert werden, achten Sie insbesondere auf die Dimensionierung der Freigabe `sapmnt`. Ab SAP_BASIS 7.52 werden Batchauftragsprotokolle standardmäßig in der Datenbank gespeichert. Ausführliche Informationen finden Sie unter [Auftragsprotokoll in der Datenbank][2360818].     
- Stellen Sie für jedes SAP-System eine separate Freigabe vom Typ `sapmnt` bereit.
- Verwenden Sie die Freigabe `sapmnt` nicht für andere Aktivitäten (beispielsweise für Schnittstellen oder für `saptrans`).
- Verwenden Sie die Freigabe `saptrans` nicht für andere Aktivitäten (beispielsweise für Schnittstellen oder für `sapmnt`).
- Es wird davon abgeraten, die Freigaben für zu viele SAP-Systeme in einem einzelnen Speicherkonto zu konsolidieren. Es gibt auch [Skalierbarkeitsziele für Speicherkonten](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets). Achten Sie auch darauf, die Grenzwerte für das Speicherkonto nicht zu überschreiten.
- Konsolidieren Sie generell maximal Freigaben für bis fünf SAP-Systeme in einem einzelnen Speicherkonto. Diese Richtlinie hilft dabei, die Grenzwerte für Speicherkonten nicht zu überschreiten, und vereinfacht die Leistungsanalyse.   
- Vermeiden Sie es generell, Freigaben für produktionsfremde SAP-Systeme und Freigaben für SAP-Produktionssysteme im gleichen Speicherkonto zu mischen.
- Es empfiehlt sich, für die Bereitstellung mindestens RHEL 8.4 zu verwenden, um von [NFS-Clientverbesserungen](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels) zu profitieren.   
- Verwenden Sie einen privaten Endpunkt. Im unwahrscheinlichen Fall eines Zonenausfalls werden Ihre NFS-Sitzungen automatisch in eine fehlerfreie Zone umgeleitet. Sie müssen die NFS-Freigaben auf Ihren virtuellen Computern nicht erneut einbinden.
- Wenn Sie Ihre virtuellen Computer verfügbarkeitszonenübergreifend bereitstellen, verwenden Sie in den Azure-Regionen mit ZRS-Unterstützung ein [Speicherkonto mit ZRS](../../../storage/common/storage-redundancy.md#zone-redundant-storage). 
- Von Azure Files wird derzeit keine automatische regionsübergreifende Replikation für Notfallwiederherstellungsszenarien unterstützt.

## <a name="setting-up-ascs"></a>Einrichten von (A)SCS

In diesem Beispiel werden die Ressourcen manuell über das [Azure-Portal](https://portal.azure.com/#home) bereitgestellt.

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Bereitstellen von Azure Load Balancer über das Azure-Portal

Nachdem Sie die virtuellen Computer für Ihr SAP-System bereitgestellt haben, erstellen Sie einen Lastenausgleich. Verwenden Sie dann die virtuellen Computer im Back-End-Pool.

1. Erstellen Sie eine interne Load Balancer Standard-Instanz.
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.90.90.10 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.NW1.ASCS**).
         1. Legen Sie die Zuweisung auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **10.90.90.10**).
         1. Auf "OK" klicken
      1. IP-Adresse 10.90.90.9 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.90.90.9** und **frontend.NW1.ERS**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **backend.NW1**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie „Virtueller Computer“ aus.
      1. Wählen Sie die virtuellen Computer des (A)SCS-Clusters mit ihren IP-Adressen aus.
      1. Klicken Sie auf "Hinzufügen".  
      
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z. B. **health.NW1.ASCS**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **01** für ASCS ERS
            * Wiederholen Sie die oben unter „c“ angegebenen Schritte, um einen Integritätstest für ERS zu erstellen (z. B. 621 **01** und **health.NW1.ERS**).
   1. Lastenausgleichsregeln
      1. Erstellen eines Back-End-Pools für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **lb.NW1.ASCS**).
         1. Wählen Sie die Front-End-IP-Adresse für ASCS, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **frontend.NW1.ASCS**, **backend.NW1** und **health.NW1.ASCS**).
         1. Wählen Sie **HA-Ports** aus.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
         * Wiederholen Sie die oben angegebenen Schritte, um Lastenausgleichsregeln für ERS zu erstellen (z. B. **lb.NW1.ERS**).
1. Wenn Ihr Szenario einen grundlegenden Lastenausgleich (intern) erfordert, führen Sie stattdessen die folgenden Schritte aus:  
   1. Erstellen der Front-End-IP-Adressen
      1. IP-Adresse 10.90.90.10 für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie den Front-End-IP-Pool aus und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Front-End-IP-Pools ein (z. B. **frontend.NW1.ASCS**).
         1. Legen Sie die Zuweisung auf „Statisch“ fest, und geben Sie die IP-Adresse ein (z. B. **10.90.90.10**).
         1. Auf "OK" klicken
      1. IP-Adresse 10.90.90.9 für ASCS ERS
         * Wiederholen Sie die oben unter „a“ angegebenen Schritte, um eine IP-Adresse für ERS zu erstellen (z. B. **10.90.90.9** und **frontend.NW1.ERS**).
   1. Erstellen des Back-End-Pools
      1. Öffnen Sie den Lastenausgleich, wählen Sie Back-End-Pools und klicken Sie auf „Hinzufügen“.
      1. Geben Sie den Namen des neuen Back-End-Pools ein (z. B. **backend.NW1**).
      1. Klicken Sie auf „Virtuellen Computer hinzufügen“.
      1. Wählen Sie die Verfügbarkeitsgruppe aus, die Sie zuvor für ASCS erstellt haben. 
      1. Wählen Sie die virtuellen Computer des A(SCS)-Clusters aus.
      1. OK klicken
   1. Erstellen der Integritätstests
      1. Port 620 **00** für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie Integritätstests aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen des neuen Integritätstests ein (z. B. **health.NW1.ASCS**).
         1. Wählen Sie TCP als Protokoll und Port 620 **00** aus, und behalten Sie „Intervall 5“ und „Fehlerschwellenwert 2“ bei.
         1. OK klicken
      1. Port 621 **01** für ASCS ERS
            * Wiederholen Sie die oben unter „c“ angegebenen Schritte, um einen Integritätstest für ERS zu erstellen (z. B. 621 **01** und **health.NW1.ERS**).
   1. Lastenausgleichsregeln
      1. 32 **00** TCP für ASCS
         1. Öffnen Sie den Lastenausgleich, wählen Sie „Lastenausgleichsregeln“ aus, und klicken Sie auf „Hinzufügen“.
         1. Geben Sie den Namen der neuen Lastenausgleichsregel ein (z. B. **lb.NW1.ASCS.3200**).
         1. Wählen Sie die Front-End-IP-Adresse für ASCS, den Back-End-Pool und den Integritätstest aus, die Sie zuvor erstellt haben (z. B. **frontend.NW1.ASCS**).
         1. Behalten Sie **TCP** als Protokoll bei, und geben Sie Port **3200** ein.
         1. Erhöhen Sie die Leerlaufzeitüberschreitung auf 30 Minuten.
         1. **Achten Sie darauf, dass Sie „Floating IP“ aktivieren.**
         1. OK klicken
      1. Zusätzliche Ports für ASCS
         * Wiederholen Sie die oben unter „d“ angegebenen Schritte für die Ports 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 und „TCP“ für ASCS.
      1. Zusätzliche Ports für ASCS ERS
         * Wiederholen Sie die oben unter „d“ angegebenen Schritte für die Ports 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 und „TCP“ für ASCS ERS.

      
      > [!IMPORTANT]
      > Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

      > [!Note]
      > Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).  

      > [!IMPORTANT]
      > Aktivieren Sie keine TCP-Zeitstempel auf Azure-VMs hinter Azure Load Balancer. Das Aktivieren von TCP-Zeitstempeln bewirkt, dass bei Integritätstests Fehler auftreten. Legen Sie den Parameter **net.ipv4.tcp_timestamps** auf **0** fest. Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](../../../load-balancer/load-balancer-custom-probe-overview.md).


### <a name="create-pacemaker-cluster"></a>Erstellen des Pacemaker-Clusters

Führen Sie die Schritte in [Einrichten von Pacemaker unter Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) aus, um ein grundlegendes Pacemaker-Cluster für diesen (A)SCS-Server zu erstellen.

### <a name="prepare-for-sap-netweaver-installation"></a>Vorbereiten der SAP NetWeaver-Installation

Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** – gilt für alle Knoten, **[1]** – gilt nur für Knoten 1, oder **[2]** – gilt nur für Knoten 2.

1. **[A]** Richten Sie die Hostnamensauflösung ein.

   Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen.

    ```bash
    sudo vi /etc/hosts
    ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

    ```bash
     # IP address of cluster node 1
     10.90.90.7    sap-cl1
     # IP address of cluster node 2
     10.90.90.8     sap-cl2
     # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
     10.90.90.10   sapascs
     # IP address of the load balancer frontend configuration for SAP Netweaver ERS
     10.90.90.9    sapers
    ```

2. **[A]** Installieren Sie den NFS-Client und andere Anforderungen.

    ```bash
    sudo yum -y install nfs-utils resource-agents resource-agents-sap
    ```

3. **[1]** Erstellen Sie die SAP-Verzeichnisse auf der NFS-Freigabe.  
   Binden Sie die NFS-Freigabe **sapnw1** vorübergehend auf einem der virtuellen Computer ein, und erstellen Sie die SAP-Verzeichnisse, die als geschachtelte Bereitstellungspunkte verwendet werden sollen.  

    ```bash
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    sudo mount -t nfs sapnfs.file.core.windows.net:/sapnfsafs/sapnw1 /saptmp -o vers=4,minorversion=1,sec=sys
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntNW1
    sudo mkdir -p usrsapNW1ascs
    sudo mkdir -p usrsapNW1ers
    sudo mkdir -p usrsapNW1sys
    # unmount the volume and delete the temporary directory
    cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

4. **[A]** Erstellen Sie die freigegebenen Verzeichnisse.

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans
    sudo mkdir -p /usr/sap/NW1/SYS
    sudo mkdir -p /usr/sap/NW1/ASCS00
    sudo mkdir -p /usr/sap/NW1/ERS01
    
    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    sudo chattr +i /usr/sap/NW1/SYS
    sudo chattr +i /usr/sap/NW1/ASCS00
    sudo chattr +i /usr/sap/NW1/ERS01
    ```

5. **[A]** Überprüfen Sie die Version des resource-agents-sap-Pakets.

   Stellen Sie sicher, dass mindestens Version 3.9.5-124.el7 des resource-agents-sap-Pakets installiert ist.
    ```
    sudo yum info resource-agents-sap
   ```


6. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

7. **[A]** Konfigurieren Sie die Auslagerungsdatei.

    ```bash
    sudo vi /etc/waagent.conf
    
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
    
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

    ```bash
    sudo service waagent restart
    ```

8. **[A]** Konfigurieren Sie RHEL.

   Konfigurieren Sie RHEL gemäß dem SAP-Hinweis [2002167] (RHEL 7.x) bzw. gemäß dem SAP-Hinweis [2772999] (RHEL 8.x).  

### <a name="installing-sap-netweaver-ascsers"></a>Installieren von SAP NetWeaver ASCS/ERS

1. **[1]** Konfigurieren Sie Standardclustereigenschaften.

    ```bash
    # If using RHEL 7.X
    pcs resource defaults resource-stickiness=1
    pcs resource defaults migration-threshold=3
    # If using RHEL 8.X
    pcs resource defaults update resource-stickiness=1
    pcs resource defaults update migration-threshold=3
    ```

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ASCS-Instanz.

    ```bash
    sudo pcs node standby sap-cl2
   
    sudo pcs resource create fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' \
      directory='/usr/sap/NW1/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
      --group g-NW1_ASCS
   
    sudo pcs resource create vip_NW1_ASCS IPaddr2 \
      ip=10.90.90.10 cidr_netmask=24 \
      --group g-NW1_ASCS
   
    sudo pcs resource create nc_NW1_ASCS azure-lb port=62000 \
      --group g-NW1_ASCS
    ```

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

    ```bash
    sudo pcs status
    
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl1
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1 
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    ```

1. **[1]** Installieren Sie SAP NetWeaver ASCS.  

   Installieren Sie SAP NetWeaver ASCS als Stamm auf dem ersten Knoten. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für ASCS zugeordnet ist (z. B. **sapascs**, **10.90.90.10**), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. **00**).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

    Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ASCS **00**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ASCS **00**“ fest, und versuchen Sie es noch mal.

    ```bash
    sudo chown nw1adm /usr/sap/NW1/ASCS00
    sudo chgrp sapsys /usr/sap/NW1/ASCS00
    ```

1. **[1]** Erstellen Sie eine virtuelle IP-Ressource und einen Integritätstest für die ERS-Instanz.

    ```bash
    sudo pcs node unstandby sap-cl2
    sudo pcs node standby sap-cl1
    
    sudo pcs resource create fs_NW1_AERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' \
      directory='/usr/sap/NW1/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW1_AERS
   
    sudo pcs resource create vip_NW1_AERS IPaddr2 \
      ip=10.90.90.9 cidr_netmask=24 \
     --group g-NW1_AERS
   
    sudo pcs resource create nc_NW1_AERS azure-lb port=62101 \
     --group g-NW1_AERS
    ```
 
   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

    ```bash
    sudo pcs status
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    ```

1. **[2]** Installieren Sie SAP NetWeaver ERS.  

   Installieren Sie SAP NetWeaver ERS auf dem zweiten Knoten als Stamm. Verwenden Sie dabei einen virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration des Lastenausgleichs für ERS zugeordnet ist (z. B. **sapers**, **10.90.90.9**), und die Instanznummer, die Sie für den Test des Lastenausgleichs verwendet haben (z. B. **01**).

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp

    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   Wenn bei der Installation kein Unterordner in „/usr/sap/**NW1**/ERS **01**“ erstellt werden kann, legen Sie den Besitzer und die Gruppe des Ordners „ERS **01**“ fest, und versuchen Sie es noch mal.

    ```
    sudo chown qaadm /usr/sap/NW1/ERS01
    sudo chgrp sapsys /usr/sap/NW1/ERS01
    ```

1. **[1]** Passen Sie die ASCS/SCS- und ERS-Instanzprofile an.

   * ASCS/SCS-Profil

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
   
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
   
    # Add the keep alive parameter, if using ENSA1
    enque/encni/set_so_keepalive = true
    ```

   Stellen Sie sowohl für ENSA1 als auch für ENSA2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.  

   * ERS-Profil

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
   
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

1. **[A]** Konfigurieren Sie Keep-Alive.

   Die Kommunikation zwischen dem SAP NetWeaver-Anwendungsserver und ASCS/SCS wird durch einen Softwarelastenausgleich weitergeleitet. Der Lastenausgleich trennt nach einem konfigurierbaren Timeout inaktive Verbindungen. Um dies zu verhindern, muss bei Verwendung von ENSA1 ein Parameter im SAP NetWeaver-ASCS/SCS-Profil festgelegt werden. Ändern Sie außerdem die Linux-Systemeinstellungen für `keepalive` auf allen SAP-Servern (sowohl für ENSA1 als auch für ENSA2). Weitere Informationen finden Sie im [SAP-Hinweis 1410736][1410736].

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

1. **[A]**  Aktualisieren Sie die Datei „/usr/sap/sapservices“.

   Um den Start der Instanzen durch das „sapinit“-Startskript zu verhindern, müssen alle von Pacemaker verwalteten Instanzen aus der Datei „/usr/sap/sapservices“ auskommentiert werden. Kommentieren Sie nicht die SAP HANA-Instanz aus, wenn sie mit HANA SR verwendet wird.

    ```bash
    sudo vi /usr/sap/sapservices
   
    # On the node where you installed the ASCS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ASCS00/exe/sapstartsrv pf=/usr/sap/NW1/SYS/profile/NW1_ASCS00_sapascs -D -u nw1adm
   
    # On the node where you installed the ERS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ERS01/exe/sapstartsrv pf=/usr/sap/NW1/ERS01/profile/NW1_ERS01_sapers -D -u nw1adm
    ```

1. **[1]** Erstellen Sie die SAP-Clusterressourcen.

   Wenn Sie mit ENSA1 (Enqueue-Server 1-Architektur) arbeiten, definieren Sie die Ressourcen wie folgt:

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
     InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
     op monitor interval=20 on-fail=restart timeout=60 \
     op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
     InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
     AUTOMATIC_RECOVER=false IS_ERS=true \
     op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_AERS
     
    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint location rsc_sap_NW1_ASCS00 rule score=2000 runs_ers_NW1 eq 1
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP hat Unterstützung für ENSA2 (Enqueue-Server 2), einschließlich Replikation, mit SAP NW 7.52 eingeführt. Ab der ABAP-Plattform 1809 wird Enqueue-Server 2 standardmäßig installiert. Informationen zur Unterstützung von Enqueue-Server 2 finden Sie im SAP Hinweis [2630416](https://launchpad.support.sap.com/#/notes/2630416).
   Installieren Sie bei Verwendung der Enqueue-Server 2-Architektur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) den Ressourcen-Agent resource-agents-sap-4.1.1-12.el7.el7.x86_64 oder höher, und definieren Sie die Ressourcen wie folgt:

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
    InstanceName=NW1_ASCS00_anftstsapvh START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
    InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_AERS
      
    sudo pcs resource meta rsc_sap_NW1_ERS01  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint order start g-NW1_ASCS then start g-NW1_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   Wenn Sie ein Upgrade von einer älteren Version durchführen und zu Enqueue Server 2 wechseln, lesen Sie den SAP-Hinweis [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Die Timeouts in der oben beschriebenen Konfiguration sind nur Beispiele und müssen möglicherweise an das spezifische SAP-Setup angepasst werden. 

   Stellen Sie sicher, dass der Clusterstatus gültig ist und alle Ressourcen gestartet sind. Es ist nicht wichtig, auf welchem Knoten die Ressourcen ausgeführt werden.

    ```bash
    sudo pcs status
    
    # Online: [ sap-cl1 sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
   ```

1. **[A]**  Fügen Sie Firewallregeln für ASCS und ERS auf beiden Knoten hinzu. Fügen Sie die Firewallregeln für ASCS und ERS auf beiden Knoten hinzu.

    ```bash
    # Probe Port of ASCS
    sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62000/tcp
    sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3200/tcp
    sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3600/tcp
    sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3900/tcp
    sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8100/tcp
    sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50013/tcp
    sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50014/tcp
    sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50016/tcp
    # Probe Port of ERS
    sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62101/tcp
    sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3201/tcp
    sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3301/tcp
    sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50113/tcp
    sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50114/tcp
    sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50116/tcp
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Vorbereitung des SAP NetWeaver-Anwendungsservers

   Für einige Datenbanken ist es erforderlich, dass die Installation der Datenbankinstanz auf einem Anwendungsserver ausgeführt wird. Bereiten Sie die virtuellen Computer für den Anwendungsserver vor, damit Sie diese in diesen Fällen verwenden können.  

   In den folgenden Schritten wird davon ausgegangen, dass Sie den Anwendungsserver auf einem Server installieren, bei dem es sich nicht um einen ASCS/SCS- oder HANA-Server handelt. Anderenfalls sind einige der nachfolgenden Schritte (wie die Konfiguration der Hostnamensauflösung) nicht erforderlich.  

   Die folgenden Elemente sind mit einem der folgenden Präfixe versehen: **[A]** (gilt für alle PAS und AAS), **[P]** (gilt nur für PAS) oder **[S]** (gilt nur für AAS).  

1. **[A]** Richten Sie die Hostnamensauflösung ein. Sie können entweder einen DNS-Server verwenden oder „/etc/hosts“ auf allen Knoten ändern. In diesem Beispiel wird die Verwendung der /etc/hosts-Datei veranschaulicht.
   Ersetzen Sie die IP-Adresse und den Hostnamen in den folgenden Befehlen:  

    ```bash
    sudo vi /etc/hosts
    ```

   Fügen Sie „/etc/hosts“ die folgenden Zeilen hinzu. Ändern Sie die IP-Adresse und den Hostnamen Ihrer Umgebung entsprechend.

    ```bash
    10.90.90.7    sap-cl1
    10.90.90.8    sap-cl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    10.90.90.10   sapascs
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    10.90.90.9    sapers
    10.90.90.12   sapa01
    10.90.90.13   sapa02
    ```

1. **[A]** Erstellen des Verzeichnisses „sapmnt“
   
    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans

    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Installieren Sie den NFS-Client und andere Anforderungen.  

    ```bash
    sudo yum -y install nfs-utils uuidd
    ```

1. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```   

1. **[A]** Konfigurieren Sie die Auslagerungsdatei.
 
    ```bash
    sudo vi /etc/waagent.conf
   
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
   
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   Starten Sie den Agent neu, um die Änderung zu aktivieren.

    ```bash
    sudo service waagent restart
    ```

## <a name="install-database"></a>Installieren der Datenbank

In diesem Fall ist SAP NetWeaver auf SAP HANA installiert. Für diese Installation können Sie jede unterstützte Datenbank verwenden. Weitere Informationen zum Installieren von SAP HANA in Azure finden Sie unter [Hochverfügbarkeit von SAP HANA auf Azure-VMs unter Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

Installieren Sie die SAP NetWeaver-Datenbankinstanz als Stamm mit einem virtuellen Hostnamen, der der IP-Adresse der Front-End-Konfiguration für den Lastenausgleich für die Datenbank zugeordnet ist.

Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

   ```bash
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installation des SAP NetWeaver-Anwendungsservers

Führen Sie die folgenden Schritte durch, um einen SAP-Anwendungsserver zu installieren.

1. **[A]** Bereiten Sie den Anwendungsserver vor.

   Führen Sie die Schritte im Kapitel [Vorbereitung des SAP NetWeaver-Anwendungsservers](high-availability-guide-rhel-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) aus, um den Anwendungsserver vorzubereiten.

1. **[A]** Installieren Sie den SAP NetWeaver-Anwendungsserver.  

   Installieren Sie einen primären oder einen zusätzlichen SAP NetWeaver-Anwendungsserver.

   Sie können den sapinst-Parameter „SAPINST_REMOTE_ACCESS_USER“ verwenden, um anderen Benutzern als Stammbenutzern die Herstellung einer Verbindung mit sapinst zu ermöglichen.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
    ```

1. **[A]** Aktualisieren Sie den sicheren SAP HANA-Speicher.

   Ändern Sie den sicheren SAP HANA-Speicher dahingehend, dass er auf den virtuellen Namen der Einrichtung der SAP HANA-Systemreplikation zeigt.

   Führen Sie den folgenden Befehl zum Auflisten der Einträge als `<sapsid>adm` aus:

    ```bash
    hdbuserstore List
    ```

   Damit werden alle Einträge aufgelistet, und die Liste sieht in etwa wie folgt aus.
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   In diesem Beispiel verweist die IP-Adresse des Standardeintrags auf den virtuellen Computer, nicht auf den Lastenausgleich. Ändern Sie den Eintrag so, dass er auf den virtuellen Hostnamen des Lastenausgleichs verweist. Achten Sie darauf, dass Sie den gleichen Port und Datenbanknamen verwenden (`30313` und `NW1` in der Beispielausgabe).


    ```bash
    su - nw1adm
    hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
    ```

## <a name="test-cluster-setup"></a>Testen der Clustereinrichtung

Testen Sie Ihren Pacemaker-Cluster sorgfältig. [Führen Sie die üblichen Failovertests durch.](./high-availability-guide-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>Nächste Schritte

* [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines – Planung und Implementierung für SAP][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP][dbms-guide]
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA auf Azure-VMs finden Sie unter [Hochverfügbarkeit für SAP HANA auf Azure Virtual Machines (VMs)][sap-hana-ha].
---
title: Regionsübergreifendes Verschieben von Azure VMware Solution-Ressourcen
description: In diesem Artikel wird beschrieben, wie Azure VMware Solution-Ressourcen von einer Azure-Region in eine andere verschoben werden.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351391"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>Verschieben von Azure VMware Solution-Ressourcen in eine andere Region

>[!IMPORTANT]
>Die Schritte in diesem Artikel beziehen sich ausschließlich auf das Verschieben von Azure VMware Solution (Quelle) in einer Region zu Azure VMware Solution (Ziel) in einer anderen Region. 

Azure VMware Solution-Ressourcen können aus unterschiedlichen Gründen in eine andere Region verschoben werden. Beispielsweise um nur in bestimmten Regionen verfügbare Features oder Dienste bereitzustellen, um Richtlinien- und Governanceanforderungen zu erfüllen oder um auf Anforderungen in Bezug auf die Kapazitätsplanung zu reagieren. 

Dieser Artikel unterstützt Sie bei der Planung und Migration von Azure VMware Solution von einer Azure-Region zu einer anderen, z. B. von Azure-Region A zu Azure-Region B.


In der Abbildung ist die empfohlene ExpressRoute-Konnektivität zwischen den beiden Azure VMware Solution-Umgebungen dargestellt.  Zwischen den beiden Umgebungen werden eine HCX-Standortkopplung und ein Dienstnetz erstellt.  Der HCX-Migrationsdatenverkehr und die Layer-2-Erweiterung werden zwischen den beiden Umgebungen verschoben (dargestellt durch die rote Linie). Informationen zu der von VMware empfohlenen HCX-Planung finden Sie unter [Planning an HCX Migration (Planen einer HCX-Migration)](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1).

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="Abbildung: ExpressRoute Global Reach-Kommunikation zwischen der Azure VMware Solution-Quellumgebung und der Azure VMware Solution-Zielumgebung" border="false":::

>[!NOTE]
>Sie müssen keinen Workflow zurück zum lokalen Standort migrieren, da der Datenverkehr zwischen den privaten Clouds (Quelle und Ziel) fließt:
>
>**Private Azure VMware Solution-Cloud (Quelle) > ExpressRoute-Gateway (Quelle) > ExpressRoute-Gateway (Ziel) > private Azure VMware Solution-Cloud (Ziel)**

In der Abbildung ist die Konnektivität zwischen den beiden Azure VMware Solution-Umgebungen dargestellt. 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="Abbildung: Kommunikation zwischen der Azure VMware Solution-Quellumgebung und der Azure VMware Solution-Zielumgebung" border="false":::


In diesem Artikel lernen Sie folgende Schritte kennen: 

> [!div class="checklist"]
> * Vorbereiten und Planen der Verschiebung in eine andere Azure-Region
> * Herstellen der Netzwerkverbindung zwischen den beiden privaten Azure VMware Solution-Clouds
> * Exportieren der Konfiguration aus der Azure VMware Solution-Quellumgebung
> * Erneutes Anwenden der unterstützen Konfigurationselemente auf die Azure VMware Solution-Zielumgebung
> * Migrieren von Workload mithilfe von VMware HCX

## <a name="prerequisites"></a>Voraussetzungen

- [Für die VMware HCX-Appliance wird ein Upgrade auf den neuesten Patch durchgeführt](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html), um mögliche Migrationsprobleme zu vermeiden.

- Bei der lokalen Inhaltsbibliothek der Quelle handelt es sich um eine [Bibliothek mit veröffentlichten Inhalten](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g).

## <a name="prepare"></a>Vorbereiten

Anhand der folgenden Schritte erfahren Sie, wie Sie Ihre private Azure VMware Solution-Cloud auf die Verschiebung in eine andere private Azure VMware Solution-Cloud vorbereiten. 

### <a name="export-the-source-configuration"></a>Exportieren der Quellkonfiguration

1. [Exportieren Sie die erweiterten Segmente, Firewallregeln, Portdetails und Routingtabelle](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html) aus der Quelle.

1. [Exportieren Sie die Inhalte einer Bestandslistenansicht in eine CSV-Datei](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html).

1. [Sortieren Sie Workloads in Migrationsgruppen (Migrationsdurchlauf)](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1).


### <a name="deploy-the-target-environment"></a>Bereitstellen der Zielumgebung

Bevor Sie die Quellkonfiguration verschieben können, müssen Sie die [Zielumgebung bereitstellen](plan-private-cloud-deployment.md).


### <a name="back-up-the-source-configuration"></a>Sichern der Quellkonfiguration

Sichern Sie die Azure VMware Solution-Konfiguration (Quelle), die die VC-, NSX-T- und Firewallrichtlinien und -regeln enthält. 

- **Compute:** Exportieren Sie die vorhandene Bestandskonfiguration. Für die Bestandssicherung können Sie RVtool (eine Open-Source-App) verwenden.

- **Netzwerk- und Firewallrichtlinien und -regeln:** Erstellen Sie am Azure VMware Solution-Ziel die gleichen Netzwerksegmente wie in der Quellumgebung.

Von Azure VMware Solution werden alle Sicherungslösungen unterstützt. Zum Installieren, Sichern von Daten und Wiederherstellen von Sicherungen sind CloudAdmin-Berechtigungen erforderlich. Weitere Informationen finden Sie unter [Sicherungslösungen für virtuelle Azure VMware Solution-Computer](ecosystem-back-up-vms.md).

- Sicherung der VM-Workload mithilfe der Commvault-Lösung:

   - [Erstellen eines VMware-Clients](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm) über das Befehlscenter für Azure VMware Solution vCenter

   - [Erstellen einer VM-Gruppe](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm) mit den für Sicherungen erforderlichen VMs

   - [Ausführen von Sicherungen für VM-Gruppen](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm).

   - [Herstellen von VMs](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm).

- Sicherung der VM-Workload mithilfe der [Veritas NetBackup-Lösung](https://vrt.as/nb4avs). 

>[!TIP]
>Sie können [Azure Resource Mover](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) verwenden, um die unterstützten Ressourcen zu überprüfen und zu migrieren, um sie in andere Regionen zu verschieben, die von Azure VMware Solution abhängig sind.

### <a name="locate-the-source-expressroute-circuit-id"></a>Suchen nach der ID der ExpressRoute-Verbindung (Quelle)

1. Melden Sie sich über die Quelle beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Verwalten** > **Konnektivität** > **ExpressRoute** aus.

3. Kopieren Sie die **ExpressRoute-ID** der Quelle.  Diese benötigen Sie, um zwischen den privaten Clouds ein Peering durchzuführen.


### <a name="create-the-targets-authorization-key"></a>Erstellen des Autorisierungsschlüssels des Ziels

1. Melden Sie sich über das Ziel beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **Verwalten** > **Konnektivität** > **ExpressRoute** und anschließend **+ Autorisierungsschlüssel anfordern** aus.

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Screenshot: Anfordern eines ExpressRoute-Autorisierungsschlüssels" border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. Geben Sie einen Namen für ihn an, und wählen Sie **Erstellen** aus.

   Die Erstellung des Schlüssels kann ungefähr 30 Sekunden dauern. Nach der Erstellung wird der neue Schlüssel in der Liste der Autorisierungsschlüssel für die private Cloud angezeigt.

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Screenshot: ExpressRoute Global Reach-Autorisierungsschlüssel":::
  
1. Kopieren Sie den Autorisierungsschlüssel und die ExpressRoute-ID. Diese Angaben werden zum Abschließen des Peerings benötigt. Der Autorisierungsschlüssel wird nach einiger Zeit nicht mehr angezeigt. Kopieren Sie ihn daher, sobald er angezeigt wird.

### <a name="peer-between-private-clouds"></a>Peering zwischen privaten Clouds

Nachdem Sie nun über die IDs der ExpressRoute-Verbindung und die Autorisierungsschlüssel für beide Umgebungen verfügen, können Sie das Peering von der Quelle zum Ziel durchführen. Verwenden Sie die Ressourcen-ID und den Autorisierungsschlüssel der ExpressRoute-Leitung Ihrer privaten Cloud dazu, das Peering abzuschließen.
 
1. Melden Sie sich über das Ziel beim [Azure-Portal](https://portal.azure.com) mit dem Abonnement an, das Sie auch für die ExpressRoute-Verbindung der Quelle verwenden.

1. Wählen Sie unter „Verwalten“ die Option **Konnektivität** > **ExpressRoute Global Reach** > **Hinzufügen** aus.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Screenshot: Registerkarte „ExpressRoute Global Reach“ in der privaten Azure VMware Solution-Cloud":::

1. Fügen Sie die ID der ExpressRoute-Verbindung und die Autorisierungsschlüssel des Ziels, die Sie im vorherigen Schritt erstellt haben, ein.  Wählen Sie anschließend **Erstellen** aus:

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Screenshot: Dialogfeld zum Eingeben der Verbindungsinformationen":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>Erstellen einer Standortkopplung zwischen privaten Clouds

Nachdem Sie eine Verbindung hergestellt haben, erstellen Sie zwischen den privaten Clouds eine VMware HCX-Standortkopplung, um die Migration Ihrer VMs zu ermöglichen. Sie können den VMware HCX-Cloud-Manager in Azure VMware Solution mit dem VMware HCX-Connector in Ihrem Rechenzentrum verbinden oder koppeln. 

1. Melden Sie sich beim vCenter Ihrer Quelle an, und wählen Sie unter **Home** die Option **HCX** aus.

1. Wählen Sie unter **Infrastructure** die Option **Site Pairing** (Standortkopplung) und anschließend die Option **Connect To Remote Site** (Verbindung mit Remotestandort herstellen) aus (in der Mitte des Bildschirms). 

1. Geben Sie die zuvor notierte URL oder IP-Adresse (`https://x.x.x.9`) des Azure VMware Solution HCX-Cloud-Managers, den Azure VMware Solution-Benutzernamen (cloudadmin\@vsphere.local) und das Kennwort ein. Wählen Sie dann **Verbinden** aus.

   > [!NOTE]
   > So richten Sie ein Standortpaar ein:
   > * Ihr VMware HCX-Connector muss in der Lage sein, das Routing an Ihre HCX-Cloud-Manager-IP-Adresse über den Port 443 durchzuführen.
   >
   > * Verwenden Sie das gleiche Kennwort, mit dem Sie sich bei vCenter angemeldet haben. Sie haben dieses Kennwort auf dem ersten Bereitstellungsbildschirm angegeben.

   Ein Bildschirm wird angezeigt, auf dem der VMware HCX-Cloud-Manager in Azure VMware Solution mit dem lokalen VMware HCX-Connector verbunden (gekoppelt) ist.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot: Kopplung des HCX-Managers in Azure VMware Solution und des VMware HCX-Connectors":::

### <a name="create-a-service-mesh-between-private-clouds"></a>Erstellen eines Dienstnetzes zwischen privaten Clouds

> [!NOTE]
> So richten Sie erfolgreich ein Service Mesh mit Azure VMware Solution ein:
>
> * Die Ports UDP 500/4500 sind zwischen den Uplink-Netzwerkprofiladressen des lokalen VMware HCC-Connectors und den Azure VMware Solution HCX-Cloudnetzwerk-Profiladressen geöffnet.
>
> * Überprüfen Sie unbedingt die [Ports, die für VMware HCX erforderlich sind](https://ports.vmware.com/home/VMware-HCX).

1. Wählen Sie unter **Infrastructure** (Infrastruktur) die Option **Interconnect** (Verbinden) > **Service Mesh** (Dienstnetz) > **Create Service Mesh** (Dienstnetz erstellen) aus.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot: Auswahlmöglichkeiten zum Starten der Erstellung eines Service Mesh." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Überprüfen Sie die vorab aufgefüllten Standorte, und wählen Sie dann **Continue** (Weiter) aus. 

   > [!NOTE]
   > Wenn dies Ihre erste Dienstnetzkonfiguration ist, müssen Sie diesen Bildschirm nicht ändern.  

1. Wählen Sie das Quellcomputeprofil und das Remotecomputeprofil aus den Dropdownlisten aus, und wählen Sie dann **Continue** (Weiter) aus.  

   Mit der Auswahl werden die Ressourcen definiert, in denen VMs VMware HCX-Dienste nutzen können.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot: Auswählen des Quellcomuteprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot: Auswählen des Remotecomputeprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Überprüfen Sie die Dienste, die aktiviert werden sollen, und wählen Sie **Continue** (Weiter) aus.  

1. Wählen Sie unter **Advanced Configuration - Override Uplink Network profiles** (Erweiterte Konfiguration – Uplinknetzwerkprofile überschreiben) die Option **Continue** (Weiter) aus.  

   Uplinknetzwerkprofile stellen Verbindungen mit dem Netzwerk her, über das die Interconnect-Appliances des Remotestandorts erreicht werden können.  
  
1. Überprüfen Sie die Einstellungen in **Advanced Configuration – Network Extension Appliance Scale Out** (Erweiterte Konfiguration – Aufskalierung für Network Extension-Appliance konfigurieren), und wählen Sie **Continue** aus. 

   Sie können bis zu acht VLANs pro Appliance einrichten, können jedoch eine weitere Appliance bereitstellen, um weitere acht VLANs hinzuzufügen. Außerdem benötigen Sie einen IP-Adressraum für die zusätzlichen Appliances. Pro Appliance wird jeweils eine IP-Adresse verwendet.  Weitere Informationen finden Sie unter [Grenzwerte für die VMware HCX-Konfiguration](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Screenshot, der zeigt, wo die VLAN-Anzahl erhöht wird" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Überprüfen Sie die Einstellungen in **Advanced Configuration – Traffic Engineering** (Erweiterte Konfiguration – Verwaltung des Datenverkehrs), nehmen Sie alle erforderlichen Änderungen vor, und wählen Sie dann **Continue** (Weiter) aus.

1. Überprüfen Sie die Topologievorschau, und wählen Sie **Continue** (Weiter) aus.

1. Geben Sie einen benutzerfreundlichen Namen für dieses Dienstnetz ein, und wählen Sie **Finish** (Fertig stellen) aus, um den Vorgang abzuschließen.  

1. Wählen Sie **View Tasks** (Aufgaben anzeigen) aus, um die Bereitstellung zu überwachen. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot: Schaltfläche zum Anzeigen von Aufgaben.":::

   Wenn die Service Mesh-Bereitstellung erfolgreich abgeschlossen wurde, werden die Dienste grün angezeigt.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot: Grüne Indikatoren für Dienste." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Überprüfen Sie die Integrität des Dienstnetzes, indem Sie den Appliancestatus überprüfen. 

1. Wählen Sie **Interconnect** > **Appliances** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Überprüfen des Gerätestatus." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

## <a name="move"></a>Move

Anhand der folgenden Schritte erfahren Sie, wie Sie Ihre private Azure VMware Solution-Cloud in eine andere private Azure VMware Solution-Cloud in einer anderen Region verschieben. 

In diesem Abschnitt wird beschrieben, wie folgende Elemente migriert werden:

- Konfiguration des Ressourcenpools und Ordnererstellung

- VM-Vorlagen und die zugehörigen Tags

- Bereitstellung logischer Segmente basierend auf den Portgruppen der Quelle und die zugehörigen VLANs 

- Netzwerksicherheitsdienste und -gruppen

- Gatewayfirewallrichtlinie und -regeln basierend auf den Firewallrichtlinien der Quelle

### <a name="migrate-the-source-vsphere-configuration"></a>Migrieren der vSphere-Konfiguration (Quelle)

In diesem Schritt wird die vSphere-Konfiguration der Quelle kopiert und in die Zielumgebung verschoben. 

1. Verwenden Sie im vCenter der Quelle die gleiche Ressourcenpoolkonfiguration, und [erstellen Sie die gleiche Ressourcenpoolkonfiguration](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4) im vCenter des Ziels.

2. Verwenden Sie im vCenter der Quelle den gleichen VM-Ordnernamen, und [erstellen Sie den gleichen VM-Ordner](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html) im vCenter des Ziels unter **Folders** (Ordner).

3. Verwenden Sie VMware HCX, um alle VM-Vorlagen vom vCenter der Quelle zum vCenter des Ziels zu migrieren.

   1. Konvertieren Sie die vorhandenen Vorlagen in der Quelle in VMs, und migrieren Sie sie dann zum Ziel.

   2. Konvertieren Sie die VMs im Ziel in VM-Vorlagen.

4. Verwenden Sie in der Quellumgebung den gleichen VM-Tagnamen, und [erstellen Sie sie im vCenter des Ziels](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html). 

5. Verwenden Sie in der vCenter-Inhaltsbibliothek der Quelle die Option für die abonnierte Bibliothek, um die ISO-, OVF-, OVA- und VM-Vorlagen in die Inhaltsbibliothek des Ziels zu kopieren:

   1. Wenn die Inhaltsbibliothek noch nicht veröffentlicht wurde, wählen Sie die Option **Enable publishing** (Veröffentlichung aktivieren).

   2. Kopieren Sie die URL der veröffentlichten Bibliothek in der Inhaltsbibliothek der Quelle.

   3. [Erstellen Sie im Ziel eine Bibliothek mit abonnierten Inhalten](deploy-vm-content-library.md), und verwenden Sie dazu die URL in der Bibliothek der Quelle.

   4. Wählen Sie **Sync Now** (Jetzt synchronisieren) aus.


### <a name="configure-the-target-nsx-t-environment"></a>Konfigurieren der NSX-T-Umgebung (Ziel)

In diesem Schritt wird die NSX-T-Konfiguration der Quelle verwendet, um die NSX-T-Umgebung des Ziels zu konfigurieren.

>[!NOTE]
>Für die NSX-T-Konfiguration der Quelle wurden mehrere Features konfiguriert. Daher muss die NSX-T-Konfiguration der Quelle kopiert oder gelesen und in der privaten Cloud des Ziels wieder erstellt werden. Verwenden Sie die L2-Erweiterung, um die IP-Adresse und Mac-Adresse der VM beizubehalten, während Sie die Quelle zur privaten AVS-Cloud im Ziel migrieren, um Ausfallzeiten aufgrund von Änderungen an der IP-Adresse und der zugehörigen Konfiguration zu vermeiden.

1. [Konfigurieren der NSX-Netzwerkkomponenten](tutorial-nsx-t-network-segment.md), die in der Zielumgebung unter dem Standardgateway der Ebene 1 benötigt werden

1. [Erstellen der Konfiguration der Sicherheitsgruppe](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)

1. [Erstellen der Richtlinie und Regeln für verteilte Firewalls](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)

1. [Erstellen der Richtlinie und Regeln für Gatewayfirewalls](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html)

1. [Erstellen des DHCP-Serverdiensts oder des DHCP-Relaydiensts](configure-dhcp-azure-vmware-solution.md). 

1. [Konfigurieren der Portspiegelung](configure-port-mirroring-azure-vmware-solution.md)

1. [Konfigurieren der DNS-Weiterleitung](configure-dns-azure-vmware-solution.md)

1. [Konfigurieren eines neuen Gateways der Ebene 1 (nicht des Standardgateways)](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html)  Diese Konfiguration basiert auf der NSX-T-Konfiguration in der Quelle. 

### <a name="migrate-the-vms-from-the-source"></a>Migrieren der VMs von der Quelle 

In diesem Schritt wird VMware HCX verwendet, um die VMs von der Quelle zum Ziel zu migrieren. Sie haben die Möglichkeit, eine Layer-2-Erweiterung von der Quelle aus zu erstellen und HCX zu verwenden, um die VMs mithilfe von vMotion mit minimaler Unterbrechung von der Quelle zum Ziel zu übertragen. 

Neben vMotion werden auch andere Methoden wie Bulk und Cold vMotion empfohlen.  Weitere Informationen:

- [Plan an HCX Migration (Planen einer HCX-Migration)](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [Migrate Virtual Machines with HCX (Migrieren von Virtual Machines mit HCX)](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>Übernahme von erweiterten Netzwerken 

In diesem Schritt führen Sie eine abschließende Gatewayübernahme durch, um die erweiterten Netzwerke zu beenden. Zudem verschieben (migrieren) Sie die Gateways aus der Azure VMware Solution-Quellumgebung in die Zielumgebung.

>[!IMPORTANT]
>Sie müssen die Gatewayübernahme nach der Migration der VLAN-Workload zur Azure VMware Solution-Zielumgebung durchführen. Dabei darf keine VM-Abhängigkeit zwischen der Quell- und Zielumgebung bestehen.

Überprüfen Sie vor der Gatewayübernahme alle migrierten Workloaddienste und die Leistung. Sobald Anwendungs- und Webdienstbesitzer die Leistung akzeptieren (mit Ausnahme von Latenzproblemen), können Sie mit der Gatewayübernahme fortfahren.  Nach der Übernahme müssen Sie die öffentlichen DNS-A- und PTR-Einträge ändern. 

Empfehlungen von VMware finden Sie unter [Cutover of extended networks (Übernahme von erweiterten Netzwerken)](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9).


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>DNAT für öffentliche IP-Adressen bei migrierten DMZ-VMs

Bis jetzt haben Sie Workloads zur Zielumgebung migriert. Diese Anwendungsworkloads müssen über das öffentliche Internet erreichbar sein. In der Zielumgebung gibt es zwei Möglichkeiten, Anwendungen zu hosten. Für Anwendungen gibt es folgende Möglichkeiten:

- Sie können unter dem Lastenausgleich des Anwendungsgateways gehostet und veröffentlicht werden.

- Sie können über das Feature für öffentliche IP-Adressen in vWAN veröffentlicht werden.

Bei einer öffentlichen IP-Adresse handelt es sich in der Regel um eine in die Azure Firewall übersetzte Ziel-NAT. Bei DNAT-Regeln wird die Anforderung einer öffentlichen IP-Adresse anhand der Firewallrichtlinie in eine private Adresse (Webserver) mit einem Port übersetzt. Weitere Informationen finden Sie unter [Verwenden der Funktion für öffentliche IP-Adressen in Azure Virtual WAN](./enable-public-internet-access.md).

>[!NOTE]
>SNAT ist standardmäßig in Azure VMware Solution konfiguriert, sodass Sie SNAT über Konnektivitätseinstellungen in der privaten Azure VMware Solution-Cloud auf der Registerkarte „Verwalten“ aktivieren müssen.

## <a name="decommission"></a>Decommission

In diesem letzten Schritt überprüfen Sie, ob alle VM-Workloads mitsamt der Netzwerkkonfiguration erfolgreich migriert wurden. Wenn keine Abhängigkeit besteht, können Sie das HCX-Dienstnetz, die Standortkopplung und die Netzwerkkonnektivität von der Quellumgebung trennen. 

>[!NOTE]
>Nachdem Sie die private Cloud außer Betrieb genommen haben, können Sie den Vorgang nicht rückgängig machen, da die Konfiguration und die Daten dabei verlorengehen.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- [Unterstützung des Verschiebevorgangs für Microsoft.AVS](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [Leitfaden zum Verschieben von Netzwerkressourcen](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [Anleitung zum Verschieben virtueller Computer](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [Anleitung zum Verschieben von App Service-Ressourcen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

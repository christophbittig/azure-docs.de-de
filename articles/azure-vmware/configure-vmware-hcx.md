---
title: Konfigurieren von VMware HCX in Azure VMware Solution
description: Konfigurieren Sie den lokalen VMware HCX-Connector für Ihre private Azure VMware Solution-Cloud.
ms.topic: tutorial
ms.date: 07/30/2021
ms.openlocfilehash: 1249a694e01e01d4e7aa31c639c4422be2372ee4
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255472"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>Konfigurieren des lokalen VMware HCX-Connectors

Nachdem Sie das [VMware HCX-Add-On installiert](install-vmware-hcx.md) haben, können Sie den lokalen VMware HCX-Connector für Ihre private Azure VMware Solution-Cloud konfigurieren.  

In dieser Schrittanleitung führen Sie die folgenden Schritte aus:

* Koppeln Ihres lokalen VMware HCX-Connectors mit Ihrem Azure VMware Solution HCX-Cloud-Manager
* Konfigurieren von Netzwerkprofil, Computeprofil und Service Mesh
* Überprüfen des Appliancestatus und Validieren, dass die Migration möglich ist

Nachdem Sie diese Schritte ausgeführt haben, verfügen Sie über eine produktionsbereite Umgebung zum Erstellen von virtuellen Computern (VMs) und zur Migration. 

## <a name="prerequisites"></a>Voraussetzungen

- [VMware HCX-Connector](install-vmware-hcx.md) wurde installiert.

- Wenn Sie VMware HCX Enterprise verwenden möchten, stellen Sie sicher, dass Sie das [ VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)-Add-On über eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support) aktiviert haben.

- [Erforderliche Softwareversionen](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)

- Ihre lokale vSphere-Umgebung (Quellumgebung) erfüllt die [Mindestanforderungen](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

- Zwischen dem lokalen Standort und der privaten Azure VMware Solution-Cloud ist [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) mit ExpressRoute-Leitungen konfiguriert.

- [Alle erforderlichen Ports](https://ports.vmware.com/home/VMware-HCX) sind für die Kommunikation zwischen lokalen Komponenten und privater Azure VMware Solution-Cloud geöffnet.

- [Definieren der VMware HCX-Netzwerksegmente](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  Die primären Anwendungsfälle für VMware HCX sind Workloadmigrationen und Notfallwiederherstellung.

- Informationen zur Verwendung von HCX finden Sie in der [VMware HCX-Dokumentation](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html).

## <a name="add-a-site-pairing"></a>Hinzufügen einer Standortkopplung

In Ihrem Rechenzentrum können Sie den VMware HCX-Cloud-Manager in Azure VMware Solution mit dem VMware HCX-Connector verbinden oder koppeln.

> [!IMPORTANT]
> Obwohl das Tool „VMware Configuration Maximum“ angibt, dass die maximale Anzahl von Standortpaaren zwischen dem lokalen HCX-Connector und HCX Cloud Manager gleich 25 ist, schränkt die Lizenzierung dies auf 3 für HCX Advanced und 10 für HCX Enterprise Edition ein.

1. Melden Sie sich bei Ihrem lokalen vCenter an, und wählen Sie unter **Home** die Option **HCX** aus.

1. Wählen Sie unter **Infrastructure** die Option **Site Pairing** (Standortkopplung) und anschließend die Option **Connect To Remote Site** (Verbindung mit Remotestandort herstellen) aus (in der Mitte des Bildschirms).

1. Geben Sie die URL oder IP-Adresse für Azure VMware Solution HCX-Cloud-Manager ein, die Sie zuvor notiert haben `https://x.x.x.9`, sowie die Anmeldeinformationen für einen Benutzer, der über die CloudAdmin-Rolle in Ihrer privaten Cloud verfügt. Wählen Sie dann **Verbinden** aus.

   > [!NOTE]
   > So richten Sie ein Standortpaar ein:
   > * Ihr VMware HCX-Connector muss in der Lage sein, das Routing an Ihre HCX-Cloud-Manager-IP-Adresse über den Port 443 durchzuführen.
   >
   > * Für Verbindungen zur Standortkopplung wird ein Dienstkonto aus Ihrer externen Identitätsquelle, z. B. Active Directory, empfohlen. Weitere Informationen zur Einrichtung separater Konten für verbundene Dienste finden Sie unter [Zugriffs- und Identitätskonzepte](./concepts-identity.md).

   Ein Bildschirm wird angezeigt, auf dem der VMware HCX-Cloud-Manager in Azure VMware Solution mit dem lokalen VMware HCX-Connector verbunden (gekoppelt) ist.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot: Websitekopplung des HCX-Managers in Azure VMware Solution und des VMware HCX-Connectors":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: HCX-Standortkopplung](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

## <a name="create-network-profiles"></a>Erstellen von Netzwerkprofilen

Vom VMware HCX-Connector wird eine Teilmenge virtueller Geräte (automatisiert) bereitgestellt, die mehrere IP-Segmente erfordern. Wenn Sie Ihre Netzwerkprofile erstellen, verwenden Sie die IP-Segmente, die Sie in der [Planungsphase](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) festgelegt haben.  Sie erstellen vier Netzwerkprofile:

   - Verwaltung
   - vMotion
   - Replikation
   - Uplink

1. Wählen Sie unter **Infrastruktur** die Option **Interconnect** > **Multi-Site Service Mesh (Dienstnetz mit mehreren Standorten)**  > **Network Profiles (Netzwerkprofile)**  > **Create Network Profile (Netzwerkprofil erstellen)** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot: Erstellungsort eines Netzwerkprofils im vSphere-Client" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Wählen Sie für jedes Netzwerkprofil das Netzwerk und die Portgruppe aus, geben Sie einen Namen an, und erstellen Sie den IP-Pool des Segments. Klicken Sie anschließend auf **Erstellen**.

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot: Details eines neuen Netzwerkprofils" lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: HCX-Netzwerkprofil](https://www.youtube.com/embed/O0rU4jtXUxc).

## <a name="create-a-compute-profile"></a>Erstellen eines Computeprofils

1. Wählen Sie unter **Infrastructure** (Infrastruktur) Folgendes aus: **Interconnect** > **Compute Profiles** > **Create Compute Profile** („Verbinden“ > „Compute-Profile“ > „Compute-Profil erstellen“).

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Starten der Erstellung eines Computeprofils." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Geben Sie einen Namen für das Profil ein, und wählen Sie **Continue** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot: Eintrag eines Computeprofilnamens und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Wählen Sie die Dienste aus, die aktiviert werden sollen, z. B. für Migration, Netzwerkerweiterung oder Notfallwiederherstellung, und wählen Sie dann **Continue** (Weiter) aus.

   > [!NOTE]
   > Im Allgemeinen ändert sich hier nichts.

1. Wählen Sie unter **Select Service Resources** (Dienstressourcen auswählen) mindestens eine Dienstressource (Cluster) aus, um die ausgewählten VMware HCX-Dienste zu aktivieren.

1. Wenn Sie die Cluster in Ihrem lokalen Rechenzentrum sehen, wählen Sie **Continue** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot: Ausgewählten Dienstressourcen und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Wählen Sie unter **Select Datastore** (Datenspeicher auswählen) die Datenspeicher-Speicherressource für die Bereitstellung der VMware HCX Interconnect-Appliances aus. Klicken Sie anschließend auf **Weiter**.

   Wenn mehrere Ressourcen ausgewählt sind, verwendet VMware HCX die erste ausgewählte Ressource, bis deren Kapazität erschöpft ist.

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot: Ausgewählte Datenspeicherressource und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. Wählen Sie unter **Select Management Network Profile** (Verwaltungsnetzwerkprofil auswählen) das Verwaltungsnetzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot: Auswahl eines Verwaltungsnetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Wählen Sie unter **Select Uplink Network Profile** (Uplinknetzwerkprofil auswählen) das Uplinknetzwerkprofil aus, das Sie in der vorherigen Prozedur erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot: Auswahl eines Uplinknetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Wählen Sie unter **Select vMotion Network Profile** (vMotion-Netzwerkprofil auswählen) das vMotion-Netzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot: Auswahl eines vMotion-Netzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Wählen Sie unter **Select vSphere Replication Network Profile** (vSphere-Replikationsnetzwerkprofil auswählen) das vMotion-Replikationsnetzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot: Auswahl eines Replikationnetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Wählen Sie unter **Select Distributed Switches for Network Extensions** (Verteilte Switches für Network Extensions auswählen) die Switches aus, die die virtuellen Computer, die zu Azure VMware Solution migriert werden sollen, in einem erweiterten Schicht-2-Netzwerk enthalten. Klicken Sie anschließend auf **Weiter**.

   > [!NOTE]
   > Falls Sie keine virtuellen Computer in erweiterten Schicht-2-Netzwerken (L2) migrieren, können Sie diesen Schritt überspringen.

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot: Auswahl eines verteilten virtuellen Switches und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Überprüfen Sie die Verbindungsregeln, und wählen Sie **Continue** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot: Verbindungsregeln und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Wählen Sie **Finish** (Fertig stellen) aus, um das Compute-Profil zu erstellen.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot: Computeprofilinformationen." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Computeprofil](https://www.youtube.com/embed/e02hsChI3b8).

## <a name="create-a-service-mesh"></a>Erstellen eines Service Mesh

>[!IMPORTANT]
>Stellen Sie sicher, dass die Ports UDP 500/4500 zwischen den Uplink-Netzwerkprofiladressen des lokalen VMware HCC-Connectors und den Azure VMware Solution HCX-Cloudnetzwerk-Profiladressen geöffnet sind.


1. Wählen Sie unter **Infrastructure** (Infrastruktur) die Option **Interconnect** (Verbinden) > **Service Mesh** (Dienstnetz) > **Create Service Mesh** (Dienstnetz erstellen) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot: Auswahlmöglichkeiten zum Starten der Erstellung eines Service Mesh." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Überprüfen Sie die vorab aufgefüllten Standorte, und wählen Sie dann **Continue** (Weiter) aus.

   > [!NOTE]
   > Wenn dies Ihre erste Dienstnetzkonfiguration ist, müssen Sie diesen Bildschirm nicht ändern.

1. Wählen Sie das Quellcomputeprofil und das Remotecomputeprofil aus den Dropdownlisten aus, und wählen Sie dann **Continue** (Weiter) aus.

   Mit der Auswahl werden die Ressourcen definiert, in denen VMs VMware HCX-Dienste nutzen können.

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot: Auswählen des Quellcomuteprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot: Auswählen des Remotecomputeprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Überprüfen Sie die Dienste, die aktiviert werden, und wählen Sie **Continue** (Weiter) aus.

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

   >[!NOTE]
   >Nach der Service Mesh-Einrichtung werden Ihnen möglicherweise ein neuer Datenspeicher und ein neuer Host in Ihrer privaten Cloud angezeigt. Dies ist ein völlig normales Verhalten nach der Service Mesh-Einrichtung.
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="Screenshot: HCX Service Mesh-Datenspeicher und -Host" lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Service Mesh](https://www.youtube.com/embed/COY3oIws108).

## <a name="create-a-network-extension"></a>Erstellen einer Netzwerkerweiterung

Dies ist ein optionaler Schritt zum Erweitern von Netzwerken aus Ihrer lokalen Umgebung um Azure VMware Solution.

1. Wählen Sie unter **Services** (Dienste) Folgendes aus: **Network Extension** > **Create a Network Extension** („Netzwerkerweiterung“ > „Netzwerkerweiterung erstellen“).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Starten der Erstellung einer Netzwerkerweiterung." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wählen Sie jedes der Netzwerke aus, die Sie um Azure VMware Solution erweitern möchten, und wählen Sie dann **Next** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot: Auswahl eines Netzwerks.":::

1. Geben Sie die lokale Gateway-IP-Adresse für jedes der Netzwerke ein, die Sie erweitern möchten, ein, und wählen Sie dann **Submit** (Senden) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot: Eintrag einer Gateway-IP-Adresse.":::

   Es dauert einige Minuten, bis die Netzwerkerweiterung abgeschlossen ist. Sobald dies der Fall ist, erfolgt die Statusänderung in **Extension complete** (Erweiterung abgeschlossen).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot: Status „Erweiterung abgeschlossen“.":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Netzwerkerweiterung](https://www.youtube.com/embed/gYR0nftKui0).

## <a name="next-steps"></a>Nächste Schritte

Wenn der Tunnelstatus für die HCX Interconnect-Appliance **UP** lautet und grün angezeigt wird, können Sie virtuelle Azure VMware Solution-Computer mithilfe von VMware HCX migrieren und schützen. Azure VMware Solution unterstützt Workloadmigrationen (mit oder ohne Netzwerkerweiterung). Sie können weiterhin Workloads in ihrer vSphere-Umgebung migrieren sowie Netzwerke lokal erstellen und VMs in diesen Netzwerken bereitstellen. Weitere Informationen finden Sie in der [VMware HCX-Dokumentation](https://docs.vmware.com/en/VMware-HCX/index.html).


---
title: Bereitstellen der Notfallwiederherstellung mit VMware Site Recovery Manager
description: Stellen Sie die Notfallwiederherstellung mit VMware Site Recovery Manager (SRM) in Ihrer privaten Azure VMware Solution Cloud bereit.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 116c7b672874c1e012b4d9abadacb10bc4ca895f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547243"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>Bereitstellen der Notfallwiederherstellung mit VMware Site Recovery Manager

In diesem Artikel wird erläutert, wie Sie die Notfallwiederherstellung für lokale virtuelle VMware-Computer (VMs) oder Azure VMware Solution-basierte VMs implementieren.  Die Lösung in diesem Artikel verwendet [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) und vSphere Replication mit Azure VMware Solution. Instanzen von SRM und Replikationsservern werden sowohl am geschützten Standort als auch am Wiederherstellungsstandort bereitgestellt.       

SRM ist eine Notfallwiederherstellungslösung, mit der die Downtime der virtuellen Computer in einer Azure VMware Solution-Umgebung bei einem Notfall minimiert werden soll. SRM automatisiert und orchestriert Failover und Failback und sorgt so für eine minimale Downtime bei einem Notfall. Außerdem stellen integrierte, unterbrechungsfreie Tests sicher, dass Ihre Zielstellungen im Hinblick auf die Wiederherstellungszeit erreicht werden. Insgesamt vereinfacht SRM die Verwaltung durch Automatisierung und stellt schnelle und hochgradig vorhersagbare Wiederherstellungszeiten sicher.  

vSphere Replication ist die Hypervisor-basierte Replikationstechnologie von VMware für vSphere-VMs. Hierdurch werden VMs vor teilweisen oder vollständigen Standortausfällen geschützt. Darüber hinaus vereinfacht es den DR-Schutz durch eine speicherunabhängige, VM-zentrierte Replikation. vSphere Replication wird jeweils auf Basis der entsprechenden VM konfiguriert, sodass es mehr Kontrolle darüber bietet, welche VMs repliziert werden.

In diesem Artikel wird erläutert, wie Sie die Notfallwiederherstellung für lokale virtuelle VMware-Computer (VMs) oder Azure VMware Solution-basierte VMs implementieren.


## <a name="supported-scenarios"></a>Unterstützte Szenarios

SRM unterstützt Sie beim Planen, Testen und Ausführen der Wiederherstellung von VMs zwischen einem geschützten vCenter Server-Standort und einem vCenter Server-Wiederherstellungsstandort. Sie können SRM mit Azure VMware Solution mit den folgenden beiden Notfallwiederherstellungsszenarien verwenden: 

- Notfallwiederherstellung von lokaler VMware auf private Azure VMware Solution Cloud 
- Notfallwiederherstellung in der privaten Cloud von der primären Azure VMware Solution zur sekundären Azure VMware Solution 

Das Diagramm zeigt das Szenario zur Bereitstellung der primären Azure VMware Solution auf die sekundäre Azure VMware Solution.

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="Diagramm der Notfallwiederherstellungslösung VMware Site Recovery Manager (SRM) in Azure VMware Solution." border="false":::

Sie können SRM verwenden, um verschiedene Wiederherstellungstypen zu implementieren, z. B.:

- **Die geplante Migration** beginnt, wenn sowohl primäre als auch sekundäre Azure VMware Solution-Standorte laufen und voll funktionsfähig sind. Dabei handelt es sich um eine geordnete Migration von virtuellen Computern vom geschützten Standort zum Wiederherstellungsstandort, bei der im Zuge der ordnungsgemäßen Migration von Workloads kein Datenverlust zu erwarten ist. 

- **Die Notfallwiederherstellung** mithilfe von SRM kann aufgerufen werden, wenn der geschützte Azure VMware Solution-Standort unerwartet offline geschaltet wird. Site Recovery Manager orchestriert den Wiederherstellungsprozess mit den Replikationsmechanismen, um Datenverluste und Systemdowntimes zu minimieren.

   In Azure VMware Solution können über die Verwendung von SRM in Kombination mit vSphere Replication nur einzelne VMs auf einem Host geschützt werden.

- Die Funktion **Bidirektionaler Schutz** verwendet eine einzelne Gruppe von gekoppelten SRM-Standorten, um VMs in beide Richtungen zu schützen. Jeder Standort kann gleichzeitig ein geschützter Standort und ein Wiederherstellungsstandort sein, jedoch für eine andere Gruppe von VMs.

>[!IMPORTANT]
>Azure VMware Solution unterstützt folgende Komponenten nicht: 
>
>- Schutzgruppen mit array-basierten Replikations- und Speicherrichtlinien 
>- VVOLs-Schutzgruppen 
>- SRM-IP-Anpassung mithilfe von SRM-Befehlszeilentools
>- 1:n- und n:1-Topologie 
>- Benutzerdefinierter SRM-Plug-In-Bezeichner oder Erweiterungs-ID


## <a name="deployment-workflow"></a>Bereitstellungsworkflow

Das Workflowdiagramm zeigt den Workflow von der primären Azure VMware Solution zur sekundären Azure VMware Solution. Darüber hinaus werden Schritte gezeigt, die innerhalb von Azure-Portal und der VMware-Umgebungen von Azure VMware Solution erforderlich sind, um den End-to-End-Schutz von VMs zu erreichen. 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="Diagramm des Bereitstellungsworkflows für VMware Site Recovery Manager auf Azure VMware Solution." border="false":::

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie dem Remotebenutzer explizit die Rollen VRM-Administrator und SRM-Administrator im Remote-vCenter bereitgestellt haben.

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>Szenario: Datenverkehr von lokalen Workloads zu Azure VMware Solution 

- Private Azure VMware Solution Cloud, die als sekundäre Region bereitgestellt wird.

- [DNS-Auflösung](configure-dns-azure-vmware-solution.md) für lokale SRM- und virtuelle Cloudappliances.

   >[!NOTE]
   >Für private Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, können Sie die private DNS-Auflösung konfigurieren. Öffnen Sie für private Clouds, die vor dem 1. Juli 2021 erstellt wurden und eine private DNS-Auflösung benötigen, eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support), um eine **private DNS-Konfiguration** anzufordern.

- ExpressRoute-Konnektivität zwischen lokalem System und Azure VMware Solution - 2 Gbps.

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>Szenario: Primäre Azure VMware Solution zur sekundären

- Die private Azure VMware Solution Cloud muss in der primären und sekundären Region bereitgestellt werden.

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="Screenshot: zwei private Azure VMware Solution Clouds in separaten Regionen":::
 
- Konnektivität, z. B. ExpressRoute-Global Reach, zwischen der privaten Quell- und Ziel-AVS-Cloud.

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="Screenshot: Konnektivität zwischen der privaten Quell- und Zielcloud":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>Installieren von SRM in Azure VMware Solution

1. Installieren Sie in Ihrem lokalen Rechenzentrum VMware SRM und vSphere.

   >[!NOTE]
   >Verwenden Sie das Bereitstellungsmodell mit der [zwei-Standort-Topologie mit einer vCenter Server-Instanz pro PSC](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Stellen Sie außerdem sicher, dass die [erforderlichen Netzwerkports für vSphere Replication](https://kb.VMware.com/s/article/2087769) geöffnet sind.

1. Wählen Sie in Ihrer privaten Azure VMware Solution Cloud unter **Verwalten** **Add-ons** > **Notfallwiederherstellung** aus.

   Der CloudAdmin-Standardbenutzer in der privaten Azure VMware Solution-Cloud verfügt nicht über ausreichende Berechtigungen zum Installieren von VMware SRM oder vSphere Replication. Der Installationsvorgang umfasst mehrere Schritte, die im Abschnitt [Voraussetzungen](#prerequisites) beschrieben sind. Stattdessen können Sie VMware SRM mit vSphere Replication als Add-On-Dienst aus Ihrer privaten Azure VMware Solution Cloud installieren.

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="Screenshot: private Azure VMware Solution Cloud zum Installieren von VMware SRM mit vSphere Replication als Add-On" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. Wählen Sie aus der **Disaster Recovery Solution**-Dropdownliste **VMware Site Recovery Manager (SRM) – vSphere Replication** aus. 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="Screenshot: Registerkarte „Notfallwiederherstellung“ unter „Add-ons“ mit ausgewählter Option „VMware Site Recovery Manager (SRM) - vSphere Replication“." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. Geben Sie den Lizenzschlüssel an, wählen Sie „Ich stimme den Geschäftsbedingungen zu“ aus und wählen Sie dann **Installieren** aus.

   >[!NOTE]
   >Wenn Sie den Lizenzschlüssel nicht angeben, wird SRM in einem Auswertungsmodus installiert. Die Lizenz wird ausschließlich zum Aktivieren von VMware SRM verwendet.
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="Screenshot: Registerkarte „Notfallwiederherstellung“ unter „Add-ons“ mit ausgewähltem Feld „Lizenzschlüssel“" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>Installieren der vSphere Replication-Appliance

Nachdem die SRM-Appliance erfolgreich installiert wurde, müssen Sie die vSphere Replication-Appliances installieren. Jeder Replikationsserver bietet Platz für bis zu 200 geschützte VMs. Skalieren Sie je nach Ihren Anforderungen ein ab oder auf. 

1. Wählen Sie aus der **Replikation mithilfe von**-Dropdownliste in der Registerkarte **Notfallwiederherstellung** **vSphere Replication** aus.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="Screenshot: ausgewählte vSphere Replication für die Option „Replikation mithilfe von“":::

1. Verschieben Sie den vSphere-Serverschieberegler, um die Anzahl der Replikationsserver anzugeben, die Sie basierend auf der zu schützenden VMs-Anzahl haben möchten. Wählen Sie dann **Installieren** aus.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="Screenshot: Erhöhen oder Verringern der Anzahl von Replikationsservern":::

1. Überprüfen Sie nach der Installation, das sowohl die SRM als auch die vSphere Replication-Appliances installiert sind.

   >[!TIP]
   >Die Schaltfläche „Deinstallieren“ gibt an, dass sowohl SRM als auch die vSphere Replication-Appliances derzeit installiert sind.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="Screenshot: Installation von SRM und Replikationsappliance":::
  

## <a name="configure-site-pairing-in-vcenter"></a>Konfigurieren der Standortkopplung in vCenter

Nach der Installation von VMware SRM und vSphere Replication müssen Sie die Konfiguration und die Standortkopplung in vCenter abschließen.

1. Anmelden bei vCenter als cloudadmin@vsphere.local.

1. Navigieren Sie zu **Site Recovery**, überprüfen Sie den Status von vSphere Replication und VMware SRM und wählen Sie dann **OPEN Site Recovery** aus, um den Client zu starten.

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="Screenshot: vSphere-Client mit korrektem Installationsstatus für vSphere Replication und Site Recovery Manager" border="true":::


1. Wählen Sie auf der neuen Registerkarte, die geöffnet wird, im Client Site Recovery (SR) die Option **NEUES STANDORTPAAR** aus.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="Screenshot: vSphere-Client mit ausgewählter Schaltfläche „Neues Standortpaar“ für Site Recovery" border="true":::

1. Geben Sie die Informationen für den Remotestandort ein und wählen Sie dann **WEITER** aus.

   >[!NOTE]
   >Eine private Azure VMware Solution Cloud wird mit einem eingebetteten Platform Services Controller (PSC) betrieben, sodass nur ein lokales vCenter ausgewählt werden kann. Wenn das Remote-vCenter einen eingebetteten Platform Service Controller (PSC) verwendet, verwenden Sie zur Angabe des PSC den FQDN (oder seine IP-Adresse) und den Port von vCenter. 
   >
   >Zum Ausführen dieser Kopplungen muss der Remotebenutzer über ausreichende Berechtigungen verfügen. Eine einfache Möglichkeit, dies sicherzustellen, besteht darin, diesem Benutzer die VRM-Administrator- und SRM-Administratorrollen in Remote-vCenter zuzuweisen. Für eine private Azure VMware Solution Remote-Cloud wird cloudadmin mit diesen Rollen konfiguriert.

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="Screenshot: Standortdetails für das neue Standortpaar" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. Wählen Sie **VERBINDEN** aus, um das Zertifikat für das Remote-vCenter zu akzeptieren.

   An diesem Punkt sollte der Client die VRM- und SRM-Appliances auf beiden Seiten als zu koppelnde Dienste erkennen.

1. Wählen Sie die zu koppelnden Appliances und dann **WEITER** aus.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="Screenshot: Details zu vCenter Server und Diensten für das neue Standortpaar" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. Wählen Sie **VERBINDEN** aus, um die Zertifikate für den Remote-VMware-SRM und das Remote-vCenter (erneut) zu akzeptieren.

1. Wählen Sie **VERBINDEN** aus, um die Zertifikate für den lokalen VMware-SRM und das lokale vCenter zu akzeptieren.

1. Überprüfen Sie die Einstellungen, und wählen Sie dann **Fertigstellen** aus.

   Bei erfolgreicher Fertigstellung zeigt der Client ein weiteres Panel für die Kopplung an. War die Fertigstellung nicht erfolgreich, wird ein Alarm gemeldet.

1. Wählen Sie unten in der rechten Ecke den Doppelpfeil nach oben aus, um das Panel zu erweitern und die **Zuletzt durchgeführten Aufgaben** und die **Alarmmeldungen** anzuzeigen.

   >[!NOTE]
   >Die Aktualisierung des SR-Clients dauert manchmal sehr lange. Wenn ein Vorgang scheinbar zu lange dauert oder „hängen bleibt“, wählen Sie das Aktualisierungssymbol in der Menüleiste aus. 

1. Wählen Sie **DETAILS ANZEIGEN** aus, um das Panel für die Remotestandortkopplung zu öffnen. Daraufhin wird ein Dialogfeld für die Anmeldung beim Remote-vCenter geöffnet.

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="Screenshot: Details des neuen Standortpaars für Site Recovery Manager und vSphere Replication" border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. Geben Sie den Benutzernamen mit ausreichenden Berechtigungen für die Replikation und Standortwiederherstellung ein und wählen Sie dann **ANMELDEN** aus. 

   Für den Kopplungsvorgang ist die Anmeldung, bei der es sich häufig um einen anderen Benutzer handelt, eine einmalige Aktion zum Einrichten der Kopplung. Der SR-Client erfordert diese Anmeldung jedes Mal, wenn der Client gestartet wird, um mit der Kopplung zu arbeiten.

   >[!NOTE] 
   >Der Benutzer mit ausreichenden Berechtigungen sollte über **VRM-Administrator-** und **SRM-Administratorrollen** verfügen, die ihm im Remote-vCenter zugewiesen sind. Der Benutzer sollte auch Zugriff auf den Remote-vCenter-Bestand haben, z. B. Ordner und Datenspeicher. Für eine private Azure VMware Solution Remote-Cloud verfügt der cloudadmin-Benutzer über die entsprechenden Berechtigungen und Zugriffe. 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="Screenshot: Anmeldeinformationen für vCenter Server" border="true":::

   Es wird eine Warnmeldung angezeigt, die angibt, dass der eingebettete VRS in der lokalen VRM nicht ausgeführt wird.  Dies liegt daran, dass Azure VMware Solution den eingebetteten VRS nicht in einer privaten Azure VMware Solution Cloud verwendet.  Stattdessen werden VRS-Appliances verwendet. 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="Screenshot: Zusammenfassung des Standortpaars für Site Recovery Manager und vSphere Replication" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>SRM-Schutz, erneuter Schutz und Failback

Nachdem Sie die Standortkopplung erstellt haben, befolgen Sie die unten beschriebene VMware-Dokumentation zum End-to-End-Schutz von VMs über Azure-Portal.

- [Verwenden von vSphere Replication mit Site Recovery Manager (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [Bestandszuordnungen für Array-basierte Replikationsschutzgruppen und vSphere-Replication-Schutzgruppen (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [Informationen zu Placeholder Virtual Machines (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [vSphere-Replication-Schutzgruppen (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [Erstellen, Testen und Ausführen von Wiederherstellungsplänen (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [Konfigurieren eines Wiederherstellungsplans (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [Anpassen von IP-Eigenschaften für Virtual Machines (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [Erneutes Schützen von Virtual Machines mit vSphere Replication (vmware.com) durch Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [Ausführen eines Failbacks (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)

   >[!NOTE]
   >Wenn IP-Anpassungsregeln für Netzwerkzuordnungen zwischen der AVS-Umgebung und der lokalen Umgebung definiert wurden, werden diese Regeln aufgrund eines [bekannten Problems](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html#knownissues) mit SRM 8.3.0 beim Failback von der AVS-Umgebung zur lokalen Umgebung nicht angewendet. Sie können diese Einschränkung umgehen, indem Sie den Schutz von allen VMs in der Schutzgruppe entfernen und dann den Schutz für sie neu konfigurieren, bevor Sie das Failback initiieren.


## <a name="ongoing-management-of-your-srm-solution"></a>Fortlaufende Verwaltung Ihrer SRM-Lösung

Microsoft möchte zwar die Installation von VMware SRM und vSphere Replication in einer privaten Azure VMware Solution Cloud vereinfachen, aber Sie sind für die Verwaltung Ihrer Lizenz und den täglichen Betrieb der Notfallwiederherstellungslösung verantwortlich. 

## <a name="scale-limitations"></a>Einschränkungen bei der Skalierung

Skalierungseinschränkungen gelten pro privater Cloud.

| Konfiguration | Begrenzung |
| --- | --- |
| Anzahl der geschützten VMs  | 1000  |
| Anzahl VMs pro Wiederherstellungsplan  | 1000  |
| Anzahl von Schutzgruppen pro Wiederherstellungsplan  | 250  |
| RPO-Werte  | 5 Min. oder mehr*  |
| Gesamtanzahl virtueller Computer pro Schutzgruppe  | 500  |
| Gesamtzahl der Wiederherstellungspläne  | 250  |

\* Informationen zu RPOs (Recovery Point Objective) von unter 15 Minuten finden Sie unter [Funktionsweise von 5-Minuten-RPOs](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html) im _vSphere Replication-Verwaltungshandbuch_.



## <a name="srm-licenses"></a>SRM-Lizenzen

Sie können VMware SRM mit einer Evaluierungslizenz oder einer Produktionslizenz installieren.  Die Evaluierungslizenz ist 60 Tage lang gültig. Nach Ablauf des Evaluierungszeitraums müssen Sie eine Produktionslizenz für VMware SRM erwerben. 

Sie können bereits vorhandene lokale VMware SRM-Lizenzen nicht für Ihre private Azure VMware Solution Cloud verwenden. Arbeiten Sie mit Ihren Vertriebsteams und VMware zusammen, um eine neue laufzeitbasierte Produktionslizenz für VMware SRM zu erwerben. 

Sobald eine Produktionslizenz von SRM erworben wurde, können Sie das Azure VMware Solution-Portal verwenden, um SRM mit der neuen Produktionslizenz zu aktualisieren. 


## <a name="uninstall-srm"></a>Deinstallieren von SRM 

Wenn Sie SRM nicht mehr benötigen, müssen Sie es vollständig deinstallieren. Bevor Sie SRM deinstallieren, müssen Sie alle SRM-Konfigurationen von beiden Standorten in der richtigen Reihenfolge entfernen. Wenn Sie vor der Deinstallation von SRM nicht alle Konfigurationen entfernen, verbleiben einige SRM-Komponenten, z. B. Platzhalter-VMs, möglicherweise in der Azure VMware Solution-Infrastruktur.

1. Wählen Sie im vSphere-Client oder vSphere-Webclient **Site Recovery** > **Site Recovery öffnen** aus.

2. Wählen Sie auf der Home-Registerkarte **Site Recovery** ein Standortpaar und dann **Details anzeigen** aus.

3. Wählen Sie die Registerkarte **Wiederherstellungspläne** aus, klicken Sie mit der rechten Maustaste auf einen Wiederherstellungsplan und wählen Sie dann **Löschen** aus.

   >[!NOTE]
   >Wiederherstellungspläne, die gerade ausgeführt werden, können nicht gelöscht werden.

4. Wählen Sie zunächst die Registerkarte **Schutzgruppen**, dann eine Schutzgruppe und schließlich die Registerkarte **Virtual Machines** aus.

5. Markieren Sie alle virtuellen Computer und wählen Sie mit der rechten Maustaste **Schutz entfernen** aus.

   Durch das Entfernen des Schutzes von einem virtuellen Computer wird die Platzhalter-VM vom Wiederherstellungsstandort gelöscht. Wiederholen Sie diesen Vorgang für alle Schutzgruppen.

6. Klicken Sie in der Registerkarte **Schutzgruppen** mit der rechten Maustaste auf eine Schutzgruppe und wählen Sie **Löschen** aus.

   >[!NOTE] 
   >Sie können keine Schutzgruppe löschen, die in einem Wiederherstellungsplan enthalten ist. Sie können keine vSphere Replication-Schutzgruppen löschen, die virtuelle Computer enthalten, auf denen der Schutz noch konfiguriert ist.

7. Wählen Sie **Standortpaar** > **Konfigurieren** aus und entfernen Sie alle Bestandszuordnungen.

   a. Wählen Sie die Registerkarten **Netzwerkzuordnungen**, **Ordnerzuordnungen** und **Ressourcenzuordnungen** aus.

   b. Wählen Sie auf jeder Registerkarte einen Standort aus, klicken Sie mit der rechten Maustaste auf eine Zuordnung und wählen Sie dann **Löschen** aus.

8. Wählen Sie für beide Standorte **Platzhalterdatenspeicher** aus, klicken Sie mit der rechten Maustaste auf den Platzhalterdatenspeicher und wählen Sie dann **Entfernen** aus.

9. Wählen Sie **Standortverbindung** > **Zusammenfassung** und dann **Standortverbindung unterbrechen** aus.

   >[!NOTE] 
   >Durch das Unterbrechen der Standortkopplung werden alle Informationen im Zusammenhang mit der Registrierung von Site Recovery Manager bei Site Recovery Manager, vCenter Server und der Platform Services Controller am Remotestandort entfernt.

10. Wählen Sie in Ihrer privaten Cloud unter **Verwalten** **Add-ons** > **Notfallwiederherstellung** und dann **Replikations-Appliances deinstallieren** aus.

11. Nachdem die Replikationsappliances deinstalliert wurden, wählen Sie auf der Registerkarte **Notfallwiederherstellung** **Deinstallieren für Site Recovery Manager** aus.

12. Wiederholen Sie diese Schritte am sekundären Azure VMware Solution Standort.


## <a name="support"></a>Support 

VMware SRM ist eine Notfallwiederherstellungslösung von VMware.  

Microsoft unterstützt nur die Installation/Deinstallation von SRM und vSphere Replication Manager sowie das Hoch-/Herunterskalieren von vSphere Replication-Appliances innerhalb von Azure VMware Solution. 

Wenden Sie sich bei allen anderen Problemen (wie Konfiguration und Replikation) an VMware.

Die Supportteams von VMware und Microsoft treten bei Bedarf miteinander in Kontakt, um SRM-Probleme auf Azure VMware Solution zu beheben.


## <a name="references"></a>References

- [VMware Site Recovery Manager-Dokumentation](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [Kompatibilitätsmatrizen für VMware Site Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [Versionshinweise zu VMware SRM 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [Dokumentation zur VMware vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [Kompatibilitätsmatrizen für vSphere-Replication 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Betriebsgrenzwerte für Site Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [Betriebsgrenzwerte von vSphere Replication 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [Berechnen der Bandbreite für vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [SRM-Installation und -Konfiguration](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [Verwaltung von vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.2/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [Voraussetzungen und bewährte Methoden für die SRM-Installation](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [Netzwerkports für SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [Netzwerkports für vSphere Replication](https://kb.vmware.com/s/article/2087769)

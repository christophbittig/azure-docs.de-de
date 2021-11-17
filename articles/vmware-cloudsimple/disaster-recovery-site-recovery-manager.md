---
title: Azure VMware Solution by CloudSimple – Einrichten der privaten Cloud als Standort für die Notfallwiederherstellung mit VMware Site Recovery Manager
description: Hier wird beschrieben, wie Sie Ihre private CloudSimple-Cloud als Standort für die Notfallwiederherstellung mithilfe von VMware Site Recovery Manager einrichten.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2408fe3cee91efcaa6dfa734548d57f226e7f545
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322529"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Einrichten der privaten Cloud als Notfallwiederherstellungsziel mit VMware Site Recovery Manager

Sie können Ihre private CloudSimple-Cloud als Notfallwiederherstellungsstandort für lokale VMware-Workloads verwenden.

Die Notfallwiederherstellungslösung basiert auf vSphere Replication und VMware Site Recovery Manager (SRM). Auf ähnliche Weise können Sie Ihre private Cloud als primären Standort aktivieren, der durch den lokalen Wiederherstellungsstandort geschützt wird.

Vorteile der CloudSimple-Lösung:

* Sie müssen kein spezielles Rechenzentrum für die Notfallwiederherstellung einrichten.
* Sie können die Azure-Standorte nutzen, an denen CloudSimple bereitgestellt ist und für weltweite geografische Resilienz sorgt.
* Sie ermöglicht Ihnen die Reduzierung der Bereitstellungs- und Gesamtbetriebskosten für die Einrichtung der Notfallwiederherstellung.

Die CloudSimple-Lösung erfordert die folgenden Schritte:

* Installieren, konfigurieren und verwalten Sie vSphere Replication und SRM in Ihrer privaten Cloud.
* Stellen Sie eigene Lizenzen für SRM bereit, wenn die private Cloud der geschützte Standort ist. Sie benötigen keine zusätzlichen SRM-Lizenzen für den CloudSimple-Standort, wenn er als Wiederherstellungsstandort verwendet wird.

Diese Lösung bietet Ihnen die vollständige Kontrolle über vSphere Replication und SRM. Die vertraute Benutzeroberfläche, API und CLI ermöglichen die Verwendung der vorhandenen Skripts und Tools.

![Site Recovery Manager-Bereitstellung](media/srm-deployment.png)

Sie können alle Versionen von vRA und SRM verwenden, die mit Ihrer privaten Cloud und lokalen Umgebung kompatibel sind. In den Beispielen in dieser Anleitung werden vRA 6.5 und SRM 6.5 verwendet. Diese Versionen sind mit der von CloudSimple unterstützten Version vSphere 6.5 kompatibel.

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

In den folgenden Abschnitten wird beschrieben, wie Sie mit SRM eine Notfallwiederherstellungslösung in Ihrer privaten Cloud bereitstellen.

1. [Überprüfen, ob die VMware-Produktversionen kompatibel sind](#verify-that-vmware-product-versions-are-compatible)
2. [Schätzen der Größe der Umgebung für die Notfallwiederherstellung](#estimate-the-size-of-your-dr-environment)
3. [Erstellen einer privaten Cloud für Ihre Umgebung](#create-a-private-cloud-for-your-environment)
4. [Einrichten privater Cloudnetzwerke für die SRM-Lösung](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Einrichten einer Site-to-Site-VPN-Verbindung zwischen dem lokalen Netzwerk und der privaten Cloud und Öffnen der erforderlichen Ports](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Einrichten von Infrastrukturdiensten in Ihrer privaten Cloud](#set-up-infrastructure-services-in-your-private-cloud)
7. [Installieren der vSphere Replication-Appliance in der lokalen Umgebung](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installieren der vSphere Replication-Appliance in der privaten Cloud](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Installieren des SRM-Servers in der lokalen Umgebung](#install-srm-server-in-your-on-premises-environment)
10. [Installieren des SRM-Servers in der privaten Cloud](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Überprüfen, ob die VMware-Produktversionen kompatibel sind

Für die Konfigurationen in dieser Anleitung müssen die folgenden Kompatibilitätsanforderungen erfüllt sein:

* In der privaten Cloud und der lokalen Umgebung muss die gleiche Version von SRM bereitgestellt werden.
* In der privaten Cloud und der lokalen Umgebung muss die gleiche Version von vSphere Replication bereitgestellt werden.
* Die Versionen von PSC (Platform Services Controller) in der privaten Cloud und der lokalen Umgebung müssen kompatibel sein.
* Die Versionen von vCenter in der privaten Cloud und der lokalen Umgebung müssen kompatibel sein.
* Die Versionen von SRM und vSphere Replication müssen miteinander und mit den Versionen von PSC und vCenter kompatibel sein.

Links zur relevanten VMware-Dokumentation und Kompatibilitätsinformationen finden Sie in der Dokumentation zu [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html).

Um die Versionen von vCenter und PSC in Ihrer privaten Cloud zu ermitteln, öffnen Sie das CloudSimple-Portal. Wechseln Sie zu **Ressourcen**, wählen Sie Ihre private Cloud aus, und klicken Sie auf die Registerkarte **vSphere Management Network**.

![Versionen von vCenter und PSC in der privaten Cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Schätzen der Größe der Umgebung für die Notfallwiederherstellung

1. Vergewissern Sie sich, dass die identifizierte lokale Konfiguration innerhalb der unterstützten Grenzwerte liegt. Die Grenzwerte für SRM 6.5 sind im Artikel [Operational Limits for Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110) (Betriebsgrenzwerte für Site Recovery Manager 6.5, in englischer Sprache) der VMware-Knowledge Base dokumentiert.
2. Stellen Sie sicher, dass ausreichend Netzwerkbandbreite zur Verfügung steht, damit die Anforderungen an Workloadgröße und RPO erfüllt werden können. Der Artikel [Berechnen der Bandbreite für vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) in der VMware-Knowledge Base bietet Orientierung im Hinblick auf Bandbreitenbegrenzungen.
3. Verwenden Sie das Größenberechnungstool von CloudSimple, um die Ressourcen zu schätzen, die an Ihrem Notfallwiederherstellungsstandort zum Schutz der lokalen Umgebung erforderlich sind.

### <a name="create-a-private-cloud-for-your-environment"></a>Erstellen einer privaten Cloud für Ihre Umgebung

Erstellen Sie über das CloudSimple-Portal eine private Cloud, indem Sie die Anweisungen und Empfehlungen in [Erstellen einer privaten Cloud](create-private-cloud.md) befolgen.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Einrichten privater Cloudnetzwerke für die SRM-Lösung

Greifen Sie auf das CloudSimple-Portal zu, um das private Cloudnetzwerk für die SRM-Lösung einzurichten.

Erstellen Sie ein VLAN für das SRM-Lösungsnetzwerk, und weisen Sie ihm einen Subnetz-CIDR-Wert zu. Anweisungen dazu finden Sie unter [Erstellen und Verwalten von VLANs/Subnetzen](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Einrichten einer Site-to-Site-VPN-Verbindung zwischen dem lokalen Netzwerk und der privaten Cloud und Öffnen der erforderlichen Ports

Richten Sie Site-to-Site-Konnektivität zwischen dem lokalen Netzwerk und der privaten Cloud ein. Anweisungen finden Sie unter [Konfigurieren einer VPN-Verbindung mit ihrer privaten CloudSimple-Cloud](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Einrichten von Infrastrukturdiensten in Ihrer privaten Cloud

Konfigurieren Sie Infrastrukturdienste in der privaten Cloud, um die Verwaltung von Workloads und Tools zu vereinfachen.

Sie können einen externen Identitätsanbieter wie unter [Verwenden von Azure AD als Identitätsanbieter für vCenter in einer privaten CloudSimple-Cloud](azure-ad.md) beschrieben hinzufügen, wenn Sie eine der folgenden Aktionen ausführen möchten:

* Identifizieren von Benutzern aus Ihrem lokalen Active Directory (AD) in Ihrer privaten Cloud
* Einrichten eines AD für alle Benutzer in Ihrer privaten Cloud
* Verwenden von Azure AD

Um das Nachschlagen von IP-Adressen, IP-Adressverwaltung und Namensauflösungsdienste für Ihre Workloads in der privaten Cloud bereitzustellen, richten Sie einen DHCP- und DNS-Server ein, wie unter [Einrichten von DNS- und DHCP-Anwendungen und -Workloads in Ihrer privaten CloudSimple-Cloud](dns-dhcp-setup.md) beschrieben.

Die Domäne „*.cloudsimple.io“ wird von Verwaltungs-VMs und Hosts in der privaten Cloud verwendet. Um Anforderungen an diese Domäne aufzulösen, konfigurieren Sie die DNS-Weiterleitung auf dem DNS-Server, wie in [Erstellen einer bedingten Weiterleitung](on-premises-dns-setup.md#create-a-conditional-forwarder) beschrieben.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installieren der vSphere Replication-Appliance in der lokalen Umgebung

Installieren Sie die vSphere Replication-Appliance (vRA) in der lokalen Umgebung entsprechend den Anweisungen in der VMware-Dokumentation. Die Installation umfasst folgende allgemeine Schritte:

1. Bereiten Sie die lokale Umgebung für die vRA-Installation vor.

2. Stellen Sie vRA mithilfe der OVF-Vorlage in der VR-ISO-Datei von vmware.com in der lokalen Umgebung bereit. Die relevanten Informationen für vRA 6.5 enthält [dieser VMware-Blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices).

3. Registrieren Sie die lokale vRA bei vCenter Single Sign-On am lokalen Standort. Ausführliche Anweisungen für vSphere Replication 6.5 finden Sie im VMware-Dokument [VMware vSphere Replication – Installation und Konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Installieren der vSphere Replication-Appliance in der privaten Cloud

Vergewissern Sie sich zunächst, dass Sie über Folgendes verfügen:

* Erreichbarkeit der IP-Adressen des Verwaltungssubnetzes in der privaten Cloud aus Subnetzen in der lokalen Umgebung.
* Erreichbarkeit der IP-Adressen des Subnetzes der SRM-Lösung der privaten Cloud aus Replikationssubnetzen in der lokalen vSphere-Umgebung.

Anweisungen finden Sie unter [Konfigurieren einer VPN-Verbindung mit ihrer privaten CloudSimple-Cloud](set-up-vpn.md). Die Schritte ähneln denen für die lokale Installation.

CloudSimple empfiehlt die Verwendung von FQDNs anstelle von IP-Adressen während der vRA-und SRM-Installation. Um den FQDN von vCenter und PSC in Ihrer privaten Cloud zu ermitteln, öffnen Sie das CloudSimple-Portal. Wechseln Sie zu **Ressourcen**, wählen Sie Ihre private Cloud aus, und klicken Sie auf die Registerkarte **vSphere Management Network**.

![Ermitteln des FQDN von vCenter/PSC in der privaten Cloud](media/srm-resources.png)

CloudSimple erfordert, dass Sie vRA und SRM nicht mithilfe des Standardbenutzers „cloudowner“ installieren, sondern einen neuen Benutzer erstellen. Dadurch werden eine lange Betriebszeit und hohe Verfügbarkeit für die private vCenter-Cloudumgebung sichergestellt. Der Standardbenutzer „cloudowner“ im vCenter der privaten Cloud verfügt jedoch nicht über ausreichende Berechtigungen zum Erstellen eines neuen Benutzers mit Administratorrechten.

Sie müssen vor der Installation von vRA und SRM die vCenter-Berechtigungen des Benutzers „cloudowner“ eskalieren und dann einen Benutzer mit Administratorrechten in der vCenter Single Sign-On-Domäne erstellen. Ausführliche Informationen zum Standardbenutzer und Berechtigungsmodell für die private Cloud finden Sie unter [Kennenlernen des Berechtigungsmodells für private Clouds](learn-private-cloud-permissions.md).

Die Installation umfasst folgende allgemeine Schritte:

1. [Eskalieren Sie Berechtigungen](escalate-private-cloud-privileges.md).
2. Erstellen Sie einen Benutzer in der privaten Cloud für die Installation von vSphere Replication und SRM. Dies wird weiter unten in [vCenter-Benutzeroberfläche: Erstellen eines Benutzers in der privaten Cloud für die Installation von vRA und SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation) erläutert.
3. Bereiten Sie die private Cloud für die vRA-Installation vor.
4. Stellen Sie vRA mithilfe der OVF-Vorlage in der VR-ISO-Datei von vmware.com in der privaten Cloud bereit. Relevante Informationen für vRA 6.5 enthält [dieser VMware-Blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices).
5. Konfigurieren Sie Firewallregeln für vRA. Dies wird weiter unten in [CloudSimple-Portal: Konfigurieren von Firewallregeln für vRA](#cloudsimple-portal-configure-firewall-rules-for-vra) erläutert.
6. Registrieren Sie vRA für die private Cloud bei vCenter Single Sign-On am Standort der privaten Cloud.
7. Konfigurieren Sie vSphere Replication-Verbindungen zwischen den beiden Appliances. Stellen Sie sicher, dass die erforderlichen Ports über die Firewalls hinweg geöffnet sind. Eine Liste der Ports, die für vSphere Replication 6.5 geöffnet sein müssen, finden Sie in [diesem Artikel der VMware-Knowledge Base](https://kb.vmware.com/s/article/2087769).

Ausführliche Installationsanweisungen für vSphere Replication 6.5 finden Sie im VMware-Dokument [VMware vSphere Replication – Installation und Konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter-Benutzeroberfläche: Erstellen eines Benutzers in der privaten Cloud für die Installation von vRA und SRM

Melden Sie sich mithilfe der Anmeldeinformationen des Benutzers „cloudowner“ bei vCenter an, nachdem Sie im CloudSimple-Portal die Berechtigungen eskaliert haben.

Erstellen Sie in vCenter den neuen Benutzer `srm-soln-admin`, und fügen Sie ihn der Administratorengruppe in vCenter hinzu.
Melden Sie sich bei vCenter als Benutzer „cloudowner“ ab, und melden Sie sich als Benutzer *srm-soln-admin* an.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple-Portal: Konfigurieren von Firewallregeln für vRA

Konfigurieren Sie Firewallregeln wie unter [Einrichten von Firewalltabellen und -regeln](firewall.md) beschrieben, um Ports zum Ermöglichen der Kommunikation zwischen folgenden Komponenten zu öffnen:

* vRA im SRM-Lösungsnetzwerk und vCenter- und ESXi-Hosts im Verwaltungsnetzwerk.
* vRA-Appliances an den beiden Standorten.

Eine Liste der Ports, die für vSphere Replication 6.5 geöffnet sein müssen, finden Sie in diesem [Artikel der VMware-Knowledge Base](https://kb.vmware.com/s/article/2087769).

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installieren des SRM-Servers in der lokalen Umgebung

Überprüfen Sie zunächst Folgendes:

* vSphere Replication Appliance ist in der lokalen Umgebung und der privaten Cloud installiert.
* Die vSphere Replication Appliances an beiden Standorten sind miteinander verbunden.
* Sie haben die VMware-Informationen zu den Voraussetzungen und bewährten Methoden gelesen. Für SRM 6.5 können Sie das VMware-Dokument [Voraussetzungen und Best Practices für die Installation von Site Recovery Manager Server](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html) zurate ziehen.

Führen Sie gemäß der VMware-Dokumentation die SRM-Serverinstallation im Bereitstellungsmodell „Zwei-Site-Topologie mit einer vCenter Server-Instanz pro Platform Services Controller“ aus, wie in diesem [vMware-Dokument](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) beschrieben. Die Installationsanweisungen für SRM 6.5 finden Sie im VMware-Dokument [Installieren von Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Installieren des SRM-Servers in der privaten Cloud

Überprüfen Sie zunächst Folgendes:

* vSphere Replication Appliance ist in der lokalen Umgebung und der privaten Cloud installiert.
* Die vSphere Replication Appliances an beiden Standorten sind miteinander verbunden.
* Sie haben die VMware-Informationen zu den Voraussetzungen und bewährten Methoden gelesen. Informationen für SRM 6.5 finden Sie unter [Voraussetzungen und Best Practices für die Installation von Site Recovery Manager Server](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Mit den folgenden Schritten wird die Installation von SRM in der privaten Cloud beschrieben.

1. [vCenter-Benutzeroberfläche: Installieren von SRM](#vcenter-ui-install-srm)
2. [CloudSimple-Portal: Konfigurieren von Firewallregeln für SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter-Benutzeroberfläche: Konfigurieren von SRM](#vcenter-ui-configure-srm)
4. [CloudSimple-Portal: Einschränken von Berechtigungen](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter-Benutzeroberfläche: Installieren von SRM

Melden Sie sich mit den Anmeldeinformationen von „srm-soln-admin“ bei vCenter an. Führen Sie dann gemäß der VMware-Dokumentation die SRM-Serverinstallation im Bereitstellungsmodell „Zwei-Site-Topologie mit einer vCenter Server-Instanz pro Platform Services Controller“ aus, wie in diesem [VMware-Dokument](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) beschrieben. Die Installationsanweisungen für SRM 6.5 finden Sie im VMware-Dokument [Installieren von Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple-Portal: Konfigurieren von Firewallregeln für SRM

Konfigurieren Sie Firewallregeln wie unter [Einrichten von Firewalltabellen und -regeln](firewall.md) beschrieben, um die Kommunikation zwischen folgenden Komponenten zuzulassen:

Dem SRM-Server und vCenter/PSC in der privaten Cloud.
Den SRM-Servern an beiden Standorten.

Eine Liste der Ports, die für vSphere Replication 6.5 geöffnet sein müssen, finden Sie in [diesem Artikel der VMware-Knowledge Base](https://kb.vmware.com/s/article/2087769).

#### <a name="vcenter-ui-configure-srm"></a>vCenter-Benutzeroberfläche: Konfigurieren von SRM

Nachdem SRM in der privaten Cloud installiert wurde, führen Sie die folgenden Aufgaben aus, wie in „Installieren von VMware Site Recovery Manager“ beschrieben. Die Anweisungen für SRM 6.5 finden Sie im VMware-Dokument [Installieren von Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Verbinden Sie die Site Recovery Manager Server-Instanzen am geschützten Standort und Wiederherstellungsstandort.
2. Stellen Sie eine Clientverbindung mit der Remoteinstanz von Site Recovery Manager Server her.
3. Installieren Sie den Site Recovery Manager-Lizenzschlüssel.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-Portal: Einschränken von Berechtigungen

Informationen zum Einschränken von Berechtigungen finden Sie unter [Einschränken von Berechtigungen](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Fortlaufende Verwaltung Ihrer SRM-Lösung

Sie haben die vollständige Kontrolle über die vSphere Replication- und SRM-Software in der privaten Cloud und müssen die erforderliche Verwaltung des Softwarelebenszyklus durchführen. Stellen Sie sicher, dass jede neue Version der Software mit vCenter und PSC in der privaten Cloud kompatibel ist, bevor Sie ein Update oder Upgrade von vSphere Replication oder SRM durchführen.

> [!NOTE]
> CloudSimple erkundet derzeit Möglichkeiten zum Bereitstellen eines verwalteten Notfallwiederherstellungsdiensts. 

## <a name="multiple-replication-configuration"></a>Konfiguration mehrerer Arten der Replikation

 [Mit SRM können gleichzeitig die arraybasierte und die vSphere-Replikationstechnologie verwendet werden](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication). Sie müssen jedoch auf unterschiedliche Gruppen von VMs angewendet werden (eine VM kann entweder durch die arraybasierte Replikation oder die vSphere-Replikation geschützt werden, jedoch nicht durch beide). Zudem kann der CloudSimple-Standort als Wiederherstellungsstandort für mehrere geschützte Standorte konfiguriert werden. Weitere Informationen zu Konfigurationen mit mehreren Standorten finden Sie unter [SRM Multi-Site Options](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) (SRM-Optionen für mehrere Standorte, in englischer Sprache).

## <a name="references"></a>References

* [VMware Site Recovery Manager-Dokumentation](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Operational limits for Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110) (Betriebsgrenzwerte für Site Recovery Manager 6.5, in englischer Sprache)
* [Berechnen der Bandbreite für vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF Choices When Deploying vSphere Replication 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/) (OFV-Option beim Bereitstellen von vSphere Replication 6.5, in englischer Sprache)
* [VMware vSphere Replication – Installation und Konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Voraussetzungen und Best Practices für die Installation von Site Recovery Manager Server](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager in einer Zwei-Site-Topologie mit einer vCenter Server-Instanz pro Platform Services Controller](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installations- und Konfigurationshandbuch für VMware Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware-Blog zu SRM mit arraybasierter Replikation und vSphere-Replikation](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware-Blog zu SRM-Optionen für mehrere Standorte](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Ports, die für vSphere Replication 5.8.x, 6.x und 8 geöffnet sein müssen](https://kb.vmware.com/s/article/2147112)

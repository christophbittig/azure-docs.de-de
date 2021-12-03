---
title: Verwalten einer privaten Cloud in Azure VMware Solution by CloudSimple
description: Beschreibt die Funktionen, mit denen Ressourcen und Aktivitäten einer privaten CloudSimple-Cloud verwaltet werden können.
author: suzizuber
ms.author: v-szuber
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d140b8cdf8bf88fac3c26199f66050f07e028d3d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336523"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Verwalten von privaten Cloudressourcen und -aktivitäten

Private Clouds werden über das CloudSimple-Portal verwaltet.  Im CloudSimple-Portal können Sie den Status, verfügbare Ressourcen, Aktivitäten in der privaten Cloud und weitere Einstellungen überprüfen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="access-the-cloudsimple-portal"></a>Zugriff auf das CloudSimple-Portal

Rufen Sie das [CloudSimple-Portal](access-cloudsimple-portal.md) auf.

## <a name="view-the-list-of-private-clouds"></a>Anzeigen der Liste privater Clouds

Auf der Seite **Resources** (Ressourcen) der Registerkarte **Private Clouds** sind alle privaten Clouds in Ihrem Abonnement aufgeführt. Die Angaben umfassen den Namen, die Anzahl der vSphere-Cluster, den Speicherort, den aktuellen Status der privaten Cloud und Ressourceninformationen.

![Seite „Private Cloud“](media/manage-private-cloud.png)

Wählen Sie eine private Cloud aus, um zusätzliche Informationen und Aktionen einzublenden.

## <a name="private-cloud-summary"></a>Private Cloud – Zusammenfassung

Sie erhalten einen umfassenden Überblick über die ausgewählte private Cloud.  Auf der Seite „Summary“ (Zusammenfassung) sind die in der privaten Cloud bereitgestellten DNS-Server aufgeführt.  Sie können die DNS-Weiterleitung von lokalen DNS-Servern an Ihre DNS-Server in der privaten Cloud einrichten.  Weitere Informationen zur DNS-Weiterleitung finden Sie im Thema zum [Konfigurieren der DNS-Namensauflösung für den lokalen Zugriff auf vCenter in einer privaten Cloud](./on-premises-dns-setup.md).

![Private Cloud – Zusammenfassung](media/private-cloud-summary.png)

### <a name="available-actions"></a>Verfügbare Aktionen

* [Launch vSphere client](./vcenter-access.md) (vSphere-Client starten): Ermöglicht den vCenter-Zugriff für diese private Cloud.
* [Purchase nodes](create-nodes.md) (Knoten kaufen): Fügt dieser privaten Cloud Knoten hinzu.
* [Expand](expand-private-cloud.md) (Erweitern): Fügt dieser privaten Cloud Knoten hinzu.
* **Refresh** (Aktualisieren): Aktualisiert die Informationen auf dieser Seite.
* **Löschen**: Sie können die private Cloud jederzeit löschen. **Stellen Sie vor dem Löschen sicher, dass Sie alle Systeme und Daten gesichert haben.** Beim Löschen einer privaten Cloud werden alle VMs, vCenter-Konfigurationseinstellungen und Daten gelöscht. Klicken Sie im Abschnitt „Summary“ (Zusammenfassung) der ausgewählten privaten Cloud auf **Delete** (Löschen). Danach werden alle Daten der privaten Cloud in einem sehr sicherheitskonformen Löschvorgang entfernt.
* [Change vSphere privileges](escalate-private-cloud-privileges.md) (vSphere-Berechtigungen ändern):  Erweitert Ihre Berechtigungen in dieser privaten Cloud.

## <a name="private-cloud-vlanssubnets"></a>VLANS/Subnetze der privaten Cloud

Sie können die Liste der für die ausgewählte private Cloud definierten VLANs/Subnetze anzeigen.  Die Liste umfasst die bei der Erstellung der privaten Cloud konfigurierten Verwaltungssubnetze/-VLANs.

![Private Cloud – VLANs/Subnetze](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Verfügbare Aktionen

* [Add VLANS/Subnets](./create-vlan-subnet.md) (VLANS/Subnetze hinzufügen): Fügt der privaten Cloud ein VLAN/Subnetz hinzu.

Wählen Sie für folgende Aktionen ein VLAN/Subnetz aus:
* [Attach firewall table](./firewall.md) (Firewalltabelle anfügen): Fügt eine Firewalltabelle an diese private Cloud an.
* **Bearbeiten**
* **Delete** (Löschen): nur benutzerdefinierte VLANs/Subnetze

## <a name="private-cloud-activity"></a>Private Cloud – Aktivitäten

Sie können folgende Informationen zur ausgewählten privaten Cloud anzeigen.  Die Aktivitätsinformationen sind in einer gefilterten Liste aller Aktivitäten in der ausgewählten privaten Cloud zusammengefasst.  Auf dieser Seite werden die letzten Aktivitäten (maximal 25) angezeigt.

* Recent alerts (Letzte Warnungen)
* Recent events (Letzte Ereignisse)
* Recent tasks (Letzte Aufgaben)
* Recent audit (Letzte Überprüfung)

![Private Cloud – Aktivitäten](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloud-Racks

Cloud-Racks sind die Bausteine Ihrer privaten Cloud. Jedes Rack umfasst eine Kapazitätseinheit. In CloudSimple werden Cloud-Racks beim Erstellen oder Erweitern einer privaten Cloud basierend auf den ausgewählten Optionen automatisch konfiguriert.  Sie können die vollständige Liste der Cloud-Racks anzeigen, einschließlich der privaten Cloud, der sie jeweils zugewiesen sind.

![Private Cloud – Cloud-Racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Die Liste der VMware-Verwaltungsressourcen und virtuellen Computer, die derzeit in der privaten Cloud konfiguriert sind. Die Informationen umfassen die Softwareversion, den vollqualifizierten Domänennamen (FQDN) und die IP-Adresse der Ressourcen.

![Private Cloud – vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Nächste Schritte

* [Nutzen von virtuellen VMware-Computern in Azure](quickstart-create-vmware-virtual-machine.md)
* Weitere Informationen zu [privaten Clouds](cloudsimple-private-cloud.md)

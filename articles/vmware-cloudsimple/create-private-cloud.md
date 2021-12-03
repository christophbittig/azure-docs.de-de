---
title: 'Azure VMware Solution by CloudSimple: Erstellen einer privaten CloudSimple-Cloud'
description: Informationen zum Erstellen einer privaten CloudSimple-Cloud zum Erweitern von VMware-Workloads auf die Cloud mit operativer Flexibilität und Kontinuität
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f8494b1f86f518508ab746b49829c559f51ad74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322624"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Erstellen einer privaten CloudSimple-Cloud

Eine private Cloud ist ein isolierter VMware-Stapel, der ESXi-Hosts, vCenter, vSAN und NSX unterstützt. Private Clouds werden über das CloudSimple-Portal verwaltet. Sie verfügen über einen eigenen vCenter-Server in einer eigenen Verwaltungsdomäne. Der Stapel wird auf dedizierten Knoten und isolierten Bare-Metal-Hardwareknoten ausgeführt.

Mithilfe einer privaten Cloud können Sie verschiedene häufige Anforderungen an die Netzwerkinfrastruktur angehen:

* **Wachstum:** Wenn Sie den Punkt erreichen, an dem Ihre Hardware für Ihre bereits bestehende Infrastruktur aktualisiert werden muss, ermöglicht eine private Cloud Erweiterungen ohne weitere Hardwareinvestitionen.

* **Schnelle Erweiterung:** Wenn temporärer oder ungeplanter Kapazitätsbedarf entsteht, können Sie mithilfe einer privaten Cloud ohne Verzögerung die zusätzlichen Kapazitäten bereitstellen.

* **Verbesserter Schutz:** Über eine private Cloud, die aus mindestens drei Knoten besteht, erhalten Sie automatische Redundanz und Schutz durch Hochverfügbarkeit.

* **Langfristige Infrastrukturanforderungen:** Wenn Ihre Rechenzentren ausgelastet sind oder Sie eine Neustrukturierung vornehmen möchten, um Ihre Kosten zu senken, bietet eine private Cloud Ihnen die Möglichkeit, einerseits Rechenzentren außer Betrieb zu nehmen und eine Migration zu einer cloudbasierten Lösung durchzuführen und andererseits die Kompatibilität mit den Vorgängen in Ihrem Unternehmen zu wahren.

Wenn Sie eine private Cloud erstellen, erhalten Sie einen einzelnen vSphere-Cluster und sämtliche VMs, die zur Verwaltung dienen und in diesem Cluster erstellt werden.

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie eine private Cloud erstellen können, müssen Knoten bereitgestellt werden. Weitere Informationen zum Bereitstellen von Knoten finden Sie unter [Bereitstellen von Knoten für Azure VMware Solution by CloudSimple](create-nodes.md).

Zuordnung eines CIDR-Bereichs für vSphere/vSAN-Subnetze für die private Cloud. Eine private Cloud wird als isolierte VMware-Stapelumgebung (mit ESXi-Hosts, vCenter, vSAN und NSX) erstellt, die von einem vCenter-Server verwaltet wird. Verwaltungskomponenten werden in dem Netzwerk bereitgestellt, das für vSphere/vSAN-Subnetze-CIDR ausgewählt ist. Der Netzwerk CIDR-Bereich wird während der Bereitstellung in unterschiedliche Subnetze aufgeteilt. Der Adressraum des vSphere/vSAN-Subnetzadressraums muss eindeutig sein. Er darf sich nicht mit einem Netzwerk überschneiden, das mit der CloudSimple-Umgebung kommuniziert. Zu den Netzwerken, die mit CloudSimple kommunizieren, gehören unter anderem lokale Netzwerke und virtuelle Azure-Netzwerke. Weitere Informationen zu vSphere/vSAN-Subnetzen finden Sie unter „Übersicht über VLANs und Subnetze“.

* Minimales Präfix für vSphere/vSAN-Subnetze-CIDR-Bereich: /24
* Maximales Präfix für vSphere/vSAN-Subnetze-CIDR-Bereich: /21


## <a name="access-the-cloudsimple-portal"></a>Zugriff auf das CloudSimple-Portal

Rufen Sie das [CloudSimple-Portal](access-cloudsimple-portal.md) auf.

## <a name="create-a-new-private-cloud"></a>Erstellen einer neuen privaten Cloud

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple Services**.
3. Wählen Sie den CloudSimple-Dienst aus, über den Sie Ihre private Cloud erstellen möchten.
4. Klicken Sie in der **Übersicht** auf **Create Private Cloud** (Private Cloud erstellen), um eine neue Browserregisterkarte für das CloudSimple-Portal zu öffnen. Melden Sie sich nach Aufforderung mit Ihren Azure-Anmeldeinformationen an.

    ![Erstellen einer privaten Cloud aus Azure](media/create-private-cloud-from-azure.png)

5. Geben Sie im CloudSimple-Portal einen Namen für Ihre private Cloud an.
6. Wählen Sie den **Standort** für Ihre private Cloud aus.
7. Wählen Sie den **Knotentyp** aus, der mit dem in Azure bereitgestellten Knotentyp übereinstimmt.
8. Geben Sie die **Knotenanzahl** an.  Für das Erstellen einer privaten Cloud sind mindestens drei Knoten erforderlich.

    ![Erstellen einer privaten Cloud: grundlegende Informationen](media/create-private-cloud-basic-info.png)

9. Klicken Sie auf **Weiter: Erweiterte Optionen**.
10. Geben Sie den CIDR-Bereich für vSphere/vSAN-Subnetze ein. Stellen Sie sicher, dass sich der CIDR-Bereich nicht mit einem Ihrer lokalen Subnetze, mit anderen Azure-Subnetzen (virtuelle Netzwerke) oder dem Gatewaysubnetz überlappt.

    **Mögliche CIDR-Bereiche:** /24, /23, /22 oder /21. Der CIDR-Bereich /24 unterstützt bis zu neun Knoten, der Bereich /23 bis zu 41 Knoten und die Bereiche /22 und /21 bis zu 64 Knoten (die maximale Anzahl an Knoten in einer privaten Cloud).

    > [!IMPORTANT]
    > IP-Adressen im vSphere/vSAN CIDR-Bereich sind zur Verwendung durch die private Cloudinfrastruktur reserviert.  Verwenden Sie keine IP-Adresse dieses Bereichs auf einem virtuellen Computer.

11. Klicken Sie auf **Weiter: Überprüfen und erstellen**.
12. Überprüfen Sie die Einstellungen. Wenn Sie irgendeine der Einstellungen ändern müssen, klicken Sie auf **Zurück**.
13. Klicken Sie auf **Erstellen**.

Der Prozess der Bereitstellung der privaten Cloud wird gestartet. Es kann bis zu zwei Stunden dauern, bis die private Cloud bereitgestellt wird.

Eine Anleitung zur Erweiterung einer vorhandenen privaten Cloud finden Sie unter [Erweitern einer privaten Cloud](expand-private-cloud.md).

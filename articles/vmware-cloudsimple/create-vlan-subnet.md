---
title: Erstellen von VLANs/Subnetzen – Azure VMware Solution by CloudSimple
description: 'Azure VMware Solution by CloudSimple: Beschreibt das Erstellen und Verwalten von VLANs/Subnetzen für Ihre privaten Clouds und das anschließende Anwenden von Firewallregeln.'
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0d74211122cdc1d65395281f9cf4f749ca825a12
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322586"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Erstellen und Verwalten von VLANs/Subnetzen für private Clouds

Öffnen Sie die Registerkarte „VLANs/Subnets“ (VLANs/Subnetze) auf der Seite „Network“ (Netzwerk), um VLANs/Subnetze für Ihre privaten Clouds zu erstellen und zu verwalten. Nachdem Sie ein VLAN/Subnetz erstellt haben, können Sie Firewallregeln anwenden.

## <a name="create-a-vlansubnet"></a>Erstellen eines VLAN/Subnetzes

1. [Greifen Sie auf das CloudSimple Portal zu](access-cloudsimple-portal.md), und wählen Sie **Network** (Netzwerk) im seitlichen Menü aus.
2. Wählen Sie **VLANs/subnets** (VLANs/Subnetze) aus.
3. Klicken Sie auf **Create VLAN/Subnet** (VLAN/Subnetz erstellen).

    ![Seite „VLAN/Subnetz“](media/vlan-subnet-page.png)

4. Wählen Sie die private Cloud für das neue VLAN/Subnetz aus.
5. Geben Sie eine VLAN-ID ein.
6. Geben Sie den Namen des Subnetzes ein.
7. Um das Routing im VLAN (Subnetz) zu aktivieren, geben Sie den CIDR-Bereich des Subnetzes an. Stellen Sie sicher, dass sich der CIDR-Bereich nicht mit einem Ihrer lokalen Subnetze, Azure-Subnetzen oder dem Gatewaysubnetz überschneidet.
8. Klicken Sie auf **Submit**(Senden).

    ![Erstellen des VLAN/Subnetzes](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Es gibt ein Kontingent von 30 VLANs für jede private Cloud. Diese Grenzwerte können durch [Kontaktaufnahme mit dem Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) erhöht werden.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Verwenden von VLAN-Informationen zum Einrichten einer verteilten Portgruppe in vSphere

Befolgen Sie zum Erstellen einer verteilten Portgruppe in vSphere die Anweisungen im VMware-Thema „Hinzufügen einer verteilten Portgruppe“ im <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere-Netzwerkleitfaden</a>. Geben Sie beim Einrichten der verteilten Portgruppe die VLAN-Informationen aus der CloudSimple-Konfiguration an.

![Verteilte Portgruppe](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Auswählen einer Firewalltabelle

Firewalltabellen und zugehörige Regeln werden auf der Seite **Network (Netzwerk) > Firewall Tables (Firewalltabellen)** definiert. Um die Firewalltabelle auszuwählen, die auf das VLAN/Subnetz für eine private Cloud angewendet werden soll, wählen Sie das VLANs/Subnetz aus, und klicken Sie dann auf der Seite **VLANs/Subnets** (VLAN/Subnetz) auf **Firewall table attachment** (Firewalltabellenanlage). Anleitungen zum Einrichten von Firewalltabellen und Definieren von Regeln finden Sie unter [Firewalltabellen](firewall.md).

![Firewalltabellenverbindung](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Ein Subnetz kann einer Firewalltabelle zugeordnet werden. Eine Firewalltabelle kann mehreren Subnetzen zugeordnet werden.

## <a name="edit-a-vlansubnet"></a>Bearbeiten eines VLAN/Subnetzes

Um die Einstellungen für ein VLAN/Subnetz zu bearbeiten, wählen Sie es auf der Seite **VLANs/Subnets** (VLANs/Subnetze) aus und klicken dann auf das Symbol **Bearbeiten**. Nehmen Sie Änderungen vor, und klicken Sie auf **Subnet** (Subnetz).

## <a name="delete-a-vlansubnet"></a>Löschen eines VLAN/Subnetzes

Um ein VLAN/Subnetz zu löschen, wählen Sie es auf der Seite **VLANs/Subnets** (VLANs/Subnetze) aus und klicken dann auf das Symbol **Löschen**. Klicken Sie auf **Löschen**, um den Vorgang zu bestätigen.

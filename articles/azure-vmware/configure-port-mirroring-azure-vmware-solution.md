---
title: Konfigurieren der Portspiegelung für Azure VMware Solution
description: Erfahren Sie, wie Sie die Portspiegelung zum Überwachen des Netzwerkdatenverkehrs konfigurieren können, was die Weiterleitung einer Kopie jedes Pakets von einem Netzwerkswitchport an einen anderen einschließt.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356597"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>Konfigurieren der Portspiegelung im Azure-Portal

Nach der Bereitstellung von Azure VMware Solution können Sie die Portspiegelung im Azure-Portal konfigurieren. Mit der Portspiegelung wird ein Protokollanalysetool an dem Port platziert, der die gespiegelten Daten empfängt. Es analysiert den Datenverkehr, der von einer Quelle, einem virtuellen Computer (VM) oder einer Gruppe von VMs an ein definiertes Ziel gesendet wird. 

In dieser Schrittanleitung können Sie die Portspiegelung zum Überwachen des Netzwerkdatenverkehrs konfigurieren, was die Weiterleitung einer Kopie jedes Pakets von einem Netzwerkswitchport an einen anderen einschließt. 

## <a name="prerequisites"></a>Voraussetzungen

Eine private Azure VMware Solution-Cloud mit Zugriff auf die vCenter- und NSX-T Manager-Verwaltungsschnittstellen Weitere Informationen finden Sie im Tutorial zum [Konfigurieren des Netzwerks](tutorial-configure-networking.md).

## <a name="create-the-vms-or-vm-groups"></a>Erstellen der VMs oder VM-Gruppen

Sie erstellen die Quell- und Ziel-VMs oder VM-Gruppen. Die Quellgruppe enthält eine einzelne VM oder mehrere VMs, auf denen der Datenverkehr gespiegelt wird.

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Workloadnetzwerk** die Option **Portspiegelung** > **VM-Gruppen** > **Hinzufügen** aus.

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="Screenshot, der das Erstellen einer VM-Gruppe für die Portspiegelung zeigt.":::

1. Geben Sie einen Namen für die neue VM-Gruppe an, wählen Sie die VMs aus der Liste aus, und klicken Sie dann auf **OK**.

1. Wiederholen Sie diese Schritte, um die Ziel-VM-Gruppe zu erstellen.

   >[!NOTE]
   >Stellen Sie vor dem Erstellen eines Portspiegelungsprofils sicher, dass Sie sowohl die Quell- als auch die Ziel-VM-Gruppen erstellt haben.

## <a name="create-a-port-mirroring-profile"></a>Erstellen eines Portspiegelungsprofils

Sie erstellen ein Portspiegelungsprofil, das die Richtung des Datenverkehrs für die Quell- und Ziel-VM-Gruppen definiert.

1. Wählen Sie **Portspiegelung** > **Portspiegelung** > **Hinzufügen** aus, und geben Sie Folgendes an:

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="Screenshot der Informationen, die für das Portspiegelungsprofil erforderlich sind.":::

   - **Name der Portspiegelung**: beschreibender Name für das Profil.

   - **Richtung**: Wählen Sie zwischen „Eingehend“, „Ausgehend“ und „Bidirektional“.

   - **Quelle**: Wählen Sie die Quell-VM-Gruppe aus.

   - **Ziel**: Wählen Sie die Ziel-VM-Gruppe aus.

   - **Beschreibung**: Geben Sie eine Beschreibung für die Portspiegelung ein.

1. Wählen Sie **OK** aus, um das Profil abzuschließen. 

   Das Profil und die VM-Gruppen werden in der Azure VMware Solution-Konsole angezeigt.

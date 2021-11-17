---
title: 'Azure VMware Solution by CloudSimple: Erstellen eines CloudSimple-Diensts'
description: Hier erfahren Sie, wie der CloudSimple-Dienst im Azure-Portal erstellt wird. Überprüfen Sie die erforderliche Konfiguration, bevor Sie beginnen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f7196142d70aee5a4e96c59c8cd4dc3d05f902d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322662"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Erstellen des Azure VMware Solution by CloudSimple-Diensts

Als ersten Schritt in Azure VMware Solution by CloudSimple erstellen Sie den Dienst „Azure VMware Solution by CloudSimple“ im Azure-Portal.

## <a name="before-you-begin"></a>Voraussetzungen

Ordnen Sie einen /28-CIDR-Block für das Gatewaysubnetz zu. Ein Gatewaysubnetz ist für jeden CloudSimple-Dienst erforderlich und für die Region spezifisch, in der es erstellt wird. Das Gatewaysubnetz wird für Edge-Netzwerkdienste verwendet und erfordert einen CIDR-Block vom Typ „/28“. Der Adressraum des Gatewaysubnetzes muss eindeutig sein. Er darf sich nicht mit einem Netzwerk überschneiden, das mit der CloudSimple-Umgebung kommuniziert. Zu den Netzwerken, die mit CloudSimple kommunizieren, gehören unter anderem lokale Netzwerke und virtuelle Azure-Netzwerke.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-the-service"></a>Erstellen des Diensts

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple Services**.
    ![Suchen nach dem CloudSimple-Dienst](media/create-cloudsimple-service-search.png)
3. Wählen Sie **CloudSimple Service** aus.
4. Klicken Sie auf **Hinzufügen**, um einen neuen Dienst zu erstellen.
    ![Hinzufügen des CloudSimple-Diensts](media/create-cloudsimple-service-add.png)
5. Wählen Sie das Abonnement aus, in dem Sie den CloudSimple-Dienst erstellen möchten.
6. Wählen Sie die Ressourcengruppe für den Dienst aus. Um eine neue Ressourcengruppe hinzuzufügen, klicken Sie auf **Neue erstellen**.
7. Geben Sie einen Namen ein, um den Dienst zu benennen.
8. Geben Sie das CIDR für das Dienstgateway ein. Geben Sie ein /28-Subnetz an, das mit keinem Ihrer lokalen Subnetze, Azure-Subnetze oder geplanten CloudSimple-Subnetze überlappt. Sie können das CIDR nicht mehr ändern, nachdem der Dienst erstellt wurde.

    ![Erstellen des CloudSimple-Diensts](media/create-cloudsimple-service.png)
9. Klicken Sie auf **OK**.

Der Dienst wird erstellt und zur Liste der Dienste hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Knoten bereitstellen](create-nodes.md).
* Erfahren Sie mehr über das [Erstellen einer privaten Cloud](create-private-cloud.md).
* Erfahren Sie mehr über das [Konfigurieren einer privaten Cloudumgebung](quickstart-create-private-cloud.md).

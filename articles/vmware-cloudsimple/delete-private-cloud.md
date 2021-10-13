---
title: Löschen einer privaten Cloud in Azure VMware Solution by CloudSimple
description: Hier erfahren Sie, wie Sie eine private CloudSimple-Cloud löschen. Wenn Sie eine private Cloud löschen, werden alle Cluster gelöscht.
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b8afadea5c1f0236166f9d2cd5b7c7fa7d6c3d6e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620391"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Löschen einer privaten CloudSimple-Cloud

CloudSimple bietet die Flexibilität, eine private Cloud zu löschen.  Eine private Cloud besteht aus einem oder mehreren vSphere-Clustern. Jeder Cluster kann 3 bis 16 Knoten enthalten. Wenn Sie eine private Cloud löschen, werden alle Cluster gelöscht.

## <a name="before-you-begin"></a>Voraussetzungen

Beim Löschen einer privaten Cloud wird die gesamte private Cloud gelöscht.  Alle Komponenten der privaten Cloud werden gelöscht.  Wenn Sie einige Daten beibehalten möchten, müssen Sie sicherstellen, dass Sie diese Daten im lokalen Speicher oder in Azure Storage gesichert haben.

Zu den Komponenten einer privaten Cloud gehören:

* CloudSimple-Knoten
* Virtuelle Computer
* VLANs/Subnetze
* Alle Benutzerdaten, die in der privaten Cloud gespeichert sind
* Alle Firewallregelanlagen für ein VLAN/Subnetz

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="delete-a-private-cloud"></a>Löschen einer privaten Cloud

1. [Rufen Sie das CloudSimple-Portal auf](access-cloudsimple-portal.md).

2. Öffnen Sie die Seite **Ressourcen**.

3. Klicken Sie auf die zu löschende private Cloud.

4. Klicken Sie auf der Zusammenfassungsseite auf **Löschen**.

    ![Löschen einer privaten Cloud](media/delete-private-cloud.png)

5. Geben Sie auf der Bestätigungsseite den Namen der privaten Cloud ein, und klicken Sie auf **Löschen**. 

    ![Löschen einer privaten Cloud – Bestätigung](media/delete-private-cloud-confirm.png)

Die private Cloud wird zum Löschen markiert.  Der Löschvorgang beginnt nach drei Stunden und löscht die private Cloud.

> [!CAUTION]
> Knoten müssen nach dem Löschen der privaten Cloud gelöscht werden.  Die Abrechnung der Knoten wird fortgesetzt, bis die Knoten aus Ihrem Abonnement gelöscht wurden.

## <a name="next-steps"></a>Nächste Schritte

* [Löschen von Knoten](delete-nodes.md)

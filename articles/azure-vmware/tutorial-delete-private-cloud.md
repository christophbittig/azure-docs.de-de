---
title: 'Tutorial: Löschen einer privaten Azure VMware Solution-Cloud'
description: Es wird beschrieben, wie Sie eine private Azure VMware Solution-Cloud löschen, die Sie nicht mehr benötigen.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ed1a4fdc2487bf470c96afa5ff8b3cde67336a40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730215"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial: Löschen einer privaten Azure VMware Solution-Cloud

Wenn Sie über eine private Azure VMware Solution-Cloud verfügen, die Sie nicht mehr benötigen, können Sie sie löschen. Die private Cloud umfasst Folgendes:

* Eine isolierte Netzwerk-Domäne

* Mindestens ein bereitgestellter vSphere-Cluster auf dedizierten Serverhosts

* Mehrere virtuelle Computer (VMs)

Wenn Sie eine private Cloud löschen, werden alle VMs, ihre Daten, Cluster und der bereitgestellte Netzwerkadressraum gelöscht. Die dedizierten Azure VMware Solution-Hosts werden auf sichere Weise bereinigt und an den freien Pool zurückgegeben.   

> [!CAUTION]
> Das Löschen der privaten Cloud beendet alle aktiven Workloads und Komponenten und kann nicht rückgängig gemacht werden. Nachdem Sie die private Cloud gelöscht haben, können Sie die Daten nicht mehr wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie die VMs und die darauf enthaltenen Daten später noch benötigen, sollten Sie die Daten sichern, bevor Sie die private Cloud löschen.  Es gibt leider keine Möglichkeit, die VMs und die zugehörigen Daten wiederherzustellen.


## <a name="delete-the-private-cloud"></a>Löschen der privaten Cloud

1. Greifen Sie im [Azure-Portal](https://portal.azure.com) auf die Azure VMware Solution-Konsole zu.

2. Wählen Sie die private Cloud aus, die Sie löschen möchten.
 
3. Geben Sie den Namen der privaten Cloud ein, und wählen Sie **Ja** aus. 

>[!NOTE]
>Der Löschvorgang dauert einige Stunden.  

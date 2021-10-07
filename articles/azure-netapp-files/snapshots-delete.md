---
title: Löschen von Momentaufnahmen mithilfe von Azure NetApp Files | Microsoft-Dokumentation
description: Hier wird das Löschen von Momentaufnahmen mithilfe von Azure NetApp Files beschrieben.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700208"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>Löschen von Momentaufnahmen mithilfe von Azure NetApp Files 

Sie können Momentaufnahmen löschen, die Sie nicht mehr beibehalten müssen. 

> [!IMPORTANT]
> Das Löschen von Momentaufnahmen kann nicht rückgängig gemacht werden. Eine gelöschte Momentaufnahme kann nicht wiederhergestellt werden. 

## <a name="steps"></a>Schritte

1. Rufen Sie das Menü **Momentaufnahmen** eines Volumes auf. Klicken Sie mit der rechten Maustaste auf die zu löschende Momentaufnahme. Klicken Sie auf **Löschen**.

    ![Screenshot: Kontextmenü einer Momentaufnahme](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Bestätigen Sie im Fenster „Momentaufnahme löschen“, dass Sie die Momentaufnahme löschen möchten, indem Sie auf **Ja** klicken. 

    ![Screenshot: Bestätigung zum Löschen der Momentaufnahme](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Momentaufnahmen](snapshots-introduction.md)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
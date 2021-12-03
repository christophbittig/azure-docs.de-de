---
title: Erfassen eines Images eines virtuellen Computers über das Portal
description: Erstellen Sie über das Azure-Portal ein Image eines virtuellen Computers.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: bfab2d65de8b15ac72fe3c3b536b76ef14f046b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437294"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>Erstellen eines Images eines virtuellen Computers im Portal

Ein Image kann über einen virtuellen Computer erstellt und dann zum Erstellen mehrerer virtueller Computer verwendet werden.

Für in Azure Compute Gallery (früher als Shared Image Gallery bezeichnet) gespeicherte Images können Sie VMs verwenden, auf denen bereits Konten erstellt wurden (spezialisiert). Alternativ können Sie die VM generalisieren, bevor Sie das Image erstellen, um Computerkonten und andere computerspezifische Informationen zu entfernen. Informationen zum Generalisieren eines virtuellen Computers finden Sie unter [Entfernen VM-spezifischer Informationen durch Generalisieren einer VM vor Erstellen eines Images](generalize.md). Weitere Informationen finden Sie unter [Generalisierte und spezialisierte Images](shared-image-galleries.md#generalized-and-specialized-images).


## <a name="capture-a-vm-in-the-portal"></a>Erfassen eines virtuellen Computers im Portal 

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), suchen Sie nach **Virtuelle Computer**, und wählen Sie die Option aus.

2. Wählen Sie Ihren virtuellen Computer in der Liste aus.

3. Wählen Sie auf der Seite **Virtueller Computer** des virtuellen Computers im oberen Menü die Option **Erfassen** aus.

   Die Seite **Image erstellen** wird angezeigt.

5. Wählen Sie unter **Ressourcengruppe** entweder **Neu erstellen** aus, und geben Sie einen Namen ein, oder wählen Sie in der Dropdownliste eine Ressourcengruppe aus. Wenn Sie einen vorhandenen Katalog verwenden möchten, wählen Sie die Ressourcengruppe für den gewünschten Katalog aus.

1. Wählen Sie zum Erstellen des Images in einem Katalog die Option **Ja, als Imageversion für einen Katalog freigeben** aus.
    
   Wenn Sie nur ein verwaltetes Image erstellen möchten, wählen Sie **Nein, nur ein verwaltetes Image erfassen** aus. Der virtuelle Computer muss generalisiert worden sein, damit ein verwaltetes Image erstellt werden kann. Die einzige weitere erforderliche Information ist ein Name für das Image.

6. Wenn Sie die Quell-VM nach der Erstellung des Image löschen möchten, wählen Sie **Diesen virtuellen Computer nach dem Erstellen des Images automatisch löschen** aus. Diese Vorgehensweise wird nicht empfohlen.

1. Wählen Sie unter **Gallery details** (Katalogdetails) den Katalog aus, oder erstellen Sie einen neuen Katalog, indem Sie **Neu erstellen** auswählen.

1. Wählen Sie unter **Betriebssystemstatus** die Option „Generalized“ (Generalisiert) oder „Specialized“ (Spezialisiert) aus. Weitere Informationen finden Sie unter [Generalisierte und spezialisierte Images](shared-image-galleries.md#generalized-and-specialized-images).
 
1. Wählen Sie eine Imagedefinition oder **Neu erstellen** aus, und geben Sie einen Namen und die entsprechenden Informationen für eine neue [Imagedefinition](shared-image-galleries.md#image-definitions) ein.

1. Geben Sie eine [Imageversionsnummer](shared-image-galleries.md#image-versions) ein. Wenn dies die erste Version dieses Images ist, geben Sie *1.0.0* ein.

1. Soll diese Version eingeschlossen werden, wenn Sie für die Imageversion *Neueste* angeben, lassen Sie **Aus aktueller Version ausschließen** deaktiviert.

1. Wählen Sie ein Datum für **Ende der Lebensdauer** aus. Mithilfe dieses Datums kann nachverfolgt werden, wann ältere Images zurückgezogen werden müssen.

1. Wählen Sie unter [Replikation](shared-image-galleries.md#replication) eine Anzahl von Standardreplikaten und dann zusätzliche Regionen aus, in denen Ihr Image repliziert werden soll.

8. Wählen Sie abschließend **Überprüfen + Erstellen** aus.

1. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus, um das Image zu erstellen.



## <a name="next-steps"></a>Nächste Schritte

- [Azure Compute Gallerys: Übersicht](shared-image-galleries.md) 

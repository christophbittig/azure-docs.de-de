---
title: 'Azure Lab Services: Empfohlene Ansätze zum Erstellen benutzerdefinierter Images für Labs'
description: Beschreibt Ansätze zum Erstellen benutzerdefinierter Images für Labs.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: aa318ad47187fd93d331c7a6f33d4699dfe42af6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339878"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>Empfohlene Vorgehensweisen zum Erstellen benutzerdefinierter Images
In diesem Artikel werden die folgenden empfohlenen Ansätze zum Erstellen eines benutzerdefinierten Images beschrieben:

-   Erstellen und Speichern eines benutzerdefinierten Images aus einem [virtuellen Vorlagencomputer (VM) eines Labs](how-to-create-manage-template.md).
-   Importieren eines benutzerdefinierten Images außerhalb des Kontexts eines Labs mit:
    - Einer [Azure-VM](https://azure.microsoft.com/services/virtual-machines/).
    - Einer VHD in Ihrer physischen Laborumgebung.

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>Speichern eines benutzerdefinierten Images von der Vorlagen-VM eines Labs 

Die Verwendung der Vorlagen-VM eines Labs zum Erstellen und Speichern eines benutzerdefinierten Images ist die einfachste Möglichkeit, ein Image zu erstellen, da sie über das Lab Services-Portal unterstützt wird.  Daher können sowohl IT-Abteilungen als auch Lehrkräfte benutzerdefinierte Images mithilfe der Vorlagen-VM eines Labs erstellen.

Beispielsweise können Sie mit einem der Marketplace-Images beginnen und dann zusätzliche Softwareanwendungen, Tools usw. installieren, die für eine Klasse erforderlich sind.  Nachdem Sie das Image eingerichtet haben, können Sie es in der [verbundenen Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md) speichern, damit Sie und andere Lehrkräfte das Image zum Erstellen neuer Labs verwenden können.

Bei diesem Ansatz sind einige wichtige Punkte zu beachten:
-   Azure Lab Services speichert automatisch ein *spezialisiertes* Image gespeichert, wenn Sie das Image von der Vorlagen-VM exportieren.  In den meisten Fällen eignen sich spezialisierte Images gut zum Erstellen neuer Labs, weil das Image computerspezifische Informationen und Benutzerprofile beibehält.  Die Verwendung eines spezialisierten Images trägt zur Sicherstellung bei, dass die installierte Software bei der Verwendung des Images zum Erstellen neuer Labs auf die gleiche Weise ausgeführt wird.  Wenn Sie ein *generalisiertes* Image erstellen müssen, müssen Sie einen der anderen empfohlenen Ansätze in diesem Artikel verwenden, um ein benutzerdefiniertes Image zu erstellen.

    Sie können Labs basierend auf generalisierten und spezialisierten Images in Azure Lab Services erstellen.  Weitere Informationen zu den Unterschieden finden Sie im Artikel [Generalisierte und spezialisierte Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

-   Für erweiterte Szenarien bei der Einrichtung Ihres Images kann es hilfreich sein, stattdessen ein Image außerhalb von Labs zu erstellen, indem Sie entweder eine Azure-VM oder eine VHD aus Ihrer physischen Laborumgebung verwenden.  Weitere Informationen finden Sie in den nächsten Abschnitten.

### <a name="how-to-save-a-custom-image-from-a-labs-template-vm"></a>Speichern eines benutzerdefinierten Images von der Vorlagen-VM eines Labs

Sie können die Vorlagen-VM eines Labs verwenden, um benutzerdefinierte Windows- oder Linux-Images zu erstellen.  Weitere Informationen finden Sie im Artikel zum [Speichern des Images in einer Shared Image Gallery](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Importieren eines benutzerdefinierten Images von einer Azure-VM

Ein anderer Ansatz ist die Verwendung einer Azure-VM zum Einrichten eines benutzerdefinierten Images.  Nachdem Sie das Image eingerichtet haben, können Sie es in einer Shared Image Gallery speichern, damit Sie und Ihre Kollegen das Image zum Erstellen neuer Labs verwenden können.

Die Verwendung einer Azure-VM bietet Ihnen mehr Flexibilität:
-   Sie können [generalisierte oder spezialisierte Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) erstellen.  Wenn Sie die Vorlagen-VM eines Labs verwenden, um ein [Image zu exportieren](how-to-use-shared-image-gallery.md), ist das Image immer spezialisiert.
-   Sie haben Zugriff auf erweiterte Features einer Azure-VM, die beim Einrichten eines Images hilfreich sein können.  Beispielsweise können Sie [Erweiterungen](../virtual-machines/extensions/overview.md) für die Konfiguration und Automatisierung nach der Bereitstellung verwenden.  Außerdem können Sie auf die [Startdiagnose](../virtual-machines/boot-diagnostics.md) und die [serielle Konsole](/troubleshoot/azure/virtual-machines/serial-console-overview) der VM zugreifen.

Das Einrichten eines Images mithilfe einer Azure-VM ist jedoch komplexer.  Daher sind IT-Abteilungen in der Regel für die Erstellung benutzerdefinierter Images auf Azure-VMs verantwortlich.

### <a name="how-to-bring-a-custom-image-from-an-azure-vm"></a>Importieren eines benutzerdefinierten Images von einer Azure-VM

Im Folgenden finden Sie die wichtigsten Schritte zum Importieren eines benutzerdefinierten Images von einer Azure-VM:

1.  Erstellen Sie eine [Azure-VM](https://azure.microsoft.com/services/virtual-machines/) mithilfe eines Marketplace-Images für Windows oder Linux.  
1.  Stellen Sie eine Verbindung mit der Azure-VM her, und installieren Sie zusätzliche Software.  Sie können auch andere Anpassungen vornehmen, die für Ihr Lab erforderlich sind.  
1.  Nachdem Sie die Einrichtung des Images abgeschlossen haben, [speichern Sie das Image der VM in einer Shared Image Gallery](../virtual-machines/image-version-vm-powershell.md).  Dabei müssen Sie auch die Definition und Version des Images erstellen.
1.  Nachdem das benutzerdefinierte Image im Katalog gespeichert wurde, kann Ihr Image zum Erstellen neuer Labs verwendet werden.   

Die Schritte variieren je nachdem, ob Sie ein benutzerdefiniertes Windows- Linux-Image erstellen.  Die ausführlichen Schritte finden Sie in den folgenden Artikeln:

-   [Importieren eines benutzerdefinierten Windows-Images von einer Azure-VM](how-to-bring-custom-windows-image-azure-vm.md)
-   [Importieren eines benutzerdefinierten Linux-Images von einer Azure-VM](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>Importieren eines benutzerdefinierten Images von einer VHD in Ihrer physischen Laborumgebung

Der dritte mögliche Ansatz besteht im Importieren eines benutzerdefinierten Images von einer VHD in Ihrer physischen Laborumgebung in eine Shared Image Gallery.  Sobald sich das Image in einer Shared Image Gallery befindet, können Sie und andere Lehrkräfte das Image verwenden, um neue Labs zu erstellen.

Es gibt einige Gründe, warum Sie diesen Ansatz ggf. verwenden möchten:

-   Sie können [generalisierte oder spezialisierte](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Images erstellen, die Sie in Ihren Labs verwenden können.  Wenn Sie die [Vorlagen-VM eines Labs verwenden, um ein Image zu exportieren](how-to-use-shared-image-gallery.md), ist das Image immer spezialisiert.
-   Sie können auf Ressourcen zugreifen, die in Ihrer lokalen Umgebung vorhanden sind.  Beispielsweise können Sie große Installationsdateien in Ihrer vorhandenen Umgebung verwenden, die zu zeitaufwändig sind, um sie auf eine Vorlagen-VM eines Labs zu kopieren.
-   Sie können Images hochladen, die mit anderen Tools wie [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction) erstellt wurden, damit Sie ein Image nicht manuell mithilfe der Vorlagen-VM eines Labs einrichten müssen.

Das Importieren eines benutzerdefinierten Images von einer VHD ist der anspruchsvollste Ansatz, da Sie sicherstellen müssen, dass das Image richtig eingerichtet ist, damit es in Azure funktioniert.  Daher sind IT-Abteilungen in der Regel für die Erstellung benutzerdefinierter Images aus VHDs verantwortlich.

### <a name="how-to-bring-a-custom-image-from-a-vhd"></a>Importieren eines benutzerdefinierten Images aus einer VHD

Im Folgenden finden Sie die wichtigsten Schritte zum Importieren eines benutzerdefinierten Images aus einer VHD:

1.  Verwenden Sie [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) auf Ihrem lokalen Computer, um eine Windows- oder Linux-VHD zu erstellen.
1.  Stellen Sie eine Verbindung mit der Hyper-V-VM her, und installieren Sie zusätzliche Software.  Sie können auch andere Anpassungen vornehmen, die für Ihr Lab erforderlich sind.  
1.  Nachdem Sie das Image eingerichtet haben, laden Sie die VHD hoch, um einen [verwalteten Datenträger](../virtual-machines/managed-disks-overview.md) in Azure zu erstellen.
1.  Erstellen Sie aus dem verwalteten Datenträger die [Definition](../virtual-machines/shared-image-galleries.md#image-definitions) und Version des Images in einer Shared Image Gallery.
1.  Nachdem das benutzerdefinierte Image im Katalog gespeichert wurde, kann das Image zum Erstellen neuer Labs verwendet werden.   

Die Schritte variieren je nachdem, ob Sie ein benutzerdefiniertes Windows- Linux-Image erstellen.  Die ausführlichen Schritte finden Sie in den folgenden Artikeln:

-   [Importieren eines benutzerdefinierten Windows-Images aus einer VHD](upload-custom-image-shared-image-gallery.md)
-   [Importieren eines benutzerdefinierten Linux-Images aus einer VHD](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Anfügen oder Trennen eines Katalogs mit freigegebenen Images](how-to-attach-detach-shared-image-gallery.md)
* [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-use-shared-image-gallery.md)
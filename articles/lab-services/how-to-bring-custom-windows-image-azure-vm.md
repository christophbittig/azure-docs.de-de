---
title: 'Azure Lab Services: Importieren eines benutzerdefinierten Windows-Images von einem virtuellen Azure-Computer'
description: Beschreibt, wie ein benutzerdefiniertes Windows-Image von einem virtuellen Azure-Computer importiert wird.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: dc946e770f719ad76fd0c46d5fe418a937f17867
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452265"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Verwenden eines benutzerdefinierten Windows-Images von einem virtuellen Azure-Computer

Die Schritte in diesem Artikel zeigen, wie ein benutzerdefiniertes Image importiert wird, das von einem [virtuellen Azure-Computer (VM)](https://azure.microsoft.com/services/virtual-machines/) gestartet wird. Bei diesem Ansatz richten Sie ein Image auf einer Azure-VM ein und importieren es in eine Shared Image Gallery, damit es in Azure Lab Services verwendet werden kann. Bevor Sie diesen Ansatz zum Erstellen eines benutzerdefinierten Images verwenden, lesen Sie [Empfohlene Ansätze zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md), um den besten Ansatz für Ihr Szenario zu ermitteln.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Berechtigungen zum Erstellen einer Azure-VM im Azure-Abonnement Ihrer Bildungseinrichtung, um die in diesem Artikel beschriebenen Schritte ausführen zu können.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Vorbereiten eines benutzerdefinierten Images auf einer Azure-VM

1. Erstellen Sie eine Azure-VM über das [Azure-Portal](../virtual-machines/windows/quick-create-portal.md), die [PowerShell](../virtual-machines/windows/quick-create-powershell.md), die [Azure CLI](../virtual-machines/windows/quick-create-cli.md) oder eine [Azure Resource Manager-Vorlage](../virtual-machines/windows/quick-create-template.md).
    
    - Wenn Sie die Datenträgereinstellungen angeben, stellen Sie sicher, dass die Größe des Datenträgers *nicht* größer als 128 GB ist.
    
1. Installieren Sie Software, und nehmen Sie alle erforderlichen Konfigurationsänderungen am Image der Azure-VM vor.

1. Optional können Sie das Image generalisieren. Führen Sie [SysPrep](../virtual-machines/generalize.md#windows) aus, wenn Sie ein generalisiertes Image erstellen möchten. Wenn Sie ein spezialisiertes Images erstellen, können Sie mit dem nächsten Schritt fortfahren.

    Erstellen Sie ein spezialisiertes Image, wenn Sie computerspezifische Informationen und Benutzerprofile verwalten möchten. Weitere Informationen zu den Unterschieden zwischen generalisierten und spezialisierten Images finden Sie unter [Generalisierte und spezialisierte Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importieren des benutzerdefinierten Images in eine Shared Image Gallery

1. Erstellen Sie in einer Shared Image Gallery eine [Imagedefinition](../virtual-machines/image-version.md), oder wählen Sie eine vorhandene Imagedefinition aus.
     - Wählen Sie **Gen 1** als **VM-Generation** aus.
     - Wählen Sie aus, ob Sie ein **spezialisiertes** oder **generalisiertes** Image für den **Betriebssystemstatus** erstellen.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    Sie können auch eine vorhandene Imagedefinition verwenden und eine neue Version für Ihr benutzerdefiniertes Image erstellen.
    
1. [Erstellen Sie eine Imageversion](../virtual-machines/image-version.md).
    - Die Eigenschaft **Versionsnummer** verwendet das folgende Format: *MajorVersion.MinorVersion.Patch*.   
    - Wählen Sie als **Quelle** in der Dropdownliste die Option **Datenträger und/oder Momentaufnahmen** aus.
    - Wählen Sie für die Eigenschaft **Betriebssystemdatenträger** den Datenträger Ihrer Azure-VM aus, die Sie in den vorherigen Schritten erstellt haben.

    Sie können Ihr benutzerdefiniertes Image auch mithilfe von PowerShell von einer Azure-VM in eine Shared Image Gallery importieren. Weitere Informationen finden Sie in dem Skript und der Infodatei unter [Bring image to shared image gallery script (Skript zum Erstellen eines Images in einem Katalog für freigegebene Images)](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/).

## <a name="create-a-lab"></a>Erstellen eines Labs

[Erstellen Sie das Lab](tutorial-setup-classroom-lab.md) in Lab Services, und wählen Sie das benutzerdefinierte Image in Shared Image Gallery aus.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Anfügen oder Trennen einer Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md)
* [Verwenden eines Katalogs mit freigegebenen Images](how-to-use-shared-image-gallery.md)
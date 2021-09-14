---
title: 'Azure Lab Services: Importieren eines benutzerdefinierten Windows-Images von einem virtuellen Azure-Computer'
description: Beschreibt, wie ein benutzerdefiniertes Windows-Image von einem virtuellen Azure-Computer importiert wird.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: ab79f4acb0b61a46e4a8f1cebe243e6df43702bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339226"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Verwenden eines benutzerdefinierten Windows-Images von einem virtuellen Azure-Computer

Die Schritte in diesem Artikel zeigen, wie ein benutzerdefiniertes Image importiert wird, das von einem [virtuellen Azure-Computer (VM)](https://azure.microsoft.com/services/virtual-machines/) gestartet wird.  Bei diesem Ansatz richten Sie ein Image auf einer Azure-VM ein und importieren es in eine Shared Image Gallery, damit es in Lab Services verwendet werden kann.  Bevor Sie diesen Ansatz zum Erstellen eines benutzerdefinierten Images verwenden, lesen Sie den Artikel [Empfohlene Ansätze zum Erstellen benutzerdefinierter Images](approaches-for-custom-image-creation.md), um den besten Ansatz für Ihr Szenario zu ermitteln.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Berechtigungen zum Erstellen einer Azure-VM im Azure-Abonnement Ihrer Bildungseinrichtung, um die in diesem Artikel beschriebenen Schritte ausführen zu können.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Vorbereiten eines benutzerdefinierten Images auf einer Azure-VM

1. Erstellen Sie eine Azure-VM mithilfe des [Azure-Portals](../virtual-machines/windows/quick-create-portal.md), mit [PowerShell](../virtual-machines/windows/quick-create-powershell.md), mit der [Azure CLI](../virtual-machines/windows/quick-create-cli.md) oder aus einer [ARM-Vorlage](../virtual-machines/windows/quick-create-template.md).
    
    - Wenn Sie die Datenträgereinstellungen angeben, stellen Sie sicher, dass die Größe des Datenträgers *nicht* größer als 128 GB ist.
    
1. Installieren Sie Software, und nehmen Sie alle erforderlichen Konfigurationsänderungen am Image der Azure-VM vor.

1. Optional können Sie das Image generalisieren. Führen Sie [SysPrep](../virtual-machines/generalize.md#windows) aus, wenn Sie ein generalisiertes Image erstellen möchten.  Wenn Sie ein spezialisiertes Images erstellen, können Sie mit dem nächsten Schritt fortfahren.

    Sie sollten ein spezialisiertes Image erstellen, wenn Sie computerspezifische Informationen und Benutzerprofile verwalten möchten.  Weitere Informationen zu den Unterschieden zwischen generalisierten und spezialisierten Images finden Sie unter [Generalisierte und spezialisierte Images](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importieren des benutzerdefinierten Images in eine Shared Image Gallery

1. Erstellen Sie in einer Shared Image Gallery eine [Imagedefinition](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition), oder wählen Sie eine vorhandene Imagedefinition aus.
     - Wählen Sie **Gen 1** als **VM-Generation** aus.
     - Wählen Sie aus, ob Sie ein **spezialisiertes** oder **generalisiertes** Image für den **Betriebssystemstatus** erstellen.

    Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    Sie können auch eine vorhandene Imagedefinition verwenden und eine neue Version für Ihr benutzerdefiniertes Image erstellen.
    
1. [Erstellen Sie eine Imageversion](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - Die Eigenschaft **Versionsnummer** verwendet das folgende Format: *MajorVersion.MinorVersion.Patch*.   
    - Wählen Sie als **Quelle** die Option **Datenträger und/oder Momentaufnahmen** aus der Dropdownliste aus.
    - Wählen Sie für die Eigenschaft **Betriebssystemdatenträger** den Datenträger Ihrer Azure-VM aus, die Sie in den vorherigen Schritten erstellt haben.

    Sie können Ihr benutzerdefiniertes Image auch mithilfe von PowerShell von einer Azure-VM in Shared Image Gallery importieren.  Weitere Informationen finden Sie im folgenden Skript und in der zugehörigen Infodatei:
    - [Hochladen eines Images in Shared Image Gallery mit einem Skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>Erstellen eines Labs

1. [Erstellen Sie das Lab](tutorial-setup-classroom-lab.md) in Azure Lab Services, und wählen Sie das benutzerdefinierte Image in Shared Image Gallery aus.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Anfügen oder Trennen einer Shared Image Gallery](how-to-attach-detach-shared-image-gallery.md)
* [Verwenden einer Shared Image Gallery](how-to-use-shared-image-gallery.md)
---
title: Erstellen von freigegebenen Azure-Linux-VM-Images über das Portal
description: Erfahren Sie, wie Sie mit dem Azure-Portal Linux-VM-Images erstellen und freigeben.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/21/2021
ms.author: cynthn
ms.openlocfilehash: 515d836e9b36a1fb20a712ab30a561ff68e3f715
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424664"
---
# <a name="create-an-azure-compute-gallery-using-the-portal"></a>Erstellen einer Azure Compute Gallery-Instanz über das Portal

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen **Gilt für**: :heavy_check_mark: :heavy_check_mark: Einheitliche Skalierungsgruppen 

Eine [Azure Compute Gallery](../shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Laden von Bereitstellungsaufgaben verwendet werden, z.B. zum Vorabladen von Anwendungen sowie für Anwendungskonfigurationen und andere Betriebssystemkonfigurationen. 

Mit der Azure Compute Gallery können Sie ihre benutzerdefinierten Images der virtuellen Computer für andere Personen in Ihrer Organisation, innerhalb eines Azure AD-Mandanten, innerhalb einer Region oder zwischen den Regionen freigeben. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie die Images logisch gruppieren können. 

Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung in Azure (RBAC) bereitstellt. Images bieten Versionsverwaltung, und Sie können sich entschließen, jede Imageversion in eine andere Gruppe von Azure-Regionen zu replizieren. Der Katalog funktioniert nur mit verwalteten Images.

Die Funktion „Azure Compute Gallery“ verfügt über mehrere Ressourcentypen. Wir werden diese in diesem Artikel verwenden oder erstellen:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


## <a name="before-you-begin"></a>Voraussetzungen

Für das Beispiel in diesem Artikel muss ein verwaltetes Image eines generalisierten virtuellen Computers oder eine Momentaufnahme eines spezialisierten virtuellen Computers vorhanden sein. Sie können im [Tutorial: Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit Azure PowerShell](tutorial-custom-images.md) lesen, wie ein verwaltetes Image erstellt wird, oder [erstellen Sie eine Momentaufnahme](../windows/snapshot-copy-managed-disk.md) für einen spezialisierten virtuellen Computer. Für verwaltete Images und Momentaufnahmen darf der Datenträger nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels bei Bedarf den Namen der Ressourcengruppe und des virtuellen Computers.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Virtuelle Computer erstellen 

Jetzt können Sie einen oder mehrere neue virtuelle Computer erstellen. Dieses Beispiel erstellt den virtuellen Computer *myVMfromImage* in *myResourceGroup* im Rechenzentrum *USA, Osten*.

1. Wechseln Sie zur Imagedefinition. Sie können den Ressourcenfilter verwenden, um alle verfügbaren Imagedefinitionen anzuzeigen.
1. Wählen Sie im Menü oben auf der Seite für die Imagedefinition **VM erstellen** aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie *myResourceGroup* als Namen ein.
1. Geben Sie unter **Name des virtuellen Computers** die Zeichenfolge *myVM* ein.
1. Wählen Sie als **Region** die Option *USA, Osten* aus.
1. Übernehmen Sie für **Verfügbarkeitsoptionen** den Standardwert *Keine Infrastrukturredundanz erforderlich*.
1. Der Wert für **Image** wird automatisch mit der neuesten Imageversion (`latest`) ausgefüllt, wenn Sie auf der Seite für die Imagedefinition begonnen haben.
1. Wählen Sie für **Größe** in der Liste der verfügbaren Größen eine VM-Größe aus und klicken Sie dann auf **Auswählen**.
1. Wenn die Quell-VM generalisiert wurde, geben Sie unter **Administratorkonto** Ihren **Benutzernamen** und den **Öffentlichen SSH-Schlüssel** ein. Wenn die Quell-VM spezialisiert wurde, sind diese Optionen abgeblendet, da die Informationen von der Quell-VM verwendet werden.
1. Wenn Sie Remotezugriff auf den virtuellen Computer zulassen möchten, wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann in der Dropdownliste **SSH (22)** aus. Wenn Sie keinen Remotezugriff auf den virtuellen Computer ermöglichen möchten, lassen Sie für ausgewählte **Öffentliche Eingangsports** die Option **Keine** ausgewählt.
1. Wählen Sie unter „Lizenzierung“ die Option **Andere** aus, sofern Ihr Image nicht auf RedHat oder SLES basiert.
1. Wählen Sie abschließend die Schaltfläche **Überprüfen + erstellen** im unteren Seitenbereich aus.
1. Nachdem die VM-Konfiguration erfolgreich überprüft wurde, wählen Sie im unteren Seitenbereich **Erstellen** aus, um die Bereitstellung zu starten.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer aus, klicken Sie auf **Löschen**, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

Wenn Sie einzelne Ressourcen löschen möchten, müssen Sie diese in umgekehrter Reihenfolge löschen. Um beispielsweise eine Imagedefinition zu löschen, müssen Sie alle Imageversionen löschen, die aus diesem Image erstellt wurden.

## <a name="next-steps"></a>Nächste Schritte

Sie können auch mithilfe von Vorlagen eine Ressource in der Azure Compute Gallery erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Erstellen einer Imagedefinition in einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Erstellen einer Imageversion in einer Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

Weitere Informationen über Azure Compute Gallery-Kataloge finden Sie in der [Übersicht](../shared-image-galleries.md). Sollten Probleme auftreten, sehen Sie unter [Problembehandlung für Kataloge](../troubleshooting-shared-images.md) nach.
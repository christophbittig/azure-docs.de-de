---
title: Aktivieren der End-to-End-Verschlüsselung mit Verschlüsselung auf dem Host – Azure-Portal – verwaltete Datenträger
description: Verwenden Sie Verschlüsselung auf dem Host, um die End-to-End-Verschlüsselung auf Ihren von Azure verwalteten Datenträgern zu aktivieren – Azure-Portal.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bd1c2d9a9d428a765a9b621652aa23fdec94f212
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456401"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Verwenden des Azure-Portals zum Aktivieren der End-to-End-Verschlüsselung mit Verschlüsselung auf dem Host

Wenn Sie die Verschlüsselung auf dem Host aktivieren, werden die auf dem VM-Host gespeicherten Daten ruhend verschlüsselt und verschlüsselt an den Speicherdienst übermittelt. Informationen zum Konzept der Verschlüsselung auf dem Host sowie zu anderen Verschlüsselungstypen für verwaltete Datenträger finden Sie unter [Verschlüsselung auf dem Host: End-to-End-Verschlüsselung für Ihre VM-Daten](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

Temporäre Datenträger und kurzlebige Betriebssystemdatenträger werden im Ruhezustand mit plattformseitig verwalteten Schlüsseln verschlüsselt, wenn Sie die End-to-End-Verschlüsselung aktivieren. Der Cache von Betriebssystemdatenträgern und regulären Datenträgern wird im Ruhezustand entweder mit kundenseitig verwalteten Schlüsseln oder mit plattformseitig verwalteten Schlüsseln verschlüsselt – abhängig vom ausgewählten Datenträgerverschlüsselungstyp. Wenn ein Datenträger beispielsweise mit kundenseitig verwalteten Schlüsseln verschlüsselt ist, wird der Cache für den Datenträger ebenfalls mit kundenseitig verwalteten Schlüsseln verschlüsselt. Ist ein Datenträger dagegen mit plattformseitig verwalteten Schlüsseln verschlüsselt, wird auch der Cache für den Datenträger mit plattformseitig verwalteten Schlüsseln verschlüsselt.

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Unterstützte VM-Größen

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Feature für Ihr Abonnement aktivieren, bevor Sie die EncryptionAtHost-Eigenschaft für Ihre VM/VMSS verwenden. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1. **Azure-Portal**: Klicken Sie im [Azure-Portal](https://portal.azure.com) auf das Symbol „Cloud Shell“:

    ![Symbol zum Starten von Cloud Shell über das Azure-Portal](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies dauert einige Minuten), bevor Sie das Feature ausprobieren.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Melden Sie sich mit dem [bereitgestellten Link](https://aka.ms/diskencryptionupdates) beim Azure-Portal an.

> [!IMPORTANT]
> Sie müssen den [bereitgestellten Link](https://aka.ms/diskencryptionupdates) verwenden, um auf das Azure-Portal zuzugreifen. Die Verschlüsselung auf dem Host ist derzeit im öffentlichen Azure-Portal ohne Verwendung des Links nicht sichtbar.

## <a name="deploy-a-vm-with-platform-managed-keys"></a>Bereitstellen eines virtuellen Computers mit plattformseitig verwalteten Schlüsseln

1. Melden Sie sich beim [Azure-Portal](https://aka.ms/diskencryptionupdates) an.
1. Suchen Sie nach **Virtuelle Computer**, und wählen Sie **+ Hinzufügen** aus, um eine VM zu erstellen.
1. Erstellen Sie einen neuen virtuellen Computer, und wählen Sie eine geeignete Region und eine unterstützte VM-Größe aus.
1. Füllen Sie die anderen Werte im Bereich **Grundlagen** nach Ihren Vorstellungen aus, und fahren Sie dann mit dem Bereich **Datenträger** fort.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Screenshot: Bereich „Grundlagen“ für die Erstellung virtueller Computer mit hervorgehobenen Optionen für Region und VM-Größe":::

1. Wählen Sie im Bereich **Datenträger** die Option **Verschlüsselung auf dem Host** aus.
1. Treffen Sie die verbleibende Auswahl nach Wunsch.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/host-based-encryption-platform-keys.png" alt-text="Screenshot: Datenträgerbereich für die Erstellung virtueller Computer mit hervorgehobener Option für die Verschlüsselung auf dem Host":::

1. Beenden Sie den VM-Bereitstellungsprozess, und wählen Sie Optionen passend für Ihre Umgebung aus.

Sie haben nun einen virtuellen Computer mit aktivierter Verschlüsselung auf dem Host bereitgestellt, und der Cache für den Datenträger wird mit plattformseitig verwalteten Schlüsseln verschlüsselt.

## <a name="deploy-a-vm-with-customer-managed-keys"></a>Bereitstellen eines virtuellen Computers mit kundenseitig verwalteten Schlüsseln

Alternativ können Sie kundenseitig verwaltete Schlüssel verwenden, um Ihre Datenträgercaches zu verschlüsseln.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Erstellen einer Azure Key Vault-Instanz und eines Datenträgerverschlüsselungssatzes

Nach dem Aktivieren des Features müssen Sie eine Azure Key Vault-Instanz und einen Datenträgerverschlüsselungssatz einrichten, sofern noch nicht geschehen.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Nachdem Sie nun einen Azure Key Vault und einen Datenträgerverschlüsselungssatz eingerichtet haben, können Sie eine VM bereitstellen, die Verschlüsselung auf dem Host verwendet.

1. Melden Sie sich beim [Azure-Portal](https://aka.ms/diskencryptionupdates) an.
1. Suchen Sie nach **Virtuelle Computer**, und wählen Sie **+ Hinzufügen** aus, um eine VM zu erstellen.
1. Erstellen Sie einen neuen virtuellen Computer, und wählen Sie eine geeignete Region und eine unterstützte VM-Größe aus.
1. Füllen Sie die anderen Werte im Bereich **Grundlagen** nach Ihren Vorstellungen aus, und fahren Sie dann mit dem Bereich **Datenträger** fort.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Screenshot: Bereich „Grundlagen“ für die Erstellung virtueller Computer mit hervorgehobenen Optionen für Region und VM-Größe":::

1. Wählen Sie im Bereich **Datenträger** unter **SSE encryption type** (SSE-Verschlüsselungstyp) die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** und anschließend Ihren Datenträgerverschlüsselungssatz aus.
1. Aktivieren Sie das Kontrollkästchen **Verschlüsselung auf dem Host**.
1. Treffen Sie die verbleibende Auswahl nach Wunsch.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-host-based-encryption-customer-managed-keys.png" alt-text="Screenshot: Datenträgerbereich für die Erstellung virtueller Computer mit hervorgehobener Option für die Verschlüsselung auf dem Host und ausgewählter Option für kundenseitig verwaltete Schlüssel":::

1. Beenden Sie den VM-Bereitstellungsprozess, und wählen Sie Optionen passend für Ihre Umgebung aus.

Sie haben nun einen virtuellen Computer mit aktivierter Verschlüsselung auf dem Host erstellt.

## <a name="disable-host-based-encryption"></a>Deaktivieren der hostbasierten Verschlüsselung

Heben Sie zuerst die Zuordnung Ihres virtuellen Computers auf. Die Verschlüsselung auf dem Host kann nur deaktiviert werden, wenn die Zuordnung Ihres virtuellen Computers aufgehoben wurde.

1. Wählen Sie auf Ihrem virtuellen Computer die Option **Datenträger** und anschließend **Zusätzliche Einstellungen** aus.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-host-based-encryption-additional-settings.png" alt-text="Screenshot: Datenträgerbereich eines virtuellen Computers mit hervorgehobener Option „Zusätzliche Einstellungen“":::

1. Wählen Sie für **Verschlüsselung auf dem Host** die Option **Nein** und anschließend **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

[Beispiele für Azure Resource Manager-Vorlagen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)

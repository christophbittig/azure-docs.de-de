---
title: 'Azure-Portal: Aktivieren kundenseitig verwalteter Schlüssel mit SSE – verwaltete Datenträger'
description: Aktivieren Sie kundenseitig verwaltete Schlüssel auf Ihren verwalteten Datenträgern über das Azure-Portal.
author: roygara
ms.date: 08/31/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 2117fc6d425233ee2de4954173981ad5cfda5f18
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318470"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Verwenden des Azure-Portals zum Aktivieren der serverseitigen Verschlüsselung mit kundenseitig verwalteten Schlüsseln für verwaltete Datenträger

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: 

Mit Azure Disk Storage können Sie Ihre eigenen Schlüssel verwalten, wenn Sie die serverseitige Verschlüsselung (Server Side Encryption, SSE) für verwaltete Datenträger verwenden. Informationen zum Konzept der SSE mit kundenseitig verwalteten Schlüsseln sowie weitere Verschlüsselungstypen für verwaltete Datenträger finden Sie im Abschnitt **Vom Kunden verwaltete Schlüssel** in folgendem Artikel zur Datenträgerverschlüsselung: [Kundenseitig verwalteter Schlüssel](disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Beschränkungen

Vorerst gelten für vom Kunden verwaltete Schlüssel die folgenden Einschränkungen:

- Wenn dieses Feature für Ihren Datenträger aktiviert ist, können Sie es nicht deaktivieren.
    Bei Bedarf müssen Sie alle Daten auf einen anderen verwalteten Datenträger ohne kundenseitig verwaltete Schlüssel kopieren:

    - Linux: [Kopieren eines verwalteten Datenträgers](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows: [Kopieren eines verwalteten Datenträgers](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

In den folgenden Abschnitten wird erläutert, wie Sie von Kunden verwaltete Schlüssel für verwaltete Datenträger aktivieren und verwenden:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Nachdem Sie nun Ihren Schlüsseltresor und den Datenträgerverschlüsselungssatz erstellt und eingerichtet haben, können Sie eine VM mit der Verschlüsselung bereitstellen.
Das Verfahren zur VM-Bereitstellung ähnelt dem Standardbereitstellungsverfahren. Die einzigen Unterschiede bestehen darin, dass Sie den virtuellen Computer in derselben Region wie Ihre anderen Ressourcen bereitstellen müssen und einen vom Kunden verwalteten Schlüssel verwenden.

1. Suchen Sie nach **Virtuelle Computer**, und wählen Sie **+ Hinzufügen** aus, um eine VM zu erstellen.
1. Wählen Sie auf dem Blatt **Standard** dieselbe Region aus, in der sich Ihr Datenträgerverschlüsselungssatz und Ihre Azure Key Vault-Instanz befinden.
1. Geben Sie die anderen Werte auf dem Blatt **Standard** nach Wunsch ein.

    ![Screenshot des VM-Erstellungsvorgangs mit hervorgehobenem Wert für die Region.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Wählen Sie auf dem Blatt **Datenträger** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** aus.
1. Wählen Sie den Datenträgerverschlüsselungssatz in der Dropdownliste **Datenträgerverschlüsselungssatz** aus.
1. Treffen Sie die verbleibende Auswahl nach Wunsch.

    ![Screenshot des VM-Erstellungsvorgangs, Datenträgerblatt. Das Dropdownfeld für den Datenträgerverschlüsselungssatz ist hervorgehoben.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Aktivieren auf einem vorhandenen Datenträger

> [!CAUTION]
> Zum Aktivieren der Datenträgerverschlüsselung auf Datenträgern, die an eine VM angefügt sind, müssen Sie die VM beenden.
    
1. Navigieren Sie zu einer VM, die sich in derselben Region befindet wie einer Ihrer Datenträgerverschlüsselungssätze.
1. Öffnen Sie die VM, und wählen Sie **Beenden** aus.

    ![Screenshot der Hauptüberlagerung für Ihre Beispiel-VM, wobei die Schaltfläche „Beenden“ hervorgehoben ist.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Nachdem die VM beendet wurde, wählen Sie **Datenträger** und dann den Datenträger aus, den Sie verschlüsseln möchten.

    ![Screenshot Ihrer Beispiel-VM mit geöffnetem Datenträgerblatt. Der Betriebssystemdatenträger ist als Beispieldatenträger hervorgehoben, den Sie auswählen können.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Wählen Sie **Verschlüsselung**, anschließend **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** und dann in der Dropdownliste den Datenträgerverschlüsselungssatz aus.
1. Wählen Sie **Speichern** aus.

    ![Screenshot Ihres Beispiel-Betriebssystemdatenträgers. Das Verschlüsselungsblatt ist geöffnet, die Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel und Ihr Beispiel-Azure Key Vault sind ausgewählt. Nach dem Treffen dieser Auswahl wird die Speichern-Schaltfläche ausgewählt.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Wiederholen Sie diesen Vorgang für alle anderen an die VM angefügten Datenträger, die Sie verschlüsseln möchten.
1. Wenn die Umstellung der Datenträger auf kundenseitig verwaltete Schlüssel abgeschlossen ist und Sie keine weiteren angefügten Datenträger mehr verschlüsseln möchten, können Sie Ihren virtuellen Computer starten.

> [!IMPORTANT]
> Von Kunden verwaltete Schlüssel basieren auf verwalteten Identitäten für Azure-Ressourcen, einem Feature von Azure Active Directory (Azure AD). Wenn Sie vom Kunden verwaltete Schlüssel konfigurieren, wird Ihren Ressourcen im Hintergrund automatisch eine verwaltete Identität zugewiesen. Wenn Sie anschließend das Abonnement, die Ressourcengruppe oder den verwalteten Datenträger von einem Azure AD-Verzeichnis in ein anderes Verzeichnis verschieben, wird die den verwalteten Datenträgern zugeordnete verwaltete Identität nicht an den neuen Mandanten übertragen, sodass kundenseitig verwaltete Schlüssel möglicherweise nicht mehr funktionieren. Weitere Informationen finden Sie unter [Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

### <a name="enable-automatic-key-rotation-on-an-existing-disk-encryption-set"></a>Aktivieren der automatischen Schlüsselrotation für einen vorhandenen Datenträgerverschlüsselungssatz

1. Navigieren Sie zum Datenträgerverschlüsselungssatz, auf dem Sie die [automatische Schlüsselrotation](disk-encryption.md#automatic-key-rotation-of-customer-managed-keys) aktivieren möchten.
1. Wählen Sie unter **Einstellungen** die Option **Schlüssel** aus.
1. Wählen Sie **automatische Schlüsselrotation** und dann **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

- [Untersuchen der Azure Resource Manager-Vorlagen zum Erstellen verschlüsselter Datenträger mit vom Kunden verwalteten Schlüsseln](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)
- [Replizieren von Computern mit Datenträgern, die für kundenseitig verwaltete Schlüssel aktiviert sind](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Einrichten der Notfallwiederherstellung von virtuellen VMware-Computern in Azure mithilfe von PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Einrichten der Notfallwiederherstellung in Azure für Hyper-V-VMs mithilfe von PowerShell und Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
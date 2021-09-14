---
title: Aktivieren von SMB Multichannel
description: Erfahren Sie, wie Sie SMB Multichannel für Azure Premium-Dateifreigaben aktivieren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7bbfba257126b741751d169af9390615bec6917c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459677"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Aktivieren von SMB Multichannel für ein FileStorage-Konto (Vorschau) 

Azure FileStorage-Konten unterstützen SMB Multichannel (Vorschau), wodurch die Leistung eines SMB 3.x-Clients erhöht wird, indem mehrere Netzwerkverbindungen mit Ihren Premium-Dateifreigaben hergestellt werden. Dieser Artikel enthält eine schrittweise Anleitung zum Aktivieren von SMB Multichannel für ein vorhandenes Speicherkonto. Ausführliche Informationen zu Azure Files SMB Multichannel finden Sie unter  [SMB Multichannel-Leistung](storage-files-smb-multichannel-performance.md).

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionale Verfügbarkeit

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines FileStorage-Kontos](./storage-how-to-create-file-share.md).

## <a name="getting-started"></a>Erste Schritte

Öffnen Sie ein PowerShell-Fenster, und melden Sie sich bei Ihrem Azure-Abonnement an. Von dort aus können Sie sich mit den folgenden Befehlen für die Vorschau von SMB Multichannel registrieren.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Die Registrierung kann bis zu einer Stunde dauern.

### <a name="verify-that-feature-registration-is-complete"></a>Überprüfen, ob die Featureregistrierung vollständig ist

Da es bis zu einer Stunde dauern kann, bis die Funktion für Ihr Speicherkonto aktiviert wird, können Sie den folgenden Befehl verwenden, um zu überprüfen, ob sie für Ihr Abonnement registriert ist:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Aktivieren von SMB Multichannel 
Nachdem Sie ein FileStorage-Konto erstellt haben, können Sie die Anweisungen zum Aktualisieren der SMB Multichannel-Einstellungen für Ihr Speicherkonto befolgen.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Melden Sie sich am Azure-Portal an, und navigieren Sie zu dem FileStorage-Speicherkonto, für das Sie SMB Multichannel konfigurieren möchten.
1. Wählen Sie **Dateifreigaben** unter **Dateidienst** aus, und wählen Sie dann **Dateifreigabeeinstellungen** aus.
1. Schalten Sie **SMB Multichannel** **ein** (oder für die Deaktivierung **aus**), und wählen Sie **Speichern** aus.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Screenshot: Speicherkonto, SMB Multichannel ist eingeschaltet"  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Wenn die Option SMB Multichannel unter **Dateifreigabeeinstellungen** nicht sichtbar ist oder bei der Aktualisierung der Konfiguration ein Fehler auftritt, der auf eine fehlgeschlagene Aktualisierung der Einstellungen hinweist, vergewissern Sie sich, dass Ihr Abonnement registriert ist und sich Ihr Konto in einer der [unterstützten Regionen](#regional-availability) mit unterstütztem Kontotyp und Replikation befindet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Legen Sie die Variablen `$resourceGroupName` und `$storageAccountName` auf Ihre Ressourcengruppe und Ihr Speicherkonto fest, bevor Sie diese PowerShell-Befehle ausführen.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Die Konfiguration von SMB Multichannel wird von der Azure CLI noch nicht unterstützt. Weitere Informationen finden Sie in den Portalanweisungen zum Konfigurieren von SMB Multichannel für ein Speicherkonto.

---

> [!NOTE]
> Alle Änderungen an den SMB Multichannel-Konfigurationseinstellungen gelten für alle Dateifreigaben unter dem Speicherkonto. Sie müssen die Freigabe jedoch auf dem Client erneut einbinden, damit die Änderungen wirksam werden.


## <a name="next-steps"></a>Nächste Schritte 

- [Binden Sie die Dateifreigabe erneut ein](storage-how-to-use-files-windows.md), um SMB Multichannel zu nutzen.
- [Beheben Sie alle Probleme, die mit SMB Multichannel in Verbindung stehen](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Weitere Informationen zu den Verbesserungen finden Sie unter [SMB Multichannel-Leistung](storage-files-smb-multichannel-performance.md)
- Weitere Informationen zum Feature Windows SMB Multichannel finden Sie unter [Verwalten von SMB Multichannel](/azure-stack/hci/manage/manage-smb-multichannel).

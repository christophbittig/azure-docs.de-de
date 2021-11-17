---
title: 'Azure-Portal: Einschränken des Import-/Exportzugriffs auf verwaltete Datenträger'
description: Aktivieren Sie über das Azure-Portal Private Link für Ihre verwalteten Datenträger. Dadurch können Sie Datenträger innerhalb Ihres virtuellen Netzwerks sicher exportieren und importieren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/03/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 57eef48a188f07847efcda31b4cd6a6043f2ae2a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491011"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>Einschränken des Import-/Exportzugriffs für verwaltete Datenträger mithilfe von Azure Private Link

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen:heavy_check_mark: Einheitliche Skalierungsgruppen

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) verwenden, um den Export und Import verwalteter Datenträger einzuschränken und sichereren Zugriff auf Daten über eine [private Verbindung](../private-link/private-link-overview.md) von Clients in Ihrem virtuellen Azure-Netzwerk zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks für Ihre verwalteten Datenträger. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und den verwalteten Datenträgern wird nur über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet und somit nicht dem öffentlichen Internet ausgesetzt.

Wenn Sie Private Link zum Exportieren und Importieren verwalteter Datenträger verwenden möchten, erstellen Sie zunächst eine Datenträgerzugriffsressource und verknüpfen sie mit einem virtuellen Netzwerk im gleichen Abonnement, indem Sie einen privaten Endpunkt erstellen. Ordnen Sie dann einer Instanz des Datenträgerzugriffs einen Datenträger oder eine Momentaufnahme zu.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>Erstellen einer Datenträgerzugriffsressource

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie über [diesen Link](https://aka.ms/disksprivatelinks) zu **Datenträgerzugriffe**.

    > [!IMPORTANT]
    > Sie müssen über den [bereitgestellten Link](https://aka.ms/disksprivatelinks) zum Bereich „Datenträgerzugriffe“ navigieren. Andernfalls wird das Blatt nicht im öffentlichen Portal angezeigt.

1. Wählen Sie **+ Erstellen** aus, um eine neue Datenträgerzugriffsressource zu erstellen.
1. Wählen Sie im Bereich **Datenträgerzugriff erstellen** Ihr Abonnement und eine Ressourcengruppe aus. Geben Sie unter **Details zur Instanz** einen Namen ein, und wählen Sie eine Region aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Screenshot: Bereich zum Erstellen des Datenträgerzugriffs. Geben Sie den gewünschten Namen ein, wählen Sie eine Region und eine Ressourcengruppe aus, und setzen Sie den Vorgang fort.":::

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Wenn Ihre Ressource erstellt wurde, navigieren Sie direkt zu ihr.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Screenshot: Schaltfläche „Zu Ressource wechseln“ im Portal":::

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Als Nächstes müssen Sie einen privaten Endpunkt erstellen und für den Datenträgerzugriff konfigurieren.

1. Wählen Sie für Ihre Datenträgerzugriffsressource unter **Einstellungen** die Option **Verbindungen mit privatem Endpunkt** aus.
1. Wählen Sie **+ Privaten Endpunkt hinzufügen** aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Screenshot: Übersichtsbereich für die Datenträgerzugriffsressource. „Verbindungen mit privatem Endpunkt“ ist hervorgehoben.":::

1. Wählen Sie im Bereich **Privaten Endpunkt erstellen** eine Ressourcengruppe aus.
1. Geben Sie einen Namen ein, und wählen Sie die gleiche Region aus, in der auch Ihre Datenträgerzugriffsressource erstellt wurde.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Screenshot: Workflow zur Erstellung des privaten Endpunkts (erster Bereich). Falls Sie nicht die richtige Region auswählen, treten später unter Umständen Fehler auf.":::

1. Klicken Sie auf **Weiter: Ressource** aus.
1. Wählen Sie im Bereich **Ressource** die Option **Mit einer Azure-Ressource in meinem Verzeichnis verbinden** aus.
1. Wählen Sie für **Ressourcentyp** den Wert **Microsoft.Compute/diskAccesses** aus.
1. Wählen Sie für **Ressource** die zuvor erstellte Datenträgerzugriffsressource aus.
1. Übernehmen Sie unter **Untergeordnete Zielressource** den Wert **Datenträger**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Screenshot: Workflow zum Erstellen des privaten Endpunkts (zweiter Bereich) mit hervorgehobenen Werten (Ressourcentyp, Ressource, Untergeordnete Zielressource)":::

1. Wählen Sie **Weiter: Konfiguration** aus.
1. Wählen Sie das virtuelle Netzwerk aus, auf das Sie den Import und Export von Datenträgern beschränken möchten. Dadurch wird der Import und Export Ihres Datenträgers für andere virtuelle Netzwerke verhindert.

    > [!NOTE]
    > Wenn für das ausgewählte Subnetz eine Netzwerksicherheitsgruppe (NSG) aktiviert ist, wird diese nur für private Endpunkte in diesem Subnetz deaktiviert. Für andere Ressourcen in diesem Subnetz wird die Netzwerksicherheitsgruppe weiterhin durchgesetzt.

1. Wählen Sie das richtige Subnetz aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Screenshot: Workflow zum Erstellen des privaten Endpunkts (dritter Bereich) mit hervorgehobenem virtuellem Netzwerk und Subnetz":::

1. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="enable-private-endpoint-on-your-disk"></a>Aktivieren des privaten Endpunkts auf Ihrem Datenträger

1. Navigieren Sie zu dem Datenträger, den Sie konfigurieren möchten.
1. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.
1. Wählen Sie **Private endpoint (through disk access)** (Privater Endpunkt (über Datenträgerzugriff)) und dann den zuvor erstellten Datenträgerzugriff aus.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Screenshot: Bereich „Netzwerk“ für verwaltete Datenträger. Privater Endpunkt und ausgewählter Datenträgerzugriff hervorgehoben. Durch das Speichern wird der Datenträger für diesen Zugriff konfiguriert.":::

1. Wählen Sie **Speichern** aus.

Sie haben nun eine private Verbindung konfiguriert, die Sie zum Importieren und Exportieren Ihres verwalteten Datenträgers verwenden können.

## <a name="next-steps"></a>Nächste Schritte

- Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region: [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) oder [Azure PowerShell-Modul](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Herunterladen einer VHD: [Windows](windows/download-vhd.md) oder [Linux](linux/download-vhd.md)
- [Häufig gestellte Fragen zu privaten Verbindungen und verwalteten Datenträgern](./faq-for-disks.yml)
- [Exportieren/Kopieren verwalteter Momentaufnahmen als VHD in ein Speicherkonto in einer anderen Region mit PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)

---
title: Initiieren eines Speicherkontofailovers
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie ein Kontofailover für den Fall initiieren, dass der primäre Endpunkt für das Speicherkonto nicht mehr verfügbar ist. Bei dem Failover wird die sekundäre Region so aktualisiert, dass sie zur primären Region für das Speicherkonto wird.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/07/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab764fd95168fef768efd0687adb5e2841bfb140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609287"
---
# <a name="initiate-a-storage-account-failover"></a>Initiieren eines Speicherkontofailovers

Wenn der primäre Endpunkt für das georedundante Speicherkonto aus einem bestimmten Grund nicht mehr verfügbar ist, können Sie ein Kontofailover initiieren. Bei einem Kontofailover wird der sekundäre Endpunkt so aktualisiert, dass er zum primären Endpunkt für das Speicherkonto wird. Nach Abschluss des Failovers können Clients in die neue primäre Region schreiben. Durch ein erzwungenes Failover können Sie die Hochverfügbarkeit für Ihre Anwendungen aufrechterhalten.

In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle ein Kontofailover für Ihr Speicherkonto initiieren. Weitere Informationen zum Kontofailover finden Sie unter [Notfallwiederherstellung und Speicherkontofailover](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Ein Kontofailover geht in der Regel mit einem gewissen Datenverlust einher. Informationen zu den Auswirkungen eines Kontofailovers und zur Vorbereitung auf Datenverluste finden Sie unter [Grundlegendes zum Vorgang des Kontofailovers](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie ein Kontofailover für Ihr Speicherkonto ausführen können, müssen Sie sicherstellen, dass Ihr Speicherkonto für Georeplikation konfiguriert ist. Ihr Speicherkonto kann jede der folgenden Redundanzoptionen verwenden:

- Georedundanter Speicher (GRS) oder georedundanter Speicher mit Lesezugriff (RA-GRS)
- Geozonenredundanter Speicher (GZRS) oder geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)

Weitere Informationen zur Azure Storage-Redundanz finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

Beachten Sie, dass die folgenden Funktionen und Dienste bei einem Kontofailover nicht unterstützt werden:

- Das Speicherkontofailover wird von der Azure-Dateisynchronisierung nicht unterstützt. Für Speicherkonten, die Azure-Dateifreigaben enthalten, die als Cloud-Endpunkte in der Azure-Dateisynchronisierung verwendet werden, sollte kein Failover durchgeführt werden. Dies würde das Funktionieren der Synchronisierung beenden und könnte außerdem bei neu einbezogenen Dateien zu unerwartetem Datenverlust führen.
- Speicherkonten mit aktiviertem hierarchischem Namespace (z. B. für Data Lake Storage Gen2) werden zurzeit nicht unterstützt.
- Für ein Speicherkonto mit Premium-Blockblobs kann kein Failover durchgeführt werden. Speicherkonten, die Premium-Blockblobs unterstützen, unterstützen derzeit keine Georedundanz.
- Für ein Speicherkonto mit Containern mit aktivierter [WORM-Unveränderlichkeitsrichtlinie](../blobs/immutable-storage-overview.md) kann kein Failover durchgeführt werden. Entsperrte/gesperrte Richtlinien für die zeitbasierte Aufbewahrung oder die gesetzliche Aufbewahrungspflicht verhindern ein Failover zur Einhaltung der Richtlinien.

## <a name="initiate-the-failover"></a>Initiieren des Failovers

## <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um ein Kontofailover im Azure-Portal zu initiieren:

1. Navigieren Sie zum Speicherkonto.
1. Wählen Sie unter **Einstellungen** die Option **Georeplikation** aus. In der folgenden Abbildung sind die Georeplikation und der Failoverstatus eines Speicherkontos dargestellt.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Screenshot: Georeplikation und Failoverstatus":::

1. Überprüfen Sie, ob Ihr Speicherkonto als georedundanter Speicher (GRS) oder georedundanter Speicher mit Lesezugriff (RA-GRS) konfiguriert ist. Wenn dies nicht der Fall ist, wählen Sie unter **Einstellungen** die Option **Konfiguration** aus, um das Konto so zu ändern, dass es georedundant ist.
1. Die Eigenschaft **Letzte Synchronisierungszeit** gibt an, wie weit der sekundäre Endpunkt hinter dem primären Endpunkt zurückliegt. Über **Letzte Synchronisierungszeit** lässt sich der Umfang des Datenverlusts nach Abschluss des Failovers abschätzen. Weitere Informationen zum Überprüfen der Eigenschaft **Letzte Synchronisierungszeit** finden Sie unter [Überprüfen der Eigenschaft „Letzte Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md).
1. Wählen Sie **Auf Failover vorbereiten** aus.
1. Lesen Sie die Informationen im Bestätigungsdialogfeld. Geben Sie anschließend **Ja** ein, um das Failover zu bestätigen und zu initiieren.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Screenshot: Bestätigungsdialogfeld für ein Kontofailover":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Das Feature „Kontofailover“ ist allgemein verfügbar, erfordert aber trotzdem ein Vorschaumodul für PowerShell. Zur Verwendung von PowerShell zum Initiieren eines Kontofailovers müssen Sie zunächst das Modul Az.Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) installieren. Führen Sie dazu folgende Schritte aus:

1. Deinstallieren Sie alle älteren Installationen von Azure PowerShell:

    - Entfernen Sie alle früheren Installationen von Azure PowerShell mit der Einstellung **Apps & Features** (unter **Einstellungen**) aus Windows.
    - Entfernen Sie alle **Azure**-Module aus `%Program Files%\WindowsPowerShell\Modules`.

1. Vergewissern Sie sich, dass die aktuelle Version von PowerShellGet installiert ist. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie den folgenden Befehl aus, um die neueste Version zu installieren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Schließen Sie nach dem Installieren von PowerShellGet das PowerShell-Fenster, und öffnen Sie es dann erneut.

1. Installieren Sie die neueste Version von Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installieren ein Azure Storage-Vorschaumodul, das Kontofailover unterstützt:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Führen Sie den folgenden Befehl aus, um ein Kontofailover über PowerShell zu initiieren:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie die folgenden Befehle aus, um ein Kontofailover über die Azure-Befehlszeilenschnittstelle zu initiieren:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Wichtige Auswirkungen eines Kontofailovers

Wenn Sie ein Kontofailover für Ihr Speicherkonto initiieren, werden die DNS-Einträge für den sekundären Endpunkt so aktualisiert, dass der sekundäre Endpunkt zum primären Endpunkt wird. Vor dem Initiieren eines Failovers sollten Sie daher die möglichen Auswirkungen auf Ihr Speicherkonto verstehen.

Wenn Sie den Umfang eines wahrscheinlichen Datenverlustes schätzen möchten, bevor Sie ein Failover initiieren, aktivieren Sie die Eigenschaft **Letzte Synchronisierungszeit**. Weitere Informationen zum Überprüfen der Eigenschaft **Letzte Synchronisierungszeit** finden Sie unter [Überprüfen der Eigenschaft „Letzte Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md).

Die Zeit bis zum Failover nach der Initiierung kann variieren, beträgt aber in der Regel weniger als eine Stunde.

Nach dem Failover wird der Speicherkontotyp automatisch in einen lokal redundanten Speicher (LRS) in der neuen primären Region konvertiert. Sie können den georedundanten Speicher (GRS) oder den georedundanten Speicher mit Lesezugriff (RA-GRS) wieder aktivieren. Beachten Sie, dass für die Konvertierung von LRS in GRS oder RA-GRS zusätzliche Kosten anfallen. Die Kosten ergeben sich aus den Gebühren für ausgehenden Netzwerkdatenverkehr zum erneuten Replizieren der Daten in die neue sekundäre Region. Weitere Informationen finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

Nachdem Sie wieder GRS für Ihr Speicherkonto aktiviert haben, beginnt Microsoft, die Daten in Ihrem Konto in die neue sekundäre Region zu replizieren. Die Replikationszeit hängt von vielen Faktoren ab, z. B.:

- Anzahl und Größe der im Speicherkonto gespeicherten Objekte. Viele kleine Objekte können länger dauern als wenige, größere Objekte.
- Verfügbare Ressourcen für die Hintergrundreplikation, z. B. CPU, Arbeitsspeicher, Datenträger und WAN-Kapazität. Livedatenverkehr hat Vorrang vor der Georeplikation.
- Bei Verwendung von Blob Storage: Anzahl der Momentaufnahmen pro Blob
- Bei Verwendung von Table Storage: die [Datenpartitionierungsstrategie](/rest/api/storageservices/designing-a-scalable-partitioning-strategy-for-azure-table-storage). Der Replikationsprozess kann nicht über die Anzahl der verwendeten Partitionsschlüssel hinaus skaliert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Notfallwiederherstellung und Speicherkontofailover](storage-disaster-recovery-guidance.md)
- [Überprüfen der Eigenschaft „Zeitpunkt der letzten Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md)
- [Verwenden von Georedundanz zum Entwerfen von hochverfügbaren Anwendungen](geo-redundant-design.md)
- [Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blob Storage](../blobs/storage-create-geo-redundant-storage.md)

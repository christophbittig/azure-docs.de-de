---
title: Ändern der Replikation eines Speicherkontos
titleSuffix: Azure Storage
description: In diesem Artikel erfahren Sie, wie Sie Art und Weise ändern, in der Daten in einem vorhandenen Speicherkonto repliziert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7b5f2e6e8f883470826343c0aee1103a9a245be4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437541"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Ändern der Replikation eines Speicherkontos

Azure Storage speichert immer mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern über Netzwerk- oder Stromausfälle bis hin zu schweren Naturkatastrophen. Durch diese Redundanz wird sichergestellt, dass Ihr Speicherkonto auch bei Ausfällen die [Servicelevelvereinbarung (SLA) für Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) erfüllt.

Azure Storage bietet die folgenden Replikationstypen:

- Lokal redundanter Speicher (LRS)
- Zonenredundanter Speicher (ZRS)
- Georedundanter Speicher (GRS) oder georedundanter Speicher mit Lesezugriff (RA-GRS)
- Geozonenredundanter Speicher (GZRS) oder geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)

Eine Übersicht über diese Optionen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Wechseln zwischen Replikationstypen

Sie können den Replikationstyp von Speicherkonten ändern, allerdings sind einige Szenarien etwas einfacher als andere. Wenn Sie Georeplikation oder Lesezugriff zur sekundären Region hinzufügen oder daraus entfernen möchten, können Sie das Azure-Portal, PowerShell oder die Azure CLI verwenden, um die Replikationseinstellung zu ändern. Wenn Sie jedoch durch Wechseln von LRS zu ZRS oder umgekehrt die Art und Weise ändern möchten, in der Daten in der primären Region repliziert werden, müssen Sie eine manuelle Migration ausführen.

Die folgende Tabelle bietet eine Übersicht über die Möglichkeiten zum Wechseln zwischen den Replikationstypen:

| Wechsel | … zu LRS | … zu GRS/RA-GRS | … zu ZRS | … zu GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>… von LRS</b> | – | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung<sup>1,2</sup> | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Anfordern einer Livemigration | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Wechsel zu GRS/RA-GRS und anschließendes Anfordern einer Livemigration<sup>3</sup> |
| <b>… von GRS/RA-GRS</b> | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung | – | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Wechsel zu LRS und anschließendes Anfordern einer Livemigration<sup>3</sup> | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Anfordern einer Livemigration<sup>3</sup> |
| <b>… von ZRS</b> | Ausführen einer manuellen Migration | Ausführen einer manuellen Migration | – | Anfordern einer Livemigration<sup>3</sup> |
| <b>… von GZRS/RA-GZRS</b> | Ausführen einer manuellen Migration | Ausführen einer manuellen Migration | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung | – |

<sup>1</sup> Hierbei fällt eine einmalige Gebühr für ausgehende Daten an.<br />
<sup>2</sup> Die Migration von LRS zu GRS wird nicht unterstützt, wenn das Speicherkonto Blobs auf der Archivebene enthält.<br />
<sup>3</sup> Die Livemigration wird für Standard-Speicherkonten vom Typ „Universell V2“ und für Premium-Dateifreigabekonten unterstützt. Die Livemigration wird für Premium-Blockblob- oder Seitenblobspeicherkonten nicht unterstützt.

> [!CAUTION]
> Wenn Sie ein [Kontofailover](storage-disaster-recovery-guidance.md) für Ihr (RA-)GRS- oder (RA-)GZRS-Konto durchgeführt haben, ist das Konto in der neuen primären Region nach dem Failover lokal redundant (LRS). Livemigration zu ZRS oder GZRS für ein LRS-Konto, das sich aus einem Failover ergibt, wird nicht unterstützt. Dies gilt sogar für sogenannte Failbackvorgänge. Wenn Sie z. B. ein Kontofailover von RA-GZRS zum LRS-Konto in der sekundären Region durchführen und das Konto dann erneut für RA-GRS konfigurieren und ein weiteres Kontofailover zur ursprünglichen primären Region durchführen, können Sie sich nicht an den Support wenden, um die ursprüngliche Livemigration zu RA-GZRS in der primären Region durchzuführen. Stattdessen müssen Sie eine manuelle Migration zu ZRS oder GZRS durchführen.

## <a name="change-the-replication-setting"></a>Ändern der Replikationseinstellung

Sie können das Azure-Portal, PowerShell oder die Azure CLI verwenden, um die Replikationseinstellung für ein Speicherkonto zu ändern, sofern Sie nicht die Art und Weise verändern, in der Daten in der primären Region repliziert werden. Wenn Sie von LRS in der primären Region zu ZRS in der primären Region oder umgekehrt migrieren, müssen Sie entweder eine manuelle Migration oder eine Livemigration durchführen.

Durch die Änderung des Replikationstyps für Ihr Speicherkonto ergeben sich keine Ausfallzeiten für Ihre Anwendungen.

# <a name="portal"></a>[Portal](#tab/portal)

Um die Redundanzoption für Ihr Speicherkonto im Azure-Portal zu ändern, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie unter **Einstellungen** die Option **Konfiguration**.
1. Aktualisieren Sie die Einstellung **Replikation**.

    :::image type="content" source="media/redundancy-migration/change-replication-option.png" alt-text="Screenshot, der anzeigt, wie Sie die Replikationsoption im Portal ändern." lightbox="media/redundancy-migration/change-replication-option.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um die Redundanzoption für Ihr Speicherkonto mit PowerShell zu ändern, führen Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aus, und geben Sie den Parameter `-SkuName` an:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Redundanzoption für Ihr Speicherkonto mit der Azure CLI zu ändern, führen Sie den Befehl [az storage account update](/cli/azure/storage/account#az_storage_account_update) aus, und geben Sie den Parameter `--sku` an:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Ausführen einer manuellen Migration zu ZRS, GZRS oder RA-GZRS

Wenn Sie durch Wechseln von LRS zu ZRS oder umgekehrt die Art und Weise ändern möchten, in der Daten in Ihrem Speicherkonto in der primären Region repliziert werden, können Sie eine manuelle Migration ausführen. Eine manuelle Migration bietet mehr Flexibilität als eine Livemigration. Bei einer manuellen Migration können Sie den zeitlichen Ablauf steuern. Verwenden Sie diese Option also, wenn die Migration an einem bestimmten Datum abgeschlossen sein soll.

Wenn Sie eine manuelle Migration von LRS zu ZRS oder umgekehrt in der primären Region durchführen, kann das Zielspeicherkonto georedundant und auch für den Lesezugriff auf die sekundäre Region konfiguriert sein. Sie können beispielsweise ein LRS-Konto in einem Schritt zu einem GZRS- oder RA-GZRS-Konto migrieren.

Eine manuelle Migration kann zu Ausfallzeiten der Anwendung führen. Wenn Ihre Anwendung eine hohe Verfügbarkeit erfordert, bietet Microsoft auch eine Option für eine Livemigration. Eine Livemigration ist eine direkte Migration ohne Ausfallzeiten.

Bei einer manuellen Migration kopieren Sie die Daten aus Ihrem vorhandenen Speicherkonto in ein neues Speicherkonto, das ZRS in der primären Region verwendet. Zum Ausführen einer manuellen Migration können Sie eine der folgenden Optionen verwenden:

- Kopieren Sie Daten mithilfe eines vorhandenen Tools wie z. B. AzCopy, einer der Azure Storage-Clientbibliotheken oder zuverlässigen Drittanbietertools.
- Wenn Sie mit Hadoop oder HDInsight vertraut sind, können Sie sowohl das Quell- als auch das Zielspeicherkonto an Ihren Cluster anfügen. Danach parallelisieren Sie den Datenkopiervorgang mit einem Tool wie z.B. DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Anfordern einer Livemigration zu ZRS, GZRS oder RA-GZRS

Wenn Sie Ihr Speicherkonto von LRS zu ZRS in der primären Region migrieren müssen, ohne dass dadurch Ausfallzeiten entstehen, können Sie eine Livemigration bei Microsoft anfordern. Um von LRS zu GZRS oder RA-GZRS zu migrieren, wechseln Sie zunächst zu GRS oder RA-GRS, und fordern Sie dann eine Livemigration an. Auf gleiche Weise können Sie eine Livemigration von GRS oder RA-GRS zu GZRS oder RA-GZRS anfordern. Um von GRS oder RA-GRS zu ZRS zu migrieren, wechseln Sie zunächst zu LRS, und fordern Sie dann eine Livemigration an.

Während einer Livemigration können Sie ohne jegliche Verluste hinsichtlich Dauerhaftigkeit oder Verfügbarkeit auf die Daten in Ihrem Speicherkonto zugreifen. Die Azure Storage-SLA wird während des Migrationsprozesses aufrechterhalten. Bei einer Livemigration treten keine Datenverluste auf. Dienstendpunkte, Zugriffsschlüssel, Shared Access Signatures und andere Kontooptionen sind auch nach der Migration unverändert.

Bei Standardleistung unterstützt ZRS nur Konten vom Typ „Universell V2“. Führen Sie deshalb ein Upgrade für Ihr Speicherkonto durch, wenn es sich um ein Konto vom Typ „Universell V1“ handelt, bevor Sie eine Anforderung für eine Livemigration zu ZRS übermitteln. Weitere Informationen finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md). Ein Speicherkonto muss Daten enthalten, damit eine Livemigration durchgeführt werden kann.

Bei Premiumleistung wird die Livemigration für Premium-Dateifreigabekonten unterstützt, aber nicht für Premium-Blockblob- oder Premium-Seitenblobkonten.

Wenn Ihr Konto RA-GRS verwendet, müssen Sie zuerst den Replikationstyp Ihres Kontos in LRS oder GRS ändern, bevor Sie mit einer Livemigration fortfahren. Dieser Zwischenschritt entfernt den sekundären schreibgeschützten Endpunkt, der von RA-GRS bereitgestellt wird.

Auch wenn Microsoft auf Ihre Anforderung zur Livemigration sofort reagiert, gibt es keine Garantie, zu welchem Zeitpunkt eine Livemigration abgeschlossen sein wird. Wenn Ihre Daten bis zu einem bestimmten Datum zu ZRS migriert werden müssen, empfiehlt Microsoft die Durchführung einer manuellen Migration. Grundsätzlich gilt: Je mehr Daten in Ihrem Konto vorhanden sind, desto länger dauert die Migration dieser Daten.

In folgenden Fällen müssen Sie eine manuelle Migration ausführen:

- Sie möchten Ihre Daten zu einem ZRS-Speicherkonto migrieren, das sich in einer anderen Region als das Quellkonto befindet.
- Ihr Speicherkonto ist ein Seitenblob- oder Blockblobkonto in einem Premium-Tarif.
- Sie möchten Daten von ZRS zu LRS, GRS oder RA-GRS migrieren.
- Ihr Speicherkonto enthält Daten auf Archivebene.

Sie können eine Livemigration über das [Azure-Support-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) anfordern.

> [!IMPORTANT]
> Wenn Sie mehr als ein Speicherkonto migrieren müssen, erstellen Sie ein einzelnes Support-Ticket und geben Sie auf der Registerkarte **Details** die Namen der zu konvertierenden Konten an.

Folgen Sie diesen Schritten, um eine Live-Migration anzufordern:

1. Navigieren Sie im Azure-Portal zu einem Speicherkonto, das Sie migrieren möchten.
1. Wählen Sie unter **Support + Troubleshooting** **Neue Support-Anfrage**.
1. Füllen Sie die Registerkarte **Grundlagen** anhand Ihrer Kontoinformationen aus:
    - **Problemtyp**: Wählen Sie **Technisch** aus.
    - **Dienst**: Wählen Sie **Meine Dienste** und **Speicherkontenverwaltung** aus.
    - **Ressource**: Wählen Sie ein Speicherkonto für die Migration aus. Wenn Sie mehrere Speicher Konten angeben müssen, können Sie dies im Abschnitt **Details** tun.
    - **Problemtyp**: Wählen Sie **Datenmigration** aus.
    - **Problem-Untertyp**: Wählen Sie **zu ZRS, gzrs oder RA-gzrs migrieren** aus.

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Screenshot: Anfordern einer Live Migration: Registerkarte Grundlagen":::

1. Wählen Sie **Weiter** aus. Auf der Registerkarte **Lösungen** können Sie die Berechtigung ihrer Speicherkonten für die Migration überprüfen.
1. Wählen Sie **Weiter** aus. Wenn Sie mehr als ein Speicherkonto migrieren möchten, geben Sie auf der Registerkarte **Details** den Namen jedes Kontos getrennt durch ein Semikolon ein.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Screenshot: Anzeigen einer Live Migration: Registerkarte Grundlagen":::

1. Füllen Sie die zusätzlichen erforderlichen Informationen auf der Registerkarte **Details** aus, und wählen Sie dann **überprüfen + erstellen** aus, um Ihr Supportticket zu prüfen und zu übermitteln. Ein Supportmitarbeiter wird sich mit Ihnen in Verbindung setzen, um Sie nach Bedarf und Wunsch unterstützen.

> [!NOTE]
> Premium-Dateifreigaben sind nur für LRS und ZRS verfügbar.
>
> GZRS-Speicherkonten unterstützen die Archivebene derzeit nicht. Weitere Informationen finden Sie unter [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](../blobs/access-tiers-overview.md).
>
> Verwaltete Datenträger sind nur für LRS verfügbar und können nicht zu ZRS migriert werden. Sie können Momentaufnahmen und Images für verwaltete SSD Standard-Datenträger in einem HDD Standard-Speicher speichern und [zwischen LRS- und ZRS-Optionen wählen](https://azure.microsoft.com/pricing/details/managed-disks/). Informationen zur Integration in Verfügbarkeitsgruppen finden Sie unter [Einführung in verwaltete Azure-Datenträger](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Wechsel von ZRS (klassisch)

> [!IMPORTANT]
> Microsoft wird ZRS Classic-Konten am 31. März 2021 als veraltet markieren und migrieren. Bevor die Option als veraltet gekennzeichnet wird, erhalten ZRS Classic-Kunden weitere Informationen.
>
> Sobald ZRS in einer Region allgemein verfügbar wird, können Kunden über das Azure-Portal in dieser Region keine klassischen ZRS-Konten mehr erstellen. Die Verwendung von Microsoft PowerShell und der Azure CLI zum Erstellen von ZRS Classic-Konten ist eine Option, bis ZRS Classic als veraltet gekennzeichnet wird. Informationen zur Verfügbarkeit von ZRS finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

ZRS Classic repliziert Daten asynchron zwischen Rechenzentren in einer oder zwei Regionen. Replizierte Daten sind möglicherweise erst wieder verfügbar, wenn Microsoft das Failover auf die sekundäre Region initiiert. Ein ZRS Classic-Konto kann nicht zu oder aus LRS, GRS oder RA-GRS konvertiert werden. ZRS Classic-Konten unterstützen zudem weder Metriken noch Protokollierungen.

ZRS Classic ist nur für **Blockblobs** in Speicherkonten vom Typ „Allgemein v1 (GPv1)“ verfügbar. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](storage-account-overview.md).

Um ZRS-Kontodaten manuell zu oder von einem LRS-, GRS-, RA-GRS- oder klassischen ZRS-Konto zu migrieren, verwenden Sie eins der folgenden Tools: AzCopy, Azure Storage-Explorer, PowerShell oder Azure CLI. Sie können auch Ihre eigene Migrationslösung mit einer der Azure Storage-Clientbibliotheken erstellen.

Sie können in den Regionen, in denen ZRS verfügbar ist, auch ein Upgrade Ihres klassischen ZRS-Speicherkontos auf ZRS durchführen. Verwenden Sie dafür das Azure-Portal, PowerShell oder die Azure CLI.

# <a name="portal"></a>[Portal](#tab/portal)

Um im Azure-Portal ein Upgrade auf ZRS durchzuführen, navigieren Sie zu den **Konfigurationseinstellungen** des Kontos, und wählen Sie **Upgrade** aus:

![Upgrade von ZRS (klassisch) zu ZRS im Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie folgenden Befehl aus, um mithilfe von PowerShell ein Upgrade auf ZRS durchzuführen:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie folgenden Befehl aus, um mithilfe der Azure CLI ein Upgrade auf ZRS durchzuführen:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kosten in Zusammenhang mit einer Änderung der Datenreplikation

Die Kosten für eine Änderung der Datenreplikation richten sich nach der Quell- und der Zieloption für den Wechsel. Die Reihenfolge der Azure Storage-Redundanzangebote vom kostengünstigsten bis zum kostenintensivsten: LRS, ZRS, GRS, RA-GRS, GZRS und RA-GZRS.

Beispielsweise fallen beim Wechsel *von* LRS zu einem beliebigen anderen Replikationstyp zusätzliche Kosten an, da Sie in eine komplexere Redundanzebene wechseln. Bei der Migration *zu* GRS oder RA-GRS wird zum Zeitpunkt der Migration eine Gebühr für ausgehende Bandbreite berechnet, da Ihr gesamtes Speicherkonto in die sekundäre Region repliziert wird. Für alle nachfolgenden Schreibvorgänge in die primäre Region entstehen ebenfalls Gebühren für ausgehende Bandbreite, um den Schreibzugriff in die sekundäre Region zu replizieren. Ausführliche Informationen zu Bandbreitengebühren finden Sie auf der [Seite mit Informationen zu Azure Storage-Preisen](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Sie Ihr Speicherkonto von GRS zu LRS migrieren, entstehen keine zusätzlichen Kosten, aber Ihre replizierten Daten werden am sekundären Standort gelöscht.

> [!IMPORTANT]
> Wenn Sie Ihr Speicherkonto von RA-GRS zu GRS oder LRS migrieren, wird das Konto nach dem Umstellungsdatum für weitere 30 Tage als RA-GRS abgerechnet.

## <a name="see-also"></a>Weitere Informationen

- [Azure-Speicherredundanz](storage-redundancy.md)
- [Überprüfen der Eigenschaft „Zeitpunkt der letzten Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md)
- [Verwenden von Georedundanz zum Entwerfen von hochverfügbaren Anwendungen](geo-redundant-design.md)

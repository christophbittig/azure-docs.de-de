---
title: Erstellen einer Azure HPC Cache-Instanz
description: Hier erfahren Sie, wie Sie eine Azure HPC Cache-Instanz erstellen.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: femila
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6f457dcf69b0f38173a3816ea1ac16c2cc7c5935
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015331"
---
# <a name="create-an-azure-hpc-cache"></a>Erstellen einer Azure HPC Cache-Instanz

Verwenden Sie das Azure-Portal oder die Azure CLI, um Ihren Cache zu erstellen.

![Screenshot: Cacheübersicht im Azure-Portal mit der Schaltfläche „Erstellen“ im unteren Bereich](media/hpc-cache-home-page.png)

Klicken Sie auf das Bild unten, um eine [Videodemonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) der Cacheerstellung und des Hinzufügens eines Speicherziels anzusehen.

[![Videominiaturansicht: Azure HPC Cache: Setup (klicken Sie, um die Videoseite aufzurufen)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definieren grundlegender Informationen

![Screenshot: Projektdetailseite im Azure-Portal.](media/hpc-cache-create-basics.png)

Wählen Sie auf der Seite **Projektdetails** das Abonnement und die Ressourcengruppe zum Hosten des Caches aus.

Legen Sie unter **Dienstdetails** den Cachenamen sowie folgende weitere Attribute fest:

* Standort: Wählen Sie eine der [unterstützten Regionen](hpc-cache-overview.md#region-availability) aus.
* Virtuelles Netzwerk: Sie können ein bereits vorhandenes virtuelles Netzwerk auswählen oder ein neues erstellen.
* Subnetz: Wählen Sie ein Subnetz mit mindestens 64 IP-Adressen (/24) aus, oder erstellen Sie es. Dieses Subnetz darf nur für diese Azure HPC Cache-Instanz verwendet werden.

## <a name="set-cache-capacity"></a>Festlegen der Cachekapazität
<!-- referenced from GUI - update aka.ms/hpc-cache-iops link if you change this header text -->

Legen Sie auf der Seite **Cache** die Kapazität Ihres Caches fest. Die hier festgelegten Werte bestimmen, wie schnell Ihr Cache Clientanforderungen erfüllen kann und wie viele Daten er speichern kann.

Die Kapazität wirkt sich auch auf die Kosten für den Cache und darauf aus, wie viele Speicherziele unterstützt werden können.

Cachekapazität ist eine Kombination aus zwei Werten:

* Die maximale Datenübertragungsrate für den Cache (Durchsatz) in GB/Sekunde
* Die zugeordnete Speichermenge für zwischengespeicherte Daten in TB

![Screenshot: Seite zum Festlegen der Cachegröße im Azure-Portal.](media/hpc-cache-create-capacity.png)

### <a name="understand-throughput-and-cache-size"></a>Grundlegendes zu Durchsatz und Cachegröße

Mehrere Faktoren können die Effizienz Ihres HPC-Caches beeinflussen, aber die Wahl eines geeigneten Durchsatzwerts und der Größe des Cachespeichers ist einer der wichtigsten.

Beachten Sie bei der Wahl eines Durchsatzwerts, dass die tatsächliche Datenübertragungsrate von der Workload, Netzwerkgeschwindigkeit und Art der Speicherziele abhängt.

Die von Ihnen ausgewählten Werte bestimmen den maximalen Durchsatz für das gesamte Cachesystem, ein Teil davon wird jedoch für zusätzliche Aufgaben verwendet. Wenn ein Client beispielsweise eine Datei anfordert, die noch nicht im Cache gespeichert ist, oder wenn die Datei als veraltet markiert ist, nutzt der Cache einen Teil des Durchsatzes, um sie aus dem Back-End-Speicher abzurufen.

Mit Azure HPC Cache wird verwaltet, welche Dateien zwischengespeichert und vorab geladen werden, um Cachetrefferquoten zu maximieren. Cacheinhalte werden kontinuierlich bewertet, und seltener verwendete Dateien werden in den langfristigen Speicher verschoben.

Wählen Sie eine Cachespeichergröße aus, die problemlos den aktiven Satz von Arbeitsdateien aufnehmen kann sowie Speicherplatz für Metadaten und andere Zusatzdaten bietet.

Durchsatz und Cachegröße beeinflussen auch, wie viele Speicherziele für einen bestimmten Cache unterstützt werden. Wenn Sie mehr als 10 Speicherziele mit Ihrem Cache verwenden möchten, müssen Sie den höchsten verfügbaren Wert für die Cachespeichergröße wählen, der für Ihre Durchsatzgröße verfügbar ist, oder eine der schreibgeschützten Konfigurationen mit hohem Durchsatz wählen. Weitere Informationen finden Sie unter [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets).

Wenn Sie Hilfe beim richtigen Dimensionieren Ihres Caches benötigen, wenden Sie sich an den Microsoft-Support.

### <a name="choose-the-cache-type-for-your-needs"></a>Wählen des Cachetyps für Ihre Anforderungen

Bei der Wahl der Cachekapazität werden Sie feststellen, dass einige Durchsatzwerte feste Cachegrößen haben, während Sie bei anderen aus mehreren Optionen für die Cachegröße auswählen können. Dies liegt daran, dass es zwei verschiedene Stile von Cacheinfrastruktur gibt:

* Standardcaches: im Menü „Durchsatz“ unter **Zwischenspeichern von Lese- und Schreibvorgängen** aufgeführt

  Bei Standardcaches können Sie aus mehreren Cachegrößenwerten auswählen. Diese Caches können schreibgeschützt oder für das Zwischenspeichern von Lese- und Schreibvorgängen konfiguriert werden.

* Caches mit hohem Durchsatz: im Menü „Durchsatz“ unter **Schreibgeschütztes Zwischenspeichern** aufgeführt

  Die Konfigurationen mit hohem Durchsatz haben festgelegte Cachegrößen, da sie mit NVME-Datenträgern vorkonfiguriert sind. Sie wurden entwickelt, um nur den Lesezugriff auf Dateien zu optimieren.

![Screenshot des Menüs „Maximaler Durchsatz“ im Portal. Es gibt mehrere Größenoptionen unter der Überschrift „Zwischenspeichern von Lese- und Schreibvorgängen“ und mehrere unter der Überschrift „Schreibgeschützt“.](media/rw-ro-cache-sizing.png)

In dieser Tabelle werden einige wichtige Unterschiede zwischen den beiden Optionen erläutert.

| attribute | Cachetarif „Standard“ | Cache mit hohem Durchsatz |
|--|--|--|
| Menükategorie „Durchsatz“ |„Zwischenspeichern von Lese- und Schreibvorgängen“| „Schreibgeschütztes Zwischenspeichern“|
| Durchsatzgrößen | 2, 4 oder 8 GB/s | 4,5, 9 oder 16 GB/s |
| Cachegrößen | 3, 6 oder 12 TB für 2 GB/s<br/> 6, 12 oder 24 TB für 4 GB/s<br/> 12, 24 oder 48 TB für 8 GB/s| 21 TB für 4,5 GB/s <br/> 42 TB für 9 GB/s <br/> 84 TB für 16 GB/s |
| Maximale Anzahl von Speicherzielen | [10 oder 20](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets) je nach Auswahl der Cachegröße | 20 |
| Kompatible Speicherzieltypen | Azure-Blob, lokaler NFS-Speicher, NFS-fähiges Blob | Lokaler NFS-Speicher <br/>NFS-fähiger Blobspeicher befindet sich für diese Kombination in der Vorschau |
| Zwischenspeicherstile | Zwischenspeichern von Lese- oder Lese-/Schreibvorgängen | Nur Zwischenspeicherung von Lesevorgängen |
| Der Cache kann beendet werden, um Kosten zu sparen, wenn er nicht benötigt wird | Ja | Nein |

Weitere Informationen zu diesen Optionen:

* [Maximale Anzahl von Speicherzielen](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)
* [Zwischenspeichermodi für Lese- und Schreibvorgänge](cache-usage-models.md#basic-file-caching-concepts)

## <a name="enable-azure-key-vault-encryption-optional"></a>Aktivieren der Azure Key Vault-Verschlüsselung (optional)

Wenn Sie die für Ihren Cachespeicher verwendeten Verschlüsselungsschlüssel verwalten möchten, geben Sie die Azure Key Vault-Informationen auf der Seite **Datenträgerverschlüsselungsschlüssel** an. Der Schlüsseltresor muss sich in derselben Region und im selben Abonnement befinden wie der Cache.

Sie können diesen Abschnitt überspringen, wenn Sie keine kundenseitig verwalteten Schlüssel benötigen. Standardmäßig verschlüsselt Azure Daten mit von Microsoft verwalteten Schlüsseln. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung](../storage/common/storage-service-encryption.md).

> [!NOTE]
> Nach dem Erstellen des Caches können Sie nicht mehr zwischen von Microsoft und von Kunden verwalteten Schlüsseln wechseln.

Eine ausführliche Erläuterung des Verschlüsselungsverfahrens mit kundenseitig verwalteten Schlüsseln finden Sie unter [Verwenden von kundenseitig verwalteten Verschlüsselungsschlüsseln für Azure HPC Cache](customer-keys.md).

![Screenshot der Seite „Verschlüsselungsschlüssel“ mit ausgewählter Option „Kundenseitig verwaltet“ und den Konfigurationsformularen „Kundenschlüsseleinstellungen“ und „Verwaltete Identitäten“.](media/create-encryption.png)

Wählen Sie **Vom Kunden verwaltet** aus, um die Verschlüsselung mit kundenseitig verwalteten Schlüsseln auszuwählen. Die Felder für die Angabe des Schlüsseltresors werden angezeigt. Wählen Sie die zu verwendende Azure Key Vault-Instanz und dann den Schlüssel und die Version für diesen Cache aus. Bei dem Schlüssel muss es sich um einen 2.048-Bit-RSA-Schlüssel handeln. Auf dieser Seite können Sie einen Schlüsseltresor, einen Schlüssel oder eine Schlüsselversion neu erstellen.

Aktivieren Sie das Kontrollkästchen **Immer aktuelle Schlüsselversion verwenden**, wenn Sie [automatische Schlüsselrotation](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys) wünschen.

Wenn Sie eine bestimmte verwaltete Identität für diesen Cache verwenden möchten, konfigurieren Sie sie im Abschnitt **Verwaltete Identitäten**. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Nach Erstellung des Caches können Sie die zugewiesene Identität nicht mehr ändern.

Wenn Sie eine systemseitig zugewiesene oder vom Benutzer zugewiesene Identität verwenden, die noch keinen Zugriff auf Ihren Schlüsseltresor hat, müssen Sie nach Erstellen des Caches einen zusätzlichen Schritt ausführen. In diesem manuellen Schritt wird die verwaltete Identität des Caches für die Verwendung des Schlüsseltresors autorisiert.

* Lesen Sie [Auswählen einer Option für verwaltete Identitäten für den Cache](customer-keys.md#choose-a-managed-identity-option-for-the-cache), um die Unterschiede in den Einstellungen der verwalteten Identität zu verstehen.
* Weitere Informationen finden Sie unter [Autorisieren der Verschlüsselung mit Azure Key Vault aus dem Cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed).

## <a name="add-resource-tags-optional"></a>Hinzufügen von Ressourcentags (optional)

Auf der Seite **Tags** können Sie Ihrer Azure HPC Cache-Instanz [Ressourcentags](../azure-resource-manager/management/tag-resources.md) hinzufügen.

## <a name="finish-creating-the-cache"></a>Fertigstellen der Cacheerstellung

Nachdem Sie den neuen Cache konfiguriert haben, klicken Sie auf die Registerkarte **Überprüfen + erstellen**. Das Portal überprüft Ihre Auswahl und ermöglicht es Ihnen, die gewählten Optionen selbst noch einmal zu überprüfen. Ist alles korrekt, klicken Sie auf **Erstellen**.

Die Cacheerstellung dauert ungefähr zehn Minuten. Sie können den Status über den Benachrichtigungsbereich des Azure-Portals nachverfolgen.

![Screenshot der Cache-Erstellungsseiten „Bereitstellung wird ausgeführt“ und „Benachrichtigungen“ im Portal](media/hpc-cache-deploy-status.png)

Wenn die Erstellung abgeschlossen ist, wird eine Benachrichtigung mit einem Link zur neuen Azure HPC Cache-Instanz angezeigt, und der Cache wird in der Liste **Ressourcen** Ihres Abonnements angezeigt.

![Screenshot: Azure HPC Cache-Instanz im Azure-Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Wenn für Ihren Cache kundenseitig verwaltete Verschlüsselungsschlüssel verwendet werden und nach der Erstellung ein manuelle Autorisierungsschritt erforderlich ist, wird der Cache möglicherweise in der Ressourcenliste aufgeführt, bevor der Bereitstellungsstatus in „Fertig“ geändert wird. Sobald der Status des Caches **Warten auf Schlüssel** lautet, können Sie ihn für die Verwendung des Schlüsseltresors [autorisieren](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed).

## <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Erstellen des Caches mit der Azure CLI

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

> [!NOTE]
> Die Azure CLI unterstützt derzeit die Erstellung eines Caches mit kundenseitig verwalteten Verschlüsselungsschlüsseln nicht. Verwenden Sie dafür das Azure-Portal.

Verwenden Sie den Befehl [az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create), um einen neuen Azure HPC Cache zu erstellen.

Geben Sie die folgenden Werte an:

* Name der Cacheressourcengruppe
* Cachename
* Azure-Region
* Cachesubnetz im folgenden Format:

  `--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  Das Cachesubnetz benötigt mindestens 64 IP-Adressen (/24) und kann keine anderen Ressourcen hosten.

* Cachekapazität: zwei Werte legen den maximalen Durchsatz Ihrer Azure HPC Cache-Instanz fest:

  * Die Cachegröße (in GB)
  * Die SKU der in der Cache-Infrastruktur verwendeten VMs

  [az hpc-cache skus list](/cli/azure/hpc-cache/skus) zeigt die verfügbaren SKUs und die gültigen Optionen für die Cachegröße für jede SKU an. Die Optionen für die Cachegröße reichen von 3 TB bis 48 TB, es werden aber nur einige Werte unterstützt.

  Dieses Diagramm zeigt die gültigen Kombinationen aus Cachegröße und SKU zum Zeitpunkt der Erstellung dieses Dokuments (Juli 2020).

  | Cachegröße | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3\.072 GB    | ja         | Nein          | nein          |
  | 6144 GB    | ja         | Ja         | nein          |
  | 12288 GB   | ja         | Ja         | ja         |
  | 24576 GB   | nein          | Ja         | ja         |
  | 49152 GB   | nein          | Nein          | ja         |

  Wenn Sie mehr als 10 Speicherziele mit Ihrem Cache verwenden möchten, wählen Sie den höchsten verfügbaren Wert für die Cachegröße für Ihre SKU aus. Diese Konfigurationen unterstützen bis zu 20 Speicherziele.

  Lesen Sie den Abschnitt **Cachekapazität festlegen** auf der Portalregisterkarte mit Anweisungen, um wichtige Informationen zu den Preisen, dem Durchsatz und der richtigen Dimensionierung des Caches für Ihren Workflow zu erhalten.

Beispiel für die Cacheerstellung:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Die Erstellung des Caches dauert einige Minuten. Bei erfolgreicher Ausführung gibt der create-Befehl eine Ausgabe ähnlich der folgenden zurück:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Die Meldung enthält einige nützliche Informationen, u. a. folgende:

* Adressen zur Clienteinbindung: Verwenden Sie diese Adressen, wenn Sie Clients mit dem Cache verbinden möchten. Weitere Informationen finden Sie unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).
* Upgradestatus: Wenn ein Softwareupdate veröffentlicht wird, ändert sich diese Meldung. Sie können ein [Upgrade der Cachesoftware](hpc-cache-manage.md#upgrade-cache-software) manuell zu einem von Ihnen gewünschten Zeitpunkt durchführen. Andernfalls wird das Upgrade nach einigen Tagen automatisch angewendet.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Das PowerShell-Modul „Az.HPCCache“ ist derzeit als öffentliche Vorschauversion verfügbar. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Funktionen werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requirements (Anforderungen)

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps). Wenn Sie sich für die Verwendung von Cloud Shell entscheiden, finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md) weitere Informationen.

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.HPCCache** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Erstellen des Caches mit Azure PowerShell

> [!NOTE]
> Azure PowerShell unterstützt derzeit die Erstellung eines Caches mit kundenseitig verwalteten Verschlüsselungsschlüsseln nicht. Verwenden Sie dafür das Azure-Portal.

Verwenden Sie das Cmdlet [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache), um einen neuen Azure HPC Cache zu erstellen.

Geben Sie die folgenden Werte an:

* Name der Cacheressourcengruppe
* Cachename
* Azure-Region
* Cachesubnetz im folgenden Format:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  Das Cachesubnetz benötigt mindestens 64 IP-Adressen (/24) und kann keine anderen Ressourcen hosten.

* Cachekapazität: zwei Werte legen den maximalen Durchsatz Ihrer Azure HPC Cache-Instanz fest:

  * Die Cachegröße (in GB)
  * Die SKU der in der Cache-Infrastruktur verwendeten VMs

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) zeigt die verfügbaren SKUs und die gültigen Optionen für die Cachegröße für jede SKU an. Die Optionen für die Cachegröße reichen von 3 TB bis 48 TB, es werden aber nur einige Werte unterstützt.

  Dieses Diagramm zeigt die gültigen Kombinationen aus Cachegröße und SKU zum Zeitpunkt der Erstellung dieses Dokuments (Juli 2020).

  | Cachegröße | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3\.072 GB    | ja         | Nein          | nein          |
  | 6144 GB    | ja         | Ja         | nein          |
  | 12.288 GB   | ja         | Ja         | ja         |
  | 24.576 GB   | nein          | Ja         | ja         |
  | 49.152 GB   | nein          | Nein          | ja         |

  Lesen Sie den Abschnitt **Cachekapazität festlegen** auf der Portalregisterkarte mit Anweisungen, um wichtige Informationen zu den Preisen, dem Durchsatz und der richtigen Dimensionierung des Caches für Ihren Workflow zu erhalten.

Beispiel für die Cacheerstellung:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Die Erstellung des Caches dauert einige Minuten. Bei erfolgreicher Ausführung gibt der create-Befehl die folgende Ausgabe zurück:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Die Meldung enthält einige nützliche Informationen, u. a. folgende:

* Adressen zur Clienteinbindung: Verwenden Sie diese Adressen, wenn Sie Clients mit dem Cache verbinden möchten. Weitere Informationen finden Sie unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).
* Upgradestatus: Wenn ein Softwareupdate veröffentlicht wird, ändert sich diese Meldung. Sie können ein [Upgrade der Cachesoftware](hpc-cache-manage.md#upgrade-cache-software) manuell zu einem von Ihnen gewünschten Zeitpunkt durchführen. Andernfalls wird das Upgrade nach einigen Tagen automatisch angewendet.

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem der Cache in der Liste **Ressourcen** aufgeführt wird, können Sie mit dem nächsten Schritt fortfahren.

* [Definieren Sie Speicherziele](hpc-cache-add-storage.md), um dem Cache Zugriff auf Ihre Datenquellen zu gewähren.
* Wenn Sie kundenseitig verwaltete Verschlüsselungsschlüssel verwenden und die [Azure Key Vault-Verschlüsselung](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed) auf der Übersichtsseite des Caches autorisieren müssen, um die Einrichtung des Caches abzuschließen, folgen Sie den Anweisungen unter [Verwenden von kundenseitig verwalteten Verschlüsselungsschlüsseln](customer-keys.md). Diesen Schritt müssen Sie ausführen, damit Sie anschließend Speicher hinzufügen können.

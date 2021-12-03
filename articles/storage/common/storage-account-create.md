---
title: Speicherkonto erstellen
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie ein Speicherkonto zum Speichern von Blobs, Dateien, Warteschlangen und Tabellen erstellen. Ein Azure Storage-Konto stellt einen eindeutigen Namespace in Microsoft Azure zum Lesen und Schreiben Ihre Daten bereit.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/18/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3be79a2af3dfb05b289a0111c9e1f9fcf17bab02
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555544"
---
# <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ein Azure Storage-Konto enthält all Ihre Azure Storage-Datenobjekte: Blobs, Dateien, Warteschlangen und Tabellen. Das Speicherkonto stellt einen eindeutigen Namespace für Ihre Azure Storage-Daten bereit, auf den von jedem Ort der Welt aus über HTTP oder HTTPS zugegriffen werden kann. Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Speicherkonto – Übersicht](storage-account-overview.md).

In diesem Artikel erfahren Sie, wie Sie über das [Azure-Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), die [Azure-Befehlszeilenschnittstelle](/cli/azure) oder eine [Azure Resource Manager-Vorlage](../../azure-resource-manager/management/overview.md) ein Speicherkonto erstellen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Keine.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie ein Azure-Speicherkonto mit PowerShell erstellen möchten, stellen Sie sicher, dass Sie das [Az PowerShell-Modul](https://www.powershellgallery.com/packages/Az) Az 0.7 oder höher installiert haben. Weitere Informationen finden Sie unter [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

Führen Sie den folgenden Befehl aus, um Ihre aktuelle Version zu ermitteln:

```powershell
Get-InstalledModule -Name "Az"
```

Informationen zur Installation oder zum Upgrade von Azure PowerShell finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können sich bei Azure anmelden und Azure-CLI-Befehle ausführen. Dazu haben Sie zwei Möglichkeiten:

- Sie können CLI-Befehle innerhalb des Azure-Portals in Azure Cloud Shell ausführen.
- Sie können die Befehlszeilenschnittstelle installieren und CLI-Befehle lokal ausführen.

### <a name="use-azure-cloud-shell"></a>Verwenden von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure-Befehlszeilenschnittstelle ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie im Azure-Portal im Menü im rechten oberen Bereich auf die Schaltfläche **Cloud Shell**:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Die Schaltfläche öffnet eine interaktive Shell, mit der Sie die in diesem Artikel beschriebenen Schritte ausführen können:

[![Screenshot des Fensters „Cloud Shell“ im Portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Lokales Installieren der Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle auch lokal installieren und verwenden. Die Beispiele in diesem Artikel erfordern Version 2.0.4 oder höher der Azure-Befehlszeilenschnittstelle. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Vorlage](#tab/template)

Keine.

---

Als nächstes melden Sie sich bei Azure an.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az_login) aus:

```azurecli-interactive
az login
```

# <a name="template"></a>[Vorlage](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ein Speicherkonto ist eine Azure Resource Manager Ressource. Resource Manager ist der Bereitstellungs- und Verwaltungsdienst für Azure. Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Jede Resource Manager-Ressource, einschließlich eines Azure-Speicherkontos, muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. In dieser Anleitung wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um ein Speicherkonto im Azure-Portal zu erstellen:

1. Wählen Sie im Menü des linken Portals **Speicherkonten** aus, um eine Liste Ihrer Speicherkonten anzuzeigen.
1. Wählen Sie auf der Seite **Speicherkonten** die Option **Erstellen** aus.

Die Optionen für Ihr neues Speicherkonto sind auf der Seite **Speicherkonto erstellen** auf Registerkarten organisiert. In den folgenden Abschnitten werden die einzelnen Registerkarten und ihre Optionen beschrieben.

### <a name="basics-tab"></a>Registerkarte „Grundeinstellungen“

Geben Sie auf der Registerkarte **Grundlagen** die grundlegenden Informationen für Ihr Speicherkonto an. Nachdem Sie die Registerkarte **Grundlagen** abgeschlossen haben, können Sie Ihr neues Speicherkonto weiter anpassen, indem Sie Optionen auf den anderen Registerkarten festlegen, oder Sie können **Überprüfen + erstellen** auswählen, um die Standardoptionen zu übernehmen und mit der Überprüfung und Erstellung des Kontos fortzufahren.

In der folgenden Tabelle werden die Felder der Registerkarte **Grundlagen** beschrieben.

| `Section` | Feld | Erforderlich oder optional | Beschreibung |
|--|--|--|--|
| Projektdetails | Subscription | Erforderlich | Wählen Sie das Abonnement für das neue Speicherkonto aus. |
| Projektdetails | Ressourcengruppe | Erforderlich | Erstellen Sie eine neue Ressourcengruppe für dieses Speicherkonto oder wählen Sie eine vorhandene Ressourcengruppe aus. Weitere Informationen finden Sie unter [Ressourcengruppen](../../azure-resource-manager/management/overview.md#resource-groups). |
| Instanzendetails | Speicherkontoname | Erforderlich | Wählen Sie einen eindeutigen Namen für Ihr Speicherkonto aus. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. |
| Instanzendetails | Region | Erforderlich | Wählen Sie die passende Region für Ihr Speicherkonto aus. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](../../availability-zones/az-overview.md).<br /><br />Nicht alle Regionen werden für alle Arten von Speicherkonten oder Redundanzkonfigurationen unterstützt. Weitere Informationen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).<br /><br />Die Wahl der Region kann sich auf die Abrechnung auswirken. Weitere Informationen finden Sie unter [Speicherkontoabrechnung](storage-account-overview.md#storage-account-billing). |
| Instanzendetails | Leistung | Erforderlich | Wählen Sie **Standard** performance for general-purpose v2 storage accounts (default) (Standardleistung für Speicherkonten vom Standardversions-V2-Speicherkonto (Standard)) aus. Diese Art von Konto wird von Microsoft für die meisten Szenarien empfohlen. Weitere Informationen finden Sie unter [Speicherkontentypen](storage-account-overview.md#types-of-storage-accounts).<br /><br />Wählen Sie **Premium** für Szenarien mit geringer Latenz aus. Wählen Sie nach dem Auswählen von **Premium** den Typ des zu erstellenden Premium-Speicherkontos aus. Die folgenden Arten von Premium-Speicherkonten sind verfügbar: <ul><li>[Blockblobs](./storage-account-overview.md)</li><li>[Dateifreigaben](../files/storage-files-planning.md#management-concepts)</li><li>[Seitenblobs](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Instanzendetails | Redundanz | Erforderlich | Wählen Sie die gewünschte Redundanzkonfiguration aus. Nicht alle Redundanzoptionen sind für alle Arten von Speicherkonten in allen Regionen verfügbar. Weitere Informationen zu Redundanzkonfigurationen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).<br /><br />Wenn Sie eine georedundante Konfiguration (GRS oder GZRS) auswählen, werden Ihre Daten in ein Rechenzentrum in einer anderen Region repliziert. Wählen Sie für den Lesezugriff auf Daten in der sekundären Region die Option **Lesezugriff auf Daten verfügbar machen** aus, wenn die regionale Nichtverfügbarkeit nicht möglich ist. |

Die folgende Abbildung zeigt eine Standardkonfiguration für ein neues Speicherkonto.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Screenshot der Standardkonfiguration für ein neues Speicherkonto – Registerkarte „Grundlagen“":::

### <a name="advanced-tab"></a>Registerkarte „Erweitert“

Auf der Registerkarte **Erweitert** können Sie zusätzliche Optionen konfigurieren und die Standardeinstellungen für Ihr neues Speicherkonto ändern. Einige dieser Optionen können auch konfiguriert werden, nachdem das Speicherkonto erstellt wurde, während andere zum Zeitpunkt der Erstellung konfiguriert werden müssen.

In der folgenden Tabelle werden die Felder der Registerkarte **Erweitert** beschrieben.

| `Section` | Feld | Erforderlich oder optional | BESCHREIBUNG |
|--|--|--|--|
| Sicherheit | Enable secure transfer (Sichere Übertragung aktivieren) | Optional | Aktivieren Sie die sichere Übertragung, um zu fordern, dass eingehende Anforderungen an dieses Speicherkonto nur über HTTPS (Standard) gesendet werden. Empfohlen für optimale Sicherheit. Weitere Informationen finden Sie unter [Erzwingen einer sicheren Übertragung für sichere Verbindungen](storage-require-secure-transfer.md). |
| Sicherheit | Infrastrukturverschlüsselung aktivieren | Optional | Die Infrastrukturverschlüsselung ist standardmäßig nicht aktiviert. Aktivieren Sie die Infrastrukturverschlüsselung, um Ihre Daten sowohl auf Dienstebene als auch auf Infrastrukturebene zu verschlüsseln. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos mit aktivierter Infrastrukturverschlüsselung für die doppelte Datenverschlüsselung](infrastructure-encryption-enable.md). |
| Sicherheit | Enable blob public access (Öffentlichen Blobzugriff aktivieren) | Optional | Wenn diese Einstellung aktiviert ist, kann ein Benutzer mit den entsprechenden Berechtigungen anonymen öffentlichen Zugriff auf einen Container im Speicherkonto aktivieren (Standard). Wenn Sie diese Einstellung deaktivieren, wird der anonyme öffentliche Zugriff auf das Speicherkonto verhindert. Weitere Informationen finden Sie unter [Verhindern des anonymem, öffentlichen Lesezugriffs auf Container und Blobs](../blobs/anonymous-read-access-prevent.md).<br> <br> Die Aktivierung des öffentlichen Zugriffs macht Blobdaten nicht verfügbar, sofern der Benutzer nicht in einem zusätzlichen Schritt explizit die Containereinstellung für den öffentlichen Zugriff konfiguriert. |
| Sicherheit | Zugriff auf Speicherkontoschlüssel aktivieren (Vorschau) | Optional | Wenn diese Einstellung aktiviert ist, können Clients Anforderungen an das Speicherkonto entweder mit den Kontozugriffsschlüsseln oder einem Azure Active Directory-Konto (Azure AD) autorisieren (Standard). Wenn Sie diese Einstellung deaktivieren, wird die Autorisierung mit den Kontozugriffsschlüsseln verhindert. Weitere Informationen finden Sie unter [Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto](shared-key-authorization-prevent.md). |
| Sicherheit | TLS-Mindestversion | Erforderlich | Wählen Sie die erforderliche Mindestversion der Transport Layer Security (TLS) für eingehende Anforderungen an ein Speicherkonto. Der Standardwert ist TLS-Version 1.2. Wenn dieser Wert auf den Standardwert festgelegt ist, werden eingehende Anforderungen mit TLS 1.0 oder TLS 1.1 abgelehnt. Weitere Informationen finden Sie unter [Erzwingen der erforderliche Mindestversion der Transport Layer Security (TLS) für Anforderungen an ein Speicherkonto](transport-layer-security-configure-minimum-version.md). |
| Data Lake Storage Gen2 | Aktivieren Sie hierarchische Namespace | Optional | Wenn Sie dieses Speicherkonto für Azure Data Lake Storage Gen2 verwenden möchten, müssen Sie einen hierarchischen Namespace konfigurieren. Weitere Informationen hierzu finden Sie unter [Einführung in Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Sicheres Dateiübertragungsprotokoll (SFTP) | Aktivieren von SFTP | Optional | Aktivieren Sie die Verwendung von Secure File Transfer Protocol (SFTP), um Daten sicher über das Internet zu übertragen. Weitere Informationen finden Sie unter [Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage](../blobs/secure-file-transfer-protocol-support.md). |
| Blobspeicher | Aktivieren der Netzwerkdateifreigabe v3 (NFS) | Optional | NFS v3 bietet Linux-Dateisystemkompatibilität bei Objektspeicherskalierung und ermöglicht es Linux-Clients, einen Container aus einem virtuellen Azure-Computer (VM) oder einem lokalen Computer in Blob Storage einzubinden. Weitere Informationen finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](../blobs/network-file-system-protocol-support.md). |
| Blobspeicher | Zugriffsebene | Erforderlich | Mit Blobzugriffsebenen können Sie Blobdaten basierend auf der Nutzung auf die kostengünstigste Weise speichern. Wählen Sie die heiße Zugriffsebene (Standard) für Daten aus, auf die häufig zugegriffen wird. Wählen Sie die kalte Zugriffsebene für Daten aus, auf die selten zugegriffen wird. Weitere Informationen finden Sie unter [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](../blobs/access-tiers-overview.md). |
| Azure Files | Aktivieren großer Dateifreigaben | Optional | Nur für Standarddateifreigaben mit LRS- oder ZRS-Redundanzen verfügbar. |
| Tabellen und Warteschlangen | Unterstützung für kundenseitig verwaltete Schlüssel aktivieren | Optional | Um den Support für kundenseitig verwaltete Schlüssel für Tabellen und Warteschlangen zu aktivieren, müssen Sie diese Einstellung zum Zeitpunkt der Erstellung des Speicherkontos auswählen. Weitere Informationen finden Sie unter [Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Tabellen und Warteschlangen unterstützt](account-encryption-key-create.md). |

### <a name="networking-tab"></a>Registerkarte „Netzwerk“

Auf der Registerkarte **Netzwerk** können Sie Einstellungen für Netzwerkkonnektivität und Routingeinstellungen für Ihr neues Speicherkonto konfigurieren. Diese Optionen können auch konfiguriert werden, nachdem das Speicherkonto erstellt wurde.

In der folgenden Tabelle werden die Felder der Registerkarte **Netzwerk** beschrieben.

| `Section` | Feld | Erforderlich oder optional | BESCHREIBUNG |
|--|--|--|--|
| Netzwerkkonnektivität | Konnektivitätsmethode | Erforderlich | Standardmäßig wird der eingehende Netzwerkdatenverkehr an den öffentlichen Endpunkt für Ihr Speicherkonto weitergeleitet. Sie können angeben, dass Datenverkehr über ein virtuelles Azure-Netzwerk an den öffentlichen Endpunkt weitergeleitet werden muss. Sie können auch private Endpunkte für Ihr Speicherkonto konfigurieren. Weitere Informationen finden Sie unter [Verwenden privater Endpunkte für Azure Storage](storage-private-endpoints.md). |
| Netzwerkrouting | Routingpräferenz | Erforderlich | Die Netzwerkroutingpräferenz legt fest, wie Netzwerkdatenverkehr von Clients über das Internet zum öffentlichen Endpunkt Ihres Speicherkontos geleitet wird. Standardmäßig verwendet ein neues Speicherkonto das Microsoft-Netzwerkrouting. Sie können auch Netzwerkdatenverkehr über den POP weiterleiten, der dem Speicherkonto am nächsten liegt, was die Netzwerkkosten senken kann. Weitere Informationen dazu finden Sie unter [Netzwerkroutingpräferenz für Azure Storage](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Registerkarte „Datenschutz“

Auf der Registerkarte **Datenschutz** können Sie Datenschutzoptionen für Blobdaten in Ihrem neuen Speicherkonto konfigurieren.  Diese Optionen können auch konfiguriert werden, nachdem das Speicherkonto erstellt wurde. Eine Übersicht über die Datenschutzoptionen in Azure Storage finden Sie unter [Übersicht über den Datenschutz.](../blobs/data-protection-overview.md)

In der folgenden Tabelle werden die Felder auf der Registerkarte **Datenschutz** beschrieben.

| `Section` | Feld | Erforderlich oder optional | Beschreibung |
|--|--|--|--|
| Wiederherstellung | Zeitpunktwiederherstellung für Container aktivieren | Optional | Point-in-Time-Wiederherstellung bietet Schutz vor versehentlichem Löschen oder Beschädigungen, da Sie Blockblobdaten in einem früheren Zustand wiederherstellen können. Weitere Informationen finden Sie unter [Zeitpunktwiederherstellung – Blockblobs](../blobs/point-in-time-restore-overview.md).<br /><br />Das Aktivieren der Zeitpunktwiederherstellung ermöglicht auch die Blobversionsierung, das vorläufig gelöschte Blob und den Blobänderungsfeed. Diese erforderlichen Features können sich auf die Kosten auswirken. Weitere Informationen finden Sie unter [Preise und Abrechnung](../blobs/point-in-time-restore-overview.md#pricing-and-billing) für die Zeitpunktwiederherstellung. |
| Wiederherstellung | Aktivieren von „Vorläufiges Löschen“ für Blobs | Optional | Das Feature für das vorläufige Löschen von Blobs schützt einzelne Blobs, Momentaufnahmen oder Versionen vor einer versehentlichen Löschung oder Überschreibung, weil die gelöschten Daten für einen Aufbewahrungszeitraum im System beibehalten werden. Während des Aufbewahrungszeitraums können Sie den Zustand eines vorläufig gelöschten Objekts zum Zeitpunkt der Löschung wiederherstellen. Weitere Informationen finden Sie unter [Vorläufiges Löschen von Blobs](../blobs/soft-delete-blob-overview.md).<br /><br />Microsoft empfiehlt die Aktivierung des vorläufigen Löschens von Blobs für Ihre Speicherkonten und die Festlegung eines Aufbewahrungszeitraums von mindestens sieben Tagen. |
| Wiederherstellung | Vorläufiges Löschen für Container (Vorschau) aktivieren | Optional | Das Feature für das vorläufige Löschen von Containers schützt einen Container und seinen Inhalt vor einer versehentlichen Löschung, weil die gelöschten Daten für einen Aufbewahrungszeitraum im System beibehalten werden. Während des Aufbewahrungszeitraums können Sie den Zustand eines vorläufig gelöschten Containers zum Zeitpunkt der Löschung wiederherstellen. Weitere Informationen finden Sie unter [Vorläufiges Löschen für Container (Vorschau)](../blobs/soft-delete-container-overview.md).<br /><br />Microsoft empfiehlt die Aktivierung des vorläufigen Löschens von Containern für Ihre Speicherkonten und die Festlegung eines Aufbewahrungszeitraums von mindestens sieben Tagen. |
| Wiederherstellung | Vorläufiges Löschen für Dateifreigaben aktivieren | Optional | Das Feature für das vorläufige Löschen von Dateifreigaben schützt eine Dateifreigabe und ihren Inhalt vor einer versehentlichen Löschung, weil die gelöschten Daten für einen Aufbewahrungszeitraum im System beibehalten werden. Während des Aufbewahrungszeitraums können Sie den Zustand einer vorläufig gelöschten Dateifreigabe zum Zeitpunkt der Löschung wiederherstellen. Weitere Informationen finden Sie unter [Verhindern des versehentlichen Löschens von Azure-Dateifreigaben.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />Microsoft empfiehlt die Aktivierung des vorläufigen Löschens für Dateifreigaben Azure Files Workloads und die Festlegung eines Aufbewahrungszeitraums von mindestens sieben Tagen. |
| Nachverfolgung | Versionsverwaltung für Blobs aktivieren | Optional | Bei der Blobversionsierung wird der Status eines Blobs automatisch in einer früheren Version gespeichert, wenn das Blob überschrieben wird. Weitere Informationen finden Sie unter [Blobversionsverwaltung (Vorschau)](../blobs/versioning-overview.md).<br /><br />Microsoft empfiehlt die Aktivierung der Blobversionsverarbeitung für einen optimalen Datenschutz für das Speicherkonto. |
| Nachverfolgung | Blob-Änderungsfeed aktivieren | Optional | Der Blob-Änderungsfeed stellt Transaktionsprotokolle aller Änderungen an allen Blobs in Ihrem Speicherkonto sowie an ihren Metadaten bereit. Weitere Informationen finden Sie unter [Änderungsfeed in Azure Blob Storage](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Registerkarte „Tags“

Auf der Registerkarte **Tags** können Sie Resource Manager-Tags angeben, um Ihre Azure-Ressourcen zu organisieren. Weitere Informationen finden Sie unter [Markieren von Ressourcen, Ressourcengruppen und Abonnements für die logische Organisation](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Wenn Sie zur Registerkarte **Überprüfen + erstellen** navigieren, führt Azure die Überprüfung der ausgewählten Speicherkontoeinstellungen aus. Wenn die Überprüfung erfolgreich ist, können Sie mit dem Erstellen des Speicherkontos fortfahren.

Wenn die Überprüfung fehlschlägt, gibt das Portal an, welche Einstellungen geändert werden müssen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Legen Sie zum Erstellen eines Speicherkontos vom Typ „Allgemein v2“ mit PowerShell zunächst eine neue Ressourcengruppe an, indem Sie den Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) aufrufen:

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Falls Sie nicht wissen, welche Region Sie für den `-Location`-Parameter angeben sollen, können Sie mit dem Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen:

```azurepowershell-interactive
Get-AzLocation | select Location
```

Erstellen Sie als Nächstes ein standardmäßiges Speicherkonto vom Typ „Universell v2“ mit georedundantem Speicher mit Lesezugriff (RA-GRS) über den Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Denken Sie daran, dass der Name Ihres Speicherkontos innerhalb von Azure eindeutig sein muss, ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Legen Sie den Parameter `EnableHierarchicalNamespace' parameter to ` für den Aufruf des Befehls **New-AzStorageAccount** auf $True` fest, um einen hierarchischen Namespace für das Speicherkonto für die Verwendung von [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) zu aktivieren.

Die folgende Tabelle zeigt, welche Werte für die `SkuName`- und `Kind`-Parameter und verwendet werden sollen, um einen bestimmten Speicherkontotyp mit der gewünschten Redundanzkonfiguration zu erstellen.

| Speicherkontotyp | Unterstützte Redundanzkonfigurationen | Unterstützte Werte für den Parameter „Variante“ | Unterstützte Werte für den Parameter „SkuName“ | Unterstützt hierarchischen Namespace |
|--|--|--|--|--|
| Standard, Universell V2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Ja |
| Premium-Blockblobs | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Ja |
| Premium-Dateifreigaben | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | Nein |
| Premium-Seitenblobs | LRS | StorageV2 | Premium_LRS | Nein |
| Legacy-Standard „Allgemein v1“ | LRS / GRS / RA-GRS | Storage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nein |
| Legacy-Blobspeicher | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nein |

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Legen Sie zum Erstellen eines Speicherkontos vom Typ „Allgemein v2“ mit Azure CLI zunächst eine neue Ressourcengruppe an, indem Sie den Befehl [az group create](/cli/azure/group#az_group_create) aufrufen.

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Falls Sie nicht wissen, welche Region Sie für den `--location`-Parameter angeben sollen, können Sie mit dem Befehl [az account list-locations](/cli/azure/account#az_account_list) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Erstellen Sie als Nächstes ein standardmäßiges Speicherkonto vom Typ „Allgemein v2“ mit georedundantem Speicher mit Lesezugriff über den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create). Denken Sie daran, dass der Name Ihres Speicherkontos innerhalb von Azure eindeutig sein muss, ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Legen Sie den Parameter `enable-hierarchical-namespace` für den Aufruf des Befehls **az storage account create** auf `true` fest, um einen hierarchischen Namespace für das Speicherkonto für die Verwendung von [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) zu aktivieren. Zum Erstellen eines hierarchischen Namespace Azure CLI Version 2.0.79 oder höher erforderlich.

Die folgende Tabelle zeigt, welche Werte für die `sku`- und `kind`-Parameter und verwendet werden sollen, um einen bestimmten Speicherkontotyp mit der gewünschten Redundanzkonfiguration zu erstellen.

| Speicherkontotyp | Unterstützte Redundanzkonfigurationen | Unterstützte Werte für den Parameter „Variante“ | Unterstützte Werte für den Parameter „sku“ | Unterstützt hierarchischen Namespace |
|--|--|--|--|--|
| Standard, Universell V2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Ja |
| Premium-Blockblobs | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Ja |
| Premium-Dateifreigaben | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | Nein |
| Premium-Seitenblobs | LRS | StorageV2 | Premium_LRS | Nein |
| Legacy-Standard „Allgemein v1“ | LRS / GRS / RA-GRS | Storage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nein |
| Legacy-Blobspeicher | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nein |

# <a name="template"></a>[Vorlage](#tab/template)

Sie können entweder Azure PowerShell oder die Azure CLI verwenden, um eine Resource Manager-Vorlage zum Erstellen eines Speicherkontos bereitzustellen. Die in diesem Artikel verwendete Resource Manager-Vorlage stammt von der Seite mit den [Azure Resource Manager-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/storage-account-create/). Wählen Sie zum Ausführen der Skripts **Testen** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Diese Vorlage dient nur als Beispiel. Es gibt viele Speicherkontoeinstellungen, die in dieser Vorlage nicht konfiguriert sind. Wenn Sie z. B. [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) verwenden möchten, ändern Sie diese Vorlage, indem Sie die `isHnsEnabledad`-Eigenschaft des Objekts `StorageAccountPropertiesCreateParameters` auf `true` festlegen.

Informationen zum Ändern dieser Vorlage und zum Erstellen neuer Vorlagen finden Sie unter:

- [Dokumentation zu Azure Resource Manager](../../azure-resource-manager/index.yml)
- [Vorlagenreferenz für Speicherkonten](/azure/templates/microsoft.storage/allversions)
- [Weitere Beispiele für Speicherkontovorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

Durch das Löschen eines Speicherkontos wird das gesamte Konto gelöscht, einschließlich aller Daten im Konto. Sichern Sie alle Daten, die Sie speichern möchten, bevor Sie das Konto löschen.

Unter bestimmten Umständen kann ein gelöschtes Speicherkonto wiederhergestellt werden, aber die Wiederherstellung ist nicht garantiert. Weitere Informationen finden Sie unter [Wiederherstellen eines gelöschten Speicherkontos](storage-account-recover.md).

Wenn Sie versuchen, ein Speicherkonto zu löschen, das einem virtuellen Azure-Computer zugewiesen ist, wird unter Umständen ein Fehler mit dem Hinweis angezeigt, dass das Speicherkonto noch verwendet wird. Hilfe zum Beheben dieses Fehlers finden Sie unter [Beheben von Fehlern beim Löschen von Speicherkonten](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto.
1. Klicken Sie auf **Löschen**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Löschen des Speicherkontos den Befehl [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount):

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Löschen des Speicherkontos den Befehl [az storage account delete](/cli/azure/storage/account#az_storage_account_delete):

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

Verwenden Sie zum Löschen des Speicherkontos entweder Azure PowerShell oder die Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativ können Sie die Ressourcengruppe löschen, wodurch das Speicherkonto und alle anderen Ressourcen in dieser Ressourcengruppe gelöscht werden. Weitere Informationen zum Löschen einer Ressourcengruppe finden Sie unter [Löschen von Ressourcengruppe und Ressourcen](../../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](storage-account-overview.md)
- [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md)
- [Verschieben eines Speicherkontos in eine andere Region](storage-account-move.md)
- [Wiederherstellen eines gelöschten Speicherkontos](storage-account-recover.md)
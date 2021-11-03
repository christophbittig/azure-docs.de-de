---
title: Aufheben der Bereitstellung eines Azure-Datenträgerpools (Vorschau)
description: Erfahren Sie, wie Sie die Bereitstellung eines Azure-Datenträgerpools aufheben, beenden und löschen können.
author: roygara
ms.date: 11/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 36c301cb5575d7627a6179520aef79c01f8cd837
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022130"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Aufheben der Bereitstellung eines Azure-Datenträgerpools (Vorschau)

In diesem Artikel wird der Löschvorgang für einen Azure-Datenträgerpool (Vorschau) und die Deaktivierung der iSCSI-Unterstützung behandelt.

## <a name="stop-a-disk-pool"></a>Beenden eines Datenträgerpools

Sie können einen Datenträgerpool beenden, um Kosten zu sparen und alle Konfigurationen beizubehalten. Wenn ein Datenträgerpool beendet wird, können Sie keine Verbindung mehr über iSCSI mit ihm herstellen. Die verwalteten Ressourcen, die zur Unterstützung des Datenträgerpools bereitgestellt wurden, werden nicht gelöscht. Sie müssen zuerst alle Clients mit iSCSI-Verbindungen mit dem Datenträgerpool trennen, bevor Sie einen Datenträgerpool beenden. Sie können einen Datenträgerpool jederzeit starten. Dadurch wird das in diesem Datenträgerpool verfügbar gemachte iSCSI-Ziel erneut aktiviert.
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Datenträgerpool, und wählen Sie **Beenden** aus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name '<yourDiskPool>' -ResourceGroupName '<yourResourceGroup>'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk-pool stop --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```
---

## <a name="disable-iscsi-support"></a>Deaktivieren der iSCSI-Unterstützung

Wenn Sie die iSCSI-Unterstützung für einen Datenträgerpool deaktivieren, können Sie keine Verbindung mehr mit einem Datenträgerpool herstellen.

Wenn Sie die iSCSI-Unterstützung zum ersten Mal für einen Datenträgerpool aktivieren, wird ein iSCSI-Ziel als Endpunkt für die iSCSI-Verbindung erstellt. Sie können die iSCSI-Unterstützung für den Datenträgerpool deaktivieren, indem Sie das iSCSI-Ziel löschen. Für jeden Datenträgerpool kann nur ein iSCSI-Ziel konfiguriert sein.

Sie können die iSCSI-Unterstützung für einen vorhandenen Datenträgerpool erneut aktivieren. Die iSCSI-Unterstützung kann für den Datenträgerpool nicht deaktiviert werden, wenn ausstehende iSCSI-Verbindungen mit dem Datenträgerpool bestehen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie nach **Datenträgerpool**, und wählen Sie Ihren Datenträgerpool aus.
1. Wählen Sie unter **Einstellungen** die Option **iSCSI** aus.
1. Deaktivieren Sie das Kontrollkästchen **iSCSI aktivieren**, und wählen Sie **Speichern** aus.    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "<yourDiskpoolName>" -Name "<youriSCSITargetName>" -ResourceGroupName "yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "<yourDiskPool>" --name "<yourIscsiTarget>" --resource-group "<yourResourceGroup>"
```
---

## <a name="delete-a-disk-pool"></a>Löschen eines Datenträgerpools

Wenn Sie einen Datenträgerpool löschen, werden alle Ressourcen in der verwalteten Ressourcengruppe ebenfalls gelöscht. Wenn ausstehende iSCSI-Verbindungen mit dem Datenträgerpool bestehen, können Sie den Datenträgerpool nicht löschen. Sie müssen zuerst alle Clients mit iSCSI-Verbindungen mit dem Datenträgerpool trennen. Datenträger, die dem Datenträgerpool hinzugefügt wurden, werden nicht gelöscht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie nach **Datenträgerpool**, und wählen Sie ihn aus. Wählen Sie dann den Datenträgerpool aus, den Sie löschen möchten.
1. Wählen Sie am oberen Rand des Bereichs die Option **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>"

Remove-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>
Remove-AzDiskPool -Name "<yourDiskpoolName>" -ResourceGroupName "<yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az disk-pool delete --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Verwaltete Azure-Datenträger](managed-disks-overview.md).

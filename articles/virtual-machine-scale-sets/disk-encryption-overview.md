---
title: Aktivieren von Azure Disk Encryption für VM-Skalierungsgruppen
description: In diesem Artikel erfahren Sie, wie Sie Microsoft Azure Disk Encryption für VM-Skalierungsgruppen aktivieren.
author: ju-shim
ms.author: jushiman
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.openlocfilehash: 579315379923b5ba8a16f94cff5984e6e904a5ec
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691300"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption für VM-Skalierungsgruppen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Azure Disk Encryption bietet Volumeverschlüsselung für Betriebssystemdatenträger und reguläre Datenträger Ihrer virtuellen Computer und trägt so zum Schutz Ihrer Daten bei, um die Sicherheits- und Compliancevorgaben der Organisation zu erfüllen. Weitere Informationen finden Sie unter [Azure Disk Encryption für Linux-VMs](../virtual-machines/linux/disk-encryption-overview.md) bzw. unter [Azure Disk Encryption für virtuelle Windows-Computer](../virtual-machines/windows/disk-encryption-overview.md).  

In folgenden Fällen kann Azure Disk Encryption auch auf VM-Skalierungsgruppen unter Windows und Linux angewendet werden:
- Skalierungsgruppen mit verwalteten Datenträgern. Für Skalierungsgruppen mit nativen (oder nicht verwalteten) Datenträgern wird Azure Disk Encryption nicht unterstützt.
- Betriebssystem- und Datenvolumes in Windows-Skalierungsgruppen
- Datenvolumes in Linux-Skalierungsgruppen. Betriebssystem-Datenträgerverschlüsselung wird zurzeit für Linux-Skalierungsgruppen NICHT unterstützt.

Anhand der Tutorials [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure CLI](disk-encryption-cli.md) und [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure PowerShell](disk-encryption-powershell.md) können Sie sich in wenigen Minuten mit den Grundlagen von Azure Disk Encryption für VM-Skalierungsgruppen vertraut machen.

## <a name="next-steps"></a>Nächste Schritte

- [Verschlüsseln von VM-Skalierungsgruppen mit Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md)
- [Verwenden von Azure Disk Encryption mit Erweiterungssequenzierung der VM-Skalierungsgruppe](disk-encryption-extension-sequencing.md)

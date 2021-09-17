---
title: Erstellen und Verschlüsseln einer VM-Skalierungsgruppe mit Azure Resource Manager-Vorlagen
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Azure Resource Manager-Vorlagen eine VM-Skalierungsgruppe erstellen und verschlüsseln.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 058ba9a3b2593ec6f635485771d5bbe140beb510
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698988"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Verschlüsseln von VM-Skalierungsgruppen mit Azure Resource Manager

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Linux-basierte VM-Skalierungsgruppen können unter Verwendung von Azure Resource Manager-Vorlagen verschlüsselt und entschlüsselt werden.

## <a name="deploying-templates"></a>Bereitstellen von Vorlagen

Wählen Sie zunächst eine geeignete Vorlage für Ihr Szenario aus.

- [Enable disk encryption on a running Linux virtual machine scale set](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-linux) (Aktivieren der Datenträgerverschlüsselung in einer ausgeführten VM-Skalierungsgruppe unter Linux)

- [Enable disk encryption on a running Windows virtual machine scale set](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-windows) (Aktivieren der Datenträgerverschlüsselung in einer ausgeführten VM-Skalierungsgruppe unter Windows)

  - [Deploy a virtual machine scale set of Linux VMs with a jumpbox and enables encryption on Linux virtual machine scale sets](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-linux-jumpbox) (Bereitstellen einer VM-Skalierungsgruppe mit virtuellen Linux-Computern und einer Jumpbox sowie Aktivieren der Verschlüsselung in Linux-basierten VM-Skalierungsgruppen)

  - [Deploy a virtual machine scale set of Windows VMs with a jumpbox and enables encryption on Windows virtual machine scale sets](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-windows-jumpbox) (Bereitstellen einer VM-Skalierungsgruppe mit virtuellen Windows-Computern und einer Jumpbox sowie Aktivieren der Verschlüsselung in Windows-basierten VM-Skalierungsgruppen)

- [Disable disk encryption on a running Linux virtual machine scale set](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-linux) (Deaktivieren der Datenträgerverschlüsselung in einer ausgeführten VM-Skalierungsgruppe unter Linux)

- [Disable disk encryption on a running Windows virtual machine scale set](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-windows) (Deaktivieren der Datenträgerverschlüsselung in einer ausgeführten VM-Skalierungsgruppe unter Windows)

Führen Sie dann die folgenden Schritte durch:

1. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.
2. Füllen Sie die erforderlichen Felder aus, und stimmen Sie dann den Geschäftsbedingungen zu.
3. Klicken Sie auf **Kaufen**, um die Vorlage bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Disk Encryption für VM-Skalierungsgruppen](disk-encryption-overview.md)
- [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure CLI](disk-encryption-cli.md)
- [Verschlüsseln von Betriebssystem- und angefügten Datenträgern in einer VM-Skalierungsgruppe mit Azure PowerShell](disk-encryption-powershell.md)
- [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md)
- [Verwenden von Azure Disk Encryption mit Erweiterungssequenzierung der VM-Skalierungsgruppe](disk-encryption-extension-sequencing.md)

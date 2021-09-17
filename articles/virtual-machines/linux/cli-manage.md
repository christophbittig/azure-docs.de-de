---
title: Häufig verwendete Azure-CLI-Befehle
description: Erfahren Sie mehr über häufig verwendete Azure CLI-Befehle für den Einstieg in die Verwaltung Ihrer virtuellen Computer im Azure Resource Manager-Modus
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/12/2017
ms.author: cynthn
ms.openlocfilehash: 64f028059504d7d4ff7905653146add0649ecd2f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697003"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Häufig verwendete Azure CLI-Befehle zum Verwalten von Azure-Ressourcen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Mit Azure CLI können Sie Ihre Azure-Ressourcen unter macOS, Linux und Windows erstellen und verwalten. In diesem Artikel werden einige der am häufigsten verwendeten Befehle zum Erstellen und Verwalten von virtuellen Computern (VMs) erläutert.

Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) weitere Informationen. Sie können auch [Cloud Shell](../../cloud-shell/quickstart.md) in Ihrem Browser verwenden.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Grundlegende Azure Resource Manager-Befehle in der Azure-Befehlszeilenschnittstelle
Ausführlichere Informationen zu bestimmten Befehlszeilenschaltern und -optionen finden Sie in der Onlinehilfe zu Befehlen und Optionen, die Sie durch Eingeben von `az <command> <subcommand> --help` aufrufen können.

### <a name="create-vms"></a>Virtuelle Computer erstellen
| Aufgabe | Azure CLI-Befehle |
| --- | --- |
| Erstellen einer Ressourcengruppe | `az group create --name myResourceGroup --location eastus` |
| Erstellen eines virtuellen Linux-Computers | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Erstellen eines virtuellen Windows-Computers | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Verwalten des VM-Status
| Aufgabe | Azure CLI-Befehle |
| --- | --- |
| Starten eines virtuellen Computers | `az vm start --resource-group myResourceGroup --name myVM` |
| Anhalten eines virtuellen Computers | `az vm stop --resource-group myResourceGroup --name myVM` |
| Aufheben der Zuordnung eines virtuellen Computers | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Neustarten eines virtuellen Computers | `az vm restart --resource-group myResourceGroup --name myVM` |
| Erneutes Bereitstellen eines virtuellen Computers | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Löschen einer VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Abrufen von VM-Informationen
| Aufgabe | Azure CLI-Befehle |
| --- | --- |
| Auflisten der virtuellen Computer | `az vm list` |
| Abrufen von Informationen zu einem virtuellen Computer | `az vm show --resource-group myResourceGroup --name myVM` |
| Abrufen der Nutzung virtueller Computerressourcen | `az vm list-usage --location eastus` |
| Abrufen Sie aller verfügbaren Größen von virtuellen Computern | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Datenträger und Images
| Aufgabe | Azure CLI-Befehle |
| --- | --- |
| Hinzufügen eines Datenträgers zu einem virtuellen Computer | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Entfernen eines Datenträgers aus einem virtuellen Computer | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Ändern der Größe eines Datenträgers | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Erstellen einer Momentaufnahme eines Datenträgers | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Erstellen eines Images einer VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Erstellen einer VM aus einem Image | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Nächste Schritte
Weitere Beispiele für CLI-Befehle finden Sie im Tutorial [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](tutorial-manage-vm.md).

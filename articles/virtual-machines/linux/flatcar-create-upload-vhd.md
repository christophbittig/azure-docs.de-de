---
title: Erstellen und Hochladen einer VHD-Datei mit Flatcar Container Linux zur Verwendung in Azure
description: Erfahren Sie, wie Sie eine VHD erstellen und hochladen, die das Betriebssystem Flatcar Container Linux enthält.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 7df838cdc036e482bc97d9ca0bb76dd648eb0a46
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695572"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Verwenden eines vordefinierten Flatcar-Images für Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Sie können vorab erstellte Images von virtuellen Azure-Festplatten mit Flatcar Container Linux für jeden der von Flatcar unterstützten Kanäle herunterladen:

- [stable](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Dieses Image ist bereits vollständig eingerichtet und für die Ausführung in Azure optimiert. Sie müssen es nur dekomprimieren.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Erstellen eines eigenen Flatcar-basierten virtuellen Computers für Azure

Alternativ können Sie auch Ihr eigenes Flatcar Container Linux-Image erstellen.

Befolgen Sie auf einem Linux-Computer die Anweisungen im [Flatcar Container Linux developer SDK guide](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/) (Entwickler-SDK-Handbuch für Flatcar Container Linux). Wenn Sie das Skript `image_to_vm.sh` ausführen, stellen Sie sicher, dass Sie `--format=azure` übergeben, um eine virtuelle Azure-Festplatte zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie über die VHD-Datei verfügen, können Sie mit der resultierenden Datei neue virtuelle Computer in Azure erstellen. Wenn Sie zum ersten Mal eine VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen einer Linux-VM aus einem benutzerdefinierten Datenträger](upload-vhd.md#option-1-upload-a-vhd).

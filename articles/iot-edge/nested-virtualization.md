---
title: Geschachtelte Virtualisierung für Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die geschachtelte Virtualisierung in Azure IoT Edge für Linux unter Windows steuern.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354913"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Geschachtelte Virtualisierung für Azure IoT Edge für Linux unter Windows
Es gibt zwei Formen der geschachtelten Virtualisierung, die mit Azure IoT Edge für Linux unter Windows kompatibel sind. Die Benutzer können wählen, ob die Bereitstellung über einen lokalen virtuellen Computer oder über einen virtuellen Azure-Computer erfolgen soll. Anhand dieses Artikels können sich Benutzer einen Überblick darüber verschaffen, welche Option für ihr Szenario am besten geeignet ist. Zudem erhalten sie Informationen zu den Konfigurationsanforderungen.

> [!NOTE]
>
> Stellen Sie sicher, dass Sie eine [Netzwerkoption](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) für die geschachtelte Virtualisierung aktivieren. Wenn Sie dies nicht tun, treten bei der Installation von EFLOW Fehler auf. 

## <a name="deployment-on-local-vm"></a>Bereitstellung auf einem lokalen virtuellen Computer

Hierbei handelt es sich um einen grundlegenden Ansatz für alle virtuellen Windows-Computer, auf denen Azure IoT Edge für Linux unter Windows gehostet wird. In diesem Fall muss zunächst eine geschachtelte Virtualisierung aktiviert werden, bevor mit der Bereitstellung begonnen wird. Weitere Informationen zum Konfigurieren dieses Szenarios finden Sie unter [Ausführen von Hyper-V auf einem virtuellen Computer mit geschachtelter Virtualisierung](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).

Wenn Sie Windows Server verwenden, müssen Sie die [Hyper-V-Rolle installieren](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Bereitstellung auf virtuellen Azure-Computern

Azure IoT Edge für Linux unter Windows ist nicht kompatibel mit einem virtuellen Azure-Computer, auf dem die Server-SKU ausgeführt wird – außer wenn ein spezielles Skript ausgeführt wird, mit dem ein Standardswitch bereitgestellt wird. Weitere Informationen zum Bereitstellen eines Standardswitches finden Sie unter [Erstellen eines virtuellen Switches für Linux unter Windows](how-to-create-virtual-switch.md).

> [!NOTE]
>
> Ein virtueller Azure-Computer, auf dem EFLOW gehostet werden soll, muss die [geschachtelte Virtualisierung unterstützen](../virtual-machines/acu.md).

---
title: Erstellen einer Azure IoT Hub-Instanz mithilfe eines PowerShell-Cmdlets | Microsoft Docs
description: Hier erfahren Sie, wie Sie mit den PowerShell-Cmdlets eine Ressourcengruppe und dann in der Ressourcengruppe eine IoT Hub-Instanz erstellen. Außerdem erfahren Sie, wie die Hub-Instanz entfernt wird.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1730887d38edf2d64a3456a188a50c3becaabbad
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551803"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Erstellen einer IoT Hub-Instanz mithilfe des Cmdlets New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Einführung

Mit Azure PowerShell-Cmdlets können Sie Azure IoT Hubs erstellen und verwalten. In diesem Tutorial erfahren Sie, wie Sie eine IoT Hub-Instanz mit PowerShell erstellen.

Für diese Anleitung wird ein Azure-Abonnement benötigt. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Verbinden mit Ihrem Azure-Abonnement

Wenn Sie die Cloud Shell-Befehlszeile verwenden, sind Sie bereits bei Ihrem Abonnement angemeldet. Wenn Sie stattdessen PowerShell lokal ausführen, geben Sie den folgenden Befehl ein, um sich bei Ihrem Azure-Abonnement anzumelden:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Für das Bereitstellen einer IoT Hub-Instanz benötigen Sie eine Ressourcengruppe. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen.

Wenn Sie eine neue Ressourcengruppe für Ihre IoT Hub-Instanz erstellen möchten, verwenden Sie den Befehl [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Mit diesem Beispiel wird die Ressourcengruppe **MyIoTRG1** in der Region **USA, Osten** erstellt:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Verwenden Sie den Befehl [New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub), um eine IoT Hub-Instanz in der Ressourcengruppe zu erstellen, die Sie im vorherigen Schritt erstellt haben. Mit diesem Beispiel wird ein **S1**-Hub mit dem Namen **MyTestIoTHub** in der Region **USA, Osten** erstellt:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Der Name der IoT Hub-Instanz muss global eindeutig sein.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Mit dem Befehl [Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) können Sie alle IoT Hub-Instanzen in Ihrem Abonnement auflisten:

```azurepowershell-interactive
Get-AzIotHub
```

Dieses Beispiel zeigt die IoT Hub-Instanz vom Typ „S1 Standard“, die Sie im vorherigen Schritt erstellt haben.

Sie können die IoT Hub-Instanz mit dem Befehl [Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) löschen:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativ können Sie eine Ressourcengruppe und alle darin enthaltenen Ressourcen mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine IoT Hub-Instanz mit einem PowerShell-Cmdlet bereitgestellt haben, möchten Sie vielleicht mehr wissen. Lesen Sie die folgenden Artikel:

* [PowerShell-Cmdlets zur Verwendung mit Ihrer IoT Hub-Instanz](/powershell/module/az.iothub/)

* [IoT Hub-Ressourcenanbieter-REST-API](/rest/api/iothub/iothubresource)

Weitere Informationen zum Entwickeln für IoT Hub finden Sie in folgenden Artikeln:

* [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDKs](iot-hub-devguide-sdks.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)

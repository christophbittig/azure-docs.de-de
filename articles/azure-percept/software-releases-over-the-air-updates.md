---
title: Softwarereleases für Azure Percept DK-OTA-Updates
description: Informationen und Downloadlinks für Over-the-Air-Updatepakete für das Azure Percept DK
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c8f05b1c2d04e492b44aef88ac31a8e4dece5e16
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222863"
---
# <a name="software-releases-for-ota-updates"></a>Softwarereleases für OTA-Updates

Das OTA-Update wurde für Benutzer erstellt, die das Dev Kit immer auf dem neuesten Stand halten möchten. Aus diesem Grund werden hier nur die Hard-Stop-Versionen und die neueste Version bereitgestellt. Wenn Sie Ihr Dev Kit in eine bestimmte (ältere) Version ändern möchten, verwenden Sie das Update über USB-Kabel. Hier finden Sie Informationen zum [Aktualisieren des Azure Percept DK über eine USB-C-Kabelverbindung](./how-to-update-via-usb.md). Verwenden Sie auch das USB-Update, wenn Sie zu einer viel höheren Version wechseln möchten.

>[!CAUTION]
>Das Dev Kit unterstützt kein Downgrade der SW-Version über OTA. Das Device Update for IoT Hub-Framework blockiert NICHT die Bereitstellung eines Images, das eine ältere als die aktuelle Version aufweist. Beim Dev Kit führt dies jedoch zu einem Verlust von Daten und Funktionen.

>[!IMPORTANT]
>Lesen Sie unbedingt das folgende Dokument, bevor Sie sich für ein Update über OTA oder USB-Kabel entscheiden.
>
>[Bestimmen Ihrer Updatestrategie](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>Hard-Stop-Version des OTA-Updates

Microsoft würde jedes Dev Kit-Release mit OTA-Paketen bereitstellen. Da es jedoch Breaking Changes für das Betriebssystem, die Firmware oder die OTA-Plattform des Dev Kit gibt, kann ein direktes OTA-Update von einer alten Version auf eine weit höhere Version zu Problemen führen. Im Allgemeinen stellt Microsoft bei einem Breaking Change sicher, dass der OTA-Updateprozess das alte System nahtlos auf die **erste Version überträgt, in der dieser Breaking Change eingeführt wird**. Diese spezifische Version wird zu einer Hard-Stop-Version für OTA. Betrachten wir als Beispiel eine bekannte Hard-Stop-Version, das **Juni-Release**. Ein OTA-Update funktioniert, wenn ein Benutzer das Dev Kit von 2104 auf 2106 und dann von 2106 auf 2107 aktualisiert. Es funktioniert jedoch NICHT, wenn ein Benutzer versucht, die Hard-Stop-Version (2106) zu überspringen und das Dev Kit direkt von 2104 auf 2107 zu aktualisieren.

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="Hard-Stop-Version eines OTA-Updates":::

## <a name="recommendations-for-applying-the-ota-update"></a>Empfehlungen zum Anwenden des OTA-Updates

**Szenario 1**: Sie aktualisieren das Dev Kit häufig (monatlich), um sicherzustellen, dass es immer auf dem neuesten Stand ist.

- Es sollte kein Problem auftreten, wenn Sie immer OTA zum Aktualisieren des Dev Kits vom letzten Release auf die neu veröffentlichte Version verwenden.

**Szenario 2**: Sie führen ein Update durch und überspringen dabei möglicherweise einige Versionen.

1. Identifizieren Sie die aktuelle Softwareversion des Dev Kit.
1. Überprüfen Sie die Liste von OTA-Paketreleases, um nach einer Hard-Stop-Version zwischen der aktuellen Version und der Zielversion zu suchen.
    - Wenn eine solche Version vorliegt, müssen Sie die Hard-Stop-Version(en) nacheinander bereitstellen, bis Sie das neueste Updatepaket bereitstellen können.
    - Andernfalls können Sie direkt das neueste OTA-Paket für das Dev Kit bereitstellen.

## <a name="identify-the-current-software-version-of-dev-kit"></a>Identifizieren der aktuellen Softwareversion des Dev Kit

**Option 1:**

1. Melden Sie sich bei [Azure Percept Studio](./overview-azure-percept-studio.md) an.
1. Wählen Sie unter **Geräte** Ihr Dev Kit-Gerät aus.
1. Suchen Sie auf der Registerkarte **Allgemein** nach den Informationen zur **Modell-** und **Softwareversion**.

**Option 2:**

1. Zeigen Sie das **IoT Edge-Gerät** des **IoT Hub**-Diensts im Microsoft Azure-Portal an.
1. Wählen Sie Ihr Dev Kit-Gerät aus der Geräteliste aus.
1. Wählen Sie **Gerätezwilling** aus.
1. Scrollen Sie durch die Gerätezwillingseigenschaften, suchen Sie unter **deviceInformation** nach **model** und **swVersion**, und notieren Sie sich ihre Werte.

## <a name="identify-the-ota-packages-to-be-deployed"></a>Identifizieren der bereitzustellenden OTA-Pakete

>[!IMPORTANT]
>Wenn die aktuelle Version Ihres Dev Kit in den nachstehenden Releases nicht enthalten ist, wird sie für ein OTA-Update NICHT unterstützt. Führen Sie ein Update über USB-Kabel durch, um die neueste Version zu erhalten.

**Aktuelles Release:**

|Freigabe|Anwendbare Version(en)|Downloadlinks|Hinweis|
|---|---|---|---|
|Service Release von Juli (2107)|2021.106.111.115|[OTA-Updatepaket 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169245)||

**Hard-Stop-Releases:**

|Freigabe|Anwendbare Version(en)|Downloadlinks|Hinweis|
|---|---|---|---|
|Service Release von Juni (2106)|2021.102.108.112, 2021.104.110.103, 2021.105.111.122 |[OTA-Manifest 2021.106.111.115 (für PE-101)](https://go.microsoft.com/fwlink/?linkid=2167127)<br>[OTA-Manifest 2021.106.111.115 (für APDK-101)](https://go.microsoft.com/fwlink/?linkid=2167235) <br>[OTA-Updatepaket 2021.106.111.115](https://go.microsoft.com/fwlink/?linkid=2167128) |Achten Sie darauf, das richtige Manifest im Hinblick auf „Modellname“ (PE-101/APDK-101) zu verwenden.|

## <a name="next-steps"></a>Nächste Schritte

[Aktualisieren Ihres Azure Percept DK-Geräts per Funk (Over-the-Air-Update)](./how-to-update-over-the-air.md)

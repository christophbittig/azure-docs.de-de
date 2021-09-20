---
title: Auswählen des Updatepakets für Azure Percept DK
description: Vorgehensweise zum Identifizieren Ihrer Azure Percept DK-Version und zum Auswählen des entsprechend besten Updatepakets
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 3e18d8a009736e8e597d5accffb654c1b95aec5d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225226"
---
# <a name="select-your-azure-percept-dk-update-package"></a>Auswählen des Updatepakets für Azure Percept DK

Diese Seite enthält Anleitungen zum Auswählen des für Ihr Development Kit am besten geeigneten Updatepakets und der Speicherorte zum Herunterladen der Updatepakete.

Weitere Informationen zum Aktualisieren Ihres Geräts finden Sie in den folgenden Artikeln:

- [Aktualisieren Ihres Azure Percept DK-Geräts per Funk (Over-the-Air-Update)](./how-to-update-over-the-air.md)
- [Aktualisieren Ihres Azure Percept-Geräts über USB](./how-to-update-via-usb.md)

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270), das [eingerichtet und mit Azure Percept Studio und IoT Hub verbunden ist](./quickstart-percept-dk-set-up.md).

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>Identifizieren des Modellnamens und der Softwareversion Ihres Development Kits

Um sicherzustellen, dass Sie das richtige Updatepaket auf Ihr Development Kit anwenden, müssen Sie zuerst ermitteln, welche Softwareversion aktuell ausgeführt wird.

> [!WARNING]
> Das Anwenden des falschen Updatepakets kann dazu führen, dass Ihr Development Kit nicht mehr funktioniert. Führen Sie deshalb unbedingt die folgenden Schritte aus, um sicherzustellen, dass Sie das richtige Updatepaket anwenden.

Option 1:

1. Melden Sie sich bei [Azure Percept Studio](./overview-azure-percept-studio.md) an.
1. Wählen Sie unter **Geräte** Ihr DevKit-Gerät aus.
1. Suchen Sie auf der Registerkarte **Allgemein** nach den Informationen zur **Modell-** und **Softwareversion**.

Option 2:

1. Zeigen Sie das **IoT Edge-Gerät** des **IoT Hub**-Diensts im Microsoft Azure-Portal an.
1. Wählen Sie Ihr DevKit-Gerät aus der Geräteliste aus.
1. Wählen Sie **Gerätezwilling** aus.
1. Scrollen Sie durch die Gerätezwillingseigenschaften, suchen Sie unter **deviceInformation** nach **model** und **swVersion**, und notieren Sie sich ihre Werte.

## <a name="determine-the-correct-update-package"></a>Ermitteln des richtigen Updatepakets

Ermitteln Sie anhand der im vorherigen Abschnitt angegebenen **model**- und **swVersion**-Informationen, welches Updatepaket Sie in der folgenden Tabelle herunterladen sollten.

|model  |swVersion  |Updatemethode  |Downloadlinks  |Hinweis  |
|---------|---------|---------|---------|---------|
|PE-101     |Alle swVersions       |**Nur USB**         |[USB-Updatepaket 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Juli-Release (2107)         |
|APDK-101     |Agent-swVersion vor 2021.106.111.115 |**Nur USB**         |[USB-Updatepaket 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Juli-Release (2107)         |
|APDK-101     |2021.106.111.115        |OTA oder USB       |[OTA-Updatepaket 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[USB-Updatepaket 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)        |Juli-Release (2107)         |

## <a name="next-steps"></a>Nächste Schritte

Aktualisieren Sie Ihre Development Kits mit den im vorherigen Abschnitt ermittelten Methoden und Updatepaketen.

- [Aktualisieren Ihres Azure Percept DK-Geräts per Funk (Over-the-Air-Update)](./how-to-update-over-the-air.md)
- [Aktualisieren Ihres Azure Percept-Geräts über USB](./how-to-update-via-usb.md)

---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 2bb305673897daa264dafc9b766061a01717099a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861164"
---
Sie verwenden das Video Analyzer-Edge-Modul, um Bewegungen im eingehenden Livevideostream zu erkennen und Ereignisse an IoT Hub zu senden. Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, und suchen Sie links unten nach „Azure IoT Hub“.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf **avasample-iot-edge-device**, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

   ![Überwachung des integrierten Ereignisendpunkts starten](../../../media/vscode-common-screenshots/start-monitoring.png)

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]

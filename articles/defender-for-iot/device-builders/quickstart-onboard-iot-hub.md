---
title: 'Schnellstart: Durchführen des Onboardings für Defender für IoT in einer Agent-basierten Lösung'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie in Ihrer Azure IoT Hub-Instanz das Onboarding für den Sicherheitsdienst „Defender für IoT“ durchführen und den Dienst aktivieren.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: f22b319a409b481fdeb26a68cdd4b58809b8d015
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281852"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Schnellstart: Durchführen des Onboardings für Defender für IoT in einer Agent-basierten Lösung

In diesem Artikel erfahren Sie, wie Sie den Dienst „Defender für IoT“ in Ihrer IoT Hub-Instanz aktivieren. Wenn Sie derzeit keinen IoT-Hub besitzen, lesen Sie zum Einstieg die Informationen unter [Erstellen eines IoT-Hubs über das Azure-Portal](../../iot-hub/iot-hub-create-through-portal.md).

Sie können Ihre IoT-Sicherheit über die IoT Hub-Instanz in Defender für IoT verwalten. Über das Verwaltungsportal in der IoT Hub-Instanz können Sie folgende Aufgaben ausführen: 

- Verwalten der IoT Hub-Sicherheit

- Grundlegende Verwaltung der Sicherheit eines IoT-Geräts ohne Installation eines Agents basierend auf der IoT Hub-Telemetrie 

- Erweiterte Verwaltung für die Sicherheit eines IoT-Geräts basierend auf dem Micro-Agent

> [!NOTE]
> Von Defender für IoT werden derzeit nur IoT Hub-Instanzen im Standard-Tarif unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Keine

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Durchführen des Onboardings für Defender für IoT in einer IoT Hub-Instanz

Für alle neuen IoT-Hubs ist Defender für IoT standardmäßig auf **Ein** festgelegt. Sie können überprüfen, ob Defender für IoT während des IoT Hub Erstellungsvorgangs auf **Ein** festgelegt wird.

So überprüfen Sie, ob die Umschaltfläche auf **Ein** festgelegt ist:

1. Navigieren Sie zum Azure-Portal.

1. Wählen Sie in der Liste der Azure-Dienste **IoT Hub** aus.

1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Auswählen der Schaltfläche „Erstellen“ auf der oberen Symbolleiste" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Wählen Sie die Registerkarte **Verwaltung** aus, und überprüfen Sie, ob die Umschaltfläche **Defender für IoT** auf **Ein** festgelegt ist.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Sicherstellen, dass die Umschaltfläche „Defender für IoT“ auf „Ein“ festgelegt ist":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Durchführen des Onboardings für Defender für IoT in eine vorhandene IoT Hub-Instanz

Sie können das Onboarding für Defender für IoT in einer vorhandenen IoT Hub-Instanz durchführen. Dort können Sie dann die Identitätsverwaltung Ihres Geräts und die Kommunikationsmuster zwischen Gerät und Cloud sowie zwischen Cloud und Gerät überwachen.

So führen Sie das Onboarding für Defender für IoT in einer vorhandenen IoT Hub-Instanz durch:

1. Navigieren Sie zur Instanz von IoT Hub. 

1. Wählen Sie die IoT Hub-Instanz für das Onboarding aus.

1. Wählen Sie im Abschnitt **Sicherheit** eine beliebige Option aus.

1. Klicken Sie auf  **IoT-Lösung schützen**, und füllen Sie das Formular zum Onboarding aus. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Auswählen der Schaltfläche „IoT-Lösung schützen“ zum Schützen Ihrer Lösung":::

Die Schaltfläche **IoT-Lösung schützen** wird nur angezeigt, wenn das Onboarding für die IoT Hub-Instanz noch nicht durchgeführt wurde oder wenn beim Onboarding die Umschaltfläche für Defender für IoT auf **Aus** festgelegt blieb.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Umschaltfläche während des Onboardings auf „Aus“ festgelegt":::

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Ihre Lösung konfigurieren:

> [!div class="nextstepaction"]
> [Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender für Cloud (Vorschau)](quickstart-create-micro-agent-module-twin.md)

---
title: Problembehandlung für Azure Percept Vision und Vision-Module
description: Hier finden Sie Tipps zur Problembehandlung für einige der häufigsten Probleme während der Vision-KI-Prototyperstellung.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0863a4e327749618c215343f1f43fc5a2550a3bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087493"
---
# <a name="troubleshoot-azure-percept-vision-and-vision-modules"></a>Problembehandlung für Azure Percept Vision und Vision-Module

In diesem Artikel finden Sie Informationen zum Beheben von Problemen mit Vision-Lösungen ohne Code in Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Löschen eines Vision-Projekts

1. Navigieren Sie zur Seite [Custom Vision-Projekte](https://www.customvision.ai/projects).

1. Zeigen Sie auf das Projekt, das Sie löschen möchten, und wählen Sie das Papierkorbsymbol aus, um das Projekt zu löschen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Screenshot: Seite „Projekte“ in Custom Vision mit hervorgehobenem Symbol „Löschen“":::

## <a name="check-which-modules-are-on-a-device"></a>Überprüfen, welche Module sich auf einem Gerät befinden

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Wählen Sie das Symbol **IoT Hub** aus.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Screenshot: Startseite des Azure-Portals mit hervorgehobenem Symbol „IoT Hub“" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Wählen Sie den IoT-Hub aus, mit dem das Zielgerät verbunden ist.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Screenshot: Liste mit IoT-Hubs":::

1. Wählen Sie **IoT Edge** und anschließend unter der Registerkarte **Geräte-ID** Ihr Gerät aus.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Screenshot: IoT Edge-Startseite":::

1. Ihre Gerätemodule werden in einer Liste auf der Registerkarte **Module** angezeigt.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Screenshot: Seite „IoT Edge“ für das ausgewählte Gerät mit dem Inhalt der Registerkarte „Module“" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Löschen eines Mediums

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Wählen Sie das Symbol **IoT Hub** aus.

1. Wählen Sie den IoT-Hub aus, mit dem das Zielgerät verbunden ist.

1. Wählen Sie **IoT Edge** aus, und aktivieren Sie das Kontrollkästchen neben der Zielgeräte-ID. Wählen Sie **Löschen** aus, um Ihr Gerät zu löschen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Screenshot: Hervorgehobene Schaltfläche „Löschen“ auf der IoT Edge-Startseite":::

## <a name="check-the-runtime-status-of-azureeyemodule"></a>Prüfen des Laufzeitstatus von azureeyemodule

Vergewissern Sie sich bei einem Problem mit **WebStreamModule**, dass **azureeyemodule** (zuständig für Vision-Modellrückschlüsse) ausgeführt wird. So überprüfen Sie den Laufzeitstatus:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) und dann zu **Alle Ressourcen** >  *\<your IoT hub>*  > **IoT Edge** >  *\<your device ID>* . 
1. Wählen Sie die Registerkarte **Module** aus, um den Laufzeitstatus aller installierten Module anzuzeigen.

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Screenshot: Bildschirm zum Laufzeitstatus der Gerätemodule" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. Ist der Laufzeitstatus von **azureeyemodule** nicht als **Wird ausgeführt** angegeben, wählen Sie **Module festlegen** > **azureeyemodule** aus. 
1. Legen Sie auf der Seite **Moduleinstellungen** die Option **Gewünschter Status** auf **Wird ausgeführt** fest, und wählen Sie **Aktualisieren** aus.

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Screenshot: Konfigurationsbildschirm „Moduleinstellungen“":::

## <a name="change-how-often-messages-are-sent-from-the-azureeyemodule"></a>Ändern, wie oft Nachrichten von azureeyemodule gesendet werden

Ihre Abonnementebene kann die Anzahl von Nachrichten begrenzen, die von Ihrem Gerät an IoT Hub gesendet werden können. Die Ebene „Free“ beispielsweise beschränkt die Anzahl von Nachrichten auf 8.000 pro Tag. Sobald dieser Grenzwert erreicht ist, funktioniert azureeyemodule nicht mehr, und Sie erhalten möglicherweise den folgenden Fehler:

|Fehlermeldung|
|------|
|*Die Gesamtanzahl der Nachrichten auf IotHub 'xxxxxxxxx' hat die zugewiesene Quote überschritten. Maximal erlaubte Nachrichtenanzahl: '8000', aktuelle Meldungsanzahl: 'xxxx'. Sende- und Empfangsvorgänge sind für diesen Hub bis zum nächsten UTC-Tag gesperrt. Erwägen Sie eine Erhöhung der Einheiten für diesen Hub, um die Quote zu erhöhen.*|

Mit dem Modulzwilling „azureeyemodule“ können Sie die Intervallrate für das Senden von Nachrichten ändern. Der für die Intervallrate eingegebene Wert gibt die Häufigkeit an, mit der die einzelnen Nachrichten gesendet werden (in Millisekunden). Je größer die Zahl, desto mehr Zeit liegt zwischen den einzelnen Nachrichten. Wenn Sie beispielsweise die Intervallrate auf 12.000 festlegen, bedeutet dies, dass alle 12 Sekunden eine Nachricht gesendet wird. Bei einem Modell, das den ganzen Tag ausgeführt wird, ergibt diese Rate 7.200 Nachrichten pro Tag. Dieser Wert liegt unter dem Grenzwert der Ebene „Free“. Der von Ihnen ausgewählte Wert hängt davon ab, wie reaktionsfähig Ihr Vision-Modell sein soll.

> [!NOTE]
> Eine Änderung der Nachrichtenintervallrate wirkt sich nicht auf die Größe der einzelnen Nachrichten aus. Die Nachrichtengröße hängt von einigen verschiedenen Faktoren ab, etwa dem Modelltyp und der Anzahl von Objekten, die in jeder Nachricht erkannt werden. Daher ist es schwierig, die Nachrichtengröße zu bestimmen.

Führen Sie die folgenden Schritte aus, um das Nachrichtenintervall zu aktualisieren:

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) an, und öffnen Sie **Alle Ressourcen**.

1. Wählen Sie auf der Seite **Alle Ressourcen** den Namen des IoT-Hubs aus, der während des Setups für Ihr Development Kit bereitgestellt wurde.

1. Wählen Sie links auf der Seite **IoT Hub** unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus. Suchen Sie auf der Seite „IoT Edge-Geräte“ die Geräte-ID Ihres Development Kit. Wählen Sie die Geräte-ID Ihres Development Kit aus, um die Seite „IoT Edge-Gerät“ zu öffnen.

1. Wählen Sie auf der Registerkarte **Module** die Option **azureeyemodule** aus.

1. Öffnen Sie auf der Seite **azureeyemodule** die Option **Zwilling der Modulkennung**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Screenshot: Modulseite" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Scrollen Sie nach unten zu **Eigenschaften**.
1. Suchen Sie nach **TelemetryInterval**, und ersetzen Sie das Element durch **TelemetryIntervalNeuralNetworkMs**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline-02.png" alt-text="Screenshot: Eigenschaften von „Zwilling der Modulkennung“" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Aktualisieren Sie den Wert für **TelemetryIntervalNeuralNetworkMs** auf den erforderlichen Wert.

1. Wählen Sie das Symbol **Speichern** aus.

## <a name="view-device-rtsp-video-stream"></a>Anzeigen des Geräte-RTSP-Videostreams

Zeigen Sie den RTSP-Videostream Ihres Geräts über [Azure Percept Studio](./how-to-view-video-stream.md) oder [VLC Media Player](https://www.videolan.org/vlc/index.html) an.

Navigieren Sie zum Öffnen des RTSP-Stream in VLC Media Player zu **Medien** > **Netzwerkstream öffnen** > **rtsp://[Geräte-IP-Adresse]:8554/result**.

Wenn Ihr RTSP-Stream teilweise durch ein graues Kästchen blockiert wird, versuchen Sie möglicherweise, ihn über eine schlechte Netzwerkverbindung anzuzeigen. Überprüfen Sie, ob die Bandbreite Ihrer Verbindung für Videostreams ausreichend ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung der Azure Percept DK-Instanz finden Sie unter [Azure Percept DK-Fehlerbehebung](./troubleshoot-dev-kit.md).

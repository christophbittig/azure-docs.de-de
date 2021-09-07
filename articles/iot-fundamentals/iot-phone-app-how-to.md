---
title: Verwenden Ihres Smartphones als Azure IoT-Gerät
description: In dieser Schrittanleitung erfahren Sie, wie Sie mithilfe der Azure IoT Plug & Play-App aus Ihrem Smartphone ein IoT-Gerät machen.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112305968"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>Verwenden Ihres Smartphones als IoT-Gerät

Eine Azure IoT-Lösung ermöglicht es Ihnen, Ihre IoT-Geräte mit einem cloudbasierten IoT-Dienst zu verbinden. Geräte senden Telemetriedaten wie Temperatur und Luftfeuchtigkeit und reagieren auf Befehle, z. B. zum Neustarten und Ändern des Übermittlungsintervalls. Geräte können auch ihren internen Zustand mit dem Dienst synchronisieren und Eigenschaften wie Gerätemodell und Betriebssystem teilen.

Mit der IoT Plug & Play-Smartphone-App können Sie in kürzester Zeit mit der Erkundung der Azure IoT-Funktionen beginnen, ohne ein dediziertes IoT-Gerät konfigurieren zu müssen.

## <a name="azure-iot-plug-and-play-app"></a>Azure IoT Plug & Play-App

Damit Sie schnell loslegen können, wird in diesem Artikel eine Smartphone-App als IoT-Gerät verwendet. Die App sendet Telemetriedaten, die von den Sensoren des Smartphones gesammelt wurden, reagiert auf Befehle, die über den Dienst aufgerufen werden, und meldet Eigenschaftswerte.

Sie können diese Smartphone-App für folgende Zwecke verwenden:

- Erkunden eines einfachen IoT-Szenarios
- Verwalten Ihres Smartphones und Interagieren mit dem Smartphone per Remotezugriff
- Testen Ihrer Konfiguration
- Als Ausgangspunkt für die Entwicklung benutzerdefinierter Geräte

## <a name="install-the-app"></a>Installieren der App

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>App-Features

### <a name="connect"></a>Verbinden

Sie können eine Verbindung mit einer IoT Central-Anwendung herstellen, indem Sie einen QR-Code in IoT Central scannen.

Informationen dazu finden Sie unter [Verbinden der App](#connect-the-app) weiter unten in diesem Leitfaden.

### <a name="telemetry"></a>Telemetrie

Die App sammelt Daten von Sensoren auf dem Smartphone, die als Telemetriedaten an den von Ihnen verwendeten IoT-Dienst gesendet werden. Sensordaten werden standardmäßig alle fünf Sekunden aggregiert. Sie können das Intervall jedoch auf der Seite mit den App-Einstellungen ändern:

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="Screenshot der Telemetrieseite in der Smartphone-App":::

Der folgende Screenshot zeigt eine Geräteansicht in IoT Central, in der einige der Gerätetelemetriedaten angezeigt werden:

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="Screenshot der Gerätetelemetrie in IoT Central":::

### <a name="properties"></a>Eigenschaften

Die App meldet den Gerätestatus, z. B. das Gerätemodell und den Hersteller. Zudem ist eine bearbeitbare Eigenschaft verfügbar, die Sie ändern können. In Ihrer Azure IoT-Lösung sehen Sie dann, dass die Änderung synchronisiert wurde:

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="Screenshot der Eigenschaftenseite in der App für mobile Geräte":::

Der folgende Screenshot zeigt die beschreibbare Eigenschaft in IoT Central, nachdem sie an das Gerät gesendet wurde:

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="Screenshot der beschreibbaren Eigenschaft in IoT Central":::

### <a name="image-upload"></a>Hochladen von Bildern

Sowohl IoT Central als auch IoT Hub ermöglichen es Ihnen, Dateien von einem Gerät in Azure Storage hochzuladen. Mit der Smartphone-App können Sie ein Bild vom Gerät hochladen.

Weitere Informationen zum Konfigurieren des Diensts zur Unterstützung von Dateiuploads von einem Gerät finden Sie in den folgenden Artikeln:

- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub](../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Central](../iot-central/core/howto-configure-file-uploads.md)

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="Screenshot der Seite zum Hochladen von Bildern in der Smartphone-App":::

### <a name="logs"></a>Protokolle

Die Smartphone-App schreibt Ereignisse in eine lokale Protokolldatei, die Sie in der App anzeigen können. Verwenden Sie die Protokolldatei, um Probleme zu beheben und die von der App ausgeführten Vorgänge besser zu verstehen:

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="Screenshot der Protokollseite in der Smartphone-App":::

### <a name="settings"></a>Einstellungen

Auf der Einstellungsseite in der App können Sie:

- Die App mit Ihrer Azure IoT-Lösung verbinden
- Die aktuellen Informationen zur Geräteregistrierung überprüfen
- Die App zurücksetzen, indem Sie die gespeicherten Daten löschen
- Die Darstellung der App anpassen
- Die Häufigkeit festlegen, mit der die App Telemetriedaten an Ihren IoT-Dienst sendet

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="Screenshot der Einstellungsseite in der Smartphone-App":::

## <a name="connect-the-app"></a>Verbinden der App

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

<!-- To do: does this need an app template? -->
Erstellen Sie eine IoT Central-Anwendung. Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../iot-central/core/howto-create-iot-central-application.md).

### <a name="register-a-device"></a>Registrieren eines Geräts

Bevor Sie die Smartphone-App verbinden, müssen Sie ein Gerät in Ihrer IoT Central-Anwendung registrieren. Wenn Sie eine Geräteregistrierung erstellen, generiert IoT Central die Geräteverbindungsinformationen.

So registrieren Sie das Gerät in IoT Central:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an, und navigieren Sie zur Seite **Geräte**.

1. Wählen Sie **Gerät erstellen** aus.

1. Wählen Sie auf der Seite **Neues Gerät erstellen** die Option **Erstellen** aus:

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="Screenshot der Erstellung eines Geräts in IoT Central":::

1. Klicken Sie in der Liste der Geräte auf den Gerätenamen, und wählen Sie dann **Verbinden** aus. Auf der Seite **Geräteverbindung** sehen Sie den QR-Code, den Sie in der Smartphone-App scannen:

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="Screenshot der Seite „Geräteverbindung“ mit dem QR-Code":::

### <a name="connect-the-device"></a>Verbinden des Geräts

Nachdem Sie das Gerät in IoT Central registriert haben, können Sie die Smartphone-App verbinden, indem Sie den QR-Code scannen. Gehen Sie wie folgt vor, um die App zu verbinden:

1. Öffnen Sie die App **IoT PnP** auf Ihrem Smartphone.

1. Wählen Sie auf der Willkommensseite **Scan QR code** (QR-Code scannen) aus. Erfassen Sie mit der Smartphonekamera den QR-Code. Warten Sie dann einige Sekunden, bis die Verbindung hergestellt wurde.

1. Auf der Telemetrieseite in der App werden die Daten angezeigt, die die App an IoT Central sendet. Auf der Protokollseite wird angezeigt, dass das Gerät eine Verbindung herstellt, und es sind mehrere Initialisierungsmeldungen zu sehen.

1. Auf der Seite **Einstellungen > Registrierung** werden die Geräte-ID und der ID-Bereich angezeigt, mit denen die App die Verbindung mit IoT Central hergestellt hat.

Weitere Informationen dazu, wie Geräte eine Verbindung mit IoT Central herstellen, finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](../iot-central/core/concepts-get-connected.md).

### <a name="verify-the-connection"></a>Überprüfen der Verbindung

So zeigen Sie die Daten an, die das Gerät in Ihrer IoT Central-Anwendung sendet:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an, und navigieren Sie zur Seite **Geräte**. Ihr Gerät wurde automatisch der Gerätevorlage **Smartphone** zugewiesen.

    > [!TIP]
    > Möglicherweise müssen Sie die Seite in Ihrem Webbrowser aktualisieren, um zu sehen, dass das Gerät der Gerätevorlage **Smartphone** zugewiesen ist.

1. Klicken Sie in der Liste der Geräte auf den Gerätenamen, und wählen Sie dann **Übersicht** aus. Auf der Seite **Übersicht** werden die Telemetriedaten von den Smartphonesensoren angezeigt:

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="Screenshot der Übersichtsseite des Geräts in IoT Central, auf der die Telemetriedaten der Smartphonesensoren angezeigt werden":::

1. Öffnen Sie die Seite **Info**, um die vom Gerät gesendeten Eigenschaften anzuzeigen.

1. Führen Sie auf der Seite **Befehle** den Befehl **LightOn** aus, um die Taschenlampe des Smartphones zu aktivieren.

> [!TIP]
> Auf der Seite **Rohdaten** werden alle vom Gerät gesendeten Daten angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Smartphone-App nun mit IoT Central verbunden haben, besteht der empfohlene nächste Schritt darin, sich weiter über [IoT Central](../iot-central/core/overview-iot-central.md) zu informieren.

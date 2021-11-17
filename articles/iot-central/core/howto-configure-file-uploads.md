---
title: Hochladen von Dateien von Geräten nach Azure Storage| Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Dateiuploads von ihren Geräten in die Cloud konfigurieren. Nachdem Sie Dateiuploads konfiguriert haben, implementieren Sie Dateiuploads auf Ihren Geräten.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2021
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 57ee46a9ba0283def1e1c2d3e2fb71bdf44ec08c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486354"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Hochladen von Dateien von Geräten in die Cloud

Mit IoT Central können Sie Medien und andere Dateien von verbundenen Geräten in den Cloudspeicher hochladen. Sie konfigurieren die Dateiuploadfunktion in Ihrer IoT Central Anwendung und implementieren dann Dateiuploads in Ihrem Gerätecode.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen Administrator in Ihrer IoT Central-Anwendung sein, um Dateiuploads konfigurieren zu können.

Sie benötigen ein Azure Storage-Konto und einen Container zum Speichern der hochgeladenen Dateien. Wenn Sie über kein Azure Storage-Konto und keinen Container verfügen, erstellen Sie [im Azure-Portal ein neues Speicherkonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Konfigurieren von Gerätedateiuploads

So konfigurieren Sie Uploads von Gerätedateien

1. Navigieren Sie in Ihrer Anwendung zum Abschnitt **Verwaltung**.

1. Wählen Sie **Upload von Gerätedateien** aus.

1. Wählen Sie das Speicherkonto und den Container aus, die Sie verwenden möchten. Wenn sich das Speicherkonto in einem anderen Azure-Abonnement als Ihre Anwendung befindet, geben Sie eine Verbindungszeichenfolge für das Speicherkonto ein.

1. Passen Sie ggf. den Uploadtimeout an, der festlegt, wie lange eine Uploadanforderung gültig bleibt. Gültige Werte liegen zwischen 1 und 24.

1. Wählen Sie **Speichern** aus. Wenn der Status **Konfiguriert** angezeigt wird, können Sie Dateien von Geräten hochladen.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Konfigurieren von Dateiuploads in der Anwendung":::

## <a name="disable-device-file-uploads"></a>Deaktivieren von Gerätedateiuploads

Wenn Sie das Hochladen von Gerätedateien in Ihre IoT Central-Anwendung deaktivieren möchten:

1. Navigieren Sie in Ihrer Anwendung zum Abschnitt **Verwaltung**.

1. Wählen Sie **Upload von Gerätedateien** aus.

1. Klicken Sie auf **Löschen**.

## <a name="upload-a-file-from-a-device"></a>Hochladen einer Datei von einem Gerät

IoT Central verwendet die IoT Hub-Funktion zum Hochladen von Dateien, um das Hochladen von Dateien von Geräten zu ermöglichen. Beispielcode, der zeigt, wie Sie Dateien von einem Gerät hochladen können, finden Sie im [Beispiel für einen IoT Central-Dateiupload von einem Gerät](/samples/azure-samples/iot-central-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie Gerätedateiuploads in IoT Central konfigurieren und implementieren, sollten Sie sich als Nächstes näher mit Gerätedateiuploads vertraut machen:

- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)

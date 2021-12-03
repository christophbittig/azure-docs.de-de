---
title: Konfigurieren des Dateiuploads im Azure-Portal | Microsoft Docs
description: Konfigurieren des IoT Hubs im Azure-Portal zum Aktivieren von Dateiuploads von verbundenen Geräten. Enthält Informationen zum Konfigurieren des Azure-Zielspeicherkontos.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.openlocfilehash: 5871ffdc16fe7c3e66c6915c72198250823d817e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555279"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurieren des Dateiuploads in IoT Hub im Azure-Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

In diesem Artikel erfahren Sie, wie Sie Dateiuploads in Ihrem IoT-Hub im Azure-Portal konfigurieren. 

Damit Sie die [Dateiuploadfunktion in IoT Hub](iot-hub-devguide-file-upload.md) nutzen können, müssen Sie Ihrem IoT-Hub zuerst ein Azure-Speicherkonto und einen Blobcontainer zuordnen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können. Zusätzlich zum Speicherkonto und Blobcontainer können Sie die Gültigkeitsdauer (TTL) für den SAS-URI und den Authentifizierungstyp festlegen, der von IoT Hub für Azure Storage verwendet wird. Sie können auch Einstellungen für die optionalen Dateiuploadbenachrichtigungen konfigurieren, die IoT Hub an Back-End-Dienste übermitteln kann.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Einen Azure IoT Hub. Wenn Sie keinen IoT-Hub haben, lesen Sie [Erstellen eines IoT-Hubs über das Portal](iot-hub-create-through-portal.md).

## <a name="configure-your-iot-hub"></a>Konfigurieren Ihres IoT-Hubs

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT-Hub, und wählen Sie **Dateiupload** aus, um die Dateiuploadeigenschaften anzuzeigen. Wählen Sie dann unter **Einstellungen für Speichercontainer** die Option **Azure Storage-Container** aus.

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-settings.png" alt-text="Screenshot: Konfigurieren von Dateiuploadeinstellungen im Portal.":::

1. Wählen Sie in Ihrem aktuellen Abonnement ein Azure Storage-Konto und einen Blobcontainer aus, das bzw. der Ihrem IoT-Hub zugeordnet werden soll. Bei Bedarf können Sie im Bereich **Speicherkonten** ein Azure Storage-Konto und im Bereich **Container** einen Blobcontainer erstellen.

   :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-container-selection.png" alt-text="Screenshot: Anzeigen von Speichercontainern für den Dateiupload.":::

1. Nachdem Sie ein Azure Storage-Konto und einen Blobcontainer ausgewählt haben, konfigurieren Sie die restlichen Dateiuploadeigenschaften.

    * **Benachrichtigungen für hochgeladene Dateien empfangen**: Aktivieren oder deaktivieren Sie Dateiuploadbenachrichtigungen über den Umschalter.

    * **SAS-TTL**: Diese Einstellung dient zum Festlegen der Gültigkeitsdauer der SAS-URIs, die von IoT Hub an das Gerät zurückgegeben werden. Standardmäßig auf 1 Stunde festgelegt, kann aber mithilfe des Schiebereglers in andere Werte geändert werden.

    * **Standard-TTL für Dateibenachrichtigungseinstellungen**: Die Gültigkeitsdauer einer Dateiuploadbenachrichtigung (Zeit bis zum Ablauf). Standardmäßig auf 1 Tag festgelegt, kann aber mithilfe des Schiebereglers in andere Werte geändert werden.

    * **Anzahl maximaler Zustellungen für Dateibenachrichtigungen**: Gibt an, wie oft IoT Hub versucht, eine Dateiuploadbenachrichtigung zu senden. Standardmäßig auf 10 festgelegt, kann aber mithilfe des Schiebereglers in andere Werte geändert werden.

    * **Authentifizierungstyp**: Standardmäßig verwendet Azure IoT Hub schlüsselbasierte Authentifizierung zum Herstellen einer Verbindung mit und Autorisieren bei Azure Storage. Sie können auch benutzerseitig oder systemseitig zugewiesene verwaltete Identitäten konfigurieren, um Azure IoT Hub bei Azure Storage zu authentifizieren. Verwaltete Identitäten stellen Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD auf sichere Weise bereit. Wenn Sie erfahren möchten, wie verwaltete Identitäten konfiguriert werden, lesen Sie [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md). Nachdem Sie eine oder mehrere verwaltete Identität(en) in Ihrem Azure Storage-Konto und IoT-Hub konfiguriert haben, können Sie eine davon zur Authentifizierung bei Azure Storage mit der Schaltfläche **System-assigned** (Systemseitig zugewiesen) oder **User-assigned** (Benutzerseitig zugewiesen) auswählen.

        > [!NOTE]
        > Mit der Einstellung für den Authentifizierungstyp wird konfiguriert, wie sich Ihr IoT-Hub bei Ihrem Azure Storage-Konto authentifiziert. Geräte authentifizieren sich bei Azure Storage immer mithilfe des SAS-URI, den sie vom IoT-Hub abrufen. 

1. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern. Überprüfen Sie unbedingt die Bestätigung auf einen erfolgreichen Abschluss. Einige Auswahloptionen, z. B. **Authentifizierungstyp**, werden erst überprüft, nachdem Sie Ihre Einstellungen gespeichert haben. 

## <a name="next-steps"></a>Nächste Schritte

* [Hochladen von Dateien von einem Gerät – Übersicht](iot-hub-devguide-file-upload.md)
* [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md)
* [Anleitungen zum Dateiupload](./iot-hub-csharp-csharp-file-upload.md)

---
title: include file
description: include file
services: iot-hub
author: JimacoMS3
ms.service: iot-hub
ms.topic: include
ms.date: 01/22/2019
ms.author: v-jbrannian
ms.custom: include file
ms.openlocfilehash: b4f390fe573e732bb01dde6ef2a63a6164d7f6e5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287384"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Zuweisen eines Azure Storage-Kontos zu IoT Hub

Wenn Sie Dateien von einem Gerät hochladen möchten, müssen Sie ein Azure Storage-Konto und einen Azure Blob Storage-Container haben, die Ihrem IoT-Hub zugeordnet wurden. Sobald Sie das Speicherkonto und den Container Ihrem IoT-Hub zugeordnet haben, kann der IoT-Hub die Elemente eines SAS-URI bereitstellen, wenn sie von einem Gerät angefordert werden. Das Gerät kann dann mithilfe dieser Elemente den SAS-URI erstellen, den es zum Authentifizieren bei Azure Storage und zum Hochladen von Dateien in den Blobcontainer verwendet.

So ordnen Sie Ihrem IoT-Hub ein Azure Storage-Konto zu:

1. Wählen Sie im linken Bereich des IoT-Hubs unter **Hubeinstellungen** die Option **Dateiupload** aus.

    :::image type="content" source="./media/iot-hub-include-associate-storage/select-storage.png" alt-text="Screenshot: Auswahl von Einstellungen für den Dateiupload im Portal" border="true":::

1. Wählen Sie im Bereich **Dateiupload** die Option **Azure Storage-Container** aus. Für diesen Artikel wird empfohlen, dass sich Ihr Speicherkonto und IoT Hub in derselben Region befinden.
    * Wenn Sie bereits ein Speicherkonto haben, das Sie nutzen möchten, wählen Sie es in der Liste aus.

    * Wählen Sie zum Erstellen eines neuen Speicherkontos **+ Speicherkonto** aus. Geben Sie einen Namen für das Speicherkonto an, und stellen Sie sicher, dass **Speicherort** auf dieselbe Region wie Ihr IoT-Hub festgelegt wird. Wählen Sie dann **OK** aus. Das neue Konto wird in derselben Ressourcengruppe wie Ihr IoT-Hub erstellt. Wählen Sie nach Abschluss der Bereitstellung das Speicherkonto in der Liste aus.

    Nachdem Sie das Speicherkonto ausgewählt haben, wird der Bereich **Container** geöffnet.

1. Wählen Sie im Bereich **Container** den Blobcontainer aus.
    * Wenn Sie bereits einen Blobcontainer haben, den Sie nutzen möchten, wählen Sie ihn in der Liste aus, und klicken Sie auf **Auswählen**.

    * Wählen Sie dann **+ Container** aus, um einen neuen Blobcontainer zu erstellen. Geben Sie einen Namen für den neuen Container an. Für die Zwecke dieses Artikels können Sie bei allen anderen Feldern die Standardwerte beibehalten. Klicken Sie auf **Erstellen**. Wählen Sie nach Abschluss der Bereitstellung den Container in der Liste aus, und klicken Sie auf **Auswählen**.

1. Vergewissern Sie sich im Bereich **Dateiupload**, dass für Dateibenachrichtigungen **On** (Aktiviert) festgelegt wurde. Bei allen anderen Einstellungen können Sie die Standardwerte beibehalten. Wählen Sie **Speichern** aus, und warten Sie, bis die Einstellungen abgeschlossen sind, bevor Sie mit dem nächsten Abschnitt weitermachen.

    :::image type="content" source="./media/iot-hub-include-associate-storage/file-upload-settings-small.png" alt-text="Screenshot: Einstellungen zum Bestätigen des Dateiuploads im Portal" border="true":::

Ausführlichere Anleitungen zum Erstellen eines Azure Storage-Kontos finden Sie unter [Erstellen eines Speicherkontos](../articles/storage/common/storage-account-create.md). Ausführlichere Anleitungen zum Zuordnen eines Speicherkontos und Blobcontainers zu einem IoT-Hub finden Sie unter [Konfigurieren des Dateiuploads im Azure-Portal](../articles/iot-hub/iot-hub-configure-file-upload.md).

---
title: Datei einfügen
description: include file
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 78ad23713d1fc58d55696502dc9ff780847a8357
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414876"
---
<!-- put the ## header in the file that includes this file -->

In diesem Abschnitt erstellen Sie eine Geräteidentität in der Identitätsregistrierung im IoT Hub. Ein Gerät kann eine Verbindung mit einem Hub nur dann herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. Öffnen Sie in Ihrem IoT Hub-Navigationsmenü die Option **IoT-Geräte**, und wählen Sie **Neu** aus, um in Ihrem IoT-Hub ein Gerät hinzuzufügen.

    ![Erstellen der Geräteidentität im Portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Geben Sie in **Gerät erstellen** ein Namen für Ihr neues Gerät ein, z.B. **myDeviceId**, und wählen Sie **Speichern** aus. Mit dieser Aktion wird eine Geräteidentität für Ihren IoT-Hub erstellt. Lassen Sie **Schlüssel automatisch generieren** aktiviert, damit die primären und sekundären Schlüssel automatisch generiert werden.

   ![Hinzufügen eines neuen Geräts](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Öffnen Sie das Gerät nach der Erstellung in der Liste im Bereich **IoT-Geräte**. Kopieren Sie die **primäre Verbindungszeichenfolge**. Diese Verbindungszeichenfolge wird vom Gerätecode verwendet, um mit dem Hub zu kommunizieren. 

    Standardmäßig werden die Schlüssel und Verbindungszeichenfolgen maskiert, da es sich hierbei um vertrauliche Informationen handelt. Wenn Sie auf das Augensymbol klicken, werden sie wie in der folgenden Abbildung angezeigt. Sie können allerdings über die Kopierschaltfläche kopiert werden, ohne die Maskierung aufzuheben.

    ![Geräte-Verbindungszeichenfolge](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den IoT-Hub zu ermöglichen. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

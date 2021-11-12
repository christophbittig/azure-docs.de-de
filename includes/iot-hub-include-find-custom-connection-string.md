---
title: include file
description: include file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: fc2dd8938f627be669519b843a97b87ddf1e3203
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288397"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Gehen Sie wie folgt vor, um eine SAS-Richtlinie zu erstellen, die die Berechtigungen **Dienstverbindung** und **Lesevorgänge in Registrierung** gewährt, und eine Verbindungszeichenfolge für diese Richtlinie abzurufen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** aus. Wählen Sie die Ressourcengruppe aus, in der sich der Hub befindet, und wählen Sie dann in der Liste der Ressourcen Ihren Hub aus.

1. Wählen Sie im linken Bereich Ihres Hubs **SAS-Richtlinien** aus.

1. Wählen Sie im Menü über der Richtlinienliste die Option **Hinzufügen** aus.

1. Geben Sie unter **Richtlinie für den gemeinsamen Zugriff hinzufügen** einen aussagekräftigen Namen für Ihre Richtlinie ein (z. B. *serviceAndRegistryRead*). Wählen Sie unter **Berechtigungen** die Berechtigungen **Lesevorgänge in Registrierung** und **Dienstverbindung** aus, und klicken Sie anschließend auf **Hinzufügen**.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png" alt-text="Screenshot: Hinzufügen einer neuen Richtlinie für gemeinsamen Zugriff" border="true":::

1. Wählen Sie Ihre neue Richtlinie aus der Liste der Richtlinien aus.

1. Wählen Sie das Kopiersymbol für **Primäre Verbindungszeichenfolge** aus, und speichern Sie den Wert.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png" alt-text="Screenshot: Abrufen der Verbindungszeichenfolge" border="true":::

Weitere Informationen zu SAS-Richtlinien und Berechtigungen für IoT-Hubs finden Sie unter [Access Control und Berechtigungen](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).

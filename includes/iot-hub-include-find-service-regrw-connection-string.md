---
title: include file
description: include file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9d5710e78b323af65a12b1f92095cdfa1f385ecb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287897"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Wenn Sie eine SAS-Richtlinie, die die Berechtigungen **Dienstverbindung** und **Schreibvorgänge in Registrierung** gewährt, und eine Verbindungszeichenfolge für diese Richtlinie abrufen möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** aus. Wählen Sie die Ressourcengruppe aus, in der sich der Hub befindet, und wählen Sie dann in der Liste der Ressourcen Ihren Hub aus.

1. Wählen Sie im linken Bereich Ihres Hubs **SAS-Richtlinien** aus.

1. Wählen Sie im Menü über der Richtlinienliste die Option **SAS-Richtlinie hinzufügen** aus.

1. Geben Sie unter **Richtlinie für den gemeinsamen Zugriff hinzufügen** einen aussagekräftigen Namen für Ihre Richtlinie ein (z. B. *serviceAndRegistryReadWrite*). Wählen Sie unter **Berechtigungen** die Berechtigungen **Schreibvorgänge in Registrierung** und **Dienstverbindung** und anschließend **Hinzufügen** aus. (Die Berechtigung **Lesevorgänge in Registrierung** wird automatisch einbezogen, wenn Sie **Schreibvorgänge in Registrierung** auswählen.)

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png" alt-text="Screenshot: Hinzufügen einer neuen SAS-Richtlinie" border="true":::

1. Wählen Sie Ihre neue Richtlinie aus der Liste der Richtlinien aus.

1. Wählen Sie unter **Schlüssel für gemeinsamen Zugriff** das Kopiersymbol für **Primäre Verbindungszeichenfolge** aus, und speichern Sie den Wert.

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png" alt-text="Screenshot: Abrufen der Verbindungszeichenfolge" border="true":::

Weitere Informationen zu SAS-Richtlinien und Berechtigungen für IoT-Hubs finden Sie unter [Access Control und Berechtigungen](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).

---
title: include file
description: include file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: ce13f1cdd3effdd08bfa76a996587edc213c5701
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288444"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Führen Sie zum Abrufen der IoT-Hub-Verbindungszeichenfolge für die Richtlinie **registryReadWrite** die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** aus. Wählen Sie die Ressourcengruppe aus, in der sich der Hub befindet, und wählen Sie dann in der Liste der Ressourcen Ihren Hub aus.

2. Wählen Sie im linken Bereich Ihres Hubs **SAS-Richtlinien** aus.

3. Wählen Sie in der Liste der Richtlinien die Richtlinie **registryRead Write** aus.

4. Wählen Sie unter **Schlüssel für gemeinsamen Zugriff** das Kopiersymbol für **Primäre Verbindungszeichenfolge** aus, und speichern Sie den Wert.

    :::image type="content" source="./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png" alt-text="Screenshot: Abrufen der Verbindungszeichenfolge" border="true":::

Weitere Informationen zu SAS-Richtlinien und Berechtigungen für IoT-Hubs finden Sie unter [Access Control und Berechtigungen](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).

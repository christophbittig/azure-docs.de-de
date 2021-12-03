---
title: So verwenden Sie das Azure-Portal, um direkte Methoden für den Azure Video Analyzer aufzurufen
description: Dieser Artikel vermittelt einen Überblick über die Verwendung des Azure-Portals zum Aufrufen direkter Methoden für den Azure Video Analyzer.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e6b2db32db398bfbc5c5431c49db1d2030f5a99e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564041"
---
# <a name="use-azure-portal-to-invoke-direct-methods-for-azure-video-analyzer"></a>Verwenden Sie das Azure-Portal, um direkte Methoden für den Azure Video Analyzer aufzurufen

[!INCLUDE [header](includes/edge-env.md)]

IoT Hub gibt Ihnen die Möglichkeit, [direkte Methoden](../../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) auf Edgegeräten von der Cloud aus aufzurufen. Das Azure Video Analyzer-Modul (Video Analyzer) stellt mehrere [direkte Methoden](./direct-methods.md) zur Verfügung, mit denen verschiedene Pipelines für die Analyse von Live-Video definiert, bereitgestellt und aktiviert werden können.

In diesem Artikel erfahren Sie, wie Sie direkte Methodenaufrufe in Live Video Analytics für ein IoT Edge-Modul über das Azure-Portal aufrufen können.

## <a name="prerequisites"></a>Voraussetzungen

* Das Modul Video Analyzer läuft auf Ihrem Edge-Gerät, entweder mit den Methoden, die in [Schnellstart: Video Analyzer](get-started-detect-motion-emit-events.md) oder anhand des [Portals](./deploy-iot-edge-device.md) beschrieben sind.
* Sie sind mit dem [Video Analyzer](../overview.md) und dem [Pipeline-Konzept](../pipeline.md) vertraut.

## <a name="invoking-direct-methods-via-azure-portal"></a>Aufrufen von direkten Methoden über das Azure-Portal

Jede der [direkten Methoden](./direct-methods.md), die vom Video Analyzer-Modul bereitgestellt werden, kann über das Azure-Portal aufgerufen werden. Die folgenden Schritte enthalten die Details für eine direkte Methode. Sie können andere direkte Methoden mit ähnlichen Schritten aufrufen. Für jede direkte Methode ist jedoch eine bestimmte Nutzlast erforderlich.

Verwenden Sie den `livePipelineList`-Methodenaufruf, um eine Liste aller Pipeline-Topologien abzurufen, die derzeit auf dem Video Analyzer-Modul bereitgestellt werden. Verwenden Sie die folgenden Schritte, um diese direkte Methode aufzurufen:

1. Anmelden im Azure-Portal
1. Finden Sie die relevante Ressourcengruppe auf der Startseite Ihres Portals, um Ihren IoT Hub zu finden, oder wenn Sie Ihren IoT Hub kennen, wählen Sie ihn aus.
    ![Ressourcengruppe auf der Startseite des Portals](./media/use-azure-portal-to-invoke-direct-methods/portal-rg-home.png)
1. Wählen Sie auf der IoT Hub-Seite unter „Automatische Geräteverwaltung“ die Option „IoT Edge“ aus, um die verschiedenen Geräte-IDs aufzulisten. Wählen Sie die entsprechende Geräte-ID aus, um die auf dem Gerät ausgeführten Module aufzulisten.
    ![IoT Hub-Seite](./media/use-azure-portal-to-invoke-direct-methods/iot-hub-page.png)
1. Wählen Sie das Video Analyzer-Modul aus, um dessen Konfigurationsseite aufzurufen.<br><br>
    ![Wählen Sie das Video Analyzer-Modul aus, um dessen Konfigurationsseite aufzurufen](./media/use-azure-portal-to-invoke-direct-methods/modules.png)
1. Wählen Sie die Menüoption „Direkte Methode“ aus. <br><br>
    ![Klicken Sie auf die Menüoption „Direkte Methode“.](./media/use-azure-portal-to-invoke-direct-methods/module-details.png)
    > [!NOTE]
    > Sie müssen in den Abschnitten für die Verbindungszeichenfolge möglicherweise einen Wert hinzufügen, wie Sie auf der aktuellen Seite sehen können. Sie brauchen im Abschnitt „Einstellungsname“ nichts auszublenden oder zu ändern. Es ist in Ordnung, diese Informationen öffentlich zu belassen.

1. Geben Sie *livePipelineList* in das Feld **Methodenname** ein.
1. Kopieren Sie den folgenden JSON-Code in das Feld für die **Nutzlast**.
    ```json
    {
    "@apiVersion": "1.1"
    }
    ```
1. Wählen Sie die Schaltfläche **Methode aufrufen** oben auf der Seite aus.<br><br>
    ![Schaltfläche „Methode aufrufen“](./media/use-azure-portal-to-invoke-direct-methods/direct-method.png)
1. Im **Ergebnisbereich** sollte eine 200-Statusmeldung angezeigt werden.<br><br>
    ![Verbindungstimeout](./media/use-azure-portal-to-invoke-direct-methods/connection-timeout.png)

## <a name="responses"></a>Antworten

| Bedingung             | Statuscode | Detaillierter Fehlercode |
|-----------------------|-------------|---------------------|
| Erfolg               | 200         | –                 |
| Allgemeine Benutzerfehler   | 400er Bereich   |                     |
| Allgemeine Serverfehler | 500er Bereich   |                     |

## <a name="next-steps"></a>Nächste Schritte

Weitere direkte Methoden finden Sie auf der Seite [Direkte Methoden](./direct-methods.md).

> [!NOTE]
> Eine Pipeline aktiviert eine bestimmte Topologie. Stellen Sie daher sicher, dass Sie die richtige Topologie eingestellt haben, bevor Sie eine Pipeline aktivieren.

[Schnellstart: Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) ist eine geeignete Referenz, um die genaue Abfolge direkter Methodenaufrufe zu verstehen.

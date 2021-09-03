---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: eacee8b325317e98cd05093b71a21889f9a933ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346679"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

[Consumergruppen](../articles/event-hubs/event-hubs-features.md#event-consumers) bieten unabhängige Ansichten des Ereignisstreams, die es Apps und Azure-Diensten ermöglichen, unabhängig voneinander Daten desselben Event Hub-Endpunkts zu nutzen. In diesem Abschnitt fügen Sie dem integrierten Endpunkt Ihres IoT-Hubs eine Consumergruppe hinzu, die später in diesem Tutorial zum Pullen von Daten vom Endpunkt verwendet wird.

Gehen Sie folgendermaßen vor, um Ihrem IoT Hub eine Consumergruppe hinzuzufügen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihren IoT-Hub.

1. Wählen Sie im linken Bereich **Integrierte Endpunkte** aus. Geben Sie im Textfeld unter **Consumergruppen** einen Namen für Ihre neue Consumergruppe ein. 

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="Erstellen einer Consumergruppe in Ihrem IoT Hub":::

1. Klicken Sie auf eine beliebige Stelle außerhalb des Textfelds, um die Consumergruppe zu speichern.
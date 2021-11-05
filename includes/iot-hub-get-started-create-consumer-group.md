---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: 090a7ae8e053b82e157d1e46e740d9b3911fef99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077585"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

[Consumergruppen](../articles/event-hubs/event-hubs-features.md#event-consumers) bieten unabhängige Ansichten des Ereignisstreams, die es Apps und Azure-Diensten ermöglichen, unabhängig voneinander Daten desselben Event Hub-Endpunkts zu nutzen. In diesem Abschnitt fügen Sie dem integrierten Endpunkt Ihres IoT-Hubs eine Consumergruppe hinzu, die später in diesem Tutorial zum Pullen von Daten vom Endpunkt verwendet wird.

Gehen Sie folgendermaßen vor, um Ihrem IoT Hub eine Consumergruppe hinzuzufügen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihren IoT-Hub.

1. Wählen Sie im linken Bereich **Integrierte Endpunkte** aus. Geben Sie im Textfeld unter **Consumergruppen** einen Namen für Ihre neue Consumergruppe ein.

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="Screenshot: Erstellen einer Consumergruppe in Ihrem IoT-Hub" border="true":::

1. Klicken Sie auf eine beliebige Stelle außerhalb des Textfelds, um die Consumergruppe zu speichern.
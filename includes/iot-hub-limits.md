---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 526a1bf1c39ad503a2f0585999247a8e91b4c727
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529740"
---
In der folgenden Tabelle sind die Grenzwerte aufgeführt, die den verschiedenen Dienstebenen S1, S2, S3 und F1 zugeordnet sind. Informationen zu den Kosten jeder *Einheit* finden Sie unter [Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Nachrichten/Tag |400.000 |6\.000.000 |300.000.000 |8\.000 |
| Maximale Anzahl der Einheiten |200 |200 |10 |1 |

> [!NOTE]
> Wenden Sie sich an den Microsoft-Support, wenn Sie voraussichtlich mehr als 200 Einheiten mit einem Hub im Tarif S1 oder S2 oder zehn Einheiten mit einem Hub im Tarif S3 verwenden.
> 
> 

Die folgende Tabelle enthält die für IoT Hub-Ressourcen geltenden Grenzwerte.

| Resource | Begrenzung |
| --- | --- |
| Maximale Anzahl kostenpflichtiger IoT Hubs pro Azure-Abonnement |50 |
| Maximale Anzahl kostenloser IoT Hubs pro Azure-Abonnement |1 |
| Maximale Anzahl von Zeichen in einer Geräte-ID | 128 |
| Maximale Anzahl von Geräte-Identitäten,<br/> die bei einem einzelnen Aufruf zurückgegeben wird |1\.000 |
| Maximale Beibehaltungsdauer von IoT Hub-Nachrichten für D2C-Nachrichten |7 Tage |
| Maximale Größe einer Nachricht von einem Gerät an die Cloud |256 KB |
| Maximale Größe eines Batches, das vom Gerät an die Cloud gesendet wird |AMQP und HTTP: 256 KB für den gesamten Batch <br/>MQTT: 256 KB für jede Nachricht |
| Maximale Anzahl von Nachrichten im Batch, das vom Gerät an die Cloud gesendet wird |500 |
| Maximale Größe einer Nachricht von der Cloud an das Gerät |64 KB |
| Maximale Gültigkeitsdauer von Nachrichten von der Cloud an das Gerät |2 Tage |
| Maximale Übermittlungsanzahl von Nachrichten von der <br/> Cloud an das Gerät |100 |
| Maximale Warteschlangentiefe von der Cloud an das Gerät pro Gerät |50 |
| Maximale Übermittlungsanzahl von Feedbacknachrichten <br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät |100 |
| Maximale Gültigkeitsdauer von Feedbacknachrichten <br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät |2 Tage |
| [Maximale Größe des Gerätezwillings](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB für den Abschnitt „Tags“ sowie jeweils 32 KB für die Abschnitte „Gewünschte Eigenschaften“ und „Gemeldete Eigenschaften“ |
| Maximale Länge des Zeichenfolgenschlüssels für den Gerätezwilling | 1 KB |
| Maximale Länge des Zeichenfolgenwerts für den Gerätezwilling | 4 KB |
| [Maximale Tiefe des Objekts im Gerätezwilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximale Größe der Nutzlast der direkten Methode | 128 KB |
| Maximale Aufbewahrungsdauer des Auftragsverlaufs | 30 Tage |
| Maximale Anzahl gleichzeitiger Aufträge | 10 (S3), 5 (S2), 1 (S1) |
| Maximale Anzahl zusätzlicher Endpunkte (über die [integrierten Endpunkte](../articles/iot-hub/iot-hub-devguide-endpoints.md) hinaus) | 10 (S1, S2 und S3) |
| Maximale Anzahl von Regeln für die Nachrichtenweiterleitung | 100 (S1, S2 und S3) |
| Maximale Anzahl der gleichzeitig verbundenen Gerätestreams | 50 (nur für S1, S2, S3 und F1) |
| Maximale Gerätestream-Datenübertragung | 300 MB pro Tag (nur für S1, S2, S3 und F1) |

> [!NOTE]
> Wenden Sie sich an den Microsoft-Support, wenn Sie mehr als 50 kostenpflichtige IoT Hubs in einem Azure-Abonnement benötigen.

> [!NOTE]
> Derzeit ist die Gesamtzahl der Geräte und Module, die bei einem einzigen IoT-Hub registriert werden können, auf 1 Mio. begrenzt. Wenn Sie diesen Grenzwert erhöhen möchten, wenden Sie sich an [Microsoft-Support](https://azure.microsoft.com/support/options/).

IoT Hub drosselt Anforderungen, wenn die folgenden Kontingente überschritten werden.

| Drosselung | Wert pro Hub |
| --- | --- |
| Identitätsregistrierungsvorgänge <br/> (Erstellen, Abrufen, Auflisten, Aktualisieren und Löschen), <br/> einzelne Import-/Exportvorgänge oder Massenimport/-export |83,33/Sekunde/Einheit (5.000/Minute/Einheit) (für S3). <br/> 1,67/Sekunde/Einheit (100/Minute/Einheit) (für S1 und S2) |
| Geräteverbindungen |6.000/Sekunde/Einheit (für S3), 120/Sekunde/Einheit (für S2), 12/Sekunde/Einheit (für S1). <br/>Mindestens 100/Sekunde |
| Senden von Nachrichten von Geräten an die Cloud |6.000/Sekunde/Einheit (für S3), 120/Sekunde/Einheit (für S2), 12/Sekunde/Einheit (für S1). <br/>Mindestens 100/Sekunde |
| C2D-Sendevorgänge | 83,33/Sekunde/Einheit (5.000/Minute/Einheit) (für S3), 1,67/Sekunde/Einheit (100/Minute/Einheit) (für S1 und S2). |
| C2D-Empfangsvorgänge |833,33/Sekunde/Einheit (50.000/Minute/Einheit) (für S3), 16,67/Sekunde/Einheit (1.000/Minute/Einheit) (für S1 und S2). |
| Dateiuploadvorgänge |83,33 Dateiuploadinitiierungen/Sekunde/Einheit (5.000/Minute/Einheit) (für S3), 1,67 Dateiuploadinitiierungen/Sekunde/Einheit (100/Minute/Einheit) (für S1 und S2). <br/> 10.000 SAS-URIs können gleichzeitig für ein Azure-Speicherkonto geöffnet sein.<br/> 10 SAS-URIs/Gerät können gleichzeitig geöffnet sein. |
| Direkte Methoden | 24 MB/s/Texteinheit (S3), 480 KB/s/Einheit (S2), 160 KB/s/Einheit (S1).<br/> Basierend auf einer Größe von 8 KB pro Verbrauchseinheit für die Drosselung. |
| Gerätezwilling-Lesevorgänge | 500/Sekunde/Einheit (für S3), maximal 100/Sekunde oder 10/Sekunde/Einheit (für S2), 100/Sekunde/Einheit (für S1) |
| Gerätezwillingsaktualisierungen | 250/Sekunde/Einheit (für S3), maximal 50/Sekunde oder 5/Sekunde/Einheit (für S2), 50/Sekunde/Einheit (für S1) |
| Auftragsvorgänge <br/> (Erstellen, Aktualisieren, Auflisten und Löschen) | 83,33/Sekunde/Einheit (5.000/Minute/Einheit) (für S3), 1,67/Sekunde/Einheit (100/Minute/Einheit) (für S2), 1,67/Sekunde/Einheit (100/Minute/Einheit) (für S1). |
| Durchsatz für Vorgänge vom Typ „Aufträge pro Gerät“ | 50/Sekunde/Einheit (für S3), maximal 10/Sekunde oder 1/Sekunde/Einheit (für S2), 10/Sekunde/Einheit (für S1). |
| Initiierungsrate für Gerätestream | 5 neue Streams/Sek. (nur für S1, S2, S3 und F1). |

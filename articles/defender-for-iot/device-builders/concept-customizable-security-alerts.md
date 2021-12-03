---
title: Benutzerdefinierte Sicherheitswarnungen für IoT Hub
description: Erfahren Sie mehr über anpassbare Sicherheitswarnungen und empfohlene Wartungsvorgänge unter Verwendung der Features und Dienste von Defender für IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 3d72ee2d4cd91d0a922a7af61fbafd2cdb9a349a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318995"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender für IoT Hub: benutzerdefinierte Sicherheitswarnungen

Defender für IoT analysiert Ihre IoT-Lösung ständig mithilfe von Advanced Analytics- und Threat Intelligence-Funktionen, um Sie vor schädlichen Aktivitäten zu warnen.

Erstellen Sie benutzerdefinierte Warnungen basierend auf Ihren Kenntnissen über das erwartete Verhalten der einzelnen Geräte. Diese Warnungen sind die effizientesten Indikatoren für potenzielle Änderungen an Ihrer Organisationsbereitstellung oder -umgebung.

Sie können die Warnungen für Defender für IoT Hub in den folgenden Listen basierend auf dem erwarteten IoT Hub- Verhalten definieren. Weitere Informationen zum Anpassen der einzelnen Warnungen finden Sie unter [Erstellen von benutzerdefinierten Warnungen](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Integrierte benutzerdefinierte Warnungen in IoT Hub

### <a name="low-severity"></a>Niedriger Schweregrad

| Warnungsname | Schweregrad | Datenquelle | BESCHREIBUNG | Warnungstyp |
|--|--|--|--|--|
| Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Anzahl von Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_AmqpC2DMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Anzahl der vom Gerät abgelehnten Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_AmqpC2DRejectedMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Gerät-zu-Cloud-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_AmqpD2CMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl direkter Methodenaufrufe liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der direkten Methodenaufrufen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_DirectMethodInvokesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Dateiuploads liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge von Dateiuploads innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_FileUploadsNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs. | IoT_CA_HttpC2DMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt nicht innerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_HttpC2DRejectedMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im HTTP-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Anzahl der Gerät-zu-Cloud-Nachrichten (HTTP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_HttpD2CMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_MqttC2DMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Anzahl der vom Gerät abgelehnten Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_MqttC2DRejectedMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Gerät-zu-Cloud-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_MqttD2CMessagesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Befehlswarteschlangenbereinigungen liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Befehlswarteschlangenbereinigungen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_QueuePurgesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl von Modulzwillingsupdates liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge der Modulzwillingsupdates innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_TwinUpdatesNotInAllowedRange |
| Benutzerdefinierte Warnung: Die Anzahl nicht autorisierter Vorgänge liegt außerhalb des zulässigen Bereichs. | Niedrig | IoT Hub | Die Menge nicht autorisierter Vorgänge innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | IoT_CA_UnauthorizedOperationsNotInAllowedRange |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Warnungen anpassen](quickstart-create-custom-alerts.md).
- [Übersicht](overview.md) über den Defender für IoT-Dienst

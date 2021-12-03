---
title: Konfigurieren und Anpassen des Defender-IoT-Micro-Agents für Azure RTOS
description: Hier erfahren Sie, wie Sie den Defender-IoT-Micro-Agent für Azure RTOS konfigurieren und anpassen.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: dc6e522e1d9453264d92100c6e7e830506d8284b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306281"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos"></a>Konfigurieren und Anpassen des Defender-IoT-Micro-Agents für Azure RTOS

In diesem Artikel wird beschrieben, wie Sie den Defender-IoT-Micro-Agent für Ihr Azure RTOS-Gerät konfigurieren, damit er Ihre Anforderungen an Netzwerk, Bandbreite und Arbeitsspeicher erfüllt.

Sie müssen im Verzeichnis `netxduo/addons/azure_iot/azure_iot_security_module/configs` eine Zielverteilungsdatei mit einer `*.dist`-Erweiterung auswählen.  

Wenn Sie eine CMake-Kompilierungsumgebung verwenden, müssen Sie einen Befehlszeilenparameter für den ausgewählten Wert auf `IOT_SECURITY_MODULE_DIST_TARGET` festlegen. Beispiel: `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

In einer IAR- oder anderen Nicht-CMake-Kompilierungsumgebung müssen Sie den Pfad `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` allen bekannten eingeschlossenen Pfaden hinzufügen. Beispiel: `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Mithilfe der folgenden Datei können das Verhalten Ihres Geräts konfigurieren.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h**

In einer CMake-Kompilierungsumgebung müssen Sie die Standardkonfiguration ändern, indem Sie die Datei `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` bearbeiten. Verwenden Sie das folgende CMake-Format `set(ASC_XXX ON)` oder die folgende Datei `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` für alle anderen Umgebungen. Beispiel: `#define ASC_XXX`.

Das Standardverhalten jeder Konfiguration wird in den folgenden Tabellen angegeben: 

## <a name="general"></a>Allgemein

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-iot-micro-agent | Der eindeutige Bezeichner des Geräts.  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | Zahl | 3.2.1 | Die Version. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Zahl  | 3 | Die Zeitspanne, die der Defender-IoT-Micro-Agent zum Senden der Sicherheitsnachricht nach einem Fehler benötigt. (in Sekunden) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Nr. | 300 | Die ausstehende Zeit des Defender-IoT -Micro-Agents (in Sekunden). Wenn die Zeit überschritten wird, ändert sich der Status in „suspend“ (anhalten). |

## <a name="collection"></a>Collection

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Nr.  | 30  | Das Startsammlungsintervall-Offset des Collectors. Während des Starts wird der Wert zur Sammlung des Systems hinzugefügt, um zu vermeiden, dass Nachrichten von mehreren Geräten gleichzeitig gesendet werden.  |
| ASC_HIGH_PRIORITY_INTERVAL | Nr. | 10 | Das Intervall des Collectors für Gruppen mit hoher Priorität (in Sekunden). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Nr. | 30 | Das Intervall des Collectors für Gruppen mit mittlerer Priorität (in Sekunden). |
| ASC_LOW_PRIORITY_INTERVAL | Nr. | 145,440  | Das Intervall des Collectors für Gruppen mit niedriger Priorität (in Sekunden). |

#### <a name="collector-network-activity"></a>Netzwerkaktivität des Collectors

Um die Konfiguration der Netzwerkaktivität Ihres Collectors anzupassen, verwenden Sie Folgendes:

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtert die `TCP`-Netzwerkaktivität. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtert die `UDP`-Netzwerkaktivitätsereignisse. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtert die `ICMP`-Netzwerkaktivitätsereignisse. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Erfasst nur die eingehenden Unicastpakete. Wenn „false“ festgelegt ist, werden außerdem Broadcast und Multicast aufgezeichnet. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolesch  | false  | Sendet ein leeres Ereignis des Collectors. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Nr. | 64 | Die maximale Anzahl von IPv4-Netzwerkereignissen, die im Arbeitsspeicher gespeichert werden sollen. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Nr. | 64  | Die maximale Anzahl von IPv6-Netzwerkereignissen, die im Arbeitsspeicher gespeichert werden sollen. |

### <a name="collectors"></a>Collectors
| Name | type | Standard | Details |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | EIN | Aktiviert den Collector für Heartbeat. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | EIN | Aktiviert den Collector für Netzwerkaktivität. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | EIN | Aktiviert den Collector für Systeminformationen.  |

Andere Konfigurations-Flags sind erweitert und enthalten nicht unterstützte Funktionen. Wenden Sie sich an den Support, um dies zu ändern oder weitere Informationen zu erhalten.
 
## <a name="supported-security-alerts-and-recommendations"></a>Unterstützte Sicherheitswarnungen und -empfehlungen

Der Defender-IoT-Micro-Agent für Azure RTOS unterstützt spezifische Sicherheitswarnungen und -empfehlungen. Stellen Sie sicher, dass Sie die [relevanten Warnungs- und Empfehlungswerte für Ihren Dienst überprüfen und anpassen](concept-rtos-security-alerts-recommendations.md).

## <a name="log-analytics-optional"></a>Log Analytics (optional)

Sie können Log Analytics aktivieren und konfigurieren, um Geräteereignisse und -aktivitäten zu untersuchen. Weitere Informationen zum Einrichten und Verwenden von Log Analytics beim Defender für IoT-Dienst finden Sie [hier](how-to-security-data-access.md#log-analytics). 

## <a name="next-steps"></a>Nächste Schritte


- Überprüfen und Anpassen von [Sicherheitswarnungen und -empfehlungen](concept-rtos-security-alerts-recommendations.md) für den Defender-IoT-Micro-Agent für Azure RTOS
- Weitere Informationen finden Sie bei Bedarf in der [API für den Defender-IoT-Micro-Agent für Azure RTOS](azure-rtos-security-module-api.md).

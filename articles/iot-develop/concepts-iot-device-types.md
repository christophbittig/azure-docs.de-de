---
title: Übersicht über Azure IoT-Gerätetypen
description: Erfahren Sie mehr über die von Azure IoT unterstützten Gerätetypen und die verfügbaren Tools.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 21eea79a6a448cd65bd12cde33efb6acabd3ada5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003646"
---
# <a name="overview-of-azure-iot-device-types"></a>Übersicht über Azure IoT-Gerätetypen
IoT-Geräte sind auf einer breiten Auswahl von Hardwareplattformen vorhanden. Es gibt kleine 8-Bit-Mikrocontroller bis hin zu den neuesten x86-CPUs in einem Desktopcomputer. Bei der Auswahl der Hardware für ein IoT-Gerät spielen viele Variablen eine Rolle. In diesem Artikel werden einige der wichtigsten Unterschiede beschrieben.

## <a name="key-hardware-differentiators"></a>Wichtige Hardwareunterscheidungsmerkmale
Wichtige Faktoren bei der Auswahl der Hardware sind die Kosten, der Energieverbrauch, das Netzwerk sowie die verfügbare Eingaben und Ausgaben.

* **Kosten**: Kleinere kostengünstigere Geräte werden normalerweise verwendet, wenn das Endprodukt durch Massenfertigung erzeugt wird. Der Nachteil besteht jedoch darin, dass die Entwicklung des Geräts in Anbetracht seiner stark eingeschränkten Funktionen relativ kostspielig sein kann. Die Entwicklungskosten können auf alle produzierten Geräte verteilt werden, sodass die Entwicklungskosten pro Gerät gering sind.

* **Leistung**: Der Energieverbrauch eines Geräts ist ein wichtiger Faktor, wenn das Gerät Akkus verwendet und keine Verbindung mit dem Stromnetz besteht. Mikrocontroller sind häufig für Szenarien mit geringem Energieverbrauch konzipiert und empfehlen sich möglicherweise für die Verlängerung der Akkulebensdauer.

* **Netzwerkzugriff**: Es gibt viele Möglichkeiten, ein Gerät mit einem Clouddienst zu verbinden. Zu den verfügbaren Optionen zählen Ethernet, WLAN und Mobilfunk. Der gewählte Verbindungstyp hängt davon ab, wo das Gerät bereitgestellt wird und wie es verwendet wird. Mobilfunk kann z. B. aufgrund der hohen Abdeckung eine attraktive Option sein, jedoch bei Geräten mit hohem Datenverkehr hohe Kosten verursachen. Ethernet-Kabelverbindungen ermöglichen geringere Datenkosten, gehen jedoch mit eingeschränkter Mobilität einher.

* **Eingaben und Ausgaben**: Die auf dem Gerät verfügbaren Eingaben und Ausgaben wirken sich direkt auf seine Fähigkeiten aus. Ein Mikrocontroller verfügt in der Regel über viele E/A-Funktionen, die direkt in den Chip integriert sind, und bietet eine große Auswahl an Sensoren, mit denen eine direkte Verbindung hergestellt werden kann.

## <a name="microcontrollers-vs-microprocessors"></a>Mikrocontroller und Mikroprozessoren
IoT-Geräte lassen sich in zwei allgemeine Kategorien unterteilen: Mikrocontroller und Mikroprozessoren.

**Mikrocontroller** sind kostengünstiger und einfacher zu verwenden als Mikroprozessoren. Ein Mikrocontroller enthält viele Funktionen, z. B. Arbeitsspeicher, Schnittstellen und E/A, direkt im Chip. Ein Mikroprozessor bezieht diese Funktionalität von Komponenten in anderen Chips. Ein Mikrocontroller verwendet häufig ein Echtzeitbetriebssystem (Real-Time OS, RTOS) oder kein Betriebssystem (Bare Metal) und bietet Echtzeitantworten und hochgradig deterministische Reaktionen auf externe Ereignisse.

Auf dem **Mikroprozessor** wird ein allgemeines Betriebssystem ausgeführt, z. B. Windows, Linux oder MacOSX, das eine nicht deterministische Echtzeitantwort bereitstellt. In der Regel ist der Zeitpunkt nicht garantiert, zu dem eine Aufgabe abgeschlossen wird. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="Mikrokontroller und Mikroprozessor":::

In der folgenden Tabelle werden einige wesentliche Unterschiede zwischen auf Mikrocontrollern und auf Mikroprozessoren basierenden Systemen gezeigt:

||Mikrocontroller|Mikroprozessor|
|-|-|-|
|**CPU**| Kleiner | Mehr |
|**RAM**| Kleiner | Mehr |
|**Blinken**| Kleiner | Mehr |
|**Betriebssystem**| Bare-Metal/RTOS | Universell (Windows/Linux) |
|**Entwicklungsschwierigkeit**| Schwieriger | Einfacher |
|**Stromverbrauch**| Geringer | Höher |
|**Kosten**| Geringer | Höher |
|**Deterministisch**| Ja | Nein – mit Ausnahmen |
|**Gerätegröße**| Kleiner | Größer |

## <a name="next-steps"></a>Nächste Schritte
Der von Ihnen ausgewählte IoT-Gerätetyp wirkt sich direkt darauf aus, wie das Gerät mit Azure IoT verbunden wird.

Durchsuchen Sie die verschiedenen [Azure IoT SDKs](about-iot-sdks.md), um das SDK zu finden, das für Ihre Geräteanforderungen am besten geeignet ist.

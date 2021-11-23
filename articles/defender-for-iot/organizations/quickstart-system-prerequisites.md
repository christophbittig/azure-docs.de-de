---
title: 'Schnellstart: Systemvoraussetzungen'
description: In dieser Schnellstartanleitung erhalten Sie Informationen zu den für die Ausführung von Microsoft Defender für IoT erforderlichen Systemvoraussetzungen.
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: f4f0912aade1f00623637791bc05aeee3a8747cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278301"
---
# <a name="quickstart-system-prerequisites"></a>Schnellstart: Systemvoraussetzungen

Dieser Artikel enthält eine Beschreibung der Systemanforderungen für die Ausführung von Microsoft Defender für IoT.

## <a name="prerequisites"></a>Voraussetzungen

- Keine

## <a name="minimum-requirements"></a>Mindestanforderungen

- Netzwerkswitches, die die Überwachung des Datenverkehrs über den SPAN-Port unterstützen.
- Hardwareappliances für NTA-Sensoren.
- Die Rolle „Mitwirkender“ für das Azure-Abonnement. Sie wird nur beim Onboarding zum Definieren von committeten Geräten und der Verbindung mit Microsoft Sentinel benötigt.
- Rolle **Mitwirkender** für Azure IoT Hub (Tarif „Free“ oder „Standard“) für die cloudbasierte Verwaltung. Stellen Sie sicher, dass das Feature **Microsoft Defender für IoT** aktiviert ist.

## <a name="supported-service-regions"></a>Unterstützte Servicebereiche

Bei Defender für IoT wird der gesamte Datenverkehr aus allen europäischen Regionen an das regionale Rechenzentrum „Europa, Westen“ weitergeleitet. Der Datenverkehr aller restlichen Regionen wird an das regionale Rechenzentrum „USA, Mitte“ weitergeleitet.

Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ermitteln der erforderlichen Appliances](how-to-identify-required-appliances.md)

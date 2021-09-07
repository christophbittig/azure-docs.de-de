---
title: Grundlegendes zum Importieren von Device Update für IoT Hub | Microsoft-Dokumentation
description: Wichtige Konzepte zum Importieren eines neuen Updates in Device Update für IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39a1acb596f4d56915aabfa0a64fc69d6e55aec2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769106"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importieren von Updates in Device Update für IoT Hub
Wenn Sie ein Update aus Device Update für IoT Hub auf Geräten bereitstellen möchten, müssen Sie es zuerst in den Device Update-Dienst _importieren_. Hier ist eine Übersicht über einige wichtige Konzepte, die Sie beim Importieren von Updates verstehen müssen.

## <a name="limits-on-importing-updates"></a>Grenzwerte für den Import von Updates
Für jede Device Update for IoT Hub-Instanz werden die folgenden Grenzwerte erzwungen:

* 5 Anbieter pro ADU-Instanz
* 5 Namen pro Anbieter
* 25 Versionen pro Name

Es gilt außerdem ein Limit von 100 Updateversionen insgesamt (für alle Anbieter und Namen).

Für jede einzelne Datei in einer Updateübermittlung beträgt die maximal zulässige Dateigröße 800 MB. Die _kombinierte_ Gesamtgröße aller Dateien in einer Updateübermittlung darf ebenfalls nicht über 800 MB liegen.

## <a name="import-manifest"></a>Importmanifest

Ein Importmanifest ist eine JSON-Datei, die wichtige Informationen zu dem gerade importierten Update definiert. Sie übermitteln sowohl Ihr Importmanifest als auch die zugeordnete(n) Updatedatei(en) (z. B. ein Firmwareupdate-Paket) im Rahmen des Importvorgangs. Die im Importmanifest definierten Metadaten werden zum Erfassen des Updates verwendet. Einige der Metadaten werden auch zum Zeitpunkt der Bereitstellung verwendet – beispielsweise zur Überprüfung, ob ein Update richtig installiert wurde.

Das Importmanifest enthält mehrere Elemente, die ein wichtiges Geräteupdate für IoT Hub-Konzepte darstellen. Diese Konzepte werden nachstehend beschrieben.

### <a name="update-identity-update-id"></a>Update-Identität (Update-ID)

Die Update-Identität stellt den eindeutigen Bezeichner für ein Update dar. Sie definiert wichtige Eigenschaften zu einem Update, das gerade importiert wird. Die Update-Identität besteht aus drei Teilen:
* Anbieter: Dies ist die Entität, die die Aktualisierung erstellt oder dafür direkt zuständig ist. Oft ist dies ein Firmenname.
* Name: Dies ist ein Bezeichner für eine Klasse von Updates. Die Klasse kann etwas von Ihnen Gewähltes sein. Oft ist es ein Geräte- oder Modellname.
* Version: Dies ist eine Versionsnummer, die das betreffende Update von anderen unterscheidet, die denselben Wert für „Anbieter“ und „Name“ haben. Diese Version wird vom Device Update für IoT Hub-Dienst verwendet und entspricht möglicherweise einer Version einer einzelnen Softwarekomponente auf dem Gerät. 

### <a name="compatibility"></a>Kompatibilität

Zur Vereinfachung von Updatebereitstellungen vergleicht Device Update für IoT Hub die Kompatibilitätseigenschaften für ein Update, die im Importmanifest definiert sind, mit entsprechenden Geräteeigenschaften. Nur Updates mit übereinstimmenden Eigenschaften werden zurückgegeben und stehen für eine Bereitstellung zur Verfügung.

### <a name="installedcriteria"></a>InstalledCriteria

Mithilfe von InstalledCriteria ermittelt der Update-Agent auf einem Gerät, ob ein Update erfolgreich installiert wurde.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie bereit sind, probieren Sie die [Schrittanleitung für den Import](./import-update.md) aus, in dem Sie Schritt für Schritt durch den Importvorgang geführt werden.



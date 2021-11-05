---
title: Datei einfügen
description: Datei einfügen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a686ab26c93130d7371bcf2435dccbd7aaa1c985
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070395"
---
Sie haben ein Ziel für den Export Ihrer Daten eingerichtet und richten jetzt den Datenexport in ihrer IoT Central Anwendung mit den folgenden Schritten ein:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an.

1. Wählen Sie im linken Bereich die Option **Datenexport** aus.

    > [!Tip]
    > Falls **Datenexport** dort nicht angezeigt wird, haben Sie keine Berechtigungen zum Konfigurieren des Datenexports in Ihrer App. Wenden Sie sich an Ihren Administrator, damit dieser den Datenexport einrichtet.

1. Wählen Sie **+ Neuer Export** aus.

1. Geben Sie einen Anzeigenamen für Ihren neuen Export ein, und stellen Sie sicher, dass der Datenexport **Aktiviert** ist.

1. Wählen Sie den zu exportierenden Datentyp aus. In der folgenden Tabelle sind die unterstützten Datenexporttypen aufgeführt:

    | Datentyp | Beschreibung | Datenformat |
    | :------------- | :---------- | :----------- |
    |  Telemetrie | Exportiert Telemetrienachrichten von Geräten nahezu in Echtzeit. Jede exportierte Nachricht enthält den vollständigen Inhalt der ursprünglichen Gerätenachricht (normalisiert).   |  [Format der Telemetrienachricht](#telemetry-format)   |
    | Eigenschaftsänderungen | Exportiert Änderungen zu den Geräte- und Cloudeigenschaften nahezu in Echtzeit. Bei schreibgeschützten Geräteeigenschaften werden Änderungen an den gemeldeten Werten exportiert. Bei Lese-/Schreibeigenschaften werden sowohl gemeldete als auch gewünschte Werte exportiert. | [Format der Eigenschaftsänderungsnachricht](#property-changes-format) |
    | Gerätekonnektivität | Exportieren von Ereignissen zu verbundenen und getrennten Geräten | [Nachrichtenformat zur Gerätekonnektivität](#device-connectivity-changes-format) |
    | Gerätelebenszyklus | Exportieren der Geräteereignisse „registered“, „deleted“, „provisioned“, „enabled“, „disabled“, „displayNameChanged“ und „deviceTemplateChanged“ | [Nachrichtenformat der Gerätelebenszyklusänderungen](#device-lifecycle-changes-format) |
    | Gerätevorlagenlebenszyklus | Exportiert Änderungen an veröffentlichten Gerätevorlagen wie etwa Erstellungs-, Aktualisierungs- und Löschvorgänge. | [Nachrichtenformat der Änderungen des Gerätevorlagenlebenszyklus](#device-template-lifecycle-changes-format) |

1. Fügen Sie optional Filter hinzu, um die Menge der exportierten Daten zu verringern. Für jeden Datenexporttyp gibt es verschiedene Arten von Filtern: <a name="DataExportFilters"></a>

    | Datentyp | Verfügbare Filter|
    |--------------|------------------|
    |Telemetrie|<ul><li>Filtern nach Gerätename, Geräte-ID, Gerätevorlage und simulierten Geräten</li><li>Filtern des Datenstroms, sodass er nur Telemetriedaten enthält, die den Filterbedingungen entsprechen</li><li>Filtern des Datenstroms, sodass er nur Telemetriedaten von Geräten mit Eigenschaften enthält, die den Filterbedingungen entsprechen</li><li>Filtern des Datenstroms, sodass er nur Telemetriedaten mit *Nachrichteneigenschaften* enthält, die den Filterbedingungen entsprechen. *Nachrichteneigenschaften* (auch als *Anwendungseigenschaften* bezeichnet) werden in einer Sammlung von Schlüssel-Wert-Paaren für jede Telemetrienachricht gesendet, die optional von Geräten gesendet werden, die die Geräte-SDKs verwenden. Zum Erstellen eines Nachrichteneigenschaftenfilters geben Sie den gesuchten Nachrichteneigenschaftsschlüssel ein, und geben Sie eine Bedingung an. Nur Telemetrienachrichten mit Eigenschaften, die die angegebene Filterbedingung erfüllen, werden exportiert. Weitere Informationen zu Anwendungseigenschaften finden Sie in der [IoT Hub-Dokumentation](../articles/iot-hub/iot-hub-devguide-messages-construct.md). </li></ul>|
    |Eigenschaftsänderungen|<ul><li>Filtern nach Gerätename, Geräte-ID, Gerätevorlage und simulierten Geräten</li><li>Filtern des Datenstroms, sodass er nur Eigenschaftsänderungen enthält, die den Filterbedingungen entsprechen</li></ul>|
    |Gerätekonnektivität|<ul><li>Filtern nach Gerätename, Geräte-ID, Gerätevorlage, Organisationen und ob das Gerät simuliert ist</li><li>Filtern des Datenstroms, sodass er nur Änderungen von Geräten mit Eigenschaften enthält, die den Filterbedingungen entsprechen</li></ul>|
    |Gerätelebenszyklus|<ul><li>Filtern nach Gerätename, Geräte-ID, Gerätevorlage und bereitgestellten, aktivierten oder simulierten Geräten</li><li>Filtern des Datenstroms, sodass er nur Änderungen von Geräten mit Eigenschaften enthält, die den Filterbedingungen entsprechen</li></ul>|
    |Gerätevorlagenlebenszyklus|<ul><li>Filtern nach Gerätevorlage</li></ul>|

1. Reichern Sie optional exportierte Nachrichten mit zusätzlichen Metadaten für Schlüssel-Wert-Paare an. Die folgenden Anreicherungen stehen für die Datenexporttypen „Telemetrie“, „Eigenschaftsänderungen“, „Gerätekonnektivität“ und „Gerätelebenszyklus“ zur Verfügung: <a name="DataExportEnrichmnents"></a>
    - **Benutzerdefinierte Zeichenfolge:** Fügt jeder Nachricht eine benutzerdefinierte statische Zeichenfolge hinzu. Geben Sie einen beliebigen Schlüssel und einen beliebigen Zeichenfolgenwert ein.
    - **Eigenschaft**, die jeder Nachricht Folgendes hinzufügt:
       - Gerätemetadaten wie Gerätename, Gerätevorlagenname, aktiviert, Organisationen, bereitgestellt und simuliert.
       - Die aktuelle vom Gerät gemeldete Eigenschaft oder den Cloudeigenschaftswert Wenn die exportierte Nachricht aus einem Gerät stammt, das die angegebene Eigenschaft nicht hat, erhält die exportierte Nachricht die Anreicherung nicht.

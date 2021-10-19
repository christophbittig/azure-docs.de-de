---
title: Datenfluss im IoT-Connector – Azure Healthcare-APIs
description: Grundlegendes zum Datenfluss des IoT-Connectors. Der IoT-Connector erfasst, normalisiert, gruppiert, transformiert und persistent IoMT-Daten im FHIR-Dienst.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: 41ad8d284636e431b74c43006bb544f737ce1f47
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005501"
---
# <a name="iot-connector-data-flow"></a>Datenfluss des IoT-Connectors

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Artikel bietet eine Übersicht über den IoT-Connector. Sie erfahren mehr über verschiedene Datenverarbeitungsphasen innerhalb des IoT-Connectors, die Gerätedaten in Fast Healthcare Interoperability Resources (FHIR&#174;)-basierte [Überwachungsressourcen](https://www.hl7.org/fhir/observation.html) transformieren.

Im Folgenden finden Sie verschiedene Phasen, die Daten durchlaufen, sobald sie vom IoT-Connector empfangen wurden.

## <a name="ingest"></a>Erfassen
Die Erfassung ist die erste Phase, in der Gerätedaten an den IoT-Connector empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird in einer Instanz von [Azure Event Hub](../../event-hubs/index.yml) gehostet. Die Azure Event Hub-Plattform unterstützt hohe Skalierbarkeit und hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Darüber hinaus kann der IoT-Connector Nachrichten asynchron verarbeiten, wodurch die Notwendigkeit entfällt, dass Geräte warten müssen, während Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize
Normalisieren ist die nächste Phase, in der Gerätedaten aus dem obigen Azure Event Hub abgerufen und mithilfe der Gerätezuordnung verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. 

Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern bietet auch die Möglichkeit, eine Eingabenachricht in mehrere normalisierte Nachrichten zu projizieren. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group
Die Gruppierung ist die nächste Phase, in der die verfügbaren normalisierten Nachrichten aus der vorherigen Phase nach drei verschiedenen Parametern gruppiert werden: Geräteidentität, Messungstyp und Zeitraum.

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine übersichtliche Möglichkeit zur Darstellung einer zeitbasierten Messreihe von einem Gerät in FHIR. Und der Zeitraum steuert die Latenz, mit der vom IoT-Connector generierte Beobachtungsressourcen in den FHIR-Dienst geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren
In der Transformationsphase werden gruppierte normalisierte Nachrichten über FHIR-Zielzuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die [Geräteressource](https://www.hl7.org/fhir/device.html) zusammen mit der zugehörigen [Patient-Ressource](https://www.hl7.org/fhir/patient.html) auch aus dem FHIR-Dienst abgerufen, indem der in der Nachricht enthaltene Gerätebezeichner verwendet wird. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
> Alle Identitätss lookups werden nach der Lösung zwischengespeichert, um die Auslastung des FHIR-Diensts zu verringern. Wenn Sie Geräte für mehrere Patienten wiederverwenden möchten, empfiehlt es sich, eine für den Patienten spezifische virtuelle Geräteressource zu erstellen und den Bezeichner des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn im FHIR-Dienst keine Geräteressource für einen bestimmten Gerätebezeichner vorhanden ist, hängt das Ergebnis vom Wert ab, der `Resolution Type` zum Zeitpunkt der Erstellung festgelegt wurde. Wenn die Einstellung auf den Wert `Lookup` festgelegt ist, wird die betreffende Nachricht ignoriert. Die Pipeline verarbeitet weiterhin andere eingehende Nachrichten. Wenn diese Einstellung auf festgelegt `Create` ist, erstellt der IoT-Connector eine reine Geräte- und Patientenressourcen im FHIR-Dienst.  

## <a name="persist"></a>Speichern
Sobald die FHIR-Überwachungsressource in der Transformationsphase generiert wurde, wird die Ressource im FHIR-Dienst gespeichert. Wenn die FHIR-Ressource neu ist, wird sie im FHIR-Dienst erstellt. Wenn die FHIR-Ressource bereits vorhanden war, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Geräte- und FHIR-Zielzuordnungen erstellen.

> [!div class="nextstepaction"]
> [Gerätezuordnung](how-to-use-device-mapping-iot.md)

> [!div class="nextstepaction"]
> [FHIR-Zielzuordnung](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
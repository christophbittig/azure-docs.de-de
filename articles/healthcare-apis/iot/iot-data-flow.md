---
title: Datenfluss im IoT-Connector – Azure Healthcare-APIs
description: Informationen zum Datenfluss des IoT-Connectors. Der IoT-Connector erfassung, normalisiert, gruppent, transformiert und persistent IoMT-Daten im FHIR-Dienst.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 14a1668996682179ef0c0beb95383be892a4e693
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719402"
---
# <a name="iot-connector-data-flow"></a>Datenfluss des IoT-Konnektors

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Artikel bietet eine Übersicht über den Datenfluss des IoT-Connectors. Sie erfahren mehr über die verschiedenen Datenverarbeitungsphasen im IoT-Connector, die Gerätedaten in Fast Healthcare Interoperability Resources(FHIR&#174;)-basierte [Beobachtungsressourcen](https://www.hl7.org/fhir/observation.html) transformieren.

Daten von gesundheitsbezogenen Geräten oder medizinischen Geräten fließen über einen Pfad, in dem der IoT-Connector Daten in FHIR transformiert und dann daten auf dem FHIR-Server gespeichert und darauf zugegriffen wird. Der Integritätsdatenpfad folgt diesen Schritten in dieser Reihenfolge: Erfassung, Normalisierung, Gruppierung, Transformation und Persistenz. In diesem Datenfluss werden Integritätsdaten im ersten Schritt der Erfassung vom Gerät abgerufen. Nachdem die Daten empfangen wurden, werden sie pro benutzerdefinierten oder vom Benutzer erstellten Schemavorlagen verarbeitet oder normalisiert, sodass die Integritätsdaten einfacher zu verarbeiten und zu gruppiert werden können. Integritätsdaten werden in drei Sperate-Parametern gruppiert. Nachdem die Integritätsdaten normalisiert und gruppiert wurden, können sie über die FHIR-Zuordnung verarbeitet oder transformiert und dann auf dem FHIR-Server gespeichert oder beibehalten werden.

In diesem Artikel werden die einzelnen Schritte im Datenfluss genauer beschrieben. Die nächsten Schritte sind das Bereitstellen eines [IoT-Connectors](deploy-iot-connector-in-azure.md) mithilfe einer Gerätezuordnung (der Normalisierungsschritt) und die Verwendung einer FHIR-Gerätezuordnung (der Transformationsschritt).

In den nächsten Abschnitten werden die Phasen beschrieben, die Daten nach dem Empfangen der Daten durch den IoT-Connector durchläuft.

## <a name="ingest"></a>Erfassen
Die Erfassung ist die erste Phase, in der Gerätedaten an den IoT-Connector empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird in einer Instanz von [Azure Event Hub](../../event-hubs/index.yml) gehostet. Die Azure Event Hub-Plattform unterstützt hohe Skalierbarkeit und hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Außerdem kann der IoT-Connector Nachrichten asynchron verarbeiten, damit Geräte nicht mehr warten müssen, bis Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize
Normalisieren ist die nächste Phase, in der Gerätedaten aus dem oben genannten Azure Event Hub abgerufen und mithilfe der Gerätezuordnung verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. 

Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern bietet auch die Möglichkeit, eine Eingabenachricht in mehrere normalisierte Nachrichten zu projektieren. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group
Gruppe ist die nächste Phase, in der die aus der vorherigen Phase verfügbaren normalisierten Nachrichten mit drei verschiedenen Parametern gruppiert werden:

* Geräteidentität
* Messungstyp 
* Zeitraum

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine übersichtliche Möglichkeit zur Darstellung einer zeitbasierten Messreihe von einem Gerät in FHIR. Der Zeitraum steuert die Latenz, mit der vom IoT-Connector generierte Beobachtungsressourcen in den FHIR-Dienst geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren
In der Phase Transformieren werden ge gruppierte normalisierte Nachrichten über FHIR-Zielzuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die [Geräteressource](https://www.hl7.org/fhir/device.html) zusammen mit der zugehörigen [Patient-Ressource](https://www.hl7.org/fhir/patient.html) auch mithilfe der in der Nachricht angegebenen Geräte-ID aus dem FHIR-Dienst abgerufen. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
> Alle Identitätssyntaufnahmen werden nach der Lösung zwischengespeichert, um die Auslastung des FHIR-Diensts zu verringern. Wenn Sie Geräte für mehrere Patienten wiederverwenden möchten, empfiehlt es sich, eine für den Patienten spezifische virtuelle Geräteressource zu erstellen und den Bezeichner des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn im FHIR-Dienst keine Geräteressource für eine bestimmte Geräte-ID vorhanden ist, hängt das Ergebnis vom Wert ab, der zum Zeitpunkt der `Resolution Type` Erstellung festgelegt wurde. Wenn die Einstellung auf den Wert `Lookup` festgelegt ist, wird die betreffende Nachricht ignoriert. Die Pipeline verarbeitet weiterhin andere eingehende Nachrichten. Wenn dieser Aufsatz auf festgelegt ist, erstellt der `Create` IoT-Connector im FHIR-Dienst ein bares Gerät und Patientenressourcen.  

## <a name="persist"></a>Speichern
Nachdem die Observation FHIR-Ressource in der Transformationsphase generiert wurde, wird die Ressource im FHIR-Dienst gespeichert. Wenn die FHIR-Ressource neu ist, wird sie im FHIR-Dienst erstellt. Wenn die FHIR-Ressource bereits vorhanden war, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Geräte- und FHIR-Zielzuordnungen erstellen.

> [!div class="nextstepaction"]
> [Gerätezuordnungen](how-to-use-device-mappings.md)

> [!div class="nextstepaction"]
> [FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

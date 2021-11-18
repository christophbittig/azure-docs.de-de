---
title: Was ist der IoT-Connector? – Azure Healthcare-APIs
description: In diesem Artikel lernen Sie die Schritte des IoT-Connectors vor dem Speichern von IoMT-Daten im FHIR-Dienst.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: b74b3c1aefe2c7dd05421385e672385196aab15e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719427"
---
# <a name="what-is-iot-connector"></a>Was ist der IoT-Connector?

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector ist ein optionales Feature von Azure Healthcare-APIs, mit dem Daten von IoMT-Geräten (Internet of Medical Things) aufgenommen werden können.

Dieser Artikel bietet eine Übersicht über den Datenfluss im IoT-Connector. Sie erfahren mehr über verschiedene Datenverarbeitungsphasen innerhalb des IoT-Connectordiensts, der Gerätedaten in Fast Healthcare Interoperability Resources(FHIR&#174;)-basierte [Beobachtungsressourcen](https://www.hl7.org/fhir/observation.html) transformiert.

Im Folgenden finden Sie die verschiedenen Phasen, die Daten nach dem Empfangen durch den IoT-Connector durchläuft.

## <a name="ingest"></a>Erfassen

Die Erfassung ist die erste Phase, in der Gerätedaten an den IoT-Connectordienst empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird in einer Instanz von Azure Event Hub gehostet. [Die Azure Event Hub-Plattform](../../event-hubs/index.yml) unterstützt eine hohe Skalierung und einen hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Außerdem kann der IoT-Connectordienst Nachrichten asynchron verarbeiten, damit Geräte nicht mehr warten müssen, bis Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize

Normalisieren ist die nächste Phase, in der Gerätedaten aus dem oben genannten Azure Event Hub abgerufen und mithilfe von Gerätezuordnungsvorlagen verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern ermöglicht auch die Projektion einer Eingabenachricht in mehrere normalisierte Nachrichten. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group

Gruppe ist die nächste Phase, in der die aus der vorherigen Phase verfügbaren normalisierten Nachrichten mit drei verschiedenen Parametern gruppiert werden: 

* Geräteidentität
* Messungstyp 
* Zeitraum

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine präzise Möglichkeit, eine zeitbasierte Reihe von Messungen eines Geräts in FHIR zu darstellen, während der Zeitraum die Latenz steuert, bei der vom IoT-Connector generierte Beobachtungsressourcen in den FHIR-Dienst geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren

In der Phase Transformieren werden ge gruppierte normalisierte Nachrichten über FHIR-Zielzuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die Geräteressource zusammen mit der zugehörigen Patient-Ressource auch mithilfe der in der Nachricht angegebenen Geräte-ID aus dem FHIR-Dienst abgerufen. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
>Alle Identitätssyntaufnahmen werden nach der Lösung zwischengespeichert, um die Auslastung des FHIR-Diensts zu verringern. Wenn Sie gerätespezifische Geräte mit mehreren Patienten wiederverwenden möchten, wird empfohlen, eine für den Patienten spezifische virtuelle Geräteressource zu erstellen und die ID des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn im FHIR-Dienst keine Geräteressource für eine bestimmte Geräte-ID vorhanden ist, hängt das Ergebnis vom Wert des Auflösungstyps ab, der zum Zeitpunkt der Erstellung festgelegt wurde. Wenn nachschlagen festgelegt ist, wird die spezifische Nachricht ignoriert, und die Pipeline wird weiterhin andere eingehende Nachrichten verarbeiten. Wenn erstellen festgelegt ist, erstellt der IoT-Connectordienst im FHIR-Dienst ein unerschädbares Gerät und Patientenressourcen.

## <a name="persist"></a>Speichern

Sobald die Observation FHIR-Ressource in der Transformationsphase generiert wurde, wird die Ressource im FHIR-Dienst gespeichert. Wenn die FHIR-Ressource neu ist, wird sie für den Dienst erstellt. Wenn die FHIR-Ressource bereits vorhanden ist, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu IoT-Connectorzuordnungen finden Sie in den folgenden Leitfäden:

>[!div class="nextstepaction"]
>[Verwenden von Gerätezuordnungen](how-to-use-device-mappings.md)

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

---
title: Was ist der IoT-Connector? – Azure Healthcare-APIs
description: In diesem Artikel lernen Sie die Schritte kennen, die der IoT-Connector vor dem Speichern von IoMT-Daten im FHIR-Dienst vornimmt.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: 648b7356cb51a7c5f4444d4b707788f5b7bbf971
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005519"
---
# <a name="what-is-iot-connector"></a>Was ist der IoT-Connector?

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der IoT-Connector ist ein optionales Feature von Azure Healthcare-APIs, das die Möglichkeit bietet, Daten von IoMT-Geräten (Internet of Medical Things) zu erfassen.

Dieser Artikel bietet eine Übersicht über den Datenfluss im IoT-Connector. Sie erfahren mehr über verschiedene Datenverarbeitungsphasen innerhalb des IoT-Connectordiensts, der Gerätedaten in Fast Healthcare Interoperability Resources (FHIR&#174;)-basierte [Überwachungsressourcen](https://www.hl7.org/fhir/observation.html) transformiert.

Im Folgenden sind die verschiedenen Phasen aufgeführt, die Daten durchlaufen, sobald sie vom IoT-Connector empfangen werden.

## <a name="ingest"></a>Erfassen

Die Erfassung ist die erste Phase, in der Gerätedaten im IoT-Connectordienst empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird in einer Instanz von Azure Event Hub gehostet. [Die Azure Event Hub-Plattform](../../event-hubs/index.yml) unterstützt eine hohe Skalierung und einen hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Darüber hinaus kann der IoT-Connectordienst Nachrichten asynchron verarbeiten, ohne dass Geräte warten müssen, während Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize

Normalisieren ist die nächste Phase, in der Gerätedaten aus dem obigen Azure Event Hub abgerufen und mithilfe von Gerätezuordnungsvorlagen verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern ermöglicht auch die Projektion einer Eingabenachricht in mehrere normalisierte Nachrichten. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group

Gruppe ist die nächste Phase, in der die normalisierten Nachrichten aus der vorherigen Phase mit drei verschiedenen Parametern gruppiert werden: 

* Geräteidentität
* Messungstyp 
* Zeitraum

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine präzise Möglichkeit, eine zeitbasierte Reihe von Messungen von einem Gerät in FHIR darzustellen, während der Zeitraum die Latenz steuert, mit der vom IoT-Connector generierte Beobachtungsressourcen in den FHIR-Dienst geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren

In der Transformationsphase werden gruppierte normalisierte Nachrichten über FHIR-Zielzuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die Geräteressource zusammen mit der zugehörigen Patient-Ressource auch aus dem FHIR-Dienst abgerufen, indem der in der Nachricht enthaltene Gerätebezeichner verwendet wird. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
>Alle Identitätss lookups werden nach der Lösung zwischengespeichert, um die Auslastung des FHIR-Diensts zu verringern. Wenn Sie beabsichtigen, Geräte mit mehreren Patienten wiederzuverwenden, wird empfohlen, eine virtuelle Geräteressource zu erstellen, die für den Patienten spezifisch ist, und die ID des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn im FHIR-Dienst keine Geräteressource für einen bestimmten Gerätebezeichner vorhanden ist, hängt das Ergebnis vom Wert des Auflösungstyps ab, der zum Zeitpunkt der Erstellung festgelegt wurde. Wenn Look up (Suchen) festgelegt ist, wird die spezifische Nachricht ignoriert, und die Pipeline verarbeitet weiterhin andere eingehende Nachrichten. Wenn diese Einstellung auf Erstellen festgelegt ist, erstellt der IoT-Connectordienst eine reine Geräte- und Patientenressourcen im FHIR-Dienst.

## <a name="persist"></a>Speichern

Sobald die FHIR-Beobachtungsressource in der Transformationsphase generiert wurde, wird die Ressource im FHIR-Dienst gespeichert. Wenn die FHIR-Ressource neu ist, wird sie für den Dienst erstellt. Wenn die FHIR-Ressource bereits vorhanden ist, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu IoT-Connectorzuordnungen finden Sie in den folgenden Handbüchern:

>[!div class="nextstepaction"]
>[Verwenden der Gerätezuordnung](how-to-use-device-mapping-iot.md)

>[!div class="nextstepaction"]
>[Verwenden der FHIR-Zielzuordnung](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
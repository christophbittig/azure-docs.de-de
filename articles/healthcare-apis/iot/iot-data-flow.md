---
title: Datenfluss im IoT-Connector – Azure Healthcare-APIs
description: Grundlegendes zum Datenfluss des IoT-Connectors. Der IoT-Connector erfasst, normalisiert, gruppiert, transformiert und persistent IoMT-Daten im FHIR-Dienst.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 090c051195559458314e89b47f3a0e4fcb6a838c
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133030904"
---
# <a name="iot-connector-data-flow"></a>Datenfluss des IoT-Konnektors

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Artikel bietet eine Übersicht über den Datenfluss des IoT-Connectors. Sie erfahren mehr über die verschiedenen Datenverarbeitungsphasen innerhalb des IoT-Connectors, die Gerätedaten in Fast Healthcare Interoperability Resources (FHIR&#174;)-basierte [Überwachungsressourcen](https://www.hl7.org/fhir/observation.html) transformieren.

Daten von gesundheitsbezogenen Geräten oder medizinischen Geräten durchlaufen einen Pfad, in dem der IoT-Connector Daten in FHIR transformiert. Anschließend werden Daten im FHIR-Dienst gespeichert und darauf zugegriffen. Der Pfad der Integritätsdaten folgt diesen Schritten in dieser Reihenfolge: Erfassen, Normalisieren, Gruppieren, Transformieren und Beibehalten. In diesem Datenfluss werden Integritätsdaten im ersten Schritt der Erfassung vom Gerät abgerufen. Nachdem die Daten empfangen wurden, werden sie pro vom Benutzer ausgewählten oder vom Benutzer erstellten Schemavorlagen verarbeitet oder normalisiert, sodass die Integritätsdaten einfacher zu verarbeiten sind und gruppiert werden können. Integritätsdaten sind in drei Betriebsparameter gruppiert. Nachdem die Integritätsdaten normalisiert und gruppiert wurden, können sie über FHIR-Zielzuordnungen verarbeitet oder transformiert und dann im FHIR-Dienst gespeichert oder gespeichert werden.

In diesem Artikel werden die einzelnen Schritte im Datenfluss ausführlicher erläutert. Die nächsten Schritte sind [die Bereitstellung eines IoT-Connectors](deploy-iot-connector-in-azure.md) mithilfe von Gerätezuordnungen (normalisierungsschritt) und FHIR-Zielzuordnungen (der Transformationsschritt).

In den nächsten Abschnitten werden die Phasen beschrieben, in denen IoMT-Daten (Internet of Medical Things, Internet der medizinischen Dinge) durchlaufen werden, sobald sie von einem Event Hub und dem IoT-Connector empfangen wurden.

:::image type="content" source="media/iot-data-flow/iot-data-flow.png" alt-text="IoMT-Daten werden von IoT-Geräten in einen Event Hub übertragen. IoMT-Daten werden vom IoT-Connector erfasst, während sie normalisiert, gruppiert, transformiert und im FHIR-Dienst gespeichert werden." lightbox="media/iot-data-flow/iot-data-flow.png":::

## <a name="ingest"></a>Erfassen
Die Erfassung ist die erste Phase, in der Gerätedaten an den IoT-Connector empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird auf einem [Azure Event Hubs](../../event-hubs/index.yml)gehostet. Azure Event Hubs Plattform unterstützt eine hohe Skalierung und einen hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Darüber hinaus kann der IoT-Connector Nachrichten asynchron verarbeiten, wodurch die Notwendigkeit entfällt, dass Geräte warten müssen, während Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize
Normalisieren ist die nächste Phase, in der Gerätedaten vom obigen Event Hub abgerufen und mithilfe der Gerätezuordnungen verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. 

Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern bietet auch die Möglichkeit, eine Eingabenachricht in mehrere normalisierte Nachrichten zu pro projectieren. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group
Gruppe ist die nächste Phase, in der die normalisierten Nachrichten aus der vorherigen Phase mit drei verschiedenen Parametern gruppiert werden:

* Geräteidentität
* Messungstyp 
* Zeitraum

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine übersichtliche Möglichkeit zur Darstellung einer zeitbasierten Messreihe von einem Gerät in FHIR. Und der Zeitraum steuert die Latenz, mit der vom IoT-Connector generierte Beobachtungsressourcen in den FHIR-Dienst geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren
In der Transformationsphase werden gruppierte normalisierte Nachrichten über FHIR-Zielzuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die [Geräteressource](https://www.hl7.org/fhir/device.html) zusammen mit der zugehörigen [Patient-Ressource](https://www.hl7.org/fhir/patient.html) auch vom FHIR-Dienst abgerufen, indem der in der Nachricht enthaltene Gerätebezeichner verwendet wird. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
> Alle Identitätss lookups werden nach der Lösung zwischengespeichert, um die Auslastung des FHIR-Diensts zu verringern. Wenn Sie Geräte für mehrere Patienten wiederverwenden möchten, empfiehlt es sich, eine für den Patienten spezifische virtuelle Geräteressource zu erstellen und den Bezeichner des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn im FHIR-Dienst keine Geräteressource für einen bestimmten Gerätebezeichner vorhanden ist, hängt das Ergebnis vom Wert ab, `Resolution Type` der zum Zeitpunkt der Erstellung festgelegt wurde. Wenn die Einstellung auf den Wert `Lookup` festgelegt ist, wird die betreffende Nachricht ignoriert. Die Pipeline verarbeitet weiterhin andere eingehende Nachrichten. Wenn diese Einstellung auf festgelegt `Create` ist, erstellt der IoT-Connector eine reine Geräte- und Patientenressourcen im FHIR-Dienst.  

## <a name="persist"></a>Speichern
Sobald die FHIR-Beobachtungsressource in der Transformationsphase generiert wurde, wird die Ressource im FHIR-Dienst gespeichert. Wenn die FHIR-Ressource "Observation" neu ist, wird sie im FHIR-Dienst erstellt. Wenn die FHIR-Ressource "Observation" bereits vorhanden ist, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Geräte- und FHIR-Zielzuordnungen erstellen.

> [!div class="nextstepaction"]
> [Gerätezuordnungen](how-to-use-device-mappings.md)

> [!div class="nextstepaction"]
> [FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

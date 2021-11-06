---
title: IoT-Connector und Azure Machine Learning Service – Azure Healthcare-APIs
description: In diesem Artikel erfahren Sie, wie Sie den IoT-Connector und den Azure Machine Learning-Dienst verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 8150734243fda78805b5ef4cbf3ab318222b21cb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894760"
---
# <a name="iot-connector-and-azure-machine-learning-service"></a>IoT-Connector und Azure Machine Learning-Dienst

In diesem Artikel erfahren Sie mehr über die Verwendung des IoT-Connectors und Azure Machine Learning Service.

## <a name="iot-connector-and-azure-machine-learning-service-reference-architecture"></a>Referenzarchitektur für IoT-Connector und Azure Machine Learning-Dienst

Der IoT-Connector ermöglicht die nahtlose Integration von IoT-Geräten in Fast Healthcare Interoperability Resources -Dienste (FHIR&#174;). Diese Referenzarchitektur soll die Einführung von IoMT-Projekten (Internet of Medical Things) beschleunigen. Diese Lösung verwendet Azure Databricks für die Machine Learning (ML). Azure ML Services mit Kubernetes oder einem Partner ML Lösung kann jedoch in die Machine Learning Bewertungsumgebung passen.

Die vier Linienfarben zeigen die verschiedenen Teile der Datenreise.

- **Blau** = IoT-Daten an den FHIR-Dienst.
- **Grün** = Datenpfad für die Bewertung von IoT-Daten
- **Rot** = Heißer Pfad für Daten, um Ärzte über das Patientenrisiko zu informieren. Das Ziel des heißen Pfads besteht darin, so nah wie möglich an Echtzeit zu sein.
- **Orange** = Warmer Pfad für Daten. Unterstützung von Ärzte in der Patientenversorgung. Datenanforderungen werden in der Regel manuell oder nach einem Aktualisierungszeitplan ausgelöst.

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="Screenshot: Referenzarchitektur des IoT-Connectors und Machine Learning-Diensts" lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**Datenerfassung: Schritte 1 bis 5**

1. Daten vom IoT-Gerät oder über ein Gerätegateway, die an Azure IoT Hub/Azure IoT Edge gesendet werden.
2. Daten von Azure IoT Edge, die an Azure IoT Hub gesendet werden.
3. Kopie der unformatierten IoT-Gerätedaten, die zur Geräteverwaltung an eine sichere Speicherumgebung gesendet werden.
4. Die PHI IoMT-Nutzlast wird von Azure IoT Hub zum IoT-Connector verschoben. Mehrere Azure-Dienste werden durch ein IoT-Connectorsymbol dargestellt.
5. Drei Teile zur Nummer 5: a. IoT-Connector fordert Patientenressource vom FHIR-Dienst an. b. Der FHIR-Dienst sendet die Patient-Ressource zurück an den IoT-Connector. c. Die IoT-Patientenüberwachung wird im FHIR-Dienst erfasst.

**Machine Learning und KI-Datenroute – Schritte 6 bis 11**

6. Normalisierter nicht gruppierter Datenstrom, der an die Azure-Funktion gesendet wird (ML Eingabe).
7. Azure Function (ML Input) fordert die Patient-Ressource auf, mit der IoMT-Nutzlast zusammenzuführen.
8. Die IoMT-Nutzlast mit PHI wird zur Verteilung an Machine Learning Compute und Speicher an Event Hub gesendet.
9. Die PHI IoMT-Nutzlast wird zur Bewertung von Beobachtungen über längere Zeitfenster an Azure Data Lake Storage Gen 2 gesendet.
10. Die PHI IoMT-Nutzlast wird an Azure Databricks gesendet, um Fenster zu erstellen, Datenanpassungen und Datenbewertungen zu erstellen.
11. Der Azure Databricks fordert bei Bedarf mehr Patientendaten aus Data Lake an. a. Azure Databricks sendet auch eine Kopie der bewerteten Daten an den Data Lake.

**Koordination von Benachrichtigungen und Pflege – Schritte 12 bis 18**

**Heißer Pfad**

12. Azure Databricks sendet eine Nutzlast an eine Azure-Funktion (ML Ausgabe).
13. RiskAssessment- und/oder Flag-Ressource, die an den FHIR-Dienst übermittelt wird. a. Für jedes Beobachtungsfenster wird eine RiskAssessment-Ressource an den FHIR-Dienst übermittelt. b. Für Beobachtungsfenster, in denen die Risikobewertung außerhalb des zulässigen Bereichs liegt, sollte auch eine Flag-Ressource an den FHIR-Dienst übermittelt werden.
14. Bewertete Daten, die zum Weiterleiten an das entsprechende Pflegeteam an das Datenrepository gesendet werden. Azure SQL Server ist das Daten-Repository, das in diesem Entwurf aufgrund der nativen Interaktion mit Power BI verwendet wird.
15. Power BI Das Dashboard wird mit der Risikobewertungsausgabe in weniger als 15 Minuten aktualisiert.

**Warmer Pfad**

16. Power BI aktualisiert das Dashboard nach dem Zeitplan für die Datenaktualisierung. In der Regel länger als 15 Minuten zwischen Aktualisierungen.
17. Füllen Sie die Care Team-App mit aktuellen Daten auf.
18. Koordination der Behandlung durch Microsoft Teams für das Gesundheitswesen Patienten-App.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über ioT connector und Machine Learning Service Integration erfahren. Eine Übersicht über den IoT-Connector finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den IoT-Connector](iot-connector-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

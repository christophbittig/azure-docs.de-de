---
title: Häufig gestellte Fragen zum IoT-Connector – Azure Healthcare-APIs
description: Dieses Dokument enthält Antworten auf die häufig gestellten Fragen zum IoT-Connector.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 153a4c86d0f6f1b2617327360569cd92d1a318ca
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990242"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>Häufig gestellte Fragen zum IoT-Connector

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Hier sind einige der häufig gestellten Fragen zum IoT-Connector.

## <a name="iot-connector-the-basics"></a>IoT-Connector: Grundlagen

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>Was sind die Unterschiede zwischen dem Azure API for FHIR IoT-Connector (Vorschauversion) und dem IoT-Connector für Azure Healthcare-APIs?

Der IoT-Connector für Azure Healthcare-APIs ist der Nachfolger des Azure API for Fast Healthcare Interoperability Resources (FHIR&#174;) IoT-Connectors (Vorschau). 

Es wurden mehrere Verbesserungen eingeführt, z. B. Endpunkte für die Erfassung von Kundennachrichten (z. B. azure Event Hub), die Verwendung von verwalteten Identitäten und Azure Role-Based Access Control (Azure RBAC).

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Kann ich den IoT-Connector mit einem anderen FHIR-Dienst als dem FHIR-Dienst für Azure Healthcare-APIs verwenden?

Nein. Der IoT-Connector für Azure Healthcare-APIs unterstützt derzeit nur den Azure Healthcare APIs FHIR-Dienst für die Persistenz von Daten. Die Open-Source-Version des IoT-Connectors unterstützt die Verwendung verschiedener FHIR-Dienste. Weitere Informationen finden Sie im Abschnitt [Open-Source-Projekte.](iot-git-projects.md)  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>Welche Versionen von FHIR werden vom IoT-Connector unterstützt?

Der IoT-Connector unterstützt derzeit nur die Persistenz von [HL7 FHIR&#174; R4](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491). 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>Welche Abonnementkontingentgrenzen gelten für IoT Connector?

* 25 IoT-Connectors pro Abonnement (nicht anpassbar)
* 10 IoT-Connectors pro Arbeitsbereich (nicht anpassbar)
* Ein FHIR-Ziel* pro IoT-Connector (nicht anpassbar)

(* – FHIR-Ziel ist eine untergeordnete Ressource des IoT-Connectors)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Kann ich den IoT-Connector mit Gerätenachrichten von Apple&#174;,Google&#174; oder Fitbit&#174; verwenden?

Ja. Der IoT-Connector unterstützt Gerätenachrichten von allen diesen Plattformen. Weitere Informationen finden Sie im Abschnitt [Open-Source-Projekte.](iot-git-projects.md)  

## <a name="more-frequently-asked-questions"></a>Weitere häufig gestellte Fragen
[Häufig gestellte Fragen zu den Azure Healthcare-APIs](../healthcare-apis-faqs.md)

[Häufig gestellte Fragen zum FHIR-Dienst für Azure Healthcare-APIs](../fhir/fhir-faq.md)

[Häufig gestellte Fragen zum DICOM-Dienst für Azure Healthcare-APIs](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

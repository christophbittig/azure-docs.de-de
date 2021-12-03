---
title: Häufig gestellte Fragen zu Azure Healthcare-APIs
description: Dieses Dokument enthält Antworten auf die häufig gestellten Fragen zu den Azure Healthcare-APIs.
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: ginle
ms.openlocfilehash: 6ce51e4cf916baaa22d14e902872185901b4d5da
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988613"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Häufig gestellte Fragen zu Azure Healthcare-APIs (Vorschauversion)

Dies sind einige der häufig gestellten Fragen zu den Azure Healthcare-APIs.

## <a name="azure-healthcare-apis-the-basics"></a>Azure Healthcare-APIs: Grundlagen

### <a name="what-is-the-azure-healthcare-apis"></a>Was sind die Azure Healthcare-APIs?
Die Azure Healthcare-APIs sind eine vollständig verwaltete Gesundheitsdatenplattform, die den schnellen Austausch und die Persistenz von Geschützten Gesundheitsinformationen (Protected Health Information, PHI) und Gesundheitsdaten durch interoperable offene Branchenstandards wie Fast Healthcare Interoperability Resources (FHIR®) und Digitale Bildverarbeitung und Kommunikation in der Medizin (DICOM®) ermöglicht.

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Was können Sie mit den Azure Healthcare-APIs tun?
Azure Healthcare-APIs ermöglichen Ihnen: 

* Verbinden Sie schnell unterschiedliche Integritätsdatenquellen und Formate wie strukturierte, Bildverarbeitungs- und Gerätedaten, und normalisieren Sie sie so, dass sie in der Cloud beibehalten werden.

* Transformieren und Erfassung von Daten in FHIR. Beispielsweise können Sie Integritätsdaten aus älteren Formaten wie HL7v2 oder CDA oder aus ioT-Hochfrequenzdaten in proprietären Geräteformaten in FHIR transformieren.

* Verbinden Daten, die in Gesundheits-APIs gespeichert sind, mit Diensten im gesamten Azure-Ökosystem wie Synapse und Produkten von Microsoft wie Teams, um neue Erkenntnisse durch Analysen und maschinelles Lernen zu gewinnen und neue Workflows sowie verbindungen mit SMART on FHIR-Anwendungen zu ermöglichen.

* Verwalten Sie erweiterte Workloads mit Unternehmensfeatures, die Zuverlässigkeit, Skalierbarkeit und Sicherheit bieten, um sicherzustellen, dass Ihre Daten geschützt sind und die für die Gesundheitsbranche erforderlichen Datenschutz- und Compliancezertifizierungen erfüllen.

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>Kann ich meine vorhandene Produktionsworkload von Azure API for FHIR zu Healthcare-APIs migrieren?
Nein, leider bieten wir derzeit keine Migrationsfunktionen an. 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Wie lauten die Preise für Azure Healthcare-APIs?
Während der Public Preview-Phase stehen Azure Healthcare-APIs kostenlos zur Verfügung.

### <a name="what-regions-are-healthcare-apis-available"></a>In welchen Regionen sind ApIs für das Gesundheitswesen verfügbar?
Aktuelle Informationen finden [Sie auf der Seite](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir) Produkte nach Region. 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Welche Abonnementkontingentgrenzen gelten für die Azure Healthcare-APIs?

#### <a name="workspace-logical-container"></a>Arbeitsbereich (logischer Container):
* 200 Instanzen pro Abonnement (nicht anpassbar)

#### <a name="dicom-service"></a>DICOM-Dienst:
* 800 Instanzen pro Abonnement (nicht anpassbar)
* 10 DICOM-Instanzen pro Arbeitsbereich (nicht anpassbar)

#### <a name="fhir-service"></a>FHIR-Dienst:
* 25 Instanzen pro Abonnement (nicht anpassbar)
* 10 FHIR-Instanzen pro Arbeitsbereich (nicht anpassbar)

#### <a name="iot-connector"></a>IoT-Connector:
* 25 IoT-Connectors pro Abonnement (nicht anpassbar)
* 10 IoT-Connectors pro Arbeitsbereich (nicht anpassbar)
* Ein FHIR-Ziel* pro IoT-Connector (nicht anpassbar)

(* – Das FHIR-Ziel ist eine untergeordnete Ressource des IoT-Connectors.

## <a name="more-frequently-asked-questions"></a>Weitere häufig gestellte Fragen
[Häufig gestellte Fragen zum FHIR-Dienst für Azure Healthcare-APIs](./fhir/fhir-faq.md)

[Häufig gestellte Fragen zum DICOM-Dienst für Azure Healthcare-APIs](./dicom/dicom-services-faqs.yml)

[Häufig gestellte Fragen zum IoT-Connector für Azure Healthcare-APIs](./iot/iot-connector-faqs.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

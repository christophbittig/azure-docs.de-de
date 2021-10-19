---
title: Häufig gestellte Fragen zu Azure Healthcare-APIs
description: Dieses Dokument enthält Antworten auf die häufig gestellten Fragen zu den Azure Healthcare-APIs.
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 07/16/2021
ms.author: ginle
ms.openlocfilehash: 3cccd2a1e418dbc66a5a8c1bfa665c6c3276552d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339978"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Häufig gestellte Fragen zu Azure Healthcare-APIs (Vorschauversion)

Dies sind einige der häufig gestellten Fragen zu den Azure Healthcare-APIs.

## <a name="azure-healthcare-apis-the-basics"></a>Azure Healthcare-APIs: Grundlagen

### <a name="what-is-the-azure-healthcare-apis"></a>Was sind die Azure Healthcare-APIs?
Die Azure Healthcare-APIs sind eine vollständig verwaltete Gesundheitsdatenplattform, die den schnellen Austausch und die Persistenz von geschützten Integritätsinformationen (Protected Health Information, PHI) und Integritätsdaten durch interoperable offene Branchenstandards wie Fast Healthcare Interoperability Resources (FHIR®) und digitale Bilderstellung und Kommunikation in der Gesundheitswesen (DICOM®) ermöglicht.

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Was ermöglichen Ihnen die Azure Healthcare-APIs?
Azure Healthcare-APIs ermöglichen Ihnen: 

* Verbinden Sie schnell unterschiedliche Integritätsdatenquellen und -formate wie strukturierte, Imageerstellungs- und Gerätedaten, und normalisieren Sie sie so, dass sie in der Cloud beibehalten werden.

* Transformieren und Erfassen von Daten in FHIR. Beispielsweise können Sie Integritätsdaten aus Legacyformaten wie HL7v2 oder CDA oder aus IoT-Daten mit hoher Frequenz in geräteeigenen Formaten in FHIR transformieren.

* Verbinden Ihre in Gesundheits-APIs gespeicherten Daten mit Diensten im gesamten Azure-Ökosystem wie Synapse und Microsoft-Produkten wie Teams, um neue Erkenntnisse durch Analysen und Machine Learning zu gewinnen und neue Workflows sowie verbindungen mit SMART on FHIR-Anwendungen zu ermöglichen.

* Verwalten Sie erweiterte Workloads mit Unternehmensfeatures, die Zuverlässigkeit, Skalierbarkeit und Sicherheit bieten, um sicherzustellen, dass Ihre Daten geschützt sind und die für die Gesundheitsbranche erforderlichen Datenschutz- und Compliancezertifizierungen erfüllen.

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>Kann ich meine vorhandene Produktionsworkload von Azure API for FHIR zu Gesundheits-APIs migrieren?
Nein, leider bieten wir derzeit keine Migrationsfunktionen an. 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Wie lauten die Preise für Azure Healthcare-APIs?
Während der Phase der öffentlichen Vorschauphase können Sie Azure Healthcare-APIs kostenlos verwenden.

### <a name="what-regions-are-healthcare-apis-available"></a>In welchen Regionen sind APIs für das Gesundheitswesen verfügbar?
Aktuelle Informationen finden Sie auf der Seite [Produkte nach Region.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir) 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Welche Abonnementkontingentgrenzen gelten für die Azure Healthcare-APIs?

#### <a name="workspace-logical-container"></a>Arbeitsbereich (logischer Container):
* 200 Instanzen pro Abonnement (nicht anpassbar)

#### <a name="dicom-server"></a>DICOM-Server:
* 800 Instanzen pro Abonnement (nicht anpassbar)
* 10 DICOM-Instanzen pro Arbeitsbereich (nicht anpassbar)

#### <a name="fhir-server"></a>FHIR-Server:
* 25 Instanzen pro Abonnement (nicht anpassbar)
* 10 FHIR-Instanzen pro Arbeitsbereich (nicht anpassbar)

#### <a name="iot-connector"></a>IoT Connector:
* 25 IoT-Connectors pro Abonnement (nicht anpassbar)
* 10 IoT-Connectors pro Arbeitsbereich (nicht anpassbar)
* 1 FHIR-Ziel* pro IoT Connector (nicht anpassbar)

## <a name="more-frequently-asked-questions"></a>Weitere häufig gestellte Fragen
[Häufig gestellte Fragen zum FHIR-Dienst der Azure Healthcare-APIs](./fhir/fhir-faq.md)

[Häufig gestellte Fragen zum DICOM-Dienst der Azure Healthcare-APIs](./dicom/dicom-services-faqs.yml)



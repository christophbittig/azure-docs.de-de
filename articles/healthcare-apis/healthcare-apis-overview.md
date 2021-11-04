---
title: Was sind die Azure Healthcare-APIs?
description: Dieser Artikel enthält eine Übersicht über die Azure Healthcare-APIs.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 8078dcb2cc0db6786881a834540922e66f47eac8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468873"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Was sind Azure Healthcare-APIs (Vorschauversion)?

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die Integritätsdaten, mit der Sie arbeiten, sind in mehreren Systemen und Formaten fragmentiert. Die Verwaltung dieser Daten ist schwierig genug, und der Versuch, erkenntnisse daraus zu gewinnen, erscheint unmöglich. Sie müssen eine Möglichkeit finden, all diese unterschiedlichen Systeme und Daten zusammenzuführen. Ein einheitlicher Ansatz für Gesundheitsdaten ermöglicht es Ihnen, operative und klinische Erkenntnisse zu gewinnen, neue Endbenutzeranwendungen zu verbinden oder neue Forsprojekte zu ermöglichen. Azure Healthcare-APIs sind eine Reihe von Tools und Connectors, mit denen Sie das Gesundheitswesen durch Erkenntnisse verbessern können, die ermittelt werden, indem sie unterschiedliche PHI-Gruppen zusammenführen und end-to-end mit Tools für Maschinelles Lernen, Analysen und KI verbinden.

Azure Healthcare-APIs bieten die folgenden Vorteile:
* Ermöglichen Sie es neuen Workloads, PHI zu nutzen, indem Sie es ermöglichen, die Daten auf konsistente Weise an einem Ort zu sammeln und darauf zuzugreifen.
* Entdecken Sie neue Erkenntnisse, indem Sie unterschiedliche PHI-Funktionen zusammenführen und end-to-end mit Tools für Maschinelles Lernen, Analysen und KI verbinden.
* Bauen Sie auf einer vertrauenswürdigen Cloud auf, und vertrauen Sie darauf, wie geschützte Integritätsinformationen verwaltet, gespeichert und verfügbar gemacht werden.
Die neue Microsoft Azure Healthcare-APIs unterstützt neben FHIR auch andere Datenstandards der Gesundheitsbranche, z. B. DICOM, und erweitert die Interoperabilität von Gesundheitsdaten. Das Geschäftsmodell und die Infrastrukturplattform wurden umgestaltet, um die Erweiterung und Einführung verschiedener und zukünftiger Datenstandards im Gesundheitswesen zu ermöglichen. Kunden können Gesundheitsdaten verschiedener Typen übergreifend für Gesundheitsstandards unter demselben Compliance-Schirm verwenden. In den verwalteten Dienst wurden Tools integriert, mit denen Kunden Daten aus älteren oder geräteeigenen Formaten in FHIR transformieren können. Einige dieser Tools wurden bereits entwickelt und open-sourced entwickelt. Andere sind neu.

Azure Healthcare-APIs ermöglichen Ihnen: 
* Verbinden Sie schnell unterschiedliche Integritätsdatenquellen und -formate wie strukturierte, Imageerstellungs- und Gerätedaten, und normalisieren Sie sie so, dass sie in der Cloud beibehalten werden.
* Transformieren und Erfassen von Daten in FHIR. Beispielsweise können Sie Integritätsdaten aus Legacyformaten wie HL7v2 oder CDA oder aus IoT-Daten mit hoher Frequenz in geräteeigenen Formaten in FHIR transformieren.
* Verbinden Ihre in Gesundheits-APIs gespeicherten Daten mit Diensten im gesamten Azure-Ökosystem wie Synapse und Microsoft-Produkten wie Teams, um neue Erkenntnisse durch Analysen und Machine Learning zu gewinnen und neue Workflows sowie verbindungen mit SMART on FHIR-Anwendungen zu ermöglichen.
* Verwalten Sie erweiterte Workloads mit Unternehmensfeatures, die Zuverlässigkeit, Skalierbarkeit und Sicherheit bieten, um sicherzustellen, dass Ihre Daten geschützt sind und die für die Gesundheitsbranche erforderlichen Datenschutz- und Compliancezertifizierungen erfüllen.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Was sind die wichtigsten Unterschiede zwischen Azure Healthcare-APIs und Azure API for FHIR?

**Verknüpfte Dienste**

Die Azure Healthcare-APIs unterstützen jetzt mehrere Integritätsdatenstandards für den Austausch strukturierter Daten. Mit einer einzelnen Sammlung von Azure Healthcare-APIs können Sie mehrere Instanzen verschiedener Diensttypen (FHIR-Dienst, DICOM-Dienst und IoT Connector) bereitstellen, die nahtlos miteinander funktionieren.

**Einführung in den DICOM-Dienst**

Azure Healthcare-APIs umfassen jetzt Unterstützung für DICOM-Dienste. DICOM ermöglicht den sicheren Austausch von Bilddaten und den zugehörigen Metadaten. DICOM ist der internationale Standard zum Übertragen, Speichern, Abrufen, Drucken, Verarbeiten und Anzeigen von medizinischen Bildinformationen und ist der primäre medizinische Bildverarbeitungsstandard, der im Gesundheitswesen akzeptiert wird. Weitere Informationen zum DICOM-Dienst finden Sie unter [Übersicht über DICOM.](./dicom/dicom-services-overview.md)

**Inkrementelle Änderungen am FHIR-Dienst**

Für den sicheren Austausch von FHIR-Daten bietet die Gesundheits-APIs einige inkrementelle Funktionen, die im Azure API for FHIR nicht verfügbar sind. 
* Unterstützung für Transaktionen: In Healthcare-APIs unterstützt der FHIR-Dienst Transaktionsbündel. Weitere Informationen zu Transaktionspaketen finden Sie unter HL7.org und unter Batch-/Transaktionsinteraktionen.
* Verbesserungen der verketteten Suche: Verkettete Suche & Reserve Verkettete Suche sind nicht mehr durch 100 Elemente pro Unterabfrage beschränkt.


## <a name="next-steps"></a>Nächste Schritte

Um mit der Arbeit mit den Azure Healthcare-APIs zu beginnen, folgen Sie den fünfminütigen Schnellstarts zum Bereitstellen eines Arbeitsbereichs.

> [!div class="nextstepaction"]
> [Bereitstellen des Arbeitsbereichs im Azure-Portal](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [Übersicht über den Arbeitsbereich](workspace-overview.md)

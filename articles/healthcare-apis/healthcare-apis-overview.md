---
title: Was sind die Azure Healthcare-APIs?
description: Dieser Artikel enthält eine Übersicht über die Azure Healthcare-APIs.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 01d808813d944d243582cc631f960a241061b46f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783952"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Was sind Azure Healthcare-APIs (Vorschauversion)?

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die Integritätsdaten, mit der Sie arbeiten, sind über mehrere Systeme und Formate hinweg fragmentiert. Die Verwaltung dieser Daten ist schwierig genug, und der Versuch, daraus Erkenntnisse zu gewinnen, erscheint unmöglich. Sie müssen eine Möglichkeit finden, all diese unterschiedlichen Systeme und Daten zusammenzubringen. Ein einheitlicher Ansatz für Gesundheitsdaten ermöglicht es Ihnen, operative und medizinische Erkenntnisse zu gewinnen, neue Endbenutzeranwendungen zu verbinden oder neue Forschungsprojekte zu ermöglichen. Azure Healthcare-APIs sind eine Reihe von Tools und Connectors, mit denen Sie das Gesundheitswesen durch Erkenntnisse verbessern können, die durch die Zusammenführung unterschiedlicher PHI-Sätze und die End-to-End-Verbindung mit Tools für maschinelles Lernen, Analysen und KI gewonnen werden.

Azure Healthcare-APIs bieten die folgenden Vorteile:
* Ermöglichen Sie es neuen Workloads, PHI zu nutzen, indem Sie es ermöglichen, die Daten auf konsistente Weise an einem Ort zu sammeln und darauf zu zugreifen.
* Entdecken Sie neue Erkenntnisse, indem Sie unterschiedliche PHI miteinander verbinden und end-to-end mit Tools für maschinelles Lernen, Analysen und KI verbinden.
* Bauen Sie auf einer vertrauenswürdigen Cloud auf, und vertrauen Sie darauf, wie geschützte Integritätsinformationen verwaltet, gespeichert und verfügbar gemacht werden.
Die neuen Microsoft Azure Healthcare-APIs unterstützen zusätzlich zu FHIR andere Datenstandards der Gesundheitsbranche wie DICOM, um die Interoperabilität von Gesundheitsdaten zu erweitern. Das Geschäftsmodell und die Infrastrukturplattform wurden überarbeitet, um die Erweiterung und Einführung verschiedener und zukünftiger Datenstandards im Gesundheitswesen zu unterstützen. Kunden können Gesundheitsdaten verschiedener Typen über Gesundheitsstandards hinweg unter demselben Compliance-Schirm verwenden. Tools wurden in den verwalteten Dienst integriert, mit denen Kunden Daten aus älteren oder gerätespezifischen Formaten in FHIR transformieren können. Einige dieser Tools wurden bereits entwickelt und open-sourced; Andere werden neu sein.

Azure Healthcare-APIs ermöglichen Ihnen: 
* Verbinden Sie schnell unterschiedliche Integritätsdatenquellen und Formate wie strukturierte, Bildverarbeitungs- und Gerätedaten, und normalisieren Sie sie so, dass sie in der Cloud beibehalten werden.
* Transformieren und Aufnehmen von Daten in FHIR. Beispielsweise können Sie Integritätsdaten aus älteren Formaten wie HL7v2 oder CDA oder aus ioT-Hochfrequenzdaten in gerätespezifischen Formaten in FHIR transformieren.
* Verbinden Daten, die in ApIs für das Gesundheitswesen gespeichert sind, mit Diensten im gesamten Azure-Ökosystem wie Synapse und Produkten von Microsoft wie Teams, um neue Erkenntnisse durch Analysen und maschinelles Lernen zu gewinnen und neue Workflows sowie verbindungen mit SMART on FHIR-Anwendungen zu ermöglichen.
* Verwalten Sie erweiterte Workloads mit Unternehmensfeatures, die Zuverlässigkeit, Skalierbarkeit und Sicherheit bieten, um sicherzustellen, dass Ihre Daten geschützt sind und die für die Gesundheitsbranche erforderlichen Datenschutz- und Compliancezertifizierungen erfüllen.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Was sind die wichtigsten Unterschiede zwischen Azure Healthcare-APIs und Azure API for FHIR?

**Verknüpfte Dienste**

Die Azure Healthcare-APIs unterstützen jetzt mehrere Standards für Gesundheitsdaten für den Austausch strukturierter Daten. Mit einer einzelnen Sammlung von Azure Healthcare-APIs können Sie mehrere Instanzen verschiedener Diensttypen (FHIR-Dienst, DICOM-Dienst und IoT Connector) bereitstellen, die nahtlos miteinander funktionieren.

**Einführung in den DICOM-Dienst**

Azure Healthcare-APIs bieten jetzt Unterstützung für DICOM-Dienste. DICOM ermöglicht den sicheren Austausch von Bilddaten und den zugehörigen Metadaten. DICOM ist der internationale Standard zum Übertragen, Speichern, Abrufen, Drucken, Verarbeiten und Anzeigen von medizinischen Bildverarbeitungsinformationen und ist der primäre medizinische Bildverarbeitungsstandard, der im Gesundheitswesen akzeptiert wird. Weitere Informationen zum DICOM-Dienst finden Sie unter [Übersicht über DICOM](./dicom/dicom-services-overview.md).

**Inkrementelle Änderungen am FHIR-Dienst**

Für den sicheren Austausch von FHIR-Daten bieten ApIs für das Gesundheitswesen einige inkrementelle Funktionen, die in der Azure API for FHIR. 
* Unterstützung für Transaktionen: In ApIs für das Gesundheitswesen unterstützt der FHIR-Dienst Transaktionsbündel. Weitere Informationen zu Transaktionspaketen finden Sie unter HL7.org und unter Batch-/Transaktionsinteraktionen.
* Verbesserungen bei der verketteten Suche: Verkettete & Reserve Chained Search sind nicht mehr durch 100 Elemente pro Unterabfrage beschränkt.


## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die fünfminütige Schnellstart-Anweisung zum Bereitstellen eines Arbeitsbereichs, um mit der Arbeit mit den Azure Healthcare-APIs zu beginnen.

> [!div class="nextstepaction"]
> [Bereitstellen eines Arbeitsbereichs im Azure-Portal](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [Übersicht über den Arbeitsbereich](workspace-overview.md)

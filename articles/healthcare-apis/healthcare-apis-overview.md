---
title: Was sind die Azure Healthcare-APIs?
description: Dieser Artikel enthält eine Übersicht über die Azure Healthcare-APIs.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: f243f43e50c4739cda36ee8d2374772df436d9f7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370382"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Was sind Azure Healthcare-APIs (Vorschauversion)?

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Azure Healthcare-APIs sind eine Reihe verwalteter API-Dienste, die auf offenen Standards und Frameworks basieren, die Workflows ermöglichen, das Gesundheitswesen zu verbessern und skalierbare und sichere Lösungen für das Gesundheitswesen anzubieten. Die Verwendung einer Reihe verwalteter API-Dienste und -Frameworks, die für die Gesundheitsbranche bestimmt sind, ist wichtig und vorteilhaft, da die von Patienten und Gesundheitskunden gesammelten Gesundheitsdaten aus mehreren Systemen, Gerätetypen und Datenformaten fragmentiert werden können. Das Gewinnen von Erkenntnissen aus Gesundheitsdaten ist eines der größten Hindernisse für die Aufrechterhaltung der Bevölkerung und der persönlichen Integrität und des allgemeinen Verständnisses. Das Zusammenführen unterschiedlicher Systeme, Workflows und Integritätsdaten ist heute wichtiger. Ein einheitlicher und ausgerichteter Ansatz für den Zugriff auf Gesundheitsdaten, Standardisierung und Trenderfassung würde die Ermittlung operativer und klinischer Erkenntnisse ermöglichen. Wir können den Prozess der Verbindungsherstellung mit neuen Geräteanwendungen optimieren und neue Forsprojekte ermöglichen. Die Verwendung von Azure Healthcare-APIs als skalierbare und sichere Gesundheitslösung kann Workflows ermöglichen, das Gesundheitswesen durch Erkenntnisse zu verbessern, die ermittelt werden, indem datasets für geschützte Gesundheitsinformationen (Protected Health Information, PHI) zusammengeführt und end-to-end mit Tools für Maschinelles Lernen, Analysen und KI verbunden werden. 

Azure Healthcare-APIs bieten die folgenden Vorteile:
* Ermöglichen Sie es neuen Workloads, PHI zu nutzen, indem Sie es ermöglichen, die Daten auf konsistente Weise an einem Ort zu sammeln und darauf zuzugreifen.
* Entdecken Sie neue Erkenntnisse, indem Sie unterschiedliche PHI-Funktionen zusammenführen und end-to-end mit Tools für Maschinelles Lernen, Analysen und KI verbinden.
* Bauen Sie auf einer vertrauenswürdigen Cloud auf, und vertrauen Sie darauf, wie geschützte Integritätsinformationen verwaltet, gespeichert und verfügbar gemacht werden.
Die neue Microsoft Azure Healthcare-APIs unterstützt neben FHIR auch andere Datenstandards der Gesundheitsbranche, z. B. DICOM, und erweitert die Interoperabilität von Gesundheitsdaten. Das Geschäftsmodell und die Infrastrukturplattform wurden umgestaltet, um die Erweiterung und Einführung verschiedener und zukünftiger Datenstandards im Gesundheitswesen zu ermöglichen. Kunden können Gesundheitsdaten verschiedener Typen übergreifend für Gesundheitsstandards unter demselben Compliancebereich verwenden. Tools wurden in den verwalteten Dienst integriert, mit denen Kunden Daten aus älteren oder geräteeigenen Formaten in FHIR transformieren können. Einige dieser Tools wurden bereits entwickelt und open-sourced entwickelt. Andere sind neu.

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

Für den sicheren Austausch von FHIR-Daten bieten ApIs des Gesundheitswesens einige inkrementelle Funktionen, die im Azure API for FHIR nicht verfügbar sind. 
* Unterstützung für Transaktionen: In Healthcare-APIs unterstützt der FHIR-Dienst Transaktionsbündel. Weitere Informationen zu Transaktionspaketen finden Sie unter HL7.org und unter Batch-/Transaktionsinteraktionen.
* Verbesserungen der verketteten Suche: Verkettete Suche & Reserve Verkettete Suche sind nicht mehr durch 100 Elemente pro Unterabfrage beschränkt.


## <a name="next-steps"></a>Nächste Schritte

Um mit der Arbeit mit den Azure Healthcare-APIs zu beginnen, befolgen Sie die fünfminütige Schnellstartanschrift zum Bereitstellen eines Arbeitsbereichs.

> [!div class="nextstepaction"]
> [Bereitstellen des Arbeitsbereichs im Azure-Portal](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [Übersicht über den Arbeitsbereich](workspace-overview.md)

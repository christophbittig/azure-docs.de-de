---
title: Was ist der FHIR-Dienst?
description: Der FHIR-Dienst ermöglicht einen schnellen Datenaustausch über FHIR-APIs. Führen Sie mit einem verwalteten Clouddienst die Erfassung, Verwaltung und dauerhafte Speicherung von geschützten Gesundheitsdaten (Protected Health Information, PHI) durch.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 08/03/2021
ms.author: chrupa
ms.openlocfilehash: 5ddbef4874a5efd9f63a099f8da52204065b6b4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779064"
---
# <a name="what-is-fhirreg-service"></a>Was ist der &reg; FHIR-Dienst?

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) ermöglicht einen schnellen Datenaustausch über Fast Healthcare Interoperability Resources-APIs (FHIR®), die durch ein verwaltetes PaaS-Angebot (Platform-as-a-Service) in der Cloud unterstützt werden. Er erleichtert allen Personen, die mit Gesundheitsdaten arbeiten, das Erfassen, Verwalten und dauerhafte Speichern von geschützten Gesundheitsdaten ([Protected Health Information, PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)) in der Cloud: 

- Verwalteter FHIR-Dienst, Bereitstellung in der Cloud innerhalb weniger Minuten 
- Enterprise FHIR-basierten Endpunkt in Azure für den Datenzugriff und die Speicherung im FHIR-Format
- Hohe Leistung und geringe Latenz
- Sichere Verwaltung von geschützten Informationen zur Gesundheit (PHI, Protected Health Information) in einer konformen Cloudumgebung
- SMART-Ansatz für FHIR für mobile und Webimplementierungen
- Bedarfsabhängiges Steuern Ihrer eigenen Daten per rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC)
- Nachverfolgen von Überwachungsprotokollen für Zugriff, Erstellung, Änderung und Lesevorgänge in jedem Datenspeicher

Mit dem FHIR-Dienst können Sie einen FHIR-Server in nur wenigen Minuten erstellen und bereitstellen, um die elastische Skalierung der Cloud zu nutzen. Die Azure-Dienste, die den FHIR-Dienst nutzen, sind für eine schnelle Leistung konzipiert, unabhängig davon, welche Größe Datasets Sie verwalten.

Mit der FHIR-API und dem konformen Datenspeicher können Sie eine sichere Verbindung mit einem beliebigen System herstellen, für das FHIR-APIs genutzt werden, und damit interagieren.  Microsoft übernimmt die Anforderungen in Bezug auf Betrieb, Wartung, Aktualisierung und Compliance im Rahmen des PaaS-Angebots, um Ihre eigenen Ressourcen für Betrieb und Entwicklung zu entlasten. 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Nutzen der Leistungsfähigkeit Ihrer Daten mit FHIR

In der Gesundheitsbranche werden Gesundheitsdaten derzeit mit Hochdruck auf den neuen Standard [FHIR&reg;](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources) umgestellt. FHIR ermöglicht die Nutzung eines stabilen, erweiterbaren Datenmodells mit einer Standardisierung in Bezug auf die Semantik und den Datenaustausch, damit alle Systeme, für die FHIR verwendet wird, zusammenarbeiten können.  Indem Sie Ihre Daten in das FHIR-Format transformieren, können Sie vorhandene Datenquellen schnell verbinden, z. B. Systeme mit elektronischen Gesundheitsakten oder Forschungsdatenbanken. Darüber hinaus ermöglicht FHIR auch den schnellen Austausch von Daten in modernen Implementierungen der mobilen Entwicklung und Webentwicklung. Vor allem kann per FHIR die Datenerfassung vereinfacht und die Entwicklung mit Analyse- und Machine Learning-Tools beschleunigt werden.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Sicheres Verwalten von Gesundheitsdaten in der Cloud

Der FHIR-Dienst ermöglicht den Datenaustausch über konsistente RESTful- und FHIR-APIs, die auf der HL7 FHIR-Spezifikation basieren. Der Dienst basiert auf einem Angebot mit verwaltetem PaaS-Ansatz in Azure und verfügt auch über eine skalierbare und sichere Umgebung für die Verwaltung und Speicherung von geschützten Gesundheitsdaten (Protected Health Information, PHI) im nativen FHIR-Format.  

### <a name="free-up-your-resources-to-innovate"></a>Mehr Zeit für Innovationen für Ihre Ressourcen

Sie könnten Ressourcen in die Entwicklung und Ausführung Ihres eigenen FHIR-Servers investieren, aber mit dem FHIR-Dienst in den Azure Healthcare-APIs übernimmt Microsoft die Workload von Vorgängen, Wartung, Updates und Complianceanforderungen, sodass Sie Ihre eigenen Betriebs- und Entwicklungsressourcen frei geben können.

### <a name="enable-interoperability-with-fhir"></a>Interoperabilität mit FHIR

Mithilfe des FHIR-Diensts können Sie eine Verbindung mit jedem System herstellen, das FHIR-APIs für Lese-, Schreib-, Such- und andere Funktionen nutzt. Der Dienst kann als leistungsfähiges Tool eingesetzt werden, um Konsolidierungen und Normalisierungen durchzuführen. Er ermöglicht die Nutzung von maschinellem Lernen mit klinischen Daten aus elektronischen Gesundheitsakten, Dashboards für Ärzte und Patienten, Programmen zur Remoteüberwachung oder mit Datenbanken außerhalb Ihres Systems, die über FHIR-APIs verfügen.

### <a name="control-data-access-at-scale"></a>Bedarfsabhängiges Steuern des Datenzugriffs

Sie haben die Kontrolle über Ihre Daten. Mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) können Sie die Speicherung und den Zugriff für Ihre Daten verwalten. Indem Sie die Sicherheit erhöhen und den Verwaltungsaufwand reduzieren, bestimmen Sie, wer Zugriff auf die von Ihnen erstellten Datasets hat. Dies basiert auf den Rollendefinitionen, die Sie für Ihre Umgebung erstellen.  

### <a name="secure-your-data"></a>Sichern der Daten

Schützen Sie Ihre geschützten Gesundheitsdaten mit einzigartigen Sicherheitsfunktionen (Security Intelligence). Ihre Daten sind für jede API-Instanz in einer eindeutigen Datenbank isoliert und durch die Möglichkeit eines Failovers über mehrere Regionen hinweg geschützt. Der FHIR-Dienst implementiert eine mehrschichtige, tiefgreifende Verteidigung und einen erweiterten Bedrohungsschutz für Ihre Daten.  

## <a name="applications-for-the-fhir-service"></a>Anwendungen für den FHIR-Dienst

FHIR-Server sind wichtige Tools für die Interoperabilität von Gesundheitsdaten. Der FHIR-Dienst ist als API und Dienst konzipiert, die Sie schnell erstellen, bereitstellen und verwenden können. Mit der Erweiterung des FHIR-Standards im Gesundheitswesen werden die Anwendungsfälle weiter zunehmen, aber einige anfängliche Kundenanwendungen, in denen der FHIR-Dienst nützlich ist, sind unten aufgeführt: 

- **Start/IoT und App-Entwicklung:**  Kunden, die eine patienten- oder anbieterorientierte App (mobil oder web) entwickeln, können den FHIR-Dienst als vollständig verwalteten Back-End-Dienst nutzen. Der FHIR-Dienst bietet eine wertvolle Ressource, da Kunden Daten verwalten und in einer sicheren Cloudumgebung austauschen können, die für Integritätsdaten konzipiert ist, smart on FHIR-Implementierungsrichtlinien nutzen und die Nutzung ihrer Technologie durch alle Anbietersysteme ermöglichen können (z. B. haben die meisten EHRs FHIR-Lese-APIs aktiviert).   

- **Umgebungen im Gesundheitswesen:**  Elektronische Gesundheitsakten sind in vielen Klinikumgebungen meist die Hauptinformationsquelle. Es ist aber nicht ungewöhnlich, dass Anbieter über mehrere Datenbanken verfügen, die nicht miteinander verbunden sind, oder dass Daten in unterschiedlichen Formaten gespeichert werden.  Mithilfe des FHIR-Diensts als Dienst, der auf diesen Systemen liegt, können Sie Daten im FHIR-Format standardisieren.  Dies ermöglicht den Datenaustausch in einem einheitlichen Format über mehrere Systeme hinweg. 

- **Forschung:** Forscher im Gesundheitswesen finden den FHIR-Standard im Allgemeinen und den FHIR-Dienst nützlich, da er Daten um ein gängiges FHIR-Datenmodell normalisiert und die Workload für maschinelles Lernen und Die gemeinsame Nutzung von Daten reduziert.
Exchange von Daten über den FHIR-Dienst stellt Überwachungsprotokolle und Zugriffssteuerungen zur Verfügung, mit denen der Datenfluss und wer Zugriff auf welche Datentypen hat. 

## <a name="fhir-from-microsoft"></a>FHIR von Microsoft

FHIR-Funktionen von Microsoft sind in drei Konfigurationen verfügbar:

* Der FHIR-Dienst in den Azure Healthcare-APIs – ein PaaS-Angebot in Azure, das einfach in der Azure-Portal bereitgestellt und von Microsoft verwaltet wird. Umfasst die Möglichkeit, andere Datasets wie DICOM im gleichen Arbeitsbereich bereitstellen zu können. Dies ist in Public Preview. 
* Azure API for FHIR: Ein PaaS-Angebot in Azure, das einfach in der Azure-Portal bereitgestellt und von Microsoft verwaltet wird. Diese Implementierung enthält nur FHIR-Daten und ist ein allgemeines Produkt. 
* FHIR-Server für Azure: Ein Open-Source-Projekt, das unter Ihrem Azure-Abonnement bereitgestellt werden kann. Sie finden es auf GitHub unter https://github.com/Microsoft/fhir-server.

Für Anwendungsfälle, bei denen der FHIR-Server erweitert oder angepasst werden muss oder der Zugriff auf zugrunde liegende Dienste, z. B. die Datenbank, erforderlich ist, ohne dass der Weg über die FHIR-APIs gewählt wird, sollten Entwickler die Open-Source-Version des FHIR-Servers für Azure wählen.  Für die Implementierung einer einsatzbereiten, produktionsbereiten FHIR-API und eines Back-End-Diensts, bei denen auf persistente Daten nur über die FHIR-API zugegriffen werden soll, sollten Entwickler den FHIR-Dienst auswählen.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die fünfminütige Schnellstartanleitung zum Bereitstellen des FHIR-Diensts, um mit der Arbeit mit dem FHIR-Dienst zu beginnen.

>[!div class="nextstepaction"]
>[Bereitstellen des FHIR-Diensts](fhir-portal-quickstart.md)

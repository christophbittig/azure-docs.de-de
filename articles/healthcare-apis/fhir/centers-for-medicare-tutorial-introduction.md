---
title: 'Tutorial: Einführung in Centers for Signaturre und Signaturid Services (CMS) – FHIR-Dienst'
description: Stellt eine Reihe von Tutorials vor, die sich auf die Regel "Interoperabilität und Patientenzugriff" von Center for Line Services (CMS) beziehen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/05/2021
ms.openlocfilehash: 0f1c14976aae6b0335d968a2967e48b89b997f28
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778335"
---
# <a name="introduction-centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule"></a>Einführung: Regel für Interoperabilität und Patientenzugriff von Centers for Signaturre und Signaturid Services (CMS)

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Reihe von Tutorials wird eine übersichtsbasierte Zusammenfassung der Regel für Interoperabilität und Patientenzugriff von Center for Line Services (CMS) und der technischen Anforderungen behandelt, die in dieser Regel beschrieben werden. Wir werden die verschiedenen Implementierungshandbücher, auf die für diese Regel verwiesen wird, durch gehen. Außerdem erfahren Sie, wie Sie den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) konfigurieren, um diese Implementierungsleitfäden zu unterstützen.


## <a name="rule-overview"></a>Regelübersicht

Das CMS hat die [Interoperabilitäts- und Patientenzugriffsregel](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) am 1. Mai 2020 veröffentlicht. Diese Regel erfordert einen kostenlosen und sicheren Datenfluss zwischen allen Personen, die an der Patientenversorgung beteiligt sind (Patienten, Anbieter und Zahlende), damit Patienten bei Bedarf auf ihre Gesundheitsinformationen zugreifen können. Die Interoperabilität hat die Gesundheitsbranche seit Jahrzehnten in einem Silos vorgelegen, was zu negativen Gesundheitsergebnissen mit höheren und unvorhersehbaren Kosten für die Pflege führt. CMS verwendet seine Autorität, um Aussteller von qualifizierten Gesundheitsplans (Qualified Health Plan, QHP) in den Federally Facilitated Exchanges (FFEs) zu reguliert, um diese Regel zu erzwingen. 

Im August 2020 hat CMS ausführlich erfahren, wie Organisationen das Ziel erfüllen können. Um sicherzustellen, dass Daten sicher und standardisiert ausgetauscht werden können, hat CMS FHIR Version 4 (R4) als grundlegenden Standard für den Datenaustausch identifiziert. 

Es gibt drei Hauptteile der Interoperabilität und des Patientenzugriffs:

* **Patientenzugriffs-API (erforderlich: 1. Juli 2021):** CMS-regulierte Zahlende (wie oben definiert) müssen eine sichere, standardsbasierte API implementieren und verwalten, die Es Patienten ermöglicht, einfach auf ihre Ansprüche zuzugreifen und auf Informationen, einschließlich Kosten, sowie eine definierte Teilmenge ihrer medizinischen Informationen über Anwendungen von Drittanbietern ihrer Wahl zuzugreifen.  

* **Anbieterverzeichnis-API (erforderlich: 1. Juli 2021):** Cms-regulierte Zahlende sind für diesen Teil der Regel erforderlich, um Anbieterverzeichnisinformationen über eine standardbasierte API öffentlich verfügbar zu machen. Indem diese Informationen zur Verfügung gestellt werden, können Entwickler von Anwendungen von Drittanbietern Dienste erstellen, die Patienten dabei unterstützen, Anbieter für bestimmte Pflegeanforderungen zu finden, und Ärzte andere Anbieter für die Koordination der Pflege finden.  

* **Payer-to-Payer Data Exchange (Erforderlich: 1. Januar 2022):** Cms-regulierte Zahlende müssen bestimmte patientenspezifische patientenspezifische Daten auf Anfrage mit anderen Zahlenden austauschen. Es ist zwar nicht erforderlich, irgendeine Art von Standard zu befolgen, es wird jedoch empfohlen, FHIR zum Austauschen dieser Daten zu verwenden. 

## <a name="key-fhir-concepts"></a>Wichtige FHIR-Konzepte

Wie bereits erwähnt, ist FHIR R4 erforderlich, um dieses Mandate zu erfüllen. Darüber hinaus wurden mehrere Implementierungsleitfäden entwickelt, die Anleitungen für die Regel bereitstellen. [Implementierungshandbücher](https://www.hl7.org/fhir/implementationguide.html) bieten zusätzlichen Kontext zusätzlich zur FHIR-Basisspezifikation. Dies schließt das Definieren zusätzlicher Suchparameter, Profile, Erweiterungen, Vorgänge, Wertsätze und Codesysteme ein.

Der FHIR-Dienst verfügt über die folgenden Funktionen, mit deren Hilfe Sie Ihre Datenbank für die verschiedenen Implementierungsleitfäden konfigurieren können:

* [Unterstützung für RESTful-Interaktionen](fhir-features-supported.md)
* [Speichern und Überprüfen von Profilen](validation-against-profiles.md)
* [Definieren und Indizieren benutzerdefinierter Suchparameter](how-to-do-custom-search.md)
* [Konvertieren von Daten](../data-transformation/convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>Implementierungshandbücher für die Patient Access-API

Die Patient Access-API beschreibt die Einhaltung von vier FHIR-Implementierungsleitfäden:

* [CARIN IG for Blue Button®](http://hl7.org/fhir/us/carin-bb/STU1/index.html): Die Zahlenden müssen die Ansprüche der Patienten stellen und auf Daten stoßen, die gemäß dem Carin Ig for Blue Button Implementation Guide (C4BB IG) verfügbar sind. Die C4BB IG stellt eine Reihe von Ressourcen bereit, die Zahlende über eine FHIR-API den Kunden anzeigen können, und enthält die Details, die für Anspruchsdaten in der Interoperabilitäts- und Patientenzugriffs-API erforderlich sind. In diesem Implementierungshandbuch wird die EOB-Ressource (ExplanationOfBenefit) als Hauptressource verwendet, und andere Ressourcen werden per Pull in andere Ressourcen gezogen, wenn auf sie verwiesen wird.
* [HL7 FHIR DaIent PDex IG:](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)Das Payer Data Exchange Implementation Guide (PDex IG) konzentriert sich darauf, sicherzustellen, dass die Zahlenden alle relevanten patientenwissenschaftlichen Daten bereitstellen, um die Anforderungen für die Patient Access API zu erfüllen. Dabei werden die US Core-Profile für R4-Ressourcen verwendet und umfassen (mindestens) Treffen, Anbieter, Organisationen, Standorte, Datumsangaben des Diensts, Diagnosen, Prozeduren und Beobachtungen. Obwohl diese Daten möglicherweise im FHIR-Format verfügbar sind, können sie auch von anderen Systemen im Format von Anspruchsdaten, HL7 V2-Nachrichten und C-CDA-Dokumenten stammen.
* [HL7 US Core IG:](https://www.hl7.org/fhir/us/core/toc.html)Das HL7 US Core Implementation Guide (US Core IG) ist der Backbone für die oben beschriebene PDex IG. Während die PDex IG einige Ressourcen noch weiter begrenzt als die US Core IG, folgen viele Ressourcen einfach den Standards in der US Core IG.

* [HL7 FHIR Da): PDex US-Amerikanisches Formulary IG:](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html)Part D Plans Müssen Formulary-Informationen über die Patient-API verfügbar machen. Dies wird im PDex US-Amerikanischen Formulary Implementation Guide (USDF IG) verwendet. Die USDF IG definiert eine FHIR-Schnittstelle zu den Informationen zur Gesundheitsversorgung, bei der es sich um eine Liste mit Marken- und generischen Gesundheitsgefährdungen handelt, für die ein Gesundheitsgefährdungs-Abhängiger zusenden Zahlung zusenden muss. Der Hauptzweisfall ist, dass Patienten nachvollziehen können, ob alternatives Mittel für ein ihnen vorgeschriebenes Mittel zur Verfügung steht, und die Kosten für Daseinser verglichen werden können.

## <a name="provider-directory-api-implementation-guide"></a>Implementierungshandbuch für die Anbieterverzeichnis-API

Die Anbieterverzeichnis-API beschreibt die Einhaltung eines Implementierungshandbuchs:

* [HL7 Da Guides PDex Plan Network IG](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/): In diesem Implementierungshandbuch wird eine FHIR-Schnittstelle für die Versicherungspläne eines Gesundheitsdienstleisters, die zugehörigen Netzwerke und die Organisationen und Anbieter definiert, die an diesen Netzwerken teilnehmen.

## <a name="touchstone"></a>Prüfstein

Touchstone ist eine hervorragende [](https://touchstone.aegis.net/touchstone/) Ressource, um die Einhaltung der verschiedenen Implementierungsleitfäden zu testen. In den nächsten Tutorials konzentrieren wir uns darauf, sicherzustellen, dass der FHIR-Dienst so konfiguriert ist, dass er verschiedene Touchstone-Tests erfolgreich besteht. Die Touchstone-Website bietet viele hervorragende Dokumentationen, die Sie beim Einrichten und Ausführen unterstützen.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun über grundlegende Kenntnisse der Regel für Interoperabilität und Patientenzugriff, Implementierungsleitfäden und des verfügbaren Testtools (Touchstone) verfügen, werden wir durch die Einrichtung des FHIR-Diensts für die CARIN IG for Blue Button gehen. 

>[!div class="nextstepaction"]
>[CARIN-Implementierungshandbuch für die blaue Schaltfläche](carin-implementation-guide-blue-button-tutorial.md)  

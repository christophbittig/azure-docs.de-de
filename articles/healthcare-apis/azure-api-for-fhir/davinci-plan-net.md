---
title: 'Tutorial: Da Plans net - Azure API for FHIR'
description: Dieses Tutorial führt Sie durch die Einrichtung des FHIR-Diensts in Azure API for FHIR, um Touchstone-Tests für das Da Payer Data Exchange-Implementierungshandbuch zu bestehen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 84dd99b8712eda5cff23bd7702f1336598ab26df
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361780"
---
# <a name="da-vinci-plan-net-for-azure-api-for-fhir"></a>Da Plans net for Azure API for FHIR

In diesem Tutorial erfahren Sie, wie Sie den FHIR-Dienst in Azure API for FHIR einrichten, um die [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für das Implementierungshandbuch für da Guides PDEX Payer Network (Plan-Net) zu bestehen.

## <a name="touchstone-capability-statement"></a>Touchstone capability statement (Touchstone-Funktionserklärung)

Der erste Test, auf den wir uns konzentrieren, ist das Testen von Azure API for FHIR mit der [da- Plan-Net capability-Anweisung](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Wenn Sie diesen Test ohne Updates ausführen, kann der Test aufgrund fehlender Suchparameter und fehlender Profile nicht ausgeführt werden.

## <a name="define-search-parameters"></a>Definieren von Suchparametern

Im Rahmen der Da Insurance Plan-Net IG müssen Sie sechs [](how-to-do-custom-search.md) neue Suchparameter für die Ressourcen "Gesundheitsdienst", "Versicherungsplan", "Rollenrolle", "Organisation" und "Organisationsmitgliedschaft" definieren. Alle sechs werden in der Capability-Anweisung getestet:

* [Bereich "Abdeckung des Gesundheitswesens"](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [Bereich "Versicherungsplanabdeckung"](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [Versicherungsplantyp](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [Abdeckungsbereich der Organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [Organization Affiliation Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [Netzwerk der Rollenrolle "2016"](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> Im unformatiert JSON-Code für diese Suchparameter ist der Name auf `Plannet_sp_<Resource Name>_<SearchParameter Name>` festgelegt. Der Touchstone-Test erwartet, dass der Name für diese nur `SearchParameter Name` den Namen (Abdeckungsbereich, Plantyp oder Netzwerk) hat.

Die restlichen Suchparameter, die für die Da Bezüglich-Plan-Net IG erforderlich sind, werden durch die Basisspezifikation definiert und sind bereits in Azure API for FHIR ohne zusätzliche Updates verfügbar.

## <a name="store-profiles"></a>Store Profile

Außerhalb der Definition von Suchparametern müssen Sie die erforderlichen Profile und Erweiterungen laden, [um](./validation-against-profiles.md#storing-profiles) diesen Test bestehen zu können. Es gibt neun Profile, die als Teil der DA-Plan-Net IG verwendet werden:

* [Plan-Net-Endpunkt](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Plan-Net-Standort](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-Net-Organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-Net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-Net-Projekt](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net-Role](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>Rest-Beispieldatei

Zur Unterstützung bei der Erstellung dieser Suchparameter und Profile verfügen wir über eine HTTP-Beispieldatei auf der Open-Source-Website, die alle oben beschriebenen Schritte in einer einzelnen Datei enthält. Nachdem Sie alle erforderlichen Profile und Suchparameter hochgeladen haben, können Sie den Funktionsauszugstest in Touchstone ausführen.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Ausführungsskript für DA-Plan net sample rest test passed (Da-Plan – Net Sample Rest-Testausführungsskript erfolgreich)":::

## <a name="touchstone-error-handling-test"></a>Touchstone– Fehlerbehandlungstest

Der zweite Test, den wir durchlaufen, ist das Testen [der Fehlerbehandlung.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS) Der einzige Schritt, den Sie tun müssen, ist das Löschen einer HealthcareService-Ressource aus Ihrer Datenbank und verwenden die ID der gelöschten HealthcareService-Ressource im Test. Die [Beispieldatei DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) auf der Open-Source-Website enthält ein Beispiel für HealthcareService zum Posten und Löschen für diesen Schritt.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Ausführungsskript für Da Plans net touchstone error test passed":::

## <a name="touchstone-query-test"></a>Touchstone-Abfragetest

Der nächste Test, den wir durch ausführen, ist [der Test der Abfragefunktionen.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS) Dieser Test testet die Konformität mit den Profilen, die Sie im ersten Test geladen haben. Sie müssen Ressourcen geladen haben, die den Profilen entsprechen. Der beste Weg wäre, anhand von Ressourcen zu testen, die bereits in Ihrer Datenbank vorhanden sind, aber wir verfügen auch über die Datei ["DaVinci_PlanNet_Sample_Resources.http"](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) mit Beispielressourcen, die aus den Beispielen in der IG entnommen wurden und mit denen Sie die Ressourcen erstellen und testen können.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Fehler beim Da-Plans-Net Query-Test":::

> [!NOTE]
> Mit den bereitgestellten Beispielressourcen sollten Sie eine Erfolgsrate von 98 % der Abfragetests erwarten.
> Es gibt ein offenes GitHub für den FHIR-Server, das dazu führt, dass einer dieser Tests fehlschlägt.
Die Ressource wurde mehrmals zurückgegeben, wenn sie sowohl Basiskriterien als auch _include erfüllt. [#2037](https://github.com/microsoft/fhir-server/issues/2037) 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir die Einrichtung von Azure API for FHIR zum Bestehen der Touchstone-Tests für das Implementierungshandbuch für das Da Guides PDEX Payer Network (Plan-Net) durchgeführt. Weitere Informationen zu den unterstützten Features in Azure API for FHIR finden Sie unter

>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)

---
title: 'Tutorial: DaUle Plan Net – Azure Healthcare-APIs'
description: In diesem Tutorial wird das Einrichten der Azure API for FHIR zum Bestehen von Touchstone-Tests für das Da Payer Data Exchange Implementation Guide (Da Payer Data Exchange Implementation Guide) durchlaufen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: e9a6da0564c5338d8a62eb34ef6d213cd8835219
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133358938"
---
# <a name="da-vinci-plan-net"></a>Da Vinci-Planungsnetzwerk (Da Vinci Plan Net)

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird das Einrichten des FHIR-Diensts in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) durchgehen, um die [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für das Implementierungshandbuch für da Minus PDEX Payer Network (Plan-Net) zu bestehen.

## <a name="touchstone-capability-statement"></a>Touchstone-Funktions-Anweisung

Der erste Test, auf den wir uns konzentrieren werden, ist das Testen des FHIR-Diensts mit der [Da Statements Plan-Net Capability-Anweisung](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Wenn Sie diesen Test ohne Updates ausführen, schlägt der Test aufgrund fehlender Suchparameter und fehlender Profile fehl.

## <a name="define-search-parameters"></a>Definieren von Suchparametern

Im Rahmen der Da Insurance Plan-Net IG müssen Sie sechs [neue Suchparameter](how-to-do-custom-search.md) für die Ressourcen "Healthcare Service", "Insurance Plan", "Zustellrolle", "Organisation" und "Organisationsmitgliedschaft" definieren. Alle sechs werden in der capability-Anweisung getestet:

* [Abdeckungsbereich des Gesundheitswesens](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [Abdeckungsbereich des Versicherungsplans](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [Plantyp "Versicherungsplan"](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [Abdeckungsbereich der Organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [Organisationsmitgliedschaftsnetzwerk](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [Netzwerk der Rollenrolle "2016"](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> Im unformatierten JSON-Code für diese Suchparameter wird der Name auf `Plannet_sp_<Resource Name>_<SearchParameter Name>` festgelegt. Der Touchstone-Test erwartet, dass der Name für diese nur der `SearchParameter Name` (Abdeckungsbereich, Plantyp oder Netzwerk) ist.

Die restlichen Suchparameter, die für die DaBase Plan-Net IG benötigt werden, werden durch die Basisspezifikation definiert und sind ohne zusätzliche Updates bereits im FHIR-Dienst verfügbar.

## <a name="store-profiles"></a>Store Profile

Außerhalb der Definition von Suchparametern müssen Sie die [erforderlichen Profile und Erweiterungen](./validation-against-profiles.md#storing-profiles) laden, um diesen Test bestehen zu können. Es gibt neun Profile, die als Teil der DaPlan-Net IG verwendet werden:

* [Plan-Net-Endpunkt](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Plan-Net-Standort](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-Net-Organisation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-Net-OrganisationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-Net-Bedingte](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net-Role](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>Beispiel-REST-Datei

Zur Unterstützung bei der Erstellung dieser Suchparameter und Profile verfügen wir über eine HTTP-Beispieldatei auf der Open-Source-Website, die alle oben beschriebenen Schritte in einer einzelnen Datei enthält. Nachdem Sie alle erforderlichen Profile und Suchparameter hochgeladen haben, können Sie den Funktionshinweistest in Touchstone ausführen.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da Minus plan net sample rest test execution script passed":::

## <a name="touchstone-error-handling-test"></a>Touchstonefehlerbehandlung – Test

Der zweite Test, den wir durchlaufen, ist das Testen [der Fehlerbehandlung.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS) Der einzige Schritt, den Sie durchführen müssen, besteht darin, eine HealthcareService-Ressource aus Ihrer Datenbank zu löschen und die ID der gelöschten HealthcareService-Ressource im Test zu verwenden. Die Beispieldatei [DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) auf der Open-Source-Website enthält ein Beispiel für HealthcareService zum Veröffentlichen und Löschen für diesen Schritt.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da Minus plan net touchstone error test execution script passed":::

## <a name="touchstone-query-test"></a>Touchstone– Abfragetest

Der nächste Test, den wir durchlaufen werden, ist der Test der [Abfragefunktionen.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS) Dieser Test testet die Konformität mit den Profilen, die Sie im ersten Test geladen haben. Sie müssen Ressourcen laden, die den Profilen entsprechen. Der beste Pfad wäre, mit Ressourcen zu testen, die Sie bereits in Ihrer Datenbank haben, aber wir haben auch die Datei [DaVinci_PlanNet_Sample_Resources.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) mit Beispielressourcen, die aus den Beispielen in der IG abgerufen wurden, die Sie zum Erstellen der Ressourcen und zum Testen verwenden können.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Fehler beim Net Query-Test für Da Plans":::

> [!NOTE]
> Mit den bereitgestellten Beispielressourcen sollten Sie eine Erfolgsrate von 98 % der Abfragetests erwarten.
> Es gibt ein offenes GitHub Problem mit dem FHIR-Server, das dazu führt, dass einer dieser Tests fehlschlägt.
Die Ressource wurde mehrmals zurückgegeben, wenn sie sowohl Basiskriterien als auch _include Kriterien erfüllt. [#2037](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir die Einrichtung des Azure API for FHIR zum Bestehen der Touchstone-Tests für den DaTop PDEX Payer Network (Plan-Net)-Implementierungsleitfaden durchlaufen. Als Nächstes erfahren Sie mehr über alle Features des FHIR-Diensts.

>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)

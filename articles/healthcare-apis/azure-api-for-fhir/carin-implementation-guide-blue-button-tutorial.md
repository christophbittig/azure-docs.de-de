---
title: Carin Blue Button Implementation Guide for Blue Button – Azure API for FHIR
description: In diesem Tutorial werden die Schritte zum Einrichten von Azure API for FHIR zum Bestehen der Touchstonetests für das CARIN-Implementierungshandbuch für Blue Button (C4BB IG) beschrieben.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 11/29/2021
ms.openlocfilehash: e89cc67df87d69d447dd4110c9bca5516fd5c464
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361792"
---
# <a name="carin-implementation-guide-for-blue-button174-for-azure-api-for-fhir"></a>CARIN-Implementierungshandbuch für blue button&#174; für Azure API for FHIR

In diesem Tutorial wird das Einrichten von Azure API for FHIR zum Bestehen der [Touchstonetests](https://touchstone.aegis.net/touchstone/) für das [CARIN-Implementierungshandbuch für Blue Button](https://build.fhir.org/ig/HL7/carin-bb/index.html) (C4BB IG) durchlaufen.

## <a name="touchstone-capability-statement"></a>Touchstone-Funktions-Anweisung

Der erste Test, auf den wir uns konzentrieren, besteht darin, Azure API for FHIR mit der [C4BB IG-Funktions-Anweisung](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)zu testen. Wenn Sie diesen Test ohne Updates für Azure API for FHIR ausführen, schlägt der Test aufgrund fehlender Suchparameter und fehlender Profile fehl. 

### <a name="define-search-parameters"></a>Definieren von Suchparametern

Im Rahmen der C4BB IG müssen Sie drei [neue Suchparameter](how-to-do-custom-search.md) für die `ExplanationOfBenefit` Ressource definieren. Zwei davon werden in der Capability-Anweisung getestet (Typ und Dienstdatum), und eine wird für `_include` Suchvorgänge (Verursacher) benötigt.  

* [type](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-type.json)
* [Dienstdatum](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [Versicherer](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> Im unformatierten JSON-Code für diese Suchparameter wird der Name auf `ExplanationOfBenefit_<SearchParameter Name>` festgelegt. Beim Touchstone-Test wird erwartet, dass der Name für diese **den Typ**, **das Dienstdatum** und **die Kennung** hat.  
 
Die restlichen Suchparameter, die für die C4BB IG erforderlich sind, werden durch die Basisspezifikation definiert und sind bereits in Azure API for FHIR ohne zusätzliche Updates verfügbar.
 
### <a name="store-profiles"></a>Store Profile

Abgesehen von der Definition von Suchparametern müssen Sie auch die [erforderlichen Profile](validation-against-profiles.md)laden, um diesen Test bestehen zu lassen. In der C4BB IG sind acht Profile definiert. 

* [C4BB-Abdeckung](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 
* [C4BB ExplanationOfBenefit Inpatient Institution](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 
* [C4BB ExplanationOfBenefit 16011 (C4BB-Erklärung)](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 
* [C4BB ExplanationOfBenefit Ausschau](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 
* [C4BB ExplanationOfBenefit Professional Non Benchian](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 
* [C4BB-Organisation](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 
* [C4BB-Patient](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 
* [C4BB-Läufe](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>Beispiel-REST-Datei

Um die Erstellung dieser Suchparameter und Profile zu unterstützen, verfügen wir über eine [HTTP-Beispieldatei,](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) die alle oben beschriebenen Schritte in einer einzelnen Datei enthält. Nachdem Sie alle erforderlichen Profile und Suchparameter hochgeladen haben, können Sie den Funktionshinweistest in Touchstone ausführen.

:::image type="content" source="media/cms-tutorials/capability-test-script-execution-results.png" alt-text="Ergebnisse der Funktionstestskriptausführung.":::

## <a name="touchstone-read-test"></a>Touchstone– Lesetest

Nach dem Testen der capabilities-Anweisung testen wir die [Lesefunktionen](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) in Azure API for FHIR mit der C4BB IG. Dieser Test testet die Konformität mit den acht Profilen, die Sie im ersten Test geladen haben. Sie müssen Ressourcen laden, die den Profilen entsprechen. Der beste Pfad wäre, mit Ressourcen zu testen, die Sie bereits in Ihrer Datenbank haben, aber wir haben auch eine [HTTP-Datei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) mit Beispielressourcen verfügbar, die aus den Beispielen in der IG abgerufen wurden, mit denen Sie die Ressourcen erstellen und testen können.

:::image type="content" source="media/cms-tutorials/test-execution-results-touchstone.png" alt-text="Touchstone liest Testausführungsergebnisse.":::

## <a name="touchstone-eob-query-test"></a>Touchstone – EOB-Abfragetest

Der nächste Test, den wir überprüfen, ist der [EOB-Abfragetest.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS) Wenn Sie den Lesetest bereits abgeschlossen haben, haben Sie alle benötigten Daten geladen. Dieser Test überprüft, ob Sie mithilfe verschiedener Parameter nach bestimmten Ressourcen und suchen `Patient` `ExplanationOfBenefit` können.

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Ergebnisse der EOB-Abfrageausführung mit Touchstone.":::

## <a name="touchstone-error-handling-test"></a>Touchstonefehlerbehandlung – Test

Der letzte Test, den wir durchlaufen, ist das Testen [der Fehlerbehandlung.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS) Der einzige Schritt, den Sie durchführen müssen, besteht darin, eine ExplanationOfBenefit-Ressource aus Ihrer Datenbank zu löschen und die ID der gelöschten Ressource im Test zu `ExplanationOfBenefit` verwenden.

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-error-handling.png" alt-text="Touchstone-EOB-Fehlerbehandlungsergebnisse.":::


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde erläutert, wie Sie die CARIN IG for Blue Button-Tests in Touchstone bestehen. Als Nächstes können Sie überprüfen, wie Sie die DaWeis-Formulartests testen.

>[!div class="nextstepaction"]
>[DaVinci-Formular für Mittel](davinci-drug-formulary-tutorial.md)       
 

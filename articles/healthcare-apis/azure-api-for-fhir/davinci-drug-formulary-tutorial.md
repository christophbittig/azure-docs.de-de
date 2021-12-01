---
title: Tutorial zu Da- Und Azure API for FHIR
description: Dieses Tutorial führt Sie durch die Einrichtung Azure API for FHIR, um die Touchstone-Tests mit dem Implementierungsleitfaden für DaVinci-Formulary zu bestehen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 86efbb3c0d0dccdc99ef9364d9c579cb5c7655bb
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133398371"
---
# <a name="tutorial-for-da-vinci-drug-formulary-for-azure-api-for-fhir"></a>Tutorial für Da- Und-Denn-Formulary für Azure API for FHIR

In diesem Tutorial erfahren Sie, wie Sie Azure API for FHIR einrichten, um die [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für das Implementierungshandbuch für [Da Payer Data Exchange US-Amerikanisches Formulary zu bestehen.](http://hl7.org/fhir/us/Davinci-drug-formulary/)

## <a name="touchstone-capability-statement"></a>Touchstone capability statement (Touchstone-Funktionserklärung)

Der erste Test, auf den wir uns konzentrieren, ist das Testen Azure API for FHIR mit der [Da- Und-Mal-Formulary-Funktionsanweisung](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Wenn Sie diesen Test ohne Updates ausführen, kann der Test aufgrund fehlender Suchparameter und fehlender Profile nicht ausgeführt werden.

### <a name="define-search-parameters"></a>Definieren von Suchparametern

Im Rahmen der Da Formulary IG für FormularyDrug müssen Sie drei neue Suchparameter für die Ressource FormularyDrug definieren. [](how-to-do-custom-search.md) Alle drei werden in der Capability-Anweisung getestet.

* [Einsentier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [Veralten](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [Name des Vornamens](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Die restlichen Suchparameter, die für die DaUch-Ig für das Formulary-Formular benötigt werden, werden durch die Basisspezifikation definiert und sind bereits in Azure API for FHIR ohne weitere Updates verfügbar.

### <a name="store-profiles"></a>Store Profile

Außerhalb der Definition von Suchparametern müssen Sie nur noch die erforderlichen Profile laden, um diesen [Test zu bestehen.](validation-against-profiles.md) Es gibt zwei Profile, die als Teil der DA-Ig für das Da- Und-Dabei-Formular verwendet werden.

* [Formulary-Abhängige](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Formulary Coverage-Plan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>Rest-Beispieldatei

Um die Erstellung dieser Suchparameter und Profile zu unterstützen, verfügen wir auf der Open-Source-Website über die HTTP-Beispieldatei [Da Proxy Formulary,](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) die alle oben beschriebenen Schritte in einer einzelnen Datei enthält. Nachdem Sie alle erforderlichen Profile und Suchparameter hochgeladen haben, können Sie den Funktionsauszugstest in Touchstone ausführen. Sie sollten eine erfolgreiche Ausführung erhalten:

:::image type="content" source="media/cms-tutorials/davinci-test-script-execution.png" alt-text="Da Soll-Testskriptausführung.":::

## <a name="touchstone-query-test"></a>Touchstone-Abfragetest

Der zweite Test ist die [Abfragefunktionen](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Mit diesem Test wird überprüft, ob Sie mithilfe verschiedener Parameter nach bestimmten Ressourcen für Abdeckungsplan und -ressourcen suchen können. Der beste Weg wäre, anhand von Ressourcen zu testen, die bereits in Ihrer Datenbank vorhanden sind, aber wir verfügen auch über die DA VINCIFORMULARY_SAMPLE_RESOURCES-HTTP-Datei mit Beispielressourcen, die aus den Beispielen in der IG entnommen wurden und mit denen Sie die Ressourcen erstellen und testen können. [](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http)

:::image type="content" source="media/cms-tutorials/davinci-test-execution-results.png" alt-text="Da Soll-Testausführungsergebnisse.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir erfahren, wie Sie die Da Payer Data Exchange US-amerikanischen Formulary in Touchstone übergeben. Als Nächstes erfahren Sie, wie Sie das Da Guides PDex-Implementierungshandbuch in Touchstone testen.

>[!div class="nextstepaction"]
>[Da Vinci-Datenaustausch für Kostenträger (Da Vinci PDex)](davinci-pdex-tutorial.md)
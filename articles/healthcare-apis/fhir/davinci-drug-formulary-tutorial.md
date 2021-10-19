---
title: Tutorial zu Da- Und-Dabei-Formularen
description: Dieses Tutorial führt Sie durch die Einrichtung des FHIR-Diensts, um die Touchstonetests für den Implementierungsleitfaden für DaVinci-Formulary zu bestehen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/06/2021
ms.openlocfilehash: 56cf21f960cadd54340be48deeb44b1e37470f69
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778812"
---
# <a name="tutorial-for-da-vinci-drug-formulary"></a>Tutorial für Da- Und-Denn-Formulary

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial erfahren Sie, wie Sie den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) einrichten, um die [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für das Implementierungshandbuch für [Da Payer Data Exchange US-Amerikanisches](http://hl7.org/fhir/us/Davinci-drug-formulary/)Formulary zu bestehen.

## <a name="touchstone-capability-statement"></a>Touchstone-Funktions-Anweisung

Der erste Test, auf den wir uns konzentrieren, ist das Testen des FHIR-Diensts mit der [Da- Und-Mal-Formulary-Funktionserklärung](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Wenn Sie diesen Test ohne Updates ausführen, kann der Test aufgrund fehlender Suchparameter und fehlender Profile nicht ausgeführt werden.

### <a name="define-search-parameters"></a>Definieren von Suchparametern

Im Rahmen der Da Formulary IG für Formulary [](how-to-do-custom-search.md) Müssen Sie drei neue Suchparameter für die Ressource FormularyDrug definieren. Alle drei werden in der Capability-Anweisung getestet.

* [Einsentier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [Veralten](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [Name des Vornamens](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Die restlichen Suchparameter, die für die Ig für das Da- Und-Dabei-Formular benötigt werden, werden durch die Basisspezifikation definiert und sind bereits im FHIR-Dienst verfügbar, ohne dass weitere Updates erforderlich sind.

### <a name="store-profiles"></a>Store Profile

Außerhalb der Definition von Suchparametern müssen Sie nur noch die erforderlichen Profile laden, um diesen [Test zu bestehen.](validation-against-profiles.md) Es gibt zwei Profile, die als Teil der DA-Ig für das Da- Und-Dabei-Formular verwendet werden.

* [Formulary-Abhängige](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Formulary Coverage-Plan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>Beispiel-REST-Datei

Um die Erstellung dieser Suchparameter und Profile zu unterstützen, verfügen wir auf der Open-Source-Website über die HTTP-Beispieldatei [Da Header Formulary,](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) die alle oben beschriebenen Schritte in einer einzelnen Datei enthält. Nachdem Sie alle erforderlichen Profile und Suchparameter hochgeladen haben, können Sie den Funktionsauszugstest in Touchstone ausführen. Sie sollten eine erfolgreiche Ausführung erhalten:

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-script-execution.png" alt-text="Da Script-Testskriptausführung.":::

## <a name="touchstone-query-test"></a>Touchstone-Abfragetest

Der zweite Test ist die [Abfragefunktionen](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Mit diesem Test wird überprüft, ob Sie mithilfe verschiedener Parameter nach bestimmten Ressourcen für Abdeckungsplan und -ressourcen suchen können. Der beste Weg wäre, anhand von Ressourcen zu testen, die bereits in Ihrer Datenbank vorhanden sind, aber wir verfügen auch über die DA VINCIFORMULARY_SAMPLE_RESOURCES-HTTP-Datei mit Beispielressourcen, die aus den Beispielen in der IG gezogen wurden und mit denen Sie die Ressourcen erstellen und testen können. [](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http)

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-execution-results.png" alt-text="Ergebnisse der Da- Und-Testausführung.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir erfahren, wie Sie die Da Payer Data Exchange US-Amerikanisches Formulary in Touchstone übergeben. Als Nächstes erfahren Sie, wie Sie das Da Guides PDex-Implementierungshandbuch in Touchstone testen.

>[!div class="nextstepaction"]
>[Da Vinci-Datenaustausch für Kostenträger (Da Vinci PDex)](davinci-pdex-tutorial.md)
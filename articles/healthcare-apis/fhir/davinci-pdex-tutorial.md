---
title: 'Tutorial: DaDex PDex – Azure Healthcare-APIs (Vorschauversion)'
description: In diesem Tutorial wird die Einrichtung des FHIR-Diensts zum Bestehen von Tests für das Da Payer Data Exchange Implementation Guide (Implementierungshandbuch für Da Payer Data Exchange) durchlaufen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: d4b6906cc646d6dd742c15b78feb8af1e83a56ce
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779856"
---
# <a name="da-vinci-pdex"></a>Da Vinci-Datenaustausch für Kostenträger (Da Vinci PDex)

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird das Einrichten des FHIR-Diensts in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) durchlaufen, um die [Touchstone-Tests](https://touchstone.aegis.net/touchstone/) für den [Da Payer Data Exchange Implementation Guide](http://hl7.org/fhir/us/davinci-pdex/toc.html) (PDex IG) zu bestehen.

> [!NOTE]
> Für alle diese Tests führen wir sie für die JSON-Tests aus. Der FHIR-Dienst unterstützt sowohl JSON als auch XML, verfügt jedoch nicht über separate Endpunkte für den Zugriff auf JSON oder XML. Aus diesem Grund schlagen alle XML-Tests fehl. Wenn Sie die Funktionsanweisung in XML anzeigen möchten, übergeben Sie einfach den \_ Formatparameter \` GET {fhirurl}/metadata? \_ format=xml\`

## <a name="touchstone-capability-statement"></a>Touchstonefunktions-Anweisung

Der erste Satz von Tests, auf den wir uns konzentrieren werden, besteht darin, den FHIR-Dienst mit der PDex IG-Funktions-Anweisung zu testen. Dies umfasst drei Tests:

* Der erste Test überprüft die grundlegende Funktions-Anweisung anhand der IG-Anforderungen und wird ohne Updates bestanden.

* Der zweite Test überprüft, ob alle Profile für US Core hinzugefügt wurden. Dieser Test wird ohne Updates bestanden, enthält jedoch eine Reihe von Warnungen. Damit diese Warnungen entfernt werden, müssen Sie [die US Core-Profile laden.](validation-against-profiles.md) Wir haben eine [HTTP-Beispieldatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) erstellt, die die Erstellung aller Profile durchläuft. Sie können die [Profile](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) auch direkt von der HL7-Website abrufen, die über die aktuellsten Versionen verfügt.

* Der dritte Test überprüft, ob der [vorgang $patient-everything](patient-everything.md) unterstützt wird.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Fehler bei der PDex-Ausführung.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-Match-Test

Der [zweite Test](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) im Abschnitt Payer Data Exchange testet das Vorhandensein des [$member-Match-Vorgangs](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html). Weitere Informationen zum $member-Match-Vorgang finden Sie in unserer [Übersicht über $member-Match-Vorgänge.](tutorial-member-match.md)

In diesem Test müssen Sie einige Beispieldaten laden, damit der Test erfolgreich ist. Wir haben [hier](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) eine Restdatei mit dem Patienten und der Abdeckung verknüpft, die Sie für den Test benötigen. Nachdem diese Daten geladen wurden, können Sie diesen Test erfolgreich bestehen. Wenn die Daten nicht geladen werden, erhalten Sie eine 422-Antwort, da keine genaue Übereinstimmung gefunden wurde.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="DaDex PDex-Testskript erfolgreich.":::

## <a name="touchstone-patient-by-reference"></a>Touchstone-Patient als Referenz

Die nächsten Tests, die wir überprüfen, sind die [Patienten anhand von Referenztests.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) Dieser Satz von Tests überprüft anhand verschiedener Suchkriterien, ob Sie einen Patienten finden können. Die beste Möglichkeit, den Patienten anhand einer Referenz zu testen, ist das Testen mit Ihren eigenen Daten, aber wir haben eine [Beispielressourcendatei](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) hochgeladen, die Sie auch laden können.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da Die PDex-Ausführung wurde übergeben.":::

## <a name="touchstone-patienteverything-test"></a>Touchstone–Patient/$everything-Test

Der letzte Test, den wir durchlaufen, ist das Testen von Patient-Everything. Für diesen Test müssen Sie einen Patienten laden, und dann verwenden Sie die ID dieses Patienten, um zu testen, ob Sie den $everything Vorgang verwenden können, um alle Daten im Zusammenhang mit dem Patienten zu pullen.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="touchstone patient/$everything test bestanden.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir schritt für Schritt erfahren, wie Sie die Tests für die zahlung zahlende Exchange in Touchstone bestehen. Als Nächstes erfahren Sie, wie Sie das Implementierungshandbuch für Da Minus PDEX Payer Network (Plan-Net) testen.

>[!div class="nextstepaction"]
>[Da Vinci-Planungsnetzwerk (Da Vinci Plan Net)](davinci-plan-net.md)  

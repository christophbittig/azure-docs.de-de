---
title: 'Tutorial: DaVersion PDex – Azure Healthcare-APIs (Vorschauversion)'
description: Dieses Tutorial führt Sie durch die Einrichtung des FHIR-Diensts zum Bestehen von Tests für das Da Payer Data Exchange Implementation Guide.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 11/12/2021
ms.openlocfilehash: 12f7cbac225cfe4e85d2df21c363d4c08fb0b4c6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494453"
---
# <a name="da-vinci-pdex"></a>Da Vinci-Datenaustausch für Kostenträger (Da Vinci PDex)

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial erfahren Sie, wie Sie den FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) einrichten, um die [Touchstonetests](https://touchstone.aegis.net/touchstone/) für das [Da Payer Data Exchange Implementation Guide](http://hl7.org/fhir/us/davinci-pdex/toc.html) (PDex IG) zu bestehen.

> [!NOTE]
> Der FHIR-Dienst unterstützt nur JSON. Der Open-Source-FHIR-Dienst von Microsoft unterstützt SOWOHL JSON als auch XML. In Open Source können Sie den _format-Parameter verwenden, um die XML-Funktionsanweisung anzuzeigen: `GET {fhirurl}/metadata?_format=xml`

## <a name="touchstone-capability-statement"></a>Touchstone capability statement (Touchstone-Funktionserklärung)

Die erste Gruppe von Tests, auf die wir uns konzentrieren, ist das Testen des FHIR-Diensts mit der PDex IG-Funktions-Anweisung. Dies umfasst drei Tests:

* Der erste Test überprüft die grundlegende Funktions-Anweisung anhand der IG-Anforderungen und wird ohne Updates übergeben.

* Der zweite Test überprüft, ob alle Profile für US Core hinzugefügt wurden. Dieser Test wird ohne Updates bestanden, enthält jedoch eine Reihe von Warnungen. Um diese Warnungen zu entfernen, müssen Sie [die US Core-Profile laden.](validation-against-profiles.md) Wir haben eine [HTTP-Beispieldatei erstellt,](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) die die Erstellung aller Profile durchfingt. Sie können die Profile auch [direkt von](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) der HL7-Website mit den neuesten Versionen erhalten.

* Der dritte Test überprüft, ob der [$patient alles unterstützt](patient-everything.md) wird.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Fehler bei der Da- Und-PDex-Ausführung.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member –Übereinstimmungstest

Der [zweite Test](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) im Abschnitt Payer Data Exchange testet das Vorhandensein des [$member-Match-Vorgangs.](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) Weitere Informationen zum Vorgang "$member-match" finden Sie in der [$member-Match-Vorgangsübersicht.](tutorial-member-match.md)

In diesem Test müssen Sie einige Beispieldaten laden, damit der Test bestanden wird. Wir haben hier eine Restdatei [mit](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) dem Patienten und der Abdeckung verknüpft, die Sie für den Test benötigen. Nachdem diese Daten geladen wurden, können Sie diesen Test erfolgreich bestehen. Wenn die Daten nicht geladen werden, erhalten Sie eine 422-Antwort, weil sie keine genaue Übereinstimmung finden.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="DaSkript des PDex-Tests wurde erfolgreich ausgeführt.":::

## <a name="touchstone-patient-by-reference"></a>Touchstone-Patient nach Referenz

Die nächsten Tests, die wir überprüfen, sind [der Patient nach Referenztests.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) Mit diesem Satz von Tests wird überprüft, ob Sie einen Patienten anhand verschiedener Suchkriterien finden können. Die beste Möglichkeit, den Patienten nach Verweis zu testen, ist das [](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) Testen mit Ihren eigenen Daten, aber wir haben eine Beispielressourcendatei hochgeladen, die Sie auch laden können, um sie zu verwenden.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da Einer PDex-Ausführung wurde erfolgreich ausgeführt.":::

## <a name="touchstone-patienteverything-test"></a>Touchstone patient/$everything test (Touchstone-Patient/$everything Test)

Der letzte Test, den wir durchlaufen, ist das Testen von patient-everything. Für diesen Test müssen Sie einen Patienten laden. Anschließend verwenden Sie die ID dieses Patienten, um zu testen, ob Sie den $everything-Vorgang verwenden können, um alle Daten im Zusammenhang mit dem Patienten zu pullen.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="Touchstone-Patient/$everything erfolgreich.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben wir erfahren, wie Sie die Payer-Exchange in Touchstone bestehen. Als Nächstes erfahren Sie, wie Sie das Implementierungshandbuch für Da Guides PDEX Payer Network (Plan-Net) testen.

>[!div class="nextstepaction"]
>[Da Vinci-Planungsnetzwerk (Da Vinci Plan Net)](davinci-plan-net.md)  

---
title: 'Tutorial: $member-Match-Vorgang'
description: Führt den $member-Match-Vorgang ein, der als Teil des Da Health Record Exchange (HRex) definiert ist.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: 2771458c6c9f23c9e87691e1a74d3e29986ad482
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778263"
---
# <a name="member-match-operation-in-fhir-service"></a>$member-Match-Vorgang im FHIR-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) ist ein Vorgang, der als Teil des Da Health Record Exchange (HRex) definiert ist. In diesem Leitfaden wird erläutert, was $member-match ist und wie sie verwendet wird.

## <a name="overview-of-member-match"></a>Übersicht über $member-Match

Der $member-Match-Vorgang wurde erstellt, um den Datenaustausch zwischen Zahlenden zu unterstützen, indem es einem neuen Zahlenden ermöglicht wurde, einen eindeutigen Bezeichner für einen Patienten vom vorherigen Zahlenden des Patienten abzurufen. Der $member-Match-Vorgang erfordert, dass im Text der Anforderung drei Informationen übergeben werden:

* Demografische Daten von Patienten
* Die alten Abdeckungsinformationen
* Die neuen Abdeckungsinformationen (basierend auf unserer Implementierung nicht erforderlich)

Nachdem die Daten übergeben wurden, überprüft der FHIR-Dienst in den Azure Healthcare-APIs (in der Regel als FHIR-Dienst bezeichnet), ob er einen Patienten finden kann, der genau den übergebenen demografischen Daten mit den übergebenen alten Abdeckungsinformationen entspricht. Wenn ein Ergebnis gefunden wird, handelt es sich bei der Antwort um ein Paket mit den ursprünglichen Patientendaten sowie einem neuen Bezeichner, der vom alten Zahlenden hinzugefügt wurde, und den alten Abdeckungsinformationen.

> [!NOTE]
> Die Spezifikation beschreibt das Übergeben und Zurückgeben der neuen Abdeckungsinformationen. Wir haben uns entschieden, diese Daten wegzulassen, um die Ergebnisse kleiner zu halten.

## <a name="example-of-member-match"></a>Beispiel für $member-Übereinstimmung

Verwenden Sie den folgenden Aufruf, um $member-match zu verwenden:

`POST {{fhirurl}}/Patient/$member-match`

Sie müssen eine Parameterressource in den Körper einschließen, die den Patienten, die alte Abdeckung und die neue Abdeckung enthält. Eine JSON-Darstellung finden Sie unter [$member-match-Beispielanforderung.](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)

Wenn eine einzelne Übereinstimmung gefunden wird, erhalten Sie eine 200-Antwort mit einem weiteren hinzugefügten Bezeichner:

:::image type="content" source="media/centers-medicare-services-tutorials/two-hundred-response.png" alt-text="200 Hundert Antwortcode.":::

Wenn die $member-Übereinstimmung keine eindeutige Übereinstimmung finden kann, erhalten Sie eine 422-Antwort mit einem Fehlercode.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie die $member-Match-Operation kennengelernt. Als Nächstes erfahren Sie mehr über das Testen der Da Payer Data Exchange IG in Touchstone, für die der $member-Match-Vorgang erforderlich ist.

>[!div class="nextstepaction"]
>[DaVinci PDex](davinci-pdex-tutorial.md)

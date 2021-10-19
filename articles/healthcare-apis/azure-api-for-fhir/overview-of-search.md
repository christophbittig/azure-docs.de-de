---
title: Übersicht über die Suche in Azure API for FHIR
description: In diesem Artikel wird eine Übersicht über die FHIR-Suche beschrieben, die in der Azure API for FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: f974cb0a5099ce23f9b7ecaf719c09239c21eca8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824527"
---
# <a name="overview-of-search-in-azure-api-for-fhir"></a>Übersicht über die Suche in Azure API for FHIR

Die FHIR-Spezifikation definiert die Grundlagen der Suche nach FHIR-Ressourcen. Dieser Artikel führt Sie durch einige wichtige Aspekte bei der Suche nach Ressourcen in FHIR. Vollständige Informationen zum Durchsuchen von FHIR-Ressourcen finden Sie unter [Suchen](https://www.hl7.org/fhir/search.html) in der HL7 FHIR-Spezifikation. In diesem Artikel werden Beispiele für die Suchsyntax gezeigt. Jede Suche wird für Ihren FHIR-Server ausgeführt, der in der Regel über die URL `https://<FHIRSERVERNAME>.azurewebsites.net` verfügt. In den Beispielen verwenden wir den Platzhalter {{FHIR_URL}} für diese URL. 

FHIR-Suchvorgänge können für einen bestimmten Ressourcentyp, ein [angegebenes](https://www.hl7.org/fhir/compartmentdefinition.html)Abteil oder alle Ressourcen durchgeführt werden. Die einfachste Möglichkeit zum Ausführen einer Suche in FHIR ist die Verwendung einer `GET` Anforderung. Wenn Sie beispielsweise alle Patienten in die Datenbank pullen möchten, können Sie die folgende Anforderung verwenden: 

```rest
GET {{FHIR_URL}}/Patient
```

Sie können auch mithilfe von `POST` suchen. Dies ist nützlich, wenn die Abfragezeichenfolge zu lang ist. Für die Suche mit `POST` können die Suchparameter als Formularkörper übermittelt werden. Dies ermöglicht eine längere, komplexere Reihe von Abfrageparametern, die in einer Abfragezeichenfolge schwer zu erkennen und zu verstehen sind.

Wenn die Suchanforderung erfolgreich ist, erhalten Sie eine FHIR-Bündelantwort vom Typ `searchset` . Wenn bei der Suche ein Fehler auftritt, finden Sie die Fehlerdetails im , damit Sie nachvollziehen können, warum `OperationOutcome` die Suche fehlgeschlagen ist.

In den folgenden Abschnitten werden die verschiedenen Aspekte der Suche beschrieben. Nachdem Sie diese Details überprüft haben, lesen Sie unsere Beispielseite, die Beispiele für Suchvorgänge enthält, die Sie in der Azure API for FHIR. [](search-samples.md)

## <a name="search-parameters"></a>Suchparameter

Bei einer Suche suchen Sie basierend auf verschiedenen Attributen der Ressource. Diese Attribute werden als Suchparameter bezeichnet. Jede Ressource verfügt über einen Satz definierter Suchparameter. Der Suchparameter muss in der Datenbank definiert und indiziert werden, damit Sie erfolgreich nach ihm suchen können.

Jeder Suchparameter verfügt über einen definierten [Datentyp.](https://www.hl7.org/fhir/search.html#ptypes) Die Unterstützung für die verschiedenen Datentypen wird unten beschrieben:


| **Suchparametertyp**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  number                    | Ja                  | Ja                       |
|  date                      | Ja                  | Ja                       |
|  Zeichenfolge                    | Ja                  | Ja                       |
|  token                     | Ja                  | Ja                       |
|  Referenz                 | Ja                  | Ja                       |
|  Zusammengesetzt                 | Partial              | Partial                   | Die Liste der unterstützten zusammengesetzten Typen wird weiter unten in diesem Artikel beschrieben. |
|  quantity                  | Ja                  | Ja                       |
|  uri                       | Ja                  | Ja                       |
|  speziell                   | Nein                   | Nein                        |

### <a name="common-search-parameters"></a>Allgemeine Suchparameter

Es gibt [allgemeine Suchparameter,](https://www.hl7.org/fhir/search.html#all) die für alle Ressourcen gelten. Diese sind unten aufgeführt, zusammen mit ihrer Unterstützung innerhalb der Azure API for FHIR:

| **Allgemeiner Suchparameter** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | Ja                  | Ja                       
| _lastUpdated                | Ja                  | Ja                       |
| _tag                        | Ja                  | Ja                       |
| _type                       | Ja                  | Ja                       |
| _security                   | Ja                  | Ja                       |
| _profile                    | Ja                  | Ja                       |
| _has                        | Partial              | Ja                       | Unterstützung für _has ist im MVP im Azure API for FHIR und in der oss-Version, die von der Cosmos wird. Weitere Informationen finden Sie im Abschnitt verketten weiter unten. |
| _query                      | Nein                   | Nein                        |
| _filter                     | Nein                   | Nein                        |
| _list                       | Nein                   | Nein                        |
| _text                       | Nein                   | Nein                        |
| _content                    | Nein                   | Nein                        |

### <a name="resource-specific-parameters"></a>Ressourcenspezifische Parameter

Mit dem Azure API for FHIR unterstützen wir fast alle [ressourcenspezifischen Suchparameter,](https://www.hl7.org/fhir/searchparameter-registry.html) die durch die FHIR-Spezifikation definiert sind. Die einzigen Suchparameter, die wir nicht unterstützen, sind unter den folgenden Links verfügbar:

* [Stu3 – Nicht unterstützte Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [Nicht unterstützte R4-Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

Sie können auch die aktuelle Unterstützung für Suchparameter in der [FHIR Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html) mit der folgenden Anforderung anzeigen:

```rest
GET {{FHIR_URL}}/metadata
```

Um die Suchparameter in der Capability-Anweisung anzuzeigen, navigieren Sie zu , um die Suchparameter für jede Ressource anzuzeigen `CapabilityStatement.rest.resource.searchParam` und die `CapabilityStatement.rest.searchParam` Suchparameter für alle Ressourcen zu finden.

> [!NOTE]
> Der Azure API for FHIR erstellt oder indiziert nicht automatisch Suchparameter, die nicht durch die FHIR-Spezifikation definiert sind. Wir unterstützen Sie jedoch beim Definieren Ihrer eigenen [Suchparameter.](how-to-do-custom-search.md)

### <a name="composite-search-parameters"></a>Zusammengesetzte Suchparameter
Mit der zusammengesetzten Suche können Sie nach Wertpaaren suchen. Wenn Sie beispielsweise nach einer Höhenüberwachung suchen, bei der die Person 60 Zoll betrug, sollten Sie sicherstellen, dass eine einzelne Komponente der Beobachtung den Code der Höhe **und** den Wert 60 enthält. Sie möchten keine Beobachtung erhalten, bei der eine Gewichtung von 60 und eine Höhe von 48 gespeichert wurde, obwohl die Beobachtung Einträge enthalten würde, die für den Wert 60 und den Code der Höhe qualifiziert sind, nur in verschiedenen Komponentenabschnitten. 

Mit dem Azure API for FHIR unterstützen wir die folgenden Suchparametertyppaare:

* Referenz, Token
* Token, Datum
* Token, Zahl, Zahl
* Token, Menge
* Token, Zeichenfolge
* Token, Token

Weitere Informationen finden Sie unter HL7 Composite Search Parameters ( Parameter für die [zusammengesetzte HL7-Suche).](https://www.hl7.org/fhir/search.html#composite) 

> [!NOTE]
> Zusammengesetzte Suchparameter unterstützen keine Modifizierer gemäß FHIR-Spezifikation.

 ### <a name="modifiers--prefixes"></a>Modifizierer & Präfixe

[Modifizierer](https://www.hl7.org/fhir/search.html#modifiers) ermöglichen es Ihnen, den Suchparameter zu ändern. Im Folgenden finden Sie eine Übersicht über alle FHIR-Modifizierer und die Unterstützung im Azure API for FHIR. 

| **Modifizierer** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  :missing     | Ja                  | Ja                       |
|  :exact       | Ja                  | Ja                       |
|  :contains    | Ja                  | Ja                       |
|  :text        | Ja                  | Ja                       |
|  :type (Referenz) | Ja             | Ja                       |
|  :not         | Ja                  | Ja                       |
|  :below (URI) | Ja                  | Ja                       |
|  :above (URI) | Ja                  | Ja                       |
|  :in (Token)  | Nein                   | Nein                        |
|  :below (Token) | Nein                 | Nein                        |
|  :above (Token) | Nein                 | Nein                        |
|  :not-in (Token) | Nein                | Nein                        |

Für Suchparameter mit einer bestimmten Reihenfolge (Zahlen, Datumsangaben und Mengen) können Sie ein [Präfix](https://www.hl7.org/fhir/search.html#prefix) für den Parameter verwenden, um Übereinstimmungen zu finden. Der Azure API for FHIR unterstützt alle Präfixe.

 ### <a name="search-result-parameters"></a>Suchergebnisparameter
Um die Verwaltung der zurückgegebenen Ressourcen zu erleichtern, gibt es Suchergebnisparameter, die Sie in Ihrer Suche verwenden können. Ausführliche Informationen zur Verwendung der einzelnen Suchergebnisparameter finden Sie auf der [HL7-Website.](https://www.hl7.org/fhir/search.html#return) 

| **Suchergebnisparameter**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Ja                  | Ja                       |
| _count                        | Ja                  | Ja                       | _count ist auf 1.000 Ressourcen beschränkt. Wenn sie höher als 1000 festgelegt ist, wird nur 1000 zurückgegeben, und im Paket wird eine Warnung zurückgegeben.                               |
| _include                      | Ja                  | Ja                       | Enthaltene Elemente sind auf 100 beschränkt. _include unter PaaS und OSS in Cosmos DB enthalten keine Unterstützung für :iterate [(#2137)](https://github.com/microsoft/fhir-server/issues/2137).                               |
| _revinclude                   | Ja                  | Ja                       |Enthaltene Elemente sind auf 100 beschränkt. _revinclude unter PaaS und OSS in Cosmos DB enthalten keine Unterstützung für :iterate [(#2137)](https://github.com/microsoft/fhir-server/issues/2137).  Es gibt auch einen falschen Statuscode für eine ungültige Anforderung [#1319.](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Ja             | Ja                   |
| _total                        | Partial              | Partial                   | _total=none und _total=accurate                               |
| _sort                         | Partial              | Partial                   | sort=_lastUpdated wird unterstützt. Sortiert den Datensatz standardmäßig in aufsteigender Reihenfolge. Sie können das Präfix "-" verwenden, um in absteigender Reihenfolge zu sortieren. Für Azure API for FHIR- und OSS Cosmos-Datenbankdatenbanken, die nach dem 20. April 2021 erstellt wurden, wird die Sortierung auch nach Vorname, Nachname und Datum der Studie unterstützt.          |
| _contained                    | Nein                   | Nein                        |
| _containedType                | Nein                   | Nein                        |
| _score                        | Nein                   | Nein                        |

Standardmäßig ist die Azure API for FHIR auf lenient handling (lenient handling) festgelegt. Dies bedeutet, dass der Server alle unbekannten oder nicht unterstützten Parameter ignoriert. Wenn Sie eine strikte Behandlung verwenden möchten, können Sie den **Prefer-Header verwenden** und `handling=strict` festlegen.

 ## <a name="chained--reverse-chained-searching"></a>Verkettete & umgekehrte verkettete Suche

Eine [verkettete Suche](https://www.hl7.org/fhir/search.html#chaining) ermöglicht ihnen die Suche mithilfe eines Suchparameters für eine Ressource, auf die von einer anderen Ressource verwiesen wird. Wenn Sie beispielsweise Feststellen suchen möchten, bei denen der Name des Patienten Jane ist, verwenden Sie Folgendes:

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

Auf ähnliche Weise können Sie eine umgekehrte verkettete Suche verwenden. Dadurch können Sie Ressourcen erhalten, in denen Sie Kriterien für andere Ressourcen angeben, die auf sie verweisen. Weitere Beispiele für verkettete und umgekehrt verkettete Suche finden Sie auf der [Seite FHIR-Suchbeispiele.](search-samples.md) 

> [!NOTE]
> In der Azure API for FHIR und in der Open Source-Datenbank, die von Cosmos DB unterstützt wird, gibt es eine Einschränkung, bei der jede Unterabfrage, die für die verketteten und umgekehrten verketteten Suchvorgänge erforderlich ist, nur 100 Elemente zurück gibt. Wenn mehr als 100 Elemente gefunden werden, erhalten Sie die folgende Fehlermeldung: "Unterabfragen in einem verketteten Ausdruck können nicht mehr als 100 Ergebnisse zurückgeben. Verwenden Sie ein selektiveres Kriterium." Um eine erfolgreiche Abfrage zu erhalten, müssen Sie genauer darauf sein, was Sie suchen.

## <a name="pagination"></a>Paginierung

Wie bereits erwähnt, sind die Ergebnisse einer Suche ein seitenseitiges Bündel. Standardmäßig gibt die Suche 10 Ergebnisse pro Seite zurück. Dies kann jedoch durch Angabe von erhöht (oder verringert) `_count` werden. Innerhalb des Bündels gibt es einen Selbstlink, der das aktuelle Ergebnis der Suche enthält. Wenn es zusätzliche Übereinstimmungen gibt, enthält das Paket einen nächsten Link. Sie können den nächsten Link weiterhin verwenden, um die nachfolgenden Seiten der Ergebnisse zu erhalten. `_count` ist auf maximal 1.000 Elemente beschränkt. 

Derzeit unterstützt der Azure API for FHIR nur den nächsten Link in Paketen und keine ersten, letzten oder vorherigen Links.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Grundlagen der Suche erfahren haben, finden Sie auf der Seite mit den Suchbeispielen Details zur Suche mithilfe verschiedener Suchparameter, Modifizierer und anderer FHIR-Suchszenarien.

>[!div class="nextstepaction"]
>[FHIR-Suchbeispiele](search-samples.md)

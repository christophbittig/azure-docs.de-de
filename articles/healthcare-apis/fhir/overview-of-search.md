---
title: Übersicht über die FHIR-Suche in Azure Healthcare-APIs
description: In diesem Artikel wird eine Übersicht über die FHIR-Suche beschrieben, die in Azure Healthcare-APIs implementiert ist.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 33a83bd007558de8b9b2300ab6eec7e8fa1a4584
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821359"
---
# <a name="overview-of-fhir-search"></a>Übersicht über die FHIR-Suche

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die FHIR-Spezifikation definiert die Grundlagen der Suche nach FHIR-Ressourcen. Dieser Artikel führt Sie durch einige wichtige Aspekte bei der Suche nach Ressourcen in FHIR. Vollständige Informationen zum Durchsuchen von FHIR-Ressourcen finden Sie unter [Suchen](https://www.hl7.org/fhir/search.html) in der HL7 FHIR-Spezifikation. In diesem Artikel werden Beispiele für die Suchsyntax gezeigt. Jede Suche wird für Ihren FHIR-Server ausgeführt, der in der Regel über die URL `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com` verfügt. In den Beispielen verwenden wir den Platzhalter {{FHIR_URL}} für diese URL. 

FHIR-Suchvorgänge können für einen bestimmten Ressourcentyp, ein [angegebenes](https://www.hl7.org/fhir/compartmentdefinition.html)Abteil oder alle Ressourcen durchgeführt werden. Die einfachste Möglichkeit zum Ausführen einer Suche in FHIR ist die Verwendung einer `GET` Anforderung. Wenn Sie beispielsweise alle Patienten in die Datenbank pullen möchten, können Sie die folgende Anforderung verwenden: 

```rest
GET {{FHIR_URL}}/Patient
```

Sie können auch mithilfe von `POST` suchen. Dies ist nützlich, wenn die Abfragezeichenfolge zu lang ist. Für die Suche mit `POST` können die Suchparameter als Formularkörper übermittelt werden. Dies ermöglicht eine längere, komplexere Reihe von Abfrageparametern, die in einer Abfragezeichenfolge schwer zu erkennen und zu verstehen sind.

Wenn die Suchanforderung erfolgreich ist, erhalten Sie eine FHIR-Bündelantwort vom Typ `searchset` . Wenn bei der Suche ein Fehler auftritt, finden Sie die Fehlerdetails im , damit Sie nachvollziehen können, warum `OperationOutcome` die Suche fehlgeschlagen ist.

In den folgenden Abschnitten werden die verschiedenen Aspekte der Suche beschrieben. Nachdem Sie diese Details überprüft haben, finden Sie auf unserer Beispielseite Beispiele für Suchvorgänge, die Sie im FHIR-Dienst in den Azure Healthcare-APIs durchführen können. [](search-samples.md)

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

Es gibt [allgemeine Suchparameter,](https://www.hl7.org/fhir/search.html#all) die für alle Ressourcen gelten. Diese sind unten aufgeführt, zusammen mit ihrer Unterstützung:

| **Allgemeiner Suchparameter** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | Ja                  | Ja                       
| _lastUpdated                | Ja                  | Ja                       |
| _tag                        | Ja                  | Ja                       |
| _type                       | Ja                  | Ja                       |
| _security                   | Ja                  | Ja                       |
| _profile                    | Ja                  | Ja                       |
| _has                        | Ja.                 | Ja                       |  |
| _query                      | Nein                   | Nein                        |
| _filter                     | Nein                   | Nein                        |
| _list                       | Nein                   | Nein                        |
| _text                       | Nein                   | Nein                        |
| _content                    | Nein                   | Nein                        |

### <a name="resource-specific-parameters"></a>Ressourcenspezifische Parameter

Mit dem FHIR-Dienst in den Azure [Healthcare-APIs](https://www.hl7.org/fhir/searchparameter-registry.html) unterstützen wir fast alle ressourcenspezifischen Suchparameter, die durch die FHIR-Spezifikation definiert sind. Die einzigen Suchparameter, die wir nicht unterstützen, sind unter den folgenden Links verfügbar:

* [NICHT unterstützte Suchparameter fürHEIT3](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [Nicht unterstützte R4-Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

Sie können die aktuelle Unterstützung für Suchparameter auch in der [FHIR Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html) mit der folgenden Anforderung sehen:

```rest
GET {{FHIR_URL}}/metadata
```

Um die Suchparameter in der Capability-Anweisung zu sehen, navigieren Sie zu , um die Suchparameter für jede Ressource und die Suchparameter für alle `CapabilityStatement.rest.resource.searchParam` `CapabilityStatement.rest.searchParam` Ressourcen zu suchen.

> [!NOTE]
> Der FHIR-Dienst in den Azure Healthcare-APIs erstellt oder indiziert nicht automatisch Suchparameter, die nicht in der FHIR-Spezifikation definiert sind. Wir unterstützen Sie jedoch bei der Definition Ihrer eigenen [Suchparameter.](how-to-do-custom-search.md)

### <a name="composite-search-parameters"></a>Zusammengesetzte Suchparameter
Mit der zusammengesetzten Suche können Sie nach Wertpaaren suchen. Wenn Sie beispielsweise nach einer Höhenerkennung suchen, bei der die Person 60 Zoll groß war, möchten  Sie sicherstellen, dass eine einzelne Komponente der Beobachtung den Höhencode und den Wert von 60 enthielt. Sie möchten keine Beobachtung erhalten, bei der eine Gewichtung von 60 und eine Höhe von 48 gespeichert wurde, obwohl die Beobachtung Einträge mit einem Wert von 60 und einen Höhencode nur in verschiedenen Komponentenabschnitten enthalten würde. 

Mit dem FHIR-Dienst für die Azure Healthcare-APIs unterstützen wir die folgenden Suchparametertyppaare:

* Verweis, Token
* Token, Datum
* Token, Zahl, Zahl
* Token, Menge
* Token, Zeichenfolge
* Token, Token

Weitere Informationen finden Sie unter Zusammengesetzte [HL7-Suchparameter.](https://www.hl7.org/fhir/search.html#composite) 

> [!NOTE]
> Zusammengesetzte Suchparameter unterstützen keine Modifizierer nach der FHIR-Spezifikation.

 ### <a name="modifiers--prefixes"></a>Modifizierer & Präfixe

[Mit Modifizierern](https://www.hl7.org/fhir/search.html#modifiers) können Sie den Suchparameter ändern. Im Folgenden finden Sie eine Übersicht über alle FHIR-Modifizierer und die Unterstützung: 

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

Für Suchparameter mit einer bestimmten Reihenfolge (Zahlen, Datumsangaben und Mengen) können Sie ein Präfix für den Parameter verwenden, um Übereinstimmungen zu finden. [](https://www.hl7.org/fhir/search.html#prefix) Der FHIR-Dienst in den Azure Healthcare-APIs unterstützt alle Präfixe.

 ### <a name="search-result-parameters"></a>Suchergebnisparameter
Um die zurückgegebenen Ressourcen zu verwalten, gibt es Suchergebnisparameter, die Sie in Ihrer Suche verwenden können. Weitere Informationen zur Verwendung der einzelnen Suchergebnisparameter finden Sie auf der [HL7-Website.](https://www.hl7.org/fhir/search.html#return) 

| **Suchergebnisparameter**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Ja                  | Ja                       |
| _count                        | Ja                  | Ja                       | _count ist auf 1.000 Ressourcen beschränkt. Wenn er höher als 1000 festgelegt ist, werden nur 1.000 zurückgegeben, und im Paket wird eine Warnung zurückgegeben.                               |
| _include                      | Ja                  | Ja                       | Enthaltene Elemente sind auf 100 beschränkt. _include unter PaaS und OSS auf Cosmos DB enthalten keine :iterate-Unterstützung [(#2137).](https://github.com/microsoft/fhir-server/issues/2137)                               |
| _revinclude                   | Ja                  | Ja                       |Enthaltene Elemente sind auf 100 beschränkt. _revinclude unter PaaS und OSS in Cosmos DB enthalten keine :iterate-Unterstützung [(#2137).](https://github.com/microsoft/fhir-server/issues/2137) Es gibt auch einen falschen Statuscode für eine ungültige Anforderung [#1319.](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Ja             | Ja                   |
| _total                        | Partial              | Partial                   | _total=none und _total=accurate                               |
| _sort                         | Partial              | Partial                   | sort=_lastUpdated wird unterstützt. Sortiert den Datensatz standardmäßig in aufsteigender Reihenfolge. Sie können das Präfix "-" verwenden, um in absteigender Reihenfolge zu sortieren. Für Azure API for FHIR- und OSS Cosmos-Datenbankdatenbanken, die nach dem 20. April 2021 erstellt wurden, wird die Sortierung auch nach Vor- und Nachname sowie nach dem datumsgemäßen Zeitpunkt unterstützt.             |
| _contained                    | Nein                   | Nein                        |
| _containedType                | Nein                   | Nein                        |
| _score                        | Nein                   | Nein                        |

Standardmäßig ist der FHIR-Dienst in den Azure Healthcare-APIs auf die lenient-Behandlung festgelegt. Dies bedeutet, dass der Server alle unbekannten oder nicht unterstützten Parameter ignoriert. Wenn Sie eine strikte Behandlung verwenden möchten, können Sie den **Prefer-Header verwenden** und `handling=strict` festlegen.

 ## <a name="chained--reverse-chained-searching"></a>Verkettete & umgekehrte verkettete Suche

Eine [verkettete Suche](https://www.hl7.org/fhir/search.html#chaining) ermöglicht ihnen die Suche mithilfe eines Suchparameters für eine Ressource, auf die von einer anderen Ressource verwiesen wird. Wenn Sie beispielsweise Feststellen suchen möchten, bei denen der Name des Patienten Jane ist, verwenden Sie Folgendes:

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

Auf ähnliche Weise können Sie eine umgekehrte verkettete Suche verwenden. Dadurch können Sie Ressourcen erhalten, in denen Sie Kriterien für andere Ressourcen angeben, die auf sie verweisen. Weitere Beispiele für verkettete und umgekehrt verkettete Suche finden Sie auf der [Seite FHIR-Suchbeispiele.](search-samples.md) 


## <a name="pagination"></a>Paginierung

Wie bereits erwähnt, sind die Ergebnisse einer Suche ein seitenseitiges Bündel. Standardmäßig gibt die Suche 10 Ergebnisse pro Seite zurück. Dies kann jedoch durch Angabe von erhöht (oder verringert) `_count` werden. Innerhalb des Bündels gibt es einen Selbstlink, der das aktuelle Ergebnis der Suche enthält. Wenn es zusätzliche Übereinstimmungen gibt, enthält das Paket einen nächsten Link. Sie können den nächsten Link weiterhin verwenden, um die nachfolgenden Seiten der Ergebnisse zu erhalten. `_count` ist auf maximal 1.000 Elemente beschränkt. 

Derzeit unterstützt der FHIR-Dienst in den Azure Healthcare-APIs nur den nächsten Link in Paketen und keine ersten, letzten oder vorherigen Links.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Grundlagen der Suche erfahren haben, finden Sie auf der Seite mit den Suchbeispielen Details zur Suche mithilfe verschiedener Suchparameter, Modifizierer und anderer FHIR-Suchszenarien.

>[!div class="nextstepaction"]
>[FHIR-Suchbeispiele](search-samples.md)

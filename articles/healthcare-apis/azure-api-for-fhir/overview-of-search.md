---
title: Übersicht über die Suche in Azure API for FHIR
description: In diesem Artikel wird eine Übersicht über die FHIR-Suche beschrieben, die in Azure API for FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/29/2021
ms.author: cavoeg
ms.openlocfilehash: b8fc847edc18e9103534961051d550340dac9e98
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133366023"
---
# <a name="overview-of-search-in-azure-api-for-fhir"></a>Übersicht über die Suche in Azure API for FHIR

Die FHIR-Spezifikation definiert die Grundlagen der Suche nach FHIR-Ressourcen. Dieser Artikel führt Sie durch einige wichtige Aspekte beim Durchsuchen von Ressourcen in FHIR. Ausführliche Informationen zum Durchsuchen von FHIR-Ressourcen finden Sie unter [Suchen](https://www.hl7.org/fhir/search.html) in der HL7 FHIR-Spezifikation. In diesem Artikel werden Beispiele für die Suchsyntax vorgestellt. Jede Suche erfolgt auf Ihrem FHIR-Server, der in der Regel über die URL `https://<FHIRSERVERNAME>.azurewebsites.net` verfügt. In den Beispielen verwenden wir den Platzhalter {{FHIR_URL}} für diese URL. 

FHIR-Suchvorgänge können für einen bestimmten Ressourcentyp, ein angegebenes [Depot](https://www.hl7.org/fhir/compartmentdefinition.html)oder alle Ressourcen durchgeführt werden. Die einfachste Möglichkeit zum Ausführen einer Suche in FHIR ist die Verwendung einer `GET` Anforderung. Wenn Sie beispielsweise alle Patienten in der Datenbank abrufen möchten, können Sie die folgende Anforderung verwenden: 

```rest
GET {{FHIR_URL}}/Patient
```

Sie können auch mit `POST` suchen. Dies ist hilfreich, wenn die Abfragezeichenfolge zu lang ist. Für die Suche mit `POST` können die Suchparameter als Formulartext übermittelt werden. Dies ermöglicht eine längere, komplexere Reihe von Abfrageparametern, die in einer Abfragezeichenfolge schwer zu erkennen und zu verstehen sind.

Wenn die Suchanforderung erfolgreich ist, erhalten Sie eine FHIR-Paketantwort vom Typ `searchset` . Wenn die Suche fehlschlägt, finden Sie die Fehlerdetails im , `OperationOutcome` damit Sie besser verstehen können, warum die Suche fehlgeschlagen ist.

In den folgenden Abschnitten werden die verschiedenen Aspekte der Suche behandelt. Nachdem Sie diese Details überprüft haben, finden Sie auf unserer [Beispielseite](search-samples.md) Beispiele für Suchvorgänge, die Sie im Azure API for FHIR vornehmen können.

## <a name="search-parameters"></a>Suchparameter

Wenn Sie eine Suche durchführen, suchen Sie basierend auf verschiedenen Attributen der Ressource. Diese Attribute werden als Suchparameter bezeichnet. Jede Ressource verfügt über einen Satz definierter Suchparameter. Der Suchparameter muss definiert und in der Datenbank indiziert werden, damit Sie erfolgreich danach suchen können.

Jeder Suchparameter verfügt über einen definierten [Datentyp.](https://www.hl7.org/fhir/search.html#ptypes) Die Unterstützung für die verschiedenen Datentypen wird unten beschrieben:

> [!WARNING]
> Derzeit besteht ein Problem bei der Verwendung von _sort auf dem Azure API for FHIR mit verketteter Suche. Weitere Informationen finden Sie unter Open-Source-Problem [#2344](https://github.com/microsoft/fhir-server/issues/2344). Dies wird während eines Release im Dezember 2021 behoben. 

| **Suchparametertyp**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
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

Es gibt [allgemeine Suchparameter,](https://www.hl7.org/fhir/search.html#all) die für alle Ressourcen gelten. Diese werden unten zusammen mit ihrer Unterstützung innerhalb der Azure API for FHIR aufgeführt:

| **Allgemeiner Suchparameter** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | Ja                  | Ja                       
| _lastUpdated                | Ja                  | Ja                       |
| _tag                        | Ja                  | Ja                       |
| _type                       | Ja                  | Ja                       |
| _security                   | Ja                  | Ja                       |
| _profile                    | Ja                  | Ja                       |
| _has                        | Partial              | Ja                       | Die Unterstützung für _has befindet sich im MVP im Azure API for FHIR und in der OSS-Version, die von Cosmos DB unterstützt wird. Weitere Informationen finden Sie unten im Verkettungsabschnitt. |
| _query                      | Nein                   | Nein                        |
| _filter                     | Nein                   | Nein                        |
| _list                       | Nein                   | Nein                        |
| _text                       | Nein                   | Nein                        |
| _content                    | Nein                   | Nein                        |

### <a name="resource-specific-parameters"></a>Ressourcenspezifische Parameter

Mit dem Azure API for FHIR unterstützen wir fast alle [ressourcenspezifischen Suchparameter,](https://www.hl7.org/fhir/searchparameter-registry.html) die durch die FHIR-Spezifikation definiert sind. Die einzigen Suchparameter, die wir nicht unterstützen, sind unter den folgenden Links verfügbar:

* [NICHT unterstützte Suchparameter fürHEIT3](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [Nicht unterstützte R4-Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

Sie können die aktuelle Unterstützung für Suchparameter auch in der [FHIR Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html) mit der folgenden Anforderung sehen:

```rest
GET {{FHIR_URL}}/metadata
```

Um die Suchparameter in der Capability-Anweisung zu sehen, navigieren Sie zu , um die Suchparameter für jede Ressource und die Suchparameter für `CapabilityStatement.rest.resource.searchParam` `CapabilityStatement.rest.searchParam` alle Ressourcen zu suchen.

> [!NOTE]
> Die Azure API for FHIR erstellt oder indiziert nicht automatisch Suchparameter, die nicht durch die FHIR-Spezifikation definiert sind. Wir unterstützen Sie jedoch bei der Definition Ihrer eigenen [Suchparameter.](how-to-do-custom-search.md)

### <a name="composite-search-parameters"></a>Zusammengesetzte Suchparameter
Mit der zusammengesetzten Suche können Sie nach Wertpaaren suchen. Wenn Sie beispielsweise nach einer Höhenerkennung suchen, bei der die Person 60 Zoll groß war, möchten  Sie sicherstellen, dass eine einzelne Komponente der Beobachtung den Höhencode und den Wert von 60 enthielt. Sie möchten keine Beobachtung erhalten, bei der eine Gewichtung von 60 und eine Höhe von 48 gespeichert wurde, obwohl die Beobachtung Einträge mit einem Wert von 60 und einem Höhencode nur in verschiedenen Komponentenabschnitten enthalten würde. 

Mit dem Azure API for FHIR unterstützen wir die folgenden Suchparametertyppaare:

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

[Mit Modifizierern](https://www.hl7.org/fhir/search.html#modifiers) können Sie den Suchparameter ändern. Im Folgenden finden Sie eine Übersicht über alle FHIR-Modifizierer und die Unterstützung im Azure API for FHIR. 

| **Modifizierer** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
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

Für Suchparameter mit einer bestimmten Reihenfolge (Zahlen, Datumsangaben und Mengen) können Sie ein Präfix für den Parameter verwenden, um Übereinstimmungen zu finden. [](https://www.hl7.org/fhir/search.html#prefix) Die Azure API for FHIR unterstützt alle Präfixe.

 ### <a name="search-result-parameters"></a>Suchergebnisparameter
Um die zurückgegebenen Ressourcen zu verwalten, gibt es Suchergebnisparameter, die Sie in Ihrer Suche verwenden können. Weitere Informationen zur Verwendung der einzelnen Suchergebnisparameter finden Sie auf der [HL7-Website.](https://www.hl7.org/fhir/search.html#return) 

| **Suchergebnisparameter**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Ja                  | Ja                       |
| _count                        | Ja                  | Ja                       | _count ist auf 1.000 Ressourcen beschränkt. Wenn er höher als 1000 festgelegt ist, werden nur 1.000 zurückgegeben, und im Bündel wird eine Warnung zurückgegeben.                               |
| _include                      | Ja                  | Ja                       | Enthaltene Elemente sind auf 100 beschränkt. _include unter PaaS und OSS in Cosmos DB enthalten keine :iterate-Unterstützung [(#2137).](https://github.com/microsoft/fhir-server/issues/2137)                               |
| _revinclude                   | Ja                  | Ja                       |Enthaltene Elemente sind auf 100 beschränkt. _revinclude unter PaaS und OSS in Cosmos DB enthalten keine :iterate-Unterstützung [(#2137).](https://github.com/microsoft/fhir-server/issues/2137)  Es gibt auch einen falschen Statuscode für eine ungültige Anforderung [#1319.](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Ja             | Ja                   |
| _total                        | Partial              | Partial                   | _total=none und _total=accurate                               |
| _sort                         | Partial              | Partial                   | sort=_lastUpdated wird für Azure API for FHIR und den FHIR-Dienst unterstützt. Für Azure API for FHIR- und OSS Cosmos DB-Datenbanken, die nach dem 20. April 2021 erstellt wurden, wird die Sortierung nach Vorname, Nachname, Geburtsdatum und Demostdatum unterstützt. Beachten Sie, dass es ein offenes Problem mit _sort mit verketteter Suche gibt, das im Open-Source-Issue [#2344 dokumentiert ist.](https://github.com/microsoft/fhir-server/issues/2344)         |
| _contained                    | Nein                   | Nein                        |
| _containedType                | Nein                   | Nein                        |
| _score                        | Nein                   | Nein                        |

> [!NOTE]
> Standardmäßig wird `_sort` der Datensatz in aufsteigender Reihenfolge sortiert. Sie können das Präfix `'-'` verwenden, um in absteigender Reihenfolge zu sortieren. Darüber hinaus ermöglichen der FHIR-Dienst und Azure API for FHIR, dass Sie nur nach einem einzelnen Feld gleichzeitig sortieren können.

Standardmäßig ist die Azure API for FHIR auf lenient handling (lenient handling) festgelegt. Dies bedeutet, dass der Server alle unbekannten oder nicht unterstützten Parameter ignoriert. Wenn Sie eine strikte Behandlung verwenden möchten, können Sie den **Prefer-Header** verwenden und `handling=strict` festlegen.

 ## <a name="chained--reverse-chained-searching"></a>Verkettete & umgekehrte verkettete Suche

Eine [verkettete Suche](https://www.hl7.org/fhir/search.html#chaining) ermöglicht ihnen die Suche mithilfe eines Suchparameters für eine Ressource, auf die von einer anderen Ressource verwiesen wird. Wenn Sie beispielsweise Feststellen suchen möchten, bei denen der Name des Patienten Jane ist, verwenden Sie Folgendes:

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

Auf ähnliche Weise können Sie eine umgekehrte verkettete Suche verwenden. Dadurch können Sie Ressourcen erhalten, in denen Sie Kriterien für andere Ressourcen angeben, die auf sie verweisen. Weitere Beispiele für verkettete und umgekehrt verkettete Suche finden Sie auf der [Seite FHIR-Suchbeispiele.](search-samples.md) 

> [!NOTE]
> In der Azure API for FHIR und dem von Cosmos DB unterstützten Open Source gibt es eine Einschränkung, bei der jede Unterabfrage, die für die verketteten und umgekehrt verketteten Suchvorgänge erforderlich ist, nur 1.000 Elemente zurück gibt. Wenn mehr als 1.000 Elemente gefunden werden, erhalten Sie die folgende Fehlermeldung: "Unterabfragen in einem verketteten Ausdruck können nicht mehr als 1.000 Ergebnisse zurückgeben. Verwenden Sie ein selektiveres Kriterium." Um eine erfolgreiche Abfrage zu erhalten, müssen Sie spezifischer sein, was Sie suchen.

## <a name="pagination"></a>Paginierung

Wie bereits erwähnt, sind die Ergebnisse einer Suche ein auspageiertes Paket. Standardmäßig gibt die Suche 10 Ergebnisse pro Seite zurück. Dies kann jedoch durch Angabe von erhöht (oder verringert) `_count` werden. Innerhalb des Bündels gibt es einen Selbstlink, der das aktuelle Ergebnis der Suche enthält. Wenn zusätzliche Übereinstimmungen enthalten sind, enthält das Paket einen nächsten Link. Sie können weiterhin den nächsten Link verwenden, um die nachfolgenden Seiten der Ergebnisse zu erhalten. `_count` ist auf maximal 1.000 Elemente beschränkt. 

Derzeit unterstützt der Azure API for FHIR nur den nächsten Link in Paketen und keine ersten, letzten oder vorherigen Links.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Grundlagen der Suche erfahren haben, finden Sie auf der Seite mit den Suchbeispielen Weitere Informationen zur Suche mit verschiedenen Suchparametern, Modifizierern und anderen FHIR-Suchszenarien.

>[!div class="nextstepaction"]
>[FHIR-Suchbeispiele](search-samples.md)

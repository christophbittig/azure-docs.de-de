---
title: Übersicht über die FHIR-Suche in Azure Healthcare-APIs
description: In diesem Artikel wird eine Übersicht über die FHIR-Suche beschrieben, die in Azure Healthcare-APIs implementiert ist.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/24/2021
ms.author: cavoeg
ms.openlocfilehash: dfdde4bb53624777d4e11cbd6652f749a6f3a394
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368842"
---
# <a name="overview-of-fhir-search"></a>Übersicht über die FHIR-Suche

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die FHIR-Spezifikation definiert die Grundlagen der Suche nach FHIR-Ressourcen. Dieser Artikel führt Sie durch einige wichtige Aspekte beim Durchsuchen von Ressourcen in FHIR. Ausführliche Informationen zum Durchsuchen von FHIR-Ressourcen finden Sie unter [Suchen](https://www.hl7.org/fhir/search.html) in der HL7 FHIR-Spezifikation. In diesem Artikel werden Beispiele für die Suchsyntax vorgestellt. Jede Suche erfolgt auf Ihrem FHIR-Server, der in der Regel über die URL `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com` verfügt. In den Beispielen verwenden wir den Platzhalter {{FHIR_URL}} für diese URL. 

FHIR-Suchvorgänge können für einen bestimmten Ressourcentyp, ein angegebenes [Depot](https://www.hl7.org/fhir/compartmentdefinition.html)oder alle Ressourcen durchgeführt werden. Die einfachste Möglichkeit zum Ausführen einer Suche in FHIR ist die Verwendung einer `GET` Anforderung. Wenn Sie beispielsweise alle Patienten in der Datenbank abrufen möchten, können Sie die folgende Anforderung verwenden: 

```rest
GET {{FHIR_URL}}/Patient
```

Sie können auch mit `POST` suchen. Dies ist hilfreich, wenn die Abfragezeichenfolge zu lang ist. Für die Suche mit `POST` können die Suchparameter als Formulartext übermittelt werden. Dies ermöglicht eine längere, komplexere Reihe von Abfrageparametern, die in einer Abfragezeichenfolge schwer zu erkennen und zu verstehen sind.

Wenn die Suchanforderung erfolgreich ist, erhalten Sie eine FHIR-Paketantwort vom Typ `searchset` . Wenn die Suche fehlschlägt, finden Sie die Fehlerdetails im , `OperationOutcome` damit Sie besser verstehen können, warum die Suche fehlgeschlagen ist.

In den folgenden Abschnitten werden die verschiedenen Aspekte der Suche behandelt. Nachdem Sie diese Details überprüft haben, finden Sie auf unserer [Beispielseite](search-samples.md) Beispiele für Suchvorgänge, die Sie im FHIR-Dienst in den Azure Healthcare-APIs durchführen können.

## <a name="search-parameters"></a>Suchparameter

Wenn Sie eine Suche durchführen, suchen Sie basierend auf verschiedenen Attributen der Ressource. Diese Attribute werden als Suchparameter bezeichnet. Jede Ressource verfügt über einen Satz definierter Suchparameter. Der Suchparameter muss definiert und in der Datenbank indiziert werden, damit Sie erfolgreich danach suchen können.

Jeder Suchparameter verfügt über einen definierten [Datentyp.](https://www.hl7.org/fhir/search.html#ptypes) Die Unterstützung für die verschiedenen Datentypen wird unten beschrieben:


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

Es gibt [allgemeine Suchparameter,](https://www.hl7.org/fhir/search.html#all) die für alle Ressourcen gelten. Diese werden unten zusammen mit ihrer Unterstützung aufgeführt:

| **Allgemeiner Suchparameter** | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
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

Mit dem FHIR-Dienst in den Azure Healthcare-APIs unterstützen wir fast alle [ressourcenspezifischen Suchparameter,](https://www.hl7.org/fhir/searchparameter-registry.html) die durch die FHIR-Spezifikation definiert werden. Die einzigen Suchparameter, die wir nicht unterstützen, sind unter den folgenden Links verfügbar:

* [STU3– Nicht unterstützte Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [Nicht unterstützte R4-Suchparameter](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

Sie können auch die aktuelle Unterstützung für Suchparameter in der [FHIR Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html) mit der folgenden Anforderung anzeigen:

```rest
GET {{FHIR_URL}}/metadata
```

Um die Suchparameter in der Capability-Anweisung anzuzeigen, navigieren Sie zu , `CapabilityStatement.rest.resource.searchParam` um die Suchparameter für jede Ressource anzuzeigen und `CapabilityStatement.rest.searchParam` die Suchparameter für alle Ressourcen zu finden.

> [!NOTE]
> Der FHIR-Dienst in den Azure Healthcare-APIs erstellt oder indiziert nicht automatisch Suchparameter, die nicht durch die FHIR-Spezifikation definiert sind. Wir unterstützen Sie jedoch beim Definieren Ihrer eigenen [Suchparameter.](how-to-do-custom-search.md)

### <a name="composite-search-parameters"></a>Zusammengesetzte Suchparameter
Mit der zusammengesetzten Suche können Sie nach Wertpaaren suchen. Wenn Sie beispielsweise nach einer Höhenüberwachung suchen, bei der die Person 60 Zoll betrug, sollten Sie sicherstellen, dass eine einzelne Komponente der Beobachtung den Code der Höhe **und** den Wert 60 enthält. Sie möchten keine Beobachtung erhalten, bei der eine Gewichtung von 60 und eine Höhe von 48 gespeichert wurde, obwohl die Beobachtung Einträge enthalten würde, die für den Wert 60 und den Code der Höhe qualifiziert sind, nur in verschiedenen Komponentenabschnitten. 

Mit dem FHIR-Dienst für die Azure Healthcare-APIs unterstützen wir die folgenden Suchparametertyppaare:

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

[Modifizierer](https://www.hl7.org/fhir/search.html#modifiers) ermöglichen es Ihnen, den Suchparameter zu ändern. Im Folgenden finden Sie eine Übersicht über alle FHIR-Modifizierer und die Unterstützung: 

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

Für Suchparameter mit einer bestimmten Reihenfolge (Zahlen, Datumsangaben und Mengen) können Sie ein [Präfix](https://www.hl7.org/fhir/search.html#prefix) für den Parameter verwenden, um Übereinstimmungen zu finden. Der FHIR-Dienst in den Azure Healthcare-APIs unterstützt alle Präfixe.

 ### <a name="search-result-parameters"></a>Suchergebnisparameter
Zur Unterstützung der Verwaltung der zurückgegebenen Ressourcen können Sie Suchergebnisparameter in Ihrer Suche verwenden. Ausführliche Informationen zur Verwendung der einzelnen Suchergebnisparameter finden Sie auf der [HL7-Website.](https://www.hl7.org/fhir/search.html#return) 

| **Suchergebnisparameter**  | **Azure API for FHIR** | **FHIR-Dienst in Azure Healthcare-APIs** | **Kommentar**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Ja                  | Ja                       |
| _count                        | Ja                  | Ja                       | _count ist auf 1.000 Ressourcen beschränkt. Wenn sie höher als 1000 festgelegt ist, wird nur 1000 zurückgegeben, und im Paket wird eine Warnung zurückgegeben.                               |
| _include                      | Ja                  | Ja                       | Enthaltene Elemente sind auf 100 beschränkt. _include unter PaaS und OSS in Cosmos DB enthalten keine Unterstützung für :iterate [(#2137)](https://github.com/microsoft/fhir-server/issues/2137).                               |
| _revinclude                   | Ja                  | Ja                       |Enthaltene Elemente sind auf 100 beschränkt. _revinclude unter PaaS und OSS in Cosmos DB enthalten keine Unterstützung für :iterate [(#2137)](https://github.com/microsoft/fhir-server/issues/2137). Es gibt auch einen falschen Statuscode für eine ungültige Anforderung [#1319.](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Ja             | Ja                   |
| _total                        | Partial              | Partial                   | _total=none und _total=accurate                               |
| _sort                         | Partial              | Partial                   | sort=_lastUpdated wird für Azure API for FHIR und den FHIR-Dienst unterstützt. Für den FHIR-Dienst und die OSS-SQL DB-FHIR-Server wird das Sortieren nach Zeichenfolgen und dateTime-Feldern unterstützt. Für Azure API for FHIR- und OSS-Cosmos Datenbankdatenbanken, die nach dem 20. April 2021 erstellt wurden, wird die Sortierung nach Vorname, Nachname, Geburtsdatum und klinischem Datum unterstützt.             |
| _contained                    | Nein                   | Nein                        |
| _containedType                | Nein                   | Nein                        |
| _score                        | Nein                   | Nein                        |

> [!NOTE]
> Standardmäßig `_sort` sortiert den Datensatz in aufsteigender Reihenfolge. Sie können das Präfix `'-'` verwenden, um in absteigender Reihenfolge zu sortieren. Darüber hinaus können Sie mit dem FHIR-Dienst und dem Azure API for FHIR nur nach einem einzelnen Feld gleichzeitig sortieren.

Standardmäßig ist der FHIR-Dienst in den Azure Healthcare-APIs auf nachsichtige Behandlung festgelegt. Dies bedeutet, dass der Server alle unbekannten oder nicht unterstützten Parameter ignoriert. Wenn Sie eine strikte Behandlung verwenden möchten, können Sie den **Prefer-Header** verwenden und `handling=strict` festlegen.

 ## <a name="chained--reverse-chained-searching"></a>Verkettete & umgekehrte verkettete Suche

Mit einer [verketteten Suche](https://www.hl7.org/fhir/search.html#chaining) können Sie mithilfe eines Suchparameters nach einer Ressource suchen, auf die von einer anderen Ressource verwiesen wird. Wenn Sie z. B. Nach Treffern suchen möchten, bei denen der Name des Patienten Jane lautet, verwenden Sie Folgendes:

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

Ebenso können Sie eine umgekehrte verkettete Suche durchführen. Dadurch können Sie Ressourcen abrufen, in denen Sie Kriterien für andere Ressourcen angeben, die auf sie verweisen. Weitere Beispiele für verkettete und umgekehrt verkettete Suche finden Sie auf der Seite [mit FHIR-Suchbeispielen.](search-samples.md) 


## <a name="pagination"></a>Paginierung

Wie bereits erwähnt, sind die Ergebnisse einer Suche ein ausgelagerunges Bündel. Standardmäßig gibt die Suche 10 Ergebnisse pro Seite zurück, dies kann jedoch durch Angabe von erhöht (oder verringert) `_count` werden. Innerhalb des Pakets gibt es einen Selbstlink, der das aktuelle Ergebnis der Suche enthält. Wenn zusätzliche Übereinstimmungen vorhanden sind, enthält das Paket einen nächsten Link. Sie können weiterhin den nächsten Link verwenden, um die nachfolgenden Ergebnisseiten abzurufen. `_count` ist auf maximal 1.000 Elemente beschränkt. 

Derzeit unterstützt der FHIR-Dienst in den Azure Healthcare-APIs nur den nächsten Link in Paketen und keine ersten, letzten oder vorherigen Links.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Suche beschäftigt haben, finden Sie auf der Seite mit den Suchbeispielen Details zur Suche mit verschiedenen Suchparametern, Modifizierern und anderen FHIR-Suchszenarien.

>[!div class="nextstepaction"]
>[FHIR-Suchbeispiele](search-samples.md)

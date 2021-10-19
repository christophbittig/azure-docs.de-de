---
title: Suchbeispiele für den FHIR-Dienst
description: Suchen mit verschiedenen Suchparametern, Modifizierern und anderen Suchtools für FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 17f2ae17f2dd4677734a71fdf395ac7cdab13b8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722195"
---
# <a name="fhir-search-examples"></a>FHIR-Suchbeispiele

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Im Folgenden finden Sie einige Beispiele für die Verwendung von FHIR-Suchvorgängen, z. B. Suchparameter und -modifizierer, ketten- und umgekehrte Kettensuche, zusammengesetzte Suche, Anzeigen des nächsten Eintragssets für Suchergebnisse und Suchen mit einer `POST` Anforderung. Weitere Informationen zur Suche finden Sie unter [Übersicht über die FHIR-Suche.](overview-of-search.md)
   
## <a name="search-result-parameters"></a>Suchergebnisparameter

### <a name="_include"></a>_include

`_include` sucht nach Ressourcen, die den angegebenen Parameter der Ressource enthalten. Sie können z. B. ressourcenübergreifend suchen, um nur diejenigen zu finden, die Informationen zu den Behandlungen für einen bestimmten Patienten enthalten, der `MedicationRequest` den `reference` Parameter `patient` enthält. Im folgenden Beispiel werden alle Patienten, auf die verwiesen wird, aus `MedicationRequests` dem `MedicationRequests` pullen:

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** und **_revinclude** sind auf 100 Elemente beschränkt.

### <a name="_revinclude"></a>_revinclude

`_revinclude` ermöglicht es Ihnen, die entgegengesetzte Richtung als zu `_include` suchen. Sie können z. B. nach Patienten suchen und dann alle Gefundenen, die auf die Patienten verweisen, in umgekehrter Reihenfolge enthalten:

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` engt das Suchergebnis auf eine Teilmenge von Feldern ein, um die Antwortgröße zu reduzieren, indem unnötige Daten weglassen werden. Der -Parameter akzeptiert eine durch Komma getrennte Liste von Basiselementen:

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

In dieser Anforderung erhalten Sie ein Bündel von Patienten zurück, aber jede Ressource enthält nur die Bezeichner und den aktiven Status des Patienten. Ressourcen in dieser zurückgegebenen Antwort enthalten den Wert , um anzugeben, dass sie `meta.tag` `SUBSETTED` ein unvollständiger Satz von Ergebnissen sind.

## <a name="search-modifiers"></a>Suchmodifizierer

### <a name="not"></a>:not

`:not` ermöglicht es Ihnen, Ressourcen zu finden, bei denen ein Attribut nicht true ist. Sie können z. B. nach Patienten suchen, bei denen das Geschlecht nicht "female" ist:

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

Als Rückgabewert erhalten Sie alle Patienteneinträge, bei denen das Geschlecht nicht "female" ist, einschließlich leerer Werte (Einträge, die ohne Geschlecht angegeben sind). Dies ist anders als bei der Suche nach Patienten, bei denen das Geschlecht ein Geschlecht ist, da dies die Einträge ohne ein bestimmtes Geschlecht nicht enthalten würde.

### <a name="missing"></a>:missing

`:missing` gibt alle Ressourcen zurück, die keinen Wert für das angegebene Element haben, wenn der Wert ist, und gibt alle Ressourcen zurück, die das angegebene Element enthalten, wenn der Wert `true` `false` ist. Für einfache Datentypelemente wird für alle Ressourcen übereinstimmen, in denen das Element mit Erweiterungen vorhanden `:missing=true` ist, aber über einen leeren Wert verfügt. Wenn Sie z. B. alle Ressourcen suchen möchten, bei denen Informationen zum Geburtsdatum `Patient` fehlen, können Sie folgendes tun:

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` wird für Parameter verwendet und gibt Ergebnisse zurück, die genau mit dem Parameter übereinstimmen, z. B. in der `string` Casing- und Zeichenkonkettung.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

Diese Anforderung gibt `Patient` Ressourcen zurück, deren Name exakt mit identisch `Jon` ist. Wenn die Ressource Patienten mit Namen wie oder hatte, würde die Suche die Ressource ignorieren und überspringen, da sie nicht genau mit dem `Jonathan` `joN` angegebenen Wert übereinstimmen würde.

### <a name="contains"></a>:contains
`:contains` wird für Parameter verwendet und sucht nach Ressourcen mit partiellen Übereinstimmungen des angegebenen Werts an einer beliebigen Stelle in der Zeichenfolge innerhalb des `string` durchsuchten Felds. `contains` berücksichtigt die Groß-/Kleinschreibung nicht und ermöglicht die Zeichenkonkettung. Zum Beispiel:

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

Diese Anforderung gibt alle Ressourcen mit `Patient` Feldern zurück, die Werte mit der `address` Zeichenfolge "Zeichenfolge" enthalten. Dies bedeutet, dass Adressen, die Werte wie "1000" oder "59 St" enthalten, als Suchergebnisse zurückgegeben werden können.

## <a name="chained-search"></a>Verkettete Suche 

Um eine Reihe von Suchvorgängen durchzuführen, die mehrere Verweisparameter abdecken, können Sie die Reihe von Verweisparametern "verketten", indem Sie sie mithilfe eines Zeitraums nach dem anderen an die Serveranforderung `.` anfügen. Wenn Sie beispielsweise alle Ressourcen mit einem Verweis auf eine Ressource anzeigen möchten, `DiagnosticReport` `subject` die eine bestimmte `Patient` `name` enthält:  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

Diese Anforderung würde alle Ressourcen mit `DiagnosticReport` einem Patientensubjekt namens "Sarah" zurückgeben. Der `.` Zeitraum, nach dem das `Patient` Feld die verkettete Suche für den Verweisparameter des Parameters `subject` ausführt.

Eine weitere häufige Verwendung einer regulären Suche (keine verkettete Suche) ist die Suche nach allen Gefundenen für einen bestimmten Patienten. `Patient`s verfügen häufig über ein oder mehrere `Encounter` s mit einem Betreff. So suchen Sie nach allen `Encounter` Ressourcen für eine mit dem `Patient` bereitgestellten `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

Mithilfe der verketteten Suche können Sie alle Ressourcen finden, die mit einer bestimmten Information `Encounter` wie z.B. dem -Wert () in Übereinstimmung mit einer bestimmten `Patient` Information `birthdate` stehen:

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

Dies ermöglicht nicht nur die Suche nach Ressourcen für einen einzelnen Patienten, sondern für alle Patienten, die den angegebenen Wert für `Encounter` das Geburtsdatum haben. 

Darüber hinaus kann die verkettete Suche mehrmals in einer Anforderung mithilfe des Symbols durchgeführt werden, mit dem Sie in einer Anforderung nach mehreren `&` Bedingungen suchen können. In solchen Fällen sucht die verkettete Suche "unabhängig" nach jedem Parameter, anstatt nach Bedingungen zu suchen, die nur alle Bedingungen gleichzeitig erfüllen:

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

Dies würde alle Ressourcen zurückgeben, die "Sarah" als und eine mit der Adresse mit `Patient` `generalPractitioner` dem Zustand WA `generalPractitioner` haben. Anders ausgedrückt: Wenn eine Patientin Sarah aus dem Bundesstaat NY und Bill aus dem Bundesstaat WA beide als die des Patienten bezeichnet hätte, würde `generalPractitioner` zurückgegeben.

Informationen zu Szenarien, in denen die Suche ein AND-Vorgang sein muss, der alle Bedingungen als Gruppe abdeckt, finden Sie unten im Beispiel für die **zusammengesetzte** Suche.

## <a name="reverse-chain-search"></a>Umgekehrte Kettensuche

Mit der Kettensuche können Sie basierend auf den Eigenschaften der Ressourcen, auf die sie verweisen, nach Ressourcen suchen. Mithilfe der umgekehrten Kettensuche können Sie dies umgekehrt tun. Sie können anhand der Eigenschaften von Ressourcen, die auf sie verweisen, mithilfe des Parameters nach Ressourcen `_has` suchen. Beispielsweise verfügt die `Observation` Ressource über einen Suchparameter, `patient` der auf eine Patient-Ressource verweist. So suchen Sie alle Patient-Ressourcen, auf die von mit `Observation` einem bestimmten verwiesen `code` wird:

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

Diese Anforderung gibt Patient-Ressourcen zurück, auf die mit `Observation` dem Code verwiesen `527` wird. 

Darüber hinaus kann die umgekehrte Kettensuche eine rekursive Struktur haben. Wenn Sie beispielsweise nach allen Patienten suchen möchten, bei denen die Beobachtung über ein Überwachungsereignis eines bestimmten Benutzers `Observation` `janedoe` verfügt, können Sie folgende Aufgaben unternehmen:

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

## <a name="composite-search"></a>Zusammengesetzte Suche

Um nach Ressourcen zu suchen, die mehrere Bedingungen gleichzeitig erfüllen, verwenden Sie die zusammengesetzte Suche, die eine Sequenz einzelner Parameterwerte mit einem Symbol `$` verbindet. Das zurückgegebene Ergebnis wäre die Schnittmenge der Ressourcen, die mit allen Bedingungen übereinstimmen, die von den parametern für die verbundene Suche angegeben werden. Solche Suchparameter werden als zusammengesetzte Suchparameter bezeichnet und definieren einen neuen Parameter, der die mehreren Parameter in einer geschachtelten Struktur kombiniert. Wenn Sie z. B. alle Ressourcen suchen möchten, die einen wert kleiner oder `DiagnosticReport` `Observation` gleich 9,2 enthalten:

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

Diese Anforderung gibt die Komponente an, die einen Code von `2823-3` enthält, der in diesem Fall "füllium" wäre. Nach dem Symbol wird der Bereich des Werts für die Komponente angegeben, der für "kleiner als oder gleich" und für den `$` `lt` `9.2` Wertbereich "zeichenium" verwendet. 

Zusammengesetzte Suchparameter können auch verwendet werden, um mehrere Komponentencodewertmengen mit einem OR zu filtern. Um z. B. die Abfrage auszudrücken, um einen diastolic-Blutdruck zu finden, der größer als 90 oder systolic-Druck größer als 140 ist:

```rest
GET [your-fhir-server]/Observation?component-code-value-quantity=http://loinc.org|8462-4$gt90,http://loinc.org|8480-6$gt140
``` 

## <a name="search-the-next-entry-set"></a>Suchen des nächsten Eintragssets

Die maximale Anzahl von Einträgen, die pro einzelner Suchabfrage zurückgegeben werden können, beträgt 1.000. Möglicherweise verfügen Sie jedoch über mehr als 1.000 Einträge, die mit der Suchabfrage übereinstimmen, und Sie möchten nach den ersten 1.000 zurückgegebenen Einträgen die nächste Gruppe von Einträgen anzeigen. In diesem Fall würden Sie den Fortsetzungstokenwert `url` in wie im folgenden Ergebnis `searchset` `Bundle` verwenden:

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

Und Sie würden eine GET-Anforderung für die bereitgestellte URL unter dem Feld `relation: next` erstellen:

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

Dadurch wird der nächste Satz von Einträgen für Ihr Suchergebnis angezeigt. ist der vollständige Satz von Suchergebnissen, und das Fortsetzungstoken ist der Link, der vom Server bereitgestellt wird, damit Sie die Einträge abrufen können, die beim ersten Satz nicht angezeigt werden, da die Beschränkung auf die maximale Anzahl von Einträgen, die für eine Suchabfrage zurückgegeben werden, `searchset` `url` besteht.

## <a name="search-using-post"></a>Suchen mit POST

Alle oben genannten Suchbeispiele haben `GET` Anforderungen verwendet. Sie können Suchvorgänge auch mithilfe `POST` von Anforderungen mithilfe von `_search` durchzuführen:

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

Diese Anforderung würde alle Ressourcen `Patient` mit dem Wert `id` 45 zurückgeben. Genau wie bei GET-Anforderungen bestimmt der Server, welche der Ressourcen die Bedingungen erfüllt, und gibt eine Bündelressource in der HTTP-Antwort zurück.

Ein weiteres Beispiel für die Suche mit POST, bei der die Abfrageparameter als Formularkörper übermittelt werden, ist:

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über die FHIR-Suche](overview-of-search.md)

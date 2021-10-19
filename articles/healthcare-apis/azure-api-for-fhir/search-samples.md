---
title: Suchbeispiele für die Azure-API für FHIR
description: Suchen mit verschiedenen Suchparametern, Modifizierern und anderen FHIR-Suchtools
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 731b85d0486c3d9fa7ba9e3af4cc2f047912fcdc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339981"
---
# <a name="fhir-search-examples-for-azure-api-for-fhir"></a>FHIR-Suchbeispiele für Azure API for FHIR

Im Folgenden finden Sie einige Beispiele für die Verwendung von FHIR-Suchvorgängen, einschließlich Suchparametern und Modifizierern, Ketten- und Umgekehrt-Kettensuche, zusammengesetzter Suche, Anzeigen des nächsten Eintragssatzes für Suchergebnisse und Suchen mit einer `POST` Anforderung. Weitere Informationen zur Suche finden Sie unter [Übersicht über die FHIR-Suche.](overview-of-search.md)
   
## <a name="search-result-parameters"></a>Suchergebnisparameter

### <a name="_include"></a>_include

`_include` sucht ressourcenübergreifend nach den Ressourcen, die den angegebenen Parameter der Ressource enthalten. Beispielsweise können Sie ressourcenübergreifend suchen, `MedicationRequest` um nur diejenigen zu finden, die Informationen über die Patienten für einen bestimmten Patienten enthalten, was dem Parameter entspricht. `reference` `patient` Im folgenden Beispiel werden alle Und alle Patienten abgerufen, auf `MedicationRequests` die von verwiesen `MedicationRequests` wird:

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** und **_revinclude** sind auf 100 Elemente beschränkt.

### <a name="_revinclude"></a>_revinclude

`_revinclude` ermöglicht es Ihnen, die entgegengesetzte Richtung als zu `_include` durchsuchen. Sie können z. B. nach Patienten suchen und dann alle Vorkommen, die auf die Patienten verweisen, in umgekehrter Reihenfolge einschließen:

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` schränkt das Suchergebnis auf eine Teilmenge von Feldern ein, um die Antwortgröße zu reduzieren, indem unnötige Daten weggelassen werden. Der -Parameter akzeptiert eine durch Kommas getrennte Liste von Basiselementen:

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

In dieser Anforderung erhalten Sie ein Bündel von Patienten, aber jede Ressource enthält nur die Bezeichner und den aktiven Status des Patienten. Ressourcen in dieser zurückgegebenen Antwort enthalten den Wert , `meta.tag` `SUBSETTED` um anzugeben, dass es sich um einen unvollständigen Satz von Ergebnissen handelt.

## <a name="search-modifiers"></a>Suchmodifizierer

### <a name="not"></a>:not

`:not` ermöglicht es Ihnen, Ressourcen zu finden, bei denen ein Attribut nicht true ist. Sie könnten z. B. nach Patienten suchen, bei denen das Geschlecht nicht frauenspezifisch ist:

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

Als Rückgabewert erhalten Sie alle Patienteneinträge, bei denen das Geschlecht nicht frauenfrei ist, einschließlich leerer Werte (Einträge ohne Geschlecht angegeben). Dies unterscheidet sich von der Suche nach Patienten, bei denen das Geschlecht spezifisch ist, da dies die Einträge ohne ein bestimmtes Geschlecht nicht enthalten würde.

### <a name="missing"></a>:missing

`:missing` gibt alle Ressourcen zurück, die keinen Wert für das angegebene Element haben, wenn der Wert `true` ist, und gibt alle Ressourcen zurück, die das angegebene Element enthalten, wenn der Wert `false` ist. Bei einfachen Datentypelementen `:missing=true` stimmt für alle Ressourcen überein, in denen das Element mit Erweiterungen vorhanden ist, aber einen leeren Wert aufweist. Wenn Sie beispielsweise alle Ressourcen suchen möchten, für die Informationen zum Geburtsdatum fehlen, haben Sie folgende `Patient` Möglichkeiten:

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` wird für Parameter verwendet `string` und gibt Ergebnisse zurück, die genau mit dem Parameter übereinstimmen, z. B. bei groß-/kleinschreibung und Zeichen verkettet.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

Diese Anforderung gibt `Patient` Ressourcen zurück, deren Name genau mit identisch `Jon` ist. Wenn die Ressource Patienten mit Namen wie oder auflistet, `Jonathan` würde die Suche die Ressource ignorieren und `joN` überspringen, da sie nicht genau mit dem angegebenen Wert übereinstimmt.

### <a name="contains"></a>:contains
`:contains` wird für Parameter verwendet `string` und sucht nach Ressourcen mit teilweisen Übereinstimmungen des angegebenen Werts an einer beliebigen Stelle in der Zeichenfolge innerhalb des zu durchsuchenden Felds. `contains` die Groß-/Kleinschreibung nicht beachtet wird und die Zeichen verkettet werden kann. Zum Beispiel:

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

Diese Anforderung gibt Alle `Patient` Ressourcen mit `address` Feldern zurück, die Werte enthalten, die die Zeichenfolge "Umschließung" enthalten. Dies bedeutet, dass Adressen, die Werte wie "Lädter" oder "59 Uhr ST" enthalten, als Suchergebnisse zurückgegeben werden können.

## <a name="chained-search"></a>Verkettete Suche 

Um eine Reihe von Suchvorgängen auszuführen, die mehrere Verweisparameter abdecken, können Sie die Reihe von Verweisparametern "verketten", indem Sie sie nacheinander mithilfe eines Punkts an die Serveranforderung `.` anfügen. Wenn Sie beispielsweise alle Ressourcen mit einem Verweis auf eine Ressource anzeigen `DiagnosticReport` `subject` `Patient` möchten, die einen bestimmten `name` enthält:  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

Diese Anforderung würde alle `DiagnosticReport` Ressourcen mit einem Patiententhema namens "Sarah" zurückgeben. Der `.` Zeitraum, nach dem das Feld `Patient` die verkettete Suche für den Verweisparameter des `subject` Parameters ausführt.

Eine weitere häufige Verwendung einer regulären Suche (keine verkettete Suche) ist die Suche nach allen Treffern für einen bestimmten Patienten. `Patient`s verfügen häufig über ein oder mehrere `Encounter` s mit einem Betreff. So suchen Sie nach allen `Encounter` Ressourcen für ein mit dem `Patient` bereitgestellten `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

Mithilfe der verketteten Suche können Sie alle Ressourcen finden, die `Encounter` mit einem bestimmten `Patient` Informationsteil übereinstimmen, z. B. `birthdate` :

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

Dies würde nicht nur das Durchsuchen von `Encounter` Ressourcen nach einem einzelnen Patienten ermöglichen, sondern auch für alle Patienten mit dem angegebenen Wert für das Geburtsdatum. 

Darüber hinaus kann die verkettete Suche in einer Anforderung mehrmals ausgeführt werden, indem das Symbol verwendet wird, mit dem Sie in einer Anforderung nach `&` mehreren Bedingungen suchen können. In solchen Fällen sucht die verkettete Suche "unabhängig" nach jedem Parameter, anstatt nach Bedingungen zu suchen, die nur alle Bedingungen gleichzeitig erfüllen:

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

Dadurch werden alle `Patient` Ressourcen zurückgegeben, die "Sarah" als `generalPractitioner` und eine mit der Adresse mit der `generalPractitioner` Zustands-WA aufweisen. Anders ausgedrückt: Wenn für einen Patienten Sarah aus dem Bundesstaat NY und Bill aus der Wa des Bundesstaats beide als die des Patienten bezeichnet `generalPractitioner` werden, wird zurückgegeben.

Informationen zu Szenarien, in denen die Suche ein AND-Vorgang sein muss, der alle Bedingungen als Gruppe abdeckt, finden Sie im beispiel für die **zusammengesetzte Suche** weiter unten.

## <a name="reverse-chain-search"></a>Umgekehrte Kettensuche

Mit der Kettensuche können Sie basierend auf den Eigenschaften der Ressourcen, auf die sie verweisen, nach Ressourcen suchen. Wenn Sie die umgekehrte Kettensuche verwenden, können Sie dies umgekehrt tun. Sie können anhand der Eigenschaften von Ressourcen, die auf sie verweisen, mithilfe des Parameters nach Ressourcen `_has` suchen. Die Ressource verfügt beispielsweise über `Observation` einen Suchparameter, `patient` der auf eine Patient-Ressource verweist. So suchen Sie alle Patientenressourcen, auf die von mit einem bestimmten verwiesen `Observation` `code` wird:

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

Diese Anforderung gibt Patientenressourcen zurück, auf die mit dem Code verwiesen `Observation` `527` wird. 

Darüber hinaus kann die umgekehrte Kettensuche eine rekursive Struktur aufweisen. Wenn Sie beispielsweise nach allen Patienten suchen möchten, `Observation` bei denen die Beobachtung über ein Überwachungsereignis eines bestimmten Benutzers `janedoe` verfügt, können Sie Folgendes tun:

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> Im Azure API for FHIR und auf dem Open-Source-FHIR-Server, der von Cosmos unterstützt wird, ist die verkettete Suche und die umgekehrte verkettete Suche eine MVP-Implementierung. Um die verkettete Suche für Cosmos DB durchzuführen, führt die Implementierung den Suchausdruck durch und gibt Unterabfragen aus, um die übereinstimmenden Ressourcen zu beheben. Dies erfolgt für jede Ebene des Ausdrucks. Wenn eine Abfrage mehr als 100 Ergebnisse zurückgibt, wird ein Fehler ausgelöst.

## <a name="composite-search"></a>Zusammengesetzte Suche

Um nach Ressourcen zu suchen, die mehrere Bedingungen gleichzeitig erfüllen, verwenden Sie die zusammengesetzte Suche, die eine Sequenz einzelner Parameterwerte mit einem Symbol `$` verbindet. Das zurückgegebene Ergebnis wäre die Schnittmenge der Ressourcen, die mit allen Bedingungen übereinstimmen, die von den verknüpften Suchparametern angegeben werden. Solche Suchparameter werden als zusammengesetzte Suchparameter bezeichnet und definieren einen neuen Parameter, der die mehreren Parameter in einer geschachtelten Struktur kombiniert. Wenn Sie z. B. alle Ressourcen suchen möchten, die einen Wert von `DiagnosticReport` `Observation` 9,2 oder kleiner als 9,2 enthalten:

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

Diese Anforderung gibt die Komponente an, die den Code enthält. In `2823-3` diesem Fall wäre dies ein 1:0-Wert. Nach dem `$` Symbol wird der Bereich des Werts für die Komponente mit für `lt` "kleiner als oder gleich" und `9.2` für den Wertbereich angegeben. 

## <a name="search-the-next-entry-set"></a>Durchsuchen des nächsten Eintragssatzes

Die maximale Anzahl von Einträgen, die pro einzelner Suchabfrage zurückgegeben werden können, beträgt 1000. Möglicherweise verfügen Sie jedoch über mehr als 1.000 Einträge, die mit der Suchabfrage übereinstimmen, und Sie möchten möglicherweise den nächsten Satz von Einträgen nach den ersten 1000 zurückgegebenen Einträgen anzeigen. In diesem Fall verwenden Sie den Fortsetzungstokenwert `url` in wie im folgenden `searchset` `Bundle` Ergebnis:

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

Außerdem würden Sie eine GET-Anforderung für die bereitgestellte URL im Feld `relation: next` durchführen:

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

Dadurch wird der nächste Satz von Einträgen für Das Suchergebnis zurückgegeben. `searchset`ist der vollständige Satz von Suchergebniseinträgen, und das `url` Fortsetzungstoken ist der Link, der vom Server bereitgestellt wird, damit Sie die Einträge abrufen können, die nicht im ersten Satz angezeigt werden, da die Maximale Anzahl von Einträgen, die für eine Suchabfrage zurückgegeben werden, eingeschränkt ist.

## <a name="search-using-post"></a>Suchen mit POST

Alle oben erwähnten Suchbeispiele haben `GET` Anforderungen verwendet. Sie können suchvorgänge auch mithilfe von `POST` Anforderungen `_search` durchführen:

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

Diese Anforderung gibt alle `Patient` Ressourcen mit dem Wert `id` 45 zurück. Wie bei GET-Anforderungen bestimmt der Server, welche der Ressourcen die Bedingungen erfüllt, und gibt eine Bündelressource in der HTTP-Antwort zurück.

Ein weiteres Beispiel für die Suche mit POST, bei der die Abfrageparameter als Formulartext übermittelt werden, ist:

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über die FHIR-Suche](overview-of-search.md)
---
title: Azure API Management-Validierungsrichtlinie für GraphQL-Anforderungen | Microsoft-Dokumentation
description: Erfahren Sie mehr über eine neue Richtlinie, die Sie in Azure API Management verwenden können, um GraphQL-Anforderungen zu überprüfen und zu autorisieren.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: 02225e0a07ef6567b8b190d9d31a957a69adbc4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095311"
---
# <a name="api-management-policy-to-validate-and-authorize-graphql-requests-preview"></a>API Management Richtlinie zum Überprüfen und Autorisieren von GraphQL-Anforderungen (Vorschau)

Dieser Artikel enthält eine Referenz für eine neue API Management Richtlinie zum Überprüfen und Autorisieren von Anforderungen an eine [GraphQL-API](graphql-api.md), die in API Management importiert wurde.

Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](./api-management-policies.md).

## <a name="validation-policy"></a>Validierungsrichtlinie

| Richtlinie | BESCHREIBUNG |
| ------ | ----------- |
| [Überprüfen der GraphQL-Anforderung](#validate-graphql-request) | Überprüft und autorisiert eine Anforderung an eine GraphQL-API. |


## <a name="validate-graphql-request"></a>Überprüfen der GraphQL-Anforderung

Die Richtlinie `validate-graphql-request` überprüft die GraphQL-Anforderung und autorisiert den Zugriff auf bestimmte Abfragepfade. Eine ungültige Abfrage ist ein „Anforderungsfehler“. Die Autorisierung erfolgt nur für gültige Anforderungen. 

**Berechtigungen**  
Da GraphQL-Abfragen ein vereinfachtes Schema verwenden:
* Berechtigungen können auf jeden Blattknoten eines Ausgabetyps angewendet werden: 
    * Mutation, Abfrage oder Abonnement
    * Einzelnes Feld in einer Typdeklaration. 
* Berechtigungen werden möglicherweise nicht angewendet auf: 
    * Eingabetypen
    * Fragments
    * Unions
    * Schnittstellen
    * Das Schemaelement   

**Berechtigte Autorisierungselemente**  
Sie können mehrere Autorisierungselemente verwenden. Der spezifischste Pfad wird verwendet, um die entsprechende Autorisierungsregel für jeden Blattknoten in der Abfrage auszuwählen. 
* Jede Autorisierung kann optional eine andere Aktion bereitstellen.
* `if`-Klauseln ermöglichen es dem Administrator, bedingte Aktionen anzugeben. 

**Introspektions-System**   
Die Richtlinie für path= `/__*` ist das [Introspektions-System](https://graphql.org/learn/introspection/). Sie können damit Introspektionsanforderungen (`__schema`, `__type` usw.) ablehnen.   

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-graphql-request error-variable-name="variable name" max-size="size in bytes" max-depth="query depth">
    <authorize-path="query path, for example: /Query/list Users or /__*" action="allow|remove|reject" />
        <if condition="policy expression" action="allow|remove|reject" />
</validate-graphql-request>
```

### <a name="example"></a>Beispiel

Im folgenden Beispiel überprüfen wir eine GraphQL-Abfrage und weisen sie zurück:
* Anforderungen, die größer als 100 KB oder mit einer Abfragetiefe größer als 4 sind. 
* Zugriff auf das Introspektions-System und die `list Users` Abfrage. 

```xml
<validate-graphql-request error-variable-name="name" max-size="102400" max-depth="4"> 
    <authorize path="/" action="allow" /> 
    <authorize path="/__*" action="reject" /> 
    <authorize path="/Query/list Users" action="reject" /> 
</validate-graphql-request> 
```

### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-graphql-request` | Stammelement                                                                                                                               | Ja      |
| `authorize` | Fügen Sie mindestens eines dieser Elemente hinzu, um die Autorisierung auf Feldebene mit Fehlern auf Anforderungs- und Feldebene zu bieten.   | Ja |
| `if` | Fügen Sie ein oder mehrere dieser Elemente für bedingte Änderungen zur Aktion für eine Autorisierung auf Feldebene hinzu. | Nein |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `error-variable-name` | Name der Variable in `context.Variables`, in der Validierungsfehler protokolliert werden.  |   Nein    | –   |
| `max-size` | Maximale Größe der Anforderungsnutzlast in Bytes. Maximal zulässiger Wert: 102.400 Byte (100 KB). (Wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/), wenn Sie diesen Grenzwert erhöhen möchten.) | Ja       | –   |
| `max-depth` | Eine ganze Zahl. Maximale Abfragetiefe. | Nein | 6 |
| `path` | Abfragepfad, für den die Autorisierungsvalidierung ausgeführt werden soll. | Ja | – |
| `action` | [Aktion,](#request-actions) die für das übereinstimmende Feld ausgeführt werden soll. Kann geändert werden, wenn eine übereinstimmende Bedingung angegeben wird. |  Ja     | –   |
| `condition` | Boolescher Wert, der bestimmt, ob der [Richtlinienausdruck](api-management-policy-expressions.md) übereinstimmt. Die erste übereinstimmende Bedingung wird verwendet. | Nein | – |

### <a name="request-actions"></a>Anforderungsaktionen

Verfügbare Lösungen sind in der folgenden Tabelle beschrieben.

|Aktion |BESCHREIBUNG  |
|---------|---------|
|`reject`     | Es tritt ein Anforderungsfehler auf, und die Anforderung wird nicht an das Back-End gesendet.     |
|`remove`     | Ein Feldfehler tritt auf, und das Feld wird aus der Anforderung entfernt.         |
|`allow`     | Das Feld wird an das Back-End übergeben.        |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound

-   **Richtlinienbereiche:** alle Bereiche

## <a name="error-handling"></a>Fehlerbehandlung

Ein Fehler bei der Überprüfung anhand des GraphQL-Schemas oder eines Fehlers für die Größe oder Tiefe der Anforderung ist ein Anforderungsfehler und führt dazu, dass bei der Anforderung ein Fehlerblock (aber kein Datenblock) auftritt. 

Ähnlich wie bei der Eigenschaft [`Context.LastError`](api-management-error-handling-policies.md#lasterror) werden alle GraphQL-Validierungsfehler automatisch in der Variablen `GraphQLErrors` weitergegeben. Wenn die Fehler separat weitergegeben werden müssen, können Sie einen Fehlervariablennamen angeben. Fehler werden in die Variable `error` und die Variable `GraphQLErrors` gepusht. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Richtlinien finden Sie hier:

-   [Richtlinien in Azure API Management](api-management-howto-policies.md)
-   [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
-   Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
-   [API Management-Richtlinienbeispiele](./policy-reference.md)
-   [Fehlerbehandlung](./api-management-error-handling-policies.md)

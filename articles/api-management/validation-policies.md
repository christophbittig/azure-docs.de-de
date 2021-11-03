---
title: Azure API Management-Validierungsrichtlinien | Microsoft-Dokumentation
description: Lernen Sie die Richtlinien kennen, die Sie in Azure API Management zum Überprüfen von Anforderungen und Antworten verwenden können.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: ee6eb475dc94407a774f4b22bd3afd676cbe83da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065587"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>API Management-Richtlinien zum Überprüfen von Anforderungen und Antworten

Dieser Artikel ist eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](./api-management-policies.md).

Verwenden Sie Validierungsrichtlinien zum Überprüfen von API-Anforderungen und -Antworten anhand eines OpenAPI-Schemas und zum Schützen vor Sicherheitsrisiken wie der Einschleusung von Headern oder Payloads. Validierungsrichtlinien sind keine Ersatz für eine Web Application Firewall, bieten aber Flexibilität zur Reaktion auf eine andere Art von Bedrohungen, die durch auf statischen, vordefinierten Regeln basierende Sicherheitsprodukte nicht abgedeckt werden.

## <a name="validation-policies"></a>Überprüfungsrichtlinien

- [Validieren von Inhalten](#validate-content): Überprüft die Größe oder das JSON-Schema eines Anforderungs- oder Antworttexts anhand des API-Schemas. 
- [Validieren von Parametern](#validate-parameters): Überprüft die Parameter für Anforderung, Abfrage oder Pfad anhand des API-Schemas.
- [Validieren von Headern](#validate-headers): Überprüft die Anforderungsheader anhand des API-Schemas.
- [Validieren von Statuscodes](#validate-status-code): Überprüft die HTTP-Statuscodes in Antworten anhand des API-Schemas.

> [!NOTE]
> Die maximale Größe des API-Schemas, das von einer Validierungsrichtlinie verwendet werden kann, beträgt 4 MB. Wenn das Schema diesen Grenzwert überschreitet, geben Validierungsrichtlinien zur Laufzeit Fehler zurück. Zum Erhöhen des Grenzwerts wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Aktionen

Jede Validierungsrichtlinie enthält ein Attribut, das eine Aktion angibt, die von API Management beim Überprüfen einer Entität in einer API-Anforderung oder -Antwort anhand des API-Schemas ausgeführt wird. Eine Aktion kann für Elemente angegeben werden, die im API-Schema repräsentiert werden, und je nach Richtlinie auch für Elemente, die im API-Schema nicht repräsentiert werden. Eine Aktion, die in einem untergeordneten Element einer Richtlinie angegeben ist, setzt eine für das übergeordnete Element angegebene Aktion außer Kraft.

Verfügbare Aktionen:

| Aktion         | BESCHREIBUNG          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `ignore` | Die Überprüfung wird übersprungen. |
| `prevent` | Die Verarbeitung von Anforderung oder Antwort wird blockiert, ein ausführlicher [Validierungsfehler](#validation-errors) wird protokolliert, und ein Fehler wird zurückgegeben. Die Verarbeitung wird unterbrochen, wenn die ersten Fehler erkannt werden. 
| `detect` | [Validierungsfehler](#validation-errors) werden protokolliert, ohne dass die Verarbeitung von Anforderung oder Antwort unterbrochen wird. |

## <a name="logs"></a>Protokolle

Details zu den Validierungsfehlern während der Richtlinienausführung werden in die Variable in `context.Variables` protokolliert, die im Attribut `errors-variable-name` im Stammelement der Richtlinie angegeben ist. Ein Validierungsfehler blockiert die weitere Verarbeitung von Anforderung oder Antwort und wird auch an die Eigenschaft `prevent` weitergegeben, sofern dies in einer `context.LastError`-Aktion konfiguriert ist. 

Verwenden Sie zum Untersuchen von Fehlern eine Richtlinie zur [Ablaufverfolgung](api-management-advanced-policies.md#Trace), um die Fehler aus Kontextvariablen in [Application Insights](api-management-howto-app-insights.md) zu protokollieren.

## <a name="performance-implications"></a>Folgen für die Leistung

Das Hinzufügen von Validierungsrichtlinien kann den API-Durchsatz beeinträchtigen. Es gelten die folgenden allgemeinen Prinzipien:
* Je größer das API-Schema ist, desto niedriger ist der Durchsatz. 
* Je größer die Payload in einer Anforderung oder Antwort ist, desto niedriger ist der Durchsatz. 
* Die Größe des API-Schemas hat größere Auswirkungen auf die Leistung als die Größe der Payload. 
* Die Validierung anhand eines API-Schemas mit einer Größe von mehreren Megabyte kann unter bestimmten Bedingungen Anforderungs- oder Antworttimeouts verursachen. Dieser Effekt ist auf den Dienstebenen **Verbrauch** und **Entwickler** deutlicher ausgeprägt. 

Es empfiehlt sich, Auslastungstests mit den erwarteten Produktionsworkloads durchzuführen, um die Auswirkungen von Validierungsrichtlinien auf den API-Durchsatz zu bewerten.

## <a name="validate-content"></a>Inhalt prüfen

Die Richtlinie `validate-content` überprüft die Größe oder das JSON-Schema eines Anforderungs- oder Antworttexts anhand des API-Schemas. Andere Formate als JSON werden nicht unterstützt.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die JSON-Payload in Anforderungen und Antworten im Erkennungsmodus überprüft. Meldungen mit Payloads von mehr als 100 KB werden blockiert. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-content` | Stammelement                                                                                                                               | Ja      |
| `content` | Fügen Sie mindestens eins dieser Elemente hinzu, um den Inhaltstyp in der Anforderung oder Antwort zu überprüfen, und führen Sie die angegebene Aktion aus.  | Nein |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `unspecified-content-type-action` | [Aktion](#actions), die für Anforderungen oder Antworten mit einem Inhaltstyp ausgeführt wird, der im API-Schema nicht angegeben ist. |  Ja     | –   |
| `max-size` | Maximale Länge des Texts der Anforderung oder Antwort in Bytes, überprüft anhand des `Content-Length`Headers. Wenn der Anforderungs- oder Antworttext komprimiert ist, stellt dieser Wert die entkomprimierte Länge dar. Maximal zulässiger Wert: 102.400 Byte (100 KB). (Wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/), wenn Sie diesen Grenzwert erhöhen möchten.) | Ja       | –   |
| `size-exceeded-action` | [Aktion](#actions), die für Anforderungen oder Antworten ausgeführt wird, deren Text die in `max-size` angegebene Größe überschreitet. |  Ja     | –   |
| `errors-variable-name` | Name der Variable in `context.Variables`, in der Validierungsfehler protokolliert werden.  |   Nein    | –   |
| `type` | Inhaltstyp, für den die Textüberprüfung ausgeführt wird, überprüft anhand des `Content-Type`-Headers. Bei diesem Wert wird die Groß-/Kleinschreibung nicht beachtet. Wenn der Wert leer ist, gilt dies für jeden im API-Schema angegebenen Inhaltstyp. |   Nein    |  –  |
| `validate-as` | Validierungs-Engine zur Überprüfung des Texts einer Anforderung oder Antwort mit dem passenden Inhaltstyp. Zurzeit ist „json“ der einzige unterstützte Wert.   |  Ja     |  –  |
| `action` | [Aktion](#actions), die für Anforderungen oder Antworten ausgeführt wird, deren Text nicht dem angegebenen Inhaltstyp entspricht.  |  Ja      | –   |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte:** inbound, outbound, on-error

-   **Richtlinienbereiche:** alle Bereiche

## <a name="validate-parameters"></a>Validieren von Parametern

Die `validate-parameters`-Richtlinie überprüft die Parameter für Header, Abfrage oder Pfad in Anforderungen anhand des API-Schemas.

> [!IMPORTANT]
> Wenn Sie eine API mithilfe einer Verwaltungs-API-Version vor `2021-01-01-preview` importiert haben, funktioniert die `validate-parameters`-Richtlinie möglicherweise nicht. Möglicherweise müssen Sie Ihre [API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) mithilfe der Management-API-Version `2021-01-01-preview` oder höher erneut importieren.


### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Beispiel

In diesem Beispiel werden alle Abfrage- und Pfadparameter im Präventionsmodus überprüft, die Header im Erkennungsmodus. Die Validierung wird für verschiedene Headerparameter außer Kraft gesetzt:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-parameters` | Stammelement Gibt die Standardvalidierungsaktionen für alle Parameter in Anforderungen an.                                                                                                                              | Ja      |
| `headers` | Fügen Sie dieses Element hinzu, um die Standardvalidierungsaktionen für Headerparameter in Anforderungen außer Kraft zu setzen.   | Nein |
| `query` | Fügen Sie dieses Element hinzu, um die Standardvalidierungsaktionen für Abfrageparameter in Anforderungen außer Kraft zu setzen.  | Nein |
| `path` | Fügen Sie dieses Element hinzu, um die Standardvalidierungsaktionen für URL-Pfadparameter in Anforderungen außer Kraft zu setzen.  | Nein |
| `parameter` | Fügen Sie mindestens ein Element für benannte Parameter hinzu, um eine Konfiguration der Validierungsaktionen auf höherer Ebene außer Kraft zu setzen. | Nein |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `specified-parameter-action` | [Aktion](#actions), die für im API-Schema angegebene Anforderungsparameter ausgeführt werden soll. <br/><br/> Wenn dieser Wert in einem `headers`-, `query`- oder `path`-Element bereitgestellt wird, setzt er den Wert von `specified-parameter-action` im `validate-parameters`-Element außer Kraft.  |  Ja     | –   |
| `unspecified-parameter-action` | [Aktion](#actions), die für im API-Schema nicht angegebene Anforderungsparameter ausgeführt werden soll. <br/><br/>Wenn dieser Wert in einem `headers`- oder `query`-Element bereitgestellt wird, setzt er den Wert von `unspecified-parameter-action` im `validate-parameters`-Element außer Kraft. |  Ja     | –   |
| `errors-variable-name` | Name der Variable in `context.Variables`, in der Validierungsfehler protokolliert werden.  |   Nein    | –   |
| `name` | Name des Parameters, für den die Validierungsaktion außer Kraft gesetzt werden soll. Bei diesem Wert wird die Groß-/Kleinschreibung nicht beachtet.  | Ja | – |
| `action` | [Aktion](#actions), die für den Parameter mit dem passenden Namen ausgeführt werden soll. Wenn der Parameter im API-Schema angegeben ist, setzt dieser Wert die Konfiguration von `specified-parameter-action` auf höherer Ebene außer Kraft. Wenn der Parameter im API-Schema nicht angegeben ist, setzt dieser Wert die Konfiguration von `unspecified-parameter-action` auf höherer Ebene außer Kraft.| Ja | – | 

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: inbound

-   **Richtlinienbereiche:** alle Bereiche

## <a name="validate-headers"></a>Validieren von Headern

Die `validate-headers`-Richtlinie überprüft die Anforderungsheader anhand des API-Schemas.

> [!IMPORTANT]
> Wenn Sie eine API mithilfe einer Verwaltungs-API-Version vor `2021-01-01-preview` importiert haben, funktioniert die `validate-headers`-Richtlinie möglicherweise nicht. Möglicherweise müssen Sie Ihre API mithilfe der Verwaltungs-API-Version `2021-01-01-preview` oder höher erneut importieren.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Beispiel

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-headers` | Stammelement Gibt Standardvalidierungsaktionen für alle Header in Antworten an.                                                                                                                              | Ja      |
| `header` | Fügen Sie mindestens ein Element für benannte Header hinzu, um die Standardvalidierungsaktionen für Header in Antworten außer Kraft zu setzen. | Nein |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `specified-header-action` | [Aktion](#actions), die für im API-Schema angegebene Antwortheader ausgeführt werden soll.  |  Ja     | –   |
| `unspecified-header-action` | [Aktion](#actions), die für im API-Schema nicht angegebene Antwortheader ausgeführt werden soll.  |  Ja     | –   |
| `errors-variable-name` | Name der Variable in `context.Variables`, in der Validierungsfehler protokolliert werden.  |   Nein    | –   |
| `name` | Name des Headers, für den die Validierungsaktion außer Kraft gesetzt werden soll. Bei diesem Wert wird die Groß-/Kleinschreibung nicht beachtet. | Ja | – |
| `action` | [Aktion](#actions), die für Header mit dem passenden Namen ausgeführt werden soll. Wenn der Header im API-Schema angegeben ist, setzt dieser Wert den Wert von `specified-header-action` im `validate-headers`-Element außer Kraft. Andernfalls setzt er den Wert von `unspecified-header-action` im validate-headers-Element außer Kraft. | Ja | – | 

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: outbound, on-error

-   **Richtlinienbereiche:** alle Bereiche

## <a name="validate-status-code"></a>Validieren von Statuscodes

Die `validate-status-code`-Richtlinie überprüft die HTTP-Statuscodes in Antworten anhand des API-Schemas. Mit dieser Richtlinie kann die Preisgabe von Details zu Back-End-Fehlern verhindert werden, die Stapelüberwachungen enthalten können. 

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Beispiel

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elemente

| Name         | BESCHREIBUNG                                                                                                                                   | Erforderlich |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-status-code` | Stammelement                                                                                                | Ja      |
| `status-code` | Fügen Sie mindestens ein Element für HTTP-Statuscodes hinzu, um die Standardvalidierungsaktion für Statuscodes in Antworten außer Kraft zu setzen. | Nein |

### <a name="attributes"></a>Attributes

| Name                       | BESCHREIBUNG                                                                                                                                                            | Erforderlich | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `unspecified-status-code-action` | [Aktion](#actions), die für HTTP-Statuscodes in Antworten ausgeführt werden soll, die im API-Schema nicht angegeben sind.  |  Ja     | –   |
| `errors-variable-name` | Name der Variable in `context.Variables`, in der Validierungsfehler protokolliert werden.  |   Nein    | –   |
| `code` | Der HTTP-Statuscode, für den die Validierungsaktion außer Kraft gesetzt werden soll. | Ja | – |
| `action` | [Aktion](#actions), die für den entsprechenden Statuscode ausgeführt werden soll, der im API-Schema nicht angegeben ist. Wenn der Statuscode im API-Schema angegeben ist, findet keine Außerkraftsetzung statt. | Ja | – | 

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

-   **Richtlinienabschnitte**: outbound, on-error

-   **Richtlinienbereiche:** alle Bereiche


## <a name="validation-errors"></a>Überprüfungsfehler
In der folgenden Tabelle werden alle möglichen Fehler der Validierungsrichtlinien aufgeführt. 

* **Details**: Können zum Untersuchen von Fehlern verwendet werden. Nicht für die öffentliche Freigabe vorgesehen.
* **Öffentliche Antwort**: An den Client zurückgegebener Fehler. Gibt keine Implementierungsdetails preis.

Wenn eine Validierungsrichtlinie die Aktion `prevent` angibt und einen Fehler erzeugt, enthält die Antwort von API Management einen HTTP-Statuscode 400, wenn die Richtlinie im eingehenden Abschnitt angewendet wird, und 502, wenn die Richtlinie im ausgehenden Abschnitt angewendet wird.


| **Name**   | **Typ**                                                        | **Überprüfungsregel** | **Details**                                                                                                                                       | **Öffentliche Antwort**                                                                                                                       | **Aktion**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | Der Anforderungstext ist {size} Byte lang und überschreitet das konfigurierte Limit von {maxSize} Byte.                                                       | Der Anforderungstext ist {size} Byte lang und überschreitet das Limit von {maxSize} Byte.                                                          | Erkennen/Verhindern |
||ResponseBody                                                    | SizeLimit           | Der Antworttext ist {size} Byte lang und überschreitet das konfigurierte Limit von {maxSize} Byte.                                                      | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {messageContentType}                 | RequestBody                                                     | Nicht angegeben.         | Der nicht angegebene Inhaltstyp „{messageContentType}“ ist nicht zulässig.                                                                                     | Der nicht angegebene Inhaltstyp „{messageContentType}“ ist nicht zulässig.                                                                             | Erkennen/Verhindern |
| {messageContentType}                 | ResponseBody                                                    | Nicht angegeben.         | Der nicht angegebene Inhaltstyp „{messageContentType}“ ist nicht zulässig.                                                                                     | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| | ApiSchema                                                       |                     | Das API-Schema ist nicht vorhanden oder konnte nicht aufgelöst werden.                                                                                          | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
|                                      | ApiSchema                                                       |                     | Das API-Schema gibt keine Definitionen an.                                                                                                        | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | Das API-Schema enthält die Definition „{definitionName}“ nicht, die dem Inhaltstyp „{messageContentType}“ zugeordnet ist.                        | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | Der Text der Anforderung entspricht nicht der Definition „{definitionName}“, die dem Inhaltstyp „{messageContentType}“ zugeordnet ist.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}                  | Der Text der Anforderung entspricht nicht der Definition „{definitionName}“, die dem Inhaltstyp „{messageContentType}“ zugeordnet ist.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}            | Erkennen/Verhindern |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | Der Text der Antwort entspricht nicht der Definition „{definitionName}“, die dem Inhaltstyp „{messageContentType}“ zugeordnet ist.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}                                       | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
|                                      | RequestBody                                                     | ValidationException | Der Text der Anforderung kann für den Inhaltstyp „{messageContentType}“ nicht validiert werden.<br/><br/>{exception details}                                                                | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
|                                      | ResponseBody                                                    | ValidationException | Der Text der Antwort kann für den Inhaltstyp „{messageContentType}“ nicht validiert werden.<br/><br/>{exception details}                                                                | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| **validate-parameters/validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | QueryParameter / PathParameter / RequestHeader                  | Nicht angegeben.         | Ein nicht angegebener {paramName} für {path parameter / query parameter / header} ist nicht zulässig.                                                               | Ein nicht angegebener {paramName} für {path parameter / query parameter / header} ist nicht zulässig.                                                       | Erkennen/Verhindern |
| {headerName}                         | ResponseHeader                                                  | Nicht angegeben.         | Ein nicht angegebener Header „{headerName}“ ist nicht zulässig.                                                                                                   | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
|                                      |ApiSchema                                                       |                     | Das API-Schema ist nicht vorhanden oder konnte nicht aufgelöst werden.                                                                                            | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
|                                       | ApiSchema                                                       |                     | Das API-Schema gibt keine Definitionen an.                                                                                                          | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Das API-Schema enthält die Definition „{definitionName}“ nicht, die dem {paramName} für {query parameter / path parameter / header} zugeordnet ist.  | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Die Anforderung darf nicht mehrere Werte für den {paramName} für {query parameter / path parameter / header} enthalten.                                           | Die Anforderung darf nicht mehrere Werte für den {paramName} für {query parameter / path parameter / header} enthalten.                                   | Erkennen/Verhindern |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Die Antwort darf nicht mehrere Werte für den Header „{headerName}“ enthalten.                                                                              | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Der Wert des {paramName} für {query parameter / path parameter / header} entspricht der Definition nicht.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}                                          | Der Wert des {paramName} für {query parameter / path parameter / header} entspricht der Definition nicht.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}                              | Erkennen/Verhindern |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Der Wert des Headers „{headerName}“ entspricht der Definition nicht.<br/><br/>{valError.Message} Zeile: {valError.LineNumber}, Position: {valError.LinePosition}                                                                              | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Der Wert des {paramName} für {query parameter / path parameter / header} kann nicht entsprechend der Definition analysiert werden. <br/><br/>{ex.Message}                                | Der Wert des {paramName} für {query parameter / path parameter / header} konnte nicht entsprechend der Definition analysiert werden. <br/><br/>{ex.Message}                      | Erkennen/Verhindern |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Der Wert des Headers „{headerName}“ konnte nicht entsprechend der Definition analysiert werden.                                                                  | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | Der {paramName} für {Query parameter / Path parameter / Header} kann nicht validiert werden.<br/><br/>{exception details}                                                                      | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | Der Header „{headerName}“ kann nicht validiert werden.<br/><br/>{exception details}                                                                                                          | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | Nicht angegeben.         | Der Antwortstatuscode „{status-code}“ ist nicht zulässig.                                                                                                | Die Anforderung konnte aufgrund eines internen Fehlers nicht verarbeitet werden. Kontaktieren Sie den API-Besitzer.                                                       | Erkennen/Verhindern |


In der folgenden Tabelle sind alle möglichen Werte für den Grund eines Validierungsfehlers sowie mögliche Werte für die Meldung aufgeführt:

|  **`Reason`**         |    **Meldung** |
|---|---|  
| Ungültige Anforderung       |     {Details} für die Kontextvariable, {Public response} für den Client|
| Antwort nicht zulässig  | {Details} für die Kontextvariable, {Public response} für den Client |






## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Richtlinien finden Sie hier:

-   [Richtlinien in Azure API Management](api-management-howto-policies.md)
-   [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
-   Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
-   [API Management-Richtlinienbeispiele](./policy-reference.md)
-   [Fehlerbehandlung](./api-management-error-handling-policies.md)

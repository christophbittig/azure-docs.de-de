---
title: Benutzerdefinierte Funktionen im IoT-Connector – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Funktionen mit IoT Connector Gerätezuordnungsvorlagen verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 2bb0d3c17de870017733291bee4680f740afc1ad
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933836"
---
# <a name="how-to-use-custom-functions"></a>Verwenden von benutzerdefinierten Funktionen

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie Kundenfunktionen des IoT-Connectors verwenden.

Viele Funktionen sind verfügbar, wenn **JmesPath als** Ausdruckssprache verwendet wird. Neben den Funktionen, die als Teil der JmesPath-Spezifikation verfügbar sind, können auch viele benutzerdefinierte Funktionen verwendet werden. In diesem Artikel werden ioT-connectorspezifische benutzerdefinierte Funktionen für die Verwendung mit der Vorlage Gerätezuordnungen während des Normalisierungsprozesses beschrieben.

> [!TIP]
> Weitere Informationen zu JmesPath-Funktionen finden [](https://jmespath.org/specification.html#built-in-functions)Sie in der JmesPath-Spezifikation.

## <a name="function-signature"></a>Funktionssignatur

Jede Funktion verfügt über eine Signatur, die der JmesPath-Spezifikation entspricht. Diese Signatur kann wie hier dargestellt werden:

```jmespath
return_type function_name(type $argname)
```

Die Signatur gibt die gültigen Typen für die Argumente an. Wenn ein ungültiger Typ für ein Argument übergeben wird, tritt ein Fehler auf.

> [!NOTE]
> Wenn mathematische Funktionen durchgeführt werden, muss das **Endergebnis** innerhalb eines C#-Long-Werts [passen.](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types) Wenn das Endergebnis in nicht in einen C#-Long-Wert passen kann, tritt ein mathematischer Fehler auf.

## <a name="exception-handling"></a>Ausnahmebehandlung

Ausnahmen können an verschiedenen Punkten innerhalb des Lebenszyklus der Ereignisverarbeitung auftreten. Hier sind die verschiedenen Punkte, an denen sie auftreten können:

|Aktion|When|Ausnahmen, die während der Analyse von Vorlagen auftreten können|Ergebnis|
|------|----|-------------------------------------------------|-------|
|**Analyse von Vorlagen**|Jedes Mal, wenn ein neuer Nachrichtenbatch empfangen wird, wird die Gerätezuordnungsvorlage geladen und analysiert.|Fehler beim Analysieren der Vorlage.|Das System versucht, die neueste Gerätezuordnungsvorlage neu zu laden und zu analysieren, bis die Analyse erfolgreich ist. Es werden keine neuen Nachrichten verarbeitet, bis die Analyse erfolgreich war.|
|**Analyse von Vorlagen**|Jedes Mal, wenn ein neuer Nachrichtenbatch empfangen wird, wird die Gerätezuordnungsvorlage geladen und analysiert.|Fehler beim Analysieren von Ausdrücken.|Das System versucht, die neueste Gerätezuordnungsvorlage neu zu laden und zu analysieren, bis die Analyse erfolgreich ist. Es werden keine neuen Nachrichten verarbeitet, bis die Analyse erfolgreich war.|
|**Funktionsausführung**|Jedes Mal, wenn eine Funktion für Daten innerhalb einer Nachricht ausgeführt wird.|Eingabedaten sind nicht mit denen der Funktionssignatur übereinstimmen.|Das System beendet die Verarbeitung dieser Nachricht. Die Nachricht wird nicht wiederholt.|
|**Funktionsausführung**|Jedes Mal, wenn eine Funktion für Daten innerhalb einer Nachricht ausgeführt wird.|Alle anderen Ausnahmen, die in der Beschreibung der Funktion aufgeführt sind.|Das System beendet die Verarbeitung dieser Nachricht. Die Nachricht wird nicht wiederholt.|

## <a name="mathematical-functions"></a>Mathematische Funktionen

### <a name="add"></a>add

```jmespath
number add(number $left, number $right)
```

Gibt das Ergebnis des Hinzufügens des linken Arguments rechts zurück.

Beispiele:

| Vorgabe                       | expression       | Ergebnis |
|-----------------------------|------------------|--------|
| –                         | add(10, 10)      | 20     |
| {"left": 40, "right": 50}   | add(left, right) | 90     |
| {"left": 0, "right": 50}    | add(left, right) | 50     |

### <a name="divide"></a>Teilen

```jmespath
number divide(number $left, number $right)
```

Gibt das Ergebnis der Division des linken Arguments durch das rechte Argument zurück.

Beispiele:

| Vorgabe                       | expression          | Ergebnis                           |
|-----------------------------|---------------------|----------------------------------|
| –                         | divide(10, 10)      | 1                                |
| {"left": 40, "right": 50}   | divide(left, right) | 0.8                              |
| {"left": 0, "right": 50}    | divide(left, right) | 0                                |
| {"left": 50, "right": 0}    | divide(left, right) | mathematischer Fehler: Division durch 0 (null) |

### <a name="multiply"></a>Multiplizieren

```jmespath
number multiply(number $left, number $right)
```

Gibt das Ergebnis der Multiplikation des linken Arguments mit der rechten zurück.

Beispiele:

| Vorgabe                       | expression            | Ergebnis |
|-----------------------------|-----------------------|--------|
| –                         | multiply(10, 10)      | 100    |
| {"left": 40, "right": 50}   | multiply(left, right) | 2000   |
| {"left": 0, "right": 50}    | multiply(left, right) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

Gibt das Ergebnis der Aushebung des linken Arguments auf die Rechte zurück.

Beispiele:

| Vorgabe                         | expression       | Ergebnis                     |
|-------------------------------|------------------|----------------------------|
| –                           | pow(10, 10)      | 10000000000                |
| {"left": 40, "right": 50}     | pow(left, right) | mathematischer Fehler: Überlauf |
| {"left": 0, "right": 50}      | pow(left, right) | 0                          |
| {"left": 100, "right": 0.5}   | pow(left, right) | 10                         |

### <a name="subtract"></a>Subtrahieren

```jmespath
number subtract(number $left, number $right)
```

Gibt das Ergebnis der Subtraktion des rechten Arguments von links zurück.

Beispiele:

| Vorgabe                       | expression            | Ergebnis |
|-----------------------------|-----------------------|--------|
| –                         | subtract(10, 10)      | 0      |
| {"left": 40, "right": 50}   | subtract(left, right) | –10    |
| {"left": 0, "right": 50}    | subtract(left, right) | -50    |

## <a name="string-functions"></a>Zeichenfolgenfunktionen

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

Erzeugt eine neue Zeichenfolge, indem der Wert von *toInsert* in die *ursprüngliche* Zeichenfolge eingefügt wird. Die Zeichenfolge wird an position *pos* innerhalb der *ursprünglichen* Zeichenfolge eingefügt.

Wenn das Positionsargument nullbasiert ist, bezieht sich die Position von 0 auf das erste Zeichen innerhalb der Zeichenfolge. 

Wenn das angegebene Positionsargument außerhalb des Bereichs der Länge des *ursprünglichen* liegt, tritt ein Fehler auf.

Beispiele:

| Vorgabe                                                     | expression                                         | Ergebnis              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {"original": "mple", "toInsert": "sa", "pos": 0}          | insertString(original, toInsert, pos)              | "Sample"            |
| {"original": "suess", "toInsert": "cc", "pos": 2}         | insertString(original, toInsert, pos)              | "Erfolg"           |
| {"original": "myString", "toInsert": ")*", "pos": 8}      | insertString(original, toInsert, pos)              | "myString!""        |
| {"original": "myString", "toInsert": ")*"}                | insertString(original, toInsert, length(original)) | "myString!""        |
| {"original": "myString", "toInsert": ")*", "pos": 100}    | insertString(original, toInsert, pos)              | error: außerhalb des Bereichs |
| {"original": "myString", "toInsert": ")*", "pos": -1}     | insertString(original, toInsert, pos)              | error: außerhalb des Bereichs |

## <a name="date-functions"></a>Datumsfunktionen

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

Erzeugt einen [ISO 8061-konformen](https://en.wikipedia.org/wiki/ISO_8601) Zeitstempel aus dem angegebenen Unix-Zeitstempel. Der Zeitstempel wird als Anzahl von Sekunden seit der Epoche (1. Januar 1970) dargestellt.

Beispiele:

| Vorgabe                 | expression              | Ergebnis                  |
|-----------------------|-------------------------|-------------------------|
| {"unix": 1625677200} | fromUnixTimestamp(unix)  | "2021-07-07T17:00:00+0" |
| {"unix": 0}          | fromUnixTimestamp(unix)  | "1970-01-01T00:00:00+0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

Erzeugt einen [ISO 8061-konformen](https://en.wikipedia.org/wiki/ISO_8601) Zeitstempel aus dem angegebenen Unix-Zeitstempel. Der Zeitstempel wird als Anzahl von Millisekunden seit der Epoche (1. Januar 1970) dargestellt.

Beispiele:

| Vorgabe                    | expression                | Ergebnis                  |
|--------------------------|---------------------------|-------------------------|
| {"unix": 1626799080000}  | fromUnixTimestampMs(unix) | "2021-07-20T16:38:00+0" |
| {"unix": 0}              | fromUnixTimestampMs(unix) | "1970-01-01T00:00:00+0" |

> [!TIP]
> Informationen zum Beheben häufiger Fehler und Probleme finden Sie im Leitfaden zur [Problembehandlung](./iot-troubleshoot-guide.md) für IoT-Connectors.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie benutzerdefinierte Funktionen des IoT-Connectors verwenden. Informationen zur Verwendung von benutzerdefinierten Funktionen mit Gerätezuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von Gerätezuordnungen](how-to-use-device-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.

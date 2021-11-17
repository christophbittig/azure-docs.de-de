---
title: Referenz zum Schreiben von Ausdrücken für Attributzuordnungen bei der Anwendungsbereitstellung von Azure Active Directory
description: Erfahren Sie, wie Ausdruckszuordnungen verwendet werden können, um Attributwerte während der automatisierten Bereitstellung von SaaS-App-Objekten in Azure Active Directory in ein akzeptables Format zu transformieren. Enthält eine Referenzliste mit Funktionen
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 83b6e19a1e67e2e7e018aaa43ba4cf6149940ed7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292678"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Referenz zum Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory

Wenn Sie die Bereitstellung für eine SaaS-Anwendung konfigurieren, ist einer der Attributzuordnungstypen, die Sie angeben können, eine Ausdruckszuordnung. Für diese Zuordnungen müssen Sie einen skriptähnlichen Ausdruck schreiben, mit dem Sie die Daten Ihrer Benutzer in Formate transformieren können, die für die SaaS-Anwendung akzeptabler sind.

## <a name="syntax-overview"></a>Syntaxübersicht

Die Syntax für die Ausdrücke für Attributzuordnungen ist den Funktionen von Visual Basic for Applications (VBA) ähnlich.

* Der gesamte Ausdruck muss mittels Funktionen definiert werden, die aus einem Namen mit darauffolgenden Argumenten in Klammern bestehen:  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Sie können Funktionen ineinander verschachteln. Beispiel:  *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Sie können drei verschiedene Argumententypen an die Funktionen übergeben:
  
  1. Attribute, die in eckige Klammern eingeschlossen werden müssen. Beispiel: [Attributname]
  2. Zeichenfolgenkonstanten, die in doppelte Anführungszeichen eingeschlossen werden müssen. Beispiel: „USA“
  3. Andere Funktionen Beispiel: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Bei Zeichenfolgenkonstanten, in denen ein umgekehrter Schrägstrich ( \ ) oder ein Anführungszeichen ( " ) benötigt wird, muss dieser bzw. dieses mit einem umgekehrten Schrägstrichsymbol ( \ ) versehen werden. Beispiel: „Unternehmensname: \\"Contoso\\""
* Bei der Syntax muss die Groß- und Kleinschreibung beachtet werden – ein wichtiger Punkt, wenn Sie die Funktionen nicht direkt von hier kopieren, sondern als Zeichenfolgen eingeben. 

## <a name="list-of-functions"></a>Liste der Funktionen

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [AppRoleAssignmentsComplex](#approleassignmentscomplex) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateDiff](#datediff) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IgnoreFlowIfNullOrEmpty](#ignoreflowifnullorempty) &nbsp;&nbsp;&nbsp;&nbsp;[IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) &nbsp;&nbsp; &nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [RandomString](#randomstring) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace)&nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Anfügen

**Funktion:** Append(Quelle, Suffix)

**Beschreibung:** Fügt das Suffix am Ende eines angegebenen Quellzeichenfolgenwerts an.

**Parameter:**

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **Suffix** |Erforderlich |String |Die Zeichenfolge, die Sie am Ende des Quellwerts anfügen möchten |


#### <a name="append-constant-suffix-to-user-name"></a>Anfügen eines konstanten Suffixes an einen Benutzernamen
Beispiel: Wenn Sie Salesforce Sandbox verwenden, müssen Sie möglicherweise ein weiteres Suffix an alle Benutzernamen anfügen, bevor Sie diese synchronisieren.

**Ausdruck:**  
`Append([userPrincipalName], ".test")`

**Beispieleingabe/-ausgabe:** 

* **EINGABE**: (userPrincipalName): "John.Doe@contoso.com"
* **AUSGABE**:  "John.Doe@contoso.com.test"

---
### <a name="approleassignmentscomplex"></a>AppRoleAssignmentsComplex

**Funktion**: AppRoleAssignmentsComplex([appRoleAssignments])

**Beschreibung**: Wird zum Bereitstellen mehrerer Rollen für einen Benutzer verwendet. Ausführliche Informationen zur Verwendung finden Sie unter [Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md#provisioning-a-role-to-a-scim-app).

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Erforderlich |String |**[appRoleAssignments]** -Objekt |

---
### <a name="bitand"></a>BitAnd
**Funktion:** BitAnd(value1, value2)

**Beschreibung:**  Diese Funktion konvertiert beide Parameter in die binäre Darstellung und legt ein Bit auf Folgendes fest:

- 0 – wenn mindestens eines der entsprechenden Bits in „value1“ und „value2“ den Wert „0“ aufweist
- 1 – wenn beide entsprechenden Bits 1 sind.

Anders gesagt: sie gibt in allen Fällen 0 zurück, außer wenn die entsprechenden Bits beider Parameter 1 sind.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value1** |Erforderlich |Nummer |Numerischer Wert, der mit „value2“ mit AND verarbeitet werden soll|
| **value2** |Erforderlich |Nummer |Numerischer Wert, der mit „value1“ mit AND verarbeitet werden soll|

**Beispiel:** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111, daher gibt `BitAnd` „7“ zurück, den Binärwert von 00000111.

---
### <a name="cbool"></a>ZBool
**Funktion:**  
`CBool(Expression)`

**Beschreibung:**  
`CBool` gibt einen booleschen Wert zurück, der auf dem ausgewerteten Ausdruck basiert. Wenn die Auswertung des Ausdrucks einen Wert ungleich 0 (null) ergibt, gibt `CBool` *True* zurück. Andernfalls wird *False* zurückgegeben.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich | expression | Ein beliebiger gültiger Ausdruck |

**Beispiel:** 
`CBool([attribute1] = [attribute2])`                                                                    
Gibt True zurück, wenn beide Attribute den gleichen Wert haben.

---
### <a name="cdate"></a>ZDate
**Funktion:**  
`CDate(expression)`

**Beschreibung:**  
Die CDate-Funktion gibt einen UTC-DateTime-Wert aus einer Zeichenfolge zurück. DateTime ist kein nativer Attributtyp, kann aber in Datumsfunktionen wie [FormatDateTime](#formatdatetime) und [DateAdd](#dateadd) verwendet werden.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich | Ausdruck | Jede gültige Zeichenfolge, die ein Datum/eine Uhrzeit darstellt. Informationen zu unterstützten Formaten finden Sie unter [.NET: Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

**Hinweise:**  
Die zurückgegebene Zeichenfolge wird immer als UTC-Wert angegeben und weist das Format **M/d/yyyy h:mm:ss tt** auf.

**Beispiel 1:** <br> 
`CDate([StatusHireDate])`  
**Beispieleingabe/-ausgabe:** 

* **EINGABE**: (StatusHireDate): "2020-03-16-07:00"
* **AUSGABE**:  "3/16/2020 7:00:00 AM" <-- *Beachten Sie, dass die UTC-Entsprechung des obigen DateTime-Werts zurückgegeben wird.*

**Beispiel 2:** <br> 
`CDate("2021-06-30+08:00")`  
**Beispieleingabe/-ausgabe:** 

* **EINGABE**: "2021-06-30+08:00"
* **AUSGABE**:  "6/29/2021 4:00:00 PM" <-- *Beachten Sie, dass die UTC-Entsprechung des obigen DateTime-Werts zurückgegeben wird.*

**Beispiel 3:** <br> 
`CDate("2009-06-15T01:45:30-07:00")`  
**Beispieleingabe/-ausgabe:** 

* **EINGABE**: "2009-06-15T01:45:30-07:00"
* **AUSGABE**:  "6/15/2009 8:45:30 AM" <-- *Beachten Sie, dass die UTC-Entsprechung des obigen DateTime-Werts zurückgegeben wird.*

---
### <a name="coalesce"></a>Coalesce
**Funktion:** Coalesce(source1, source2, ..., defaultValue)

**Beschreibung:** Gibt den ersten Quellwert zurück, der nicht NULL ist. Wenn alle Argumente NULL sind und „defaultValue“ vorhanden ist, wird der Standardwert (defaultValue) zurückgegeben. Wenn alle Argumente NULL sind und „defaultValue“ nicht vorhanden ist, gibt Coalesce NULL zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **source1 … sourceN** | Erforderlich | String |Erforderlich, unterschiedlich oft. Normalerweise der Name des Attributs aus dem Quellobjekt |
| **defaultValue** | Optional | String | Der zu verwendende Standardwert, wenn alle Quellwerte NULL sind. Kann eine leere Zeichenfolge ("") sein.

#### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>E-Mail-Wert übermitteln, wenn nicht NULL, andernfalls userPrincipalName-Wert übermitteln
Beispiel: Sie möchten das E-Mail-Attribut übermitteln, wenn es vorhanden ist. Andernfalls möchten Sie stattdessen den Wert von „userPrincipalName“ übermitteln.

**Ausdruck:**  
`Coalesce([mail],[userPrincipalName])`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (mail): NULL
* **EINGABE** (userPrincipalName): "John.Doe@contoso.com"
* **AUSGABE**:  "John.Doe@contoso.com"


---
### <a name="converttobase64"></a>ConvertToBase64
**Funktion:** ConvertToBase64(source)

**Beschreibung:**  Die ConvertToBase64-Funktion konvertiert eine Zeichenfolge in eine Unicode-Base64-Zeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Eine in Base64 zu konvertierende Zeichenfolge|

**Beispiel:** 
`ConvertToBase64("Hello world!")`

Gibt „SABlAGwAbABvACAAdwBvAHIAbABkACEA“ zurück.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funktion:** ConvertToUTF8Hex(source)

**Beschreibung:**  Die ConvertToUTF8Hex-Funktion konvertiert eine Zeichenfolge in einen hexadezimal-codierten UTF8-Wert.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Eine in UTF-8 (Hex) zu konvertierende Zeichenfolge|

**Beispiel:** 
`ConvertToUTF8Hex("Hello world!")`

Gibt „48656C6C6F20776F726C6421“ zurück.

---
### <a name="count"></a>Anzahl
**Funktion:** Count(attribute)

**Beschreibung:**  Die Count-Funktion gibt die Anzahl von Elementen in einem mehrwertigen Attribut zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |Attribut |Mehrwertiges Attribut, aus dem Elemente gezählt werden|

---
### <a name="cstr"></a>CStr
**Funktion:** CStr(value)

**Beschreibung:** Die CStr-Funktion konvertiert einen Wert in einen Zeichenfolgendatentyp.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | Numerischer Wert, Verweisattribut oder boolescher Wert | Kann ein numerischer Wert, ein Verweisattribut oder ein boolescher Wert sein. |

**Beispiel:** 
`CStr([dn])`

Gibt „cn=Joe,dc=contoso,dc=com“ zurück

---
### <a name="dateadd"></a>DateAdd
**Funktion:**  
`DateAdd(interval, value, dateTime)`

**Beschreibung:**  
Gibt eine Datums-/Uhrzeitzeichenfolge zurück, die ein Datum darstellt, zu dem ein bestimmtes Zeitintervall hinzugefügt wurde. Das zurückgegebene Datum weist das folgende Format auf: **M/d/yyyy h:mm:ss tt**.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **interval** |Erforderlich | String | Gibt das Zeitintervall an, das Sie hinzufügen möchten. Die zulässigen Werte werden unterhalb dieser Tabelle aufgeführt. |
| **value** |Erforderlich | Number | Die Anzahl der Einheiten, die Sie hinzufügen möchten. Der Wert kann positiv (für Datumsangaben in der Zukunft) oder negativ (für Datumsangaben in der Vergangenheit) sein. |
| **dateTime** |Erforderlich | Datetime | DateTime-Wert, der das Datum darstellt, dem das Intervall hinzugefügt wird. |

Die **interval**-Zeichenfolge muss einem der folgenden Werte entsprechen: 
 * yyyy: Jahr 
 * m: Monat
 * d: Tag
 * ww: Woche
 * h: Stunde
 * n: Minute
 * s: Sekunde

**Beispiel 1: Hinzufügen von 7 Tagen zum Einstellungsdatum**  
`DateAdd("d", 7, CDate([StatusHireDate]))`
* **EINGABE**: (StatusHireDate): 2012-03-16-07:00
* **AUSGABE**: 3/23/2012 7:00:00 AM

**Beispiel 2: Abrufen des Datums zehn Tage vor dem Einstellungsdatum**  
`DateAdd("d", -10, CDate([StatusHireDate]))`
* **EINGABE**: (StatusHireDate): 2012-03-16-07:00
* **AUSGABE**: 3/6/2012 7:00:00 AM

**Beispiel 3: Dem Einstellungsdatum zwei Wochen hinzufügen**  
`DateAdd("ww", 2, CDate([StatusHireDate]))`
* **EINGABE**: (StatusHireDate): 2012-03-16-07:00
* **AUSGABE**: 3/30/2012 7:00:00 AM

**Beispiel 4: Dem Einstellungsdatum zwei Wochen hinzufügen**  
`DateAdd("m", 10, CDate([StatusHireDate]))`
* **EINGABE**: (StatusHireDate): 2012-03-16-07:00
* **AUSGABE**: 1/16/2013 7:00:00 AM

**Beispiel 5: Dem Einstellungsdatum zwei Wochen hinzufügen**  
`DateAdd("yyyy", 2, CDate([StatusHireDate]))`
* **EINGABE**: (StatusHireDate): 2012-03-16-07:00
* **AUSGABE**: 3/16/2014 7:00:00 AM
---
### <a name="datediff"></a>DateDiff
**Funktion:**  
`DateDiff(interval, date1, date2)`

**Beschreibung:**  
Diese Funktion verwendet den *interval*-Parameter, um eine Zahl zurückzugeben, die den Unterschied zwischen den beiden Eingabedaten angibt. Er gibt  zurück. 
  * eine positive Zahl, wenn date2 > date1, 
  * eine negative Zahl, wenn date2 < date1, 
  * 0, wenn date2 == date1

**Parameter:** 

| Name | Erforderlich/Optional | type | Notizen |
| --- | --- | --- | --- |
| **interval** |Erforderlich | String | Zeitintervall, das zum Berechnen der Differenz verwendet werden soll |
| **date1** |Erforderlich | Datetime | DateTime, das ein gültiges Datum darstellt |
| **date2** |Erforderlich | Datetime | DateTime, das ein gültiges Datum darstellt |

Die **interval**-Zeichenfolge muss einem der folgenden Werte entsprechen: 
 * yyyy: Jahr 
 * m: Monat
 * d: Tag
 * ww: Woche
 * h: Stunde
 * n: Minute
 * s: Sekunde

**Beispiel 1: Vergleichen des aktuellen Datums mit dem Einstellungsdatum von Workday mit unterschiedlichen Intervallen** <br>
`DateDiff("d", Now(), CDate([StatusHireDate]))`

| Beispiel | interval | date1 | date2 | output |
| --- | --- | --- | --- | --- |
| Positive Differenz in Tagen zwischen zwei Datumsangaben | T | 2021-08-18+08:00 | 2021-08-31+08:00 | 13 |
| Negative Differenz in Tagen zwischen zwei Datumsangaben | T | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -3449 |
| Differenz in Wochen zwischen zwei Datumsangaben | ww | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -493 | 
| Differenz in Monaten zwischen zwei Datumsangaben | m | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -113 | 
| Differenz in Jahren zwischen zwei Datumsangaben | yyyy | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | –9 | 
| Differenz, wenn beide Datumsangaben identisch sind | T | 2021-08-31+08:00 | 2021-08-31+08:00 | 0 | 
| Differenz in Stunden zwischen zwei Datumsangaben | h | 2021-08-24 | 2021-08-25 | 24 | 
| Differenz in Minuten zwischen zwei Datumsangaben | n | 2021-08-24 | 2021-08-25 | 1440 | 
| Differenz in Sekunden zwischen zwei Datumsangaben | s | 2021-08-24 | 2021-08-25 | 86.400 | 

**Beispiel 2: Kombinieren von DateDiff mit der IIF-Funktion zum Festlegen des Attributwerts** <br>
Wenn ein Konto in Workday aktiv ist, legen Sie das Attribut des Nutzers *accountEnabled* (Konto aktiviert) nur dann auf Wahr (True) fest, wenn das Einstellungsdatum innerhalb der nächsten fünf Tage liegt. 

```
Switch([Active], , 
  "1", IIF(DateDiff("d", Now(), CDate([StatusHireDate])) > 5, "False", "True"), 
  "0", "False")
```

---

### <a name="datefromnum"></a>DateFromNum
**Funktion:** DateFromNum(value)

**Beschreibung:** Die DateFromNum-Funktion konvertiert einen Wert im AD-Datumsformat in einen DateTime-Typ.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | Date | AD-Datum, das in den DateTime-Typ konvertiert werden soll |

**Beispiel:** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Gibt einen DateTime-Wert zurück, der für den 1. Januar 2012 um 23:00 Uhr steht.

---
### <a name="formatdatetime"></a>FormatDatumZeit
**Funktion:** FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

**Beschreibung:** Konvertiert eine Datumszeichenfolge aus einem Format in ein anderes Format.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **dateTimeStyles** | Optional | String | Verwenden Sie diese Zeichenfolge, um die Formatierungsoptionen anzugeben, mit denen die Art der Analyse einer Zeichenfolge für eine Reihe von Datums- und Uhrzeitanalysemethoden angepasst wird. Unterstützte Werte finden Sie in der [Dokumentation zu DateTimeStyles](/dotnet/api/system.globalization.datetimestyles). Wird das Feld leer gelassen, wird als Standardwert „DateTimeStyles.RoundtripKind, DateTimeStyles.AllowLeadingWhite, DateTimeStyles.AllowTrailingWhite“ verwendet.  |
| **Eingabeformat** |Erforderlich |String |Erwartetes Format des Quellwerts. Informationen zu unterstützten Formaten finden Sie unter [.NET: Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **Ausgabeformat** |Erforderlich |String |Format des Ausgabedatums. |



#### <a name="output-date-as-a-string-in-a-certain-format"></a>Ausgabedatum eines Datums als Zeichenfolge in einem bestimmten Format
Beispiel: Sie möchten Datumsangaben in einem bestimmten Format an eine SaaS-Anwendung wie ServiceNow senden. Sie können ggf. den folgenden Ausdruck verwenden: 

**Ausdruck:** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (extensionAttribute1): 20150123105347.1Z
* **AUSGABE**:  2015-01-23


---
### <a name="guid"></a>Guid
**Funktion:** Guid()

**Beschreibung:** Die Guid-Funktion generiert eine neue GUID nach dem Zufallsprinzip.

**Beispiel:** <br>
`Guid()`<br>
Beispielausgabe: "1088051a-cd4b-4288-84f8-e02042ca72bc"

---
### <a name="ignoreflowifnullorempty"></a>IgnoreFlowIfNullOrEmpty
**Funktion**: IgnoreFlowIfNullOrEmpty(expression)

**Beschreibung**: Die IgnoreFlowIfNullOrEmpty-Funktion weist den Bereitstellungsdienst an, das Attribut zu ignorieren und aus dem Flow zu löschen, wenn die eingeschlossene Funktion oder das eingeschlossene Attribut NULL oder leer ist.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** | Erforderlich | Ausdruck | Der auszuwertende Ausdruck |

**Beispiel 1: Entfernen eines Attributs aus dem Flow, wenn es NULL ist** <br>
`IgnoreFlowIfNullOrEmpty([department])` <br>
Mit dem obigen Ausdruck wird das Attribut „department“ aus dem Bereitstellungsflow entfernt, wenn es NULL oder leer ist. <br>

**Beispiel 2: Entfernen eines Attributs aus dem Flow, wenn der Ausdruck als eine leere Zeichenfolge oder NULL ausgewertet wird** <br>
Angenommen, das *prefix* des Attributs „SuccessFactors“ wird mithilfe der folgenden Ausdruckszuordnung dem lokalen Active Directory-Attribut *personalTitle* zugeordnet: <br>
`IgnoreFlowIfNullOrEmpty(Switch([prefix], "", "3443", "Dr.", "3444", "Prof.", "3445", "Prof. Dr."))` <br>
Der obige Ausdruck wertet zuerst die [Switch](#switch)-Funktion aus. Wenn das *prefix*-Attribut keine der in der *Swicht*-Funktion aufgeführten Werte auflistet, gibt *Switch* eine leere Zeichenfolge zurück, und das Attribut *personalTitle* wird nicht in den Bereitstellungsflow für die lokale Active Directory-Instanz eingeschlossen.

---
### <a name="iif"></a>IIF
**Funktion:** IIF(condition,valueIfTrue,valueIfFalse)

**Beschreibung:**  Die IIF-Funktion gibt basierend auf einer angegebenen Bedingung einen Wert aus einem Satz möglicher Werte zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **condition** |Erforderlich |Variable oder Ausdruck |Ein Wert oder Ausdruck, der als TRUE oder FALSE ausgewertet werden kann. |
| **valueIfTrue** |Erforderlich |Variable oder Zeichenfolge | Der zurückgegebene Wert, wenn die Bedingung als „True“ ausgewertet wird. |
| **valueIfFalse** |Erforderlich |Variable oder Zeichenfolge |Der zurückgegebene Wert, wenn die Bedingung als „False“ ausgewertet wird.|

**Beispiel:** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Funktion:** InStr(value1,value2,start,compareType)

**Beschreibung:**  Die InStr-Funktion sucht nach dem ersten Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value1** |Erforderlich |String |Zu durchsuchende Zeichenfolge |
| **value2** |Erforderlich |String |Zu suchende Zeichenfolge |
| **start** |Optional |Integer |Startposition für die Suche nach der Teilzeichenfolge|
| **compareType** |Optional |Enum |Kann „vbTextCompare“ oder „vbBinaryCompare“ sein |

**Beispiel:** 
`InStr("The quick brown fox","quick")`

Wird als 5 ausgewertet

`InStr("repEated","e",3,vbBinaryCompare)`

Wird als 7 ausgewertet.

---
### <a name="isnull"></a>IsNull
**Funktion:** IsNull(Expression)

**Beschreibung:**  Die IsNull-Funktion gibt „True“ zurück, wenn die Auswertung des Ausdrucks einen Nullwert ergibt. Für ein Attribut wird ein Nullwert durch die Abwesenheit des Attributs ausgedrückt.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich |Ausdruck |Der auszuwertende Ausdruck |

**Beispiel:** 
`IsNull([displayName])`

Gibt „True“ zurück, wenn das Attribut nicht vorhanden ist

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funktion:** IsNullOrEmpty(Expression)

**Beschreibung:**  Die IsNullOrEmpty-Funktion gibt „True“ zurück, wenn der Ausdruck einem Nullwert oder einer leeren Zeichenfolge entspricht. Wird für ein Attribut als „True“ ausgewertet, wenn das Attribut nicht vorhanden oder zwar vorhanden, aber eine leere Zeichenfolge ist.
Die Umkehrung dieser Funktion heißt "IsPresent".

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich |Ausdruck |Der auszuwertende Ausdruck |

**Beispiel:** 
`IsNullOrEmpty([displayName])`

Gibt „True“ zurück, wenn das Attribut nicht vorhanden ist oder eine leere Zeichenfolge darstellt

---
### <a name="ispresent"></a>IsPresent
**Funktion:** IsPresent (Ausdruck)

**Beschreibung:**  Die IsPresent-Funktion gibt „True“ zurück, wenn der Ausdruck zu einer Zeichenfolge ausgewertet wird, die kein Nullwert und nicht leer ist. Die umgekehrte Funktion heißt „IsNullOrEmpty“.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich |Ausdruck |Der auszuwertende Ausdruck |

**Beispiel:** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Funktion:** IsString(Expression)

**Beschreibung:**  Die IsString-Funktion wird als „True“ ausgewertet, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Ausdruck** |Erforderlich |Ausdruck |Der auszuwertende Ausdruck |

---
### <a name="item"></a>Element
**Funktion:** Item(attribute, index)

**Beschreibung:**  Die Item-Funktion gibt ein Element aus einer mehrwertigen Zeichenfolge oder einem mehrwertigen Attribut zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |attribute |Das zu durchsuchende mehrwertige Attribut |
| **Index** |Erforderlich |Integer | Index für ein Element in der mehrwertigen Zeichenfolge|

**Beispiel:** 
`Item([proxyAddresses], 1)` gibt das erste Element im mehrwertigen Attribut zurück. Index 0 sollte nicht verwendet werden. 

---
### <a name="join"></a>Join
**Funktion:**  Join(Trennzeichen, Quelle1, Quelle2, …)

**Beschreibung:** „Join()“ ist vergleichbar mit „Append()“, kann jedoch mehrere **Quellzeichenfolgenwerte** in einer einzelnen Zeichenfolge kombinieren, wobei die Werte jeweils durch eine **Trennzeichenfolge** getrennt werden.

Wenn einer der Quellwerte ein mehrwertiges Attribut ist, werden die einzelnen Werte in diesem Attribut miteinander verknüpft und dabei durch den Trennzeichenwert getrennt.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Trennzeichen** |Erforderlich |String |Zeichenfolge, die zur Trennung von Quellwerten verwendet wird, wenn diese zu einer einzelnen Zeichenfolge zusammengesetzt werden. Kann "" sein, wenn kein Trennzeichen erforderlich ist. |
| **source1 … sourceN** |Erforderlich, unterschiedlich oft |String |Zeichenfolgenwerte, die zusammengesetzt werden sollen. |

---
### <a name="left"></a>Left
**Funktion:** Left(String,NumChars)

**Beschreibung:**  Die Left-Funktion gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück. Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
Wenn "numChars" < 0, wird die Eingabezeichenfolge zurückgegeben.
Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.
Wenn die Zeichenfolge weniger Zeichen enthält als in „numChars“ angegeben, wird eine identische Zeichenfolge (also eine Zeichenfolge, die alle in Parameter 1 enthaltenen Zeichen enthält) zurückgegeben.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **String** |Erforderlich |attribute | Die Zeichenfolge, aus der Zeichen zurückgegeben werden sollen |
| **NumChars** |Erforderlich |Integer | Eine Zahl, die die Anzahl von Zeichen angibt, die ab dem Anfang der Zeichenfolge (links) zurückgegeben werden sollen|

**Beispiel:** 
`Left("John Doe", 3)`

Gibt „Joh“ zurück.

---
### <a name="mid"></a>Mid
**Funktion:**  Mid(Quelle, Start, Länge)

**Beschreibung:**  Gibt eine Teilzeichenfolge des Quellwerts zurück. Eine Teilzeichenfolge ist eine Zeichenfolge, die nur einige der Zeichen aus der Quellzeichenfolge enthält.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs. |
| **start** |Erforderlich |Integer |Index in der **Quellzeichenfolge** , an dem die Teilzeichenfolge beginnen soll. Das erstes Zeichen in der Zeichenfolge hat den Index 1, das zweite Zeichen hat den Index 2 usw. |
| **length** |Erforderlich |Integer |Die Länge der Teilzeichenfolge. Wenn die Länge außerhalb der **Quell**-Zeichenfolge endet, gibt die Funktion die Teilzeichenfolge zwischen dem **Start**-Index und dem Ende der **Quell**-Zeichenfolge zurück. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktion:** NormalizeDiacritics(Quelle)

**Beschreibung:** Erfordert ein einzelnes Zeichenfolgenargument. Gibt die Zeichenfolge zurück und ersetzt sämtliche diakritische Zeichen durch entsprechende nicht diakritische Zeichen. Dient in der Regel dazu, Vor- und Nachnamen mit diakritischen Zeichen (Akzenten) in zulässige Werte zu konvertieren, die in verschiedenen Benutzer-IDs verwendet werden können (etwa in Benutzerprinzipalnamen, SAM-Kontonamen und E-Mail-Adressen).

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String | In der Regel ein Attribut für einen Vor- oder Nachnamen. |


| Zeichen mit diakritischem Zeichen  | Normalisiertes Zeichen | Zeichen mit diakritischem Zeichen  | Normalisiertes Zeichen | 
| --- | --- | --- | --- | 
| ä, à, â, ã, å, á, ą, ă, ā, ā́, ā̀, ā̂, ā̃, ǟ, ā̈, ǡ, a̱, å̄ | a | Ä, À, Â, Ã, Å, Á, Ą, Ă, Ā, Ā́, Ā̀, Ā̂, Ā̃, Ǟ, Ā̈, Ǡ, A̱, Å̄ | Ein | 
| æ, ǣ | ae | Æ, Ǣ | AE | 
| ç, č, ć, c̄, c̱ | c | Ç, Č, Ć, C̄, C̱ | C | 
| ď, d̄, ḏ | T | Ď, D̄, Ḏ | D | 
| ë, è, é, ê, ę, ě, ė, ē, ḗ, ḕ, ē̂, ē̃, ê̄, e̱, ë̄, e̊̄ | e | Ë, È, É, Ê, Ę, Ě, Ė, Ē, Ḗ, Ḕ, Ē̂, Ē̃, Ê̄, E̱, Ë̄, E̊̄ | E | 
| ğ, ḡ, g̱ | g | Ğ, Ḡ, G̱ | G | 
| ï, î, ì, í, ı, ī, ī́, ī̀, ī̂, ī̃, i̱ | i | Ï, Î, Ì, Í, İ, Ī, Ī́, Ī̀, Ī̂, Ī̃, I̱ | I |  
| ľ, ł, l̄, ḹ, ḻ | l |  Ł, Ľ, L̄, Ḹ, Ḻ | L | 
| ñ, ń, ň, n̄, ṉ | n |  Ñ, Ń, Ň, N̄, Ṉ | N | 
| ö, ò, ő, õ, ô, ó, ō, ṓ, ṑ, ō̂, ō̃, ȫ, ō̈, ǭ, ȭ, ȱ, o̱ | o |  Ö, Ò, Ő, Õ, Ô, Ó, Ō, Ṓ, Ṑ, Ō̂, Ō̃, Ȫ, Ō̈, Ǭ, Ȭ, Ȱ, O̱ | O | 
| ø, ø̄, œ̄  | oe |  Ø, Ø̄, Œ̄ | OE | 
| ř, r̄, ṟ, ṝ | r |  Ř, R̄, Ṟ, Ṝ | R | 
| ß | ss | | | 
| š, ś, ș, ş, s̄, s̱ | s |  Š, Ś, Ș, Ş, S̄, S̱ | E | 
| ť, ț, t̄, ṯ | t | Ť, Ț, T̄, Ṯ | T | 
| ü, ù, û, ú, ů, ű, ū, ū́, ū̀, ū̂, ū̃, u̇̄, ǖ, ṻ, ṳ̄, u̱ | u |  Ü, Ù, Û, Ú, Ů, Ű, Ū, Ū́, Ū̀, Ū̂, Ū̃, U̇̄, Ǖ, Ṻ, Ṳ̄, U̱ | U | 
| ÿ, ý, ȳ, ȳ́, ȳ̀, ȳ̃, y̱ | j | Ÿ, Ý, Ȳ, Ȳ́, Ȳ̀, Ȳ̃, Y̱ | J | 
| ź, ž, ż, z̄, ẕ | z | Ź, Ž, Ż, Z̄, Ẕ | Z | 


#### <a name="remove-diacritics-from-a-string"></a>Entfernen diakritischer Zeichen aus einer Zeichenfolge
Beispiel: Ersetzen Sie Zeichen, die Akzente enthalten, durch entsprechende Zeichen, die keine Akzente enthalten.

**Ausdruck:** NormalizeDiacritics([givenName])

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (givenName): „Zoë“
* **AUSGABE**:  „Zoe“


---
### <a name="not"></a>Not
**Funktion:**  Not(Quelle)

**Beschreibung:** Kehrt den booleschen Wert der **Quelle** um. Lautet der **Quellwert** also „True“, gibt die Funktion „False“ zurück. Andernfalls gibt sie „True“ zurück.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |Boolesche Zeichenfolge |Die erwarteten **Quellwerte** sind TRUE oder FALSE. |

---
### <a name="now"></a>jetzt
**Funktion**: Now()

**Beschreibung:**  
Die Now-Funktion gibt eine Zeichenfolge zurück, die die aktuelle UTC-DateTime im Format **M/d/yyyy h:mm:ss tt** darstellt.

**Beispiel:** 
`Now()` <br>
Zurückgegebener Beispielwert: *7/2/2021 3:33:38 PM*

---
### <a name="numfromdate"></a>NumFromDate
**Funktion:** NumFromDate (Wert)

**Beschreibung:** Die NumFromDate-Funktion konvertiert einen „DateTime“-Wert in das Active Directory-Format, das zum Festlegen von Attributen wie [accountExpires](/windows/win32/adschema/a-accountexpires) erforderlich ist. Verwenden Sie diese Funktion zum Konvertieren von „DateTime“-Werten, die von HR-Cloud-Apps wie Workday und SuccessFactors empfangen werden, in die entsprechende Azure AD-Darstellung. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **value** |Erforderlich | String | Zeichenfolge mit Datum und Uhrzeit im unterstützten Format. Unterstützte Formate finden Sie unter https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Beispiel:**
* Beispiel für Workday: Wenn Sie das Attribut *ContractEndDate* von Workday im Format *2020-12-31-08:00* dem Feld *accountExpires* (Konto läuft ab) in AD zuordnen möchten, so erfahren Sie hier, wie Sie diese Funktion verwenden und die Zeitzonenverschiebung so ändern, dass er Ihrer lokalen Zeitzone entspricht. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), " 23:59:59-08:00"))`

* Beispiel für SuccessFactors: Wenn Sie das Attribut *endDate* von SuccessFactors im Format *M/d/yyyy hh:mm:ss tt* dem Feld *accountExpires* (Konto läuft ab) in AD zuordnen möchten, erfahren Sie hier, wie Sie diese Funktion verwenden und die Zeitzonenverschiebung so ändern, dass er Ihrer lokalen Zeitzone entspricht.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd")," 23:59:59-08:00"))`


---
### <a name="pcase"></a>PCase
**Funktion:** PCase(Quelle, wordSeparators)

**Beschreibung:** Die PCase-Funktion konvertiert das erste Zeichen jedes Worts in einer Zeichenfolge in einen Großbuchstaben und alle anderen Zeichen in Kleinbuchstaben.

**Parameter:** 

| Name | Erforderlich/Optional | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Quell**-Wert zum Konvertieren in gemischte Groß-/Kleinschreibung. |
| **wordSeparators** |Optional |String |Angeben eines Satzes an Zeichen, die als Worttrennzeichen verwendet werden (Beispiel: " ,-'") |

**Hinweise:**

* Wenn der Parameter *wordSeparators* nicht angegeben wird, ruft PCase intern die .NET-Funktion [ToTitleCase](/dotnet/api/system.globalization.textinfo.totitlecase) auf, um die *Quell*-Zeichenfolge in die richtige Groß-/Kleinschreibung zu konvertieren. Die .NET-Funktion *ToTitleCase* unterstützt einen umfassenden Satz an [Unicode-Zeichenkategorien](https://www.unicode.org/reports/tr44/#General_Category_Values) als Worttrennzeichen. 
  * Leerzeichen
  * Neue-Zeile-Zeichen
  * *Steuerzeichen* wie Strg
  * Steuerzeichen für die *Formatierung*
  * *Verbindungszeichen* wie Unterstrich
  * *Bindestrichzeichen* wie Gedankenstrich und Bindestrich (einschließlich Zeichen wie Halbgeviertstrich, Geviertstrich, Doppelbindestrich usw.)
  * *Öffnende Satzzeichen* und *Schließende Satzzeichen*, die in Paaren wie Klammern, geschweiften Klammern, spitzen Klammern usw. vorkommen. 
  * *Öffnende Anführungszeichen* und *Schließende Anführungszeichen* wie einfache Anführungszeichen, doppelte Anführungszeichen und eckige Anführungszeichen. 
  * *Andere Satzzeichen* wie Ausrufezeichen, Nummernzeichen, Prozentzeichen, Kaufmännisches Und-Zeichen, Sternchen, Komma, Punkt, Doppelpunkt, Semikolon usw. 
  * *Mathematische Zeichen* wie Pluszeichen, Kleiner als-Zeichen und Größer als-Zeichen, vertikale Linie, Tilde, Gleichheitszeichen usw.
  * *Währungssymbole* wie Dollarzeichen, Centzeichen, Pfundzeichen, Eurozeichen usw. 
  * *Modifikationszeichen* wie Makron, Akzente, Maßpfeile usw. 
  * *Sonstige Symbolzeichen* wie Copyrightzeichen, Gradzeichen, Zeichen für registriertes Warenzeichen usw. 
* Wenn der Parameter *wordSeparators* angegeben wird, verwendet PCase nur die als Worttrennzeichen angegebenen Zeichen. 

**Beispiel:**

Angenommen, Sie beziehen die Attribute *firstName* und *lastName* von SAP SuccessFactors, und in der Personalverwaltung stehen beide Attribute in Großbuchstaben. Mithilfe der PCase-Funktion können Sie den Namen wie unten gezeigt in die richtige Groß-/Kleinschreibung konvertieren. 

| Ausdruck | Eingabe | Ausgabe | Hinweise |
| --- | --- | --- | --- |
| `PCase([firstName])` | *firstName* = "PABLO GONSALVES (SECOND)" | "Pablo Gonsalves (Second)" | Da der Parameter *wordSeparators* nicht angegeben wird, verwendet die Funktion *PCase* den Standardzeichensatz als Worttrennzeichen. |
| `PCase([lastName]," '-")` | *lastName* = "PINTO-DE'SILVA" | "Pinto-De'Silva" | Die Funktion *PCase* verwendet Zeichen im Parameter *wordSeparators*, um Wörter zu erkennen und sie in die richtige Groß-/Kleinschreibung zu transformieren. |
| `PCase(Join(" ",[firstName],[lastName]))` | *firstName* = GREGORY, *lastName* = "JAMES" | "Gregory James" | Sie können die Join-Funktion in PCase schachteln. Da der Parameter *wordSeparators* nicht angegeben wird, verwendet die Funktion *PCase* den Standardzeichensatz als Worttrennzeichen.  |


---

### <a name="randomstring"></a>Zufällige Zeichenfolge (RandomString)
**Funktion:** Zufälllige Zeichenfolge(Länge, Mindestanzahl an Nummern, Mindestanzahl an Sonderzeichen, Mindestanzahl an Großbuchstaben, Mindestanzahl an Kleinbuchstaben, unzulässige Zeichen) [RandomString(Length, MinimumNumbers, MinimumSpecialCharacters , MinimumCapital, MinimumLowerCase, CharactersToAvoid)]

**Beschreibung:** Die RandomString-Funktion generiert eine zufällige Zeichenfolge basierend auf den angegebenen Bedingungen. Die zulässigen Zeichen können [hier](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference) ausfindig gemacht werden.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Länge** |Erforderlich |Number |Gesamtlänge der zufälligen Zeichenfolge. Dieser sollte größer oder gleich der Summe aus der Mindestanzahl an Nummern (MinimumNumbers), Mindestanzahl an Sonderzeichen (MinimumSpecialCharacters) und der Mindestanzahl an Großbuchstaben (MinimumCapital) sein. Maximal 256 Zeichen.|
| **Mindestanzahl an Nummern** (MinimumNumbers) |Erforderlich |Number |Die Mindestanzahl an Nummern in der zufälligen Zeichenfolge.|
| **Mindestanzahl an Sonderzeichen** (MinimumSpecialCharacters) |Erforderlich |Number |Mindestanzahl an Sonderzeichen.|
| **Mindestanzahl an Großbuchstaben** (MinimumCapital) |Erforderlich |Number |Mindestanzahl an Großbuchstaben in der zufälligen Zeichenfolge.|
| **Mindestanzahl an Kleinbuchstaben** (MinimumLowerCase) |Erforderlich |Number |Mindestanzahl an Kleinbuchstaben in der zufälligen Zeichenfolge.|
| **Unzulässige Zeichen** (CharactersToAvoid) |Optional |String |Zeichen, die beim Generieren der zufälligen Zeichenfolge ausgeschlossen werden sollen.|


**Beispiel 1:** – Generieren einer zufälligen Zeichenfolge ohne Sonderzeicheneinschränkungen: `RandomString(6,3,0,0,3)`
Generiert eine zufällige Zeichenfolge mit 6 Zeichen. Die Zeichenfolge enthält 3 Zahlen und 3 Kleinbuchstaben (1a73qt).

**Beispiel 2:** – Generieren einer zufälligen Zeichenfolge mit Sonderzeicheneinschränkungen: `RandomString(10,2,2,2,1,"?,")`
Generiert eine zufällige Zeichenfolge mit 10 Zeichen. Die Zeichenfolge enthält mindestens 2 Zahlen, 2 Sonderzeichen, 2 Großbuchstaben, einen Kleinbuchstaben und schließt die Zeichen "?" und "," (1@!2BaRg53) aus.

---

### <a name="removeduplicates"></a>RemoveDuplicates
**Funktion:** RemoveDuplicates(attribute)

**Beschreibung:**  Die RemoveDuplicates-Funktion stellt sicher, dass in einer übergebenen mehrwertigen Zeichenfolge jeder Wert eindeutig ist.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **attribute** |Erforderlich |Mehrwertiges Attribut |Mehrwertiges Attribut, aus dem Duplikate entfernt werden|

**Beispiel:** 
`RemoveDuplicates([proxyAddresses])`  Gibt ein bereinigtes proxyAddress-Attribut zurück, aus dem alle doppelten Werte entfernt wurden.

---
### <a name="replace"></a>Replace
**Funktion:** Replace(Quelle, AlterWert, RegexMuster, RegexGruppenname, Ersatzwert, Ersatzattributname, Vorlage)

**Beschreibung:** Hiermit werden Werte in einer Zeichenfolge unter Beachtung der Groß-/Kleinschreibung ersetzt. Die Funktion weist ein anderes Verhalten auf, je nachdem, welche Parameter angegeben werden:

* Bei Angabe von **AlterWert** und **Ersatzwert**:
  
  * Ersetzt alle Vorkommen von **AlterWert** in der **Quelle** durch **Ersatzwert**.
* Bei Angabe von **AlterWert** und **Vorlage**:
  
  * Ersetzt alle Vorkommen von **AlterWert** in **Vorlage** durch den **Quellwert**).
* Bei Angabe von **RegexMuster** und **Ersatzwert**:

  * Die Funktion wendet das **RegexMuster** auf die Zeichenfolge in **Quelle** an, und Sie können die RegexGruppennamen zum Erstellen der Zeichenfolgen für **Ersatzwert** verwenden.
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzwert**:
  
  * Die Funktion wendet das **RegexMuster** auf die **Quellzeichenfolge** an und ersetzt alle mit **RegexGruppenname** übereinstimmenden Werte durch **Ersatzwert**.
* Bei Angabe von **RegexMuster**, **RegexGruppenname** und **Ersatzattributname**:
  
  * Falls kein Wert für **Quelle** vorhanden ist, wird **Quelle** zurückgegeben.
  * Wenn **Quelle** einen Wert umfasst, wendet die Funktion das **RegexMuster** auf die Zeichenfolge in **Quelle** an und ersetzt alle mit **RegexGruppenname** übereinstimmenden Werte durch den Wert, der **Ersatzattributname** zugeordnet ist.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Objekt **Quelle**. |
| **AlterWert** |Optional |String |Wert, der in **Quelle** oder **Vorlage** ersetzt werden soll. |
| **RegexMuster** |Optional |String |Regex-Muster für den Wert, der in der **Quelle** ersetzt wird. Oder, bei Verwendung von **Ersatzeigenschaftsname**, das Muster, das zum Extrahieren des Werts aus **Ersatzeigenschaftsname** verwendet wird. |
| **RegexGruppenname** |Optional |String |Name der Gruppe im **RegexMuster**. Nur bei Verwendung von **Ersatzeigenschaftsname** wird der Wert dieser Gruppe als **Ersatzwert** aus **Ersatzeigenschaftsname** extrahiert. |
| **Ersatzwert** |Optional |String |Neuer Wert, durch den der alte Wert ersetzt wird. |
| **Ersatzattributname** |Optional |String |Name des Attributs, das als Ersatzwert verwendet werden soll. |
| **Vorlage** |Optional |String |Bei Angabe des Werts **Vorlage** wird **AlterWert** in der Vorlage gesucht und durch den Wert von **Quelle** ersetzt. |

#### <a name="replace-characters-using-a-regular-expression"></a>Ersetzen von Zeichen mit einem regulären Ausdruck
Beispiel: Sie müssen Zeichen suchen, die mit einem regulären Ausdruck übereinstimmen, und diese entfernen.

**Ausdruck:** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Beispieleingabe/-ausgabe:**

* **EINGABE:** (mailNickname: "john_doe72"
* **AUSGABE**: "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktion:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Beschreibung:** Es sind mindestens zwei Argumente erforderlich, bei denen es sich um eindeutige Regeln für die Generierung von Werten handelt, die mit Ausdrücken definiert werden. Mit der Funktion wird jede Regel ausgewertet. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit in der Ziel-App bzw. im Zielverzeichnis sicherzustellen. Der erste eindeutige Wert, der gefunden wird, wird zurückgegeben. Wenn alle Werte am Ziel bereits vorhanden sind, wird der Eintrag hinterlegt, und die Ursache wird in den Überwachungsprotokollen protokolliert. Für die Anzahl von Argumenten, die bereitgestellt werden können, gilt keine Obergrenze.


 - Diese Funktion muss sich auf der obersten Ebene befinden und kann nicht geschachtelt werden.
 - Diese Funktion kann nicht auf Attribute angewandt werden, die über eine Rangfolge für den Abgleich verfügen.     
 - Diese Funktion darf nur für die Erstellung von Einträgen verwendet werden. Legen Sie die Eigenschaft **Zuordnung anwenden** auf **Nur beim Erstellen von Objekten** fest.
 - Diese Funktion wird derzeit nur für die Benutzerbereitstellung von Workday in Active Directory und für die Benutzerbereitstellung von SuccessFactors in Active Directory unterstützt. Sie kann nicht für andere Bereitstellungsanwendungen verwendet werden. 
 - Die LDAP-Suche, die die Funktion *SelectUniqueValue* (Einzigartigen Wert auswählen) im lokalen Active Directory ausführt, escapet keine Sonderzeichen wie diakritische Zeichen. Wenn Sie eine Zeichenfolge wie "Jéssica Smith" übergeben, die ein Sonderzeichen enthält, treten Verarbeitungsfehler auf. Bitte verschachteln Sie die Funktion [Diakritische Zeichen normen](#normalizediacritics) (NormalizeDiacritics) wie im Beispiel unten gezeigt, um die Sonderzeichen zu normen. 


**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Mindestens zwei erforderlich, keine Obergrenze |String | Liste mit auszuwertenden Regeln für die Generierung eindeutiger Werte |

#### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generieren eines eindeutigen Werts für das Attribut userPrincipalName (UPN)
Beispiel: Basierend auf dem Vornamen, zweiten Vornamen und Nachnamen müssen Sie einen Wert für das UPN-Attribut generieren und die Eindeutigkeit im AD-Zielverzeichnis überprüfen, bevor Sie den Wert dem UPN-Attribut zuweisen.

**Ausdruck:** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Beispieleingabe/-ausgabe:**

* **EINGABE** (PreferredFirstName): „John“
* **EINGABE** (PreferredLastName): „Smith“
* **OUTPUT**: "John.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com nicht bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "J.Smith@contoso.com", wenn der UPN-Wert John.Smith@contoso.com bereits im Verzeichnis vorhanden ist
* **OUTPUT**: "Jo.Smith@contoso.com", wenn die beiden obigen UPN-Werte bereits im Verzeichnis enthalten sind



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktion:** SingleAppRoleAssignment([appRoleAssignments])

**Beschreibung:** Gibt eine einzelne appRoleAssignment aus der Liste aller appRoleAssignments zurück, die einem Benutzer für eine bestimmte Anwendung zugewiesen sind. Diese Funktion ist erforderlich, um das appRoleAssignments-Objekt in eine einzelne Namenszeichenfolge für eine Rolle zu konvertieren. Die bewährteste Methode ist, sicherzustellen, dass einem Benutzer jeweils nur eine App-Rollen-Zuweisung (appRoleAssignment) zugewiesen ist. Wenn mehrere Rollen zugewiesen sind, ist die für die Rolle zurückgegebene Zeichenfolge möglicherweise nicht vorhersehbar. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Erforderlich |String |**[appRoleAssignments]** -Objekt |

---
### <a name="split"></a>Split
**Funktion:** Split(Quelle, Trennzeichen)

**Beschreibung:** Unterteilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens in ein mehrwertiges Array.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |
| **Trennzeichen** |Erforderlich |String |Gibt das Zeichen zum Aufteilen der Zeichenfolge an (Beispiel: „,“). |

#### <a name="split-a-string-into-a-multi-valued-array"></a>Unterteilen einer Zeichenfolge in ein mehrwertiges Array
Beispiel: Sie müssen eine durch Trennzeichen getrennte Liste von Zeichenfolgen in ein Array unterteilen, das in ein mehrwertiges Attribut wie das PermissionSets-Attribut von Salesforce eingegeben werden kann. In diesem Beispiel wurde eine Liste von Berechtigungssätzen in extensionAttribute5 in Azure AD eingegeben.

**Ausdruck:** Split([extensionAttribute5], ",")

**Beispieleingabe/-ausgabe:** 

* **EINGABE:** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **AUSGABE:** ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**Funktion:**  StripSpaces(Quelle)

**Beschreibung:**  Entfernt alle Leerzeichen (" ") aus der Quellzeichenfolge.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Quelle** , der aktualisiert werden soll. |

---
### <a name="switch"></a>Schalter
**Funktion:**  Switch(Quelle, Standardwert, Schlüssel1, Wert1, Schlüssel2, Wert2, …)

**Beschreibung:** Wenn der **Quellwert** einem **Schlüssel** entspricht, wird der **Wert** für diesen **Schlüssel** zurückgegeben. Wenn der **Quellwert** keinem Schlüssel entspricht, wird der **Standardwert** zurückgegeben.  **Schlüssel-** und **Wertparameter** müssen immer paarweise angegeben werden. Die Funktion erwartet immer eine gerade Anzahl von Parametern. Die Funktion sollte nicht für referenzielle Attribute wie etwa „manager“ verwendet werden. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |**Source** , der aktualisiert werden soll. |
| **defaultValue** |Optional |String |Der Standardwert, der verwendet werden soll, wenn die Quelle mit keinem Schlüssel übereinstimmt. Kann eine leere Zeichenfolge ("") sein. |
| **key** |Erforderlich |String |**Schlüssel**, der mit dem **Quellwert** verglichen werden soll. |
| **value** |Erforderlich |String |Der Ersatzwert für die **Quelle** , die mit dem Schlüssel übereinstimmt. |

#### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersetzen eines Werts anhand eines vordefinierten Satzes von Optionen
Beispiel: Definieren Sie die Zeitzone des Benutzers anhand des Bundesstaatscodes, der in Azure AD gespeichert ist. Wenn der Bundesstaatscode keiner der vordefinierten Optionen entspricht, soll der Standardwert "Australien/Sydney" verwendet werden.

**Ausdruck:**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (state): „QLD“
* **AUSGABE**: „Australien/Brisbane“


---
### <a name="tolower"></a>ToLower
**Funktion:** ToLower(source, culture)

**Beschreibung:** Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Kleinbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

Wenn Sie für vorhandene Werte im Zielsystem die Kleinschreibung festlegen möchten, [aktualisieren Sie das Schema für Ihre Zielanwendung](./customize-application-attributes.md#editing-the-list-of-supported-attributes), und legen Sie die Eigenschaft „caseExact“ für das gewünschte Attribut auf TRUE fest. 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

#### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertieren des generierten userPrincipalName-Werts (UPN) in Kleinbuchstaben
Beispiel: Sie möchten den UPN-Wert generieren, indem Sie die Quellfelder „PreferredFirstName“ und „PreferredLastName“ verketten und alle Zeichen in Kleinbuchstaben konvertieren. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Beispieleingabe/-ausgabe:**

* **EINGABE** (PreferredFirstName): „John“
* **EINGABE** (PreferredLastName): „Smith“
* **AUSGABE**: „john.smith@contoso.com“


---
### <a name="toupper"></a>ToUpper
**Funktion:** ToUpper(source, culture)

**Beschreibung:** Konvertiert einen *source*-Zeichenfolgenwert mithilfe der angegebenen Kulturregeln in Großbuchstaben. Ohne Angabe der *culture*-Information wird die invariante Kultur verwendet.

Wenn Sie für vorhandene Werte im Zielsystem die Großschreibung festlegen möchten, [aktualisieren Sie das Schema für Ihre Zielanwendung](./customize-application-attributes.md#editing-the-list-of-supported-attributes) und stellen Sie die Eigenschaft „caseExact“ für das gewünschte Attribut auf „wahr“ („true“). 

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **Quelle** |Erforderlich |String |Normalerweise der Name des Attributs aus dem Quellobjekt |
| **culture** |Optional |String |Das Format für den Kulturnamen lautet basierend auf dem Standard RFC 4646 *languagecode2-country/regioncode2*, wobei *languagecode2* der aus zwei Buchstaben bestehende Sprachcode und *country/regioncode2* der aus zwei Buchstaben bestehende Subkulturcode ist. Beispiele sind „ja-JP“ für Japanisch (Japan) und „en-US“ für Englisch (USA). In Fällen, in denen kein aus zwei Buchstaben bestehender Sprachcode verfügbar ist, wird ein aus drei Buchstaben bestehender, von ISO 639-2 abgeleiteter Code verwendet.|

---
### <a name="word"></a>Wort
**Funktion:** Word(String,WordNumber,Delimiters)

**Beschreibung:**  Die Word-Funktion gibt ein in einer Zeichenfolge enthaltenes Wort auf der Grundlage von Parametern zurück, die die zu verwendenden Trennzeichen und die Nummer des zurückzugebenden Worts beschreiben. Alle Folgen von Zeichen in einer Zeichenfolge, die durch eines der in „delimiters“ enthaltenen Zeichen getrennt werden, werden als Wörter behandelt:

Wenn "WordNumber" < 1, wird eine leere Zeichenfolge zurückgegeben
Wenn "string" einen Nullwert hat, wird eine leere Zeichenfolge zurückgegeben
Falls „string“ weniger Wörter enthält als für „WordNumber“ angegeben, wird eine leere Zeichenfolge zurückgegeben. Dies gilt auch, wenn „string“ keine durch Trennzeichen identifizierten Wörter enthält.

**Parameter:** 

| Name | Erforderlich/wiederholt | type | Notizen |
| --- | --- | --- | --- |
| **String** |Erforderlich |Mehrwertiges Attribut |Eine Zeichenfolge, aus der ein Wort zurückgegeben werden soll.|
| **WordNumber** |Erforderlich | Integer | Eine Zahl, die angibt, welche Wortnummer zurückgegeben werden soll|
| **delimiters** |Erforderlich |String| Eine Zeichenfolge, die das/die Trennzeichen darstellt, das/die zum Identifizieren von Wörtern verwendet werden soll(en)|

**Beispiel:** 
`Word("The quick brown fox",3," ")`

Gibt „brown“ zurück

`Word("This,string!has&many separators",3,",!&#")`

Gibt „has“ zurück

---

## <a name="examples"></a>Beispiele
Dieser Abschnitt enthält weitere Beispiele für die Verwendung von Ausdrucksfunktionen. 

### <a name="strip-known-domain-name"></a>Entfernen eines bekannten Domänennamens
Sie müssen einen bekannten Domänennamen aus der E-Mail-Adresse eines Benutzers entfernen, um einen Benutzernamen zu erhalten. Wenn die Domäne beispielsweise "contoso.com" lautet, können Sie den folgenden Ausdruck verwenden:

**Ausdruck:**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (mail): "john.doe@contoso.com"
* **AUSGABE**: "john.doe"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generieren eines Benutzeralias durch Verketten von Teilen des Vor- und Nachnamens
Sie müssen einen Benutzeraliasnamen generieren, indem Sie die ersten drei Buchstaben des Vornamens und die ersten fünf Buchstaben des Nachnamens des Benutzers verwenden.

**Ausdruck:**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Beispieleingabe/-ausgabe:** 

* **EINGABE** (givenName): „John“
* **EINGABE** (surname): „Doe“
* **AUSGABE**:  „JohDoe“


## <a name="related-articles"></a>Verwandte Artikel
* [Automatisieren der Bereitstellung/Bereitstellungsaufhebung von Benutzern für SaaS-Apps](../app-provisioning/user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](../app-provisioning/customize-application-attributes.md)
* [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
* [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Kontobereitstellungsbenachrichtigungen](../app-provisioning/user-provisioning.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)

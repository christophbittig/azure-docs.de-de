---
title: 'EDIFACT CONTRL: Bestätigungen und Fehlercodes'
description: Erfahren Sie mehr über CONTRL-Bestätigungen und -Fehlercodes für EDIFACT-Nachrichten in Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/25/2021
ms.openlocfilehash: 056538b5a6b52fcae646f5f03c6e39c8fce6429f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768076"
---
# <a name="contrl-acknowledgments-and-error-codes-for-edifact-messages-in-azure-logic-apps"></a>CONTRL-Bestätigungen und -Fehlercodes für EDIFACT-Nachrichten in Azure Logic Apps

Sie können in Azure Logic Apps Workflows erstellen, die EDIFACT-Nachrichten für die EDI-Kommunikation (Electronic Data Interchange) verarbeiten, wenn Sie **EDIFACT**-Vorgänge verwenden. Beim EDI-Messaging geben Bestätigungen den Status der Verarbeitung eines EDI-Austauschs an. Beim Empfang eines Austauschs kann die [**EDIFACT-Decodierungsaktion**](logic-apps-enterprise-integration-edifact-decode.md) Bestätigungen mehrerer Typen an den Absender zurückgeben. Dies hängt davon ab, welche Bestätigungstypen aktiviert sind und welche Überprüfungsebene angegeben wurde.

In diesem Thema finden Sie eine kurze Übersicht über die CONTRL-Bestätigung von EDIFACT, einschließlich der Segmente von CONTRL-Bestätigungen bei einem Austausch und der in diesen Segmenten verwendeten Fehlercodes.

## <a name="contrl-as-technical-and-functional-acknowledgment"></a>CONTRL als technische und Funktionsbestätigung

Die CONTRL-Bestätigung (ACK) dient als technische *und* als Funktionsbestätigung für EDIFACT-codierte Nachrichten. Die vollständige CONTRL-Nachricht dient als Funktionsbestätigung, während Abschnitte in der Funktionsbestätigung für die technische Bestätigung wiederverwendet werden. Wenn Sie z. B. sowohl technische als auch Funktionsbestätigungen in den Vereinbarungseigenschaften für den sendenden Partner oder in den globalen Eigenschaften auswählen, generiert die **EDIFACT-Decodierungsaktion** zwei CONTRL-Nachrichten: eine technische CONTRL-Bestätigung und eine CONTRL-Funktionsbestätigung. Die CONTRL-Bestätigung entspricht dem Schema für **EFACT_<*Versionsnummer*>_CONTRL.xsd**.

> [!NOTE]
> Bei der Verarbeitung von EDIFACT-Nachrichten in Azure Logic Apps wird eine CONTRL-Nachricht als Antwort auf einen empfangenen Austausch gesendet, der nur CONTRL-Nachrichten enthält: zur Bestätigung, Annahme oder Ablehnung. In Logic Apps wird keine CONTRL-Nachricht als Antwort auf einen empfangenen Austausch gesendet, der CONTRL-Nachrichten enthält. 
>
> Fehler in empfangenen CONTRL-Nachrichten müssen auf andere Weise als durch eine CONTRL-Nachricht gemeldet werden. Enthält ein Austausch neben Datennachrichten auch CONTRL-Nachrichten, wird die in Reaktion auf diesen Austausch generierte CONTRL-Nachricht so generiert, als ob keine CONTRL-Nachrichten im empfangenen Austausch enthalten sind.

Als technische Bestätigung gibt die CONTRL-Nachricht an, dass der Austauschempfänger den Betreffaustausch sowie Folgendes empfangen hat:

* Überprüfte Teile des Austauschs, um die Syntaxgenauigkeit der Datenelemente zu bestätigen, die in das Berichtssegment der UCI-Austauschantwort kopiert wurden
* Übernahme der Verantwortung für die Benachrichtigung des Absenders über die Annahme oder Ablehnung der anderen Teile im Austausch
* Ergreifen angemessener Maßnahmen zur Benachrichtigung des Absenders

> [!NOTE]
> Eine technische CONTRL-Bestätigung meldet den Status `Rejected` nur, wenn die eingehende EDIFACT-Nachricht ein Duplikat ist oder der Umschlag Fehler enthält, z. B. ein Problem mit dem Zeichensatz. EDIFACT meldet den Status `Interchange accepted with errors` nicht in einer technischen CONTRL-Bestätigung, wie dies in X12 über das Feld TA104 in einer TA1-Bestätigung geschieht. Wenn die EDIFACT-Nachricht teilweise akzeptiert wird, meldet die technische CONTRL-Bestätigung den Status `Accepted`. In einigen Szenarien meldet die CONTRL-Bestätigung auch dann den Status `Accepted`, wenn ein Teil der Nachricht abgelehnt wurde. In diesen Szenarien kann das UCI5-Element den Fehler melden.

Als Funktionsbestätigung meldet die CONTRL-Nachricht den Status (akzeptiert oder abgelehnt) des empfangenen Austauschs, der Gruppe oder der Nachricht, einschließlich aller Fehler oder nicht unterstützten Funktionen. Die Nachricht gibt auch Folgendes über den Austauschempfänger an:

* Die referenzierten Stufen des bestätigten Austauschs wurden empfangen.
* Es wurde bestätigt, dass keine schwerwiegenden Syntaxfehler auf der bestätigten referenzierten Stufen eine weitere Verarbeitung des Austauschs verhindern.
* Es wurde bestätigt, dass alle bestätigten Teile der Dienstsegmente semantisch richtig sind (wenn keine Fehler gemeldet wurden).
* Es wird den in den bestätigten referenzierten Stufen der Dienstsegmente angeforderten Aktionen entsprochen.
* In den folgenden Fällen wurde die Verantwortung für die Benachrichtigung des Absenders mit anderen Mitteln als dem Senden einer CONTRL-Nachricht akzeptiert:

  * Syntaktische oder semantische Fehler wurden später im relevanten Teil gefunden.
  * Der Teil kann aus einem anderen Grund nicht verarbeitet werden, nachdem der Teil in einer übermittelten CONTRL-Nachricht bestätigt wurde.

* Es werden angemessene Maßnahmen ergriffen, um sicherzustellen, dass solche Fehler gefunden werden und der Absender benachrichtigt wird.

Eine Ablehnung impliziert Folgendes für den Empfänger:

* Der Austausch oder relevante Teile davon können aus den in der CONTRL-Nachricht angegebenen Gründen nicht bestätigt werden.
* Es werden keine weiteren Maßnahmen für Geschäftsinformationen unternommen, die im abgelehnten Teil des Betreffaustauschs enthalten sind.

## <a name="contrl-technical-ack-segments"></a>Segmente in technischen CONTRL-Bestätigungen

Wenn Sie die Option zum Generieren einer technischen Bestätigung in einer EDIFACT-Vereinbarung auswählen oder das Nachrichtenfeld **UNB9** auf `2` festgelegt ist, wird eine CONTRL-Nachricht als technische Bestätigung generiert, um die Ergebnisse vom Empfang des Austauschs zu melden.

In der folgenden Tabelle sind die Segmente in technischen CONTRL-Bestätigungen bei einem Austausch beschrieben. Dabei gelten die folgenden Definitionen:

* V = Verbindlich
* O = Optional

| Name | Obligatorisch oder optional | BESCHREIBUNG |
|------|-----------------------|-------------|
| UNH-Nachrichtenheader | M | Das Nachrichtenheadersegment (UNH) identifiziert die Nachricht und gibt sie an. |
| UCI-Austauschantwort | M | Identifiziert den Austausch und gibt die Art des Austauschempfangs an. Das UCI-Segment weist ein maximales Vorkommen von `1` auf und meldet daher den ersten Fehler, der in einem Kontrollsegment gefunden wurde. |
| UNT-Nachrichtennachspann | M | Im UCI5-Datenelement mit dem Namen `Syntax Error Code` wurde ein Fehler gemeldet. Bei EDIFACT-codierten Nachrichten gibt es keine `Accepted with errors`-Bedingung wie bei X12-codierten Austauschvorgängen. |
||||

### <a name="contrl-technical-ack-data-elements"></a>Datenelemente in technischen CONTRL-Bestätigungen

Die technische CONTRL-Bestätigung enthält folgende Datenelemente:

| Element | Name | Verwendung |
|---------|------|-------|
| UNH1 | Nachrichtenverweisnummer | - |
| UNH2 | Unterkomponenten der Nachrichten-ID | Unterkomponenten: <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Austauschkontrollnummer | Zugeordnet aus dem UNB5-Feld der empfangenen Nachricht |
| UCI2 | Austauschabsender | Zugeordnet aus dem UNB2-Feld der empfangenen Nachricht. <p><p>- Verbindlich: die erste Unterkomponente oder Identifikation <br>- Optional: die zweite Unterkomponente (Codequalifizierer) und die dritte Komponente (Adresse für Rückmeldung) |
| UCI3 | Austauschempfänger | Zugeordnet aus dem UNB3-Feld der empfangenen Nachricht. <p><p>- Verbindlich: die erste Unterkomponente oder Identifikation <br>- Optional: die zweite Unterkomponente (Codequalifizierer) |
| UCI4 | Aktionscode | Dieses Element ist verbindlich. Aktionscodes: <p><p>- 8, wenn der Austausch akzeptiert wird <br>- 7, wenn der Austausch akzeptiert wird, einige Transaktionssätze jedoch abgelehnt werden <br>- 4, wenn der Austausch wegen eines Fehlers im UNA- oder UNB-Segment abgelehnt wird |
| UCI5 | Syntaxfehlercode | Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung. |
| UCI6 | Dienstsegmenttag | Dieses Element ist bedingt optional und identifiziert das Segment mit der Fehlerbedingung im UCI5-Datenelement. |
| UCI7 | Datenelementidentifikation | Identifiziert die Datenelemente mit der Fehlerbedingung im UCI5-Datenelement. Unterkomponenten: <p><p>- Verbindlich: Position des fehlerhaften Datenelements im Segment <br>- Bedingt optional: Position des fehlerhaften Komponentendatenelements im Segment und Vorkommen fehlerhafter Datenelemente im Segment. |
| UCI8 | - | - |
| UNT1 | Anzahl der Segmente | - |
| UNT2 | Nachrichtenverweisnummer | - |
||||

## <a name="contrl-functional-ack-segments"></a>Segmente in CONTRL-Funktionsbestätigungen

Wenn Sie die Option zum Generieren einer Funktionsbestätigung in einer EDIFACT-Vereinbarung auswählen oder das Nachrichtenfeld **UNB9** auf `1` festgelegt ist, wird eine CONTRL-Nachricht als Funktionsbestätigung generiert, um die Ergebnisse der Syntaxüberprüfung für den Austausch zu melden.

In der folgenden Tabelle sind die Segmente in CONTRL-Funktionsbestätigungen bei einem Austausch beschrieben. Dabei gelten die folgenden Definitionen:

* V = Verbindlich
* O = Optional

| Name | Obligatorisch oder optional | BESCHREIBUNG |
|------|-----------------------|-------------|
| UNH-Nachrichtenheader | M | Das Nachrichtenheadersegment (UNH) identifiziert die Nachricht und gibt sie an. |
| UCI-Segment | M | Identifiziert den Austausch, gibt den Status für den Austauschempfang an und enthält Verweise auf die UNA-, UNB- und UNZ-Segmente im empfangenen Austausch. Das UCI-Segment weist ein maximales Vorkommen von `1` auf und meldet daher den ersten Fehler, der in einem Kontrollsegment gefunden wurde. |
| UCF-Segment | V, wenn das UNG-Segment vorhanden ist | Identifiziert ein Gruppensegment, das durch den UNG-Header und den UNE-Nachspann gekapselt wird, und gibt die Art eventueller Fehler an. |
| UCM-Segment | M | Identifiziert ein Nachrichtensegment, das durch den UNH-Header und den UNT-Nachspann gekapselt wird, und gibt Art eventueller Fehler an. |
| UCS-Segment | M | Identifiziert einen Transaktionssatz und gibt die Art eventueller Fehler an. |
| UCD-Segment | O, bedingt | Identifiziert ein fehlerhaftes zusammengesetztes Datenelement oder Komponentendatenelement und gibt die Art des Fehlers an. |
| UNT-Nachrichtennachspann | M | Enthält eine empfangene CONTRL-Funktionsbestätigung nur UNH-, UCI- und UNT-Segmente, verarbeitet die EDIReceive-Pipeline die Bestätigung als technische CONTRL-Empfangsbestätigung. Auf einer Berichtsebene kann jede Instanz eines Segments nur einen Fehler melden, etwa bei den Segmenten UCI, UCF, UCM, UCS und UCD. |
||||

### <a name="sg-loops-and-contrl-functional-ack-structure"></a>SG-Schleifen und Struktur von CONTRL-Funktionsbestätigungen

Abhängig davon, ob der empfangene Austausch Gruppen enthält, unterscheidet sich die Struktur der CONTRL-Funktionsbestätigung.

* Enthält der Austausch eine Gruppe, enthält die Bestätigung ein UCF-Segment pro Gruppe. Jedes UCF-Segment enthält ein UCM-Segment pro Nachricht. Jedes UCM-Segment enthält ebenfalls eine Reihe kombinierter UCS- und UCD-Segmente.

  Das XML-Format der Bestätigungsnachricht enthält die folgenden Schleifenelemente:

  * Ein SG3Loop-Element, das jedes UCF-Segment kapselt
  * Ein SG4Loop-Element, das jedes UCM-Element kapselt
  * Ein SG5Loop-Element, das jedes Paar von UCS- und UCD-Element kapselt

  SG-Schleifentags sind im nativen EDI-Nachrichtenformat nicht enthalten.

* Enthält der Austausch keine Gruppen, enthält die Bestätigung keine UCF-Segmente. Stattdessen enthält die Bestätigung ein UCM-Segment pro Nachricht. Jedes UCM-Segment enthält eine Reihe kombinierter UCS- und UCD-Segmente.

* Das XML-Format der Bestätigungsnachricht enthält die folgenden Schleifenelemente:

  * Ein SG1Loop-Element, das jedes UCM-Segment kapselt
  * Ein SG2Loop-Element, das jedes Paar von UCS- und UCD-Element kapselt

  Wie bei Austauschvorgängen, die Gruppen enthalten, sind die SG-Tags im nativen Bestätigungsformat nicht enthalten.

Gemäß dem Standard und der branchenüblichen Verwendung werden SG1/SG4-Schleifen bei akzeptierten Transaktionssätzen nicht erwartet. Um die Einhaltung der Standards zu unterstützen, können Sie jedoch die Generierung von SG1/SG4-Schleifen erzwingen, indem Sie die folgenden Schritte ausführen:
 
1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto.
1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Vereinbarungen** aus.
1. Öffnen Sie Ihre EDIFACT-Vereinbarung, und wählen Sie **Sendeeinstellungen** aus.
1. Wählen Sie unter **Bestätigung** die Option **Bestätigung (CONTRL)** aus. Nun können Sie **SG1/SG4-Schleife für akzeptierte Transaktionssätze generieren** auswählen.

   Wenn dieses Kontrollkästchen aktiviert ist, generiert die Empfangspipeline SG1/SG4-Schleifen unabhängig davon, ob der Transaktionssatz akzeptiert oder abgelehnt wurde. Andernfalls werden diese Schleifen nur bei fehlerhaften Transaktionssätzen generiert, bei denen UCM5 nicht `7` ist.

### <a name="contrl-functional-ack-data-elements"></a>Datenelemente in CONTRL-Funktionsbestätigungen

Die CONTRL-Nachricht enthält mehrere verbindliche Datenelemente, die aus dem empfangenen Austausch kopiert werden. Wenn das Datenelement im Austausch fehlt oder syntaktisch ungültig ist, kann keine syntaktisch gültige CONTRL-Nachricht generiert werden. Der Fehler muss dann auf andere Weise gemeldet werden.

Die CONTRL-Funktionsbestätigung enthält folgende Datenelemente:

| Element | Name | Verwendung |
|---------|------|-------|
| UNH1 | Nachrichtenverweisnummer | - |
| UNH2 | Unterkomponenten der Nachrichten-ID | Unterkomponenten: <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Austauschkontrollnummer | Zugeordnet aus dem UNB5-Feld der empfangenen Nachricht |
| UCI2 | Austauschabsender | Zugeordnet aus dem UNB2-Feld der empfangenen Nachricht. <p><p>- Verbindlich: die erste Unterkomponente oder Identifikation <br>- Optional: die zweite Unterkomponente (Codequalifizierer) und die dritte Komponente (Adresse für Rückmeldung) |
| UCI3 | Austauschempfänger | Zugeordnet aus dem UNB3-Feld der empfangenen Nachricht. <p><p>- Verbindlich: die erste Unterkomponente oder Identifikation <br>- Optional: die zweite Unterkomponente (Codequalifizierer) |
| UCI4 | Aktionscode | Dieses Element ist verbindlich. Aktionscodes: <p><p>- 8, wenn der Austausch akzeptiert wird <br>- 7, wenn der Austausch akzeptiert wird, einige Transaktionssätze jedoch abgelehnt werden <br>- 4, wenn der Austausch wegen eines Fehlers im UNA- oder UNB-Segment abgelehnt wird |
| UCI5 | Syntaxfehlercode | Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung. |
| UCI6 | Dienstsegmenttag | Dieses Element ist bedingt optional und identifiziert das Segment mit der Fehlerbedingung im UCI5-Datenelement. |
| UCI7 | Datenelementidentifikation | Identifiziert die Datenelemente mit der Fehlerbedingung im UCI5-Datenelement. Unterkomponenten: <p><p>- Verbindlich: Position des fehlerhaften Datenelements im Segment <br>- Bedingt optional: Position des fehlerhaften Komponentendatenelements im Segment und Vorkommen fehlerhafter Datenelemente im Segment. |
| UCI8 | - | - |
| UCF1 | Gruppenverweisnummer | Dieses Element ist verbindlich und wird über das UNG5-Feld in der empfangenen Nachricht zugeordnet. |
| UCF2 | Identifikation des Anwendungsabsenders | Dieses Element ist bedingt optional und wird über das UNG2-Feld in der empfangenen Nachricht zusammen mit Unterkomponenten zugeordnet. |
| UCF3 | Identifikation des Anwendungsempfängers | Dieses Element ist bedingt optional und wird über das UNG3-Feld in der empfangenen Nachricht zusammen mit Unterkomponenten zugeordnet. |
| UCF4 | Aktionscodes | Dieses Element ist verbindlich, und der Code gilt für diese Ebene und alle niedrigeren Ebenen. Aktionscodes: <p><p>- 7, wenn der Austausch akzeptiert wird <br>- 4, wenn der Austausch wegen eines Fehlers im UNA- oder UNB-Segment abgelehnt wird |
| UCF5 | Syntaxfehler, codiert | Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung in der Gruppe. |
| UCF6 | Dienstsegmenttag | Dieses Element ist bedingt optional und identifiziert das fehlerhafte Segment in der Gruppe. |
| UCF7 | Datenelementidentifikation | Gibt die Datenelemente an, in denen die im UCF5-Datenelement angegebene Fehlerbedingung vorliegt. Unterkomponenten: <p><p>- Verbindlich: Position des fehlerhaften Datenelements im Segment und Vorkommen fehlerhafter Datenelemente im Segment <br>- Bedingt optional: Position des fehlerhaften Komponentendatenelements im Segment |
| UCM1 | Nachrichtenverweisnummer| Dieses Element ist verbindlich und wird über das UNH1-Feld in der empfangenen Nachricht zugeordnet. |
| UCM2 | Nachrichten-ID | Dieses Element ist bedingt optional und wird über das UNH2-Feld in der empfangenen Nachricht zusammen mit Unterkomponenten zugeordnet. |
| UCM3 | Aktionscodes | Dieses Element ist verbindlich, und der Code gilt für diese Ebene und alle niedrigeren Ebenen. Aktionscodes: <p><p>- 7, wenn der Austausch akzeptiert wird <br>- 4, wenn der Austausch wegen eines Fehlers im UNA- oder UNB-Segment abgelehnt wird |
| UCM4 | Syntaxfehler, codiert | Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung in der Gruppe. |
| UCM5 | Dienstsegmenttag | Dieses Element ist bedingt optional und identifiziert das UNH- oder UNT-Segment im Fehler. |
| UCM7 | Datenelementidentifikation | Gibt die Datenelemente an, in denen die im UCM5-Datenelement angegebene Fehlerbedingung vorliegt. Unterkomponenten: <p><p>- Verbindlich: Position des fehlerhaften Datenelements im Segment und Vorkommen fehlerhafter Datenelemente im Segment <br>- Bedingt optional: Position des fehlerhaften Komponentendatenelements im Segment |
| UCS1 | Segmentposition im Nachrichtentext | Dieses Element ist verbindlich und gibt die Position des fehlerhaften Segments an, beginnend mit UNH als `1`. Um zu melden, dass ein Segment fehlt, gibt dieser Wert die numerische Position des letzten Segments an, das vor der Position verarbeitet wurde, an der das fehlende Segment erwartet wird. Um eine fehlende Segmentgruppe zu melden, wird das erste Segment in der Gruppe als nicht vorhanden gemeldet. |
| UCS2 | Syntaxfehlercode | Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung in der Gruppe. |
| UCD1 | Syntaxfehlercode| Dieses Element ist bedingt optional und identifiziert ggf. die Fehlerbedingung in der Gruppe. <p><p>**Hinweis:** Bei einem Fehler bei der XSD-Überprüfung meldet das UCD1-Datenelement den Codewert `12, Invalid Value`. |
| UCD2 | Datenelementidentifikation | Gibt die Datenelemente an, in denen die im UCD1-Datenelement angegebene Fehlerbedingung vorliegt. Unterkomponenten: <p><p>- Verbindlich: Position des fehlerhaften Datenelements im Segment und Vorkommen fehlerhafter Datenelemente im Segment <br>- Bedingt optional: Position des fehlerhaften Komponentendatenelements im Segment |
| UNT1 | Anzahl der Segmente | - |
| UNT2 | Nachrichtenverweisnummer | - |
||||

## <a name="contrl-ack-error-codes"></a>Fehlercodes in CONTRL-Bestätigungen

Diese Fehler gelten auf Austausch-, Gruppen-, Nachrichten- und Datenebene. Beim Auftreten eines unterstützten Fehlers wird der gesamte Austausch, die Gruppe oder der Transaktionssatz zurückgewiesen. Bei EDIFACT-codierten Nachrichten gibt es keine `Accepted with errors`-Bedingung wie bei X12-codierten Nachrichten.

### <a name="standard-edifact-contrl-ack-error-codes"></a>Standardfehlercodes in EDIFACT-CONTRL-Bestätigungen

In der folgenden Tabelle sind die unterstützten Fehlercodes gemäß EDIFACT-Spezifikation aufgeführt, die im UCI5-Feld der CONTRL-Bestätigung für die EDIFACT-Nachrichtenverarbeitung in Azure Logic Apps verwendet werden.

| Fehlercode | Bedingung | Ursache | Unterstützt? |
|------------|-----------|-------|------------|
| 2 | Syntaxversion oder -ebene nicht unterstützt. | Benachrichtigung, dass die Syntaxversion oder Ebene vom Empfänger nicht unterstützt wird | Nein |
| 7 | Austauschempfänger ist nicht der tatsächliche Empfänger. | Benachrichtigung, dass sich der Austauschempfänger (S003) vom tatsächlichen Empfänger unterscheidet | Nein |
| 12 | Ungültiger Wert. | Benachrichtigung, dass der Wert eines eigenständigen Datenelements, eines zusammengesetzten Datenelements oder eines Komponentendatenelements nicht mit den relevanten Spezifikationen für den Wert kompatibel ist. | Ja |
| 13 | Missing | Benachrichtigung, dass ein verbindlicher oder auf andere Weise erforderlicher Dienst oder ein Benutzersegment, ein Datenelement, ein zusammengesetztes Datenelement oder ein Komponentendatenelement fehlt | Ja |
| 14 | Wert wird an dieser Position nicht unterstützt. | Benachrichtigung, dass der Empfänger die Verwendung des betreffenden Werts eines angegebenen eigenständigen Datenelements, eines zusammengesetzten Datenelements oder eines Komponentendatenelements an der Position nicht unterstützt, an der es verwendet wird. Der Wert kann gemäß den relevanten Spezifikationen gültig sein und wird eventuell an einer anderen Position unterstützt. | Nein |
| 15 | An dieser Position nicht unterstützt. | Benachrichtigung, dass der Empfänger die Verwendung des Segmenttyps, des eigenständigen Datenelementtyps, des zusammengesetzten Datenelements oder des Komponentendatenelements an der angegebenen Position nicht unterstützt. | Ja |
| 16 | Zu viele Bestandteile. | Benachrichtigung, dass das angegebene Segment zu viele Datenelemente enthielt, oder dass das angegebene zusammengesetzte Datenelement zu viele Komponentendatenelemente enthielt. | Ja |
| 17 | Keine Vereinbarung. | Es ist keine Vereinbarung vorhanden, die den Empfang eines Austauschs, einer Gruppe, einer Nachricht oder eines Pakets mit dem Wert des angegebenen eigenständigen Datenelements, des zusammengesetzten Datenelements oder des Komponentendatenelements zulässt. | Nein |
| 18 | Unbekannter Fehler | Benachrichtigung, dass ein Fehler erkannt wurde, die Art des Fehlers wird jedoch nicht gemeldet. | Nein |
| 19 | Ungültige Dezimalschreibweise. | Benachrichtigung, dass das als Dezimalschreibweise in UNA angegebene Zeichen ungültig ist, dass die in einem Datenelement verwendete Dezimalschreibweise nicht konsistent mit der in UNA angegebenen ist. | Nein |
| 20 | Zeichen als Dienstzeichen ungültig. | Benachrichtigung, dass ein in UNA angegebenes Zeichen als Dienstzeichen ungültig ist. | Nein |
| 21 | Ungültige(s) Zeichen. | Benachrichtigung, dass mindestens ein im Austausch verwendetes Zeichen kein gültiges Zeichen wie durch den im UNB-Segment angegebenen Syntaxbezeichner definiert ist. Das ungültige Zeichen ist Teil der Ebene, auf die verwiesen wird, oder es folgt unmittelbar auf den Bezeichnerteil des Austauschs. | Ja |
| 22 | Ungültige(s) Dienstzeichen. | Benachrichtigung, dass mindestens ein im Austausch verwendetes Dienstzeichen kein gültiges Dienstzeichen wie im UNA-Segment angegeben oder kein Standarddienstzeichen ist. Wenn der Code im UCS- oder UCD-Segment verwendet wird, folgte das ungültige Zeichen unmittelbar auf den identifizierten Teil des Austauschs. | Nein |
| 23 | Unbekannter Austauschabsender. | Benachrichtigung, dass der Austauschabsender (S002) unbekannt ist. | Nein |
| 24 | Zu alt. | Benachrichtigung, dass der empfangene Austausch oder die Gruppe älter als ein in einem IA oder durch einen Empfänger festgelegter Grenzwert ist. | Nein |
| 25 | Testindikator wird nicht unterstützt. | Benachrichtigung, dass die Testverarbeitung für den angegebenen Austausch, die Gruppe, die Nachricht oder das Paket nicht ausgeführt werden kann. | Nein |
| 26 | Duplikat erkannt. | Benachrichtigung, dass ein mögliches Duplikat eines zuvor empfangenen Austauschs, einer Gruppe, einer Nachricht oder eines Pakets erkannt wurde. Die frühere Übertragung wurde möglicherweise zurückgewiesen. | Ja |
| 27 | Sicherheitsfunktion nicht unterstützt. | Benachrichtigung, dass eine Sicherheitsfunktion, die sich auf die Ebene oder das Datenelement bezieht, auf die oder das verwiesen wird, nicht unterstützt wird. | Nein |
| 28 | Verweise stimmen nicht überein. | Benachrichtigung, dass der Kontrollverweis im UNB-, UNG-, UNH-, UNO-, USH- oder USD-Segment nicht mit dem Kontrollverweis im UNZ-, UNE-, UNT-, UNP-, UST- oder USU-Segment übereinstimmt. | Nein |
| 29 | Kontrollanzahl stimmt nicht mit der Anzahl der empfangenen Instanzen überein. | Benachrichtigung, dass die Anzahl der Gruppen, Nachrichten oder Segmente nicht mit der im UNZ-, UNE-, UNT- oder UST-Segment angegebenen Anzahl übereinstimmt oder dass die Länge eines Objekts bzw. die Länge verschlüsselter Daten nicht mit der im UNO-, UNP-, USD- oder USU-Segment angegebenen Länge übereinstimmt. | Ja |
| 30 | Mischung aus Gruppen und Nachrichten/Paketen. | Benachrichtigung, dass Gruppen mit Nachrichten oder Paketen außerhalb von Gruppen im Austausch gemischt wurden. | Nein |
| 31 | Mehrere Nachrichtentypen in der Gruppe. | Benachrichtigung, dass verschiedene Nachrichtentypen in einer funktionalen Gruppe enthalten sind. | Ja |
| 32 | Niedrigere Ebene leer. | Benachrichtigung für eine der folgenden Bedingungen: <p><p>- Der Austausch enthält keine Nachrichten, Pakete oder Gruppen. <br>- Eine Gruppe enthält keine Nachrichten oder Pakete. | Nein |
| 33 | Ungültiges Vorkommen außerhalb Nachricht, Paket oder Gruppe.|Benachrichtigung über ein ungültiges Segment oder Datenelement im Austausch, zwischen Nachrichten, zwischen Paketen oder zwischen Gruppen. Die Zurückweisung wird auf der darüber liegenden Ebene gemeldet. | Ja |
| 34 | Schachtelungsindikator unzulässig. | Benachrichtigung, dass ausdrückliche Schachtelung in einer Nachricht verwendet wurde, in der dies unzulässig ist. | Nein |
| 35 | Zu viele Datenelement oder Segmentwiederholungen. | Benachrichtigung, dass ein eigenständiges Datenelement, ein zusammengesetztes Datenelement oder ein Segment zu häufig wiederholt wird. | Ja |
| 36 | Zu viele Segmentgruppenwiederholungen. | Benachrichtigung, dass eine Segmentgruppe zu häufig wiederholt wurde. | Ja |
| 37 | Ungültiger Zeichentyp. | Benachrichtigung für eine der folgenden Bedingungen: <p><p>- Mindestens ein numerisches Zeichen wird in einem alphabetischen Datenelement (Komponente) verwendet. <br>- Mindestens ein alphabetisches Zeichen wird in einem numerischen Datenelement (Komponente) verwendet. | Ja |
| 38 | Fehlende Stelle vor einem Dezimaltrennzeichen. | Benachrichtigung, dass einem Dezimaltrennzeichen nicht mindestens eine Ziffer vorangestellt wurde. | Ja |
| 39 | Datenelement zu lang. | Benachrichtigung, dass die Länge des empfangenen Datenelements die in der Datenelementbeschreibung angegebene maximale Länge überschritten hat. | Ja |
| 40 | Datenelement zu kurz. | Benachrichtigung, dass die Länge des empfangenen Datenelements kürzer als die in der Datenelementbeschreibung angegebene minimale Länge ist. | Ja |
| 41 | Dauerhafter Netzwerkkommunikationsfehler. | Benachrichtigung, dass ein dauerhafter Fehler durch das Kommunikationsnetzwerk gemeldet wurde, das für die Übertragung des Austauschs verwendet wird. Eine erneute Übertragung eines identischen Austauschs mit den gleichen Parametern auf Netzwerkebene ist nicht erfolgreich. | Nein |
| 42 | Vorübergehender Netzwerkkommunikationsfehler. | Benachrichtigung, dass ein vorübergehender Fehler durch das Kommunikationsnetzwerk gemeldet wurde, das für die Übertragung des Austauschs verwendet wird. Eine erneute Übertragung eines identischen Austauschs ist möglicherweise erfolgreich. | Nein |
| 43 | Unbekannter Austauschempfänger. | Benachrichtigung, dass der Austauschempfänger einem Netzwerkanbieter nicht bekannt ist. | Nein |
| 45 | Nachfolgendes Trennzeichen | Benachrichtigung für eine der folgenden Bedingungen: <p><p>- Das letzte Zeichen vor dem Segmentabschlusszeichen ist ein Datenelementtrennzeichen, ein Trennzeichen für ein zusammengesetztes Datenelement oder ein Trennzeichen für ein wiederholtes Datenelement. <br>- Das letzte Zeichen vor einem Datenelementtrennzeichen ist ein Trennzeichen für ein zusammengesetztes Datenelement oder ein Trennzeichen für ein wiederholtes Datenelement. | Ja |
| 46 | Zeichensatz nicht unterstützt. | Benachrichtigung für eine der folgenden Bedingungen: <p><p>- Mindestens ein verwendetes Zeichen ist nicht in dem durch den Syntaxbezeichner definierten Zeichensatz enthalten. <br>- Der von der Escapesequenz für die Codeerweiterungstechnik identifizierte Zeichensatz wird vom Empfänger nicht unterstützt. | Ja |
| 47 | Umschlagfunktion nicht unterstützt. | Benachrichtigung, dass die vorliegende Umschlagstruktur durch den Empfänger nicht unterstützt wird. | Ja |
| 48 | Verletzung der Abhängigkeitsbedingung. | Benachrichtigung, dass eine Fehlerbedingung aufgrund einer Verletzung der Abhängigkeitsbedingung aufgetreten ist. | Nein |
|||||

### <a name="azure-logic-apps-contrl-ack-error-codes"></a>Fehlercodes in CONTRL-Bestätigungen in Azure Logic Apps

In der folgenden Tabelle sind die benutzerdefinierten Fehlercodes aufgeführt, die nicht in der EDIFACT-Spezifikation, aber im UCI5-Feld der CONTRL-Bestätigung für die EDIFACT-Nachrichtenverarbeitung enthalten sind und speziell in Azure Logic Apps verwendet werden.

| Fehlercode | Bedingung | Ursache |
|------------|-----------|-------|
| 70 | Transaktionssatz fehlt oder ungültiger Transaktionssatzbezeichner. | Benachrichtigung, dass der Transaktionssatzbezeichner fehlt oder ungültig ist. |
| 71 | Fehlende Übereinstimmung der Transaktionssatz- oder Gruppenkontrollnummer. | Benachrichtigung, dass die Transaktionssatz- oder Gruppenkontrollnummern nicht übereinstimmen. |
| 72 | Unbekannte Segment-ID. | Benachrichtigung, dass die Segment-ID nicht erkannt wurde. |
| 73 | XML nicht an der richtigen Position. | Benachrichtigung, dass ein Problem beim Serialisieren des XML-Stammelements aufgetreten ist. |
| 74 | Zu wenige Segmentgruppenwiederholungen. | Benachrichtigung, dass eine Segmentgruppe weniger häufig als erforderlich wiederholt wurde. |
| 75 | Zu wenige Segmentwiederholungen. | Benachrichtigung, dass ein Segment weniger häufig als erforderlich wiederholt wurde. |
| 76 | Zu wenige Datenelemente gefunden. | Benachrichtigung, dass nicht genügend Datenelemente gefunden wurden. |
||||

## <a name="next-steps"></a>Nächste Schritte

[Austauschen von EDIFACT-Nachrichten](logic-apps-enterprise-integration-edifact.md)

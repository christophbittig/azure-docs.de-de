---
title: 'X12: 997-Bestätigungen und -Fehlercodes'
description: Erfahren Sie mehr über 997-Funktionsbestätigungen und -Fehlercodes für X12-Nachrichten in Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: ec727997663286b732fe7bf1d00a50da2f706a0a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481390"
---
# <a name="997-functional-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>997-Funktionsbestätigungen und -Fehlercodes für X12-Nachrichten in Azure Logic Apps

Sie können in Azure Logic Apps Workflows erstellen, die X12-Nachrichten für die EDI-Kommunikation (Electronic Data Interchange) verarbeiten, wenn Sie **X12**-Vorgänge verwenden. Beim EDI-Messaging geben Bestätigungen den Status der Verarbeitung eines EDI-Austauschs an. Beim Empfang eines Austauschs kann die [**X12-Decodierungsaktion**](logic-apps-enterprise-integration-x12-decode.md) mehrere Typen von Bestätigungen an den Absender zurückgeben. Dies hängt davon ab, welche Bestätigungstypen aktiviert sind und welche Überprüfungsebene angegeben wurde.

Beispielsweise meldet der Empfänger den Status der Überprüfung von Funktionsgruppenheader (GS) und Funktionsgruppennachspann (GE) in der empfangenen X12-codierten Nachricht, indem er eine *997-Funktionsbestätigung (ACK)* zusammen mit jedem Fehler sendet, der während der Verarbeitung aufgetreten ist. Die **X12-Decodierungsaktion** generiert immer eine 4010-konforme 997-Funktionsbestätigung, während sowohl die [**X12-Codierungsaktion**](logic-apps-enterprise-integration-x12-encode.md) als auch die **X12-Decodierungsaktion** eine 5010-kompatible 997-Funktionsbestätigung überprüfen können.

Der Empfänger sendet die 997-Bestätigung in einem GS- (Funktionsgruppenheader) und GE-Umschlag (Funktionsgruppennachspann). Dieser GS- und GE-Umschlag unterscheidet sich jedoch nicht von den anderen Transaktionssätzen.

In diesem Thema finden Sie eine kurze Übersicht über die 997-Bestätigung von X12, einschließlich der Segmente von 997-Funktionsbestätigungen bei einem Austausch und der in diesen Segmenten verwendeten Fehlercodes. Weiterführende Informationen finden Sie in der folgenden Dokumentation:

* [X12: Technische TA1-Bestätigungen und Fehlercodes](logic-apps-enterprise-integration-x12-ta1-acknowledgment.md)
* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-x12.md)
* [Austauschen von EDIFACT-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-edifact.md)
* [Was ist Azure Logic Apps?](logic-apps-overview.md)
* [B2B-Unternehmensintegrationslösungen mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="997-ack-segments"></a>

## <a name="997-ack-segments"></a>Segmente in 997-Funktionsbestätigungen

In der folgenden Tabelle sind die Segmente in 997-Bestätigungen bei einem Austausch beschrieben. Dabei werden die folgenden Definitionen angewandt:

* V = Verbindlich
* O = Optional

| Position | Segment-ID | Name | Erforderliche Bezeichnung <br>(Erf. Bez.) | Maximale Nutzung | Schleifenwiederholung |
|----------|------------|------|--------------------------------------|-------------|-------------|
| 010 | ST | Transaktionssatzheader, für die Bestätigung | M | 1 | - |
| 020 | AK1 | Funktionsgruppen-Antwortheader | M | 1 | - |
| 030 | AK2 | Transaktionssatz-Antwortheader | O | 1 | 999.999 <br>(Schleifen-ID = AK2) |
| 040 | AK3 | Datensegmenthinweis | O | 1 | 999.999 <br>(Schleifen-ID = AK2 oder AK3) |
| 050 | AK4 | Datenelementhinweis| O | 9 9 | - |
| 060 | AK5 | Transaktionssatz-Antwortnachspann | M | 1 | - |
| 070 | AK9 | Funktionsgruppen-Antwortnachspann | M | 1 | - |
| 080 | SE | Transaktionssatznachspann für die Bestätigung | M | 1 | - |
|||||||

In den folgenden Abschnitten finden Sie weitere Informationen zu den einzelnen Bestätigungssegmenten. Die Segmente in den Schleifen AK2 bis AK5 liefern Informationen zu einem Fehler mit einem Transaktionssatz.

### <a name="ak1"></a>AK1

Das verbindliche Segment AK1 gibt die Funktionsgruppe an, die mit den folgenden Datenelementen bestätigt wird:

| Element | BESCHREIBUNG |
|---------|-------------|
| AK101 | Verbindlich, identifiziert die Funktionsgruppen-ID (GS01), die von der Funktionsgruppe bestätigt werden soll |
| AK102 | Verbindlich, identifiziert die Kontrollnummer der Gruppe (GS06 und GE02), die von der Funktionsgruppe bestätigt werden soll |
| AK103 | Optional, identifiziert die EDI-Implementierungsversion, die im GS08-Segment der ursprünglichen Transaktion gesendet wurde. AK103 unterstützt eine eingehende 5010-konforme 997-Bestätigung. |
|||

### <a name="ak2"></a>AK2

Das optionale Segment AK2 enthält eine Bestätigung für einen Transaktionssatz in der empfangenen Funktionsgruppe. Wenn mehrere AK2-Segmente vorhanden sind, werden sie als eine Reihe von Schleifen gesendet. Jede AK2-Schleife identifiziert einen Transaktionssatz in der empfangenen Reihenfolge. Eine AK2-Schleife enthält AK3-, AK4- und AK5-Segmente, sofern ein Transaktionssatz Fehler aufweist. Weitere Informationen finden Sie weiter unten in den Segmentbeschreibungen.

Das AK2-Segment identifiziert den Transaktionssatz mithilfe der folgenden Datenelemente:

| Element | BESCHREIBUNG |
|---------|-------------|
| AK201 | Verbindlich, identifiziert die Transaktionssatz-ID (ST01) des zu bestätigenden Transaktionssatzes |
| AK202 | Verbindlich, identifiziert die Transaktionssatz-Kontrollnummer (ST02 und SE02) des zu bestätigenden Transaktionssatzes |
| AK203 | Optional, identifiziert die EDI-Implementierungsversion, die im ST03-Segment der ursprünglichen Transaktion gesendet wurde. AK203 unterstützt eingehende 5010-konforme 997-Bestätigungen. |
|||

#### <a name="generate-ak2-segments"></a>Generieren von AK2-Segmenten

Sie können angeben, dass AK2-Segmente für *alle* akzeptierten oder abgelehnten Transaktionssätze oder *nur* für abgelehnte Transaktionssätze generiert werden. Andernfalls generiert Azure Logic Apps AK2-Schleifen *nur* für abgelehnte Transaktionssätze. Wenn eine Vereinbarung für den Austausch, auf den geantwortet wird, nicht aufgelöst wird, werden die Erstellungseinstellungen der 997-Funktionsbestätigung standardmäßig auf die Ausweichvereinbarungseinstellungen festgelegt und für akzeptierte Transaktionssätze keine AK2-Segmente generiert.

Damit Azure Logic Apps AK2-Segmente für akzeptierte Transaktionssätze generiert, bei denen AK501 == A ist, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihr Integrationskonto im Azure-Portal, und öffnen Sie dann das X12-Vereinbarungsartefakt zwischen Ihren X12-Handelspartnern.

1. Öffnen Sie den Bereich **Empfangseinstellungen**, und stellen Sie sicher, dass **FA erwartet** ausgewählt ist. Sie können dann **AK2-/IK2-Schleife einschließen** auswählen.

### <a name="ak3"></a>AK3

Das optionale AK3-Segment meldet Fehler in einem Datensegment und bestimmt dessen Position. Ein AK3-Segment wird für jedes Segment in einem Transaktionssatz mit einem oder mehreren Fehlern erzeugt. Wenn mehrere AK3-Segmente vorhanden sind, werden sie als eine Reihe von Schleifen mit einem Segment pro Schleife gesendet. Das AK3-Segment gibt die Position jedes fehlerhaften Segments an und meldet den Typ des Syntaxfehlers an der jeweiligen Position anhand der folgenden Datenelemente:

| Element | BESCHREIBUNG |
|---------|-------------|
| AK301 | Verbindlich, identifiziert das fehlerhafte Segment anhand seiner X12-Segment-ID, z. B. NM1. |
| AK302 | Verbindlich, identifiziert die Segmentnummer des fehlerhaften Segments. Das ST-Segment ist `1`, und jedes Segment erhöht den Segmentzähler um 1. |
| AK303 | Verbindlich, identifiziert eine gebundene Schleife, die von einem LS-Segment (Loop Start, Schleifenstart) und einem LE-Segment (Loop End, Schleifenende) eingeschlossen ist. AK303 enthält die Werte der LS- und LE-Segmente, die das fehlerhafte Segment gebunden haben. |
| AK304 | Optional, gibt den Code für den Fehler im Datensegment an. AK304 ist zwar optional, das Element ist jedoch erforderlich, wenn ein Fehler für das identifizierte Segment vorhanden ist. Informationen zu AK304-Fehlercodes finden Sie unter [Fehlercodes in 997-Bestätigungen: Datensegmenthinweis](#997-ack-error-codes). |
|||

### <a name="ak4"></a>AK4

Das optionale AK4-Segment meldet Fehler in einem Datensegment oder einer zusammengesetzten Datenstruktur und bestimmt die Position des Datenelements. Ein AK4-Segment wird gesendet, wenn das AK304-Datenelement `"8", "Segment has data element errors"` ist. Es kann innerhalb jedes AK3-Segments bis zu 99-mal wiederholt werden. Das AK4-Segment gibt die Position jedes fehlerhaften Datenelements bzw. jeder fehlerhaften zusammengesetzten Datenstruktur an und meldet den Typ des Syntaxfehlers an der jeweiligen Position anhand der folgenden vier Datenelemente:

| Element | BESCHREIBUNG |
|---------|-------------|
| AK401 | Verbindlich, ein zusammengesetztes Datenelement mit den folgenden Feldern: AK41.1, AK41.2 und AK41.3 <p><p>- AK401.1: identifiziert das Datenelement oder die zusammengesetzte Datenstruktur mit dem Fehler anhand seiner Nummer. Wenn beispielsweise das zweite Datenelement im Segment einen Fehler aufweist, hat AK401 den Wert `2`. <br>AK401.2: identifiziert den numerischen Zählerstand des Komponentendatenelements in einer fehlerhaften zusammengesetzten Datenstruktur. Wenn AK401 einen Fehler in einer Datenstruktur meldet, die nicht zusammengesetzt ist, wird AK401.2 nicht gewertet. <br>- AK41.3: Dieses optionale Feld ist die Position des sich wiederholenden Datenelements. AK41.3 unterstützt eingehende 5010-konforme 997-Bestätigungen. |
| AK402 | Optional, identifiziert die einfache X12-Datenelementnummer des fehlerhaften Elements. NM101 entspricht beispielsweise der einfachen X12-Datenelementnummer 98. |
| AK403 | Verbindlich, meldet den Fehler im identifizierten Element. Informationen zu AK403-Fehlercodes finden Sie unter [Fehlercodes in 997-Bestätigungen: Datenelementhinweis](#997-ack-error-codes). |
| AK404 | Optional, enthält eine Kopie des identifizierten fehlerhaften Datenelements. AK404 wird nicht verwendet, wenn der Fehler ein ungültiges Zeichen angibt. |
|||

### <a name="ak5"></a>AK5

Das AK5-Segment meldet, ob der im AK2-Segment bestimmte Transaktionssatz akzeptiert oder (unter Angabe von Gründen) abgelehnt wird. Das AK5-Segment ist verbindlich, wenn die optionale AK2-Schleife in die Bestätigung einbezogen ist. Das AK4-Segment gibt mit einem verbindlichen Datenelement den Status des Transaktionssatzes an und stellt anhand von ein bis fünf optionalen Datenelementen basierend auf der Syntaxbearbeitung des Transaktionssatzes Fehlercodes bereit.

| Element | BESCHREIBUNG |
|---------|-------------|
| AK501 | Verbindlich, gibt an, ob der identifizierte Transaktionssatz akzeptiert oder abgelehnt wurde. Informationen zu AK501-Fehlercodes finden Sie unter [Fehlercodes in 997-Bestätigungen: Transaktionsantwortnachspann](#997-ack-error-codes). |
| AK502–AK506 | Optional, gibt die Art des Fehlers an. Informationen zu AK502-Fehlercodes finden Sie unter [Fehlercodes in 997-Bestätigungen: Transaktionssatz-Antwortnachspann](#997-ack-error-codes). |
|||

### <a name="ak9"></a>AK9

Das verbindliche AK9-Segment gibt an, ob die im AK1-Segment bestimmte Funktionsgruppe akzeptiert oder (unter Angabe von Gründen) abgelehnt wird. Das AK9-Segment gibt den Status des Transaktionssatzes und die Art eventueller Fehler mithilfe von vier verbindlichen Datenelementen an. Das Segment gibt alle gemeldeten Fehler über ein bis fünf optionale Elemente an.

| Element | BESCHREIBUNG |
|---------|-------------|
| AK901 | Verbindlich, gibt an, ob die im AK1-Segment identifizierte Funktionsgruppe akzeptiert oder abgelehnt wurde. Informationen zu AK901-Fehlercodes finden Sie unter [Fehlercodes in 997-Bestätigungen: Funktionsgruppen-Antwortnachspann](#997-ack-error-codes). |
| AK902 | Verbindlich, gibt die Anzahl der Transaktionssätze im identifizierten Funktionsgruppennachspann (GE01) an. |
| AK903 | Verbindlich, gibt die Anzahl der empfangenen Transaktionsätze an. |
| AK904 | Verbindlich, gibt die Anzahl der Transaktionssätze an, die in der identifizierten Funktionsgruppe akzeptiert wurden. |
| AK905–AK909 | Optional, gibt ein bis fünf Fehler an, die in der identifizierten Funktionsgruppe gemeldet wurden. Informationen zu Fehlercodes in AK905–AK909 finden Sie unter [Fehlercodes in 997-Bestätigungen: Funktionsgruppen-Antwortnachspann](#997-ack-error-codes). |
|||

<a name="997-ack-error-codes"></a>

## <a name="997-ack-error-codes"></a>Fehlercodes in 997-Bestätigungen

In diesem Abschnitt werden die Fehlercodes behandelt, die in [Segmenten in 997-Bestätigungen](#997-ack-segments) verwendet werden.  In jeder Tabelle werden die unterstützten und nicht unterstützten Fehlercodes gemäß der X12-Spezifikation aufgeführt, die bei der X12-Nachrichtenverarbeitung in Azure Logic Apps verwendet werden.

### <a name="ak304-error-codes---data-segment-note"></a>AK304-Fehlercodes: Datensegmenthinweis

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die im Datenelement AK304 des AK3-Segments (Datensegmenthinweis) verwendet werden:

| Fehlercode | Bedingung | Unterstützt? |
|------------|-----------|------------|
| 1 | Unbekannte Segment-ID. | Ja |
| 2 | Unerwartetes Segment | Ja |
| 3 | Obligatorisches Segment fehlt | Ja |
| 4 | Schleife tritt häufiger auf als die zulässige Maximalanzahl | Ja |
| 5 | Segment überschreitet maximale Verwendung | Ja |
| 6 | Segment nicht im Transaktionssatz definiert | Ja |
| 7 | Sequenz des Segments nicht ordnungsgemäß | Ja |
| 8 | Datenelementfehler im Segment | Ja |
| 511 | Nachfolgendes Trennzeichen gefunden (benutzerdefinierter Code) | Ja |
||||

### <a name="ak403-error-codes----data-element-note"></a>AK403-Fehlercodes: Datenelementhinweis

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die im Datenelement AK403 des AK4-Segments (Datenelementhinweis) verwendet werden:

| Fehlercode | Bedingung | Unterstützt? |
|------------|-----------|------------|
| 1 | Obligatorisches Datenelement fehlt | Ja |
| 2 | Erforderliches Bedingungsdatenelement fehlt | Ja |
| 3 | Zu viele Datenelemente | Ja |
| 4 | Datenelement zu kurz | Ja |
| 5 | Datenelement zu lang | Ja |
| 6 | Ungültiges Zeichen in Datenelement | Ja |
| 7 | Ungültiger Codewert | Ja |
| 8 | Ungültiges Datum | Ja |
| 9 | Ungültige Zeit | Ja |
| 10 | Ausschlussbedingung verletzt | Ja |
||||

### <a name="ak501-error-codes---transaction-set-response-trailer"></a>AK501-Fehlercodes: Transaktionssatz-Antwortnachspann

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die im Datenelement AK501 des AK5-Segments (Transaktionssatz-Antwortnachspann) verwendet werden:

| Fehlercode | Bedingung | Unterstützt? |
|------------|-----------|------------|
| Ein | Akzeptiert | Ja |
| E | Akzeptiert, es wurden allerdings Fehler festgestellt | Ja <p><p>**Hinweis:** Keiner der Fehlercodes führt zum Status `E`. |
| M | Abgelehnt, Fehler beim Nachrichtenauthentifizierungscode (MAC) | Nein |
| P | Teilweise akzeptiert, mindestens ein Transaktionssatz wurde abgelehnt | Ja |
| R | Rejected (Abgelehnt) | Ja |
| W | Abgelehnt, Fehler bei Gültigkeitstests für Sicherung | Nein |
| X | Abgelehnt, Inhalt konnte nach Entschlüsselung nicht analysiert werden | Nein |
||||

### <a name="ak502-to-ak506-error-codes---transaction-set-response-trailer"></a>Fehlercodes AK502–AK506: Transaktionssatz-Antwortnachspann

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die in den Datenelementen AK502 bis AK506 des AK5-Segments (Transaktionssatz-Antwortnachspann) verwendet werden:

| Fehlercode | Bedingung | Unterstützt oder <br>korreliert mit AK501? |
|------------|-----------|-----------------------------------------|
| 1 | Transaktionssatz nicht unterstützt | Ja, R |
| 2 | Transaktionssatz-Nachspann fehlt | Ja, R |
| 3 | Transaktionssatz-Kontrollnummer im Vorspann und Nachspann stimmen nicht überein | Ja, R |
| 4 | Anzahl einbezogener Segmente stimmt nicht mit tatsächlicher Anzahl überein | Ja, R |
| 5 | Fehler in mehreren Segmenten | Ja, R |
| 6 | Fehlender oder ungültiger Transaktionssatzbezeichner | Ja, R |
| 7 | Fehlende oder ungültige Transaktionssatz-Kontrollnummer, möglicherweise ist eine doppelte Transaktionsnummer aufgetreten | Ja, R |
| 8 bis 27 | - | Nein |
||||

### <a name="ak901-error-codes---functional-group-response-trailer"></a>AK901-Fehlercodes: Funktionsgruppen-Antwortnachspann

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die im Datenelement AK901 des AK9-Segments (Funktionsgruppen-Antwortnachspann) verwendet werden:

| Fehlercode | Bedingung | Unterstützt oder <br>korreliert mit AK501? |
|------------|-----------|-----------------------------------------|
| Ein | Akzeptiert | Ja |
| E | Akzeptiert, es wurden allerdings Fehler festgestellt | Ja |
| M | Abgelehnt, Fehler beim Nachrichtenauthentifizierungscode (MAC) | Nein |
| P | Teilweise akzeptiert, mindestens ein Transaktionssatz wurde abgelehnt | Ja |
| R | Rejected (Abgelehnt) | Ja |
| W | Abgelehnt, Fehler bei Gültigkeitstests für Sicherung | Nein |
| X | Abgelehnt, Inhalt konnte nach Entschlüsselung nicht analysiert werden | Nein |
||||

### <a name="ak905-to-ak909-error-codes---functional-group-response-trailer"></a>Fehlercodes AK905–AK909: Funktionsgruppen-Antwortnachspann

In der folgenden Tabelle werden die Fehlercodes aufgelistet, die in den Datenelementen AK905 bis AK909 des AK9-Segments (Funktionsgruppen-Antwortnachspann) verwendet werden:

| Fehlercode | Bedingung | Unterstützt oder <br>korreliert mit AK501? |
|------------|-----------|-----------------------------------------|
| 1 | Funktionsgruppe nicht unterstützt | Nein |
| 2 | Funktionsgruppenversion nicht unterstützt | Nein |
| 3 | Funktionsgruppen-Nachspann fehlt | Ja |
| 4 | Gruppenkontrollnummer im Vorspann und Nachspann der Funktionsgruppe stimmen nicht überein | Ja |
| 5 | Anzahl einbezogener Transaktionssätze stimmt nicht mit tatsächlicher Anzahl überein | Ja |
| 6 | Gruppenkontrollnummer verletzt Syntax, möglicherweise ist eine doppelte Gruppenkontrollnummer aufgetreten | Ja |
| 7 bis 26 | - | Nein |
||||

## <a name="next-steps"></a>Nächste Schritte

* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-x12.md)
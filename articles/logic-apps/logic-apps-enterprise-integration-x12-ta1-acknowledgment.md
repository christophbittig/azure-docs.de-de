---
title: 'X12: TA1-Bestätigungen und -Fehlercodes'
description: Erfahren Sie mehr über technische TA1-Bestätigungen und -Fehlercodes für X12-Nachrichten in Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: be168489b6cdccd3662a174ceab34d093db41244
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481396"
---
# <a name="ta1-technical-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Technische TA1-Bestätigungen und -Fehlercodes für X12-Nachrichten in Azure Logic Apps

Sie können in Azure Logic Apps Workflows erstellen, die X12-Nachrichten für die EDI-Kommunikation (Electronic Data Interchange) verarbeiten, wenn Sie **X12**-Vorgänge verwenden. Beim EDI-Messaging geben Bestätigungen den Status der Verarbeitung eines EDI-Austauschs an. Beim Empfang eines Austauschs kann die [**X12-Decodierungsaktion**](logic-apps-enterprise-integration-x12-decode.md) einen oder mehrere Bestätigungstypen an den Absender zurückgeben. Dies hängt davon ab, welche Bestätigungstypen aktiviert sind und welche Überprüfungsebene angegeben wurde.

Beispielsweise meldet der Empfänger den Status der Überprüfung des Austauschsteuerungsheaders (ISA) und des Austauschsteuerungs-Nachspanns (IEA) in der empfangenen X12-codierten Nachricht, indem er eine *technische TA1-Bestätigung (ACK)* sendet. Wenn dieser Header und der Nachspann gültig sind, sendet der Empfänger unabhängig vom Status anderer Inhalte eine positive TA1-Bestätigung (ACK). Sind der Header und der Nachspann nicht gültig, sendet der Empfänger unabhängig vom Status anderer Inhalte stattdessen eine **TA1-Bestätigung (ACK)** mit einem Fehlercode.

Die X12-TA1-Bestätigung folgt dem Schema **X12_<*Versionsnummer*>_TA1.xsd**. Der Empfänger sendet die TA1-Bestätigung in einem ISA- und IEA-Umschlag. ISA- und IEA-Umschlag unterscheiden sich jedoch nicht von den in anderen Austauscharten verwendeten Umschlägen.

In diesem Thema finden Sie eine kurze Übersicht über die X12-TA1-Bestätigung, einschließlich der TA1-ACK-Segmente bei einem Austausch und der in diesen Segmenten verwendeten Fehlercodes. Weiterführende Informationen finden Sie in der folgenden Dokumentation:

* [X12: 997-Funktionsbestätigungen und -Fehlercodes](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-x12.md)
* [Austausch von EDIFACT-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-edifact.md)
* [Was ist Azure Logic Apps?](logic-apps-overview.md)
* [B2B-Unternehmensintegrationslösungen mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="ta1-ack-segments"></a>

## <a name="ta1-ack-segments"></a>TA1-ACK-Segmente

In der folgenden Tabelle werden die TA1-ACK-Segmente in einem Austausch beschrieben:

| TA1-Feld | Feldname | Zuordnung zum eingehenden Austausch | Wert |
|-----------|------------|--------------------------------|-------|
| TA101 | Austauschkontrollnummer | ISA13 – Austauschkontrollnummer | - |
| TA102 | Austauschdatum | ISA09 – Datum des Austauschs | - |
| TA103 | Austauschuhrzeit | ISA10 – Uhrzeit des Austauschs | - |
| TA104 | Austauschbestätigungscode* | – | * Das Verhalten der Engine basiert auf der Datenelementvalidierung mit Ausnahme von Sicherheits- und Authentifizierungsinformationen, die auf Zeichenfolgenvergleichen in den Konfigurationsinformationen basieren. <p>Der Wert für das Engineverhalten (TA104) lautet A, E oder R, basierend auf den folgenden Definitionen: <p><p>A = Annehmen <br>E = Austausch mit Fehlern angenommen <br>R = Austausch abgelehnt oder angehalten <p><p>Weitere Informationen finden Sie unter [TA1-ACK-Fehlercodes](#ta1-ack-error-codes). |
| TA105 | Austauschhinweiscode | – | Verarbeitungsergebnis-Fehlercode Weitere Informationen finden Sie unter [TA1-ACK-Fehlercodes](#ta1-ack-error-codes). |
|||||

<a name="ta1-ack-error-codes"></a>

## <a name="ta1-ack-error-codes"></a>TA1-ACK-Fehlercodes

In diesem Abschnitt werden die Fehlercodes behandelt, die in [TA1-ACK-Segmenten](#ta1-ack-segments) verwendet werden. In der folgenden Tabelle werden die unterstützten und nicht unterstützten Fehlercodes gemäß X12-Spezifikation aufgeführt, die bei der X12-Nachrichtenverarbeitung in Azure Logic Apps verwendet werden. Die TA104-Werte in der Spalte **Engineverhalten** basieren auf den folgenden Definitionen:

* A = Annehmen
* E = Austausch mit Fehlern angenommen
* R = Austausch abgelehnt oder angehalten

| Bedingung | Engine-Verhalten <br>(TA104-Wert) | TA105-Wert | Unterstützt? |
|-----------|-----------------------------------|-------------|------------|
| Erfolg | Ein | 000 | Ja |
| Die Austauschkontrollnummern im Header ISA 13 und im Nachspann IEA02 stimmen nicht überein | E | 001 | Ja |
| Der Standard in ISA11 (Kontrollstandards) wird nicht unterstützt | E | 002 | Ja, falls ein ID-Konflikt vorliegt. |
| Version der Steuerelemente wird nicht unterstützt | E | 003 | Nein, stattdessen wird Fehlercode 017 verwendet. |
| Ungültiges Segmentabschlusszeichen* <p><p>* Das Segmentabschlusszeichen kann die folgenden gültigen Kombinationen aufweisen: <p><p>– Nur Segmentabschlusszeichen <br>– Segmentabschlusszeichen gefolgt von Suffix 1 und Suffix 2 | R | 004 | Ja |
| Ungültiger Austausch-ID-Qualifizierer für Absender | R | 005 | Ja, falls ein ID-Konflikt vorliegt. |
| Ungültige Austauschabsender-ID | E | 006 | Ja, wenn ein Austausch an einem Empfangsport eingeht, der eine Authentifizierung erforderlich macht. <p><p>**Hinweis**: Eigenschaften in Bezug auf die Absender-ID werden überprüft. Wenn diese Eigenschaften inkonsistent sind oder Parteieinstellungen nicht verfügbar sind, weil sie nicht festgelegt wurden, wird der Austausch abgelehnt. |
| Ungültiger Austausch-ID-Qualifizierer für Empfänger | R | 007 | Ja, falls ein ID-Konflikt vorliegt. |
| Ungültige Austauschempfänger-ID | E | 008 | Nein* <p><p>*Wird unterstützt, wenn ein Austausch an einem Empfangsport eingeht, der eine Authentifizierung erforderlich macht. Eigenschaften in Bezug auf die Absender-ID werden überprüft. Wenn diese Eigenschaften inkonsistent sind oder Parteieinstellungen nicht verfügbar sind, weil sie nicht festgelegt wurden, wird der Austausch abgelehnt. |
| Unbekannte Austauschempfänger-ID | E | 009 | Ja |
| Ungültiger Wert für Autorisierungsinformationenqualifizierer | R | 010 | Ja, falls ein ID-Konflikt vorliegt. |
| Ungültiger Wert für Autorisierungsinformationen | R | 011 | Ja, wenn die Partei eingerichtet oder mit einem Wert konfiguriert wurde. |
| Ungültiger Wert für Sicherheitsinformationenqualifizierer | R | 012 | Ja, falls ein ID-Konflikt vorliegt. |
| Ungültiger Wert für Sicherheitsinformationen | R | 013 | Ja, wenn die Partei eingerichtet oder mit einem Wert konfiguriert wurde. |
| Ungültiger Wert für Austauschdatum | R | 014 | Ja |
| Ungültiger Wert für Austauschuhrzeit | R | 015 | Ja |
| Ungültiger Wert für Austauschkontrollstandardsbezeichner | R | 016 | Ja |
| Ungültiger Wert für ID der Austauschversion | R | 017 | Ja, gibt an, dass der Enumerationswert ungültig ist. |
| Ungültiger Wert für Austauschkontrollnummer | R | 018 | Ja |
| Ungültiger Wert für Bestätigungsanforderung | E | 019 | Ja |
| Ungültiger Testindikatorwert | E | 020 | Ja |
| Ungültiger Wert für die Anzahl von eingeschlossenen Gruppen | E | 021 | Ja |
| Ungültige Kontrollstruktur | R | 022 | Ja |
| Falsches (vorzeitiges) Dateiende (Übertragung) | R | 023 | Ja |
| Ungültiger Austauschinhalt, z. B. ungültiges GS-Segment | R | 024 | Ja |
| Doppelte Austauschkontrollnummer | R, basierend auf Einstellungen | 025 | Ja |
| Ungültiges Datenelementtrennzeichen | R | 026 | Ja |
| Ungültiges Komponentenelementtrennzeichen | R | 027 | Ja |
| Ungültiges Übermittlungsdatum in verzögerter Übermittlungsbestätigung | - | - | Nein |
| Ungültige Übermittlungsuhrzeit in verzögerter Übermittlungsbestätigung | - | - | Nein |
| Ungültiger Übermittlungszeitcode in verzögerter Übermittlungsbestätigung | - | - | Nein |
| Ungültige Dienstgüte | - | - | Nein |
||||

## <a name="next-steps"></a>Nächste Schritte

* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](logic-apps-enterprise-integration-x12.md)
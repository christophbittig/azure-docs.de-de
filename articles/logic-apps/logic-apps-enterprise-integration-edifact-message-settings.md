---
title: EDIFACT-Nachrichteneinstellungen
description: Ein Referenzleitfaden für die EDIFACT-Nachrichteneinstellungen in den Vereinbarungen für Azure Logic Apps mit dem Unternehmensintegrationspaket.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 09/29/2021
ms.openlocfilehash: 05d39063a6b85eae3f247e44f10118b4cf99eded
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403145"
---
# <a name="reference-for-edifact-message-settings-in-agreements-for-azure-logic-apps"></a>Eine Referenz für die EDIFACT-Nachrichteneinstellungen in Vereinbarungen für Azure Logic Apps

In dieser Referenz werden die Eigenschaften beschrieben, die Sie in einer EDIFACT-Vereinbarung festlegen können, um anzugeben wie die Nachrichten zwischen den [Handelspartnern](logic-apps-enterprise-integration-partners.md) gehandhabt werden sollen. Richten Sie die Eigenschaften auf der Grundlage Ihrer Vereinbarung mit dem Partner ein, der Nachrichten mit Ihnen austauscht.

<a name="EDIFACT-inbound-messages"></a>

## <a name="edifact-receive-settings"></a>EDIFACT-Empfangseinstellungen

![Screenshot: Das Azure-Portal, dass die EDIFACT-Vereinbarungseinstellungen für eingehende Nachrichten zeigt.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-receive-settings.png)

### <a name="identifiers"></a>Bezeichner

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNB6.1 (Empfängerverweiskennwort)** | Ein alphanumerischer Wert mit 1–14 Zeichen. |
| **UNB6.2 (Empfängerverweisqualifizierer)** | Ein alphanumerischer Wert mit 1–2 Zeichen. |
|||

### <a name="acknowledgments"></a>Danksagungen

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtenquittung (CONTRL)** | Die technische (CONTRL) Bestätigung wird anhand der Sendeeinstellungen der Vereinbarung an den Absender des Austauschs zurückgegeben. |
| **Bestätigung (CONTRL)** | Die technische (CONTRL) Bestätigung wird anhand der Sendeeinstellungen der Vereinbarung an den Absender des Austauschs zurückgegeben. |
|||

<a name="receive-settings-schemas"></a>

### <a name="schemas"></a>Schemas

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNH2.1 (Typ)** | Der Typ des Transaktionssatzes. |
| **UNH2.2 (Version)** | Die Versionsnummer der Nachricht mit 1–3 Zeichen. |
| **UNH2.3 (Freigabe)** | Die Freigabenummer der Nachricht mit 1–3 Zeichen. |
| **UNH2.5 (zugewiesener Code für die Zuordnung)** | Der zugewiesene Code ist alphanumerisch und beinhaltet 1–6 Zeichen. |
| **UNG2.1 (ID für APP-Sender)** |Geben Sie einen alphanumerischen Wert mit einer Länge von mindestens einem und maximal 35 Zeichen ein. |
| **UNG2.2 (Codequalifizierer für APP-Sender)** |Geben Sie einen alphanumerischen Wert mit maximal vier Zeichen ein. |
| **Schema** | Das zuvor hochgeladene Schema, das Sie in einem der beiden Ressourcentypen verwenden möchten: <p>– Verbrauch: Ein mit Ihrer Logik-App verknüpftes Integrationskonto. <br>– Standard: Erstellen Ihrer Logik-App-Ressource |
|||

### <a name="control-numbers"></a>Kontrollnummern

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Doppelte Austauschkontrollnummern nicht zulassen** | Blockiert doppelte Austauschvorgänge. Die EDIFACT-Decodierungsaktion stellt sicher, dass die Austauschkontrollnummer (UNB5) für den empfangenen Austausch nicht mit einer zuvor verarbeiteten Austauschkontrollnummer übereinstimmt. Wenn eine Übereinstimmung erkannt wird, wird der Austausch nicht verarbeitet. |
| **Innerhalb der folgenden Anzahl von Tagen auf UNB5-Duplikate prüfen** | Wenn Sie sich gegen das Zulassen von doppelten Austauschkontrollnummern entschieden haben, können Sie die Anzahl von Tagen angeben, nach denen die Überprüfung durchgeführt werden soll. |
| **Doppelte Gruppenkontrollnummern nicht zulassen** | Blockieren Sie Austauschvorgänge mit doppelten Gruppenkontrollnummern (UNG5). |
| **Doppelte Transaktionssatz-Kontrollnummern nicht zulassen** | Blockieren Sie Austauschvorgänge mit doppelten Transaktionssatz-Kontrollnummern (UNH1). |
| **EDIFACT-Bestätigungskontrollnummer** | Weisen Sie die in einer Bestätigung zu verwendenden Transaktionssatz-Referenznummern zu, indem Sie einen Wert für das Präfix, einen Referenznummernbereich und ein Suffix eingeben. |
|||

### <a name="validation"></a>Überprüfen

Nachdem Sie die Einrichtung einer Überprüfungszeile abgeschlossen haben, wird die nächste Zeile automatisch angezeigt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Zeile mit der Bezeichnung **Standard**.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtentyp** | Der EDI-Nachrichtentyp. |
| **EDI-Überprüfung** | Führen Sie die EDI-Validierung für die Datentypen aus, die durch die EDI-Eigenschaften des Schemas, Längenbeschränkungen, leere Datenelemente und nachgestellte Trennzeichen definiert sind. |
| **Erweiterte Überprüfung** | Wenn der Datentyp nicht EDI ist, führen Sie die Datenelementanforderung sowie die zulässigen Wiederholungen, Enumerationen und die Überprüfung der Datenelementlänge (Min. und Max.) aus. |
| **Führende/nachfolgende Nullen zulassen** | Behalten Sie alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| **Führende/nachfolgende Nullen abschneiden** | Entfernen Sie die führenden oder nachfolgende Nullen und Leerzeichen. |
| **Richtlinie für nachfolgende Trennzeichen** | Generiert nachfolgende Trennzeichen. <p> - **Nicht zulässig**: Wenn der empfangene Austauschvorgang keine nachfolgenden Begrenzungs- und Trennzeichen enthalten darf. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>- **Optional**: Akzeptiert Austauschvorgänge mit oder ohne nachfolgende Trennzeichen. <p>- **Erforderlich**: Wenn der empfangene Austauschvorgang nachfolgenden Begrenzungs- und Trennzeichen enthalten muss. |
|||

### <a name="internal-settings"></a>Interne Einstellungen

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Leere XML-Tags erstellen, wenn nachfolgende Trennzeichen zulässig sind** | Der Austauschsender enthält leere XML-Tags als nachfolgende Trennzeichen. |
| **Trennen des Austauschs in Transaktionssätze – Transaktionssätze bei Fehler anhalten** | Analysieren Sie jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag auf den Transaktionssatz angewendet wird. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen. |
| **Trennen des Austauschs in Transaktionssätze – Austausch bei Fehler anhalten** | Analysieren Sie jeden Transaktionssatz in einem Austausch in ein separates XML-Dokument, indem der geeignete Umschlag angewendet wird. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |
| **Austausch beibehalten – Transaktionssätze bei Fehler anhalten** | Behalten Sie den Austausch bei und erstellen Sie ein XML-Dokument für den gesamten Batchaustausch. Hält nur die Transaktionssätze an, die die Überprüfung nicht bestehen. Alle anderen Transaktionssätze werden verarbeitet. |
| **Austausch beibehalten – Austausch bei Fehler anhalten** | Behalten Sie den Austausch bei und erstellen Sie ein XML-Dokument für den gesamten Batchaustausch. Hält den gesamten Austausch an, wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht. |
|||

<a name="EDIFACT-outbound-messages"></a>

## <a name="edifact-send-settings"></a>EDIFACT-Sendeeinstellungen

![Ein Screenshot: Das Azure-Portal, dass die EDIFACT-Vereinbarungseinstellungen für ausgehende Nachrichten zeigt.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-send-settings.png)

### <a name="identifiers"></a>Bezeichner

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNB1.2 (Syntaxversion)** | Ein Wert von 1 bis 4. |
| **UNB2.3 (Adresse für Rückmeldung des Absenders)** | Ein alphanumerischer Wert mit 1–14 Zeichen. |
| **UNB3.3 (Adresse für Rückmeldung des Empfängers)** | Ein alphanumerischer Wert mit 1–14 Zeichen. |
| **UNB6.1 (Empfängerverweiskennwort)** | Ein alphanumerischer Wert mit 1–14 Zeichen. |
| **UNB6.2 (Empfängerverweisqualifizierer)** | Ein alphanumerischer Wert mit 1–2 Zeichen. |
| **UNB7 (Anwendungsverweis-ID)** | Ein alphanumerischer Wert mit 1–14 Zeichen. |
|||

### <a name="acknowledgment"></a>Bestätigung

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtenquittung (CONTRL)** | Der Hostpartner, der die Nachricht sendet, fordert eine technische Bestätigung (CONTRL) vom Gastpartner an. |
| **Bestätigung (CONTRL)** | Der Hostpartner, der die Nachricht sendet, fordert eine Funktionsbestätigung (CONTRL) vom Gastpartner an. |
| **SG1/SG4-Schleife für akzeptierte Transaktionssätze generieren** | Wenn Sie sich entschieden haben, eine Funktionsbestätigung anzufordern, erzwingt diese Einstellung die Generierung von SG1/SG4-Schleifen in den funktionalen Bestätigungen für akzeptierte Transaktionssätze. |
|||

### <a name="schemas"></a>Schemas

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNH2.1 (Typ)** | Der Typ des Transaktionssatzes. |
| **UNH2.2 (Version)** | Die Nachrichtenversionsnummer. |
| **UNH2.3 (Freigabe)** | Die Nachrichtenfreigabenummer. |
| **Schema** | Das zuvor hochgeladene Schema, das Sie in einem der beiden Ressourcentypen verwenden möchten: <p>– Verbrauch: Ein mit Ihrer Logik-App verknüpftes Integrationskonto. <br>– Standard: Erstellen Ihrer Logik-App-Ressource |
|||

### <a name="envelopes"></a>Umschläge

Nachdem Sie die Einrichtung einer Umschlagzeile abgeschlossen haben, wird die nächste Zeile automatisch angezeigt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Zeile mit der Bezeichnung **Standard**.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNB8 (Verarbeitungsprioritätscode)** | Geben Sie einen alphabetischen Wert mit einer maximalen Länge von einem Zeichen ein. |
| **UNB10 (Kommunikationsvereinbarung )** | Ein alphanumerischer Wert mit 1-40 Zeichen. |
| **UNB11 (Testindikator)** | Geben Sie an, dass es sich bei dem generierten Austausch um Testdaten handelt. |
| **UNA-Segment übernehmen (Zeichenfolgedienstanweisung)** | Generieren Sie ein UNA-Segment für den zu sendenden Austausch. |
| **UNG-Segmente übernehmen (Funktionsgruppenheader)** | Erstellen Sie Gruppierungssegmente im Funktionsgruppenheader für die an den Gstpartner gesendete Nachrichten. Die folgenden Werte werden verwendet, um die UNG-Segmente zu erstellen: <p>- **Schema**: Das zuvor hochgeladene Schema, das Sie in einem der beiden Ressourcentypen verwenden möchten: <p>– Verbrauch: Ein mit Ihrer Logik-App verknüpftes Integrationskonto. <p>– Standard: Erstellen Ihrer Logik-App-Ressource <p>- **UNG1**: Ein alphanumerischer Wert mit 1–6 Zeichen. <p>- **UNG2.1**: Ein alphanumerischer Wert mit 1–35 Zeichen. <p>- **UNG2.2**: Ein alphanumerischer Wert mit 1–4 Zeichen. <p>- **UNG3.1**: Ein alphanumerischer Wert mit 1–35 Zeichen. <p>- **UNG3.2**: Ein alphanumerischer Wert mit 1–4 Zeichen. <p>- **UNG6**: Ein alphanumerischer Wert mit 1–3 Zeichen. <p>- **UNG7.1**: Ein alphanumerischer Wert mit 1–3 Zeichen. <p>- **UNG7.2**: Ein alphanumerischer Wert mit 1–3 Zeichen. <p>- **UNG7.3**: Ein alphanumerischer Wert mit 1–6 Zeichen. <p>- **UNG8**: Ein alphanumerischer Wert mit 1–14 Zeichen. |
|||

### <a name="character-sets-and-separators"></a>Zeichensätze und Trennzeichen

Mit Ausnahme des Zeichensatzes können Sie einen unterschiedlichen Satz von Begrenzungszeichen für die einzelnen Nachrichtentypen angeben. Wenn Sie keinen Zeichensatz für ein Nachrichtenschema angeben, wird der **Standard** zeichensatz verwendet.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNB1.1 (Systembezeichner)** | Der EDIFACT-Zeichensatz, der auf den ausgehenden Austausch angewendet werden soll. |
| **Schema** | Das zuvor hochgeladene Schema, das Sie in einem der beiden Ressourcentypen verwenden möchten: <p>– Verbrauch: Ein mit Ihrer Logik-App verknüpftes Integrationskonto. <p>– Standard: Erstellen Ihrer Logik-App-Ressource <p>Wählen Sie für das ausgewählte Schema den gewünschten Trennzeichensatz aus. Orientieren Sie sich dabei an den folgenden Trennzeichenbeschreibungen. Nachdem Sie die Einrichtung einer Schemazeile abgeschlossen haben, wird die nächste Zeile automatisch angezeigt. |
| **Eingabetyp** | Der Eingabetyp für die Nachricht. |
| **Komponententrennzeichen** | Ein einzelnes Zeichen, das zum Trennen zusammengesetzter Datenelemente verwendet werden soll. |
| **Datenelementtrennzeichen** | Geben Sie ein einzelnes Zeichen zum Trennen einfacher Datenelemente innerhalb zusammengesetzter Datenelementen an. |
| **UNA3 (Dezimalnotation)** | Die Notation, die im ausgehenden Austausch verwendet werden soll. |
| **UNA4 (Freigabeindikator)** | Ein Wert als Freigabeindikator ein, der anzeigt, dass das folgende Zeichen kein Syntaxtrennzeichen, Abschlusszeichen oder Freigabezeichen, sondern Bestandteil der Originaldaten ist. |
| **UNA5 (Wiederholungstrennzeichen)** | Ein Wert, der als Wiederholungstrennzeichen zu verwenden ist, mit dem Segmente getrennt werden, die sich innerhalb eines Transaktionssatzes wiederholen. |
| **Segmentabschlusszeichen** | Ein einzelnes Zeichen, das das Ende eines EDI-Segments anzeigt. |
| **Suffix** | Das Zeichen, das mit dem Segmentbezeichner verwendet werden soll. Wenn Sie ein Suffix angeben, kann das Segmentabschlusszeichen-Datenelement leer sein. Wenn das Segmentabschlusszeichen leer gelassen wird, müssen Sie ein Suffix angeben. |
|||

### <a name="control-numbers"></a>Kontrollnummern

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **UNB5 (Austauschkontrollnummer)** | Ein Präfix und eine Reihe von Werten, die als Austauschkontrollnummer zu verwenden sind sowie ein Suffix. Diese Werte werden verwendet, um einen ausgehenden Austausch zu generieren. Die Kontrollnummer ist erforderlich, aber das Präfix und das Suffix sind optional. Die Kontrollnummer wird für jede neue Nachricht inkrementiert, während das Präfix und das Suffix gleich bleiben. |
| **UNG5 (Gruppenkontrollnummer)** | Ein Präfix und eine Reihe von Werten, die als Austauschkontrollnummer zu verwenden sind sowie ein Suffix. Diese Werte werden verwendet, um die Gruppenkontrollnummer zu generieren. Die Kontrollnummer ist erforderlich, aber das Präfix und das Suffix sind optional. Die Kontrollnummer wird für jede neue Nachricht inkrementiert, bis der Maximalwert erreicht ist, während das Präfix und das Suffix gleich bleiben. |
| **UNH1 (Verweisnummer des Nachrichtenheaders)** | Ein Präfix und einen Bereich mit Werten für die Austauschkontrollnummer sowie ein Suffix. Diese Werte werden verwendet, um die Verweisnummer des Nachrichtenheaders zu generieren. Die Verweisnummer ist erforderlich, aber das Präfix und das Suffix sind optional. Präfix und Suffix sind optional. Die Verweisnummer ist erforderlich. Die Verweisnummer wird für jede neue Nachricht inkrementiert, während das Präfix und das Suffix gleich bleiben. |
|||

### <a name="validation"></a>Überprüfen

Nachdem Sie die Einrichtung einer Überprüfungszeile abgeschlossen haben, wird die nächste Zeile automatisch angezeigt. Wenn Sie keine Regeln angeben, verwendet die Überprüfung die Zeile mit der Bezeichnung **Standard**.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| **Nachrichtentyp** | Der EDI-Nachrichtentyp. |
| **EDI-Überprüfung** | Führen Sie die EDI-Validierung für die Datentypen aus, die durch die EDI-Eigenschaften des Schemas, Längenbeschränkungen, leere Datenelemente und nachgestellte Trennzeichen definiert sind. |
| **Erweiterte Überprüfung** | Wenn der Datentyp nicht EDI ist, führen Sie die Datenelementanforderung sowie zulässige Wiederholung, Enumerationen und die Überprüfung der Datenelementlänge (Min./Max.) aus. |
| **Führende/nachfolgende Nullen zulassen** | Behalten Sie alle zusätzlichen führenden oder nachfolgenden Nullen und Leerzeichen bei. Diese Zeichen werden nicht entfernt. |
| **Führende/nachfolgende Nullen abschneiden** | Entfernt führende oder nachfolgende Nullen. |
| **Richtlinie für nachfolgende Trennzeichen** | Generiert nachfolgende Trennzeichen. <p>- **Nicht zulässig**: Wenn der gesendete Austauschvorgang keine nachfolgenden Begrenzungs- und Trennzeichen enthalten darf. Wenn der Austauschvorgang nachfolgende Trennzeichen enthält, wird er als ungültig deklariert. <p>- **Optional**: Sendet Austauschvorgänge mit oder ohne nachfolgende Trennzeichen. <p>-  **Erforderlich**: Der gesendete Austauschvorgang muss nachfolgenden Begrenzungs- und Trennzeichen enthalten. |
|||

## <a name="next-steps"></a>Nächste Schritte

[Austauschen von EDIFACT-Nachrichten](../logic-apps/logic-apps-enterprise-integration-edifact.md)
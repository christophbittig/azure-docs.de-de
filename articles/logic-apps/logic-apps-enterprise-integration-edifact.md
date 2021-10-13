---
title: Austauschen von EDIFACT-Nachrichten in B2B-Workflows
description: Tauschen Sie EDIFACT-Nachrichten zwischen Partnern aus, indem Sie Workflows mithilfe von Azure Logic Apps und dem Enterprise Integration Pack erstellen.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401556"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>Austauschen von EDIFACT-Nachrichten mithilfe von Workflows in Azure Logic Apps

Wenn Sie EDIFACT-Nachrichten in Workflows, die Sie mit Azure Logic Apps erstellen, senden und empfangen möchten, können Sie den **EDIFACT**-Connector verwenden. Dieser stellt Trigger und Aktionen bereit, die die EDIFACT-Kommunikation unterstützen und verwalten.

In diesem Artikel wird gezeigt, wie Sie einem bestehenden Logik-App-Workflow die EDIFACT-Aktionen für die Codierung und Decodierung hinzufügen. Sie können zwar einen beliebigen Trigger verwenden, um Ihren Workflow zu starten, in den Beispielen wird jedoch der [Anforderungstrigger](../connectors/connectors-native-reqres.md) verwendet. Weitere Informationen zu den Triggern, Aktionen und Grenzwerten des **EDIFACT**-Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/edifact/) gemäß der Dokumentation in der Swagger-Datei des Connectors.

![Screenshot: Übersicht über den Vorgang "EDIFACT-Nachricht decodieren" mit den Eigenschaften der Nachrichtendecodierung](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>EDIFACT-Codierung und -Decodierung

In den folgenden Abschnitten werden die Aufgaben beschrieben, die Sie mithilfe der EDIFACT-Aktionen für die Codierung und Decodierung ausführen können.

### <a name="encode-to-edifact-message-action"></a>Aktion „In EDIFACT-Nachricht codieren“

* Lösen Sie die Vereinbarung durch Abgleich von Senderqualifizierer und -bezeichner sowie von Empfängerqualifizierer und -bezeichner auf.

* Serialisieren Sie den elektronischen Datenaustausch (Electronic Data Interchange, EDI), der XML-codierte Nachrichten im Austausch in EDI-Transaktionssätze konvertiert.

* Wenden Sie Header- und Nachspannsegmente für den Transaktionssatz an.

* Generieren Sie eine Austauschkontrollnummer, eine Gruppenkontrollnummer und eine Transaktionssatz-Kontrollnummer für jeden ausgehenden Austausch.

* Ersetzen Sie Trennzeichen in den Nutzlastdaten.

* Überprüfen Sie EDI- und partnerspezifische Eigenschaften, z. B. das Schema für Transaktionssatz-Datenelemente anhand des Nachrichtenschemas, Transaktionssatz-Datenelemente und die erweiterte Überprüfung für Transaktionssatz-Datenelemente.

* Generieren Sie ein XML-Dokument für jeden Transaktionssatz.

* Fordern Sie eine technische Bestätigung, Funktionsbestätigung oder beides an, sofern konfiguriert.

  * Als technische Bestätigung kennzeichnet die CONTRL-Nachricht den Empfang für einen Austauschvorgang.

  * Als Funktionsbestätigung kennzeichnet die CONTRL-Nachricht die Annahme oder Ablehnung für den empfangenen Austausch, die Gruppe oder Nachricht, einschließlich einer Liste von Fehlern oder nicht unterstützten Funktionen.

### <a name="decode-edifact-message-action"></a>Aktion „EDIFACT-Nachricht decodieren“

* Überprüfen Sie den Umschlag anhand der Handelspartnervereinbarung.

* Lösen Sie die Vereinbarung durch Abgleich von Senderqualifizierer und -bezeichner sowie von Empfängerqualifizierer und -bezeichner auf.

* Teilen Sie einen Austausch in mehrere Transaktionssätze auf, wenn der Austausch entsprechend der **Empfangseinstellungen** der Vereinbarung mehr als eine Transaktion aufweist.

* Disassemblieren des Austauschs

* Überprüfen Sie EDI- (Electronic Data Interchange) und partnerspezifische Eigenschaften, z. B. die Struktur des Austauschumschlags, das Umschlagschema anhand des Kontrollschemas, das Schema für Transaktionssatz-Datenelemente anhand des Nachrichtenschemas und die erweiterte Überprüfung für Transaktionssatz-Datenelemente.

* Stellen Sie sicher, dass die Austausch-, Gruppen- und Transaktionssatz-Kontrollnummern keine Duplikate sind, falls konfiguriert, beispielsweise:

  * Gleicht die Austauschkontrollnummer mit zuvor empfangenen Austauschvorgängen ab.

  * Überprüfen Sie die Gruppenkontrollnummer anhand anderer Gruppenkontrollnummern im Austausch.

  * Überprüfen Sie die Transaktionssatz-Kontrollnummer anhand anderer Transaktionssatz-Kontrollnummern in dieser Gruppe.

* Trennen Sie den Austausch in Transaktionssätze, oder behalten Sie den gesamten Austausch bei, z. B.:

  * Trennen des Austauschs in Transaktionssätze – Transaktionssätze bei Fehler anhalten

    Die Decodierungsaktion trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. Die Aktion gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.

  * Trennen des Austauschs in Transaktionssätze – Austausch bei Fehler anhalten

    Die Decodierungsaktion trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die Aktion alle Transaktionssätze in diesem Austausch in `badMessages` aus.

  * Austausch beibehalten – Transaktionssätze bei Fehler anhalten

    Die Decodierungsaktion behält den Austausch bei und verarbeitet den gesamten Batchaustausch. Die Aktion gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.

  * Austausch beibehalten – Austausch bei Fehler anhalten

    Die Decodierungsaktion behält den Austausch bei und verarbeitet den gesamten Batchaustausch. Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die Aktion alle Transaktionssätze in diesem Austausch in `badMessages` aus.

* Generieren Sie eine technische Bestätigung, Funktionsbestätigung oder beides, sofern konfiguriert.

  * Eine technische Bestätigung oder die CONTRL-Bestätigung, die die Ergebnisse einer Syntaxüberprüfung des vollständig empfangenen Austauschs meldet.

  * Eine Funktionsbestätigung, die die Annahme oder Ablehnung für den empfangenen Austausch oder die empfangene Gruppe bestätigt.

## <a name="connector-reference"></a>Connector-Referenz

Technische Informationen zum **EDIFACT**-Connector finden Sie auf der [Referenzseite des Connectors](/connectors/edifact/), auf der die Trigger, Aktionen und Grenzwerte wie in der Swagger-Datei des Connectors dokumentiert beschrieben werden. Lesen Sie auch die [B2B-Protokollgrenzwerte für Nachrichtengrößen](logic-apps-limits-and-config.md#b2b-protocol-limits) für Workflows, die in [mehrinstanzenfähigen Azure Logic Apps-Instanzen, einzelinstanzfähigen Azure Logic Apps-Instanzen oder der Integrationsdienstumgebung (Integration Service Environment, ISE)](logic-apps-overview.md#resource-environment-differences) ausgeführt werden. In einer [Integrationsdienstumgebung (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) zum Beispiel verwendet die ISE-Version dieses Connectors die [B2B-Nachrichtengrenzwerte für ISE](logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)**](logic-apps-overview.md#resource-environment-differences) und die **EDIFACT**-Vorgänge verwenden, benötigt Ihre Logik-App-Ressource keine Verbindung mit Ihrem Integrationskonto. Sie benötigen dieses Konto jedoch weiterhin, um Artefakte wie Partner, Vereinbarungen und Zertifikate zu speichern und die EDIFACT-, [X12](logic-apps-enterprise-integration-x12.md)- oder [AS2](logic-apps-enterprise-integration-as2.md)-Vorgänge zu verwenden. Ihr Integrationskonto muss noch andere Anforderungen erfüllen, z.B. dass es dasselbe Azure-Abonnement verwendet und sich am selben Ort befindet wie Ihre Logic-App-Ressource.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)**](logic-apps-overview.md#resource-environment-differences) und die **EDIFACT**-Vorgänge verwenden, benötigt Ihr Workflow eine Verbindung mit Ihrem Integrationskonto. Diese erstellen Sie direkt aus Ihrem Workflow heraus, wenn Sie den AS2-Vorgang hinzufügen.

* Mindestens zwei [Parteien](logic-apps-enterprise-integration-partners.md) (Handelspartner) in Ihrem Integrationskonto. Die Definitionen für beide Partner müssen denselben Qualifizierer für die *Geschäftsidentität* verwenden, der für dieses Szenario **ZZZ - Mutually Defined** lautet.

* Eine [EDIFACT-Vereinbarung](logic-apps-enterprise-integration-agreements.md) in Ihrem Integrationskonto zwischen den Parteien, die an Ihrem Workflow teilnehmen. Jede Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner. Der Inhalt in den Nachrichten zwischen Ihnen und dem Partner muss mit dem Vereinbarungstyp übereinstimmen.

  > [!IMPORTANT]
  > Der EDIFACT-Connector unterstützt nur UTF-8-Zeichen. Wenn Ihre Ausgabe unerwartete Zeichen enthält, überprüfen Sie, ob Ihre EDIFACT-Nachrichten den UTF-8-Zeichensatz verwenden.

* Die Logik-App-Ressource und der Workflow, in der bzw. dem Sie die EDIFACT-Vorgänge verwenden möchten.

  Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](quickstart-create-first-logic-app-workflow.md).

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>Codierung von EDIFACT-Nachrichten

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die EDIFACT-Aktion hinzufügen möchten, die Option **Neuer Schritt** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus. Geben Sie im Suchfeld `edifact encode`ein. Wählen Sie für dieses Beispiel die Aktion **In EDIFACT-Nachricht nach Vereinbarungsname codieren** aus.

   ![Screenshot, der das Azure-Portal, den Workflow-Designer und die ausgewählte Aktion „In EDIFACT-Nachricht nach Vereinbarungsname codieren“ zeigt.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > Sie können stattdessen die Aktion **In EDIFACT-Nachricht nach Identitäten codieren** auswählen, Sie müssen jedoch später andere Werte angeben, z. B. **Senderbezeichner** und **Empfängerbezeichner**, der in Ihrer EDIFACT-Vereinbarung angegeben ist. Sie müssen auch die **zu codierende XML-Nachricht** angeben, bei der es sich um die Ausgabe des Triggers oder einer vorherigen Aktion handeln kann.

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „In EDIFACT-Nachricht nach Vereinbarungsname codieren“](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wenn der EDIFACT-Vorgang im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an, die für diesen Vorgang spezifisch sind:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name der EDIFACT-Vereinbarung** | Ja | Die zu verwendende EDIFACT-Vereinbarung. |
   | **Zu codierende XML-Nachricht** | Ja | Der Geschäftsbezeichner für den Absender der Nachricht, wie in Ihrer EDIFACT-Vereinbarung angegeben |
   | Weitere Parameter | Nein | Dieser Vorgang umfasst die folgenden weiteren Parameter: <p>- **Datenelementtrennzeichen** <br>- **Freigabeindikator** <br>- **Komponententrennzeichen** <br>- **Wiederholungstrennzeichen** <br>- **Segmentabschlusszeichen** <br>- **Segmentabschlusszeichensuffix** <br>- **Dezimalindikator** <p>Weitere Informationen finden Sie unter [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Die XML-Nachrichtennutzdaten können beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger sein:

   ![Screenshot des Vorgangs „In EDIFACT-Nachricht nach Vereinbarungsname codieren“ mit den Nachrichtencodierungseigenschaften](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die EDIFACT-Aktion hinzufügen möchten, die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus. Geben Sie im Suchfeld `edifact encode`ein. Wählen Sie die Aktion **In EDIFACT-Nachricht nach Vereinbarungsname codieren** aus.

   ![Screenshot, der das Azure-Portal, den Workflow-Designer und den ausgewählten Vorgang „In EDIFACT-Nachricht nach Vereinbarungsname codieren“ zeigt.](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > Sie können stattdessen die Aktion **In EDIFACT-Nachricht nach Identitäten codieren** auswählen, Sie müssen jedoch später andere Werte angeben, z. B. **Senderbezeichner** und **Empfängerbezeichner**, der in Ihrer EDIFACT-Vereinbarung angegeben ist. Sie müssen auch die **zu codierende XML-Nachricht** angeben, bei der es sich um die Ausgabe des Triggers oder einer vorherigen Aktion handeln kann.

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „In EDIFACT-Nachricht nach Parametername codieren“](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wenn der EDIFACT-Detailbereich im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name der EDIFACT-Vereinbarung** | Ja | Die zu verwendende EDIFACT-Vereinbarung. |
   | **Zu codierende XML-Nachricht** | Ja | Der Geschäftsbezeichner für den Absender der Nachricht, wie in Ihrer EDIFACT-Vereinbarung angegeben |
   | Weitere Parameter | Nein | Dieser Vorgang umfasst die folgenden weiteren Parameter: <p>- **Datenelementtrennzeichen** <br>- **Freigabeindikator** <br>- **Komponententrennzeichen** <br>- **Wiederholungstrennzeichen** <br>- **Segmentabschlusszeichen** <br>- **Segmentabschlusszeichensuffix** <br>- **Dezimalindikator** <p>Weitere Informationen finden Sie unter [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Die Nachrichtennutzdaten sind beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger:

   ![Screenshot des Vorgangs „In EDIFACT-Nachricht nach Parametername codieren“ mit den Nachrichtencodierungseigenschaften](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>Decodierung von EDIFACT-Nachrichten

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die EDIFACT-Aktion hinzufügen möchten, die Option **Neuer Schritt** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus. Geben Sie im Suchfeld `edifact encode`ein. Wählen Sie die Aktion **EDIFACT-Nachricht decodieren** aus.

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „EDIFACT-Nachricht decodieren“](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wenn der EDIFACT-Vorgang im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an, die für diesen Vorgang spezifisch sind:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Zu decodierende EDIFACT-Flatfilenachricht** | Ja | Die zu decodierende XML-Flatfilenachricht. |
   | Weitere Parameter | Nein | Dieser Vorgang umfasst die folgenden weiteren Parameter: <p>- **Komponententrennzeichen** <br>- **Datenelementtrennzeichen** <br>- **Freigabeindikator** <br>- **Wiederholungstrennzeichen** <br>- **Segmentabschlusszeichen** <br>- **Segmentabschlusszeichensuffix** <br>- **Dezimalindikator** <br>- **Nutzdatenzeichensatz** <br>- **Segmentabschlusszeichensuffix** <br>- **Austausch beibehalten** <br>- **Austausch bei Fehler anhalten** <p>Weitere Informationen finden Sie unter [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Die zu decodierenden XML-Nachrichtennutzdaten können beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger sein:

   ![Screenshot des Vorgangs „EDIFACT-Nachricht decodieren“ mit den Eigenschaften der Nachrichtendecodierung](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die EDIFACT-Aktion hinzufügen möchten, die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus. Geben Sie im Suchfeld `edifact encode`ein. Wählen Sie die Aktion **EDIFACT-Nachricht decodieren** aus.

   ![Screenshot, der das Azure-Portal, den Workflow-Designer und den ausgewählten Vorgang „EDIFACT-Nachricht decodieren“ zeigt.](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „EDIFACT-Nachricht decodieren“](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wenn der EDIFACT-Detailbereich im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name der EDIFACT-Vereinbarung** | Ja | Die zu verwendende EDIFACT-Vereinbarung. |
   | **Zu codierende XML-Nachricht** | Ja | Der Geschäftsbezeichner für den Absender der Nachricht, wie in Ihrer EDIFACT-Vereinbarung angegeben |
   | Weitere Parameter | Nein | Dieser Vorgang umfasst die folgenden weiteren Parameter: <p>- **Datenelementtrennzeichen** <br>- **Freigabeindikator** <br>- **Komponententrennzeichen** <br>- **Wiederholungstrennzeichen** <br>- **Segmentabschlusszeichen** <br>- **Segmentabschlusszeichensuffix** <br>- **Dezimalindikator** <p>Weitere Informationen finden Sie unter [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact-message-settings.md). |
   ||||

   Die Nachrichtennutzdaten sind beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger:

   ![Screenshot des Vorgangs „EDIFACT-Nachricht decodieren“ mit den Eigenschaften der Nachrichtendecodierung](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>Behandeln von UNH2.5-Segmenten in EDIFACT-Dokumenten

In einem EDIFACT-Dokument wird das [UNH2.5-Segment](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas) für die Schemasuche verwendet. In der folgenden exemplarischen EDIFACT-Nachricht ist das UNH-Feld beispielsweise `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Führen Sie die folgenden Schritte aus, um ein EDIFACT-Dokument zu behandeln oder eine EDIFACT-Nachricht zu verarbeiten, das bzw. die über ein UN2.5-Segment verfügt:

1. Aktualisieren Sie ein Schema, oder stellen Sie ein Schema bereit, das über den UNH2.5-Stammknotennamen verfügt.

   Angenommen, der Schemastammname für das exemplarische UNH-Feld lautet beispielsweise `EFACT_D03B_ORDERS_EAN008`. Für jedes Element vom Typ `D03B_ORDERS`, das über ein anderes UNH2.5-Segment verfügt, muss ein eigenes Schema bereitgestellt werden.

1. Fügen Sie im [Azure-Portal](https://portal.azure.com) das Schema Ihrer Integrationskontoressource oder Logik-App-Ressource hinzu, abhängig davon, ob Sie mit dem Ressourcentyp **Logik-App (Verbrauch)** oder **Logik-App (Standard)** arbeiten.

1. Laden Sie unabhängig davon, ob Sie die EDIFACT-Aktion für die Decodierung oder Codierung verwenden, Ihr Schema hoch, und richten Sie die Schemaeinstellungen in den Abschnitten **Empfangseinstellungen** bzw. **Sendeeinstellungen** Ihrer EDIFACT-Vereinbarung ein.

1. Zur Bearbeitung Ihrer EDIFACT-Vereinbarung wählen Sie im Bereich **Vereinbarungen** Ihre Vereinbarung aus. Wählen Sie in der Symbolleiste des Bereichs **Vereinbarungen** die Option **Als JSON bearbeiten** aus.

   * Suchen Sie im Abschnitt `receiveAgreement` der Vereinbarung den Abschnitt `schemaReferences`, und fügen Sie den UNH2.5-Wert hinzu.

     ![Screenshot, der das Azure-Portal mit dem Abschnitt „receiveAgreement“ einer EDIFACT-Vereinbarung im JSON-Editor zeigt, wobei der Abschnitt „schemaReferences“ hervorgehoben ist.](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * Suchen Sie im Abschnitt `sendAgreement` der Vereinbarung den Abschnitt `schemaReferences`, und fügen Sie den UNH2.5-Wert hinzu.

     ![Screenshot, der das Azure-Portal mit dem Abschnitt „sendAgreement“ einer EDIFACT-Vereinbarung im JSON-Editor zeigt, wobei der Abschnitt „schemaReferences“ hervorgehoben ist.](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>Nächste Schritte

* [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact-message-settings.md)

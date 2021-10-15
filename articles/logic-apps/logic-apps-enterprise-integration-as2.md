---
title: Austauschen von AS2-Nachrichten in B2B-Workflows
description: Tauschen Sie AS2-Nachrichten zwischen Partner*innen aus, indem Sie Workflows mit Azure Logic Apps und Enterprise Integration Pack erstellen.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8023073aecae3a1f97c82a16a5be952df7425186
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399646"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>Austauschen von AS2-Nachrichten mithilfe von Workflows in Azure Logic Apps

Wenn Sie AS2-Nachrichten in Workflows, die Sie mit Azure Logic Apps erstellen, senden und empfangen möchten, können Sie den **AS2**-Connector verwenden. Dieser stellt Trigger und Aktionen bereit, die die AS2-Kommunikation (Version 1.2) unterstützen und verwalten.

* Wenn Sie mit dem Ressourcentyp **Logik-App (Verbrauch)** arbeiten und keine Nachverfolgungsfunktionen benötigen, verwenden Sie den Connector **AS2 (v2)** anstelle des ursprünglichen **AS2**-Connectors, der veraltet ist.

  Mit Ausnahme der Nachverfolgung bietet der Connector **AS2 (v2)** eine bessere Leistung, die gleichen Funktionen wie die ursprüngliche Version, ist in der Azure Logic Apps-Runtime nativ und sorgt für erhebliche Leistungsverbesserungen in Bezug auf Nachrichtengröße, -durchsatz und -wartezeit. Außerdem erfordert der v2-Connector keine Verbindung mit Ihrem Integrationskonto. Stellen Sie stattdessen sicher, dass Sie, wie in den Voraussetzungen beschrieben, Ihr Integrationskonto mit der Logik-App-Ressource verbinden, in der Sie den Connector verwenden möchten.

* Wenn Sie mit dem Ressourcentyp **Logik-App (Standard)** arbeiten, ist derzeit nur der ursprüngliche **AS2**-Connector verfügbar.

  Technische Informationen zur ursprünglichen Version des **AS2**-Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/as2/), auf der die Trigger, Aktionen und Grenzwerte wie in der Swagger-Datei des Connectors dokumentiert beschrieben werden.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

In den folgenden Listen werden Aktionen beschrieben, die der Connector **AS2 (v2)** für das Einrichten der Sicherheit und Zuverlässigkeit beim Übertragen von Nachrichten bereitstellt:

* [Aktion **AS2-Nachricht codieren**](#encode) zum Bereitstellen von Verschlüsselung, einer digitalen Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (Message Disposition Notifications, MDNs), die beim Unterstützen der Nichtabstreitbarkeit helfen. Diese Aktion wendet z. B. AS2/HTTP-Header an und führt die folgenden Aufgaben aus, wenn sie konfiguriert sind:

  * Signieren ausgehender Nachrichten
  * Verschlüsseln ausgehender Nachrichten
  * Komprimieren der Nachricht
  * Dateinamen im MIME-Header übertragen

* [Aktion **AS2-Nachricht decodieren**](#decode) zum Bereitstellen von Entschlüsselung, einer digitalen Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (Message Disposition Notifications, MDNs). Diese Aktion führt z. B. die folgenden Aufgaben aus:

  * Verarbeiten von AS2/HTTP-Headern
  * Abstimmen empfangener MDNs mit der ursprünglichen ausgehenden Nachricht
  * Aktualisieren und Korrelieren von Datensätzen in der Nichtabstreitbarkeits-Datenbank
  * Schreiben von Datensätzen für AS2-Statusberichte
  * Base64-codierte Ausgabe von Nutzlastinhalten
  * Ermitteln, ob MDNs erforderlich sind Ermittlung auf Grundlage der AS2-Vereinbarung, ob MDNs synchron oder asynchron sein müssen
  * Generieren einer synchronen oder asynchronen MDN auf Grundlage der AS2-Vereinbarung
  * Festlegen der Korrelationstoken und Eigenschaften für MDNs

  Diese Aktion führt auch die folgenden Aufgaben aus, wenn sie konfiguriert ist:

  * Überprüfen der Signatur
  * Entschlüsseln der Nachrichten
  * Dekomprimieren der Nachrichten
  * Suchen nach und Unterbinden von doppelten Nachrichten-IDs

### <a name="standard"></a>[Standard](#tab/standard)

Weitere Informationen zu den Triggern, Aktionen und Grenzwerten des ursprünglichen **AS2**-Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/as2/) gemäß der Dokumentation in der Swagger-Datei des Connectors.

---

In diesem Artikel wird gezeigt, wie Sie einem bestehenden Logik-App-Workflow die AS2-Aktionen für die Codierung und Decodierung hinzufügen. Sie können zwar einen beliebigen Trigger verwenden, um Ihren Workflow zu starten, in den Beispielen wird jedoch der [Anforderungstrigger](../connectors/connectors-native-reqres.md) verwendet.

## <a name="limits"></a>Grenzwerte

Informationen zu den Grenzwerten des AS2-Connectors für Workflows, die in [mehrinstanzenfähigen Azure Logic Apps-Instanzen, einzelinstanzfähigen Azure Logic Apps-Instanzen oder der Integrationsdienstumgebung (Integration Service Environment, ISE)](logic-apps-overview.md#resource-environment-differences) ausgeführt werden, finden Sie unter [B2B-Protokollgrenzwerte für Nachrichtengrößen](logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)**](logic-apps-overview.md#resource-environment-differences) und die **AS2 (v2)-Vorgänge** verwenden, benötigt Ihre Logik-App-Ressource keine Verbindung mit Ihrem Integrationskonto. Sie benötigen dieses Konto jedoch weiterhin, um Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den AS2-, [X12](logic-apps-enterprise-integration-x12.md)- oder [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)**](logic-apps-overview.md#resource-environment-differences) und die ursprünglichen **AS2**-Vorgänge verwenden, benötigt Ihr Workflow eine Verbindung mit Ihrem Integrationskonto. Diese erstellen Sie direkt aus Ihrem Workflow heraus, wenn Sie den AS2-Vorgang hinzufügen.

* Mindestens zwei [Parteien](logic-apps-enterprise-integration-partners.md) (Handelspartner) in Ihrem Integrationskonto. Die Definitionen für beide Partner müssen denselben Qualifizierer für die *Geschäftsidentität* verwenden, der für dieses Szenario **AS2Identity** lautet.

* Eine [AS2-Vereinbarung](logic-apps-enterprise-integration-agreements.md) in Ihrem Integrationskonto zwischen den Parteien, die an Ihrem Workflow teilnehmen. Jede Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner. Der Inhalt der Nachrichten zwischen Ihnen und dem*der Partner*in muss mit dem Vereinbarungstyp übereinstimmen.

* Die Logik-App-Ressource und der Workflow, in der bzw. dem Sie die AS2-Vorgänge verwenden möchten.

  > [!NOTE]
  > Der Connector **AS2 (v2)** stellt nur Aktionen, aber keine Trigger bereit. In diesem Artikel wird in den Beispielen für diesen Connector der [Anforderungstrigger](../connectors/connectors-native-reqres.md) verwendet. Der ursprüngliche **AS2**-Connector umfasst Trigger und Aktionen. Weitere Informationen zu den Triggern, Aktionen und Grenzwerten des ursprünglichen **AS2**-Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/as2/) gemäß der Dokumentation in der Swagger-Datei des Connectors.

  Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](quickstart-create-first-logic-app-workflow.md).

* Wenn Sie [Azure Key Vault](../key-vault/general/overview.md) für die Zertifikatsverwaltung verwenden, überprüfen Sie, ob Ihre Tresorschlüssel die Vorgänge **Verschlüsseln** und **Entschlüsseln** zulassen. Andernfalls treten beim Codieren und Decodieren Fehler auf.

  1. Öffnen Sie im Azure-Portal Ihren Schlüsseltresor. Wählen Sie im Schlüsseltresormenü unter **Einstellungen** die Option **Schlüssel** aus.

  1. Wählen Sie im Bereich **Schlüssel** Ihren Schlüssel aus. Wählen Sie im Bereich **Versionen** die verwendete Schlüsselversion aus.

  1. Vergewissern Sie sich im Bereich **Schlüsselversion** unter **Zulässige Vorgänge**, dass die Vorgänge **Verschlüsseln** und **Entschlüsseln** ausgewählt sind, z. B.:

     ![Screenshot des Azure-Portals mit geöffneten Bereichen „Schlüsseltresor“, „Schlüssel“ und „Schlüsselversion“ und den ausgewählten Vorgängen „Verschlüsseln“ und „Entschlüsseln“](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codieren von AS2-Nachrichten

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die AS2-Aktion hinzufügen möchten, die Option **Neuer Schritt** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus. Geben Sie im Suchfeld `as2 encode`ein. Wählen Sie die Aktion **AS2-Codierung** aus.

   ![Screenshot von Azure-Portal, Workflow-Designer und ausgewählter Aktion „AS2-Codierung“](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Wenn der AS2-Vorgang im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Zu codierende Nachricht** | Ja | Die Nachrichtennutzlast. |
   | **AS2-Absender** | Ja | Der Geschäftsbezeichner für den Absender der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben |
   | **AS2-Empfänger** | Ja | Der Geschäftsbezeichner für den Empfänger der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben |
   ||||

   Die Nachrichtennutzdaten sind beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger:

   ![Screenshot der Aktion „AS2-Codierung“ mit den Nachrichtencodierungseigenschaften](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > Wenn beim Senden von signierten oder verschlüsselten Nachrichten Probleme auftreten, sollten Sie andere SHA256-Algorithmusformate ausprobieren. Die AS2-Spezifikation stellt keine Informationen zu SHA256-Formaten bereit, sodass jeder Anbieter seine eigene Implementierung bzw. sein eigenes Format verwendet.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die AS2-Aktion hinzufügen möchten, die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus. Geben Sie im Suchfeld `as2 encode`ein. Wählen Sie die Aktion **In AS2-Nachricht codieren** aus.

   ![Screenshot von Azure-Portal, Workflow-Designer und ausgewähltem Vorgang „In AS2-Nachricht codieren“](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „In AS2-Nachricht codieren“](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wenn der AS2-Detailbereich im Designer angezeigt wird, geben Sie Informationen für die folgenden Eigenschaften an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Zu codierende Nachricht** | Ja | Die Nachrichtennutzlast. |
   | **AS2-Absender** | Ja | Der Geschäftsbezeichner für den Absender der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben |
   | **AS2-Empfänger** | Ja | Der Geschäftsbezeichner für den Empfänger der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben |
   ||||

   Die Nachrichtennutzdaten sind beispielsweise der **Textteil** der Inhaltsausgabe vom Anforderungstrigger:

   ![Screenshot des Vorgangs „AS2-Codierung“ mit den Nachrichtencodierungseigenschaften](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > Wenn beim Senden von signierten oder verschlüsselten Nachrichten Probleme auftreten, sollten Sie andere SHA256-Algorithmusformate ausprobieren. Die AS2-Spezifikation stellt keine Informationen zu SHA256-Formaten bereit, sodass jeder Anbieter seine eigene Implementierung bzw. sein eigenes Format verwendet.

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodieren von AS2-Nachrichten

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die AS2-Aktion hinzufügen möchten, die Option **Neuer Schritt** aus. In diesem Beispiel wird der [Anforderungstrigger](../connectors/connectors-native-reqres.md) verwendet.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus. Geben Sie im Suchfeld `as2 decode`ein. Wählen Sie die Aktion **AS2-Decodierung** aus.

   ![Screenshot von Azure-Portal, Workflow-Designer und ausgewählter Aktion „AS2-Decodierung“](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Wählen Sie in der Form des AS2-Vorgangs für die Eigenschaften **Zu codierende Nachricht** und **Nachrichtenheader** die Werte aus den vorherigen Trigger- oder Aktionsausgaben aus.

   In diesem Beispiel können Sie die Ausgaben des Anforderungstriggers auswählen.

   ![Screenshot von Azure-Portal, Workflow-Designer und dem Vorgang „AS2-Decodierung“ mit ausgewählter Ausgabe „Body“ und „Header“ aus dem Anforderungstrigger](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den Workflow im Designer.

1. Wählen Sie im Designer unter dem Trigger oder der Aktion, dem bzw. der Sie die AS2-Aktion hinzufügen möchten, die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus. Geben Sie im Suchfeld `as2 decode`ein. Wählen Sie die Aktion **AS2-Nachricht decodieren** aus.

   ![Screenshot von Azure-Portal, Workflow-Designer und ausgewähltem Vorgang „AS2-Nachricht decodieren“](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie die folgenden Informationen an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Verbindungsname** | Ja | Ein Name für die Verbindung |
   | **Integrationskonto** | Ja | Wählen Sie in der Liste der verfügbaren Integrationskonten das Konto aus, das Sie nutzen möchten. |
   ||||

   Beispiel:

   ![Screenshot des Verbindungsbereichs „AS2-Nachricht decodieren“](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Wählen Sie im AS2-Detailbereich für die Eigenschaften **Zu codierende Nachricht** und **Nachrichtenheader** die Werte aus den vorherigen Trigger- oder Aktionsausgaben aus.

   In diesem Beispiel können Sie die Ausgaben des Anforderungstriggers auswählen.

   ![Screenshot von Azure-Portal, Workflow-Designer und dem Vorgang „AS2-Nachricht decodieren“ mit ausgewählter Ausgabe „Body“ und „Header“ aus dem Anforderungstrigger](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>Beispiel

Wenn Sie eine uneingeschränkt funktionsfähige Logik-App und ein AS2 (v2)-Beispielszenario bereitstellen möchten, sehen Sie sich die Vorlage und das Szenario unter [Azure Logic Apps - AS2 Send Receive](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/) (Azure Logic Apps – AS2: Senden/Empfangen) an.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über andere [Connectors für Azure Logic Apps](../connectors/apis-list.md).

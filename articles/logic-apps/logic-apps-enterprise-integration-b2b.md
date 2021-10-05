---
title: Austauschen von B2B-Nachrichten mithilfe von Workflows
description: Tauschen Sie Nachrichten zwischen Handelspartnern aus, indem Sie Workflows mithilfe von Azure Logic Apps und dem Enterprise Integration Pack erstellen.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 4b8907b9a50be3bd6021b72dd05c7d5445da03f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660201"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>Austauschen von B2B-Nachrichten zwischen Partnern mithilfe von Workflows in Azure Logic Apps

Wenn Sie über ein Integrationskonto verfügen, für das Parteien und Vereinbarungen definiert werden, haben Sie folgende Möglichkeit: Sie können einen automatisierten B2B-Workflow (Business to Business) erstellen, mit dem Nachrichten zwischen Handelspartnern über Azure Logic Apps ausgetauscht werden. Sie können in Ihrem Workflow Connectors verwenden, die Branchenstandardprotokolle wie AS2, X12, EDIFACT und RosettaNet unterstützen. Sie können auch Vorgänge einbinden, die von anderen [Connectors in Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors) bereitgestellt werden, z. B. Office 365 Outlook, SQL Server und Salesforce.

In diesem Artikel wird veranschaulicht, wie Sie einen Beispielworkflow für eine Logik-App erstellen, der über einen **Anforderungstrigger** eine HTTP-Anforderung empfängt, den Inhalt der Nachricht über die Aktionen **AS2-Decodierung** und **X12-Decodierung** decodiert und dann mit der **Antwortaktion** eine Antwort zurückgibt. In diesem Beispiel wird der Workflow-Designer im Azure-Portal verwendet, aber die Schritte für den Workflow-Designer in Visual Studio sind sehr ähnlich.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie unter [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto eine [Verbindung mit Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Artefakte in Ihrem Workflow verwenden können.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto keine Verbindung mit Ihrer Logik-App-Ressource. Sie ist aber trotzdem erforderlich, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)- oder [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

  > [!NOTE]
  > Derzeit unterstützt nur der Ressourcentyp **Logik-App (Verbrauch)** [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Mindestens zwei [Parteien](logic-apps-enterprise-integration-partners.md) (Handelspartner) in Ihrem Integrationskonto. Die Definitionen für beide Partner müssen denselben Qualifizierer für die *Geschäftsidentität* verwenden, der AS2, X12, EDIFACT oder RosettaNet lautet.

* Eine [AS2-Vereinbarung und eine X12-Vereinbarung](logic-apps-enterprise-integration-agreements.md) für die Partner, die Sie in diesem Workflow verwenden. Jede Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner.

* Eine Logik-App-Ressource mit einem leeren Workflow, in dem Sie den [Anforderungstrigger](../connectors/connectors-native-reqres.md) und danach die folgenden Aktionen hinzufügen können:

  * [AS2-Decodierung](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Bedingung](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Condition), von der eine [Antwort](../connectors/connectors-native-reqres.md) (Response) in Abhängigkeit davon gesendet wird, ob die Aktion „AS2-Decodierung“ erfolgreich ist.

  * [X12-Nachricht decodieren](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>Hinzufügen des Anforderungstriggers

Um den Workflow in diesem Beispiel zu starten, fügen Sie den Anforderungstrigger hinzu.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den leeren Workflow im Workflow-Designer.

1. Wählen Sie im Suchfeld des Designers die Option **Alle** aus, sofern sie noch nicht ausgewählt ist. Geben Sie im Suchfeld `when a http request`ein. Wählen Sie den Anforderungstrigger **Beim Empfang einer HTTP-Anforderung** aus.

   ![Screenshot von Azure-Portal und mehrinstanzenfähigem Designer mit „Beim Empfang einer HTTP-Anforderung“ im Suchfeld und ausgewähltem Anforderungstrigger](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. Lassen Sie das Feld **JSON-Schema für Anforderungstext** im Trigger leer.

   Der Grund dafür ist, dass der Trigger eine X12-Nachricht im Flatfileformat empfängt.

   ![Screenshot des mehrinstanzenfähigen Designers und der Eigenschaften des Anforderungstriggers](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

   In diesem Schritt wird die **HTTP POST-URL** generiert, über die Sie später eine Anforderung senden, die dann den Logik-App-Workflow auslöst.

   ![Screenshot des mehrinstanzenfähigen Designers und der generierten URL für den Anforderungstrigger](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. Kopieren und speichern Sie die URL zur späteren Verwendung.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App-Ressource und den leeren Workflow im Workflow-Designer.

1. Wählen Sie im Designer **Vorgang auswählen** aus. Wählen Sie unter dem Suchfeld die Option **Integriert** aus, sofern sie noch nicht ausgewählt ist. Geben Sie im Suchfeld `when a http request`ein. Wählen Sie den Anforderungstrigger **Beim Empfang einer HTTP-Anforderung** aus.

   ![Screenshot von Azure-Portal und einzelinstanzfähigem Designer mit „Beim Empfang einer HTTP-Anforderung“ im Suchfeld und ausgewähltem Anforderungstrigger](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. Lassen Sie das Feld **JSON-Schema für Anforderungstext** im Trigger leer.

   Der Grund dafür ist, dass der Trigger eine X12-Nachricht im Flatfileformat empfängt.

   ![Screenshot des einzelinstanzfähigen Designers und der Eigenschaften des Anforderungstriggers](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

   In diesem Schritt wird die **HTTP POST-URL** generiert, über die Sie später eine Anforderung senden, die dann den Logik-App-Workflow auslöst.

   ![Screenshot des einzelinstanzfähigen Designers und der generierten URL für den Anforderungstrigger](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. Kopieren und speichern Sie die URL zur späteren Verwendung.

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>Hinzufügen der Aktion für die AS2-Decodierung

Fügen Sie nun die B2B-Aktionen für dieses Beispiel hinzu, das die AS2- und X12-Aktionen verwendet.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus.

   > [!TIP]
   > Wählen Sie zum Ausblenden der Details zum Anforderungstrigger die Titelleiste des Triggers aus.

   ![Screenshot des mehrinstanzenfähigen Designers und des Triggers mit ausgewählter Option „Neuer Schritt“](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Alle** aus, sofern sie noch nicht ausgewählt ist. Geben Sie in das Suchfeld `as2` ein, und wählen Sie **AS2-Decodierung** aus.

   ![Screenshot des mehrinstanzenfähigen Designers mit ausgewählter Aktion „AS2-Decodierung“](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. Geben Sie für die Eigenschaft der Aktion **Zu decodierende Nachricht** die Eingabe ein, die von der AS2-Aktion decodiert werden soll, also die `body`-Ausgabe vom Anforderungstrigger. Sie haben mehrere Möglichkeiten, diesen Inhalt als Eingabe der Aktion anzugeben. Sie können ihn in der Liste mit dem dynamischen Inhalt auswählen oder einen Ausdruck verwenden:

   * Klicken Sie in das Feld **Zu decodierende Nachricht**, um in der Liste mit den verfügbaren Triggerausgaben eine Auswahl zu treffen. Wählen Sie in der Liste mit dem dynamischen Inhalt unter **Beim Empfang einer HTTP-Anforderung** den Eigenschaftswert **Text** (Body) aus. Beispiel:

     ![Screenshot des mehrinstanzenfähigen Designers mit Auswahl in der Liste mit dem dynamischen Inhalt und ausgewählter „Body“-Eigenschaft](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > Wenn keine Triggerausgaben angezeigt werden, wählen Sie in der Liste mit den dynamischen Eigenschaften unter **Beim Empfang einer HTTP-Anforderung** die Option **Weitere anzeigen** aus.

   * Klicken Sie zum Eingeben eines Ausdrucks, mit dem auf die `body`-Ausgabe des Triggers verwiesen wird, in das Feld **Zu decodierende Nachricht**. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

     `triggerOutputs()['body']`

     Oder geben Sie im Feld **Zu decodierende Nachricht** direkt diesen Ausdruck ein:

     `@triggerBody()`

     Der Ausdruck wird in das **Body**-Token aufgelöst.

     ![Screenshot des mehrinstanzenfähigen Designers mit aufgelöster Ausgabe der „Body“-Eigenschaft](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. Geben Sie für die Eigenschaft **Nachrichtenheader** der Aktion alle Header ein, die für die AS2-Aktion benötigt werden. Diese sind in der `headers`-Ausgabe vom Anforderungstrigger enthalten.

   1. Wählen Sie zum Eingeben eines Ausdrucks, mit dem auf die `headers`-Ausgabe des Triggers verwiesen wird, die Option **Switch Message headers to text mode** (Nachrichtenheader auf Textmodus umstellen) aus.

      ![Screenshot des mehrinstanzenfähigen Designers mit ausgewählter Option „Nachrichtenheader auf Textmodus umstellen“](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. Klicken Sie in das Feld **Nachrichtenheader**. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

      `triggerOutputs()['Headers']`

      In der Aktion **AS2-Decodierung** wird der Ausdruck jetzt als Token angezeigt:

      ![Screenshot des mehrinstanzenfähigen Designers mit dem Feld „Nachrichtenheader“ und dem Token „@triggerOutputs()['Headers']“](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit das Ausdruckstoken in das entsprechende **Headertoken** aufgelöst wird. Nach diesem Schritt sieht die Aktion **AS2-Decodierung** wie im folgenden Beispiel aus:

      ![Screenshot des mehrinstanzenfähigen Designers und der Ausgabe der aufgelösten Header des Triggers](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Wählen Sie unter dem Trigger die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

   ![Screenshot des einzelinstanzfähigen Designers und des Triggers mit ausgewähltem Pluszeichen](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus, sofern sie noch nicht ausgewählt ist. Geben Sie in das Suchfeld `as2` ein, und wählen Sie **AS2-Nachricht decodieren** aus.

   ![Screenshot des einzelinstanzfähigen Designers mit ausgewählter Aktion „AS2-Nachricht decodieren“](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. Wenn Sie aufgefordert werden, eine Verbindung mit Ihrem Integrationskonto herzustellen, geben Sie einen Namen für Ihre Verbindung an, und wählen Sie Ihr Integrationskonto und dann **Erstellen** aus.

1. Geben Sie für die **body**-Eigenschaft der Aktion die Eingabe ein, die von der AS2-Aktion decodiert werden soll, also die `body`-Ausgabe vom Anforderungstrigger. Sie haben mehrere Möglichkeiten, diesen Inhalt als Eingabe der Aktion anzugeben. Sie können ihn in der Liste mit dem dynamischen Inhalt auswählen oder einen Ausdruck verwenden:

   * Klicken Sie in das Feld der **body**-Eigenschaft, um in der Liste mit den verfügbaren Triggerausgaben eine Auswahl zu treffen. Wählen Sie in der Liste mit dem dynamischen Inhalt unter **Beim Empfang einer HTTP-Anforderung** den Eigenschaftswert **Text** (Body) aus. Beispiel:

     ![Screenshot des einzelinstanzfähigen Designers mit Auswahl in der Liste mit dem dynamischen Inhalt und ausgewählter „Body“-Eigenschaft](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > Wenn keine Triggerausgaben angezeigt werden, wählen Sie in der Liste mit den dynamischen Eigenschaften unter **Beim Empfang einer HTTP-Anforderung** die Option **Weitere anzeigen** aus.

   * Klicken Sie zum Eingeben eines Ausdrucks, mit dem auf die `body`-Ausgabe des Triggers verwiesen wird, in das Feld der **body**-Eigenschaft. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

     `triggerOutputs()['body']`

     Oder geben Sie im Feld der **body**-Eigenschaft direkt diesen Ausdruck ein:

     `@triggerBody()`

     Der Ausdruck wird in das **Body**-Token aufgelöst.

     ![Screenshot des einzelinstanzfähigen Designers mit aufgelöster Ausgabe der „Body“-Eigenschaft](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. Geben Sie im Feld der **Headers**-Eigenschaft alle Header ein, die für die AS2-Aktion benötigt werden. Diese sind in der `headers`-Ausgabe vom Anforderungstrigger enthalten.

   1. Wählen Sie zum Eingeben eines Ausdrucks, mit dem auf die `headers`-Ausgabe des Triggers verwiesen wird, die Option **Switch Message headers to text mode** (Nachrichtenheader auf Textmodus umstellen) aus.

      ![Screenshot des einzelinstanzfähigen Designers mit ausgewählter Option „Nachrichtenheader auf Textmodus umstellen“](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. Klicken Sie in das Feld der **Headers**-Eigenschaft. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

      `triggerOutputs()['Headers']`

      In der Aktion **AS2-Nachricht decodieren** wird der Ausdruck jetzt als Token angezeigt:

      ![Screenshot des einzelinstanzfähigen Designers mit der „Headers“-Eigenschaft und dem Token „@triggerOutputs()['Headers']“](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit das Ausdruckstoken in das entsprechende **Headertoken** aufgelöst wird. Nach diesem Schritt sieht die Aktion **AS2-Decodierung** wie im folgenden Beispiel aus:

      ![Screenshot des einzelinstanzfähigen Designers und der Ausgabe der aufgelösten Header des Triggers](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>Hinzufügen der Antwortaktion als Nachrichtenbestätigung

Zum Benachrichtigen der Parteien, dass die Nachricht empfangen wurde, können Sie eine Antwort mit einer Benachrichtigung über den AS2-Nachrichtenstatus (AS2 Message Disposition Notification, MDN) zurückgeben, indem Sie die Aktionen „Bedingung“ und „Antwort“ verwenden. Wenn Sie diese Aktionen unmittelbar nach der AS2-Aktion hinzufügen, kann der Logik-App-Workflow die Verarbeitung fortsetzen, wenn die AS2-Aktion erfolgreich ausgeführt wird. Andernfalls wird bei einem Fehler der AS2-Aktion die Verarbeitung durch den Logik-App-Workflow beendet.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Wählen Sie unter der Aktion **AS2-Decodierung** die Option **Neuer Schritt** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Integriert** aus, sofern sie noch nicht ausgewählt ist. Geben Sie im Suchfeld `condition`ein. Wählen Sie die Aktion **Bedingung** aus.

   ![Screenshot des mehrinstanzenfähigen Designers und der Aktion „Bedingung“](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   Jetzt wird die Form „Bedingung“ angezeigt, einschließlich der Pfade, die bestimmen, ob die Bedingung erfüllt ist.

   ![Screenshot des mehrinstanzenfähigen Designers und der Form „Bedingung“ mit leeren Pfaden](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. Geben Sie nun die auszuwertende Bedingung an. Geben Sie im Feld **Wert auswählen** den folgenden Ausdruck ein:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Stellen Sie im mittleren Feld sicher, dass der Vergleichsvorgang auf `is equal to` festgelegt ist. Geben Sie im Feld auf der rechten Seite den Wert `Expected` ein.

1. Speichern Sie Ihren Logik-App-Workflow. Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

   ![Screenshot des mehrinstanzenfähigen Designers und der Form „Bedingung“ mit einem Vorgang](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. Geben Sie nun die Antworten an, die zurückgegeben werden sollen, wenn die Aktion **AS2-Decodierung** erfolgreich bzw. nicht erfolgreich ist.

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Decodierung** erfolgreich ist, unter der Form **TRUE** die Option **Aktion hinzufügen** aus. Geben Sie unter **Vorgang auswählen** im Suchfeld `response` ein, und wählen Sie **Antwort** aus.

      ![Screenshot des mehrinstanzenfähigen Designers und der Aktion „Antwort“](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. Geben Sie die folgenden Ausdrücke an, um über die Ausgabe der Aktion **AS2-Decodierung** auf die Benachrichtigung über den AS2-Nachrichtenstatus (AS2 Message Disposition Notification, MDN) zuzugreifen:

      * Geben Sie in der **Headers**-Eigenschaft der Aktion **Antwort** diesen Ausdruck ein:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Geben Sie in der **Body**-Eigenschaft der Aktion **Antwort** diesen Ausdruck ein:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit die Ausdrücke als Token aufgelöst werden:

      ![Screenshot des mehrinstanzenfähigen Designers und des aufgelösten Ausdrucks für den Zugriff auf die AS2-MDN](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Decodierung** nicht erfolgreich ist, unter der Form **FALSE** die Option **Aktion hinzufügen** aus. Geben Sie unter **Vorgang auswählen** im Suchfeld `response` ein, und wählen Sie **Antwort** aus. Richten Sie die Aktion **Antwort** (Response) ein, um den gewünschten Status und Fehler zurückzugeben.

1. Speichern Sie Ihren Logik-App-Workflow.

### <a name="standard"></a>[Standard](#tab/standard)

1. Wählen Sie unter der Aktion **AS2-Nachricht decodieren** die Option **Neuen Schritt einfügen** (das Pluszeichen) und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter dem Suchfeld **Vorgang auswählen** die Option **Integriert** aus, sofern sie noch nicht ausgewählt ist. Geben Sie im Suchfeld `condition`ein. Wählen Sie die Aktion **Bedingung** aus.

   ![Screenshot des einzelinstanzfähigen Designers mit ausgewählter Aktion „Bedingung“](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   Jetzt wird die Form „Bedingung“ angezeigt, einschließlich der Pfade, die bestimmen, ob die Bedingung erfüllt ist.

   ![Screenshot des einzelinstanzfähigen Designers und der Form „Bedingung“ mit leeren Pfaden](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. Geben Sie nun die auszuwertende Bedingung an. Wählen Sie die Form **Bedingung** aus, um den Detailbereich anzuzeigen. Geben Sie im Feld **Wert auswählen** den folgenden Ausdruck ein:

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   Stellen Sie im mittleren Feld sicher, dass der Vergleichsvorgang auf `is equal to` festgelegt ist. Geben Sie im Feld auf der rechten Seite den Wert `Expected` ein.

1. Speichern Sie Ihren Logik-App-Workflow. Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

   ![Screenshot des einzelinstanzfähigen Designers und der Form „Bedingung“ mit einem Vorgang](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. Geben Sie nun die Antworten an, die zurückgegeben werden sollen, wenn die Aktion **AS2-Nachricht decodieren** erfolgreich bzw. nicht erfolgreich ist.

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Nachricht decodieren** erfolgreich ist, unter der Form **TRUE** das Pluszeichen und dann die Option **Aktion hinzufügen** aus. Geben Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Zeichenfolge `response` ein, und wählen Sie **Antwort** aus.

      ![Screenshot des einzelinstanzfähigen Designers und der Aktion „Antwort“](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. Geben Sie die folgenden Ausdrücke an, um über die Ausgabe der Aktion **AS2-Decodierung** auf die Benachrichtigung über den AS2-Nachrichtenstatus (AS2 Message Disposition Notification, MDN) zuzugreifen:

      * Geben Sie in der **Headers**-Eigenschaft der Aktion **Antwort** diesen Ausdruck ein:

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * Geben Sie in der **Body**-Eigenschaft der Aktion **Antwort** diesen Ausdruck ein:

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit die Ausdrücke als Token aufgelöst werden:

      ![Screenshot des einzelinstanzfähigen Designers und des aufgelösten Ausdrucks für den Zugriff auf die AS2-MDN](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Nachricht decodieren** nicht erfolgreich ist, unter der Form **FALSE** das Pluszeichen und dann die Option **Aktion hinzufügen** aus. Geben Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Zeichenfolge `response` ein, und wählen Sie **Antwort** aus. Richten Sie die Aktion **Antwort** (Response) ein, um den gewünschten Status und Fehler zurückzugeben.

1. Speichern Sie Ihren Logik-App-Workflow.

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>Hinzufügen der Aktion „X12-Nachricht decodieren“

Fügen Sie nun die Aktion **X12-Nachricht decodieren** hinzu.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Wählen Sie unter der Aktion **Antwort** (Response) die Option **Aktion hinzufügen** aus.

1. Geben Sie unter **Vorgang auswählen** im Suchfeld `x12 decode` ein, und wählen Sie **X12-Nachricht decodieren** aus.

   ![Screenshot des mehrinstanzenfähigen Designers und der ausgewählten Aktion „X12-Nachricht decodieren“](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. Gehen Sie wie folgt vor, wenn Sie bei der X12-Aktion zum Angeben von Verbindungsinformationen aufgefordert werden: Geben Sie den Namen für die Verbindung an, wählen Sie das gewünschte Integrationskonto aus, und wählen Sie dann die Option **Erstellen** aus.

   ![Screenshot des mehrinstanzenfähigen Designers und der Verbindung mit dem Integrationskonto](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. Geben Sie nun die Eingabe für die X12-Aktion an. In diesem Beispiel wird die Ausgabe der AS2-Aktion verwendet, wobei es sich um den Nachrichteninhalt handelt. Beachten Sie aber, dass dieser Inhalt im JSON-Objektformat vorliegt und Base64-codiert ist. Daher müssen Sie diesen Inhalt in eine Zeichenfolge konvertieren.

   Geben Sie im Feld **Zu decodierende X12-Flatfilenachricht** den folgenden Ausdruck ein, um die AS2-Ausgabe zu konvertieren:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. Speichern Sie Ihren Logik-App-Workflow. Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

    ![Screenshot des mehrinstanzenfähigen Designers und der Konvertierung von Base64-codiertem Inhalt in eine Zeichenfolge](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. Speichern Sie Ihren Logik-App-Workflow.

   Fahren Sie mit dem Hinzufügen der erforderlichen Aktionen in Ihrem Logik-App-Workflow fort, falls Sie dafür weitere Schritte benötigen, z. B. zum Decodieren des Nachrichteninhalts und Ausgeben des Inhalts im JSON-Objektformat.

### <a name="standard"></a>[Standard](#tab/standard)

1. Wählen Sie unter der Aktion **Antwort** das Pluszeichen und anschließend die Option **Aktion hinzufügen** aus. Wählen Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus, sofern sie noch nicht ausgewählt ist. Geben Sie in das Suchfeld `x12 decode` ein, und wählen Sie **X12-Nachricht decodieren** aus.

   ![Screenshot des einzelinstanzfähigen Designers und der ausgewählten Aktion „X12-Nachricht decodieren“](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. Gehen Sie wie folgt vor, wenn Sie bei der X12-Aktion zum Angeben von Verbindungsinformationen aufgefordert werden: Geben Sie den Namen für die Verbindung an, wählen Sie das gewünschte Integrationskonto aus, und wählen Sie dann die Option **Erstellen** aus.

   ![Screenshot des einzelinstanzfähigen Designers und der Verbindung mit dem Integrationskonto](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. Geben Sie nun die Eingabe für die X12-Aktion an. In diesem Beispiel wird die Ausgabe der AS2-Aktion verwendet, wobei es sich um den Nachrichteninhalt handelt. Beachten Sie aber, dass dieser Inhalt im JSON-Objektformat vorliegt und Base64-codiert ist. Daher müssen Sie diesen Inhalt in eine Zeichenfolge konvertieren.

   Geben Sie im Feld **Zu decodierende X12-Flatfilenachricht** den folgenden Ausdruck ein, um die AS2-Ausgabe zu konvertieren:

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. Speichern Sie Ihren Logik-App-Workflow. Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

    ![Screenshot des einzelinstanzfähigen Designers und der Konvertierung von Base64-codiertem Inhalt in eine Zeichenfolge](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. Speichern Sie Ihren Logik-App-Workflow erneut.

   Fahren Sie mit dem Hinzufügen der erforderlichen Aktionen in Ihrem Logik-App-Workflow fort, falls Sie dafür weitere Schritte benötigen, z. B. zum Decodieren des Nachrichteninhalts und Ausgeben des Inhalts im JSON-Objektformat.

---

Die Einrichtung Ihres B2B-Logik-App-Workflows ist damit abgeschlossen. In einer realen App werden die decodierten X12-Daten in einer branchenspezifischen App oder einem Datenspeicher gespeichert. Lesen Sie beispielsweise die folgende Dokumentation:

* [Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps](/logic-apps-using-sap-connector.md)

* [Überwachen, Erstellen und Verwalten von SFTP-Dateien mithilfe von SSH und Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Zum Herstellen von Verbindungen mit Ihren eigenen branchenspezifischen Apps und Verwenden dieser APIs in Ihrer Logik-App können Sie weitere Aktionen hinzufügen oder [benutzerdefinierte APIs schreiben](logic-apps-create-api-app.md).

## <a name="next-steps"></a>Nächste Schritte

* [Empfangen von und Antworten auf eingehende HTTPS-Aufrufe](../connectors/connectors-native-reqres.md)
* [Austauschen von AS2-Nachrichten für die B2B-Unternehmensintegration](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Weitere Informationen zum [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)

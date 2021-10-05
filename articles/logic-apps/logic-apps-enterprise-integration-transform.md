---
title: Transformieren von XML in Unternehmensintegrationsworkflows
description: Transformieren Sie XML mithilfe von Zuordnungen in Azure Logic Apps mit dem Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 027c1f44d756494432a076ec32f06e627f916b99
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553730"
---
# <a name="transform-xml-for-workflows-in-azure-logic-apps"></a>Transformieren von XML für Workflows in Azure Logic Apps

In B2B-Szenarios (Business-to-Business) für die Unternehmensintegration müssen Sie möglicherweise XML in ein anderes Format konvertieren. Ihr Logik-App-Workflow kann XML mithilfe der Aktion **XML transformieren** und einer vordefinierten [*Zuordnung*](logic-apps-enterprise-integration-maps.md) transformieren. Angenommen, Sie erhalten von einem Kunden regelmäßig B2B-Aufträge oder -Rechnungen im Datumsformat „JJJJMMTT“ (Jahr/Monat/Tag). In Ihrer Organisation wird jedoch das Datumsformat „MMTTJJJJ“ (Monat/Tag/Jahr) verwendet. Mithilfe einer Zuordnung können Sie das Format „JJJJMMTT“ in das Format „MMTTJJJJ“ transformieren, bevor Auftrags- oder Rechnungsdetails in Ihrer Datenbank für Kundenaktivitäten gespeichert werden.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie unter [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein Logik-App-Workflow, der bereits mit einem Trigger beginnt, sodass Sie die Aktion **XML transformieren** an der gewünschten Stelle in Ihrem Workflow hinzufügen können.

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden, für die Sie die Aktion **XML transformieren** verwenden möchten.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie ein Integrationskonto mit den folgenden Elementen:

    * Der [Zuordnung](logic-apps-enterprise-integration-maps.md) zum Transformieren von XML-Inhalten

    * Einer [Verbindung mit Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account)

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie keine Zuordnungen in Ihrem Integrationskonto speichern. In diesem Fall können Sie [Ihrer Logik-App-Ressource Zuordnungen direkt im Azure-Portal oder in Visual Studio Code hinzufügen](logic-apps-enterprise-integration-maps.md). Derzeit wird nur XSLT 1.0 unterstützt. Sie können diese Zuordnungen dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

    Sie benötigen dieses Konto jedoch, um Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)- und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Sie müssen Ihre Logik-App-Ressource jedoch nicht mit Ihrem Integrationskonto verknüpfen. Daher ist die Verknüpfungsfunktion nicht verfügbar. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

    > [!NOTE]
    > Derzeit unterstützt nur der Ressourcentyp **Logik-App (Verbrauch)** [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

## <a name="add-transform-xml-action"></a>Hinzufügen der Aktion „XML transformieren“

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App und den Workflow in der Designeransicht.

1. Fügen Sie im Falle einer leeren Logik-App ohne Trigger einen Trigger Ihrer Wahl hinzu. In diesem Beispiel wird der Anforderungstrigger verwendet. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   Geben Sie zum Hinzufügen des Anforderungstriggers im Suchfeld des Designers `HTTP request` ein, und wählen Sie den Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** aus.

1. Führen Sie unter dem Schritt in Ihrem Workflow, für den Sie die Aktion **XML transformieren** hinzufügen möchten, einen der folgenden Schritte aus:

   Wählen Sie für eine auf dem Verbrauchstarif oder dem ISE-Plan basierende Logik-App einen der folgenden Schritte aus:

   * Wenn Sie die Aktion **XML transformieren** am Ende des Workflows hinzufügen möchten, klicken Sie auf **Neuer Schritt**.

   * Wenn Sie die Aktion **XML transformieren** zwischen zwei vorhandenen Schritten hinzufügen möchten, zeigen Sie auf den Pfeil, der die beiden Schritte miteinander verbindet. Daraufhin wird ein Pluszeichen ( **+** ) angezeigt. Wählen Sie dieses Pluszeichen und anschließend die Option **Aktion hinzufügen** aus.

   Wählen Sie für eine auf dem Standardtarif basierende Logik-App einen der folgenden Schritt aus:

   * Wenn Sie die Aktion **XML transformieren** am Ende des Workflows hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) und anschließend auf **Aktion hinzufügen**.

   * Wenn Sie die Aktion **XML transformieren** zwischen zwei vorhandenen Schritten hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) zwischen diesen Schritten und dann auf **Aktion hinzufügen**.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld `transform xml`ein. Wählen Sie in der Liste der Aktionen die Aktion **XML transformieren** aus.

1. Verwenden Sie zur Angabe des XML-Inhalts, den Sie transformieren möchten, beliebige XML-Daten, die Sie in der HTTP-Anforderung erhalten. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird.

   Die Liste mit den dynamischen Inhalten enthält Eigenschaftstoken, die die Ausgaben der vorherigen Schritte im Workflow darstellen. Sollte eine erwartete Eigenschaft in der Liste fehlen, überprüfen Sie in der Trigger- oder Aktionsüberschrift der Liste, ob die Option **Mehr anzeigen** vorhanden ist.

   Für eine auf dem Verbrauchstarif oder dem ISE-Plan basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit mehreren Mandanten, mit geöffneter Liste „Dynamischer Inhalt“ und Cursor im Feld „Inhalt“](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   Für eine auf dem Standardtarif basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit einem Mandanten, mit geöffneter Liste „Dynamischer Inhalt“ und Cursor im Feld „Inhalt“](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. Wählen Sie in der Liste „Dynamischer Inhalt“ das Eigenschaftstoken für den Inhalt aus, den Sie überprüfen möchten.

   In diesem Beispiel wird das Token **Text** für den Trigger ausgewählt.

   > [!NOTE]
   > Vergewissern Sie sich, dass der ausgewählte Inhalt im XML-Format vorhanden ist. Befindet sich der Inhalt nicht im XML-Format oder ist er Base64-codiert, müssen Sie einen Ausdruck für die Verarbeitung des Inhalts angeben. Hierfür können Sie z. B. [Ausdrucksfunktionen](workflow-definition-language-functions-reference.md) wie `base64ToBinary()` zum Decodieren des Inhalts oder `xml()` zum Verarbeiten des Inhalts im XML-Format verwenden.

1. Öffnen Sie zur Angabe der Zuordnung für die Transformation die Liste **Zuordnung**, und wählen Sie die zuvor hinzugefügte Zuordnung aus.

1. Speichern Sie anschließend Ihren Logik-App-Workflow.

   Sie haben die Aktion **XML transformieren** nun fertig eingerichtet. Für eine reale App sollten Sie die transformierten Daten ggf. in einer branchenspezifischen App wie Salesforce speichern. Fügen Sie zum Senden der transformierten Ausgabe an Salesforce eine Salesforce-Aktion hinzu.

1. Wenn Sie die Transformationsaktion testen möchten, lösen Sie Ihren Workflow aus, und führen Sie ihn aus. Senden Sie beispielsweise für den Anforderungstrigger eine Anforderung an die Endpunkt-URL des Triggers.

   Die Aktion **XML transformieren** wird ausgeführt, wenn der Workflow ausgelöst wurde und XML-Inhalte für die Transformation verfügbar sind.

## <a name="advanced-capabilities"></a>Erweiterte Funktionen

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referenzassembly oder benutzerdefinierter Code aus Zuordnungen

In Workflows vom Typ **Logik-App (Verbrauch)** unterstützt die Aktion **XML transformieren** Zuordnungen, die auf eine externe Assembly verweisen. Weitere Informationen finden Sie unter [Hinzufügen von XSLT-Zuordnungen für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-maps.md#add-assembly).

### <a name="byte-order-mark"></a>Bytereihenfolge-Marke

Die Antwort der Transformation beginnt standardmäßig mit der Bytereihenfolge-Marke (Byte Order Mark, BOM). Sie können auf diese Funktion nur im Codeansicht-Editor zugreifen. Wenn Sie diese Funktion deaktivieren möchten, legen Sie die Eigenschaft `transformOptions` auf `disableByteOrderMark` fest:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von XSLT-Zuordnungen für XML-Transformationen in Azure Logic Apps](logic-apps-enterprise-integration-maps.md)
* [XML-Überprüfung für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
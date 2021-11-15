---
title: Überprüfung von XML in Unternehmensintegrationsworkflows
description: Überprüfen von XML mithilfe von Schemas in Workflows, die Azure Logic Apps und das Enterprise Integration Pack nutzen.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 20edb5f3b94e9014ce08d012cb0033a3bfb28db5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457466"
---
# <a name="validate-xml-in-workflows-with-azure-logic-apps"></a>Überprüfen von XML in Workflows mithilfe von Azure Logic Apps

In B2B-Szenarien für die Unternehmensintegration müssen die beteiligten Parteien per Vereinbarung häufig sicherstellen, dass die ausgetauschten Nachrichten gültig sind, bevor eine Datenverarbeitung gestartet werden kann. Ihr Logik-App-Workflow kann XML-Nachrichten und Dokumente mithilfe der Aktion **XML-Überprüfung** und eines vordefinierten [Schemas](logic-apps-enterprise-integration-schemas.md) überprüfen.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie in [B2B-Unternehmensintegrations-Workflows mit Azure Logic Apps und Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Der Logik-App-Workflow (leer oder bereits vorhanden), in dem Sie die Aktion **XML-Überprüfung** verwenden möchten.

  Wenn Sie über einen leeren Workflow verfügen, verwenden Sie einen Trigger nach Wahl. In diesem Beispiel wird der Anforderungstrigger verwendet.

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. zur Verwendung in Ihren Unternehmensintegrations- und B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Das Konto befindet sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource, in der Sie die Aktion **XML-Überprüfung*** verwenden möchten.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)**](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie ein Integrationskonto mit den folgenden Elementen:

    * Dem [Schema](logic-apps-enterprise-integration-schemas.md), das zum Validieren von XML-Inhalt verwendet werden soll.

    * Einer [Verbindung mit Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account)

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie keine Schemas in Ihrem Integrationskonto speichern. Stattdessen können Sie [Ihrer Logik-App-Ressource Schemas direkt im Azure-Portal oder in Visual Studio Code hinzufügen](logic-apps-enterprise-integration-schemas.md). Sie können diese Schemas dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

    Sie benötigen weiterhin ein Integrationskonto, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zu speichern und um die [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgänge zu verwenden. Allerdings brauchen Sie Ihre Logic-App-Ressource nicht mit Ihrem Integrationskonto verknüpfen, so dass die Verknüpfungsfunktionalität nicht besteht. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

    > [!NOTE]
    > Derzeit unterstützt nur der **Logic App (Verbrauch)** Ressourcentyp [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logic App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

## <a name="add-xml-validation-action"></a>Hinzufügen der XML-Überprüfungsaktion

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App und den Workflow in der Designeransicht.

1. Fügen Sie im Falle einer leeren Logik-App ohne Trigger einen Trigger Ihrer Wahl hinzu. In diesem Beispiel wird der Anforderungstrigger verwendet. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   Um den Anforderungstrigger hinzuzufügen, geben Sie im Suchfeld des Designers `HTTP request` ein, und wählen Sie den Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** aus.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, in dem Sie die Aktion **XML-Überprüfung** einfügen möchten, einen der folgenden Schritte aus:

   Wählen Sie für eine verbrauchsbasierte App oder eine auf dem ISE-Plan basierende Logik-App einen Schritt aus:

   * Wenn Sie die Aktion **XML-Überprüfung** am Ende des Workflows hinzufügen möchten, wählen Sie **Neuer Schritt** aus.

   * Wenn Sie die Aktion **XML-Überprüfung** zwischen vorhandenen Schritten einfügen möchten, zeigen Sie auf den Pfeil, der die beiden Schritte verbindet. Daraufhin wird ein Pluszeichen ( **+** ) angezeigt. Wählen Sie dieses Pluszeichen und anschließend die Option **Aktion hinzufügen** aus.

   Wählen Sie für eine auf dem Standard-Plan basierende Logik-App einen Schritt aus:

   * Um die Aktion **XML-Überprüfung** am Ende Ihres Workflows einzufügen, wählen Sie das Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

   * Um die Aktion **XML-Überprüfung** zwischen vorhandenen Schritten einzufügen, wählen Sie das Pluszeichen ( **+** ) zwischen diesen Schritten und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld `xml validation`ein. Wählen Sie in der Liste mit den Aktionen die Aktion **XML-Überprüfung** aus.

1. Klicken Sie zum Angeben des zu überprüfenden XML-Inhalts in das Feld **Inhalt**, um die dynamische Inhaltsliste anzuzeigen.

   Die Liste mit den dynamischen Inhalten enthält Eigenschaftstoken, die die Ausgaben der vorherigen Schritte im Workflow darstellen. Sollte eine erwartete Eigenschaft in der Liste fehlen, überprüfen Sie in der Trigger- oder Aktionsüberschrift der Liste, ob die Option **Mehr anzeigen** vorhanden ist.

   Für eine auf dem Verbrauchstarif oder dem ISE-Plan basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit mehreren Mandanten, mit geöffneter Liste „Dynamischer Inhalt“ und Cursor im Feld „Inhalt“](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Für eine auf dem Standardtarif basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit einem Mandanten, mit geöffneter Liste „Dynamischer Inhalt“ und Cursor im Feld „Inhalt“](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. Wählen Sie in der Liste „Dynamischer Inhalt“ das Eigenschaftstoken für den Inhalt aus, den Sie überprüfen möchten.

   In diesem Beispiel wird das Token **Text** für den Trigger ausgewählt.

1. Um das Schema anzugeben, das für die Validierung verwendet werden soll, öffnen Sie die Liste **Schemaname** und wählen das Schema aus, das Sie zuvor hinzugefügt haben.

1. Speichern Sie Ihren Logik-App-Workflow, wenn Sie fertig sind.

   Sie haben die Einrichtung Ihrer Aktion **XML-Überprüfung** abgeschlossen. In einer echten App empfiehlt es sich ggf., die überprüften Daten in einer branchenspezifischen App wie Salesforce zu speichern. Um die überprüfte Ausgabe an Salesforce zu senden, fügen Sie eine Salesforce-Aktion hinzu.

1. Um Ihre Überprüfungsaktion zu testen, lösen Sie die Ausführung Ihres Workflows aus. Senden Sie beispielsweise für den Anforderungstrigger eine Anforderung an die Endpunkt-URL des Triggers.

   Die Aktion **XML-Überprüfung** wird ausgeführt, nachdem der Workflow ausgelöst wurde und sobald XML-Inhalte zur Überprüfung verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Schemas für die XML-Überprüfung in Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [XML-Transformation für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-transform.md)

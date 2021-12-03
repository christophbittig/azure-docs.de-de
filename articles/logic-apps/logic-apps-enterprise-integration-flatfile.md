---
title: Codieren oder Decodieren von Flatfiles
description: Codieren oder Decodieren von Flatfiles für die Unternehmensintegration in Azure Logic Apps mithilfe des Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 5956f8fb7a1de3baaac0a69ef9e5b63d445416eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327118"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Codieren und Decodieren von Flatfiles in Azure Logic Apps

Bevor Sie XML-Inhalt in einem B2B-Szenario (Business-to-Business) an einen Geschäftspartner senden, möchten Sie den Inhalt eventuell zuerst codieren. Wenn Sie einen Logik-App-Workflow erstellen, können Sie Flatfiles mithilfe der [integrierten](../connectors/built-in.md#integration-account-built-in-actions) **Flatfile**-Aktionen codieren und decodieren.

Obwohl keine **Flatfile**-Trigger verfügbar sind, können Sie einen anderen Trigger oder eine andere Aktion verwenden, um den XML-Inhalt aus verschiedenen Quellen zur Codierung oder Decodierung in Ihren Workflow abzurufen oder in diesen einzuspeisen. Beispielsweise können Sie den Anforderungstrigger, eine andere App oder andere [Connectors verwenden, die von Azure Logic Apps unterstützt werden](../connectors/apis-list.md). Sie können **Flatfile**-Aktionen mit Workflows in den [Ressourcentypen **Logik-App (Verbrauch)** und **Logik-App (Standard)**](single-tenant-overview-compare.md) verwenden.

> [!NOTE]
> Für **Logik-App (Standard)** befinden sich die **Flatfile**-Aktionen derzeit in der Vorschauphase. 

In diesem Artikel wird gezeigt, wie Sie einem bestehenden Logik-App-Workflow die Flatfile-Aktionen für die Codierung und Decodierung hinzufügen. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen in der folgenden Dokumentation:

* [Was ist Azure Logic Apps?](logic-apps-overview.md)
* [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps und Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)**](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigen Sie ein Integrationskonto mit den folgenden Elementen:

    * Das Flatfile-[Schema](logic-apps-enterprise-integration-schemas.md), das zum Codieren oder Decodieren des XML-Inhalts verwendet werden soll.

    * Einer [Verbindung mit Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account)

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)**](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie keine Schemas in Ihrem Integrationskonto speichern. Stattdessen können Sie [Ihrer Logik-App-Ressource Schemas direkt im Azure-Portal oder in Visual Studio Code hinzufügen](logic-apps-enterprise-integration-schemas.md). Sie können diese Schemas dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

    Sie benötigen weiterhin ein Integrationskonto, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zu speichern und um die [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgänge zu verwenden. Allerdings brauchen Sie Ihre Logic-App-Ressource nicht mit Ihrem Integrationskonto verknüpfen, so dass die Verknüpfungsfunktionalität nicht besteht. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

    > [!NOTE]
    > Derzeit unterstützt nur der **Logic App (Verbrauch)** Ressourcentyp [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Der Logik-App-Workflow (leer oder bereits vorhanden), in dem Sie die **Flatfile**-Aktion verwenden möchten.

  Wenn Sie über einen leeren Workflow verfügen, verwenden Sie einen beliebigen Trigger zum Starten des Workflows. In diesem Beispiel wird der Anforderungstrigger verwendet.

## <a name="limits"></a>Einschränkungen

Vergewissern Sie sich, dass die enthaltenen XML-Gruppen in dem von Ihnen erstellten Flatfile-Schema keine übermäßige Anzahl der `max count`-Eigenschaft haben, die auf einen Wert *größer als 1* gesetzt ist. Vermeiden Sie die Verschachtelung einer XML-Gruppe mit einem `max count` Eigenschaftswert größer als 1 innerhalb einer anderen XML-Gruppe mit einer `max count` Eigenschaft größer als 1.

Jedes Mal, wenn das Flat-File-Schema die Wahl des nächsten Fragments zulässt, generiert die Azure Logic Apps-Engine, die das Schema analysiertt, ein *Symbol* und eine *Vorhersage* für dieses Fragment. Wenn das Schema zu viele solche Konstrukte zulässt, z. B. mehr als 100.000, wird die Schemaerweiterung übermäßig groß, was zu viel Ressourcen und Zeit verbraucht.

## <a name="add-flat-file-encoding-action"></a>Hinzufügen der Aktion „Flatfile-Codierung“

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Fügen Sie im Falle eines leeren Workflows ohne Trigger einen Trigger Ihrer Wahl hinzu. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb des Logik-App-Workflows werden verarbeitet. Führen Sie die folgenden Schritte aus, um den Anforderungstrigger hinzuzufügen:

   1. Wählen Sie im Suchfeld des Designers die Option **Integriert** aus. Geben Sie in das Suchfeld des Designers `HTTP request` ein.

   1. Wählen Sie aus der Triggerliste den Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

   > [!TIP]
   > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, für den Sie die Aktion **Flatfile-Codierung** hinzufügen möchten, eine Option aus:

   * Wenn Sie die Aktion **Flatfile-Codierung** am Ende des Workflows hinzufügen möchten, klicken Sie auf **Neuer Schritt**.

   * Wenn Sie die Aktion **Flatfile-Codierung** zwischen zwei vorhandenen Schritten hinzufügen möchten, zeigen Sie auf den Pfeil, der die beiden Schritte miteinander verbindet. Daraufhin wird ein Pluszeichen ( **+** ) angezeigt. Wählen Sie dieses Pluszeichen und anschließend die Option **Aktion hinzufügen** aus.

1. Geben Sie in das Suchfeld **Vorgang auswählen** die Zeichenfolge `flat file` ein. Wählen Sie in der Aktionenliste die Aktion namens **Flatfile-Codierung** aus.

   ![Screenshot des Azure-Portals und des Verbrauchs-Designers mit „Flatfile“ im Suchfeld und ausgewählter Aktion „Flatfile-Codierung“.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-consumption.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu codierende Inhalt enthält.

   ![Screenshot des Verbrauchs-Designers und der Eigenschaft „Inhalt“ mit dynamischer Inhaltsliste und ausgewähltem Inhalt zur Codierung.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-consumption.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** (Textkörper) nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus.
   > Sie können den zu decodierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Schemaname** das Schema aus, das in Ihrem verknüpften Integrationskonto für die Codierung vorhanden ist, z. B.:

   ![Screenshot des Verbrauchs-Designers und der geöffneten Liste „Schemaname“ mit ausgewähltem Schema, das für die Codierung verwendet werden soll.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-consumption.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihr Integrationskonto keine Schemadateien, die für die Codierung verwendet werden können. Laden Sie das Schema, das Sie verwenden möchten, in Ihr Integrationskonto hoch.

1. Speichern Sie den Workflow. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Führen Sie zum Testen Ihres Workflows eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie codieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile codieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die codierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die codierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Codieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Fügen Sie im Falle eines leeren Workflows ohne Trigger einen Trigger Ihrer Wahl hinzu. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb des Logik-App-Workflows werden verarbeitet. Führen Sie die folgenden Schritte aus, um den Anforderungstrigger hinzuzufügen:

   1. Wählen Sie im Designer **Vorgang auswählen** aus. Wählen Sie in dem geöffneten Bereich **Vorgang auswählen** unter dem Suchfeld **Integriert** aus.

   1. Geben Sie im Suchfeld `HTTP request`ein. Wählen Sie aus der Triggerliste den Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

     > [!TIP]
     > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, für den Sie die Aktion **Flatfile-Codierung** hinzufügen möchten, eine Option aus:

   * Wenn Sie die Aktion **Flatfile-Codierung** am Ende des Workflows hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) und anschließend auf **Aktion hinzufügen**.

   * Wenn Sie die Aktion **Flatfile-Codierung** zwischen zwei vorhandenen Schritten hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) zwischen diesen Schritten und dann auf **Neuen Schritt hinzufügen**.

1. Wählen Sie in dem geöffneten Bereich **Vorgang auswählen** unter dem Suchfeld **Integriert** aus.

1. Geben Sie im Suchfeld `flat file`ein. Wählen Sie in der Aktionenliste die Aktion namens **Flatfile-Codierung** aus.

   ![Screenshot des Azure-Portals und des Standardworkflow-Designers mit „Flatfile“ im Suchfeld und ausgewählter Aktion „Flatfile-Codierung“.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-standard.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu codierende Inhalt enthält.

   ![Screenshot des Standardworkflow-Designers und der Eigenschaft „Inhalt“ mit dynamischer Inhaltsliste und ausgewähltem Inhalt zur Codierung.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-standard.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** (Textkörper) nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus.
   > Sie können den zu codierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Name** das Schema aus, das Sie zuvor zur Codierung in Ihre Logik-App-Ressource hochgeladen haben. Beispiel:

   ![Screenshot des Standardworkflow-Designers und der geöffneten Liste „Name“ mit ausgewähltem Schema, das für die Codierung verwendet werden soll.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-standard.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihre Logik-App-Standardressource keine Schemadateien, die für die Codierung verwendet werden können. Erfahren Sie, wie Sie [das Schema, das Sie verwenden möchten, in Ihre Logik-App-Standardressource hochladen](logic-apps-enterprise-integration-schemas.md).

1. Speichern Sie den Workflow. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Führen Sie zum Testen Ihres Workflows eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie codieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile codieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die codierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die codierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Codieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

---

## <a name="add-flat-file-decoding-action"></a>Hinzufügen der Aktion „Flatfile-Decodierung“

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Fügen Sie im Falle eines leeren Workflows ohne Trigger einen Trigger Ihrer Wahl hinzu. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb des Logik-App-Workflows werden verarbeitet. Führen Sie die folgenden Schritte aus, um den Anforderungstrigger hinzuzufügen:

   1. Wählen Sie im Suchfeld des Designers die Option **Integriert** aus. Geben Sie in das Suchfeld des Designers `HTTP request` ein.

   1. Wählen Sie aus der Triggerliste den Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

   > [!TIP]
   > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, für den Sie die Aktion **Flatfile-Decodierung** hinzufügen möchten, eine Option aus:

   * Wenn Sie die Aktion **Flatfile-Decodierung** am Ende des Workflows hinzufügen möchten, klicken Sie auf **Neuer Schritt**.

   * Wenn Sie die Aktion **Flatfile-Decodierung** zwischen zwei vorhandenen Schritten hinzufügen möchten, zeigen Sie auf den Pfeil, der die beiden Schritte miteinander verbindet. Daraufhin wird ein Pluszeichen ( **+** ) angezeigt. Wählen Sie dieses Pluszeichen und anschließend die Option **Aktion hinzufügen** aus.

1. Geben Sie in das Suchfeld **Vorgang auswählen** die Zeichenfolge `flat file` ein. Wählen Sie in der Aktionenliste die Aktion namens **Flatfile-Decodierung** aus.

   ![Screenshot des Azure-Portals und des Verbrauchs-Designers mit „Flatfile“ im Suchfeld und ausgewählter Aktion „Flatfile-Decodierung“.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-consumption.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu decodierende Inhalt enthält.

   ![Screenshot des Verbrauchs-Designers und der Eigenschaft „Inhalt“ mit dynamischer Inhaltsliste und ausgewähltem Inhalt zur Decodierung.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-consumption.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** (Textkörper) nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus. Sie können den zu decodierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Schemaname** das Schema aus, das in Ihrem verknüpften Integrationskonto für die Decodierung vorhanden ist, z. B.:

   ![Screenshot des Verbrauchs-Designers und der geöffneten Liste „Schemaname“ mit ausgewähltem Schema, das für die Decodierung verwendet werden soll.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-consumption.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihr Integrationskonto keine Schemadateien, die für die Decodierung verwendet werden können. Laden Sie das Schema, das Sie verwenden möchten, in Ihr Integrationskonto hoch.

1. Speichern Sie den Workflow. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Führen Sie zum Testen Ihres Workflows eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie decodieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile decodieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die decodierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die decodierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Decodieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Fügen Sie im Falle eines leeren Workflows ohne Trigger einen Trigger Ihrer Wahl hinzu. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb des Logik-App-Workflows werden verarbeitet. Führen Sie die folgenden Schritte aus, um den Anforderungstrigger hinzuzufügen:

   1. Wählen Sie im Designer **Vorgang auswählen** aus. Wählen Sie in dem geöffneten Bereich **Vorgang auswählen** unter dem Suchfeld **Integriert** aus.

   1. Geben Sie im Suchfeld `HTTP request`ein. Wählen Sie aus der Triggerliste den Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

     > [!TIP]
     > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, für den Sie die Aktion **Flatfile-Decodierung** hinzufügen möchten, eine Option aus:

   * Wenn Sie die Aktion **Flatfile-Decodierung** am Ende des Workflows hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) und anschließend auf **Aktion hinzufügen**.

   * Wenn Sie die Aktion **Flatfile-Decodierung** zwischen zwei vorhandenen Schritten hinzufügen möchten, klicken Sie auf das Pluszeichen ( **+** ) zwischen diesen Schritten und dann auf **Neuen Schritt hinzufügen**.

1. Wählen Sie in dem geöffneten Bereich **Vorgang auswählen** unter dem Suchfeld **Integriert** aus.

1. Geben Sie im Suchfeld `flat file`ein. Wählen Sie in der Aktionenliste die Aktion namens **Flatfile-Decodierung** aus.

   ![Screenshot des Azure-Portals und des Standardworkflow-Designers mit „Flatfile“ im Suchfeld und ausgewählter Aktion „Flatfile-Decodierung“.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-standard.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu decodierende Inhalt enthält.

   ![Screenshot des Standardworkflow-Designers und der Eigenschaft „Inhalt“ mit dynamischer Inhaltsliste und ausgewähltem Inhalt zur Decodierung.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-standard.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** (Textkörper) nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus.
   > Sie können den zu decodierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Name** das Schema aus, das Sie zuvor zur Decodierung in Ihre Logik-App-Ressource hochgeladen haben. Beispiel:

   ![Screenshot des Standardworkflow-Designers und der geöffneten Liste „Name“ mit ausgewähltem Schema, das für die Decodierung verwendet werden soll.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-standard.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihre Logik-App-Standardressource keine Schemadateien, die für die Decodierung verwendet werden können. Erfahren Sie, wie Sie [das Schema, das Sie verwenden möchten, in Ihre Logik-App-Standardressource hochladen](logic-apps-enterprise-integration-schemas.md).

1. Speichern Sie den Workflow. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Führen Sie zum Testen Ihres Workflows eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie decodieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile decodieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die decodierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die decodierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Decodieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

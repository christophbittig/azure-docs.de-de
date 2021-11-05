---
title: Codieren oder Decodieren von Flatfiles
description: Codieren oder Decodieren von Flatfiles für die Unternehmensintegration in Azure Logic Apps mithilfe des Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/01/2021
ms.openlocfilehash: 73b7538c41f1a560d07a702660a28f41b2c0e1de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072444"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Codieren und Decodieren von Flatfiles in Azure Logic Apps

Bevor Sie XML-Inhalt in einem B2B-Szenario (Business-to-Business) an einen Geschäftspartner senden, möchten Sie den Inhalt eventuell zuerst codieren. Indem Sie eine Logik-App erstellen, können Sie Flatfiles mithilfe des **Flatfile**-Connectors codieren und decodieren. Ihre Logik-App kann diesen XML-Inhalt aus verschiedenen Quellen abrufen, z. B. dem Anforderungstrigger, einer anderen App oder anderen [von Azure Logic Apps unterstützten Connectors](../connectors/apis-list.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und [mit der Logik-App verknüpft](./logic-apps-enterprise-integration-create-integration-account.md#link-account) ist, in der Sie den **Flatfile**-Connector verwenden möchten. Sowohl Ihre Logik-App als auch Ihr Integrationskonto müssen an demselben Standort oder in derselben Azure-Region vorhanden sein.

* Mindestens zwei [Handelspartner](logic-apps-enterprise-integration-partners.md), die Sie bereits in Ihrem Integrationskonto definiert haben.

* Ein Flatfile-[Schema](logic-apps-enterprise-integration-schemas.md), das Sie zum Codieren oder Decodieren des XML-Inhalts in Ihr Integrationskonto hochgeladen haben.

* Die Logik-App, in der Sie den **Flatfile**-Connector und einen Trigger verwenden möchten, der den Workflow Ihre Logik-App startet. Der **Flatfile**-Connector stellt nur Aktionen und keine Trigger bereit. Sie können entweder den Trigger oder eine andere Aktion verwenden, um den XML-Inhalt zur Codierung oder Decodierung an Ihre Logik-App zu übertragen. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Einschränkungen

Vergewissern Sie sich, dass die enthaltenen XML-Gruppen in dem von Ihnen erstellten Flatfile-Schema keine übermäßige Anzahl der `max count`-Eigenschaft haben, die auf einen Wert *größer als 1* gesetzt ist. Vermeiden Sie die Verschachtelung einer XML-Gruppe mit einem `max count` Eigenschaftswert größer als 1 innerhalb einer anderen XML-Gruppe mit einer `max count` Eigenschaft größer als 1.

Jedes Mal, wenn das Flat-File-Schema die Wahl des nächsten Fragments zulässt, generiert die Azure Logic Apps-Engine, die das Schema analysiertt, ein *Symbol* und eine *Vorhersage* für dieses Fragment. Wenn das Schema zu viele solche Konstrukte zulässt, z. B. mehr als 100.000, wird die Schemaerweiterung übermäßig groß, was zu viel Ressourcen und Zeit verbraucht.

## <a name="add-flat-file-encode-action"></a>Aktion „Flatfile-Codierung“ hinzufügen

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie in Ihrer Logik-App unter dem Trigger oder der Aktion **Neuer Schritt** > **Aktion hinzufügen** aus. In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb der Logik-App werden verarbeitet.

   > [!TIP]
   > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Geben Sie unter **Aktion auswählen** den Wert `flat file` ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Flatfile-Codierung**

   ![Aktion „Flatfile-Codierung“ auswählen](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu codierende Inhalt enthält.

   ![Zu codierenden Inhalt aus der dynamischen Inhaltsliste auswählen](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus.
   > Sie können den zu decodierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Schemaname** das Schema aus, das in Ihrem verknüpften Integrationskonto für die Codierung vorhanden ist, z. B.:

   ![Auswählen des für die Codierung zu verwendenden Schemas](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihr Integrationskonto keine Schemadateien, die für die Codierung verwendet werden können. Laden Sie das Schema, das Sie verwenden möchten, in Ihr Integrationskonto hoch.

1. Speichern Sie Ihre Logik-App. Führen Sie zum Testen Ihres Connectors eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie codieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile codieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die codierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die codierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Codieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Aktion „Flatfile decodieren“ hinzufügen

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie in Ihrer Logik-App unter dem Trigger oder der Aktion **Neuer Schritt** > **Aktion hinzufügen** aus. In diesem Beispiel wird der Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** verwendet, und eingehende Anforderungen von außerhalb der Logik-App werden verarbeitet.

   > [!TIP]
   > Das Bereitstellen eines JSON-Schemas ist optional. Wenn Sie eine Beispielnutzlast aus der eingehenden Anforderung besitzen, wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden**, geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig** aus. Das Schema wird im Feld **JSON-Schema für Anforderungstext** angezeigt.

1. Geben Sie unter **Aktion auswählen** den Wert `flat file` ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Flatfile-Decodierung**

   ![Aktion „Flatfile-Decodierung“ auswählen](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Klicken Sie in das Feld **Inhalt**, sodass die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste im Abschnitt **Beim Empfang einer HTTP-Anforderung** die Eigenschaft **Body** aus, die die Anforderungstextausgabe des Triggers sowie den zu decodierende Inhalt enthält.

   ![Zu decodierenden Inhalt aus der dynamischen Inhaltsliste auswählen](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Wenn die Eigenschaft **Body** nicht in der dynamischen Inhaltsliste angezeigt wird, wählen Sie **Mehr anzeigen** neben der Abschnittsbezeichnung **Beim Empfang einer HTTP-Anforderung** aus. Sie können den zu decodierenden Inhalt auch direkt in das Feld **Inhalt** eingeben.

1. Wählen Sie in der Liste **Schemaname** das Schema aus, das in Ihrem verknüpften Integrationskonto für die Decodierung vorhanden ist, z. B.:

   ![Auswählen des für die Decodierung zu verwendenden Schemas](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Wenn in der Liste kein Schema angezeigt wird, enthält Ihr Integrationskonto keine Schemadateien, die für die Decodierung verwendet werden können. Laden Sie das Schema, das Sie verwenden möchten, in Ihr Integrationskonto hoch.

1. Speichern Sie Ihre Logik-App. Führen Sie zum Testen Ihres Connectors eine Anforderung an den HTTPS-Endpunkt aus, der in der Eigenschaft **HTTP POST URL** des Anforderungstriggers angezeigt wird, und schließen Sie den XML-Inhalt, den Sie decodieren möchten, in den Anforderungstext ein.

Das Einrichten der Aktion „Flatfile decodieren“ ist damit abgeschlossen. In einer echten App empfiehlt es sich ggf., die decodierten Daten in einer branchenspezifischen App wie Salesforce zu speichern. Sie können die decodierten Daten auch an einen Handelspartner senden. Um die Ausgabe der Decodieraktion an Salesforce oder Ihren Handelspartner zu senden, verwenden Sie die anderen [in Azure Logic Apps verfügbaren Connectors](../connectors/apis-list.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

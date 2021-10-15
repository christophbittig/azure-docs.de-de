---
title: Senden von Benachrichtigungen an einen Microsoft Teams-Kanal
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Benachrichtigungen von einer Azure Data Factory- oder Synapse Analytics-Pipeline an einen Microsoft Teams-Kanal senden.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: d1a23e166c322a4a74c3ec175ea672d78ed23de9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368154"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>Senden von Benachrichtigungen von einer Azure Data Factory- oder Synapse Analytics-Pipeline an einen Microsoft Teams-Kanal

Es ist häufig erforderlich, während oder nach der Ausführung einer Pipeline Benachrichtigungen zu senden. Benachrichtigungen ermöglichen proaktive Warnungen und verringern die Notwendigkeit einer reaktiven Überwachung zur Ermittlung von Problemen.  Informieren Sie sich auch über das [Senden von E-Mail-Benachrichtigungen mithilfe von Logik-Apps](tutorial-control-flow-portal.md#create-email-workflow-endpoints), die von einer Data Factory- oder Synapse-Pipeline aufgerufen werden können.  Viele Unternehmen verwenden zunehmend Microsoft Teams für die Zusammenarbeit.  In diesem Artikel erfahren Sie, wie Sie Benachrichtigungen von Pipelinewarnungen in Microsoft Teams konfigurieren. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Benachrichtigungen von Ihren Pipelines an Teams senden können, müssen Sie einen [Incoming Webhook](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using) (eingehender Webhook) für Ihren Teams-Kanal erstellen. Wenn Sie zu diesem Zweck einen neuen Teams-Kanal erstellen müssen, lesen Sie die [Teams-Dokumentation](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525).  

1.  Öffnen Sie Microsoft Teams, und wechseln Sie zur Registerkarte „Apps“. Suchen Sie nach „Incoming Webhook“, und wählen Sie den Incoming Webhook-Connector aus.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="Abbildung der Incoming Webhook-App auf der Registerkarte „Apps“ in Teams":::

1.  Fügen Sie den Connector der Teams-Website hinzu, an die Sie die Benachrichtigungen senden möchten.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="Hervorgehobene Schaltfläche &quot;Zu einem Team hinzufügen&quot; für die Incoming Webhook-App":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="Abbildung der Aufforderung zur Teamauswahl im Konfigurationsdialogfeld für die Incoming Webhook-App in Teams":::

1.  Benennen Sie den Webhook nach Bedarf, und laden Sie optional ein Symbol zur Identifikation Ihrer Nachrichten hoch.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="Hervorgehobene name-Eigenschaft, optionaler Bildupload und Schaltfläche &quot;Erstellen&quot; auf der Optionsseite für den eingehenden Webhook":::  

1.  Kopieren Sie die Webhook-URL des Speichers, der bei der Erstellung generiert wird, zur späteren Verwendung in ADF.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="Abbildung der neuen Webhook-URL auf der Optionsseite für den eingehenden Webhook nach der Erstellung":::

1.  Die Benachrichtigung wird in dem Kanal angezeigt, in dem Sie den Webhookconnector hinzufügen.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="Abbildung der Benachrichtigung in dem Kanal, in dem Sie den Webhookconnector hinzugefügt haben":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>Schritte zum Senden von Benachrichtigungen an einen Teams-Kanal aus einer Pipeline:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1.  Erstellen Sie eine neue **Pipeline über eine Vorlage**. Der Vorlagenkatalog bietet eine Pipelinevorlage, die den Einstieg in Teams-Benachrichtigungen vereinfacht.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="Abbildung des Menüs &quot;Pipeline über Vorlage&quot; in Azure Data Factory Studio":::

1.  Suchen Sie nach „Teams“, und wählen Sie dann die Vorlage für das **Senden von Benachrichtigungen an einen Kanal in Microsoft Teams** zur Verwendung aus.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="Abbildung der Vorlage für das &quot;Senden von Benachrichtigungen an einen Kanal in Microsoft Teams&quot; im Vorlagenkatalog":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="Abbildung mit Details der Vorlage für das &quot;Senden von Benachrichtigungen an einen Kanal in Microsoft Teams&quot; nach deren Auswahl im Vorlagenkatalog":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Abbildung der Eigenschaften der Pipeline, die mithilfe der Vorlage zum &quot;Senden von Benachrichtigung an einen Kanal in Microsoft Teams&quot; erstellt wurde":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  Erstellen Sie eine neue **Pipeline über eine Vorlage**. Der Vorlagenkatalog bietet eine Pipelinevorlage, die den Einstieg in Teams-Benachrichtigungen vereinfacht.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="Abbildung des Menüs &quot;Pipeline über Vorlage&quot; in Azure Data Factory Studio":::

1.  Suchen Sie nach „Teams“, und wählen Sie dann die Vorlage für das **Senden von Benachrichtigungen an einen Kanal in Microsoft Teams** zur Verwendung aus.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="Abbildung der Vorlage für das &quot;Senden von Benachrichtigungen an einen Kanal in Microsoft Teams&quot; im Vorlagenkatalog":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="Abbildung mit Details der Vorlage für das &quot;Senden von Benachrichtigungen an einen Kanal in Microsoft Teams&quot; nach deren Auswahl im Vorlagenkatalog":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Abbildung der Eigenschaften der Pipeline, die mithilfe der Vorlage zum &quot;Senden von Benachrichtigung an einen Kanal in Microsoft Teams&quot; erstellt wurde":::

---

3.  Es wird empfohlen, die **Abonnement-ID**, die **Ressourcengruppe** und die **Teams-Webhook-URL** (siehe [Voraussetzungen](#prerequisites)) der aktuellen Data Factory als Standardwerte für die jeweiligen Parameter hinzuzufügen.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="Abbildung der empfohlenen Eigenschaften der Pipeline, die mithilfe der Vorlage zum &quot;Senden von Benachrichtigung an einen Kanal in Microsoft Teams&quot; erstellt wurde":::

    Diese Parameter werden zum Erstellen der Überwachungs-URL verwendet. Angenommen, Sie stellen kein gültiges Abonnement und keine Ressourcengruppe bereit (für die Data Factory, zu der die Pipelines gehören). In diesem Fall enthält die Benachrichtigung keine gültige Pipelineüberwachungs-URL, die Nachrichten funktionieren aber trotzdem.  Darüber hinaus verhindert das Hinzufügen dieser Parameter, dass diese Werte immer von einer anderen Pipeline übergeben werden müssen. Wenn Sie beabsichtigen, diese Werte mithilfe eines metadatengesteuerten Ansatzes festzulegen, sollten Sie sie entsprechend anpassen.
    
1.  Fügen Sie der Pipeline eine Aktivität **Pipeline ausführen** hinzu, von der aus Sie Benachrichtigungen an den Teams-Kanal senden möchten. Wählen Sie die Pipeline aus der Vorlage für das **Senden von Benachrichtigung an einen Kanal in Microsoft Teams** als **Aufgerufene Pipeline** in der Aktivität **Pipeline ausführen** aus.

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="Abbildung der Aktivität &quot;Pipeline ausführen&quot; in der Pipeline, die mithilfe der Vorlage zum &quot;Senden von Benachrichtigung an einen Kanal in Microsoft Teams&quot; erstellt wurde":::

1.  Passen Sie die Parameter nach Bedarf an den Aktivitätstyp an.

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="Abbildung der Anpassung der Parameter in der Pipeline, die mithilfe der Vorlage zum &quot;Senden von Benachrichtigung an einen Kanal in Microsoft Teams&quot; erstellt wurde":::   
  
1.  Empfangen Sie die Benachrichtigungen in Teams.

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="Abbildung von Pipelinebenachrichtigungen in einem Teams-Kanal":::
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Hinzufügen dynamischer Nachrichten mit Systemvariablen und Ausdrücken

Mithilfe von [Systemvariablen](control-flow-system-variables.md) und [Ausdrücken](control-flow-expression-language-functions.md) können Sie Ihre Nachrichten dynamisch gestalten. Beispiel:  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Die oben genannten Ausdrücke geben die relevanten Fehlermeldungen zu einem Fehler zurück, die dann als Benachrichtigung an einen Teams-Kanal gesendet werden können. Weitere Informationen finden Sie im Artikel [Ausgabeeigenschaften der Copy-Aktivität](copy-activity-monitoring.md).

Außerdem wird empfohlen, das von Microsoft Teams unterstützte [Schema für die Benachrichtigungsnutzdaten](https://adaptivecards.io/explorer/AdaptiveCard.html) zu überprüfen und die obige Vorlage weiter an Ihre Anforderungen anzupassen.

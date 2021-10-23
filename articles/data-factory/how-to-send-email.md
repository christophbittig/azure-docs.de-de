---
title: Senden von E-Mails
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie eine E-Mail mit einer Azure Data Factory- oder Azure Synapse-Pipeline senden.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668901"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>Senden einer E-Mail mit einer Azure Data Factory- oder Azure Synapse-Pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Es ist häufig erforderlich, während oder nach der Ausführung einer Pipeline Benachrichtigungen zu senden. Benachrichtigungen ermöglichen proaktive Warnungen und verringern die Notwendigkeit einer reaktiven Überwachung zur Ermittlung von Problemen.  In diesem Artikel erfahren Sie, wie Sie E-Mail-Benachrichtigungen von einer Azure Data Factory- oder Azure Synapse-Pipeline konfigurieren. 

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- **Logik-App.** Um das Senden einer E-Mail auszulösen, definieren Sie den Workflow mithilfe von [Logic Apps](../logic-apps/logic-apps-overview.md). Ausführliche Informationen zum Erstellen eines Logic-App-Workflows finden Sie unter [How to create a logic app (Vorgehensweise: Erstellen einer Logic-App)](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-email-workflow-in-your-logic-app"></a>Erstellen des E-Mail-Workflows in Ihrer Logik-App

Erstellen Sie einen Logik-App-Workflow mit dem Namen `SendEmailFromPipeline`. Definieren Sie den Workflow-Trigger als `When an HTTP request is received`, und fügen Sie eine Aktion von `Office 365 Outlook – Send an email (V2)` hinzu.

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="Logik-App-Workflow-Designer mit „E-Mail senden (V2)“. Aktion von einem HTTP-Anforderungstrigger":::

Geben Sie für den HTTP-Anforderungstrigger den folgenden JSON-Code für `Request Body JSON Schema` an:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Die HTTP-Anforderung im Logik-App-Designer sollte wie folgt aussehen:

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="Logik-App-Workflow-Designer für den HTTP-Anforderungstrigger mit aufgefülltem Feld „JSON-Schema für Anforderungstext“":::

Passen Sie für die Aktion **E-Mail senden (V2)** die Formatierungseinstellungen der E-Mail mithilfe der Eigenschaften aus dem JSON-Schema für den Anforderungstext nach Bedarf an:

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="Logik-App-Workflow-Designer für die Aktion „E-Mail senden (V2)“":::

Speichern Sie den Workflow. Notieren Sie sich dann die Workflow-URL für Ihren neuen Workflow:

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="Registerkarte „Übersicht“ im Logik-App-Workflow mit hervorgehobener Workflow-URL":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>Erstellen einer Pipeline zum Auslösen Ihres Logik-App-E-Mail-Workflows

Nachdem Sie den Logik-App-Workflow zum Senden von E-Mails erstellt haben, können Sie ihn über eine Pipeline mithilfe einer Aktivität vom Typ **Web** auslösen.  

1. Erstellen Sie eine neue Pipeline, und suchen Sie in der Kategorie **Allgemein** nach der Aktivität vom Typ **Web**, um sie in den Bearbeitungsbereich zu ziehen.

1. Wählen Sie die Aktivität **Web1** und anschließend die Registerkarte **Einstellungen** aus.

   Geben Sie im Feld **URL** die URL aus dem Logik-App-Workflow an, die Sie zuvor erstellt haben.

   Geben Sie als **Textkörper** den folgenden JSON-Code ein:
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    Verwenden Sie dynamische Ausdrücke, um nützliche Meldungen für Ereignisse in Ihren Pipelines zu generieren.  Beachten Sie, dass das JSON-Format hier dem JSON-Format entspricht, das Sie in der Logik-App definiert haben. Sie können diese auch nach Bedarf anpassen.
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="Pipeline mit einer konfigurierten Webaktivität sowie der URL des Logik-App-Workflows und dem JSON-Nachrichtentext":::

1. Wählen Sie den Hintergrundbereich des Pipeline-Designers aus, um die Eigenschaftenseite der Pipeline auszuwählen und einen neuen Parameter namens „receiver“ hinzuzufügen. Geben Sie dabei eine E-Mail-Adresse als Standardwert an.
   
   In diesem Beispiel wird die E-Mail-Adresse des Empfängers auf der Grundlage eines Pipelineparameters angegeben, den Sie beliebig definieren.  Der Wert für „receiver“ kann aus einem beliebigen Ausdruck oder sogar aus verknüpften Datenquellen übernommen werden.

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="Konfiguration des Parameters „receiver“ im Pipeline-Designer":::

1. Veröffentlichen Sie Ihre Pipeline, und lösen Sie sie dann manuell aus, um zu überprüfen, ob die E-Mail wie erwartet gesendet wird.

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="Manuelles Auslösen der Pipeline"::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Hinzufügen dynamischer Nachrichten mit Systemvariablen und Ausdrücken

Mithilfe von [Systemvariablen](control-flow-system-variables.md) und [Ausdrücken](control-flow-expression-language-functions.md) können Sie Ihre Nachrichten dynamisch gestalten. Beispiel:  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Die oben genannten Ausdrücke geben die relevanten Fehlermeldungen zu einem Fehler der Copy-Aktivität zurück, die dann an Ihre Webaktivität umgeleitet werden können, die die E-Mail sendet. Ausführlichere Informationen finden Sie im Artikel [Ausgabeeigenschaften der Copy-Aktivität](copy-activity-monitoring.md).

## <a name="next-steps"></a>Nächste Schritte

[Senden von Teams-Benachrichtigungen über eine Pipeline](how-to-send-notifications-to-teams.md)

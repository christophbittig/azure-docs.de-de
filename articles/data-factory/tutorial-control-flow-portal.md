---
title: Verzweigen und Verketten von Aktivitäten in einer Pipeline mithilfe des Azure-Portals
description: Hier erfahren Sie, wie Sie den Datenfluss in einer Azure Data Factory-Pipeline über das Azure-Portal steuern.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: e3ed05264ab942383d2828384d36f2a5817ddbe6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806051"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Verzweigen und Verketten von Aktivitäten in einer Azure Data Factory-Pipeline mithilfe des Azure-Portals

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Data Factory-Pipeline, die einige Ablaufsteuerungsfunktionen vorstellt. Diese Pipeline führt eine einfache Kopieraktivität aus einem Container in Azure Blob Storage in einen anderen Container im selben Speicherkonto durch. War die Kopieraktivität erfolgreich, sendet die Pipeline eine E-Mail mit Details zum erfolgreichen Kopiervorgang (beispielsweise die geschriebene Datenmenge). War die Kopieraktivität nicht erfolgreich, sendet die Pipeline eine E-Mail mit Fehlerdetails (beispielsweise die Fehlermeldung). In diesem Tutorial erfahren Sie, wie Sie Parameter übergeben.

Eine allgemeine Übersicht über das Szenario: :::image type="content" source="media/tutorial-control-flow-portal/overview.png" alt-text="Diagramm: Azure Blob Storage als Ziel für einen Kopiervorgang. Bei erfolgreichem Vorgang wird eine E-Mail mit Details gesendet, bei nicht erfolgreichem Vorgang wird eine E-Mail mit Fehlerdetails gesendet.":::

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen eines verknüpften Azure Storage Diensts.
> * Erstellen eines Azure-Blobdatasets.
> * Erstellen einer Pipeline, die eine Kopieraktivität und eine Webaktivität enthält.
> * Senden von Aktivitätsausgaben an nachfolgende Aktivitäten.
> * Verwenden von Parameterübergabe und Systemvariablen.
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.

In diesem Tutorial wird das Azure-Portal verwendet. Andere Mechanismen zur Interaktion mit Azure Data Factory, finden Sie unter "Schnellstart" im Inhaltsverzeichnis.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**. Sie verwenden den Blob Storage als **Quelldatenspeicher**. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als **Senkendatenspeicher**. Wenn Sie in Azure SQL-Datenbank noch keine Datenbank haben, lesen Sie den Artikel [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank.

### <a name="create-blob-table"></a>Erstellen Sie eine Blob-Tabelle

1. Starten Sie den Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn als **input.txt**-Datei auf Ihrem Datenträger.

    ```
    John,Doe
    Jane,Doe
    ```
2. Führen Sie mit einem Tool wie [Azure Storage-Explorer](https://storageexplorer.com/) die folgenden Schritte aus:
    1. Erstellen Sie den Container **adfv2branch**.
    2. Erstellen Sie im Container **adfv2branch** den Ordner **input**.
    3. Laden Sie die Datei **input.txt** in den Container hoch.

## <a name="create-email-workflow-endpoints"></a>Erstellen von E-Mail-Workflow-Endpunkten
Um das Senden einer E-Mail auszulösen, definieren Sie den Workflow mithilfe von [Logic Apps](../logic-apps/logic-apps-overview.md). Ausführliche Informationen zum Erstellen eines Logic-App-Workflows finden Sie unter [How to create a logic app (Vorgehensweise: Erstellen einer Logic-App)](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Erfolgs-E-Mail-Workflow
Erstellen Sie einen Logik-App-Workflow mit dem Namen `CopySuccessEmail`. Definieren Sie den Workflow-Trigger als `When an HTTP request is received`, und fügen Sie eine Aktion von `Office 365 Outlook – Send an email` hinzu.

:::image type="content" source="media/tutorial-control-flow-portal/success-email-workflow.png" alt-text="Erfolgs-E-Mail-Workflow":::

Geben Sie für Ihren Anforderungstrigger das `Request Body JSON Schema` mit dem folgenden JSON ein:

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

Die Anforderung im Logik-App-Designer sollte wie in der folgenden Abbildung aussehen:

:::image type="content" source="media/tutorial-control-flow-portal/logic-app-designer-request.png" alt-text="Logik-App-Designer: Anforderung":::

Für die **Send Email (E-Mail senden)** -Aktion, passen Sie die Formatierungseinstellungen der E-Mail Ihren Bedürfnissen an, indem Sie die Eigenschaften nutzen, die in der Textkörper JSON-Schema-Anforderung übergeben wurden. Beispiel:

:::image type="content" source="media/tutorial-control-flow-portal/send-email-action-2.png" alt-text="Logik-App-Designer: E-Mail-Sendeaktion":::

Speichern Sie den Workflow. Notieren Sie sich die HTTP-Post-Anforderungs-URL für Ihren Erfolgs-E-Mail-Workflow:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Fehler-E-Mail-Workflow
Erstellen Sie mit den gleichen Schritten einen weiteren Logic Apps-Workflow (**CopyFailEmail**). Im Anforderungs-Trigger ist `Request Body JSON schema` gleich. Ändern Sie das Format Ihrer E-Mail (beispielsweise `Subject`), um eine E-Mail für einen nicht erfolgreichen Vorgang zu erhalten. Beispiel:

:::image type="content" source="media/tutorial-control-flow-portal/fail-email-workflow-2.png" alt-text="Logik-App-Designer: Fehler-E-Mail-Workflow":::

Speichern Sie den Workflow. Notieren Sie sich die HTTP-Post-Anforderungs-URL für Ihren Fehler-E-Mail-Workflow:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Sie sollten nun über zwei Workflow-URLs verfügen:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus:

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="Auswahl von Data Factory im Bereich &quot;Neu&quot;":::

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

     :::image type="content" source="./media/tutorial-control-flow-portal/new-azure-data-factory.png" alt-text="Seite „Neue Data Factory“":::

   Der Name der Azure Data Factory muss **global eindeutig** sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).

   *Der Data Factory-Name „ADFTutorialDataFactory“ ist nicht verfügbar.*

3. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
4. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:

      - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
      - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
         
        Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
4. Wählen Sie **V2** als **Version** aus.
5. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.
6. Wählen Sie die Option **An Dashboard anheften** aus.     
7. Klicken Sie auf **Erstellen**.      
8. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...).

    :::image type="content" source="media/tutorial-control-flow-portal/deploying-data-factory.png" alt-text="Kachel „Die Data Factory wird bereitgestellt“":::
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.

   :::image type="content" source="./media/tutorial-control-flow-portal/data-factory-home-page.png" alt-text="Data Factory-Startseite":::
10. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.


## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität und zwei Webaktivitäten. Zum Erstellen der Pipeline verwenden Sie folgende Features:

- Parameter für die Pipeline, auf die durch Datasets zugegriffen wird.
- Webaktivität zum Aufrufen von Logic Apps-Workflows für den Versand von Erfolgs-/Misserfolgs-E-Mails.
- Herstellen einer Verbindung zwischen Aktivitäten (bei Erfolg und Fehler)
- Verwenden der Ausgabe aus einer Aktivität als eine Eingabe für die nachfolgende Aktivität.

1. Klicken Sie auf der Startseite der Data Factory Benutzeroberfläche auf die Kachel **Orchestrieren**.  

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Ein Screenshot, der die ADF-Startseite zeigt.":::
3. Wechseln Sie im Eigenschaftenfenster für die Pipeline zur Registerkarte **Parameter**, und fügen Sie mithilfe der Schaltfläche **Neu** die drei folgenden Zeichenfolgenparameter hinzu: „sourceBlobContainer“, „sinkBlobContainer“ und „receiver“.

    - **sourceBlobContainer:** Parameter in der Pipeline, der vom Quell-Blobdataset verarbeitet wird.
    - **sinkBlobContainer:** Parameter in der Pipeline, der vom Senken-Blobdataset verarbeitet wird.
    - **receiver:** Dieser Parameter wird von den beiden Webaktivitäten in der Pipeline verwendet, die Erfolgs- oder Fehlermeldungen an den Empfänger senden, dessen E-Mail-Adresse durch diesen Parameter angegeben wird.

   :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-parameters.png" alt-text="Menü für neue Pipeline":::
4. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Datenfluss**, und ziehen Sie die Aktivität **Kopieren** auf die Oberfläche des Pipeline-Designers.

   :::image type="content" source="./media/tutorial-control-flow-portal/drag-drop-copy-activity.png" alt-text="Ziehen und Ablegen der Kopieraktivität":::
5. Wechseln Sie im unteren Bereich des Fensters **Eigenschaften** für die Aktivität **Kopieren** zur Registerkarte **Quelle**, und klicken Sie auf **+ Neu**. In diesem Schritt erstellen Sie ein Quelldataset für die Kopieraktivität.

   :::image type="content" source="./media/tutorial-control-flow-portal/new-source-dataset-button.png" alt-text="Screenshot: Erstellen eines Quelldatasets für die Kopieraktivität":::
6. Wählen Sie im Fenster **Neues Dataset** die Option **Azure Blob Storage** aus, und klicken Sie dann auf **Fertig stellen**.

   :::image type="content" source="./media/tutorial-control-flow-portal/select-azure-blob-storage.png" alt-text="Auswählen von „Azure Blob Storage“":::
7. Sie sehen einen neuen **Registerkarte** mit dem Titel **AzureBlob1**. Ändern Sie den Namen des Datasets in **SourceBlobDataset**.

   :::image type="content" source="./media/tutorial-control-flow-portal/dataset-general-page.png" alt-text="Allgemeine Einstellungen des Datasets":::
8. Wechseln Sie im Fenster **Eigenschaften** zur Registerkarte **Verbindung**, und klicken Sie für **Verknüpfter Dienst** auf „Neu“. In diesem Schritt erstellen Sie einen verknüpften Dienst, der Ihr Azure Storage-Konto mit der Data Factory verknüpft.

   :::image type="content" source="./media/tutorial-control-flow-portal/dataset-connection-new-button.png" alt-text="Datasetverbindung – neuer verknüpfter Dienst":::
9. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** die Zeichenfolge **AzureStorageLinkedService** ein.
    2. Wählen Sie unter **Speicherkontoname** Ihr Azure-Speicherkonto aus.
    3. Klicken Sie auf **Speichern**.

   :::image type="content" source="./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png" alt-text="Neuer mit Azure Storage verknüpfter Dienst":::
12. Geben Sie `@pipeline().parameters.sourceBlobContainer` für den Ordner und `emp.txt` für den Dateinamen ein. Mit dem Pipelineparameter „sourceBlobContainer“ legen Sie den Ordnerpfad für das Dataset fest.

    :::image type="content" source="./media/tutorial-control-flow-portal/source-dataset-settings.png" alt-text="Einstellungen des Quelldatasets":::

13. Wechseln Sie zur Registerkarte **Pipeline**, oder klicken Sie in der Strukturansicht auf die Pipeline. Vergewissern Sie sich, dass unter **Source Dataset** (Quelldataset) die Option **SourceBlobDataset** ausgewählt ist.
      
   :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png" alt-text="Quelldataset":::

13. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Senke**, und klicken Sie für **Sink Dataset** (Senkendataset) auf **+ Neu**. In diesem Schritt erstellen Sie ein Senkendataset für die Kopieraktivität. Die Vorgehensweise ähnelt dabei der Erstellung des Quelldatasets.

    :::image type="content" source="./media/tutorial-control-flow-portal/new-sink-dataset-button.png" alt-text="Schaltfläche für neues Senkendataset":::
14. Wählen Sie im Fenster **Neues Dataset** die Option **Azure Blob Storage** aus, und klicken Sie dann auf **Fertig stellen**.
15. Geben Sie auf der Einstellungsseite **Allgemein** für das Dataset unter **Name** die Zeichenfolge **SinkBlobDataset** ein.
16. Wechseln Sie zur Registerkarte **Verbindung**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureStorageLinkedService** aus.
    2. Geben Sie `@pipeline().parameters.sinkBlobContainer` als Ordner ein.
    1. Geben Sie `@CONCAT(pipeline().RunId, '.txt')` als Dateiname ein. Durch den Ausdruck wird die ID der aktuellen Pipelineausführung als Dateiname verwendet. Eine Liste der unterstützten Systemvariablen und Ausdrücke finden Sie unter [Von Azure Data Factory unterstützte Systemvariablen](control-flow-system-variables.md) sowie unter [Ausdrücke und Funktionen in Azure Data Factory](control-flow-expression-language-functions.md).

        :::image type="content" source="./media/tutorial-control-flow-portal/sink-dataset-settings.png" alt-text="Einstellungen für Senkendataset":::
17. Wechseln Sie im oberen Bereich zur Registerkarte **Pipeline**. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein**, und ziehen Sie eine Aktivität vom Typ **Web** auf die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **SendSuccessEmailActivity** fest. Die Webaktivität ermöglicht einen Aufruf an jeden beliebigen REST-Endpunkt. Weitere Informationen zur Aktivität finden Sie unter [Web Activity (Webaktivität)](control-flow-web-activity.md). Diese Pipeline verwendet eine Webaktivität, um den Logik-Apps-E-Mail-Workflow aufzurufen.

    :::image type="content" source="./media/tutorial-control-flow-portal/success-web-activity-general.png" alt-text="Ziehen und Ablegen der ersten Webaktivität":::
18. Wechseln Sie von der Registerkarte **Allgemein** zur Registerkarte **Einstellungen**, und führen Sie die folgenden Schritte aus:
    1. Geben Sie unter **URL** die URL für den Logic Apps-Workflow an, der die Erfolgs-E-Mail sendet.  
    2. Wählen Sie unter **Methode** die Option **POST** aus.
    3. Klicken Sie im Abschnitt **Header** auf den Link **+ Header hinzufügen**.
    4. Fügen Sie einen Header namens **Content-Type** hinzu, und legen Sie ihn auf **application/json** fest.
    5. Geben Sie unter **Text** den folgenden JSON-Code an:

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Die Nachrichtentext enthält folgende Eigenschaften:

       - Nachricht: Übergibt den Wert von `@{activity('Copy1').output.dataWritten`. Greift auf eine Eigenschaft der vorherigen Kopieraktivität zurück, und übergibt den Wert von DataWritten. Für den Fehlerfall, übergeben Sie die Fehlerausgabe anstelle von `@{activity('CopyBlobtoBlob').error.message`.
       - Data Factory-Name: Übergibt den Wert von `@{pipeline().DataFactory}`. Dies ist eine Systemvariable, die Ihnen den Zugriff auf den Namen der entsprechenden Data Factory ermöglicht. Eine Liste der Systemvariablen finden Sie im Artikel [Systemvariablen](control-flow-system-variables.md)
       - Pipeline-Name: Übergibt den Wert von `@{pipeline().Pipeline}`. Dies ist auch eine Systemvariable, die Ihnen den Zugriff auf den entsprechenden Pipelinenamen ermöglicht.
       - Empfänger: Übergibt den Wert von „\@pipeline().parameters.receiver“). Zugriff auf die Pipeline-Parameter

         :::image type="content" source="./media/tutorial-control-flow-portal/web-activity1-settings.png" alt-text="Einstellungen für die erste Webaktivität":::         
19. Stellen Sie eine Verbindung zwischen der Aktivität **Kopieren** und der Aktivität **Web** her, indem Sie die grüne Schaltfläche neben der Kopieraktivität auf die Webaktivität ziehen.

    :::image type="content" source="./media/tutorial-control-flow-portal/connect-copy-web-activity1.png" alt-text="Herstellen einer Verbindung zwischen Kopier- und Webaktivität":::
20. Ziehen Sie eine weitere Aktivität vom Typ **Web** aus der Toolbox **Aktivitäten** auf die Oberfläche des Pipeline-Designers, und legen Sie den Namen auf **SendFailureEmailActivity** fest.

    :::image type="content" source="./media/tutorial-control-flow-portal/web-activity2-name.png" alt-text="Name der zweiten Webaktivität":::
21. Wechseln Sie zur Registerkarte **Einstellungen**, und führen Sie die folgenden Schritte aus:

    1. Geben Sie unter **URL** die URL für den Logic Apps-Workflow an, der die Fehler-E-Mail sendet.  
    2. Wählen Sie unter **Methode** die Option **POST** aus.
    3. Klicken Sie im Abschnitt **Header** auf den Link **+ Header hinzufügen**.
    4. Fügen Sie einen Header namens **Content-Type** hinzu, und legen Sie ihn auf **application/json** fest.
    5. Geben Sie unter **Text** den folgenden JSON-Code an:

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        :::image type="content" source="./media/tutorial-control-flow-portal/web-activity2-settings.png" alt-text="Einstellungen für die zweite Webaktivität":::         
22. Wählen Sie im Pipeline-Designer die Aktivität **Kopieren** aus, klicken Sie auf die Schaltfläche **+->** , und wählen Sie **Fehler** aus.  

    :::image type="content" source="./media/tutorial-control-flow-portal/select-copy-failure-link.png" alt-text="Screenshot: Auswählen von „Fehler“ für die Kopieraktivität im Pipeline-Designer":::
23. Ziehen Sie die **rote** Schaltfläche neben der Kopieraktivität auf die zweite Webaktivität (**SendFailureEmailActivity**). Sie können die Aktivitäten verschieben, sodass die Pipeline wie in der folgenden Abbildung aussieht:

    :::image type="content" source="./media/tutorial-control-flow-portal/full-pipeline.png" alt-text="Vollständige Pipeline mit allen Aktivitäten":::
24. Klicken Sie zum Überprüfen der Pipeline auf der Symbolleiste auf die Schaltfläche **Überprüfen**. Schließen der **Pipeline Validierungsausgabe** Fenster, indem Sie auf die **>>** Schaltfläche.

    :::image type="content" source="./media/tutorial-control-flow-portal/validate-pipeline.png" alt-text="Überprüfen der Pipeline":::
24. Wählen Sie zum Veröffentlichen der Entitäten (Datasets, Pipelines usw.) im Data Factory-Dienst die Option **Alle veröffentlichen**. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird.

    :::image type="content" source="./media/tutorial-control-flow-portal/publish-button.png" alt-text="Veröffentlichen":::

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Auslösen einer erfolgreichen Pipelineausführung
1. Um **Trigger** eine Pipeline ausgeführt wird, klicken Sie auf **Trigger** auf der Symbolleiste, und klicken Sie auf **Trigger jetzt**.

    :::image type="content" source="./media/tutorial-control-flow-portal/trigger-now-menu.png" alt-text="Auslösen einer Pipelineausführung":::
2. Führen Sie im Fenster **Pipeline Run** (Pipelineausführung) die folgenden Schritte aus:

    1. Geben Sie für den Parameter **sourceBlobContainer** die Zeichenfolge **adftutorial/adfv2branch/input** ein.
    2. Geben Sie für den Parameter **sinkBlobContainer** die Zeichenfolge **adftutorial/adfv2branch/output** ein.
    3. Geben Sie eine **e-Mail-Adresse** von der **Empfänger**.
    4. Klicken Sie auf **Fertig stellen**.

        :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-run-parameters.png" alt-text="Parameter für die Pipelineausführung":::

## <a name="monitor-the-successful-pipeline-run"></a>Überwachen der erfolgreichen Pipelineausführung

1. Wechseln Sie zum Überwachen der Pipelineausführung im linken Bereich zur Registerkarte **Überwachen**. Sie sehen die Pipelineausführung, die Sie manuell ausgelöst haben. Aktualisieren Sie die Liste mithilfe der Schaltfläche **Aktualisieren**.

    :::image type="content" source="./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png" alt-text="Erfolgreiche Pipelineausführung":::
2. Klicken **Sie zum Anzeigen von** Aktivitätsausführungen, die mit dieser Pipelineausführung verknüpft sind, in der Spalte **Aktionen** auf den ersten Link. Sie können zur vorherigen Ansicht zurückkehren, indem Sie oben auf **Pipelines** klicken. Aktualisieren Sie die Liste mithilfe der Schaltfläche **Aktualisieren**.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-runs-success.png" alt-text="Screenshot: Anzeigen der Liste mit Aktivitätsausführungen":::

## <a name="trigger-a-pipeline-run-that-fails"></a>Auslösen einer nicht erfolgreichen Pipelineausführung
1. Wechseln Sie im linken Bereich zur Registerkarte **Bearbeiten**.
2. Um **Trigger** eine Pipeline ausgeführt wird, klicken Sie auf **Trigger** auf der Symbolleiste, und klicken Sie auf **Trigger jetzt**.
3. Führen Sie im Fenster **Pipeline Run** (Pipelineausführung) die folgenden Schritte aus:

    1. Geben Sie für den Parameter **sourceBlobContainer** die Zeichenfolge **adftutorial/dummy/input** ein. Stellen Sie sicher, dass der Ordner „dummy“ im Container „adftutorial“ nicht vorhanden ist.
    2. Geben Sie für den Parameter **sinkBlobContainer** die Zeichenfolge **adftutorial/dummy/output** ein.
    3. Geben Sie eine **e-Mail-Adresse** von der **Empfänger**.
    4. Klicken Sie auf **Fertig stellen**.

## <a name="monitor-the-failed-pipeline-run"></a>Überwachen der nicht erfolgreichen Pipelineausführung

1. Wechseln Sie zum Überwachen der Pipelineausführung im linken Bereich zur Registerkarte **Überwachen**. Sie sehen die Pipelineausführung, die Sie manuell ausgelöst haben. Aktualisieren Sie die Liste mithilfe der Schaltfläche **Aktualisieren**.

    :::image type="content" source="./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png" alt-text="Nicht erfolgreiche Pipelineausführung":::
2. Klicken Sie für die Pipelineausführung auf den Link **Fehler**, um Fehlerdetails anzuzeigen.

    :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-error-message.png" alt-text="Pipelinefehler":::
2. Klicken **Sie zum Anzeigen von** Aktivitätsausführungen, die mit dieser Pipelineausführung verknüpft sind, in der Spalte **Aktionen** auf den ersten Link. Aktualisieren Sie die Liste mithilfe der Schaltfläche **Aktualisieren**. Beachten Sie, dass die Kopieraktivität in der Pipeline nicht erfolgreich war. Die Webaktivität hat erfolgreich eine Fehler-E-Mail an den angegebenen Empfänger gesendet.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-runs-failure.png" alt-text="Aktivitätsausführungen":::
4. Klicken Sie in der Spalte **Aktionen** auf den Link **Fehler**, um Fehlerdetails anzuzeigen.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-run-error.png" alt-text="Fehler bei der Aktivitätsausführung":::

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen eines verknüpften Azure Storage Diensts.
> * Erstellen eines Azure-Blobdatasets.
> * Erstellen einer Pipeline, die eine Kopieraktivität und eine Webaktivität enthält.
> * Senden von Aktivitätsausgaben an nachfolgende Aktivitäten.
> * Verwenden von Parameterübergabe und Systemvariablen.
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Sie können jetzt mit dem Abschnitt „Konzepte“ fortfahren, um weitere Informationen zu Azure Data Factory zu erhalten.
> [!div class="nextstepaction"]
>[Pipelines und Aktivitäten](concepts-pipelines-activities.md)

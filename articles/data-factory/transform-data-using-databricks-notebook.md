---
title: Ausführen eines Databricks-Notebooks mit der Aktivität
description: Hier erfahren Sie, wie Sie die Databricks-Notebook-Aktivität in einer Azure Data Factory-Instanz verwenden, um ein Databricks-Notebook für den Cluster mit den Databricks-Aufträgen auszuführen.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 09/08/2021
ms.openlocfilehash: a2086feece0bb37068c57534740b2c58d072d0ea
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806385"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Ausführen eines Databricks-Notebooks mit der Databricks-Notebook-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Azure Data Factory-Pipeline zu erstellen, die ein Databricks-Notebook für den Cluster mit den Databricks-Aufträgen ausführt. Außerdem übergibt sie während der Ausführung Azure Data Factory-Parameter an das Databricks-Notebook.

In diesem Tutorial führen Sie die folgenden Schritte aus:

  - Erstellen einer Data Factory.

  - Erstellen einer Pipeline mit Databricks-Notebook-Aktivität

  - Auslösen einer Pipelineausführung

  - Überwachen der Pipelineausführung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Das folgende Video enthält eine 11-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Voraussetzungen

  - **Azure Databricks-Arbeitsbereich** [Erstellen Sie einen Databricks-Arbeitsbereich](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal), oder verwenden Sie einen vorhandenen Arbeitsbereich. Sie erstellen ein Python-Notebook in Ihrem Azure Databricks-Arbeitsbereich. Anschließend führen Sie das Notebook aus und übergeben Parameter mit Azure Data Factory.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.

1. Wählen Sie im Azure-Portalmenü die Option **Ressource erstellen** und dann **Integration** und **Data Factory** aus.

    :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Screenshot: Auswahl der Data Factory im Bereich „Neu“":::

1. Wählen Sie auf der Seite **Data Factory erstellen** auf der Registerkarte **Grundlagen** Ihr Azure-**Abonnement** aus, in dem Sie die Data Factory erstellen möchten.

1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
    
    1. Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus.
    
    1. Wählen Sie **Neu erstellen** aus, und geben Sie den Namen einer neuen Ressourcengruppe ein.

    Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

1. Wählen Sie unter **Region** den Standort für die Data Factory aus.

    In der Liste werden nur Standorte angezeigt, die von Data Factory unterstützt werden und an denen Ihre Azure Data Factory-Metadaten gespeichert werden. Die von Data Factory verwendeten zugeordneten Datenspeicher (z. B. Azure Storage und Azure SQL-Datenbank) und Computedienste (z. B. Azure HDInsight) können in anderen Regionen ausgeführt werden.

1. Geben Sie unter **Name** den Namen **ADFTutorialDataFactory** ein.
    
    Der Name der Azure Data Factory muss *global eindeutig* sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory. (Verwenden Sie beispielsweise **&lt;IhrName&gt;ADFTutorialDataFactory**.) Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](./naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Screenshot: Fehler, wenn kein Name verfügbar ist":::

1. Wählen Sie **V2** als **Version** aus.

1. Klicken Sie auf **Weiter: Git-Konfiguration**, und aktivieren Sie das Kontrollkästchen **Git später konfigurieren**.

1. Wählen Sie **Überprüfen und erstellen** und nach erfolgreicher Prüfung **Erstellen** aus. 

1. Wählen Sie nach der Erstellung **Zu Ressource wechseln** aus, um zur Seite **Data Factory** zu navigieren. Wählen Sie die Kachel **Azure Data Factory Studio öffnen** aus, um die Anwendung für die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte des Browsers zu starten.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Screenshot: Startseite für die Azure Data Factory mit der Kachel „Azure Data Factory öffnen“":::

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In diesem Abschnitt erstellen Sie einen verknüpften Databricks-Dienst. Dieser verknüpfte Dienst enthält die Verbindungsinformationen für den Databricks-Cluster:

### <a name="create-an-azure-databricks-linked-service"></a>Erstellen eines verknüpften Azure Databricks-Diensts

1.  Wechseln Sie auf der Startseite im linken Bereich zur Registerkarte **Verwalten**.

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Screenshot: Registerkarte „Verwalten“":::

1.  Wählen Sie unter **Verbindungen** die Option **Verknüpfte Dienste** und dann **+ Neu** aus.
    
    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-6.png" alt-text="Screenshot: Erstellen einer neuen Verbindung":::

1.  Wählen Sie im Fenster **Neuer verknüpfter Dienst**  die Option **Compute** &gt; **Azure Databricks** und dann **Weiter** aus.
    
    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-7.png" alt-text="Screenshot: Angeben eines verknüpften Databricks-Diensts":::

1.  Führen Sie im Fenster **Neuer verknüpfter Dienst** die folgenden Schritte aus:
    
    1.  Geben Sie unter **Name** den Namen **_AzureDatabricks\_LinkedService_** ein.
    
    1.  Wählen Sie den richtigen **Databricks-Arbeitsbereich** aus, in dem Sie Ihr Notebook ausführen möchten.

    1.  Wählen Sie unter **Cluster auswählen** die Option **Neuer Auftragscluster** aus.
    
    1.  Für **Databricks-Arbeitsbereichs-URL** sollten die entsprechenden Informationen automatisch aufgefüllt werden.

    1.  Führen Sie für **Zugriffstoken** die Generierung über den Azure Databricks-Arbeitsbereich durch. Die Schritte finden Sie [hier](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  Wählen Sie unter **Clusterversion** die Version aus, die Sie verwenden möchten.

    1.  Wählen Sie für dieses Tutorial unter **General Purpose (HDD)** (Universell (HDD)) für **Cluster node type** (Clusterknotentyp) die Option **Standard\_D3\_v2**. 
    
    1.  Geben Sie für **Workers** die Zahl **2** ein.
    
    1.  Klicken Sie auf **Erstellen**.

        :::image type="content" source="media/transform-data-using-databricks-notebook/new-databricks-linked-service.png" alt-text="Screenshot: Konfiguration des neuen verknüpften Azure Databricks-Diensts":::

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1.  Klicken Sie auf die Schaltfläche **+** (Pluszeichen), und wählen Sie im Menü dann die Option **Pipeline**.

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-9.png" alt-text="Screenshot: Schaltflächen zum Erstellen einer neuen Pipeline":::

1.  Erstellen Sie einen **Parameter** für die Verwendung in der **Pipeline**. Diesen Parameter übergeben Sie später an die Databricks-Notebook-Aktivität. Wählen Sie in der leeren Pipeline die Registerkarte **Parameter** und dann **+ Neu** aus, und geben Sie als Namen **name** ein.

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-10.png" alt-text="Screenshot: Erstellen eines neuen Parameters":::

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-11.png" alt-text="Screenshot: Erstellen des Parameters „name“":::

1.  Erweitern Sie in der Toolbox **Aktivitäten** die Option **Databricks**. Ziehen Sie die **Notebook**-Aktivität aus der Toolbox **Aktivitäten** auf die Oberfläche des Pipeline-Designers.

    :::image type="content" source="media/transform-data-using-databricks-notebook/new-adf-pipeline.png" alt-text="Screenshot: Ziehen des Notebooks auf die Designeroberfläche":::

1.  Führen Sie in den Eigenschaften im unteren Bereich des Fensters für das **Databricks-** **Notebook** die folgenden Schritte aus:

    1. Wechseln Sie zur Registerkarte **Azure Databricks**.

    1. Wählen Sie **AzureDatabricks\_LinkedService** aus (wie oben erstellt).

    1. Wechseln Sie zur Registerkarte **Einstellungen**.

    1. Wählen Sie einen **Notebook-Pfad** für Databricks aus. Wir erstellen ein Notebook und geben den Pfad hier an. Sie erhalten den Notebook-Pfad, indem Sie die nächsten Schritte ausführen.

       1. Starten Sie Ihren Azure Databricks-Arbeitsbereich.

       1. Erstellen Sie im Arbeitsbereich unter **Neuer Ordner** einen neuen Ordner, und geben Sie ihm den Namen **adftutorial**.

          :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png" alt-text="Screenshot: Erstellen eines neuen Ordners":::        

       1. [Screenshot: Erstellen eines neuen Notebooks](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) Geben Sie dem Notebook den Namen **mynotebook**, und klicken Sie im Ordner **adftutorial** auf **Erstellen**.

          :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png" alt-text="Screenshot: Erstellen eines neuen Notebooks":::  

          :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png" alt-text="Screenshot: Festlegen der Eigenschaften des neuen Notebooks":::

       1. Fügen Sie im neu erstellten Notebook „mynotebook“ den folgenden Code hinzu:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

          :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png" alt-text="Screenshot: Erstellen von Widgets für Parameter"::: 

       1. Der **Notebook-Pfad** lautet hier **/adftutorial/mynotebook**.

1.  Wechseln Sie zurück zum **Erstellungstool für die Data Factory-Benutzeroberfläche**. Navigieren Sie unter der Aktivität **Notebook1** zur Registerkarte **Einstellungen**.

    a.  Fügen Sie der Notebook-Aktivität einen **Parameter** hinzu. Verwenden Sie den Parameter, den Sie der **Pipeline** zuvor hinzugefügt haben.

       :::image type="content" source="media/transform-data-using-databricks-notebook/new-adf-parameters.png" alt-text="Screenshot: Hinzufügen eines Parameters":::

    b.  Geben Sie dem Parameter den Namen **input**, und geben Sie den Wert als Ausdruck **\@pipeline().parameters.name** an.

1.  Wählen Sie zum Überprüfen der Pipeline in der Symbolleiste die Schaltfläche **Überprüfen**. Wählen Sie die Schaltfläche **Schließen** aus, um das Überprüfungsfenster zu schließen.

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-18.png" alt-text="Screenshot: Überprüfen der Pipeline":::

1.  Wählen Sie **Alle veröffentlichen** aus. Die Data Factory-Benutzeroberfläche veröffentlicht Entitäten (verknüpfte Dienste und Pipeline) für den Azure Data Factory-Dienst.

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-19.png" alt-text="Screenshot: Veröffentlichen der neuen Data Factory-Entitäten":::

## <a name="trigger-a-pipeline-run"></a>Auslösen einer Pipelineausführung

Wählen Sie auf der Symbolleiste die Option **Trigger hinzufügen** und dann **Trigger now** (Jetzt auslösen) aus.

:::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-20.png" alt-text="Screenshot: Auswählen des Befehls „Trigger now“ (Jetzt auslösen)":::

Im Dialogfeld **Pipelineausführung** wird nach dem Parameter **name** gefragt. Verwenden Sie hier **/path/filename** als Parameter. Klicken Sie auf **OK**.

:::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-21.png" alt-text="Screenshot: Angeben eines Werts für den Parameter „name“":::

## <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1.  Wechseln Sie zur Registerkarte **Überwachen**. Vergewissern Sie sich, dass eine Pipelineausführung angezeigt wird. Die Erstellung eines Databricks-Auftragsclusters, in dem das Notebook ausgeführt wird, dauert ca. fünf bis acht Minuten.

    :::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-22.png" alt-text="Screenshot: Überwachen der Pipeline"::: 

1.  Wählen Sie von Zeit zu Zeit die Option **Aktualisieren**, um den Status der Pipelineausführung zu überprüfen.

1.  Wenn Sie die der Pipelineausführung zugeordneten Aktivitätsausführungen anzeigen möchten, wählen Sie unter der Spalte **Pipelinename** den Link **pipeline1** aus.

1. Wählen Sie auf der Seite **Aktivitätsausführungen** in der Spalte **Aktivitätsname** die Option **Ausgabe** aus, um die Ausgabe der einzelnen Aktivitäten anzuzeigen. Im Bereich **Ausgabe** finden Sie den Link zu Databricks-Protokollen, um ausführlichere Spark-Protokolle zu erhalten.

1. Sie können zur Ansicht „Pipelineausführungen“ zurückkehren, indem Sie im Breadcrumbmenü oben den Link **Alle Pipelineausführungen** auswählen.

## <a name="verify-the-output"></a>Überprüfen der Ausgabe

Sie können sich im **Azure Databricks-Arbeitsbereich** anmelden und zu **Cluster** navigieren. Unter **Auftrag** wird der Status *„Ausstehende Ausführung“, „Wird ausgeführt“ oder „Beendet“* angezeigt.

:::image type="content" source="media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png" alt-text="Screenshot: Anzeigen des Auftragsclusters und des Auftrags"::: 

Sie können auf den **Auftragsnamen** klicken und weitere Details anzeigen. Bei einer erfolgreichen Ausführung können Sie die übergebenen Parameter und die Ausgabe des Python-Notebooks überprüfen.

:::image type="content" source="media/transform-data-using-databricks-notebook/databricks-output.png" alt-text="Screenshot: Anzeigen der Ausführungsdetails und der Ausgabe"::: 

## <a name="next-steps"></a>Nächste Schritte

Die Pipeline in diesem Beispiel löst eine Databricks-Notebook-Aktivität aus und übergibt einen Parameter. Sie haben Folgendes gelernt:

  - Erstellen einer Data Factory.

  - Erstellen einer Pipeline, für die die Databricks-Notebook-Aktivität verwendet wird

  - Auslösen einer Pipelineausführung

  - Überwachen der Pipelineausführung.
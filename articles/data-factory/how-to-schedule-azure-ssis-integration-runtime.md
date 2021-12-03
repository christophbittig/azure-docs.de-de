---
title: Planen von Azure-SSIS Integration Runtime
description: Dieser Artikel beschreibt die Planung des Startens und Beendens einer Azure-SSIS Integration Runtime mithilfe von Azure Data Factory.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7615c7d3d0556aef2bd2f73148ac986709082ce0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845720"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Starten und Beenden von Azure-SSIS Integration Runtimes nach einem Zeitplan

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dieser Artikel beschreibt die Planung des Startens und Beendens einer Azure-SSIS Integration Runtime (IR) mithilfe von Azure Data Factory (ADF). Azure-SSIS IR ist eine dedizierte ADF-Computeressource für das Ausführen von SSIS-Paketen (SQL Server Integration Services). Das Ausführen von Azure-SSIS IR ist mit Kosten verbunden. Sie sollten Ihre IR daher in der Regel nur ausführen, wenn Sie SSIS-Pakete in Azure ausführen müssen, und die IR beenden, wenn Sie sie nicht mehr benötigen. Sie können die ADF-Benutzeroberfläche (UI) oder -App oder Azure PowerShell verwenden, um [die IR manuell zu starten oder zu beenden](manage-azure-ssis-integration-runtime.md).

Alternativ können Sie Webaktivitäten in ADF-Pipelines erstellen, um Ihre IR nach einem Zeitplan zu starten oder zu beenden, z.B. Starten am Morgen vor dem Ausführen Ihrer täglichen ETL-Workloads und Beenden am Nachmittag, wenn diese erledigt sind.  Sie können auch eine Aktivität „SSIS-Paket ausführen“ zwischen zwei Webaktivitäten verketten, die Ihre IR starten und beenden. Auf diese Weise wird Ihre IR nach Bedarf gestartet und beendet, direkt vor und nach der Paketausführung. Weitere Informationen zur Aktivität „SSIS-Paket ausführen“ finden Sie im Artikel [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in einer ADF-Pipeline](how-to-invoke-ssis-package-ssis-activity.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie Ihre Azure-SSIS IR noch nicht bereitgestellt haben, stellen Sie sie mithilfe der folgenden Anweisungen im [Tutorial](./tutorial-deploy-ssis-packages-azure.md) bereit. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Erstellen und Planen von ADF-Pipelines zum Starten oder Beenden einer Azure-SSIS IR
In diesem Abschnitt erfahren Sie, wie Sie mithilfe von Webaktivitäten in ADF-Pipelines Ihre Azure-SSIS IR nach Zeitplan oder nach Bedarf starten und beenden. Wir führen Sie durch die Erstellung von drei Pipelines: 

1. Die erste Pipeline enthält eine Webaktivität, die Ihre Azure-SSIS IR startet. 
2. Die zweite Pipeline enthält eine Webaktivität, die Ihre Azure-SSIS IR beendet.
3. Die dritte Pipeline enthält eine Aktivität „SSIS-Paket ausführen“, die zwischen zwei Webaktivitäten verkettet ist, die Ihre Azure-SSIS IR starten bzw. beenden. 

Nachdem Sie diese Pipelines erstellt und getestet haben, können Sie einen Zeitplantrigger erstellen und der Pipeline zuordnen. Der Zeitplantrigger definiert einen Zeitplan zum Ausführen der zugehörigen Pipeline. 

Beispielsweise können Sie zwei Trigger erstellen. Der erste wird täglich um 6:00 Uhr ausgeführt und ist der ersten Pipeline zugeordnet, während der zweite für die Ausführung täglich um 18:00 Uhr geplant und der zweiten Pipeline zugeordnet ist.  Auf diese Weise wird Ihre IR jeden Tag in einem Zeitraum zwischen 6:00 Uhr und 18:00 Uhr ausgeführt und ist damit bereit für Ihre täglichen ETL-Workloads.  

Wenn Sie einen dritten Trigger erstellen, der für eine Ausführung täglich um Mitternacht geplant ist, und ihn der dritten Pipeline zuordnen, wird diese Pipeline täglich um Mitternacht ausgeführt und startet Ihre IR unmittelbar vor der Ausführung Ihres Pakets. Die IR wird dann unmittelbar nach der Paketausführung beendet, sodass sie nicht im Leerlauf ausgeführt wird.

### <a name="create-your-adf"></a>Erstellen Ihrer ADF

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.    
2. Klicken Sie im Menü auf der linken Seite nacheinander auf **Neu**, **Data + Analytics** und **Data Factory**. 
   
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png" alt-text="Neu -> Data Factory":::
   
3. Geben Sie auf der Seite **Neue Data Factory** unter **Name** die Zeichenfolge **MyAzureSsisDataFactory** ein. 
      
   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png" alt-text="Seite „Neue Data Factory“":::
 
   Der Name der ADF muss global eindeutig sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der ADF (z.B. in „<IhrName>MyAzureSsisDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für ADF-Artefakte finden Sie im Artikel [Data Factory – Benennungsregeln](naming-rules.md).
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die ADF erstellt werden soll. 
5. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
     
   - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
   - Wählen Sie **Neu erstellen** aus, und geben Sie den Namen der neuen Ressourcengruppe ein.   
         
   Weitere Informationen zu Ressourcengruppen finden Sie im Artikel [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).
   
6. Wählen Sie **V2** als **Version** aus.
7. Wählen Sie für **Speicherort** in der Dropdownliste einen der unterstützten Speicherorte für die Erstellung von ADFs aus.
8. Wählen Sie die Option **An Dashboard anheften** aus.     
9. Klicken Sie auf **Erstellen**.
10. Auf dem Azure-Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying Data Factory** (Data Factory wird bereitgestellt). 

    :::image type="content" source="media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png" alt-text="Kachel „Die Data Factory wird bereitgestellt“":::
   
11. Nach Abschluss der Erstellung wird die ADF-Seite angezeigt, wie weiter unten dargestellt.
   
    :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png" alt-text="Data Factory-Startseite":::
   
12. Klicken Sie auf **Erstellen und Überwachen**, um die ADF-UI/-App auf einer separaten Registerkarte zu starten.

### <a name="create-your-pipelines"></a>Erstellen Ihrer Pipelines

1. Wählen Sie auf der Startseite die Option **Orchestrieren** aus. 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die ADF-Startseite zeigt.":::
   
2. Erweitern Sie in der Toolbox **Aktivitäten** das Menü **Allgemein**, ziehen Sie die Aktivität **Web** auf die Oberfläche des Pipeline-Designers, und legen Sie sie dort ab. Ändern Sie im Fenster „Aktivitätseigenschaften“ auf der Registerkarte **Allgemein** den Namen der Aktivität in **startMyIR**. Wechseln Sie zur Registerkarte **Einstellungen**, und führen Sie die folgenden Aktionen aus:

   1. Geben Sie unter **URL** die folgende URL für die REST-API ein, die die Azure-SSIS IR startet, und ersetzen Sie dabei `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` und `{integrationRuntimeName}` durch die tatsächlichen Werte für Ihre IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`. . Alternativ können Sie auch die Ressourcen-ID Ihrer IR von der Überwachungsseite der ADF-Benutzeroberfläche/-App kopieren und einfügen, um den folgenden Teil der oben angegebenen URL zu ersetzen: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`.
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png" alt-text="ADF-SSIS IR-Ressourcen-ID":::
  
   2. Wählen Sie unter **Methode** die Option **POST** aus. 
   3. Geben Sie unter **Text** die Zeichenfolge `{"message":"Start my IR"}` ein.
   4. Wählen Sie für **Authentifizierung** die Option **Verwaltete Identität** aus, um die angegebene systemseitig verwaltete Identität für Ihre ADF zu verwenden. Weitere Informationen hierzu finden Sie in dem Artikel [Verwaltete Identität für Data Factory](./data-factory-service-identity.md).
   5. Geben Sie `https://management.azure.com/` als **Ressource** ein.
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png" alt-text="ADF-Webaktivität nach Zeitplan für SSIS IR":::
  
3. Klonen Sie die erste Pipeline, um eine zweite zu erstellen, wobei Sie den Aktivitätsnamen dabei in **stopMyIR** ändern und die folgenden Eigenschaften ersetzen.

   1. Geben Sie unter **URL** die folgende URL für die REST-API ein, die die Azure-SSIS IR beendet, und ersetzen Sie dabei `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` und `{integrationRuntimeName}` durch die tatsächlichen Werte für Ihre IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`.
   2. Geben Sie unter **Text** die Zeichenfolge `{"message":"Stop my IR"}` ein. 

4. Erstellen Sie eine dritte Pipeline, ziehen Sie eine Aktivität **SSIS-Paket ausführen** von der Toolbox **Aktivitäten** auf die Pipeline-Designer-Oberfläche, legen Sie sie dort ab, und konfigurieren Sie sie anhand der Anweisungen im Artikel [Aufrufen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in ADF](how-to-invoke-ssis-package-ssis-activity.md).  Verketten Sie als Nächstes die Aktivität „SSIS-Paket ausführen“ zwischen zwei Webaktivitäten, die Ihre IR starten/beenden, ähnlich den Webaktivitäten in der ersten und der zweiten Pipeline.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png" alt-text="ADF-Webaktivität nach Bedarf für SSIS IR":::

5. Sie können die dritte Pipeline auch automatisch auf der Grundlage einer Vorlage statt manuell erstellen. Wählen Sie dazu neben **Pipeline** das Symbol **...** aus, um ein Dropdownmenü mit Pipelineaktionen anzuzeigen. Wählen Sie die Aktion **Pipeline über Vorlage** aus, aktivieren Sie unter **Kategorie** das Kontrollkästchen **SSIS**, und wählen Sie die Vorlage **ADF-Pipeline für das rechtzeitige Starten und Beenden der Azure-SSIS IR vor und nach dem Ausführen des SSIS-Pakets planen** aus. Wählen Sie anschließend im Dropdownmenü **Azure-SSIS Integration Runtime** Ihre IR und schließlich die Schaltfläche **Diese Vorlage verwenden** aus. Ihre Pipeline wird automatisch erstellt. Dabei bleibt nur noch das SSIS-Paket übrig, das Sie der Aktivität „SSIS-Paket ausführen“ zuweisen müssen.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-on-demand-ssis-ir-template.png" alt-text="ADF nach Bedarf für SSIS IR-Vorlage":::

6. Um die dritte Pipeline stabiler zu machen, können Sie sicherstellen, dass die Webaktivitäten zum Starten/Beenden Ihrer IR bei vorübergehenden Fehlern aufgrund von Netzwerkkonnektivitätsproblemen oder anderen Problemen wiederholt und erst abgeschlossen werden, wenn Ihre IR tatsächlich gestartet/beendet wurde. Dazu können Sie jede Webaktivität durch eine Until-Aktivität ersetzen, die wiederum zwei Webaktivitäten enthält: eine zum Starten/Beenden der IR und eine andere zum Überprüfen des IR-Status. Rufen Sie die Until-Aktivitäten *Start SSIS IR* (SSIS IR starten) und *Stop SSIS IR* (SSIS IR beenden) auf.  Die Until-Aktivität *Start SSIS IR* (SSIS IR starten) enthält die Webaktivitäten *Try Start SSIS IR* (Starten der SSIS IR versuchen) und *Get SSIS IR Status* (SSIS IR-Status abrufen). Die Until-Aktivität *Stop SSIS IR* (SSIS IR beenden) enthält die Webaktivitäten *Try Stop SSIS IR* (Beenden der SSIS IR versuchen) und *Get SSIS IR Status* (SSIS IR-Status abrufen). Geben Sie auf der Registerkarte **Einstellungen** der Until-Aktivitäten *Start SSIS IR*/*Stop SSIS IR* (SSIS IR starten/SSIS IR beenden) unter **Ausdruck** die Zeichenfolge `@equals('Started', activity('Get SSIS IR Status').output.properties.state)`/`@equals('Stopped', activity('Get SSIS IR Status').output.properties.state)` ein.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir.png" alt-text="ADF-Until-Aktivität nach Bedarf für SSIS IR":::

   In den beiden Until-Aktivitäten ähneln die Webaktivitäten *Try Start SSIS IR*/*Try Stop SSIS IR* (Starten der SSIS IR versuchen/Beenden der SSIS IR versuchen) den Webaktivitäten in der ersten bzw. zweiten Pipeline. Führen Sie auf der Registerkarte **Einstellungen** der Webaktivität *Get SSIS IR Status* (SSIS IR-Status abrufen) die folgenden Aktionen aus:

   1. Geben Sie unter **URL** die folgende URL für die REST-API ein, die den Azure-SSIS IR-Status abruft, und ersetzen Sie dabei `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` und `{integrationRuntimeName}` durch die tatsächlichen Werte für Ihre IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}?api-version=2018-06-01`.
   2. Wählen Sie unter **Methode** die Option **GET** aus. 
   3. Wählen Sie für **Authentifizierung** die Option **Verwaltete Identität** aus, um die angegebene systemseitig verwaltete Identität für Ihre ADF zu verwenden. Weitere Informationen hierzu finden Sie in dem Artikel [Verwaltete Identität für Data Factory](./data-factory-service-identity.md).
   4. Geben Sie `https://management.azure.com/` als **Ressource** ein.
    
      :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-until-activity-on-demand-ssis-ir-open.png" alt-text="ADF-Until-Aktivität nach Bedarf für SSIS IR geöffnet":::

7. Weisen Sie der verwalteten Identität für Ihre ADF die Rolle **Mitwirkender** für sich selbst zu, sodass Webaktivitäten in den zugehörigen Pipelines die REST-API zum Starten/Beenden der darin bereitgestellten Azure-SSIS IRs aufrufen können.  Klicken Sie auf Ihrer ADF-Seite im Azure-Portal auf **Zugriffssteuerung (IAM)** und auf **+ Rollenzuweisung hinzufügen**, und führen Sie dann auf dem Blatt **Rollenzuweisung hinzufügen** die folgenden Aktionen aus:

   1. Wählen Sie als **Rolle** die Option **Mitwirkender**. 
   2. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus. 
   3. Suchen Sie unter **Auswählen** nach dem Namen Ihrer ADF, und wählen Sie ihn aus. 
   4. Klicken Sie auf **Speichern**.
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png" alt-text="Rollenzuweisung für mit ADF verwaltete Identitäten":::

8. Überprüfen Sie Ihre ADF und alle Pipelineeinstellungen, indem Sie auf der Symbolleiste der Factorypipeline auf **Alle überprüfen/Überprüfen** klicken. Schließen Sie die **Ausgabe der Factory-/Pipelineüberprüfung**, indem Sie auf die Schaltfläche **>>** klicken.  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png" alt-text="Überprüfen der Pipeline":::

### <a name="test-run-your-pipelines"></a>Testlauf Ihrer Pipelines

1. Wählen Sie auf der Symbolleiste für die einzelnen Pipelines **Testlauf** aus, und beachten Sie das Fenster **Ausgabe** im unteren Bereich. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png" alt-text="Testlauf":::
    
2. Wenn Sie das SSIS-Paket im SSIS-Katalog (SSISDB) speichern, können Sie zum Testen der dritten Pipeline SQL Server Management Studio (SSMS) starten, um die Ausführung zu überprüfen. Führen Sie im Fenster **Mit Server verbinden** die folgenden Aktionen aus. 

    1. Geben Sie unter **Servername** die Zeichenfolge **&lt;Ihr Servername&gt;.database.windows.net** ein.
    2. Wählen Sie **Optionen >>** aus.
    3. Wählen Sie unter **Mit Datenbank verbinden** die Option **SSISDB** aus.
    4. Wählen Sie **Verbinden**. 
    5. Erweitern Sie **Integration Services-Kataloge** -> **SSISDB** -> Ihr Ordner -> **Projekte** -> Ihr SSIS-Projekt -> **Pakete** . 
    6. Klicken Sie mit der rechten Maustaste auf das auszuführende SSIS-Paket, und wählen Sie **Berichte** -> **Standardberichte** -> **Alle Ausführungen** aus. 
    7. Stellen Sie sicher, dass es ausgeführt wurde. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png" alt-text="Überprüfen der Ausführung des SSIS-Pakets":::

### <a name="schedule-your-pipelines"></a>Planen Ihrer Pipelines

Nachdem Ihre Pipelines nun wie erwartet funktioniert, können Sie Trigger erstellen, um sie in einem festgelegten Rhythmus auszuführen. Ausführliche Informationen zum Zuordnen von Triggern zu Pipelines finden Sie im Artikel [Auslösen der Pipeline nach einem Zeitplan](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Wählen Sie auf der Symbolleiste für die Pipeline die Option **Trigger** und dann **Neu/Bearbeiten** aus. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png" alt-text="Screenshot, der die Menüoption Auslöser -> Neu/Bearbeiten hervorhebt.":::

2. Wählen Sie unter **Trigger hinzufügen** die Option **+ Neu** aus.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png" alt-text="„Add Triggers“ (Trigger hinzufügen) – „Neu“":::

3. Führen Sie im Bereich **Neuer Trigger** die folgenden Aktionen durch: 

    1. Geben Sie für **Name** einen Namen für den Trigger ein. Im folgenden Beispiel ist **Run daily** der Name des Triggers. 
    2. Wählen Sie unter **Typ** die Option **Zeitplan** aus. 
    3. Geben Sie unter **Startdatum (UTC)** ein Startdatum und eine Startzeit in UTC ein. 
    4. Geben Sie unter **Wiederholung** eine Frequenz für den Trigger ein. Im folgenden Beispiel ist dies einmal **täglich**. 
    5. Wählen Sie für **Ende** die Option **Kein Ende** aus, oder wählen Sie **On Date** (An Datum) aus, und geben Sie ein Enddatum und eine Uhrzeit ein. 
    6. Wählen Sie **Aktiviert** aus, um den Trigger sofort nach dem Veröffentlichen der gesamten ADF-Einstellungen zu aktivieren. 
    7. Wählen Sie **Weiter** aus.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png" alt-text="Trigger -> „Neu/Bearbeiten“":::
    
4. Überprüfen Sie auf der Seite **Trigger Run Parameters** (Ausführungsparameter für Trigger) etwaige Warnungen, und wählen Sie **Fertig stellen** aus. 
5. Veröffentlichen Sie die gesamten ADF-Einstellungen, indem Sie auf der Symbolleiste der Factory **Alle veröffentlichen** auswählen. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png" alt-text="Alle veröffentlichen":::

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Überwachen Ihrer Pipelines und Trigger im Azure-Portal

1. Verwenden Sie zum Überwachen der Ausführung von Triggern und Pipelines die Registerkarte **Monitor** auf der linken Seite der ADF-Benutzeroberfläche/-App. Ausführliche Schritte finden Sie im Artikel [Überwachen der Pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png" alt-text="Pipelineausführungen":::

2. Wenn Sie mit einer Pipelineausführung verbundene Aktivitätsausführungen anzeigen möchten, wählen Sie in der Spalte **Aktionen** den ersten Link (**View Activity Runs**, Aktivitätsausführungen anzeigen) aus. Für die dritte Pipeline werden drei Aktivitätsausführungen angezeigt, eine für jede verkettete Aktivität in der Pipeline (Webaktivität zum Starten Ihrer IR, Aktivität „SSIS-Paket ausführen“ zum Ausführen des Pakets und Webaktivität zum Beenden Ihrer IR). Wählen Sie den Link **Pipelines** ganz oben aus, um wieder die Pipelineausführungen anzuzeigen.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png" alt-text="Aktivitätsausführungen":::

3. Wählen Sie zum Anzeigen der Triggerausführungen in der Dropdownliste ganz oben unter **Pipelineausführungen** die Option **Triggerausführungen** aus. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png" alt-text="Triggerausführungen":::

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Überwachen Ihrer Pipelines und Trigger mit PowerShell

Verwenden Sie Skripts wie in den folgenden Beispielen, um Ihre Pipelines und Trigger zu überwachen.

1. Rufen Sie den Status einer Pipelineausführung ab.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Rufen Sie Informationen zu einem Trigger ab.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Rufen Sie den Status einer Triggerausführung ab.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Erstellen und Planen eines Azure Automation-Runbooks, das eine Azure-SSIS IR startet/beendet

In diesem Abschnitt erfahren Sie, wie Sie ein Azure Automation-Runbook erstellen, das ein PowerShell-Skript ausführt und dadurch Ihre Azure-SSIS IR nach einem Zeitplan startet und beendet.  Dies ist nützlich, wenn Sie zusätzliche Skripts vor/nach dem Starten/Beenden Ihrer IR für eine Vor- oder Nachverarbeitung ausführen möchten.

### <a name="create-your-azure-automation-account"></a>Erstellen des Azure Automation-Kontos

Wenn Sie noch kein Azure Automation-Konto haben, erstellen Sie eines anhand der Anweisungen in diesem Schritt. Eine ausführliche Anleitung finden Sie im Artikel [Erstellen eines Azure Automation-Kontos](../automation/quickstarts/create-account-portal.md). Im Rahmen dieses Schritts erstellen Sie ein **ausführendes Azure-Konto** (einen Dienstprinzipal in Ihrem Azure Active Directory) und weisen ihm die Rolle **Mitwirkender** für Ihr Azure-Abonnement zu. Stellen Sie sicher, dass es sich um dasselbe Abonnement handelt, das auch Ihre ADF mit der Azure-SSIS IR enthält. Azure Automation verwendet dieses Konto für die Authentifizierung bei Azure Resource Manager und die Verarbeitung Ihrer Ressourcen. 

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die ADF-Benutzeroberfläche/-App wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.    
3. Wählen Sie im linken Menü **Neu** aus, dann **Überwachung + Verwaltung** und schließlich **Automatisierung**. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png" alt-text="Screenshot, der die Option Überwachung + Verwaltung > Automatisierung hervorhebt.":::
    
2. Führen Sie im Bereich **Automation-Konto hinzufügen** die folgenden Aktionen aus.

    1. Geben Sie unter **Name** einen Namen für Ihr Azure Automation-Konto ein. 
    2. Wählen Sie unter **Abonnement** das Abonnement aus, das Ihre ADF mit der Azure-SSIS IR enthält. 
    3. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen, oder wählen Sie **Vorhandene verwenden** aus, um eine vorhandene auszuwählen. 
    4. Wählen Sie unter **Standort** einen Standort für Ihr Azure Automation-Konto aus. 
    5. Wählen Sie unter **Create Azure Run As account** (Ausführendes Azure-Konto erstellen) zur Bestätigung **Ja** aus. In Azure Active Directory wird ein Dienstprinzipal erstellt, dem die Rolle **Mitwirkender** in Ihrem Azure-Abonnement zugewiesen wird.
    6. Wählen Sie **An Dashboard anheften** aus, um ihn dauerhaft auf dem Azure-Dashboard anzuzeigen. 
    7. Klicken Sie auf **Erstellen**. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png" alt-text="Neu -> Überwachung + Verwaltung -> Automatisierung":::
   
3. Sie können den Bereitstellungsstatus des Azure Automation-Kontos auf dem Azure-Dashboard und unter den Benachrichtigungen einsehen. 
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png" alt-text="Bereitstellen von Automation"::: 
    
4. Nachdem Ihr Azure Automation-Konto erfolgreich erstellt wurde, wird es auf der Startseite angezeigt. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png" alt-text="Automation-Startseite":::

### <a name="import-adf-modules"></a>Importieren von ADF-Modulen

1. Wählen Sie im Abschnitt **FREIGEGEBENE RESSOURCEN** im Menü links die Option **Module** aus, und überprüfen Sie, ob in der Liste der Module **Az.DataFactory** + **Az.Profile** enthalten ist.

   :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png" alt-text="Überprüfen der erforderlichen Module":::

2.  Wenn **Az.DataFactory** nicht vorhanden ist, navigieren Sie zum PowerShell-Katalog für das Modul [Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), und wählen Sie **In Azure Automation bereitstellen**, danach Ihr Azure Automation-Konto und anschließend **OK** aus. Kehren Sie im Menü links zum Abschnitt **FREIGEGEBENE RESSOURCEN** zurück, und zeigen Sie **Module** an. Warten Sie, bis sich der **STATUS** des Moduls **Az.DataFactory** in **Verfügbar** ändert.

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png" alt-text="Überprüfen des Data Factory-Moduls":::

3.  Wenn **Az.Profile** nicht vorhanden ist, navigieren Sie zum PowerShell-Katalog für das Modul [Az.Profile](https://www.powershellgallery.com/packages/Az.profile/), und wählen Sie **In Azure Automation bereitstellen**, danach Ihr Azure Automation-Konto und schließlich **OK** aus. Kehren Sie im Menü links zum Abschnitt **FREIGEGEBENE RESSOURCEN** zurück, und zeigen Sie **Module** an. Warten Sie, bis sich der **STATUS** des Moduls **Az.Profile** in **Verfügbar** ändert.

    :::image type="content" source="media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png" alt-text="Überprüfen des Profile-Moduls":::

### <a name="create-your-powershell-runbook"></a>Erstellen des PowerShell-Runbooks

Im folgenden Abschnitt finden Sie Schritte zum Erstellen eines PowerShell-Runbooks. Das mit dem Runbook verbundene Skript startet/beendet eine Azure-SSIS IR basierend auf dem für den **VORGANG**-Parameter angegebenen Befehl. Dieser Abschnitt enthält keine vollständigen Details zum Erstellen eines Runbooks. Weitere Informationen finden Sie im Artikel [Erstellen eines Runbooks](../automation/learn/powershell-runbook-managed-identity.md).

1. Wechseln Sie zur Registerkarte **Runbooks**, und wählen Sie auf der Symbolleiste **+ Runbook hinzufügen** aus. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png" alt-text="Screenshot, auf dem die Schaltfläche „+ Runbook hinzufügen“ hervorgehoben ist":::
   
2. Wählen Sie **Neues Runbook erstellen** aus, und führen Sie die folgenden Aktionen aus: 

    1. Geben Sie für **Name** die Zeichenfolge **StartStopAzureSsisRuntime** ein.
    2. Wählen Sie als **Runbooktyp** den Typ **PowerShell** aus.
    3. Klicken Sie auf **Erstellen**.
    
   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png" alt-text="Schaltfläche „Runbook hinzufügen“":::
   
3. Kopieren Sie das folgende PowerShell-Skript, und fügen Sie es in das Runbook-Skriptfenster ein. Speichern und veröffentlichen Sie das Runbook mithilfe der Schaltflächen **Speichern** und **Veröffentlichen** auf der Symbolleiste. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png" alt-text="Bearbeiten eines PowerShell-Runbooks":::
    
4. Testen Sie das Runbook, indem Sie auf der Symbolleiste die Schaltfläche **Starten** auswählen. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png" alt-text="Schaltfläche zum Starten eines Runbooks":::
    
5. Führen Sie im Bereich **Runbook starten** die folgenden Aktionen aus: 

    1. Geben Sie unter **Ressourcengruppenname** den Namen der Ressourcengruppe ein, in der sich die ADF mit der Azure-SSIS IR befindet. 
    2. Geben Sie unter **Name der Data Factory** den Namen Ihrer ADF mit der Azure-SSIS IR ein. 
    3. Geben Sie unter **Azure-SSIS Name** den Namen der Azure-SSIS IR ein. 
    4. Geben Sie unter **VORGANG** die Zeichenfolge **START** ein. 
    5. Klicken Sie auf **OK**.  

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png" alt-text="Fenster „Runbook starten“":::
   
6. Wählen Sie im Auftragsfenster die Kachel **Ausgabe** aus. Warten Sie im Ausgabefenster, bis die Meldung **##### Abgeschlossen #####** angezeigt wird, nachdem zuvor **##### Wird gestartet #####** angezeigt wurde. Das Starten einer Azure-SSIS IR dauert etwa 20 Minuten. Schließen Sie das Fenster **Auftrag**, und kehren Sie zum Fenster **Runbook** zurück.

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png" alt-text="Screenshot mit hervorgehobener Kachel „Ausgabe“":::
    
7. Wiederholen Sie die vorherigen beiden Schritte, wobei Sie als Wert für **VORGANG** dieses Mal **STOPP** verwenden. Starten Sie das Runbook erneut, indem Sie auf der Symbolleiste die Schaltfläche **Starten** auswählen. Geben Sie die Namen Ihrer Ressourcengruppe, Ihrer ADF und Ihrer Azure-SSIS IR ein. Geben Sie unter **VORGANG** die Zeichenfolge **STOPP** ein. Warten Sie im Ausgabefenster, bis die Meldung **##### Abgeschlossen #####** angezeigt wird, nachdem zuvor **##### Wird beendet #####** angezeigt wurde. Das Beenden einer Azure-SSIS IR dauert nicht so lange wie das Starten. Schließen Sie das Fenster **Auftrag**, und kehren Sie zum Fenster **Runbook** zurück.

8. Sie können Ihr Runbook auch über einen Webhook auslösen, der durch Auswählen des Menüelements **Webhooks** oder nach einem Zeitplan erstellt werden kann, der durch Auswählen des Menüelements **Zeitpläne** wie unten angegeben erstellt werden kann.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Erstellen von Zeitplänen für das Runbook zum Starten/Beenden der Azure-SSIS IR

Im vorherigen Abschnitt haben Sie ein Azure Automation-Runbook erstellt, das eine Azure-SSIS IR starten oder beenden kann. In diesem Abschnitt erstellen Sie zwei Zeitpläne für das Runbook. Beim Konfigurieren des ersten Zeitplans geben Sie **START** für **VORGANG** ein. Gleichermaßen geben Sie beim Konfigurieren des zweiten Zeitplans **STOPP** für **VORGANG** an. Ausführliche Schritte zum Erstellen von Zeitplänen finden Sie im Artikel [Erstellen eines Zeitplans](../automation/shared-resources/schedules.md#create-a-schedule).

1. Wählen Sie im Fenster **Runbook** die Option **Zeitpläne** aus, und wählen Sie auf der Symbolleiste **+ Zeitplan hinzufügen** aus. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png" alt-text="Azure SSIS-IR – gestartet":::
   
2. Führen Sie im Bereich **Runbook planen** die folgenden Aktionen aus: 

    1. Wählen Sie **Zeitplan mit Runbook verknüpfen** aus. 
    2. Wählen Sie **Neuen Zeitplan erstellen** aus.
    3. Geben Sie im Bereich **Neuer Zeitplan** unter **Name** die Zeichenfolge **Start IR daily** ein. 
    4. Geben Sie für **Startet** eine Uhrzeit ein, die wenige Minuten nach der aktuellen Zeit liegt. 
    5. Wählen Sie unter **Wiederholung** die Option **Serie** aus. 
    6. Geben Sie für **Wiederholen alle** die Zahl **1** ein, und wählen Sie **Tag** aus. 
    7. Klicken Sie auf **Erstellen**. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png" alt-text="Zeitplan für den Start der Azure SSIS-IR":::
    
3. Wechseln Sie zur Registerkarte **Parameter und Ausführungseinstellungen**. Geben Sie die Namen Ihrer Ressourcengruppe, Ihrer ADF und Ihrer Azure-SSIS IR an. Geben Sie unter **VORGANG** die Zeichenfolge **START** ein, und wählen Sie **OK** aus. Wählen Sie erneut **OK** aus, um den Zeitplan auf der Seite **Zeitpläne** des Runbooks anzuzeigen. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png" alt-text="Screenshot mit hervorgehobenem Feld „Vorgang“":::
    
4. Wiederholen Sie die vorherigen beiden Schritte, um einen Zeitplan mit dem Namen **Stop IR daily** zu erstellen. Geben Sie als Uhrzeit eine Zeit ein, die mindestens 30 Minuten nach dem für den Zeitplan **Start IR daily** angegebenen liegt. Geben Sie unter **VORGANG** die Zeichenfolge **STOPP** ein, und wählen Sie **OK** aus. Wählen Sie erneut **OK** aus, um den Zeitplan auf der Seite **Zeitpläne** des Runbooks anzuzeigen. 

5. Wählen Sie im Fenster **Runbook** im linken Menü **Aufträge** aus. Die über die Zeitpläne zu den angegebenen Zeitpunkten erstellten Aufträge sollten nun mit ihrem Status angezeigt werden. Es werden ähnlich dem Ablauf beim Testen des Runbooks Details zum Auftrag angezeigt, etwa die Ausgabe. 

   :::image type="content" source="./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png" alt-text="Zeitplan zum Starten der Azure SSIS-IR":::
    
6. Wenn Sie mit dem Testen fertig sind, deaktivieren Sie die Zeitpläne, indem Sie sie bearbeiten. Wählen Sie im linken Menü **Zeitpläne** aus, wählen Sie **Start IR daily/Stop IR daily** aus, und wählen Sie für **Aktiviert** die Option **Nein** aus. 

## <a name="next-steps"></a>Nächste Schritte
Informationen finden Sie im folgenden Blogbeitrag:
-   [Modernisieren und Erweitern von ETL/ELT-Workflows mit SSIS-Aktivitäten in ADF-Pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Informationen finden Sie in den folgenden Artikeln der SSIS-Dokumentation: 

- [Bereitstellen, Ausführen und Überwachen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to the SSISDB Catalog database on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Herstellen einer Verbindung mit der SSISDB-Katalogdatenbank in Azure)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Planen der Ausführung eines SSIS-Pakets in Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung)
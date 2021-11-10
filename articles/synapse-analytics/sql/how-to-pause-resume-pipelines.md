---
title: Anhalten und Fortsetzen dedizierter SQL-Pools mit Synapse-Pipelines
description: Erfahren Sie, wie Sie das Anhalten und Fortsetzen für einen dedizierten SQL-Pool mit Synapse-Pipelines in Azure Synapse Analytics automatisieren.
author: julieMSFT
ms.author: jrasnick
ms.service: synapse-analytics
ms.reviewer: wiassaf
ms.subservice: sql
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d31cb6d07958c5f14ac5e45f0094e772b168a535
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847924"
---
# <a name="pause-and-resume-dedicated-sql-pools-with-synapse-pipelines"></a>Anhalten und Fortsetzen dedizierter SQL-Pools mit Synapse-Pipelines

Das Anhalten und Fortsetzen für dedizierte SQL-Pools lässt sich mithilfe von Synapse-Pipelines in Azure Synapse Analytics automatisieren. Anhalten und Fortsetzen können verwendet werden, um Kosten für einen dedizierten SQL-Pool zu sparen. Diese Lösung lässt sich problemlos in einen vorhandenen Datenorchestrierungsprozess einschließen. 

Die folgenden Schritte führen Sie durch das Einrichten des automatisierten Anhaltens und Fortsetzens.

1. Erstellen einer Pipeline.
1. Einrichten von Parametern in Ihrer Pipeline.
1. Identifizieren der Liste dedizierter SQL-Pools in Ihrem Azure Synapse-Arbeitsbereich.
1. Filtern aller dedizierten SQL-Pools aus der Liste, die nicht angehalten oder fortgesetzt werden sollen. 
1. Durchlaufen jedes dedizierten SQL-Pools in einer Schleife, und:
    1. Überprüfen des Zustands des dedizierten SQL-Pools.
    1. Auswerten des Zustands des dedizierten SQL-Pools.
    1. Anhalten oder Fortsetzen des dedizierten SQL-Pools.

Diese Schritte werden in einer einfachen Pipeline in Azure Synapse beschrieben:

![Einfache Synapse-Pipeline](./media/how-to-pause-resume-pipelines/simple-pipeline.png)


Je nach Art Ihrer Umgebung ist der gesamte hier beschriebene Prozess möglicherweise nicht gültig, und Sie sollten eventuell nur die passenden Schritte auswählen. Der hier beschriebene Prozess kann verwendet werden, um alle Instanzen in einer Entwicklungs-, Test- oder PoC-Umgebung anzuhalten oder fortzusetzen. Für eine Produktionsumgebung ist es wahrscheinlicher, das Sie das Anhalten oder Fortsetzen auf Instanzbasis planen, sodass nur die Schritte 5a bis 5c erforderlich sind.

In den obigen Schritten werden die REST-APIs für Azure Synapse und Azure SQL verwendet:

- [Dedizierte SQL-Pool: Vorgänge](/rest/api/synapse/sqlpools)
 
- [Azure SQL-Datenbank-REST-API](/rest/api/sql)

Synapse-Pipelines ermöglichen die Automatisierung des Anhaltens und Fortsetzens, aber Sie können diese Befehle bei Bedarf über das Tool oder die Anwendung Ihrer Wahl ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein vorhandener [Azure Synapse-Arbeitsbereich](../get-started-create-workspace.md)
- Mindestens ein [dedizierter SQL-Pool](../get-started-analyze-sql-pool.md)
- Ihrem Arbeitsbereich muss die Azure-Rolle „Mitwirkender“ zugewiesen sein. Siehe [Gewähren der Azure-Rolle „Mitwirkender“ im Arbeitsbereich für Synapse-Administratoren](../security/how-to-set-up-access-control.md#step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace).

## <a name="step-1-create-a-pipeline-in-synapse-studio"></a>Schritt 1: Erstellen einer Pipeline in Synapse Studio
1. Navigieren Sie zu Ihrem Arbeitsbereich, und öffnen Sie Synapse Studio. 
1. Wählen Sie das Symbol **Integrieren** und dann das **+** -Zeichen aus, um eine neue Pipeline zu erstellen. 
1. Nennen Sie Ihre Pipeline „PauseResume“.

    ![Erstellen einer Pipeline in Synapse Studio](./media/how-to-pause-resume-pipelines/create-pipeline.png) 

## <a name="step-2-create-pipeline-parameters"></a>Schritt 2: Erstellen von Pipelineparametern 

Die Pipeline, die Sie erstellen, wird von Parametern gesteuert. Mit Parametern können Sie eine generische Pipeline erstellen, die Sie über mehrere Abonnements, Ressourcengruppen oder dedizierte SQL-Pools hinweg verwenden können. Wählen Sie die Registerkarte **Parameter** am unteren Rand des Pipelinebildschirms aus. Wählen Sie **+Neu** aus, um jeden der folgenden Parameter zu erstellen:

    
|Name  |type  |Standardwert  |BESCHREIBUNG|
|---------|---------|---------|-----------|
|ResourceGroup    |Zeichenfolge        |Synapse          |Name der Ressourcengruppe für Ihre dedizierten SQL-Pools|
|SubscriptionID   |Zeichenfolge        |`<SubscriptionID>` |Abonnement-ID für Ihre Ressourcengruppe|
|WorkspaceName    |Zeichenfolge        |Synapse          |Name Ihres Arbeitsbereichs|
|SQLPoolName      |Zeichenfolge        |SQLPool1         |Name Ihres dedizierten SQL-Pools|
|PauseorResume    |Zeichenfolge        |Anhalten            |Der gewünschte Zustand am Ende der Pipelineausführung|

![Pipelineparameter in Synapse Studio](./media/how-to-pause-resume-pipelines/pipeline-parameters.png)

## <a name="step-3-create-list-of-dedicated-sql-pools"></a>Schritt 3: Erstellen einer Liste dedizierter SQL-Pools
 
 Richten Sie eine **Web** aktivität ein. Sie verwenden diese Aktivität, um die Liste dedizierter SQL-Pools zu erstellen, indem Sie die REST-API-Anforderung „Dedizierte SQL-Pools – Nach Server auflisten“ aufrufen. Die Ausgabe ist eine JSON-Zeichenfolge, die eine Liste der dedizierten SQL-Pools in Ihrem Arbeitsbereich enthält. Die JSON-Zeichenfolge wird an die nächste Aktivität übergeben.
1. Ziehen Sie unter **Aktivitäten** > **Allgemein** eine **Web** aktivität als erste Phase Ihrer Pipeline auf die Pipeline-Canvas.  
1. Nennen Sie diese Phase auf der Registerkarte **Allgemein** „GET List“ (Liste abrufen). 
1. Wählen Sie die Registerkarte **Einstellungen** aus, klicken Sie auf den **URL**-Eintragsraum, und wählen Sie dann **Dynamischen Inhalt hinzufügen** aus. Kopieren Sie unten die GET-Anforderung, die mithilfe der Zeichenfolgenfunktion @concat parametrisiert wurde, und fügen Sie sie in das Feld für dynamische Inhalte ein. Wählen Sie **Fertig stellen** aus.
Der folgende Code ist eine einfache Get-Anforderung:

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools?api-version=2019-06-01-preview
    ```
    
    GET-Anforderung, die mit der Zeichenfolgenfunktion @concat parametrisiert wurde:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools?api-version=2019-06-01-preview')
    ``` 
1. Wählen Sie die Dropdownliste für **Methode** und dann **Get** aus.  
1. Wählen Sie **Erweitert** aus, um den Inhalt zu erweitern. Wählen Sie **MSI** als Authentifizierungstyp aus. Geben Sie für Ressource `https://management.azure.com/` ein. 
    > [!IMPORTANT]
    > Bei allen Webaktivitäten/REST-API-Webaufrufen müssen Sie sicherstellen, dass die Synapse-Pipeline gegenüber einem dedizierten SQL-Pool authentifiziert ist. Zum Ausführen dieser REST-API-Aufrufe ist eine [verwaltete Identität](../../data-factory/control-flow-web-activity.md#managed-identity) erforderlich. 
        
        
    ![Webaktivitätsliste für dedizierte SQL-Pools](./media/how-to-pause-resume-pipelines/web-activity-list-sql-pools.png)



## <a name="step-4-filter-dedicated-sql-pools"></a>Schritt 4: Filtern dedizierter SQL-Pools
Entfernen Sie dedizierte SQL-Pools, die Sie nicht anhalten oder fortsetzen möchten. Verwenden Sie eine Filteraktivität, die die von der „Get List“-Aktivität übergebenen Werte filtert.  In diesem Beispiel extrahieren wir die Datensätze aus dem Array, deren Namen nicht „prod“ enthalten. Wenden Sie nach Bedarf weitere Bedingungen an. Filtern Sie beispielsweise nach der SKU/dem Namen des Synapse-Arbeitsbereichs, um sicherzustellen, dass nur gültige dedizierte SQL-Pools identifiziert werden.
1. Wählen Sie die Aktivität **Filter**(n) unter **Iteration und Bedingungen** aus, und ziehen Sie sie auf die Pipeline-Canvas.    
![Filtern dedizierter SQL-Pools](./media/how-to-pause-resume-pipelines/filter-sql-pools.png)    
1. Verbinden Sie die „Get List“-Webaktivität mit der „Filter“-Aktivität. Wählen Sie die grüne Registerkarte an der Webaktivität aus, und ziehen Sie sie in das Filter-Feld.
1. Geben Sie für **Elemente** den Wert `@activity('Get list').output.value` ein, wobei „GET List“ der Name der vorangehenden Webaktivität ist.
1. Geben Sie als **Bedingung** den Wert `@not(endswith(item().name,'prod'))` ein. Die verbleibenden Datensätze im Array werden dann an die nächste Aktivität übergeben.

## <a name="step-5-create-a-foreach-loop"></a>Schritt 5: Erstellen einer ForEach-Schleife
Erstellen Sie eine ForEach-Aktivität, um jeden dedizierten SQL-Pool in Schleife zu durchlaufen. 
1. Wählen Sie die Aktivität **ForEach** unter **Iteration und Bedingungen** aus, und ziehen Sie sie auf die Pipeline-Canvas.
1. Geben Sie auf der Registerkarte **Allgemein** der Aktivität einen Namen, wir haben „ForEach_pool“ verwendet. 
1. Wählen Sie auf der Registerkarte „Einstellungen“ die Eingabe **Elemente** und dann **Dynamischen Inhalt hinzufügen** aus. Scrollen Sie zu den **Aktivitätsausgaben**, und wählen Sie die Ausgabe Ihrer Filteraktivität aus. Fügen Sie der Aktivität `.value` hinzu. Der Wert sollte `@activity('Filter_PROD').output.value` ähneln. Klicken Sie auf **Fertig stellen**.    
    ![Durchlaufen dedizierter SQL-Pools in Schleife](./media/how-to-pause-resume-pipelines/loop-through-sql-pools.png)
1. Wählen Sie die Registerkarte **Aktivitäten** und dann den Bearbeitungsstift aus, um die ForEach-Schleifen-Canvas zu öffnen. 

## <a name="step-5a-check-the-state-of-the-dedicated-sql-pools"></a>Schritt 5a: Überprüfen des Zustands der dedizierten SQL-Pools    
Zum Überprüfen des Zustands des dedizierten SQL-Pools ist eine Webaktivität erforderlich, ähnlich wie in Schritt 1. Diese Aktivität ruft die [REST-API „Dedizierten SQL-Poolzustand überprüfen“ für Azure Synapse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#check-database-state) auf. 
1. Wählen Sie unter **Allgemein** eine **Web** aktivität aus, und ziehen Sie sie auf die Pipeline-Canvas.    
2. Nennen Sie diese Phase auf der Registerkarte **Allgemein** „CheckState“. 
3. Wählen Sie die Registerkarte **Einstellungen** aus.     
4. Klicken Sie in den **URL**-Eintragsraum, und wählen Sie dann **Dynamischen Inhalt hinzufügen** aus. Kopieren Sie unten die GET-Anforderung, die mithilfe der Zeichenfolgenfunktion @concat parametrisiert wurde, und fügen Sie sie in das Feld für dynamische Inhalte ein. Wählen Sie **Fertig stellen** aus. Beim erneuten Überprüfen des Zustands wird mithilfe des folgenden Aufrufs eine Get-Anforderung verwendet:

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    Die mithilfe der Zeichenfolgenfunktion @concat parametrisierte GET-Anforderung:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',item().name,'?api-version=2019-06-01-preview')
    ```
    
    In diesem Fall verwenden wir „item().name“. Dies ist der Name des dedizierten SQL-Pools aus Schritt 1, der von der ForEach-Schleife an diese Aktivität übergeben wurde. Wenn Sie eine Pipeline zum Steuern eines einzelnen dedizierten SQL-Pools verwenden, können Sie hier den Namen Ihres dedizierten SQL-Pools einbetten oder einen Parameter aus der Pipeline verwenden. Beispielsweise können Sie „pipeline().parameters.SQLPoolName“ verwenden.

    Die Ausgabe ist eine JSON-Zeichenfolge, die Details zum dedizierten SQL-Pool enthält, einschließlich seines Status (in „properties.status“). Die JSON-Zeichenfolge wird an die nächste Aktivität übergeben. 
1. Wählen Sie die Dropdownliste für **Methode** aus, und wählen Sie **Get** aus. Wählen Sie **Erweitert** aus, um den Inhalt zu erweitern. Wählen Sie **MSI** als Authentifizierungstyp aus. Geben Sie für Ressource `https://management.azure.com/` ein. 

![Überprüfen des Zustands des dedizierten SQL-Pools](./media/how-to-pause-resume-pipelines/check-sql-pool-state.png)
    

    
## <a name="step-5b-evaluate-the-state-of-the-dedicated-sql-pools"></a>Schritt 5b: Auswerten des Zustands der dedizierten SQL-Pools
Werten Sie den gewünschten Zustand, „Anhalten“ oder „Fortsetzen“, und den aktuellen Status, „Online“ oder „Angehalten“, aus, und initiieren Sie dann „Anhalten“ oder „Fortsetzen“ nach Bedarf.

1. Wählen Sie die Aktivität **Switch** (Wechseln) unter **Iteration und Bedingungen** aus, und ziehen Sie sie auf die Pipeline-Canvas.    
1. Verbinden Sie die Aktivität **Switch** mit der Aktivität **CheckState**. Wählen Sie die grüne Registerkarte an der Webaktivität aus, und ziehen Sie sie in das Switch-Feld.  
1. Geben Sie dieser Phase auf der Registerkarte **Allgemein** den Namen „State-PauseOrResume“. 

    Basierend auf dem gewünschten Zustand und dem aktuellen Status erfordern nur die folgenden beiden Kombinationen eine Zustandsänderung: „Paused->Resume“ (Angehalten->Fortsetzen) oder „Online->Pause“ (Online->Anhalten). 

1. Kopieren Sie auf der Registerkarte **Aktivitäten** den folgenden Code in den **Ausdruck**.

    ```HTTP
    @concat(activity('CheckState').output.properties.status,'-',pipeline().parameters.PauseOrResume)
    ```
    
    Wobei „CheckState“ (Zustand überprüfen) der Name der vorhergehenden Webaktivität ist, mit „output.properties.status“, das den aktuellen Status definiert, und „pipeline().parameters.PauseOrResume“, das den gewünschten Zustand angibt.
    
    Die Überprüfungsbedingung überprüft den gewünschten Zustand und den aktuellen Status. Wenn der gewünschte Zustand „Resume“ (Fortsetzen) und der aktuelle Status „Paused“ (Angehalten) ist, wird in innerhalb des „Paused->Resume“-Falls (Angehalten->Fortsetzen) eine Resume-Aktivität (Fortsetzen) aufgerufen. Wenn der gewünschte Zustand „Paused“ (Angehalten) und der aktuelle Status „Online“ ist, wird in innerhalb des „Online->Pause“-Falls (Online->Anhalten) eine Pause-Aktivität (Anhalten) aufgerufen. Alle anderen Fälle, z. B. ein gewünschter Zustand „Pause“ (Anhalten) und ein aktueller Status „Paused“ (Angehalten) oder ein gewünschter Zustand „Resume“ (Fortsetzen) und ein aktueller Status „Online“ erfordern keine Aktion und werden vom Standardfall behandelt, der über keine Aktivitäten verfügt.
1. Wählen Sie auf der Registerkarte „Aktivitäten“ die Option **+ Fall hinzufügen** aus.  Fügen Sie die Fälle `Paused-Resume` und `Online-Pause` hinzu. 
    ![Überprüfen der Statusbedingung des dedizierten SQL-Pools](./media/how-to-pause-resume-pipelines/check-condition.png)

### <a name="step-5c-pause-or-resume-dedicated-sql-pools"></a>Schritt 5c: Anhalten oder Fortsetzen dedizierter SQL-Pools     

Der letzte und einzige relevante Schritt für einige Anforderungen besteht darin, das Anhalten oder Fortsetzen Ihres dedizierten SQL-Pools zu initiieren. In diesem Schritt wird erneut eine Webaktivität verwendet, die die [REST-API „Compute anhalten oder fortsetzen“ für Azure Synapse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#pause-compute) aufruft. 
1. Wählen Sie den Bearbeitungsstift für Aktivitäten aus, und fügen Sie der „State-PauseorResume“-Canvas eine **Web** aktivität hinzu. 
1. Wählen Sie die Registerkarte **Einstellungen** aus, klicken Sie auf den **URL**-Eintragsraum, und wählen Sie dann **Dynamischen Inhalt hinzufügen** aus. Kopieren Sie unten die POST-Anforderung, die mithilfe der Zeichenfolgenfunktion @concat parametrisiert wurde, und fügen Sie sie in das Feld für dynamische Inhalte ein. Wählen Sie **Fertig stellen** aus. 
1. Wählen Sie die Dropdownliste für **Methode** und dann **POST** aus.
1. Geben Sie im „Body“-Abschnitt (Textkörper) „Pause and Resume“ (Anhalten und Fortsetzen) ein.
1. Wählen Sie **Erweitert** aus, um den Inhalt zu erweitern. Wählen Sie **MSI** als Authentifizierungstyp aus. Geben Sie für Ressource `https://management.azure.com/` ein. 
1. Fügen Sie mithilfe des unten stehenden, parametrisierten Codes eine zweite Aktivität für die Fortsetzen-Funktion hinzu.

    ![Fortsetzen eines dedizierten SQL-Pools](./media/how-to-pause-resume-pipelines/true-condition-resume.png)
    
    
    Im folgenden Beispiel wird ein dedizierter SQL-Pool fortgesetzt, indem eine POST-Anforderung mithilfe des folgenden Aufrufs aufgerufen wird:
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/resume?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    Sie können die oben stehende POST-Anweisung mithilfe der Zeichenfolgenfunktion @concat parametrisieren:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/resume?api-version=2019-06-01-preview')
    ```
    
    In diesem Fall verwenden wir die Aktivität 'Check State'.output.name mit den Namen der dedizierten SQL-Pools aus Schritt 3a, die über die Switch-Bedingung an diese Aktivität übergeben wurden. Wenn Sie eine einzelne Aktivität mit einer einzelnen Datenbank verwenden, können Sie hier den Namen Ihres dedizierten SQL-Pools einbetten oder einen Parameter aus der Pipeline verwenden. Beispielsweise können Sie „pipeline().parameters.DatabaseName“ verwenden.
    
    Die POST-Anforderung zum Anhalten eines dedizierten SQL-Pools lautet:
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/pause?api-version=2019-06-01-preview HTTP/1.1    
    ```    
    
    Die POST-Anforderung kann wie gezeigt mit der Zeichenfolgenfunktion @concat parametrisiert werden:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/pause?api-version=2019-06-01-preview')
    ```

## <a name="pipeline-run-output"></a>Ausgabe der Pipelineausführung

Wenn die vollständige Pipeline ausgeführt wird, wird die unten aufgeführte Ausgabe angezeigt. Sie können Ihre Pipeline ausführen, indem Sie den Modus **Debug** oder **add trigger** (Trigger hinzufügen) auswählen. Für die folgenden Pipelineergebnisse wurde der Pipelineparameter namens „ResourceGroup“ auf eine einzelne Ressourcengruppe mit zwei Synapse-Arbeitsbereichen festgelegt. Eine wurde „testprod“ genannt und herausgefiltert, die zweite wurde „test1“ genannt. Der dedizierte SQL-Pool „test1“ wurde angehalten, sodass der Auftrag eine Fortsetzung initiiert hat.

![Ausgabe der Pipelineausführung](./media/how-to-pause-resume-pipelines/pipeline-run-output.png)

## <a name="save-your-pipeline"></a>Speichern Ihrer Pipeline

Um Ihre Pipeline zu speichern, wählen Sie **Alle veröffentlichen** über Ihrer Pipeline aus.

## <a name="schedule-your-pause-or-resume-pipeline-to-run"></a>Planen der Ausführung Ihrer Pipeline zum Anhalten oder Fortsetzen

Um Ihre Pipeline zu planen, wählen Sie am Anfang Ihrer Pipeline **Trigger hinzufügen** aus. Befolgen Sie die Bildschirme, um die Ausführung Ihrer Pipeline zu einem bestimmten Zeitpunkt zu planen.

![Auswählen des Triggers zum Festlegen der Zeit für die Ausführung der Pipeline](./media/how-to-pause-resume-pipelines/trigger.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur verwalteten Identität für Azure Synapse und dazu, wie eine verwaltete Identität Ihrem dedizierten SQL-Pool hinzugefügt wird, finden Sie hier:

[Azure Synapse-Arbeitsbereich – verwaltete Identität](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)

[Erteilen von Berechtigungen für die verwaltete Identität eines Arbeitsbereichs (Vorschau)](../security/how-to-grant-workspace-managed-identity-permissions.md)

---
title: Kontinuierliche Integration und Bereitstellung in Azure Synapse Analytics
description: Erfahren Sie, wie Sie kontinuierliche Integration und kontinuierliche Bereitstellung (CI/CD) verwenden, um Änderungen zwischen Umgebungen in einem Azure Synapse Analytics-Arbeitsbereich bereitzustellen.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729245"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Kontinuierliche Integration und Bereitstellung für einen Azure Synapse Analytics-Arbeitsbereich

Kontinuierliche Integration (Continuous Integration, CI) ist der Prozess, bei dem die Erstellung und das Testen von Code jedes Mal automatisiert wird, wenn ein Teammitglied eine Änderung an die Versionskontrolle überträgt. Kontinuierliche Bereitstellung (Continuous Delivery, CD) ist der Prozess des Erstellens, Testens, Konfigurierens und Bereitstellens von mehreren Test- oder Staging-Umgebungen in eine Produktionsumgebung.

In einem Azure Synapse Analytics-Arbeitsbereich verschiebt CI/CD alle Einheiten von einer Umgebung (Entwicklung, Test, Produktion) in eine andere Umgebung. Das Promoten Ihres Arbeitsbereichs in einen anderen Arbeitsbereich ist ein zweiteiliger Prozess. Im ersten Schritt verwenden Sie eine [Azure Resource Manager-Vorlage (ARM-Vorlage)](../../azure-resource-manager/templates/overview.md), um Arbeitsbereichsressourcen (Pools und Arbeitsbereich) zu erstellen oder zu aktualisieren. Migrieren Sie dann Artefakte wie SQL-Skripte und Notebooks, Spark-Auftragsdefinitionen, Pipelines, Datensätze und Datenflüsse mithilfe der Azure Synapse CI/CD-Tools in Azure DevOps oder auf GitHub. 

In diesem Artikel wird beschrieben, wie eine Azure DevOps-Release-Pipeline und GitHub Actions verwendet werden können, um die Bereitstellung eines Azure Synapse-Arbeitsbereichs in mehreren Umgebungen zu automatisieren.

## <a name="prerequisites"></a>Voraussetzungen

Um die Bereitstellung eines Azure Synapse-Arbeitsbereichs in mehreren Umgebungen zu automatisieren, müssen die folgenden Voraussetzungen und Konfigurationen vorhanden sein.

### <a name="azure-devops"></a>Azure DevOps

- Bereiten Sie ein Azure DevOps-Projekt für die Ausführung der Release-Pipeline vor.
- [Gewähren Sie allen Benutzern, die Code einchecken werden, Basiszugriff auf Organisationsebene](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true), damit sie das Repository sehen können.
- Erteilen Sie dem Azure Synapse-Repository die Berechtigung Owner.
- Stellen Sie sicher, dass Sie einen selbst gehosteten Azure DevOps VM-Agent erstellt haben oder einen von Azure DevOps gehosteten Agenten verwenden.
- Erteilen Sie die Berechtigung [, eine Azure Resource Manager-Dienstverbindung für die Ressourcengruppe](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true) zu erstellen.
- Ein Azure Active Directory-Administrator (Azure AD) muss die [Agent-Erweiterung für die Azure DevOps Synapse-Arbeitsbereichsbereitstellung in der Azure DevOps-Organisation installieren](/azure/devops/marketplace/install-extension).
- Erstellen Sie ein Dienstkonto für die Pipelineausführung, oder legen Sie für die Ausführung ein vorhandenes Dienstkonto fest. Sie können anstelle eines Dienstkontos ein persönliches Zugriffstoken verwenden, aber Ihre Pipelines funktionieren nicht mehr, wenn das Benutzerkonto gelöscht wird.

### <a name="github"></a>GitHub

- Erstellen Sie ein GitHub-Repository, das die Artefakte des Azure-Synapse-Arbeitsbereichs und die Arbeitsbereichsvorlage enthält. 
- Stellen Sie sicher, dass Sie einen selbst gehosteten Runner erstellt haben oder einen auf GitHub gehosteten Runner verwenden.

### <a name="azure-active-directory"></a>Azure Active Directory

- Wenn Sie einen Dienstprinzipal verwenden, erstellen Sie in Azure AD einen Dienstprinzipal, der für die Bereitstellung verwendet wird. 
- Wenn Sie eine verwaltete Identität verwenden, aktivieren Sie die vom System zugewiesene verwaltete Identität auf Ihrer VM in Azure als Agent oder Runner und fügen Sie diese dann zu Azure Synapse Studio als Synapse-Admin hinzu.
- Verwenden Sie die Azure AD-Administratorrolle, um diese Aktionen durchzuführen.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Sie können diese Voraussetzungen automatisieren und bereitstellen, indem Sie die gleiche Pipeline, eine ARM-Vorlage oder die Azure-Befehlszeilenschnittstelle verwenden, aber diese Prozesse werden in diesem Artikel nicht beschrieben.

- Der "Quell"-Arbeitsbereich, der für die Entwicklung verwendet wird, muss mit einem Git-Repository in Azure Synapse Studio konfiguriert werden. Weitere Informationen finden Sie unter [Quellsteuerung in Azure Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Richten Sie einen leeren Arbeitsbereich für die Bereitstellung ein:

  1. Erstellen Sie einen neuen Azure Synapse-Arbeitsbereich.
  1. Erteilen Sie dem VM-Agenten und dem Dienstprinzipal die Berechtigung Contributor für die Ressourcengruppe, in der sich der neue Arbeitsbereich befindet.
  1. Konfigurieren Sie im Arbeitsbereich die Git-Repository-Verbindung nicht.
  1. Suchen Sie im Azure-Portal den neuen Azure-Synapse-Arbeitsbereich und erteilen Sie sich und dem Benutzer, der die Azure-DevOps-Pipeline Azure-Synapse-Arbeitsbereich ausführen wird, die Berechtigung Owner. 
  1. Fügen Sie den Azure DevOps VM-Agenten und den Dienstprinzipal zur Contributor-Rolle für den Arbeitsbereich hinzu. (Die Rolle sollte geerbt worden sein, aber überprüfen Sie, ob dies der Fall ist).
  1. Gehen Sie im Azure Synapse-Arbeitsbereich zu **Studio** > **Verwaltung** > **Zugriffskontrolle**. Fügen Sie den Azure DevOps VM-Agenten und den Dienstprinzipal zur Arbeitsbereich-Administratorgruppe hinzu.
  1. Öffnen Sie das Speicherkonto, das für den Arbeitsbereich verwendet wird. Fügen Sie im Bereich **Identitäts- und Zugriffsverwaltung** den VM-Agenten und den Dienstprinzipal zur Rolle Storage Blob Data Contributor hinzu.
  1. Erstellen Sie einen Schlüsseltresor im Support-Abonnement und stellen Sie sicher, dass sowohl der vorhandene Arbeitsbereich als auch der neue Arbeitsbereich mindestens über die Berechtigungen GET und LIST für den Tresor verfügen.
  1. Damit die automatisierte Bereitstellung funktioniert, stellen Sie sicher, dass alle Verbindungszeichenfolgen, die in Ihren verknüpften Diensten angegeben sind, im Schlüsseltresor vorhanden sind.

### <a name="other-prerequisites"></a>Weitere Voraussetzungen
 
- Spark-Pools und selbst gehostete Integrationslaufzeiten werden nicht in einer Arbeitsbereichsbereitstellungsaufgabe erstellt. Wenn Sie einen verknüpften Dienst haben, der eine selbst gehostete Integrationslaufzeit verwendet, erstellen Sie die Laufzeit manuell in dem neuen Arbeitsbereich.
- Wenn die Elemente im Entwicklungsarbeitsbereich mit den spezifischen Pools verknüpft sind, stellen Sie sicher, dass Sie die gleichen Namen für die Pools im Zielarbeitsbereich in der Parameterdatei erstellen oder parametrisieren.  
- Wenn Ihre bereitgestellten SQL-Pools zum Zeitpunkt der Bereitstellung angehalten sind, kann die Bereitstellung möglicherweise nicht erfolgreich durchgeführt werden.

Weitere Informationen finden Sie unter [CI/CD in Azure Synapse Analytics Teil 4 - Die Release-Pipeline](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Einrichten einer Releasepipeline in Azure DevOps

In diesem Abschnitt erfahren Sie, wie Sie einen Azure Synapse-Arbeitsbereich in Azure DevOps bereitstellen. 

1. Öffnen Sie in [Azure DevOps](https://dev.azure.com/) das Projekt, das Sie für die Veröffentlichung erstellt haben.

1. Wählen Sie im linken Menü **Pipelines** > **Releases**.

    :::image type="content" source="media/create-release-pipeline.png" alt-text="Screenshot, der die Auswahl von Pipelines und dann Releases im Azure DevOps-Menü zeigt.":::        
 
1. Wählen Sie **Neue Pipeline** aus. Wenn Sie bestehende Pipelines haben, wählen Sie **Neu** > **Neue Release-Pipeline**.

1. Wählen Sie die Vorlage **Leere Stufe** aus.

    :::image type="content" source="media/create-release-select-empty.png" alt-text="Screenshot, der die Auswahl der Jobvorlage &quot;Leer&quot; zeigt":::.

1. Geben Sie in **Stufenname** den Namen Ihrer Umgebung ein.

1. Wählen Sie **Artefakt hinzufügen**, und wählen Sie dann das Git-Repository, das mit Azure Synapse Studio in Ihrer Entwicklungsumgebung konfiguriert ist. Wählen Sie das Git-Repository, in dem Sie Ihre Pools und Ihre Arbeitsbereich-ARM-Vorlage verwalten. Wenn Sie GitHub als Quelle verwenden, erstellen Sie eine Dienstverbindung für Ihr GitHub-Konto und ziehen Sie Repositories. Weitere Informationen finden Sie unter [Dienstverbindungen](/azure/devops/pipelines/library/service-endpoints).

    :::image type="content" source="media/release-creation-github.png" alt-text="Screenshot, der die Auswahl von GitHub zeigt, um einen Veröffentlichungszweig für ein neues Artefakt hinzuzufügen.":::

1. Wählen Sie die Ressource ARM-Vorlagenzweig. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="Screenshot, der die Einstellung der Ressource ARM-Vorlagenzweig zeigt ":::.

1. Wählen Sie für die Artefakte **Standardzweig** das Repository [Veröffentlichungszweig](source-control.md#configure-publishing-settings). Standardmäßig ist der Veröffentlichungszweig `workspace_publish`. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="Screenshot, der die Einstellung der Artefaktverzweigung zeigt ":::.

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>Einrichten einer Stage-Task für eine ARM-Vorlage zum Erstellen und Aktualisieren einer Ressource 

Wenn Sie eine ARM-Vorlage haben, die eine Ressource bereitstellt, wie z. B. einen Azure Synapse-Arbeitsbereich, einen Spark- und SQL-Pool oder einen Schlüsseltresor, fügen Sie eine Azure Resource Manager-Bereitstellungsaufgabe hinzu, um diese Ressourcen zu erstellen oder zu aktualisieren:

1. Wählen Sie in der Stufenansicht die Option **Stufenaufgaben anzeigen** aus.

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="Screenshot, der die Einstellung der Bühnenansicht zeigt.":::

1. Erstellen Sie eine neue Aufgabe. Suchen Sie nach **AMR-Vorlagenbereitstellung**, und wählen Sie dann **Hinzufügen** aus.

1. Wählen Sie auf der Registerkarte Bereitstellung **Aufgaben** das Abonnement, die Ressourcengruppe und den Speicherort für den Arbeitsbereich. Geben Sie die Anmeldeinformationen an, falls dies erforderlich ist.

1. Wählen Sie für **Aktion** die Option **Ressourcengruppe erstellen oder aktualisieren**.

1. Wählen Sie für **Vorlage** die Ellipsenschaltfläche ( **...** ). Gehen Sie zur ARM-Vorlage des Arbeitsbereichs.

1. Für **Vorlagenparameter**, wählen Sie **...** um die Parameterdatei auszuwählen.

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="Screenshot, der den: Arbeitsbereich und die Pools zeigt ":::.

1. Wählen Sie für **Vorlagenparameter überschreiben** die Option **...** und geben Sie dann die Parameterwerte ein, die Sie für den Arbeitsbereich verwenden möchten. 

1. Wählen Sie für **Entwicklungsmodus** die Option **Inkrementell**.

1. (Optional) Fügen Sie **Azure PowerShell** für die Berechtigung hinzu und aktualisieren Sie die Zuweisung der Arbeitsbereichsrolle. Wenn Sie eine Freigabe-Pipeline verwenden, um einen Azure Synapse-Arbeitsbereich zu erstellen, wird der Dienstprinzipal der Pipeline als Standard-Arbeitsbereichsadministrator hinzugefügt. Sie können PowerShell ausführen, um anderen Konten Zugriff auf den Arbeitsbereich zu gewähren. 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="Screenshot, der die Ausführung eines PowerShell-Skripts zur Erteilung von Berechtigungen demonstriert.":::

> [!WARNING]
> Im Modus der vollständigen Bereitstellung werden Ressourcen in der Ressourcengruppe, die nicht in der neuen ARM-Vorlage angegeben sind, *gelöscht*. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](../../azure-resource-manager/templates/deployment-modes.md).

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>Einrichten einer Bühnenaufgabe für die Bereitstellung von Azure Synapse-Artefakten 

Verwenden Sie die Erweiterung [Synapse workspace deployment](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy), um andere Elemente in Ihrem Azure Synapse-Arbeitsbereich bereitzustellen. Zu den Elementen, die Sie bereitstellen können, gehören Datensätze, SQL-Skripte und Notizbücher, eine Spark-Auftragsdefinition, ein Datenfluss, eine Pipeline, ein verknüpfter Dienst, Anmeldeinformationen und eine Integrationslaufzeit.  

1. Suchen Sie nach der Erweiterung auf dem Visual Studio Marketplace [ und holen Sie diese dort ab](https://marketplace.visualstudio.com/azuredevops).

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="Der Screenshot zeigt die Synapse-Erweiterung zur Bereitstellung des Arbeitsbereichs, wie sie im Visual Studio Marketplace angezeigt wird.":::

1. Wählen Sie die Azure DevOps-Organisation aus, in der Sie die Erweiterung installieren möchten. 

    :::image type="content" source="media/install-extension.png" alt-text="Screenshot, der die Auswahl einer Organisation zeigt, in der die Erweiterung für die Bereitstellung des Synapse-Arbeitsbereichs installiert werden soll.":::

1. Vergewissern Sie sich, dass der Dienstprinzipal der Azure DevOps-Pipeline die Berechtigung Abonnement erhalten hat und als Synapse-Arbeitsbereich-Admin für den Arbeitsbereich zugewiesen ist. 

1. Um eine neue Aufgabe zu erstellen, suchen Sie nach **Synapse-Arbeitsbereichsbereitstellung**, und wählen Sie dann **Hinzufügen**.

    :::image type="content" source="media/add-extension-task.png" alt-text="Screenshot, der die Suche nach der Bereitstellung des Synapse-Arbeitsbereichs zur Erstellung einer Aufgabe zeigt.":::

1. In der Aufgabe, neben **Vorlage**, wählen Sie **...** um die Vorlagendatei zu wählen.

1. Wählen Sie neben **Vorlagenparameter** die Option **...**  um die Parameterdatei auszuwählen.

1. Wählen Sie eine Verbindung, eine Ressourcengruppe und einen Namen für den Arbeitsbereich. 

1. Wählen Sie neben **Vorlagenparameter überschreiben** die Option **...** . Geben Sie die Parameterwerte ein, die Sie für den Arbeitsbereich verwenden möchten, einschließlich der Verbindungszeichenfolgen und Kontoschlüssel, die in Ihren verknüpften Diensten verwendet werden. Weitere Informationen finden Sie unter [CI/CD in Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434).

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="Screenshot, der die Einrichtung der Synapse-Bereitstellung für den Arbeitsbereich zeigt.":::

> [!IMPORTANT]
> In CI/CD-Szenarien muss der Typ der Integrationslaufzeit in den verschiedenen Umgebungen derselbe sein. Wenn Sie beispielsweise eine selbst gehostete Integrationslaufzeit in der Entwicklungsumgebung haben, muss dieselbe Integrationslaufzeit in anderen Umgebungen, wie z. B. in Test und Produktion, selbst gehostet sein. Wenn Sie Integrationslaufzeiten über mehrere Stufen hinweg gemeinsam nutzen, müssen die Integrationslaufzeiten in allen Umgebungen, z. B. in der Entwicklungs-, Test- und Produktionsumgebung, verknüpft und selbst gehostet sein.

### <a name="create-a-release-for-deployment"></a>Erstellen Sie eine Freigabe für die Bereitstellung 

Nachdem Sie alle Änderungen gespeichert haben, können Sie **Freigabe erstellen** wählen, um manuell eine Freigabe zu erstellen. Wie Sie die Erstellung von Releases automatisieren können, erfahren Sie unter [Azure DevOps Release Triggers](/azure/devops/pipelines/release/triggers).

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="Screenshot, der das Pipeline-Fenster für neue Releases zeigt, wobei Release erstellen hervorgehoben ist":::.

## <a name="set-up-a-release-in-github-actions"></a>Einrichten einer Freigabe in GitHub Actions 

In diesem Abschnitt erfahren Sie, wie Sie GitHub-Workflows mithilfe von GitHub Actions für die Bereitstellung von Azure Synapse-Arbeitsbereichen erstellen.

Sie können die [GitHub-Aktion für Azure Resource Manager-Vorlage](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) verwenden, um die Bereitstellung einer ARM-Vorlage in Azure für den Arbeitsbereich und die Compute-Pools zu automatisieren.

### <a name="workflow-file"></a>Workflowdatei

Definieren Sie einen GitHub Actions-Workflow in einer YAML-Datei (.yml) unter dem Pfad */.github/workflows/* in Ihrem Repository. Die Definition enthält die verschiedenen Schritte und Parameter, aus denen der Workflow besteht.

Die .yml-Datei hat zwei Abschnitte:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Bereitstellen** | Stellen Sie die Artefakte des Arbeitsbereichs bereit. |

### <a name="configure-github-actions-secrets"></a>Konfigurieren von GitHub Actions-Geheimnissen

GitHub Actions Secrets sind Umgebungsvariablen, die verschlüsselt sind. Jeder, der über Collaborator-Berechtigungen für dieses Repository verfügt, kann diese Geheimnisse verwenden, um mit Aktionen im Repository zu interagieren.

1. Wählen Sie im GitHub-Repository die Registerkarte **Einstellungen** und dann die Option **Geheimnisse** > **Neues Repository-Geheimnis**.

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="Screenshot, der die GitHub-Elemente zeigt, die zur Erstellung eines neuen Repository-Geheimnisses auszuwählen sind.":::

1. Fügen Sie ein neues Geheimnis für die Client-ID hinzu, und fügen Sie ein neues Client-Geheimnis hinzu, wenn Sie den Dienstprinzipal für die Bereitstellung verwenden. Sie können auch wählen, ob Sie die Abonnement-ID und die Mandanten-ID als Geheimnisse speichern möchten. 

### <a name="add-your-workflow"></a>Hinzufügen des Workflows

Gehen Sie in Ihrem GitHub-Repository zu **Aktionen**. 

1. Klicken Sie auf **Set up your workflow yourself** (Workflow selbst einrichten).
1. Löschen Sie in der Workflow-Datei alles nach dem Abschnitt `on:`. Ihr verbleibender Arbeitsablauf könnte zum Beispiel so aussehen: 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Benennen Sie Ihren Arbeitsablauf um. Suchen Sie auf der Registerkarte **Marktplatz** nach der Aktion zur Bereitstellung des Synapse-Arbeitsbereichs und fügen Sie dann die Aktion hinzu. 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="Screenshot, der die Suche nach der Aufgabe zur Bereitstellung des Synapse-Arbeitsbereichs auf der Registerkarte Marketplace zeigt.":::

1. Legen Sie die erforderlichen Werte und die Arbeitsbereichsvorlage fest:

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. Jetzt können Sie Ihre Änderungen festschreiben. Wählen Sie **Übertragung starten**, geben Sie den Titel ein und fügen Sie eine Beschreibung hinzu (optional). Wählen Sie dann **Neue Datei übertragen**.

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="Screenshot, der die Übergabe des Workflows in GitHub zeigt.":::

   Die Datei erscheint im Ordner *.github/workflows* in Ihrem Repository.

   > [!NOTE]
   > Managed Identity wird nur mit selbst gehosteten VMs in Azure unterstützt. Stellen Sie sicher, dass der Runner auf "self-hosted" eingestellt ist. Aktivieren Sie die vom System zugewiesene verwaltete Identität für Ihre VM und fügen Sie diese zu Azure Synapse Studio als Synapse-Admin hinzu.

### <a name="review-your-deployment"></a>Überprüfen der Bereitstellung

1. Gehen Sie in Ihrem GitHub-Repository zu **Aktionen**.
1. Um detaillierte Protokolle der Ausführung Ihres Workflows zu sehen, öffnen Sie das erste Ergebnis:

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="Screenshot, der die Auswahl des Protokolls für die Bereitstellung des Arbeitsbereichs im Repository Actions in GitHub zeigt.":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>Erstellen von benutzerdefinierten Parametern in der Arbeitsbereichsvorlage 

Wenn Sie automatisiertes CI/CD verwenden und einige Eigenschaften während der Bereitstellung ändern möchten, diese aber nicht standardmäßig parametrisiert sind, können Sie die Standardparametervorlage überschreiben.

Um die Standard-Parametervorlage außer Kraft zu setzen, erstellen Sie eine benutzerdefinierte Parametervorlage mit dem Namen *template-parameters-definition.json* im Stammordner Ihres Git-Kollaborationszweigs. Sie müssen genau diesen Dateinamen verwenden. Wenn Azure Synapse Arbeitsbereich aus dem Collaboration-Zweig veröffentlicht, liest er diese Datei und verwendet ihre Konfiguration, um die Parameter zu generieren. Wenn Azure Synapse Arbeitsbereich diese Datei nicht findet, wird die Standardparametervorlage verwendet.

### <a name="custom-parameter-syntax"></a>Benutzerdefinierte Parametersyntax

Sie können die folgenden Richtlinien verwenden, um eine benutzerdefinierte Parameterdatei zu erstellen:

* Geben Sie den Eigenschaftenpfad unter dem relevanten Entitätstyp ein.
* Wenn Sie einen Eigenschaftsnamen auf `*` setzen, bedeutet dies, dass Sie alle Eigenschaften unterhalb der Eigenschaft parametrisieren möchten (nur bis zur ersten Ebene, nicht rekursiv). Sie können Ausnahmen von dieser Konfiguration festlegen.
* Wenn Sie den Wert einer Eigenschaft als Zeichenfolge festlegen, geben Sie damit an, dass die Eigenschaft parametrisiert werden soll. Verwenden Sie das Format `<action>:<name>:<stype>`.
   *  `<action>` kann für eines dieser Zeichen durchgeführt werden:
      * `=` bedeutet, dass der aktuelle Wert als Standardwert für den Parameter beibehalten werden soll.
      * `-` bedeutet, dass der Standardwert für den Parameter nicht beibehalten werden soll.
      * `|` ist ein Sonderfall für Geheimnisse aus Azure Key Vault für Verbindungszeichenfolgen oder Schlüssel.
   * `<name>` ist der Name des Parameters. Wenn dieser Wert leer ist, wird der Name der Eigenschaft verwendet. Beginnt der Wert mit dem Zeichen `-`, wird der Name gekürzt. `AzureStorage1_properties_typeProperties_connectionString` wird beispielsweise in `AzureStorage1_connectionString` gekürzt.
   * `<stype>` ist der Typ des Parameters. Wenn `<stype>` leer ist, wird standardmäßig der Typ `string` verwendet. Unterstützte Werte: `string`, `securestring`, `int`, `bool`, `object`, `secureobject` und `array`.
* Wenn Sie ein Array in der Datei angeben, bedeutet dies, dass die entsprechende Eigenschaft in der Vorlage ein Array ist. Azure Synapse iteriert durch alle Objekte im Array unter Verwendung der angegebenen Definition. Das zweite Objekt (eine Zeichenfolge) wird zum Namen der Eigenschaft, der bei jeder Iteration als Name für den Parameter verwendet wird.
* Eine Definition kann nicht spezifisch für eine Ressourceninstanz sein. Jede Definition gilt für alle Ressourcen dieses Typs.
* Standardmäßig sind alle sicheren Zeichenfolgen (z. B. Key Vault-Geheimnisse) und sicheren Zeichenfolgen (z. B. Verbindungszeichenfolgen, Schlüssel und Token) parametrisiert.

### <a name="parameter-template-definition-example"></a>Beispiel für die Definition einer Parametervorlage 

Im Folgenden finden Sie ein Beispiel für eine Parametervorlagendefinition:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Im Folgenden wird erläutert, wie die vorangehende Vorlage nach Ressourcentyp aufgebaut ist.

**`notebooks`**

- Jede Eigenschaft im `properties/bigDataPool/referenceName`-Pfad wird mit ihrem Standardwert parametrisiert. Sie können einen angehängten Spark-Pool für jede Notebook-Datei parametrisieren. 

**`sqlscripts`**

- Im `properties/content/currentConnection`-Pfad werden sowohl die `poolName`- als auch die `databaseName`-Eigenschaften als Strings ohne die Standardwerte in der Vorlage parametrisiert. 

**`pipelines`**

- Jede Eigenschaft im `activities/typeProperties/waitTimeInSeconds`-Pfad ist parametrisiert. Jede Aktivität in einer Pipeline, die eine Eigenschaft auf Codeebene mit dem Namen `waitTimeInSeconds` enthält (z. B. die Aktivität `Wait`), wird als Zahl mit einem Standardnamen parametrisiert. Die Eigenschaft hat keinen Standardwert in der Ressourcenmanager-Vorlage. Stattdessen wird die Eigenschaft während der Bereitstellung des Ressourcenmanagers als Eingabe benötigt.
- Die `headers`-Eigenschaft (zum Beispiel in einer `Web`-Aktivität) wird mit dem `object`-Typ (Objekt) parametrisiert. Die `headers`-Eigenschaft hat einen Standardwert, der derselbe ist wie der Wert der Quellfabrik.

**`integrationRuntimes`**

- Alle Eigenschaften im `typeProperties`-Pfad sind mit ihren jeweiligen Standardwerten parametrisiert. Zum Beispiel befinden sich zwei Eigenschaften unter `IntegrationRuntimes` type properties: `computeProperties` und `ssisProperties`. Beide Eigenschaftentypen werden mit ihren jeweiligen Standardwerten und -typen (Objekt) erstellt.

**`triggers`**

- Unter `typeProperties` sind zwei Eigenschaften parametrisiert:
  - Die Eigenschaft `maxConcurrency` hat einen Standardwert und ist vom Typ `string`. Der Standardparametername der `maxConcurrency`-Eigenschaft ist `<entityName>_properties_typeProperties_maxConcurrency`.
  - Die Eigenschaft `recurrence` wird ebenfalls parametrisiert. Alle Eigenschaften unter der Eigenschaft `recurrence` werden als Strings parametrisiert, mit Standardwerten und Parameternamen. Eine Ausnahme ist die Eigenschaft `interval`, die als Typ `int` parametrisiert ist. An den Parameternamen ist das Suffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` angehängt. Analog dazu ist die Eigenschaft `freq` eine Zeichenfolge und wird als Zeichenfolge parametrisiert. Die Eigenschaft `freq` wird jedoch ohne Standardwert parametrisiert. Der Name wird abgekürzt und mit einem Suffix versehen, z. B. `<entityName>_freq`.

**`linkedServices`**

- Verknüpfte Dienste sind ein Sonderfall. Da verknüpfte Dienste und Datasets eine breite Palette von Typen umfassen, können Sie eine typspezifische Anpassung vornehmen. Im vorangegangenen Beispiel wird für alle verknüpften Dienste des Typs `AzureDataLakeStore` eine bestimmte Vorlage angewendet. Für alle anderen (identifiziert durch die Verwendung des Zeichens `*`) wird eine andere Vorlage angewandt.
- Die Eigenschaft `connectionString` ist als `securestring`-Wert parametrisiert. Sie hat keinen Standardwert. Der Parametername wird verkürzt und mit dem Suffix `connectionString` versehen.
- Die `secretAccessKey`-Eigenschaft wird als `AzureKeyVaultSecret`-Wert parametrisiert (z. B. in einem mit Amazon S3 verknüpften Dienst). Die Eigenschaft wird automatisch als Azure Key Vault-Geheimnis parametrisiert und aus dem konfigurierten Key Vault abgerufen. Sie können auch den Schlüsseltresor selbst parametrisieren.

**`datasets`**

- Obwohl Sie Typen in Datasets anpassen können, ist eine explizite Konfiguration auf \*-Ebene nicht erforderlich. Im vorherigen Beispiel werden alle Dataseteigenschaften unter `typeProperties` parametrisiert.

## <a name="best-practices-for-cicd"></a>Bewährte Methoden für CI/CD

Wenn Sie die Git-Integration mit Ihrem Azure Synapse-Arbeitsbereich verwenden und über eine CI/CD-Pipeline verfügen, die Ihre Änderungen von der Entwicklung zum Test und dann zur Produktion weiterleitet, empfehlen wir die folgenden Best Practices:

-   **Integrieren Sie nur den Entwicklungsarbeitsbereich mit Git**. Wenn Sie die Git-Integration verwenden, integrieren Sie nur Ihren *Entwicklungs* Azure Synapse-Arbeitsbereich in Git. Änderungen an den Test- und Produktionsarbeitsbereichen werden über CI/CD bereitgestellt, und eine Git-Integration wird hierfür nicht benötigt.
-   **Bereiten Sie Pools vor, bevor Sie Artefakte migrieren**. Wenn Sie ein SQL-Skript oder ein Notizbuch haben, das mit Pools im Entwicklungsarbeitsbereich verbunden ist, verwenden Sie denselben Namen für Pools in verschiedenen Umgebungen. 
-   **Synchronisieren Sie die Versionierung in Infrastruktur-als-Code-Szenarien**. Um die Infrastruktur (Netzwerke, virtuelle Maschinen, Load Balancer und Verbindungstopologie) in einem beschreibenden Modell zu verwalten, verwenden Sie die gleiche Versionierung, die das DevOps-Team für den Quellcode verwendet. 
-   **Prüfen Sie die Best Practices für Azure Data Factory**. Wenn Sie Data Factory verwenden, lesen Sie die [Best Practices für Data Factory Artefakte](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd).

## <a name="troubleshoot-artifacts-deployment"></a>Fehlerbehebung bei der Bereitstellung von Artefakten 

### <a name="use-the-synapse-workspace-deployment-task"></a>Verwenden der Aufgabe zur Synapse-Arbeitsbereichsbereitstellung

In Azure Synapse sind einige Artefakte, anders als in Data Factory, keine Ressourcenmanager-Ressourcen. Sie können die ARM-Vorlagenbereitungsaufgabe nicht zur Bereitstellung von Azure Synapse-Artefakten verwenden. Verwenden Sie stattdessen die Synapse-Workspace-Bereitstellungsaufgabe.
 
### <a name="unexpected-token-error-in-release"></a>Unerwarteter Tokenfehler in Release

Wenn Ihre Parameterdatei Parameterwerte enthält, die nicht escaped sind, kann die Freigabe-Pipeline die Datei nicht parsen und erzeugt einen `unexpected token`-Fehler. Wir schlagen vor, dass Sie Parameter überschreiben oder Key Vault verwenden, um Parameterwerte abzurufen. Sie können auch doppelte Escape-Zeichen verwenden, um das Problem zu beheben.

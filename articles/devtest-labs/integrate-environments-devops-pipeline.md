---
title: Integrieren von Umgebungen in Azure Pipelines
description: Hier erfahren Sie, wie Sie Azure DevTest Labs-Umgebungen in Ihre CI- und CD-Pipelines (Continuous Integration und Continuous Delivery) von Azure DevOps integrieren.
ms.topic: how-to
ms.date: 11/15/2021
ms.openlocfilehash: b3478242dddd7141100f05ae44a09902a2546ffd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488099"
---
# <a name="integrate-environments-into-your-cicd-pipelines"></a>Integrieren von Umgebungen in Ihre CI/CD-Pipelines 
In diesem Artikel erfahren Sie, wie Sie im Rahmen einer vollständigen Pipeline eine Umgebung erstellen und bereitstellen und die Umgebung anschließend wieder löschen. Sie können diese Umgebung verwenden, um Ihre CI/CD-Releasepipelines (Continuous Integration/Continuous Delivery) in Azure DevTest Labs zu integrieren. Sie verwenden die Erweiterung Azure DevTest Labs Tasks in Azure DevOps Services für die Integration. Diese Erweiterungen vereinfachen beispielsweise die schnelle Bereitstellung einer [Umgebung](devtest-lab-test-env.md) für eine bestimmte Testaufgabe und die anschließende Löschung der Umgebung nach Abschluss des Tests. 

In Ihrer eigenen benutzerdefinierten Build-, Test- und Bereitstellungspipeline müssten diese Aufgaben üblicherweise einzeln ausgeführt werden. Die Erweiterungen in diesem Artikel werden zusätzlich zu den folgenden [Aufgaben zum Erstellen/Löschen eines virtuellen DTL-Computers](devtest-lab-integrate-ci-cd.md) verwendet:

- Erstellen einer Umgebung
- Löschen einer Umgebung

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie Ihre CI/CD-Pipeline in Azure DevTest Labs integrieren können, müssen Sie die folgenden Aufgaben ausführen: 
1. Installieren Sie die Erweiterung [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) über Visual Studio Marketplace. 
1. [Erstellen Sie ein Lab](devtest-lab-create-lab.md), und stellen Sie sicher, dass es für die Verwendung der **öffentlichen Umgebung** konfiguriert ist, die standardmäßig aktiviert wird.

## <a name="create-a-release-definition--environment"></a>Erstellen einer Releasedefinition und einer Umgebung
Führen Sie die folgenden Schritte aus, um die Releasedefinition zu erstellen:

1. Wählen Sie in Ihrem Azure DevOps-Projekt im Abschnitt **Pipelines** die Option **Releases** aus.
1. Wählen Sie auf der Registerkarte **Releases** die Option **Neue Pipeline** aus.  Im Fenster **Vorlage auswählen** finden Sie auf der rechten Seite die Liste der empfohlenen Vorlagen für allgemeine Bereitstellungsmuster. 
1. Wählen Sie für diese Pipeline **Leerer Auftrag** aus, um mit dem Erstellen der Umgebung zu beginnen, die für Entwicklungs- oder Testzwecke verwendet werden soll.
1. Wählen Sie im leeren Auftrag **Aufgaben** in der Symbolleiste und dann **Phase 1** aus.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="Screenshot: Öffnen der Phase „Pipelinefreigabe“.":::

1. Um der Phase Aufgaben hinzuzufügen, wählen Sie das Pluszeichen (+) im Abschnitt **Agent-Auftrag** aus. Eine durchsuchbare Liste der verfügbaren Aufgaben wird angezeigt. 
1. Suchen Sie im Fenster **Aufgabe hinzufügen** nach `Azure DevTest Labs Create Environment`.
1. Wählen Sie die Aufgabe `Azure DevTest Labs Create Environment` in den Ergebnissen und dann **Hinzufügen** aus.
1. Wählen Sie die Aufgabe aus, die Sie gerade erstellt haben. Das Fenster **Azure DevTest Labs: Umgebung erstellen (Vorschau)** wird angezeigt.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="Screenshot der Felder, die für die Azure Pipelines-Umgebung für Azure DevTest Labs erforderlich sind.":::

2. Konfigurieren Sie die Umgebung auf der Registerkarte **Aufgaben** wie folgt:

   |Feld|Beschreibung|
   |-----|-----------|
   |**Azure RM-Abonnement**|Dies ist das Azure Resource Manager-Abonnement, das vor der Ausführung konfiguriert wird. Wählen Sie in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints).|
   |**Labname**|Wählen Sie den Namen eines Labs aus, das Sie zuvor erstellt haben (siehe Voraussetzungen). In der Praxis sollten Sie das Lab auswählen, für das die Bereitstellung erfolgen soll. Die Ressource wird in diesem Lab erstellt.  Sie können auch die Variablen (`$(labName)`) verwenden.  Wählen Sie diese Angaben mithilfe der Dropdownlisten aus. Bei manueller Eingabe des Anzeigenamens tritt ein Fehler auf.|
   |**Umgebungsname**|Der Name der Umgebung, die im ausgewählten Lab erstellt werden soll.|
   |**Repositoryname**|Der Name des Quellcoderepositorys, das die Vorlage enthält. Sie können das Standardrepository (`Public Environment Repo`) oder ein anderes Repository auswählen, das die Vorlage enthält, die Sie verwenden möchten. Repositorys werden in den Labrichtlinien entworfen.  Wählen Sie diese Angaben mithilfe der Dropdownlisten aus. Bei manueller Eingabe des Anzeigenamens tritt ein Fehler auf.|
   |**Vorlagenname**|Der Name der Vorlage, die zum Erstellen der Umgebung verwendet werden soll. Wählen Sie den Namen der Umgebungsvorlage aus. Wählen Sie diese Angaben mithilfe der Dropdownlisten aus. Bei manueller Eingabe des Anzeigenamens tritt ein Fehler auf.| 
   |**Umgebungsname**|Geben einen Namen ein, der die Umgebungsinstanz innerhalb des Labs eindeutig identifiziert.  Dieser Wert muss innerhalb des Labs eindeutig sein.|
   |**Parameterdatei** & **Parameteraußerkraftsetzungen**|Verwenden Sie diese Option, um benutzerdefinierte Parameter an die Umgebung zu übergeben. Die Parameterwerte können mithilfe einer der beiden Optionen oder mithilfe beider Optionen festgelegt werden. Beispielsweise können Sie diese Felder verwenden, um das verschlüsselte Kennwort zu übergeben. Sie können Variablen auch verwenden, um das Übergeben geheimer Informationen in die Protokolle zu vermeiden und sogar eine Verknüpfung mit Azure Key Vault erstellen.|

## <a name="delete-the-environment"></a>Löschen der Umgebung
In der letzten Phase wird die Umgebung, die Sie in Ihrer Azure DevTest Labs-Instanz bereitgestellt haben, wieder gelöscht. Eine Umgebung wird in der Regel gelöscht, nachdem Sie die erforderlichen Entwicklungsaufgaben oder Tests auf den bereitgestellten Ressourcen ausgeführt haben.

Wählen Sie in der Releasedefinition **Aufgaben hinzufügen** aus, und fügen Sie dann auf der Registerkarte **Bereitstellen** eine **Azure DevTest Labs-Aufgabe** um Löschen einer Umgebung hinzu. Konfigurieren Sie ihn wie folgt:

1. Informationen zum Löschen des virtuellen Computers finden Sie unter [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints).
    2. Wählen Sie unter **Labname** das Lab aus, in dem sich die Umgebung befindet.
    3. Geben Sie unter **Umgebungsname** den Namen der zu entfernenden Umgebung ein.
2. Geben Sie einen Namen für die Releasedefinition ein, und speichern Sie sie.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln: 
- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- Resource Manager-Schnellstartvorlagen für die DevTest Labs-Automatisierung aus dem [GitHub-Repository für DevTest Labs](https://github.com/Azure/azure-quickstart-templates)
- [VSTS Troubleshooting page (Seite zur Problembehandlung bei VSTS)](/azure/devops/pipelines/troubleshooting)

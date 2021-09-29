---
title: Automatisiertes Veröffentlichen für Continuous Integration und Delivery
description: Erfahren Sie, wie Sie automatisch für Continuous Integration und Delivery veröffentlichen.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 08/23/2021
ms.openlocfilehash: 921db5e082748ce814a19c96c5de4ac3061750e2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219489"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatisiertes Veröffentlichen für Continuous Integration und Delivery

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Übersicht

Bei Continuous Integration wird jede Änderung, die an Ihrer Codebasis vorgenommen wird, automatisch getestet. Der Continuous Delivery-Prozess folgt so früh wie möglich auf das Testen während des Continuous Integration-Prozesses. Änderungen werden dabei in ein Staging- oder Produktionssystem gepusht.

In Azure Data Factory bedeuten Continuous Integration und Continuous Delivery (CI/CD), dass Data Factory-Pipelines von einer Umgebung wie Entwicklung, Test oder Produktion in eine andere verschoben werden. Data Factory verwendet [ARM-Vorlagen (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) zum Speichern der Konfiguration Ihrer verschiedenen Data Factory-Entitäten wie Pipelines, Datasets und Datenflüsse.

Es gibt zwei empfohlene Methoden zum Höherstufen einer Data Factory in eine andere Umgebung:

- Automatisierte Bereitstellung über die Integration von Data Factory in [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Manuelles Hochladen einer ARM-Vorlage über die Data Factory-Benutzeroberflächenintegration in Azure Resource Manager.

Weitere Informationen finden Sie unter [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-delivery.md).

In diesem Artikel liegt der Fokus auf den Verbesserungen bei Continuous Deployment und auf dem Feature für die automatisierte Veröffentlichung für CI/CD.

## <a name="continuous-deployment-improvements"></a>Verbesserungen bei Continuous Deployment

Im Feature „Automatisierte Veröffentlichung“ sind die Funktionen **Alle überprüfen** und **ARM-Vorlage exportieren** von der Data Factory-Benutzeroberfläche zusammengefasst. Die Logik wird über das öffentlich verfügbare npm-Paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) bereitgestellt. Daher können Sie diese Aktionen programmgesteuert auslösen, anstatt zur Data Factory-Benutzeroberfläche zu wechseln und manuell eine Schaltfläche auszuwählen. Mithilfe dieser Möglichkeit werden Ihre CI/CD-Pipelines zu echten Continuous Integration-Prozessen.

### <a name="current-cicd-flow"></a>Aktueller CI/CD-Flow

1. Alle Benutzer nehmen Änderungen in ihren privaten Branches vor.
1. Push an Master ist nicht zulässig. Benutzer müssen einen Pull Request erstellen, um Änderungen vorzunehmen.
1. Benutzer müssen die Data Factory-Benutzeroberfläche laden und **Veröffentlichen** auswählen, um Änderungen an Data Factory bereitzustellen und die ARM-Vorlagen im Veröffentlichungsbranch zu generieren.
1. Die DevOps-Releasepipeline ist so konfiguriert, dass sie jedes Mal, wenn eine neue Änderung in den Veröffentlichungsbranch gepusht wird, ein neues Release erstellt und die ARM-Vorlage bereitstellt.

:::image type="content" source="media/continuous-integration-delivery-improvements/current-ci-cd-flow.png" alt-text="Diagramm mit dem aktuellen CI/CD-Flow":::

### <a name="manual-step"></a>Manueller Schritt

Im aktuellen CI/CD-Flow ist die Benutzeroberfläche der Vermittler zum Erstellen der ARM-Vorlage. Folglich muss ein Benutzer die Data Factory-Benutzeroberfläche aufrufen und manuell **Veröffentlichen** auswählen, um die Generierung der ARM-Vorlage zu starten und sie in den Veröffentlichungsbranch einzufügen.

### <a name="the-new-cicd-flow"></a>Der neue CI/CD-Flow

1. Alle Benutzer nehmen Änderungen in ihren privaten Branches vor.
1. Push an Master ist nicht zulässig. Benutzer müssen einen Pull Request erstellen, um Änderungen vorzunehmen.
1. Der Buildvorgang der Azure DevOps-Pipeline wird bei jedem neuen Commit an den Masterbranch ausgeführt. Die Ressourcen werden überprüft, und es wird eine ARM-Vorlage als Artefakt generiert, wenn die Überprüfung erfolgreich ist.
1. Die DevOps-Releasepipeline ist so konfiguriert, dass sie jedes Mal ein neues Release erstellt und die ARM-Vorlage bereitstellt, wenn ein neuer Build verfügbar ist.

:::image type="content" source="media/continuous-integration-delivery-improvements/new-ci-cd-flow.png" alt-text="Diagramm mit dem neuen CI/CD-Flow":::

### <a name="what-changed"></a>Was hat sich geändert?

- Der neue Buildprozess verwendet eine DevOps-Buildpipeline.
- Die Buildpipeline verwendet das npm-Paket „ADFUtilities“, das alle Ressourcen überprüft und die ARM-Vorlagen generiert. Dabei kann es sich um einzelne oder verknüpfte Vorlagen handeln.
- Die Buildpipeline ist anstelle der Data Factory-Benutzeroberfläche (Schaltfläche **Veröffentlichen**) für das Überprüfen von Data Factory-Ressourcen und das Erstellen der ARM-Vorlage verantwortlich.
- Die DevOps-Releasedefinition nutzt nun diese neue Buildpipeline anstelle des Git-Artefakts.

> [!NOTE]
> Sie können weiterhin den vorhandenen Mechanismus, d. h. den `adf_publish`-Branch, oder den neuen Flow verwenden. Beide Verfahren werden unterstützt.

## <a name="package-overview"></a>Paketübersicht

Im Paket sind zurzeit zwei Befehle verfügbar:

- Exportieren einer ARM-Vorlage
- Überprüfen

### <a name="export-arm-template"></a>Exportieren einer ARM-Vorlage

Führen Sie `npm run build export <rootFolder> <factoryId> [outputFolder]` aus, um die ARM-Vorlage unter Verwendung der Ressourcen eines bestimmten Ordners zu exportieren. Mit diesem Befehl wird vor dem Generieren der ARM-Vorlage auch eine Überprüfung ausgeführt. Ein Beispiel:

```dos
npm run build export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` ist ein obligatorisches Feld, das den Speicherort der Data Factory-Ressourcen darstellt.
- `FactoryId` ist ein obligatorisches Feld, das die Data Factory-Ressourcen-ID im Format `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` darstellt.
- `OutputFolder` ist ein optionaler Parameter, der den relativen Pfad zum Speichern der generierten ARM-Vorlage angibt.

> [!NOTE]
> Die generierte ARM-Vorlage wird nicht in der Liveversion der Factory veröffentlicht. Die Bereitstellung sollte mithilfe einer CI/CD-Pipeline erfolgen.

### <a name="validate"></a>Überprüfen

Führen Sie `npm run build validate <rootFolder> <factoryId>` aus, um alle Ressourcen eines bestimmten Ordners zu überprüfen. Ein Beispiel:

```dos
npm run build validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` ist ein obligatorisches Feld, das den Speicherort der Data Factory-Ressourcen darstellt.
- `FactoryId` ist ein obligatorisches Feld, das die Data Factory-Ressourcen-ID im Format `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` darstellt.

## <a name="create-an-azure-pipeline"></a>Erstellen einer Azure-Pipeline

npm-Pakete können zwar auf verschiedene Weise genutzt werden, aber einer der Hauptvorteile ist die Nutzung über [Azure Pipelines](/azure/devops/pipelines/get-started/). Bei jedem Merge in den Kollaborationsbranch kann eine Pipeline ausgelöst werden, die zuerst den gesamten Code überprüft und dann die ARM-Vorlage in ein [Buildartefakt](/azure/devops/pipelines/artifacts/build-artifacts) exportiert, das von einer Releasepipeline verwendet werden kann. Der Unterschied zum aktuellen CI/CD-Prozess besteht darin, dass Sie *in der Releasepipeline nicht auf den vorhandenen `adf_publish`-Branch, sondern auf dieses Artefakt verweisen*.

Gehen Sie dazu wie folgt vor:

1. Öffnen Sie ein Azure DevOps-Projekt, und navigieren Sie zu **Pipelines**. Wählen Sie **Neue Pipeline** aus.

   :::image type="content" source="media/continuous-integration-delivery-improvements/new-pipeline.png" alt-text="Screenshot der Schaltfläche „Neue Pipeline“":::

2. Wählen Sie das Repository aus, in dem Sie das YAML-Skript Ihrer Pipeline speichern möchten. Es wird empfohlen, die Datei im Ordner „build“ im gleichen Repository wie Ihre Data Factory-Ressourcen zu speichern. Stellen Sie sicher, dass im Repository die Datei *package.json* vorhanden ist, die den Paketnamen enthält, wie im folgenden Beispiel gezeigt:

   ```json
   {
       "scripts":{
           "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
       },
       "dependencies":{
           "@microsoft/azure-data-factory-utilities":"^0.1.5"
       }
   } 
   ```

3. Wählen Sie **Starterpipeline** aus. Wenn Sie die YAML-Datei wie im Beispiel unten hochgeladen oder zusammengeführt haben, können Sie auch direkt darauf verweisen und sie bearbeiten.

   :::image type="content" source="media/continuous-integration-delivery-improvements/starter-pipeline.png" alt-text="Screenshot der Starterpipeline":::

   ```yaml
   # Sample YAML file to validate and export an ARM template into a build artifact
   # Requires a package.json file located in the target repository
   
   trigger:
   - main #collaboration branch
   
   pool:
     vmImage: 'ubuntu-latest'
   
   steps:
   
   # Installs Node and the npm packages saved in your package.json file in the build
   
   - task: NodeTool@0
     inputs:
       versionSpec: '10.x'
     displayName: 'Install Node.js'
   
   - task: Npm@1
     inputs:
       command: 'install'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       verbose: true
     displayName: 'Install npm package'
   
   # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
   # Enter the appropriate subscription and name for the source factory.
   
   - task: Npm@1
     inputs:
       command: 'custom'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
     displayName: 'Validate'
   
   # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
   # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
   
   - task: Npm@1
     inputs:
       command: 'custom'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
     displayName: 'Validate and Generate ARM template'
   
   # Publish the artifact to be used as a source for a release pipeline.
   
   - task: PublishPipelineArtifact@1
     inputs:
       targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
       artifact: 'ArmTemplates'
       publishLocation: 'pipeline'
   ```

4. Geben Sie den YAML-Code ein. Es wird empfohlen, dass Sie die YAML-Datei als Ausgangspunkt verwenden.

5. Speichern Sie das Projekt, und führen Sie es aus. Wenn Sie die YAML-Datei verwendet haben, wird diese jedes Mal ausgelöst, wenn der Mainbranch aktualisiert wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Continuous Integration und Continuous Delivery in Data Factory: [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-delivery.md).

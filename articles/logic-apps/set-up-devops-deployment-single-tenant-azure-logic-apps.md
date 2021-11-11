---
title: Einrichten von DevOps für Azure Logic Apps-Instanzen mit nur einem Mandanten
description: Vorgehensweise zum Einrichten der DevOps-Bereitstellung für Workflows in Azure Logic Apps-Instanzen mit nur einem Mandanten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: bfc901e3fa9c3a3f266f9cddffde84c4d1461c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425499"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>Einrichten der DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten

In diesem Artikel erfahren Sie, wie Sie mithilfe von DevOps-Tools und -Prozessen ein auf einem einzelnen Mandanten basierendes Logik-App-Projekt aus Visual Studio Code in Ihrer Infrastruktur bereitstellen. Wählen Sie in Abhängigkeit davon, ob Sie GitHub oder Azure DevOps für die Bereitstellung bevorzugen, den Pfad und die Tools aus, die für Ihr Szenario am besten geeignet sind. Sie können die enthaltenen Beispiele verwenden, die Logik-App-Beispielprojekte sowie Beispiele für die Azure-Bereitstellung mithilfe von GitHub oder Azure DevOps enthalten. Weitere Informationen zu DevOps für einzelne Mandanten finden Sie unter [DevOps-Bereitstellungsübersicht für Azure Logic Apps-Instanzen mit nur einem Mandanten](devops-deployment-single-tenant-azure-logic-apps.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ein auf einem einzelnen Mandanten basierendes Logik-App-Projekt, das mit [Visual Studio Code und der Azure Logic Apps-Erweiterung (Standard)](create-single-tenant-workflows-visual-studio-code.md#prerequisites) erstellt wurde.

  Wenn Sie noch kein Logik-App-Projekt oder keine Infrastruktur eingerichtet haben, können Sie die enthaltenen Beispielprojekte verwenden, um eine Beispiel-App und -infrastruktur auf Grundlage der Quell- und Bereitstellungsoptionen bereitzustellen, die Sie verwenden möchten. Weitere Informationen zu diesen Beispielprojekten und den enthaltenen Ressourcen für die Ausführung der Beispiel-Logik-App, finden Sie unter [Bereitstellen Ihrer Infrastruktur](#deploy-infrastructure).

- Wenn Sie eine Bereitstellung in Azure durchführen möchten, benötigen Sie eine vorhandene **Logik-App-Ressource (Standard)** , die in Azure erstellt wurde. Informationen zum schnellen Erstellen einer leeren Logik-App-Ressource finden Sie unter [Erstellen von auf einzelnen Mandanten basierenden Logik-App-Workflows – Portal](create-single-tenant-workflows-azure-portal.md).

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>Bereitstellen von Infrastrukturressourcen

Wenn Sie noch kein Logik-App-Projekt oder keine Infrastruktur eingerichtet haben, können Sie die folgenden Beispielprojekte verwenden, um eine Beispiel-App und -infrastruktur auf Grundlage Ihrer gewünschten Quell- und Bereitstellungsoptionen bereitzustellen:

- [GitHub-Beispiel für Azure Logic Apps-Instanzen mit nur einem Mandanten](https://github.com/Azure/logicapps/tree/master/github-sample)

  Dieses Beispiel enthält ein Logik-App-Beispielprojekt für Azure Logic Apps-Instanzen mit nur einem Mandanten sowie Beispiele für die Azure-Bereitstellung und GitHub Actions.

- [Azure DevOps-Beispiel für Azure Logic Apps-Instanzen mit nur einem Mandanten](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  Dieses Beispiel enthält ein Logik-App-Beispielprojekt für Azure Logic Apps-Instanzen mit nur einem Mandanten sowie Beispiele für die Azure-Bereitstellung und Azure Pipelines.
  
Beide Beispiele enthalten die folgenden Ressourcen, die eine Logik-App für die Ausführung verwendet.

| Ressourcenname | Erforderlich | BESCHREIBUNG |
|---------------|----------|-------------|
| Logik-App (Standard) | Ja | Diese Azure-Ressource enthält die Workflows, die in Azure Logic Apps-Instanzen mit nur einem Mandanten ausgeführt werden. |
| Functions Premium- oder App Service-Hostingplan | Ja | Diese Azure-Ressource gibt die Hostingressourcen an, die zum Ausführen Ihrer Logik-App verwendet werden soll, z. B. Compute, Verarbeitung, Speicher, Netzwerk usw. <p><p>**Wichtig**: In der aktuellen Erfahrung erfordert die **Logik-App-Ressource (Standard)** den [**Workflow Standard**-Hostingplan](logic-apps-pricing.md#standard-pricing), der auf dem Functions Premium-Hostingplan basiert. |
| Azure-Speicherkonto | Ja, für zustandsbehaftete und zustandslose Workflows | Diese Azure-Ressource speichert die Metadaten, die Schlüssel für die Zugriffssteuerung, den Zustand, die Ein- und Ausgaben, den Ausführungsverlauf sowie andere Informationen zu Ihren Workflows. |
| Application Insights | Optional | Diese Azure-Ressource bietet Überwachungsfunktionen für Ihre Workflows. |
| API-Verbindungen | Optional, wenn nicht vorhanden. | Diese Azure-Ressourcen definieren alle verwalteten API-Verbindungen, die Ihre Workflows zum Ausführen verwalteter Connectorvorgänge verwenden, z. B. Office 365, SharePoint usw. <p><p>**Wichtig**: In Ihrem Logik-App-Projekt enthält die Datei **connections.json** Metadaten, Endpunkte und Schlüssel für alle verwalteten API-Verbindungen und Azure-Funktionen, die von Ihren Workflows verwendet werden. Um unterschiedliche Verbindungen und Funktionen in jeder Umgebung zu verwenden, stellen Sie sicher, dass Sie die Datei **connections.json** parametrisieren und die Endpunkte aktualisieren. <p><p>Weitere Informationen finden Sie unter [API-Verbindungsressourcen und Zugriffsrichtlinien](#api-connection-resources). |
| ARM-Vorlage (Azure Resource Manager) | Optional | Diese Azure-Ressource definiert eine Baseline-Infrastrukturbereitstellung, die Sie wiederverwenden oder [exportieren](../azure-resource-manager/templates/template-tutorial-export-template.md) können. Die Vorlage enthält außerdem die erforderlichen Zugriffsrichtlinien, z. B. für die Verwendung verwalteter API-Verbindungen. <p><p>**Wichtig**: Beim Exportieren der ARM-Vorlage sind nicht alle zugehörigen Parameter für alle API-Verbindungsressourcen enthalten, die von Ihren Workflows verwendet werden. Weitere Informationen finden Sie unter [Suchen von API-Verbindungsparametern](#find-api-connection-parameters). |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>API-Verbindungsressourcen und Zugriffsrichtlinien

In Azure Logic Apps-Instanzen mit nur einem Mandanten muss für jede verwaltete oder API-Verbindungsressource in Ihren Workflows eine zugeordnete Zugriffsrichtlinie vorhanden sein. Diese Richtlinie benötigt die Identität Ihrer Logik-App, um die richtigen Berechtigungen für den Zugriff auf die verwaltete Connectorinfrastruktur bereitzustellen. Die enthaltenen Beispielprojekte beinhalten eine ARM-Vorlage, die alle erforderlichen Infrastrukturressourcen enthält, einschließlich dieser Zugriffsrichtlinien.

Das folgende Diagramm zeigt die Abhängigkeiten zwischen Ihrem Logik-App-Projekt und den Infrastrukturressourcen:

![Konzeptionelles Diagramm der Infrastrukturabhängigkeiten für ein Logik-App-Projekt im Azure Logic Apps-Modell mit nur einem Mandanten.](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>Suchen von API-Verbindungsparametern

Wenn Ihre Workflows verwaltete API-Verbindungen verwenden, werden bei Verwendung der Exportvorlagenfunktion nicht alle zugehörigen Parameter eingeschlossen. In einer ARM-Vorlage hat jede [API-Verbindungsressourcendefinition](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions) das folgende allgemeine Format:

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

Um die Werte zu ermitteln, die Sie im `properties`-Objekt zum Abschließen der Verbindungsressourcendefinition verwenden müssen, können Sie die folgende API für einen bestimmten Connector verwenden:

`GET https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2016-06-01`

Suchen Sie in der Antwort nach dem `connectionParameters`-Objekt, das alle Informationen enthält, die Sie zum Abschließen der Ressourcendefinition für diesen spezifischen Connector benötigen. Das folgende Beispiel zeigt eine Beispielressourcendefinition für eine verwaltete SQL-Verbindung:

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

Alternativ können Sie die Netzwerkablaufverfolgung überprüfen, wenn Sie eine Verbindung im Logik-App-Designer erstellen. Suchen Sie den `PUT`-Aufruf an die verwaltete API für den Connector, wie zuvor beschrieben, und überprüfen Sie den Anforderungstext auf alle benötigten Informationen.

## <a name="deploy-logic-app-resources-zip-deploy"></a>Bereitstellen von Logik-App-Ressourcen (ZIP-Bereitstellung)

Nachdem Sie Ihr Logik-App-Projekt in Ihr Quellrepository gepusht haben, können Sie Build- und Releasepipelines einrichten, die Logik-Apps in der Infrastruktur innerhalb oder außerhalb von Azure bereitstellen.

### <a name="build-your-project"></a>Erstellen Ihres Projekts

Um eine Buildpipeline auf Grundlage Ihres Logik-App-Projekttyps einzurichten, führen Sie die entsprechenden, in der folgenden Tabelle aufgelisteten Aktionen aus:

| Projekttyp | Beschreibung und Schritte |
|--------------|-----------------------|
| NuGet-basiert | Die NuGet-basierte Projektstruktur basiert auf .NET Framework. Um diese Projekte zu erstellen, stellen Sie sicher, dass Sie die Buildschritte für .NET Standard einhalten. Weitere Informationen finden Sie in der Dokumentation zu [Erstellen eines NuGet-Pakets mit MSBuild](/nuget/create-packages/creating-a-package-msbuild). |
| Paketbasiert | Das paketbasierte Erweiterungsprojekt ist nicht sprachspezifisch und erfordert keine sprachspezifischen Buildschritte. Sie können eine beliebige Methode verwenden, um Ihre Projektdateien zu zippen. <p><p>**Wichtig**: Stellen Sie sicher, dass ihre ZIP-Datei die tatsächlichen Buildartefakte einschließlich aller Workflowordner, Konfigurationsdateien wie „host.json“, „connections.json“ und alle anderen zugehörigen Dateien enthält. |
|||

### <a name="release-to-azure"></a>Release in Azure

Um eine Releasepipeline einzurichten, die in Azure bereitstellt, wählen Sie die zugehörige Option für GitHub, Azure DevOps oder Azure CLI aus.

> [!NOTE]
> Azure Logic Apps unterstützt derzeit keine Azure-Bereitstellungsslots.

#### <a name="github"></a>[GitHub](#tab/github)

Für GitHub-Bereitstellungen können Sie Ihre Logik-App mithilfe von [GitHub Actions](https://docs.github.com/actions) bereitstellen, z. B. die GitHub-Aktion in Azure Functions. Diese Aktion erfordert, dass Sie die folgenden Informationen übergeben:

- Den Namen der für die Bereitstellung zu verwendenden Logik-App
- Die ZIP-Datei, die Ihre tatsächlichen Buildartefakte einschließlich aller Workflowordner, Konfigurationsdateien wie „host.json“, „connections.json“ und alle anderen zugehörigen Dateien enthält.
- Ihr für die Authentifizierung verwendetes [Veröffentlichungsprofil](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials).

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: 'MyLogicAppName'
   package: 'MyBuildArtifact.zip'
   publish-profile: 'MyLogicAppPublishProfile'
```

Weitere Informationen finden Sie in der Dokumentation zu [Continuous Delivery mithilfe einer GitHub-Aktion](../azure-functions/functions-how-to-github-actions.md).

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Für Azure DevOps-Bereitstellungen können Sie Ihre Logik-App mithilfe der [Azure Function-App-Bereitstellungsaufgabe (app deploy)](/azure/devops/pipelines/tasks/deploy/azure-function-app?view=azure-devops&preserve-view=true) in Azure Pipelines bereitstellen. Diese Aktion erfordert, dass Sie die folgenden Informationen übergeben:

- Den Namen der für die Bereitstellung zu verwendenden Logik-App
- Die ZIP-Datei, die Ihre tatsächlichen Buildartefakte einschließlich aller Workflowordner, Konfigurationsdateien wie „host.json“, „connections.json“ und alle anderen zugehörigen Dateien enthält.
- Ihr für die Authentifizierung verwendetes [Veröffentlichungsprofil](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials).

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: 'MyServiceConnection'
     appType: 'workflowapp'
     appName: 'MyLogicAppName'
     package: 'MyBuildArtifact.zip'
     deploymentMethod: 'zipDeploy'
```

Weitere Informationen finden Sie in der Dokumentation zum [Bereitstellen einer Azure-Funktion mittels Azure Pipelines](/azure/devops/pipelines/targets/azure-functions-windows).

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie andere Bereitstellungstools verwenden, können Sie Ihre auf einem Einzelmandanten basierende Logik-App mithilfe der Azure CLI bereitstellen. Bevor Sie beginnen, benötigen Sie Folgendes:

- Die aktuelle Azure CLI-Erweiterung muss auf Ihrem Computer installiert sein.

  - Wenn Sie nicht über diese Erweiterung verfügen, lesen Sie den [Installationsleitfaden für Ihr Betriebssystem oder Ihre Plattform](/cli/azure/install-azure-cli).

  - Wenn Sie nicht sicher sind, ob Sie über die neueste Version verfügen, führen Sie die [Schritte zum Überprüfen Ihrer Umgebung und CLI-Version](#check-environment-cli-version) aus.

- Die *Vorschau* der Einzelmandantenerweiterung für Azure Logic Apps (Standard) für Azure CLI.

  Wenn Sie nicht über diese Erweiterung verfügen, führen Sie die [Schritte zum Installieren der Erweiterung](#install-logic-apps-cli-extension) aus. Obwohl Azure Logic Apps in der Einzelmandantenversion allgemein verfügbar ist, befindet sich die Azure Logic Apps-Einzelmandantenerweiterung für Azure CLI weiterhin in der Vorschau.

- Eine Azure-Ressourcengruppe für die Bereitstellung Ihrer Logik-App.

  Wenn Sie nicht über diese Ressourcengruppe verfügen, führen Sie die [Schritte zum Erstellen der Ressourcengruppe](#create-resource-group) aus.

- Ein Azure-Speicherkonto, das mit Ihrer Logik-App für die Aufbewahrung von Daten und des Ausführungsverlaufs verwendet werden soll.

  Wenn Sie nicht über dieses Speicherkonto verfügen, führen Sie die [Schritte zum Erstellen eines Speicherkontos](/cli/azure/storage/account#az_storage_account_create) aus.

<a name="check-environment-cli-version"></a>

##### <a name="check-environment-and-cli-version"></a>Überprüfen der Umgebung und CLI-Version

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Vergewissern Sie sich in einem Terminal- oder Befehlsfenster, dass Ihr Abonnement aktiv ist, indem Sie den Befehl [`az login`](/cli/azure/authenticate-azure-cli) ausführen:

   ```azurecli-interactive
   az login
   ```

1. Überprüfen Sie in einem Terminal- oder Befehlsfenster Ihre Version der Azure CLI, indem Sie den Befehl `az` mit dem folgenden erforderlichen Parameter ausführen:

   ```azurecli-interactive
   az --version
   ```

1. Sollten Sie nicht über die neueste Azure CLI-Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

   Weitere Informationen zur neuesten Version finden Sie in den [neuesten Versionshinweisen](/cli/azure/release-notes-azure-cli?tabs=azure-cli).

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Installieren der Azure Logic Apps-Erweiterung (Standard) für Azure CLI

Derzeit ist nur die *Vorschauversion* für diese Erweiterung verfügbar. Wenn Sie diese Erweiterung noch nicht installiert haben, führen Sie den Befehl `az extension add` mit den folgenden erforderlichen Parametern aus:

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

Damit Sie die neueste Version 0.1.2 erhalten, führen Sie die folgenden Befehle aus, um die vorhandene Erweiterung zu entfernen und dann die neueste Version aus der Quelle zu installieren:

```azurecli-interactive
az extension remove --name logicapp
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

> [!NOTE]
> Wenn eine neue Erweiterungsversion verfügbar ist, wird in der aktuellen und höheren Versionen eine Meldung angezeigt. Während sich diese Erweiterung in der Vorschauphase befindet, können Sie den folgenden Befehl verwenden, um ein Upgrade auf die neueste Version durchzuführen, ohne sie manuell entfernen und erneut installieren zu müssen:
>
> `az logicapp upgrade`

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>Ressourcengruppe erstellen

Wenn Sie noch keine Ressourcengruppe für Ihre Logik-App eingerichtet haben, erstellen Sie die Gruppe, indem Sie den Befehl `az group create` ausführen. Wenn Sie nicht bereits ein Standardabonnement für Ihr Azure-Konto festgelegt haben, stellen Sie sicher, dass Sie den Parameter `--subscription` mit Ihrem Abonnementnamen oder -bezeichner verwenden. Andernfalls müssen Sie den Parameter `--subscription` nicht verwenden.

> [!TIP]
> Führen Sie zum Festlegen eines Standardabonnements den folgenden Befehl aus, und ersetzen Sie `MySubscription` durch Ihren Abonnementnamen oder -bezeichner.
>
> `az account set --subscription MySubscription`

Der folgende Befehl erstellt beispielsweise eine Ressourcengruppe mit dem Namen `MyResourceGroupName` unter Verwendung des Azure-Abonnements mit dem Namen `MySubscription` am Speicherort `eastus`:

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

Wenn Ihre Ressourcengruppe erfolgreich erstellt wurde, wird `provisioningState` als `Succeeded` angezeigt:

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

<a name="deploy-logic-app"></a>

##### <a name="deploy-logic-app"></a>Bereitstellen einer Logik-App

Um Ihr gezipptes Artefakt in einer Azure-Ressourcengruppe bereitzustellen, führen Sie den Befehl `az logicapp deployment` mit folgenden erforderlichen Parametern aus:

> [!IMPORTANT]
> Stellen Sie sicher, dass Ihre ZIP-Datei die Artefakte Ihres Projekts auf Stammebene enthält. Zu diesen Artefakten zählen alle Workflowordner, Konfigurationsdateien wie „host.json“ und „connections.json“ sowie alle anderen zugehörigen Dateien. Fügen Sie keine zusätzlichen Ordner hinzu, und platzieren Sie keine Artefakte in Ordnern, die noch nicht in Ihrer Projektstruktur vorhanden sind. Diese Liste zeigt ein Beispiel für die MyBuildArtifacts.zip-Dateistruktur:
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli-interactive
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

---

### <a name="release-to-containers"></a>Release in Containern

Wenn Sie Ihre Logik-App containerisieren, funktioniert die Bereitstellung größtenteils wie jeder andere Container, den Sie bereitstellen und verwalten.

Beispiele für die Implementierung einer End-to-End-Containerbuild- und -bereitstellungspipeline finden Sie unter [CI/CD für Container](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/).

## <a name="next-steps"></a>Nächste Schritte

- [DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten](devops-deployment-single-tenant-azure-logic-apps.md)

Teilen Sie uns bitte Ihre Erfahrungen mit den Azure Logic Apps-Instanzen mit nur einem Mandanten mit.

- Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
- Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/logicappsdevops).

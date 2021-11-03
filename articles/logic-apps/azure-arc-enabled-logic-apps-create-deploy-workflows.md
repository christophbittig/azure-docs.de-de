---
title: Erstellen und Bereitstellen von Workflows mit Logic Apps mit Azure Arc-Unterstützung
description: Erstellen Sie Logik-App-Workflows auf Basis eines einzelnen Mandanten, die überall ausgeführt werden können, wo Kubernetes ausgeführt werden kann, und stellen Sie sie dann bereit.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, reylons, archidda, sopai, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca9458581468ea359ae1ca2f7e034c7459f87fea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082434"
---
# <a name="create-and-deploy-single-tenant-based-logic-app-workflows-with-azure-arc-enabled-logic-apps-preview"></a>Erstellen und Bereitstellen von Logik-App-Workflows auf Basis eines einzelnen Mandanten mit Logic Apps mit Azure Arc-Unterstützung (Vorschau)

> [!NOTE]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Logic Apps mit Azure Arc-Unterstützung und dem Azure-Portal können Sie Logic App-Workflows auf Basis eines einzelnen Mandanten in einer Kubernetes-Infrastruktur erstellen und bereitstellen, die Sie betreiben und verwalten. Ihre Logik-Apps werden an einem *benutzerdefinierten Speicherort* ausgeführt, der einem Kubernetes-Cluster mit Azure Arc-Unterstützung zugeordnet ist, auf dem Sie das Azure App Service-Plattformerweiterungspaket installiert und aktiviert haben.

Dieser Cluster kann z. B. Azure Kubernetes Service, Bare-Metal-Kubernetes oder eine andere Einrichtung sein. Mit dem Erweiterungspaket können Sie Plattformdienste wie Azure Logic Apps, Azure App Service und Azure Functions in Ihrem Kubernetes-Cluster ausführen. 

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Was ist Logic Apps mit Azure Arc-Unterstützung?](azure-arc-enabled-logic-apps-overview.md)
- [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung](../logic-apps/single-tenant-overview-compare.md)
- [Übersicht über Azure Arc](../azure-arc/overview.md)
- [Übersicht über Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md)
- [Benutzerdefinierte Standorte in Kubernetes-Clustern mit Azure Arc-Unterstützung](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [App Service, Funktionen und Logic Apps in Azure Arc (Vorschau)](../app-service/overview-arc-integration.md)
- [Einrichten eines Kubernetes-Clusters mit Azure Arc-Unterstützung zum Ausführen von App Service, Funktionen und Logic Apps (Vorschau)](../app-service/manage-create-arc-environment.md)

## <a name="prerequisites"></a>Voraussetzungen

In diesem Abschnitt werden die allgemeinen Voraussetzungen für alle Ansätze und Tools beschrieben, mit denen Sie Ihre Logik-App-Workflows erstellen und bereitstellen können. Toolspezifische Voraussetzungen werden zusammen mit den entsprechenden Schritten angezeigt.

- Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Eine Kubernetes-Umgebung mit einem Kubernetes-Cluster mit Azure Arc-Unterstützung und einem *benutzerdefinierten Standort*, an dem Sie Azure Logic Apps, Azure App Service und Azure Functions hosten und ausführen können.

  > [!IMPORTANT]
  > Stellen Sie sicher, dass Sie für Ihre Kubernetes-Umgebung, Ihren benutzerdefinierten Speicherort und Ihre Logik-App denselben Ressourcenspeicherort verwenden.

  Wenn Sie die App Service-Bundleerweiterung in Ihrem Kubernetes-Cluster erstellen, können Sie außerdem das [Standardskalierungsverhalten ändern](#change-scaling), das für die Ausführung Ihrer Logik-App-Workflows gilt. Wenn Sie die Erweiterung mithilfe des Azure CLI-Befehls [ **`az k8s-extension create`**](/cli/azure/k8s-extension) erstellen, stellen Sie sicher, dass Sie die Konfigurationseinstellung `keda.enabled=true` einschließen:

  `az k8s-extension create {other-command-options} --configuration-settings "keda.enabled=true"`

  Weitere Informationen finden Sie in der folgenden Dokumentation:

  - [App Service, Funktionen und Logic Apps in Azure Arc (Vorschau)](../app-service/overview-arc-integration.md)
  - [Clustererweiterungen in Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/conceptual-extensions.md)
  - [Einrichten eines Kubernetes-Clusters mit Azure Arc-Unterstützung zum Ausführen von App Service, Funktionen und Logic Apps (Vorschau)](../app-service/manage-create-arc-environment.md)
  - [Ändern des Standardskalierungsverhaltens](#change-scaling)

- Ihre eigene Azure AD-Identität (Azure Active Directory)

  Wenn Ihre Workflows von Azure gehostete Verbindungen wie Office 365 Outlook oder Azure Storage verwenden müssen, muss Ihre Logik-App eine Azure AD-Identität für die Authentifizierung verwenden. Logic Apps mit Azure Arc-Unterstützung können in jeder Infrastruktur ausgeführt werden, erfordern jedoch eine Identität, die über die Berechtigung verfügt, von Azure gehostete Verbindungen zu verwenden. Um diese Identität einzurichten, erstellen Sie eine App-Registrierung in Azure AD, die Ihre Logik-App als erforderliche Identität verwendet.

  > [!NOTE]
  > Die Unterstützung verwalteter Identitäten ist für Logic Apps mit Azure Arc-Unterstützung derzeit nicht verfügbar.

  Führen Sie die folgenden Schritte aus, um eine Azure AD-App-Registrierung (Azure Active Directory) mithilfe der Azure CLI zu erstellen:

  1. Erstellen Sie mit dem [`az ad sp create`](/cli/azure/ad/sp#az_ad_sp_create)-Befehl eine App-Registrierung.

  1. Führen Sie den [`az ad sp show`](/cli/azure/ad/sp#az_ad_sp_show)-Befehl aus, um alle Details zu überprüfen.

  1. Suchen und speichern Sie in der Ausgabe beider Befehle die Werte für Client-ID, Objekt-ID, Mandanten-ID und geheimer Clientschlüssel, die Sie zur späteren Verwendung beibehalten müssen.

  Führen Sie die folgenden Schritte aus, um eine Azure AD-App-Registrierung (Azure Active Directory) mithilfe des Azure-Portals zu erstellen:

  1. Erstellen Sie mithilfe des [Azure-Portals](../active-directory/develop/quickstart-register-app.md) eine neue Azure AD-App-Registrierung.

  1. Suchen Sie nach Abschluss der Erstellung die neue App-Registrierung im Portal.

  1. Wählen Sie im Registrierungsmenü **Übersicht** aus, und speichern Sie die Werte für Client-ID, Mandanten-ID und geheimer Clientschlüssel.

  1. Um die Objekt-ID zu ermitteln, wählen Sie neben dem Feld **Verwaltete Anwendung im lokalen Verzeichnis** den Namen für Ihre App-Registrierung aus. Kopieren Sie in der Eigenschaftenansicht die Objekt-ID.

## <a name="create-and-deploy-logic-apps"></a>Erstellen und Bereitstellen von Logik-Apps

Wählen Sie abhängig davon, ob Sie Azure CLI, Visual Studio Code oder das Azure-Portal verwenden möchten, die entsprechende Registerkarte aus, um die spezifischen Voraussetzungen und Schritte zu überprüfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bevor Sie beginnen, benötigen Sie Folgendes:

- Die aktuelle Azure CLI-Erweiterung muss auf Ihrem Computer installiert sein.

  - Wenn Sie nicht über diese Erweiterung verfügen, lesen Sie den [Installationsleitfaden für Ihr Betriebssystem oder Ihre Plattform](/cli/azure/install-azure-cli).

  - Wenn Sie nicht sicher sind, ob Sie über die neueste Version verfügen, führen Sie die [Schritte zum Überprüfen Ihrer Umgebung und CLI-Version](#check-environment-cli-version) aus.

- Die Azure Logic Apps-Erweiterung (Standard) für die Azure CLI (*Vorschauversion*).

  Azure Logic Apps in der Einzelmandantenversion ist allgemein verfügbar, die Azure Logic Apps-Erweiterung befindet sich allerdings weiterhin in der Vorschau.

- Eine [Azure-Ressourcengruppe](#create-resource-group) für die Erstellung Ihrer Logik-App.

  Wenn Sie nicht über eine solche Ressourcengruppe verfügen, führen Sie die [Schritte zum Erstellen der Ressourcengruppe](#create-resource-group) aus.

- Ein Azure-Speicherkonto, das mit Ihrer Logik-App für die Aufbewahrung von Daten und des Ausführungsverlaufs verwendet werden soll.

  Wenn Sie nicht über ein solches Speicherkonto verfügen, können Sie während der Erstellung der Logik-App eines erstellen. Alternativ dazu können Sie die [Schritte zum Erstellen eines Speicherkontos](/cli/azure/storage/account#az_storage_account_create) ausführen.

<a name="check-environment-cli-version"></a>

#### <a name="check-environment-and-cli-version"></a>Überprüfen der Umgebung und CLI-Version

1. Melden Sie sich beim Azure-Portal an. Überprüfen Sie, ob Ihr Abonnement aktiv ist, indem Sie den folgenden Befehl ausführen:

   ```azurecli-interactive
   az login
   ```

1. Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle in einem Terminal oder Befehlsfenster, indem Sie den folgenden Befehl ausführen:

   ```azurecli-interactive
   az --version
   ```

   Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli).

1. Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Installieren der Azure Logic Apps-Erweiterung (Standard) für die Azure CLI

Installieren Sie die *Vorschau* der Einzelmandantenerweiterung für Azure Logic Apps (Standard) für die Azure CLI, indem Sie den folgenden Befehl ausführen:

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>Ressourcengruppe erstellen

Sollten Sie noch nicht über eine Ressourcengruppe für Ihre Logik-App verfügen, erstellen Sie die Gruppe mithilfe des Befehls `az group create`. Wenn Sie nicht bereits ein Standardabonnement für Ihr Azure-Konto festgelegt haben, stellen Sie sicher, dass Sie den Parameter `--subscription` mit Ihrem Abonnementnamen oder -bezeichner verwenden. Andernfalls müssen Sie den Parameter `--subscription` nicht verwenden.

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

#### <a name="create-logic-app"></a>Erstellen einer Logik-App

Führen Sie zum Erstellen einer Logik-App mit Azure Arc-Unterstützung den Befehl `az logicapp create` mit den folgenden erforderlichen Parametern aus. Die Ressourcenstandorte für Ihre Logik-App, den benutzerdefinierten Standort und die Kubernetes-Umgebung müssen identisch sein.

| Parameter | Beschreibung |
|------------|-------------|
| `--name -n` | Ein eindeutiger Name für Ihre Logik-App. |
| `--resource-group -g` | Der Name der [Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-cli.md), in der Sie Ihre Logik-App erstellen möchten. [Erstellen Sie eine Ressourcengruppe](#create-resource-group), falls noch keine vorhanden ist. |
| `--storage-account -s` | Das [Speicherkonto](/cli/azure/storage/account), das mit Ihrer Logik-App verwendet werden soll. Für Speicherkonten in derselben Ressourcengruppe verwenden Sie einen Zeichenfolgenwert. Für Speicherkonten in einer anderen Ressourcengruppe verwenden Sie eine Ressourcen-ID. |
|||

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation
```

Um eine Logik-App mit Azure Arc-Unterstützung über ein privates ACR-Image (Azure Container Registry) zu erstellen, führen Sie den Befehl `az logicapp create` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   --deployment-container-image-name myacr.azurecr.io/myimage:tag
   --docker-registry-server-password MyPassword 
   --docker-registry-server-user MyUsername
```

#### <a name="show-logic-app-details"></a>Anzeigen von Logik-App-Details

Um Details zu Ihrer Logik-App mit Azure Arc-Unterstützung anzuzeigen, führen Sie den Befehl `az logicapp show` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="deploy-logic-app"></a>Bereitstellen einer Logik-App

Um Ihre Logik-App mit Azure Arc-Unterstützung über die [Kudu-ZIP-Bereitstellung von Azure App Service](../app-service/resources-kudu.md) bereitzustellen, führen Sie den Befehl `az logicapp deployment source config-zip` mit den folgenden erforderlichen Parametern aus:

> [!IMPORTANT]
> Stellen Sie sicher, dass Ihre ZIP-Datei die Artefakte Ihres Projekts auf Stammebene enthält. Zu diesen Artefakten zählen alle Workflowordner, Konfigurationsdateien wie „host.json“ und „connections.json“ sowie alle anderen zugehörigen Dateien. Fügen Sie keine zusätzlichen Ordner hinzu, und platzieren Sie keine Artefakte in Ordnern, die noch nicht in Ihrer Projektstruktur vorhanden sind. Diese Liste zeigt ein Beispiel für die MyBuildArtifacts.zip-Dateistruktur:
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

#### <a name="start-logic-app"></a>Starten einer Logik-App

Führen Sie zum Starten Ihrer Logik-App mit Azure Arc-Unterstützung den Befehl `az logicapp start` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp start --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="stop-logic-app"></a>Beenden einer Logik-App

Führen Sie zum Beenden Ihrer Logik-App mit Azure Arc-Unterstützung den Befehl `az logicapp stop` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp stop --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="restart-logic-app"></a>Neustarten einer Logik-App

Führen Sie zum erneuten Starten Ihrer Logik-App mit Azure Arc-Unterstützung den Befehl `az logicapp restart` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp restart --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="delete-logic-app"></a>Löschen der Logik-App

Führen Sie zum Löschen Ihrer Logik-App mit Azure Arc-Unterstützung den Befehl `az logicapp delete` mit den folgenden erforderlichen Parametern aus:

```azurecli
az logicapp delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Sie können Ihre Logik-App-Workflows von Anfang bis Ende in Visual Studio Code erstellen, bereitstellen und überwachen. Es gibt keine Änderungen oder Unterschiede bei den Designer-Funktionen zwischen der Entwicklung von Logik-App-Workflows, die in Azure Logic Apps mit einem einzelnen Mandanten ausgeführt werden, und Logic Apps mit Azure Arc-Unterstützung.

1. Um ein Logik-App-Projekt zu erstellen, befolgen Sie die Voraussetzungen und Schritte in der Dokumentation [Erstellen von Integrationsworkflows in Azure Logic Apps mit einzelnem Mandanten mit Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

1. Wenn Sie bereit für die Bereitstellung in Azure sind, verwenden Sie die Erfahrung **In Logik-App bereitstellen**, um Ihre Logik-App-Ressource an Ihrem zuvor erstellten benutzerdefinierten Standort zu erstellen und Ihre Workflows am gleichen Standort bereitzustellen.

   1. Öffnen Sie in einem leeren Bereich im Fenster Ihres Logik-App-Projekts das Kontextmenü, und wählen Sie **In Logik-App bereitstellen** aus.

   1. Wählen Sie das Azure-Abonnement aus, das Ihrem benutzerdefinierten Standort zugeordnet ist.

   1. Wählen Sie **Neue Logik-App in Azure erstellen (Erweitert)** aus, um eine neue Logic Apps-Ressource mit Azure Arc-Unterstützung zu erstellen. Alternativ können Sie eine vorhandene Logik-App-Ressource aus der Liste auswählen und die nächsten Schritte überspringen.

   1. Geben Sie einen global eindeutigen Namen für Ihre Logik-App an.

   1. Wählen Sie den benutzerdefinierten Standort für die Kubernetes-Umgebung mit Azure Arc-Unterstützung aus, in der Sie die Bereitstellung vornehmen möchten. Wenn Sie stattdessen eine generische Azure-Region auswählen, erstellen Sie eine Logic App-Ressource ohne Azure Arc-Unterstützung, die in Azure Logic Apps mit einem einzelnen Mandanten ausgeführt wird.

   1. Wählen oder erstellen Sie eine neue Ressourcengruppe, in der Sie die Logik-App bereitstellen möchten.

   1. Wählen oder erstellen Sie ein neues Speicherkonto zum Speichern des Status und der Metadaten Ihrer Logik-App.

   1. Wählen oder erstellen Sie eine neue Application Insights-Ressource zum Speichern von Anwendungsprotokollen für Ihre Logik-App.

   1. Wenn Sie dies nicht durchgeführt haben, richten Sie Ihre Azure Active Directory-Identität (Azure AD) ein, damit Ihre Logik-App verwaltete API-Verbindungen authentifizieren kann. Weitere Informationen finden Sie in den allgemeinen [Voraussetzungen](#prerequisites).

   1. Geben Sie Client-ID, Mandanten-ID, Objekt-ID und geheimen Clientgeheimnis für Ihre Azure AD-Identität ein.

      > [!NOTE]
      > Sie müssen diesen Schritt nur einmal ausführen. Visual Studio Code aktualisiert die Datei „connections.json“ Ihres Projekts und die Zugriffsrichtlinien Ihrer verwalteten API-Verbindungen für Sie.

1. Wenn Sie fertig sind, ist Ihre Logik-App live und wird in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung ausgeführt, damit Sie sie testen können.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Die Bearbeitungsfunktion des portalbasierten Designers wird derzeit für Logic Apps mit Azure Arc-Unterstützung entwickelt. Sie können Ihre Logik-Apps mithilfe des portalbasierten Designers erstellen, bereitstellen und anzeigen, Sie können sie jedoch nach der Bereitstellung nicht mehr im Portal bearbeiten. Vorerst können Sie ein Logik-App-Projekt lokal in Visual Studio Code erstellen und bearbeiten und dann mithilfe von Visual Studio Code, der Azure CLI oder automatisierten Bereitstellungen bereitstellen.

1. Verwenden Sie das Azure-Portal, und [erstellen Sie eine Ressource für eine **Logik- App (Standard)**](create-single-tenant-workflows-azure-portal.md). Wählen Sie für das Ziel **Veröffentlichen** die Option **Docker-Container** aus. Wählen Sie unter **Region** Ihren zuvor erstellen benutzerdefinierten Speicherort als Speicherort für die App aus.

   Standardmäßig wird die **Logik-App-Ressource (Standard)** in Azure Logic Apps mit einzelnem Mandanten ausgeführt. Für Logic Apps mit Azure Arc-Unterstützung wird Ihre Logik-App-Ressource jedoch an dem benutzerdefinierten Standort ausgeführt, den Sie für Ihre Kubernetes-Umgebung erstellt haben. Außerdem müssen Sie nicht selbst einen App-Service-Plan erstellen – dieser wird für Sie erstellt.

   > [!IMPORTANT]
   > Die Ressourcenstandorte für Ihre Logik-App, den benutzerdefinierten Standort und die Kubernetes-Umgebung müssen identisch sein.

1. [Bearbeiten Sie die Logik-App mithilfe von Visual Studio Code, und stellen Sie sie bereit](create-single-tenant-workflows-visual-studio-code.md).

1. Nachdem Sie Ihre Logik-App erstellt und bereitgestellt haben, können Sie Ihre Workflows wie gewohnt mithilfe des Portals oder von Application Insights überwachen und anzeigen.

   Die Portalfunktion für bereitgestellte Logik-Apps ist derzeit im schreibgeschützten Modus verfügbar, d. h. Sie können Ihre Workflows oder App-Einstellungen nicht ändern. Sie können jedoch weiterhin den Ausführungsverlauf, den Triggerverlauf und andere Informationen zu Ihren Apps anzeigen. Vorerst können Sie zum Aktualisieren Ihrer Logik-Apps die Azure CLI, Visual Studio Code oder automatisierte Bereitstellungen verwenden.

---

## <a name="set-up-connection-authentication"></a>Einrichten der Verbindungsauthentifizierung

Derzeit unterstützen Kubernetes-Cluster mit Azure Arc-Unterstützung nicht die Verwendung der verwalteten Identität einer Logik-App zur Authentifizierung von verwalteten API-Verbindungen. Sie erstellen diese in Azure gehosteten und verwalteten Verbindungen, wenn Sie verwaltete Connectors in Ihren Workflows verwenden.

Stattdessen müssen Sie ihre eigene App-Registrierung in Azure Active Directory (Azure AD) erstellen. Sie können diese App-Registrierung dann als Identität für Logik-Apps verwenden, die in Logic Apps mit Azure Arc-Unterstützung bereitgestellt und ausgeführt werden. Weitere Informationen finden Sie in den [allgemeinen Voraussetzungen](#prerequisites).

Bei ihrer App-Registrierung benötigen Sie die Client-ID, die Objekt-ID, die Mandanten-ID und den geheimen Clientschlüssel. Wenn Sie Visual Studio Code für die Bereitstellung verwenden, verfügen Sie über eine integrierte Benutzeroberfläche zum Einrichten Ihrer Logik-App mit einer Azure AD-Identität. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen von Logik-App-Workflows – Visual Studio Code](#create-and-deploy-logic-apps).

Wenn Sie jedoch Visual Studio Code für die Entwicklung verwenden, aber die Azure-Befehlszeilenschnittstelle oder automatisierte Pipelines für die Bereitstellung verwenden, führen Sie diese Schritte aus.

### <a name="configure-connection-and-app-settings-in-your-project"></a>Konfigurieren von Verbindungs- und App-Einstellungen in Ihrem Projekt

1. Suchen Sie in der Datei **connections.json** Ihres Logik-App-Projekts das `authentication`-Objekt der verwalteten Verbindung. Ersetzen Sie den Inhalt dieses Objekts durch Ihre [App-Registrierungsinformationen](#prerequisites), die Sie zuvor in den allgemeinen Voraussetzungen generiert haben:

   ```json
   "authentication": {
      "type": "ActiveDirectoryOAuth",
      "audience": "https://management.core.windows.net/",
      "credentialType": "Secret",
      "clientId": "@appsetting('WORKFLOWAPP_AAD_CLIENTID')",
      "tenant": "@appsetting('WORKFLOWAPP_AAD_TENANTID')",
      "secret": "@appsetting('WORKFLOWAPP_AAD_CLIENTSECRET')"
   } 
   ```

1. Fügen Sie in der Datei **local.settings.json** Ihres Logik-App-Projekts Ihre Client-ID, Objekt-ID, Mandanten-ID und den geheimen Clientschlüssel hinzu. Nach der Bereitstellung werden diese Einstellungen zu Ihren Logik-App-Einstellungen.

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "WORKFLOWAPP_AAD_CLIENTID":"<my-client-ID>",
         "WORKFLOWAPP_AAD_OBJECTID":"<my-object-ID",
         "WORKFLOWAPP_AAD_TENANTID":"<my-tenant-ID>",
         "WORKFLOWAPP_AAD_CLIENTSECRET":"<my-client-secret>"
      }
   }
   ```

> [!IMPORTANT]
> Achten Sie bei Produktionsszenarien oder -umgebungen darauf, dass Sie solche Geheimnisse und sensiblen Informationen schützen und sichern, z. B. durch die Verwendung eines [Schlüsseltresors](../app-service/app-service-key-vault-references.md).

### <a name="add-access-policies"></a>Hinzufügen von Zugriffsrichtlinien

In Azure Logic Apps mit einzelnem Mandanten verfügt jede Logik-App über eine Identität, der durch Zugriffsrichtlinien die Berechtigung erteilt wird, in Azure gehostete und verwaltete Verbindungen zu verwenden. Sie können diese Zugriffsrichtlinien mithilfe des Azure-Portals oder von Infrastrukturbereitstellungen einrichten.

#### <a name="arm-template"></a>ARM-Vorlage

Fügen Sie in Ihrer ARM-Vorlage (Azure Resource Manager) die folgende Ressourcendefinition für *jede* verwaltete API-Verbindung ein, und stellen Sie die folgenden Informationen bereit:

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| <*connection-name*> | Der Name Ihrer verwalteten API-Verbindung, z. B. `office365`. |
| <*object-ID*> | Die Objekt-ID für Ihre Azure AD-Identität, die zuvor bei der Registrierung Ihrer App gespeichert wurde. |
| <*tenant-ID*> | Die Mandanten-ID für Ihre Azure AD-Identität, die zuvor bei der Registrierung Ihrer App gespeichert wurde. |
|||

```json
{
   "type": "Microsoft.Web/connections/accessPolicies",
   "apiVersion": "2016-06-01",
   "name": "[concat('<connection-name>'),'/','<object-ID>')]",
   "location": "<location>",
   "dependsOn": [
      "[resourceId('Microsoft.Web/connections', parameters('connection_name'))]"
   ],
   "properties": {
      "principal": {
         "type": "ActiveDirectory",
         "identity": {
            "objectId": "<object-ID>",
            "tenantId": "<tenant-ID>"
         }
      }
   }
}
```

Weitere Informationen finden Sie in der Dokumentation zu [Microsoft.Web/connections/accesspolicies (ARM-Vorlage)](/azure/templates/microsoft.web/connections?tabs=json).

#### <a name="azure-portal"></a>Azure-Portal

Verwenden Sie für diese Aufgabe Ihre zuvor gespeicherte Client-ID als *Anwendungs-ID*.

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und öffnen Sie sie. Wählen Sie im Menü Ihrer Logik-App unter **Workflows** die Option **Verbindungen** aus, die alle Verbindungen in den Workflows Ihrer Logik-App auflistet.

1. Wählen Sie unter **API-Verbindungen** eine Verbindung aus (in diesem Beispiel `office365`).

1. Wählen Sie im Menü der Verbindung unter **Einstellungen** die Option **Zugriffsrichtlinien** > **Hinzufügen** aus.

1. Suchen Sie im Bereich **Zugriffsrichtlinie hinzufügen** im Suchfeld nach Ihrer zuvor gespeicherten Client-ID, und wählen Sie sie aus.

1. Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus.

1. Wiederholen Sie diese Schritte für jede in Azure gehostete Verbindung in Ihrer Logik-App.

## <a name="automate-devops-deployment"></a>Automatisieren der DevOps-Bereitstellung

Zum Erstellen und Bereitstellen Ihrer Logik-Apps mit Azure Arc-Unterstützung können Sie dieselben Pipelines und Prozesse wie für [Logik-Apps verwenden, die auf einem einzelnen Mandanten basieren](single-tenant-overview-compare.md). Um Infrastrukturbereitstellungen mithilfe von Pipelines für DevOps zu automatisieren, nehmen Sie die folgenden Änderungen auf der Infrastrukturebene sowohl für Nicht-Container- als auch für Containerbereitstellungen vor.

### <a name="standard-deployment-non-container"></a>Standardbereitstellung (Nicht-Container)

Wenn Sie „zip deploy“ für die Bereitstellung von Logik-Apps verwenden, müssen Sie keine Docker-Registrierung für das Hosting von Containerimages einrichten. Obwohl Logik-Apps in Kubernetes technisch gesehen in Containern ausgeführt werden, verwaltet Logic Apps mit Azure Arc-Unterstützung diese Container für Sie. Führen Sie für dieses Szenario die folgenden Aufgaben durch, wenn Sie Ihre Infrastruktur einrichten:

- Benachrichtigen Sie den Ressourcenanbieter, dass Sie eine Logik-App in Kubernetes erstellen.
- Schließen Sie einen App Service-Plan in Ihre Bereitstellung ein. Weitere Informationen hierzu finden Sie unter [Einschließen eines App Service-Plans in die Bereitstellung](#include-app-service-plan).

Fügen Sie in Ihre [ARM-Vorlage (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) die folgenden Werte ein:

| Element | JSON-Eigenschaft | BESCHREIBUNG |
|------|---------------|-------------|
| Standort | `location` | Stellen Sie sicher, dass Sie denselben Ressourcenstandort (Azure-Region) wie für Ihren benutzerdefinierten Standort und Ihre Kubernetes-Umgebung verwenden. Die Ressourcenstandorte für Ihre Logik-App, den benutzerdefinierten Standort und die Kubernetes-Umgebung müssen identisch sein. <p><p>**Hinweis**: Dieser Wert ist *nicht identisch mit dem Namen Ihres benutzerdefinierten Standorts*. |
| App-Art | `kind` | Der Typ der App, die Sie bereitstellen, damit die Azure-Plattform Ihre App identifizieren kann. Für Azure Logic Apps sehen diese Informationen wie im folgenden Beispiel aus: `kubernetes,functionapp,workflowapp,linux` |
| Erweiterter Standort | `extendedLocation` | Für dieses Objekt muss das Element `"name"` Ihres *benutzerdefinierten Standorts* für Ihre Kubernetes-Umgebung vorhanden sein, und `"type"` muss auf `"CustomLocation"` festgelegt sein. |
| Ressourcen-ID des Hostingplans | `serverFarmId` | Die Ressourcen-ID des zugeordneten App Service-Plans, die wie folgt formatiert ist: <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| Speicherverbindungszeichenfolge | `AzureWebJobsStorage` | Die Verbindungszeichenfolge für Ihr Speicherkonto. <p><p>**Wichtig**: Sie müssen die Verbindungszeichenfolge für Ihr Speicherkonto in Ihrer ARM-Vorlage angeben. Achten Sie bei Produktionsszenarien oder -umgebungen darauf, dass Sie solche Geheimnisse und sensiblen Informationen schützen und sichern, z. B. durch die Verwendung eines Schlüsseltresors. |
||||

#### <a name="arm-template"></a>ARM-Vorlage

Das folgende Beispiel beschreibt eine beispielhafte Logic Apps-Ressourcendefinition mit Azure Arc-Unterstützung, die Sie in Ihrer ARM-Vorlage verwenden können. Weitere Informationen finden Sie in der Dokumentation zu [Microsoft.Web/sites – Vorlagenformat (JSON)](/azure/templates/microsoft.web/sites?tabs=json).

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": "kubernetes,functionapp,workflowapp,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "clientAffinityEnabled": false,
      "name": "[parameters('name')]",
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "Node|12"
      }
   }
}
```

### <a name="container-deployment"></a>Containerbereitstellung

Wenn Sie es vorziehen, Containertools und Bereitstellungsprozesse zu verwenden, können Sie Ihre Logik-Apps containerisieren und in Logic Apps mit Azure Arc-Unterstützung bereitstellen. Führen Sie für dieses Szenario die folgenden allgemeinen Aufgaben durch, wenn Sie Ihre Infrastruktur einrichten:

- Richten Sie eine Docker-Registrierung zum Hosten Ihrer Containerimages ein.

- Fügen Sie zum Containerisieren Ihrer Logik-App dem Stammordner Ihres Logik-App-Projekts die folgende Dockerfile-Datei hinzu, und führen Sie die Schritte zum Erstellen und Veröffentlichen eines Images in Ihrer Docker-Registrierung aus. Beispiel: [Tutorial: Erstellen und Bereitstellen von Containerimages in der Cloud mit Azure Container Registry Tasks](../container-registry/container-registry-tutorial-quick-task.md).

  > [!NOTE]
  > Wenn Sie [SQL als Speicheranbieter verwenden](set-up-sql-db-storage-single-tenant-standard-workflows.md), vergewissern Sie sich, dass Sie ein Azure Functions-Image, Version 3.3.1 oder höher, verwenden.

  ```text
  FROM mcr.microsoft.com/azure-functions/node:3.3.1
  ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
  AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
  FUNCTIONS_V2_COMPATIBILITY_MODE=true
  COPY . /home/site/wwwroot
  RUN cd /home/site/wwwroot
  ```

- Benachrichtigen Sie den Ressourcenanbieter, dass Sie eine Logik-App in Kubernetes erstellen.

- Zeigen Sie in Ihrer Bereitstellungsvorlage auf die Docker-Registrierung und das Containerimage, in denen Sie die Bereitstellung planen. Azure Logic Apps mit einzelnem Mandanten verwendet diese Informationen, um das Containerimage von Ihrer Docker-Registrierung abzurufen.

- Schließen Sie einen App Service-Plan in Ihre Bereitstellung ein. Weitere Informationen hierzu finden Sie unter [Einschließen eines App Service-Plans in die Bereitstellung](#include-app-service-plan).

Fügen Sie in Ihre [ARM-Vorlage (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) die folgenden Werte ein:

| Element | JSON-Eigenschaft | BESCHREIBUNG |
|------|---------------|-------------|
| Standort | `location` | Stellen Sie sicher, dass Sie denselben Ressourcenstandort (Azure-Region) wie für Ihren benutzerdefinierten Standort und Ihre Kubernetes-Umgebung verwenden. Die Ressourcenstandorte für Ihre Logik-App, den benutzerdefinierten Standort und die Kubernetes-Umgebung müssen identisch sein. <p><p>**Hinweis**: Dieser Wert ist *nicht identisch* mit dem *Namen* Ihres benutzerdefinierten Standorts. |
| App-Art | `kind` | Der Typ der App, die Sie bereitstellen, damit die Azure-Plattform Ihre App identifizieren kann. Für Azure Logic Apps sehen diese Informationen wie im folgenden Beispiel aus: `kubernetes,functionapp,workflowapp,container` |
| Erweiterter Standort | `extendedLocation` | Für dieses Objekt muss das Element `"name"` Ihres *benutzerdefinierten Standorts* für Ihre Kubernetes-Umgebung vorhanden sein, und `"type"` muss auf `"CustomLocation"` festgelegt sein. |
| Containername | `linuxFxVersion` | Der Name Ihres Containers, der wie folgt formatiert ist: `DOCKER\|<container-name>` |
| Ressourcen-ID des Hostingplans | `serverFarmId` | Die Ressourcen-ID des zugeordneten App Service-Plans, die wie folgt formatiert ist: <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| Speicherverbindungszeichenfolge | `AzureWebJobsStorage` | Die Verbindungszeichenfolge für Ihr Speicherkonto. <p><p>**Wichtig**: Wenn Sie einen Docker-Container bereitstellen, müssen Sie die Verbindungszeichenfolge für Ihr Speicherkonto in Ihrer ARM-Vorlage angeben. Achten Sie bei Produktionsszenarien oder -umgebungen darauf, dass Sie solche Geheimnisse und sensiblen Informationen schützen und sichern, z. B. durch die Verwendung eines Schlüsseltresors. |
||||

Fügen Sie diese Werte in Ihre Vorlage ein, um auf Ihre Docker-Registrierung und das Containerimage zu verweisen:

| Element | JSON-Eigenschaft | BESCHREIBUNG |
|------|---------------|-------------|
| Server-URL der Docker-Registrierung | `DOCKER_REGISTRY_SERVER_URL` | Die URL für den Docker-Registrierungsserver. |
| Docker-Registrierungsserver | `DOCKER_REGISTRY_SERVER_USERNAME` | Der Benutzername für den Zugriff auf den Docker-Registrierungsserver. |
| Kennwort des Docker-Registrierungsservers | `DOCKER_REGISTRY_SERVER_PASSWORD` | Das Kennwort für den Zugriff auf den Docker-Registrierungsserver. |
||||

#### <a name="arm-template"></a>ARM-Vorlage

Das folgende Beispiel beschreibt eine beispielhafte Logic Apps-Ressourcendefinition mit Azure Arc-Unterstützung, die Sie in Ihrer ARM-Vorlage verwenden können. Weitere Informationen finden Sie in der Dokumentation zu [Microsoft.Web/sites – Vorlagenformat (ARM-Vorlage)](/azure/templates/microsoft.web/sites?tabs=json).

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": " kubernetes,functionapp,workflowapp,container",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "name": "[parameters('name')]",
      "clientAffinityEnabled": false,
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }, 
            {
               "name": "DOCKER_REGISTRY_SERVER_URL",
               "value": "<docker-registry-server-URL>"
            },
            { 
               "name": "DOCKER_REGISTRY_SERVER_USERNAME",
               "value": "<docker-registry-server-username>"
            },
            {
               "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
               "value": "<docker-registry-server-password>"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "DOCKER|<container-name>"
      }
   }
}
```

<a name="include-app-service-plan"></a>

### <a name="include-app-service-plan-with-deployment"></a>Einschließen eines App Service-Plans in die Bereitstellung

Unabhängig davon, ob Sie über eine Standard- oder Containerbereitstellung verfügen, müssen Sie einen App Service-Plan in Ihre Bereitstellung einschließen. Obwohl dieser Plan für eine Kubernetes-Umgebung weniger relevant wird, erfordern sowohl die Standard- als auch die Containerbereitstellung weiterhin einen App Service-Plan.

Während andere Erstellungsoptionen in der Regel die Bereitstellung der Azure-Ressource für diesen Plan behandeln, müssen Sie die Azure-Ressource für den Plan explizit erstellen, wenn Ihre Bereitstellungen „Infrastructure-as-Code“-Vorlagen verwenden. Die Hostingplanressource ändert sich nicht, sondern nur die `sku`-Informationen.

Fügen Sie in Ihre [ARM-Vorlage (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) die folgenden Werte ein:

| Element | JSON-Eigenschaft | BESCHREIBUNG |
|------|---------------|-------------|
| Standort | `location` | Stellen Sie sicher, dass Sie denselben Ressourcenstandort (Azure-Region) wie für Ihren benutzerdefinierten Standort und Ihre Kubernetes-Umgebung verwenden. Die Ressourcenstandorte für Ihre Logik-App, den benutzerdefinierten Standort und die Kubernetes-Umgebung müssen identisch sein. <p><p>**Hinweis**: Dieser Wert ist *nicht identisch mit dem Namen Ihres benutzerdefinierten Standorts*. |
| Typ | `kind` | Die Art des bereitzustellenden App Service-Plans. Dies muss `kubernetes,linux` sein. |
| Erweiterter Standort | `extendedLocation` | Für dieses Objekt muss das Element `"name"` Ihres *benutzerdefinierten Standorts* für Ihre Kubernetes-Umgebung vorhanden sein, und `"type"` muss auf `"CustomLocation"` festgelegt sein. |
| Name des Hostingplans | `name` | Der Name für den App Service-Plan. |
| Planebene | `sku: tier` | Die App Service-Planebene, die `K1` lautet. |
| Planname | `sku: name` | Der Name des App Service-Plans, der `Kubernetes` lautet. |
||||

#### <a name="arm-template"></a>ARM-Vorlage

Im folgenden Beispiel wird eine Beispielressourcendefinition für den App Service-Plan beschrieben, die Sie mithilfe Ihrer App-Bereitstellung verwenden können. Weitere Informationen finden Sie in der Dokumentation zu [Microsoft.Web/serverfarms – Vorlagenformat (ARM-Vorlage)](/azure/templates/microsoft.web/serverfarms?tabs=json).

```json
{
   "type": "Microsoft.Web/serverfarms",
   "apiVersion": "2020-12-01",
   "location": "<location>",
   "name": "<hosting-plan-name>",
   "kind": "kubernetes,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
   },
   "sku": {
      "tier": "K1",
      "name": "Kubernetes", 
      "capacity": 1
   },
   "properties": {
      "kubeEnvironmentProfile": {
         "id": "[parameters('kubeEnvironmentId')]"
      }
   }
}
```

<a name="change-scaling"></a>

## <a name="change-default-scaling-behavior"></a>Ändern des Standardskalierungsverhaltens

Logic Apps mit Azure Arc-Unterstützung verwaltet automatisch die Skalierung für Ihre Logik-Apps basierend auf der Anzahl der *Aufträge* in der Back-End-Speicherwarteschlange. Sie können jedoch das Standardskalierungsverhalten ändern.

In einer Logik-App gibt die Workflowdefinition die Reihenfolge der auszuführenden Aktionen an. Bei jeder Auslösung einer Workflowausführung erstellt die Azure Logic Apps-Runtime einen *Auftrag* für jeden Aktionstyp in der Workflowdefinition. Die Runtime organisiert diese Aufträge dann in einem *Auftragssequenzierer*. Dieser Sequenzierer orchestriert die Ausführung der Aufträge für die Workflowdefinition, aber die zugrunde liegende Azure Logic Apps-Engine für die Auftragsorchestrierung führt jeden Auftrag aus.

Bei zustandsbehafteten Workflows verwendet die Engine für die Auftragsorchestrierung entsprechende Speicherwarteschlangennachrichten, um Aufträge in den Auftragssequenzierern zu planen. Im Hintergrund überwachen *Auftragsdispatcher* (oder *Dispatcher-Workerinstanzen*) diese Auftragswarteschlangen. Die Orchestrierungsengine verwendet eine standardmäßige minimale und maximale Anzahl von Workerinstanzen, um die Auftragswarteschlangen zu überwachen. Bei zustandslosen Workflows behält die Orchestrierungsengine Aktionszustände vollständig im Arbeitsspeicher bei.

Um das Standardskalierungsverhalten zu ändern, geben Sie eine andere minimale und maximale Anzahl von Workerinstanzen an, die die Auftragswarteschlangen überwachen.

### <a name="prerequisites-to-change-scaling"></a>Voraussetzungen zum Ändern der Skalierung

Auf Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung muss für die zuvor erstellte App Service-Paketerweiterung die Eigenschaft `keda.enabled` auf `true` festgelegt sein. Weitere Informationen finden Sie in den [allgemeinen Voraussetzungen](#prerequisites).

### <a name="change-scaling-threshold"></a>Ändern des Skalierungsschwellenwerts

In Logic Apps mit Azure Arc-Unterstützung löst die Länge der Auftragswarteschlange ein Skalierungsereignis aus und legt einen Schwellenwert dafür fest, wie oft die Skalierung für Ihre Logik-App erfolgt. Sie können die Warteschlangenlänge ändern, bei der der Standardwert auf `20` Aufträge festgelegt ist. Erhöhen Sie die Warteschlangenlänge, um die Häufigkeit der Skalierung zu verringern. Verringern Sie die Warteschlangenlänge, um die Häufigkeit der Skalierung zu erhöhen. Für diesen Prozess sind möglicherweise einige Tests erforderlich.

Um die Länge der Warteschlange zu ändern, legen Sie in der Datei **host.json** auf der Stammebene Ihres Logik-App-Projekts die `Runtime.ScaleMonitor.KEDA.TargetQueueLength`-Eigenschaft fest. Beispiel:

```json
"extensions": {
   "workflow": {
      "settings": {
         "Runtime.ScaleMonitor.KEDA.TargetQueueLength": "10"
      }
   }
}
```

### <a name="change-throughput-maximum"></a>Ändern des maximalen Durchsatzes

Für eine vorhandene Logik-App-Ressource können Sie die maximale Anzahl von Workerinstanzen ändern, für die der Standardwert auf `2` festgelegt ist. Dieser Wert steuert die Obergrenze für die Anzahl von Workerinstanzen, die die Auftragswarteschlangen überwachen können.

Um dieses Maximum zu ändern, verwenden Sie die Azure CLI (nur Erstellen der Logik-App) und das Azure-Portal.

#### <a name="azure-cli"></a>Azure CLI

Um eine neue Logik-App zu erstellen, führen Sie den Befehl `az logicapp create` mit den folgenden Parametern aus:

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 1] [--max-worker-count 4]
```

Verwenden Sie den Parameter `--settings`, um die maximale Anzahl von Instanzen zu konfigurieren:

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --settings "K8SE_APP_MAX_INSTANCE_COUNT=10"
```

#### <a name="azure-portal"></a>Azure-Portal

Fügen Sie in den Einstellungen Ihrer auf einem einzelnen Mandanten basierenden Logik-App den Einstellungswert `K8SE_APP_MAX_INSTANCE_COUNT` hinzu oder bearbeiten Sie diesen, indem Sie die folgenden Schritte ausführen:

1. Suchen Sie im Azure-Portal Ihre auf einem einzelnen Mandanten basierende Logik-App, und öffnen Sie diese.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Konfiguration** aus.

1. Fügen Sie im Bereich **Konfiguration** unter **Anwendungseinstellungen** entweder eine neue Anwendungseinstellung hinzu, oder bearbeiten Sie den vorhandenen Wert, sofern er bereits hinzugefügt wurde.

   1. Wählen Sie **Neue Anwendungseinstellung** aus, und fügen Sie die `K8SE_APP_MAX_INSTANCE_COUNT`-Einstellung mit dem gewünschten maximalen Wert hinzu.

   1. Bearbeiten Sie den vorhandenen Wert für die `K8SE_APP_MAX_INSTANCE_COUNT`-Einstellung.

1. Speichern Sie Ihre Änderungen, wenn Sie fertig sind.

### <a name="change-throughput-minimum"></a>Ändern des minimalen Durchsatzes

Für eine vorhandene Logik-App-Ressource können Sie die minimale Anzahl von Workerinstanzen ändern, für die der Standardwert auf `1` festgelegt ist. Dieser Wert steuert die Untergrenze für die Anzahl von Workerinstanzen, die die Auftragswarteschlangen überwachen können. Erhöhen Sie diesen Wert für die Hochverfügbarkeit oder Leistung.

Verwenden Sie die Azure CLI oder das Azure-Portal, um diesen minimalen Wert zu ändern.

#### <a name="azure-cli"></a>Azure CLI

Für eine vorhandene Logik-App-Ressource führen Sie den Befehl `az logicapp scale` mit den folgenden Parametern aus:

```azurecli
az logicapp scale --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --instance-count 5 
```

Um eine neue Logik-App zu erstellen, führen Sie den Befehl `az logicapp create` mit den folgenden Parametern aus:

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 2] [--max-worker-count 4]
```

#### <a name="azure-portal"></a>Azure-Portal

Ändern Sie in den Einstellungen Ihrer auf einem einzelnen Mandanten basierenden Logik-App den Eigenschaftswert **Aufskalieren**, indem Sie die folgenden Schritte ausführen:

1. Suchen Sie im Azure-Portal Ihre auf einem einzelnen Mandanten basierende Logik-App, und öffnen Sie diese.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Aufskalieren** aus.

1. Ziehen Sie im Bereich **Aufskalieren** den Schieberegler für die Mindestanzahl der Instanzen auf den gewünschten Wert.

1. Speichern Sie Ihre Änderungen, wenn Sie fertig sind.

## <a name="troubleshoot-problems"></a>Behandeln von Problemen

Versuchen Sie die folgenden Optionen, um weitere Informationen über Ihre bereitgestellten Logik-Apps zu erhalten:

### <a name="access-app-settings-and-configuration"></a>Zugreifen auf App-Einstellungen und die Konfiguration

Um auf Ihre App-Einstellungen zuzugreifen, führen Sie den Befehl `az logicapp config appsettings` mit den folgenden Parametern aus:

```azurecli
az logicapp config appsettings list --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

Um eine App-Einstellung zu konfigurieren, führen Sie den Befehl `az logicapp config appsettings set` mit den folgenden Parametern aus. Stellen Sie sicher, dass Sie den Parameter `--settings` mit dem Namen und Wert Ihrer Einstellung verwenden.

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --settings "MySetting=1"
```

Um eine App-Einstellung zu löschen, führen Sie den Befehl `az logicapp config appsettings delete` mit den folgenden Parametern aus. Stellen Sie sicher, dass Sie den Parameter `--setting-names` mit dem Namen der Einstellung verwenden, die Sie löschen möchten.

```azurecli
az logicapp config appsettings delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --setting-names MySetting
```

### <a name="view-logic-app-properties"></a>Anzeigen von Logik-App-Eigenschaften

Um die Informationen und Eigenschaften Ihrer App anzuzeigen, führen Sie den Befehl `az logicapp show` mit den folgenden Parametern aus:

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="monitor-workflow-activity"></a>Überwachen der Workflowaktivität

Führen Sie die folgenden Schritte aus, um die Aktivität für einen Workflow in Ihrer Logik-App anzeigen zu können: 

1. Suchen Sie im Azure-Portal nach Ihrer bereitgestellten Logik-App und öffnen Sie sie.

1. Wählen Sie im Menü der Logik-App die Option  **Workflows** und dann Ihren Workflow aus. 

1. Wählen Sie im Menü „Workflow“ die Option  **Überwachen** aus.

### <a name="collect-logs"></a>Erfassen von Protokollen

Wenn Sie protokollierte Daten zu Ihrer Logik-App abrufen möchten, aktivieren Sie Application Insights in Ihrer Logik-App, sofern dies noch nicht aktiviert ist.

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Logik-Apps mit Azure Arc-Unterstützung](azure-arc-enabled-logic-apps-overview.md)

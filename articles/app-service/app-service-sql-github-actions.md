---
title: 'Tutorial: Verwenden von GitHub Actions zum Bereitstellen in App Service für Container und Herstellen einer Datenbankverbindung'
description: Erfahren Sie, wie Sie eine ASP.NET Core-App in Azure und Azure SQL-Datenbank mit GitHub Actions bereitstellen.
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 24001d71721f2e3ee243248705ed8d4b64571c83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675400"
---
# <a name="tutorial-use-github-actions-to-deploy-to-an-app-service-custom-container-and-connect-to-a-database"></a>Tutorial: Verwenden von GitHub Actions zum Bereitstellen in App Service für Container und Herstellen einer Datenbankverbindung

Dieses Tutorial führt Sie durch die Einrichtung eines GitHub Actions-Workflows zum Bereitstellen einer containerisierten ASP.NET Core-Anwendung mit einem [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)-Back-End. Nach Abschluss verfügen Sie über eine ASP.NET-App, die in Azure ausgeführt wird und mit SQL-Datenbank verbunden ist. Zunächst erstellen Sie Azure-Ressourcen mit einem auf einer [ARM-Vorlage](../azure-resource-manager/templates/overview.md) beruhenden GitHub Actions-Workflow.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Verwenden eines GitHub Actions-Workflows zum Hinzufügen von Ressourcen zu Azure mit einer ARM-Vorlage (Azure Resource Manager-Vorlage)
> - Verwenden eines GitHub Actions-Workflows zum Erstellen eines Containers mit den neuesten Web-App-Änderungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial abzuschließen, benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.
  - Ein GitHub-Repository, in dem Sie Ihre Resource Manager-Vorlagen und Ihre Workflowdateien speichern können. Informationen zum Erstellen eines neuen Repositorys finden Sie [in diesem Hilfeartikel](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="download-the-sample"></a>Herunterladen des Beispiels

[Verzweigen Sie das Beispielprojekt](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/) im Repository mit den Azure-Beispielen.

```
https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/
```

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Öffnen Sie Azure Cloud Shell unter https://shell.azure.com. Sie können alternativ die Azure-Befehlszeilenschnittstelle verwenden, wenn Sie sie lokal installiert haben. (Weitere Informationen zu Cloud Shell finden Sie in der Übersicht zu Cloud Shell.)

```azurecli-interactive
    az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie müssen sich mit einem Dienstprinzipal authentifizieren, damit das Ressourcenbereitstellungsskript funktioniert. Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Klicken auf die Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

Ersetzen Sie im Beispiel die Platzhalter durch Ihre Abonnement-ID, den Ressourcengruppennamen und den Dienstprinzipalnamen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung. Hilfe dazu finden Sie unter [Konfigurieren von Anmeldeinformationen für die Bereitstellung](https://github.com/Azure/login#configure-deployment-credentials).

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf die spezifische App Service-App und nicht auf die gesamte Ressourcengruppe beschränkt.

## <a name="configure-the-github-secret-for-authentication"></a>Konfigurieren des GitHub-Geheimnisses für die Authentifizierung

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf Benutzerebene](#generate-deployment-credentials) zu verwenden, fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

## <a name="add-a-sql-server-secret"></a>Hinzufügen eines SQL Server-Geheimnisses

Erstellen Sie in Ihrem Repository ein neues Geheimnis für `SQL_SERVER_ADMIN_PASSWORD`. Bei diesem Geheimnis kann es sich um ein beliebiges Kennwort handeln, das den Azure-Standards für Kennwortsicherheit entspricht. Sie können nicht noch einmal auf dieses Kennwort zugreifen. Speichern Sie es daher separat.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Der Workflow zum Erstellen von Azure-Ressourcen führt eine [ARM-Vorlage](../azure-resource-manager/templates/overview.md) aus, um Ressourcen in Azure bereitzustellen. Der Workflow:

- Checkt den Quellcode mithilfe der [Check-Out-Aktion](https://github.com/marketplace/actions/checkout) aus.
- Meldet sich mit der [Azure-Anmeldeaktion](https://github.com/marketplace/actions/azure-login) bei Azure an und sammelt Umgebungs- und Azure-Ressourceninformationen.
- Stellt Ressourcen mit der [Aktion „Bereitstellen“ aus Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) bereit.

So führen Sie den Workflow zum Erstellen von Azure-Ressourcen aus:

1. Öffnen Sie die Datei `azuredeploy.yaml` in `.github/workflows` in Ihrem Repository.

1. Aktualisieren Sie den Wert von `AZURE_RESOURCE_GROUP` auf den Namen Ihrer Ressourcengruppe.

1. Navigieren Sie zu **Aktionen**, und wählen Sie **Workflow ausführen** aus.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="Führen Sie den GitHub Actions-Workflow aus, um Ressourcen hinzuzufügen.":::

1. Vergewissern Sie sich, dass Ihrer Aktion erfolgreich ausgeführt wurde, indem Sie auf der Seite **Aktionen** nach einem grünen Häkchen suchen.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="Erfolgreiche Ausführung von „Ressourcen erstellen“.":::

## <a name="add-container-registry-and-sql-secrets"></a>Hinzufügen von Geheimnissen für die Containerregistrierung und SQL

1. Öffnen Sie im Azure-Portal die neu erstellte Azure Container Registry in Ihrer Ressourcengruppe.

1. Wechseln Sie zu **Zugriffsschlüssel**, und kopieren Sie die Werte für Benutzername und Kennwort.

1. Erstellen Sie neue GitHub-Geheimnisse für die `ACR_USERNAME`- und `ACR_PASSWORD`-Kennwörter in Ihrem Repository.

1. Öffnen Sie im Azure-Portal Ihre Azure SQL-Datenbank. Öffnen Sie **Verbindungszeichenfolgen**, und kopieren Sie den Wert.

1. Erstellen Sie ein neues Geheimnis für `SQL_CONNECTION_STRING`. Ersetzen Sie `{your_password}` durch Ihre Dataset-ID (`SQL_SERVER_ADMIN_PASSWORD`).

## <a name="build-push-and-deploy-your-image"></a>Erstellen, Pushen und Bereitstellen Ihres Images

Der Erstellungs-, Push- und Bereitstellungsworkflow erstellt einen Container mit den neuesten App-Änderungen, pusht den Container in die [Azure Container Registry](../container-registry/index.yml) und aktualisiert den Stagingslot der Webanwendung, sodass er auf den aktuellsten per Push bereitgestellten Container verweist. Der Workflow containerisiert einen Erstellungs- und Bereitstellungsauftrag:

- Der Erstellungsauftrag checkt den Quellcode mit der [Check-Out-Aktion](https://github.com/marketplace/actions/checkout) aus. Anschließend verwendet der Auftrag die [Docker-Anmeldeaktion](https://github.com/marketplace/actions/docker-login) und ein benutzerdefiniertes Skript, um sich bei der Azure Container Registry zu authentifizieren, ein Containerimage zu erstellen und es in der Azure Container Registry bereitzustellen.
- Der Bereitstellungsauftrag meldet sich bei Azure mithilfe der [Azure-Anmeldeaktion](https://github.com/marketplace/actions/azure-login) an und sammelt Umgebungs- und Azure-Ressourceninformationen. Anschließend aktualisiert der Auftrag die Web App-Einstellungen mithilfe der [Aktion „Azure App Service-Einstellungen“](https://github.com/marketplace/actions/azure-app-service-settings) und führt die Bereitstellung in einem App Service-Stagingslot mithilfe der [Aktion „Azure Web Deploy“](https://github.com/marketplace/actions/azure-webapp) aus. Schließlich führt der Auftrag ein benutzerdefiniertes Skript aus, um die SQL-Datenbank zu aktualisieren, und vertauscht den Stagingslot mit dem Produktionsslot.

So führen Sie den Erstellungs-, Push- und Bereitstellungsworkflow aus:

1. Öffnen Sie Ihre `build-deploy.yaml`-Datei in `.github/workflows` in Ihrem Repository.

1. Überprüfen Sie, ob die Umgebungsvariablen für `AZURE_RESOURCE_GROUP` und `WEB_APP_NAME` mit denen in `azuredeploy.yaml` übereinstimmen.

1. Aktualisieren Sie den Wert `ACR_LOGIN_SERVER` für Ihren Azure Container Registry-Anmeldeserver.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)
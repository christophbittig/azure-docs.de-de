---
title: include file
description: Datei einfügen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078340"
---
1. Da Sie den Branch `main` bereitstellen, müssen Sie den Standardbereitstellungsbranch für Ihre App Service-App auf `main` festlegen (siehe [Ändern des Bereitstellungsbranchs](../articles/app-service/deploy-local-git.md#change-deployment-branch)). Legen Sie die App-Einstellung `DEPLOYMENT_BRANCH` in Cloud Shell mit dem Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) fest. 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. Kehren Sie zum lokalen Terminalfenster zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie *\<deploymentLocalGitUrl-from-create-step>* durch die URL des Git-Remotespeicherorts, die Sie unter [Erstellen einer Web-App](#create-a-web-app) gespeichert haben.

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. Führen Sie einen Pushvorgang zum Azure-Remotespeicherort durch, um Ihre App mit dem folgenden Befehl bereitzustellen. Wenn Sie von der Git-Anmeldeinformationsverwaltung zur Eingabe von Anmeldeinformationen aufgefordert werden, müssen Sie die Anmeldeinformationen eingeben, die Sie in **Konfigurieren eines Bereitstellungsbenutzers** erstellt haben (nicht die Anmeldeinformationen, die Sie zur Anmeldung beim Azure-Portal verwenden).

    ```bash
    git push azure main
    ```

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Während der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

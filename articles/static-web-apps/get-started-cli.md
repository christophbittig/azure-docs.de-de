---
title: 'Schnellstart: Erstellen Ihrer ersten statischen Website mit Azure Static Web Apps und der CLI'
description: Erfahren Sie, wie Sie mit der Azure CLI eine statische Website für Azure Static Web Apps bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 035a13eeb09f60ca8e16a4f41281341b2ea9dae0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003513"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Schnellstart: Erstellen Ihrer ersten statischen Website mithilfe der Azure CLI

Azure Static Web Apps veröffentlicht eine Website in einer Produktionsumgebung, indem Apps aus einem GitHub-Repository erstellt werden. In dieser Schnellstartanleitung stellen Sie über die Azure CLI eine Web-Anwendung in Azure Static Web Apps bereit.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Persönliches GitHub-Zugriffstoken](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com)-Konto
- Installation der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (mindestens Version 2.8.0)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Wechseln Sie anschließend mit dem folgenden Befehl in den neuen Ordner:

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

Nachdem das Repository erstellt wurde, können Sie nun über die Azure CLI eine statische Web-App erstellen.

> [!IMPORTANT]
> Vergewissern Sie sich, dass Sie sich im Terminal im Ordner _my-first-static-web-app_ befinden.

1. Melden Sie sich mit dem folgenden Befehl bei der Azure CLI an:

    ```azurecli
    az login
    ```

1. Erstellen Sie eine neue statische Web-App aus Ihrem Repository:

    # <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-location "src" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > Die an den Parameter `s` übergebene URL darf nicht das Suffix `.git` enthalten.

    - `<RESOURCE_GROUP_NAME>`: Ersetzen Sie diesen Wert durch einen vorhandenen [Azure-Ressourcengruppennamen](../azure-resource-manager/management/manage-resources-cli.md).

      - Ausführliche Informationen zum Auflisten von Ressourcengruppen finden Sie in der Dokumentation zu [az group](/cli/azure/group#az_group_list).

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Ersetzen Sie diesen Wert durch den GitHub-Benutzernamen.

    - `<LOCATION>`: Ersetzen Sie diesen Wert durch den nächstgelegenen Standort. Beispiele für Optionen: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ und _WestUS2_

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Ersetzen Sie diesen Wert durch das zuvor generierte [persönliche GitHub-Zugriffstoken](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token). Mindestberechtigungen: Bereich `workflow` für öffentliche Repositorys und Bereich `repo` für private Repositorys

    Sie können jetzt die erstellte App in Azure anzeigen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Suchen Sie über die obere Suchleiste nach **my-first-web-static-app**.

1. Wählen Sie **my-first-web-static-app** aus.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz mit dem folgenden Befehl löschen:

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](add-api.md)

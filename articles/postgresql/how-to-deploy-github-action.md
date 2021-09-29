---
title: 'Schnellstart: Herstellen einer Verbindung mit Azure PostgreSQL mithilfe von GitHub Actions'
description: Verwenden von Azure PostgreSQL über einen GitHub Actions-Workflow
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: acfed042be582475f3d186b5bcfbb6754d621b28
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766889"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Schnellstart: Verwenden von GitHub Actions zum Herstellen einer Verbindung mit Azure PostgreSQL

**GILT FÜR:** :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL (Einzelserver) :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL (Flexibler Server)  

Verwenden Sie als Einstieg in [GitHub Actions](https://docs.github.com/en/actions) einen Workflow zum Bereitstellen von Datenbankupdates für [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes:
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Repository mit Beispieldaten (`data.sql`). Wenn Sie kein GitHub-Konto besitzen, können Sie sich [kostenlos registrieren](https://github.com/join).
- Ein Azure Database for PostgreSQL-Server.
    - [Schnellstart: Erstellen eines Azure-Datenbank für PostgreSQL-Servers im Azure-Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein GitHub Actions-Workflow wird durch eine YAML-Datei (.yml) im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus zwei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Bereitstellen** | 1. Bereitstellen der Datenbank |

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

Ersetzen Sie die Platzhalter `server-name` durch den Namen Ihres in Azure gehosteten PostgreSQL-Servers. Ersetzen Sie `subscription-id` und `resource-group` durch die Abonnement-ID und die Ressourcengruppe, die mit Ihrem PostgreSQL-Server verbunden sind.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten gezeigt Zugriff auf Ihre Datenbank gewähren. Kopieren Sie dieses JSON-Ausgabeobjekt zur späteren Verwendung.

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
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf den spezifischen Server beschränkt und umfasst nicht die gesamte Ressourcengruppe.

## <a name="copy-the-postgresql-connection-string"></a>Kopieren der PostgreSQL-Verbindungszeichenfolge

Wechseln Sie im Azure-Portal zum Azure Database for PostgreSQL-Server, und öffnen Sie **Einstellungen** > **Verbindungszeichenfolgen**. Kopieren Sie die **ADO.NET**-Verbindungszeichenfolge. Ersetzen Sie die Platzhalterwerte für `your_database` und `your_password`. Die Verbindungszeichenfolge ähnelt der folgenden Ausgabe:

> [!IMPORTANT]
> - Verwenden Sie ```user=adminusername@servername``` für Einzelserver. Beachten Sie, dass ```@servername``` erforderlich ist.
> - Verwenden Sie ```user= adminusername``` ohne ```@servername``` für flexible Server.

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

Sie verwenden die Verbindungszeichenfolge als GitHub-Geheimnis.

## <a name="configure-the-github-secrets"></a>Konfigurieren der GitHub-Geheimnisse

1. Suchen Sie auf [GitHub](https://github.com/) nach Ihrem Repository.

1. Wählen Sie **Settings > Secrets > New secret** (Einstellungen > Geheimnisse > Neues Geheimnis) aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

    Wenn Sie die Workflowdatei später konfigurieren, verwenden Sie das Geheimnis für die Eingabe `creds` der Azure-Anmeldeaktion. Beispiel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Wählen Sie erneut **New secret** (Neues Geheimnis) aus.

1. Fügen Sie den Wert der Verbindungszeichenfolge in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_POSTGRESQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Hinzufügen des Workflows

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen).

2. Wählen Sie **Set up your workflow yourself** (Workflow selbst einrichten) aus.

2. Löschen Sie alles nach dem Abschnitt `on:` Ihrer Workflowdatei. Der verbleibende Workflow könnte beispielsweise wie folgt aussehen:

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Benennen Sie den Workflow in `PostgreSQL for GitHub Actions` um, und fügen Sie die Aktionen zum Auschecken und Anmelden hinzu. Diese Aktionen checken Ihren Websitecode aus und authentifizieren Sie mithilfe des zuvor erstellten GitHub-Geheimnisses `AZURE_CREDENTIALS` mit Azure.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Verwenden Sie die Aktion für die Azure PostgreSQL-Bereitstellung, um eine Verbindung mit Ihrer PostgreSQL-Instanz herzustellen. Ersetzen Sie `POSTGRESQL_SERVER_NAME` durch den Namen Ihres Servers. Sie sollten über eine PostgreSQL-Datendatei namens `data.sql` auf der Stammebene Ihres Repositorys verfügen.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Vervollständigen Sie Ihren Workflow, indem Sie eine Aktion zum Abmelden von Azure hinzufügen. Hier sehen Sie den fertigen Workflow: Die Datei wird im Ordner `.github/workflows` Ihres Repositorys angezeigt.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Überprüfen der Bereitstellung

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen).

1. Öffnen Sie das erste Ergebnis, um ausführliche Protokolle zur Ausführung des Workflows anzuzeigen.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Protokoll zur Ausführung der GitHub-Aktionen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihre Azure PostgreSQL-Datenbank und das Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Weitere Informationen zum Herstellen einer Verbindung mit dem Server](how-to-connect-query-guide.md)

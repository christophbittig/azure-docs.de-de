---
title: 'Schnellstart: Herstellen einer Verbindung mit Azure SQL-Datenbank mithilfe von GitHub Actions'
description: Verwenden von Azure SQL über einen GitHub Actions-Workflow
author: juliakm
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.topic: quickstart
ms.author: jukullam
ms.date: 05/05/2021
ms.custom: github-actions-azure
ms.reviewer: mathoma
ms.openlocfilehash: 5da39e966165dea12934d0e2da6077a135feb554
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087284"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Herstellen einer Verbindung mit Azure SQL-Datenbank mithilfe von GitHub Actions

Verwenden Sie als Einstieg in [GitHub Actions](https://docs.github.com/en/actions) einen Workflow zum Bereitstellen von Datenbankupdates für [Azure SQL-Datenbank](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes: 
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Repository mit einem DACPAC-Paket (`Database.dacpac`). Wenn Sie kein GitHub-Konto besitzen, können Sie sich [kostenlos registrieren](https://github.com/join).  
- Azure SQL-Datenbank.
    - [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](single-database-create-quickstart.md)
    - [Erstellen eines DACPAC-Pakets aus der vorhandenen SQL Server-Datenbank](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein GitHub Actions-Workflow wird durch eine YAML-Datei (.yml) im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus zwei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Bereitstellen** | 1. Bereitstellen der Datenbank |

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

Ersetzen Sie die Platzhalter `server-name` durch den Namen Ihrer in Azure gehosteten SQL Server-Instanz. Ersetzen Sie `subscription-id` und `resource-group` durch die Abonnement-ID und die Ressourcengruppe, die mit Ihrer SQL Server-Instanz verbunden sind.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor 
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} 
                            --sdk-auth
```

Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie in diesem Beispiel Zugriff auf Ihre Datenbank gewähren. Kopieren Sie das JSON-Ausgabeobjekt zur späteren Verwendung.

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

## <a name="copy-the-sql-connection-string"></a>Kopieren der SQL-Verbindungszeichenfolge 

Wechseln Sie im Azure-Portal zur Azure SQL-Datenbank, und öffnen Sie **Einstellungen** > **Verbindungszeichenfolgen**. Kopieren Sie die **ADO.NET**-Verbindungszeichenfolge. Ersetzen Sie die Platzhalterwerte für `your_database` und `your_password`. Die Verbindungszeichenfolge ähnelt der folgenden Ausgabe. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
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

1. Fügen Sie den Wert der Verbindungszeichenfolge in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_SQL_CONNECTION_STRING`.


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

1. Benennen Sie den Workflow in `SQL for GitHub Actions` um, und fügen Sie die Aktionen zum Auschecken und Anmelden hinzu. Durch diese Aktionen wird Ihr Websitecode ausgecheckt, und Sie werden mithilfe des zuvor erstellten GitHub-Geheimnisses `AZURE_CREDENTIALS` bei Azure authentifiziert. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Verwenden Sie die Aktion für die Azure SQL-Bereitstellung, um eine Verbindung mit Ihrer SQL-Instanz herzustellen. Ersetzen Sie `SQL_SERVER_NAME` durch den Namen Ihres Servers. Sie sollten über ein DACPAC-Paket (`Database.dacpac`) auf der Stammebene Ihres Repositorys verfügen. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        dacpac-package: './Database.dacpac'
    ``` 

1. Vervollständigen Sie Ihren Workflow, indem Sie eine Aktion zum Abmelden von Azure hinzufügen. Hier sehen Sie den fertigen Workflow: Die Datei wird im Ordner `.github/workflows` Ihres Repositorys angezeigt.

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        dacpac-package: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Überprüfen der Bereitstellung

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen). 

1. Öffnen Sie das erste Ergebnis, um ausführliche Protokolle zur Ausführung des Workflows anzuzeigen. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Protokoll zur Ausführung der GitHub-Aktionen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihre Azure SQL-Datenbank und das Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)

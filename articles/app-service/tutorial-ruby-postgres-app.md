---
title: 'Tutorial: Linux-Ruby-App mit PostgresSQL'
description: Hier erfahren Sie, wie Sie eine Linux-Ruby-App in Azure App Service ausführen, die mit einer PostgreSQL-Datenbank in Azure verbunden ist. In diesem Tutorial wird Rails verwendet.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: 0b92ff0b8e7bc6421e40e439deb44c8c8454b8db
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224327"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Erstellen einer Ruby- und Postgres-App in Azure App Service für Linux

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Tutorial wird gezeigt, wie Sie eine Ruby-App erstellen und mit einer PostgreSQL-Datenbank verbinden. Nachdem Sie diese Schritte ausgeführt haben, verfügen Sie über eine [Ruby on Rails](https://rubyonrails.org/)-App, die in App Service unter Linux ausgeführt wird.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Screenshot: Ruby on Rails-App-Beispiel mit dem Titel „Aufgaben“":::

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer PostgreSQL-Datenbank in Azure
> * Herstellen einer Verbindung zwischen einer Ruby on Rails-App und PostgreSQL
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Diagnoseprotokollen aus Azure
> * Verwalten der App im Azure-Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Installation von Git](https://git-scm.com/)
- [Installieren von Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
- [Installieren von Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [Laden Sie PostgreSQL herunter, und führen Sie es aus.](https://www.postgresql.org/download/)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="prepare-local-postgres"></a>Vorbereiten der lokalen Postgres-Instanz

In diesem Schritt erstellen Sie eine Datenbank auf einem lokalen Postgres-Server für die Verwendung in diesem Tutorial.

### <a name="connect-to-local-postgres-server"></a>Herstellen einer Verbindung mit dem lokalen Postgres-Server

1. Öffnen Sie das Terminalfenster, und führen Sie `psql` aus, um eine Verbindung mit Ihrem lokalen Postgres-Server herzustellen.

    ```bash
    sudo -u postgres psql
    ```

    Wenn die Verbindung hergestellt wurde, wird die Postgres-Datenbank ausgeführt. Ist dies nicht der Fall, stellen Sie sicher, dass die lokale Postgres-Datenbank gestartet wurde, indem Sie die Schritte unter [Downloads: PostgreSQL Core Distribution](https://www.postgresql.org/download/) ausführen.

1. Geben Sie `\q` ein, um den Postgres-Client zu beenden. 

1. Erstellen Sie einen Postgres Benutzer, der Datenbanken erstellen kann. Führen Sie dazu unter Verwendung des angemeldeten Linux-Benutzernamenn den folgenden Befehl aus:

    ```bash
    sudo -u postgres createuser -d <signed-in-user>
    ```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Lokales Erstellen einer Ruby on Rails-App
In diesem Schritt rufen Sie eine Ruby on Rails-Beispielanwendung ab, konfigurieren ihre Datenbankverbindung und führen sie lokal aus. 

### <a name="clone-the-sample"></a>Klonen des Beispiels

1. Wechseln Sie im Terminalfenster mit `cd` in ein Arbeitsverzeichnis.

1. Klonen Sie das Beispielrepository, und wechseln Sie zum Repositorystamm.

    ```bash
    git clone https://github.com/Azure-Samples/rubyrails-tasks.git
    cd rubyrails-tasks
    ```

1. Stellen Sie sicher, dass der Standardbranch `main` ist.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > Die Änderung des Branchnamens ist für App Service nicht erforderlich. Da aber viele Repositorys ihren Standardbranch in `main` ändern, erfahren Sie in diesem Tutorial auch, wie Sie ein Repository aus `main` bereitstellen. Weitere Informationen finden Sie unter [Ändern des Bereitstellungsbranchs](deploy-local-git.md#change-deployment-branch).

1. Installieren Sie die erforderlichen Pakete.

    ```bash
    bundle install --path vendor/bundle
    ```

### <a name="run-the-sample-locally"></a>Lokales Ausführen des Beispiels

1. Führen Sie [die Rails-Migrationen](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) aus, um die von der Anwendung benötigten Tabellen zu erstellen. Im Git-Repository können Sie im Verzeichnis _db/migrate_ sehen, welche Tabellen bei den Migrationen erstellt werden.

    ```bash
    rake db:create
    rake db:migrate
    ```

1. Führen Sie die Anwendung aus.

    ```bash
    rails server
    ```

1. Navigieren Sie in einem Browser zu `http://localhost:3000`. Fügen Sie auf der Seite einige Aufgaben hinzu.

    ![Erfolgreiche Verbindung zwischen Ruby on Rails und Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

1. Um den Rails-Server zu beenden, geben Sie `Ctrl + C` im Terminal ein.

## <a name="create-postgres-in-azure"></a>Erstellen einer Postgres-Instanz in Azure

In diesem Schritt erstellen Sie eine Postgres-Datenbank in [Azure Database for PostgreSQL](../postgresql/index.yml). Später konfigurieren Sie die Ruby on Rails-Anwendung für eine Verbindung mit dieser Datenbank.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Erstellen einer Postgres-Datenbankinstanz in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

1. Installieren Sie mit folgendem Befehl die `db-up`-Erweiterung:

    ```azurecli
    az extension add --name db-up
    ```

1. Erstellen Sie die Postgres-Datenbankinstanz wie im folgenden Beispiel gezeigt mit dem Befehl [`az postgres up`](/cli/azure/postgres#az_postgres_up) in Azure. Ersetzen Sie *\<postgresql-name>* durch einen *eindeutigen* Namen (der Serverendpunkt ist *https://\<postgresql-name>.postgres.database.azure.com*). Geben Sie für *\<admin-username>* und *\<admin-password>* Anmeldeinformationen ein, um einen Administratorbenutzer für diesen Postgres-Server zu erstellen.

    <!-- Issue: without --location -->
    ```azurecli
    az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
    ```

    Dieser Befehl kann einige Zeit in Anspruch nehmen, da er Folgendes bewirkt:
    
    - Er erstellt eine [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology) namens `myResourceGroup`, wenn diese noch nicht vorhanden ist. Jede Azure-Ressource muss sich in einer dieser Ressourcengruppen befinden. `--resource-group` ist optional.
    - Er erstellt eine Postgres-Serverinstanz mit dem Administratorbenutzer.
    - Er erstellt eine `sampledb`-Datenbank.
    - Er ermöglicht den Zugriff über Ihre lokale IP-Adresse.
    - Er ermöglicht den Zugriff über Azure-Dienste.
    - Er erstellt einen neuen Datenbankbenutzer mit Zugriff auf die `sampledb`-Datenbank.
    
    Sie können alle Schritte separat mit anderen `az postgres`-Befehlen und `psql` ausführen, aber `az postgres up` führt alle in einem Schritt für Sie aus.
    
    Wenn der Befehl abgeschlossen ist, suchen Sie die Ausgabezeilen, die mit `Ran Database Query:` beginnen. Sie zeigen den Datenbankbenutzer, der für Sie erstellt wurde, mit Benutzernamen `root` und Kennwort `Sampledb1` an. Sie werden sie später verwenden, um die Verbindung Ihrer App mit der Datenbank herzustellen.
    
    <!-- not all locations support az postgres up -->
    > [!TIP]
    > `--location <location-name>`: Kann auf eine beliebige [Azure-Region](https://azure.microsoft.com/global-infrastructure/regions/) festgelegt werden. Sie können die Regionen, die für Ihr Abonnement verfügbar sind, mit dem [`az account list-locations`](/cli/azure/account#az_account_list_locations)-Befehl abrufen. Legen Sie bei Produktions-Apps für Ihre Datenbank und Ihre App den gleichen Standort fest.
    
## <a name="connect-app-to-azure-postgres"></a>Herstellen einer Verbindung zwischen der App und Azure Postgres

In diesem Schritt stellen Sie eine Verbindung zwischen der Ruby on Rails-Anwendung und der Postgres-Datenbank her, die Sie in Azure Database for PostgreSQL erstellt haben.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurieren der Datenbankverbindung

Öffnen Sie im Repository die Datei _config/database.yml_. Ersetzen Sie am Ende der Datei die Produktionsvariablen durch den folgenden Code. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Speichern Sie die Änderungen.

### <a name="test-the-application-locally"></a>Lokales Testen der Anwendung

1. Legen Sie im lokalen Terminal die folgenden Umgebungsvariablen fest:

    ```bash
    export DB_HOST=<postgres-server-name>.postgres.database.azure.com
    export DB_DATABASE=sampledb 
    export DB_USERNAME=root@<postgres-server-name>
    export DB_PASSWORD=Sampledb1
    ```

1. Führen Sie die Rails-Datenbankmigrationen mit den Produktionswerten aus, die Sie soeben konfiguriert haben, um die Tabellen in Ihrer Postgres-Datenbank in Azure Database for PostgreSQL zu erstellen.

    ```bash
    rake db:migrate RAILS_ENV=production
    ```

1. Bei Ausführung in der Produktionsumgebung benötigt die Rails-Anwendung vorkompilierte Ressourcen. Generieren Sie die erforderlichen Ressourcen mit dem folgenden Befehl:

    ```bash
    rake assets:precompile
    ```

1. In der Rails-Produktionsumgebung werden auch geheime Schlüssel zum Verwalten der Sicherheit verwendet. Generieren Sie einen geheimen Schlüssel.

    ```bash
    rails secret
    ```

1. Speichern Sie den geheimen Schlüssel für die jeweiligen Variablen, die von der Rails-Produktionsumgebung verwendet werden. Verwenden Sie der Einfachheit halber für beide Variablen den gleichen Schlüssel.

    ```bash
    export RAILS_MASTER_KEY=<output-of-rails-secret>
    export SECRET_KEY_BASE=<output-of-rails-secret>
    ```

1. Aktivieren Sie die Rails-Produktionsumgebung für die Bereitstellung von JavaScript- und CSS-Dateien.

    ```bash
    export RAILS_SERVE_STATIC_FILES=true
    ```

1. Führen Sie die Beispielanwendung in der Produktionsumgebung aus.

    ```bash
    rails server -e production
    ```

1. Navigieren Sie zu `http://localhost:3000`. Wenn die Seite ohne Fehler geladen wird, stellt die Ruby on Rails-Anwendung eine Verbindung mit der Postgres-Datenbank in Azure her.

1. Fügen Sie auf der Seite einige Aufgaben hinzu.

    ![Erfolgreiche Verbindung zwischen Ruby on Rails und Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

1. Um den Rails-Server zu beenden, geben Sie `Ctrl + C` im Terminal ein.

### <a name="commit-your-changes"></a>Committen Ihrer Änderungen

1. Führen Sie die folgenden Git-Befehle aus, um für Ihre Änderungen einen Commit durchzuführen:

    ```bash
    git add .
    git commit -m "database.yml updates"
    ```

Ihre App kann jetzt bereitgestellt werden.

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

In diesem Schritt stellen Sie die mit Postgres verbundene Rails-Anwendung in Azure App Service bereit.

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Erstellen einer Web-App

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurieren der Datenbankeinstellungen

Verwenden Sie in App Service den Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) in Cloud Shell, um Umgebungsvariablen als _App-Einstellungen_ festzulegen.

Mit dem folgenden Cloud Shell-Befehl werden die App-Einstellungen `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` und `DB_PASSWORD` konfiguriert. Ersetzen Sie die Platzhalter _&lt;appname>_ und _&lt;postgres-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Konfigurieren der Rails-Umgebungsvariablen

1. [Generieren Sie im lokalen Terminal ein neues Geheimnis](configure-language-ruby.md#set-secret_key_base-manually) für die Rails-Produktionsumgebung in Azure.

    ```bash
    rails secret
    ```

1. Ersetzen Sie im folgenden Cloud Shell-Befehl die beiden _&lt;output-of-rails-secret>_ -Platzhalter durch den neuen geheimen Schlüssel, den Sie im lokalen Terminal generiert haben.

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
    ```

    `ASSETS_PRECOMPILE="true"` weist den Ruby-Standardcontainer an, bei jeder Git-Bereitstellung Ressourcen vorzukompilieren. Weitere Informationen finden Sie unter [Vorkompilieren von Ressourcen](configure-language-ruby.md#precompile-assets) und [Bereitstellen von statischen Ressourcen](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

1. Da Sie den Branch `main` bereitstellen, müssen Sie den Standardbereitstellungsbranch für Ihre App Service-App auf `main` festlegen (siehe [Ändern des Bereitstellungsbranchs](deploy-local-git.md#change-deployment-branch)). Legen Sie die App-Einstellung `DEPLOYMENT_BRANCH` in Cloud Shell mit dem Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) fest. 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. Fügen Sie im lokalen Terminal Ihrem lokalen Git-Repository eine Azure-Remoteinstanz hinzu.

    ```bash
    git remote add azure <paste-copied-url-here>
    ```

1. Führen Sie einen Pushvorgang an die Azure-Remoteinstanz durch, um die Ruby on Rails-Anwendung bereitzustellen. Sie werden zur Angabe des Kennworts aufgefordert, das Sie zuvor bei der Erstellung des Bereitstellungsbenutzers angegeben haben.

    ```bash
    git push azure main
    ```

    Während der Bereitstellung meldet Azure App Service seinen Status mit Git.

    <pre>
    Counting objects: 3, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
    Total 3 (delta 2), reused 0 (delta 0)
    remote: Updating branch 'main'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'a5e076db9c'.
    remote: Running custom deployment command...
    remote: Running deployment command...
    ...
    &lt; Output has been truncated for readability &gt;
    </pre>
    
### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Browsen Sie zu `http://<app-name>.azurewebsites.net`, und fügen Sie der Liste einige Aufgaben hinzu.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Screenshot: Azure-App-Beispiel mit dem Titel „Aufgaben“, das zeigt, wie der Liste Aufgaben hinzugefügt werden":::

Glückwunsch! Sie führen eine datengesteuerte Ruby on Rails-App in Azure App Service aus.

## <a name="update-model-locally-and-redeploy"></a>Lokales Aktualisieren und erneutes Bereitstellen des Modells

In diesem Schritt nehmen Sie eine einfache Änderung am `task`-Datenmodell und der Web-App vor und veröffentlichen das Update dann in Azure.

Für das tasks-Szenario ändern Sie die Anwendung, damit Sie eine Aufgabe als abgeschlossen kennzeichnen können.

### <a name="add-a-column"></a>Hinzufügen eines Spaltennamens

1. Navigieren Sie im Terminal zum Stamm des Git-Repositorys.

1. Generieren Sie eine neue Migration, die der Tabelle `Tasks` eine boolesche Spalte namens `Done` hinzufügt:

    ```bash
    rails generate migration AddDoneToTasks Done:boolean
    ```

    Mit diesem Befehl wird im Verzeichnis _db/migrate_ eine neue Migrationsdatei generiert.

1. Führen Sie im Terminal Rails-Datenbankmigrationen aus, um die Änderung in der lokalen Datenbank vorzunehmen.

    ```bash
    rake db:migrate
    ```

### <a name="update-application-logic"></a>Aktualisieren der Anwendungslogik

1. Öffnen Sie die Datei *app/controllers/tasks_controller.rb*. Suchen Sie am Ende der Datei nach der folgenden Zeile:

    ```rb
    params.require(:task).permit(:Description)
    ```

1. Ändern Sie diese Zeile so, dass sie den neuen Parameter `Done` enthält.

    ```rb
    params.require(:task).permit(:Description, :Done)
    ```

### <a name="update-the-views"></a>Aktualisieren der Ansichten

1. Öffnen Sie die Datei *app/views/tasks/_form.html.erb*, bei der es sich um das Bearbeitungsformular handelt.

1. Suchen Sie die Zeile `<%=f.error_span(:Description) %>`, und fügen Sie direkt darunter den folgenden Code ein:

    ```erb
    <%= f.label :Done, :class => 'control-label col-lg-2' %>
    <div class="col-lg-10">
      <%= f.check_box :Done, :class => 'form-control' %>
    </div>
    ```

1. Öffnen Sie die Datei *app/views/tasks/show.html.erb*, bei der es sich um die Ansichtsseite mit einem Datensatz handelt. 

    Suchen Sie die Zeile `<dd><%= @task.Description %></dd>`, und fügen Sie direkt darunter den folgenden Code ein:

    ```erb
      <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
      <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
    ```

    Öffnen Sie die Datei *app/views/tasks/index.html.erb*, bei der es sich um die Indexseite für alle Datensätze handelt.

    Suchen Sie die Zeile `<th><%= model_class.human_attribute_name(:Description) %></th>`, und fügen Sie direkt darunter den folgenden Code ein:

    ```erb
    <th><%= model_class.human_attribute_name(:Done) %></th>
    ```

1. Suchen Sie in derselben Datei die Zeile `<td><%= task.Description %></td>`, und fügen Sie direkt darunter den folgenden Code ein:

    ```erb
    <td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
    ```

### <a name="test-the-changes-locally"></a>Lokales Testen der Änderungen

1. Führen Sie im lokalen Terminal den Rails-Server aus.

    ```bash
    rails server
    ```

1. Navigieren Sie zu `http://localhost:3000`, um die Änderung des Aufgabenstatus anzuzeigen, und fügen Sie Elemente hinzu, oder bearbeiten Sie die Elemente.

    ![Hinzugefügtes Kontrollkästchen in der Aufgabe](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

1. Um den Rails-Server zu beenden, geben Sie `Ctrl + C` im Terminal ein.

### <a name="publish-changes-to-azure"></a>Veröffentlichen von Änderungen in Azure

1. Führen Sie im Terminal Rails-Datenbankmigrationen für die Produktionsumgebung aus, um die Änderung in der Azure-Datenbank vorzunehmen.

    ```bash
    rake db:migrate RAILS_ENV=production
    ```

1. Führen Sie für alle Änderungen in Git einen Commit aus, und übertragen Sie dann die Codeänderungen per Pushvorgang an Azure.

    ```bash
    git add .
    git commit -m "added complete checkbox"
    git push azure main
    ```

1. Wechseln Sie nach Abschluss des `git push`-Vorgangs zur Azure-App, und testen Sie die neuen Funktionen.

    ![Auf Azure veröffentlichte Änderungen an Modell und Datenbank](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

    Wenn Sie Aufgaben hinzugefügt haben, werden sie in der Datenbank beibehalten. Bei Updates des Datenschemas bleiben vorhandene Daten erhalten.

## <a name="stream-diagnostic-logs"></a>Streamen von Diagnoseprotokollen

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Verwalten der Azure-App

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um die erstellte App zu verwalten.

1. Klicken Sie im linken Menü auf **App Services** und anschließend auf den Namen Ihrer Azure-App.

    ![Portalnavigation zur Azure-App](./media/tutorial-php-mysql-app/access-portal.png)

    Die Übersichtsseite Ihrer App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Beenden, Starten, Neustarten, Durchsuchen und Löschen durchführen.

    Im linken Menü werden Seiten für die Konfiguration Ihrer App angezeigt.

    ![App Service-Seite im Azure-Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Postgres-Datenbank in Azure
> * Herstellen einer Verbindung zwischen einer Ruby on Rails-App und Postgres
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Diagnoseprotokollen aus Azure
> * Verwalten der App im Azure-Portal

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Tutorial: Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer App](app-service-web-tutorial-custom-domain.md)

Oder sehen Sie sich weitere Ressourcen an:

> [!div class="nextstepaction"]
> [Konfigurieren der Ruby-App](configure-language-ruby.md)

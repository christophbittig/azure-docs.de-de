---
title: 'Tutorial: Verwenden des Dienstconnectors zum Erstellen einer Django-App mit Postgres für Azure App Service'
description: Hier erfahren Sie, wie Sie eine Python-Web-App mit einer PostgreSQL-Datenbank erstellen und in Azure bereitstellen. In diesem Tutorial wird das Django-Framework verwendet. Die App wird in Azure App Service für Linux gehostet, und die App Service-Instanz und Datenbank sind mit dem Dienstconnector verbunden.
ms.devlang: python
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
zone_pivot_groups: postgres-server-options
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a3307cfea56d18cbbe0186cd3c927fafdd4074
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853031"
---
# <a name="tutorial-using-service-connector-preview-to-build-a-django-app-with-postgres-on-azure-app-service"></a>Tutorial: Verwenden des Dienstconnectors (Vorschauversion) zum Erstellen einer Django-App mit Postgres für Azure App Service

> [!NOTE]
> In diesem Tutorial verwenden Sie den Dienstconnector (Vorschauversion), mit dem Sie Ihre Web-App einfacher mit dem Datenbankdienst verbinden können. Das Tutorial hier ist eine Abwandlung des [Tutorials zu App Service](../app-service/tutorial-python-postgresql-app.md) mit dem Zweck, dieses Vorschaufeature zu nutzen, damit Sie Ähnlichkeiten erkennen können. In Abschnitt [4.2 Konfigurieren von Umgebungsvariablen zum Herstellen einer Verbindung mit der Datenbank](#42-configure-environment-variables-to-connect-the-database) in diesem Tutorial erfahren Sie, wo der Dienstconnector ins Spiel kommt und den im App Service-Tutorial beschriebenen Verbindungsaufbau vereinfacht.

::: zone pivot="postgres-single-server"

In diesem Tutorial erfahren Sie, wie Sie eine datengesteuerte Python-[Django](https://www.djangoproject.com/)-Web-App in [Azure App Service](overview.md) bereitstellen und mit einer Azure Database for Postgres-Datenbank verbinden. Sie können PostgresSQL Flexible Server (Vorschau) auch ausprobieren, indem Sie die oben genannte Option auswählen. Flexible Server bietet einen einfacheren Bereitstellungsmechanismus und niedrigere laufende Kosten.

In diesem Tutorial wird die Azure CLI verwendet, um folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Einrichten der anfänglichen Umgebung mit Python und der Azure CLI
> * Erstellen einer Azure Database for PostgreSQL-Datenbankinstanz
> * Bereitstellen von Code für Azure App Service und Herstellen einer Verbindung mit PostgresSQL
> * Aktualisieren Ihres Codes und erneutes Bereitstellen
> * Anzeigen von Diagnoseprotokollen
> * Verwalten der Web-App im Azure-Portal

:::zone-end

::: zone pivot="postgres-flexible-server"

In diesem Tutorial wird veranschaulicht, wie Sie eine datengesteuerte Python-Web-App ([Django](https://www.djangoproject.com/)) für [Azure App Service](overview.md) bereitstellen und mit einer [Datenbankinstanz von Azure Database for PostgreSQL – Flexibler Server (Vorschau)](../postgresql/flexible-server/index.yml) verbinden. Wenn Sie PostgreSQL Flexible Server (Vorschau) nicht verwenden können, wählen Sie oben die Option „Einzelserver“ aus. 

In diesem Tutorial wird die Azure CLI verwendet, um folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Einrichten der anfänglichen Umgebung mit Python und der Azure CLI
> * Erstellen einer Datenbank mit Azure Database for PostgreSQL – Flexibler Server
> * Bereitstellen von Code für Azure App Service und Herstellen einer Verbindung mit PostgreSQL – Flexibler Server
> * Aktualisieren Ihres Codes und erneutes Bereitstellen
> * Anzeigen von Diagnoseprotokollen
> * Verwalten der Web-App im Azure-Portal


:::zone-end

## <a name="1-set-up-your-initial-environment"></a>1. Einrichten der anfänglichen Umgebung

1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installieren Sie <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 oder höher</a>.
1. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.

Öffnen Sie ein Terminalfenster, und überprüfen Sie, ob mindestens die Python-Version 3.6 installiert ist:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Vergewissern Sie sich, dass Sie mindestens Version 2.30.0 der Azure CLI verwenden:

```azurecli
az --version
```

Wenn Sie ein Upgrade durchführen müssen, versuchen Sie es mit dem Befehl `az upgrade` (erfordert mindestens Version 2.30.0), oder lesen Sie die Informationen unter <a href="/cli/azure/install-azure-cli" target="_blank">Installieren der Azure CLI</a>.

Melden Sie sich anschließend über die CLI bei Azure an:

```azurecli
az login
```

Dieser Befehl öffnet einen Browser zum Erfassen Ihrer Anmeldeinformationen. Wenn der Befehl abgeschlossen ist, wird eine JSON-Ausgabe mit Informationen zu Ihren Abonnements angezeigt.

Nachdem Sie sich angemeldet haben, können Sie Azure-Befehle mit der Azure CLI ausführen, um Ressourcen in Ihrem Abonnement zu verwenden.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="2-clone-or-download-the-sample-app"></a>2. Klonen oder Herunterladen der Beispiel-App

# <a name="git-clone"></a>[Git-Klon](#tab/clone)

Klonen Sie das Beispielrepository:

```terminal
git clone https://github.com/Azure-Samples/serviceconnector-webapp-postgresql-django.git
```

Navigieren Sie anschließend in diesen Ordner:

```terminal
cd serviceconnector-webapp-postgresql-django
```

::: zone pivot="postgres-flexible-server"

Verwenden Sie für Flexibler Server (Vorschau) den Branch „flexible-server“ des Beispiels, der einige erforderliche Änderungen enthält, z. B. die Art und Weise, wie die URL des Datenbankservers festgelegt wird, und mit dem `'OPTIONS': {'sslmode': 'require'}` der Django-Datenbankkonfiguration gemäß den Anforderungen von Azure PostgreSQL – Flexibler Server hinzugefügt wird.

```terminal
git checkout flexible-server
```

::: zone-end

# <a name="download"></a>[Download](#tab/download)

Besuchen Sie [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp).

::: zone pivot="postgres-flexible-server"
Wählen Sie für Flexibler Server (Vorschau) das Branchsteuerelement mit der Bezeichnung „master“ aus, und wählen Sie stattdessen den Branch „flexible-server“ aus.
::: zone-end

Wählen Sie **Clone** (Klonen) und anschließend die Option **Download ZIP** (ZIP herunterladen) aus. 

Entpacken Sie die ZIP-Datei in einem Ordner namens *djangoapp*. 

Öffnen Sie dann ein Terminalfenster im Ordner *djangoapp*.

---

Das Beispiel „djangoapp“ enthält die datengesteuerte Django-Umfrage-App, die Sie erhalten, indem Sie die in der Django-Dokumentation unter [Schreiben Ihrer ersten Django-App](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) beschriebenen Schritte ausführen. Der Einfachheit halber wird Ihnen hier die fertige App zur Verfügung gestellt.

Das Beispiel wird außerdem für die Ausführung in einer Produktionsumgebung wie App Service angepasst:

- Die Produktionseinstellungen befinden sich in der Datei *azuresite/production.py*. Die Entwicklungseinstellungen befinden sich in *azuresite/settings.py*.
- Von der App werden Produktionseinstellungen verwendet, wenn die Umgebungsvariable `WEBSITE_HOSTNAME` festgelegt ist. Von Azure App Service wird diese Variable automatisch auf die URL der Web-App festgelegt, z. B. `msdocs-django.azurewebsites.net`.

Die Produktionseinstellungen gelten nicht speziell für App Service, sondern ermöglichen die Konfiguration von Django für die Ausführung in einer beliebigen Produktionsumgebung. Weitere Informationen finden Sie in der [Bereitstellungsprüfliste für Django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Ausführliche Informationen zu einigen Änderungen finden Sie unter [Produktionseinstellungen für Django in Azure](../app-service/configure-language-python.md#production-settings-for-django-apps).

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="3-create-postgres-database-in-azure"></a>3. Erstellen einer Postgres-Datenbankinstanz in Azure

::: zone pivot="postgres-single-server"
<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Aktivieren Sie das Zwischenspeichern von Parametern mit der Azure-Befehlszeilenschnittstelle, damit Sie diese Parameter nicht bei jedem Befehl angeben müssen. (Zwischengespeicherte Werte werden im Ordner *.azure* gespeichert.)

```azurecli
az config param-persist on 
```

Installieren Sie die Erweiterung `db-up` für die Azure CLI:

```azurecli
az extension add --name db-up
```

Wird der Befehl `az` nicht erkannt, vergewissern Sie sich, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](#1-set-up-your-initial-environment) beschrieben installiert wurde.

Erstellen Sie anschließend mithilfe des Befehls [`az postgres up`](/cli/azure/postgres#az_postgres_up) die Postgres-Datenbank in Azure:

```azurecli
az postgres up --resource-group ServiceConnector-tutorial-rg --location eastus --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Ersetzen** Sie *\<postgres-server-name>* durch einen **innerhalb von Azure eindeutigen** Namen. (Der Serverendpunkt ist `https://<postgres-server-name>.postgres.database.azure.com`.) Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einem anderen eindeutigen Wert.
- Geben Sie für *\<admin-username>* und *\<admin-password>* Anmeldeinformationen ein, um einen Administratorbenutzer für diesen Postgres-Server zu erstellen. Der Administratorbenutzername darf nicht *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* oder *public* lauten. Er kann nicht mit *pg_* beginnen. Das Kennwort muss **8 bis 128 Zeichen** aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (z. B. !, #, %). Das Kennwort darf nicht den Benutzernamen enthalten.
- Verwenden Sie das Zeichen `$` nicht im Benutzernamen oder Kennwort. Später erstellen Sie Umgebungsvariablen mit diesen Werten. Dabei hat das Zeichen `$` innerhalb des Linux-Containers, der zum Ausführen von Python-Apps verwendet wird, eine besondere Bedeutung.
- Der hier verwendete [Tarif](../postgresql/concepts-pricing-tiers.md) „B_Gen5_1“ (Basic, 5. Generation, ein Kern) ist der günstigste. Lassen Sie bei Produktionsdatenbanken das Argument `--sku-name` weg, um stattdessen den Tarif „GP_Gen5_2“ (Universell, 5. Generation, zwei Kerne) zu verwenden.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen einer [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology) namens `ServiceConnector-tutorial-rg` (sofern noch nicht vorhanden)
- Erstellen eines nach dem Argument `--server-name` benannten Postgres-Servers
- Erstellen eines Administratorkontos mithilfe der Argumente `--admin-user` und `--admin-password`. Sie können diese Argumente weglassen, damit der Befehl eindeutige Anmeldeinformationen für Sie generiert.
- Erstellen einer nach dem Argument `--database-name` benannten `pollsdb`-Datenbank
- Ermöglichen des Zugriffs über Ihre lokale IP-Adresse
- Ermöglichen des Zugriffs über Azure-Dienste
- Erstellen eines neuen Datenbankbenutzers mit Zugriff auf die Datenbank `pollsdb`

Die Schritte können zwar auch separat mit anderen Befehlen vom Typ `az postgres` und `psql` ausgeführt werden, mit `az postgres up` werden jedoch alle Schritte gemeinsam ausgeführt.

Nach Abschluss der Befehlsausführung wird ein JSON-Objekt mit verschiedenen Verbindungszeichenfolgen für die Datenbank sowie mit der Server-URL, einem generierten Benutzernamen (beispielsweise „joyfulKoala@msdocs-djangodb-12345“) und einem GUID-Kennwort zurückgegeben. **Kopieren Sie den Benutzernamen und das Kennwort zur späteren Verwendung in diesem Tutorial in eine temporäre Textdatei.**

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`: Kann auf eine beliebige [Azure-Region](https://azure.microsoft.com/global-infrastructure/regions/) festgelegt werden. Sie können die Regionen, die für Ihr Abonnement verfügbar sind, mit dem [`az account list-locations`](/cli/azure/account#az_account_list_locations)-Befehl abrufen. Legen Sie bei Produktions-Apps für Ihre Datenbank und Ihre App den gleichen Standort fest.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. Aktivieren Sie das Zwischenspeichern von Parametern mit der Azure-Befehlszeilenschnittstelle, damit Sie diese Parameter nicht bei jedem Befehl angeben müssen. (Zwischengespeicherte Werte werden im Ordner *.azure* gespeichert.)

    ```azurecli
    az config param-persist on 
    ```

1. Erstellen Sie eine [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology). (Sie können den Namen bei Bedarf ändern.) Der Ressourcengruppenname wird zwischengespeichert und automatisch auf nachfolgende Befehle angewandt.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg --location eastus
    ```

1. Erstellen Sie den Datenbankserver (der Vorgang dauert einige Minuten):

    ```azurecli
    az postgres flexible-server create --sku-name Standard_B1ms --public-access all
    ```
    
    Wird der Befehl `az` nicht erkannt, vergewissern Sie sich, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](#1-set-up-your-initial-environment) beschrieben installiert wurde.
    
    Der Befehl [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) führt die folgenden Aktionen aus, die einige Minuten in Anspruch nehmen:
    
    - Erstellen Sie eine Standardressourcengruppe, wenn noch kein zwischengespeicherter Name vorhanden ist.
    - Erstellen Sie eine Instanz von PostgreSQL – Flexibler Server:
        - Standardmäßig verwendet der Befehl einen generierten Namen wie `server383813186`. Sie können mit dem Parameter `--name` einen eigenen Namen angeben. Der Name muss in ganz Azure eindeutig sein.
        - Der Befehl verwendet den kostengünstigsten Tarif `Standard_B1ms`. Lassen Sie das Argument `--sku-name` aus, um den Standardtarif `Standard_D2s_v3` zu verwenden.
        - Der Befehl verwendet die Ressourcengruppe und den Speicherort, die im vorherigen Befehl `az group create` zwischengespeichert wurden. In diesem Beispiel handelt es sich dabei um die Ressourcengruppe `ServiceConnector-tutorial-rg` in der Region `eastus`.
    - Erstellen Sie ein Administratorkonto mit einem Benutzernamen und einem Kennwort. Sie können diese Werte direkt in den Parametern `--admin-user` und `--admin-password` angeben.
    - Erstellen Sie eine Datenbank mit der Standardbezeichnung `flexibleserverdb`. Mit dem Parameter `--database-name` können Sie einen eigenen Datenbanknamen angeben.
    - Aktivieren Sie den vollständigen öffentlichen Zugriff, den Sie mithilfe des Parameters `--public-access` steuern können.
    
1. Kopieren Sie nach Abschluss des Befehls die **JSON-Ausgabe des Befehls in eine Datei**, da Sie die Werte aus der Ausgabe, insbesondere Host, Benutzername und Kennwort sowie den Datenbanknamen, später in diesem Tutorial benötigen.

::: zone-end

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Bereitstellen des Codes in Azure App Service

In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App, verbinden die App mit der Postgres-Datenbank und stellen anschließend Ihren Code auf dem Host bereit.

### <a name="41-create-the-app-service-app"></a>4.1 Erstellen der App Service-App

::: zone pivot="postgres-single-server"

Vergewissern Sie sich im Terminal, dass Sie sich im Repositoryordner *djangoapp* befinden, der den App-Code enthält.

Erstellen Sie mithilfe des Befehls [`az webapp up`](/cli/azure/webapp#az_webapp_up) eine App Service-App (den Hostprozess):

```azurecli
az webapp up --resource-group ServiceConnector-tutorial-rg --location eastus --plan ServiceConnector-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Achten Sie beim `--location`-Argument darauf, einen Standort zu verwenden, der vom [Dienstconnector unterstützt](concept-region-support.md) wird.
- **Ersetzen** Sie *\<app-name>* durch einen innerhalb von Azure eindeutigen Namen. (Der Serverendpunkt ist `https://<app-name>.azurewebsites.net`.) Zulässige Zeichen für *\<app-name>* sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Erstellen einer [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology) (sofern noch nicht vorhanden). Verwenden Sie in diesem Befehl die gleiche Ressourcengruppe, in der Sie zuvor schon die Datenbank erstellt haben.
- Erstellen des [App Service-Plans](../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* im Basic-Tarif (B1), sofern er nicht vorhanden ist. `--plan` und `--sku` sind optional.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung
- Zwischenspeichern allgemeiner Parameter wie dem Namen der Ressourcengruppe und dem App Service-Plan in der Datei *.azure/config*. Daher müssen bei späteren Befehlen nicht noch mal die gleichen Parameter angegeben werden. Wenn Sie die App also beispielsweise erneut bereitstellen möchten, nachdem Sie Änderungen vorgenommen haben, können Sie `az webapp up` einfach ohne Parameter erneut ausführen. Bei Befehlen von CLI-Erweiterungen (beispielsweise `az postgres up`) wird der Cache dagegen aktuell nicht genutzt. Daher müssen hier die Ressourcengruppe und der Standort mit der anfänglichen Verwendung von `az webapp up` angegeben werden.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. Vergewissern Sie sich im Terminal, dass Sie sich im Repositoryordner *djangoapp* befinden, der den App-Code enthält.

1. Wechseln Sie zum Branch `flexible-server` der Beispiel-App. Dieser Branch enthält eine spezifische Konfiguration, die für PostgreSQL – Flexibler Server erforderlich ist:

    ```cmd
    git checkout flexible-server
    ```

1. Führen Sie den folgenden Befehl [`az webapp up`](/cli/azure/webapp#az_webapp_up) aus, um den App Service-Host für die App zu erstellen:

    ```azurecli
    az webapp up --name <app-name> --sku B1 
    ```
    <!-- without --sku creates PremiumV2 plan -->
        
    Dieser Befehl führt die folgenden Aktionen aus, die einige Minuten dauern können. Dabei werden die Ressourcengruppe und der Standort `az group create` (in diesem Beispiel die Gruppe `Python-Django-PGFlex-rg` in der Region `eastus`) verwendet, die im vorherigen Befehl zwischengespeichert wurden.
    
    <!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
    <!-- No it doesn't. az webapp up doesn't respect --resource-group -->
    - Erstellen Sie den [App Service-Plan](../app-service/overview-hosting-plans.md) im Basic-Tarif (B1). Sie können `--sku` auslassen, um die Standardwerte zu verwenden.
    - Erstellen Sie die App Service-App.
    - Aktivieren Sie die Standardprotokollierung für die App.
    - Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung

::: zone-end

Nach erfolgreicher Bereitstellung wird eine JSON-Ausgabe wie im folgenden Beispiel generiert:

![Beispielausgabe des Befehls „az webapp up“](../app-service/media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](../app-service/configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Konfigurieren der Umgebungsvariablen für die Datenbankverbindung

Nachdem der Code nun in App Service bereitgestellt ist, muss als Nächstes eine Verbindung zwischen der App und der Postgres-Datenbank in Azure hergestellt werden.

Vom App-Code werden Datenbankinformationen in vier Umgebungsvariablen namens `AZURE_POSTGRESQL_HOST`, `AZURE_POSTGRESQL_NAME`, `AZURE_POSTGRESQL_USER` und `AZURE_POSTGRESQL_PASS` erwartet.

Um in App Service Umgebungsvariablen festzulegen, erstellen Sie mit dem Befehl [az connection create]() „App-Einstellungen“.

::: zone pivot="postgres-single-server"

```azurecli
az webapp connection create postgres --client-type django
```

Die Ressourcengruppe sowie der App- und Datenbankname werden den zwischengespeicherten Werten entnommen. Sie müssen während der Ausführung dieses Befehls das Administratorkennwort Ihrer Postgres-Datenbank angeben.

- Der Befehl erstellt wie vom App-Code erwartet die Einstellungen AZURE_POSTGRESQL_HOST, AZURE_POSTGRESQL_NAME, AZURE_POSTGRESQL_USER, AZURE_POSTGRESQL_PASS.
- Wenn Sie Ihre Administratoranmeldeinformationen vergessen haben, können Sie sie mit diesem Befehl zurücksetzen.


::: zone-end

::: zone pivot="postgres-flexible-server"
```azurecli
az webapp connection create postgres --client-type django
```

Die Ressourcengruppe sowie der App- und Datenbankname werden den zwischengespeicherten Werten entnommen. Sie müssen während der Ausführung dieses Befehls das Administratorkennwort Ihrer Postgres-Datenbank angeben.

- Der Befehl erstellt wie vom App-Code erwartet die Einstellungen AZURE_POSTGRESQL_HOST, AZURE_POSTGRESQL_NAME, AZURE_POSTGRESQL_USER, AZURE_POSTGRESQL_PASS.
- Wenn Sie Ihre Administratoranmeldeinformationen vergessen haben, können Sie sie mit diesem Befehl zurücksetzen.

::: zone-end

> [!NOTE]
> Wird die Fehlermeldung „Das Abonnement ist nicht für die Verwendung des Ressourcenanbieters "{0}" registriert.“ angezeigt, führen Sie `az provider register -n Microsoft.ServiceLinker` aus, um den Dienstconnector-Ressourcenanbieter zu registrieren. Führen Sie anschließend erneut den Verbindungsbefehl aus. 

In Ihrem Python-Code wird auf diese Einstellungen in Form von Umgebungsvariablen mit Anweisungen wie `os.environ.get('AZURE_POSTGRESQL_HOST')`zugegriffen. Weitere Informationen finden Sie unter [Zugreifen auf Umgebungsvariablen](../app-service/configure-language-python.md#access-environment-variables).

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](../app-service/configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Ausführen einer Django-Datenbankmigration

Durch Django-Datenbankmigrationen wird sichergestellt, dass das Schema in der PostgreSQL-Datenbank in Azure mit den Angaben im Code übereinstimmt.

1. Führen `az webpp ssh` Sie aus, um eine SSH-Sitzung für die Web-App im Browser zu öffnen:

    ```azurecli
    az webapp ssh
    ```


1. Führen Sie in der SSH-Sitzung die folgenden Befehle aus. (Befehle können mithilfe von **STRG**+**UMSCHALT**+**V** eingefügt werden.)

    ```bash
    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Sollten Fehler auftreten, die mit der Herstellung der Datenbankverbindung zusammenhängen, überprüfen Sie die Werte der im vorherigen Abschnitt erstellten Anwendungseinstellungen.

1. Durch den Befehl `createsuperuser` werden Sie zur Eingabe von Superuser-Anmeldeinformationen aufgefordert. Verwenden Sie für dieses Tutorial den Standardbenutzernamen `root`, drücken Sie für die E-Mail-Adresse die **EINGABETASTE**, um sie leer zu lassen, und geben Sie `Pollsdb1` als Kennwort ein.

1. Wenn eine Fehlermeldung angezeigt wird, dass die Datenbank gesperrt ist, stellen Sie sicher, dass Sie den Befehl `az webapp settings` im vorherigen Abschnitt ausgeführt haben. Ohne diese Einstellungen kann der Migrationsbefehl (migrate) nicht mit der Datenbank kommunizieren, was zu einem Fehler führt.

Treten Probleme auf? Lesen Sie zunächst das [Handbuch zur Problembehandlung](../app-service/configure-language-python.md#troubleshooting), andernfalls [informieren Sie uns darüber](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Erstellen einer Frage für die Umfrage in der App

1. Öffnen Sie die Website der App. In der App sollten „Polls app“ (Umfrage-App) und „No polls are available“ (Keine Umfragen verfügbar) angezeigt werden, da die Datenbank noch keine spezifischen Umfragen enthält.

    ```azurecli
    az webapp browse
    ```

    Wird „Anwendungsfehler“ angezeigt, haben Sie wahrscheinlich entweder die erforderlichen Einstellungen im vorherigen Schritt ([Konfigurieren der Umgebungsvariablen für die Datenbankverbindung](#42-configure-environment-variables-to-connect-the-database)) nicht erstellt, oder diese Werte enthalten Fehler. Führen Sie den Befehl `az webapp config appsettings list` aus, um die Einstellungen zu überprüfen.

    Nachdem Sie die Einstellungen zum Beheben von Fehlern aktualisiert haben, räumen Sie eine Minute für den Neustart der App ein, und aktualisieren Sie dann den Browser.

1. Browsen Sie zur Administratorseite der Web-App, indem Sie an die URL `/admin` anfügen, z. B. `http://<app-name>.azurewebsites.net/admin`. Melden Sie sich unter Verwendung der Superuser-Anmeldeinformationen für Django aus dem vorherigen Abschnitt (`root` und `Pollsdb1`) an. Wählen Sie unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten.

1. Navigieren Sie wieder zur Hauptwebsite (`http://<app-name>.azurewebsites.net`), und vergewissern Sie sich, dass die Fragen nun angezeigt werden. Beantworten Sie Fragen nach Belieben, um Daten in der Datenbank zu generieren.

**Glückwunsch!** Sie führen eine Python-Django-Web-App in Azure App Service für Linux mit einer aktiven Postgres-Datenbank aus.

> [!NOTE]
> Zur Erkennung von Django-Projekten wird von App Service jedes Unterverzeichnis nach einer Datei namens *wsgi.py* durchsucht. Diese wird standardmäßig durch `manage.py startproject` erstellt. Wird die Datei von App Service gefunden, wird die Django-Web-App geladen. Weitere Informationen finden Sie unter [Konfigurieren einer Linux-Python-App für Azure App Service](../app-service/configure-language-python.md).


## <a name="5-clean-up-resources"></a>5. Bereinigen von Ressourcen

Wenn Sie die App behalten oder mit den zusätzlichen Tutorials fortfahren möchten, gehen Sie direkt zu [Nächste Schritte](#next-steps). Andernfalls können Sie die für dieses Tutorial erstellte Ressourcengruppe löschen, um laufende Gebühren zu vermeiden:

```azurecli
az group delete --name ServiceConnector-tutorial-rg --no-wait
```

Wenn Sie die Ressourcengruppe löschen, wird auch die Zuordnung aller darin enthaltenen Ressourcen aufgehoben, und die Ressourcen werden gelöscht. Stellen Sie sicher, dass Sie die Ressourcen in der Gruppe nicht mehr benötigen, bevor Sie den Befehl ausführen.

Das Löschen aller Ressourcen kann einige Zeit dauern. Das Argument `--no-wait` ermöglicht, dass der Befehl sofort zurückgegeben wird.

Treten Probleme auf? [Informieren Sie uns darüber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)

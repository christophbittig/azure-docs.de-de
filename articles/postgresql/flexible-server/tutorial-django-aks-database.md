---
title: 'Tutorial: Bereitstellen von Django in einem AKS-Cluster mit Azure Database for PostgreSQL – Flexible Server über die Azure-Befehlszeilenschnittstelle (CLI)'
description: Hier erfahren Sie, wie Sie Django in AKS mit Azure Database for PostgreSQL – Flexible Server schnell erstellen und bereitstellen.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1c19100cad65c965c567c93c67db1033a4bba10
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136843"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Tutorial: Bereitstellen einer Django-App in AKS mit Azure Database for PostgreSQL – Flexible Server

In dieser Schnellstartanleitung wird eine Django-Anwendung in einem AKS-Cluster (Azure Kubernetes Service) mit Azure Database for PostgreSQL – Flexible Server (Vorschau) unter Verwendung der Azure-Befehlszeilenschnittstelle (CLI) bereitgestellt.

**[AKS](../../aks/intro-kubernetes.md)** ist ein verwalteter Kubernetes-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. **[Azure Database for PostgreSQL – Flexible Server (Vorschau)](overview.md)** ist ein vollständig verwalteter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität in Bezug auf Verwaltungsfunktionen und Konfigurationseinstellungen für Datenbanken bietet.

> [!NOTE]
> - Azure Database for PostgreSQL – Flexible Server ist derzeit in der öffentlichen Vorschau verfügbar
> - Für diese Schnellstartanleitung werden Grundkenntnisse von Kubernetes-Konzepten, Django und PostgreSQL vorausgesetzt.

## <a name="pre-requisites"></a>Voraussetzungen
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Starten Sie [Azure Cloud Shell](https://shell.azure.com) in einem neuen Browserfenster. Sie können die [Azure CLI](/cli/azure/install-azure-cli#install) auch auf Ihrem lokalen Computer installieren. Wenn Sie eine lokale Installation verwenden, melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) bei der Azure CLI an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen. 
- Führen Sie [az version](/cli/azure/reference-index?#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen. Für diesen Artikel ist die aktuelle Azure CLI-Version erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie eine Ressourcengruppe namens *django-project* mit dem Befehl [az-group-create](/cli/azure/group#az_group_create) am Speicherort *eastus*.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Der Standort für die Ressourcengruppe ist der Ort, an dem die Metadaten der Ressourcengruppen gespeichert werden. Darüber hinaus werden dort Ihre Ressourcen in Azure ausgeführt, sofern Sie im Rahmen der Ressourcenerstellung keine andere Region angeben.

Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

Erstellen Sie mithilfe des Befehls [az aks create](/cli/azure/aks#az_aks_create) einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *djangoappcluster* mit einem Knoten erstellt. Dieser Schritt dauert einige Minuten.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben.

> [!NOTE]
> Beim Erstellen eines AKS-Clusters wird automatisch eine zweite Ressourcengruppe erstellt, um die AKS-Ressourcen zu speichern. Weitere Informationen finden Sie unter [Warum werden zwei Ressourcengruppen mit AKS erstellt?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks).

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. 

> [!NOTE] 
> Wenn Sie die Azure CLI lokal ausführen, führen Sie den Befehl [az aks install-cli](/cli/azure/aks#az_aks_install_cli) aus, um `kubectl` zu installieren.

Mit dem Befehl [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), um eine Liste der Clusterknoten zurückzugeben.

```azurecli-interactive
kubectl get nodes
```

Die folgende Beispielausgabe zeigt den in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Knoten den Status *Bereit* hat:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Erstellen einer Instanz von Azure Database for PostgreSQL – Flexible Server
Erstellen Sie mithilfe des Befehls [az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) einen flexiblen Server. Mit dem folgenden Befehl wird ein Server mit Dienststandards und -werten aus dem lokalen Kontext Ihrer Azure CLI erstellt:

```azurecli-interactive
az postgres flexible-server create --public-access all
```

Der erstellte Server weist die folgenden Attribute auf:
- Bei der erstmaligen Bereitstellung des Servers wird eine neue leere Datenbank (```postgres```) erstellt. In dieser Schnellstartanleitung wird diese Datenbank verwendet.
- Er besitzt automatisch generierte Werte für Servername, Administratorbenutzername, Administratorkennwort und Ressourcengruppenname (sofern nicht bereits im lokalen Kontext angegeben) und befindet sich am gleichen Standort wie die Ressourcengruppe.
- Mithilfe des public-access-Arguments können Sie einen Server mit öffentlichem Zugriff auf einen beliebigen Client mit richtigem Benutzernamen und Kennwort erstellen.
- Da in dem Befehl der lokale Kontext verwendet wird, wird der Server in der Ressourcengruppe ```django-project``` und in der Region ```eastus``` erstellt.


## <a name="build-your-django-docker-image"></a>Erstellen Ihres Django-Docker-Images

Erstellen Sie eine neue [Django-Anwendung](https://docs.djangoproject.com/en/3.1/intro/), oder verwenden Sie Ihr vorhandenes Django-Projekt. Stellen Sie sicher, dass sich der Code in dieser Ordnerstruktur befindet. 

> [!NOTE] 
> Wenn Sie nicht über eine Anwendung verfügen, können Sie direkt zu [**Kubernetes-Manifestdatei erstellen**](./tutorial-django-aks-database.md#create-kubernetes-manifest-file) wechseln, um unser Beispielimage [mksuni/django-aks-app:latest](https://hub.docker.com/r/mksuni/django-aks-app) zu verwenden. 

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Aktualisieren Sie ```ALLOWED_HOSTS``` in ```settings.py```, um sicherzustellen, dass die Django-Anwendung die externe IP-Adresse verwendet, die der Kubernetes-App zugewiesen wird.

```python
ALLOWED_HOSTS = ['*']
```

Aktualisieren Sie in der Datei ```settings.py``` den Abschnitt ```DATABASES={ }```. Der folgende Codeausschnitt liest Datenbankhost, Benutzername und Kennwort aus der Kubernetes-Manifestdatei.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Generieren der Datei „requirements.txt“
Erstellen Sie die Datei ```requirements.txt```, um die Abhängigkeiten für die Django-Anwendung aufzulisten. Hier ist ein Beispiel für eine Datei ```requirements.txt```. Sie können [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) verwenden, um die Datei „requirements.txt“ für Ihre vorhandene Anwendung zu generieren.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Erstellen eines Dockerfile
Erstellen Sie eine neue Datei namens ```Dockerfile```, und kopieren Sie den folgenden Codeausschnitt. Mit diesem Dockerfile wird Python 3.8 eingerichtet. Außerdem werden alle Anforderungen installiert, die in der Datei „requirements.txt“ aufgeführt sind.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Erstellen Ihres Images
Stellen Sie in einem Terminal mithilfe des Befehls ```cd``` sicher, dass Sie sich im Verzeichnis ```my-django-app``` befinden. Führen Sie den folgenden Befehl aus, um Ihr Bulletin Board-Image zu erstellen:

``` bash

docker build --tag myblog:latest .

```

Stellen Sie Ihr Image in [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) oder in [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md) bereit.

> [!IMPORTANT]
>Führen Sie bei Verwendung von Azure Container Registry (ACR) den Befehl ```az aks update``` aus, um das ACR-Konto mit dem AKS-Cluster zu verknüpfen.
>
>```azurecli-interactive
>az aks update -n djangoappcluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Erstellen einer Kubernetes-Manifestdatei

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. Erstellen Sie eine Manifestdatei namens ```djangoapp.yaml```, und fügen Sie die folgende YAML-Definition ein. 

>[!IMPORTANT]
> - Ersetzen Sie ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` durch den Namen und das Tag Ihres tatsächlichen Django-Docker-Images (beispielsweise ```docker-hub-user/myblog:latest```).  Sie können die Demobeispiel-App ```mksuni/django-aks-app:latest``` in der Manifestdatei verwenden.
> - Aktualisieren Sie den Abschnitt ```env``` mit Ihren Werten für ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` und ```YOUR-DATABASE-PASSWORD``` Ihres flexiblen PostgreSQL-Servers.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USER
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Bereitstellen von Django in einem AKS-Cluster
Stellen Sie die Anwendung über den Befehl [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f djangoapp.yaml
```

In der folgenden Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment "django-app" created
service "python-svc" created
```

Bei der Bereitstellung einer ```django-app``` können Sie Einzelheiten der Bereitstellung beschreiben, z. B. die Images, die für die App verwendet werden sollen, die Anzahl der Pods und die Pod-Konfiguration. Es wird ein ```python-svc```-Dienst erstellt, um die Anwendung über eine externe IP-Adresse verfügbar zu machen.

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) mit dem Argument `--watch`.

```azurecli-interactive
kubectl get service python-svc --watch
```

Zu Beginn wird die externe IP-Adresse (*EXTERNAL-IP*) für den Dienst *django-app* mit dem Status *ausstehend* angezeigt.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Django-Anwendung anzuzeigen.  

>[!NOTE]
> - Aktuell verwendet die Django-Website kein HTTPS. Es empfiehlt sich, [TLS mit Ihren eigenen Zertifikaten zu aktivieren](../../aks/ingress-own-tls.md).
> - Sie können [HTTP-Routing](../../aks/http-application-routing.md) für Ihren Cluster aktivieren. Wenn HTTP-Routing aktiviert ist, wird in Ihrem AKS-Cluster ein Eingangscontroller konfiguriert. Darüber hinaus erstellt die Lösung bei der Bereitstellung von Anwendungen auch öffentlich zugängliche DNS-Namen für Anwendungsendpunkte.

## <a name="run-database-migrations"></a>Ausführen von Datenbankmigrationen

Für jede Django-Anwendung müssen Sie eine Datenbankmigration ausführen oder statische Dateien sammeln. Diese Django-Shellbefehle können Sie mit ```$ kubectl exec <pod-name> -- [COMMAND]``` ausführen.  Vor dem Ausführen des Befehls müssen Sie mithilfe von ```kubectl get pods``` den Pod-Namen ermitteln. 

```bash
$ kubectl get pods
```

Eine Ausgabe wie die folgende sollte angezeigt werden:
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Nachdem Sie den Pod-Name gefunden haben, können Sie mit dem Befehl ```$ kubectl exec <pod-name> -- [COMMAND]``` Django-Datenbankmigrationen ausführen. Hinweis: ```/code/``` ist das Arbeitsverzeichnis für das Projekt, das im o. g. ```Dockerfile``` definiert ist.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

Die Ausgabe würde wie folgt aussehen: 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Wenn Probleme auftreten, führen Sie ```kubectl logs <pod-name>``` aus, um festzustellen, welche Ausnahme von der Anwendung ausgelöst wird. Wird die Anwendung erfolgreich ausgeführt, wird bei Ausführung von ```kubectl logs``` eine Ausgabe wie die folgende angezeigt:

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen.  Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung](../../aks/kubernetes-service-principal.md#additional-considerations). Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [auf das Kubernetes-Webdashboard für Ihren AKS-Cluster zugreifen](../../aks/kubernetes-dashboard.md).
- Erfahren Sie, wie Sie [Continuous Deployment aktivieren](../../aks/deployment-center-launcher.md).
- Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](../../aks/tutorial-kubernetes-scale.md) vertraut.
- Erfahren Sie, wie Sie Ihren [flexiblen Postgres-Server](./quickstart-create-server-cli.md) verwalten.
- Informieren Sie sich über das [Konfigurieren von Serverparametern](./howto-configure-server-parameters-using-cli.md) für Ihren Datenbankserver.

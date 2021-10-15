---
title: Beispiel zum Ausführen eines Containers mit dem „docker run“-Befehl
titleSuffix: Azure Cognitive Services
description: Docker-Ausführungsbefehl für Integritätscontainer
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/02/2021
ms.author: aahi
ms.openlocfilehash: 68f320a7af2d41bfb29f6d7f7f84ccdf718044d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799078"
---
## <a name="install-the-container"></a>Installieren des Containers

Es gibt mehrere Möglichkeiten zum Installieren und Ausführen des Text Analytics for Health-Containers. 

- Verwenden Sie das [Azure-Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare), um eine Ressource für die Textanalyse zu erstellen, und Docker, um Ihren Container abzurufen.
- Verwenden Sie eine Azure-VM mit Docker, um den Container auszuführen. Informationen finden Sie unter [Docker in Azure](../../../docker/index.yml).
- Verwenden Sie die folgenden PowerShell- und Azure CLI-Skripts, um die Ressourcenbereitstellung und Containerkonfiguration zu automatisieren.

### <a name="run-the-container-locally"></a>Lokales Ausführen des Containers

Um den Container nach dem Herunterladen des Containerimages in Ihrer eigenen Umgebung auszuführen, führen Sie den folgenden `docker run`-Befehl aus. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Schlüssel für Ihre Textanalyseressource. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Der Endpunkt für den Zugriff auf die Textanalyse-API. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Dieser Befehl:

- Führt den *Textanalyse für Gesundheit*-Container aus dem Containerimage aus
- Ordnet 6 CPU-Kerne und 12 GB Arbeitsspeicher zu.
- Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
- Akzeptiert die Bestimmungen des Endbenutzer-Lizenzvertrags (EULA) und der Verantwortlichen Ki (RAI)
- Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

### <a name="run-the-container-with-client-library-support"></a>Ausführen des Containers mit Clientbibliotheksunterstützung

Ab der Containerversion `3.0.017010001-onprem-amd64` (oder wenn Sie den `latest`-Container verwenden) können Sie den Textanalyse für den Integritätscontainer ausführen, um mit dem Integritätsvorgang Textanalyse [Clientbibliothek](../quickstarts/client-libraries-rest-api.md) zu arbeiten. Fügen Sie zu diesem Zweck folgenden Parameter zum Befehl `docker run` hinzu:

`enablelro=true`

Verwenden Sie anschließend beim Authentifizieren des Textanalyse-Clients den Endpunkt, auf dem Ihr Container ausgeführt wird:

`http://localhost:5000`

Wenn Sie beispielsweise C# verwenden, verwenden Sie den folgenden Code:

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

### <a name="demo-ui-to-visualize-output"></a>Demobenutzeroberfläche zur Visualisierung von Ausgaben

> [!NOTE]
> Die Demo ist nur für den Text Analytics for Health-Container verfügbar.

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.  Wir haben auch ein Visualisierungstool im Container bereitgestellt, auf das Sie zugreifen können, indem Sie `/demo` an den Endpunkt des Containers anfügen. Beispiel:

```
http://<serverURL>:5000/demo
```

Verwenden Sie die unten aufgeführte cURL-Beispielanforderung, um eine Abfrage an den bereitgestellten Container zu senden, und ersetzen Sie die Variable `serverURL` durch den passenden Wert.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installieren des Containers mithilfe von Azure Web App für Container

Azure [Web-App für Container](https://azure.microsoft.com/services/app-service/containers/) ist eine Azure-Ressource, die für die Ausführung von Containern in der Cloud vorgesehen ist. Sie bietet Standardfunktionen wie automatische Skalierung, Unterstützung von Docker-Containern und Docker Compose, HTTPS-Unterstützung und vieles mehr.

> [!NOTE]
> Mithilfe von Azure Web App erhalten Sie automatisch eine Domäne im Format `<appservice_name>.azurewebsites.net`

Führen Sie dieses PowerShell-Skript an der Azure CLI aus, um eine Web-App für Container zu erstellen, und verwenden Sie dazu Ihr Abonnement und das Containerimage über HTTPS. Warten Sie, bis das Skript abgeschlossen wurde (ungefähr 25–30 Minuten), bevor Sie die erste Anforderung absenden.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installieren des Containers mithilfe von Azure Container Instance

Sie können auch eine Azure-Containerinstanz (ACI) verwenden, um die Bereitstellung zu vereinfachen. ACI ist eine Ressource, die Ihnen das Ausführen von Docker-Containern nach Bedarf in einer verwalteten, serverlosen Azure-Umgebung ermöglicht. 

Schritte zum Bereitstellen einer ACI-Ressource mithilfe des Azure-Portals finden Sie unter [Verwenden von Azure Container Instances](../../containers/azure-container-instance-recipe.md). Alternativ können Sie das unten dargestellte PowerShell-Skript an der Azure CLI verwenden, das eine ACI in Ihrem Abonnement erstellt und dazu das Containerimage verwendet.  Warten Sie, bis das Skript abgeschlossen wurde (ungefähr 25–30 Minuten), bevor Sie die erste Anforderung absenden.  Wählen Sie wegen des Limits für die Maximalanzahl von CPUs pro ACI-Ressource diese Option nicht aus, wenn Sie davon ausgehen, dass Sie mehr als 5 große Dokumente (jeweils ca. 5000 Zeichen) pro Anforderung übermitteln werden.
Informationen zur Verfügbarkeit finden Sie im Artikel [Regionale ACI-Unterstützung](../../../container-instances/container-instances-region-availability.md). 

> [!NOTE] 
> Azure Container Instances beinhalten keine HTTPS-Unterstützung für die integrierten Domänen. Wenn Sie HTTPS benötigen, müssen Sie es manuell konfigurieren. Dazu gehört auch das Erstellen eines Zertifikats und das Registrieren einer Domäne. Anweisungen für die Ausführung mit NGINX finden Sie unten.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Schützen der ACI-Konnektivität

Standardmäßig wird bei der Verwendung von ACI mit der Container-API keine Sicherheit bereitgestellt. Das hat den Grund, dass Container meist als Teil eines Pods ausgeführt werden, das von außen durch eine Netzwerkbrücke geschützt ist. Jedoch können Sie einen Container mit einer Front-End-Komponente verändern, wodurch der Containerendpunkt privat bleibt. In den folgenden Beispielen wird [NGINX](https://www.nginx.com) als Eingangsgateway verwendet, um HTTPS/SSL und die Authentifizierung mit Clientzertifikaten zu unterstützen.

> [!NOTE]
> NGINX ist ein hochleistungsfähiger Open Source-HTTP-Server und -Proxy. Ein NGINX-Container kann verwendet werden, um eine TLS-Verbindung für einen einzelnen Container zu terminieren. Komplexere TLS-Terminierungslösungen sind mit NGINX-basierten Eingangsgateways ebenfalls möglich.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Einrichten von NGINX als Eingangsgateway

NGINX verwendet [Konfigurationsdateien](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/), um Features zur Laufzeit zu aktivieren. Um TLS-Terminierung für einen anderen Dienst zu ermöglichen, müssen Sie ein SSL-Zertifikat angeben, um die TLS-Verbindung zu terminieren, und `proxy_pass`, um eine Adresse für den Dienst anzugeben. Unten sehen Sie ein Beispiel.


> [!NOTE]
> `ssl_certificate` erwartet die Angabe eines Pfads innerhalb des lokalen Dateisystems des NGINX-Containers. Die für `proxy_pass` angegebene Adresse muss im Netzwerk des NGINX-Containers verfügbar sein.

Der NGINX-Container lädt alle Dateien in der `_.conf_`, die unter `/etc/nginx/conf.d/` in den HTTP-Konfigurationspfad eingehängt werden.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker Compose-Beispieldatei

Im Beispiel unten wird veranschaulicht, wie eine [Docker Compose](https://docs.docker.com/compose/reference/overview)-Datei erstellt werden kann, um die Container für nginx und Text Analytics for Health bereitzustellen:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Führen Sie zum Initialisieren dieser Docker Compose-Datei den folgenden Befehl an einer Konsole auf der Stammebene der Datei aus:

```bash
docker-compose up
```

Weitere Informationen finden Sie in der NGINX-Dokumentation unter [NGINX SSL Termination](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/) (NGINX SSL-Terminierung).
---
title: So verwenden Sie Container für die Textanalyse für Integrität
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie medizinische Informationen lokal extrahieren und kennzeichnen, indem Textanalyse Docker-Container für die Integrität verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: b3ab4a3a3261fbd53d5294cf213823a369adbd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095529"
---
# <a name="use-text-analytics-for-health-containers"></a>Textanalyse für das Integritätssystem (Container) verwenden

Mit Containern können Sie die Textanalyse für die Integritäts-API in Ihrer eigenen Infrastruktur hosten. Wenn Sie Sicherheits- oder Datengovernance-Anforderungen haben, die nicht durch das Remote-Aufrufen von Textanalyse für Integrität erfüllt werden können, sind Container möglicherweise eine gute Option.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung von Container für die Textanalyse für Integritätn müssen die folgenden Voraussetzungen erfüllt sein. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

* [Docker](https://docs.docker.com/) ist auf einem Hostcomputer installiert. Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. 
    * Unter Windows muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.
    * Sie sollten über grundlegende Kenntnisse der [Docker-Konzepte](https://docs.docker.com/get-started/overview/) verfügen. 
* Eine <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">Sprachressource</a> mit dem kostenlosen (F0) oder standardmäßigen (S) [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Anforderungen und Empfehlungen für den Hostcomputer

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

In der folgenden Tabelle werden die minimalen und empfohlenen Spezifikationen für die Container für die Textanalyse für Integritätn beschrieben. Jeder CPU-Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen. Die zulässigen Transaktionen pro Sekunde (Transactions Per Second, TPS) sind ebenfalls aufgeführt.

|  | Mindestspezifikationen für Hosts | Empfohlene Hostspezifikationen | Mindestanzahl von TPS | Maximale Anzahl von TPS|
|---|---------|-------------|--|--|
| **1 Dokument/Anforderung**   |  4 Kerne, 10 GB Arbeitsspeicher | 6 Kerne, 12 GB Arbeitsspeicher |15 | 30|
| **10 Dokumente/Anforderung**   |  6 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 20 GB Arbeitsspeicher |15 | 30|

CPU-Kernanzahl und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um dieses Containerimage aus der öffentlichen Microsoft-Containerregistrierung herunterzuladen.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```


[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Wenn sich der Container auf dem Hostcomputer befindet, verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um die Container auszuführen. Der Container wird so lange ausgeführt, bis Sie ihn beenden.

> [!IMPORTANT]
> * In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
> * Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
>   * Die Bestätigung der [verantwortungsbewussten KI](/legal/cognitive-services/text-analytics/transparency-note-health) (responsible AI, RAI) muss ebenfalls mit dem Wert vorhanden `accept` sein.
> * Die Container für die Stimmungsanalyse und Spracherkennung verwenden Version 3 der API und sind allgemein verfügbar. Der Container für die Schlüsselbegriffserkennung verwendet Version 2 der API und befindet sich in der Vorschauphase.

Es gibt mehrere Möglichkeiten zum Installieren und Ausführen des Text Analytics for Health-Containers. 

- Verwenden Sie das Azure-Portal, um eine Sprachressource zu erstellen, und Docker, um Ihren Container abzurufen.
- Verwenden Sie eine Azure-VM mit Docker, um den Container auszuführen. Informationen finden Sie unter [Docker in Azure](../../../../docker/index.yml).
- Verwenden Sie die folgenden PowerShell- und Azure CLI-Skripts, um die Ressourcenbereitstellung und Containerkonfiguration zu automatisieren.

### <a name="run-the-container-locally"></a>Lokales Ausführen des Containers

Um den Container nach dem Herunterladen des Containerimages in Ihrer eigenen Umgebung auszuführen, führen Sie den folgenden `docker run`-Befehl aus. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Schlüssel für Ihre Language-Ressource. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Der Endpunkt für den Zugriff auf die API. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

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

- Führt den Textanalyse für Gesundheit-Container aus dem Containerimage aus
- Ordnet 6 CPU-Kerne und 12 GB Arbeitsspeicher zu.
- Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
- Akzeptiert die Bestimmungen des Endbenutzer-Lizenzvertrags (EULA) und der Verantwortlichen Ki (RAI)
- Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

### <a name="demo-ui-to-visualize-output"></a>Demobenutzeroberfläche zur Visualisierung von Ausgaben

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
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
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

Schritte zum Bereitstellen einer ACI-Ressource mithilfe des Azure-Portals finden Sie unter [Verwenden von Azure Container Instances](../../../containers/azure-container-instance-recipe.md). Alternativ können Sie das unten dargestellte PowerShell-Skript an der Azure CLI verwenden, das eine ACI in Ihrem Abonnement erstellt und dazu das Containerimage verwendet.  Warten Sie, bis das Skript abgeschlossen wurde (ungefähr 25–30 Minuten), bevor Sie die erste Anforderung absenden.  Wählen Sie wegen des Limits für die Maximalanzahl von CPUs pro ACI-Ressource diese Option nicht aus, wenn Sie davon ausgehen, dass Sie mehr als 5 große Dokumente (jeweils ca. 5000 Zeichen) pro Anforderung übermitteln werden.
Informationen zur Verfügbarkeit finden Sie im Artikel [Regionale ACI-Unterstützung](../../../../container-instances/container-instances-region-availability.md). 

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
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
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

Im Beispiel unten wird veranschaulicht, wie eine [Docker Compose](https://docs.docker.com/compose/reference/overview)-Datei erstellt werden kann, um die Container für nginx und Integrität bereitzustellen:

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
    image: nginx
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

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]


### <a name="structure-the-api-request-for-the-container"></a>Strukturieren der API-Anforderung für den Container

Sie können mithilfe von Postman oder der unten aufgeführten cURL-Beispielanforderung eine Abfrage an den von Ihnen bereitgestellten Container senden und darin die Variable `serverURL` durch den geeigneten Wert ersetzen.  Beachten Sie, dass die Version der API in der URL für den Container anders als diejenige der gehosteten API ist.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Der folgende JSON-Code ist ein Beispiel für eine JSON-Datei, die an den POST-Text der Textanalyse-für-Integrität-Anforderung angefügt ist:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Container-Antworttext

Der folgende JSON-Code ist ein Beispiel für den Textanalyse-für-Integrität-Antworttext aus dem synchronen containerisierten Aufruf:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

## <a name="run-the-container-with-client-library-support"></a>Ausführen des Containers mit Clientbibliotheksunterstützung

Ab der Containerversion `3.0.017010001-onprem-amd64` (oder bei Verwendung des Containers `latest`) können Sie den Container für die Textanalyse für Integrität mithilfe der [Clientbibliothek](../quickstart.md) ausführen. Fügen Sie zu diesem Zweck folgenden Parameter zum Befehl `docker run` hinzu:

`enablelro=true`

Verwenden Sie anschließend beim Authentifizieren des Client-Objekts den Endpunkt, auf dem Ihr Container ausgeführt wird:

`http://localhost:5000`

Wenn Sie beispielsweise C# verwenden, verwenden Sie den folgenden Code:

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](configure-containers.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Die Container für die Textanalyse für Integrität senden Abrechnungsinformationen mithilfe einer _Sprachressource_ in Ihrem Azure-Konto an Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Textanalyse für Integrität kennengelernt. Zusammenfassung:

* Textanalyse für die Integrität stellt einen Linux-Container für Docker zur Verfügung.
* Containerimages werden aus Microsoft Container Registry (MCR) heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Textanalyse für Integrität über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die Konfigurationseinstellungen unter [Konfigurieren von Containern](configure-containers.md) an.

---
title: Installieren und Ausführen von Docker-Containern für die Formularerkennung v2.1
titleSuffix: Azure Applied AI Services
description: Verwenden Sie die Docker-Container für die Formularerkennung lokal, um Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellen und Struktur in Formularen und Dokumenten zu identifizieren und zu extrahieren.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: Lokal, Docker, Container, Identifizieren
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdd04a42ba97721d5d5dafd23f9e8e7b5b661f4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026997"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>Installieren und Ausführen der Container für die Formularerkennung v2.1 (Vorschau)

> [!IMPORTANT]
>
> * Container für die Formularerkennung befinden sich in der geschlossenen Vorschauphase. Wenn Sie die Container verwenden möchten, müssen Sie eine [Onlineanforderung](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) einreichen und diese genehmigen lassen. Weitere Informationen finden Sie nachfolgend unter [**Anfordern der Genehmigung für die Containerausführung**](#request-approval-to-run-the-container).
>
> * Im Onlineanforderungsformular müssen Sie eine gültige E-Mail-Adresse angeben, die zu der Organisation gehört, die die Azure-Abonnement-ID besitzt und in der Sie Zugriff auf dieses Abonnement haben oder erhalten haben.

Die Azure-Formularerkennung ist eine Azure Applied AI Services-Instanz und erlaubt Ihnen, unter Verwendung der Technologie für maschinelles Lernen Software für die automatisierte Datenverarbeitung zu entwickeln. Mit der Formularerkennung identifizieren und extrahieren Sie Schlüssel-Wert-Paare, Auswahlmarkierungen, Tabellendaten und Ähnliches aus Formulardokumenten und geben strukturierte Daten aus, die auch die Beziehungen in der ursprünglichen Datei umfassen.

In diesem Artikel erfahren Sie, wie Sie Container für die Formularerkennung herunterladen, installieren und ausführen. Container ermöglichen Ihnen, den Formularerkennungsdienst in Ihrer eigenen Umgebung auszuführen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. Die Formularerkennungsfunktionen werden jetzt durch sechs Featurecontainer für die Formularerkennung unterstützt: **Layout**, **Visitenkarte**, **Ausweisdokument**, **Quittung**, **Rechnung** und **Benutzerdefiniert**. (Für die Quittung-, Visitenkarte- und Ausweisdokument-Container ist außerdem der OCR-Container für das **Lesen** erforderlich.)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/), um zu beginnen.  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

Außerdem benötigen Sie Folgendes, um Container für die Formularerkennung verwenden zu können:

| Erforderlich | Zweck |
|----------|---------|
| **Kenntnisse zu Docker** | Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden [Begriffe und Befehle](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology) für `docker` kennen. |
| **Installierte Docker-Engine** | <ul><li>Die Docker-Engine muss auf einem [Hostcomputer](#host-computer-requirements) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).</li><li> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. </li><li> Unter **Windows** muss Docker auch für die Unterstützung von **Linux**-Containern konfiguriert werden.</li></ul>  |
|**Formularerkennungsressource** | Eine [**Azure-Formularerkennungsressource in Form eines einzelnen Diensts**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder eine [**Cognitive Services-Ressource für mehrere Dienste**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) im Azure-Portal. Um die Container verwenden zu können, benötigen Sie den zugeordneten API-Schlüssel und den Endpunkt-URI. Beide Werte finden Sie im Azure-Portal auf der Seite **Schlüssel und Endpunkt** für die Formularerkennung: <ul><li>**{FORM_RECOGNIZER_API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel.<li>**{FORM_RECOGNIZER_ENDPOINT_URI}** : Der Endpunkt für die Ressource zum Nachverfolgen von Abrechnungsinformationen.</li></li></ul>|
| **Maschinelles Sehen-API-Ressource** | **Zum Verarbeiten von Visitenkarten, Ausweisdokumenten und Quittungen benötigen Sie eine Ressource für maschinelles Sehen.** <ul><li>Sie können auf das Feature zur Texterkennung entweder als Azure-Ressource (REST-API oder SDK) oder als **cognitive-services-recognize-text**-[Container](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) zugreifen. Es werden die üblichen Gebühren [abgerechnet](#billing).</li> <li>Wenn Sie den Container **cognitive-services-recognize-text** verwenden, vergewissern Sie sich, dass der Schlüssel für maschinelles Sehen für den Formularerkennungscontainer der im Befehl `docker run` für maschinelles Sehen oder im Befehl `docker compose` für den Container **cognitive-services-recognize-text** angegebene Schlüssel ist und dass Ihr Abrechnungsendpunkt der Endpunkt des Containers ist (z. B. `http://localhost:5000`). Wenn Sie sowohl den Maschinelles Sehen-Container als auch den Container für die Formularerkennung zusammen auf demselben Host verwenden, können nicht beide mit dem Standardport *5000* gestartet werden. </li></ul></br>Übergeben Sie sowohl den API-Schlüssel als auch die Endpunkte für Ihre Azure-Cloud für maschinelles Sehen oder Ihren Cognitive Services-Container:<ul><li>**{COMPUTER_VISION_API_KEY}** : Einer der beiden verfügbaren Ressourcenschlüssel.</li><li> **{COMPUTER_VISION_ENDPOINT_URI}** : Der Endpunkt für die Ressource zum Nachverfolgen von Abrechnungsinformationen.</li></ul> |

|Optional|Zweck|
|---------|----------|
|**Azure CLI (Befehlszeilenschnittstelle)** | Mit der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) können Sie eine Reihe von Onlinebefehlen verwenden, um Azure-Ressourcen zu erstellen und zu verwalten. Sie ist für die Installation in Windows-, macOS- und Linux-Umgebungen verfügbar und kann in einem Docker-Container und in Azure Cloud Shell ausgeführt werden. |
|||

## <a name="request-approval-to-run-the-container"></a>Anfordern der Genehmigung für die Containerausführung

Vervollständigen und übermitteln Sie das [Antragsformular für geschlossene Dienste](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu), um die Genehmigung zum Ausführen des Containers zu erhalten.

Im Formular müssen Sie Informationen über Sie selbst, Ihr Unternehmen und das Benutzerszenario eintragen, für das Sie den Container verwenden möchten. Das Azure Cognitive Services-Team überprüft das Formular nach der Übermittlung und sendet Ihnen eine E-Mail mit der Entscheidung.

Im Formular müssen Sie eine E-Mail-Adresse angeben, die einer Azure-Abonnement-ID zugeordnet ist. Die Azure-Ressource, die Sie zum Ausführen des Containers verwenden, muss mit der genehmigten Azure-Abonnement-ID erstellt worden sein. Überprüfen Sie Ihre E-Mail-Adresse (Posteingang und Junk-Ordner) auf Updates zum Status Ihrer Anwendung von Microsoft. Nach der Genehmigung können Sie den Container ausführen, nachdem Sie ihn aus Microsoft Container Registry (MCR) heruntergeladen haben (weiter unten in diesem Artikel beschrieben).

## <a name="host-computer-requirements"></a>Anforderungen an Hostcomputer

Der Host ist ein x64-basierter Computer, auf dem der Docker-Container ausgeführt wird. Dies kann ein lokaler Computer oder ein Docker-Hostingdienst in Azure sein, z. B.:

* [Azure Kubernetes Service](../../../aks/index.yml)
* [Azure Container Instances](../../../container-instances/index.yml)
* Ein in [Azure Stack](/azure-stack/operator) bereitgestellter [Kubernetes](https://kubernetes.io/)-Cluster Weitere Informationen finden Sie unter [Bereitstellen von Kubernetes in Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

### <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

#### <a name="required-containers"></a>Erforderliche Container

Die folgende Tabelle enthält die zusätzlichen Hilfscontainer für jeden einzelnen Formularerkennungscontainer, den Sie herunterladen. Weitere Informationen finden Sie im Abschnitt [Abrechnung](#billing).

| Featurecontainer | Hilfscontainer |
|---------|-----------|
| **Layout** | Keine |
| **Visitenkarte** | **Maschinelles Sehen – Lesen**|
| **Ausweisdokument** | **Maschinelles Sehen – Lesen** |
| **Rechnung**   | **Layout** |
| **Rechnung** |**Maschinelles Sehen – Lesen** |
| **Benutzerdefiniert** | **Benutzerdefinierte API**, **Benutzerdefiniert überwacht**, **Layout**|

#### <a name="recommended-cpu-cores-and-memory"></a>Empfohlene CPU-Kerne und Arbeitsspeicher

> [!Note]
> Die Mindestanforderungen und empfohlenen Werte basieren auf Docker-Grenzwerten und *nicht* auf den Ressourcen des Hostcomputers.

##### <a name="read-layout-and-prebuilt-containers"></a>Lesen, Layout und vordefinierte Container

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
| Read 3.2 | 8 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 24 GB Arbeitsspeicher|
| Layout 2.1-preview | 8 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 24 GB Arbeitsspeicher |
| Visitenkarte 2.1-preview | 2 Kerne, 4 GB Arbeitsspeicher | 4 Kerne, 4 GB Arbeitsspeicher |
| Ausweisdokument 2.1-preview | Ein Kern, 2 GB Arbeitsspeicher |2 Kerne, 2 GB Arbeitsspeicher |
| Rechnung 2.1-preview | 4 Kerne, 8 GB Arbeitsspeicher | 8 Kerne, 8 GB Arbeitsspeicher |
| Quittung 2.1-preview |  4 Kerne, 8 GB Arbeitsspeicher | 8 Kerne, 8 GB Arbeitsspeicher  |

##### <a name="custom-containers"></a>Benutzerdefinierte Container

Die folgenden Anforderungen an Hostcomputer gelten für das **Trainieren und Analysieren** von Anforderungen:

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
| Benutzerdefinierte API| 0,5 Kerne, 0,5 GB Arbeitsspeicher| 1 Kern, 1 GB Arbeitsspeicher |
|Benutzerdefiniert überwacht | 4 Kerne, 2 GB Arbeitsspeicher | 8 Kerne, 4 GB Arbeitsspeicher|

Wenn Sie nur Analyseaufrufe ausführen, lauten die Anforderungen des Hostcomputers wie folgt:

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
|Benutzerdefiniert überwacht (Analysieren) | 1 Kern, 0,5 GB | 2 Kerne, 1 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.
* Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Befehl `docker compose` oder `docker run` verwendet werden.

> [!TIP]
> Mithilfe des Befehls [docker images](https://docs.docker.com/engine/reference/commandline/images/) können Sie Ihre heruntergeladenen Containerimages auflisten. Mit dem folgenden Befehl werden beispielsweise die ID, das Repository und das Tag jedes heruntergeladenen Containerimages in Form einer Tabelle aufgelistet:
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>Ausführen des Containers mit dem Befehl **docker-compose up**

* Ersetzen Sie die Werte {ENDPOINT_URI} und {API_KEY} durch Ihren Ressourcenendpunkt-URI und den API-Schlüssel von der Azure-Ressourcenseite.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Seite mit Schlüsseln und Endpunkt im Azure-Portal.":::

* Stellen Sie sicher, dass der EULA-Wert auf „accept“ festgelegt ist.

* Die Werte für `EULA`, `Billing` und `ApiKey` müssen angegeben werden, andernfalls wird der Container nicht gestartet.

> [!IMPORTANT]
> Die Abonnementschlüssel werden für den Zugriff auf Ihre Formularerkennungsressource verwendet. Geben Sie Ihre Schlüssel nicht weiter. Speichern Sie diese beispielsweise sicher mit Azure Key Vault. Es wird außerdem empfohlen, diese Schlüssel regelmäßig neu zu generieren. Für einen API-Aufruf ist nur ein Schlüssel erforderlich. Beim erneuten Generieren des ersten Schlüssels können Sie den zweiten Schlüssel für kontinuierlichen Zugriff auf den Dienst verwenden.

### <a name="layout"></a>[Layout](#tab/layout)

Im Folgenden finden Sie ein eigenständiges Beispiel für `docker compose` zum Ausführen des Layoutcontainers der Formularerkennung.  Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration. Geben Sie die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {{FORM_RECOGNIZER_API_KEY} für Ihre Layoutcontainerinstanz ein.

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

Jetzt können Sie den Dienst mit dem Befehl [**docker compose**](https://docs.docker.com/compose/) starten:

```bash
docker-compose up
```

### <a name="business-card"></a>[Visitenkarte](#tab/business-card)

Im Folgenden finden Sie ein eigenständiges Beispiel für den Befehl `docker compose` zum gemeinsamen Ausführen der Formularerkennungscontainer für Visitenkarten und Lesen. Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration. Geben Sie die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihre Visitenkartencontainerinstanz ein. Geben Sie {COMPUTER_VISION_ENDPOINT_URI} und {COMPUTER_VISION_API_KEY} für den Lesen-Container für maschinelles Sehen ein.

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Jetzt können Sie den Dienst mit dem Befehl [**docker compose**](https://docs.docker.com/compose/) starten:

```bash
docker-compose up
```

### <a name="id-document"></a>[Ausweisdokument](#tab/id-document)

Im Folgenden finden Sie ein eigenständiges Beispiel für den Befehl `docker compose` zum gemeinsamen Ausführen der Formularerkennungscontainer für Ausweisdokumente und Lesen. Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration. Geben Sie die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihre Ausweisdokument-Containerinstanz ein. Geben Sie die Werte {COMPUTER_VISION_ENDPOINT_URI} und {COMPUTER_VISION_API_KEY} für Ihren Lesen-Container für maschinelles Sehen ein.

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Jetzt können Sie den Dienst mit dem Befehl [**docker compose**](https://docs.docker.com/compose/) starten:

```bash
docker-compose up
```

### <a name="invoice"></a>[Rechnung](#tab/invoice)

Im Folgenden finden Sie ein eigenständiges Beispiel für den Befehl `docker compose` zum gemeinsamen Ausführen der Formularerkennungscontainer für Rechnungen und Layout. Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration.  Geben Sie die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihre Rechnungs- und Layoutcontainer ein.

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Jetzt können Sie den Dienst mit dem Befehl [**docker compose**](https://docs.docker.com/compose/) starten:

```bash
docker-compose up
```

### <a name="receipt"></a>[Rechnung](#tab/receipt)

Im Folgenden finden Sie ein eigenständiges Beispiel für den Befehl `docker compose` zum gemeinsamen Ausführen der Formularerkennungscontainer für Quittungen und Lesen. Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration. Geben Sie die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihren Quittungscontainer ein. Geben Sie die Werte {COMPUTER_VISION_ENDPOINT_URI} und {COMPUTER_VISION_API_KEY} für Ihren Lesen-Container für maschinelles Sehen ein.

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Jetzt können Sie den Dienst mit dem Befehl [**docker compose**](https://docs.docker.com/compose/) starten:

```bash
docker-compose up
```

### <a name="custom"></a>[Benutzerdefiniert](#tab/custom)

Zusätzlich zu den oben genannten [Voraussetzungen](#prerequisites) müssen Sie folgende Schritte zum Verarbeiten eines benutzerdefinierten Dokuments ausführen:

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; Erstellen Sie einen Ordner zum Speichern der folgenden Dateien:

  1. [ **.env**](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; Erstellen Sie einen Ordner zum Speichern der Eingabedaten

  1. Geben Sie diesem Ordner den Namen **shared**.
  1. Hier wird auf diesen Ordner als **{SHARED_MOUNT_PATH}** verwiesen.
  1. Kopieren Sie den Dateipfad an einem geeigneten Ort, z. B. in *Microsoft Editor*. Sie müssen ihn unten Ihrer **.env**-Datei hinzufügen.

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; Erstellen Sie einen Ordner zum Speichern der Protokolle, die vom Formularerkennungsdienst auf Ihrem lokalen Computer geschrieben werden.

  1. Geben Sie diesem Ordner den Namen **output**.
  1. Hier wird auf diesen Ordner als **{OUTPUT_MOUNT_PATH}** verwiesen.
  1. Kopieren Sie den Dateipfad an einem geeigneten Ort, z. B. in *Microsoft Editor*. Sie müssen ihn unten Ihrer **.env**-Datei hinzufügen.

#### <a name="bullet-create-an-environment-file"></a>&bullet; Erstellen Sie eine Umgebungsdatei

  1. Geben Sie dieser Datei den Namen **.env**.

  1. Deklarieren Sie die folgenden Umgebungsvariablen:

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; Erstellen Sie eine **nginx**-Datei

  1. Geben Sie dieser Datei den Namen **nginx.conf**.

  1. Geben Sie die folgende Konfiguration ein:

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/;

        }

        location /status {
            proxy_pass http://docker-api/status;

        }

        location /ready {
            proxy_pass http://docker-api/ready;

        }

        location /swagger {
            proxy_pass http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* Legen Sie sich einen Satz mit mindestens sechs Formularen desselben Typs zurecht. Diese Daten verwenden Sie zum Trainieren des Modells und zum Testen eines Formulars. Sie können ein [Beispieldataset](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren). Laden Sie die Trainingsdateien in den Ordner **shared** herunter, den Sie oben erstellt haben.

* Wenn Sie Ihre Daten beschriften möchten, laden Sie das [Beispielbeschriftungstool für die Formularerkennung für Windows](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga) herunter. Durch den Download wird die EXE-Datei des Beschriftungstools importiert, das Sie zum Beschriften der Daten verwenden, die in Ihrem lokalen Dateisystem vorhanden sind. Sie können alle Warnungen ignorieren, die während des Downloadvorgangs auftreten.

#### <a name="create-a-new-sample-labeling-tool-project"></a>Erstellen eines neuen Projekts für das Beispielbeschriftungstool

* Öffnen Sie das Beschriftungstool, indem Sie auf die EXE-Datei für das Beispielbeschriftungstool doppelklicken.
* Wählen Sie im linken Bereich des Tools die Registerkarte „Connections“ (Verbindungen) aus.
* Wählen Sie die Option zum Erstellen eines neuen Projekts aus, und geben Sie einen Namen und eine Beschreibung ein.
* Wählen Sie als Anbieter die Option für das lokale Dateisystem aus. Stellen Sie beim lokalen Ordner sicher, dass Sie den Pfad zu dem Ordner eingeben, in dem Sie die Beispieldatendateien gespeichert haben.
* Navigieren Sie zurück zur Registerkarte „Home“, und wählen Sie die Option „Use custom to train a model with labels and key value pairs“ (Benutzerdefiniertes Trainieren eines Modells mit Beschriftungen und Schlüssel-Wert-Paaren) aus.
* Wählen Sie im linken Bereich die Schaltfläche „train“ (Trainieren) aus, um das Modell mit Beschriftungen zu trainieren.
* Speichern Sie diese Verbindung, und verwenden Sie sie, um Ihre Anforderungen zu beschriften.
* Sie können die Datei Ihrer Wahl mit dem trainierten Modell analysieren lassen.

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; Erstellen Sie die Datei **docker compose**

1. Geben Sie der Datei den Namen **docker-compose.yml**.

2. Im Folgenden finden Sie ein eigenständiges Beispiel für den Befehl `docker compose` zum gemeinsamen Ausführen der Formularerkennungscontainer Layout, Beschriftungstool, benutzerdefinierte API und „Benutzerdefiniert überwacht“. Bei `docker compose` verwenden Sie eine YAML-Datei, um die Dienste Ihrer Anwendung zu konfigurieren. Dann erstellen und starten Sie mit dem Befehl `docker-compose up` alle Dienste aus Ihrer Konfiguration.

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>Stellen Sie sicher, dass der Dienst ausgeführt wird

Stellen Sie sicher, dass der Dienst betriebsbereit ist, indem Sie diese Befehle in einer Ubuntu-Shell ausführen.

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>Erstellen einer neuen Verbindung

* Wählen Sie im linken Bereich des Tools die Registerkarte **Connections** (Verbindungen) aus.
* Wählen Sie die Option **create a new project** (Neues Projekt erstellen) aus, und geben Sie einen Namen und eine Beschreibung ein.
* Wählen Sie als Anbieter die Option **local file system** (Lokales Dateisystem) aus. Stellen Sie beim lokalen Ordner sicher, dass Sie den Pfad zu dem Ordner eingeben, in dem Sie die **Beispieldatendateien** gespeichert haben.
* Navigieren Sie zurück zur Registerkarte „Home“, und wählen Sie **Use custom to train a model with labels and key value pairs** (Benutzerdefiniertes Trainieren eines Modells mit Beschriftungen und Schlüssel-Wert-Paaren) aus.
* Wählen Sie im linken Bereich die Schaltfläche **train** (Trainieren) aus, um das Modell mit Beschriftungen zu trainieren.
* Speichern Sie diese Verbindung mit **Save**, und verwenden Sie sie, um Ihre Anforderungen zu beschriften.
* Sie können die Datei Ihrer Wahl mit dem trainierten Modell analysieren lassen.

---

## <a name="validate-that-the-service-is-running"></a>Überprüfen Sie, ob der Dienst ausgeführt wird.

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist:

* Der Container stellt eine Homepage unter `\` als visuelle Validierung zur Verfügung, dass der Container ausgeführt wird.

* Sie können Ihren bevorzugten Webbrowser öffnen und zu der externen IP-Adresse und dem verfügbar gemachten Port des betreffenden Containers navigieren. Verwenden Sie die verschiedenen URLs der untenstehenden Anforderungen, um zu überprüfen, ob der Container ausgeführt wird. Die unten aufgeführten Beispiel-URLs für Anforderungen sind `http://localhost:5000`, aber Ihr spezifischer Container kann variieren. Denken Sie daran, dass Sie zur **externen IP-Adresse** Ihres Containers und zum verfügbar gemachten Port navigieren.

  Anforderungs-URL    | Zweck
  ----------- | --------
  |**http://<span></span>localhost:5000/** | Der Container stellt eine Startseite bereit.
  |**http://<span></span>localhost:5000/ready**     | Dies ermöglicht eine Überprüfung mit GET-Anforderung, dass der Container bereit ist, eine Abfrage des Modells zu akzeptieren. Diese Anforderung kann für Live- und Bereitschaftstests von Kubernetes verwendet werden.
  |**http://<span></span>localhost:5000/status** | Dies ermöglicht eine Überprüfung mit GET-Anforderung, dass der zum Starten des Containers verwendete API-Schlüssel gültig ist, ohne dass eine Endpunktabfrage veranlasst wird. Diese Anforderung kann für Live- und Bereitschaftstests von Kubernetes verwendet werden.
  |**http://<span></span>localhost:5000/swagger** | Der Container stellt eine umfassende Dokumentation für die Endpunkte sowie die Funktion Jetzt ausprobieren bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht.
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="Screenshot der Homepage für Azure-Container.":::

## <a name="stop-the-containers"></a>Beenden der Container

Zum Beenden des Containers verwenden Sie den folgenden Befehl:

```console
docker-compose down
```

## <a name="billing"></a>Abrechnung

Der Container für die Formularerkennung sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ Formularerkennung in Ihrem Azure-Konto.

Abfragen des Containers werden gemäß des Tarifs der Azure-Ressource in Rechnung gestellt, die für `ApiKey` verwendet wird. Ihnen wird jede Containerinstanz in Rechnung gestellt, die zum Verarbeiten Ihrer Dokumente und Bilder verwendet wird. Wenn Sie also das Visitenkartenfeature verwenden, werden Ihnen die Formularerkennungs-Containerinstanzen `BusinessCard` und `Compuer Vision Read` in Rechnung gestellt. Beim Rechnungsfeature werden Ihnen die Formularerkennungs-Containerinstanzen `Invoice` und `Layout` in Rechnung gestellt. *Siehe* die Preise für die Container [Formularerkennung](https://azure.microsoft.com/pricing/details/form-recognizer/) und der [Lesen-Funktion](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) für maschinelles Sehen.

Für die Ausführung von Azure Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht mit dem Endpunkt für Messung/Abrechnung verbunden sind. Sie müssen sicherstellen, dass die Container jederzeit Abrechnungsinformationen an den Abrechnungsendpunkt übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. das analysierte Bild oder den analysierten Text) an Microsoft.

### <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Der Container benötigt die Abrechnungsargumentwerte, um ausgeführt werden zu können. Diese Werte ermöglichen es dem Container, eine Verbindung mit dem Abrechnungsendpunkt herzustellen. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten. Wenn der Container nicht innerhalb des zulässigen Zeitfensters eine Verbindung mit Azure herstellt, wird der Container weiterhin ausgeführt, verarbeitet aber keine Anfragen, bis der Abrechnungsendpunkt wiederhergestellt wurde. Es erfolgen 10 Verbindungsversuche im gleichen Zeitintervall von 10 bis 15 Minuten. Wenn die Verbindung mit dem Abrechnungsendpunkt nicht innerhalb der 10 Versuche hergestellt werden kann, wird die Bereitstellung von Anforderungen durch den Container beendet. Ein Beispiel für die Informationen, die für die Abrechnung an Microsoft gesendet werden, finden Sie in den [Häufig gestellten Fragen zu Cognitive Services-Containern](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work).

### <a name="billing-arguments"></a>Abrechnungsargumente

Mit dem Befehl [**docker-compose up**](https://docs.docker.com/engine/reference/commandline/compose_up/) wird der Container gestartet, wenn alle drei der folgenden Optionen über gültige Werte verfügen:

| Option | BESCHREIBUNG |
|--------|-------------|
| `ApiKey` | Der API-Schlüssel der Cognitive Services-Ressource, der zum Nachverfolgen von Abrechnungsinformationen verwendet wird.<br/>Der Wert dieser Option muss für die bereitgestellte Ressource, die in `Billing` angegeben wurde, auf einen API-Schlüssel festgelegt werden. |
| `Billing` | Der Endpunkt der Cognitive Services-Ressource, der zum Nachverfolgen von Abrechnungsinformationen verwendet wird.<br/>Der Wert dieser Option muss auf den Endpunkt-URI einer bereitgestellten Azure-Ressource festgelegt werden.|
| `Eula` | Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.<br/>Der Wert dieser Option muss auf **accept** festgelegt werden. |

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](form-recognizer-container-configuration.md).

## <a name="summary"></a>Zusammenfassung

Das ist alles! In diesem Artikel haben Sie die Konzepte und Workflows zum Herunterladen, Installieren und Ausführen von Containern für die Formularerkennung kennengelernt. Zusammenfassung:

* Die Formularerkennung stellt sieben Linux-Container für Docker bereit.
* Containerimages werden aus der MCR heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Bei der Instanziierung eines Containers müssen Sie die Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (etwa das analysierte Bild oder den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurationseinstellungen für Formularerkennungscontainer](form-recognizer-container-configuration.md) 

---
title: Rezepte für Docker-Container
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Container mit einigen oder allen Konfigurationseinstellungen für die Bereitstellung und Wiederverwendung erstellen, testen und speichern.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: e11de4634d7488f75649535b56bd5129d7c1d65a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473393"
---
# <a name="create-containers-for-reuse"></a>Erstellen wiederverwendbarer Container

Hier finden Sie Containerrezepte für die Erstellung wiederverwendbarer Cognitive Services-Container. Container können mit einzelnen oder allen Konfigurationseinstellungen erstellt werden, sodass diese beim Start des Containers _nicht_ benötigt werden.

Nachdem Sie über diese neue Containerebene (mit Einstellungen) verfügen und sie lokal getestet haben, können Sie den Container in einer Containerregistrierung speichern. Beim Start des Containers werden nur die Einstellungen benötigt, die momentan nicht im Container gespeichert sind. Der private Registrierungscontainer bietet eine Konfigurationsumgebung, an die Sie diese Einstellungen übergeben können.

## <a name="docker-run-syntax"></a>Syntax für „docker run“

In diesem Dokument wird in allen Beispielen für `docker run` von einer Windows-Konsole mit dem Zeilenfortsetzungszeichen `^` ausgegangen. Beachten Sie bei Ihrer eigenen Verwendung Folgendes:

* Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.
* Achten Sie bei Verwendung eines Windows-fremden Betriebssystems oder einer Windows-fremden Konsole darauf, die richtige Konsole/das richtige Terminal, die richtige Ordnersyntax für Einbindungen sowie das richtige Zeilenfortsetzungszeichen für Ihre Konsole und Ihr System zu verwenden.  Da es sich beim Cognitive Services-Container um ein Linux-Betriebssystem handelt, wird für die Zieleinbindung eine Linux-artige Ordnersyntax verwendet.
* In Beispielen für `docker run` wird das Verzeichnis auf dem Laufwerk `c:` verwendet, um Berechtigungskonflikte in Windows zu vermeiden. Wenn Sie ein bestimmtes Verzeichnis als Eingabeverzeichnis verwenden möchten, müssen Sie dem Docker-Dienst möglicherweise die erforderliche Berechtigung gewähren.

## <a name="store-no-configuration-settings-in-image"></a>Keine Speicherung von Konfigurationseinstellungen im Image

In den Beispielbefehlen vom Typ `docker run` werden für die einzelnen Dienste keine Konfigurationseinstellungen im Container gespeichert. Wenn Sie den Container über eine Konsole oder über einen Registrierungsdienst starten, müssen diese Konfigurationseinstellungen übergeben werden. Der private Registrierungscontainer bietet eine Konfigurationsumgebung, an die Sie diese Einstellungen übergeben können.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Wiederverwendungsrezept: Speichern aller Konfigurationseinstellungen mit dem Container

Wenn Sie alle Konfigurationseinstellungen speichern möchten, erstellen Sie ein `Dockerfile` mit diesen Einstellungen.

Probleme bei diesem Ansatz:

* Der neue Container hat nicht den gleichen Namen und nicht das gleiche Tag wie der ursprüngliche Container.
* Um diese Einstellungen zu ändern, müssen Sie die Werte des Dockerfiles ändern, das Image neu erstellen und die Veröffentlichung in Ihrer Registrierung erneut ausführen.
* Wenn jemand Zugang zu Ihrer Containerregistrierung oder zu Ihrem lokalen Host erlangt, kann diese Person den Container ausführen und die Cognitive Services-Endpunkte nutzen.
* Falls Ihr Cognitive Service keine Eingabeeinbindungen benötigt, fügen Sie Ihrem Dockerfile die Zeilen vom Typ `COPY` nicht hinzu.

Erstellen Sie das Dockerfile (auf der Grundlage des vorhandenen Cognitive Services-Containers, den Sie verwenden möchten), und verwenden Sie anschließend Docker-Befehle im Dockerfile, um vom Container benötigte Informationen festzulegen oder zu pullen.

In diesem Beispiel werden folgende Schritte ausgeführt:

* Festlegen des Abrechnungsendpunkts (`{BILLING_ENDPOINT}`) auf der Grundlage des Hostumgebungsschlüssels mithilfe von `ENV`
* Festlegen des API-Schlüssels für die Abrechnung (`{ENDPOINT_KEY}`) auf der Grundlage des Hostumgebungsschlüssels mithilfe von „ENV“

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Wiederverwendungsrezept: Speichern von Abrechnungseinstellungen mit dem Container

Das folgende Beispiel zeigt die Erstellung des Sprachdienst-Stimmungscontainers auf der Grundlage eines Dockerfiles:

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Der Container kann ganz nach Bedarf [lokal](#how-to-use-container-on-your-local-host) oder über Ihren [privaten Registrierungscontainer](#how-to-add-container-to-private-registry) erstellt und ausgeführt werden.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Wiederverwendungsrezept: Speichern von Abrechnungs- und Einbindungseinstellungen mit dem Container

In diesem Beispiel wird gezeigt, wie Sie Language Understanding verwenden und dabei Abrechnungsinformationen und Modelle aus dem Dockerfile speichern.

* Die Modelldatei für Language Understanding (LUIS) wird mithilfe von `COPY` aus dem Dateisystem des Hosts kopiert.
* Der LUIS-Container unterstützt mehrere Modelle. Sind alle Modelle im gleichen Ordner gespeichert, ist jeweils eine Anweisung vom Typ `COPY` erforderlich.
* Führen Sie die Docker-Datei aus dem relativen übergeordneten Element des Modelleingabeverzeichnisses aus. Führen Sie im folgenden Beispiel die Befehle `docker build` und `docker run` aus dem relativen übergeordneten Element von `/input` aus. Die erste Eingabe (`/input`) im Befehl `COPY` ist das Verzeichnis des Hostcomputers. Die zweite Eingabe (`/input`) ist das Verzeichnis des Containers.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Der Container kann ganz nach Bedarf [lokal](#how-to-use-container-on-your-local-host) oder über Ihren [privaten Registrierungscontainer](#how-to-add-container-to-private-registry) erstellt und ausgeführt werden.

## <a name="how-to-use-container-on-your-local-host"></a>Verwenden eines Containers auf Ihrem lokalen Host

Ersetzen Sie zum Erstellen der Docker-Datei `<your-image-name>` durch den neuen Namen des Images, und verwenden Sie Folgendes:

```console
docker build -t <your-image-name> .
```

Verwenden Sie Folgendes, um das Image auszuführen und zu entfernen, wenn der Container beendet wird (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Hinzufügen eines Containers zur privaten Registrierung

Gehen Sie wie folgt vor, um das Dockerfile zu verwenden und das neue Image in Ihrer privaten Containerregistrierung zu platzieren:  

1. Erstellen Sie ein `Dockerfile` mit dem Text aus dem Wiederverwendungsrezept. Ein `Dockerfile` besitzt keine Erweiterung.

1. Ersetzen Sie alle Werte in spitzen Klammern durch Ihre eigenen Werte.

1. Erstellen Sie mithilfe des weiter unten angegebenen Befehls ein Image auf der Grundlage der Datei (entweder über die Befehlszeile oder über das Terminal): Ersetzen Sie den Wert in spitzen Klammern (`<>`) durch Ihren eigenen Containernamen und das zugehörige Tag.  

    Die Tagoption (`-t`) ermöglicht das Hinzufügen von Informationen zu den Änderungen, die für den Container vorgenommen wurden. Der Containername `modified-LUIS` gibt beispielsweise an, dass der ursprüngliche Container mit Schichten versehen wurde. Der Tagname `with-billing-and-model` gibt Aufschluss darüber, wie der LUIS-Container (Language Understanding) geändert wurde.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Melden Sie sich über eine Konsole bei der Azure-Befehlszeilenschnittstelle an. Dieser Befehl öffnet einen Browser und erfordert eine Authentifizierung. Nach der Authentifizierung können Sie den Browser schließen und Ihre Arbeit in der Konsole fortsetzen.

    ```azurecli
    az login
    ```

1. Melden Sie sich mit der Azure-Befehlszeilenschnittstelle über eine Konsole bei Ihrer privaten Registrierung an.

    Ersetzen Sie die Werte in spitzen Klammern, und geben Sie für `<my-registry>` Ihren eigenen Registrierungsnamen an.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Sie können sich auch per Docker-Anmeldung anmelden, wenn Ihnen ein Dienstprinzipal zugewiesen wurde.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Taggen Sie den Container mit dem Speicherort der privaten Registrierung. Ersetzen Sie die Werte in spitzen Klammern, und geben Sie für `<my-registry>` Ihren eigenen Registrierungsnamen an. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Wenn Sie keinen Tagnamen verwenden, wird `latest` impliziert.

1. Pushen Sie das neue Image an Ihre private Containerregistrierung. Der Containername aus dem folgenden CLI-Befehl wird in Ihrer privaten Containerregistrierung als Name des Repositorys verwendet:

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Deploy and run container on Azure Container Instance](azure-container-instance-recipe.md) (Bereitstellen und Ausführen eines Containers unter Azure Container Instances)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->
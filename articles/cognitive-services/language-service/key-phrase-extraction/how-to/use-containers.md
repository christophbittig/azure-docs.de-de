---
title: Lokale Verwendung von Docker-Containern für Schlüsselbegriffserkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Docker-Container für Schlüsselbegriffserkennung lokal verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
keywords: Lokal, Docker, Container, Verarbeitung natürlicher Sprache
ms.openlocfilehash: 51d7c53beaeabe57df5fe817cdb5c16c6d1d4252
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096131"
---
# <a name="install-and-run-key-phrase-extraction-containers"></a>Installieren und Ausführen von Containern für Schlüsselbegriffserkennung


Mithilfe von Containern können Sie die Schlüsselbegriffserkennungs-API in Ihrer eigenen Infrastruktur hosten. Wenn Sie Sicherheits- oder Datengovernance-Anforderungen haben, die durch das Remote-Aufrufen der Schlüsselbegriffserkennung nicht erfüllt werden können, sind Container möglicherweise eine gute Option.


> [!NOTE]
> * Das kostenlose Konto ist auf 5.000 Textdatensätze pro Monat beschränkt, und für Container können nur die [Tarife](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Free** und **Standard** verwendet werden. Weitere Informationen zu den Transaktionsanforderungsraten finden Sie unter [Datengrenzwerte](call-api.md#data-limits).

Container ermöglichen es Ihnen, Schlüsselbegriffserkennung in ihrer eigenen Umgebung auszuführen und eignen sich hervorragend für Ihre spezifischen Anforderungen bezüglich Sicherheit und Datengovernance. Container für die Schlüsselbegriffserkennung ermöglichen eine erweiterte Verarbeitung von natürlicher Sprache anhand von unformatiertem Text und bieten drei Hauptfunktionen: Stimmungsanalyse, Schlüsselbegriffserkennung und Sprachenerkennung.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen.
* [Docker](https://docs.docker.com/) ist auf einem Hostcomputer installiert. Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. 
    * Unter Windows muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.
    * Sie sollten über grundlegende Kenntnisse der [Docker-Konzepte](https://docs.docker.com/get-started/overview/) verfügen. 
* Eine <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">Sprachressource</a> mit dem kostenlosen (F0) oder standardmäßigen (S) [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Anforderungen und Empfehlungen für den Hostcomputer

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

In der folgenden Tabelle werden die minimalen und empfohlenen Spezifikationen für die verfügbaren Schlüsselbegriffserkennungs-Container beschrieben. Jeder CPU-Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen. Die zulässigen Transaktionen pro Sekunde (Transactions Per Second, TPS) sind ebenfalls aufgeführt.

|  | Mindestspezifikationen für Hosts | Empfohlene Hostspezifikationen | Mindestanzahl von TPS | Maximale Anzahl von TPS|
|---|---------|-------------|--|--|
| **Schlüsselbegriffserkennung**   | 1 Kern, 2 GB Arbeitsspeicher | 1 Kern, 4 GB Arbeitsspeicher |15 | 30| 

CPU-Kernanzahl und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den Befehl [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), um ein Containerimage aus Microsoft Container Registry herunterzuladen.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Wenn sich der Container auf dem Hostcomputer befindet, verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um die Container auszuführen. Der Container wird so lange ausgeführt, bis Sie ihn beenden.

> [!IMPORTANT]
> * In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
> * Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
> * Die Container für die Stimmungsanalyse und Spracherkennung verwenden Version 3 der API und sind allgemein verfügbar. Der Container für die Schlüsselbegriffserkennung verwendet Version 2 der API und befindet sich in der Vorschauversion.

Zum Ausführen des Containers für die *Schlüsselbegriffserkennung* führen Sie den folgenden `docker run`-Befehl aus. Ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Schlüssel für Ihre Schlüsselbegriffserkennungs-Ressource. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Der Endpunkt für den Zugriff auf die Schlüsselbegriffserkennungs-API. Sie finden ihn im Azure-Portal auf der Seite **Key and endpoint** (Schlüssel und Endpunkt) Ihrer Ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Dieser Befehl:

* Führt einen *Schlüsselbegriffserkennungs*-Container auf der Grundlage des Containerimages aus.
* Weist einen einzelnen CPU-Kern und 4 GB Arbeitsspeicher zu
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](../../concepts/configure-containers.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Die Container für die Schlüsselbegriffserkennung senden Abrechnungsinformationen mithilfe einer _Schlüsselbegriffserkennungs-Ressource_ in Ihrem Azure-Konto an Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]
\
## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Schlüsselbegriffserkennungs-Containern kennengelernt. Zusammenfassung:

* Die Schlüsselbegriffserkennung stellt Linux-Container für Docker zur Verfügung.
* Containerimages werden aus Microsoft Container Registry (MCR) heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für Schlüsselbegriffserkennung über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die Konfigurationseinstellungen unter [Konfigurieren von Containern](../../concepts/configure-containers.md) an.

---
title: Installieren und Ausführen von Docker-Containern für die Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Docker-Container für die Textanalyse-API, um die Verarbeitung natürlicher Sprache, z. B. Stimmungsanalysen, lokal durchzuführen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: aahi
keywords: Lokal, Docker, Container, Stimmungsanalyse, Verarbeitung natürlicher Sprache
ms.openlocfilehash: 82e247d51351417a987205b2c65ea26be737ec5c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473420"
---
# <a name="install-and-run-text-analytics-containers"></a>Installieren und Ausführen von Containern für die Textanalyse

Container ermöglichen es Ihnen, die Textanalyse-APIs in ihrer eigenen Umgebung auszuführen und eignen sich hervorragend für Ihre spezifischen Anforderungen bezüglich Sicherheit und Datengovernance. Die folgenden Textanalyse-Container sind verfügbar:

* Stimmungsanalyse
* Sprachenerkennung
* Schlüsselbegriffserkennung (Vorschauversion)
* Textanalyse für Gesundheit 

> [!NOTE]
> * Entitätsverknüpfung und NER stehen derzeit nicht als Container zur Verfügung.
> * Die Speicherorte für Containerimages haben sich möglicherweise vor Kurzem geändert. Lesen Sie diesen Artikel, um mehr zum aktualisierten Speicherort für diesen Container zu erfahren.
> * Das kostenlose Konto ist auf 5.000 Textdatensätze pro Monat beschränkt, und für Container können nur die [Tarife](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Free** und **Standard** verwendet werden. Weitere Informationen zu den Transaktionsanforderungsraten finden Sie unter [Datengrenzwerte](../concepts/data-limits.md).

Container ermöglichen es Ihnen, die Textanalyse-APIs in ihrer eigenen Umgebung auszuführen und eignen sich hervorragend für Ihre spezifischen Anforderungen bezüglich Sicherheit und Datengovernance. Container für die Textanalyse ermöglichen eine erweiterte Verarbeitung von natürlicher Sprache anhand von unformatiertem Text und bieten drei Hauptfunktionen: Standpunktanalyse, Schlüsselbegriffserkennung und Sprachenerkennung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung von Textanalyse-Containern müssen die folgenden Voraussetzungen erfüllt sein. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

* [Docker](https://docs.docker.com/) ist auf einem Hostcomputer installiert. Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. 
    * Unter Windows muss Docker auch für die Unterstützung von Linux-Containern konfiguriert werden.
    * Sie sollten über grundlegende Kenntnisse der [Docker-Konzepte](https://docs.docker.com/get-started/overview/) verfügen. 
* Eine <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Textanalyse-Ressource"  target="_blank">Textanalyse-Ressource</a> im [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) „Free“ (F0) oder „Standard“ (S).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Anforderungen und Empfehlungen für den Hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

In der folgenden Tabelle werden die minimalen und empfohlenen Spezifikationen für die verfügbaren Textanalyse-Container beschrieben. Jeder CPU-Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen. Die zulässigen Transaktionen pro Sekunde (Transactions Per Second, TPS) sind ebenfalls aufgeführt.

|  | Mindestspezifikationen für Hosts | Empfohlene Hostspezifikationen | Mindestanzahl von TPS | Maximale Anzahl von TPS|
|---|---------|-------------|--|--|
| **Sprachenerkennung**   | 1 Kern, 2 GB Arbeitsspeicher | 1 Kern, 4 GB Arbeitsspeicher |15 | 30| 
| **Schlüsselbegriffserkennung (Vorschau)**   | 1 Kern, 2 GB Arbeitsspeicher | 1 Kern, 4 GB Arbeitsspeicher |15 | 30| 
| **Standpunktanalyse**   | 1 Kern, 2 GB Arbeitsspeicher | 4 Kerne, 8 GB Arbeitsspeicher |15 | 30|
| **Text Analytics for Health: 1 Dokument/Anforderung**   |  4 Kerne, 10 GB Arbeitsspeicher | 6 Kerne, 12 GB Arbeitsspeicher |15 | 30|
| **Text Analytics for Health: 10 Dokumente/Anforderungen**   |  6 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 20 GB Arbeitsspeicher |15 | 30|

CPU-Kernanzahl und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

# <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Schlüsselbegriffserkennung (Vorschauversion)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Sprachenerkennung](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Textanalyse für Gesundheit](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ausführen des Containers mit `docker run`

Wenn sich der Container auf dem Hostcomputer befindet, verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um die Container auszuführen. Der Container wird so lange ausgeführt, bis Sie ihn beenden.

> [!IMPORTANT]
> * In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
> * Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).
>   * Wenn Sie den Container für die Textanalyse im Gesundheitsbereich ausführen, muss die Bestätigung der [verantwortungsbewussten KI](/legal/cognitive-services/text-analytics/transparency-note-health) ebenfalls mit dem Wert `accept` vorhanden sein.
> * Die Container für die Stimmungsanalyse und Spracherkennung verwenden Version 3 der API und sind allgemein verfügbar. Der Container für die Schlüsselbegriffserkennung verwendet Version 2 der API und befindet sich in der Vorschauphase.

# <a name="sentiment-analysis"></a>[Standpunktanalyse](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Schlüsselbegriffserkennung (Vorschauversion)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Sprachenerkennung](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Textanalyse für Gesundheit](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Abfragen des Vorhersageendpunkts des Containers

Der Container stellt REST-basierte Endpunkt-APIs für die Abfragevorhersage bereit.

Verwenden Sie für Container-APIs den Host `http://localhost:5000`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie den Container mit einer [Ausgabenbereitstellung](../text-analytics-resource-container-config.md#mount-settings) ausführen und die Protokollierung aktiviert ist, generiert der Container Protokolldateien. Diese sind hilfreich, um Probleme beim Starten oder Ausführen des Containers zu beheben.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Abrechnung

Der Container für die Textanalyse sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource vom Typ _Textanalyse_ in Ihrem Azure-Konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Weitere Informationen zu diesen Optionen finden Sie unter [Konfigurieren von Containern](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Textanalyse kennengelernt. Zusammenfassung:

* Textanalyse stellt drei Linux-Container für Docker bereit, die verschiedene Funktionen beinhalten:
   * *Standpunktanalyse*
   * *Schlüsselbegriffserkennung (Vorschauversion)* 
   * *Sprachenerkennung*
   * *Textanalyse für Gesundheit*
* Containerimages werden aus Microsoft Container Registry (MCR) heruntergeladen.
* Containerimages werden in Docker ausgeführt.
* Sie können entweder die REST-API oder das SDK verwenden, um Vorgänge in Containern für die Textanalyse über den Host-URI des Containers aufzurufen.
* Bei der Instanziierung eines Containers müssen Sie Abrechnungsinformationen angeben.

> [!IMPORTANT]
> Für die Ausführung von Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht zu Messzwecken mit Azure verbunden sind. Kunden müssen sicherstellen, dass Container jederzeit Abrechnungsinformationen an den Messungsdienst übermitteln können. Cognitive Services-Container senden keine Kundendaten (z. B. den analysierten Text) an Microsoft.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die Konfigurationseinstellungen unter [Konfigurieren von Containern](../text-analytics-resource-container-config.md) an.

---
title: Lokale Verwendung von Azure Cognitive Services-Containern
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Cognitive Services mithilfe von Docker-Containern lokal verwendet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.service: cognitive-services
ms.topic: article
ms.date: 10/28/2021
ms.author: aahi
keywords: lokal, Docker, Container, Kubernetes
ms.openlocfilehash: 8758a4688e66a1000c142ab775e563ff829bc79b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158280"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-Container

Azure Cognitive Services bietet mehrere [Docker-Container](https://www.docker.com/what-container), mit denen Sie lokal die gleichen APIs verwenden können, die in Azure verfügbar sind. Die Verwendung dieser Container gibt Ihnen die Flexibilität, Cognitive Services aus Compliance-, Sicherheits- oder anderen betrieblichen Gründen näher an Ihre Daten heranzubringen. Die Containerunterstützung ist derzeit für einige Dienste von Azure Cognitive Services verfügbar.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Die Verwendung von Containern ist ein Ansatz zur Softwareverteilung, bei dem eine Anwendung oder ein Dienst, einschließlich der Abhängigkeiten und der Konfiguration, als Containerimage zusammengepackt wird. Das Containerimage kann mit geringer oder ganz ohne Bearbeitung auf einem Containerhost bereitgestellt werden. Container sind voneinander und vom zugrunde liegenden Betriebssystem isoliert und nehmen weniger Speicher in Anspruch als ein virtueller Computer. Container können für kurzfristige Aufgaben über Containerimages instanziiert und wieder entfernt werden, wenn sie nicht mehr benötigt werden.

## <a name="features-and-benefits"></a>Features und Vorteile

- **Unveränderliche Infrastruktur**: Ermöglichen Sie es den DevOps-Teams, einen konsistenten und zuverlässigen Satz bekannter Systemparameter zu nutzen und sich gleichzeitig an Änderungen anzupassen. Container bieten die Flexibilität, sich innerhalb eines vorhersehbaren Ökosystems zu bewegen und Konfigurationsabweichungen zu vermeiden.
- **Kontrolle über Daten:** Wählen Sie aus, wo Ihre Daten von Cognitive Services verarbeitet werden. Dies kann wichtig sein, wenn Sie keine Daten an die Cloud senden können, aber Zugriff auf Cognitive Services-APIs benötigen. Einheitliche Unterstützung in Hybridumgebungen – für Daten, Verwaltung, Identität und Sicherheit.
- **Kontrolle über Modellaktualisierungen:** Flexibilität bei der Versionsverwaltung und Aktualisierung der in den Lösungen eingesetzten Modelle.
- **Portable Architektur:** Ermöglicht die Erstellung einer portablen Anwendungsarchitektur, die in Azure, lokal und am Edge eingesetzt werden kann. Container können direkt für [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml) oder einen [Kubernetes](https://kubernetes.io/)-Cluster mit Bereitstellung in [Azure Stack](/azure-stack/operator) bereitgestellt werden. Weitere Informationen finden Sie unter [Bereitstellen von Kubernetes in Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Hoher Durchsatz/niedrige Latenz:** Bietet Kunden die Möglichkeit, für hohe Durchsatzraten und niedrige Latenzanforderungen zu skalieren, indem Cognitive Services physisch in der Nähe ihrer Anwendungslogik und -daten ausgeführt wird. Container erzwingen keine Obergrenzen für die Transaktionen pro Sekunde (TPS) und können für die zentrale und horizontale Skalierung konfiguriert werden, um bei Bedarf die erforderlichen Hardwareressourcen bereitzustellen.
- **Skalierbarkeit:** Mit der ständig wachsenden Beliebtheit von Software für Containerisierung und Containerorchestrierung wie Kubernetes steht die Skalierbarkeit steht im Vordergrund des technologischen Fortschritts. Aufbauend auf einer skalierbaren Clusterbasis sorgt die Anwendungsentwicklung für eine entsprechende Hochverfügbarkeit.

## <a name="containers-in-azure-cognitive-services"></a>Container in Azure Cognitive Services

Azure Cognitive Services-Container bieten den folgenden Satz von Docker-Containern, von denen jeder eine Teilmenge der Funktionalität von Diensten in Azure Cognitive Services enthält. Anweisungen und Imagespeicherorte finden Sie in den folgenden Tabellen. Eine Liste mit [Containerimages](containers/container-image-tags.md) ist ebenfalls verfügbar.

### <a name="decision-containers"></a>Entscheidungscontainer

| Dienst |  Container | BESCHREIBUNG | Verfügbarkeit |
|--|--|--|--|
| [Anomalieerkennung][ad-containers] | **Anomalieerkennung** ([Image](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Die Anomalieerkennungs-API bietet Ihnen die Möglichkeit, Anomalien in Zeitreihendaten durch maschinelles Lernen zu überwachen und zu erkennen. | Allgemein verfügbar |

### <a name="language-containers"></a>Sprachcontainer

| Dienst |  Container | BESCHREIBUNG | Verfügbarkeit |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([Image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Lädt Ihr trainiertes oder veröffentlichtes Language Understanding-Modell (auch als LUIS-App bezeichnet) in einen Docker-Container und ermöglicht den Zugriff auf die Abfragevorhersagen von den API-Endpunkten des Containers. Sie können Abfrageprotokolle vom Container erfassen und wieder in das [LUIS-Portal](https://www.luis.ai) hochladen, um die Vorhersagegenauigkeit der App zu verbessern. | Allgemein verfügbar |
| [Sprachdienst][ta-containers-keyphrase] | **Schlüsselbegriffserkennung** ([Bild](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrahiert die Schlüsselbegriffe, um die wichtigsten Punkte zu ermitteln. Wenn der eingegebene Text beispielsweise „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt die API die Kernpunkte „Essen“ und „hervorragendes Personal“ zurück. | Vorschau |
| [Sprachdienst][ta-containers-language] |  **Textsprachenerkennung** ([Image](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Erkennt die Sprache von Eingabetexten für bis zu 120 Sprachen und meldet einen einzigen Sprachcode für jedes Dokument, das auf Anforderung gesendet wird. Der Sprachcode ist mit einem Wert kombiniert, der die Stärke der Bewertung angibt. | Allgemein verfügbar |
| [Sprachdienst][ta-containers-sentiment] | **Standpunktanalyse** ([Bild](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analysiert unformatierten Text auf Hinweise auf positive oder negative Stimmungen. Von dieser Version der Standpunktanalyse werden für jedes Dokument und jeden darin enthaltenen Satz Stimmungsbezeichnungen (beispielsweise *Positiv* oder *Negativ*) zurückgegeben. |  Allgemein verfügbar |
| [Sprachdienst][ta-containers-health] |  **Text Analytics for Health** | Extraktion und Bezeichnung medizinischer Informationen aus unstrukturiertem klinischem Text. | Vorschau |
| [Translator][tr-containers] | **Translator** | Übersetzt Text in verschiedene Sprachen und Dialekte. | Beschränkte Vorschauversion. [Zugriff anfordern](https://aka.ms/csgate-translator). | 

### <a name="speech-containers"></a>Speech-Container

> [!NOTE]
> Sie müssen für die Verwendung von Speech-Containern ein [Onlineanforderungsformular](https://aka.ms/csgate) ausfüllen.

| Dienst |  Container | BESCHREIBUNG | Verfügbarkeit |
|--|--|--|
| [Spracherkennungsdienst-API][sp-containers-stt] |  **Spracherkennung** ([Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Wandelt fortlaufende Sprache in Echtzeit in Text um. | Allgemein verfügbar |
| [Spracherkennungsdienst-API][sp-containers-cstt] | **Benutzerdefinierte Spracherkennung** ([Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Wandelt fortlaufende Sprache in Echtzeit in Text um und verwendet dazu ein benutzerdefiniertes Modell. | Allgemein verfügbar |
| [Spracherkennungsdienst-API][sp-containers-tts] | **Sprachsynthese** ([Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Konvertiert Text in natürlich klingende Sprache. | Allgemein verfügbar |
| [Spracherkennungsdienst-API][sp-containers-ctts] | **Benutzerdefinierte Sprachsynthese** ([Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Konvertiert Text in natürlich klingende Sprache und verwendet dazu ein benutzerdefiniertes Modell. | Beschränkte Vorschauversion |
| [Spracherkennungsdienst-API][sp-containers-ntts] | **Neuronale Sprachsynthese** ([Abbildung](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Konvertiert Text mithilfe von Deep Neural Network-Technologie in natürlich klingende Sprache, die eine natürlichere synthetische Sprache ermöglicht. | Allgemein verfügbar |
| [Spracherkennungsdienst-API][sp-containers-lid] | **Speech-Sprachenerkennung** ([Image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Bestimmt die Sprache der gesprochenen Audiodaten. | Beschränkte Vorschauversion |

### <a name="vision-containers"></a>Vision-Container


| Dienst |  Container | BESCHREIBUNG | Verfügbarkeit |
|--|--|--|--|
| [Maschinelles Sehen][cv-containers] | **Read OCR** ([Image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Mit dem Read OCR-Container können Sie gedruckten und handschriftlichen Text aus Bildern und Dokumenten mit Unterstützung für die Dateiformate JPEG, PNG, BMP, PDF und TIFF extrahieren. Weitere Informationen finden Sie in der Dokumentation zur [Lese-API](./computer-vision/overview-ocr.md). | Beschränkte Vorschauversion. [Zugriff anfordern][request-access]. |
| [Räumliche Analyse][spa-containers] | **Räumliche Analyse** ([Image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analysiert in Echtzeit gestreamte Videodaten, um räumliche Bezüge zwischen Personen, ihre Bewegungen und ihre Interaktionen mit Objekten in der physischen Umgebung zu verstehen. | Vorschau |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Darüber hinaus werden einige Container im Angebot [einer Ressource für mehrere Dienste](cognitive-services-apis-create-account.md) von Cognitive Services unterstützt. Sie können eine einzelne All-In-One-Ressource von Cognitive Services erstellen und denselben Abrechnungsschlüssel für alle unterstützten Dienste verwenden:

* Maschinelles Sehen
* LUIS
* Sprachdienst

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen die folgenden Voraussetzungen erfüllen, bevor Sie die Container der Azure Cognitive Services verwenden können:

**Docker-Engine:** Die Docker-Engine muss lokal installiert sein. Docker stellt Pakete zur Konfiguration der Docker-Umgebung unter [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) und [Windows](https://docs.docker.com/docker-for-windows/) zur Verfügung. Unter Windows muss Docker für die Unterstützung von Linux-Containern konfiguriert werden. Docker-Container können auch direkt für [Azure Kubernetes Service](../aks/index.yml) oder [Azure Container Instances](../container-instances/index.yml) bereitgestellt werden.

Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können.

**Kenntnisse von Microsoft Container Registry und Docker:** Sie sollten über Grundkenntnisse der Konzepte von Microsoft Container Registry und Docker verfügen, einschließlich Registrierungen, Repositorys, Container und Containerimages, und die grundlegenden `docker`-Befehle kennen.

Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).

Einzelne Container können auch ihre eigenen Anforderungen haben, wie z.B. Anforderungen an die Server- und Speicherzuordnung.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Entwicklerbeispiele

Beispiele für Entwickler finden Sie in unserem [GitHub-Repository](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu [Containeranleitungen](containers/container-reuse-recipe.md), die Sie mit Cognitive Services verwenden können.

Installieren und erkunden Sie die Funktionalität der Container in Azure Cognitive Services:

* [Container für die Anomalieerkennung][ad-containers]
* [Container für maschinelles Sehen][cv-containers]
* [Container für Language Understanding (LUIS)][lu-containers]
* [Container für die Speech Services-API][sp-containers]
* [Container für Sprachdienst][ta-containers]
* [Übersetzer-Container][tr-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: language-service/overview.md#deploy-on-premises-using-docker-containers
[ta-containers-keyphrase]: language-service/key-phrase-extraction/how-to/use-containers.md
[ta-containers-language]: language-service/language-detection/how-to/use-containers.md
[ta-containers-sentiment]: language-service/sentiment-opinion-mining/how-to/use-containers.md
[ta-containers-health]: language-service/text-analytics-for-health/how-to/use-containers.md
[tr-containers]: translator/containers/translator-how-to-install-container.md
[request-access]: https://aka.ms/csgate

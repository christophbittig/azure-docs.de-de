---
title: Konfigurieren der Textanalyse für Integritätscontainer
titleSuffix: Azure Cognitive Services
description: Die Textanalyse stellt für Integritätscontainer ein allgemeines Konfigurationsframework bereit, sodass Sie Aspekte wie Speicher, Protokollierung und Telemetrie sowie Sicherheitseinstellungen für Ihre Container ganz einfach konfigurieren und verwalten können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 801deee67503a7f7010e3c986b2ab1cf818c9f51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095530"
---
# <a name="configure-text-analytics-for-health-docker-containers"></a>Konfigurieren der Textanalyse für Docker-Integritätscontainer

Die Textanalyse für Integrität stellt für jeden Container ein allgemeines Konfigurationsframework bereit, sodass Sie Aspekte wie Speicher, Protokollierung und Telemetrie sowie Sicherheitseinstellungen für Ihre Container ganz einfach konfigurieren und verwalten können. Mehrere [Beispiele für docker run-Befehle](use-containers.md#run-the-container-with-docker-run) sind ebenfalls verfügbar.

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](use-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für ApiKey angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource des Typs _Sprache_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung für **Sprache** unter **Schlüssel und Endpunkt**

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die Einstellung `Billing` gibt den Endpunkt-URI der Ressource für die _Sprache_ in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine Ressource für die _Sprache_ in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht **Sprache** mit der Bezeichnung `Endpoint`

|Erforderlich| Name | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | String | URI des Abrechnungsendpunkts. Weitere Informationen zum Erhalt eines Abrechnungs-URI finden Sie unter [Ermitteln erforderlicher Parameter](use-containers.md#gathering-required-parameters). Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../../../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Eula-Einstellung

[!INCLUDE [Container shared configuration eula settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-Einstellungen

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Anmeldeinformationseinstellungen für HTTP-Proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging-Einstellungen
 
[!INCLUDE [Container shared configuration logging settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Einbindungseinstellungen

Verwenden Sie Bindungsbereitstellungen zum Lesen und Schreiben von Daten im Container. Sie können eine Eingabe- oder Ausgabebereitstellung über die Option `--mount` im Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) angeben.

Die Textanalyse für Integritätscontainer verwendet keine Ein- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](use-containers.md#host-computer-requirements-and-recommendations) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| Name | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | String | Die Textanalyse für Integritätscontainer verwendet dies nicht.|
|Optional| `Output` | String | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](use-containers.md).
* Verwenden weiterer [Cognitive Services-Container](../../../cognitive-services-container-support.md)

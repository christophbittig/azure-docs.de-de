---
title: Konfigurieren des Containers für die Formularerkennung
titleSuffix: Azure Applied AI Services
description: Hier erfahren Sie, wie Sie den Container für die Formularerkennung konfigurieren, um Formular- und Tabellendaten zu analysieren.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356663"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurieren des Containers für die Formularerkennung

> [!IMPORTANT]
>
> Container für die Formularerkennung befinden sich in der geschlossenen Vorschauphase. Wenn Sie die Container verwenden möchten, müssen Sie eine [Onlineanforderung](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) einreichen und diese genehmigen lassen. Weitere Informationen finden Sie nachfolgend unter [**Anfordern der Genehmigung für die Containerausführung**](form-recognizer-container-install-run.md#request-approval-to-run-the-container).

Mithilfe von Containern für die Formularerkennung können Sie eine Anwendungsarchitektur erstellen, die sowohl von widerstandsfähigen Cloudfunktionen als auch von der Edgeposition profitieren kann. Container bieten eine minimalistische virtuelle, praktisch isolierte Umgebung, die einfach lokal und in der Cloud bereitgestellt werden kann. In diesem Artikel lernen Sie, die Runtimeumgebung für Container für die Formularerkennung über die Argumente des Befehls `docker compose` zu konfigurieren. Die Formularerkennungsfunktionen werden durch sechs Formularerkennungscontainer unterstützt: **Layout**, **Visitenkarte**,**Ausweisdokument**, **Quittung**, **Rechnung** und **Benutzerdefiniert**. Dieser Container verfügen über mehrere erforderliche sowie einige optionale Einstellungen. Einige Beispiele finden Sie im Abschnitt [Beispieldatei „docker-compose.yml“](#example-docker-composeyml-file).

## <a name="configuration-settings"></a>Konfigurationseinstellungen

Jeder Container hat die folgenden Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Ja|[ApiKey](#apikey-and-billing-configuration-setting)|Nachverfolgen von Abrechnungsinformationen|
|Ja|[Abrechnung](#apikey-and-billing-configuration-setting)|Gibt den Endpunkt-URI der Dienstressource in Azure an. Weitere Informationen zum Abrufen finden Sie unter _[Abrechnung]](form-recognizer-container-install-run.md#billing)_ . Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten _finden Sie unter_ [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../../../cognitive-services/cognitive-services-custom-subdomains.md).|
|Ja|[Eula](#eula-setting)| Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.|
|Nein|[ApplicationInsights](#applicationinsights-setting)|Ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](/azure/application-insights)-Telemetriedaten in Ihrem Container.|
|Nein|[Fluentd](#fluentd-settings)|Schreibt Protokoll- und optional auch Metrikdaten auf einen Fluentd-Server.|
|Nein|HTTP-Proxy|Konfiguriert einen HTTP-Proxy für ausgehende Anforderungen.|
|Nein|[Logging](#logging-settings)|Bietet Unterstützung für die ASP.NET Core-Protokollierung für Ihren Container. |

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-and-billing-configuration-setting), [`Billing`](#apikey-and-billing-configuration-setting) und [`Eula`](#eula-setting) werden zusammen verwendet. Sie müssen für alle drei Einstellungen gültige Werte angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](form-recognizer-container-install-run.md#billing).

## <a name="apikey-and-billing-configuration-setting"></a>ApiKey und „Billing configuration setting“ (Konfigurationseinstellung für die Abrechnung)

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Beim Wert für ApiKey muss es sich um einen gültigen Schlüssel für die Ressource handeln, die im Abschnitt „Billing configuration setting“ für `Billing` angegeben wurde.

Die `Billing`-Einstellung gibt den Endpunkt-URI der Ressource in Azure an, die zum Messen der Abrechnungsinformationen für den Container verwendet wird. Beim Wert für diese Konfigurationseinstellung muss es sich um einen gültigen URI-Endpunkt für eine Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

 Diese Einstellungen sind im Azure-Portal auf der Seite **Schlüssel und Endpunkt** aufgeführt.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Seite mit Schlüsseln und Endpunkt im Azure-Portal.":::

## <a name="eula-setting"></a>Eula-Einstellung

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Fluentd-Einstellungen

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Anmeldeinformationseinstellungen für HTTP-Proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging-Einstellungen

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>Volumeeinstellungen

Verwenden Sie [**Volumes**](https://docs.docker.com/storage/volumes/), um Daten aus dem Container zu lesen und hinein zu schreiben. Volumes werden bevorzugt zum Beibehalten von Daten verwendet, die von Docker-Containern generiert und verwendet werden. Sie können eine Eingabeeinbindung oder eine Ausgabeeinbindung angeben, indem Sie die `volumes`-Option einschließen und `type` (binden), `source` (Pfad zum Ordner) und `target` (Dateipfadparameter) angeben.

Der Container für die Formularerkennung erfordert ein Ein- und ein Ausgabevolume. Das Eingabevolume kann schreibgeschützt sein (`ro`) und ist für den Zugriff auf die Daten erforderlich, die für Training und Bewertung verwendet werden. Das Ausgabevolume muss beschreibbar sein und wird zum Speichern der Modelle und temporären Daten verwendet.

Die genaue Syntax für den Speicherort des Hostvolumes variiert je nach Betriebssystem des Hosts. Darüber hinaus ist der Zugriff auf den Volumespeicherort des [Hostcomputers](form-recognizer-container-install-run.md#host-computer-requirements) möglicherweise aufgrund eines Konflikts zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Einbindungspunkt auf dem Host verwendeten Berechtigungen nicht möglich.

## <a name="example-docker-composeyml-file"></a>Beispieldatei „docker-compose.yml“

Die **Docker Compose**-Methode besteht aus drei Schritten:

 1. Erstellen Sie ein Dockerfile.
 1. Definieren Sie die Dienste in einer **docker-compose.yml**-Datei, damit sie zusammen in einer isolierten Umgebung ausgeführt werden können.
 1. Führen Sie `docker-compose up` aus, um Ihre Dienste zu starten und auszuführen.

### <a name="single-container-example"></a>Beispiel für einen einzelnen Container

Geben Sie in diesem Beispiel die Werte für {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihre Layoutcontainerinstanz ein.

#### <a name="layout-container"></a>**Layoutcontainer**

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

### <a name="multiple-containers-example"></a>Beispiel für mehrere Container

#### <a name="receipt-and-ocr-read-containers"></a>**Beleg- und OCR-Lesecontainer**

Geben Sie in diesem Beispiel die Werte {FORM_RECOGNIZER_ENDPOINT_URI} und {FORM_RECOGNIZER_API_KEY} für Ihren Belegcontainer und {COMPUTER_VISION_ENDPOINT_URI} und {COMPUTER_VISION_API_KEY} für Ihren Lesecontainer für maschinelles Sehen ein.

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
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

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zum Ausführen mehrerer Container und zum Befehl „docker compose“](form-recognizer-container-install-run.md).

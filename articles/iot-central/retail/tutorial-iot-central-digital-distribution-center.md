---
title: 'Tutorial: Digitales Verteilzentrum – Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial wird die Bereitstellung und Verwendung der Anwendungsvorlage für digitale Verteilzentren für IoT Central gezeigt.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 70ea24b64a1173d2a397d9603d020506f53be37e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434662"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendungsvorlage für digitale Verteilzentren

Verwenden Sie die IoT Central-Anwendungsvorlage für *digitale Verteilzentren* und die Anweisungen in diesem Artikel, um eine End-to-End-Lösung für digitale Verteilzentren zu entwickeln.

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="Digitales Verteilzentrum":::

1. Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
2. Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
3. Daten werden zur Bearbeitung an den gewünschten Azure-Dienst geroutet
4. Mithilfe von Azure-Diensten wie ASA oder Azure Functions können Datenströme neu formatiert und an die gewünschten Speicherkonten gesendet werden
5. Verarbeitete Daten werden in der heißen Speicherebene für Aktionen in Quasi-Echtzeit oder in der kalten Zugriffsebene zur weiteren Erkenntnisverbesserung auf Grundlage von ML oder Batchanalyse gespeichert. 
6. Logik-Apps können verwendet werden, um verschiedene Geschäftsworkflows in Endbenutzer-Geschäftsanwendungen zu unterstützen

### <a name="video-cameras"></a>Videokameras 

Videokameras sind die Hauptsensoren in diesem digital verbundenen Ökosystem im Enterprise-Maßstab. Verbesserungen in Machine Learning und künstlicher Intelligenz, die es ermöglichen, Videodaten in strukturierte Daten umzuwandeln, die am Edge verarbeitet werden, bevor sie in die Cloud gesendet werden. Wir können IP-Kameras verwenden, um Bilder zu erfassen, sie in der Kamera zu komprimieren und die komprimierten Daten anschließend über Edge-Compute an die Pipeline zur Videoanalyse zu senden, oder GigE-Vision-Kameras verwenden, um die Bilder im Sensor zu erfassen und sie direkt an den Azure IoT Edge zu senden, der sie dann vor der Verarbeitung in der Pipeline zur Videoanalyse komprimiert. 

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway

Die „Kameras-als-Sensoren“ und die Edge-Workloads werden lokal von Azure IoT Edge verwaltet, und der Kameradatenstrom wird von der Analysepipeline verarbeitet. Die Pipeline zur Verarbeitung der Videoanalyse in Azure IoT Edge bringt viele Vorteile mit sich, z. B. kürzere Reaktionszeit und niedriger Bandbreitenverbrauch, und dies führt zu geringer Latenz und somit zu einer schnelleren Datenverarbeitung. Nur die wichtigsten Metadaten, Erkenntnisse oder Aktionen werden für weitere Maßnahmen oder zur Untersuchung an die Cloud gesendet. 

### <a name="device-management-with-iot-central"></a>Geräteverwaltung mit IoT Central
 
Azure IoT Central ist eine Entwicklungsplattform für Lösungen, die die Konnektivität, Konfiguration und Verwaltung von IoT-Geräten und Azure IoT Edge-Gateways vereinfacht. Die Plattform verringert Aufwand und Kosten von Entwicklungen für IoT-Geräteverwaltung, -betrieb und verwandte Entwicklungen erheblich. Kunden und Partner können eine End-to-End-Enterprise-Lösung aufbauen, um eine digitale Feedbackschleife in Verteilzentren zu verwirklichen.

### <a name="business-insights-and-actions-using-data-egress"></a>Geschäftliche Erkenntnisse und Aktionen über ausgehende Daten 

Die IoT Central-Plattform bietet umfassende Optionen zur Erweiterbarkeit über CDE (Continuous Data Export) und APIs. Geschäftserkenntnisse, die auf der Verarbeitung von Telemetriedaten oder auf unverarbeiteter Telemetrie basieren, werden normalerweise in eine bevorzugte Branchenanwendung exportiert. Dies kann per Webhook, Service Bus, Event Hub oder Blobspeicher erreicht werden, um Machine Learning-Modelle zu erstellen, zu trainieren, bereitzustellen und weitere Erkenntnisse zu gewinnen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]

> * Erstellen einer Anwendung für digitale Verteilzentren
> * Einführung in die Anwendung

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App sind keine besonderen Voraussetzungen erforderlich.
* Ein Azure-Abonnement ist empfehlenswert, Sie können es aber auch ohne versuchen.

## <a name="create-digital-distribution-center-application-template"></a>Erstellen einer Anwendungsvorlage für digitale Verteilzentren

Gehen Sie wie folgt vor, um die Anwendung zu erstellen:

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und anschließend die Registerkarte **Einzelhandel** aus.

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="Screenshot: Erstellen einer App":::

1. Wählen Sie unter **Digitales Verteilzentrum** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung 

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard

Das Standarddashboard ist ein Portal für den Betreiber eines Verteilzentrums. Northwind Trader ist ein fiktiver Lösungsanbieter für Verteilzentren, der Transportsysteme verwaltet. 

Auf diesem Dashboard sehen Sie ein Gateway und eine Kamera als IoT-Gerät. Das Gateway stellt Telemetriedaten zu Paketen bereit, z. B. gültig, ungültig, nicht identifiziert und Größe, sowie zugehörige Eigenschaften des Gerätezwillings. Alle Downstreambefehle werden auf IoT-Geräten ausgeführt, z. B. auf einer Kamera. Dieses Dashboard ist vorkonfiguriert, sodass die Aktivität von kritischen Vorgängen auf Geräten im Verteilzentrum hervorgehoben wird.

Das Dashboard ist logisch organisiert, um die Geräteverwaltungsfunktionen des Azure-IoT-Gateways und des IoT-Geräts anzuzeigen.  

* Sie können Gatewaybefehle und -steuerungsaufgaben ausführen.
* Verwalten Sie alle Kameras, die zu der Lösung gehören.
* Verwalten Sie alle Kameras, die zu der Lösung gehören.
* Verwalten Sie alle Kameras, die zu der Lösung gehören.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="Screenshot: Dashboard für das digitale Verteilzentrum":::

### <a name="device-template"></a>Gerätevorlage

Klicken Sie auf die Registerkarte „Gerätevorlagen“, dann wird das Gatewayfunktionsmodell angezeigt. Ein Funktionsmodell ist um zwei unterschiedliche Schnittstellen strukturiert: **Kamera** und **Digital Distribution Gateway**.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Screenshot: Gerätevorlage „Digital Distribution Gateway“ in der Anwendung":::

**Kamera:** Diese Schnittstelle organisiert alle kameraspezifischen Befehlsfunktionen. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Screenshot: Kameraschnittstelle in der Gerätevorlage „Digital Distribution Gateway“":::

**Digital Distribution Gateway:** Diese Schnittstelle stellt die Telemetriedaten von der Kamera, für die Cloud definierte Gerätezwillingseigenschaften und Gatewayinformationen dar.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Screenshot: Schnittstelle „Digital Distribution Gateway“ in der Gerätevorlage „Digital Distribution Gateway“":::

### <a name="gateway-commands"></a>Gatewaybefehle

Diese Schnittstelle organisiert alle Gatewaybefehlsfunktionen.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Screenshot: Schnittstelle für Gatewaybefehle in der Gerätevorlage „Digital Distribution Gateway“":::

### <a name="rules"></a>Regeln

Wählen Sie die Registerkarte „Regeln“ aus, um zwei verschiedene Regeln anzuzeigen, die in dieser Anwendungsvorlage vorhanden sind. Diese Regeln sind so konfiguriert, dass sie zwecks weiterer Untersuchungen E-Mail-Benachrichtigungen an die Operatoren senden.

 **Warnung über zu viele ungültige Pakete:** Diese Regel wird ausgelöst, wenn die Kamera eine große Anzahl von ungültigen Paketen erkennt, die sich durch das Transportsystem ziehen.

**Großes Paket:** Diese Regel wird auslöst, wenn die Kamera ein extrem großes Paket erkennt, das nicht auf Qualität überprüft werden kann. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="Screenshot: Liste der Regeln in der Anwendung für digitale Verteilzentren":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, löschen Sie die Anwendungsvorlage, indem Sie **Verwaltung** > **Anwendungseinstellungen** besuchen und auf **Löschen** klicken.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="Screenshot: Löschen der Anwendung, wenn Sie sie nicht mehr benötigen.":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Lösungsarchitektur digitaler Verteilzentren finden Sie hier:

> [!div class="nextstepaction"]
> [Architektur der IoT Central-Anwendungsvorlage für digitale Verteilzentren](./architecture-digital-distribution-center.md)

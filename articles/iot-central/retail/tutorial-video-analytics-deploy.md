---
title: 'Tutorial: Azure IoT-Videoanalyse: Objekt- und Bewegungserkennung | Microsoft-Dokumentation'
description: 'In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ für IoT Central bereitstellen und verwenden.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473746"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“

Hier finden Sie eine Übersicht über die Anwendungsvorlage *Videoanalyse: Objekt- und Bewegungserkennung*. Die Anwendungsvorlage **Videoanalyse: Objekt- und Bewegungserkennung** ermöglicht die Erstellung von IoT-Lösungen mit Funktionen für die Analyse von Livevideos.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Abbildung der Übersicht über die Objekt- und Bewegungserkennungskomponenten der Videoanalyse":::

Die Videoanalyselösung umfasst folgende wichtige Komponenten:

### <a name="live-video-analytics-lva"></a>Live Video Analytics (LVA)

LVA bietet eine Plattform für die Erstellung intelligenter Videoanwendungen, die den Edgebereich und die Cloud umfassen. Mithilfe der Plattform können Sie intelligente Videoanwendungen erstellen, die den Edgebereich und die Cloud umfassen. Die Plattform bietet die Möglichkeit, Livevideos zu erfassen, aufzuzeichnen und zu analysieren sowie die Ergebnisse (Video oder Videoanalyse) in Azure-Diensten zu veröffentlichen. Die Azure-Dienste können in der Cloud oder im Edgebereich ausgeführt werden. Über die Plattform können IoT-Lösungen durch Videoanalyse verbessert werden.

Weitere Informationen finden Sie unter [Live Video Analytics](https://github.com/Azure/live-video-analytics) auf GitHub.

### <a name="iot-edge-lva-gateway-module"></a>IoT Edge-LVA-Gatewaymodul

Das IoT Edge-LVA-Gatewaymodul instanziiert Kameras als neue Geräte und verbindet sie unter Verwendung des IoT-Geräteclient-SDK direkt mit IoT Central.

In dieser Referenzimplementierung werden von den Geräten symmetrische Schlüssel aus dem Edgebereich verwendet, um eine Verbindung mit der Lösung herzustellen. Weitere Informationen zur Gerätekonnektivität finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](../core/concepts-get-connected.md)

### <a name="media-graph"></a>Mediendiagramm

Mithilfe des Mediendiagramms können Sie definieren, von wo aus die Medien erfasst, wie Sie verarbeitet und wohin die Ergebnisse übermittelt werden sollen. Verbinden Sie zum Konfigurieren des Mediendiagramms Komponenten (oder Knoten) nach Bedarf. Weitere Informationen finden Sie unter [Mediendiagramm](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) auf GitHub.

Das folgende Video enthält eine exemplarische Vorgehensweise zur Verwendung der _Videoanalyse: Objekt- und Bewegungserkennung_, um eine IoT Central-Lösung bereitzustellen:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

In diesen Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen der Anwendung
> * Bereitstellen einer IoT Edge-Instanz, die eine Verbindung mit der Anwendung herstellt
> * Überwachen und Verwalten der Anwendung

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Gehen Sie wie folgt vor, um eine IoT Central-Anwendung unter Verwendung der Vorlage für eine Videoanalyseanwendung bereitzustellen:

1. Schließen Sie eines der folgenden Tutorials ab: [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) oder [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md). Damit haben Sie folgende Möglichkeiten:
    - Erstellen eines Azure Media Services-Kontos
    - Erstellen der IoT Central-Anwendung auf der Grundlage der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“
    - Konfigurieren eines Gatewaygeräts in der IoT Central-Anwendung. Über das Gateway können Kamerageräte eine Verbindung mit der Anwendung herstellen.

1. Schließen Sie entweder das [Tutorial zum Erstellen einer IoT Edge-Instanz für die Videoanalyse (Linux-VM)](tutorial-video-analytics-iot-edge-vm.md) oder das [Tutorial: Erstellen einer IoT Edge-Instanz für die Videoanalyse (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) ab, um folgende Aktionen durchzuführen:
    - Erstellen eines virtuellen Azure-Computers mit installierter Azure IoT Edge-Runtime und Vorbereiten der IoT Edge-Installation zum Hosten des Videoanalysemoduls
    - Verbinden des IoT Edge-Geräts mit Ihrer IoT Central-Anwendung

1. Führen Sie im Rahmen des [Tutorials zum Überwachen und Verwalten einer Videoanalyseanwendung](tutorial-video-analytics-manage.md) die folgenden Schritte aus:
    - Hinzufügen von Kameras für die Objekt- und Bewegungserkennung zum Gateway in Ihrer IoT Central-Anwendung
    - Starten der Kameraverarbeitung
    - Installieren eines lokalen Media Players für die Betrachtung aufgezeichneter Videos in AMS
    - Betrachten aufgezeichneter Videos mit erkannten Objekten
    - Bereinigen

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Anwendung nicht mehr benötigen, können Sie alle erstellten Ressourcen wie folgt entfernen:

1. Navigieren Sie in der IoT Central-Anwendung zur Seite **Ihre Anwendung** im Abschnitt **Verwaltung**. Wählen Sie anschließend die Option **Löschen**.
1. Löschen Sie im Azure-Portal die Ressourcengruppe **lva-rg**.
1. Beenden Sie auf Ihrem lokalen Computer den Docker-Container **amp-viewer**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben hier einen Überblick über die Schritte zum Bereitstellen und Verwenden der Vorlage für eine Videoanalyseanwendung erhalten. Sehen Sie sich nun die folgenden Artikel zu den ersten Schritte an:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) oder

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md)
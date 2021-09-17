---
title: 'Tutorial: Micro-Fulfillment-Center in Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage für Micro-Fulfillment-Center für IoT Central bereitstellen und nutzen.
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 09/01/2021
ms.openlocfilehash: 9eb70a08679427af9e91ffd6df3cd8f827cc76cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476302"
---
# <a name="tutorial-deploy-and-walk-through-the-micro-fulfillment-center-application-template"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendungsvorlage für Micro-Fulfillment-Center

Verwenden Sie die IoT Central-Anwendungsvorlage für *Micro-Fulfillment-Center* und die Anweisungen in diesem Artikel, um eine End-to-End-Lösung für Micro-Fulfillment-Center zu entwickeln.

![Azure IoT Central Store Analytics](./media/tutorial-micro-fulfillment-center-app/micro-fulfillment-center-architecture-frame.png)

1. Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
2. Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
3. Kontinuierlicher Datenexport an den gewünschten Azure-Dienst zur Bearbeitung
4. Daten können im gewünschten Format strukturiert und an einen Speicherdienst gesendet werden
5. Geschäftsanwendungen können Daten abfragen und Erkenntnisse generieren, die Einzelhandelsvorgänge stützen

### <a name="robotic-carriers"></a>Transportroboter

Eine Micro-Fulfillment-Center-Lösung verfügt wahrscheinlich über zahlreiche Transportroboter, die verschiedene Telemetriesignale generieren. Diese Signale können von einem Gatewaygerät erfasst, aggregiert und anschließend an IoT Central gesendet werden, wie auf der linken Seite des Architekturdiagramms zu sehen.  

### <a name="condition-monitoring-sensors"></a>Sensoren zur Zustandsüberwachung

Eine IoT-Lösung beginnt mit einer Reihe von Sensoren, die aussagekräftige Signale aus Ihrem Fulfillment-Center erfassen. Dies wird durch verschiedene Arten von Sensoren ganz am linken Rand des Architekturdiagramms oben wiedergegeben.

### <a name="gateway-devices"></a>Gatewaygeräte

Viele IoT-Sensoren können unformatierte Signale direkt in die Cloud oder in ein Gatewaygerät in ihrer Nähe einspeisen. Das Gatewaygerät führt vor dem Senden von zusammengefassten Einblicken an eine IoT Central-Anwendung eine Datenaggregation auf dem Edge aus. Die Gatewaygeräte sind ggf. außerdem für die Weiterleitung von Befehls- und Steuerungsvorgängen an die Sensorgeräte zuständig. 

### <a name="iot-central-application"></a>IoT Central-Anwendung

Die Azure IoT Central-Anwendung erfasst Daten von verschiedenen IoT-Sensoren, Robotern und Gatewaygeräten innerhalb der Fulfillment-Center-Umgebung und generiert eine Reihe aussagekräftiger Erkenntnisse.

Azure IoT Central bietet darüber hinaus eine maßgeschneiderte Benutzeroberfläche für den Betreiber des Geschäfts, die ihm die Überwachung und Verwaltung der Infrastrukturgeräte aus der Ferne erlaubt.

### <a name="data-transform"></a>Datentransformation

Die Azure IoT Central-Anwendung kann in einer Lösung so konfiguriert werden, dass sie unformatierte oder aggregierte Erkenntnisse an eine Reihe von Azure-PaaS-Diensten (Platform-as-a-Service) exportiert, die die Daten bearbeiten und die Erkenntnisse anreichern können, bevor sie in einer Geschäftsanwendung landen. 

### <a name="business-application"></a>Geschäftsanwendung

Die IoT-Daten können die Basis verschiedener Geschäftsanwendungen bilden, die in einer Einzelhandelsumgebung bereitgestellt sind. Ein Manager oder Mitarbeiter eines Fulfillment-Centers kann diese Anwendungen nutzen, um geschäftliche Erkenntnisse zu visualisieren und in Echtzeit zielgerichtete Maßnahmen zu ergreifen. Informationen zum Erstellen eines Power BI-Echtzeit-Dashboards für Ihr Einzelhandelsteam finden Sie in [diesem Tutorial](./tutorial-in-store-analytics-create-app.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]

> * Bereitstellen der Anwendungsvorlage
> * Verwenden der Anwendungsvorlage

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-micro-fulfillment-application"></a>Erstellen einer Anwendung für ein Micro-Fulfillment-Center

Gehen Sie wie folgt vor, um die Anwendung zu erstellen:

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und anschließend die Registerkarte **Einzelhandel** aus.

   :::image type="content" source="media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png" alt-text="Screenshot: Erstellen einer App":::

1. Wählen Sie unter **Micro-Fulfillment-Center** die Option **App erstellen** aus.

## <a name="walk-through-the-application"></a>Einführung in die Anwendung 

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

Nach erfolgreicher Bereitstellung der App-Vorlage wird das **Micro-Fulfillment-Center-Dashboard für Northwind Traders** angezeigt. Northwind Traders ist ein fiktiver Einzelhändler, der über ein in dieser Azure IoT Central-Anwendung verwaltetes Micro-Fulfillment-Center verfügt. Dieses Dashboard enthält Informationen und Telemetriedaten zu den Geräten in dieser Vorlage sowie die verfügbaren Befehle, Aufträge und Aktionen. Das Dashboard ist logisch in zwei Abschnitte unterteilt. Auf der linken Seite können Sie die Umgebungsbedingungen innerhalb der Fulfillment-Struktur und auf der rechten Seite den Status eines Transportroboters in Ihrer Einrichtung überwachen.  

Das Dashboard ermöglicht Folgendes:
   * Anzeigen von Gerätetelemetriedaten (etwa die Anzahl von Entnahmen und verarbeiteten Aufträgen) und Eigenschaften (etwa den Struktursystemstatus)  
   * Anzeigen des Grundrisses und der Position der Transportroboter innerhalb der Fulfillment-Struktur
   * Auslösen von Befehlen – etwa zum Zurücksetzen des Steuerungssystems, zum Aktualisieren der Firmwareversion eines Transportroboters oder zum Ändern der Netzwerkkonfiguration

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-1.png" alt-text="Screenshot: Obere Hälfte des Dashboards des Micro-Fulfillment-Centers von Northwind Traders":::
   * Betrachten eines Beispiels für das Dashboard, über das ein Operator die Bedingungen innerhalb des Fulfillment-Centers überwachen kann
   * Überwachen der Integrität der Nutzlasten, die auf dem Gatewaygerät innerhalb des Fulfillment-Centers ausgeführt werden

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-2.png" alt-text="Screenshot: Untere Hälfte des Dashboards des Micro-Fulfillment-Centers von Northwind Traders.":::


### <a name="device-template"></a>Gerätevorlage

Wenn Sie die Registerkarte „Gerätevorlagen“ auswählen, werden für die Vorlage zwei unterschiedliche Gerätetypen angezeigt: 
   * **Robotic Carrier** (Transportroboter): Diese Gerätevorlage stellt die Definition eines funktionierenden Transportroboters dar, der in der Fulfillment-Struktur eingesetzt wird und ordnungsgemäß funktioniert. Wenn Sie die Vorlage auswählen, sehen Sie, dass von dem Roboter Gerätedaten wie Temperatur und Achsenposition sowie Eigenschaften wie etwa der Status des Transportroboters gesendet werden. 
   * **Structure Condition Monitoring** (Überwachung der Strukturbedingungen): Diese Gerätevorlage stellt eine Sammlung von Geräten dar, mit deren Hilfe Sie sowohl Umgebungsbedingungen als auch das Gatewaygerät überwachen können, von dem verschiedene Edgeworkloads für den Betrieb Ihres Fulfillment-Centers gehostet werden. Das Gerät sendet Telemetriedaten wie etwa die Temperatur, die Anzahl von Entnahmen und die Anzahl von Aufträgen. Darüber hinaus sendet es Informationen zum Zustand und zur Integrität der in Ihrer Umgebung ausgeführten Computeworkloads. 

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/device-templates.png" alt-text="Gerätevorlagen für das Micro-Fulfillment-Center":::



Wenn Sie die Registerkarte „Gerätegruppen“ auswählen, sehen Sie auch, dass für diese Gerätevorlagen automatisch Gerätegruppen erstellt werden.

### <a name="rules"></a>Regeln

Auf der Registerkarte **Regeln** sehen Sie eine Beispielregel, die in der Anwendungsvorlage vorhanden ist, um die Temperaturbedingungen für den Transportroboter zu überwachen. Diese Regel kann verwendet werden, um den Operator zu benachrichtigen, wenn ein bestimmter Roboter in der Einrichtung zu heiß wird und zu Wartungszwecken offline geschaltet werden muss. 

Nutzen Sie die Beispielregel als Orientierungshilfe, um Regeln zu definieren, die besser zu Ihren geschäftlichen Funktionen passen.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/rules.png" alt-text="Screenshot: Registerkarte „Regeln“":::

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die Anwendungsvorlage. Navigieren Sie zu **Verwaltung** > **Anwendungseinstellungen**, und wählen Sie **Löschen** aus.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/delete.png" alt-text="Screenshot: Seite „Anwendungseinstellungen“ für das Micro-Fulfillment-Center":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

> [!div class="nextstepaction"]
> [Micro-Fulfillment-Center-Lösungsarchitektur](./architecture-micro-fulfillment-center.md)

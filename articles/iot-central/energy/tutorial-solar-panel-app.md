---
title: 'Tutorial: Azure IoT – Überwachung von Solarpanels | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage zum Überwachen von Solarpanels für IoT Central bereitstellen und nutzen.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182777"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Bereitstellen und Durchlaufen der App-Vorlage für die Überwachung von Solarpanels 

Verwenden Sie die IoT Central-Anwendungsvorlage zum *Überwachen von Solarpanels* und die Anleitungen in diesem Artikel, um eine End-to-End-Lösung für die Überwachung von Solarpanels zu entwickeln.

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="Solarpanelarchitektur.":::

Diese Architektur umfasst die folgenden Komponenten. Einige Anwendungen erfordern möglicherweise nicht alle der hier aufgeführten Komponenten.

### <a name="solar-panels-and-connectivity"></a>Solarpanels und Konnektivität

Solarpanels sind eine wichtige Quelle für erneuerbare Energien. Je nach Art und Aufbau der Solarpanels können Sie eine Verbindung entweder über Gateways oder über andere zwischengeschaltete Geräte und proprietäre Systeme herstellen. Unter Umständen wird eine IoT Central-Geräte-Bridge benötigt, um Geräte zu verbinden, die nicht direkt verbunden werden können. Bei der IoT Central-Gerätebridge handelt es sich um eine Open-Source-Lösung. Die Details finden Sie [hier](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>IoT Central-Plattform

Azure IoT Central ist eine Plattform, die das Erstellen Ihrer IoT-Lösungen vereinfacht und den Aufwand und die Kosten für die Verwaltung, den Betrieb und die Entwicklung von IoT-Lösungen senkt. Mit IoT Central können Sie Ihre IoT-Ressourcen (Internet of Things) einfach und in jeder Größenordnung verbinden, überwachen und verwalten. Nachdem Sie Ihre Solarpanels mit IoT Central verbunden haben, verwendet die App-Vorlage integrierte Features wie Gerätemodelle, Befehle und Dashboards. Die App-Vorlage verwendet außerdem den IoT Central-Speicher für Szenarien mit warmen Pfaden, z. B. Datenüberwachung nahezu in Echtzeit, Analysen, Regeln und Visualisierung.

### <a name="extensibility-options-to-build-with-iot-central"></a>Erweiterbarkeitsoptionen für die Erstellung mit IoT Central

Die IoT Central-Plattform bietet zwei Erweiterbarkeitsoptionen: Kontinuierlichen Datenexport (Continuous Data Export, CDE) und APIs. Die Kunden und Partner können zwischen diesen Optionen auswählen, um ihre Lösungen an bestimmte Anforderungen anzupassen. Beispielsweise konfigurierte einer unserer Partner CDE mit Azure Data Lake Storage (ADLS). Er verwendet ADLS für die langfristige Datenaufbewahrung und andere Speicherszenarien mit kalten Pfaden, z. B. Batchverarbeitung, Überwachung und Berichterstellung. 


In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]

> * Kostenloses Erstellen einer Solarpanel-App
> * Einführung in die Anwendung
> * Bereinigen von Ressourcen


## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.


## <a name="create-a-solar-panel-monitoring-application"></a>Erstellen einer Anwendung für die Überwachung von Solarpanels


1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und dann die Registerkarte **Energieversorgung** aus:

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="Vorlage für intelligente Stromzähler":::

1. Wählen Sie unter **Überwachung von Solarpanels** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard

Nach Bereitstellung der Anwendungsvorlage sollten Sie die App etwas genauer betrachten. Wie Sie sehen, beinhaltet sie ein exemplarisches intelligentes Zählergerät, ein Gerätemodell und ein Dashboard.

Adatum ist ein fiktives Energieversorgungsunternehmen, das Solarpanel überwacht und verwaltet. Im Dashboard für die Überwachung von Solarpanels werden die entsprechenden Eigenschaften, Daten und Beispielbefehle angezeigt. Dieses Dashboard ermöglicht es Ihnen oder Ihrem Supportteam, folgende Aktivitäten proaktiv durchzuführen, bevor Probleme mit zusätzlichem Supportaufwand auftreten:
* Überprüfen der aktuellen Bereichsinformationen und des [ Installationsorts](../core/howto-use-location-data.md) auf der Karte.
* Überprüfen von Panel- und Verbindungsstatus
* Überprüfen der Trends bei Energieerzeugung und Temperatur, um Anomalien zu erkennen
* Nachverfolgen der gesamten Energieerzeugung zu Planungs- und Abrechnungszwecken
* Aktivieren eines Panels und Aktualisieren der Firmwareversion bei Bedarf In der Vorlage werden über die Befehlsschaltflächen die möglichen Funktionen angezeigt, jedoch keine echten Befehle gesendet.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="Screenshot: Vorlage für die Überwachung von Solarpaneln: Dashboard":::

### <a name="devices"></a>Geräte

Die App enthält ein Beispielgerät für ein Solarpanel. Wählen Sie zum Anzeigen von Gerätedetails die Option **Geräte** aus.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="Screenshot: Vorlage für die Überwachung von Solarpaneln: Geräte":::

Wählen Sie das Beispielgerät (**SP0123456789**) aus. Auf der Registerkarte **Update Properties** (Eigenschaften aktualisieren) können Sie die schreibbaren Eigenschaften des Geräts aktualisieren und die aktualisierten Werte im Dashboard anzeigen. 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="Screenshot: Vorlage für die Überwachung von Solarpaneln: Registerkarte zum Aktualisieren von Eigenschaften":::


### <a name="device-template"></a>Gerätevorlage

Wählen Sie die Registerkarte **Gerätevorlagen** aus, um das Gerätemodell für Solarpanel anzuzeigen. Das Modell verfügt über vordefinierte Schnittstellen für Daten, Eigenschaften, Befehle und Ansichten.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="Screenshot: Vorlage für die Überwachung von Solarpaneln: Gerätevorlagen":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr benötigen, können Sie sie mit den folgenden Schritten löschen:

1. Wählen Sie im linken Bereich die Option **Verwaltung** aus.
1. Wählen Sie **Anwendungseinstellungen** > **Löschen** aus. 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="Screenshot: Vorlage für die Überwachung von Solarpaneln: Verwaltung":::

---
title: 'Tutorial: Azure IoT – Überwachung von intelligenten Stromzählern | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage zum Überwachen von intelligenten Stromzählern für IoT Central bereitstellen und nutzen.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179486"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Bereitstellen und Durchlaufen der App-Vorlage zum Überwachen von intelligenten Stromzählern 

Verwenden Sie die IoT Central-Anwendungsvorlage zum *Überwachen von intelligenten Stromzählern* und die Anleitungen in diesem Artikel, um eine End-to-End-Lösung für die Überwachung von intelligenten Stromzählern zu entwickeln.

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="Architektur für intelligente Stromzähler.":::

Diese Architektur umfasst die folgenden Komponenten. Für einige Lösungen sind möglicherweise nicht alle der hier aufgeführten Komponenten erforderlich.

### <a name="smart-meters-and-connectivity"></a>Intelligente Stromzähler und Konnektivität

Ein intelligenter Stromzähler zählt unter den Energieressourcen zu den wichtigsten Geräten. Er zeichnet Daten zum Energieverbrauch auf und kommuniziert sie an Hilfsprogramme zur Überwachung und für andere Anwendungen, wie etwa Abrechnung und Reaktion auf die Nachfrage. Der Stromzähler kann je nach Typ entweder über Gateways oder über andere zwischengeschaltete Geräte oder Systeme (z. B. Edgegeräte und Head-End-Systeme) eine Verbindung mit IoT Central herstellen. Erstellen Sie eine IoT Central-Geräte-Bridge, um Geräte zu verbinden, die nicht direkt verbunden werden können. Bei der IoT Central-Gerätebridge handelt es sich um eine Open-Source-Lösung. Die Details finden Sie [hier](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>IoT Central-Plattform

Azure IoT Central ist eine Plattform, die das Erstellen Ihrer IoT-Lösungen vereinfacht und den Aufwand und die Kosten für die Verwaltung, den Betrieb und die Entwicklung von IoT-Lösungen senkt. Mit IoT Central können Sie Ihre IoT-Ressourcen (Internet of Things) einfach und in jeder Größenordnung verbinden, überwachen und verwalten. Nachdem Sie Ihre intelligenten Stromzähler mit IoT Central verbunden haben, verwendet die App-Vorlage integrierte Features wie Gerätemodelle, Befehle und Dashboards. Die App-Vorlage verwendet außerdem den IoT Central-Speicher für Szenarien mit warmen Pfaden, z. B. Datenüberwachung nahezu in Echtzeit, Analysen, Regeln und Visualisierung. 

### <a name="extensibility-options-to-build-with-iot-central"></a>Erweiterbarkeitsoptionen für die Erstellung mit IoT Central

Die IoT Central-Plattform bietet zwei Erweiterbarkeitsoptionen: Kontinuierlichen Datenexport (Continuous Data Export, CDE) und APIs. Die Kunden und Partner können zwischen diesen Optionen auswählen, um ihre Lösungen an bestimmte Anforderungen anzupassen. Beispielsweise konfigurierte einer unserer Partner CDE mit Azure Data Lake Storage (ADLS). Er verwendet ADLS für die langfristige Datenaufbewahrung und andere Speicherszenarien mit kalten Pfaden, z.B. Batchverarbeitung, Überwachung und Berichterstellung.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

- Kostenloses Erstellen einer App für intelligente Zähler
- Anwendungsablauf
- Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
* Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-a-smart-meter-monitoring-application"></a>Erstellen einer Anwendung zum Überwachen von intelligenten Stromzählern

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und dann die Registerkarte **Energieversorgung** aus:

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="Vorlage für intelligente Stromzähler":::

1. Wählen Sie unter **Überwachung von intelligenten Stromzählern** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard

Nachdem Sie die Anwendungsvorlage bereitgestellt haben, enthält sie ein Beispielgerät für einen intelligenten Stromzähler, ein Gerätemodell und ein Dashboard. 

Adatum ist ein fiktives Energieversorgungsunternehmen, das intelligente Zähler überwacht und verwaltet. Im Dashboard für die Überwachung von intelligenten Zählern werden die entsprechenden Eigenschaften, Daten und Beispielbefehle angezeigt. In diesem Dashboard können Bediener und Supportteams proaktiv die folgenden Aktivitäten durchführen, bevor daraus Supportfälle werden: 
* Überprüfen der aktuellen Informationen zum Zähler und dessen [Installationsort](../core/howto-use-location-data.md) auf der Karte
* Proaktives Überprüfen des Status des intelligenten Zählers und des Verbindungsstatus 
* Überwachen der minimalen und maximalen Spannungswerte für die Netzwerkintegrität 
* Überprüfen der Energie-, Leistung- und Spannungstrends zur Erfassung anomaler Muster 
* Nachverfolgen des gesamten Energieverbrauchs für Planungs- und Abrechnungszwecke
* Befehls- und Steuerungsvorgänge, z. B. erneutes Verbinden des Zählers und Aktualisieren der Firmwareversion In der Vorlage werden über die Befehlsschaltflächen die möglichen Funktionen angezeigt, jedoch keine realen Befehle gesendet. 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="Dashboard für die Überwachung von intelligenten Stromzählern.":::

### <a name="devices"></a>Geräte

Die App enthält ein Beispielgerät für einen intelligenten Zähler. Sie können die Gerätedetails anzeigen, indem Sie auf die Registerkarte **Geräte** klicken.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="Intelligente Stromzähler.":::

Klicken Sie auf den Link für das Beispielgerät **SM0123456789**, um die Gerätedetails anzuzeigen. Auf der Seite **Eigenschaften aktualisieren** können Sie die schreibbaren Eigenschaften des Gerätes aktualisieren und die aktualisierten Werte im Dashboard anzeigen.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="Eigenschaften des intelligenten Stromzählers.":::

### <a name="device-template"></a>Gerätevorlage

Klicken Sie auf die Registerkarte **Gerätevorlagen**, um das Gerätemodell für intelligente Zähler anzuzeigen. Das Modell umfasst eine vordefinierte Schnittstelle für Daten, Eigenschaften, Befehle und Ansichten.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="Gerätevorlagen für intelligente Stromzähler.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, können Sie sie mit den folgenden Schritten löschen:

1. Öffnen Sie im linken Bereich die Registerkarte „Verwaltung“.
1. Wählen Sie „Anwendungseinstellungen“ aus, und klicken Sie unten auf der Seite auf die Schaltfläche „Löschen“. 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="Löschen der Anwendung.":::

## <a name="next-steps"></a>Nächste Schritte

> [Tutorial: Bereitstellen und Durchlaufen einer Anwendungsvorlage für Solarpanels](tutorial-solar-panel-app.md)


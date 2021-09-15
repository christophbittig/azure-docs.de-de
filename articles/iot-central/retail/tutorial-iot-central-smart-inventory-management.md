---
title: 'Tutorial: Azure IoT – Intelligente Bestandsverwaltung | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage für intelligente Bestandsverwaltung für IoT Central bereitstellen und verwenden.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437506"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>Tutorial: Bereitstellen und Durchlaufen der Anwendungsvorlage für intelligente Bestandsverwaltung

Verwenden Sie die IoT Central-Anwendungsvorlage *Intelligente Bestandsverwaltung* und die Anleitungen in diesem Artikel, um eine End-to-End-Lösung für intelligente Bestandsverwaltung zu entwickeln.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="Intelligente Bestandsverwaltung.":::

1. Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
2. Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
3. Daten werden zur Bearbeitung an den gewünschten Azure-Dienst geroutet
4. Mithilfe von Azure-Diensten wie ASA oder Azure Functions können Datenströme neu formatiert und an die gewünschten Speicherkonten gesendet werden 
5. Verarbeitete Daten werden in der heißen Speicherebene für Aktionen in Quasi-Echtzeit oder in der kalten Zugriffsebene zur weiteren Erkenntnisverbesserung auf Grundlage von ML oder Batchanalyse gespeichert. 
6. Logik-Apps können verwendet werden, um verschiedene Geschäftsworkflows in Endbenutzer-Geschäftsanwendungen zu unterstützen

### <a name="details"></a>Details

Im folgenden Abschnitt wird jeder Teil der Telemetrieerfassung in der konzeptionellen Architektur aus RFID- (Radio-frequency Identification) und BLE-Transpondern (Bluetooth Low Energy) umrissen.

### <a name="rfid-tags"></a>RFID-Tags

RFID-Transponder übertragen Daten zu einem Element durch Funkwellen. RFID-Transponder weisen, sofern nicht anders angegeben, in der Regel keinen Akku auf. Transponder empfangen Energie aus den Funkwellen, die vom Leser generiert werden und übertragen ein Signal zurück zum RFID-Leser.

### <a name="ble-tags"></a>BLE-Transponder

Energiefunkfeuer senden Datenpakete in regelmäßigen Abständen. Die Funkfeuerdaten werden von BLE-Lesern oder auf Smartphone installierten Diensten erkannt, die sie dann an die Cloud übertragen.

### <a name="rfid--ble-readers"></a>RFID- und BLE-Leser

Der RFID-Leser konvertiert die Funkwellen in eine besser verwendbare Datenform. Die von den Transpondern gesammelten Informationen werden dann auf einem lokalen Edgeserver gespeichert oder mithilfe von JSON-RPC 2.0 über MQTT an die Cloud gesendet.
BLE-Leser, die auch als Zugriffspunkte (Access Points, AP) bezeichnet werden, sind RFID-Lesern ähnlich. Diese werden verwendet, um Bluetooth-Signale in der Nähe zu erkennen und ihre Nachricht mithilfe von JSON-RPC 2.0 über MQTT an eine lokale Azure IoT Edge-Instanz oder die Cloud weiterzugeben.
Viele Leser sind imstande, RFID- und Beacon-Signale zu lesen sowie weitere Sensorfunktionen bereitzustellen, etwa für Temperatur, Feuchtigkeit, Beschleunigungsmesser und Drehrate.

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-Gateway

Azure IoT Edge bietet einen Ort für die lokale Vorverarbeitung dieser Daten, bevor sie in die Cloud gesendet werden. Ferner können künstliche Intelligenz aus Cloudworkloads, Azure und Diensten von Drittanbietern sowie Geschäftslogik mithilfe von Standardcontainern bereitgestellt werden.

### <a name="device-management-with-iot-central"></a>Geräteverwaltung mit IoT Central 

Azure IoT Central ist eine Plattform für die Lösungsentwicklung, die die Konnektivität, Konfiguration und Verwaltung von IoT-Geräten vereinfacht. Die Plattform verringert Aufwand und Kosten von Entwicklungen für IoT-Geräteverwaltung, -betrieb und verwandte Entwicklungen erheblich. Kunden und Partner können eine End-to-End-Enterprise-Lösung aufbauen, um eine digitale Feedbackschleife in der Bestandsverwaltung zu verwirklichen.

### <a name="business-insights--actions-using-data-egress"></a>Geschäftliche Erkenntnisse und Aktionen über ausgehende Daten 

Die IoT Central-Plattform bietet umfassende Optionen zur Erweiterbarkeit über CDE (Continuous Data Export) und APIs. Geschäftserkenntnisse, die auf der Verarbeitung von Telemetriedaten oder auf unverarbeiteter Telemetrie basieren, werden normalerweise in eine bevorzugte Branchenanwendung exportiert. Dies kann per Webhook, Service Bus, Event Hub oder Blobspeicher erreicht werden, um Machine Learning-Modelle zu erstellen, zu trainieren, bereitzustellen und weitere Erkenntnisse zu gewinnen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]

> * Erstellen einer Anwendung für intelligente Bestandsverwaltung 
> * Einführung in die Anwendung 

## <a name="prerequisites"></a>Voraussetzungen

* Zum Bereitstellen dieser App sind keine besonderen Voraussetzungen erforderlich.
* Ein Azure-Abonnement ist empfehlenswert, Sie können es aber auch ohne versuchen.

## <a name="create-smart-inventory-management-application"></a>Erstellen einer Anwendung für intelligente Bestandsverwaltung

Führen Sie die folgenden Schritte zum Erstellen der Anwendung aus:

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie in der Navigationsleiste auf der linken Seite **Erstellen** und dann die Registerkarte **Einzelhandel** aus: :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="Screenshot: Erstellen einer App aus der Anwendungsvorlage für intelligente Bestandsverwaltung":::

1. Wählen Sie unter **Intelligente Bestandsverwaltung** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboard"></a>Dashboard 

Nach erfolgreicher Bereitstellung der App-Vorlage ist das Standarddashboard ein Portal für den Betreiber intelligenter Bestandsverwaltung. Northwind Trader ist ein fiktiver Anbieter von intelligenter Bestandsverwaltung, der Lagerräume mit Bluetooth Low Energy (BLE) und Einzelhandelsfilialen mit RFID (Radio Frequency Identification) verwaltet. Auf diesem Dashboard sehen Sie zwei verschiedene Gateways, die Telemetrie zum Bestand zusammen mit zugeordneten Befehlen, Aufträgen und Aktionen bereitstellen, die Sie ausführen können. Dieses Dashboard ist vorkonfiguriert, sodass die Aktivität von kritischen Vorgängen bei der intelligenten Bestandsverwaltung hervorgehoben wird.
Das Dashboard ist logisch in zwei verschiedene Gateway-Geräteverwaltungsvorgänge unterteilt. 
   * Das Lager wird mit einem festen BLE-Gateway und BLE-Tags auf den Paletten bereitgestellt, um den Bestand in einer größeren Einrichtung nachverfolgen zu können.
   * Die Einzelhandelsfiliale wird mit einem festen RFID-Gateway und RFID-Tags auf Ebene der einzelnen Posten implementiert, um den Bestand in einem Outlet nachzuverfolgen.
  * Anzeigen von dem [Gatewaystandort](../core/howto-use-location-data.md), dem Status und den zugehörigen Details 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="Screenshot: Obere Hälfte des Dashboards für die intelligente Bestandsverwaltung.":::

    * Sie können die Gesamtanzahl der Gateways, der aktiven und der unbekannten Tags einfach nachverfolgen.
    * Sie können Vorgänge der Geräteverwaltung durchführen, beispielsweise Firmwareupdates, Deaktivieren und Aktivieren von Sensoren sowie Aktualisieren von Sensorschwellenwerten, Telemetrieintervallen und Geräteserviceverträgen.
    * Gatewaygeräte können mithilfe eines vollständigen oder inkrementellen Scans bedarfsgesteuert die Bestandsverwaltung durchführen.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="Screenshot: Untere Hälfte des Dashboards für die intelligente Bestandsverwaltung.":::

### <a name="device-template"></a>Gerätevorlage

Klicken Sie auf die Registerkarte „Gerätevorlagen“, dann wird das Gatewayfunktionsmodell angezeigt. Ein Funktionsmodell ist um zwei unterschiedliche Schnittstellen strukturiert: **Gatewaytelemetrie und -eigenschaften** und **Gatewaybefehle**.

**Gatewaytelemetrie und -eigenschaften:** Diese Schnittstelle stellt die gesamte Telemetrie im Zusammenhang mit Sensor-, Standort- und Geräteinformationen sowie Gerätezwillings-Eigenschaftsfunktionen wie Gatewayschwellenwerten und Aktualisierungsintervallen dar.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="Screenshot: Gatewaygerätevorlage für den Bestand in der Anwendung.":::

**Gatewaybefehle:** Diese Schnittstelle organisiert alle Gatewaybefehlsfunktionen.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="Screenshot: Schnittstelle für Gatewaybefehle in der Gatewaygerätevorlage für den Bestand.":::

### <a name="rules"></a>Regeln

Wählen Sie die Registerkarte „Regeln“ aus, um zwei verschiedene Regeln anzuzeigen, die in dieser Anwendungsvorlage vorhanden sind. Diese Regeln sind so konfiguriert, dass sie zwecks weiterer Untersuchungen E-Mail-Benachrichtigungen an die Operatoren senden.

**Gateway offline:** Diese Regel wird ausgelöst, wenn das Gateway für einen längeren Zeitraum keine Meldung an die Cloud sendet. Das Gateway reagiert möglicherweise aufgrund eines niedrigen Akkuladestands, eines Verbindungsverlusts oder des Gerätezustands nicht.

**Unbekannte Tags:** Es ist wichtig, alle RFID- und BLE-Tags nachzuverfolgen, die einer Ressource zugeordnet sind. Wenn das Gateway zu viele unbekannte Tags erkennt, deutet dies auf Probleme bei der Synchronisierung in den Tagbeschaffungsanwendungen hin.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="Screenshot: Liste der Regeln in der Anwendung für intelligente Bestandsverwaltung.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, löschen Sie die Anwendungsvorlage, indem Sie **Verwaltung** > **Anwendungseinstellungen** besuchen und auf **Löschen** klicken.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="Screenshot: Löschen der Anwendung, wenn Sie sie nicht mehr benötigen.":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über die intelligente Bestandsverwaltung:

> [!div class="nextstepaction"]
> [Architektur der IoT Central-Anwendungsvorlage für intelligente Bestandsverwaltung](./architecture-smart-inventory-management.md)

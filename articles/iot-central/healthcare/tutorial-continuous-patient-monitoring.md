---
title: 'Tutorial: Kontinuierliche Patientenüberwachung in Azure IoT | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Anwendungsvorlage für die kontinuierliche Patientenüberwachung für IoT Central bereitstellen und nutzen.
author: philmea
ms.author: philmea
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 4d5b00526219b0a4ade24b7522ea4826236fd70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057759"
---
# <a name="tutorial-deploy-and-walkthrough-the-continuous-patient-monitoring-app-template"></a>Tutorial: Bereitstellen und Durchlaufen der App-Vorlage für die kontinuierliche Überwachung von Patienten

:::image type="content" source="media/cpm-architecture.png" alt-text="Architektur für die ständige Überwachung von Patienten":::

## <a name="bluetooth-low-energy-ble-medical-devices"></a>Medizinische Geräte mit Bluetooth Low Energy (BLE)

Bei vielen medizinischen Wearables, die in IoT-Lösungen im Gesundheitswesen zum Einsatz kommen, handelt es sich um BLE-Geräte. Diese Geräte können nicht direkt mit der Cloud kommunizieren und benötigen ein Gateway für den Datenaustausch mit Ihrer Cloudlösung. In dieser Architektur wird als Gateway eine Mobiltelefonanwendung verwendet.

## <a name="mobile-phone-gateway"></a>Mobiltelefongateway

Die Hauptfunktion der Mobiltelefonanwendung besteht darin, BLE-Daten von medizinischen Geräten zu erfassen und an IoT Central weiterzugeben. Die App führt Patienten außerdem durch die Geräteeinrichtung und ermöglicht es ihnen, ihre persönlichen Gesundheitsdaten anzuzeigen. Bei anderen Lösungen kann ein Tabletgateway oder ein statisches Gateway in einem Krankenzimmer verwendet werden. Für Android und iOS steht eine Open-Source-Beispielanwendung für Mobilgeräte zur Verfügung, die als Ausgangspunkt für die Anwendungsentwicklung verwendet werden kann. Weitere Informationen finden Sie in der mobilen Beispielanwendung [Continuous Patient Monitoring auf GitHub](https://github.com/iot-for-all/iotc-cpm-sample).

## <a name="export-to-azure-api-for-fhirreg"></a>Exportieren nach Azure API for FHIR&reg;

Azure IoT Central ist HIPAA-konform und für HITRUST&reg; zertifiziert. Per [Azure API for FHIR](../../healthcare-apis/fhir/overview.md) können Patientengesundheitsdaten auch an andere Dienste gesendet werden. Azure API for FHIR ist eine standardbasierte API für klinische Gesundheitsdaten. Mit dem [Azure IoT-Konnektor für FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) können Sie Azure API for FHIR als Ziel für den kontinuierlichen Datenexport über IoT Central verwenden.

## <a name="machine-learning"></a>Machine Learning

Verwenden Sie Machine Learning-Modelle mit Ihren FHIR-Daten, um Erkenntnisse zu gewinnen und Ihr Pflegeteam bei der Entscheidungsfindung zu unterstützen. Weitere Informationen finden Sie in der [Dokumentation zu Azure Machine Learning](../../machine-learning/index.yml).

## <a name="provider-dashboard"></a>Anbieterdashboard

Erstellen Sie mithilfe der Daten von Azure API for FHIR ein Dashboard für Patientenerkenntnisse, oder integrieren Sie sie direkt in eine elektronische Patientenakte für Pflegeteams. Pflegeteams können das Dashboard verwenden, um Patienten zu unterstützen und frühzeitig Anzeichen für eine Verschlechterung zu erkennen. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines Power BI-Anbieterdashboards](tutorial-health-data-triage.md).

In diesem Tutorial lernen Sie Folgendes:

- Erstellen einer Anwendungsvorlage
- Exemplarische Vorgehensweise für die Anwendungsvorlage

## <a name="prerequisites"></a>Voraussetzungen

- Zum Bereitstellen dieser App müssen keine besonderen Voraussetzungen erfüllt werden.
- Sie können den Tarif „Free“ oder ein Azure-Abonnement verwenden.

## <a name="create-continuous-patient-monitoring-application"></a>Erstellen einer Anwendung für die kontinuierliche Überwachung von Patienten

1. Navigieren Sie zur Buildwebsite für [Azure IoT Central](https://aka.ms/iotcentral). Melden Sie sich dann mit einem persönlichen Microsoft-Konto oder mit einem Geschäfts-, Schul- oder Unikonto an. Wählen Sie auf der linken Navigationsleiste **Erstellen** und dann die Registerkarte **Gesundheitswesen** aus: :::image type="content" source="media/app-manager-health.png" alt-text="Anwendungsvorlage":::

1. Wählen Sie unter **Kontinuierliche Patientenüberwachung** die Option **App erstellen** aus.

Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Einführung in die Anwendung

In den nächsten Abschnitten werden die wichtigsten Features der Anwendung behandelt:

### <a name="dashboards"></a>Dashboards

Nach der Bereitstellung der App-Vorlage gelangen Sie zunächst zum **Lamna-Dashboard für die Überwachung interner Patienten**. Lamna Healthcare ist ein fiktives Krankenhaus mit zwei Einrichtungen: Woodgrove Hospital und Burkville Hospital. Das Operatordashboard für Woodgrove Hospital bietet folgende Möglichkeiten:

* Zeigen Sie Gerätetelemetriedaten und -eigenschaften an, z. B. den **Akkustand** oder den **Konnektivitätsstatus** Ihres Geräts.

* Zeigen Sie den **Raumplan** und den Standort des Smart Vitals Patch-Geräts an.

* Führen Sie die **erneute Bereitstellung** des Smart Vitals Patch-Geräts für einen neuen Patienten durch.

* Zeigen Sie ein Beispiel für ein **Anbieterdashboard** an, das von einem Pflegeteam des Krankenhauses ggf. zum Nachverfolgen der Patienten genutzt wird.

* Ändern Sie den **Patientenstatus** Ihres Geräts, um anzugeben, ob das Gerät für einen internen oder externen Patienten verwendet wird.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Status stationärer Patienten":::

Sie können auch **Go to remote patient dashboard** (Zu Dashboard für externe Patienten wechseln) auswählen, um das zweite Operatordashboard für Burkville Hospital anzuzeigen. Dieses Dashboard enthält ähnliche Aktionen, Telemetriedaten und Informationen. Darüber hinaus werden mehrere verwendete Geräte angezeigt, für die Sie jeweils die **Firmware aktualisieren** können.

:::image type="content" source="media/lamna-remote.png" alt-text="Remote-Operatordashboard":::

### <a name="device-templates"></a>Gerätevorlagen

Wenn Sie **Gerätevorlagen** auswählen, werden die beiden Gerätetypen in der Vorlage angezeigt:

- **Smart Vitals Patch**: Bei diesem Gerät handelt es sich um ein Pflaster zur Messung verschiedener Vitalwerte. Es kann für die Überwachung von Patienten innerhalb und außerhalb des Krankenhauses verwendet werden. Wenn Sie die Vorlage auswählen, sehen Sie, dass das Pflaster Gerätedaten wie Akkustand und Gerätetemperatur sowie Daten zum Gesundheitszustand des Patienten wie Atemfrequenz und Blutdruck sendet.

- **Smart Knee Brace**: Bei diesem Gerät handelt es sich um eine Stützmanschette für das Knie, die von Patienten mit einem neuen Kniegelenk verwendet wird. Wenn Sie auf diese Vorlage klicken, werden Funktionen wie Gerätedaten, Bewegungsumfang und Beschleunigung angezeigt.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Smart Patch-Vorlage":::

### <a name="device-groups"></a>Gerätegruppen

Verwenden Sie Gerätegruppen, um mehrere Geräte logisch zu gruppieren und anschließend Massenabfragen oder -vorgänge für sie ausführen zu können.

Wenn Sie die Registerkarte „Gerätegruppen“ auswählen, wird für jede Gerätevorlage in der Anwendung eine Standardgerätegruppe angezeigt. Es wurden auch zwei zusätzliche Beispielgerätegruppen erstellt: eine für bereitzustellende Geräte (**Provision devices**) und eine für Geräte mit veralteter Firmware (**Devices with outdated firmware**). Diese Beispielgerätegruppen können als Eingaben für die Ausführung einiger [Aufträge](#jobs) in der Anwendung verwendet werden.

### <a name="rules"></a>Regeln

Wenn Sie **Regeln** auswählen, werden die drei Regeln in der Vorlage angezeigt:

- **Brace temperature high** (Hohe Manschettentemperatur): Diese Regel wird ausgelöst, wenn die Gerätetemperatur der intelligenten Kniemanschette fünf Minuten lang über 95 &deg;F (35 °C) liegt. Verwenden Sie diese Regel, um den Patienten und das Pflegeteam zu informieren und das Gerät per Remotesteuerung herunterzukühlen.

- **Fall detected** (Sturz erkannt): Diese Regel wird ausgelöst, wenn ein Sturz des Patienten erkannt wird. Verwenden Sie diese Regel, um eine Aktion zum Alarmieren eines Teams zu konfigurieren, das dem gestürzten Patienten zur Hilfe eilt.

- **Patch battery low** (Niedriger Akkustand des Patchgeräts): Diese Regel wird ausgelöst, wenn der Akkustand des Geräts unter zehn Prozent sinkt. Verwenden Sie diese Regel, um für den Patienten eine Benachrichtigung mit dem Hinweis auszulösen, dass er sein Gerät aufladen sollte.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Regeln":::

### <a name="jobs"></a>Aufträge

Aufträge ermöglichen die Ausführung von Massenvorgängen für eine Gruppe von Geräten. Dazu werden [Gerätegruppen](#device-groups) als Eingabe verwendet. Die Anwendungsvorlage verfügt über zwei Beispielaufträge, die ein Operator ausführen kann:

* **Update knee brace firmware** (Firmware der Kniemanschette aktualisieren): Dieser Auftrag ermittelt Geräte in der Gerätegruppe **Devices with outdated firmware** (Geräte mit veralteter Firmware) und führt einen Befehl aus, um diese Geräte auf die neueste Firmwareversion zu aktualisieren. Bei diesem Beispielauftrag wird davon ausgegangen, dass die Geräte einen **Aktualisierungsbefehl** verarbeiten und die Firmwaredateien anschließend aus der Cloud abrufen können.  

* **Re-provision devices** (Geräte erneut bereitstellen): Sie verfügen über eine Reihe von Geräten, die kürzlich an das Krankenhaus zurückgegeben wurden. Dieser Auftrag ermittelt Geräte in der Gerätegruppe **Provision Devices** (Geräte bereitstellen) und führt einen Befehl aus, um sie für die nächsten Patienten bereitzustellen.

### <a name="devices"></a>Geräte

Wählen Sie die Registerkarte **Geräte** und anschließend eine Instanz von **Smart Knee Brace** aus. Es sind drei Ansichten verfügbar, in denen Sie sich Informationen zum jeweils ausgewählten Gerät ansehen können. Diese Ansichten werden erstellt und veröffentlicht, wenn Sie die Gerätevorlage für Ihr Gerät erstellen. Daher sind diese Ansichten für alle Geräte, die Sie verbinden oder simulieren, einheitlich.

Die Ansicht **Dashboard** bietet eine Übersicht über für Operatoren relevante Telemetriedaten und Eigenschaften des Geräts.

Auf der Registerkarte **Eigenschaften** können Sie Cloudeigenschaften bearbeiten und Geräteeigenschaften lesen/schreiben.

Auf der Registerkarte **Befehle** können Sie Befehle auf dem Gerät ausführen.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Dashboard Kniemanschette":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr verwenden möchten, sollten Sie sie löschen, indem Sie unter **Verwaltung > Anwendungseinstellungen** auf **Löschen** klicken.

:::image type="content" source="media/admin-delete.png" alt-text="Bereinigen der Ressourcen":::

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über die Erstellung eines Anbieterdashboards zu informieren, mit dem eine Verbindung mit Ihrer IoT Central-Anwendung hergestellt wird.

> [!div class="nextstepaction"]
> [Erstellen eines Power BI-Anbieterdashboards](tutorial-health-data-triage.md)
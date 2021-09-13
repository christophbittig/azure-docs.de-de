---
title: Referenzarchitektur für die mit Azure IoT Central erstellte Lösung für vernetzte Abfallwirtschaft | Microsoft-Dokumentation
description: Lernen Sie Konzepte der mit Azure IoT Central erstellten Lösung für vernetzte Abfallwirtschaft kennen.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 60fe5b51ffa04de79598f3f4744a8bf2bb57966a
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229897"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Überwachung für vernetzte Abfallwirtschaft: Referenzarchitektur 

Eine Lösung für vernetzte Abfallwirtschaft kann mithilfe der **App-Vorlage „Azure IoT Central“** als Einstieg in IoT-Anwendungen erstellt werden. Dieser Artikel enthält einen Leitfaden mit einer allgemeinen Referenzarchitektur für die Entwicklung einer Komplettlösung.

![Architektur für vernetzte Abfallwirtschaft](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)

Konzepte:

1. Geräte und Konnektivität  
1. IoT Central
1. Erweiterbarkeit und Integrationen
1. Geschäftsanwendungen

In diesem Artikel werden die wichtigsten Komponenten beschrieben, die bei einer Lösung für Abfallwirtschaft normalerweise eine Rolle spielen.

## <a name="devices-and-connectivity"></a>Geräte und Konnektivität

Geräte im öffentlichen Raum (z. B. Abfallbehälter) können per LPWAN (Low-Power Wide Area Network) oder über einen externen Netzbetreiber vernetzt werden. Nutzen Sie für diese Gerätetypen [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md), um Ihre Gerätedaten an Ihre IoT-Anwendung in Azure IoT Central zu senden. Sie können auch Gerätegateways verwenden, die IP-fähig sind und direkt mit IoT Central verbunden werden können.

## <a name="iot-central"></a>IoT Central

Azure IoT Central ist eine IoT-App-Plattform, die eine schnelle Erstellung und Bereitstellung einer IoT-Lösung ermöglicht. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.

Wenn Sie Ihre intelligenten Abfallwirtschaftsgeräte mit IoT Central verbinden, stellt die Anwendung Befehls-, Steuerungs-, Überwachungs- und Alarmfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierter Zugriffssteuerung (RBAC), konfigurierbare Dashboards und Erweiterungsoptionen zur Verfügung.

## <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen

Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:

* Transformieren und Integrieren Ihrer IoT-Daten für erweiterte Analysen, z. B. für das Training von Machine Learning-Modellen durch kontinuierlichen Datenexport aus der IoT Central-Anwendung
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden
* Programmgesteuertes Zugreifen auf Ihre IoT-Anwendung in IoT Central über IoT Central-APIs

## <a name="business-applications"></a>Geschäftsanwendungen

Sie können IoT-Daten verwenden, um verschiedene Geschäftsanwendungen in einem Abfallentsorgungsunternehmen zu betreiben. Bei einer Lösung für vernetzte Abfallwirtschaft können Sie beispielsweise die Aussendung von Müllfahrzeugen optimieren. Diese Optimierung kann basierend auf IoT-Sensordaten von vernetzten Abfallbehältern erfolgen. Sie können in Ihrer [IoT Central-Anwendung für vernetzte Abfallwirtschaft](./tutorial-connected-waste-management.md) Regeln und Aktionen in [Connected Field Service](/dynamics365/field-service/connected-field-service) konfigurieren und festlegen, damit Warnungen erstellt werden. Konfigurieren Sie Power Automate-Regeln in IoT Central, um Workflows anwendungs- und dienstübergreifend zu automatisieren. Darüber hinaus können Informationen basierend auf Dienstaktivitäten in Connected Field Service zurück an Azure IoT Central gesendet werden.

Sie können die folgenden Integrationsprozesse problemlos mit IoT Central und Connected Field Service konfigurieren:

* Azure IoT Central kann Informationen zu Geräteanomalien zur Diagnose an Connected Field Service senden.
* Mit Connected Field Service können Vorfälle oder Arbeitsaufträge erstellt werden, die über Geräteanomalien ausgelöst werden.
* Connected Field Service kann auch genutzt werden, um Techniker für die Inspektion einzuplanen, damit Ausfallzeiten verhindert werden.
* Das Gerätedashboard von Azure IoT Central kann mit relevanten Dienst- und Zeitplaninformationen aktualisiert werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie eine Azure IoT Central-Anwendung für [vernetzte Abfallwirtschaft](./tutorial-connected-waste-management.md) erstellen.
* Informieren Sie sich über [IoT Central-Vorlagen für Behörden](./overview-iot-central-government.md).
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../core/overview-iot-central.md).

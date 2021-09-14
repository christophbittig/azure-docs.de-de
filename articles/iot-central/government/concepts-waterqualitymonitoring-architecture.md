---
title: Referenzarchitektur für eine mit Azure IoT Central erstellte Lösung zur Überwachung der Wasserqualität | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Konzepte einer Lösung zur Überwachung der Wasserqualität, die mit Azure IoT Central erstellt wurde.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 83906682a188a0d6abf859589a38311a0d726a05
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229810"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Überwachung der Wasserqualität: Referenzarchitektur 

Die Lösung für die Überwachung der Wasserqualität kann mit der **Azure IoT Central-App-Vorlage** als Einstieg in IoT-Anwendungen erstellt werden. Dieser Artikel enthält einen Leitfaden mit einer allgemeinen Referenzarchitektur für die Entwicklung einer Komplettlösung. 

![Architektur zur Überwachung der Wasserqualität](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Konzepte:

1. Geräte und Konnektivität  
1. IoT Central
1. Erweiterbarkeit und Integrationen
1. Geschäftsanwendungen

In diesem Artikel werden die wichtigsten Komponenten beschrieben, die in der Regel eine Rolle in einer Lösung zur Überwachung der Wasserqualität spielen.

## <a name="devices-and-connectivity"></a>Geräte und Konnektivität

Wasserverwaltungslösungen nutzen intelligente Wasserwirtschaftsgeräte wie Strömungsmessgeräte, Geräte zur Überwachung der Wasserqualität, intelligente Ventile und Leckdetektoren.

Geräte in intelligenten Wasserlösungen können über Niedrigenergieweitverkehrnetze (Low-Power Wide Area Networks, LPWAN) oder über einen Drittanbieternetzwerkoperator verbunden werden. Nutzen Sie für diese Gerätetypen die [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md), um Ihre Gerätedaten an Ihre IoT-Anwendung in Azure IoT Central zu senden. Sie können auch Gerätegateways verwenden, die IP-fähig sind und direkt mit IoT Central verbunden werden können.

## <a name="iot-central"></a>IoT Central

Azure IoT Central ist eine IoT-App-Plattform, die eine schnelle Erstellung und Bereitstellung einer IoT-Lösung ermöglicht. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.

Wenn Sie Ihre intelligenten Wasserwirtschaftsgeräte mit IoT Central verbinden, stellt die Anwendung Befehls-, Steuerungs-, Überwachungs- und Benachrichtigungsfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierten Zugriffssteuerung (RBAC), konfigurierbare Dashboards sowie Erweiterungsoptionen zur Verfügung.

## <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen

Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:

* Transformieren und Integrieren Ihrer IoT-Daten für erweiterte Analysen, z. B. für das Training von Machine Learning-Modellen durch kontinuierlichen Datenexport aus der IoT Central-Anwendung
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden
* Programmgesteuertes Zugreifen auf Ihre IoT-Anwendung in IoT Central über IoT Central-APIs

## <a name="business-applications"></a>Geschäftsanwendungen

Sie können IoT-Daten verwenden, um verschiedene Geschäftsanwendungen in einem Wasserversorgungsunternehmen zu betreiben. In Ihrer [IoT Central-Anwendung für die Überwachung des Wasserverbrauchs](tutorial-water-consumption-monitoring.md) können Sie Regeln und Aktionen konfigurieren und so festlegen, dass in [Connected Field Service](/dynamics365/field-service/connected-field-service) Warnungen erstellt werden. Konfigurieren Sie Power Automate-Regeln in IoT Central, um Workflows anwendungs- und dienstübergreifend zu automatisieren. Darüber hinaus können Informationen basierend auf Dienstaktivitäten in Connected Field Service zurück an Azure IoT Central gesendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die Erstellung einer IoT Central-Anwendung zur [Überwachung der Wasserqualität](./tutorial-water-quality-monitoring.md).
* Informieren Sie sich über [IoT Central-Vorlagen für Behörden](./overview-iot-central-government.md).
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../core/overview-iot-central.md).

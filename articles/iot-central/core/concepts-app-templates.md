---
title: Anwendungsvorlagen in Azure IoT Central | Microsoft-Dokumentation
description: Azure IoT Central-Anwendungsvorlagen ermöglichen Ihnen den Einstieg in die Entwicklung von IoT-Lösungen.
author: ankitscribbles
ms.author: ankitgup
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f11a033fc1cd97786e540773f5b8e6ca0a08106b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350631"
---
# <a name="what-are-application-templates"></a>Was sind Anwendungsvorlagen?

Anwendungsvorlagen in Azure IoT Central sind ein Tool, das Sie beim Einstieg in die IoT-Lösungsentwicklung unterstützt. App-Vorlagen sind vielseitig einsetzbar. Sie können sich beispielsweise einen ersten Eindruck von den Möglichkeiten verschaffen oder Ihre Anwendung für den Weiterverkauf an Ihre Kunden vollständig anpassen.

Anwendungsvorlagen bestehen aus:

- Beispieldashboards
- Beispielgerätevorlagen
- Simulierten Geräten, die Echtzeitdaten erzeugen
- Vorkonfigurierten Regeln und Aufträgen
- Umfassender Dokumentation einschließlich Tutorials und Anleitungen

Sie wählen die Anwendungsvorlage aus, wenn Sie Ihre Anwendung erstellen. Sie können die Vorlage nach dem Erstellen der Anwendung nicht mehr ändern.

## <a name="custom-templates"></a>Benutzerdefinierte Vorlagen

Wenn Sie Ihre Anwendung von Grund auf neu erstellen möchten, wählen Sie die Vorlage **Benutzerdefinierte Anwendung** aus. Die ID der Vorlage „Benutzerdefinierte Anwendung“ lautet `iotc-pnp-preview`.

## <a name="industry-focused-templates"></a>Branchenorientierten Vorlagen

Azure IoT Central ist eine branchenunabhängige Anwendungsplattform. Anwendungsvorlagen sind branchenspezifische Beispiele, die heute für diese Branchen verfügbar sind:

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="connected-logistics"></a>Vernetzte Logistik

Bei den weltweiten Ausgaben im Bereich Logistik wird für 2020 eine Summe von 10,6 Billionen US-Dollar erwartet. Der Großteil dieser Ausgaben entfällt auf den Warentransport, und Transportdienstleister stehen unter enormem Wettbewerbsdruck.

Mithilfe von IoT-Sensoren können Umgebungsbedingungen wie Temperatur, Luftfeuchtigkeit, Neigung, Erschütterung, Licht und Standort einer Sendung erfasst und überwacht werden. Von IoT-Sensoren und -Geräten gesammelte Telemetriedaten können mit anderen Datenquellen wie etwa Wetter- und Verkehrsinformationen in cloudbasierten Business Intelligence-Systemen kombiniert werden.

Eine Lösung für vernetzte Logistik hat unter anderem folgende Vorteile:

* Sendungsüberwachung mit Nachverfolgung in Echtzeit 
* Sendungsintegrität mit Echtzeitüberwachung von Umgebungsbedingungen
* Schutz vor Diebstahl, Verlust oder Beschädigung von Sendungen
* Geofencing, Routenoptimierung, Flottenmanagement und Fahrzeuganalyse
* Vorhersagen für planbare Versand- und Ankunftszeiten von Sendungen

Die folgenden Screenshots zeigen das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden:

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-1.png" alt-text="Screenshot: Obere Hälfte des Dashboards für Abläufe der vernetzten Logistik":::

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-2.png" alt-text="Screenshot: Untere Hälfte des Dashboards für Abläufe der vernetzten Logistik":::

Weitere Informationen finden Sie im Tutorial [Bereitstellung und exemplarische Vorgehensweise für eine Anwendungsvorlage für vernetzte Logistik](../retail/tutorial-iot-central-connected-logistics.md).

## <a name="digital-distribution-center"></a>Digitales Verteilzentrum

Die Lieferketten weltweit präsenter Hersteller und Einzelhändler werden mit der Zeit immer verzweigter und komplexer. Die Konsumenten erwarten mittlerweile ein breit gefächertes Produktangebot, das innerhalb von ein bis zwei Tagen nach dem Kauf geliefert werden kann. Verteilzentren sind gezwungen, sich an diese Trends anzupassen und die bestehenden Ineffizienzen zu beseitigen.

Handarbeit bedeutet heutzutage, dass 55 bis 65 Prozent der Kosten eines Verteilzentrums auf die Zusammenstellung und Verpackung von Sendungen entfallen. Die manuelle Zusammenstellung und Verpackung ist außerdem in der Regel langsamer als automatisierte Systeme, und die Bewältigung des Versandvolumens wird durch einen stark schwankenden Personalbedarf zusätzlich erschwert. Diese saisonale Schwankung führt zu einer hohen Mitarbeiterfluktuation und erhöht die Wahrscheinlichkeit kostspieliger Fehler.

Lösungen, die auf IoT-fähigen Kameras basieren, können Vorteile in Bezug auf die Transformation ergeben, indem eine digitale Feedbackschleife ermöglicht wird. Daten aus dem gesamten Verteilzentrum führen zu verwertbaren Erkenntnissen, die wiederum zu besseren Daten führen.

Ein digitales Verteilzentrum hat unter anderem folgende Vorteile:

* Kameraüberwachung der Waren beim Eingang und beim Durchlaufen des Fördersystems
* Automatische Erkennung fehlerhafter Waren
* Effiziente Auftragsnachverfolgung
* Geringere Kosten, höhere Produktivität und optimierte Nutzung

Der folgende Screenshot zeigt das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden: 

:::image type="content" source="media/concepts-app-templates/digital-distribution-center-dashboard.png" alt-text="Digitales Verteilzentrum: Dashboard":::

Weitere Informationen finden Sie im Tutorial [Bereitstellen und Durchlaufen einer Anwendungsvorlage für digitale Verteilzentren](../retail/tutorial-iot-central-digital-distribution-center.md).

## <a name="in-store-analytics---condition-monitoring"></a>In-Store-Analyse – Bedingungsüberwachung

Die Umgebungsbedingungen in Geschäften sind für viele Einzelhändler eine wichtige Möglichkeit, sich von der Konkurrenz abzuheben. Einzelhändler möchten den Kunden in ihren Geschäften eine angenehme Umgebung bieten.  

Mithilfe der IoT Central-Anwendungsvorlage „In-Store-Analyse – Bedingungsüberwachung“ können Sie eine End-to-End-Lösung erstellen. Die Anwendungsvorlage ermöglicht die Herstellung einer digitalen Verbindung mit einer Einzelhandelsumgebung sowie deren digitale Überwachung mithilfe verschiedener Sensorgeräte. Diese Sensorgeräte generieren Telemetriedaten, aus denen Sie geschäftliche Erkenntnisse gewinnen können, die den Einzelhändler dabei unterstützen, seine Betriebskosten zu senken und eine erstklassige Kundenerfahrung zu bieten.

Verwenden Sie die Anwendungsvorlage für Folgendes:

* Verbinden verschiedener Arten von IoT-Sensoren mit einer IoT Central-Anwendungsinstanz
* Überwachen und Verwalten der Integrität des Sensornetzwerks sowie der Gatewaygeräte in der Umgebung
* Erstellen benutzerdefinierter Regeln für die Umgebungsbedingungen in einem Geschäft, um Warnungen für Filialleiter auszulösen
* Gewinnen von Erkenntnissen auf der Grundlage der Umgebungsbedingungen, die von den Mitarbeitern des Einzelhandelsgeschäfts zur Verbesserung der Kundenerfahrung genutzt werden können
* Exportieren der aggregierten Erkenntnisse in vorhandene oder neue Geschäftsanwendungen, um den Mitarbeitern zeitnah hilfreiche Informationen zur Verfügung zu stellen

Die Anwendungsvorlage verfügt über eine Reihe von Gerätevorlagen und nutzt mehrere simulierte Geräte, um das Dashboard aufzufüllen. 

Der folgende Screenshot zeigt das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden: 

:::image type="content" source="media/concepts-app-templates/in-store-analytics-condition-dashboard.png" alt-text="In-Store-Analyse – Bedingungsüberwachung":::

Weitere Informationen finden Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="in-store-analytics---checkout"></a>In-Store-Analyse – Auschecken

Einige Einzelhändler nutzen die Kassenumgebung (Checkout) in ihren Geschäften, um sich von der Konkurrenz abzuheben. Einzelhändler möchten Kunden in ihren Geschäften durch einen reibungslosen Ablauf an der Kasse dazu animieren, erneut bei ihnen einzukaufen.  

Mithilfe der IoT Central-Anwendungsvorlage „In-Store-Analyse – Auschecken“ können Sie eine Lösung erstellen, die Mitarbeiter mit Erkenntnissen aus dem Kassenbereich eines Geschäfts versorgt. So können beispielsweise Sensoren Informationen zur Länge von Warteschlangen sowie zur durchschnittlichen Wartezeit an den einzelnen Kassen liefern.

Verwenden Sie die Anwendungsvorlage für Folgendes:

* Verbinden verschiedener Arten von IoT-Sensoren mit einer IoT Central-Anwendungsinstanz
* Überwachen und Verwalten der Integrität des Sensornetzwerks sowie der Gatewaygeräte in der Umgebung
* Erstellen benutzerdefinierter Regeln für die Kassenbedingungen in einem Geschäft, um Warnungen für Mitarbeiter auszulösen
* Gewinnen von Erkenntnissen auf der Grundlage der Kassenbedingungen, die von den Mitarbeitern des Einzelhandelsgeschäfts zur Verbesserung der Kundenerfahrung genutzt werden können
* Exportieren der aggregierten Erkenntnisse in vorhandene oder neue Geschäftsanwendungen, um den Mitarbeitern zeitnah hilfreiche Informationen zur Verfügung zu stellen

Die Anwendungsvorlage verfügt über eine Reihe von Gerätevorlagen und nutzt mehrere simulierte Geräte, um das Dashboard mit Daten zur Länge von Kassenwarteschlangen aufzufüllen. 

Der folgende Screenshot zeigt das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden: 

:::image type="content" source="media/concepts-app-templates/In-Store-Analytics-Checkout-Dashboard.png" alt-text="In-Store-Analyse – Auschecken":::

Weitere Informationen finden Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="smart-inventory-management"></a>Intelligente Bestandsverwaltung

Bestand sind die Waren, über die ein Einzelhändler verfügt. Mithilfe der Bestandsverwaltung wird sichergestellt, dass sich das richtige Produkt zur richtigen Zeit am richtigen Ort befindet. Ein Einzelhändler muss die Kosten für die Lagerung von Überbestand gegen die Kosten abwägen, die entstehen, wenn er nicht über genügend Bestand verfügt, um die Nachfrage zu decken.

Die Bestandsverwaltung lässt sich mithilfe von IoT-Daten optimieren, die durch RFID-Tags, Beacons und Kameras generiert werden. Von IoT-Sensoren und -Geräten gesammelte Telemetriedaten können mit anderen Datenquellen wie etwa Wetter- und Verkehrsinformationen in cloudbasierten Business Intelligence-Systemen kombiniert werden.

Eine intelligente Bestandsverwaltung hat unter anderem folgende Vorteile:

* Geringeres Risiko, dass Artikel nicht vorrätig sind, und Gewährleistung der angestrebten Kundenservicequalität 
* Ausführliche Analyse und Erkenntnisse zur Bestandsgenauigkeit nahezu in Echtzeit
* Hilfreiche Tools zur Bestimmung der passenden Bestandsmenge für Kundenbestellungen

Bei dieser Anwendungsvorlage stehen die Gerätekonnektivität sowie die Konfiguration und Verwaltung von RFID- und BLE-Lesegeräten (Bluetooth Low Energy) im Mittelpunkt.

Der folgende Screenshot zeigt das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden:

:::image type="content" source="media/concepts-app-templates/smart-inventory-management-dashboard.png" alt-text="Intelligente Bestandsverwaltung: Dashboard":::

Weitere Informationen finden Sie im Tutorial [Bereitstellen und Durchlaufen einer Anwendungsvorlage für intelligente Bestandsverwaltung](../retail/tutorial-iot-central-smart-inventory-management.md).

## <a name="micro-fulfillment-center"></a>Micro-Fulfillment-Center

Aufgrund des stetig wachsenden Wettbewerbsdrucks im Einzelhandel müssen Einzelhändler kontinuierlich versuchen, die Lücke zwischen Nachfrage und Deckung zu schließen. Ein neuer Trend zur Deckung der steigenden Kundennachfrage besteht darin, den Bestand in der Nähe der Endkunden und der von ihnen besuchten Geschäfte vorzuhalten.

Mithilfe der IoT Central-Anwendungsvorlage „Micro-Fulfillment-Center“ können Sie sämtliche Aspekte Ihrer vollständig automatisierten Fulfillment-Center überwachen und verwalten. Die Vorlage enthält eine Reihe simulierter Sensoren für die Bedingungsüberwachung sowie simulierte Transportroboter, um die Lösungsentwicklung zu beschleunigen. Diese Sensorgeräte erfassen aussagekräftige Signale, aus denen geschäftliche Erkenntnisse gewonnen werden können, auf deren Grundlage Einzelhändler ihre Betriebskosten senken und Kundenerfahrungen generieren können.

Die Anwendungsvorlage ermöglicht Folgendes: 

- Nahtloses Verbinden verschiedener IoT-Sensoren (etwa Roboter oder Bedingungsüberwachungssensoren) mit einer IoT Central-Anwendungsinstanz
- Überwachen und Verwalten der Integrität des Sensornetzwerks sowie der Gatewaygeräte in der Umgebung
- Erstellen benutzerdefinierter Regeln für die Umgebungsbedingungen in einem Fulfillment-Center, um geeignete Warnungen auszulösen
- Gewinnen von Erkenntnissen auf der Grundlage der Umgebungsbedingungen in Ihrem Fulfillment-Center, die von den Lagermitarbeitern genutzt werden können
- Exportieren der aggregierten Erkenntnisse in vorhandene oder neue Geschäftsanwendungen, um die Mitarbeiter bei ihrer Arbeit zu unterstützen

Der folgende Screenshot zeigt das Standarddashboard in der Anwendungsvorlage. Das Dashboard kann vollständig an Ihre individuellen Lösungsanforderungen angepasst werden:

:::image type="content" source="media/concepts-app-templates/MFC-Dashboard.png" alt-text="Micro-Fulfillment-Center":::

Weitere Informationen finden Sie im Tutorial [Bereitstellen und Durchlaufen einer Anwendungsvorlage für intelligente Bestandsverwaltung](../retail/tutorial-micro-fulfillment-center.md).

## <a name="video-analytics---object-and-motion-detection"></a>Videoanalyse: Objekt- und Bewegungserkennung

Mit der IoT Central-Anwendungsvorlage *Videoanalyse: Objekt- und Bewegungserkennung* können Sie schnell eine Lösung mit Intelligent Edge-Kameras zur Objekt- und Bewegungserkennung bereitstellen, verwalten und überwachen.

Die Videoanalyseanwendung verwendet ein in IoT Edge ausgeführtes LVA-Modul ([Live Video Analytics](#live-video-analytics)). Das LVA-Modul bietet eine Plattform für die Erstellung intelligenter Videoanwendungen, die den Edge und die Cloud umfassen. Mithilfe der Plattform lassen sich IoT-Lösungen wie etwa die Videoanalyseanwendung mit Objekt- und Bewegungserkennung erweitern.

Die Anwendungsvorlage enthält vier Anwendungsdashboards:

* Unter **Erste Schritte** finden Sie Links zu Ressourcen, die Sie bei den ersten Schritten mit der Anwendungsvorlage unterstützen.

- Das **Demodashboard** enthält eine Darstellung der Arten von Informationen, die Sie von den angeschlossenen Kameras anzeigen können.
- **(Sample) Real Camera Management** ((Beispiel) Verwaltung echter Kameras) nutzt simulierte Kameras, um zu veranschaulichen, wie Sie Ihre Kameras über die Anwendung verwalten können.
- **(Sample) Real Camera Monitor** ((Beispiel) Überwachung für echte Kamera) nutzt simulierte Kameras, um zu veranschaulichen, wie Sie Ihre Kameras über die Anwendung überwachen können.

:::image type="content" source="media/concepts-app-templates/live-video-analytics.png" alt-text="Videoanalyse: Objekt- und Bewegungserkennung":::

Eine Bereitstellungsanleitung für die Lösung finden Sie im Tutorial [Erstellen einer Videoanalyseanwendung mit Objekt- und Bewegungserkennung in Azure IoT Central](../retail/tutorial-video-analytics-deploy.md).

### <a name="live-video-analytics"></a>Live Video Analytics

[Live Video Analytics](https://github.com/Azure/live-video-analytics) ist eine Plattform für die Erstellung intelligenter Videoanwendungen, die den Edge und die Cloud umfassen. Die Plattform bietet die Möglichkeit, Livevideos zu erfassen, aufzuzeichnen und zu analysieren sowie die Ergebnisse (Video oder Videoanalyse) in Azure-Diensten zu veröffentlichen. Die Azure-Dienste können in der Cloud oder im Edgebereich ausgeführt werden. Mithilfe der Plattform können IoT-Lösungen durch Videoanalysen ergänzt werden.

## <a name="smart-meter-monitoring"></a>Intelligente Messgerätüberwachung

 Intelligente Messgeräte ermöglichen nicht nur eine automatisierte Abrechnung, sondern auch erweiterte Messanwendungsfälle wie Messwerte in Echtzeit und bidirektionale Kommunikation. Mit der App-Vorlage für intelligente Messgeräte können Versorger und Partner den Status und die Daten von intelligenten Messgeräten überwachen und Alarme und Benachrichtigungen definieren. Sie enthält Beispielbefehle z. B. für das Trennen der Verbindung mit dem Messgerät und das Aktualisieren der Software. Die Messgerätdaten können so eingerichtet werden, dass sie an andere Geschäftsanwendungen ausgegeben werden, oder es können benutzerdefinierte Lösungen mit ihnen entwickelt werden. 

Hauptfunktionen der App:

- Beispielmodell eines Messgeräts
- Informationen und Livestatus zum Messgerät
- Messwerte wie Energie, Leistung und Spannung
- Beispielbefehle für Messgeräte 
- Integrierte Visualisierung und Dashboards
- Erweiterbarkeit für die Entwicklung benutzerdefinierter Lösungen

Sie können die [Überwachungs-App für intelligente Messgeräte kostenlos ausprobieren](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) – ohne Azure-Abonnement und ohne jegliche Verpflichtung.

Nachdem Sie die App bereitgestellt haben, werden die simulierten Messdaten auf dem Dashboard angezeigt, wie in der folgenden Abbildung dargestellt. Diese Vorlage ist eine Beispiel-App, die Sie für Ihre spezifischen Anwendungsfälle mühelos erweitern und anpassen können.

:::image type="content" source="media/concepts-app-templates/smart-meter-app-dashboard.png" alt-text="Dashboard der App für intelligente Messgeräte":::

## <a name="solar-panel-monitoring"></a>Überwachung von Solarpaneln

Mit der App zum Überwachen von Solarpanels können Versorger und Partner Solarpanels nahezu in Echtzeit überwachen, z. B. die Energieerzeugung und den Verbindungsstatus. Sie kann Benachrichtigungen basierend auf definierten Schwellenwertkriterien senden. Sie enthält Beispielbefehle, z. B. zum Aktualisieren der Firmware und anderer Eigenschaften. Die Solarpaneldaten können so eingerichtet werden, dass sie an andere Geschäftsanwendungen ausgegeben werden, oder es können benutzerdefinierte Lösungen mit ihnen entwickelt werden. 

Hauptfunktionen der App: 

- Beispielmodell eines Solarpanelgeräts 
- Informationen und Livestatus zum Solarpanel
- Erzeugung von Sonnenenergie und andere Messwerte
- Beispiele für Befehle und Steuerung
- Integrierte Visualisierung und Dashboards
- Erweiterbarkeit für die Entwicklung benutzerdefinierter Lösungen

Sie können die [Überwachungs-App für Solarpanels kostenlos ausprobieren](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) – ohne Azure-Abonnement und ohne jegliche Verpflichtung.

Nachdem Sie die App bereitgestellt haben, werden die simulierten Solarpaneldaten innerhalb von 1 bis 2 Minuten wie auf dem nachstehenden Dashboard dargestellt. Diese Vorlage ist eine Beispiel-App, die Sie für Ihre spezifischen Anwendungsfälle mühelos erweitern und anpassen können. 

:::image type="content" source="media/concepts-app-templates/solar-panel-app-dashboard.png" alt-text="Dashboard der Solarpanel-App":::

## <a name="water-quality-monitoring"></a>Überwachung der Wasserqualität

Die herkömmliche Überwachung der Wasserqualität basiert auf manuellen Stichproben und Laboranalysen vor Ort, was zeitaufwendig und teuer ist. Durch eine Remoteüberwachung der Wasserqualität in Echtzeit können Probleme behandelt werden, bevor sich diese auf die Bürger auswirken. Darüber hinaus können Wasserversorger und Umweltbehörden dank fortschrittlicher Analysen und Vorwarnungen frühzeitig auf potenzielle Probleme mit der Wasserqualität reagieren und Wasserreinigungen im Voraus planen.  

Die App zur Überwachung der Wasserqualität ist eine IoT Central-App-Vorlage, die Ihnen den Einstieg in die Entwicklung Ihrer IoT-Lösung erleichtert und Wasserversorgern die digitale Überwachung der Wasserqualität in Smart Citys ermöglicht.

:::image type="content" source="media/concepts-app-templates/water-quality-monitoring-dashboard-full.png" alt-text="App-Vorlage zur Überwachung der Wasserqualität":::

Die App-Vorlage umfasst Folgendes:

- Beispieldashboards
- Beispielvorlagen für Geräte zur Überwachung der Wasserqualität
- Simulierte Geräte zur Überwachung der Wasserqualität
- Vorkonfigurierten Regeln und Aufträgen
- White Label-Branding 

Das Tutorial für die Anwendung zur Überwachung der Wasserqualität finden Sie [hier](../government/tutorial-water-quality-monitoring.md).

## <a name="water-consumption-monitoring"></a>Überwachung des Wasserverbrauchs

Bei der herkömmlichen Überwachung des Wasserverbrauchs müssen Wasserzähler vor Ort abgelesen werden. Immer mehr Städte ersetzen herkömmliche Zähler durch fortschrittliche intelligente Zähler, um die Remoteüberwachung des Verbrauchs sowie die Remotesteuerung von Ventilen und somit die Steuerung des Wasserflusses zu ermöglichen. Die Überwachung des Wasserverbrauchs kann in Verbindung mit digitalem Feedback für Bürger das Bewusstsein stärken und zur Senkung des Wasserverbrauchs beitragen. 

Die App zur Überwachung des Wasserverbrauchs ist eine IoT Central-App-Vorlage, die Ihnen den Einstieg in die Entwicklung Ihrer IoT-Lösung erleichtert und Wasserversorgern und Städten die Remoteüberwachung und -steuerung des Wasserflusses und somit die Senkung des Wasserverbrauchs ermöglicht. 

:::image type="content" source="media/concepts-app-templates/water-consumption-monitoring-dashboard-full.png" alt-text="App-Vorlage zur Überwachung des Wasserverbrauchs":::

Die App-Vorlage zur Überwachung des Wasserverbrauchs umfasst folgende vorkonfigurierte Komponenten:

- Beispieldashboards
- Beispielvorlagen für Geräte zur Überwachung der Wasserqualität
- Simulierte Geräte zur Überwachung der Wasserqualität
- Vorkonfigurierten Regeln und Aufträgen
- White Label-Branding

 Das Tutorial für die Anwendung zur Überwachung des Wasserverbrauchs finden Sie [hier](../government/tutorial-water-consumption-monitoring.md).

## <a name="connected-waste-management"></a>Vernetzte Abfallwirtschaft 

Die App für die vernetzte Abfallwirtschaft ist eine IoT Central-App-Vorlage, die Ihnen den Einstieg in die Entwicklung Ihrer IoT-Lösung erleichtert und Smart Citys die Remoteüberwachung der Müllabfuhr ermöglicht, um sie effizienter zu machen. 

:::image type="content" source="media/concepts-app-templates/connected-waste-management-dashboard.png" alt-text="App-Vorlage für eine vernetzte Abfallwirtschaft":::

Die App-Vorlage für eine vernetzte Abfallwirtschaft umfasst folgende vorkonfigurierte Komponenten:

- Beispieldashboards
- Beispielvorlagen für Geräte für vernetzte Abfallbehälter
- Simulierte Geräte für vernetzte Abfallbehälter
- Vorkonfigurierten Regeln und Aufträgen
- White Label-Branding 

Das Tutorial für die Anwendung für eine vernetzte Abfallwirtschaft finden Sie [hier](../government/tutorial-connected-waste-management.md).

## <a name="continuous-patient-monitoring"></a>Fortlaufende Patientenüberwachung 

Im Bereich der IoT-Lösungen für das Gesundheitswesen ist die kontinuierliche Patientenüberwachung eines der wichtigsten Hilfsmittel zur Verringerung erneuter Krankenhausaufenthalte, zur effektiveren Behandlung chronischer Erkrankungen sowie zur Verbesserung der Ergebnisse für die Patienten. Die kontinuierliche Patientenüberwachung kann in zwei Hauptkategorien unterteilt werden:

1. **Überwachung stationärer Patienten:** Mithilfe medizinischer Wearables und anderer Krankenhausgeräte können Patientenversorgungsteams die Vitalparameter und Erkrankungen von Patienten überwachen, ohne dass eine Pflegekraft mehrmals täglich nach dem Patienten sehen muss. Dank Benachrichtigung wissen Patientenversorgungsteams sofort, wann ein Patient dringend medizinische Hilfe benötigt, und können sich ihre Zeit somit besser einteilen.
1. **Remote-Patientenüberwachung:** Außerhalb des Krankenhauses können Patienten mithilfe von medizinischen Wearables und Patientenberichten überwacht werden, um das Risiko eines erneuten Krankenhausaufenthalts zu senken. Daten von chronisch Erkrankten und Reha-Patienten können gesammelt werden, um sicherzustellen, dass sich Patienten an Pflegepläne halten und Patientenversorgungsteams frühzeitig auf Anzeichen für eine Verschlechterung des Patientenzustands aufmerksam werden.

Mit dieser Anwendungsvorlage können Lösungen für beide Kategorien der kontinuierlichen Patientenüberwachung erstellt werden. Es ergeben sich folgende Vorteile:

- Nahtlose Verbindung verschiedener Arten von medizinischen Wearables mit einer IoT Central-Instanz
- Überwachung und Verwaltung der Geräte, um sicherzustellen, dass Sie einwandfrei funktionieren
- Erstellung benutzerdefinierter Regeln für Gerätedaten, um geeignete Warnungen auszulösen
- Export der Patientengesundheitsdaten nach Azure API for FHIR (kompatibler Datenspeicher)
- Export der aggregierten Erkenntnisse in vorhandene oder neue Geschäftsanwendungen

:::image type="content" source="media/concepts-app-templates/in-patient-dashboard.png" alt-text="Dashboard für die kontinuierliche Patientenüberwachung":::


## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, was IoT Central-Anwendungsvorlagen sind, beginnen Sie mit dem [Erstellen einer IoT Central-Anwendung](quick-deploy-iot-central.md).

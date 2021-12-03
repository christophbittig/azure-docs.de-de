---
title: IoT-Konzepte und Azure IoT Hub | Microsoft-Dokumentation
description: In diesem Artikel werden die grundlegenden Konzepte für neue Benutzer von Azure IoT Hub erläutert.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: d04f5b8ace6bfb331e9460745271d15b82a3fb98
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553462"
---
# <a name="iot-concepts-and-azure-iot-hub"></a>IoT-Konzepte und Azure IoT Hub

In diesem Artikel werden das Internet der Dinge (Internet of Things, IoT), Azure IoT Hub und IoT-Geräte erläutert.

## <a name="iot-concepts"></a>IoT-Konzepte

Das Internet der Dinge (Internet of Things, IoT) wird in der Regel als Netzwerk physischer Geräte definiert, die über das Internet oder ein anderes Kommunikationsnetzwerk eine Verbindung mit anderen Geräten und Diensten herstellen und mit ihnen Daten austauschen. Zurzeit gibt es weltweit mehr als zehn Milliarden verbundene Geräte, und jedes Jahr werden weitere hinzugefügt. Alles, was mit den erforderlichen Sensoren und Software eingebettet werden kann, kann über das Internet verbunden werden. Die folgenden Technologien haben IoT ermöglicht:

- Zugriff auf kostengünstige Sensoren mit geringer Leistung.
- Verschiedene Protokolle, die Internetkonnektivität ermöglichen.
- Cloud Computing-Plattformen, z. B. Azure.
- Big Data.
- Machine Learning.
- Künstliche Intelligenz.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub ist ein verwalteter Dienst, der in der Cloud gehostet wird und als zentraler Nachrichten-Hub für die Kommunikation zwischen einer IoT-Anwendung und deren angeschlossenen Geräten fungiert. Sie können Millionen von Geräten und deren Back-End-Lösungen zuverlässig und sicher verbinden. Fast jedes Gerät kann mit einem IoT Hub verbunden werden. 

Es werden mehrere Messagingmuster unterstützt, einschließlich D2C-Telemetrie, Hochladen von Dateien von Geräten und Anforderung-Antwort-Methoden zum Steuern Ihrer Geräte aus der Cloud. IoT Hub unterstützt auch die Überwachung, um das Erstellen von Geräten sowie Geräteverbindungen und Gerätefehler nachzuverfolgen.

IoT Hub lässt sich auf Millionen von gleichzeitig verbundenen Geräten und Millionen von Ereignissen pro Sekunde skalieren, um Ihre IoT-Workloads zu unterstützen. Weitere Informationen zum Skalieren Ihres IoT-Hubs finden Sie unter [Skalieren von Azure IoT Hub](iot-hub-scaling.md). Weitere Informationen zu den verschiedenen Dienstebenen von IoT Hub und der besten Option für Ihre Skalierbarkeitsanforderungen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/iot-hub/).

Sie können IoT Hub in andere Azure-Dienste integrieren, um vollständige End-to-End-Lösungen zu erstellen. Verwenden Sie z.B. Folgendes:

* [Azure Event Grid](../event-grid/index.yml): Mit diesem Dienst können Sie schnell, zuverlässig, skalierbar und sicher auf kritische Ereignisse reagieren.

* [Azure Logic Apps](../logic-apps/index.yml): Mit diesem Dienst automatisieren Sie Ihre Geschäftsprozesse.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md): Mit diesem Dienst fügen Sie Ihrer Lösung Modelle für maschinelles Lernen und KI hinzu.

* [Azure Stream Analytics](../stream-analytics/index.yml): Mit diesem Dienst führen Sie in Echtzeit Analyseberechnungen für die Datenströme aus Ihren Geräten aus.

IoT Hub hat eine [IoT Hub Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/iot-hub/) von 99,9 %. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

Jedes Azure-Abonnement verfügt über standardmäßige Kontingentgrenzen, um einen Missbrauch des Dienstes zu verhindern. Diese Grenzen können sich auf den Umfang Ihrer IoT-Lösung auswirken. Der derzeitige Grenzwert liegt bei 50 IoT Hub-Instanzen pro Abonnement. Wenden Sie sich an den Support, um eine Erhöhung des Kontingents anzufordern. Weitere Informationen finden Sie unter [IoT Hub-Kontingente und -Drosselung](iot-hub-devguide-quotas-throttling.md). Weitere Details zu Kontingentgrenzen finden Sie in einem der folgenden Artikel:

* [Einschränkungen bei Azure-Abonnementdiensten](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-devices"></a>IoT-Geräte

IoT-Geräte unterscheiden sich von anderen Clients wie Browsern und mobilen Apps. Für IoT-Geräte gilt beispielsweise Folgendes:

- Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener.
- Sie können an Remotestandorten bereitgestellt werden, an denen der physische Zugriff teuer ist.
- Sie sind möglicherweise nur über das Back-End der Lösung erreichbar.
- Sie verfügen möglicherweise über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.
- Sie bieten möglicherweise intermittierende, langsame oder teure Netzwerkkonnektivität.
- Sie müssen möglicherweise geschützte, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.

### <a name="device-identity"></a>Geräteidentität

Bei jedem IoT-Hub gibt es eine Identitätsregistrierung, in der Informationen zu den Geräten und Modulen gespeichert werden, die eine Verbindung mit ihm herstellen dürfen. Damit ein Gerät oder Modul eine Verbindung herstellen kann, muss die Identitätsregistrierung des IoT-Hubs einen Eintrag für dieses Gerät bzw. Modul enthalten. Das Gerät oder Modul muss sich beim IoT-Hub zudem mit Anmeldeinformationen authentifizieren, die in der Identitätsregistrierung gespeichert sind.

Wir unterstützen zwei Authentifizierungsmethoden zwischen dem Gerät und dem IoT Hub. Sie können eine SAS-tokenbasierte Authentifizierung oder eine X.509-Zertifikatauthentifizierung verwenden.

Die SAS-basierte Sicherheitstokenmethode ermöglicht es, dass das Gerät bei jedem Aufruf des IoT Hubs authentifiziert wird, indem dem Aufruf der symmetrische Schlüssel zugeordnet wird. Die X.509-basierte Authentifizierung ermöglicht die Authentifizierung eines IoT-Geräts auf physischer Ebene im Rahmen des Transport Layer Security (TLS)-Standardverbindungsaufbaus. Die auf Sicherheitstoken basierende Methode kann ohne X.509-Authentifizierung genutzt werden, dies ist allerdings weniger sicher. Die Wahl zwischen den beiden Methoden wird in erster Linie davon bestimmt, wie sicher die Geräteauthentifizierung erfolgen muss und ob ein sicherer Speicher auf dem Gerät vorhanden ist (in dem der private Schlüssel geschützt gespeichert wird).

Sie können mithilfe des [IoT Hub Device Provisioning Service](../iot-dps/index.yml) viele Geräte gleichzeitig einrichten und bereitstellen.

### <a name="device-communication"></a>Gerätekommunikation

Nachdem Sie Ihre Authentifizierungsmethode ausgewählt haben, wird die Internetverbindung zwischen IoT-Gerät und IoT Hub standardmäßig mit TLS (Transport Layer Security) geschützt. Azure IoT unterstützt TLS 1.2, TLS 1.1 und TLS 1.0 in der angegebenen Reihenfolge. Unterstützung für TLS 1.0 wird nur zum Zweck der Abwärtskompatibilität geboten. Über die TLS-Unterstützung in IoT Hub können Sie herausfinden, wie Sie Ihren Hub für die Verwendung von TLS 1.2 konfigurieren, weil diese Version die höchste Sicherheit bietet.

### <a name="device-communication-patterns"></a>Muster für Gerätekommunikation

IoT-Geräte senden in der Regel Telemetriedaten von den Sensoren an Back-End-Dienste in der Cloud. Es sind jedoch auch andere Kommunikationstypen möglich, z. B. ein Back-End-Dienst, der Befehle an Ihre Geräte sendet. Einige Beispiele für verschiedene Kommunikationstypen sind die folgenden: 

*  Ein Kühllaster, der alle 5 Minuten Temperaturdaten an einen IoT Hub sendet
*  Der Back-End-Dienst, der einen Befehl an ein Gerät sendet, um die Sendefrequenz von Telemetriedaten für eine Problemdiagnose zu ändern
*  Ein Gerät zur Überwachung eines diskontinuierlichen Rührkessels in einem Chemiewerk, das eine Warnung sendet, wenn die Temperatur einen bestimmten Wert überschreitet

### <a name="device-telemetry"></a>Gerätetelemetrie

Beispiele für Telemetriedaten, die von einem Gerät empfangen werden, können Sensordaten wie Geschwindigkeit oder Temperatur, eine Fehlermeldung wie ein verpasstes Ereignis oder eine Informationsmeldung zur Mitteilung sein, dass das Gerät fehlerfrei ist. IoT-Geräte senden Ereignisse an eine Anwendung, um Erkenntnisse zu gewinnen. Anwendungen erfordern möglicherweise bestimmte Teilmengen von Ereignissen für die Verarbeitung oder den Speicher an verschiedenen Endpunkten.

### <a name="device-properties"></a>Geräteeigenschaften

Eigenschaften können aus dem IoT Hub gelesen oder festgelegt und zum Senden von Benachrichtigungen verwendet werden, wenn eine Aktion abgeschlossen wurde. Ein Beispiel für eine bestimmte Eigenschaft auf einem Gerät ist die Temperatur. Dies kann eine schreibbare Eigenschaft sein, die auf dem Gerät aktualisiert oder aus einem an das Gerät angeschlossenen Temperatursensor gelesen werden kann. 

Sie können Eigenschaften in IoT Hub mithilfe von [Gerätezwillingen](iot-hub-devguide-device-twins.md) oder [Plug & Play](../iot-develop/overview-iot-plug-and-play.md) aktivieren.

Wenn Sie etwas über die Unterschiede zwischen Gerätezwillingen und Plug & Play erfahren möchten, lesen Sie [Plug & Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="device-commands"></a>Gerätebefehle

Ein Beispiel für einen Befehl ist der Neustart eines Geräts. IoT Hub implementiert Befehle, indem es Ihnen ermöglicht wird, direkte Methoden auf Geräten aufzurufen. [Direkte Methoden](iot-hub-devguide-direct-methods.md) stellen eine Interaktion des Typs „Anforderung/Antwort“ mit einem Gerät dar, die einem HTTP-Aufruf darin ähnelt, dass sie sofort (nach einem vom Benutzer angegebenen Timeout) zu einem Erfolg oder Fehler führt. Dieser Ansatz eignet sich für Szenarien, in denen die Vorgehensweise bei sofortigen Aktionen unterschiedlich ist, je nachdem, ob das Gerät reagieren konnte oder nicht.

### <a name="act-on-device-data"></a>Reagieren auf Gerätedaten

IoT Hub ermöglicht es Ihnen, den Nutzen Ihrer Gerätedaten bei anderen Azure-Diensten zu erhöhen, sodass Sie zur vorhersagbaren Problemlösung (statt zur reaktiven Verwaltung) wechseln können. Verbinden Sie Ihren IoT Hub mit anderen Azure-Diensten, um Machine Learning, Analysen und KI durchzuführen, auf Echtzeitdaten zu reagieren, die Verarbeitung zu optimieren und tiefere Erkenntnisse zu gewinnen.

#### <a name="built-in-endpoint-collects-device-data-by-default"></a>Integrierter Endpunkt sammelt Gerätedaten standardmäßig

Ein integrierter Endpunkt sammelt standardmäßig Daten von Ihrem Gerät. Die Daten werden mithilfe eines „Anforderung/Antwort“-Musters über dedizierte IoT-Geräteendpunkte gesammelt, stehen für eine maximale Dauer von sieben Tagen zur Verfügung und können verwendet werden, um Aktionen auf einem Gerät durchzuführen. Dies sind die Daten, die vom Geräteendpunkt akzeptiert werden:

*    Senden von Gerät-an-Cloud-Nachrichten.
*    Empfangen von Cloud-an-Gerät-Nachrichten.
*    Einleiten von Dateiuploads.
*    Abrufen und Aktualisieren der Eigenschaften von Gerätezwillingen.
*    Empfangen von Anforderungen direkter Methoden.

Weitere Informationen zu IoT Hub-Endpunkten finden Sie unter [IoT Hub Dev Guide Endpoints](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints) (Entwicklungsleitfaden für IoT – Endpunkte).

#### <a name="message-routing-sends-data-to-other-endpoints"></a>Nachrichtenrouting sendet Daten an andere Endpunkte

Daten können zur Weiterverarbeitung auch an andere Dienste weitergeleitet werden. Mit der Aufskalierung der IoT-Lösung können auch die Anzahl der Geräte, die Menge von Ereignissen, die Vielfalt von Ereignissen und verschiedene Dienste variieren. Eine flexible, skalierbare, konsistente und zuverlässige Methode für das Routing von Ereignissen ist für dieses Muster erforderlich. Sobald eine Nachrichtenroute erstellt wurde, werden Daten nicht mehr an den integrierten Endpunkt übertragen – es sei denn, dass eine Fallbackroute konfiguriert wurde. Ein Tutorial mit mehreren Verwendungsmöglichkeiten des Nachrichtenroutings finden Sie im [Tutorial zum Routing](tutorial-routing.md).

IoT Hub lässt sich auch in Event Grid integrieren, wodurch es Ihnen ermöglicht wird, Daten an mehrere Abonnenten aufzufächern. Event Grid ist ein vollständig verwalteter Ereignisdienst, mit dem Sie Ereignisse über viele verschiedene Azure-Dienste und -Anwendungen hinweg problemlos verwalten können. Entwickelt im Hinblick auf Leistung und Skalierung vereinfacht er das Erstellen ereignisgesteuerter Anwendungen und serverloser Architekturen. Die Unterschiede zwischen Nachrichtenrouting und der Nutzung von Event Grid werden im [Vergleich zwischen Nachrichtenrouting und Event Grid](iot-hub-event-grid-routing-comparison.md) erläutert.

## <a name="next-steps"></a>Nächste Schritte

Um eine umfassende IoT-Lösung zu testen, lesen Sie die Schnellstartanleitungen zu IoT Hub:

* [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

Weitere Informationen zu den Möglichkeiten, wie Sie IoT-Lösungen mit Azure IoT erstellen und bereitstellen können, finden Sie unter:

* [Grundlagen: Azure IoT-Technologien und -Lösungen](../iot-fundamentals/iot-services-and-technologies.md).
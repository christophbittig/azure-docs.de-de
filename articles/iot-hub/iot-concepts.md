---
title: Grundlegende Konzepte für neue Azure IoT Hub-Benutzer | Microsoft-Dokumentation
description: In diesem Artikel werden die grundlegenden Konzepte für neue Benutzer von Azure IoT Hub erläutert.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 28c91ee86a2657e5483d6d0287be8d42bd1fac23
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354854"
---
# <a name="iot-concepts"></a>IoT-Konzepte

IoT Hub ist ein verwalteter Dienst, der in der Cloud gehostet wird und als zentraler Nachrichten-Hub für die Kommunikation in beide Richtungen zwischen einer IoT-Anwendung und den angeschlossenen Geräten fungiert. Sie können Millionen von Geräten und deren Back-End-Lösungen zuverlässig und sicher verbinden. Fast jedes Gerät kann mit einem IoT Hub verbunden werden. 

Es werden mehrere Messagingmuster unterstützt, einschließlich D2C-Telemetrie, Hochladen von Dateien von Geräten und Anforderung-Antwort-Methoden zum Steuern Ihrer Geräte aus der Cloud. IoT Hub unterstützt auch die Überwachung, um das Erstellen von Geräten, das Verbinden von Geräten und Gerätefehler nachzuverfolgen.

Mit den IoT Hub-Funktionen können Sie skalierbare IoT-Lösungen mit vollem Funktionsumfang erstellen, z.B. für die Verwaltung von Werkzeugen und Maschinen für die industrielle Fertigung, für die Nachverfolgung wertvoller Ressourcen im Gesundheitswesen oder für die Überwachung der Nutzung von Bürogebäuden.

Die Merkmale von IoT-Geräten unterscheiden sich von anderen Clients wie Browsern und mobilen Apps. Die [Geräte-SDKs](iot-hub-devguide-sdks.md) unterstützen Sie beim Bewältigen der Herausforderungen, sichere und zuverlässige Verbindungen zwischen Geräten und Ihrem Back-End-Dienst herzustellen. 

Für IoT-Geräte gilt beispielsweise Folgendes:

- Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener (im Gegensatz zu einem Smartphone).
- Sie können an Remotestandorten bereitgestellt werden, an denen der physische Zugriff teuer ist.
- Sie sind unter Umständen nur über das Back-End der Lösung erreichbar.
- Sie verfügen ggf. über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.
- Sie verfügen ggf. über intermittierende, langsame oder teure Netzwerkverbindungen.
- Sie müssen unter Umständen proprietäre, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.

## <a name="securely-connect-and-communicate"></a>Sicheres Verbinden und Kommunizieren

Dank der gerätebasierten Authentifizierung kann jedes Gerät eine sichere Verbindung mit IoT Hub herstellen und sicher verwaltet werden. Sie verfügen über vollständige Kontrolle über den Gerätezugriff und können Verbindungen pro Gerät steuern.

### <a name="devices-have-a-secure-identity"></a>Geräte haben eine sichere Identität

Jede IoT Hub-Instanz verfügt über eine Identitätsregistrierung, in der Informationen zu den Geräten und Modulen gespeichert werden, die eine Verbindung mit der IoT Hub-Instanz herstellen dürfen. Damit ein Gerät oder Modul eine Verbindung mit einem IoT-Hub herstellen kann, muss in der Identitätsregistrierung des IoT-Hubs ein Eintrag für dieses Gerät bzw. Modul vorhanden sein. Das Gerät oder Modul muss sich beim IoT-Hub zudem mit Anmeldeinformationen authentifizieren, die in der Identitätsregistrierung gespeichert sind.

Wir unterstützen zwei Authentifizierungsmethoden zwischen dem Gerät und dem IoT Hub. In einem Fall können Sie eine SAS-tokenbasierte Authentifizierung nutzen. Die andere unterstützte Methode verwendet die X.509-Zertifikatauthentifizierung.

Die SAS-basierte Sicherheitstokenmethode ermöglicht es, dass das Gerät bei jedem Aufruf des IoT Hubs authentifiziert wird, indem dem Aufruf der symmetrische Schlüssel zugeordnet wird. Die X.509-basierte Authentifizierung ermöglicht die Authentifizierung eines IoT-Geräts auf physischer Ebene im Rahmen des Transport Layer Security (TLS)-Standardverbindungsaufbaus. Die auf Sicherheitstoken basierende Methode kann ohne X.509-Authentifizierung genutzt werden, dies ist allerdings weniger sicher. Die Wahl zwischen den beiden Methoden wird in erster Linie davon bestimmt, wie sicher die Geräteauthentifizierung erfolgen muss und ob ein sicherer Speicher auf dem Gerät vorhanden ist (in dem der private Schlüssel geschützt gespeichert wird).

Azure IoT SDKs generieren Token automatisch, ohne dass eine spezielle Konfiguration erforderlich ist. Wenn Sie das SDK nicht verwenden, müssen Sie die Sicherheitstoken generieren.

Sie können mithilfe des [IoT Hub Device Provisioning Service](../iot-dps/index.yml) viele Geräte gleichzeitig einrichten und bereitstellen.

### <a name="devices-can-securely-communicate-with-an-iot-hub"></a>Geräte können mit einem IoT Hub sicher kommunizieren

Nachdem Sie Ihre Authentifizierungsmethode ausgewählt haben, wird die Internetverbindung zwischen IoT-Gerät und IoT Hub standardmäßig mit TLS (Transport Layer Security) geschützt. Azure IoT unterstützt TLS 1.2, TLS 1.1 und TLS 1.0 in der angegebenen Reihenfolge. Unterstützung für TLS 1.0 wird nur zum Zweck der Abwärtskompatibilität geboten. Über die TLS-Unterstützung in IoT Hub können Sie herausfinden, wie Sie Ihren Hub für die Verwendung von TLS 1.2 konfigurieren, weil diese Version die höchste Sicherheit bietet.

## <a name="communication-patterns-with-a-device"></a>Kommunikationsmuster bei einem Gerät

IoT-Geräte senden in der Regel Telemetriedaten von den Sensoren an Back-End-Dienste in der Cloud. Es sind jedoch auch andere Kommunikationstypen möglich, z. B. ein Back-End-Dienst, der Befehle an Ihre Geräte sendet. Einige Beispiele für verschiedene Kommunikationstypen sind die folgenden: 

*  Ein Kühllaster, der alle 5 Minuten Temperaturdaten an einen IoT Hub sendet
*  Der Back-End-Dienst, der einen Befehl an ein Gerät sendet, um die Sendefrequenz von Telemetriedaten für eine Problemdiagnose zu ändern
*  Ein Gerät zur Überwachung eines diskontinuierlichen Rührkessels in einem Chemiewerk, das eine Warnung sendet, wenn die Temperatur einen bestimmten Wert überschreitet

### <a name="telemetry-is-data-emitted-by-a-device"></a>Telemetriedaten sind von einem Gerät ausgegebene Daten 

Beispiele für Telemetriedaten, die von einem Gerät empfangen werden, können Sensordaten wie Geschwindigkeit oder Temperatur, eine Fehlermeldung wie ein verpasstes Ereignis oder eine Informationsmeldung zur Mitteilung sein, dass das Gerät fehlerfrei ist. IoT-Geräte senden Ereignisse (Benachrichtigungen, Bestätigungen, Telemetriedaten) an eine Anwendung, um Erkenntnisse zu gewinnen. Anwendungen erfordern möglicherweise bestimmte Teilmengen von Ereignissen für die Verarbeitung oder den Speicher an verschiedenen Endpunkten.

### <a name="properties-are-state-values-or-data-that-applications-can-access"></a>Eigenschaften sind Zustandswerte oder Daten, auf die Anwendungen zugreifen können. 

So sind beispielsweise die aktuelle Firmwareversion des Geräts oder schreibbare Eigenschaften, die aktualisiert werden können, z. B. eine Temperatur, Eigenschaften. Eigenschaften können aus dem IoT Hub gelesen oder festgelegt und zum Senden von Benachrichtigungen verwendet werden, wenn eine Aktion abgeschlossen wurde. Ein Beispiel für eine bestimmte Eigenschaft auf einem Gerät ist die Temperatur. Dies kann eine schreibbare Eigenschaft sein, die auf dem Gerät aktualisiert oder aus einem an das Gerät angeschlossenen Temperatursensor gelesen werden kann. 

Sie können Eigenschaften in IoT Hub mithilfe von [Gerätezwillingen](iot-hub-devguide-device-twins.md) oder [Plug & Play](../iot-develop/overview-iot-plug-and-play.md) aktivieren.

    
Wenn Sie etwas über die Unterschiede zwischen Gerätezwillingen und Plug & Play erfahren möchten, lesen Sie [Plug & Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="commands-can-be-used-to-execute-methods-directly-on-connected-devices"></a>Befehle können zum Ausführen von Methoden direkt auf verbundenen Geräten verwendet werden. 

Ein Beispiel für einen Befehl ist der Neustart des Geräts. IoT Hub implementiert Befehle, indem es Ihnen ermöglicht wird, direkte Methoden auf Geräten aus der Cloud aufzurufen. [Direkte Methoden](iot-hub-devguide-direct-methods.md) stellen eine Interaktion des Typs „Anforderung/Antwort“ mit einem Gerät dar, die einem HTTP-Aufruf darin ähnelt, dass sie sofort (nach einem vom Benutzer angegebenen Timeout) zu einem Erfolg oder Fehler führt. Dieser Ansatz eignet sich für Szenarien, in denen die Vorgehensweise bei sofortigen Aktionen unterschiedlich ist, je nachdem, ob das Gerät reagieren konnte oder nicht.

## <a name="view-and-act-on-data-collected-from-your-devices"></a>Anzeigen von und Reagieren auf Daten, die von Ihren Geräten gesammelt wurden

IoT Hub bietet Ihnen die Möglichkeit, den Nutzen Ihrer Gerätedaten bei anderen Azure-Diensten zu erhöhen, sodass Sie zur vorhersagbaren Problemlösung (statt zur reaktiven Verwaltung) wechseln können. Verbinden Sie Ihren IoT Hub mit anderen Azure-Diensten, um Machine Learning, Analysen und KI durchzuführen, auf Echtzeitdaten zu reagieren, die Verarbeitung zu optimieren und tiefere Erkenntnisse zu gewinnen.

### <a name="built-in-endpoint-collects-data-from-your-devices-by-default"></a>Der integrierte Endpunkt sammelt standardmäßig Daten von Ihren Geräten

Ein integrierter Endpunkt sammelt standardmäßig Daten von Ihrem Gerät. Die Daten werden mithilfe eines „Anforderung/Antwort“-Musters über dedizierte IoT-Geräteendpunkte gesammelt, sind maximal sieben Tage lang verfügbar und können verwendet werden, um Aktionen auf einem Gerät durchzuführen. Dies sind die Daten, die vom Geräteendpunkt akzeptiert werden:

*    Senden von Gerät-an-Cloud-Nachrichten. Ein Gerät verwendet diesen Endpunkt, um D2C-Nachrichten zu senden.

*    Empfangen von Cloud-an-Gerät-Nachrichten. Ein Gerät verwendet diesen Endpunkt, um gezielte C2D-Nachrichten zu empfangen.

*    Einleiten von Dateiuploads. Ein Gerät verwendet diesen Endpunkt zum Empfangen eines Azure Storage-SAS-URIs von IoT Hub, um eine Datei hochzuladen.

*    Abrufen und Aktualisieren der Eigenschaften von Gerätezwillingen. Ein Gerät verwendet diesen Endpunkt für den Zugriff auf die Eigenschaften seines Gerätezwillings. 

*    Empfangen von Anforderungen direkter Methoden. Ein Gerät verwendet diesen Endpunkt zum Lauschen auf Anforderungen direkter Methoden. 

Weitere Informationen zu IoT Hub-Endpunkten finden Sie unter [IoT Hub Dev Guide Endpoints](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints) (Entwicklungsleitfaden für IoT – Endpunkte).

### <a name="use-message-routing-to-send-data-to-other-endpoints-for-processing"></a>Verwenden des Nachrichtenroutings zum Senden von Daten an andere Endpunkte zur Verarbeitung

Daten können zur Weiterverarbeitung auch an andere Dienste weitergeleitet werden. Mit der Aufskalierung der IoT-Lösung können auch die Anzahl der Geräte, die Menge von Ereignissen, die Vielfalt von Ereignissen und verschiedene Dienste variieren. Eine flexible, skalierbare, konsistente und zuverlässige Methode für das Routing von Ereignissen ist für dieses Muster erforderlich. Daten können auch gefiltert werden, um sie an verschiedene Dienste zu senden. Sobald eine Nachrichtenroute erstellt wurde, werden Daten nicht mehr an den integrierten Endpunkt übertragen – es sei denn, dass eine Fallbackroute konfiguriert wurde. Ein Tutorial mit mehreren Verwendungsmöglichkeiten des Nachrichtenroutings finden Sie im [Tutorial zum Routing](tutorial-routing.md).

IoT Hub lässt sich auch in Event Grid integrieren, wodurch es Ihnen ermöglicht wird, Daten an mehrere Abonnenten aufzufächern. Event Grid ist ein vollständig verwalteter Ereignisdienst, mit dem Sie Ereignisse über viele verschiedene Azure-Dienste und -Anwendungen hinweg problemlos verwalten können. Entwickelt im Hinblick auf Leistung und Skalierung vereinfacht er das Erstellen ereignisgesteuerter Anwendungen und serverloser Architekturen. Informieren Sie sich ausführlicher über Event Grid. Die Unterschiede zwischen Nachrichtenrouting und der Nutzung von Event Grid werden im [Vergleich zwischen Nachrichtenrouting und Event Grid](iot-hub-event-grid-routing-comparison.md) erläutert.
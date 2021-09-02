---
title: Problembehandlung bei der Überwachung und häufig gestellte Fragen – Azure IoT Edge
description: Problembehandlung – Azure Monitor-Integration und häufig gestellte Fragen
author: veyalla
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 9d3e89ee74dd1f0274ad742cae4a9706f54b7780
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350048"
---
# <a name="faq-and-troubleshooting"></a>FAQ und Fehlerbehebung

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>Das Collectormodul kann keine Metriken von integrierten Endpunkten sammeln

### <a name="check-if-modules-are-on-the-same-docker-network"></a>Überprüfen, ob sich Module in demselben Docker-Netzwerk befinden

Das Metriksammlermodul basiert auf dem eingebetteten DNS-Resolver von Docker für benutzerdefinierte Netzwerke. Der DNS-Resolver stellt die IP-Adresse für Metrikendpunkte mit Modulname bereit. Beispiel: *http://**edgeHub**:9600/metrics*.

Wenn Module nicht in demselben Netzwerk-Namespace ausgeführt werden, schlägt dieser Mechanismus fehl. Beispielsweise erfordern einige Szenarien die Ausführung von Modulen im Hostnetzwerk. Die Sammlung schlägt in solchen Szenarien fehl, wenn sich das Metriksammlermodul in einem anderen Netzwerk befindet.

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>Überprüfen Sie, ob die Umgebungsvariable *httpSettings__enabled* auf *false* festgelegt wurde.

Die von IoT Edge-Systemmodulen verfügbar gemachten integrierten Metrikendpunkte verwenden das HTTP-Protokoll. Sie stehen sogar innerhalb des Modulnetzwerks nicht zur Verfügung, wenn HTTP über die Umgebungsvariableneinstellung in Edge Hub- oder Edge Agent-Modulen explizit deaktiviert wird.

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>Festlegen der Umgebungsvariablen *NO_PROXY* bei Verwendung des HTTP-Proxyservers

Weitere Informationen finden Sie unter [Proxy-Aspekte](how-to-collect-and-transport-metrics.md#proxy-considerations).

### <a name="update-moby-engine"></a>Aktualisieren der Moby-Engine

Sorgen Sie auf Linux-Hosts dafür, dass Sie eine aktuelle Version der Container-Engine verwenden. Wir empfehlen eine Aktualisierung auf die neueste Version anhand der [Installationsanleitungen](how-to-install-iot-edge.md#install-a-container-engine).

## <a name="how-do-i-collect-logs-along-with-metrics"></a>Wie sammle ich von Protokolle zusammen mit Metriken?

Sie könnten [integrierte Protokoll-Pullfeatures](how-to-retrieve-iot-edge-logs.md) verwenden. Eine Beispiellösung, bei der die integrierten Protokollabruffunktionen verwendet werden, steht unter [ **https://aka.ms/iot-elms**](https://aka.ms/iot-elms) zur Verfügung.

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>Warum werden im Azure-Portal auf der Seite „Metriken“ keine Gerätemetriken angezeigt?

Die Azure Monitor-Technologie [native Metriken](../azure-monitor/essentials/data-platform-metrics.md) unterstützt das Prometheus-Datenformat noch nicht direkt. Protokollbasierte Metriken eignen sich aufgrund von Folgendem zurzeit besser für IoT Edge-Metriken:

* Native Unterstützung für das Prometheus-Metrikformat über die Standardtabelle *InsightsMetrics*.
* Erweiterte Datenverarbeitung über [KQL](/azure/data-explorer/kusto/query/) für Visualisierungen und Warnungen.

Die Verwendung von Log Analytics als Metrikdatenbank ist der Grund, weshalb Metriken im Azure-Portal auf der Seite **Protokolle** statt in **Metriken** angezeigt werden.

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>Wie konfiguriere ich den Metrikensammler in einer mehrstufigen Bereitstellung?

Der Metriksammler hat keine Dienstermittlungsfunktionalität. Wir empfehlen, das Modul in die Basis- oder *untere* Bereitstellungsebene einzubeziehen. Beziehen Sie in die Konfiguration des Moduls alle Metrikendpunkte ein, mit denen das Modul bereitgestellt werden könnte. Wenn ein Modul in einer endgültigen Bereitstellung nicht angezeigt wird, aber sein Endpunkt in der Sammlungsliste, versucht der Collector, zu sammeln, einen Fehler zu erstellen und weiterzumachen.

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>Wie erweitere ich die Überwachungslösung mit benutzerdefinierten Metriken?

Lesen Sie dazu den Artikel [Benutzerdefinierte Metriken](how-to-add-custom-metrics.md).

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>Wie kann ich feststellen, zu welchem Gerät eine bestimmte Metrik gehört?

Codieren Sie Geräteinformationen in den Metrikbezeichnungen. Weitere Informationen finden Sie unter [Namenskonventionen](how-to-add-custom-metrics.md#naming-conventions).

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>Wie erstelle ich eine Warnungsregel, die Geräte von mehreren IoT-Hubs einbezieht?

Wenn Sie [eine Warnungsregel erstellen](how-to-create-alerts.md#create-an-alert-rule), können Sie [deren Bereich ändern](how-to-create-alerts.md#select-alert-rule-scope) in eine Ressourcengruppe oder ein Abonnement. Die Warnungsregel gilt dann für alle IoT-Hubs in diesem Bereich.

## <a name="alerts-arent-firing-when-they-should"></a>Warnungen werden nicht ausgelöst, wenn sie es sollten

Vergewissern Sie sich beim Erstellen einer Warnungsregel, ob die Warnungslogik ausgelöst wird, indem Sie das Vorschaudiagramm überprüfen.

Wenn Sie das Problem nicht finden können, erstellen Sie einen [technischen Supportincident](https://azure.microsoft.com/support/create-ticket/) für den **Log Analytics**-Dienst.

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>Mein Gerät wird in der Überwachungsarbeitsmappe nicht angezeigt

Die Arbeitsmappe basiert auf Gerätemetriken, die über die *ResourceId* mit der richtigen IoT-Hub- oder IoT Central-Anwendung verknüpft werden. Vergewissern Sie sich, dass der Metriksammler mit der richtigen *ResourceId* [konfiguriert](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) wurde.

Vergewissern Sie sich anhand von Protokollen des Metriksammlermoduls, dass das Gerät während des ausgewählten Zeitbereichs Metriken gesendet hat.

Beachten Sie: Es kann eine Erfassungsverzögerung von einigen Minuten geben, bevor Metriken angezeigt werden.

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>Ich habe einen Fehler gefunden oder eine Frage zu Metriken, die in der Arbeitsmappe angezeigt werden

Öffnen Sie ein Problem im [GitHub-Repository von Azure IoT Edge](https://github.com/azure/iotedge/issues) mit „[monitor-workbook]“ im Titel.

Die Vorlage für die Arbeitsmappen [steht auf GitHub öffentlich zur Verfügung](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub). Pull Requests mit Verbesserungen oder Fixes sind sehr willkommen!

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>In den öffentlichen Vorlagen werden keine Arbeitsmappen angezeigt

Stellen Sie sicher, dass Sie die Seite **Arbeitsmappen** auf Ihrer IoT-Hub- oder IoT Central-Anwendungsseite im Portal (und nicht in Ihrem Log Analytics-Arbeitsbereich) anzeigen.

Wenn die Arbeitsmappen immer noch nicht angezeigt werden, versuchen Sie, die Präproduktionsumgebung des Azure-Portals zu verwenden: [`https://ms.portal.azure.com`](https://ms.portal.azure.com). Manchmal dauert es längere Zeit, bis Arbeitsmappenupdates in der Produktionsumgebung angezeigt werden. In der Präproduktion stehen sie aber zur Verfügung.

:::zone-end
---
title: Hinzufügen von benutzerdefinierten Metriken – Azure IoT Edge
description: Verbessern integrierter Metriken mithilfe von szenariospezifischen Metriken aus benutzerdefinierten Modulen
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8e9c5b74b19af00228f03b26450b987d87283376
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340457"
---
# <a name="add-custom-metrics-preview"></a>Hinzufügen von benutzerdefinierten Metriken (Vorschau)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Sammeln Sie zusätzlich zu den integrierten Metriken, die von den Systemmodulen bereitgestellt werden, benutzerdefinierte Metriken von Ihren IoT Edge-Modulen. Die [integrierten Metriken](how-to-access-built-in-metrics.md) bieten Ihnen eine grundlegende Übersicht über die Integrität Ihrer Bereitstellung. Möglicherweise benötigen Sie jedoch zusätzliche Informationen aus benutzerdefinierten Modulen, um das Bild zu vervollständigen. Benutzerdefinierte Module können in Ihre Überwachungslösung integriert werden, indem Sie die entsprechende [Prometheus-Clientbibliothek](https://prometheus.io/docs/instrumenting/clientlibs/) für die Ausgabe von Metriken nutzen. Mit diesen zusätzlichen Informationen können Sie neue Ansichten oder Warnungen aktivieren, die speziell auf Ihre Anforderungen zugeschnitten sind.

## <a name="sample-modules-repository"></a>Repository für Beispielmodule

Beispiele für benutzerdefinierte Module, die für die Ausgabe von Metriken instrumentiert wurden, finden Sie im [azure-samples-Repository](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics). Selbst wenn noch kein Beispiel in Ihrer gewünschten Sprache verfügbar ist, kann Ihnen der allgemeine Ansatz bei der Entwicklung weiterhelfen.

## <a name="naming-conventions"></a>Namenskonventionen

Allgemeine Informationen finden Sie in den [Best Practices](https://prometheus.io/docs/practices/naming/) der Prometheus-Dokumentation. Die folgenden zusätzlichen Empfehlungen können für IoT Edge-Szenarien hilfreich sein.

* Fügen Sie den Modulnamen am Anfang des Metriknamens ein, um deutlich zu machen, welches Modul die Metrik ausgegeben hat.

* Beziehen Sie den IoT Hub-Namen oder den Namen der IoT Central-Anwendung, die IoT Edge-Geräte-ID und die Modul-ID als Bezeichnungen (auch als *tags*/*dimensions* bezeichnet) in jede Metrik mit ein. Diese Informationen sind als Umgebungsvariablen für jedes Modul verfügbar, das vom IoT Edge-Agent gestartet wird. Der Ansatz wird durch das Beispiel im Beispiel-Repository [demonstriert](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49). Ohne diese Zusatzinformationen ist es unmöglich, einen bestimmten Metrikwert dem entsprechenden Gerät zuzuordnen.

* Fügen Sie eine Instanz-ID in die Bezeichnungen ein. Eine Instanz-ID kann eine beliebige eindeutige ID wie z. B. eine [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) sein, die während des Modulstarts generiert wird. Instanz-ID-Informationen können bei der Abstimmung von Modulneustarts helfen, während die Metriken eines Moduls im Back-End verarbeitet werden.

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>Konfigurieren des Metrikensammlers für das Sammeln benutzerdefinierter Metriken

Sobald ein benutzerdefiniertes Modul Metriken ausgibt, besteht der nächste Schritt im Konfigurieren des [Metrikensammler-Moduls](how-to-collect-and-transport-metrics.md#metrics-collector-module), mit dem diese benutzerdefinierten Metriken gesammelt und abtransportiert werden.

Die `MetricsEndpointsCSV`-Umgebungsvariable muss aktualisiert werden, sodass sie die URL des Metrikendpunkts vom benutzerdefinierten Modul enthält. Wenn Sie die Umgebungsvariable aktualisieren, müssen Sie sicherstellen, dass diese auch die Endpunkte des Systemmoduls (wie im Konfigurationsbeispiel für den [Metrikensammler](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) gezeigt) enthält.

>[!NOTE]
>Standardmäßig muss der Metrikendpunkt eines benutzerdefinierten Moduls keinem Hostport zugeordnet werden, damit der Metrikensammler darauf zugreifen kann. Bei Linux ist es so, dass beide Module in einem [benutzerdefinierten Docker-Bridge-Netzwerk](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge) namens *azure-iot-edge* gestartet werden, sofern dies nicht explizit überschrieben wird.
>
>Benutzerdefinierte Docker-Netzwerke enthalten einen DNS-Standardresolver, der die modulübergreifende Kommunikation mithilfe von Modulnamen (Containern) ermöglicht. Wenn beispielsweise ein benutzerdefiniertes Modul mit dem Namen *module1* Metriken auf dem HTTP-Port *9600* über den Pfad */metrics* ausgibt, sollte der Sammler so konfiguriert werden, dass er die Metriken vom Endpunkt *http://module1:9600/metrics* erfasst.

Führen Sie den folgenden Befehl auf dem IoT Edge-Gerät aus, um zu testen, ob auf die von dem benutzerdefinierten Modul am HTTP-Port *9600* über den Pfad */metrics* ausgegebenen Metriken zugegriffen werden kann:

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>Hinzufügen benutzerdefinierter Visualisierungen

Sobald Sie benutzerdefinierte Metriken in Log Analytics erhalten, können Sie benutzerdefinierte Visualisierungen und Warnungen erstellen. Die Überwachungsarbeitsmappen können durch das Hinzufügen von abfragebasierte Visualisierungen erweitert werden.

Jeder Metrik wird die Ressourcen-ID des IoT-Hubs oder der IoT Central-Anwendung zugeordnet. Deshalb können Sie über die Seite **Protokolle** des zugeordneten IoT Hubs oder der IoT Central-Anwendung überprüfen, ob Ihre benutzerdefinierten Metriken ordnungsgemäß erfasst wurden, statt dafür den unterstützenden Log Analytics-Arbeitsbereich zu nutzen. Verwenden Sie diese einfache KQL-Abfrage für die Überprüfung:

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

Nachdem Sie sichergestellt haben, dass die Metriken korrekt erfasst werden, können Sie entweder eine neue Arbeitsmappe erstellen oder eine vorhandene Arbeitsmappe erweitern. Verwenden Sie die [ Arbeitsmappen-Dokumentation](../azure-monitor/visualize/workbooks-overview.md) und Abfragen aus den kuratierten [IoT Edge-Arbeitsmappen](how-to-explore-curated-visualizations.md) als Leitfaden.

Wenn Sie mit den Ergebnissen zufrieden sind, können Sie die [Arbeitsmappe für Ihr Team freigeben](../azure-monitor/visualize/workbooks-access-control.md) oder sie im Rahmen der Ressourcenbereitstellungen in Ihrer Organisation [programmgesteuert bereitstellen](../azure-monitor/visualize/workbooks-automate.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über zusätzliche Visualisierungsoptionen für Metriken mit [kuratierten Arbeitsmappen](how-to-explore-curated-visualizations.md).

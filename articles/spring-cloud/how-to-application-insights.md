---
title: Verwenden des Java-In-Process-Agents für Application Insights in Azure Spring Cloud
description: Hier erfahren Sie, wie Sie Apps und Microservices mit dem Java-In-Process-Agent für Application Insights in Azure Spring Cloud überwachen.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1505837a316943c2d22f82a0107bb7a1990e0e83
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340426"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Java-In-Process-Agent für Application Insights in Azure Spring Cloud (Vorschau)

In diesem Artikel wird erklärt, wie Sie Apps und Microservices mithilfe des Application Insights Java-Agenten in Azure Spring Cloud überwachen können.

Mit diesem Feature können Sie die folgenden Aktionen ausführen:

* Suchen nach Ablaufverfolgungsdaten mit unterschiedlichen Filtern.
* Anzeigen eines Abhängigkeitsdiagramms für Microservices
* Überprüfen der Anforderungsleistung
* Überwachen von Livemetriken in Echtzeit
* Überprüfen auf Anforderungsfehler
* Überprüfen von Anwendungsmetriken

Application Insights bietet viele überwachbare Perspektiven, z. B.:

* Anwendungszuordnung
* Leistung
* Fehler
* Metriken
* Livemetriken
* Verfügbarkeit

> [!NOTE]
> Diese Previewfunktion wird in Mooncake und neuen Regionen wie UAE noch nicht unterstützt.

## <a name="enable-java-in-process-agent-for-application-insights"></a>Aktivieren des Java-In-Process-Agents für Application Insights

Führen Sie die folgenden Schritte aus, um die Previewfunktion für den Java-In-Process-Agent zu aktivieren.

1. Navigieren Sie zur Dienstübersichtsseite Ihrer Dienstinstanz.
2. Wählen Sie auf dem Blatt „Überwachung“ den Eintrag **Application Insights** aus.
3. Klicken Sie auf die Schaltfläche **Application Insights aktivieren**, um die **Application Insights**-Integration zu aktivieren.
4. Wählen Sie eine vorhandene Application Insights-Instanz aus, oder erstellen Sie eine neue.
5. Klicken Sie auf **Enable Java in-process agent** (Java-In-Process-Agent aktivieren), um die Vorschau des Java-In-Process-Agent-Features zu aktivieren. Hier können Sie auch eine Stichprobenhäufigkeit zwischen 0 und 100 festlegen.
6. Wählen Sie **Speichern** aus, um die Änderung zu speichern.

## <a name="portal"></a>Portal

1. Navigieren Sie zur Seite **Dienst | Übersicht**, und klicken Sie im Abschnitt **Überwachung** auf **Application Insights**.
2. Klicken Sie auf **Application Insights aktivieren**, um Application Insights in Azure Spring Cloud zu aktivieren.
3. Klicken Sie auf **Enable Java in-process agent** (Java-In-Process-Agent aktivieren), um die Java-IPA-Previewfunktion zu aktivieren. Wenn eine IPA-Previewfunktion aktiviert ist, können Sie eine optionale Stichprobenhäufigkeit konfigurieren (standardmäßig 10,0 %).

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Verwenden des Application Insights-Features

Wenn das **Application Insights**-Feature aktiviert ist, können Sie die folgenden Aktionen ausführen:

Klicken Sie im linken Navigationsbereich auf **Application Insights**, um zur Seite **Übersicht** in Application Insights zu springen.

* Klicken Sie auf **Anwendungsübersicht**, um den Status von Aufrufen zwischen Anwendungen anzuzeigen.

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Klicken Sie auf die Verbindung zwischen customers-service und `petclinic`, um weitere Details anzuzeigen, z. B. eine SQL-Abfrage.

* Klicken Sie im linken Navigationsbereich auf **Leistung**, um die Leistungsdaten für die Vorgänge aller Anwendungen sowie Abhängigkeiten und Rollen anzuzeigen.

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Klicken Sie im linken Navigationsbereich auf **Fehler**, um zu sehen, ob etwas Unerwartetes für Ihre Anwendungen angezeigt wird.

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Klicken Sie im linken Navigationsbereich auf **Metriken**, und wählen Sie den gewünschten Namespace aus. Es werden ggf. sowohl Spring Boot-Metriken als auch benutzerdefinierte Metriken angezeigt.

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Klicken Sie im linken Navigationsbereich auf **Livemetriken**, um die Echtzeitmetriken für verschiedene Dimensionen anzuzeigen.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Klicken Sie im linken Navigationsbereich auf **Verfügbarkeit**, um die Verfügbarkeit und Reaktionsfähigkeit von Web-Apps zu überwachen, indem Sie [Verfügbarkeitstests in Application Insights erstellen](../azure-monitor/app/monitor-web-app-availability.md).

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-Vorlage

Kopieren Sie den folgenden Inhalt in `azuredeploy.json`, um die Azure Resource Manager-Vorlage zu verwenden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Wenden Sie die ARM-Vorlage mit einem der folgenden CLI-Befehle an:

* Für eine vorhandene Azure Spring Cloud-Instanz:

   ```azurecli
   az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate]    "samplingRate" --name "assignedName" --resource-group "resourceGroupName"
   ```

* Für eine neu erstellte Azure Spring Cloud-Instanz:

   ```azurecli
   az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName"    --disable-app-insights false --enable-java-agent true --name "assignedName" --resource-group    "resourceGroupName"
   ```

* So deaktivieren Sie AppInsights:

   ```azurecli
   az spring-cloud app-insights update --disable --name "assignedName" --resource-group "resourceGroupName"
   ```

## <a name="java-agent-updateupgrade"></a>Update/Upgrade des Java-Agents

Für den Java-Agent wird regelmäßig ein Update/Upgrade mit dem JDK durchgeführt, das sich auf die folgenden Szenarien auswirken kann.

> [!Note]
> Ein Update/Upgrade der JDK-Version erfolgt vierteljährlich.

* Vorhandene Anwendungen, die den Java-Agent vor dem Update/Upgrade verwenden, sind davon nicht betroffen.
* Anwendungen, die nach dem Update/Upgrade erstellt werden, nutzen die neue Version des Java-Agents.
* Vorhandene Anwendungen, die den Java-Agent zuvor nicht verwendet haben, erfordern einen Neustart oder eine erneute Bereitstellung, um die neue Version des Java-Agents zu nutzen.

## <a name="java-agent-configuration-hot-loading"></a>Hot-Loading der Java-Agent-Konfiguration

Azure Spring Cloud ermöglicht einen Hot-Loading-Mechanismus, um die Einstellungen der Agent-Konfiguration ohne Neustart von Anwendungen anzupassen.

> [!Note]
> Der Hot-Loading-Mechanismus hat eine Verzögerung von Minuten.

* Wenn der Java-Agent zuvor aktiviert wurde, erfordern Änderungen an der Application Insights-Instanz und/oder SamplingRate KEINEN Neustart von Anwendungen.
* Wenn Sie den Java-Agent aktivieren, müssen Sie Anwendungen neu starten.
* Wenn Sie den Java-Agent deaktivieren, beenden die Anwendungen das Senden aller Überwachungsdaten nach einer Verzögerung von Minuten. Sie können Anwendungen neu starten, um den Agent aus der Java-Laufzeitumgebung zu entfernen.

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Konzeptabgleich zwischen Azure Spring Cloud und Application Insights

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __Anwendungsübersicht__/Rolle<br />* __Livemetriken__/Rolle<br />* __Fehler__/Rollen/Cloudrolle<br />* __Leistung__/Rollen/Cloudrolle |
| `App Instance`     | * __Anwendungsübersicht__/Rolleninstanz<br />* __Livemetriken__/Dienstname<br />* __Fehler__/Rolle/Cloudinstanz<br />* __Leistung__/Rollen/Cloudinstanz |

Der Name `App Instance` aus Azure Spring Cloud wird in den folgenden Szenarien geändert oder generiert:

* Sie erstellen eine neue Anwendung.
* Sie stellen eine JAR-Datei oder Quellcode für eine vorhandene Anwendung bereit.
* Sie initiieren eine Blau-Grün-Bereitstellung.
* Sie starten die Anwendung neu.
* Sie beenden die Bereitstellung einer Anwendung und starten sie dann neu.

Wenn Daten in Application Insights gespeichert werden, enthält es den Verlauf von Azure Spring Cloud-App-Instanzen, die seit der Aktivierung des Java-Agents erstellt oder bereitgestellt wurden. Dies bedeutet, dass Sie im Application Insights-Portal die Anwendungsdaten sehen können, die am gestrigen Tag erstellt wurden, aber dann innerhalb eines bestimmten Zeitraums gelöscht werden, z. B. in den letzten 24 Stunden. Die folgenden Szenarien veranschaulichen diese Funktionsweise:

* Sie haben heute um 8:00 Uhr eine Anwendung aus Azure Spring Cloud mit aktivierten Java-Agent erstellt, und anschließend stellen Sie heute gegen 8:10 Uhr eine JAR-Datei für diese Anwendung bereit. Nach einigen Tests ändern Sie den Code und stellen heute um 8:30 Uhr eine neue JAR-Datei für diese Anwendung bereit. Dann machen Sie eine Pause, und wenn Sie gegen 11:00 Uhr zurückkehren, überprüfen Sie einige Daten aus Application Insights. Sie sehen:
  * Drei Instanzen in der Anwendungsübersicht mit Zeitbereichen in den letzten 24 Stunden sowie Fehler, Leistung und Metriken.
  * Eine Instanz in der Anwendungsübersicht mit einem Zeitbereich in der letzten Stunde sowie Fehler, Leistung und Metriken.
  * Eine Instanz in Livemetriken.
* Sie haben heute um 8:00 Uhr eine Anwendung aus Azure Spring Cloud mit aktivierten Java-Agent erstellt, und anschließend stellen Sie heute gegen 8:10 Uhr eine JAR-Datei für diese Anwendung bereit. Heute gegen 8:30 Uhr versuchen Sie eine Blau-Grün-Bereitstellung mit einer anderen JAR-Datei. Derzeit verfügen Sie über zwei Bereitstellungen für diese Anwendung. Nach einer Pause heute gegen 11:00 Uhr möchten Sie einige Daten aus Application Insights überprüfen. Sie sehen:
  * Drei Instanzen in der Anwendungsübersicht mit Zeitbereichen in den letzten 24 Stunden sowie Fehler, Leistung und Metriken.
  * Zwei Instanzen in der Anwendungsübersicht mit Zeitbereichen in der letzten Stunde sowie Fehler, Leistung und Metriken.
  * Zwei Instanzen in Livemetriken.

## <a name="see-also"></a>Weitere Informationen

* [Verwenden der verteilten Ablaufverfolgung mit Azure Spring Cloud](./how-to-distributed-tracing.md)
* [Analysieren von Protokollen und Metriken](diagnostic-services.md)
* [Streamen von Protokollen in Echtzeit](./how-to-log-streaming.md)

---
title: Verwenden des Java-In-Process-Agents für Application Insights in Azure Spring Cloud
description: Hier erfahren Sie, wie Sie Apps und Microservices mit dem Java-In-Process-Agent für Application Insights in Azure Spring Cloud überwachen.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5652c28f90c697bc2394e2bb018bff3d8a7ddc7a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324667"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud"></a>Application Insights - Java-Prozess-Agent in der Azure Spring Cloud

In diesem Artikel wird erklärt, wie Sie Apps und Microservices mithilfe des Application Insights Java-Agenten in Azure Spring Cloud überwachen können.

Mit diesem Feature können Sie die folgenden Aktionen ausführen:

* Suchen nach Ablaufverfolgungsdaten mit unterschiedlichen Filtern.
* Anzeige einer Abhängigkeitsliste von Microservices.
* Überprüfen der Anforderungsleistung
* Überwachen von Livemetriken in Echtzeit
* Überprüfen auf Anforderungsfehler
* Überprüfen von Anwendungsmetriken

Application Insights können viele beobachtbare Perspektiven bieten, darunter:

* Anwendungszuordnung
* Leistung
* Fehler
* Metriken
* Livemetriken
* Verfügbarkeit

## <a name="enable-java-in-process-agent-for-application-insights"></a>Aktivieren des Java-In-Process-Agents für Application Insights

Aktivieren Sie den Java In-Process Agent mit dem folgenden Verfahren.

1. Navigieren Sie zur Dienstübersichtsseite Ihrer Dienstinstanz.
2. Wählen Sie den Eintrag **Anwendungsinsights** im Bereich **Überwachung**.
3. Wählen Sie **Application Insights** aktivieren, um die Integration von **Application Insights** zu aktivieren.
4. Wählen Sie eine vorhandene Application Insights-Instanz aus, oder erstellen Sie eine neue.
   Hier können Sie auch eine Stichprobenhäufigkeit zwischen 0 und 100 festlegen.
5. Wählen Sie **Speichern** aus, um die Änderung zu speichern.

## <a name="portal"></a>Portal

1. Navigieren Sie zur Seite **Dienst | Übersicht**, und klicken Sie im Abschnitt **Überwachung** auf **Application Insights**.
2. Klicken Sie auf **Application Insights aktivieren**, um Application Insights in Azure Spring Cloud zu aktivieren.
3. Wenn **Anwendungseinblicke** aktiviert ist, können Sie eine optionale Abtastrate konfigurieren (Standard 10,0%).

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

> [!Note]
> Verwenden Sie nicht dieselbe KI in verschiedenen Azure Spring Cloud-Instanzen, da sonst gemischte Daten angezeigt werden.

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

* Wählen Sie im linken Navigationsbereich **Live Metrics**, um die Echtzeitmetriken für verschiedene Dimensionen anzuzeigen.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Klicken Sie im linken Navigationsbereich auf **Verfügbarkeit**, um die Verfügbarkeit und Reaktionsfähigkeit von Web-Apps zu überwachen, indem Sie [Verfügbarkeitstests in Application Insights erstellen](../azure-monitor/app/monitor-web-app-availability.md).

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="automation"></a>Automation

In den folgenden Abschnitten wird beschrieben, wie Sie Ihre Bereitstellung mithilfe von Azure Resource Manager-Vorlagen (ARM-Vorlagen) oder Terraform automatisieren können.
    
### <a name="arm-templates"></a>ARM-Vorlagen

Zur Bereitstellung mit einer ARM-Vorlage kopieren Sie den folgenden Inhalt in eine *azuredeploy.json*-Datei. Weitere Informationen finden Sie unter [Microsoft.AppPlatform Spring/monitoringSettings](/azure/templates/microsoft.appplatform/spring/monitoringsettings).

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

### <a name="terraform"></a>Terraform

Für eine Terraform-Bereitstellung verwenden Sie die folgende Vorlage. Weitere Informationen finden Sie unter [azurerm_spring_cloud_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_service).

```terraform
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_application_insights" "example" {
  name                = "tf-test-appinsights"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku_name            = "S0"

  config_server_git_setting {
    uri          = "https://github.com/Azure-Samples/piggymetrics"
    label        = "config"
    search_paths = ["dir1", "dir2"]
  }

  trace {
    connection_string = azurerm_application_insights.example.connection_string
    sample_rate       = 10.0
  }

  tags = {
    Env = "staging"
  }
}
```

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Sie können Application Insights mit Azure CLI-Befehlen verwalten. Stellen Sie sicher, dass Sie in den folgenden Befehlen den Text *\<placeholder>* durch die beschriebenen Werte ersetzen. Der Platzhalter *\<service-name>* bezieht sich auf den Namen Ihrer Azure Spring Cloud-Instanz.

Um Application Insights beim Erstellen einer Azure Spring Cloud-Instanz zu konfigurieren, verwenden Sie den folgenden Befehl. Für das Argument `app-insights` können Sie einen Application Insights-Namen oder eine Ressourcen-ID angeben.

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name "serviceName" \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

Sie können auch eine Application Insights-Verbindungszeichenfolge (bevorzugt) oder einen Instrumentierungsschlüssel verwenden, wie im folgenden Beispiel gezeigt.

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Um Application Insights beim Erstellen einer Azure Spring Cloud-Instanz zu deaktivieren, verwenden Sie den folgenden Befehl:

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable-app-insights
```

Um die Application Insights-Einstellungen einer vorhandenen Azure Spring Cloud-Instanz zu überprüfen, verwenden Sie den folgenden Befehl:

```azurecli
az spring-cloud app-insights show \
    --resource-group <resource-group-name> \
    --name <service-name>
```

Um Application Insights über eine Verbindungszeichenfolge (bevorzugt) oder einen Instrumentierungsschlüssel zu aktivieren, verwenden Sie den folgenden Befehl:

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

Um Application Insights über den Ressourcennamen oder die ID zu aktivieren, verwenden Sie den folgenden Befehl:

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

Um Application Insights auf einer vorhandenen Azure Spring Cloud-Instanz zu deaktivieren, verwenden Sie den folgenden Befehl:

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable
```

## <a name="java-agent-updateupgrade"></a>Update/Upgrade des Java-Agents

Der Java-Agent wird regelmäßig mit dem JDK aktualisiert/aufgerüstet, was sich auf die folgenden Szenarien auswirken kann.

> [!Note]
> Ein Update/Upgrade der JDK-Version erfolgt vierteljährlich.

* Vorhandene Anwendungen, die den Java-Agent vor dem Update/Upgrade verwenden, sind davon nicht betroffen.
* Anwendungen, die nach dem Update/Upgrade erstellt werden, nutzen die neue Version des Java-Agents.
* Vorhandene Anwendungen, die den Java-Agent zuvor nicht verwendet haben, erfordern einen Neustart oder eine erneute Bereitstellung, um die neue Version des Java-Agents zu nutzen.

## <a name="java-agent-configuration-hot-loading"></a>Hot-Loading der Java-Agent-Konfiguration

Azure Spring Cloud ermöglicht einen Hot-Loading-Mechanismus, um die Einstellungen der Agent-Konfiguration ohne Neustart von Anwendungen anzupassen.

> [!Note]
> Der Heißlademechanismus hat eine Verzögerung in Minuten.

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
* [Anwendungszuordnung](../azure-monitor/app/app-map.md)
* [Livemetriken](../azure-monitor/app/live-stream.md)
* [Leistung](../azure-monitor/app/tutorial-performance.md)
* [Fehler](../azure-monitor/app/tutorial-runtime-exceptions.md)
* [Metriken](../azure-monitor/essentials/tutorial-metrics.md)
* [Protokolle](../azure-monitor/logs/data-platform-logs.md)
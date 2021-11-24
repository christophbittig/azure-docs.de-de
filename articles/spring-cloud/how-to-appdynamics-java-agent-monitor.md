---
title: Überwachung von Spring Boot-Anwendungen mit dem AppDynamics Java Agent (Vorschau)
titleSuffix: Azure Spring Cloud
description: Wie man den AppDynamics Java-Agenten zur Überwachung von Spring Boot-Anwendungen in Azure Spring Cloud verwendet.
author: KarlErickson
ms.author: jiec
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: b549ce3e8e79b7315144f52484fe63f7553982c4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492642"
---
# <a name="how-to-monitor-spring-boot-apps-with-the-appdynamics-java-agent-preview"></a>Überwachung von Spring Boot-Anwendungen mit dem AppDynamics Java Agent (Vorschau)

In diesem Artikel wird erklärt, wie man den AppDynamics Java Agent zur Überwachung von Spring Boot-Anwendungen in der Azure Spring Cloud verwendet.

Mit dem AppDynamics Java Agent können Sie:

- Überwachen von Anwendungen
- Konfigurieren Sie den AppDynamics Java Agent mithilfe von Umgebungsvariablen
- Überprüfen Sie alle Überwachungsdaten aus dem AppDynamics Dashboard

Im folgenden Video wird der AppDynamics Java In-Process-Agent vorgestellt.

<br>

> [!VIDEO https://www.youtube.com/embed/4dZuRX5bNAs]

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* [Ein AppDynamics-Konto](https://www.appdynamics.com/)

## <a name="activate-the-appdynamics-java-in-process-agent"></a>Aktivieren Sie den AppDynamics Java In-Process-Agent

Für den gesamten Arbeitsablauf müssen Sie das tun:

* Aktivieren Sie den AppDynamics Java In-Process Agent in Azure Spring Cloud, um Anwendungsmetrikdaten zu generieren.
* Verbinden Sie den AppDynamics Agent mit dem AppDynamics Controller, um die Daten im Controller zu sammeln und zu visualisieren.

![Das Diagramm zeigt eine Spring Boot-Anwendung im Feld "Azure Spring Cloud" mit einem in zwei Richtungen verlaufenden Pfeil, der sie mit einem Feld "AppDynamics Agent" verbindet, das ebenfalls einen Pfeil hat, der auf ein Feld "AppDynamics Controller" zeigt](media/how-to-appdynamics-java-agent-monitor/appdynamics-activation.jpg)

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-cli"></a>Aktivieren einer Anwendung mit dem AppDynamics Agent unter Verwendung der Azure CLI

Gehen Sie wie folgt vor, um eine Anwendung über die Azure CLI zu aktivieren.

1. Erstellen Sie eine Ressourcengruppe.
1. Erstellen Sie eine Instanz von Azure Spring Cloud.
1. Erstellen Sie eine Anwendung mit dem folgenden Befehl. Ersetzen Sie die Platzhalter *\<...>* durch Ihre eigenen Werte.

    ```azurecli
    az spring-cloud app create \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --is-public true
    ```

1. Erstellen Sie eine Bereitstellung mit dem AppDynamics Agent unter Verwendung von Umgebungsvariablen.

    ```azurecli
    az spring-cloud app deploy \
        --resource-group "<your-resource-group-name>" \
        --service "<your-Azure-Spring-Cloud-instance-name>" \
        --name "<your-app-name>" \
        --jar-path app.jar \
        --jvm-options="-javaagent:/opt/agents/appdynamics/java/javaagent.jar" \
        --env APPDYNAMICS_AGENT_APPLICATION_NAME=<your-app-name> \
              APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY=<your-agent-access-key> \
              APPDYNAMICS_AGENT_ACCOUNT_NAME=<your-agent-account-name> \
              APPDYNAMICS_AGENT_NODE_NAME=<your-agent-node-name> \
              APPDYNAMICS_AGENT_TIER_NAME=<your-agent-tier-name> \
              APPDYNAMICS_CONTROLLER_HOST_NAME=<your-AppDynamics-controller-host-name> \
              APPDYNAMICS_CONTROLLER_SSL_ENABLED=true \
              APPDYNAMICS_CONTROLLER_PORT=443
    ```

Azure Spring Cloud installiert den AppDynamics-Java-Agenten unter dem Pfad */opt/agents/appdynamics/java/javaagent.jar* vor. Sie können den Agenten über die JVM-Optionen Ihrer Anwendungen aktivieren und dann den Agenten mithilfe von Umgebungsvariablen konfigurieren. Die Werte für diese Variablen finden Sie unter [Überwachung der Azure Spring Cloud mit Java Agent](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent). Weitere Informationen darüber, wie diese Variablen bei der Anzeige und Organisation von Berichten in der AppDynamics-Benutzeroberfläche helfen, finden Sie unter [Ebenen und Knoten](https://docs.appdynamics.com/21.9/en/application-monitoring/tiers-and-nodes).

### <a name="activate-an-application-with-the-appdynamics-agent-using-the-azure-portal"></a>Aktivieren Sie eine Anwendung mit dem AppDynamics Agent über das Azure-Portal

Um eine Anwendung über das Azure-Portal zu aktivieren, führen Sie die folgenden Schritte aus.

1. Navigieren Sie zu Ihrer Azure Spring Cloud-Instanz im Azure-Portal.

1. Wählen Sie **Apps** im Abschnitt **Einstellungen** des linken Navigationsfensters.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png" alt-text="Azure-Portal-Screenshot mit dem Abschnitt &quot;Apps&quot;" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-list.png":::

1. Wählen Sie die Anwendung, um zur Seite **Übersicht** zu navigieren.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png" alt-text="Azure-Portal-Screenshot der Übersichtsseite der App" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-overview.png":::

1. Wählen Sie **Konfiguration** im linken Navigationsbereich, um die Umgebungsvariablen der Anwendung hinzuzufügen, zu aktualisieren oder zu löschen.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png" alt-text="Azure-Portal-Screenshot mit dem Abschnitt &quot;Umgebungsvariablen&quot; auf der Konfigurationsseite der Anwendung " lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-env.png":::

1. Wählen Sie **Allgemeine Einstellungen**, um die JVM-Optionen der Anwendung hinzuzufügen, zu aktualisieren oder zu löschen.

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png" alt-text="Azure-Portal-Screenshot zeigt den Abschnitt &quot;Allgemeine Einstellungen&quot; der Konfigurationsseite der Anwendung, wobei &quot;JVM-Optionen&quot; hervorgehoben sind" lightbox="media/how-to-appdynamics-java-agent-monitor/azure-spring-cloud-app-configuration-general.png":::

## <a name="automate-provisioning"></a>Automatisieren der Bereitstellung

Sie können eine Bereitstellungsautomatisierungspipeline auch mit Terraform oder einer Azure Resource Manager-Vorlage (ARM-Vorlage) ausführen. Mit dieser Pipeline können Sie alle neuen Anwendungen, die Sie erstellen und bereitstellen, ganz ohne Aufwand instrumentieren und überwachen.

### <a name="automate-provisioning-using-terraform"></a>Automatisieren der Bereitstellung mit Terraform

Um die Umgebungsvariablen in einer Terraform-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Verwalten einer aktiven Azure Spring Cloud-Bereitstellung](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/appdynamics/java/javaagent.jar"
  ...
    environment_variables = {
      "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
      "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
      "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
      "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
      "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
      "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
      "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
      "APPDYNAMICS_CONTROLLER_PORT" : "443"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Automatisieren der Bereitstellung mithilfe einer ARM-Vorlage

Um die Umgebungsvariablen in einer ARM-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Microsoft.AppPlatform/Spring/apps/deployments/skus](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "APPDYNAMICS_AGENT_APPLICATION_NAME" : "<your-app-name>",
    "APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY" : "<your-agent-access-key>",
    "APPDYNAMICS_AGENT_ACCOUNT_NAME" : "<your-agent-account-name>",
    "APPDYNAMICS_AGENT_NODE_NAME" : "<your-agent-node-name>",
    "APPDYNAMICS_AGENT_TIER_NAME" : "<your-agent-tier-name>",
    "APPDYNAMICS_CONTROLLER_HOST_NAME" : "<your-AppDynamics-controller-host-name>",
    "APPDYNAMICS_CONTROLLER_SSL_ENABLED" : "true",
    "APPDYNAMICS_CONTROLLER_PORT" : "443"
  },
  "jvmOptions": "-javaagent:/opt/agents/appdynamics/java/javaagent.jar",
  ...
}
```

## <a name="review-reports-in-the-appdynamics-dashboard"></a>Berichte im AppDynamics Dashboard überprüfen

Dieser Abschnitt zeigt verschiedene Berichte in AppDynamics.

Der folgende Screenshot zeigt einen Überblick über Ihre Anwendungen im AppDynamics-Dashboard:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg" alt-text="AppDynamics-Screenshot zeigt die Seite Anwendungen" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-birds-eye-view-of-apps.jpg":::

Das **Anwendungs-Dashboard** zeigt die Gesamtinformationen für jede Ihrer Anwendungen, wie in den folgenden Screenshots anhand von Anwendungsbeispielen gezeigt wird:

- `api-gateway`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg" alt-text="AppDynamics-Screenshot mit dem Anwendungs-Dashboard für das Beispiel api-gateway app" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-api-gateway.jpg":::

- `customers-service`

   :::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg" alt-text="AppDynamics-Screenshot mit dem Anwendungs-Dashboard für das Beispiel der Kunden-Service-App" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service.jpg":::

Die folgende Abbildung zeigt, wie Sie grundlegende Informationen aus dem Dashboard **Datenbankaufrufe** abrufen können.

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg" alt-text="AppDynamics-Screenshot mit dem Dashboard Datenbankaufrufe" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customer-service-db-calls.jpg":::

Sie können auch Informationen über die langsamsten Datenbankaufrufe erhalten, wie in diesen Screenshots gezeigt:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg" alt-text="AppDynamics-Screenshot mit der Seite der langsamsten Datenbankaufrufe" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service.jpg":::

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg" alt-text="AppDynamics-Screenshot mit der Seite &quot;Korrelierte Snapshots&quot;, die von der Seite &quot;Langsamste Datenbankaufrufe&quot; aus aufgerufen wird" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-slowest-db-calls-from-customers-service-2.jpg":::

Der folgende Screenshot zeigt die Analyse der Speichernutzung im Abschnitt **Heap** der Seite **Memory**:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg" alt-text="AppDynamics-Screenshot zeigt den Heap-Abschnitt der Seite Speicher" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-memory-usage.jpg":::

Sie können auch den Garbage-Collection-Prozess sehen, wie in diesem Screenshot dargestellt:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg" alt-text="AppDynamics-Screenshot mit dem Abschnitt Garbage Collection auf der Seite Speicher" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-garbage-collection.jpg":::

Die folgende Bildschirmabbildung zeigt die Seite **Slow Transactions**: 

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg" alt-text="AppDynamics-Screenshot mit der Seite &quot;Langsame Transaktionen&quot;" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-slowest-transactions.jpg":::

Sie können weitere Metriken für die JVM definieren, wie in diesem Bildschirmfoto des **Metrik-Browsers** gezeigt:

:::image type="content" source="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg" alt-text="AppDynamics-Screenshot zeigt den Metrik-Browser" lightbox="media/how-to-appdynamics-java-agent-monitor/appdynamics-dashboard-customers-service-jvm-metric-browser.jpg":::

## <a name="view-appdynamics-agent-logs"></a>AppDynamics Agent-Protokolle anzeigen

Standardmäßig druckt Azure Spring Cloud die Protokolle der Ebene *Info* des AppDynamics-Agenten nach `STDOUT`. Die Protokolle werden mit den Anwendungsprotokollen gemischt. Sie können die explizite Agent-Version aus den Anwendungsprotokollen abrufen.

Sie können die Protokolle des AppDynamics Agent auch von den folgenden Orten abrufen:

* Azure Spring Cloud-Protokolle
* Azure Spring Cloud Application Insights
* Azure Spring Cloud-LogStream

## <a name="learn-about-appdynamics-agent-upgrade"></a>Erfahren Sie mehr über das AppDynamics Agent Upgrade

Der AppDynamics Agent wird regelmäßig mit dem JDK aktualisiert (vierteljährlich). Das Agent-Upgrade kann sich auf die folgenden Szenarien auswirken:

* Bestehende Anwendungen, die AppDynamics Agent vor dem Upgrade verwenden, bleiben unverändert, müssen jedoch neu gestartet oder neu bereitgestellt werden, um die neue Version von AppDynamics Agent zu nutzen.
* Anwendungen, die nach dem Upgrade erstellt werden, verwenden die neue Version von AppDynamics Agent.

## <a name="configure-vnet-injection-instance-outbound-traffic"></a>Konfigurieren Sie den ausgehenden Verkehr der VNet-Injektionsinstanz

Stellen Sie bei VNet-Injektionsinstanzen von Azure Spring Cloud sicher, dass der ausgehende Datenverkehr für AppDynamics Agent richtig konfiguriert ist. Einzelheiten finden Sie unter [SaaS-Domänen und IP-Bereiche](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges) und [Kundenverantwortlichkeiten für den Betrieb von Azure Spring Cloud in VNET](vnet-customer-responsibilities.md).

## <a name="understand-the-limitations"></a>Verstehen Sie die Grenzen

Um die Einschränkungen des AppDynamics-Agenten zu verstehen, siehe [Überwachung der Azure Spring Cloud mit Java-Agent](https://docs.appdynamics.com/21.11/en/application-monitoring/install-app-server-agents/java-agent/monitor-azure-spring-cloud-with-java-agent).

## <a name="next-steps"></a>Nächste Schritte

* [Verwendung von Application Insights Java In-Process Agent in Azure Spring Cloud](/azure/spring-cloud/how-to-application-insights)

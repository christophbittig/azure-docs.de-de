---
title: Überwachen von Spring Boot-Apps mit dem New Relic-Agent von Java
titleSuffix: Azure Spring Cloud
description: Hier erfahren Sie, wie Sie Spring Boot-Anwendungen mit dem New Relic-Agent von Java überwachen.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 7c0a659effc973737647f3868154be1e5154afba
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493702"
---
# <a name="how-to-monitor-spring-boot-apps-using-new-relic-java-agent-preview"></a>Überwachen von Spring Boot-Apps mit dem New Relic-Agent von Java (Vorschau)

Dieses Feature ermöglicht die Überwachung von Spring Boot-Anwendungen in Azure Spring Cloud mit dem New Relic-Java-Agent.

Mit dem New Relic-Agent von Java können Sie die folgenden Funktionen verwenden:
* Nutzen des New Relic-Agents von Java.
* Konfigurieren des New Relic-Agents von Java mit Umgebungsvariablen.
* Überprüfen aller Überwachungsdaten aus dem New Relic-Dashboard.

Im folgenden Video wird beschrieben, wie Sie Spring Boot-Anwendungen in Azure Spring Cloud mithilfe von New Relic One aktivieren und überwachen.

<br>

> [!VIDEO https://www.youtube.com/embed/4GQPwJSP3ys?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="prerequisites"></a>Voraussetzungen

* Ein [New Relic](https://newrelic.com/)-Konto.
* [Azure CLI-Version 2.0.67 oder höher](/cli/azure/install-azure-cli)

## <a name="activate-the-new-relic-java-in-process-agent"></a>Aktivieren des prozessinternen New Relic-Agents von Java

Verwenden Sie das folgende Verfahren, um auf den Agent zuzugreifen:

1. Erstellen Sie eine Instanz von Azure Spring Cloud.

2. Erstellen einer Anwendung.

    ```azurecli
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. Erstellen Sie eine Bereitstellung mit dem New Relic-Agent und Umgebungsvariablen.

    ```azurecli
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

Azure Spring Cloud installiert den New Relic-Agent von Java unter */opt/agents/newrelic/java/newrelic-agent.jar* vor. Kunden können den Agent über **JVM-Optionen** von Anwendungen aktivieren und den Agent mithilfe der [Umgebungsvariablen des New Relic-Agents von Java](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables) konfigurieren.

## <a name="portal"></a>Portal

Sie können diesen Agent auch über das Portal mit dem folgenden Verfahren aktivieren.

1. Suchen die App unter **Einstellungen**/**Apps** im Navigationsbereich.

   [ ![Suchen nach der zu überwachenden App](media/new-relic-monitoring/find-app.png) ](media/new-relic-monitoring/find-app.png)

2. Klicken Sie auf die Anwendung, um zur Seite **Übersicht** zu navigieren.

   [ ![Seite „Übersicht“](media/new-relic-monitoring/overview-page.png) ](media/new-relic-monitoring/overview-page.png)

3. Klicken Sie im linken Navigationsbereich auf **Konfiguration**, um die **Umgebungsvariablen** der Anwendung hinzuzufügen, zu aktualisieren oder zu löschen.

   [ ![Umgebung aktualisieren](media/new-relic-monitoring/configurations-update-environment.png) ](media/new-relic-monitoring/configurations-update-environment.png)

4. Klicken Sie auf **Allgemeine Einstellungen**, um die **JVM-Option** der Anwendung hinzuzufügen, zu aktualisieren oder zu löschen.

   [ ![Aktualisieren der JVM-Option](media/new-relic-monitoring/update-jvm-option.png) ](media/new-relic-monitoring/update-jvm-option.png)

5. Zeigen Sie die **Zusammenfassungsseite** der Anwendungs-API bzw. des Gateways im New Relic-Dashboard an.

   [ ![Seite „Zusammenfassung“ der App](media/new-relic-monitoring/app-summary-page.png) ](media/new-relic-monitoring/app-summary-page.png)

6. Zeigen Sie die Seite **Zusammenfassung** des Kundendiensts der Anwendung im „New Relic“-Dashboard an.
 
   [ ![Seite „Kundendienst“](media/new-relic-monitoring/customers-service.png) ](media/new-relic-monitoring/customers-service.png)  

7. Zeigen Sie die Seite **Dienstzuordnung** im „New Relic“-Dashboard an.  

   [ ![ Seite „Dienstzuordnung“](media/new-relic-monitoring/service-map.png) ](media/new-relic-monitoring/service-map.png)

8. Zeigen Sie die Seite **JVMs** der Anwendung im „New Relic“-Dashboard an.

   [ ![Seite „JVM“](media/new-relic-monitoring/jvm-page.png) ](media/new-relic-monitoring/jvm-page.png)

9. Zeigen Sie das Anwendungsprofil im „New Relic“-Dashboard an.

   [ ![Anwendungsprofil](media/new-relic-monitoring/profile-app.png) ](media/new-relic-monitoring/profile-app.png)

## <a name="automate-provisioning"></a>Automatisieren der Bereitstellung

Sie können eine Bereitstellungsautomatisierungspipeline auch mit Terraform oder einer Azure Resource Manager-Vorlage (ARM-Vorlage) ausführen. Mit dieser Pipeline können Sie alle neuen Anwendungen, die Sie erstellen und bereitstellen, ganz ohne Aufwand instrumentieren und überwachen.

### <a name="automate-provisioning-using-terraform"></a>Automatisieren der Bereitstellung mit Terraform

Um die Umgebungsvariablen in einer Terraform-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Verwalten einer aktiven Azure Spring Cloud-Bereitstellung](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
resource "azurerm_spring_cloud_java_deployment" "example" {
  ...
  jvm_options = "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar"
  ...
    environment_variables = {
      "NEW_RELIC_APP_NAME": "<app-name>",
      "NEW_RELIC_LICENSE_KEY": "<new-relic-license-key>"
  }
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Automatisieren der Bereitstellung mithilfe einer ARM-Vorlage

Um die Umgebungsvariablen in einer ARM-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Microsoft.AppPlatform/Spring/apps/deployments/skus](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"deploymentSettings": {
  "environmentVariables": {
    "NEW_RELIC_APP_NAME" : "<app-name>",
    "NEW_RELIC_LICENSE_KEY" : "<new-relic-license-key>"
  },
  "jvmOptions": "-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar",
  ...
}
```

## <a name="view-new-relic-java-agent-logs"></a>Anzeigen von Protokollen des New Relic-Agents von Java

Standardmäßig gibt Azure Spring Cloud die Protokolle des New Relic-Java-Agents in `STDOUT` aus. Die Protokolle werden mit den Anwendungsprotokollen gemischt. Sie können die explizite Agent-Version aus den Anwendungsprotokollen abrufen.

Sie können die Protokolle des New Relic-Agents auch an den folgenden Speicherorten abrufen:

* Azure Spring Cloud-Protokolle
* Azure Spring Cloud Application Insights
* Azure Spring Cloud-LogStream

Sie können einige von New Relic bereitgestellte Umgebungsvariablen nutzen, um die Protokollierung des neuen Agents zu konfigurieren, z. B. `NEW_RELIC_LOG_LEVEL`, um den Protokolliergrad zu steuern. Weitere Informationen finden Sie unter [New Relic-Umgebungsvariablen](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables).

> [!CAUTION]
> Es wird dringend empfohlen, das Standardverhalten der Protokollierung *nicht* außer Kraft zu setzen, das von Azure Spring Cloud für New Relic bereitgestellt wird. Andernfalls werden die Protokollierungsszenarien in den oben beschriebenen Szenarien blockiert, und die Protokolldateien gehen möglicherweise verloren. Beispielsweise sollten Sie die folgenden Umgebungsvariablen nicht an Ihre Anwendungen übergeben. Protokolldateien können nach einem Neustart oder einer erneuten Bereitstellung von Anwendungen verloren gehen.
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>Update/Upgrade des New Relic-Java-Agents

Der New Relic-Java-Agent führt regelmäßig ein Update/Upgrade des JDK aus. Das Update/Upgrade des Agents kann sich auf die folgenden Szenarien auswirken.

* Vorhandene Anwendungen, die den New Relic-Java-Agent vor dem Update/Upgrade verwenden, bleiben unverändert.
* Vorhandene Anwendungen, die den New Relic-Java-Agent vor dem Update/Upgrade verwenden, erfordern einen Neustart oder eine erneute Bereitstellung, um die neue Version des New Relic-Java-Agents zu nutzen.
* Neue Anwendungen, die nach dem Update/Upgrade erstellt werden, verwenden die neue Version des New Relic-Java-Agents.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Konfiguration des ausgehenden Datenverkehrs der VNET-Injektionsinstanz

Für eine VNET-Injektionsinstanz von Azure Spring Cloud müssen Sie sicherstellen, dass der ausgehende Datenverkehr für den New Relic-Java-Agent ordnungsgemäß konfiguriert ist. Weitere Informationen finden Sie unter [Netzwerke von New Relic](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents).

## <a name="next-steps"></a>Nächste Schritte

* [Verteilte Ablaufverfolgung und App Insights](how-to-distributed-tracing.md)

---
title: Überwachen von Spring Boot-Apps mit Dynatrace Java OneAgent
description: Wie man Dynatrace Java OneAgent zur Überwachung von Spring Boot-Anwendungen in Azure Spring Cloud verwendet
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 4e84cb9d33e850ed3148c41cff25c3fe17a93ae8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486257"
---
# <a name="how-to-monitor-spring-boot-apps-with-dynatrace-java-oneagent"></a>Überwachen von Spring Boot-Apps mit Dynatrace Java OneAgent

Dieser Artikel erklärt, wie man Dynatrace OneAgent verwendet, um Spring Boot Anwendungen in Azure Spring Cloud zu überwachen.


Mit Dynatrace OneAgent können Sie die folgenden Aufgaben ausführen:

* Überwachen von Apps mit dem Dynatrace OneAgent.
* Konfigurieren von Dynatrace OneAgent mithilfe von Umgebungsvariablen.
* Überprüfen aller Überwachungsdaten aus dem Dynatrace-Dashboard.

Das folgende Video stellt Dynatrace OneAgent vor.

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* [Ein Dynatrace-Konto](https://www.dynatrace.com/)
* [Ein Dynatrace-PaaS-Token und ein Mandantentoken](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Aktivieren von Dynatrace OneAgent

In den folgenden Abschnitten wird beschrieben, wie Dynatrace OneAgent aktiviert wird.

### <a name="prepare-your-azure-spring-cloud-environment"></a>Vorbereiten Ihrer Azure Spring Cloud-Umgebung

1. Erstellen Sie eine Instanz von Azure Spring Cloud.
1. Erstellen Sie eine Anwendung, die Sie an Dynatrace melden möchten, indem Sie den folgenden Befehl ausführen. Ersetzen Sie die Platzhalter *\<...>* durch Ihre eigenen Werte.
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>Bestimmen der Werte für die erforderlichen Umgebungsvariablen

Um Dynatrace OneAgent für Ihre Azure Spring Cloud-Instanz zu aktivieren, müssen Sie vier Umgebungsvariablen konfigurieren: `DT_TENANT`, `DT_TENANTTOKEN`, `DT_CONNECTION_POINT` und `DT_CLUSTER_ID`. Weitere Informationen finden Sie unter [Integrieren von OneAgent mit Azure Spring Cloud](https://www.dynatrace.com/support/help/shortlink/azure-spring).

Für Anwendungen mit mehreren Instanzen verfügt Dynatrace über mehrere Möglichkeiten, diese zu gruppieren. `DT_CLUSTER_ID` ist eine der Möglichkeiten. Weitere Informationen finden Sie unter [Anpassen der Struktur von Prozessgruppen](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/).

### <a name="add-the-environment-variables-to-your-application"></a>Hinzufügen der Umgebungsvariablen zu Ihrer Anwendung

Sie können ihrer Anwendung die Schlüssel-Wert-Paare der Umgebungsvariablen hinzufügen, indem Sie entweder das Azure-Portal oder die Azure CLI verwenden.

#### <a name="option-1-azure-cli"></a>Option 1: Azure CLI

Um die Schlüssel-Wert-Paare mit der Azure CLI hinzuzufügen, führen Sie den folgenden Befehl aus und ersetzen die Platzhalter *\<...>* durch die in den vorherigen Schritten ermittelten Werte.

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>Option 2: Portal

Um die Schlüssel-Wert-Paare mithilfe des Azure-Portals hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zur Liste Ihrer vorhandenen Anwendungen.

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Screenshot: Azure-Portal mit dem Abschnitt „Azure Spring Cloud-Apps“." lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. Wählen Sie eine Anwendung aus, um zur Seite **Übersicht** der Anwendung zu navigieren.

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="Screenshot des Abschnitts „Übersicht“ der Anwendung." lightbox="media/dynatrace-oneagent/overview-application.png":::

1. Wählen Sie **Konfigurationen** aus, um Werte für die Anwendung im Abschnitt **Umgebungsvariablen** hinzuzufügen, zu aktualisieren oder zu löschen.

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="Screenshot der Registerkarte „Umgebungsvariablen“ im Abschnitt „Konfiguration“ der Anwendung." lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automate-provisioning"></a>Automatisieren der Bereitstellung

Mit Terraform oder einer Azure Resource Manager-Vorlage (ARM-Vorlage) können Sie auch eine Bereitstellungsautomatisierungspipeline ausführen. Mit dieser Pipeline können Sie alle neuen Anwendungen, die Sie erstellen und bereitstellen, ganz ohne Aufwand instrumentieren und überwachen.

### <a name="automate-provisioning-using-terraform"></a>Automatisieren der Bereitstellung mit Terraform

Um die Umgebungsvariablen in einer Terraform-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Verwalten einer aktiven Azure Spring Cloud-Bereitstellung](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment).

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>Automatisieren der Bereitstellung mithilfe einer ARM-Vorlage

Um die Umgebungsvariablen in einer ARM-Vorlage zu konfigurieren, fügen Sie der Vorlage den folgenden Code hinzu, und ersetzen Sie dabei die Platzhalter *\<...>* durch Ihre eigenen Werte. Weitere Informationen finden Sie unter [Microsoft.AppPlatform/Spring/apps/deployments/skus](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json).

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>Anzeigen von Berichten in Dynatrace

In diesem Abschnitt wird beschrieben, wie Sie verschiedene Berichte in Dynatrace finden.

> [!NOTE]
> Das Menü und die Benutzeroberfläche von Dynatrace werden schrittweise weiterentwickelt. Aus diesem Grund kann das Dashboard in andere Abschnitte der Dynatrace-Website verschoben werden, und die folgenden Screenshots spiegeln möglicherweise nicht die aktuelle Version der Benutzeroberfläche wider.

Nachdem Sie Ihrer Anwendung die Umgebungsvariablen hinzugefügt haben, beginnt Dynatrace mit dem Erfassen von Daten. Verwenden Sie zum Anzeigen von Berichten das [Dynatrace-Menü](https://www.dynatrace.com/support/help/get-started/navigation/), navigieren Sie zu **Services** (Dienste), und wählen Sie dann Ihre Anwendung aus.

Sie finden den **Dienstfluss** unter **\<your-app-name>/Details/Service flow**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Screenshot des Berichts „Service Flow“ von Dynatrace." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

Die **Methodenhotspots** finden Sie unter **\<your-app-name>/Details/Method hotspots**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="Screenshot des Berichts „Method hotspots“." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

Die **Datenbankanweisungen** finden Sie unter **\<your-app-name> /Details/Response time analysis**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="Screenshot: Bericht „Response time analysis“ und Abschnitt „Database statements“." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

Navigieren Sie dann zum Abschnitt **Multidimensional analysis** (Mehrdimensionale Analyse).

Sie finden die **Top-Datenbankanweisungen** unter **Multidimensional analysis/Top database statements**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="Screenshot des Berichts „Top database statements“." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

Die **Übersicht über Ausnahmen** finden Sie unter **Multidimensional analysis/Exceptions overview**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="Screenshot des Berichts „Exceptions overview“" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::.

Navigieren Sie nun zum Abschnitt **Profiling and optimization** (Profilerstellung und Optimierung).

Sie finden die **CPU-Analyse** unter **Profiling and optimization/CPU analysis**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="Screenshot des Berichts „CPU analysis“." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

Navigieren Sie nun zum Abschnitt **Databases** (Datenbanken).

**Backtrace** finden Sie unter **Databases/Details/Backtrace**:

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="Screenshot des Berichts „Backtrace“." lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="view-dynatrace-oneagent-logs"></a>Anzeigen von Dynatrace OneAgent-Protokollen

Standardmäßig gibt Azure Spring Cloud die Protokolle der *Infoebene* des Dynatrace OneAgent an `STDOUT` aus. Die Protokolle werden mit den Anwendungsprotokollen gemischt. Sie können die explizite Agent-Version aus den Anwendungsprotokollen abrufen.

Sie können die Protokolle des Dynatrace-Agents auch an den folgenden Speicherorten abrufen:

* Azure Spring Cloud-Protokolle
* Azure Spring Cloud Application Insights
* Azure Spring Cloud-LogStream

Sie können einige von Dynatrace bereitgestellte Umgebungsvariablen verwenden, um die Protokollierung für Dynatrace OneAgent zu konfigurieren. `DT_LOGLEVELCON` steuert beispielsweise den Protokollierungsgrad.

> [!CAUTION]
> Es wird dringend empfohlen, das Standardverhalten der Protokollierung nicht außer Kraft zu setzen, das von Azure Spring Cloud für Dynatrace bereitgestellt wird. Andernfalls werden die oben beschriebenen Protokollierungsszenarien blockiert, und die Protokolldateien gehen möglicherweise verloren. Beispielsweise sollten Sie die Umgebungsvariable `DT_LOGLEVELFILE` nicht an Ihre Anwendungen ausgeben.

## <a name="dynatrace-oneagent-upgrade"></a>Dynatrace OneAgent-Upgrade

Das automatische Upgrade von Dynatrace OneAgent ist deaktiviert und wird vierteljährlich mit dem JDK aktualisiert. Das Agent-Upgrade kann sich auf die folgenden Szenarien auswirken:

* Vorhandene Anwendungen, die Dynatrace OneAgent vor dem Upgrade verwenden, bleiben unverändert, erfordern jedoch einen Neustart oder eine erneute Bereitstellung, um die neue Version von Dynatrace OneAgent zu verwenden.
* Anwendungen, die nach dem Upgrade erstellt werden, verwenden die neue Version von Dynatrace OneAgent.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Konfiguration des ausgehenden Datenverkehrs der VNet-Injektionsinstanz

Für eine VNet-Injektionsinstanz von Azure Spring Cloud müssen Sie sicherstellen, dass der ausgehende Datenverkehr für Dynatrace-Kommunikationsendpunkte ordnungsgemäß für Dynatrace OneAgent konfiguriert ist. Informationen zum Abrufen von `communicationEndpoints` finden Sie unter [Bereitstellungs-API: Abrufen von Konnektivitätsinformationen für OneAgent](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/). Weitere Informationen finden Sie unter [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNet](vnet-customer-responsibilities.md).

## <a name="dynatrace-support-model"></a>Dynatrace-Unterstützungsmodell

Informationen zu Einschränkungen bei der Bereitstellung von Dynatrace OneAgent im reinen Anwendungsmodus finden Sie im Abschnitt [Cloud-Anwendungsplattformen](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms) der [OneAgent-Plattform- und -Funktionsunterstützungsmatrix](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der verteilten Ablaufverfolgung mit Azure Spring Cloud](how-to-distributed-tracing.md)

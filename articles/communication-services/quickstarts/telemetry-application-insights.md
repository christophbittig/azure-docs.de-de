---
title: 'Schnellstart: Exportieren von SDK-Telemetriedaten in Application Insights'
titleSuffix: An Azure Communication Services quickstart
description: Erfahren Sie, wie Sie Telemetriedaten vom Azure Communication Services SDK in Application Insights exportieren.
author: peiliu
manager: vravikumar
services: azure-communication-services
ms.author: peiliu
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: data
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 71f294cb42977b89fc36d8f750aa43baadb31f47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609496"
---
# <a name="quickstart-using-azure-opentelemetry-exporter-to-export-sdk-telemetry-data-to-application-insights"></a>Schnellstart: Verwenden von Azure OpenTelemetry Exporter zum Exportieren von SDK-Telemetriedaten in Application Insights

Der Azure OpenTelemetry Exporter ist ein SDK innerhalb von [Azure Monitor](../../azure-monitor/index.yml). Damit können Sie Ablaufverfolgungsdaten mithilfe von OpenTelemetry exportieren und die Daten an [Application Insights](../../azure-monitor/app/app-insights-overview.md) senden. OpenTelemetry bietet eine standardisierte Möglichkeit für Anwendungen und Frameworks, Telemetrieinformationen zu sammeln.

Azure Application Insights ist ein Feature von Azure Monitor, das zum Überwachen von Liveanwendungen verwendet wird. Es zeigt Telemetriedaten zu Ihrer Anwendung in einer Microsoft Azure-Ressource an. Der Telemetriemodell ist standardisiert, damit eine plattform- und sprachunabhängige Überwachung erstellt werden kann.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/telemetry-app-insights-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/telemetry-app-insights-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/telemetry-app-insights-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/telemetry-app-insights-python.md)]
::: zone-end

In der Ausgabe der App wird die jeweils abgeschlossene Aktion beschrieben:
<!---cSpell:disable --->
```console
Created an identity with ID: <identity-id>
Issued an access token with 'chat' scope that expires at <expiry-data>
```
<!---cSpell:enable --->

## <a name="view-the-telemetry-data-in-application-insights"></a>Visualisieren Ihrer Telemetriedaten in Application Insights
Zum Analysieren der Telemetriedaten aus dem SDK navigieren Sie zur Registerkarte `Performance` und dann zu `Dependencies`. Sie können die `Create User Activity` und `Get Token Activity` sehen, die wir nachverfolgt haben.

:::image type="content" source="media/application-insights-dependencies.png" alt-text="Screenshot: Telemetriedateneinträge in Application Insights":::

Zum Anzeigen weiterer Details können Sie einen Drilldown in die Beispiele machen:

:::image type="content" source="media/application-insights-samples-drill-down.png" alt-text="Screenshot: Drilldownansicht der Beispiele":::

In der Drilldownansicht gibt es weitere Informationen zur Aktivität, z. B. wo sie aufgerufen wurde, ihren Zeitstempel, den Namen, die Leistung, den Typ usw. Sie können außerdem den Cloudrollennamen und die Instanz-ID anzeigen, die wir im obigen Beispielcodeausschnitt definiert haben. Beachten Sie, dass die benutzerdefinierten Eigenschaften, die nachverfolgt wurden, auch hier angezeigt werden:

:::image type="content" source="media/application-insights-e2e-details.png" alt-text="End-to-End-Ansicht der Transaktionsdetails":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten von Telemetry Exporter
> * Exportieren von Telemetriedaten in Application Insights
> * Anzeigen der exportierten Daten in Application Insights

Das könnte Sie auch interessieren:

- [Weitere Informationen zum Analysieren von Daten in Application Insights](/powerapps/maker/canvas-apps/application-insights)
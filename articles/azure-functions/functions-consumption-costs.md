---
title: Abschätzen der Kosten des Verbrauchstarifs in Azure Functions
description: Erfahren Sie, wie Sie die Kosten besser abschätzen, die Sie eventuell verursachen, wenn Sie Ihre Funkions-App in einem Verbrauchstarif in Azure ausführen.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 9544dded7516b07ad7d919d08b0b9cd4e12d0607
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567321"
---
# <a name="estimating-consumption-plan-costs"></a>Abschätzen der Kosten des Verbrauchstarifs

Zurzeit gibt es drei Typen von Hostingtarifen für eine App, die in Azure Functions ausgeführt wird, wobei jeder Tarif ein eigenes Preismodell aufweist: 

| Planen | BESCHREIBUNG |
| ---- | ----------- |
| [**Verbrauch**](consumption-plan.md) | Ihnen wird nur die Zeit in Rechnung gestellt, die Ihre Funktions-App auch ausgeführt wird. Dieser Tarif umfasst pro Abonnement eine [free grant][seite mit der preisübersicht] (kostenlose Zuweisung).|
| [**Premium**](functions-premium-plan.md) | Bietet Ihnen dieselben Features und Skalierungsverfahren wie der Verbrauchstarif, aber mit mehr Leistung und VNET-Zugriff. Die Kosten basieren auf dem von Ihnen gewählten Tarif. Weitere Informationen finden Sie unter [Premium-Plan (Premium-Tarif) für Azure Functions](functions-premium-plan.md). |
| [**Dediziert (App Service)**](dedicated-plan.md) <br/>(Tarif „Basic“ oder höher) | Wenn Sie in dedizierten VMs oder in Isolierung ausführen müssen, benutzerdefinierte Images verwenden oder die Überkapazität Ihres App Service-Plans nutzen möchten. Verwendet [normale App Service-Planabrechnung](https://azure.microsoft.com/pricing/details/app-service/). Die Kosten basieren auf dem von Ihnen gewählten Tarif.|

Sie haben den Plan ausgewählt, der die Leistung Ihrer Funktion sowie Ihre Kostenanforderungen am besten unterstützt. Weitere Informationen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md).

Dieser Artikel behandelt nur den Verbrauchstarif, da dieser Tarif zu variablen Kosten führt. Dieser Artikel ersetzt den Artikel [Häufig gestellte Fragen zur Abrechnung von Plankosten](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

Durable Functions können auch in einem Verbrauchstarif ausgeführt werden. Weitere Informationen zu den Kostenüberlegungen bei der Verwendung von Durable Functions finden Sie unter [Abrechnung von Durable Functions](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Kosten des Verbrauchstarifs

Die Ausführungs *kosten* einer einzelnen Funktionsausführung werden in *GB-Sekunden* gemessen. Die Ausführungskosten werden berechnet, indem die Speichernutzung mit der Ausführungsdauer kombiniert wird. Eine Funktion, die länger ausgeführt wird, kostet mehr, ebenso wie eine Funktion, die mehr Arbeitsspeicher verbraucht. 

Stellen Sie sich einen Fall vor, bei dem die von der Funktion verwendete Arbeitsspeichermenge konstant bleibt. In diesem Fall erfolgt die Berechnung der Kosten durch einfache Multiplikation. Nehmen wir beispielsweise an, dass ihre Funktion 0,5 GB für 3 Sekunden verbraucht hat. Dann betragen die Ausführungskosten `0.5GB * 3s = 1.5 GB-seconds`. 

Da sich die Speichernutzung im Laufe der Zeit ändert, ist die Berechnung im Wesentlichen das Integral der Arbeitsspeichernutzung im Lauf der Zeit.  Das System führt diese Berechnung durch, indem die Speichernutzung des Prozesses (zusammen mit untergeordneten Prozessen) in regelmäßigen Abständen stichprobenhaft erfasst wird. Wie auf der [Seite mit der Preisübersicht] erwähnt, wird die Speichernutzung auf den nächstgelegenen 128-MB-Bucket aufgerundet. Wenn Ihr Prozess 160 MB verwendet, werden Ihnen 256 MB in Rechnung gestellt. Die Berechnung berücksichtigt dabei die Parallelität, bei der es sich um die gleichzeitige Ausführung mehrerer Funktionen im selben Prozess handelt.

> [!NOTE]
> Obwohl die CPU-Nutzung in den Ausführungskosten nicht direkt berücksichtigt wird, kann sie sich auf die Kosten auswirken, wenn sie die Ausführungszeit der Funktion beeinflusst.

Bei durch HTTP ausgelösten Funktionen werden Ihnen keine Ausführungen in Rechnung gestellt, wenn ein Fehler auftritt, bevor die Ausführung Ihres Funktionscodes beginnt. Dies bedeutet, dass 401-Antworten von der Plattform aufgrund der API-Schlüsselüberprüfung oder des Authentifizierungs-/Autorisierungsfeatures von App Service nicht auf Ihre Ausführungskosten angerechnet werden. Ebenso werden 5xx-Statuscodeantworten nicht gezählt, wenn diese auf der Plattform auftreten, bevor eine Funktion die Anforderung verarbeitet. Eine 5xx-Antwort, die nach dem Ausführungsbeginn Ihres Funktionscodes generiert wurde, wird dennoch als Ausführung gezählt, selbst wenn der Fehler nicht von Ihrem Funktionscode ausgelöst wird.

## <a name="other-related-costs"></a>Andere verwandte Kosten

Wenn Sie die Gesamtkosten der Ausführung ihrer Funktionen in einem beliebigen Plan abschätzen, denken Sie daran, dass die Functions-Laufzeit mehrere andere Azure-Dienste verwendet, die jeweils gesondert abgerechnet werden. Wenn Sie Preise für Funktions-Apps berechnen, müssen Sie für alle Trigger und Bindungen, die in andere Azure-Dienste integriert werden, diese zusätzlichen Dienste erstellen und dafür bezahlen. 

Für Funktionen, die in einem Verbrauchstarif ausgeführt werden, bestehen die Gesamtkosten aus den Ausführungskosten ihrer Funktionen plus die Kosten für Bandbreite und zusätzliche Dienste. 

Wenn Sie die Gesamtkosten für ihre Funktions-App und der zugehörige Dienste abschätzen, verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Verwandte Kosten | BESCHREIBUNG |
| ------------ | ----------- |
| **Speicherkonto** | Für jede Funktions-App ist es erforderlich, dass Sie über ein zugeordnetes universelles [Azure Storage-Konto](../storage/common/storage-introduction.md#types-of-storage-accounts) verfügen, das [gesondert abgerechnet](https://azure.microsoft.com/pricing/details/storage/) wird. Dieses Konto wird von der Functions-Laufzeit intern verwendet, aber Sie können es auch für Storage-Trigger und -Bindungen verwenden. Wenn Sie kein Speicherkonto besitzen, wird mit der Erstellung der Funktions-App eins für Sie erstellt. Weitere Informationen finden Sie unter [Speicherkontoanforderungen](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Functions basiert auf [Application Insights](../azure-monitor/app/app-insights-overview.md), um eine Hochleistungsüberwachungs-Erfahrung für Ihre Funktions-Apps bereitzustellen. Wenn dies auch nicht erforderlich ist, sollten Sie doch die [Application Insights-Integration aktivieren](configure-monitoring.md#enable-application-insights-integration). Monatlich ist eine kostenlose Zuweisung von Telemetriedaten enthalten. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Netzwerkbandbreite** | Sie bezahlen nicht für Datenübertragungen zwischen Azure-Diensten innerhalb derselben Region. Allerdings können Sie Kosten für ausgehende Datenübertragungen in eine andere Region oder außerhalb von Azure verursachen. Weitere Informationen finden Sie in den [Bandbreitenpreisdetails](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Verhalten, die die Ausführungszeit beeinflussen

Die folgenden Verhalten Ihrer Funktionen können sich auf die Ausführungszeit auswirken:

+ **Trigger und Bindungen**: Die Zeit, die das Lesen von Eingaben aus Ihren und das Schreiben der Ausgaben in Ihre [Funktionsbindungen](functions-triggers-bindings.md) benötigt, wird als Ausführungszeit gezählt. Wenn Ihre Funktion beispielsweise eine Ausgabenbindung verwendet, um eine Nachricht in eine Azure Storage-Warteschlange zu schreiben, beinhaltet Ihre Ausführungszeit die Zeit, die zum Schreiben der Nachricht in die Warteschlange benötigt wird, was in die Berechnung der Funktionskosten einfließt. 

+ **Asynchrone Ausführung**: Die Zeit, die Ihre Funktion auf die Ergebnisse einer asynchronen Anforderung wartet (`await` in C#), wird als Ausführungszeit gezählt. Die GB-Sekunden-Berechnung basiert auf der Start- und Endzeit der Funktion und der Arbeitsspeichernutzung in diesem Zeitraum. Was im Verlauf dieses Zeitraums hinsichtlich der CPU-Aktivität geschieht, wird nicht in die Berechnung einbezogen. Sie können die Kosten möglicherweise während asynchroner Vorgänge verringern, indem Sie [Durable Functions](durable/durable-functions-overview.md) verwenden. In awaits verbrachte Zeit wird Ihnen für Orchestratorfunktionen nicht berechnet.

## <a name="viewing-cost-related-data"></a>Anzeigen von kostenbezogenen Daten

In [Ihrer Rechnung](../cost-management-billing/understand/download-azure-invoice.md) können Sie die kostenbezogenen Daten **Total Executions – Functions** (Ausführungen gesamt) und **Execution Time – Functions** (Ausführungszeit) zusammen mit den tatsächlich abgerechneten Kosten sehen. Diese Rechnungsdaten stellen jedoch eine monatliche Aggregation für einen vergangenen Abrechnungszeitraum dar. 

### <a name="function-app-level-metrics"></a>Metriken auf Ebene der Funktions-App

Um die Kostenauswirkungen Ihrer Funktionen besser zu verstehen, können Sie Azure Monitor verwenden, um kostenbezogene Metriken anzuzeigen, die von Ihren Funktions-Apps generiert werden. 

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]

### <a name="function-level-metrics"></a>Metriken auf Funktionsebene

Funktionsausführungseinheiten sind eine Kombination aus Ausführungszeit und Ihrer Speichernutzung, was diese zu einer schwierigen Metrik macht, um die Speichernutzung zu verstehen. Arbeitsspeicherdaten sind keine aktuell in Azure Monitor verfügbare Metrik. Wenn Sie jedoch die Speichernutzung Ihrer App optimieren möchten, können Sie die von Application Insights erfassten Daten der Leistungsindikatoren verwenden.  

Falls noch nicht geschehen, [aktivieren Sie Application Insights in Ihrer Funktions-App](configure-monitoring.md#enable-application-insights-integration). Mit dieser aktivierten Integration können Sie [diese Telemetriedaten im Portal abfragen](analyze-telemetry-data.md#query-telemetry-data). 

Zum Abrufen der Metrikdaten von Azure Monitor können Sie entweder den [Azure Monitor-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md) im [Azure-Portal] oder REST-APIs verwenden.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zur Überwachung von Funktions-Apps](functions-monitoring.md)

[Seite mit der Preisübersicht]:https://azure.microsoft.com/pricing/details/functions/
[Azure portal]: https://portal.azure.com

---
title: Bewährte Methoden für Azure Functions
description: Erfahren Sie mehr über bewährte Methoden zum Entwerfen, Bereitstellen und Verwalten von effizientem Funktionscode, der in Azure ausgeführt wird.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f2efa490a9788f0e52b4dfb19b4359f247a671e9
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057717"
---
# <a name="best-practices-for-reliable-azure-functions"></a>Bewährte Methoden für zuverlässigen Azure Functions-Code

Azure Functions ist eine ereignisgesteuerte Compute-on-Demand-Umgebung, die die vorhandene Azure App Service-Anwendungsplattform um Funktionen zur Implementierung von Code erweitert, der durch Ereignisse ausgelöst wird, die in Azure, in Diensten von Drittanbietern und in lokalen Systemen auftreten. Mit Functions können Sie Lösungen erstellen, indem Sie eine Verbindung mit Datenquellen oder Messaginglösungen herstellen, wodurch die Verarbeitung und Reaktion auf Ereignisse vereinfacht wird. Functions wird in Azure-Rechenzentren ausgeführt, die mit vielen integrierten Komponenten komplex sind. In einer gehosteten Cloudumgebung wird erwartet, dass VMs gelegentlich neu gestartet oder verschoben werden können und Systemupgrades durchgeführt werden. Ihre Funktions-Apps sind wahrscheinlich auch von externen APIs, Azure-Diensten und anderen Datenbanken abhängig, die ebenfalls anfällig für regelmäßige Unzuverlässigkeiten sind. 

In diesem Artikel werden einige bewährte Methoden zum Entwerfen und Bereitstellen effizienter Funktions-Apps beschrieben, die fehlerfrei bleiben und in einer cloudbasierten Umgebung gut funktionieren.

## <a name="choose-the-correct-hosting-plan"></a>Auswählen des richtigen Hostingplans 

Wenn Sie eine Funktions-App in Azure erstellen, müssen Sie einen Hostingplan für die App auswählen. Der von Ihnen ausgewählten Plan wirkt sich auf Leistung, Zuverlässigkeit und Kosten aus. Für Functions sind drei grundlegende Hostingpläne verfügbar: 

+ [Verbrauchstarif](consumption-plan.md)
+ [Premium-Plan](functions-premium-plan.md)
+ [Dedizierter (App Service-) Plan](dedicated-plan.md)

Alle Hostingpläne sind allgemein verfügbar (GA), wenn Sie Linux oder Windows ausführen.

Im Kontext der App Service-Plattform ist der Premium-Plan, der zum dynamischen Hosten Ihrer Funktionen verwendet wird, der Elastic Premium Plan (EP). Es gibt andere dedizierte Pläne (App Service), die als Premium bezeichnet werden. Weitere Informationen finden Sie im Artikel [Premium-Plan](functions-premium-plan.md).

Der von Ihnen ausgewählte Hostingplan bestimmt folgendes Verhalten:

+   Wie Ihre Funktions-App bedarfsbasiert skaliert und die Instanzzuordnung verwaltet wird.
+   Die für jede Instanz der Funktions-App verfügbaren Ressourcen.
+   Die Unterstützung für erweiterte Funktionen wie Azure Virtual Network-Konnektivität

Weitere Informationen zur Auswahl des richtigen Hostingplans sowie einen detaillierten Vergleich der Pläne finden Sie unter [Azure Functions-Hostingoptionen](functions-scale.md).

Es ist wichtig, den richtigen Plan auszuwählen, wenn Sie Ihre Funktions-App erstellen. Functions bietet eine eingeschränkte Möglichkeit, Ihren Hostingplan zu wechseln, in erster Linie zwischen Verbrauchs- und Elastic Premium-Plänen. Weitere Informationen finden Sie unter [Planmigration](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration). 

## <a name="configure-storage-correctly"></a>Richtiges Konfigurieren des Speichers

Functions erfordert, dass Ihrer Funktions-App ein Speicherkonto zugeordnet ist. Die Speicherkontoverbindung wird vom Functions-Host für Vorgänge wie das Verwalten von Triggern und das Protokollieren von Funktionsausführungen verwendet. Sie wird auch beim dynamischen Skalieren von Funktions-Apps verwendet. Weitere Informationen finden Sie unter [Speicherüberlegungen für Azure Functions](storage-considerations.md).

Ein falsch konfiguriertes Dateisystem oder Speicherkonto in Ihrer Funktions-App kann sich auf die Leistung und Verfügbarkeit Ihrer Funktionen auswirken. Hilfe bei der Problembehandlung eines falsch konfigurierten Speicherkontos finden Sie im Artikel zur [Problembehandlung für Speicher](functions-recover-storage-account.md). 

### <a name="storage-connection-settings"></a>Speicherverbindungseinstellungen

Funktions-Apps, die dynamisch skaliert werden, können entweder über einen Azure Files-Endpunkt in Ihrem Speicherkonto oder von den Dateiservern ausgeführt werden, die Ihren skalierten Instanzen zugeordnet sind. Dieses Verhalten wird durch die folgenden Anwendungseinstellungen gesteuert:

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

Diese Einstellungen werden nur unterstützt, wenn die Ausführung in einem Premium-Plan oder in einem Verbrauchsplan unter Windows erfolgt.

Wenn Sie Ihre Funktions-App entweder im Azure-Portal oder mit der Azure CLI oder Azure PowerShell erstellen, werden diese Einstellungen bei Bedarf für Ihre Funktions-App erstellt. Wenn Sie Ihre Ressourcen aus einer Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen, müssen Sie auch `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` in die Vorlage einschließen. 

Schließen Sie in Ihrer ersten Bereitstellung mithilfe einer ARM-Vorlage das `WEBSITE_CONTENTSHARE`-Element nicht ein, das für Sie generiert wird.   

Sie können die folgenden ARM-Vorlagenbeispiele verwenden, um diese Einstellungen richtig zu konfigurieren:

+ [Verbrauchstarif](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [Dedizierter Plan](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [Premium-Plan mit VNet-Integration](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [Verbrauchstarif mit einem Bereitstellungsslot](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>Speicherkontokonfiguration

Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Warteschlangen- und Tabellenspeicher unterstützt. Functions basiert für Vorgänge wie das Verwalten von Triggern und das Ausführen von Protokollierfunktionen auf Azure Storage. Die Verbindungszeichenfolge des Speicherkontos für Ihre Funktions-App finden Sie in den Anwendungseinstellungen `AzureWebJobsStorage` und `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`.

Beachten Sie beim Erstellen dieses Speicherkontos die folgenden Überlegungen: 

+ Erstellen Sie das Speicherkonto in derselben Region wie die Funktions-App, um die Latenz zu reduzieren.

+ Um die Leistung in der Produktion zu verbessern, verwenden Sie ein separates Speicherkonto für jede Funktions-App. Dies gilt insbesondere für Durable Functions und durch Event Hub ausgelöste Funktionen. 

+ Verwenden Sie für durch Event Hub ausgelöste Funktionen kein Konto mit [aktivierter Data Lake Storage-Funktion](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81).

### <a name="handling-large-data-sets"></a>Verwalten von großen Datasets

Wenn die Ausführung unter Linux erfolgt, können Sie zusätzlichen Speicher hinzufügen, indem Sie eine Dateifreigabe einbinden. Das Einbinden einer Freigabe ist eine praktische Möglichkeit für eine Funktion, ein großes vorhandenes Dataset zu verarbeiten. Weitere Informationen finden Sie unter [Einbinden von Dateifreigaben](storage-considerations.md#mount-file-shares).

## <a name="organize-your-functions"></a>Organisieren Ihrer Funktionen 

Sie entwickeln und veröffentlichen im Rahmen Ihrer Lösung wahrscheinlich mehrere Funktionen. Diese Funktionen werden häufig in einer einzelnen Funktions-App zusammengefasst, können aber auch in separaten Funktions-Apps ausgeführt werden. In den Hostingplänen Premium und Dedicated (App Service) können auch mehrere Funktions-Apps dieselben Ressourcen gemeinsam nutzen, indem sie im selben Plan ausgeführt werden. Wie Sie Ihre Funktionen und Funktions-Apps gruppieren, hat Einfluss auf die Leistung, Skalierung, Konfiguration, Bereitstellung und Sicherheit Ihrer gesamten Lösung. 

Für den Verbrauchstarif und den Premium-Plan werden alle Funktionen in einer Funktions-App dynamisch gemeinsam skaliert.

Weitere Informationen zum Organisieren Ihrer Funktionen finden Sie unter [Bewährte Methoden für die Funktionsorganisation](performance-reliability.md#function-organization-best-practices).

## <a name="optimize-deployments"></a>Optimieren von Bereitstellungen

Beim Bereitstellen einer Funktions-App ist es wichtig zu beachten, dass die Bereitstellungseinheit für Funktionen in Azure die Funktions-App ist. Alle Funktionen in einer Funktions-App werden zur gleichen Zeit bereitgestellt, in der Regel aus demselben Bereitstellungspaket.  

Berücksichtigen Sie diese Optionen für eine erfolgreiche Bereitstellung:

+  Planen Sie die Ausführung Ihrer Funktionen aus dem Bereitstellungspaket. Dieser Ansatz [Ausführung aus dem Paket](run-functions-from-deployment-package.md) bietet die folgenden Vorteile:

    + Reduziert das Risiko von Sperrungen beim Kopieren von Dateien. 
    + Kann direkt in einer Produktions-App bereitgestellt werden, wodurch ein Neustart ausgelöst wird. 
    + Sie wissen, dass alle Dateien im Paket für Ihre App verfügbar sind. 
    + Verbessert die Leistung von ARM-Vorlagenbereitstellungen.
    + Kann Kaltstartzeiten verringern, insbesondere für JavaScript-Funktionen mit großen npm-Paketstrukturen.

+ Erwägen Sie die Verwendung von [Continuous Deployment](functions-continuous-deployment.md), um Bereitstellungen mit Ihrer Quellcodeverwaltungslösung zu verbinden. Mit Continuous Deployments können Sie die Ausführung ebenfalls aus dem Bereitstellungspaket vornehmen.

+ Für [Hosting im Premium-Plan](functions-premium-plan.md) sollten Sie einen Aufwärmtrigger hinzufügen, um die Latenz zu verringern, wenn neue Instanzen hinzugefügt werden. Weitere Informationen finden Sie unter [Azure Functions-Aufwärmtrigger](functions-bindings-warmup.md). 

## <a name="write-robust-functions"></a>Schreiben robuster Funktionen

Es gibt mehrere Entwurfsprinzipien, die Sie befolgen können, wenn Sie Ihren Funktionscode schreiben, um die allgemeine Leistung und Verfügbarkeit Ihrer Funktionen zu verbessern. Zu diesen Prinzipien gehören:
 
+ [Vermeiden von zeitintensiven Funktionen.](performance-reliability.md#avoid-long-running-functions) 
+ [Planen funktionsübergreifender Kommunikation.](performance-reliability.md#cross-function-communication) 
+ [Schreiben von zustandslosen Funktionen.](performance-reliability.md#write-functions-to-be-stateless)
+ [Schreiben von defensiven Funktionen.](performance-reliability.md#write-defensive-functions)

Da vorübergehende Fehler bei Cloud Computing häufig vorkommen, sollten Sie beim Zugriff auf cloudbasierte Ressourcen ein [Wiederholungsmuster](/azure/architecture/patterns/retry) verwenden. Viele Trigger und Bindungen implementieren bereits Wiederholungsversuche. 

## <a name="design-for-security"></a>Sicherheitsorientiertes Design

Sicherheit wird am besten während der Planungsphase und nicht erst berücksichtigt, nachdem Ihre Funktionen einsatzbereit sind. Informationen zum sicheren Entwickeln und Bereitstellen von Funktionen finden Sie unter [Sichern von Azure Functions](security-concepts.md).  

## <a name="consider-concurrency"></a>Inbetrachtziehen von Parallelität

Wenn der Bedarf für Ihre Funktions-App aufgrund eingehender Ereignisse zunimmt, werden Funktions-Apps, die im Verbrauchstarif und Premium-Plan ausgeführt werden, aufskaliert. Es ist wichtig zu verstehen, wie Ihre Funktions-App auf Last reagiert und wie die Trigger konfiguriert werden können, um eingehende Ereignisse zu verarbeiten. Eine allgemeine Übersicht finden Sie unter [Ereignisgesteuerte Skalierung in Azure Functions](event-driven-scaling.md).

Dedizierte Pläne (App Service) erfordern, dass Sie die Aufskalierung Ihrer Funktions-Apps vorsehen. 

### <a name="worker-process-count"></a>Anzahl von Workerprozessen

In einigen Fällen ist es effizienter, die Last zu bewältigen, indem mehrere Prozesse (so genannte Sprachworkerprozesse) in der Instanz vor der horizontalen Skalierung erstellt werden. Die maximal zulässige Anzahl von Sprachworkerprozessen wird durch die [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)-Einstellung gesteuert. Der Standardwert für diese Einstellung ist `1`. Dies bedeutet, dass nicht mehrere Prozesse verwendet werden. Nachdem die maximale Anzahl von Prozessen erreicht wurde, wird die Funktions-App auf mehr Instanzen aufskaliert, um die Last zu verarbeiten. Diese Einstellung gilt nicht für [Funktionen der C#-Klassenbibliothek](functions-dotnet-class-library.md), die im Hostprozess ausgeführt werden.

Wenn Sie `FUNCTIONS_WORKER_PROCESS_COUNT` für einen Premium-Plan oder einen dedizierten Plan (App Service) verwenden, beachten Sie die Anzahl der Kerne, die von Ihrem Plan bereitgestellt werden. Beispielsweise bietet der Premium-Plan `EP2` zwei Kerne, daher sollten Sie mit dem Wert `2` beginnen und bei Bedarf um zwei bis zum Höchstwert erhöhen.

### <a name="trigger-configuration"></a>Triggerkonfiguration

Bei der Planung von Durchsatz und Skalierung ist es wichtig zu verstehen, wie die verschiedenen Triggertypen Ereignisse verarbeiten. Mit einigen Triggern können Sie das Batchverarbeitungsverhalten steuern und Parallelität verwalten. Eine häufige Anpassung der Werte in diesen Optionen kann die Skalierung der Instanz an die Anforderungen der aufgerufenen Funktionen vereinfachen. Diese Konfigurationsoptionen werden auf alle Trigger in einer Funktions-App angewendet und in der Datei „host.json“ für die App verwaltet. Details zu den Einstellungen finden Sie im Abschnitt zur Konfiguration der jeweiligen Triggerreferenz.

Weitere Informationen zur Verarbeitung von Nachrichtenstreams durch Functions finden Sie unter [Zuverlässige Azure Functions-Ereignisverarbeitung](functions-reliable-event-processing.md).

### <a name="plan-for-connections"></a>Planen von Verbindungen

Funktions-Apps, die im [Verbrauchstarif](consumption-plan.md) ausgeführt werden, unterliegen Verbindungsgrenzwerten. Diese Grenzwerte werden pro Instanz erzwungen. Aufgrund dieser Grenzwerte und als allgemeine bewährte Methode sollten Sie Ihre ausgehenden Verbindungen aus Ihrem Funktionscode optimieren. Weitere Informationen finden Sie unter [Verwalten von Verbindungen in Azure Functions](manage-connections.md). 

### <a name="language-specific-considerations"></a>Sprachspezifische Erwägungen

Beachten Sie für die Sprache Ihrer Wahl die folgenden Überlegungen:

# <a name="c"></a>[C#](#tab/csharp)

+ [Verwenden Sie asynchronem Code bei Vermeidung von blockierenden Aufrufen](performance-reliability.md#use-async-code-but-avoid-blocking-calls).

+ [Verwenden Sie Abbruchtoken](functions-dotnet-class-library.md?#cancellation-tokens) (nur prozessintern).

# <a name="java"></a>[Java](#tab/java)

+ Erwägen Sie für Anwendungen, die eine Mischung aus CPU-gebundenen und E/A-gebundenen Vorgängen sind, die Verwendung [weiterer Workerprozesse](functions-app-settings.md#functions_worker_process_count).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [Verwenden Sie `async` und `await`](functions-reference-node.md#use-async-and-await).

+ [Verwenden Sie mehrere Workerprozesse für CPU-gebundene Anwendungen](functions-reference-node.md?tabs=v2#scaling-and-concurrency).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Überprüfen Sie die Parallelitätsüberlegungen](functions-reference-powershell.md#concurrency).

# <a name="python"></a>[Python](#tab/python)

+ [Verbessern der Durchsatzleistung von Python-Apps in Azure Functions](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>Maximieren der Verfügbarkeit

Kaltstart ist ein wichtiger Aspekt bei serverlosen Architekturen. Weitere Informationen finden Sie unter [Kaltstarts](event-driven-scaling.md#cold-start). Wenn Kaltstart ein Thema in Ihrem Szenario ist, finden Sie ausführlichere Informationen im Beitrag [Grundlegendes zu serverlosem Kaltstart](https://azure.microsoft.com/blog/understanding-serverless-cold-start/). 

Der Premium-Plan ist der empfohlene Plan zum Reduzieren von Kaltstarts bei gleichzeitiger Beibehaltung dynamischer Skalierung. Mithilfe der folgenden Anleitung können Sie Kaltstarts reduzieren und die Verfügbarkeit in allen drei Hostingplänen verbessern. 

| Planen | Leitfaden |
| --- | --- | 
| **Premium-Plan** | • [Implementieren eines Aufwärmtriggers in Ihrer Funktions-App](functions-bindings-warmup.md)<br/>• [Festlegen der Werte für Always-Ready-Instanzen und des Grenzwerts für maximalen Burst](functions-premium-plan.md#plan-and-sku-settings)<br/>• [Verwenden der Triggerunterstützung für virtuelle Netzwerke bei Verwendung von Nicht-HTTP-Triggern für ein virtuelles Netzwerk](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **Dedizierte Pläne** | • [Ausführung in mindestens zwei Instanzen mit aktivierter Azure App Service-Integritätsprüfung](../app-service/monitor-instances-health-check.md)<br/>• [Implementieren automatischer Skalierung](/azure/architecture/best-practices/auto-scaling)|
| **Verbrauchstarif** | • Überprüfen Sie die Verwendung von Singletonmustern und die Parallelitätseinstellungen für Bindungen und Trigger, um zu vermeiden, dass künstliche Einschränkungen für die Skalierung Ihrer Funktions-App gelten.<br/>• [Überprüfen Sie die `functionAppScaleLimit`-Einstellung, die horizontale Skalierung einschränken kann](event-driven-scaling.md#limit-scale-out)<br/>• Überprüfen Sie während der Entwicklung und beim Testen, ob ein Grenzwert für das tägliche Nutzungskontingent (GB-Sek.) festgelegt wurde. Erwägen Sie, diesen Grenzwert in Produktionsumgebungen zu entfernen. |

## <a name="monitor-effectively"></a>Effektive Überwachung

Azure Functions bietet eingebaute Integration in Azure Application Insights zur Überwachung der Funktionsausführung und der von Ihrem Code geschriebenen Ablaufverfolgungen. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Azure Monitor bietet auch Funktionen zum Überwachen der Integrität der Funktions-App selbst. Weitere Informationen finden Sie unter [Verwenden von Azure Monitor-Metriken mit Azure Functions](monitor-metrics.md).

Bei der Verwendung der Application Insights-Integration zur Überwachung Ihrer Funktionen sollten Sie die folgenden Punkte beachten:

+ Stellen Sie sicher, dass die Anwendungseinstellung [AzureWebJobsDashboard](functions-app-settings.md#azurewebjobsdashboard) entfernt wird. Diese Einstellung wurde in einer älteren Version von Functions unterstützt. Wenn sie vorhanden ist, verbessert das Entfernen von `AzureWebJobsDashboard` die Leistung Ihrer Funktionen. 

+  Überprüfen Sie die [Application Insights-Protokolle](analyze-telemetry-data.md). Wenn Daten fehlen, die Sie erwarten, sollten Sie die Einstellungen für Stichprobenentnahmen anpassen, um Ihr Überwachungsszenario besser zu erfassen. Sie können die `excludedTypes`-Einstellung verwenden, um bestimmte Typen von der Stichprobenentnahme auszuschließen, z. B. `Request` oder `Exception`. Weitere Informationen finden Sie unter [Konfigurieren des Samplings](configure-monitoring.md?tabs=v2#configure-sampling).

Azure Functions ermöglicht es Ihnen auch, [vom System generierte und benutzergenerierte Protokolle an Azure Monitor-Protokolle](functions-monitor-log-analytics.md) zu senden. Die Integration in Azure Monitor-Protokolle befindet sich derzeit in der Vorschau. 

## <a name="build-in-redundancy"></a>Integrierte Redundanz

Ihre geschäftlichen Anforderungen erfordern möglicherweise, dass Ihre Funktionen immer verfügbar sind, auch während eines Rechenzentrumsausfalls. Informationen dazu, wie Sie einen Ansatz mit mehreren Regionen verwenden, um ihre kritischen Funktionen immer auszuführen, finden Sie unter [Georedundante Azure Functions-Notfallwiederherstellung und -Hochverfügbarkeit](functions-geo-disaster-recovery.md).

## <a name="next-steps"></a>Nächste Schritte

[Verwalten Ihrer Funktions-App](functions-how-to-use-azure-function-app-settings.md)

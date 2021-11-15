---
title: Skalierung und Hosting von Azure Functions
description: Erfahren Sie, wie Sie zwischen einem Azure Functions-Verbrauchstarif und -Premium-Plan (Premium-Tarif) wählen.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9deb2e40c081b776a8dfa5fc5fb96efcfc4c219f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252937"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions-Hostingoptionen

Wenn Sie eine Funktions-App in Azure erstellen, müssen Sie einen Hostingplan für die App auswählen. Für Azure Functions gibt es drei grundlegende Hostingpläne: [Verbrauchstarif](consumption-plan.md), [Premium-Tarif](functions-premium-plan.md) und [Dedicated-Tarif (App Service)](dedicated-plan.md). Alle Hostingpläne sind sowohl für Linux- als auch für Windows-VMs allgemein verfügbar.

Der von Ihnen gewählte Hostingplan bestimmt folgendes Verhalten:

* Wie Ihre Funktions-App skaliert wird.
* Die für jede Instanz der Funktions-App verfügbaren Ressourcen.
* Die Unterstützung für erweiterte Funktionen wie Azure Virtual Network-Konnektivität

Dieser Artikel enthält einen ausführlichen Vergleich der verschiedenen Hostingpläne und [Kubernetes-basiertem Hosting](functions-kubernetes-keda.md).

> [!NOTE]
> Wenn Sie Ihre Funktionen in einem Kubernetes-Cluster hosten möchten, sollten Sie einen [Azure Arc-fähigen Kubernetes-Cluster](../azure-arc/kubernetes/overview.md) verwenden. Hosting auf einem für Azure Arc aktivierten Kubernetes-Cluster befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [App Service, Funktionen und Logic Apps in Azure Arc](../app-service/overview-arc-integration.md).  

## <a name="overview-of-plans"></a>Übersicht über die Pläne

Im Folgenden finden Sie eine Zusammenfassung der Vorteile der drei wichtigsten Hostingpläne für Functions:

| Planen | Vorteile |
| --- | --- |  
|**[Verbrauchsplan](consumption-plan.md)**| Skalieren Sie Ihre Computeressourcen automatisch, und zahlen Sie nur dann für diese Ressourcen, wenn Ihre Funktionen tatsächlich ausgeführt werden.<br/><br/>Im Verbrauchsplan werden Instanzen des Functions-Hosts basierend auf der Anzahl von eingehenden Ereignissen dynamisch hinzugefügt und entfernt.<br/><br/> ✔ Standardhostingplan.<br/>✔ Sie bezahlen nur, wenn Ihre Funktionen ausgeführt werden.<br/>✔ Die Skalierung erfolgt automatisch – selbst in Zeiten hoher Last.|  
|**[Premium-Plan](functions-premium-plan.md)**|In diesem Plan werden Ressourcen automatisch nach Bedarf skaliert. Nutzen Sie vorab aufgewärmte (also betriebsbereite) Worker, um Anwendungen nach einem Leerlauf ohne jede Verzögerung auszuführen, profitieren Sie von leistungsstärkeren Instanzen für die Ausführung, und stellen Sie Verbindungen mit virtuellen Netzwerken her. <br/><br/>Ziehen Sie den Premium-Plan für Azure Functions in folgenden Situationen in Betracht: <br/><br/>✔ Ihre Funktions-Apps werden kontinuierlich oder nahezu kontinuierlich ausgeführt.<br/>✔ Sie verfügen über eine hohe Anzahl kleiner Ausführungen und haben im Verbrauchstarif hohe Ausführungskosten, aber geringe Kosten für Gigabytesekunden.<br/>✔ Sie benötigen weitere CPU- oder Arbeitsspeicheroptionen zusätzlich zu den vom Verbrauchsplan bereitgestellten.<br/>✔ Ihr Code muss länger ausgeführt werden, als im Verbrauchsplan als maximal zulässige Ausführungsdauer angegeben ist.<br/>✔ Sie benötigen Features, die im Rahmen des Verbrauchstarifs nicht zur Verfügung stehen, z. B. Konnektivität mit virtuellen Netzwerken.<br/>✔ Sie möchten ein benutzerdefiniertes Linux-Image bereitstellen, auf dem Ihre Funktionen ausgeführt werden sollen. |  
|**[Dedizierter Plan](dedicated-plan.md)** |Führen Sie Ihre Funktionen in einem App Service-Plan zu den regulären [Preisen dieses Plans](https://azure.microsoft.com/pricing/details/app-service/windows/) aus.<br/><br/>Dieser Plan eignet sich am besten in zeitintensiven Szenarien, in denen [Durable Functions](durable/durable-functions-overview.md) nicht verwendet werden kann. Ziehen Sie einen App Service-Plan in folgenden Situationen in Betracht:<br/><br/>✔ Sie verfügen über nicht ausgelastete virtuelle Computer, auf denen bereits andere App Service-Instanzen ausgeführt werden.<br/>✔ Sie benötigen vorhersagbare Skalierung und Kosten.|  

Die Vergleichstabellen in diesem Artikel enthalten auch die folgenden Hostingoptionen, die die größtmögliche Kontrolle und Isolation bereitstellen, in denen Sie Ihre Funktions-Apps ausführen können.  

| Hostingoption | Details |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | Die App Service-Umgebung (App Service Environment, ASE) ist ein App Service-Feature, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Apps in großem Maßstab bereitstellt.<br/><br/>App Service-Umgebungen eignen sich ideal für Anwendungsworkloads mit folgenden Anforderungen: <br/><br/>✔ Sehr großer Umfang.<br/>✔ Vollständige Computeisolation und sicherer Netzwerkzugriff<br/>✔ Hohe Speicherauslastung|  
| **Kubernetes**<br/>([Direkt](functions-kubernetes-keda.md) oder<br/>[Azure Arc](../app-service/overview-arc-integration.md)) | Kubernetes bietet eine vollständig isolierte und dedizierte Umgebung, die auf der Kubernetes-Plattform aufsetzt.<br/><br/> Kubernetes eignet sich für Anwendungsworkloads mit folgenden Anforderungen: <br/>✔ Benutzerdefinierte Hardwareanforderungen.<br/>✔ Isolierung und sicherer Netzwerkzugriff.<br/>✔ Möglichkeit zur Ausführung in Hybrid- oder Multicloudumgebungen.<br/>✔ Parallele Ausführung mit vorhandenen Kubernetes-Anwendungen und -Diensten.|  

In den verbleibenden Tabellen in diesem Artikel werden die verschiedenen Features und Verhaltensweisen der Pläne verglichen. Einen Kostenvergleich zwischen dynamischen Hostingplänen (Verbrauch und Premium) finden Sie auf der Seite [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/). Die Preise für die verschiedenen Optionen bei einem dedizierten Plan finden Sie auf der Seite [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Betriebssystem/Runtime

In der folgenden Tabelle werden die in den einzelnen Hostingplänen unterstützten Betriebssysteme und Programmiersprachen gezeigt.

| | Linux<sup>1</sup><br/>Nur Code | Windows<sup>2</sup><br/>Nur Code | Linux<sup>1,3</sup><br/>Docker-Container |
| --- | --- | --- | --- |
| **[Verbrauchsplan](consumption-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript | Keine Unterstützung  |
| **[Premium-Plan](functions-premium-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript  | 
| **[Dedizierter Plan](dedicated-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |
| **[ASE](dedicated-plan.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>TypeScript |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript | 
| **[Kubernetes (direkt)](functions-kubernetes-keda.md)** | – | – |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |
| **[Azure Arc (Vorschauversion)](../app-service/overview-arc-integration.md)** | .NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>Python<br/>TypeScript | – |.NET Core 3.1<br/>.NET 5.0<br/>JavaScript<br/>Java<br/>PowerShell Core<br/>Python<br/>TypeScript |

<sup>1</sup> Linux ist das einzige unterstützte Betriebssystem für den Python-Runtimestapel. <br/>
<sup>2</sup> Windows ist das einzige unterstützte Betriebssystem für den PowerShell-Runtimestapel.<br/>
<sup>3</sup> Linux ist das einzige unterstützte Betriebssystem für Docker-Container.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Skalieren

In der folgenden Tabelle wird das Skalierungsverhalten der verschiedenen Hostingpläne verglichen.

| Planen | Aufskalieren | Maximale Anzahl Instanzen |
| --- | --- | --- |
| **[Verbrauchsplan](consumption-plan.md)** | [Ereignisgesteuert.](event-driven-scaling.md) Das Aufskalieren erfolgt automatisch – selbst in Zeiten hoher Lasten. Die Azure Functions-Infrastruktur skaliert CPU- und Arbeitsspeicherressourcen durch Hinzufügen zusätzlicher Instanzen des Functions-Hosts basierend auf der Anzahl der eingehenden Triggerereignisse. | 200 |
| **[Premium-Plan](functions-premium-plan.md)** | [Ereignisgesteuert.](event-driven-scaling.md) Das Aufskalieren erfolgt automatisch – selbst in Zeiten hoher Lasten. Die Azure Functions-Infrastruktur skaliert CPU- und Arbeitsspeicherressourcen durch Hinzufügen zusätzlicher Instanzen des Functions-Hosts basierend auf der Anzahl der Ereignisse, für die Funktionen ausgelöst werden. |100|
| **[Dedicated-Plan](dedicated-plan.md)** <sup>1</sup> | Manuelle Skalierung/Autoskalierung |10 – 20|
| **[ASE](dedicated-plan.md)** <sup>1</sup> | Manuelle Skalierung/Autoskalierung |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Ereignisgesteuerte Autoskalierung für Kubernetes-Cluster über [KEDA](https://keda.sh). | Variiert je&nbsp;nach&nbsp;Cluster.&nbsp;&nbsp;|

<sup>1</sup> Spezifische Grenzwerte für die verschiedenen Optionen des App Service-Plans finden Sie unter [Grenzwerte für App Service-Pläne](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Kaltstartverhalten

| Planen | Details | 
| -- | -- |
| **[Verbrauchsplan](consumption-plan.md)** | Apps im Leerlauf werden möglicherweise auf null skaliert, sodass bei einigen Anforderungen die Latenz beim Start steigen kann.  Der Verbrauchsplan bietet einige Optimierungen, um die Kaltstartzeit zu verkürzen, beispielsweise den Abruf von vorab aufgewärmten (betriebsbereiten) Platzhalterfunktionen, für die bereits der Funktionshost und die Sprachprozesse ausgeführt werden. |
| **[Premium-Plan](functions-premium-plan.md)** | Ständig betriebsbereite (warme) Instanzen, um jegliche Kaltstarts zu vermeiden. |
| **[Dedizierter Plan](dedicated-plan.md)** | In einem Dedicated-Plan kann der Functions-Host kontinuierlich ausgeführt werden, sodass ein Kaltstart kein Problem darstellt. |
| **[ASE](dedicated-plan.md)** | In einem Dedicated-Plan kann der Functions-Host kontinuierlich ausgeführt werden, sodass ein Kaltstart kein Problem darstellt. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Abhängig von der KEDA-Konfiguration können Apps so konfiguriert werden, dass ein Kaltstart vermieden wird. Wenn sie für die Skalierung auf null konfiguriert sind, tritt bei neuen Ereignissen ein Kaltstart auf. 

## <a name="service-limits"></a>Diensteinschränkungen

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Netzwerkfeatures

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Abrechnung

| Planen | Details |
| --- | --- |
| **[Verbrauchsplan](consumption-plan.md)** | Sie bezahlen nur für die Zeit, in der Ihre Funktionen ausgeführt werden. Die Abrechnung erfolgt auf der Grundlage der Anzahl von Ausführungen, der Ausführungszeit und des verwendeten Arbeitsspeichers. |
| **[Premium-Plan](functions-premium-plan.md)** | Die Abrechnung für den Premium-Plan basiert auf der Anzahl von Kernsekunden und dem für benötigte und vorab aufgewärmte Instanzen verwendeten Arbeitsspeicher. Pro Plan muss immer mindestens eine Instanz aufgewärmt sein. Dieser Plan bietet die am besten vorhersagbaren Preise. |
| **[Dedizierter Plan](dedicated-plan.md)** | Sie zahlen für Funktions-Apps in einem App Service-Plan das gleiche wie für andere App Service-Ressourcen, etwa Web-Apps.|
| **[App Service-Umgebung (ASE)](dedicated-plan.md)** | Es gibt eine monatliche Pauschalgebühr für eine ASE, mit der die Infrastruktur abgedeckt ist und die sich nicht mit der Größe der ASE ändert. Darüber hinaus fallen Kosten pro vCPU im App Service-Plan an. Alle in einer ASE gehosteten Apps befinden sich in der isolierten Preis-SKU. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Sie zahlen nur die Kosten für Ihren Kubernetes-Cluster, keine zusätzlichen Gebühren für Functions. Ihre Funktions-App wird als Anwendungsworkload in Ihrem Cluster ausgeführt, genau wie eine ganz normale App. |

## <a name="next-steps"></a>Nächste Schritte

+ [Bereitstellungstechnologien in Azure Functions](functions-deployment-technologies.md) 
+ [Azure Functions: Entwicklerhandbuch](functions-reference.md)

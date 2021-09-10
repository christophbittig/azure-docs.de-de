---
title: Netzwerkoptionen von Azure Functions
description: Enthält eine Übersicht über alle Netzwerkoptionen, die in Azure Functions verfügbar sind.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: b998f2876e323ad224d4ecb8afddd4c4f7d5f118
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346822"
---
# <a name="azure-functions-networking-options"></a>Netzwerkoptionen von Azure Functions

In diesem Artikel werden die Netzwerkfunktionen beschrieben, die für die Hostingoptionen von Azure Functions zur Verfügung stehen. Alle folgenden Netzwerkoptionen geben Ihnen die Möglichkeit zum Zugreifen auf Ressourcen ohne Verwendung von Adressen, die über das Internet geroutet werden können, oder zum Einschränken des Internetzugriffs auf eine Funktions-App.

Die Hostingmodelle weisen verschiedene verfügbare Ebenen der Netzwerkisolation auf. Durch Auswahl der jeweils richtigen Ebene lassen sich Ihre Anforderungen an die Netzwerkisolation erfüllen.

Sie können Funktions-Apps auf verschiedene Arten hosten:

* Sie können unter Tarifoptionen wählen, die in einer mehrinstanzenfähigen Infrastruktur ausgeführt werden und über unterschiedliche Ebenen von Konnektivitäts- und Skalierungsoptionen für virtuelle Netzwerke verfügen:
    * Im [Verbrauchstarif](consumption-plan.md) wird als Reaktion auf Last dynamisch skaliert, und der Tarif bietet Optionen für eine minimale Netzwerkisolation.
    * Im [Premium-Tarif](functions-premium-plan.md) wird ebenfalls dynamisch skaliert, der Tarif ermöglicht aber eine umfassendere Netzwerkisolation.
    * Beim [Azure App Service-Plan](dedicated-plan.md) wird eine feste Skalierung verwendet, und er weist eine ähnliche Netzwerkisolation wie der Premium-Tarif auf.
* Sie können Funktionen in einer [App Service-Umgebung](../app-service/environment/intro.md) ausführen. Mit dieser Methode werden die Funktionen in Ihrem virtuellen Netzwerk bereitgestellt und eine umfassende Netzwerksteuerung und -isolation ermöglicht.

## <a name="matrix-of-networking-features"></a>Matrix der Netzwerkfunktionen

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Einschränkungen für eingehenden Zugriff

Mit Zugriffseinschränkungen können Sie eine nach Priorität sortierte Liste mit IP-Adressen definieren, über die der Zugriff auf Ihre App zugelassen bzw. abgelehnt wird. Die Liste kann IPv4- und IPv6-Adressen oder bestimmte Subnetze eines virtuellen Netzwerks mit [Dienstendpunkten](#use-service-endpoints) enthalten. Wenn mindestens ein Eintrag vorhanden ist, enthält die Liste am Ende einen impliziten Eintrag vom Typ „Alle ablehnen“. IP-Einschränkungen können für alle Hostingoptionen für Funktionen verwendet werden.

Zugriffseinschränkungen sind für [Premium](functions-premium-plan.md), [Verbrauch](consumption-plan.md) und [App Service](dedicated-plan.md) verfügbar.

> [!NOTE]
> Mit den geltenden Netzwerkbeschränkungen können Sie nur in Ihrem virtuellen Netzwerk bereitstellen, oder wenn Sie die IP-Adresse des Computers, mit dem Sie auf das Azure-Portal zugreifen, der Liste der sicheren Empfänger hinzugefügt haben. Sie können die Funktion jedoch weiterhin über das Portal verwalten.

Weitere Informationen finden Sie unter [Azure App Service – statische Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md).

### <a name="use-service-endpoints"></a>Verwenden von Dienstendpunkten

Die Verwendung von Dienstendpunkten ermöglicht das Einschränken des Zugriffs auf ausgewählte Subnetze virtueller Azure-Netzwerke. Erstellen Sie zum Einschränken des Zugriffs auf ein bestimmtes Subnetz eine Einschränkungsregel vom Typ **Virtual Network**. Anschließend können Sie das Abonnement, das virtuelle Netzwerk und das Subnetz auswählen, für das Sie den Zugriff zulassen oder ablehnen möchten. 

Falls für das von Ihnen ausgewählte Subnetz nicht bereits Dienstendpunkte mit Microsoft.Web aktiviert sind, wird dies automatisch durchgeführt, sofern Sie nicht das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. In einem Szenario, bei dem Sie Dienstendpunkte in der App aktivieren möchten, aber nicht im Subnetz, hängt es vor allem davon ab, ob Sie über die Berechtigungen für die Aktivierung im Subnetz verfügen. 

Falls die Dienstendpunkte im Subnetz bei Ihnen von einer anderen Person aktiviert werden müssen, sollten Sie das Kontrollkästchen **Fehlende Microsoft.Web-Dienstendpunkte ignorieren** aktivieren. Ihre App wird für Dienstendpunkte konfiguriert, weil damit zu rechnen ist, dass diese später im Subnetz aktiviert werden. 

![Screenshot: Bereich „Add IP Restriction“ (IP-Einschränkung hinzufügen) mit Auswahl des Typs „Virtual Network“](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Sie können Dienstendpunkte nicht nutzen, um den Zugriff auf Apps einzuschränken, die in einer App Service-Umgebung ausgeführt werden. Wenn Ihre App in einer App Service-Umgebung enthalten ist, können Sie den Zugriff darauf steuern, indem Sie IP-Zugriffsregeln anwenden. 

Informationen zum Einrichten von Dienstendpunkten Sie unter [Tutorial: Einrichten von privatem Websitezugriff für Azure Functions](functions-create-private-site-access.md).

## <a name="private-endpoint-connections"></a>Private Endpunktverbindungen

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Konfigurieren Sie zum Aufrufen anderer Dienste, die über eine Verbindung mit einem privaten Endpunkt verfügen (z. B. Speicher oder Service Bus), Ihre App so, dass sie [ausgehende Aufrufe an private Endpunkte](#private-endpoints) ausführt.

## <a name="virtual-network-integration"></a>Integration in ein virtuelles Netzwerk

Durch die Integration des virtuellen Netzwerks kann die Funktions-App auf Ressourcen eines virtuellen Netzwerks zugreifen.
Azure Functions unterstützt zwei Arten der Integration virtueller Netzwerke:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Bei der Integration virtueller Netzwerke in Azure Functions wird die gemeinsame Infrastruktur mit App Service-Web-Apps verwendet. Weitere Informationen zu den beiden Arten der Integration virtueller Netzwerke finden Sie unter:

* [Regionale Integration des virtuellen Netzwerks](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integration des virtuellen Netzwerks mit erforderlichem Gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Informationen zum Einrichten der Integration virtueller Netzwerke finden Sie unter [Tutorial: Integrieren von Functions in ein virtuelles Azure-Netzwerk](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regionale Integration des virtuellen Netzwerks

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Herstellen einer Verbindung mit durch den Dienstendpunkt gesicherten Ressourcen

Um eine höhere Sicherheitsstufe zu gewährleisten, können Sie eine Reihe von Azure-Diensten auf ein virtuelles Netzwerk beschränken, indem Sie Dienstendpunkte verwenden. Sie müssen Ihre Funktions-App dann in dieses virtuelle Netzwerk integrieren, um auf die Ressource zugreifen zu können. Diese Konfiguration wird für alle [Pläne](functions-scale.md#networking-features) unterstützt, welche die Integration in ein virtuelles Netzwerk unterstützen.

Weitere Informationen finden Sie unter [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Einschränken Ihres Speicherkontos auf ein virtuelles Netzwerk 

Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Queue- und Table Storage unterstützt. Sie können dieses Speicherkonto durch eines ersetzen, das mit Dienstendpunkten oder einem privaten Endpunkt geschützt ist. 

Dieses Feature wird für alle von Windows Virtual Network unterstützten SKUs im Plan „Dedicated“ (App Service) und für die Premium-Pläne unterstützt. Es wird ebenfalls mit privatem DNS für SKUs unterstützt, die Linux-VNets unterstützen. Der Verbrauchstarif und benutzerdefiniertes DNS für Linux-Pläne werden nicht unterstützt. Informationen zum Einrichten einer Funktion mit einem Speicherkonto, das auf ein privates Netzwerk beschränkt ist, finden Sie unter [Einschränken des Speicher Kontos für ein virtuelles Netzwerk](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network).

## <a name="use-key-vault-references"></a>Verwenden von Key Vault-Verweisen

Sie können mithilfe von Azure Key Vault-Verweisen Geheimnisse aus Ihrer Azure Key Vault-Instanz in Ihrer Azure Functions-Anwendung verwenden, ohne dass Codeänderungen erforderlich sind. Azure Key Vault ist ein Dienst, der eine zentralisierte Verwaltung von Geheimnissen mit voller Kontrolle über Zugriffsrichtlinien und Überprüfungsverlauf ermöglicht.

Wenn für die App die VNet-Integration konfiguriert ist, können [Key Vault-Verweise](../app-service/app-service-key-vault-references.md) zum Abrufen von Geheimnissen aus einem Tresor mit Netzwerkeinschränkung verwendet werden.

## <a name="virtual-network-triggers-non-http"></a>Trigger für virtuelle Netzwerke (nicht HTTP)

HTTP-fremde Triggerfunktionen können aktuell auf zwei Arten von einem virtuellen Netzwerk aus verwendet werden:

+ Sie können Ihre Funktions-App in einem Premium-Plan ausführen und die Triggerunterstützung für virtuelle Netzwerke aktivieren.
+ Sie können Ihre Funktions-App in einem App Service-Plan oder in einer App Service-Umgebung ausführen.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium-Plan mit Triggern für virtuelle Netzwerke

Bei Verwendung eines Premium-Plans können Sie HTTP-fremde Triggerfunktionen mit Diensten verbinden, die innerhalb eines virtuellen Netzwerks ausgeführt werden. Hierzu müssen Sie für Ihre Funktions-App die Triggerunterstützung für virtuelle Netzwerke aktivieren. Die Einstellung **Runtime Scale Monitoring** (Überwachung der Runtimeskalierung) finden Sie im [Azure-Portal](https://portal.azure.com) unter **Konfiguration** > **Einstellungen der Funktionsruntime**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Trigger für virtuelle Netzwerke können auch mithilfe des folgenden Azure CLI-Befehls aktiviert werden:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Das Aktivieren von Triggern für virtuelle Netzwerke kann sich auf die Leistung Ihrer Anwendung auswirken, da die Instanzen Ihres App Service-Plans Ihre Trigger überwachen müssen, um den Zeitpunkt einer erforderlichen Skalierung zu bestimmen. Diese Auswirkung ist wahrscheinlich nur geringfügig.

Trigger für virtuelle Netzwerke werden ab Version 2.x der Functions-Runtime unterstützt. Folgende HTTP-fremde Triggertypen werden unterstützt:

| Durchwahl | Mindestversion |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 oder höher |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 oder höher|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 oder höher|

> [!IMPORTANT]
> Wenn Sie die Triggerunterstützung für virtuelle Netzwerke aktivieren, werden nur die in der obigen Tabelle aufgeführten Triggertypen dynamisch mit Ihrer Anwendung skaliert. Sie können zwar weiterhin Trigger verwenden, die nicht in der Tabelle aufgeführt sind, diese werden jedoch nicht über die Anzahl vorab aufgewärmter Instanzen hinaus skaliert. Die vollständige Triggerliste finden Sie unter [Trigger und Bindungen](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service-Plan und App Service-Umgebung mit Triggern für virtuelle Netzwerke

Wenn Ihre Funktions-App entweder in einem App Service-Plan oder in einer App Service-Umgebung ausgeführt wird, können Sie HTTP-fremde Triggerfunktionen verwenden. Damit Ihre Funktionen ordnungsgemäß ausgelöst werden, muss eine Verbindung mit einem virtuellen Netzwerk bestehen. Außerdem muss auf die in der Triggerverbindung definierte Ressource zugegriffen werden können.

Angenommen, Sie möchten Azure Cosmos DB so konfigurieren, dass nur Datenverkehr aus einem virtuellen Netzwerk akzeptiert wird. In diesem Fall müssen Sie Ihre Funktions-App in einem App Service-Plan bereitstellen, der die VNET-Integration mit diesem virtuellen Netzwerk ermöglicht. Die Integration ermöglicht das Auslösen einer Funktion durch diese Azure Cosmos DB-Ressource.

## <a name="hybrid-connections"></a>Hybridverbindungen

[Hybrid Connections](../azure-relay/relay-hybrid-connections-protocol.md) ist ein Feature von Azure Relay, das Sie zum Zugreifen auf Anwendungsressourcen in anderen Netzwerken verwenden können. Es ermöglicht den Zugriff von Ihrer App auf einen Anwendungsendpunkt. Sie können es nicht verwenden, um auf Ihre Anwendung zuzugreifen. Hybrid Connections steht für Funktionen unter Windows in allen Tarifen (mit Ausnahme des Verbrauchstarifs) zur Verfügung.

Bei der Verwendung in Azure Functions entspricht jede Hybridverbindung einer Kombination aus einem einzelnen TCP-Host und einem Port. Dies bedeutet, dass sich der Hybridverbindungsendpunkt in einem beliebigen Betriebssystem und einer beliebigen Anwendung befinden kann, solange der Zugriff über einen TCP-Überwachungsport erfolgt. Das Feature „Hybrid Connections“ verfügt nicht über Informationen zum Anwendungsprotokoll oder zum abzurufenden Inhalt und benötigt diese Informationen auch nicht. Es ermöglicht lediglich den Netzwerkzugriff.

Weitere Informationen finden Sie in der [App Service-Dokumentation zu Hybrid Connections](../app-service/app-service-hybrid-connections.md). Diese Konfigurationsschritte unterstützen auch Azure Functions.

>[!IMPORTANT]
> Hybrid Connections wird nur in Windows-Tarifen unterstützt. Linux wird nicht unterstützt.

## <a name="outbound-ip-restrictions"></a>IP-Einschränkungen für ausgehenden Datenverkehr

IP-Einschränkungen für ausgehenden Datenverkehr sind in einem Premium-Plan, einem App Service-Plan oder in einer App Service-Umgebung verfügbar. Sie können ausgehende Einschränkungen für das virtuelle Netzwerk konfigurieren, wo Ihre App Service-Umgebung bereitgestellt wird.

Wenn Sie eine Funktions-App in einen Premium-Tarif oder einen App Service-Plan mit einem virtuellen Netzwerk integrieren, kann die App standardmäßig weiterhin ausgehende Aufrufe ins Internet vornehmen. Durch Hinzufügen der Anwendungseinstellung `WEBSITE_VNET_ROUTE_ALL=1` erzwingen Sie, dass der gesamte ausgehende Datenverkehr an das virtuelle Netzwerk gesendet wird. Dort können Netzwerksicherheitsgruppen-Regeln verwendet werden, um den Datenverkehr einzuschränken.

Informationen zum Steuern der ausgehenden IP-Adresse mithilfe eines virtuellen Netzwerks finden Sie unter [Tutorial: Steuern der ausgehenden IP-Adresse von Azure Functions mit einem Azure Virtual Network-NAT-Gateway](functions-how-to-use-nat-gateway.md). 

## <a name="automation"></a>Automation
Die folgenden APIs ermöglichen es Ihnen, regionale Integrationen virtueller Netzwerke programmgesteuert zu verwalten:

+ **Azure CLI**: Verwenden Sie die Befehle [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration), um Integrationen regionaler virtueller Netzwerk hinzuzufügen, aufzulisten oder zu entfernen.  
+ **ARM-Vorlagen**: Die Integration regionaler virtueller Netzwerke kann mithilfe einer Azure Resource Manager-Vorlage aktiviert werden. Ein vollständiges Beispiel finden Sie in [dieser Functions-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Netzwerk und zu Azure Functions:

* [Tutorial zum Einstieg in die Integration des virtuellen Netzwerks](./functions-create-vnet.md)
* [Häufig gestellte Fragen zu Netzwerken in Functions](./functions-networking-faq.yml)
* [Weitere Informationen zur Integration des virtuellen Netzwerks mit App Service und Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Weitere Informationen zu virtuellen Netzwerken in Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivieren weiterer Netzwerkfunktionen und Steuerung mit App Service-Umgebungen](../app-service/environment/intro.md)
* [Verbinden mit einzelnen lokalen Ressourcen ohne Änderungen an der Firewall mithilfe von Hybrid Connections](../app-service/app-service-hybrid-connections.md)

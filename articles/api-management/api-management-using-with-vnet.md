---
title: Herstellen einer Verbindung mit einem virtuellen Netzwerk mit Azure API Management
description: Hier erfahren Sie, wie Sie eine Verbindung mit einem virtuellen Netzwerk in Azure API Management einrichten und darüber auf Webdienste zugreifen.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 6eb89c069cb8ce1017b84f5c886231ae767a25a8
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537414"
---
# <a name="connect-to-a-virtual-network-using-azure-api-management"></a>Herstellen einer Verbindung mit einem virtuellen Netzwerk mit Azure API Management

Azure API Management kann in einem virtuellen Azure-Netzwerk (VNet) bereitgestellt werden, damit der Dienst auf Back-End-Dienste im Netzwerk zugreifen kann. Informationen zu VNet-Konnektivitätsoptionen, Anforderungen und Überlegungen finden Sie unter [Verwenden eines virtuellen Netzwerks mit Azure API Management](virtual-network-concepts.md).

In diesem Artikel wird erläutert, wie Sie VNet-Konnektivität für Ihre API Management-Instanz im *externen* Modus einrichten, in dem das Entwicklerportal, das API-Gateway und andere API Management-Endpunkte über das öffentliche Internet zugänglich sind. Spezifische Konfigurationen für den *internen* Modus, bei denen die Endpunkte nur innerhalb des VNet zugänglich sind, finden Sie unter [Verwenden von Azure API Management mit einem internen virtuellen Netzwerk](./api-management-using-with-internal-vnet.md).

:::image type="content" source="media/api-management-using-with-vnet/api-management-vnet-external.png" alt-text="Herstellen einer Verbindung mit dem externen VNet":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

Einige Voraussetzungen unterscheiden sich abhängig von der Version (`stv2` oder `stv1`) der [Computeplattform](compute-infrastructure.md) für das Hosting Ihrer API Management-Instanz.

> [!TIP]
> Wenn Sie das Portal verwenden, um die Netzwerkkonfiguration Ihrer API Management-Instanz zu erstellen oder zu aktualisieren, wird die Instanz auf der `stv2` Computeplattform gehostet.

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **Eine API Management-Instanz.** Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

* **Ein virtuelles Netzwerk und ein Subnetz** müssen sich in derselben Region und in demselben Abonnement befinden wie Ihre API Management-Instanz. Das Subnetz kann andere Azure-Ressourcen enthalten.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **Eine API Management-Instanz.** Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

* **Ein virtuelles Netzwerk und ein Subnetz** müssen sich in derselben Region und in demselben Abonnement befinden wie Ihre API Management-Instanz.

    Das Subnetz muss dediziert für API Management-Instanzen verwendet werden. Beim Versuch, eine Azure API Management-Instanz in einem Subnetz eines Ressourcen-Manager-VNet bereitzustellen, das andere Ressourcen enthält, tritt ein Fehler bei der Bereitstellung auf.

---

## <a name="enable-vnet-connection"></a>VNet-Verbindung Aktivieren

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-compute-platform"></a>Aktivieren von VNet-Konnektivität über das Azure-Portal (`stv2` Computeplattform)

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre API Management-Instanz zu suchen. Suchen Sie die **API Management-Dienste**, und wählen Sie sie aus.
1. Wählen Sie Ihre API Management-Instanz aus.

1. Wählen Sie **Virtuelles Netzwerk** aus.
1. Wählen Sie den **externen** Zugriffstyp aus.
    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Auswählen eines VNet im Azure-Portal":::

1. In der Liste der Standorte (Regionen), an denen Ihr API Management-Dienst bereitgestellt wird: 
    1. Wählen Sie einen **Standort** aus.
    1. Wählen Sie **Virtuelles Netzwerk**, **Subnetz** und **IP-Adresse** aus. 
    * Die Liste der VNets enthält Resource Manager-VNets, die in Ihrem Azure-Abonnement zur Verfügung stehen und in der Region eingerichtet sind, die Sie konfigurieren.

        :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="VNet-Einstellungen im Portal":::

1. Wählen Sie **Übernehmen**. Die Seite **Virtuelles Netzwerk** Ihrer API Management-Instanz wird mit Ihren neuen Optionen für das VNet und Subnetz aktualisiert.

1. Fahren Sie mit dem Konfigurieren der VNet-Einstellungen für die restlichen Standorte Ihrer API Management-Instanz fort.

7. Wählen Sie auf der oberen Navigationsleiste **Speichern** und dann **Netzwerkkonfiguration anwenden** aus.

    Es kann zwischen 15 und 45 Minuten dauern, bis die API Management-Instanz aktualisiert wurde.

### <a name="enable-connectivity-using-a-resource-manager-template"></a>Aktivieren von Konnektivität über eine Resource Manager-Vorlage

Verwenden Sie die folgenden Vorlagen, um eine API Management-Instanz bereitzustellen und eine Verbindung mit einem VNet herzustellen. Die Vorlagen unterscheiden sich abhängig von der Version (`stv2` oder `stv1`) der [Computeplattform](compute-infrastructure.md) für das Hosting Ihrer API Management-Instanz.

### <a name="stv2"></a>[stv2](#tab/stv2)

#### <a name="api-version-2021-01-01-preview"></a>API-Version 2021-01-01-preview 

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet-publicip)

     [![Bereitstellung in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="stv1"></a>[stv1](#tab/stv1)

#### <a name="api-version-2020-12-01"></a>API-Version 2020-12-01

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet)

     [![Bereitstellung in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets"></a>Aktivieren von Konnektivität über Azure PowerShell-Cmdlets 

[Erstellen](/powershell/module/az.apimanagement/new-azapimanagement) oder [aktualisieren](/powershell/module/az.apimanagement/update-azapimanagementregion) Sie eine API Management-Instanz in einem VNet.

---

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a>Herstellen einer Verbindung mit einem innerhalb eines virtuellen Netzwerk gehosteten Webdienst
Nachdem Sie Ihren API Management-Dienst mit dem VNet verbunden haben, können Sie auf Back-End-Dienste innerhalb des VNet auf die gleiche Weise zugreifen wie auf öffentliche Dienste. Geben Sie beim Erstellen oder Bearbeiten einer API die lokale IP-Adresse oder den Hostnamen Ihres Webdiensts (falls ein DNS-Server für das VNet konfiguriert wurde) im Feld **Webdienst-URL** ein.

:::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-add-api.png" alt-text="API über VNet hinzufügen":::

## <a name="network-configuration"></a>Netzwerkkonfiguration
Weitere Netzwerkkonfigurationseinstellungen sind in den folgenden Abschnitten enthalten. 

In den Einstellungen werden gängige Konfigurationsprobleme aufgelistet, die beim Bereitstellen des API Management-Diensts in einem VNet auftreten können.

### <a name="custom-dns-server-setup"></a>Setup eines benutzerdefinierten DNS-Servers 
Im externen VNet-Modus verwaltet Azure das DNS standardmäßig. Der API Management Dienst ist von mehreren Azure-Diensten abhängig. Wenn API Management in einem VNet mit einem benutzerdefiniertem DNS-Server gehostet wird, muss es die Hostnamen dieser Azure-Dienste auflösen können.  
* Eine Anleitung zum Einrichten eines benutzerdefinierten DNS-Servers finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).  
* Referenzinformationen finden Sie in der [erforderlichen Ports](#required-ports) und in den Netzwerkanforderungen.

> [!IMPORTANT]
> Wenn Sie benutzerdefinierte DNS-Server für das VNet verwenden möchten, richten Sie diesen **vor** dem Bereitstellen eines API Management-Diensts ein. Andernfalls müssen Sie den API Management-Dienst jedes Mal aktualisieren, wenn Sie die DNS-Server durch Ausführen des [Vorgangs „Netzwerkkonfiguration anwenden“](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates) ändern.

### <a name="required-ports"></a>Erforderliche Ports  

Ein- und ausgehender Datenverkehr im Subnetz, in dem API Management bereitgestellt wurde, kann mithilfe von [Netzwerksicherheitsgruppen][NetworkSecurityGroups] gesteuert werden. Wenn einer der folgenden Ports nicht verfügbar ist, funktioniert API Management möglicherweise nicht ordnungsgemäß und kann möglicherweise nicht mehr aufgerufen werden. 

Beim Hosten einer API Management-Dienstinstanz in einem VNET werden die in der folgenden Tabelle angegebenen Ports verwendet. Manche Anforderungen unterscheiden sich abhängig von der Version (`stv2` oder `stv1`) der [Computeplattform](compute-infrastructure.md) für das Hosting Ihrer API Management-Instanz.

>[!IMPORTANT]
> Fett markierte Elemente in der Spalte *Zweck* sind zur erfolgreichen Bereitstellung des API Management-Diensts erforderlich. Die Blockierung der anderen Ports **beeinträchtigt** jedoch die Möglichkeit, den **ausgeführten Dienst zu verwenden und zu überwachen sowie die verbindliche SLA bereitzustellen**.

#### <a name="stv2"></a>[stv2](#tab/stv2)

| Quell-/Zielport(s) | Direction          | Transportprotokoll |   [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Quelle/Ziel   | Zweck (\*)                                                 | VNet-Typ |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Eingehend            | TCP                | INTERNET/VIRTUAL_NETWORK            | Kommunikation zwischen Clients und API Management                      | Extern             |
| */3443                     | Eingehend            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Verwaltungsendpunkt für Azure-Portal und PowerShell         | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | **Abhängigkeit von Azure Storage**                             | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) und Azure Key Vault-Abhängigkeit                  | Extern & Intern  |
| * / 1433                     | Ausgehend           | TCP                | VIRTUAL_NETWORK/SQL                 | **Zugriff auf Azure SQL-Endpunkte**                           | Extern & Intern  |
| * / 443                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                | **Zugriff auf Azure Key Vault**                         | Extern & Intern  |
| * / 5671, 5672, 443          | Ausgehend           | TCP                | VIRTUAL_NETWORK/Event Hub            | Abhängigkeit für [Richtlinie zum Anmelden bei Event Hub](api-management-howto-log-event-hubs.md) und Überwachungs-Agent | Extern & Intern  |
| */445                      | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | Abhängigkeit von Azure-Dateifreigabe für [GIT](api-management-configuration-repository-git.md)                      | Extern & Intern  |
| * / 443, 12000                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Erweiterung für Integrität und Überwachung         | Extern & Intern  |
| * / 1886, 443                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Veröffentlichen von [Diagnoseprotokollen und Metriken](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) und [Application Insights](api-management-howto-app-insights.md)                   | Extern & Intern  |
| * / 25, 587, 25028                       | Ausgehend           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinden mit SMTP-Relay zum Senden von E-Mails                    | Extern & Intern  |
| * / 6381 - 6383              | Ein- und ausgehend | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Zugreifen auf den Redis-Dienst für [Cache](api-management-caching-policies.md)richtlinien zwischen Computern         | Extern & Intern  |
| * / 4290              | Ein- und ausgehend | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synchronisieren von Zählern für [Ratenbegrenzung](api-management-access-restriction-policies.md#LimitCallRateByKey)srichtlinien zwischen Computern         | Extern & Intern  |
| * / 6390                       | Eingehend            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Lastenausgleich von Azure-Infrastruktur                          | Extern & Intern  |

#### <a name="stv1"></a>[stv1](#tab/stv1)

| Quell-/Zielport(s) | Direction          | Transportprotokoll |   [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Quelle/Ziel   | Zweck (\*)                                                 | VNet-Typ |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Eingehend            | TCP                | INTERNET/VIRTUAL_NETWORK            | Kommunikation zwischen Clients und API Management                      | Extern             |
| */3443                     | Eingehend            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Verwaltungsendpunkt für Azure-Portal und PowerShell         | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | **Abhängigkeit von Azure Storage**                             | Extern & Intern  |
| * / 443                  | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md)-Abhängigkeit                  | Extern & Intern  |
| * / 1433                     | Ausgehend           | TCP                | VIRTUAL_NETWORK/SQL                 | **Zugriff auf Azure SQL-Endpunkte**                           | Extern & Intern  |
| * / 5671, 5672, 443          | Ausgehend           | TCP                | VIRTUAL_NETWORK/Event Hub            | Abhängigkeit für [Richtlinie zum Anmelden bei Event Hub](api-management-howto-log-event-hubs.md) und Überwachungs-Agent | Extern & Intern  |
| */445                      | Ausgehend           | TCP                | VIRTUAL_NETWORK/Storage             | Abhängigkeit von Azure-Dateifreigabe für [GIT](api-management-configuration-repository-git.md)                      | Extern & Intern  |
| * / 443, 12000                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Erweiterung für Integrität und Überwachung         | Extern & Intern  |
| * / 1886, 443                     | Ausgehend           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Veröffentlichen von [Diagnoseprotokollen und Metriken](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) und [Application Insights](api-management-howto-app-insights.md)                   | Extern & Intern  |
| * / 25, 587, 25028                       | Ausgehend           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinden mit SMTP-Relay zum Senden von E-Mails                    | Extern & Intern  |
| * / 6381 - 6383              | Ein- und ausgehend | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Zugreifen auf den Redis-Dienst für [Cache](api-management-caching-policies.md)richtlinien zwischen Computern         | Extern & Intern  |
| * / 4290              | Ein- und ausgehend | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synchronisieren von Zählern für [Ratenbegrenzung](api-management-access-restriction-policies.md#LimitCallRateByKey)srichtlinien zwischen Computern         | Extern & Intern  |
| * / *                         | Eingehend            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Lastenausgleich von Azure-Infrastruktur                          | Extern & Intern  |

---

### <a name="tls-functionality"></a>TLS-Funktionalität  
  Um die Erstellung und Überprüfung der TLS/SSL-Zertifikatkette zu ermöglichen, benötigt der API Management-Dienst ausgehende Netzwerkkonnektivität mit `ocsp.msocsp.com`, `mscrl.microsoft.com` und `crl.microsoft.com`. Diese Abhängigkeit ist nicht erforderlich, wenn Zertifikate, die Sie in API Management hochladen, die vollständige Kette zum Stamm der Zertifizierungsstelle enthalten.

### <a name="dns-access"></a>DNS-Zugriff
  Ausgehender Zugriff über `port 53` wird für die Kommunikation mit DNS-Servern benötigt. Wenn ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist, muss der DNS-Server über das Subnetz, in der sich API Management befindet, erreichbar sein.

### <a name="metrics-and-health-monitoring"></a>Metriken und Systemüberwachung 

Ausgehende Netzwerkkonnektivität mit Azure Monitoring-Endpunkten, die unter den folgenden Domänen aufgelöst werden, werden unter dem AzureMonitor-Diensttag für die Verwendung mit Netzwerksicherheitsgruppen dargestellt.

|     Azure-Umgebung | Endpunkte                                                                                                                                                                                                                                                                                                                                           |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure – Öffentlich      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
| Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
| Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |
  
### <a name="regional-service-tags"></a>Regionale Diensttags

NSG-Regeln, die ausgehende Verbindungen mit den Diensttags von Storage, SQL und Event Hubs zulassen, können die regionalen Versionen der Tags verwenden, die der Region entsprechen, die die API Management-Instanz enthält (z. B. Storage.WestUS für eine API Management-Instanz in der Region „USA, Westen“). In Bereitstellungen mit mehreren Regionen sollte die NSG in den einzelnen Regionen Datenverkehr zu den Diensttags für diese Region und die primäre Region zulassen.

> [!IMPORTANT]
> Ermöglichen Sie die Veröffentlichung des [Entwicklerportals](api-management-howto-developer-portal.md) für eine API Management-Instanz in einem VNet, indem Sie ausgehende Verbindungen mit Blobspeicher in der Region „USA, Westen“ zulassen. Verwenden Sie beispielsweise das Diensttag **Storage.WestUS** in einer Netzwerksicherheitsgruppen-Regel. Die Verbindung mit Blobspeicher in der Region „USA, Westen“ ist zurzeit erforderlich, um das Entwicklerportal für eine beliebige API Management-Instanz zu veröffentlichen.

### <a name="smtp-relay"></a>SMTP-Relay  
  
Ausgehende Netzwerkverbindungen für das SMTP-Relay erlauben. Die Auflösung erfolgt unter den Hosts `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` und `ies.global.microsoft.com`.

> [!NOTE]
> Nur das in API Management bereitgestellte SMTP-Relay kann zum Senden von E-Mails von Ihrer Instanz verwendet werden.

### <a name="developer-portal-captcha"></a>CAPTCHA des Entwicklerportals 
Ausgehende Netzwerkverbindungen für das CAPTCHA des Entwicklerportals erlauben. Die Auflösung erfolgt unter den Hosts `client.hip.live.com` und `partner.hip.live.com`.

### <a name="azure-portal-diagnostics"></a>Diagnose im Azure-Portal  
  Wenn Sie die API Management-Erweiterung in einem VNet verwenden, wird ausgehender Zugriff auf `dc.services.visualstudio.com` an `port 443` benötigt, um die Übermittlung von Diagnoseprotokollen aus dem Azure-Portal zu ermöglichen. Dieser Zugriff ermöglicht die Behandlung von Problemen, die bei der Verwendung von Erweiterungen auftreten können.

### <a name="azure-load-balancer"></a>Azure Load Balancer  
  Sie müssen keine eingehenden Anforderungen vom Diensttag `AZURE_LOAD_BALANCER` für die SKU `Developer` zulassen, da hinter ihr nur eine Compute-Einheit bereitgestellt wird. Eingehender Datenverkehr von [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) wird jedoch bei der Skalierung auf eine höhere SKU (etwa `Premium`) kritisch, da ein Fehler beim Integritätstest des Lastenausgleichs dann dazu führt, dass die Bereitstellung nicht erfolgreich ist.

### <a name="application-insights"></a>Application Insights  
  Wenn Sie [Azure Application Insights](api-management-howto-app-insights.md) für die Überwachung von API Management aktiviert haben, lassen Sie ausgehende Verbindungen mit dem [Telemetrieendpunkt](../azure-monitor/app/ip-addresses.md#outgoing-ports) des VNet zu.

### <a name="force-tunneling-traffic-to-on-premises-firewall-using-expressroute-or-network-virtual-appliance"></a>Tunnelerzwingung für Datenverkehr zur lokalen Firewall per Express Route oder virtueller Netzwerkappliance  
  Im Allgemeinen konfigurieren und definieren Sie eine eigene Standardroute (0.0.0.0/0). Dadurch fließt der gesamte Datenverkehr aus dem per API Management delegierten Subnetz über eine lokale Firewall oder an ein virtuelles Netzwerkgerät. Bei diesem Datenverkehrsfluss funktioniert die Verbindung mit Azure API Management nicht mehr, da ausgehender Datenverkehr entweder lokal blockiert oder mittels NAT in eine nicht mehr nachvollziehbare Gruppe von Adressen übersetzt wird, die nicht mehr mit verschiedenen Azure-Endpunkten funktionieren. Für die Behebung dieses Problems gibt es mehrere Methoden: 

  * Aktivieren Sie [Dienstendpunkte][ServiceEndpoints] in dem Subnetz, in dem der API Management-Dienst für Folgendes bereitgestellt wird:
      * Azure SQL
      * Azure Storage
      * Azure Event Hub
      * Azure Key Vault (v2-Plattform) 
  
     Indem Sie Endpunkte direkt über das per API Management-Subnetz für diese Dienste aktivieren, können Sie das Microsoft Azure-Backbonenetzwerk verwenden, das optimales Routing für Dienstdatenverkehr ermöglicht. Bei Verwendung von Dienstendpunkten mit API Management mit Tunnelerzwingung erfolgt für den Datenverkehr der obigen Azure-Dienste keine Tunnelerzwingung. Für den übrigen Datenverkehr der API Management-Dienstabhängigkeiten wird die Tunnelerzwingung genutzt, und er darf nicht verloren gehen. Geht er verloren, funktioniert der API Management-Dienst nicht ordnungsgemäß.

  * Der gesamte Datenverkehr auf Steuerungsebene, der aus dem Internet an den Verwaltungsendpunkt Ihres API Management-Diensts fließt, wird über einen spezifischen Satz mit IP-Adressen für eingehenden Datenverkehr geleitet, die von API Management gehostet werden. Wenn für den Datenverkehr die Tunnelerzwingung verwendet wird, werden die Antworten nicht symmetrisch wieder diesen IP-Quelladressen für eingehenden Datenverkehr zugeordnet. Um diese Einschränkung zu umgehen, legen Sie das Ziel der folgenden benutzerdefinierten Routen ([UDRs][UDRs]) auf „Internet“ fest, um den Datenverkehr zurück an Azure zu leiten. Die IP-Adressen für eingehenden Datenverkehr auf Steuerungsebene werden im Artikel [IP-Adressen der Steuerungsebene](#control-plane-ip-addresses) aufgeführt.

  * Lösen Sie für die anderen API Management-Dienstabhängigkeiten, für die Tunnelerzwingung genutzt wird, den Hostnamen auf, und stellen Sie eine Verbindung mit dem Endpunkt her. Dazu zählen unter anderem folgende Einstellungen:
      - Metriken und Systemüberwachung
      - Diagnose im Azure-Portal
      - SMTP-Relay
      - CAPTCHA des Entwicklerportals

## <a name="routing"></a>Routing

+ Eine öffentliche IP-Adresse mit Lastenausgleich (VIP) ist reserviert, um Zugriff auf alle Dienstendpunkte und Ressourcen außerhalb des VNet zu ermöglichen.
  + Öffentliche IP-Adressen mit Lastenausgleich finden Sie auf dem Blatt **Übersicht/Essentials** im Azure-Portal.
+ Eine IP-Adresse aus einem Subnetz-IP-Bereich (DIP) wird für den Zugriff auf Ressourcen innerhalb des VNet verwendet.

> [!NOTE]
> Die VIP-Adressen der API Management-Instanz ändern sich in folgenden Fällen:
> * Das VNet wird aktiviert oder deaktiviert. 
> * API Management wird vom **externen** in den **internen** VNet-Modus oder umgekehrt verschoben.
> * Einstellungen zur [Zonenredundanz](zone-redundancy.md) werden an einem Standort für Ihre Instanz aktiviert, aktualisiert oder deaktiviert (nur Premium-SKU).

## <a name="control-plane-ip-addresses"></a>IP-Adressen der Steuerungsebene

Die folgenden IP-Adressen werden nach **Azure-Umgebung** unterteilt. Wenn eingehende Anforderungen zugelassen werden, müssen mit **Global** markierte IP-Adressen zusammen mit der **regionsspezifischen** IP-Adresse zugelassen werden.

| **Azure-Umgebung**|   **Region**|  **IP-Adresse**|
|-----------------|-------------------------|---------------|
| Azure – Öffentlich| USA, Süden-Mitte (Global)| 104.214.19.224|
| Azure – Öffentlich| USA, Norden-Mitte (Global)| 52.162.110.80|
| Azure – Öffentlich| Australien, Mitte| 20.37.52.67|
| Azure – Öffentlich| Australien, Mitte 2| 20.39.99.81|
| Azure – Öffentlich| Australien (Osten)| 20.40.125.155|
| Azure – Öffentlich| Australien, Südosten| 20.40.160.107|
| Azure – Öffentlich| Brasilien Süd| 191.233.24.179|
| Azure – Öffentlich| Brasilien, Südosten| 191.232.18.181|
| Azure – Öffentlich| Kanada, Mitte| 52.139.20.34|
| Azure – Öffentlich| Kanada, Osten| 52.139.80.117|
| Azure – Öffentlich| Indien, Mitte| 13.71.49.1|
| Azure – Öffentlich| USA (Mitte)| 13.86.102.66|
| Azure – Öffentlich| USA, Mitte (EUAP)| 52.253.159.160|
| Azure – Öffentlich| Asien, Osten| 52.139.152.27|
| Azure – Öffentlich| East US| 52.224.186.99|
| Azure – Öffentlich| USA (Ost) 2| 20.44.72.3|
| Azure – Öffentlich| USA, Osten 2 (EUAP)| 52.253.229.253|
| Azure – Öffentlich| Frankreich, Mitte| 40.66.60.111|
| Azure – Öffentlich| Frankreich, Süden| 20.39.80.2|
| Azure – Öffentlich| Deutschland, Norden| 51.116.0.0|
| Azure – Öffentlich| Deutschland, Westen-Mitte| 51.116.96.0|
| Azure – Öffentlich| Japan, Osten| 52.140.238.179|
| Azure – Öffentlich| Japan, Westen| 40.81.185.8|
| Azure – Öffentlich| Jio Indien, Mitte| 20.192.234.160|
| Azure – Öffentlich| Jio Indien, Westen| 20.193.202.160|
| Azure – Öffentlich| Korea, Mitte| 40.82.157.167|
| Azure – Öffentlich| Korea, Süden| 40.80.232.185|
| Azure – Öffentlich| USA Nord Mitte| 40.81.47.216|
| Azure – Öffentlich| Nordeuropa| 52.142.95.35|
| Azure – Öffentlich| Norwegen, Osten| 51.120.2.185|
| Azure – Öffentlich| Norwegen, Westen| 51.120.130.134|
| Azure – Öffentlich| Südafrika, Norden| 102.133.130.197|
| Azure – Öffentlich| Südafrika, Westen| 102.133.0.79|
| Azure – Öffentlich| USA Süd Mitte| 20.188.77.119|
| Azure – Öffentlich| Indien (Süden)| 20.44.33.246|
| Azure – Öffentlich| Asien, Südosten| 40.90.185.46|
| Azure – Öffentlich| Schweiz, Norden| 51.107.0.91|
| Azure – Öffentlich| Schweiz, Westen| 51.107.96.8|
| Azure – Öffentlich| VAE, Mitte| 20.37.81.41|
| Azure – Öffentlich| Vereinigte Arabische Emirate, Norden| 20.46.144.85|
| Azure – Öffentlich| UK, Süden| 51.145.56.125|
| Azure – Öffentlich| UK, Westen| 51.137.136.0|
| Azure – Öffentlich| USA, Westen-Mitte| 52.253.135.58|
| Azure – Öffentlich| Europa, Westen| 51.145.179.78|
| Azure – Öffentlich| Indien, Westen| 40.81.89.24|
| Azure – Öffentlich| USA (Westen)| 13.64.39.16|
| Azure – Öffentlich| USA, Westen 2| 51.143.127.203|
| Azure – Öffentlich| USA, Westen 3| 20.150.167.160|
| Azure China 21Vianet| China, Norden (Global)| 139.217.51.16|
| Azure China 21Vianet| China, Osten (Global)| 139.217.171.176|
| Azure China 21Vianet| China, Norden| 40.125.137.220|
| Azure China 21Vianet| China, Osten| 40.126.120.30|
| Azure China 21Vianet| China, Norden 2| 40.73.41.178|
| Azure China 21Vianet| China, Osten 2| 40.73.104.4|
| Azure Government| US Government, Virginia (Global)| 52.127.42.160|
| Azure Government| US Government, Texas (Global)| 52.127.34.192|
| Azure Government| US Government, Virginia| 52.227.222.92|
| Azure Government| US Government, Iowa| 13.73.72.21|
| Azure Government| US Gov Arizona| 52.244.32.39|
| Azure Government| US Gov Texas| 52.243.154.118|
| Azure Government| USDoD, Mitte| 52.182.32.132|
| Azure Government| USDoD, Osten| 52.181.32.192|

## <a name="troubleshooting"></a>Problembehandlung
* **Nicht erfolgreiche Erstbereitstellung des API Management-Diensts in einem Subnetz** 
  * Stellen Sie einen virtuellen Computer im selben Subnetz bereit. 
  * Stellen Sie eine Verbindung mit dem virtuellen Computer her, und überprüfen Sie die Konnektivität mit den folgenden Ressourcen in Ihrem Azure-Abonnement:
    * Azure Storage-Blob
    * Azure SQL-Datenbank
    * Azure Storage-Tabelle
    * Azure Key Vault (für eine API Management-Instanz, die auf der [`stv2` Plattform](compute-infrastructure.md) gehostet wird)

  > [!IMPORTANT]
  > Entfernen Sie nach der Konnektivitätsüberprüfung alle Ressourcen im Subnetz, bevor Sie API Management im Subnetz bereitstellen (erforderlich, wenn API Management auf der Plattform `stv1` gehostet wird).

* **Überprüfen des Netzwerkkonnektivitätsstatus**  
  * Nach dem Bereitstellen von API Management im Subnetz können Sie im Portal die Konnektivität Ihrer Instanz mit Abhängigkeiten – etwa Azure Storage – überprüfen. 
  * Wählen Sie im Portal im linken Menü unter **Deployment and infrastructure** (Bereitstellung und Infrastruktur) die Option **Netzwerkkonnektivitätsstatus** aus.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Überprüfen des Netzwerkkonnektivitätsstatus im Portal":::

  | Filtern | BESCHREIBUNG |
  | ----- | ----- |
  | **Erforderlich** | Wählen Sie diese Option aus, um die Konnektivität erforderlicher Azure-Dienste für API Management zu überprüfen. Ein Fehler weist darauf hin, dass die Instanz keine Kernvorgänge zum Verwalten von APIs durchführen kann. |
  | **Optional** | Wählen Sie diese Option aus, um die Konnektivität optionaler Dienste zu überprüfen. Ein Fehler gibt nur an, dass die spezifische Funktion nicht ausgeführt wird (z. B. SMTP). Ein Fehler kann zu einer Beeinträchtigung bei der Verwendung und Überwachung der API Management-Instanz sowie bei der Erfüllung der verbindlichen SLA führen. |

  Sehen Sie sich zum Beheben von Konnektivitätsproblemen die [Netzwerkkonfigurationseinstellungen](#network-configuration) an, und korrigieren Sie die entsprechenden Netzwerkeinstellungen.

* **Inkrementelle Updates**  
  Wenn Sie Änderungen an Ihrem Netzwerk vornehmen, sollten Sie sich mithilfe der [NetworkStatus-API](/rest/api/apimanagement/2020-12-01/network-status) vergewissern, dass der API Management-Dienst weiterhin Zugriff auf wichtige Ressourcen hat. Der Konnektivitätsstatus sollte alle 15 Minuten aktualisiert werden.

* **Ressourcennavigationslinks**  
  Eine APIM-Instanz, die auf der [`stv1`Computeplattform](compute-infrastructure.md) gehostet wird,reserviert bei der Bereitstellung in einem Resource Manager-VNet-Subnetz das Subnetz, indem ein Ressourcennavigationslink erstellt wird. Wenn das Subnetz bereits eine Ressource von einem anderen Hersteller enthält, tritt bei der Bereitstellung ein **Fehler** auf. Wenn Sie einen API Management-Dienst löschen oder in ein anderes Subnetz verschieben, wird der Ressourcennavigationslink ebenfalls entfernt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Verbinden eines virtuellen Netzwerks mit dem Back-End über VPN Gateway](../vpn-gateway/design.md#s2smulti)
* [Herstellen einer Verbindung mit einem virtuellen Netzwerk in verschiedenen Bereitstellungsmodellen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Debuggen von APIs mit der Anforderungsablaufverfolgung](api-management-howto-api-inspector.md)
* [Virtual Network – häufig gestellte Fragen](../virtual-network/virtual-networks-faq.md)
* [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

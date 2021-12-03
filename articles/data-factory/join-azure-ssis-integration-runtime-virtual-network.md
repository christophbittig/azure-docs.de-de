---
title: Verknüpfung der Azure-SSIS-Integrationslaufzeit mit einem virtuellen Netzwerk
description: Erfahren Sie, wie Sie die Azure-SSIS-Integrationslaufzeit mit einem Azure Virtual Network verknüpfen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9766ed3ed870e41c44a2dd84bbaad578ec54509c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087873"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network"></a>Verknüpfung der Azure-SSIS-Integrationslaufzeit mit einem virtuellen Netzwerk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bei Verwendung von SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) sollten Sie in folgenden Szenarien die Azure-SSIS Integration Runtime (IR) mit einem virtuellen Azure-Netzwerk verknüpfen:

- Sie möchten von SSIS-Paketen, die auf Ihrer Azure-SSIS IR ausgeführt werden, auf lokale Datenspeicher/Ressourcen zugreifen, ohne eine selbst gehostete IR als Proxy zu konfigurieren und zu verwalten.

- Sie möchten einen Azure SQL Database Server verwenden, der mit einem privaten Endpunkt/IP-Firewall-Regel/virtuellen Netzwerkdienst-Endpunkt oder Azure SQL Managed Instance konfiguriert ist, der ein virtuelles Netzwerk zum Hosten der SSIS-Katalogdatenbank (SSISDB) verbindet.

- Sie möchten über SSIS-Pakete, die auf Ihrem Azure-SSIS IR ausgeführt werden, auf Azure-Datenspeicher/-ressourcen zugreifen, die mit einem privaten Endpunkt, einer IP-Firewallregel oder einem VNS-Dienstendpunkt konfiguriert sind.

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung zu Datenquellen/Ressourcen herstellen, die mit IP-Firewallregeln konfiguriert sind.

Mit ADF können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das über das klassische Bereitstellungsmodell oder das Azure Resource Manager-Bereitstellungsmodell erstellt wurde.

> [!IMPORTANT]
> Das klassische virtuelle Netzwerk wird gerade als veraltet eingestuft. Daher sollten Sie stattdessen das virtuelle Azure Resource Manager-Netzwerk verwenden. Wenn Sie das klassische virtuelle Netzwerk bereits verwenden, wechseln Sie so bald wie möglich zum virtuellen Azure Resource Manager-Netzwerk.

Im Tutorial [Konfigurieren Azure-SSIS IR zum Beitreten zu einem virtuellen Netzwerk](tutorial-deploy-ssis-virtual-network.md) werden die Mindestschritte mit der Express-Einschleusungsmethode für virtuelle Netzwerke über die Azure-Portal/ADF-Benutzeroberfläche veranschaulicht. In diesem und anderen Artikeln wie dem Artikel [Konfigurieren eines virtuellen Netzwerks zum Einfügen Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)  wird das Tutorial erweitert, und es werden alle optionalen Schritte beschrieben:

- Wenn Sie die standardmäßige Einschleusungsmethode für virtuelle Netzwerke verwenden.
- Wenn Sie das klassische virtuelle Netzwerk verwenden.
- Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen (BYOIP) verwenden.
- Wenn Sie Ihren eigenen DNS-Server verwenden.
- Wenn Sie eine Netzwerksicherheitsgruppe (NSG) verwenden.
- Wenn Sie benutzerdefinierte Routen (UDRs) verwenden.
- Sie verwenden eine benutzerdefinierte Azure-SSIS IR.
- Wenn Sie Azure PowerShell verwenden, um Ihre Azure-SSIS IR bereitzustellen.

## <a name="access-to-on-premises-data-stores"></a>Zugriff auf lokale Datenspeicher

Wenn SSIS-Pakete auf lokale Datenspeicher zugreifen, können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das mit dem lokalen Netzwerk verbunden ist. Alternativ können Sie auch eine selbst gehostete IR als Proxy für Ihre Azure-SSIS-IR konfigurieren und verwalten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md). 

Wenn Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, sind einige wichtige Punkte zu beachten: 

- Wenn mit Ihrem lokalen Netzwerk kein virtuelles Netzwerk verbunden ist, erstellen Sie zunächst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network), mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Site-to-Site-[VPN-Gatewayverbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) oder eine [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)-Verbindung von diesem virtuellen Netzwerk zu Ihrem lokalen Netzwerk. 

- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk am selben Standort wie die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie die IR mit diesem virtuellen Netzwerk verknüpfen. 

- Wenn bereits ein klassisches virtuelles Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine [Verbindung zwischen dem klassischen virtuellen Netzwerk und dem virtuellen Azure Resource Manager-Netzwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie zuerst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Verbindung [zwischen den virtuellen Azure Resource Manager-Netzwerken](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-ssisdb-in-azure-sql-database-server-or-managed-instance"></a>Hosten von SSISDB auf Azure SQL-Datenbank Server oder verwaltete Instanz

Wenn Sie SSISDB in einem Azure SQL Database Server hosten, der mit einem virtuellen Netzwerkdienstendpunkt konfiguriert ist, stellen Sie sicher, dass Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk und Subnetz verbinden.

Wenn Sie SSISDB in einer Azure SQL Managed Instance hosten, die in ein virtuelles Netzwerk eingebunden ist, stellen Sie sicher, dass Sie Ihre Azure-SSIS IR in dasselbe virtuelle Netzwerk einbinden, aber in ein anderes Subnetz als die verwaltete Instanz. Um Ihre Azure-SSIS IR mit einem anderen virtuellen Netzwerk als die verwaltete Instanz zu verbinden, empfehlen wir entweder ein virtuelles Netzwerk-Peering (das auf dieselbe Region beschränkt ist) oder eine Verbindung zwischen den virtuellen Netzwerken. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-data-stores"></a>Zugriff auf Azure-Datenspeicher

Wenn Ihre SSIS-Pakete auf Azure-Datenspeicher/Ressourcen zugreifen, die [virtuelle Netzwerkdienst-Endpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) unterstützen, und Sie den Zugriff auf diese Ressourcen von Azure-SSIS IR aus sichern möchten, können Sie Ihr Azure-SSIS IR mit einem virtuellen Netzwerk-Subnetz verbinden, das für virtuelle Netzwerkdienst-Endpunkte konfiguriert ist, und dann eine virtuelle Netzwerkregel auf der Firewall der betreffenden Ressourcen hinzufügen, um den Zugriff aus demselben Subnetz zuzulassen.

## <a name="access-to-other-cloud-data-stores"></a>Zugriff auf andere Clouddatenspeicher

Wenn Ihre SSIS-Pakete auf andere Cloud-Datenspeicher/Ressourcen zugreifen, die nur bestimmte statische öffentliche IP-Adressen zulassen, und Sie den Zugriff auf diese Ressourcen von Azure-SSIS IR aus sichern möchten, können Sie [öffentliche IP-Adressen](../virtual-network/virtual-network-public-ip-address.md) mit Ihrem Azure-SSIS IR verknüpfen, während Sie es mit einem virtuellen Netzwerk verbinden und dann eine IP-Firewall-Regel auf der Firewall der betreffenden Ressourcen hinzufügen, um den Zugriff von diesen IP-Adressen aus zuzulassen. Es gibt dafür zwei Möglichkeiten: 

- Bei der Erstellung von Azure-SSIS IR können Sie Ihre eigenen statischen öffentlichen IP-Adressen mitbringen und sie über [ADF UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) oder [Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md) mit Ihrer Azure-SSIS IR verknüpfen. Nur die ausgehende Internetverbindung aus Ihrem Azure-SSIS IR verwendet diese öffentlichen IP-Adressen, während andere Ressourcen im Subnetz sie nicht verwenden.

- Sie können auch eine [virtuelle Netzwerkadressübersetzung (NAT)](../virtual-network/nat-gateway/nat-overview.md) in dem Subnetz konfigurieren, dem Ihre Azure-SSIS IR beitritt, und alle ausgehenden Internetverbindungen von diesem Subnetz werden eine bestimmte öffentliche IP-Adresse verwenden.

In allen Fällen kann das virtuelle Netzwerk nur über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines virtuellen Netzwerks zum Einschleusen einer Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express-Einschleusungsmethode für virtuelle Netzwerke](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Standard-Einschleusungsmethode für virtuelle Netzwerke](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk über die ADF-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Der Azure-SQL-Datenbank-Server zum Hosten von SSISDB wird verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anweisungen zur Verwendung des Azure-SQL-Datenbank-Servers, der mit einem VNET-Dienstendpunkt/einer IP-Firewallregel/einem privaten Endpunkt konfiguriert ist, oder einer Azure SQL verwalteten Instanz, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird veranschaulicht, wie Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.

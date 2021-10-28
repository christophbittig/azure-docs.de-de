---
title: Beitreten einer Azure-SSIS-Integrationslaufzeit zu einem virtuellen Netzwerk
description: Erfahren Sie, wie Sie eine Azure-SSIS Integration Runtime mit einem Azure Virtual Network verknüpfen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c89dd88c7163f74abb9246d57d72c485c1ed7efc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218880"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Beitreten einer Azure-SSIS-Integrationslaufzeit zu einem virtuellen Netzwerk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bei Verwendung von SQL Server Integration Services (SSIS) in Azure Data Factory sollten Sie in folgenden Szenarien die Azure-SSIS Integration Runtime (IR) mit einem Azure Virtual Network verknüpfen:

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen, ohne eine selbstgehostete IR als Proxy zu konfigurieren oder zu verwalten. 

- Sie möchten die SSIS-Katalogdatenbank (SSISDB) in einer Azure SQL-Datenbank mit IP-Firewallregeln/VNET-Dienstendpunkten oder einer SQL Managed Instance mit einem privatem Endpunkt hosten. 

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung zu Azure-Ressourcen herstellen, die mit VNET-Dienstendpunkten konfiguriert sind.

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung zu Datenquellen/Ressourcen herstellen, die mit IP-Firewallregeln konfiguriert sind.

Mit Data Factory können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das über das klassische Bereitstellungsmodell oder das Azure Resource Manager-Bereitstellungsmodell erstellt wurde.

> [!IMPORTANT]
> Das klassische virtuelle Netzwerk wird gerade als veraltet eingestuft. Daher sollten Sie stattdessen das virtuelle Azure Resource Manager-Netzwerk verwenden.  Wenn Sie das klassische virtuelle Netzwerk bereits verwenden, wechseln Sie so bald wie möglich zum virtuellen Azure Resource Manager-Netzwerk.

Im Tutorial zum [Konfigurieren einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) für die Verknüpfung mit einem virtuellen Netzwerk](tutorial-deploy-ssis-virtual-network.md) werden die grundlegenden Schritte mit dem Azure-Portal beschrieben. Dieser Artikel baut auf dem Tutorial auf, und es werden alle optionalen Aufgaben beschrieben:

- Sie verwenden ein virtuelles Netzwerk (klassisch).
- Sie verwenden Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR.
- Sie verwenden einen eigenen DNS-Server (Domain Name System).
- Sie verwenden eine Netzwerksicherheitsgruppe (NSG) im Subnetz.
- Sie verwenden Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR).
- Sie verwenden eine benutzerdefinierte Azure-SSIS IR.
- Wenn Sie Azure PowerShell-Bereitstellung verwenden.

## <a name="access-to-on-premises-data-stores"></a>Zugriff auf lokale Datenspeicher

Wenn SSIS-Pakete auf lokale Datenspeicher zugreifen, können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das mit dem lokalen Netzwerk verbunden ist. Sie können auch eine selbstgehostete IR als Proxy für die Azure-SSIS IR konfigurieren und verwalten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Wenn Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, sind einige wichtige Punkte zu beachten: 

- Wenn mit Ihrem lokalen Netzwerk kein virtuelles Netzwerk verbunden ist, erstellen Sie zunächst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network), mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Site-to-Site-[VPN-Gatewayverbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) oder eine [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)-Verbindung von diesem virtuellen Netzwerk zu Ihrem lokalen Netzwerk. 

- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk am selben Standort wie die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie die IR mit diesem virtuellen Netzwerk verknüpfen. 

- Wenn bereits ein klassisches virtuelles Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine [Verbindung zwischen dem klassischen virtuellen Netzwerk und dem virtuellen Azure Resource Manager-Netzwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie zuerst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Verbindung [zwischen den virtuellen Azure Resource Manager-Netzwerken](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hosten des SSIS-Katalogs in der SQL-Datenbank

Wenn Sie Ihren SSIS-Katalog in einer Azure SQL-Datenbank mit VNET-Dienstendpunkten hosten, müssen Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk und Subnetz verknüpfen.

Wenn Sie Ihren SSIS-Katalog in einer SQL Managed Instance mit einem privaten Endpunkt hosten, müssen Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk, aber in einem anderen Subnetz als die verwaltete Instanz verknüpfen. Wenn Sie die Azure-SSIS IR mit einem anderen virtuellen Netzwerk als die SQL Managed Instance verknüpfen möchten, empfiehlt sich das Peering virtueller Netzwerke (das auf dieselbe Region beschränkt ist) oder eine Verbindung zwischen den virtuellen Netzwerken. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Zugriff auf Azure-Dienste

Wenn Ihre SSIS-Pakete auf Azure-Ressourcen zugreifen, die [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) unterstützen, und Sie den Zugriff auf diese Ressourcen ausgehend von Azure-SSIS IR sichern möchten, können Sie Ihre Azure-SSIS IR zu einem für VNET-Dienstendpunkte konfigurierten Subnetz hinzufügen und dann eine VNET-Regel zu den relevanten Azure-Ressourcen hinzufügen, um Zugriff ausgehend vom gleichen Subnetz zuzulassen.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Zugriff auf Datenquellen, die durch eine IP-Firewallregel geschützt sind

Wenn Ihre SSIS-Pakete auf Datenspeicher/Ressourcen zugreifen, für die nur bestimmte statische öffentliche IP-Adressen zulässig sind, und Sie den Zugriff auf diese Ressourcen ausgehend von Azure-SSIS IR schützen möchten, können Sie Azure-SSIS IR Ihre eigenen [öffentlichen IP-Adressen](../virtual-network/ip-services/virtual-network-public-ip-address.md) zuordnen, wenn Sie die IR mit einem virtuellen Netzwerk verknüpfen. Anschließend können Sie den relevanten Ressourcen eine IP-Firewallregel hinzufügen, um den Zugriff über diese IP-Adressen zuzulassen. Hierfür gibt es zwei Möglichkeiten: 

- Wenn Sie Azure-SSIS IR erstellen, können Sie Ihre eigenen öffentlichen IP-Adressen mitbringen und sie über die Benutzeroberfläche von [Azure Data Factory Studio UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) oder [Azure PowerShell SDK](join-azure-ssis-integration-runtime-virtual-network-powershell.md) spezifizieren. Die von Ihnen angegebenen öffentlichen IP-Adressen werden nur für die ausgehende Internetkonnektivität der Azure-SSIS IR verwendet, und nicht von anderen Geräten im Subnetz.
- Sie können auch [Virtual Network NAT](../virtual-network/nat-gateway/nat-overview.md) für das Subnetz einrichten, in das die Azure-SSIS IR eingebunden wird. Für die gesamte ausgehende Konnektivität des Subnetzes werden dann die von Ihnen angegebenen öffentlichen IP-Adressen genutzt.

In allen Fällen kann das virtuelle Netzwerk nur über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Virtuelle Netzwerk-Details der Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über die Azure Data Factory Studio UI](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe von Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Sie erfahren, wie Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer SQL Managed Instance in einem virtuellen Netzwerk verwenden, um den SSIS-Katalog zu hosten. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen. Darüber hinaus enthält er Statusbeschreibungen für die zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR aufskalieren, indem Sie weitere Knoten hinzufügen.
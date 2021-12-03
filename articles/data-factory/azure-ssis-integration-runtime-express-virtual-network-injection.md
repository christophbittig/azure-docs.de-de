---
title: Konfigurieren eines virtuellen Netzwerks für die Expresseinschleusung von Azure-SSIS Integration Runtime
description: Hier erfahren Sie, wie Sie ein virtuelles Netzwerk für die Expresseinschleusung von Azure-SSIS Integration Runtime konfigurieren.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1d904e33f830f36e41acd0afd32dc7dd7dfaf2c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095575"
---
# <a name="express-virtual-network-injection-method-preview"></a>Expresseinschleusungsmethode für virtuelle Netzwerke (Vorschau)

Wenn Sie SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) verwenden, gibt es zwei Methoden zum Verknüpfen Ihrer Azure-SSIS Integration Runtime (IR) mit einem virtuellen Netzwerk: „Standard“ und „Express“. Wenn Sie die Express-Methode verwenden, müssen Sie Ihr virtuelles Netzwerk so konfigurieren, dass es folgende Anforderungen erfüllt: 

- Stellen Sie sicher, dass *Microsoft.Batch* ein registrierter Ressourcenanbieter im Azure-Abonnement mit dem virtuellen Netzwerk ist, mit dem Ihre Azure-SSIS IR verknüpft werden soll. Ausführliche Anleitungen finden Sie im Abschnitt [Registrieren von Azure Batch als Ressourcenanbieter](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Stellen Sie sicher, dass es in Ihrem virtuellen Netzwerk keine Ressourcensperre gibt.

- Wählen Sie ein geeignetes Subnetz in dem virtuellen Netzwerk aus, mit dem Ihre Azure-SSIS IR verknüpft werden soll. Weitere Informationen finden Sie weiter unten im Abschnitt [Auswählen eines Subnetzes](#subnet).

- Sorgen Sie dafür, dass dem Benutzer, der Azure-SSIS IR erstellt, die erforderlichen Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) zum Verknüpfen des virtuellen Netzwerks/Subnetzes erteilt werden.  Weitere Informationen finden Sie weiter unten im Abschnitt [Auswählen von Berechtigungen für virtuelle Netzwerke](#perms).

Je nach Ihrem spezifischen Szenario können Sie optional Folgendes konfigurieren:

- Wenn Sie eine statische öffentliche IP-Adresse für den ausgehenden Datenverkehr Ihrer Azure-SSIS IR verwenden möchten, lesen Sie weiter unten den Abschnitt [Konfigurieren einer statischen öffentlichen IP-Adresse](#ip).

- Wenn Sie Ihren eigenen DNS-Server (Domain Name System) im virtuellen Netzwerk verwenden möchten, lesen Sie weiter unten den Abschnitt [Konfigurieren eines benutzerdefinierten DNS-Servers](#dns).

- Wenn Sie eine Netzwerksicherheitsgruppe (NSG) verwenden möchten, um den ausgehenden Datenverkehr im Subnetz einzuschränken, lesen Sie weiter unten den Abschnitt [Konfigurieren einer Netzwerksicherheitsgruppe (NSG)](#nsg).

- Wenn Sie benutzerdefinierte Routen (User-Defined Routes, UDRs) zum Überwachen/Überprüfen von ausgehendem Datenverkehr verwenden möchten, lesen Sie weiter unten den Abschnitt [Konfigurieren von UDRs](#udr).

Dieses Diagramm veranschaulicht die für die Azure-SSIS IR erforderlichen Verbindungen:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-express.png" alt-text="Diagramm der erforderlichen Verbindungen für Ihre Expresseinschleusung von Azure-SSIS IR in virtuelle Netzwerke":::

## <a name="select-a-subnet"></a><a name="subnet"></a>Auswählen eines Subnetzes

Zum Aktivieren der Expresseinschleusung virtueller Netzwerke müssen Sie ein geeignetes Subnetz auswählen, mit dem Ihre Azure-SSIS IR verknüpft werden soll:

- Wählen Sie nicht das GatewaySubnet aus, da es Gateways für virtuelle Netzwerke vorbehalten ist.

- Stellen Sie sicher, dass das ausgewählte Subnetz über IP-Adressen für mindestens zweimal die Anzahl Ihrer Azure-SSIS IR-Knoten verfügt. Wir benötigen sie, um Unterbrechungen beim Rollout von Patches/Upgrades für Ihre Azure-SSIS IR zu vermeiden. Azure reserviert auch einige IP-Adressen, die nicht in jedem Subnetz verwendet werden können. Die erste und letzte IP-Adresse werden für Protokollkonformität reserviert, während drei weitere Adressen für Azure-Dienste reserviert werden. Weitere Informationen finden Sie im Abschnitt [Einschränkungen für Subnetz-IP-Adressen](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

- Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten genutzt wird (z. B. Azure SQL Managed Instance, App Service usw.).

- Das ausgewählte Subnetz muss an den Dienst *Microsoft.Batch/batchAccounts* delegiert werden. Weitere Informationen finden Sie im Artikel [Übersicht über die Subnetzdelegierung](../virtual-network/subnet-delegation-overview.md). Ausführliche Anleitungen finden Sie im Abschnitt [Delegieren eines Subnetzes an Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Auswählen von virtuellen Netzwerkberechtigungen

Um die standardmäßige Einschleusung virtueller Netzwerke zu aktivieren, müssen dem Benutzer, der Azure-SSIS IR erstellt, die erforderlichen RBAC-Berechtigungen zum Verknüpfen des virtuellen Netzwerks/Subnetzes erteilt werden. Sie haben zwei Möglichkeiten:

- Verwenden Sie die integrierte Rolle *Netzwerkmitwirkender*. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.

- Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung *Microsoft.Network/virtualNetworks/subnets/join/action* enthält.

Ausführliche Anleitungen finden Sie im Abschnitt [Erteilen von Berechtigungen für virtuelle Netzwerke](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms).

## <a name="configure-a-static-public-ip-address"></a><a name="ip"></a>Konfigurieren einer statischen öffentlichen IP-Adresse

Wenn Sie eine statische öffentliche IP-Adresse für den ausgehenden Datenverkehr Ihrer Azure-SSIS IR verwenden möchten, damit Sie sie für Ihre Firewalls zulassen können, müssen Sie die [Netzwerkadressenübersetzung (Network Address Translation, NAT) des virtuellen Netzwerks](../virtual-network/nat-gateway/nat-overview.md) konfigurieren, um sie einrichten zu können.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Konfigurieren eines benutzerdefinierten DNS-Servers

Wenn Sie Ihren eigenen DNS-Server im virtuellen Netzwerk verwenden möchten, um Ihre privaten Hostnamen aufzulösen, stellen Sie sicher, dass er auch globale Azure-Hostnamen auflösen kann (z. B. Ihren Azure Blob Storage `<your storage account>.blob.core.windows`).

Es wird empfohlen, Ihren eigenen DNS-Server so zu konfigurieren, dass nicht aufgelöste DNS-Anforderungen an die IP-Adresse rekursiver Azure-Konfliktlöser (*168.63.129.16*) weitergeleitet werden.

Weitere Informationen finden Sie im Abschnitt [DNS-Servernamensauflösung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Zurzeit müssen Sie Ihren eigenen DNS-Server mit einem benutzerdefinierten Standardsetup konfigurieren, damit Azure-SSIS IR ihn verwenden kann. Führen Sie dazu die folgenden Schritte aus:

1. Laden Sie ein benutzerdefiniertes Setupskript ([main.cmd](https://expressvnet.blob.core.windows.net/customsetup/main.cmd)) und die zugehörige Datei ([setupdnsserver.ps1](https://expressvnet.blob.core.windows.net/customsetup/setupdnsserver.ps1)) herunter.

1. Ersetzen Sie „your-dns-server-ip“ in „main.cmd“ durch die IP-Adresse Ihres eigenen DNS-Servers.

1. Laden Sie „main.cmd + setupdnsserver.ps1“ in Ihren eigenen Azure Storage-Blobcontainer für benutzerdefiniertes Standardsetup hoch, und geben Sie dessen SAS-URI bei der Bereitstellung von Azure-SSIS IR ein. Lesen Sie dazu den Artikel [Anpassen von Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

> [!NOTE]
> Verwenden Sie bitte einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für Ihren privaten Hostnamen (verwenden Sie z. B. `<your_private_server>.contoso.com` statt `<your_private_server>`). Alternativ können Sie ein benutzerdefiniertes Standardsetup in Ihrer Azure-SSIS IR verwenden, damit Ihr eigenes DNS-Suffix (z. B. `contoso.com`) an alle nicht qualifizierten einteiligen Domänennamen automatisch angefügt und in einen FQDN umgewandelt wird, bevor Sie es in DNS-Abfragen verwenden. Weitere Informationen finden Sie im Abschnitt [Beispiele für benutzerdefinierte Standardsetups](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Konfigurieren einer Netzwerksicherheitsgruppe (NSG)

Wenn Sie eine NSG in dem Subnetz verwenden möchten, das mit Ihrer Azure-SSIS IR verknüpft ist, lassen Sie den folgenden ausgehenden Datenverkehr zu: 

| Transportprotokoll | `Source` | Quellports | Destination | Zielports | Kommentare | 
|--------------------|--------|--------------|-------------|-------------------|----------| 
| TCP | *VirtualNetwork* | * | *DataFactoryManagement* | *443* | Erforderlich, damit Ihre Azure-SSIS IR auf ADF-Dienste zugreifen kann.<br/><br/>Der ausgehende Datenverkehr verwendet vorerst nur den öffentlichen ADF-Endpunkt. | 
| TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000–11999* | (Optional) Nur erforderlich, wenn Sie Azure SQL-Datenbank-Server/Verwaltete Instanz zum Hosten des SSIS-Katalogs (SSISDB) verwenden.<br/><br/>Wenn Ihr(e) Azure SQL-Datenbank-Server/Verwaltete Instanz mit einem öffentlichen Endpunkt/VNET-Dienstendpunkt konfiguriert ist, verwenden Sie das Diensttag *SQL* als Ziel.<br/><br/>Wenn Ihr(e) Azure SQL-Datenbank-Server/Verwaltete Instanz mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Diensttag *VirtualNetwork* als Ziel.<br/><br/>Wenn die Verbindungsrichtlinie Ihres Servers auf *Proxy* statt auf *Redirect* festgelegt ist, wird nur Port *1433* benötigt. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Optional) Nur erforderlich, wenn Sie Azure Storage-Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden.<br/><br/>Wenn Ihr Azure Storage mit einem öffentlichen Endpunkt/VNET-Dienstendpunkt konfiguriert ist, verwenden Sie das Diensttag *Storage* als Ziel.<br/><br/>Wenn Ihr Azure Storage mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Diensttag *VirtualNetwork* als Ziel. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Optional) Nur erforderlich, wenn Sie auf Azure Files zugreifen müssen.<br/><br/>Wenn Ihr Azure Storage mit einem öffentlichen Endpunkt/VNET-Dienstendpunkt konfiguriert ist, verwenden Sie das Diensttag *Storage* als Ziel.<br/><br/>Wenn Ihr Azure Storage mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Diensttag *VirtualNetwork* als Ziel. | 

## <a name="configure-udrs"></a><a name="udr"></a>Konfigurieren von UDRs

Wenn Sie den ausgehenden Datenverkehr von Ihrer Azure-SSIS IR überwachen/überprüfen möchten, können Sie ihn mithilfe von [benutzerdefinierten Routen (User-Defined Routes, UDRs)](../virtual-network/virtual-networks-udr-overview.md) über [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Tunnelerzwingung, die die BGP-Route (Border Gateway Protocol), *0.0.0.0/0* ankündigt, an das virtuelle Netzwerk, an ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA), das als Firewall konfiguriert ist, oder an den [Azure Firewall](../firewall/overview.md)-Dienst umleiten.

Entsprechend unserer Anleitung im Abschnitt [Konfigurieren einer Netzwerksicherheitsgruppe (NSG)](#nsg) weiter oben müssen Sie ähnliche Regeln für das Firewallgerät/den Firewalldienst implementieren, um den ausgehenden Datenverkehr von Ihrer Azure-SSIS IR zuzulassen:

- Wenn Sie Azure Firewall verwenden, gilt Folgendes:
  - Sie müssen Port *443* für ausgehenden TCP-Datenverkehr mit dem Diensttag *DataFactoryManagement* als Ziel öffnen.

  - Wenn Sie Azure SQL-Datenbank-Server/Verwaltete Instanz zum Hosten von SSISDB verwenden, müssen Sie die Ports *1433, 11000-11999* für ausgehenden TCP-Datenverkehr mit dem Diensttag *Sql/VirtualNetwork* als Ziel öffnen.

  - Wenn Sie Azure Storage Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden, müssen Sie Port *443* für ausgehenden TCP-Datenverkehr mit dem Diensttag *Storage/VirtualNetwork* als Ziel öffnen.

  - Wenn Sie auf Azure Files zugreifen müssen, müssen Sie Port *445* für ausgehenden TCP-Datenverkehr mit dem Diensttag *Storage/VirtualNetwork* als Ziel öffnen.

- Wenn Sie andere Firewallgeräte/-dienste verwenden:
  - Sie müssen Port *443* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder dem folgenden Azure-umgebungsspezifischen FQDN als Ziel öffnen:

    | Azure-Umgebung | FQDN |
    |-------------------|------|
    | <b>Azure öffentlich</b> | _\*.frontend.clouddatahub.net_ |
    | <b>Azure Government</b> | _\*.frontend.datamovement.azure.us_ |
    | <b>Azure China 21Vianet</b> | _\*.frontend.datamovement.azure.cn_ |

  - Wenn Sie Azure SQL-Datenbank-Server/Verwaltete Instanz zum Hosten von SSISDB verwenden, müssen Sie die Ports *1433, 11000-11999* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder Ihrem Azure SQL-Datenbank-Server/Verwaltete Instanz-FQDN als Ziel öffnen.

  - Wenn Sie Azure Storage-Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden, müssen Sie Port *443* für ausgehenden TCP-Datenverkehr mit dem Diensttag *0.0.0.0/0* oder Ihrem Azure Blob Storage-FQDN als Ziel öffnen.

  - Wenn Sie auf Azure Files zugreifen müssen, müssen Sie Port *445* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder Ihrem Azure Files-FQDN als Ziel öffnen.

## <a name="next-steps"></a>Nächste Schritte

- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über die ADF-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Darin wird der Azure SQL-Datenbank-Server zum Hosten von SSISDB verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Er enthält Anleitungen zur Verwendung des Azure-SQL-Datenbank-Servers, der mit einem VNET-Dienstendpunkt/einer IP-Firewallregel/einem privaten Endpunkt konfiguriert wurde, oder einer Azure SQL Managed Instance, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird gezeigt, wie Sie Ihre Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.

---
title: Azure Firewall-Tunnelerzwingung
description: Sie können die Tunnelerzwingung so konfigurieren, dass der Internetdatenverkehr zur weiteren Verarbeitung an eine zusätzliche Firewall oder ein virtuelles Netzwerkgerät weitergeleitet wird.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122343035"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall-Tunnelerzwingung

Wenn Sie eine neue Azure Firewall konfigurieren, können Sie den gesamten Internetdatenverkehr an den festgelegten nächsten Hop weiterleiten, statt dass er direkt ins Internet verläuft. Sie können beispielsweise eine über BGP angekündigte Standardroute oder eine benutzerdefinierte Route (User Defined Route, UDR) verwenden, um zu erzwingen, dass der Datenverkehr zu einer lokalen Edgefirewall oder einer anderen virtuellen Netzwerkappliance (NVA) geleitet wird, um den Netzwerkverkehr zu verarbeiten, bevor er an das Internet weitergeleitet wird. Um diese Konfiguration zu unterstützen, müssen Sie Azure Firewall mit aktivierter Konfiguration für Tunnelerzwingung erstellen. Dies ist eine obligatorische Anforderung, um Dienstunterbrechungen zu vermeiden. Wenn es sich um eine bereits vorhandene Firewall handelt, müssen Sie die Firewall im Modus „Tunnelerzwingung“ neu erstellen, damit diese Konfiguration unterstützt wird. Weitere Informationen dazu, wie Sie eine Firewall im Modus „Tunnelerzwingung“ beenden und neu starten, finden Sie in den [häufig gestellten Fragen zu Azure Firewall](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall).

## <a name="forced-tunneling-configuration"></a>Konfiguration der Tunnelerzwingung

Sie können die Tunnelerzwingung während der Firewallerstellung konfigurieren, indem Sie den Modus „Tunnelerzwingung“ wie unten gezeigt aktivieren. Zur Unterstützung der Tunnelerzwingung wird der Datenverkehr der Dienstverwaltung vom Kundendatenverkehr getrennt. Ein zusätzliches dediziertes Subnetz namens **AzureFirewallManagementSubnet** (Mindestsubnetz-Größe / 26) mit einer eigenen zugeordneten öffentlichen IP-Adresse ist erforderlich. 

Im Modus „Tunnelerzwingung“ wird das Verwaltungssubnetz (AzureFirewallManagementSubnet) zu *operativen* Zwecken im Azure Firewall-Dienst integriert. Standardmäßig ordnet der Dienst dem Verwaltungssubnetz eine vom System bereitgestellte Routingtabelle zu. Die einzige in diesem Subnetz zulässige Route ist eine Standardroute zum Internet, und *Gatewayrouten verteilen* muss deaktiviert sein. Vermeiden Sie es, Kundenroutentabellen zum Verwaltungssubnetz zuzuordnen, wenn Sie die Firewall erstellen. 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="Konfigurieren der Tunnelerzwingung":::

Innerhalb dieser Konfiguration kann das Subnetz *AzureFirewallSubnet* jetzt Routen zu jeder beliebigen lokalen Firewall oder jedem NVA enthalten, die bzw. das den Netzwerkverkehr erst verarbeitet, bevor er ans Internet übergeben wird. Sie können diese Routen auch über BGP an *AzureFirewallSubnet* veröffentlichen, wenn **Gatewayrouten verteilen** in diesem Subnetz aktiviert ist.

Sie können z. B. eine Standardroute im Subnetz *AzureFirewallSubnet* mit Ihrem VPN-Gateway als nächstem Hop erstellen, um auf Ihr lokales Gerät zu gelangen. Sie können auch **Gatewayrouten verteilen** aktivieren, um die entsprechenden Routen zum lokalen Netzwerk abzurufen.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Routenverteilung des Gateways für virtuelle Netzwerke":::

Wenn Sie jedoch die Tunnelerzwingung aktiviert haben, wird der Internet-gebundene Datenverkehr an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet übersetzt, wodurch die Quelle vor Ihrer lokalen Firewall verborgen wird.

Wenn Ihre Organisation einen öffentlichen IP-Adressbereich für private Netzwerke verwendet, leitet Azure Firewall den Datenverkehr per SNAT an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet weiter. Sie können Azure Firewall jedoch so konfigurieren, dass Ihr öffentlicher IP-Adressbereich **nicht** per SNAT weitergeleitet wird. Weitere Informationen finden Sie unter [Azure Firewall SNAT – private Adressbereiche](snat-private-range.md).

Nachdem Sie Azure Firewall zur Unterstützung von Tunnelerzwingung konfiguriert haben, können Sie diese Konfiguration nicht mehr rückgängig machen. Wenn Sie alle anderen IP-Konfigurationen in Ihrer Firewall entfernen, wird auch die IP-Konfiguration der Verwaltung entfernt, und die Zuordnung der Firewall wird aufgehoben. Die der Verwaltungs-IP-Konfiguration zugewiesene öffentliche IP-Adresse kann nicht entfernt werden, aber Sie können eine andere öffentliche IP-Adresse zuweisen.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk über das Azure-Portal](tutorial-hybrid-portal.md)

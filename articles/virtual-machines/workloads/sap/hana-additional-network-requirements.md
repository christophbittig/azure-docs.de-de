---
title: Weitere Netzwerkanforderungen für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie mehr über die hinzugefügten Netzwerkanforderungen für SAP HANA in Azure (große Instanzen), die möglicherweise vorhanden sind.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/3/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91f2116eb7eb2e6d7908e5c8c4e5171fff8c294
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361070"
---
# <a name="other-network-requirements-for-large-instances"></a>Weitere Netzwerkanforderungen für große Instanzen

In diesem Artikel werden andere Netzwerkanforderungen behandelt, die bei der Bereitstellung von SAP HANA in Azure (große Instanzen) auftreten können.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie die Schritte in folgenden Abschnitten abgeschlossen haben:
- [Verbinden von Azure-VMs mit HANA (große Instanzen)](hana-connect-azure-vm-large-instances.md)
- [Verbinden eines virtuellen Netzwerks mit HANA (große Instanzen)](hana-connect-vnet-express-route.md)

## <a name="add-more-ip-addresses-or-subnets"></a>Hinzufügen weiterer IP-Adressen oder Subnetze

Möglicherweise müssen Sie weitere IP-Adressen oder Subnetze hinzufügen. Verwenden Sie zum Hinzufügen weiterer IP-Adressen oder Subnetze das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI).

Fügen Sie dem Adressraum des virtuellen Netzwerks den neuen IP-Adressbereich als neuen Bereich hinzu. Generieren Sie keinen neuen aggregierten Bereich. Übermitteln Sie diese Änderung an Microsoft. Dadurch können Sie über diesen neuen IP-Adressbereich eine Verbindung mit HANA (große Instanzen) auf Ihrem Client herstellen. Sie können eine Azure-Supportanfrage öffnen, um den neuen Adressraum des virtuellen Netzwerks hinzufügen zu lassen. Nachdem Sie die Bestätigung erhalten haben, gehen Sie wie unter [Verbinden von Azure-VMs mit HANA (große Instanzen)](hana-connect-azure-vm-large-instances.md) beschrieben vor. 

Informationen zum Erstellen eines weiteren Subnetzes im Azure-Portal finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure-Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network). Informationen zur Erstellung mithilfe von PowerShell finden Sie unter [Erstellen eines virtuellen Netzwerks mithilfe von PowerShell](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Hinzufügen virtueller Netzwerke

Nachdem Sie zunächst Verbindungen mit einem oder mehreren virtuellen Azure-Netzwerken hergestellt haben, möchten Sie möglicherweise weitere virtuelle Netzwerke verbinden, die auf SAP HANA in Azure (große Instanzen) zugreifen. Senden Sie zuerst eine Azure-Supportanfrage. Geben Sie in dieser Anfrage die spezifischen Informationen zur Identifizierung der jeweiligen Azure-Bereitstellung an. Geben Sie auch den IP-Adressbereich oder die Bereiche des Adressraums des virtuellen Azure-Netzwerks an. Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam stellt dann die erforderlichen Informationen bereit, die Sie zum Herstellen der Verbindung mit den hinzugefügten virtuellen Netzwerken und Azure ExpressRoute benötigen. Für jedes virtuelle Netzwerk benötigen Sie einen eindeutigen Autorisierungsschlüssel, um eine Verbindung mit der ExpressRoute-Leitung für HANA (große Instanzen) herzustellen.

## <a name="increase-expressroute-circuit-bandwidth"></a>Erhöhen der Bandbreite der ExpressRoute-Leitung

Wenden Sie sich an das SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Wenn Ihnen geraten wird, die Bandbreite der ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) zu erhöhen, erstellen Sie eine Azure-Supportanfrage. (Sie können eine Erhöhung der Bandbreite einer einzelnen Verbindung um maximal 10 Gbit/s anfordern.) Sie erhalten dann nach Abschluss des Vorgangs eine Benachrichtigung. Sie müssen keine weiteren Aktionen ausführen, um diese höhere Geschwindigkeit in Azure zu aktivieren.

## <a name="add-another-expressroute-circuit"></a>Hinzufügen einer weiteren ExpressRoute-Leitung

Wenden Sie sich an das SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Wenn Ihnen geraten wird, eine weitere ExpressRoute-Leitung hinzuzufügen, erstellen Sie eine Azure-Supportanfrage (einschließlich einer Anfrage zum Erhalten der Autorisierungsinformationen zum Herstellen der Verbindung mit der neuen Leitung). Bevor Sie die Anfrage stellen, müssen Sie den für die virtuellen Netzwerke verwendeten Adressraum definieren. Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam kann dann die Autorisierung bereitstellen.

Nachdem die neue Leitung erstellt wurde und die Konfiguration von SAP HANA in der Microsoft Azure-Dienstverwaltung abgeschlossen ist, erhalten Sie eine Benachrichtigung mit den Informationen, die Sie zum Fortsetzen des Vorgangs benötigen. Sie können die virtuellen Azure-Netzwerke nicht mit dieser hinzugefügten Leitung verbinden, wenn sie bereits mit einer anderen ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) in derselben Azure-Region verbunden sind.

## <a name="delete-a-subnet"></a>Löschen eines Subnetzes

Zum Entfernen eines Subnetzes des virtuellen Netzwerks können Sie das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) verwenden. Wenn der IP-Adressbereich oder der Adressraum Ihres virtuellen Azure-Netzwerks ein aggregierter Bereich ist, besteht keine Notwendigkeit zur Nachverfolgung bei Microsoft. (Das virtuelle Netzwerk verbreitet weiterhin den BGP-Routenadressraum, der das gelöschte Subnetz umfasst.) 

Möglicherweise haben Sie den Adressbereich/Adressraum des virtuellen Azure-Netzwerks in Form von mehreren IP-Adressbereichen definiert. Einer dieser Bereiche könnte Ihrem gelöschten Subnetz zugewiesen worden sein. Löschen Sie diesen unbedingt aus dem Adressraum des virtuellen Netzwerks. Informieren Sie dann das SAP HANA in Microsoft Azure-Dienstverwaltungsteam darüber, dass der Bereich aus den Bereichen entfernt werden muss, mit denen SAP HANA in Azure (große Instanzen) kommunizieren darf.

Weitere Informationen finden Sie unter [Löschen eines Subnetzes](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Löschen eines virtuellen Netzwerks

Informationen hierzu finden Sie unter [Löschen eines virtuellen Netzwerks](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam entfernt die vorhandenen Autorisierungen für die ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen). Außerdem wird auch der IP-Adressbereich oder der Adressraum des virtuellen Azure-Netzwerks für die Kommunikation mit HANA (große Instanzen) entfernt.

Öffnen Sie nach dem Entfernen des virtuellen Netzwerks eine Azure-Supportanfrage, in der Sie die zu entfernenden IP-Adressbereiche/Bereiche des Adressraums angeben.

Achten Sie darauf, dass Sie alles entfernen. Löschen Sie Folgendes:
- ExpressRoute-Verbindung
- Gateway des virtuellen Netzwerks
- Öffentliche IP-Adresse des VNet-Gateways
- Virtuelles Netzwerk

## <a name="delete-an-expressroute-circuit"></a>Löschen einer ExpressRoute-Leitung

Um eine zusätzliche ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) zu entfernen, öffnen Sie eine Azure-Supportanfrage beim SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Fordern Sie an, dass die Leitung gelöscht wird. Im Azure-Abonnement können Sie das virtuelle Netzwerk nach Bedarf löschen oder beibehalten. Sie müssen jedoch die Verbindung zwischen der ExpressRoute-Leitung für HANA (große Instanzen) und dem verknüpften Gateway des virtuellen Netzwerks löschen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Installieren und Konfigurieren von SAP HANA in Azure (große Instanzen).

> [!div class="nextstepaction"]
> [Installieren und Konfigurieren von SAP HANA (große Instanzen)](hana-installation.md)

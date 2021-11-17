---
title: 'Azure VMware Solution by CloudSimple: Lokale Verbindung mithilfe von ExpressRoute'
description: Beschreibt, wie eine lokale Verbindung mithilfe von ExpressRoute aus dem CloudSimple-Regionsnetzwerk angefordert wird.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0c80e9e96fae2247d7e49f58bbad3918a4912d32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305615"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Herstellen einer Verbindung aus der lokalen Umgebung mit CloudSimple mithilfe von ExpressRoute

Wenn Sie bereits über eine Azure ExpressRoute-Verbindung von einem externen Standort (z.B. aus einer lokalen Umgebung) mit Azure verfügen, können Sie diese mit ihrer CloudSimple-Umgebung verbinden. Hierfür können Sie eine Azure-Funktion verwenden, mit der zwei ExpressRoute-Leitungen miteinander verbunden werden können. Diese Methode stellt eine sichere, private Verbindung mit hoher Bandbreite und geringer Latenz zwischen den beiden Umgebungen her.

[![Lokale ExpressRoute-Verbindung – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Voraussetzungen

Ein **/29**-Netzwerkadressblock ist erforderlich, um Global Reach-Verbindungen vom lokalen Standort aus herzustellen.  Der „/29“-Adressraum wird für das Transitnetzwerk zwischen ExpressRoute-Leitungen verwendet.  Zwischen dem Transitnetzwerk und Azure Virtual Networks, lokalen Netzwerken oder privaten CloudSimple-Netzwerken darf es keine Überschneidungen geben.

## <a name="prerequisites"></a>Voraussetzungen

* Eine Azure ExpressRoute-Leitung ist erforderlich, bevor Sie die Verbindung zwischen der Leitung und den Netzwerken der privaten CloudSimple-Cloud herstellen können.
* Es ist ein Benutzer mit Berechtigungen zum Erstellen von Autorisierungsschlüsseln für eine ExpressRoute-Leitung erforderlich.

## <a name="scenarios"></a>Szenarien

Wenn Sie eine Verbindung Ihres lokalen Netzwerks mit Ihrem Netzwerk in der privaten Cloud herstellen, können Sie die private Cloud auf verschiedene Weise verwenden, folgende Szenarios inbegriffen:

* Zugreifen auf Ihr privates Cloudnetzwerk, ohne eine Site-to-Site-VPN-Verbindung zu erstellen.
* Verwenden Ihres lokalen Active Directory als Identitätsquelle für Ihre private Cloud.
* Migrieren virtueller Computer, die lokal ausgeführt werden, zu Ihrer privaten Cloud.
* Verwenden Ihrer privaten Cloud als Teil einer Lösung für die Notfallwiederherstellung.
* Nutzen der lokalen Ressourcen auf den Workload-VMs in Ihrer privaten Cloud.

## <a name="connecting-expressroute-circuits"></a>Verbinden von ExpressRoute-Leitungen

Zum Einrichten der ExpressRoute-Verbindung müssen Sie eine Autorisierung für Ihre ExpressRoute-Leitung erstellen und die Autorisierungsinformationen CloudSimple zur Verfügung stellen.


### <a name="create-expressroute-authorization"></a>Erstellen der ExpressRoute-Autorisierung

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie in der oberen Suchleiste nach **ExpressRoute circuit** (ExpressRoute-Leitung), und klicken Sie unter **Services** (Dienste) auf **ExpressRoute circuits** (ExpressRoute-Leitungen).
    [![ExpressRoute-Leitungen](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Wählen Sie die ExpressRoute-Leitung aus, die Sie mit Ihrem CloudSimple-Netzwerk verbinden möchten.

4. Klicken Sie auf der Seite „ExpressRoute“ auf **Authorizations** (Autorisierungen), geben Sie einen Namen für die Autorisierung ein, und klicken Sie dann auf **Save** (Speichern).
    [![ExpressRoute-Leitungsautorisierung](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopieren Sie die Ressourcen-ID und den Autorisierungsschlüssel durch Klicken auf das Kopiersymbol. Fügen Sie die ID und den Schlüssel in eine Textdatei ein.
    [![ExpressRoute-Leitungsautorisierung: Kopieren](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > Die **Ressourcen-ID** muss aus der Benutzeroberfläche kopiert werden und sollte im Format ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` vorliegen, wenn Sie sie für den Support bereitstellen.

6. Reichen Sie ein Ticket für die zu erstellende Verbindung beim <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Support</a> ein.
    * Problemtyp: **Technisch**
    * Abonnement: **Wählen Sie das Abonnement aus, in dem der CloudSimple-Dienst bereitgestellt wird.**
    * Dienst: **VMware Solution by CloudSimple**
    * Problemtyp: **Service Request**
    * Problemuntertyp: **Herstellen einer ExpressRoute-Verbindung mit der lokalen Umgebung**
    * Geben Sie die Ressourcen-ID und den Autorisierungsschlüssel an, den Sie im Detailbereich kopiert und gespeichert haben.
    * Geben Sie einen„/29“-Netzwerkadressraum für das Transitnetzwerk an.
    * Verwenden Sie die Standardroute über ExpressRoute?
    * Sollte der Private Cloud-Datenverkehr die Standardroute über ExpressRoute verwenden?

    > [!IMPORTANT]
    > Mithilfe der Standardroute können Sie den gesamten Internetdatenverkehr aus der privaten Cloud mithilfe Ihrer lokalen Internetverbindung senden.  Um die für die private Cloud konfigurierte Standardroute zu deaktivieren und die Standardroute für die lokale Verbindung zu verwenden, geben Sie die Details im Supportticket an.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Azure-Netzwerkverbindungen](cloudsimple-azure-network-connection.md)  

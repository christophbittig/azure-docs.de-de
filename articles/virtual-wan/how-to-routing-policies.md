---
title: Konfigurieren von Routingrichtlinien für den Virtual WAN-Hub
titleSuffix: Azure Virtual WAN
description: Erfahren Sie, wie Sie Virtual WAN-Routingrichtlinien konfigurieren.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: 5fb694cd1dab2d53495e2e4b513ce6bfe2ebaff9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101828"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>Konfigurieren von Routingabsichten und Routingrichtlinien für den Virtual WAN-Hub

>[!NOTE] 
> Die Hubroutingabsicht befindet sich derzeit in der geschlossenen öffentlichen Vorschauversion. 
> 
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel (SLA) bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Einige Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Um Zugriff auf die Vorschauversion zu erhalten, stellen Sie zwei Hubs in derselben Azure-Region zusammen mit beliebigen Gateways (Site-to-Site-VPN-Gateways, Point-to-Site-Gateways und ExpressRoute-Gateways) bereit. Wenden Sie sich anschließend an previewinterhub@microsoft.com, und geben Sie die Virtual WAN-ID, Abonnement-ID und Azure-Region an, für die Sie Routingabsichten konfigurieren möchten. Erwarten Sie innerhalb von 48 Stunden (innerhalb der Geschäftszeiten von Montag-Freitag) eine Antwort mit der Bestätigung der Funktionsfreigabe. Beachten Sie, dass alle Gateways, die nach der Aktivierung des Features erstellt wurden, vom Virtual WAN-Team aktualisiert werden müssen.

## <a name="background"></a>Hintergrund 

Kunden, die Azure Firewall Manager zum Einrichten von Richtlinien für öffentlichen und privaten Datenverkehr nutzen, können ihre Netzwerke jetzt mithilfe von Routingabsicht und Routingrichtlinien deutlich einfacher einrichten.

Über Routingabsicht und Routingrichtlinien können Sie angeben, wie der Virtual WAN-Hub internetgebundenen und privaten Datenverkehr (Point-to-Site, Site-to-Site, ExpressRoute, virtuelle Netzwerkgeräte im Virtual WAN-Hub und im virtuellen Netzwerk) weiterleiten soll. Es gibt zwei Arten von Routingrichtlinien: Richtlinien zum Routing von Internetdatenverkehr und Routingrichtlinien für privaten Datenverkehr. Jeder Virtual WAN-Hub kann höchstens eine Richtlinie für das Routing von Internetdatenverkehr und eine Richtlinie für das Routing von privatem Datenverkehr mit jeweils einer Ressource für den nächsten Hop verwenden. 

Während privater Datenverkehr sowohl Zweigstellen- als auch VNet-Adresspräfixe umfasst, werden diese von Routingrichtlinien in Konzepten der Routingabsicht als eine Entität betrachtet.

>[!NOTE]
> In der geschlossenen öffentlichen Vorschauversion der Routingrichtlinien für Virtual WAN-Hubs wird der Datenverkehr zwischen den Hubs nur dann von Azure Firewall geprüft, wenn sich die Virtual WAN-Hubs in derselben Region befinden. 


* **Routingrichtlinie für Internetdatenverkehr**: Wenn eine Routingrichtlinie für Internetdatenverkehr für einen Virtual WAN-Hub konfiguriert ist, leiten alle Zweigstellen- (Benutzer-VPN [Point-to-Site-VPN], Site-to-Site-VPN und ExpressRoute) und VNet-Verbindungen mit diesem Virtual WAN-Hub internetgebundenen Datenverkehr an die Azure Firewall-Ressource oder den Sicherheitsanbieter eines Drittanbieters weiter, die bzw. der im Rahmen der Routingrichtlinie angegeben ist.
 

* **Routingrichtlinie für privaten Datenverkehr**: Wenn eine Routingrichtlinie für privaten Datenverkehr für einen Virtual WAN-Hub konfiguriert ist, wird der **gesamte** Zweigstellen- und VNet-Datenverkehr des Virtual WAN-Hubs (eingehend, ausgehend und zwischen Hubs) an die Azure Firewall-Ressource des nächsten Hops weitergeleitet, die in der Routingrichtlinie für privaten Datenverkehr angegeben wurde. 

    Mit anderen Worten, wenn eine Richtlinie für das Routing von privatem Datenverkehr auf dem Virtual WAN-Hub konfiguriert ist, wird der gesamte Datenverkehr zwischen Zweigstellen, zwischen Zweigstellen und virtuellen Netzwerken, zwischen virtuellen Netzwerken und Zweigstellen sowie zwischen Hubs über Azure Firewall geleitet.


## <a name="key-considerations"></a>Wichtige Aspekte
* Sie werden **nicht** in der Lage sein, Routingrichtlinien für Ihre Bereitstellungen zu aktivieren, wenn bereits benutzerdefinierte Routingtabellen konfiguriert sind oder wenn statische Routen in Ihrer Standardroutingtabelle konfiguriert sind.
* Derzeit werden Richtlinien für das Routing von privatem Datenverkehr nicht in Hubs mit verschlüsselten ExpressRoute-Verbindungen unterstützt (Site-to-Site-VPN-Tunnel, die über ExpressRoute (private Verbindung) ausgeführt werden). 
* In der geschlossenen öffentlichen Vorschauversion der Routingrichtlinien für Virtual WAN-Hubs wird der Datenverkehr zwischen den Hubs nur dann von Azure Firewall geprüft, wenn sich die Virtual WAN-Hubs in derselben Region befinden.
* Routingabsicht und Routingrichtlinien müssen derzeit über den in Schritt 3 von **Voraussetzungen** angegebenen Link zum benutzerdefinierten Portal konfiguriert werden. Routingabsichten und -richtlinien werden über Terraform, PowerShell und CLI nicht unterstützt. 

## <a name="prerequisites"></a>Voraussetzungen
1. Erstellen Sie eine Virtual WAN-Instanz. Stellen Sie sicher, dass Sie mindestens zwei virtuelle Hubs in der **gleichen** Region erstellen. Sie können zum Beispiel ein Virtual WAN mit zwei virtuellen Hubs in der Region „USA, Osten“ einrichten. 
2. Konvertieren Sie Ihren Virtual WAN-Hub in einen geschützten Virtual WAN-Hub, indem Sie eine Azure Firewall in den virtuellen Hubs in der gewählten Region bereitstellen. Weitere Informationen zur Konvertierung Ihres Virtual WAN-Hubs in einen geschützten Virtual WAN-Hub finden Sie in diesem [Dokument](howto-firewall.md).
3. Stellen Sie alle Site-to-Site-VPN-, Point-to-Site-VPN- und ExpressRoute-Gateways bereit, die Sie zu Testzwecken verwenden möchten. Wenden Sie sich an **previewinterhub@microsoft.com** mit der **Virtual WAN-Ressourcen-ID** und der **Region für den Azure Virtual WAN-Hub**, in der Sie Routingrichtlinien konfigurieren möchten. Um die Virtual WAN-ID zu finden, öffnen Sie das Azure-Portal, navigieren Sie zu Ihrer Virtual WAN-Ressource, und wählen Sie „Einstellungen > Eigenschaften > Ressourcen-ID“ aus. Beispiel: 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  Sie sollten innerhalb von 24–48 Stunden eine Antwort mit einer Bestätigung der Funktionsfreigabe erhalten. 
5. Stellen Sie sicher, dass Ihre virtuellen Hubs **keine** benutzerdefinierten Routingtabellen oder Routen aufweisen, die Sie in „defaultRouteTable“ eingefügt haben. Sie werden **nicht** in der Lage sein, Routingrichtlinien für Ihre Bereitstellungen zu aktivieren, wenn bereits benutzerdefinierte Routingtabellen konfiguriert sind oder wenn statische Routen in Ihrer Standardroutingtabelle konfiguriert sind. 

## <a name="configuring-routing-policies"></a>Konfigurieren von Routingrichtlinien
1. Navigieren Sie über den Link zum benutzerdefinierten Portal, den Sie in der Bestätigungs-E-Mail aus Schritt 3 im Abschnitt **Voraussetzungen** erhalten haben, zu dem Virtual WAN-Hub, für den Sie Routingrichtlinien konfigurieren möchten.
1. Wählen Sie unter „Sicherheit“ die Option **Einstellungen für geschützte virtuelle Hubs** und dann **Sicherheitsanbieter- und Routingeinstellungen für diesen geschützten virtuellen Hub in Azure Firewall Manager verwalten**
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="Screenshot: Ändern der Einstellungen für den geschützten Hub"lightbox="./media/routing-policies/secured-hub-settings.png":::
1. Wählen Sie im Menü den Hub aus, für den Sie Ihre Routingrichtlinien konfigurieren möchten.
1. Wählen Sie **Sicherheitskonfiguration** unter **Einstellungen** aus.
1. Wenn Sie eine Richtlinie für die Weiterleitung des Internetdatenverkehrs konfigurieren möchten, wählen Sie **Azure Firewall** oder den entsprechenden Internetsicherheitsanbieter aus der Dropdownliste für **Internetdatenverkehr** aus. Wählen Sie andernfalls **Keine** aus.
1. Wenn Sie eine Richtlinie für die Weiterleitung von privatem Datenverkehr (für Zweigstellen- und virtuellen Netzwerkdatenverkehr) über Azure Firewall konfigurieren möchten, wählen Sie **Azure Firewall** im Dropdownmenü für **Privater Datenverkehr** aus. Andernfalls wählen Sie **Azure Firewall umgehen** aus.

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="Screenshot: Konfigurieren von Routingrichtlinien"lightbox="./media/routing-policies/configuring-intents.png":::

7. Wenn Sie eine Richtlinie für das Routing von privatem Datenverkehr konfigurieren möchten und über Zweigstellen oder virtuelle Netzwerke verfügen, die Nicht-IANA-RFC1918-Präfixe ankündigen, wählen Sie **Präfixe für privaten Datenverkehr** aus, und geben Sie die Nicht-IANA-RFC1918-Präfixbereiche im angezeigten Textfeld an. Wählen Sie **Fertig** aus. 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="Screenshot: Bearbeiten von Präfixen für privaten Datenverkehr"lightbox="./media/routing-policies/private-prefixes.png":::

8. Wählen Sie für **Inter-hub** (Zwischen Hubs) die Option **Aktiviert** aus. Wenn Sie diese Option aktivieren, wird sichergestellt, dass Ihre Routingrichtlinien auf die Routingabsicht dieses Virtual WAN-Hubs angewendet werden. 
9. Wählen Sie **Speichern** aus. Dieser Vorgang wird etwa 10 Minuten dauern. 
10. Wiederholen Sie die Schritte 2 bis 8 für andere geschützte Virtual WAN-Hubs, für die Sie Routingrichtlinien konfigurieren möchten. 
 
## <a name="routing-policy-configuration-examples"></a>Beispiele für die Konfiguration von Routingrichtlinien 

Im folgenden Abschnitt werden zwei gängige Szenarien beschrieben, in denen Kunden Routingrichtlinien auf geschützte Virtual WAN-Hubs anwenden.

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>Alle Virtual WAN-Hubs sind geschützt (mit Azure Firewall bereitgestellt)

In diesem Szenario werden alle Virtual WAN-Hubs mit einer Azure Firewall-Instanz bereitgestellt. In diesem Szenario können Sie auf jedem Virtual WAN-Hub eine Richtlinie für das Routing von Internetdatenverkehr, eine Richtlinie für das Routing von privatem Datenverkehr oder beide Richtlinien konfigurieren. 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="Screenshot: Architektur mit zwei geschützten Hubs"lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

Betrachten Sie die folgende Konfiguration, bei der Hub 1 und Hub 2 über Routingrichtlinien für privaten und Internetdatenverkehr verfügen. 

**Hub 1-Konfiguration:**
* Richtlinie für privaten Datenverkehr mit Azure Firewall für Hub 1 des nächsten Hops
* Richtlinie für Internetdatenverkehr mit Azure Firewall für Hub 1 des nächsten Hops 

**Hub 2-Konfiguration:**
* Richtlinie für privaten Datenverkehr mit Azure Firewall für Hub 2 des nächsten Hops
* Richtlinie für Internetdatenverkehr mit Azure Firewall für Hub 2 des nächsten Hops 

Im Folgenden sind die Datenverkehrsflüsse aufgeführt, die sich aus einer solchen Konfiguration ergeben. 

> [!NOTE]
> Der Internetdatenverkehr muss die **lokale** Azure Firewall-Instanz durchlaufen, da die Standardroute (0.0.0.0/0) **nicht** über Hubs hinweg weitergegeben wird.

| From |   To |  VNets von Hub 1 | Branches von Hub 1 | VNets von Hub 2 | Branches von Hub 2| Internet|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| VNets von Hub 1     | &#8594;| AzFW von Hub 1 |   AzFW von Hub 1    | AzFW von Hub 1 und 2 | AzFW von Hub 1 und 2 | AzFW von Hub 1 |
| Branches von Hub 1   | &#8594;|  AzFW von Hub 1  |   AzFW von Hub 1    | AzFW von Hub 1 und 2 | AzFW von Hub 1 und 2 | AzFW von Hub 1|
| VNets von Hub 2     | &#8594;| AzFW von Hub 1 und 2 |   AzFW von Hub 1 und 2    | AzFW von Hub 2  | AzFW von Hub 2 | AzFW von Hub 2|
| Branches von Hub 2   | &#8594;|   AzFW von Hub 1 und 2  |    AzFW von Hub 1 und 2    | AzFW von Hub 2 |  AzFW von Hub 2 | AzFW von Hub 2|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>Mischung aus geschützten und regulären Virtual WAN-Hubs 

In diesem Szenario werden nicht alle Virtual WAN-Hubs mit einer Azure Firewall-Instanz bereitgestellt. In diesem Szenario können Sie auf den geschützten Virtual WAN-Hubs eine Richtlinie für das Routing von Internetdatenverkehr und eine Richtlinie für das Routing von privatem Datenverkehr konfigurieren. 

Betrachten Sie die folgende Konfiguration, bei der Hub 1 (Normal) und Hub 2 (Geschützt) in einem Virtual WAN bereitgestellt werden. Hub 2 verfügt über Routingrichtlinien für privaten und Internetdatenverkehr. 

**Hub 1-Konfiguration:**
* Nicht zutreffend (Routingrichtlinien können nicht konfiguriert werden, wenn der Hub nicht mit Azure Firewall bereitgestellt wird)

**Hub 2-Konfiguration:**
* Richtlinie für privaten Datenverkehr mit Azure Firewall für Hub 2 des nächsten Hops
* Richtlinie für Internetdatenverkehr mit Azure Firewall für Hub 2 des nächsten Hops 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="Screenshot: Architektur mit einem geschützten Hub und einem normalen Hub"lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 Im Folgenden sind die Datenverkehrsflüsse aufgeführt, die sich aus einer solchen Konfiguration ergeben. Branches und virtuelle Netzwerke, die mit Hub 1 verbunden sind, können über Azure Firewall im Hub **nicht** auf das Internet zugreifen, da die Standardroute (0.0.0.0/0) **nicht** über Hubs weitergegeben wird.

| From |   To |  VNets von Hub 1 | Branches von Hub 1 | VNets von Hub 2 | Branches von Hub 2| Internet |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| VNets von Hub 1     | &#8594;| Direkt |   Direkt   | AzFW von Hub 2 | AzFW von Hub 2 | - |
| Branches von Hub 1   | &#8594;|  Direkt |   Direkt    | AzFW von Hub 2 | AzFW von Hub 2 | - |
| VNets von Hub 2     | &#8594;| AzFW von Hub 2 |   AzFW von Hub 2    | AzFW von Hub 2  | AzFW von Hub 2 | AzFW von Hub 2|
| Branches von Hub 2   | &#8594;|   AzFW von Hub 2  |    AzFW von Hub 2    | AzFW von Hub 2 |  AzFW von Hub 2 | AzFW von Hub 2|


## <a name="troubleshooting"></a>Problembehandlung

Im folgenden Abschnitt werden häufige Probleme beim Konfigurieren von Routingrichtlinien auf Ihrem Virtual WAN Hub beschrieben.  Lesen Sie die folgenden Abschnitte, und wenn Ihr Problem immer noch nicht behoben ist, wenden Sie sich an previewinterhub@microsoft.com, um Unterstützung zu erhalten. Erwarten Sie eine Antwort innerhalb von 48 Stunden (innerhalb der Geschäftszeiten von Montag-Freitag). 

### <a name="troubleshooting-configuration-issues"></a>Problembehandlung von Konfigurationsproblemen
*  Vergewissern Sie sich, dass Sie von previewinterhub@microsoft.com die Bestätigung erhalten haben, dass Sie für Ihr Abonnement und die von Ihnen gewählte Region Zugriff auf die geschlossene öffentliche Vorschauversion erhalten haben. Sie können Routingrichtlinien **nicht** konfigurieren, ohne zuvor Zugriff auf die Vorschauversion zu erhalten.
* Stellen Sie nach der Aktivierung der Routingrichtlinienfunktion für Ihre Bereitstellung sicher, dass Sie **nur** den Link zum benutzerdefinierten Portal verwenden, der als Teil Ihrer Bestätigungs-E-Mail bereitgestellt wurde. Verwenden Sie keine PowerShell-, CLI- oder REST-API-Aufrufe, um Ihre Virtual WAN-Bereitstellungen zu verwalten.  Dazu gehört das Erstellen neuer Branchverbindungen (Site-to-Site-VPN, Point-to-Site-VPN oder ExpressRoute). 

>[!NOTE]
> Wenn Sie Terraform verwenden, werden Routingrichtlinien derzeit nicht unterstützt.

*  Stellen Sie sicher, dass Ihre virtuellen Hubs nicht über benutzerdefinierte Routentabellen oder statische Routen in „defaultRouteTable“ verfügen. Sie werden **nicht** in der Lage sein, die Option **Enable Interhub** (Interhub aktivieren) in Firewall Manager auf Ihrem Virtual WAN-Hub auszuwählen, wenn benutzerdefinierte Routingtabellen konfiguriert sind oder wenn statische Routen in Ihrer „defaultRouteTable“ vorhanden sind. 

### <a name="troubleshooting-data-path"></a>Problembehandlung für den Datenpfad 

* Derzeit ist die Verwendung von Azure Firewall zur Überprüfung des Datenverkehrs zwischen Hubs nur für Virtual WAN-Hubs verfügbar, die in der **gleichen** Azure-Region bereitgestellt werden. 
* Derzeit werden Richtlinien für das Routing von privatem Datenverkehr nicht in Hubs mit verschlüsselten ExpressRoute-Verbindungen unterstützt (Site-to-Site-VPN-Tunnel, die über ExpressRoute (private Verbindung) ausgeführt werden). 
* Sie können überprüfen, ob die Routingrichtlinien ordnungsgemäß angewendet wurden, indem Sie die effektiven Routen von „DefaultRouteTable“ überprüfen. Wenn private Routingrichtlinien konfiguriert sind, sollten Sie in „DefaultRouteTable“ Routen für private Datenverkehrspräfixe mit dem nächsten Hop „Azure Firewall“ sehen. Wenn Routingrichtlinien für Internetdatenverkehr konfiguriert sind, sollten Sie eine Standardroute (0.0.0.0/0) in „DefaultRouteTable“ mit dem nächsten Hop „Azure Firewall“ sehen.
* Wenn Site-to-Site-VPN-Gateways oder Point-to-Site-VPN-Gateways erstellt wurden, **nachdem** die Aktivierung des Features für Ihre Bereitstellung bestätigt wurde, müssen Sie sich erneut an previewinterhub@microsoft.com wenden, um das Feature aktivieren zu lassen. 

### <a name="troubleshooting-azure-firewall"></a>Problembehandlung für Azure Firewall

* Wenn Sie in Ihren Branches/Virtuellen Netzwerken Präfixe verwenden, die nicht [IANA RFC1918](https://datatracker.ietf.org/doc/html/rfc1918) entsprechen, müssen Sie diese Präfixe im Textfeld „Private Präfixe“ in Firewall Manager angeben.
* Wenn Sie Nicht-RFC1918-Adressen als Teil des Textfelds **Präfixe für privaten Datenverkehr** in Firewall Manager angegeben haben, müssen Sie möglicherweise SNAT-Richtlinien für Ihre Firewall konfigurieren, um SNAT für nicht RFC1918 entsprechenden privaten Datenverkehr zu deaktivieren. Weitere Informationen finden Sie in folgenden [Dokument](../firewall/snat-private-range.md). 
* Ziehen Sie in Erwägung, Azure Firewall-Protokolle zu konfigurieren und anzuzeigen, um die Problembehandlung und Analyse Ihres Netzwerkdatenverkehrs zu erleichtern. Weitere Informationen zur Einrichtung der Überwachung für Azure Firewall finden Sie im folgenden [Dokument](../firewall/firewall-diagnostics.md). Eine Übersicht über die verschiedenen Arten von Firewallprotokollen finden Sie [hier](../firewall/logs-and-metrics.md).
* Weitere Informationen zu Azure Firewall finden Sie in der [Azure Firewall-Dokumentation](../firewall/overview.md).



## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>Warum kann ich die „defaultRouteTable“ nicht über den von previewinterhub@microsoft.com bereitgestellten Link zum benutzerdefinierten Portal bearbeiten?

Als Teil der geschlossenen öffentlichen Vorschau von Routingrichtlinien wird das Routing Ihres Virtual WAN-Hubs vollständig von Firewall Manager verwaltet. Außerdem wird die verwaltete Vorschau von Routingrichtlinien neben dem benutzerdefinierten Routing **nicht** unterstützt. Benutzerdefiniertes Routing mit Routingrichtlinien wird zu einem späteren Zeitpunkt unterstützt. 

Sie können jedoch weiterhin die effektiven Routen der „DefaultRouteTable“ anzeigen, indem Sie zur Registerkarte **Effektive Routen** navigieren.

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>Kann ich eine Routingrichtlinie für privaten Datenverkehr konfigurieren und auch Internetdatenverkehr (0.0.0.0/0) über ein virtuelles Netzwerkgerät in einem virtuellen Spoke-Netzwerk senden?

Dieses Szenario wird in der geschlossenen öffentlichen Vorschau nicht unterstützt. Wenden Sie sich jedoch an previewinterhub@microsoft.com, um Ihr Interesse an der Implementierung dieses Szenarios zu bekunden. 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>Wird die Standardroute (0.0.0.0/0) über Hubs hinweg weitergegeben?

Nein. Derzeit senden Branches und virtuelle Netzwerke den Datenverkehr über eine Azure Firewall-Instanz, die innerhalb des Virtual WAN-Hubs, mit dem die Branches und virtuellen Netzwerke verbunden sind, bereitgestellt wird, in das Internet. Sie können keine Verbindung konfigurieren, um über die Firewall in einem Remotehub auf das Internet zuzugreifen.

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>Warum werden RFC1918-Präfixe für meine lokalen Geräte angekündigt?

Wenn Routingrichtlinien für privaten Datenverkehr konfiguriert sind, kündigen die Virtual WAN-Gateways zusätzlich zu den expliziten Präfixen für Branches und virtuelle Netzwerke automatisch statische Routen an, die in der Standardroutingtabelle enthalten sind (RFC1918-Präfixe: 10.0.0.0/8,172.16.0.0/12,192.168.0.0/16).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).
Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).

---
title: Was ist der Azure Private Link-Dienst?
description: Erfahren Sie mehr über den Azure Private Link-Dienst.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: a621d93bda3c3a032932a9d6349dcbce0190610c
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740244"
---
# <a name="what-is-azure-private-link-service"></a>Was ist der Azure Private Link-Dienst?

Der Azure Private Link-Dienst ist der Verweis auf Ihren eigenen Dienst, der von Azure Private Link unterstützt wird. Ihr Dienst, der hinter [Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md) ausgeführt wird, kann für den Zugriff auf Private Link aktiviert werden, sodass die Nutzer Ihres Dienstes privat über ihre eigenen virtuellen Netzwerke darauf zugreifen können. Ihre Kunden können einen privaten Endpunkt in ihrem VNet erstellen und diesem Dienst zuordnen. Dieser Artikel erläutert Konzepte, die sich auf die Seite des Dienstanbieters beziehen. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Private Link-Dienstworkflow" border="true":::

*Abbildung: Azure Private Link-Dienst*

## <a name="workflow"></a>Workflow

![Private Link-Dienstworkflow](media/private-link-service-overview/private-link-service-workflow.png)


*Abbildung: Azure Private Link-Dienstworkflow*

### <a name="create-your-private-link-service"></a>Erstellen eines Private Link-Diensts

- Konfigurieren Sie Ihre Anwendung so, dass sie hinter einem Load Balancer Standard in Ihrem virtuellen Netzwerk ausgeführt wird. Wenn Sie Ihre Anwendung bereits hinter einem Load Balancer Standard konfiguriert haben, können Sie diesen Schritt überspringen.   
- Erstellen Sie einen Private Link-Dienst, der auf den obigen Lastausgleich verweist. Wählen Sie im Auswahlprozess des Lastausgleichs die Front-End-IP-Konfiguration aus, für die Sie den Datenverkehr empfangen möchten. Wählen Sie ein Subnetz für NAT-IP-Adressen für den Private Link-Dienst aus. Es wird empfohlen, dass im Subnetz mindestens acht NAT-IP-Adressen zur Verfügung stehen. Der gesamte Consumerdatenverkehr scheint von diesem Pool privater IP-Adressen an den Dienstanbieter zu stammen. Wählen Sie die entsprechenden Eigenschaften/Einstellungen für den Private Link-Dienst aus.    

    > [!NOTE]
    > Der Azure Private Link-Dienst wird nur von Load Balancer Standard unterstützt. 
    
### <a name="share-your-service"></a>Freigeben Ihres Diensts

Nachdem Sie einen Private Link-Dienst erstellt haben, generiert Azure einen weltweit eindeutigen Moniker namens „Alias“, der auf dem Namen basiert, den Sie für Ihren Dienst angeben. Sie können entweder den Alias oder den Ressourcen-URI Ihres Diensts offline für Ihre Kunden freigeben. Consumer können eine Private Link-Verbindung mit dem Alias oder Ressourcen-URI starten.
 
### <a name="manage-your-connection-requests"></a>Verwalten Ihrer Verbindungsanforderungen

Nachdem ein Consumer eine Verbindung hergestellt hat, kann der Dienstanbieter die Verbindungsanforderung annehmen oder ablehnen. Alle Verbindungsanforderungen werden unter der Eigenschaft **privateendpointconnections** des Private Link-Diensts aufgeführt.
 
### <a name="delete-your-service"></a>Löschen Ihres Diensts

Wenn der Private Link-Dienst nicht mehr verwendet wird, können Sie ihn löschen. Bevor Sie den Dienst jedoch löschen, stellen Sie sicher, dass ihm keine privaten Endpunktverbindungen zugeordnet sind. Sie können alle Verbindungen ablehnen und den Dienst löschen.

## <a name="properties"></a>Eigenschaften

Ein Private Link-Dienst legt die folgenden Eigenschaften fest: 

|Eigenschaft |Erklärung  |
|---------|---------|
|Bereitstellungsstatus (provisioningState)  |Eine schreibgeschützte Eigenschaft, die den aktuellen Bereitstellungsstatus für den Private Link-Dienst auflistet. Geeignete Bereitstellungsstatus sind: „Wird gelöscht; Fehler; Erfolgreich; Wird aktualisiert“. Wenn der Bereitstellungsstatus „Erfolgreich“ lautet, haben Sie Ihren Private Link-Dienst erfolgreich bereitgestellt.        |
|Alias (alias)     | Alias ist eine weltweit eindeutige schreibgeschützte Zeichenfolge für Ihren Dienst. Sie hilft Ihnen, die Kundendaten für Ihren Dienst zu maskieren und erstellt gleichzeitig einen einfach zu teilenden Namen für Ihren Dienst. Wenn Sie einen Private Link-Dienst erstellen, generiert Azure den Alias für Ihren Dienst, den Sie mit Ihren Kunden teilen können. Ihre Kunden können unter diesem Alias eine Verbindung zu Ihrem Dienst anfordern.          |
|Sichtbarkeit (visibility)     | Sichtbarkeit ist die Eigenschaft, die die Einstellungen für die Offenlegung für Ihren Private Link-Dienst steuert. Dienstanbieter können auswählen, ob sie das Risiko für ihren Dienst auf Abonnements mit rollenbasierten Zugriffssteuerungsberechtigungen (Azure RBAC), eine eingeschränkte Gruppe von Abonnements oder alle Azure-Abonnements begrenzen möchten.          |
|Automatische Genehmigung (autoApproval)    |   Die automatische Genehmigung steuert den automatisierten Zugriff auf den Private Link-Dienst. Die in der automatischen Genehmigungsliste angegebenen Abonnements werden automatisch genehmigt, wenn eine Verbindung von privaten Endpunkten in diesen Abonnements angefordert wird.          |
|Front-End-IP-Konfiguration des Lastenausgleichs (loadBalancerFrontendIpConfigurations)    |    Der Private Link-Dienst ist an die Front-End-IP-Adresse eines Load Balancer Standard gebunden. Der gesamte für den Dienst bestimmte Datenverkehr erreicht das Front-End des Load Balancer Standard. Sie können Load Balancer Standard-Regeln konfigurieren, um diesen Datenverkehr an geeignete Back-End-Pools weiterzuleiten, in denen Ihre Anwendungen ausgeführt werden. Front-End-IP-Konfigurationen für den Lastenausgleich unterscheiden sich von NAT-IP-Konfigurationen.      |
|NAT-IP-Konfiguration (ipConfigurations)    |    Diese Eigenschaft bezieht sich auf die NAT-IP-Konfiguration (Network Address Translation) für den Private Link-Dienst. Die NAT-IP-Adresse kann aus einem beliebigen Subnetz im virtuellen Netzwerk eines Dienstanbieters ausgewählt werden. Der Private Link-Dienst führt die zielseitige NAT-Erstellung für den Private Link-Datenverkehr durch. Dadurch wird sichergestellt, dass es keinen IP-Konflikt zwischen Quell- (Consumerseite) und Zieladressraum (Dienstanbieter) gibt. Auf der Zielseite (Dienstanbieterseite) wird die NAT-IP-Adresse als Quell-IP-Adresse für alle von Ihrem Dienst empfangenen Pakete und die Ziel-IP-Adresse für alle von Ihrem Dienst gesendeten Pakete angezeigt.       |
|Private Endpunktverbindungen (privateEndpointConnections)     |  Diese Eigenschaft listet die privaten Endpunkte auf, die sich mit dem Private Link-Dienst verbinden. Mehrere private Endpunkte können mit demselben Private Link-Dienst verbunden werden, und der Dienstanbieter kann den Status für einzelne private Endpunkte steuern.        |
|TCP-Proxy V2 (EnableProxyProtocol)     |  Mit dieser Eigenschaft kann der Dienstanbieter den TCP-Proxy V2 zum Abrufen von Verbindungsinformationen über den Dienstconsumer verwenden. Der Dienstanbieter ist für das Einrichten von Empfängerkonfigurationen zuständig, um den Proxy V2-Protokollheader analysieren zu können.        |
|||


### <a name="details"></a>Details

- Auf den Private Link-Dienst kann von genehmigten privaten Endpunkten in einer beliebigen öffentlichen Region aus zugegriffen werden. Der private Endpunkt kann über dasselbe virtuelle Netzwerk, regionale VNets mit Peering, globale VNets mit Peering und lokal über private VPN- oder ExpressRoute-Verbindungen erreicht werden. 
 
- Beim Erstellen eines Private Link-Diensts wird für die Lebensdauer der Ressource eine Netzwerkschnittstelle erstellt. Diese Schnittstelle kann vom Kunden nicht verwaltet werden.
 
- Der Private Link-Dienst muss in derselben Region wie das virtuelle Netzwerk und der Load Balancer Standard bereitgestellt werden.  
 
- Auf einen einzelnen Private Link-Dienst kann von mehreren privaten Endpunkten zugegriffen werden, die zu verschiedenen VNets, Abonnements und/oder Active Directory-Mandanten gehören. Die Verbindung wird über einen Verbindungsworkflow hergestellt. 
 
- Mehrere Private Link-Dienste können auf demselben Load Balancer Standard mit unterschiedlichen Front-End-IP-Konfigurationen erstellt werden. Die Anzahl der Private Link-Dienste, die Sie pro Load Balancer Standard und pro Abonnement erstellen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu  [Azure-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
 
- Der Private Link-Dienst kann mit mehr als einer NAT-IP-Konfiguration verknüpft sein. Die Auswahl von mehreren NAT-IP-Konfigurationen kann Dienstanbietern bei der Skalierung helfen. Heute können Dienstanbieter bis zu acht NAT-IP-Adressen pro Private Link-Dienst zuweisen. Mit jeder NAT-IP-Adresse können Sie mehr Ports für Ihre TCP-Verbindungen zuweisen und auf diese Weise aufskalieren. Nachdem Sie mehrere NAT-IP-Adressen zu einem Private Link-Dienst hinzugefügt haben, können Sie die NAT-IP-Adressen nicht mehr löschen. Dadurch wird sichergestellt, dass beim Löschen der NAT-IP-Adressen die aktiven Verbindungen nicht beeinträchtigt werden.


## <a name="alias"></a>Alias

**Alias** ist ein weltweit eindeutiger Name für Ihren Dienst. Sie hilft Ihnen, die Kundendaten für Ihren Dienst zu maskieren und erstellt gleichzeitig einen einfach zu teilenden Namen für Ihren Dienst. Wenn Sie einen Private Link-Dienst erstellen, generiert Azure einen Alias für Ihren Dienst, den Sie mit Ihren Kunden teilen können. Ihre Kunden können unter diesem Alias eine Verbindung zu Ihrem Dienst anfordern.

Der Alias besteht aus drei Teilen: *Präfix*.*GUID*.*Suffix*

- Das Präfix ist der Dienstname. Sie können ein eigenes Präfix auswählen. Nachdem der „Alias“ erstellt wurde, können Sie ihn nicht mehr ändern, also wählen Sie Ihr Präfix entsprechend aus.  
- Die GUID wird von der Plattform bereitgestellt. Dies trägt dazu bei, den Namen weltweit eindeutig zu gestalten. 
- Das Suffix wird von Azure angefügt: *region*.azure.privatelinkservice 

Vollständiger Alias:  *Präfix*. {GUID}.*region*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Steuern der Dienstoffenlegung

Der Private Link-Dienst bietet Ihnen drei Optionen für die Einstellung **Sichtbarkeit**, um die Sichtbarkeit Ihres Diensts zu steuern. Ihre Sichtbarkeitseinstellung bestimmt, ob ein Benutzer eine Verbindung mit Ihrem Dienst herstellen kann. Im Folgenden finden Sie die Optionen für die Sichtbarkeitseinstellungen, die von der restriktivsten zur am wenigsten restriktivsten geordnet sind:
 
- **Nur rollenbasierte Zugriffssteuerung:** Wenn Ihr Dienst für den privaten Verbrauch über verschiedene VNets konzipiert ist, deren Besitzer Sie sind, können Sie die rollenbasierte Zugriffssteuerung als Zugriffssteuerungsmechanismus in Abonnements verwenden, die demselben AD DS-Mandanten zugeordnet sind. Hinweis: Mandantenübergreifende Sichtbarkeit ist über RBAC zulässig.
- **Eingeschränkt nach Abonnement:** Wenn Ihr Dienst von verschiedenen Mandanten genutzt wird, können Sie die Sichtbarkeit auf eine begrenzte Anzahl von Abonnements beschränken, die Sie als vertrauenswürdig erachten. Autorisierungen können vorab genehmigt werden.
- **Jeder mit Ihrem Alias:** Wählen Sie diese Option aus, wenn Sie Ihren Dienst öffentlich verfügbar machen möchten, sodass jeder mit Ihrem Private Link-Dienstalias eine Verbindung anfordern kann. 

## <a name="control-service-access"></a>Steuern des Dienstzugriffs

Consumer, für die Ihr Private Link-Dienst offengelegt ist (über die Sichtbarkeitseinstellung gesteuert), können in ihren VNets einen privaten Endpunkt erstellen und eine Verbindung zu Ihrem Private Link-Dienst anfordern. Die private Endpunktverbindung wird im Zustand „Ausstehend“ für das Private Link-Dienstobjekt erstellt. Der Dienstanbieter ist für die Bearbeitung der Verbindungsanforderung zuständig. Sie können die Verbindung entweder genehmigen, ablehnen oder löschen. Nur genehmigte Verbindungen können Datenverkehr an den Private Link-Dienst senden.

Die Genehmigung der Verbindungen kann automatisiert werden, indem Sie die Eigenschaft „auto-approval“ (automatische Genehmigung) im Private Link-Dienst verwenden. Die automatische Genehmigung ist eine Möglichkeit für Dienstanbieter, eine Reihe von Abonnements für den automatisierten Zugriff auf ihren Dienst vorab zu genehmigen. Kunden müssen ihre Abonnements offline freigeben, damit Dienstanbieter sie in die Liste der automatischen Genehmigungen aufnehmen können. Die automatische Genehmigung ist eine Teilmenge des Sichtbarkeitsarrays. Die Sichtbarkeit steuert die Einstellungen für die Offenlegung, während die automatische Genehmigung die Genehmigungseinstellungen für Ihren Dienst steuert. Wenn ein Kunde eine Verbindung von einem Abonnement in der automatischen Genehmigungsliste anfordert, wird die Verbindung automatisch genehmigt und hergestellt. Dienstanbieter müssen die Anforderung nicht mehr manuell genehmigen. Wenn ein Kunde hingegen eine Verbindung von einem Abonnement im Sichtbarkeitsarray und nicht im automatischen Genehmigungsarray anfordert, erreicht die Anforderung den Dienstanbieter, aber der Dienstanbieter muss die Verbindungen manuell genehmigen.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Abrufen von Verbindungsinformationen mithilfe von TCP-Proxy v2

Bei Verwendung des Private Link-Diensts ist die Quell-IP-Adresse der von dem privaten Endpunkt kommenden Pakete die NAT (Netzwerkadressenübersetzung) auf der Dienstanbieterseite, die die NAT-IP verwendet, die vom virtuellen Netzwerk des Anbieters zugewiesen wird. Daher erhalten die Anwendungen die zugeordnete NAT-IP-Adresse anstelle der tatsächlichen Quell-IP-Adresse der Dienstconsumer. Wenn Ihre Anwendung die tatsächliche Quell-IP-Adresse von der Consumerseite benötigt, können Sie das Proxyprotokoll in Ihrem Dienst aktivieren und die Informationen aus dem Proxyprotokollheader abrufen. Zusätzlich zur Quell-IP-Adresse enthält der Proxyprotokollheader auch die LinkID des privaten Endpunkts. Die Kombination aus Quell-IP-Adresse und LinkID kann Dienstanbietern bei der eindeutigen Identifizierung ihrer Kunden helfen. Weitere Informationen zum Proxyprotokoll finden Sie [hier](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Diese Informationen werden wie folgt mithilfe eines benutzerdefinierten TLV-Vektors (Type-Length-Value) codiert:

Details zum benutzerdefinierten TLV:

|Feld |Länge (Oktette)  |BESCHREIBUNG  |
|---------|---------|----------|
|type  |1        |PP2_TYPE_AZURE (0xEE)|
|Länge  |2      |Länge des Werts|
|Wert  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 Bytes); stellt die LINKID des privaten Endpunkts dar. Codiert im Little-Endian-Format.|

 > [!NOTE]
 > Der Dienstanbieter ist für die Sicherstellung zuständig, dass der Dienst hinter dem Lastenausgleich im Tarif „Standard“ so konfiguriert ist, dass er den Proxyprotokollheader gemäß der [Spezifikation](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) analysiert, wenn das Proxyprotokoll für den Private Link-Dienst aktiviert ist. Die Anforderung schlägt fehl, wenn die Proxyprotokolleinstellung für den Private Link-Dienst aktiviert ist, aber der Dienst des Dienstanbieters nicht zum Analysieren des Headers konfiguriert ist. Ebenso schlägt die Anforderung fehl, wenn der Dienst des Dienstanbieters einen Proxyprotokollheader erwartet, während die Einstellung für den Private Link-Dienst nicht aktiviert ist. Sobald die Proxyprotokolleinstellung aktiviert ist, wird der Proxyprotokollheader auch in die HTTP/TCP-Integritätstests vom Host zu den virtuellen Back-End-Computern einbezogen, obwohl im Header keine Clientinformationen enthalten sind. 

## <a name="limitations"></a>Einschränkungen

Nachfolgend sind die bekannten Einschränkungen bei der Nutzung des Private Link-Diensts aufgeführt:
- Wird nur von Load Balancer Standard unterstützt Wird nicht von Load Balancer Basic unterstützt  
- Wird nur von Load Balancer Standard unterstützt, wenn der Back-End-Pool über die Netzwerkschnittstelle einer VM oder VMSS konfiguriert wird
- Unterstützt nur IPv4-Datenverkehr
- Unterstützt nur TCP- und UDP-Datenverkehr


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines Private Link-Diensts mit Azure PowerShell](create-private-link-service-powershell.md)
- [Erstellen eines Private Link-Diensts mithilfe der Azure CLI](create-private-link-service-cli.md)

---
title: Replizieren von Daten über ExpressRoute mithilfe des Azure Migrate-Servermigrationstools
description: Verwenden von Azure ExpressRoute für die Replikation mit dem Azure Migrate-Servermigrationstool.
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: c16b4a91f297621fa96e0e18f816d77e9f3b4e2a
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112322402"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replizieren von Daten über ExpressRoute mithilfe des Azure Migrate-Servermigrationstools

In diesem Artikel wird erläutert, wie Sie das [Azure Migrate-Servermigrationstool](./migrate-services-overview.md#azure-migrate-server-migration-tool) zum Replizieren von Daten über eine Azure ExpressRoute-Verbindung konfigurieren, während Sie Server zu Azure migrieren.

## <a name="understand-azure-expressroute-circuits"></a>Informationen zu Azure ExpressRoute-Verbindungen

Über eine ExpressRoute-Verbindung wird Ihre lokale Infrastruktur über einen Konnektivitätsanbieter mit Microsoft verbunden. Sie können ExpressRoute-Verbindungen konfigurieren, um privates Peering, Microsoft-Peering oder beides zu verwenden. Weitere Informationen zu den Peeringoptionen mit ExpressRoute finden Sie unter [ExpressRoute-Verbindungen und Peering](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

Das Azure Migrate-Servermigrationstool unterstützt Sie beim Migrieren von lokalen Servern und Servern von anderen Clouds zu Azure Virtual Machines. Das Tool richtet einen kontinuierlichen Replikationsstream zum Replizieren von Daten von den zu migrierenden Servern zu den verwalteten Datenträgern in Ihrem Azure-Abonnement ein. Wenn Sie bereit sind, die Server zu migrieren, werden die replizierten Daten in Azure zum Migrieren der Server verwendet.

Sie können die von Ihren lokalen Servern replizierten Daten so konfigurieren, dass sie über das Internet oder eine ExpressRoute-Verbindung an Ihr Azure-Abonnement gesendet werden. Über das Internet gesendete Daten verwenden eine sichere verschlüsselte Verbindung. Wenn Sie über eine große Anzahl von zu migrierenden Servern verfügen, können Sie ExpressRoute für die Replikation verwenden, um eine effizientere Migration unter Verwendung der bereitgestellten Bandbreite ausführen, die über Ihre ExpressRoute-Verbindung verfügbar ist.

In diesem Artikel erfahren Sie, wie Sie Daten mithilfe der folgenden Mechanismen replizieren:
> [!div class="checklist"]
>
> * Mit einer ExpressRoute-Verbindung mit privatem Peering.
> * Mit einer ExpressRoute-Verbindung mit Microsoft-Peering.

## <a name="replicate-data-by-using-an-expressroute-circuit-with-private-peering"></a>Replizieren von Daten mithilfe einer ExpressRoute-Verbindung mit privatem Peering

> [!Note]
> Dieser Artikel zeigt, wie die Replikation über eine private Peeringverbindung für die [Migration von virtuellen VMware-Computern ohne Agent zu Azure](./tutorial-migrate-vmware.md) funktioniert. Um Unterstützung für private Endpunkte für [andere Replikationsmethoden](./migrate-services-overview.md#azure-migrate-server-migration-tool) zu verwenden, lesen Sie [Verwenden von Azure Migrate mit privaten Endpunkten](./how-to-use-azure-migrate-with-private-endpoints.md).
 
Bei der Methode zum Migrieren von virtuellen VMware-Computern zu Azure ohne Agent lädt die Azure Migrate-Appliance zunächst Replikationsdaten in ein Speicherkonto (Cachespeicherkonto) in Ihrem Abonnement hoch. Azure Migrate verschiebt dann die replizierten Daten aus dem Cachespeicherkonto auf durch das Replikat verwaltete Datenträger in Ihrem Abonnement.

Wenn Sie eine private Peeringverbindung für die Replikation verwenden möchten, erstellen Sie einen privaten Endpunkt und fügen diesen an das Cachespeicherkonto an. Private Endpunkte verwenden eine oder mehrere private IP-Adressen Ihres virtuellen Netzwerks, sodass das Speicherkonto effektiv in Ihr virtuelles Azure-Netzwerk integriert wird. Der private Endpunkt ermöglicht der Azure Migrate-Appliance, mithilfe des privaten ExpressRoute-Peerings eine Verbindung mit dem Cachespeicherkonto herzustellen. Daten können dann direkt an die private IP-Adresse übertragen werden. <br/>

![Screenshot: Replikationsprozess.](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
> - Die Azure Migrate-Appliance kommuniziert mit dem Azure Migrate-Dienst, um zusätzlich zu den Replikationsdaten Informationen zu den Aktivitäten der Steuerungsebene zu erhalten. Zu diesen Aktivitäten gehört die Orchestrierung der Replikation. Die Steuerungsebenenkommunikation zwischen der Azure Migrate-Appliance und dem Azure Migrate-Dienst erfolgt weiterhin über das Internet und den öffentlichen Endpunkt des Azure Migrate-Diensts.
> - Der private Endpunkt des Speicherkontos sollte über das Netzwerk zugänglich sein, in dem die Azure Migrate-Appliance bereitgestellt wird.
> - Das DNS muss so konfiguriert sein, dass DNS-Abfragen von der Azure Migrate-Appliance für den Blobdienstendpunkt des Cachespeicherkontos in die private IP-Adresse des privaten Endpunkts aufgelöst werden, der mit dem Cachespeicherkonto verknüpft ist.
> - Der Zugriff auf das Cachespeicherkonto muss über dessen öffentlichen Endpunkt möglich sein. Azure Migrate verwendet den öffentlichen Endpunkt des Cachespeicherkontos zum Verschieben von Daten aus dem Speicherkonto auf vom Replikat verwaltete Datenträger.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen für die Ressourcengruppe und das virtuelle Netzwerk, in dem die privaten Endpunkte erstellt werden, über die folgenden Berechtigungen verfügen.

Anwendungsfall | Berechtigungen
--- | --- 
 Erstellen und Verwalten privater Endpunkte | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action 
|Fügen Sie einen privaten Endpunkt an ein virtuelles Netzwerk oder Subnetz an.<br/>Diese Berechtigung ist für das virtuelle Netzwerk erforderlich, in dem der private Endpunkt erstellt wird.| Microsoft.Network/virtualNetworks/subnet/join/action <br/> Microsoft.Network/virtualNetworks/join/action
|Verknüpfen des privaten Endpunkts mit einem Speicherkonto <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|Erstellen einer Netzwerkschnittstelle und Verknüpfen mit einer Netzwerksicherheitsgruppe | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (optional)
Erstellen und Verwalten privater DNS-Zonen| Rolle „Mitwirkender für private DNS-Zone“ <br/> _Oder_ <br/> Microsoft.Network/privateDnsZones/A/* <br/> Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="identify-the-cache-storage-account"></a>Identifizieren des Cachespeicherkontos

 Azure Migrate erstellt automatisch ein Cachespeicherkonto, wenn Sie die Replikation (über die Benutzeroberfläche des Azure-Portals) für einen virtuellen Computer zum ersten Mal in einem Azure Migrate-Projekt konfigurieren. Das Speicherkonto wird in demselben Abonnement und in derselben Ressourcengruppe erstellt, in dem bzw. der Sie das Azure Migrate-Projekt erstellt haben.

So erstellen und suchen Sie das Speicherkonto:

1. Verwenden Sie das Azure-Portal, um mindestens einen virtuellen Computer im Azure Migrate-Projekt zu replizieren.
1. Navigieren Sie zur Ressourcengruppe des Azure Migrate-Projekts.
1. Suchen Sie das Cachespeicherkonto, indem Sie das Präfix **Isa** im Speicherkontonamen ermitteln.

   ![Screenshot: Ressourcengruppenansicht.](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Wenn Sie in der Ressourcengruppe über mehrere Speicherkonten mit dem Präfix **Isa** verfügen, können Sie das Speicherkonto überprüfen, indem Sie zu den Replikationseinstellungen und dem Zielkonfigurationsmenü für jede der replizierenden VMs im Projekt navigieren.
>
> ![Screenshot: Übersicht über Replikationseinstellungen.](./media/replicate-using-expressroute/storage-account.png)

### <a name="upgrade-the-cache-storage-account-to-general-purpose-v2"></a>Durchführen eines Upgrades des Cachspeicherkontos auf „Universell v2“

Sie können private Endpunkte nur für ein Speicherkonto des Typs „Universell v2“ erstellen. Wenn es sich bei dem Cachespeicherkonto nicht um ein Speicherkonto des Typs „Universell v2“ handelt, führen Sie ein Upgrade aus.

1. Wechseln Sie zum Speicherkonto.
1. Wählen Sie **Konfiguration** aus.
1. Klicken Sie unter **Kontoart** auf **Upgrade durchführen**.
1. Geben Sie unter **Upgrade bestätigen** den Namen Ihres Kontos ein.
1. Klicken Sie unten auf der Seite auf **Upgrade durchführen**.

   ![Screenshot: Durchführen eines Upgrades eines Speicherkontos.](./media/replicate-using-expressroute/upgrade-storage-account.png) 

### <a name="create-a-private-endpoint-for-the-storage-account"></a>Erstellen eines privaten Endpunkts für das Speicherkonto

1. Navigieren Sie zu Ihrem Speicherkonto, klicken Sie im Menü auf der linken Seite auf **Netzwerk**, und klicken Sie dann auf die Registerkarte **Private Endpunktverbindungen**.
1. Wählen Sie **+ Privaten Endpunkt hinzufügen** aus.

    1. Wählen Sie im Fenster **Privaten Endpunkt erstellen** das **Abonnement** und die **Ressourcengruppe** aus. Geben Sie einen Namen für Ihren privaten Endpunkt ein, und wählen Sie die Speicherkontoregion aus.
     
       ![Screenshot: Fenster für die Konfiguration eines privaten Endpunkts.](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    1. Geben Sie auf der Registerkarte **Ressource** den **Abonnementnamen** des Abonnements ein, in dem sich das Speicherkonto befindet. Wählen Sie **Microsoft.Storage/storageAccounts** als **Ressourcentyp** aus. Geben Sie unter **Ressource** den Namen des Replikationsspeicherkontos vom Typ „Universell v2“ an. Wählen Sie für **Untergeordnete Zielressource** die Option **Blob** aus.
     
       ![Screenshot: Einstellungen für den privaten Endpunkt des Speicherkontos.](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    1. Wählen Sie auf der Registerkarte **Konfiguration** die Optionen für **Virtuelles Netzwerk** und **Subnetz** für den privaten Endpunkt des Speicherkontos aus.

       > [!Note]
       > Das virtuelle Netzwerk muss den ExpressRoute-Gatewayendpunkt enthalten oder eine Verbindung mit dem virtuellen Netzwerk mit dem ExpressRoute-Gateway aufweisen.

       Wählen Sie im Abschnitt **Private DNS-Integration** **Ja** aus, und führen Sie die Integration mit einer privaten DNS-Zone aus. Wenn Sie **Ja** auswählen, wird die DNS-Zone automatisch mit dem ausgewählten virtuellen Netzwerk verknüpft. Außerdem werden die für DNS-Auflösung neuer IP-Adressen erforderliche DNS-Einträge und vollqualifizierte Domänennamen hinzugefügt, die für den privaten Endpunkt erstellt wurden. Informieren Sie sich weiter über [private DNS-Zonen](../dns/private-dns-overview.md).

       ![Screenshot: Private DNS-Zonen.](./media/replicate-using-expressroute/private-dns-zone.png)

    1. Sie können auch **Tags** für Ihren privaten Endpunkt hinzufügen.

    1. Nachdem Sie die Eingabe der Details abgeschlossen haben, wählen Sie die Registerkarte **Überprüfen und erstellen** aus. Wählen Sie nach Abschluss der Überprüfung **Erstellen** aus, um den privaten Endpunkt zu erstellen.

> [!Note]
> Wenn der Benutzer, der den privaten Endpunkt erstellt hat, auch Besitzer des Speicherkontos ist, wird der private Endpunkt automatisch genehmigt. Andernfalls muss der Besitzer den privaten Endpunkt für die Verwendung genehmigen.

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Erstellen privater DNS-Zonen und manuelles Hinzufügen von DNS-Einträgen (optional)

Wenn Sie die Option zur Integration in eine private DNS-Zone zum Zeitpunkt der Erstellung des privaten Endpunkts nicht ausgewählt haben, müssen Sie eine private DNS-Zone manuell erstellen.

> [!Note]
> Wenn Sie für die Integration mit einer privaten DNS-Zone **Ja** ausgewählt haben, können Sie diesen Abschnitt überspringen.

So erstellen Sie eine private DNS-Zone:

1. Wählen Sie **Private DNS-Zonen** aus.

    ![Screenshot: Erstellen einer privaten DNS-Zone.](./media/replicate-using-expressroute/create-private-dns.png)

    1. Wählen Sie auf der Seite **Private DNS-Zonen** die Option **+ Hinzufügen** aus, um eine neue Zone zu erstellen.
    1. Geben Sie auf der Seite **Private DNS-Zone erstellen** die erforderlichen Details ein. Geben Sie **_privatelink_.blob.core.windows.net** als Namen für die private DNS-Zone ein.
    1. Überprüfen und erstellen Sie die DNS-Zone anschließend auf der Registerkarte **Überprüfen und erstellen**.

1. Verknüpfen Sie die private DNS-Zone mit Ihrem virtuellen Netzwerk.

    Die von Ihnen erstellte private DNS-Zone muss mit dem virtuellen Netzwerk verknüpft werden, an das der private Endpunkt angefügt ist.

    1. Navigieren Sie zu der im vorherigen Schritt erstellten privaten DNS-Zone, und navigieren Sie dann auf der linken Seite zu „VNet-Verknüpfungen“. Wählen Sie **+ Hinzufügen**.
    1. Geben Sie die erforderlichen Details ein. Die Felder **Abonnement** und **Virtuelles Netzwerk** müssen mit entsprechenden Angaben zum virtuellen Netzwerk ausgefüllt werden, an das Ihr privater Endpunkt angefügt ist. Die anderen Felder können Sie unverändert lassen.

1. Der nächste Schritt besteht darin, der DNS-Zone DNS-Einträge hinzuzufügen. Fügen Sie einen Eintrag für den FQDN des Speicherkontos in Ihre private DNS-Zone ein.

    1. Navigieren Sie zu Ihrer privaten DNS-Zone, und navigieren Sie auf der linken Seite zum Abschnitt **Übersicht**. Wählen Sie **+ Ressourceneintraggsatz** aus, um mit dem Hinzufügen von Einträgen zu beginnen.
    1. Fügen Sie auf der Seite **Datensatzgruppe hinzufügen** einen Eintrag für den FQDN und die private IP-Adresse als A-Eintrag hinzu.

> [!Important]
> Möglicherweise benötigen Sie zusätzliche DNS-Einstellungen, um die private IP-Adresse des privaten Endpunkts des Speicherkontos aus der Quellumgebung aufzulösen. Informationen zur erforderlichen DNS-Konfiguration finden Sie unter [DNS-Konfiguration des privaten Azure-Endpunkts](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).  

### <a name="verify-network-connectivity-to-the-storage-account"></a>Die Netzwerkkonnektivität des Speicherkontos verifizieren

Um die private Verbindung zu überprüfen, führen Sie von der lokal ausgeführten, die Azure Migrate-Appliance hostenden VM eine DNS-Auflösung für den Ressourcen-Endpunkt des Cachespeicherkontos aus und stellen Sie sicher, dass dabei eine private IP-Adresse zurückgegeben wird.

Ein anschauliches Beispiel für die DNS-Auflösung des Cachespeicherkontos. 

- „nslookup _Speicherkontoname_.blob.core.windows.net“ eingeben. Ersetzen Sie dabei <Speicherkontoname> durch den Namen des von Azure Migrate erstellten Cachespeicherkontos.  

    Sie erhalten eine Meldung ähnlich der folgenden:  

   ![Beispiel für die DNS-Auflösung](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Für das Speicherkonto wird als private IP-Adresse 10.1.0.5 zurückgegeben. Diese Adresse sollte zum privaten Endpunkt des Speicherkontos gehören. 

Führen Sie bei einer nicht korrekten DNS-Auflösung die folgenden Schritte aus:  

- **Tipp:** Sie können die DNS-Einträge Ihrer Quellumgebung manuell aktualisieren, indem Sie die DNS-Hostdatei auf Ihrer lokalen Appliance mit dem FQDN-Link des Speicherkontos, „_Speicherkontoname_.blob.core.windows.net“ und der zugehörigen privaten IP-Adresse bearbeiten. Diese Option wird nur für Testzwecke empfohlen. 
- Wenn Sie ein benutzerdefiniertes DNS verwenden, überprüfen Sie dessen Einstellungen und vergewissern sich, dass die DNS-Konfiguration korrekt ist. Einen entsprechenden Leitfaden finden Sie unter [Was ist privater Endpunkt in Azure? – DNS-Konfiguration](../private-link/private-endpoint-overview.md#dns-configuration). 
- Wenn Sie von Azure bereitgestellte DNS-Server verwenden, verwenden Sie diesen Leitfaden als Referenz [für die weitere Problembehandlung](./troubleshoot-network-connectivity.md#validate-the-private-dns-zone).   

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-expressroute-private-peering-connectivity"></a>Konfigurieren von Proxyumgehungsregeln auf der Azure Migrate-Appliance (für private ExpressRoute-Peeringkonnektivität) 
Für Proxyumgehung können Sie wie folgt eine Proxyumgehungsregel für das Cachespeicherkonto hinzufügen: 
- _speicherkontoname_.blob.core.windows.net.

> [!Important]
>  Umgehen Sie *.blob.core.windows.net nicht, da Azure Migrate ein anderes Speicherkonto verwendet, das Internetzugriff benötigt. Dieses Speicherkonto (Gatewayspeicherkonto) wird nur verwendet, um Statusinformationen zu den replizierten VMs zu speichern. Sie können das Gatewayspeicherkonto ermitteln, indem Sie das Präfix _**gwsa**_ im Speicherkontonamen in der Azure Migrate-Projektressourcengruppe identifizieren. 

## <a name="replicate-data-by-using-an-expressroute-circuit-with-microsoft-peering"></a>Replizieren von Daten mithilfe einer ExpressRoute-Verbindung mit Microsoft-Peering

Sie können das Microsoft-Peering oder eine vorhandene öffentliche Peeringdomäne verwenden (für neue ExpressRoute-Verbindungen veraltet), um den Replikationsdatenverkehr über eine ExpressRoute-Verbindung weiterzuleiten.

![Abbildung: Replikation mit Microsoft-Peering.](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Auch wenn Replikationsdaten über die Peeringverbindung von Microsoft übertragen werden, benötigen Sie trotzdem Internetkonnektivität vom lokalen Standort für Datenverkehr auf Steuerungsebene und andere URLs, die über ExpressRoute nicht erreichbar sind. Die Replikationsappliance oder der Hyper-V-Host benötigt Zugriff auf die URLs, um den Replikationsprozess zu orchestrieren. Überprüfen Sie die auf den Migrationsszenarien [VMware-Migrationen ohne Agent](./migrate-appliance.md#public-cloud-urls) und [Agent-basierte Replikation](./migrate-replication-appliance.md) basierenden URL-Anforderungen. 

 Konfigurieren Sie für die Replikationsdatenübertragung über Microsoft-Peering, und konfigurieren Sie Routenfilter, um Routen für die Azure Storage-Endpunkte anzukündigen. Dies sind die regionalen BGP-Communitys für die Azure-Zielregion (Region für die Migration). Konfigurieren Sie Routenfilter zum Ankündigen von Routen für andere öffentliche Endpunkte nach Bedarf, um Datenverkehr auf Steuerungsebene über Microsoft-Peering weiterzuleiten.  

- Regionale BGP-Community für die Azure-Quellregion (Azure Migrate-Projektbereich)
- Regionale BGP-Community für die Azure-Zielregion (Region für die Migration)
- BGP-Community für Azure Active Directory (12076:5060)

Informieren Sie sich weiter über [Routenfilter](../expressroute/how-to-routefilter-portal.md), und sehen Sie sich die Liste der [BGP-Communitys für ExpressRoute](../expressroute/expressroute-routing.md#bgp) an.

### <a name="proxy-configuration-for-expressroute-microsoft-peering"></a>Proxykonfiguration für ExpressRoute-Microsoft-Peering

Wenn die Appliance einen Proxy für Internetkonnektivität verwendet, müssen Sie möglicherweise Proxyumgehung für bestimmte URLs konfigurieren, um sie über die Microsoft-Peeringverbindung weiterzuleiten. 

#### <a name="configure-proxy-bypass-rules-for-expressroute-microsoft-peering-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Konfigurieren von Proxyumgehungsregeln für ExpressRoute-Microsoft-Peering auf der Azure Migrate-Appliance (für VMware-Migrationen ohne Agent)

1. Melden Sie sich über Remotedesktop bei der Azure Migrate-Appliance an.
2.  Öffnen Sie mithilfe von Editor die Datei *C:/ProgramData/MicrosoftAzure/Config/appliance.json*.
3. Ändern Sie in der Datei die Zeile `"EnableProxyBypassList": "false"` in `"EnableProxyBypassList": "true"`. Speichern Sie die Änderungen, und starten Sie die Appliance neu.
4. Wenn Sie nach dem Neustart den Konfigurations-Manager der Appliance öffnen, werden in der Benutzeroberfläche der Web-App die Proxyumgehungsoptionen angezeigt. 
5. Für Replikationsdatenverkehr können Sie eine Proxyumgehungsregel für „.*.blob.core.windows.net“ konfigurieren. Sie können Proxyumgehungsregeln für andere Endpunkte auf Steuerungsebene nach Bedarf konfigurieren. Zu diesen Endpunkten gehören: 

    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com

> [!Note]
> Auf die folgenden URLs kann nicht über ExpressRoute zugegriffen werden, und sie erfordern Internetverbindung: *.portal.azure.com, *.windows.net, *.msftauth.net, *.msauth.net, *.microsoft.com, *.live.com, *.office.com, *.microsoftonline.com, *.microsoftonline-p.com, *.microsoftazuread-sso.com, management.azure.com, *.services.visualstudio.com (optional), aka.ms/* (optional), download.microsoft.com/download.


#### <a name="configure-proxy-bypass-rules-expressroute-microsoft-peering-on-the-replication-appliance-for-agent-based-migrations"></a>Konfigurieren von Proxyumgehungsregeln für ExpressRoute-Microsoft-Peering auf der Replikationsappliance (für Migrationen mit Agent)

Führen Sie die folgenden Schritte aus, um die Proxyumgehungsliste auf dem Konfigurationsserver und den Prozessservern zu konfigurieren:

1. Laden Sie das [PsExec-Tool](/sysinternals/downloads/psexec) herunter, um auf Systembenutzerkontext zuzugreifen.
2. Öffnen Sie Internet Explorer im Systembenutzerkontext, indem Sie den folgenden Befehl in einer Eingabeaufforderung ausführen: `psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"`.
3. Fügen Sie Proxyeinstellungen in Internet Explorer hinzu.
4. Für Replikationsdatenverkehr können Sie eine Proxyumgehungsregel für „.*.blob.core.windows.net“ konfigurieren. Sie können Proxyumgehungsregeln für andere Endpunkte auf Steuerungsebene nach Bedarf konfigurieren. Zu diesen Endpunkten gehören: 

    - .*.backup.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com

Mit den Umgehungsregeln für den Azure Storage-Endpunkt wird sichergestellt, dass der Replikationsdatenverkehr über ExpressRoute weitergeleitet werden kann, während die Kommunikation auf Steuerungsebene über den Proxyserver für das Internet erfolgt. 

> [!Note]
> Auf die folgenden URLs kann nicht über ExpressRoute zugegriffen werden, und sie erfordern Internetverbindung: *.portal.azure.com, *.windows.net, *.msftauth.net, *.msauth.net, *.microsoft.com, *.live.com, *.office.com, *.microsoftonline.com, *.microsoftonline-p.com, *.microsoftazuread-sso.com, management.azure.com, *.services.visualstudio.com (optional), aka.ms/* (optional), download.microsoft.com/download, dev.mysql.com.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [ExpressRoute-Verbindungen](../expressroute/expressroute-circuit-peerings.md)
- [ExpressRoute-Routingdomänen](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- [Private Endpunkte](../private-link/private-endpoint-overview.md)

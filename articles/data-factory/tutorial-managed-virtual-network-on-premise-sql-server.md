---
title: Zugreifen auf eine lokale SQL Server-Instanz über ein verwaltetes Data Factory-VNet unter Verwendung eines privaten Endpunkts
description: In diesem Tutorial werden die Schritte für die Verwendung des Azure-Portals zum Einrichten des Private Link-Diensts und zum Zugreifen auf eine lokale SQL Server-Instanz über ein verwaltetes VNet mithilfe eines privaten Endpunkts erläutert.
author: lrtoyou1223
ms.author: lle
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 05/06/2021
ms.openlocfilehash: d924abc214d01cc4e981d7398a6a250753cb50a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605029"
---
# <a name="tutorial-how-to-access-on-premises-sql-server-from-data-factory-managed-vnet-using-private-endpoint"></a>Tutorial: Zugreifen auf eine lokale SQL Server-Instanz über ein verwaltetes Data Factory-VNet unter Verwendung eines privaten Endpunkts

In diesem Tutorial werden die Schritte für die Verwendung des Azure-Portals zum Einrichten des Private Link-Diensts und zum Zugreifen auf eine lokale SQL Server-Instanz über ein verwaltetes VNet mithilfe eines privaten Endpunkts erläutert.

> [!NOTE]
> Mit der in diesem Artikel vorgestellten Lösung wird SQL Server-Konnektivität beschrieben, Sie können einen ähnlichen Ansatz aber auch verwenden, um andere verfügbare [lokale Connectors](connector-overview.md) zu verbinden und abzufragen, die in Azure Data Factory unterstützt werden.

:::image type="content" source="./media/tutorial-managed-virtual-network/sql-server-access-model.png" alt-text="Screenshot: Zugriffsmodell von SQL Server" lightbox="./media/tutorial-managed-virtual-network/sql-server-access-model-expanded.png":::

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Virtuelles Netzwerk**. Wenn Sie kein virtuelles Netzwerk besitzen, erstellen Sie eins anhand der Anweisungen unter [Schnellstart: Erstellen eines virtuellen Netzwerks im Azure-Portal](../virtual-network/quick-create-portal.md).
* **Virtuelles Netzwerk zu lokalem Netzwerk:** Erstellen Sie über [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=/azure/virtual-network/toc.json) oder [VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=/azure/virtual-network/toc.json) eine Verbindung zwischen dem virtuellen Netzwerk und dem lokalen Netzwerk.
* **Data Factory mit aktiviertem verwalteten VNet:** Wenn Sie keine Data Factory-Instanz besitzen oder das verwaltete VNet nicht aktiviert ist, erstellen Sie anhand der Anweisungen unter [Sicheres Kopieren von Daten aus Azure Blob Storage in eine SQL-Datenbank mithilfe von privaten Endpunkten](tutorial-copy-data-portal-private.md) eine entsprechende Instanz.

## <a name="create-subnets-for-resources"></a>Erstellen von Subnetzen für Ressourcen

**Verwenden Sie das Portal, um Subnetze in Ihrem virtuellen Netzwerk zu erstellen.**

| Subnet | Beschreibung |
|:--- |:--- |
|be-subnet |Subnetz für Back-End-Server|
|fe-subnet |Subnetz für einen internen Standard-Lastenausgleich|
|pls-subnet|Subnetz für den Private Link-Dienst|

:::image type="content" source="./media/tutorial-managed-virtual-network/subnets.png" alt-text="Screenshot: Subnetze" lightbox="./media/tutorial-managed-virtual-network/subnets-expanded.png":::

## <a name="create-a-standard-load-balancer"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz mit Azure PowerShell

Erstellen Sie über das Portal einen internen Lastenausgleich im Tarif „Standard“.

1. Wählen Sie links oben auf dem Bildschirm die Optionen **Ressource erstellen > Netzwerk > Lastenausgleich** aus.
2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    |:--- |:--- |
    |Subscription|Wählen Sie Ihr Abonnement aus.|
    |Ressourcengruppe|Wählen Sie Ihre Ressourcengruppe aus.|
    |Name|Geben Sie **myLoadBalancer** ein.|
    |Region|Wählen Sie **USA, Osten** aus.|
    |type|Wählen Sie **Intern** aus.|
    |SKU|Wählen Sie **Standard** aus.|
    |Virtuelles Netzwerk|Wählen Sie Ihr virtuelles Netzwerk aus.|
    |Subnet|Wählen Sie das im vorherigen Schritt erstellte Subnetz **fe-subnet** aus.|
    |IP-Adresszuweisung|Wählen Sie **Dynamisch** aus.|
    |Verfügbarkeitszone|Wählen Sie **Zonenredundant** aus.|

3. Übernehmen Sie bei den anderen Einstellungen die Standardwerte, und wählen Sie **Überprüfen + erstellen** aus.
4. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus.

    :::image type="content" source="./media/tutorial-managed-virtual-network/create-load-balancer.png" alt-text="Screenshot: Schritt zum Erstellen des Standard-Lastenausgleichs":::

## <a name="create-load-balancer-resources"></a>Erstellen von Load Balancer-Ressourcen

### <a name="create-a-backend-pool"></a>Erstellen eines Back-End-Pools

Der Back-End-Adresspool enthält die IP-Adressen der virtuellen NICs, die mit dem Lastenausgleich verbunden sind.

Erstellen Sie den Back-End-Adresspool **myBackendPool**, um virtuelle Computer für den Lastenausgleich von Internetdatenverkehr einzubeziehen.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.
2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen** aus.
3. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die Zeichenfolge **myBackendPool** als Name für Ihren Back-End-Pool ein, und wählen Sie anschließend **Hinzufügen** aus.

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Der Status Ihrer App wird vom Lastenausgleich mithilfe eines Integritätstests überwacht.

Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Instanz durch den Integritätstest virtuelle Computer hinzugefügt oder daraus entfernt.

Erstellen Sie zur Überwachung der Integrität der virtuellen Computer einen Integritätstest mit dem Namen **myHealthProbe**.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.
2. Wählen Sie unter **Einstellungen** die Option **Integritätstests** und dann **Hinzufügen** aus.

    | Einstellung | Wert |
    |:--- |:--- |
    |Name|Geben Sie **myHealthProbe** ein.|
    |Protocol|Wählen Sie **TCP** aus.|
    |Port|Geben Sie „22“ ein.|
    |Intervall|Geben Sie für das **Intervall** den Wert **15** (Sekunden zwischen Testversuchen) ein.|
    |Fehlerhafter Schwellenwert|Wählen Sie **2** als Wert für den **Fehlerschwellenwert** bzw. als Anzahl aufeinander folgender Testfehler aus, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.|

3. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus.

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool für den Empfang des Datenverkehrs. Quell- und Zielport werden in der Regel definiert.

In diesem Abschnitt wird eine Lastenausgleichsregel mit folgenden Merkmalen erstellt:

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.
2. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** und dann **Hinzufügen** aus.
3. Konfigurieren Sie die Lastenausgleichsregel mit folgenden Werten:

    | Einstellung | Wert |
    |:--- |:--- |
    |Name|Geben Sie **myRule** ein.|
    |IP-Version|Wählen Sie **IPv4** aus.|
    |Front-End-IP-Adresse|Wählen Sie **LoadBalancerFrontEnd** aus.|
    |Protocol|Wählen Sie **TCP** aus.|
    |Port|Geben Sie **1433** ein.|
    |Back-End-Port|Geben Sie **1433** ein.|
    |Back-End-Pool|Wählen Sie **myBackendPool** aus.|
    |Integritätstest|Wählen Sie **myHealthProbe** aus.|
    |Leerlaufzeitüberschreitung (Minuten)|Bewegen Sie den Schieberegler auf **15 Minuten**.|
    |TCP-Zurücksetzung|Wählen Sie **Deaktiviert** aus.|

4. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie dann **OK** aus.

## <a name="create-a-private-link-service"></a>Erstellen eines Private Link-Diensts

In diesem Abschnitt erstellen Sie einen Private Link-Dienst hinter einem Standardlastenausgleich.

1. Wählen Sie im Azure-Portal oben links auf der Seite die Option **Ressource erstellen** aus.
2. Suchen Sie im Feld **Marketplace durchsuchen** nach **Private Link**.
3. Klicken Sie auf **Erstellen**.
4. Wählen Sie in der **Übersicht** unter **Private Link Center** die blaue Schaltfläche **Create private link service** (Private Link-Dienst erstellen) aus.
5. Geben Sie auf der Registerkarte **Grundlagen** unter **Create private link service** (Private Link-Dienst erstellen) die folgenden Informationen ein, bzw. wählen Sie sie aus:

    |Einstellung |Wert |
    |---------|--------|
    |**Projektdetails**||
    |Subscription |Wählen Sie Ihr Abonnement aus.|
    |Ressourcengruppe |Wählen Sie Ihre Ressourcengruppe aus.|
    |**Instanzendetails**||
    |Name  |Geben Sie **myPrivateLinkService** ein.|
    |Region  |Wählen Sie **USA, Osten** aus.|

6. Wählen Sie die Registerkarte **Ausgangseinstellungen** oder unten auf der Seite die Option **Weiter: Ausgangseinstellungen** aus.
7. Geben Sie auf der Registerkarte **Ausgangseinstellungen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    |:--- |:--- |
    |Load Balancer|Wählen Sie **myLoadBalancer** aus.|
    |Front-End-IP-Adresse des Lastenausgleichs|Wählen Sie **LoadBalancerFrontEnd** aus.|
    |NAT-Quellsubnetz|Wählen Sie **pls-subnet** aus.|
    |TCP-Proxy V2 aktivieren|Übernehmen Sie den Standardwert **Nein**.|
    |**Einstellungen für private IP-Adressen**||
    |Übernehmen Sie die Standardeinstellungen.||

8. Wählen Sie die Registerkarte **Zugriffssicherheit** oder unten auf der Seite die Option **Weiter: Zugriffssicherheit** aus.
9. Übernehmen Sie auf der Registerkarte **Zugriffssicherheit** die Standardeinstellung **Nur rollenbasierte Zugriffssteuerung**.
10. Wählen Sie die Registerkarte **Tags** aus, oder **Weiter: Tags** unten auf der Seite.
11. Wählen Sie die Registerkarte **Überprüfen und erstellen** oder unten auf der Seite die Option **Weiter: Überprüfen + erstellen** aus.
12. Wählen Sie auf der Registerkarte **Überprüfen und erstellen** die Option **Erstellen** aus.


## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

1. Navigieren Sie links oben im Portal zu **Ressource erstellen > Compute > Virtueller Computer**.
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    |Einstellung |Wert|
    |---------|--------|
    |**Projektdetails**||
    |Subscription |Wählen Sie Ihr Azure-Abonnement.|
    |Ressourcengruppe |Wählen Sie Ihre Ressourcengruppe aus.|
    |**Instanzendetails**||
    |Name des virtuellen Computers  |Geben Sie **myVM1** ein.|
    |Region  |Wählen Sie **USA, Osten** aus.|
    |Verfügbarkeitsoptionen  |Wählen Sie **Verfügbarkeitszonen** aus.|
    |Verfügbarkeitszone  |Wählen Sie **1**.| 
    |Image  |Wählen Sie **Ubuntu Server 18.04 LTS – Gen1** aus.| 
    |Azure Spot-Instanz  |Wählen Sie **Nein** aus.| 
    |Size   |Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung.| 
    |**Administratorkonto**||
    |Username |Geben Sie einen Benutzernamen ein.|
    |Quelle für öffentlichen SSH-Schlüssel  |Generieren Sie ein neues Schlüsselpaar.|
    |Schlüsselpaarname  |mySSHKey|    
    |**Regeln für eingehende Ports**||
    |Öffentliche Eingangsports |Keine|   

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung |Wert|
    |---------|--------|
    |**Netzwerkschnittstelle**||
    |Virtuelles Netzwerk |Wählen Sie Ihr virtuelles Netzwerk aus.|
    |Subnet |**be-subnet**|
    |Öffentliche IP-Adresse |Wählen Sie **Keine** aus.|
    |NIC-Netzwerksicherheitsgruppe |Wählen Sie **Keine**.|
    |**Lastenausgleich**||
    |Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren?|Wählen Sie **Ja** aus.|
    |**Lastenausgleichseinstellungen**||
    |Optionen für den Lastenausgleich |Wählen Sie **Azure-Lastenausgleich** aus.|
    |Wählen Sie einen Lastenausgleich aus. |Wählen Sie **myLoadBalancer** aus.|
    |Wählen Sie einen Back-End-Pool aus. |Wählen Sie **myBackendPool** aus.|    

5. Klicken Sie auf **Überprüfen + erstellen**.
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.
7. Sie können die Schritte 1 bis 6 wiederholen, um mehr als eine Back-End-Server-VM für Hochverfügbarkeit zu erhalten.

## <a name="creating-forwarding-rule-to-endpoint"></a>Erstellen einer Weiterleitungsregel an einen Endpunkt

1. Melden Sie sich an, und kopieren Sie das Skript [ip_fwd.sh](https://github.com/sajitsasi/az-ip-fwd/blob/main/ip_fwd.sh) auf Ihre Back-End-Server-VMs. 
2. Führen Sie das Skript mit den folgenden Optionen aus:<br/>
    **sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433**<br/>
    <FQDN/IP> ist die Ziel-IP-Adresse Ihrer SQL Server-Instanz.<br/>
    
    > [!Note] 
    > Wenn Sie einen FQDN für eine lokale SQL Server-Instanz verwenden möchten, müssen Sie einen Eintrag in der Azure DNS-Zone hinzufügen.
    
3. Führen Sie den folgenden Befehl aus, und überprüfen Sie die IP-Tabellen (iptables) auf Ihren Back-End-Server-VMs. Ihre IP-Tabellen enthalten einen einzelnen Eintrag mit Ihrer Ziel-IP-Adresse.<br/>
    **sudo iptables -t nat -v -L PREROUTING -n --line-number**

    :::image type="content" source="./media/tutorial-managed-virtual-network/command-record-1.png" alt-text="Screenshot: Befehlsdatensatz":::

    >[!Note]
    > Wenn Sie über mehrere SQL Server-Instanzen oder Datenquellen verfügen, müssen Sie mehrere Lastenausgleichsregeln und IP-Tabelleneinträge mit unterschiedlichen Ports definieren. Andernfalls kommt es zu einem Konflikt. Beispiel:<br/>
    >
    >|                  |Port in Lastenausgleichsregel|Back-End-Port in Lastenausgleichsregel|Auf Back-End-Server-VM ausgeführter Befehl|
    >|------------------|---------|--------|---------|
    >|**SQL Server 1**|1433 |1433 |sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433|
    >|**SQL Server 2**|1434 |1434 |sudo ./ip_fwd.sh -i eth0 -f 1434 -a <FQDN/IP> -b 1433|
    
## <a name="create-a-private-endpoint-to-private-link-service"></a>Erstellen eines privaten Endpunkts für einen Private Link-Dienst

1. Wählen Sie im linken Menü „Alle Dienste“ > „Alle Ressourcen“ und anschließend in der Ressourcenliste Ihre Data Factory aus.
2. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.
3. Navigieren Sie zur Registerkarte **Verwalten** und anschließend zum Abschnitt **Verwaltete private Endpunkte**.
4. Wählen Sie unter **Verwaltete private Endpunkte** die Option **+ Neu** aus.
5. Wählen Sie in der Liste die Kachel **Private Link-Dienst** und anschließend **Weiter** aus.
6. Geben Sie den Namen des privaten Endpunkts ein, und wählen Sie in der Liste „Private Link-Dienst“ die Option **myPrivateLinkService** aus.
7. Fügen Sie den FQDN Ihres lokalen SQL-Zielservers hinzu.

    :::image type="content" source="./media/tutorial-managed-virtual-network/private-endpoint-6.png" alt-text="Screenshot: Einstellungen des privaten Endpunkts":::

8. Erstellen Sie den privaten Endpunkt.

## <a name="create-a-linked-service-and-test-the-connection"></a>Erstellen eines verknüpften Diensts und Testen der Verbindung

1. Navigieren Sie zur Registerkarte **Verwalten** und anschließend zum Abschnitt **Verwaltete private Endpunkte**.
2. Wählen Sie unter **Verknüpfter Dienst** die Option **+ Neu** aus.
3. Wählen Sie in der Liste die Kachel **SQL Server** und anschließend **Weiter** aus.    

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-1.png" alt-text="Screenshot: Seite zum Erstellen des verknüpften Diensts":::   

4. Aktivieren Sie **Interaktives Authoring**.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-2.png" alt-text="Screenshot: Aktivieren von „Interaktives Authoring“":::

5. Geben Sie den **FQDN** Ihrer lokalen SQL Server-Instanz sowie **Benutzername** und **Kennwort** ein.
6. Klicken Sie anschließend auf **Verbindung testen**.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-3.png" alt-text="Screenshot: Seite zum Erstellen des verknüpften SQL Server-Diensts":::

## <a name="troubleshooting"></a>Problembehandlung

Navigieren Sie zur Back-End-Server-VM, und vergewissern Sie sich, dass „telnet“ für die SQL Server-Instanz funktioniert: **telnet **<** FQDN**>** 1433**.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial erfahren Sie, wie Sie auf Microsoft Azure SQL Managed Instance über ein verwaltetes Data Factory-VNet mithilfe eines privaten Endpunkts zugreifen:

> [!div class="nextstepaction"]
> [Tutorial: Zugreifen auf SQL Managed Instance über ein verwaltetes Data Factory-VNet mithilfe eines privaten Endpunkts](tutorial-managed-virtual-network-sql-managed-instance.md)

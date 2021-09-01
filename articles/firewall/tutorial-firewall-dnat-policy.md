---
title: 'Tutorial: Filtern von eingehendem Internetdatenverkehr mit der Azure Firewall-DNAT-Richtlinie im Portal'
description: In diesem Tutorial erfahren Sie, wie Sie DNAT der Azure Firewall-Richtlinie über das Azure-Portal bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2f863c9fc5bad369813ee37c51ded73e96980a54
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034204"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-policy-dnat-using-the-azure-portal"></a>Tutorial: Filtern von eingehendem Internetdatenverkehr mit Azure Firewall-Richtlinien-DNAT im Azure-Portal

Sie können die Ziel-Netzwerkadressübersetzung (Destination Network Address Translation, DNAT) der Azure Firewall-Richtlinie so konfigurieren, dass eingehender Internetdatenverkehr für Ihre Subnetze übersetzt und gefiltert wird. Wenn Sie DNAT konfigurieren, ist die *Aktion für die Regelsammlung* auf **DNAT** festgelegt. Jede Regel in der NAT-Regelsammlung kann dann verwendet werden, um die öffentliche IP-Adresse und den Port Ihrer Firewall in eine private IP-Adresse und den zugehörigen Port zu übersetzen. Mit DNAT-Regeln wird implizit eine entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Aus Sicherheitsgründen besteht die empfohlene Vorgehensweise darin, eine bestimmte Internetquelle hinzuzufügen, um DNAT-Zugriff auf das Netzwerk zu gewähren und die Verwendung von Platzhaltern zu vermeiden. Weitere Informationen zur Logik für die Azure Firewall-Regelverarbeitung finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Netzwerkumgebung zu Testzwecken
> * Bereitstellen einer Firewall und Richtlinie
> * Erstellen einer Standardroute
> * Konfigurieren einer DNAT-Regel
> * Testen der Firewall

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.



## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie auf der Startseite des Azure-Portals auf **Ressourcengruppen** und dann auf **Hinzufügen**.
4. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Geben Sie unter **Ressourcengruppenname** die Zeichenfolge **RG-DNAT-Test** ein.
5. Wählen Sie für **Region** eine Region aus. Alle anderen Ressourcen, die Sie erstellen, müssen sich in derselben Region befinden.
6. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

## <a name="set-up-the-network-environment"></a>Einrichten der Netzwerkumgebung

In diesem Tutorial erstellen Sie zwei mittels Peering verknüpfte VNETs:

- **VN-Hub**:In diesem VNET befindet sich die Firewall.
- **VN-Spoke**: In diesem VNET befindet sich der Workloadserver.

Erstellen Sie zuerst die VNETs, und führen Sie anschließend das Peering dafür durch.

### <a name="create-the-hub-vnet"></a>Erstellen des Hub-VNET

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Klicken Sie unter **Netzwerk** auf **Virtuelle Netzwerke**.
3. Wählen Sie **Hinzufügen**.
7. Wählen Sie für **Ressourcengruppe** **RG-DNAT-Test** aus.
1. Geben Sie unter **Name** den Namen **VN-Hub** ein.
1. Wählen Sie für **Region** dieselbe Region aus, die Sie zuvor bereits verwendet haben.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Übernehmen Sie für **IPv4-Adressraum** den Standardwert **10.0.0.0/16**.
1. Wählen Sie unter **Subnetzname** die Einstellung **Standard** aus.
1. Bearbeiten Sie die Einstellung für **Subnetzname**, und geben Sie **AzureFirewallSubnet** ein.

     Die Firewall befindet sich diesem Subnetz, und der Subnetzname **muss** „AzureFirewallSubnet“ lauten.
     > [!NOTE]
     > Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Geben Sie unter **Subnetzadressbereich** **10.0.1.0/26** ein.
11. Klicken Sie auf **Speichern**.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

### <a name="create-a-spoke-vnet"></a>Erstellen eines Spoke-VNET

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Klicken Sie unter **Netzwerk** auf **Virtuelle Netzwerke**.
3. Wählen Sie **Hinzufügen**.
1. Wählen Sie für **Ressourcengruppe** **RG-DNAT-Test** aus.
1. Geben Sie unter **Name** den Namen **VN-Spoke** ein.
1. Wählen Sie für **Region** dieselbe Region aus, die Sie zuvor bereits verwendet haben.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Bearbeiten Sie unter **IPv4-Adressraum** die Standardeinstellung, und geben Sie **192.168.0.0/16** ein.
1. Wählen Sie **Subnetz hinzufügen** aus.
1. Geben Sie für den Typ von **Subnetzname** **SN-Workload** ein.
10. Geben Sie unter **Subnetzadressbereich** den Bereich **192.168.1.0/24** ein.
11. Wählen Sie **Hinzufügen**.
1. Klicken Sie auf **Überprüfen und erstellen**.
1. Klicken Sie auf **Erstellen**.

### <a name="peer-the-vnets"></a>Verknüpfen der VNETs per Peering

Führen Sie nun das Peering für die beiden VNETs durch.

1. Klicken Sie auf das virtuelle Netzwerk **VN-Hub**.
2. Klicken Sie unter **Einstellungen** auf **Peerings**.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **This virtual network** (Dieses virtuelle Netzwerk) für **Peering link name** (Name des Peeringlinks) **Peer-HubSpoke** ein.
5. Geben Sie unter **Virtuelles Remotenetzwerk** für **Peering link name** (Name des Peeringlinks) **Peer-SpokeHub** ein. 
1. Wählen Sie **VN-Spoke** für das virtuelle Netzwerk aus.
1. Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Hinzufügen**.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie einen virtuellen Workloadcomputer, und ordnen Sie ihn im Subnetz **SN-Workload** an.

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus.
2. Wählen Sie unter **Beliebt** die Option **Windows Server 2016 Datacenter** aus.

**Grundlagen**

1. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Wählen Sie für **Ressourcengruppe** **RG-DNAT-Test** aus.
1. Geben Sie unter **Name des virtuellen Computers** **Srv-Workload** ein.
1. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
1. Geben Sie einen Benutzernamen und ein Kennwort ein.
1. Klicken Sie auf **Weiter: Datenträger**.

**Datenträger**
1. Klicken Sie auf **Weiter: Netzwerk** aus.

**Netzwerk**

1. Klicken Sie unter **Virtuelles Netzwerk** auf **VN-Spoke**.
2. Wählen Sie unter **Subnetz** die Option **SN-Workload**.
3. Wählen Sie unter **Öffentliche IP** die Option **Keine** aus.
4. Klicken Sie unter **Öffentliche Eingangsports** auf **Keine**. 
2. Lassen Sie die restlichen Standardeinstellungen unverändert, und klicken Sie auf **Weiter: Verwaltung**.

**Verwaltung**

1. Wählen Sie für **Verwaltung** **Deaktivieren** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.

**Überprüfen + erstellen**

Überprüfen Sie die Zusammenfassung, und klicken Sie auf **Erstellen**. Dies nimmt einige Minuten in Anspruch.

Nachdem die Bereitstellung abgeschlossen ist, können Sie sich die private IP-Adresse für den virtuellen Computer notieren. Sie wird später beim Konfigurieren der Firewall benötigt. Klicken Sie auf den Namen des virtuellen Computers und dann unter **Einstellungen** auf **Netzwerk**, um nach der privaten IP-Adresse zu suchen.

## <a name="deploy-the-firewall-and-policy"></a>Bereitstellen der Firewall und Richtlinie

1. Wählen Sie auf der Startseite des Portals **Ressource erstellen** aus.
1. Suchen Sie nach **Firewall**, und wählen Sie dann **Firewall** aus.
1. Klicken Sie auf **Erstellen**. 
1. Konfigurieren Sie die Firewall auf der Seite **Firewall erstellen** anhand der folgenden Tabelle:

   |Einstellung  |Wert  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |Wählen Sie **RG-DNAT-Test** aus. |
   |Name     |**FW-DNAT-test**|
   |Region     |Wählen Sie den gleichen Standort aus wie zuvor.|
   |Firewallverwaltung|**Firewallrichtlinie zum Verwalten dieser Firewall verwenden**|
   |Firewallrichtlinie|**Neu hinzufügen:**<br>**fw-dnat-pol**<br>Ihre ausgewählte Region 
   |Virtuelles Netzwerk auswählen     |**Vorhandene verwenden**: VN-Hub|
   |Öffentliche IP-Adresse     |**Neu hinzufügen**, Name: **fw-pip**|

5. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie auf **Überprüfen + erstellen**.
6. Überprüfen Sie die Zusammenfassung, und wählen Sie dann **Erstellen** aus, um die Firewall zu erstellen.

   Die Bereitstellung dauert einige Minuten.
7. Navigieren Sie nach Abschluss der Bereitstellung zur Ressourcengruppe **RG-DNAT-Test**, und klicken Sie auf die Firewall **FW-DNAT-test**.
8. Notieren Sie sich die private und öffentliche IP-Adresse der Firewall. Sie verwenden diese später, wenn Sie die Standardroute und NAT-Regel erstellen.

## <a name="create-a-default-route"></a>Erstellen einer Standardroute

Konfigurieren Sie die ausgehende Standardroute für das Subnetz **SN-Workload** so, dass sie die Firewall durchläuft.

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Wählen Sie unter **Netzwerk** die Option **Routingtabellen** aus.
3. Wählen Sie **Hinzufügen**.
5. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Wählen Sie für **Ressourcengruppe** **RG-DNAT-Test** aus.
1. Wählen Sie unter **Region** die gleiche Region aus wie zuvor.
1. Geben Sie unter **Name** den Namen **RT-FW-route** ein.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie **Zu Ressource wechseln** aus.
1. Klicken Sie auf **Subnetze** und dann auf **Zuordnen**.
1. Klicken Sie unter **Virtuelles Netzwerk** auf **VN-Spoke**.
1. Wählen Sie unter **Subnetz** die Option **SN-Workload**.
1. Klicken Sie auf **OK**.
1. Klicken Sie auf **Routen** und dann auf **Hinzufügen**.
1. Geben Sie für **Routenname** den Namen **fw-dg** ein.
1. Geben Sie unter **Adresspräfix** die Zeichenfolge **0.0.0.0/0** ein.
1. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** aus.

    Azure Firewall ist eigentlich ein verwalteter Dienst, in dieser Situation kann aber „Virtuelles Gerät“ verwendet werden.
18. Geben Sie unter **Adresse des nächsten Hops** die private IP-Adresse für die Firewall ein, die Sie sich zuvor notiert haben.
19. Klicken Sie auf **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurieren einer NAT-Regel

Mit dieser Regel können Sie eine Remotedesktopverbindung mit dem virtuellen Computer „Srv-Workload“ über die Firewall herstellen.

1. Öffnen Sie die Ressourcengruppe **RG-DNAT-Test**, und wählen Sie die Firewallrichtlinie **fw-dnat-pol** aus. 
1. Wählen Sie unter **Einstellungen** die Option **DNAT-Regeln** aus.
2. Wählen Sie **Regelsammlung hinzufügen** aus.
3. Geben Sie für **Name** den Wert **rdp** ein.
1. Geben Sie für **Priorität** den Wert **200** ein.
1. Wählen Sie für **Regelsammlungsgruppe** die Option **DefaultDnatRuleCollectionGroup** aus.
1. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **rdp-nat** ein.
1. Wählen Sie unter **Quelltyp** die Option **IP-Adresse** aus.
1. Geben Sie unter **Quelle** die Zeichenfolge **\*** ein.
1. Wählen Sie für **Protokoll** die Option **TCP** aus.
1. Geben Sie unter **Zielports** den Wert **3389** ein.
1. Wählen Sie unter **Zieltyp** die Option **IP-Adresse** aus.
1. Geben Sie unter **Ziel** die öffentliche IP-Adresse der Firewall ein.
1. Geben Sie für **Übersetzte Adresse** die private IP-Adresse für **Srv-Workload** ein.
1. Geben Sie für **Übersetzter Port** den Wert **3389** ein.
1. Wählen Sie **Hinzufügen**.


## <a name="test-the-firewall"></a>Testen der Firewall

1. Verbinden Sie einen Remotedesktop mit der öffentlichen IP-Adresse der Firewall. Sie sollten mit dem virtuellen Computer **Srv-Workload** verbunden werden.
2. Schließen Sie den Remotedesktop.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **RG-DNAT-Test** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen und Konfigurieren von Azure Firewall Premium](premium-deploy.md)
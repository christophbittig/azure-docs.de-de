---
title: 'Tutorial: Bereitstellen und Konfigurieren von Azure Firewall und einer Richtlinie über das Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Firewall und Richtlinienregeln über das Azure-Portal bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4c9472b419fd796ae53383f73c168e6ac43342bb
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965863"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-and-policy-using-the-azure-portal"></a>Tutorial: Bereitstellen und Konfigurieren von Azure Firewall und einer Richtlinie über das Azure-Portal

Die Steuerung des ausgehenden Netzwerkzugriffs ist ein wichtiger Teil eines umfassenden Netzwerksicherheitsplans. Vielleicht möchten Sie beispielsweise den Zugriff auf Websites einschränken. Mitunter kann es auch empfehlenswert sein, die verfügbaren Ports einzuschränken.

Eine Möglichkeit zur Steuerung des ausgehenden Netzwerkzugriffs aus einem Azure-Subnetz ist Azure Firewall und eine Firewallrichtlinie. Mit Azure Firewall und einer Firewallrichtlinie können Sie Folgendes konfigurieren:

* Anwendungsregeln, die vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) definieren, auf die von einem Subnetz aus zugegriffen werden kann.
* Netzwerkregeln, die die Quelladresse, das Protokoll, den Zielport und die Zieladresse definieren.

Die konfigurierten Firewallregeln werden auf den Netzwerkdatenverkehr angewendet, wenn Sie Ihren Netzwerkdatenverkehr an die Firewall als Subnetz-Standardgateway weiterleiten.

In diesem Tutorial erstellen Sie der Einfachheit halber ein einzelnes vereinfachtes VNET mit zwei Subnetzen.

Für Produktionsbereitstellungen wird ein [Hub-Spoke-Modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) empfohlen, bei dem sich die Firewall in einem eigenen VNET befindet. Die Workloadserver befinden sich in per Peering verknüpften VNETs in derselben Region mit einem oder mehreren Subnetzen.

* **AzureFirewallSubnet:** Das Subnetz mit der Firewall.
* **Workload-SN:** Das Subnetz mit dem Workloadserver. Der Netzwerkdatenverkehr dieses Subnetzes durchläuft die Firewall.

![Netzwerkinfrastruktur des Tutorials](media/tutorial-firewall-deploy-portal/tutorial-network.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Netzwerkumgebung zu Testzwecken
> * Bereitstellen einer Firewall und einer Firewallrichtlinie
> * Erstellen einer Standardroute
> * Konfigurieren einer Anwendungsregel zum Zulassen des Zugriffs auf www.google.com
> * Konfigurieren einer Netzwerkregel, um den Zugriff auf externe DNS-Server zuzulassen
> * Konfigurieren einer NAT-Regel, um einen Remotedesktop für den Testserver zuzulassen
> * Testen der Firewall

Sie können für dieses Verfahren auch [Azure PowerShell](deploy-ps-policy.md) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="set-up-the-network"></a>Einrichten des Netzwerks

Erstellen Sie zunächst eine Ressourcengruppe für die Ressourcen, die zum Bereitstellen der Firewall benötigt werden. Erstellen Sie dann ein VNET, Subnetze und Testserver.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Ressourcengruppe enthält alle Ressourcen für das Tutorial.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie im Menü des Azure-Portals die Option **Ressourcengruppen** aus, oder suchen Sie auf einer beliebigen Seite nach *Ressourcengruppen*, und wählen Sie diese Option anschließend aus. Wählen Sie anschließend **Hinzufügen**.
4. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Geben Sie unter **Ressourcengruppenname** die Zeichenfolge *Test-FW-RG* ein.
1. Wählen Sie für **Region** eine Region aus. Alle anderen Ressourcen, die Sie erstellen, müssen sich in derselben Region befinden.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

### <a name="create-a-vnet"></a>Erstellen eines VNET

Dieses VNET soll drei Subnetze beinhalten.

> [!NOTE]
> Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
1. Wählen Sie **Netzwerk** aus.
1. Suchen Sie nach **Virtuelles Netzwerk**, und wählen Sie die Option aus.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Wählen Sie für **Ressourcengruppe** die Gruppe **Test-FW-RG** aus.
1. Geben Sie unter **Name** die Zeichenfolge **Test-FW-VN** ein.
1. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Übernehmen Sie für **IPv4-Adressraum** den Standardwert **10.0.0.0/16**.
1. Wählen Sie unter **Subnetz** die Einstellung **Standard** aus.
1. Ändern Sie den Wert unter **Subnetzname** in **AzureFirewallSubnet**. Die Firewall befindet sich diesem Subnetz, und der Subnetzname **muss** „AzureFirewallSubnet“ lauten.
1. Geben Sie unter **Adressbereich** die Zeichenfolge **10.0.1.0/26** ein.
1. Wählen Sie **Speichern** aus.

   Erstellen Sie als Nächstes ein Subnetz für den Workloadserver.

1. Wählen Sie **Subnetz hinzufügen** aus.
4. Geben Sie unter **Subnetzname** die Zeichenfolge **Workload-SN** ein.
5. Geben Sie unter **Subnetzadressbereich** den Bereich **10.0.2.0/24** ein.
6. Wählen Sie **Hinzufügen**.
7. Klicken Sie auf **Überprüfen und erstellen**.
8. Klicken Sie auf **Erstellen**.

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie nun den virtuellen Workloadcomputer, und ordnen Sie ihn im Subnetz **Workload-SN** an.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
2. Wählen Sie **Windows Server 2016 Datacenter** aus.
4. Geben Sie die folgenden Werte für den virtuellen Computer ein:

   |Einstellung  |Wert  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Name des virtuellen Computers     |**Srv-Work**|
   |Region     |Wie zuvor|
   |Image|Windows Server 2016 Datacenter|
   |Benutzername des Administrators     |Geben Sie einen Benutzernamen ein.|
   |Kennwort     |Geben Sie ein Kennwort ein.|

4. Wählen Sie unter **Regeln für eingehende Ports** für **Öffentliche Eingangsports** die Option **Keine** aus.
6. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und wählen Sie **Weiter: Datenträger**.
7. Übernehmen Sie die Standardeinstellungen für Datenträger, und wählen Sie **Weiter: Netzwerk** aus.
8. Stellen Sie sicher, dass als virtuelles Netzwerk **Test-FW-VN** und als Subnetz **Workload-SN** ausgewählt ist.
9. Wählen Sie unter **Öffentliche IP** die Option **Keine** aus.
11. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und wählen Sie **Weiter: Verwaltung** aus.
12. Wählen Sie **Deaktivieren** aus, um die Startdiagnose zu deaktivieren. Übernehmen Sie für die anderen Einstellungen die Standardwerte, und klicken Sie dann auf **Bewerten + erstellen**.
13. Überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und wählen Sie dann **Erstellen** aus.
1. Wählen Sie nach Abschluss der Bereitstellung die Ressource **Srv-Work** aus, und notieren Sie sich die private IP-Adresse zur späteren Verwendung.

## <a name="deploy-the-firewall-and-policy"></a>Bereitstellen der Firewall und Richtlinie

Stellen Sie die Firewall im VNET bereit.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
2. Geben Sie **Firewall** in das Suchfeld ein, und drücken Sie die **EINGABETASTE**.
3. Wählen Sie **Firewall** aus, und klicken Sie anschließend auf **Erstellen**.
4. Konfigurieren Sie die Firewall auf der Seite **Firewall erstellen** anhand der folgenden Tabelle:

   |Einstellung  |Wert  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Test-FW-RG** |
   |Name     |**Test-FW01**|
   |Region     |Wählen Sie den gleichen Standort aus wie zuvor.|
   |Firewallverwaltung|**Firewallrichtlinie zum Verwalten dieser Firewall verwenden**|
   |Firewallrichtlinie|**Neu hinzufügen**:<br>**fw-test-pol**<br>Ihre ausgewählte Region 
   |Virtuelles Netzwerk auswählen     |**Vorhandene verwenden**: **Test-FW-VN**|
   |Öffentliche IP-Adresse     |**Neu hinzufügen**:<br>**Name**: **fw-pip**|

5. Übernehmen Sie für die anderen Standardwerte und klicken Sie auf **Überprüfen und erstellen**.
6. Überprüfen Sie die Zusammenfassung, und wählen Sie dann **Erstellen** aus, um die Firewall zu erstellen.

   Die Bereitstellung dauert einige Minuten.
7. Navigieren Sie nach Abschluss der Bereitstellung zur Ressourcengruppe **Test-FW-RG**, und wählen Sie die Firewall **Test-FW01** aus.
8. Notieren Sie sich die öffentlichen IP-Adressen der Firewall. Diese Adressen werden später verwendet.

## <a name="create-a-default-route"></a>Erstellen einer Standardroute

Konfigurieren Sie die ausgehende Standardroute für das Subnetz **Workload-SN** so, dass sie die Firewall durchläuft.

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Dienste** aus, oder suchen Sie auf einer beliebigen Seite nach *Alle Dienste*, und wählen Sie diese Option anschließend aus.
2. Wählen Sie unter **Netzwerk** die Option **Routingtabellen** aus.
3. Wählen Sie **Hinzufügen**.
5. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
6. Wählen Sie für **Ressourcengruppe** die Gruppe **Test-FW-RG** aus.
7. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
4. Geben Sie unter **Name** die Zeichenfolge **Firewall-route** ein.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

1. Wählen Sie auf der Seite Firewall-Route die Option **Subnetze** aus und klicken Sie dann auf **Zuordnen**.
1. Wählen Sie **Virtuelles Netzwerk** > **Test-FW-VN** aus.
1. Wählen Sie unter **Subnetz** die Option **Workload-SN** aus. Stellen Sie sicher, dass Sie nur das Subnetz **Workload-SN** für diese Route auswählen. Andernfalls funktioniert die Firewall nicht korrekt.

13. Klicken Sie auf **OK**.
14. Wählen Sie **Routen** und dann **Hinzufügen** aus.
15. Geben Sie für **Routenname** den Namen **fw-dg** ein.
16. Geben Sie unter **Adresspräfix** die Zeichenfolge **0.0.0.0/0** ein.
17. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** aus.

    Azure Firewall ist eigentlich ein verwalteter Dienst, in dieser Situation kann aber „Virtuelles Gerät“ verwendet werden.
18. Geben Sie unter **Adresse des nächsten Hops** die private IP-Adresse für die Firewall ein, die Sie sich zuvor notiert haben.
19. Klicken Sie auf **OK**.

## <a name="configure-an-application-rule"></a>Konfigurieren einer Anwendungsregel

Hierbei handelt es sich um die Anwendungsregel, die ausgehenden Zugriff auf `www.google.com` ermöglicht.

1. Öffnen Sie **Test-FW-RG**, und wählen Sie die Firewallrichtlinie **fw-test-pol** aus.
1. Wählen Sie **Anwendungsregeln** aus.
1. Wählen Sie **Regelsammlung hinzufügen** aus.
1. Geben Sie unter **Name** die Zeichenfolge **App-Coll01** ein.
1. Geben Sie für **Priorität** den Wert **200** ein.
1. Wählen Sie unter **Regelsammlungsaktion** die Option **Zulassen** aus.
1. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **Allow-Google** ein.
1. Wählen Sie unter **Quelltyp** die Option **IP-Adresse** aus.
1. Geben Sie unter **Quelle** die Adresse **10.0.2.0/24** ein.
1. Geben Sie unter **Protokoll:Port** die Zeichenfolge **http, https** ein.
1. Wählen Sie unter **Zieltyp** die Option **FQDN** aus.
1. Geben Sie unter **Ziel** die Adresse **`www.google.com`** ein.
1. Wählen Sie **Hinzufügen**.

Azure Firewall enthält eine integrierte Regelsammlung für Infrastruktur-FQDNs, die standardmäßig zulässig sind. Diese FQDNs sind plattformspezifisch und können nicht für andere Zwecke verwendet werden. Weitere Informationen finden Sie unter [Infrastruktur-FQDNs](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurieren einer Netzwerkregel

Hierbei handelt es sich um die Netzwerkregel, die ausgehenden Zugriff auf zwei IP-Adressen am Port 53 (DNS) zulässt.

1. Wählen Sie **Netzwerkregeln** aus.
2. Wählen Sie **Regelsammlung hinzufügen** aus.
3. Geben Sie unter **Name** die Zeichenfolge **Net-Coll01** ein.
4. Geben Sie für **Priorität** den Wert **200** ein.
5. Wählen Sie unter **Regelsammlungsaktion** die Option **Zulassen** aus.
1. Wählen Sie für **Regelsammlungsgruppe** die Option **DefaultNetworkRuleCollectionGroup** aus.
1. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **Allow-DNS** ein.
1. Wählen Sie unter **Quelltyp** die Option **IP-Adresse** aus.
1. Geben Sie unter **Quelle** die Adresse **10.0.2.0/24** ein.
1. Wählen Sie für **Protokoll** die Option **UDP** aus.
1. Geben Sie unter **Zielports** den Wert **53** ein.
1. Wählen Sie unter **Zieltyp** die Option **IP-Adresse** aus.
1. Geben Sie als **Ziel** die Adresse **209.244.0.3,209.244.0.4** ein.<br>Dies sind öffentliche DNS-Server, die von CenturyLink betrieben werden.
2. Wählen Sie **Hinzufügen**.

## <a name="configure-a-dnat-rule"></a>Konfigurieren einer DNAT-Regel

Mit dieser Regel können Sie eine Remotedesktopverbindung mit dem virtuellen Computer „Srv-Work“ über die Firewall herstellen.

1. Wählen Sie **DNAT-Regeln** aus.
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
1. Geben Sie für **Übersetzte Adresse** die private IP-Adresse für **Srv-work** ein.
1. Geben Sie für **Übersetzter Port** den Wert **3389** ein.
1. Wählen Sie **Hinzufügen**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändern der primären und sekundären DNS-Adresse für die Netzwerkschnittstelle **Srv-Work**

In diesem Tutorial konfigurieren Sie zu Testzwecken die primäre und sekundäre DNS-Adresse des Servers. Hierbei handelt es sich nicht um eine generelle Azure Firewall-Anforderung.

1. Wählen Sie im Menü des Azure-Portals die Option **Ressourcengruppen** aus, oder suchen Sie auf einer beliebigen Seite nach *Ressourcengruppen*, und wählen Sie diese Option anschließend aus. Wählen Sie die Ressourcengruppe **Test-FW-RG** aus.
2. Wählen Sie die Netzwerkschnittstelle für die VM **Srv-Work** aus.
3. Wählen Sie unter **Einstellungen** die Option **DNS-Server** aus.
4. Wählen Sie unter **DNS-Server** die Option **Benutzerdefiniert** aus.
5. Geben Sie **209.244.0.3** in das Textfeld **DNS-Server hinzufügen** und **209.244.0.4** in das nächste Textfeld ein.
6. Wählen Sie **Speichern** aus.
7. Starten Sie den virtuellen Computer **Srv-Work** neu.

## <a name="test-the-firewall"></a>Testen der Firewall

Testen Sie nun die Firewall, um sicherzustellen, dass sie wie erwartet funktioniert.

1. Stellen Sie eine Remotedesktopverbindung mit der öffentlichen IP-Adresse der Firewall her, und melden Sie sich beim virtuellen Computer **Srv-Work** an. 
3. Navigieren Sie in Internet Explorer zu `https://www.google.com`.
4. Klicken Sie in den Sicherheitswarnungen von Internet Explorer auf **OK** > **Schließen**.

   Die Google-Startseite sollte nun angezeigt werden.

5. Navigieren Sie zu `https://www.microsoft.com`.

   Sie sollten durch die Firewall blockiert werden.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

* Sie können zum einzigen zulässigen FQDN navigieren, aber nicht zu anderen.
* Sie können DNS-Namen mithilfe des konfigurierten externen DNS-Servers auflösen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **Test-FW-RG** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen und Konfigurieren von Azure Firewall Premium](premium-deploy.md)
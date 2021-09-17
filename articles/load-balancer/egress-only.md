---
title: Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)
titleSuffix: Azure Load Balancer
description: In diesem Artikel wird beschrieben, wie Sie einen internen Lastenausgleich mit ausgehender Netzwerkadressenübersetzung (NAT) erstellen.
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/21/2021
ms.author: allensu
ms.openlocfilehash: d2f54b8ea7161f297528ac6ea82fde548827033a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681187"
---
# <a name="outbound-only-load-balancer-configuration"></a>Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)

Verwenden Sie eine Kombination aus internen und externen Load Balancer Standard-Instanzen, um für virtuelle Computer hinter einem internen Lastenausgleichsmodul die Konnektivität in ausgehender Richtung sicherzustellen. 

Diese Konfiguration ermöglicht ausgehende NAT (Network Address Translation, Netzwerkadressenübersetzung) für ein internes Lastenausgleichsmodul, um für Ihren Back-End-Pool ein Setup vom Typ „Nur ausgehend“ zu erhalten.

> [!NOTE]
> **Azure Virtual Network NAT** ist die empfohlene Konfiguration für ausgehende Konnektivität in Produktionsbereitstellungen. Weitere Informationen zu **Azure Virtual Network NAT** und der **NAT Gateway**-Ressource finden Sie unter **[Was ist Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)** .
>
> Informationen zum Bereitstellen einer Lastenausgleichskonfiguration nur für ausgehenden Daten mit Azure Virtual Network NAT und einem NAT-Gateway finden Sie unter [Tutorial: Integrieren eines NAT-Gateways in einen internen Lastenausgleich über das Azure-Portal](../virtual-network/nat-gateway/tutorial-nat-gateway-load-balancer-internal-portal.md).
>
> Weitere Informationen zu ausgehenden Verbindungen in Azure und dem standardmäßigen ausgehenden Zugriff finden Sie unter [Verwendung von SNAT (Source Network Address Translation) für ausgehende Verbindungen](load-balancer-outbound-connections.md) und [Standardzugriff in ausgehender Richtung](../virtual-network/default-outbound-access.md).

:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Abbildung: Lastenausgleichskonfiguration nur für ausgehenden Datenverkehr" border="true":::

*Abbildung: Lastenausgleichskonfiguration vom Typ „Nur ausgehend“*

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-load-balancers"></a>Erstellen des virtuellen Netzwerks und Lastenausgleichs

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz für den Lastenausgleich und den virtuellen Computer (VM).  Anschließend erstellen Sie den Lastenausgleich.

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie das virtuelle Netzwerk sowie Subnetze für die VM, den Lastenausgleich und den Bastion-Host.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie für **Name** den Wert **myResourceGroupLB** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(USA) USA, Osten 2** aus. |

4. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

5. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

6. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

7. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **myBackendSubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

8. Wählen Sie **Speichern** aus.

9. Wählen Sie die Registerkarte **Sicherheit** .

10. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/27** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |

11. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

### <a name="create-internal-load-balancer"></a>Erstellen eines internen Lastenausgleichs

In diesem Abschnitt erstellen Sie den internen Lastenausgleich.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **myResourceGroupLB** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myInternalLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(USA) USA, Osten 2** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **LoadBalancerFrontend** ein.

7. Wählen Sie unter **Subnetz** den Eintrag **myBackendSubnet** aus.

8. Wählen Sie **Dynamisch** für die **Zuweisung** aus.

9. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

10. Wählen Sie **Hinzufügen**.

11. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

12. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

13. Geben Sie unter **Back-End-Pool hinzufügen** im Feld **Name** den Namen **myInternalBackendPool** ein.

14. Wählen Sie unter **Back-End-Pool-Konfiguration** die Option **Netzwerkschnittstelle** oder **IP-Adresse** aus.

15. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

16. Wählen Sie **Hinzufügen**.

17. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

18. Klicken Sie auf **Erstellen**.

### <a name="create-public-load-balancer"></a>Erstellen eines öffentlichen Lastenausgleichsmoduls

In diesem Abschnitt erstellen Sie den öffentlichen Lastenausgleich.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **myResourceGroupLB** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myPublicLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(USA) USA, Osten 2** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif          | Übernehmen Sie den Standardwert für **Region**. |

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **LoadBalancerFrontend** ein.

7. Wählen Sie als **IP-Version** die Option **IPv4** oder **IPv6** aus.

    > [!NOTE]
    > IPv6 wird derzeit nicht mit Routingpräferenz oder regionsübergreifendem Lastenausgleich (globale Ebene) unterstützt.

8. Wählen Sie für den **IP-Typ** die Option **IP-Adresse** aus.

    > [!NOTE]
    > Weitere Informationen zu IP-Präfixen finden Sie unter [Präfix für öffentliche IP-Adressen](../virtual-network/public-ip-address-prefix.md).

9. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus.

10. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myPublicIP** ein.

11. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

12. Übernehmen Sie unter **Routingpräferenz** die Standardeinstellung **Microsoft-Netzwerk**.

13. Klicken Sie auf **OK**.

14. Wählen Sie **Hinzufügen**.

15. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

16. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

17. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myPublicBackendPool** ein.

18. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

19. Wählen Sie unter **Back-End-Pool-Konfiguration** die Option **Netzwerkschnittstelle** oder **IP-Adresse** aus.

20. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

21. Wählen Sie **Hinzufügen**.

22. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

23. Klicken Sie auf **Erstellen**.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

In diesem Abschnitt erstellen Sie eine VM. Während der Erstellung fügen Sie diese dem Back-End-Pool des internen Lastenausgleichs hinzu. Nachdem die VM erstellt wurde, fügen Sie diese dem Back-End-Pool des öffentlichen Lastenausgleichs hinzu.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** > **Virtueller Computer** aus.
   
3. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroupLB** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **(USA) USA, Osten 2** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |  |
    | Öffentliche Eingangsports | Wählen Sie **Keine** aus. |

4. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
5. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Übernehmen Sie die Standardeinstellung **Basic**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Aktivieren Sie die Option. |
    | **Lastenausgleichseinstellungen** |
    | Optionen für den Lastenausgleich | Wählen Sie **Azure-Lastenausgleich** aus. |
    | Wählen Sie einen Lastenausgleich aus. | Wählen Sie **myInternalLoadBalancer** aus.  |
    | Wählen Sie einen Back-End-Pool aus. | Wählen Sie **myInternalBackendPool** aus. |
   
6. Klicken Sie auf **Überprüfen + erstellen**. 
  
7. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

### <a name="add-vm-to-backend-pool-of-public-load-balancer"></a>Hinzufügen einer VM zum Back-End-Pool des öffentlichen Lastenausgleichs

In diesem Abschnitt fügen Sie die zuvor erstellte VM dem Back-End-Pool des öffentlichen Lastenausgleichs hinzu.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie **myPublicLoadBalancer** aus.

3. Wählen Sie in **myPublicLoadBalancer** unter **Einstellungen** die Option **Back-End-Pools** aus.

4. Wählen Sie auf der Seite **Back-End-Pools** unter **Back-End-Pool** den Eintrag **myPublicBackendPool** aus.

5. Wählen Sie in **myPublicBackendPool** unter **Virtuelle Netzwerke** die Option **myVNet** aus.

6. Klicken Sie unter **Virtuelle Computer** auf die blaue Schaltfläche **+ Hinzufügen**.

7. Aktivieren Sie unter **Dem Back-End-Pool virtuelle Computer hinzufügen** das Kontrollkästchen neben **myVM**.

8. Wählen Sie **Hinzufügen**.

9. Wählen Sie **Speichern** aus.
## <a name="test-connectivity-before-outbound-rule"></a>Testen der Konnektivität vor der Ausgangsregel

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM** aus.

3. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Öffnen Sie Internet Explorer.

7. Geben Sie in der Adressleiste **https://whatsmyip.org** ein.

8. Bei der Verbindungsherstellung sollte ein Fehler auftreten. Standardmäßig ist für eine öffentliche Load Balancer Standard-Instanz [kein ausgehender Datenverkehr ohne definierte Ausgangsregel zulässig](load-balancer-overview.md#securebydefault).
 
## <a name="create-a-public-load-balancer-outbound-rule"></a>Erstellen einer öffentlichen Ausgangsregel für den Lastenausgleich

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie **myPublicLoadBalancer** aus.

3. Wählen Sie in **myPublicLoadBalancer** unter **Einstellungen** die Option **Ausgangsregeln** aus.

4. Wählen Sie unter **Ausgangsregeln** die Option **+ Hinzufügen** aus.

5. Geben Sie die folgenden Informationen ein bzw. wählen Sie sie aus, um die Ausgangsregel zu konfigurieren.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myOutboundRule** ein. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus.|
    | Protokoll | Übernehmen Sie die Standardeinstellung **Alle**. |
    | Leerlaufzeitüberschreitung (Minuten) | Bewegen Sie den Schieberegler auf **15 Minuten**.|
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**.|
    | Back-End-Pool | Wählen Sie **myPublicBackendPool** aus.|
    | **Portzuordnung** |  |
    | Portzuordnung | Wählen Sie **Anzahl ausgehender Ports manuell auswählen** aus. |
    | **Ausgehende Ports** |  |
    | Auswählen nach | Wählen Sie **Ports pro Instanz** aus. |
    | Ports pro Instanz | Geben Sie **10000** ein.

6. Wählen Sie **Hinzufügen**.

## <a name="test-connectivity-after-outbound-rule"></a>Testen der Konnektivität nach der Ausgangsregel

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM** aus.

3. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Öffnen Sie Internet Explorer.

7. Geben Sie in der Adressleiste **https://whatsmyip.org** ein.

8. Bei der Verbindungsherstellung sollte kein Fehler auftreten.

9. Die angezeigte IP-Adresse sollte die Front-End-IP-Adresse von **myPublicLoadBalancer** sein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie sie nicht mehr benötigen, löschen Sie die Ressourcengruppe, die Lastenausgleichsmodule, die VM und alle zugehörigen Ressourcen. 

Wählen Sie hierzu die Ressourcengruppe **myResourceGroupLB** und dann die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Konfiguration vom Typ „Nur ausgehend“ mit einer Kombination aus öffentlichen und internen Lastenausgleichsmodulen erstellt.  

Mit dieser Konfiguration können Sie einen Lastenausgleich für eingehenden internen Datenverkehr für Ihren Back-End-Pool durchführen, während gleichzeitig alle öffentlichen eingehenden Verbindungen verhindert werden.

Weitere Informationen zu Azure Load Balancer und Azure Bastion finden Sie unter [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md) und [Was ist Azure Bastion?](../bastion/bastion-overview.md).


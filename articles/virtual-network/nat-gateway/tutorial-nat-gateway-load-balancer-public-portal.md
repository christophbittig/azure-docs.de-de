---
title: 'Tutorial: Integrieren eines NAT-Gateways in einen öffentlichen Lastenausgleich: Azure-Portal'
titleSuffix: Virtual Network NAT
description: In diesem Tutorial erfahren Sie, wie Sie ein Virtual Network NAT-Gateway über das Azure-Portal in einen öffentlichen Lastenausgleich integrieren.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5ef476f5de715d1f80823bf6d61316c1af406737
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122202058"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Tutorial: Integrieren eines NAT-Gateways in einen öffentlichen Lastenausgleich über das Azure-Portal

In diesem Tutorial erfahren Sie, wie Sie ein NAT-Gateway in einen öffentlichen Lastenausgleich integrieren.

Azure Load Balancer Standard ist standardmäßig sicher. Ausgehende Konnektivität wird explizit durch Aktivieren der Übersetzung der Quellnetzwerkadresse (Source Network Address Translation, SNAT) für ausgehenden Datenverkehr definiert. Die SNAT wird in einer Lastenausgleichsregel oder in Ausgangsregeln aktiviert. 

Durch die NAT-Gatewayintegration werden keine Ausgangsregeln für ausgehende SNAT des Back-End-Pools mehr benötigt. 

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen einer Azure Load Balancer-Instanz
> * Erstellen von zwei virtuellen Computern für den Back-End-Pool von Azure Load Balancer
> * Erstellen eines NAT-Gateways
> * Überprüfen der ausgehenden Konnektivität der virtuellen Computer im Back-End-Pool des Lastenausgleichs

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie in **Name** die Zeichenfolge **TutorPubLBNAT-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **USA, Osten** aus. |

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

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt erstellen Sie einen zonenredundanten Lastenausgleich für virtuelle Computer. Bei Zonenredundanz können eine oder mehrere Verfügbarkeitszonen ausfallen, aber der Datenpfad bleibt dennoch so lange verfügbar, wie eine Zone in der Region fehlerfrei bleibt.

Während der Erstellung des Lastenausgleichs konfigurieren Sie Folgendes:

* Front-End-IP-Adresse
* Back-End-Pool
* Lastenausgleichsregeln für eingehenden Datenverkehr

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **TutorPubLBNAT-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(USA) USA, Osten** aus.                                        |
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
    > Weitere Informationen zu IP-Präfixen finden Sie unter [Präfix für öffentliche IP-Adressen](../../virtual-network/public-ip-address-prefix.md).

9. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus.

10. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myPublicIP** ein.

11. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../../availability-zones/az-overview.md).

12. Übernehmen Sie unter **Routingpräferenz** die Standardeinstellung **Microsoft-Netzwerk**.

13. Klicken Sie auf **OK**.

14. Wählen Sie **Hinzufügen**.

15. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

16. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

17. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

18. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

19. Wählen Sie unter **Backend Pool Configuration** (Konfiguration des Back-End-Pools) die Option **NIC** oder **IP-Adresse** aus.

20. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

21. Wählen Sie **Hinzufügen**.

22. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

23. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

24. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontend** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myHealthProbe** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diesen Wert aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |
    | Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Übernehmen Sie die Standardeinstellung **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern Zugriff auf das Internet zu gewähren**. |

25. Wählen Sie **Hinzufügen** aus.

26. Wählen Sie unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

27. Klicken Sie auf **Erstellen**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt werden zwei virtuelle Computer (**myVM1** und **myVM2**) in zwei verschiedenen Zonen (**Zone 1** und **Zone 2**) erstellt.

Diese virtuellen Computer werden dem Back-End-Pool des zuvor erstellten Lastenausgleichs hinzugefügt.

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **TutorPubLBNAT-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitszonen** aus. |
    | Verfügbarkeitszone | Wählen Sie **1** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Behalten Sie die Standardwerte bei. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |  |
    | Öffentliche Eingangsports | Wählen Sie **Keine** aus. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Geben Sie in **Zielportbereiche** den Wert **80** ein. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** den Namen **myNSGRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Aktivieren Sie das Kontrollkästchen.|
    | **Lastenausgleichseinstellungen** |
    | Optionen für den Lastenausgleich | Wählen Sie **Azure-Lastenausgleich** aus. |
    | Wählen Sie einen Lastenausgleich aus. | Wählen Sie **myLoadBalancer** aus.  |
    | Wählen Sie einen Back-End-Pool aus. | Wählen Sie **myBackendPool** aus. |
   
5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

7. Führen Sie die Schritte 1 bis 7 aus, um einen virtuellen Computer mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | Verfügbarkeitszone | **2** |
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus. | 

## <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

In diesem Abschnitt wird ein NAT-Gateway erstellt und dem Subnetz in dem virtuellen Netzwerk zugewiesen, das Sie zuvor erstellt haben.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk> NAT-Gateway** aus, oder suchen Sie über das Suchfeld nach **NAT-Gateway**.

2. Klicken Sie auf **Erstellen**. 

3. Geben Sie auf der Registerkarte **Grundlagen** unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Wählen Sie Ihr Azure-Abonnement.                                  |
    | Ressourcengruppe   | Wählen Sie **TutorPubLBNAT-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myNATGateway** ein.                                    |
    | Region           | Wählen Sie **USA, Osten** aus.  |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **10** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** oder unten auf der Seite die Schaltfläche **Weiter: Ausgehende IP-Adresse** aus.

5. Geben Sie auf der Registerkarte **Ausgehende IP-Adresse** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | **Einstellung** | **Wert** |
    | ----------- | --------- |
    | Öffentliche IP-Adressen | Wählen Sie **Neue öffentliche IP-Adresse erstellen** aus. </br> Geben Sie unter **Name** den Namen **myNATgatewayIP** ein. </br> Klicken Sie auf **OK**. |

6. Wählen Sie die Registerkarte **Subnetz** oder unten auf der Seite die Schaltfläche **Weiter: Subnetz** aus.

7. Wählen Sie auf der Registerkarte **Subnetz** in der Dropdownliste **Virtuelles Netzwerk** die Option **myVNet** aus.

8. Aktivieren Sie das Kontrollkästchen neben **myBackendSubnet**.

9. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

10. Klicken Sie auf **Erstellen**.

## <a name="test-nat-gateway"></a>Testen des NAT-Gateways

In diesem Abschnitt testen Sie das NAT-Gateway. Sie ermitteln zunächst die öffentliche IP-Adresse des NAT-Gateways. Anschließend stellen Sie eine Verbindung mit dem virtuellen Testcomputer her und überprüfen die ausgehende Verbindung über das NAT-Gateway.
    
1. Suchen Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für das NAT-Gateway. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myPublicIP** aus.

2. Notieren Sie sich die öffentliche IP-Adresse:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Screenshot: Ermitteln der öffentlichen IP-Adresse des NAT-Gateways" border="true":::

3. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM1** in der Ressourcengruppe **TutorPubLBNAT-rg** aus.

4. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

5. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

7. Öffnen Sie **Internet Explorer** auf **myVM1**.

8. Geben Sie in der Adressleiste **https://whatsmyip.com** ein.

9. Vergewissern Sie sich, dass die angezeigte IP-Adresse mit der NAT-Gatewayadresse übereinstimmt, die Sie im vorherigen Schritt notiert haben:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Screenshot: Internet Explorer mit externer ausgehenden IP-Adresse" border="true":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die das virtuelle Netzwerk, den virtuellen Computer und das NAT-Gateway wie folgt:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

2. Wählen Sie die Ressourcengruppe **TutorPubLBNAT-rg** aus.

3. Wählen Sie die Option **Ressourcengruppe löschen**.

4. Geben Sie **TutorPubLBNAT-rg** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Virtual Network NAT finden Sie unter folgendem Link:
> [!div class="nextstepaction"]
> [Übersicht über Virtual Network NAT](nat-overview.md)
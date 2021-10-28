---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs: Azure-Portal'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Lastenausgleich über das Azure-Portal erstellen.
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 59bd93cf5c17675e99a14484d8a13821e287047a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234565"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer über das Azure-Portal

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie einen öffentlichen Lastenausgleich und drei virtuelle Computer über das Azure-Portal erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **CreatePubLBQS-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(Europa) Europa, Westen** aus. |

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

## <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

In diesem Abschnitt erstellen Sie für Ressourcen im virtuellen Netzwerk ein NAT-Gateway für ausgehenden Internetzugriff. 

1. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

2. Wählen Sie unter **NAT-Gateways** die Option **+ Erstellen** aus.

3. Geben Sie unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |    |
    | Name des NAT-Gateways | Geben Sie **myNATgateway** ein. |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** oder unten auf der Seite die Schaltfläche **Weiter: Ausgehende IP-Adresse** aus.

5. Wählen Sie unter **Ausgehende IP-Adresse** neben **Öffentliche IP-Adressen** die Option **Neue öffentliche IP-Adresse erstellen** aus.

6. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myNATGatewayIP** ein.

7. Klicken Sie auf **OK**.

8. Wählen Sie die Registerkarte **Subnetz** oder unten auf der Seite die Schaltfläche **Weiter: Subnetz** aus.

9. Wählen Sie unter **Virtuelles Netzwerk** auf der Registerkarte **Subnetz** die Option **myVNet** aus.

10. Wählen Sie unter **Subnetzname** die Option **myBackendSubnet** aus.

11. Wählen Sie unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus, oder wählen Sie die Registerkarte **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Erstellen eines Lastenausgleichs

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
    | Resource group         | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(Europa) Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif          | Übernehmen Sie den Standardwert für **Region**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Load Balancer Standard-Instanz" border="true":::

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **LoadBalancerFrontend** ein.

7. Wählen Sie als **IP-Version** die Option **IPv4** oder **IPv6** aus.

    > [!NOTE]
    > IPv6 wird derzeit nicht mit Routingpräferenz oder regionsübergreifendem Lastenausgleich (globale Ebene) unterstützt.

8. Wählen Sie für den **IP-Typ** die Option **IP-Adresse** aus.

    > [!NOTE]
    > Weitere Informationen zu IP-Präfixen finden Sie unter [Präfix für öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-address-prefix.md).

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

17. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

18. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

19. Wählen Sie unter **Back-End-Pool-Konfiguration** die Option **Netzwerkschnittstelle** oder **IP-Adresse** aus.

20. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

21. Wählen Sie **Hinzufügen**.

22. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

23. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

24. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
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

26. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

27. Klicken Sie auf **Erstellen**.

    > [!NOTE]
    > In diesem Beispiel haben Sie ein NAT-Gateway erstellt, um ausgehenden Internetzugriff zu ermöglichen. Die Registerkarte „Ausgangsregeln“ in der Konfiguration wird ausgelassen, da sie optional und für das NAT-Gateway nicht erforderlich ist. Weitere Informationen zum Azure-NAT Gateway finden Sie unter [Was ist Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwendung von SNAT (Source Network Address Translation) für ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md).

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt werden drei virtuelle Computer (**myVM1**, **myVM2** und **myVM3**) in drei verschiedenen Zonen (**Zone 1**, **Zone 2** und **Zone 3**) erstellt. 

Diese virtuellen Computer werden dem Back-End-Pool des zuvor erstellten Lastenausgleichs hinzugefügt.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** > **Virtueller Computer** aus.
   
3. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **(Europa) Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitszonen** aus. |
    | Verfügbarkeitszone | Wählen Sie **1** aus. |
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
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Wählen Sie unter **Dienst** die Option **HTTP** aus. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** den Namen **myNSGRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Aktivieren Sie das Kontrollkästchen. |
    | **Lastenausgleichseinstellungen** |
    | Optionen für den Lastenausgleich | Wählen Sie **Azure-Lastenausgleich** aus. |
    | Wählen Sie einen Lastenausgleich aus. | Wählen Sie **myLoadBalancer** aus.  |
    | Wählen Sie einen Back-End-Pool aus. | Wählen Sie **myBackendPool** aus. |
   
6. Klicken Sie auf **Überprüfen + erstellen**. 
  
7. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

8. Führen Sie die Schritte 1 bis 7 aus, um zwei weitere virtuelle Computer mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Verfügbarkeitszone | **2** |**3**|
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.| Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-2-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **CreatePubLBQS-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(Europa) Europa, Westen** aus. |

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
    | Subnetzadressbereich | Geben Sie **10.1.0.0/27** ein. |

8. Wählen Sie **Speichern** aus.

9. Wählen Sie die Registerkarte **Sicherheit** .

10. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/24** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |


11. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt erstellen Sie drei VMs (**myVM1**, **myVM2** und **myVM3**).

Die drei virtuellen Computer werden einer Verfügbarkeitsgruppe namens **myAvailabilitySet** hinzugefügt.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** > **Virtueller Computer** aus.
   
3. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **(Europa) Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitsgruppe** aus. |
    | Verfügbarkeitsgruppe | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** die Zeichenfolge **myAvailabilitySet** ein. </br> Klicken Sie auf **OK**. |
    | Image | **Windows Server 2019 Datacenter – Gen 1** |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |   |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

4. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
5. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **myBackendSubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Geben Sie unter **Zielportbereiche** den Wert **80** ein. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** den Namen **myNSGRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Übernehmen Sie die Standardeinstellung (deaktiviert). |
 
6. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter** > **Verwaltung**.

7. Wählen Sie auf der Registerkarte **Verwaltung** die folgenden Werte aus, oder geben Sie sie ein:
    
    | Einstellung | Wert |
    |---|---|
    | **Überwachung** | |
    | Startdiagnose | Wählen Sie **Aus** aus. |

8. Klicken Sie auf **Überprüfen + erstellen**. 
  
9. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

10. Führen Sie die Schritte 1 bis 9 aus, um zwei weitere virtuelle Computer mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2 | VM 3 |
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Verfügbarkeitsgruppe| Wählen Sie **myAvailabilitySet** aus. | Wählen Sie **myAvailabilitySet** aus.|
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.| Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt.

Während der Erstellung des Lastenausgleichs konfigurieren Sie Folgendes:

* Front-End-IP-Adresse
* Back-End-Pool
* Lastenausgleichsregeln für eingehenden Datenverkehr

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Lastenausgleich** die Option **+ Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(Europa) Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Basic** aus. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Load Balancer Basic-Instanz" border="true":::
 
4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **LoadBalancerFrontend** ein.

7. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

8. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus.

9. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myPublicIP** ein.

10. Wählen Sie unter **Zuweisung** die Option **Statisch** aus.

11. Klicken Sie auf **OK**.

12. Wählen Sie **Hinzufügen**.

13. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

14. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

15. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

16. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

17. Wählen Sie unter **Verknüpft mit** die Option **Virtuelle Computer** aus.

18. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

19. Wählen Sie unter **Virtuelle Computer** die blaue Schaltfläche **+ Hinzufügen** aus.

20. Aktivieren Sie unter **Dem Back-End-Pool virtuelle Computer hinzufügen** die Kontrollkästchen neben **myVM1**, **myVM2** und **myVM3**.

21. Wählen Sie **Hinzufügen**.

22. Wählen Sie unter **Back-End-Pool hinzufügen** die Option **Hinzufügen** aus.

23. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

24. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

25. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **myHealthProbe** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diesen Wert aus. |
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |

26. Wählen Sie **Hinzufügen**.

27. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

28. Klicken Sie auf **Erstellen**.

---

## <a name="install-iis"></a>Installieren von IIS

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM1** aus.

2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

3. Klicken Sie auf **Bastion verwenden**.

4. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme** > **Windows PowerShell**.

7. Führen Sie im PowerShell-Fenster die im Anschluss angegebenen Befehle aus. Diese bewirken Folgendes:

    * Installieren des IIS-Servers
    * Entfernen der Standarddatei „iisstart.htm“
    * Hinzufügen einer neuen Datei namens „iisstart.htm“ zum Anzeigen des Namens des virtuellen Computers

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

8. Schließen Sie die Bastion-Sitzung mit **myVM1**.

9. Wiederholen Sie die Schritte 1 bis 8, um IIS und die aktualisierte Datei „iisstart.htm“ auf **myVM2** und **myVM3** zu installieren.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

1. Geben Sie im Suchfeld am oberen Seitenrand den Suchbegriff **Lastenausgleich** ein.  Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Ermitteln Sie unter **Öffentliche IP-Adresse** auf der Seite **Übersicht** die öffentliche IP-Adresse für den Lastenausgleich.

3. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die benutzerdefinierte VM-Seite des IIS-Webservers wird im Browser angezeigt.

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/load-balancer-test.png" alt-text="Screenshot: Lastenausgleichstest":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe **CreatePubLBQS-rg** aus, die die Ressourcen enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

* Erstellen einer Azure Load Balancer-Instanz (Standard oder Basic)
* Anfügen von drei virtuellen Computern an den Lastenausgleich
* Testen des Lastenausgleichs

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)
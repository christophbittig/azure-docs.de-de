---
title: 'Schnellstart: Erstellen eines internen Lastenausgleichs – Azure-Portal'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen internen Lastenausgleich über das Azure-Portal erstellen.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9c8f46bf615b0d0c9fe8289057a8244a1d57185d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732807"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Schnellstart: Erstellen eines internen Lastenausgleichs für virtuelle Computer über das Azure-Portal

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie über das Azure-Portal einen internen Lastenausgleich und drei virtuelle Computer erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

Wenn Sie einen internen Lastenausgleich erstellen, wird ein virtuelles Netzwerk als Netzwerk für den Lastenausgleich konfiguriert. 

Eine private IP-Adresse in dem virtuellen Netzwerk wird als Front-End (standardmäßig mit dem Namen **LoadBalancerFrontend**) für den Lastenausgleich konfiguriert. 

Die Front-End-IP-Adresse kann **statisch** oder **dynamisch** sein.

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **CreateIntLBQS-rg** ein. </br> Klicken Sie auf **OK**. |
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
    | Resource group | Wählen Sie **CreateIntLBQS-rg** aus. |
    | **Instanzendetails** |    |
    | Name des NAT-Gateways | Geben Sie **myNATgateway** ein. |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** oder unten auf der Seite die Schaltfläche **Weiter: Ausgehende IP-Adresse** aus.

5. Wählen Sie unter **Ausgehende IP-Adresse** neben **Öffentliche IP-Adressen** die Option **Neue öffentliche IP-Adresse erstellen** aus.

6. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Wert **myNATgatewayIP** ein.

7. Klicken Sie auf **OK**.

8. Wählen Sie die Registerkarte **Subnetz** oder unten auf der Seite die Schaltfläche **Weiter: Subnetz** aus.

9. Wählen Sie unter **Virtuelles Netzwerk** auf der Registerkarte **Subnetz** die Option **myVNet** aus.

10. Wählen Sie unter **Subnetzname** die Option **myBackendSubnet** aus.

11. Wählen Sie unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus, oder wählen Sie die Registerkarte **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Erstellen eines Lastenausgleichs

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt.

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
    | Resource group         | Wählen Sie **CreateIntLBQS-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(Europa) Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Load Balancer Standard-Instanz" border="true":::

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Namen **LoadBalancerFrontend** ein.

7. Wählen Sie unter **Subnetz** den Eintrag **myBackendSubnet** aus.

8. Wählen Sie **Dynamisch** für die **Zuweisung** aus.

9. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

10. Wählen Sie **Hinzufügen**.

11. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

12. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

13. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

14. Wählen Sie unter **Backend Pool Configuration** (Konfiguration des Back-End-Pools) die Option **NIC** oder **IP-Adresse** aus.

15. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

16. Wählen Sie **Hinzufügen**.

17. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

18. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

19. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

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

20. Wählen Sie **Hinzufügen**.

21. Wählen Sie unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

22. Klicken Sie auf **Erstellen**.

    > [!NOTE]
    > In diesem Beispiel haben Sie ein NAT-Gateway erstellt, um ausgehenden Internetzugriff zu ermöglichen. Die Registerkarte „Ausgangsregeln“ in der Konfiguration wird ausgelassen, da sie für das NAT-Gateway nicht erforderlich ist. Weitere Informationen zum Azure-NAT Gateway finden Sie unter [Was ist Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
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
    | Ressourcengruppe | Wählen Sie **CreateIntLBQS-rg** aus. |
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
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Geben Sie unter **Zielportbereiche** den Wert **80** ein. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** den Namen **myNSGRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
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

2. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Namen **CreateIntLBQS-rg** ein. </br> Klicken Sie auf **OK**. |
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

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt.

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
    | Resource group         | Wählen Sie **CreateIntLBQS-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(Europa) Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Wählen Sie **Basic** aus. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Load Balancer Standard-Instanz" border="true":::

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Namen **LoadBalancerFrontend** ein.

7. Wählen Sie unter **Subnetz** den Eintrag **myBackendSubnet** aus.

8. Wählen Sie **Dynamisch** für die **Zuweisung** aus.

9. Wählen Sie **Hinzufügen**.

10. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

11. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

12. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool** ein.

13. Wählen Sie unter **Verknüpft mit** die Option **Virtuelle Computer** aus.

14. Wählen Sie unter **IP-Version** die Option **IPv4** oder **IPv6** aus.

15. Wählen Sie **Hinzufügen**.

16. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

17. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

18. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

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
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |

19. Wählen Sie **Hinzufügen**.

20. Wählen Sie unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

21. Klicken Sie auf **Erstellen**.

    > [!NOTE]
    > In diesem Beispiel haben Sie ein NAT-Gateway erstellt, um ausgehenden Internetzugriff zu ermöglichen. Die Registerkarte „Ausgangsregeln“ in der Konfiguration wird ausgelassen, da sie für das NAT-Gateway nicht erforderlich ist. Weitere Informationen zum Azure-NAT Gateway finden Sie unter [Was ist Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).
    > Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwendung von SNAT (Source Network Address Translation) für ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md).

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
    | Ressourcengruppe | Wählen Sie **CreateIntLBQS-rg** aus. |
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
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Aktivieren Sie das Kontrollkästchen. |
    | **Lastenausgleichseinstellungen** |   |
    | Optionen für den Lastenausgleich | Wählen Sie **Azure-Lastenausgleich** aus. |
    | Wählen Sie einen Lastenausgleich aus. | Wählen Sie **myLoadBalancer** aus. |
    | Wählen Sie einen Back-End-Pool aus. | Wählen Sie **myBackendPool** aus. |
 
6. Klicken Sie auf **Überprüfen + erstellen**. 
  
7. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

8. Führen Sie die Schritte 1 bis 7 aus, um zwei weitere virtuelle Computer mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2 | VM 3 |
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Verfügbarkeitsgruppe| Wählen Sie **myAvailabilitySet** aus. | Wählen Sie **myAvailabilitySet** aus.|
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.| Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]
---

## <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt wird ein virtueller Computer namens **myTestVM** erstellt.  Dieser virtuelle Computer wird zum Testen der Lastenausgleichskonfiguration verwendet.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** > **Virtueller Computer** aus.
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreateIntLBQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myTestVM** ein. |
    | Region | Wählen Sie **(Europa) Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |   |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte **Netzwerk** die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus. |
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie die im vorherigen Schritt erstellte Netzwerksicherheitsgruppe **MyNSG** aus.|
       
5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

## <a name="install-iis"></a>Installieren von IIS

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM1** aus.

3. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Klicken Sie auf **Bastion verwenden**. 

5. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

6. Wählen Sie **Verbinden**.

7. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme** > **Windows PowerShell** > **Windows PowerShell**.

8. Führen Sie im PowerShell-Fenster die folgenden Befehle aus. Diese bewirken Folgendes:

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

9. Schließen Sie die Bastion-Sitzung mit **myVM1**.

10. Wiederholen Sie die Schritte 1 bis 9, um IIS und die aktualisierte Datei „iisstart.htm“ auf **myVM2** und **myVM3** zu installieren.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

In diesem Abschnitt testen Sie den Lastenausgleich, indem Sie eine Verbindung mit **myTestVM** herstellen und die Webseite überprüfen.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie **myLoadBalancer** aus.

3. Notieren Sie sich in der **Übersicht** von **myLoadBalancer** die Adresse neben **Private IP-Adresse**, oder kopieren Sie sie.

4. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

5. Wählen Sie **myTestVM** aus.

6. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

7. Klicken Sie auf **Bastion verwenden**.

8. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

9. Öffnen Sie **Internet Explorer** auf **myTestVM**.

10. Geben Sie die IP-Adresse aus dem vorherigen Schritt in die Adressleiste des Browsers ein. Die benutzerdefinierte Seite mit einem der Back-End-Servernamen wird im Browser angezeigt.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Screenshot: Erwartungsgemäße Anzeige des Browserfensters mit der benutzerdefinierten Seite" border="true":::
   
Sie können vom Clientcomputer aus eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Lastenausgleich den Datenverkehr auf die beiden virtuellen Computer verteilt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe **CreateIntLBQS-rg** aus, die die Ressourcen enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

* Erstellen einer internen Azure Load Balancer-Instanz (Standard oder Basic)
* Anfügen von drei virtuellen Computern an den Lastenausgleich
* Konfigurieren der Lastenausgleichsregel für den Datenverkehr sowie eines Integritätstests und anschließendes Testen des Lastenausgleichs 

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)
---
title: 'Tutorial: Lastenausgleich von Konfigurationen mit mehreren IP-Adressen – Azure-Portal'
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie etwas über den Lastenausgleich über primäre und sekundäre NIC-Konfigurationen hinweg mithilfe des Azure-Portals.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: c0a5f441b27d379ff94db41fabfdf398a19ca667
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254663"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>Tutorial: Lastenausgleich von Konfigurationen mit mehreren IP-Adressen über das Azure-Portal 

Um mehrere Websites zu hosten, können Sie eine andere Netzwerkschnittstelle verwenden, die einem virtuellen Computer zugeordnet ist. Azure Load Balancer unterstützt die Bereitstellung des Lastenausgleichs, um die Hochverfügbarkeit der Websites zu unterstützen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Konfigurieren eines virtuellen Netzwerks, eines Subnetzes und eines NAT-Gateways.
> * Erstellen zweier Windows Server-VMs
> * Erstellen einer sekundären NIC und von Netzwerkkonfigurationen für jeden virtuellen Computer
> * Erstellen zweier IIS-Websites (Internetinformationsserver) auf jedem der virtuellen Computer
> * Binden der Websites an die Netzwerkkonfigurationen
> * Erstellen und Konfigurieren eines Azure Load Balancers
> * Testen des Lastenausgleichs

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk für den Lastenausgleich und virtuelle Computer.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

3. Wählen Sie unter **Virtuelle Netzwerke** die Option **+ Erstellen** aus.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie in **Name** den Wert **TutorialLBIP-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **(Europa) Europa, Westen** aus. |

5. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

7. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

8. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **myBackendSubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

9. Wählen Sie **Speichern** aus.

10. Wählen Sie die Registerkarte **Sicherheit** .

11. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/27** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |


12. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

13. Klicken Sie auf **Erstellen**.

## <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

In diesem Abschnitt erstellen Sie für Ressourcen im virtuellen Netzwerk ein NAT-Gateway für ausgehenden Internetzugriff. 

1. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

2. Wählen Sie unter **NAT-Gateways** die Option **+ Erstellen** aus.

3. Geben Sie unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **TutorialLBIP-rg** aus. |
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

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt erstellen Sie zwei virtuelle Computer zum Hosten der IIS-Websites.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** und dann **+ Virtueller Computer** aus.

3. Geben Sie unter **VM erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **TutorialLBIP-rg** aus. |
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

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Wählen Sie unter **Dienst** die Option **HTTP** aus. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** den Namen **myNSGrule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
   
7. Klicken Sie auf **Überprüfen + erstellen**. 
  
8. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

9. Führen Sie die Schritte 1 bis 8 aus, um einen weiteren virtuellen Computer mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2 |
    | ------- | ---- |
    | Name |  **myVM2** |
    | Verfügbarkeitszone | **2** |
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus. |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>Erstellen sekundärer Netzwerkkonfigurationen

In diesem Abschnitt ändern Sie die private IP-Adresse der vorhandenen NIC von jedem virtuellen Computer in **Statisch**. Als Nächstes fügen Sie jedem virtuellen Computer mit einer **statischen** privaten IP-Adressenkonfiguration eine neue NIC-Ressource hinzu.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM1** aus.

3. Wenn der virtuelle Computer ausgeführt wird, beenden Sie den virtuellen Computer. 

4. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.

5. Wählen Sie unter **Netzwerk** den Namen der Netzwerkschnittstelle neben **Netzwerkschnittstelle** aus. Die Netzwerkschnittstelle beginnt mit dem Namen des virtuellen Computers und hat eine Zufallszahl zugewiesen. In diesem Beispiel **myVM1266**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Screenshot der Netzwerkkonfiguration von „myVM1“ im Azure-Portal.":::

6. Wählen Sie auf der Seite „Netzwerkschnittstelle“ unter **Einstellungen** die Option **IP-Konfigurationen** aus.

7. Wählen Sie unter **IP-Konfigurationen** die Option **ipconfig1** aus.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="Screenshot der Netzwerkschnittstellenkonfiguration von „myVM1“.":::

8. Wählen Sie unter **Zuweisung** in der **ipconfig1**-Konfiguration **Statisch** aus.

9. Wählen Sie **Speichern** aus.

10. Kehren Sie zur Seite **Übersicht** von **myVM1** zurück.

11. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.

12. Wählen Sie auf der Seite **Netzwerk** die Option **Netzwerkschnittstelle anfügen** aus.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="Screenshot von „Netzwerkschnittstelle anfügen“ von myVM1.":::

13. Wählen Sie unter **Netzwerkschnittstelle anfügen** die Option **Netzwerkschnittstelle erstellen und anfügen** aus.

14. Geben Sie unter **Virtuelles Netzwerk erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Resource group | Wählen Sie **TutorialLBIP-rg** aus. |
    | **Netzwerkschnittstelle** |  |
    | Name | Geben Sie **myVM1NIC2** ein. |
    | Subnet | Wählen Sie **myBackendSubnet (10.1.0.0/24)** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus. |
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **myNSG** aus. |
    | Zuweisung der privaten IP-Adresse | Wählen Sie **Statisch** aus. |
    | Private IP-Adresse | Geben Sie **10.1.0.6** ein. |

15. Klicken Sie auf **Erstellen**. 

16. Starten Sie den virtuellen Computer.

17. Wiederholen Sie die Schritte 1 bis 16 für **myVM2**, und ersetzen Sie dabei die folgenden Informationen:

    | Einstellung | myVM2 |
    | ------  | ----- |
    | Name | **myVM2NIC2** |
    | Private IP-Adresse | **10.1.0.7** |

## <a name="configure-virtual-machines"></a>Konfigurieren virtueller Computer

Sie stellen über Azure Bastion eine Verbindung mit **myVM1** und **myVM2** her und konfigurieren die Konfiguration des sekundären Netzwerks in diesem Abschnitt. Sie fügen eine Route für das Gateway für die Konfiguration des sekundären Netzwerks hinzu. Anschließend installieren Sie IIS auf jedem virtuellen Computer und passen die Websites so an, dass der Hostname des virtuellen Computers angezeigt wird.

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM1** aus.

3. Starten Sie **myVM1**.

4. Wählen Sie unter **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

5. Klicken Sie auf **Bastion verwenden**.

6. Geben Sie die Werte für Benutzername und Kennwort ein, die Sie beim Erstellen des virtuellen Computers verwendet haben.

7. Wählen Sie **Zulassen** aus, damit Bastion die Zwischenablage verwenden kann.

8. Navigieren Sie auf dem Serverdesktop zu „Start > Windows-Verwaltungsprogramme > Windows PowerShell > Windows PowerShell“.

9. Führen Sie im Windows PowerShell-Fenster den Befehl `route print` aus, der für einen virtuellen Computer mit zwei angefügten Netzwerkschnittstellen eine Ausgabe ähnlich der folgenden zurückgibt:

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    In diesem Beispiel ist **Microsoft Hyper-V Network Adapter #3 (Schnittstelle 13)** die sekundäre Netzwerkschnittstelle, der kein Standardgateway zugeordnet ist.

10. Führen Sie im PowerShell-Fenster den Befehl `ipconfig /all` aus, um zu prüfen, welche IP-Adresse der sekundären Netzwerkschnittstelle zugewiesen ist. In diesem Beispiel ist 10.1.0.6 der Schnittstelle 13 zugewiesen. Für die sekundäre Netzwerkschnittstelle wird keine Standardgateway-Adresse zurückgegeben.

11. Um den gesamten Datenverkehr für Adressen außerhalb des Subnetzes an das Gateway zu leiten, führen Sie den folgenden Befehl aus:

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    In diesem Beispiel ist **10.1.0.1** das Standardgateway für das zuvor von Ihnen erstellte virtuelle Netzwerk.

12. Führen Sie die folgenden Befehle in den PowerShell-Fenstern aus, um IIS und die Testwebsites zu installieren und zu konfigurieren:

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. Trennen Sie die Bastion-Verbindung mit **myVM1**.

14. Wiederholen Sie die Schritte 1 bis 13 für **myVM2**. Verwenden Sie den unten angegebenen PowerShell-Code für **myVM2** für die IIS-Installation.

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt erstellen Sie einen zonenredundanten Lastenausgleich für virtuelle Computer. 

Bei Zonenredundanz können eine oder mehrere Verfügbarkeitszonen ausfallen, aber der Datenpfad bleibt dennoch so lange verfügbar, wie eine Zone in der Region fehlerfrei bleibt.

Während der Erstellung des Lastenausgleichs konfigurieren Sie Folgendes:

* Zwei Front-End-IP-Adressen, eine für jede Website.
* Back-End-Pool
* Lastenausgleichsregeln für eingehenden Datenverkehr

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **TutorialLBIP-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **(Europa) Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif          | Übernehmen Sie den Standardwert für **Region**. |

4. Wählen Sie unten auf der Seite **Weiter: Front-End-IP-Konfiguration** aus.

5. Wählen Sie unter **Front-End-IP-Konfiguration** die Option **+ Front-End-IP-Adresse hinzufügen** aus.

6. Geben Sie unter **Name** den Wert **Frontend-contoso** ein.

7. Wählen Sie als **IP-Version** die Option **IPv4** aus.

    > [!NOTE]
    > IPv6 wird derzeit nicht mit Routingpräferenz oder regionsübergreifendem Lastenausgleich (globale Ebene) unterstützt.

8. Wählen Sie für den **IP-Typ** die Option **IP-Adresse** aus.

    > [!NOTE]
    > Weitere Informationen zu IP-Präfixen finden Sie unter [Präfix für öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-address-prefix.md).

9. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus.

10. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myPublicIP-contoso** ein.

11. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

    > [!NOTE]
    > In Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) haben Sie die Möglichkeit, „Keine Zone“ (Standardoption), eine bestimmte Zone oder die Option „Zonenredundant“ auszuwählen. Die Auswahl hängt von Ihren spezifischen Domänenfehleranforderungen ab. In Regionen ohne Verfügbarkeitszonen wird dieses Feld nicht angezeigt. </br> Weitere Informationen zu Verfügbarkeitszonen finden Sie in der [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md).

12. Übernehmen Sie unter **Routingpräferenz** die Standardeinstellung **Microsoft-Netzwerk**.

13. Klicken Sie auf **OK**.

14. Wählen Sie **Hinzufügen**.

14. Wählen Sie **+ Front-End-IP-Adresse hinzufügen** aus.

15. Geben Sie unter **Name** den Wert **Frontend-fabrikam** ein.

7. Wählen Sie als **IP-Version** die Option **IPv4** aus.

8. Wählen Sie für den **IP-Typ** die Option **IP-Adresse** aus.

9. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu erstellen** aus.

10. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Namen **myPublicIP-fabrikam** ein.

11. Wählen Sie unter **Verfügbarkeitszone** die Option **Zonenredundant** aus.

14. Wählen Sie **Hinzufügen**.

15. Wählen Sie unten auf der Seite **Weiter: Back-End-Pools** aus.

16. Wählen Sie auf der Registerkarte **Back-End-Pools** die Option **+ Back-End-Pool hinzufügen** aus.

17. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool-contoso** ein.

18. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

19. Wählen Sie unter **Backend-Pool-Konfiguration** die Option **NIC** aus.

20. Wählen Sie als **IP-Version** die Option **IPv4** aus.

21. Wählen Sie in **Vrtuelle Computer** die Option **+ Hinzufügen** aus.

22. Wählen Sie **myVM1** und **myVM2** aus, die **ipconfig1 (10.1.0.4)** und **ipconfig1 (10.1.0.5)** entsprechen.

23. Wählen Sie **Hinzufügen**.

21. Wählen Sie **Hinzufügen**.

22. Wählen Sie **+ Back-End-Pool hinzufügen** aus.

23. Geben Sie unter **Back-End-Pool hinzufügen** in das Feld **Name** den Namen **myBackendPool-fabrikam** ein.

24. Wählen Sie unter **Virtuelle Netzwerke** die Option **myVNet** aus.

19. Wählen Sie unter **Backend-Pool-Konfiguration** die Option **NIC** aus.

20. Wählen Sie als **IP-Version** die Option **IPv4** aus.

21. Wählen Sie in **Vrtuelle Computer** die Option **+ Hinzufügen** aus.

22. Wählen Sie **myVM1** und **myVM2** aus, die **ipconfig1 (10.1.0.6)** und **ipconfig1 (10.1.0.7)** entsprechen.

23. Wählen Sie **Hinzufügen**.

21. Wählen Sie **Hinzufügen**.

22. Wählen Sie unten auf der Seite die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr** aus.

23. Wählen Sie unter **Lastenausgleichsregel** auf der Registerkarte **Regeln für eingehenden Datenverkehr** die Option **+ Lastenausgleichsregel hinzufügen** aus.

24. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule-contoso** ein. |
    | IP-Version | Wählen Sie **IPv4** aus. |
    | Front-End-IP-Adresse | Wählen Sie **Frontend-contoso** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool-contoso** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** die Option **myHealthProbe-contoso** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diesen Wert aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |
    | Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Übernehmen Sie die Standardeinstellung **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern Zugriff auf das Internet zu gewähren**. |

25. Wählen Sie **Hinzufügen** aus.

26. Wählen Sie **+ Lastenausgleichsregel hinzufügen** aus.

27. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPrule-fabrikam** ein. |
    | IP-Version | Wählen Sie **IPv4** aus. |
    | Front-End-IP-Adresse | Wählen Sie **Frontend-fabrikam** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool-fabrikam** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie **myHealthProbe-fabrikam** in **Name** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
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

## <a name="test-load-balancer"></a>Testen des Load Balancers

In diesem Abschnitt ermitteln Sie die öffentliche IP-Adresse für jede Website. Sie geben die IP-Adresse in einen Browser ein, um die zuvor von Ihnen erstellten Websites zu testen.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

2. Wählen Sie **myPublicIP-contoso** aus.

3. Kopieren Sie die **IP-Adresse** auf die Seite „Übersicht“ von **myPublicIP-contoso**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="Screenshot der öffentlichen IP-Adresse von „myPublicIP-fabrikam“.":::

4. Öffnen Sie einen Webbrowser und fügen Sie die öffentliche IP-Adresse in die Adressleiste ein.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="Screenshot der Website von contoso im Webbrowser.":::

5. Kehren Sie zu **Öffentliche IP-Adressen** zurück. Wählen Sie **myPublicIP-fabrikam** aus.

6. Kopieren Sie die **IP-Adresse** auf die Seite „Übersicht“ von **myPublicIP-fabrikam**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="Screenshot der öffentlichen IP-Adresse von „myPublicIP-contoso“.":::

7. Öffnen Sie einen Webbrowser und fügen Sie die öffentliche IP-Adresse in die Adressleiste ein.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="Screenshot der Website von fabrikam im Webbrowser.":::

8. Um den Lastenausgleich zu testen, aktualisieren Sie den Browser, oder fahren Sie einen der virtuellen Computer herunter.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie die VMs und den Lastenausgleich wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.  Wählen Sie in den Suchergebnissen **Ressourcengruppen** aus.

2. Wählen Sie unter **Ressourcengruppen** die Option **TutorialLBIP-rg** aus.

3. Klicken Sie auf **Ressourcengruppe löschen**.

4. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen **TutorialLBIP-rg** ein. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen eines regionsübergreifenden Lastenausgleichs zu informieren:

> [!div class="nextstepaction"]
> [Erstellen eines regionsübergreifenden Lastenausgleichs mithilfe des Azure-Portals](tutorial-cross-region-portal.md)
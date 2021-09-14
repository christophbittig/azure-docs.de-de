---
title: 'Tutorial: Konfigurieren der Portweiterleitung: Azure-Portal'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial wird gezeigt, wie Sie die Portweiterleitung per Azure Load Balancer konfigurieren, um in einem virtuellen Azure-Netzwerk eine Verbindung mit VMs zu erstellen.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 8/26/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0d36be9f0ba0fc9e1b29e3fdaf0b3f9857db2204
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112145"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-azure-portal"></a>Tutorial: Konfigurieren der Portweiterleitung in Azure Load Balancer mithilfe des Azure-Portals

Mit der Portweiterleitung können Sie die Verbindung mit virtuellen Computern (VMs) in einem virtuellen Azure-Netzwerk mithilfe einer öffentlichen IP-Adresse und Portnummer von Azure Load Balancer herstellen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und virtueller Computer (VMs)
> * Erstellen eines NAT-Gateways für ausgehenden Internetzugriff für den Back-End-Pool
> * Erstellen eines öffentlichen Lastausgleichs der Standard-SKU mit Front-End-IP, Integritätstest, Back-End-Konfiguration, Lastenausgleichsregel und NAT-Regeln für eingehenden Datenverkehr
> * Installieren und Konfigurieren eines Webservers für die VMs, um Portweiterleitung und Lastenausgleichsregeln zu veranschaulichen

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-virtual-machines"></a>Erstellen eines virtuellen Netzwerks und virtueller Computer (VMs)

Für die Ressourcen in diesem Tutorial werden ein virtuelles Netzwerk und ein Subnetz benötigt. In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und VMs für die später ausgeführten Schritte.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Suchfeld oben im Portal den Suchbegriff **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** > **Virtueller Computer** aus.
   
4. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **TutorialLBPF-rg** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |    |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Geben Sie **(USA) USA, Westen 2** ein. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitszone** aus. |
    | Verfügbarkeitszone | Geben Sie **1** ein. |
    | Bild | Wählen Sie **Ubuntu Server 20.04 LTS – Gen2** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine VM-Größe aus. |
    | **Administratorkonto** |    |
    | Authentifizierungsart | Wählen Sie **Öffentlicher SSH-Schlüssel** aus. |
    | Username | Geben Sie **azureuser** ein. |
    | Quelle für öffentlichen SSH-Schlüssel | Wählen Sie **Neues Schlüsselpaar generieren** aus. |
    | Schlüsselpaarname | Geben Sie **myKey** ein. |
    | **Regeln für eingehende Ports** |    |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/create-vm-portal.png" alt-text="Screenshot: Erstellen einer VM":::

5. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.

6. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Wählen Sie **Neu erstellen**. </br> Geben Sie als **Name** den Wert **myVNet** ein. </br> Geben Sie in **Adressraum** unter **Adressbereich** den Wert **10.1.0.0/16** ein. </br> Geben Sie in **Subnetze** unter **Subnetzname** den Wert **myBackendSubnet** ein. </br> Geben Sie unter **Adressbereich** den Wert **10.1.0.0/24** ein. </br> Klicken Sie auf **OK**. |
    | Subnet | Wählen Sie **myBackendSubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus. |
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie als **Name** den Wert **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Wählen Sie unter **Dienst** die Option **HTTP** aus. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie als **Name** den Wert **myNSGRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |

7. Wählen Sie die Registerkarte **Überprüfen + erstellen** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Überprüfen + erstellen**.

8. Klicken Sie auf **Erstellen**.

9. Wählen Sie an der Eingabeaufforderung **Neues Schlüsselpaar generieren** die Option **Privaten Schlüssel herunterladen und Ressource erstellen** aus. Ihre Schlüsseldatei wird als „myKey.pem“ heruntergeladen. Sie müssen wissen, an welchen Speicherort die PEM-Datei heruntergeladen wurde. Sie benötigen den Pfad zur Schlüsseldatei in späteren Schritten.

8. Führen Sie die Schritte 1 bis 8 aus, um eine weitere VM mit den unten angegebenen Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2 |
    | ------- | ----- |
    | **Grundlagen** |    |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | **myVM2** |
    | Verfügbarkeitszone | **2** |
    | **Administratorkonto** |   |
    | Authentifizierungsart | **Öffentlicher SSH-Schlüssel** |
    | Quelle für öffentlichen SSH-Schlüssel | Wählen Sie **In Azure gespeicherten vorhandenen Schlüssel verwenden** aus. |
    | Gespeicherte Schlüssel | Wählen Sie **myKey** aus. |
    | **Netzwerk** |   |
    | **Netzwerkschnittstelle** |  |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus. |
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus. |

## <a name="create-nat-gateway"></a>Erstellen eines NAT Gateways

In diesem Abschnitt erstellen Sie ein NAT-Gateway für ausgehenden Internetzugriff für Ressourcen im virtuellen Netzwerk. 

1. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

2. Wählen Sie unter **NAT-Gateways** die Option **+ Erstellen** aus.

3. Geben Sie unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **TutorialLBPF-rg** aus. |
    | **Instanzendetails** |    |
    | Name des NAT-Gateways | Geben Sie **myNATgateway** ein. |
    | Verfügbarkeitszone | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein. |

4. Wählen Sie die Registerkarte **Ausgehende IP-Adresse** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Ausgehende IP-Adresse**.

5. Wählen Sie unter **Ausgehende IP-Adresse** neben **Öffentliche IP-Adressen** die Option **Neue öffentliche IP-Adresse erstellen** aus.

6. Geben Sie unter **Öffentliche IP-Adresse hinzufügen** in das Feld **Name** den Wert **myNATGatewayIP** ein.

7. Klicken Sie auf **OK**.

8. Wählen Sie die Registerkarte **Subnetz** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Subnetz**.

9. Wählen Sie auf der Registerkarte **Subnetz** unter **Virtuelles Netzwerk** die Option **myVNet** aus.

10. Wählen Sie unter **Subnetzname** die Option **myBackendSubnet** aus.

11. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**, oder wählen Sie die Registerkarte **Überprüfen + erstellen** aus.

12. Klicken Sie auf **Erstellen**.

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

In diesem Abschnitt erstellen Sie einen Lastenausgleich. Hierbei werden auch die Front-End-IP-Adresse, der Back-End-Pool, der Lastenausgleich und NAT-Regeln für eingehenden Datenverkehr konfiguriert.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie auf der Seite **Load Balancer** die Option **Erstellen** aus.

3. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Projektdetails** |   |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **TutorialLBPF-rg** aus. |
    | **Instanzendetails** |   |
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **USA, Westen 2** aus.                                        |
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

17. Geben Sie unter **Back-End-Pool hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myBackendPool** ein. |
    | Virtuelles Netzwerk | Wählen Sie **myVNet (TutorialLBPF-rg)** aus. |
    | Konfiguration des Back-End-Pools | Wählen Sie **Netzwerkschnittstelle** aus. |
    | IP-Version | Wählen Sie **IPv4** aus. |

18. Wählen Sie unter **Virtuelle Computer** die Option **+ Hinzufügen** aus.

19. Aktivieren Sie unter **Dem Back-End-Pool virtuelle Computer hinzufügen** die Kontrollkästchen neben **myVM1** und **myVM2**.

20. Wählen Sie **Hinzufügen**.

21. Wählen Sie **Hinzufügen**.

22. Klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Regeln für eingehenden Datenverkehr**.

23. Wählen Sie auf der Registerkarte **Regeln für eingehenden Datenverkehr** unter **Lastenausgleichsregel** die Option **+ Lastenausgleichsregel hinzufügen** aus.

24. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie abhängig von Ihren Anforderungen **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontend** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** den Wert **myHealthProbe** ein. </br> Wählen Sie für **Protokoll** die Option **HTTP** aus. </br> Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus. |
    | Sitzungspersistenz | Wählen Sie **Keine**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diese Option aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Unverankerte IP | Wählen Sie **Deaktiviert** aus. |
    | Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Übernehmen Sie die Standardeinstellung **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern Zugriff auf das Internet zu gewähren**. |

25. Wählen Sie **Hinzufügen** aus.

26. Wählen Sie auf der Registerkarte **Eingangsregeln** unter **NAT-Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus.

27. Geben Sie unter **NAT-Regel für eingehenden Datenverkehr hinzufügen** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myNATRuleVM1-221** ein. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontend** aus. |
    | Dienst | Wählen Sie **Benutzerdefiniert** aus. |
    | Protocol | Übernehmen Sie die Standardeinstellung **TCP**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diese Option aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Port | Geben Sie **221** ein. |
    | Virtueller Zielcomputer | Wählen Sie **myVM1** aus. |
    | Netzwerk-IP-Konfiguration | Wählen Sie **ipconfig1 (10.1.0.4)** aus. |
    | Portzuordnung | Wählen Sie **Benutzerdefiniert** aus. |
    | Unverankerte IP | Übernehmen Sie den Standardwert **Deaktiviert**. |
    | Zielport | Geben Sie **22** ein. |

28. Wählen Sie **Hinzufügen**.

29. Wählen Sie **+ NAT-Regel für eingehenden Datenverkehr hinzufügen** aus.

30. Geben Sie unter **NAT-Regel für eingehenden Datenverkehr hinzufügen** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myNATRuleVM2-222** ein. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontend** aus. |
    | Dienst | Wählen Sie **Benutzerdefiniert** aus. |
    | Protocol | Übernehmen Sie die Standardeinstellung **TCP**. |
    | Leerlaufzeitüberschreitung (Minuten) | Geben Sie **15** ein, oder wählen Sie diese Option aus. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Port | Geben Sie **222** ein. |
    | Virtueller Zielcomputer | Geben Sie **myVM2** ein. |
    | Netzwerk-IP-Konfiguration | Wählen Sie **ipconfig1 (10.1.0.5)** aus. |
    | Portzuordnung | Wählen Sie **Benutzerdefiniert** aus. |
    | Unverankerte IP | Übernehmen Sie den Standardwert **Deaktiviert**. |
    | Zielport | Geben Sie **22** ein. |

31. Wählen Sie **Hinzufügen**.

32. Klicken Sie unten auf der Seite auf die blaue Schaltfläche **Überprüfen + erstellen**.

33. Klicken Sie auf **Erstellen**.

## <a name="install-web-server"></a>Installieren des Webservers

In diesem Abschnitt stellen Sie über die NAT-Regeln für eingehenden Datenverkehr eine SSH-Verbindung mit den VMs her und installieren einen Webserver.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

2. Wählen Sie **myLoadBalancer** aus.

3. Notieren Sie sich die öffentliche IP-Adresse, die auf der Seite **Übersicht** von **myLoadBalancer** unter **Öffentliche IP-Adresse** angezeigt wird.  In diesem Beispiel lautet sie **20.190.2.163**.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/get-public-ip.png" alt-text="Screenshot: öffentliche IP-Adresse im Azure-Portal":::

4. Wenn Sie einen Mac- oder Linux-Computer verwenden, öffnen Sie eine Bash-Eingabeaufforderung. Wenn Sie einen Windows Computer verwenden, öffnen Sie eine PowerShell-Eingabeaufforderung.

5. Öffnen Sie an Ihrer Eingabeaufforderung eine SSH-Verbindung mit **myVM1**. Ersetzen Sie die IP-Adresse durch die im vorherigen Schritt abgerufene Adresse, und verwenden Sie Port **221**, den Sie für die NAT-Regel für eingehenden Datenverkehr für myVM1 angegeben haben. Ersetzen Sie den Pfad zur PEM-Datei durch den Pfad, in den die Schlüsseldatei heruntergeladen wurde.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 221
    ```

    > [!TIP]
    > Der von Ihnen erstellte SSH-Schlüssel kann bei der nächsten Erstellung einer VM in Azure verwendet werden. Wählen Sie einfach die Option **In Azure gespeicherten Schlüssel verwenden** für **Öffentliche SSH-Schlüsselquelle** aus, wenn Sie das nächste Mal einen virtuellen Computer erstellen. Der private Schlüssel befindet sich bereits auf Ihrem Computer, sodass Sie nichts herunterladen müssen.

6. Aktualisieren Sie über die SSH-Sitzung Ihre Paketquellen, und installieren Sie dann das aktuelle NGINX-Paket.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

7. Geben Sie `Exit` ein, um die SSH-Sitzung zu verlassen.

8. Öffnen Sie an Ihrer Eingabeaufforderung eine SSH-Verbindung mit **myVM2**. Ersetzen Sie die IP-Adresse durch die im vorherigen Schritt abgerufene Adresse, und verwenden Sie Port **222**, den Sie für die NAT-Regel für eingehenden Datenverkehr für myVM2 angegeben haben. Ersetzen Sie den Pfad zur PEM-Datei durch den Pfad, in den die Schlüsseldatei heruntergeladen wurde.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 222
    ```

9. Aktualisieren Sie über die SSH-Sitzung Ihre Paketquellen, und installieren Sie dann das aktuelle NGINX-Paket.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

10. Geben Sie `Exit` ein, um die SSH-Sitzung zu verlassen.

## <a name="test-the-web-server"></a>Testen des Webservers

In diesem Abschnitt öffnen Sie Ihren Webbrowser und geben die IP-Adresse für den Lastenausgleich ein, den Sie im vorherigen Schritt abgerufen haben.

1. Öffnen Sie Ihren Webbrowser.

2. Geben Sie in der Adressleiste die IP-Adresse für den Lastenausgleich ein. In diesem Beispiel lautet sie **20.190.2.163**.

3. Es wird die NGINX-Standardwebsite angezeigt.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/web-server-test.png" alt-text="Screenshot: Testen des NGINX-Webservers":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie die VMs und den Lastenausgleich wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.  Wählen Sie in den Suchergebnissen **Ressourcengruppen** aus.

2. Wählen Sie unter **Ressourcengruppen** die Option **TutorialLBPF-rg** aus.

3. Klicken Sie auf **Ressourcengruppe löschen**.

4. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen **TutorialLBPF-rg** ein. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen eines regionsübergreifenden Lastenausgleichs zu informieren:

> [!div class="nextstepaction"]
> [Erstellen eines regionsübergreifenden Lastenausgleichs mithilfe des Azure-Portals](tutorial-cross-region-portal.md)

---
title: 'Filtern von Netzwerkdatenverkehr: Tutorial – Azure-Portal'
titlesuffix: Azure Virtual Network
description: In diesem Tutorial erfahren Sie, wie Sie an ein Subnetz gerichteten Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe unter Verwendung des Azure-Portals filtern.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 4af40f7f1caab7e4517c0dd621e400f58e3a29ad
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091367"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal

Sie können eine Netzwerksicherheitsgruppe verwenden, um eingehenden und ausgehenden Netzwerkdatenverkehr im Subnetz eines virtuellen Netzwerks zu filtern.

Netzwerksicherheitsgruppen enthalten Sicherheitsregeln, die Netzwerkdatenverkehr nach IP-Adresse, Port und Protokoll filtern. Sicherheitsregeln werden auf Ressourcen angewendet, die in einem Subnetz bereitgestellt sind. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Netzwerksicherheitsgruppe und Sicherheitsregeln
> * Erstellen eines virtuellen Netzwerks und Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
> * Bereitstellen von virtuellen Computern in einem Subnetz
> * Testen von Datenverkehrsfiltern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Wählen Sie im Portal oben links die Option **Ressource erstellen** aus.

2. Geben Sie in das Suchfeld **Virtuelles Netzwerk** ein. Wählen Sie in den Suchergebnissen **Virtuelles Netzwerk** aus.

3. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Erstellen** aus.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**.  </br> Geben Sie **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myVNet** ein. |
    | Region | Wählen Sie **(USA) USA, Osten** aus. |

5. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

6. Klicken Sie auf **Erstellen**.

## <a name="create-application-security-groups"></a>Erstellen von Anwendungssicherheitsgruppen

Mithilfe einer Anwendungssicherheitsgruppe können Sie Server mit ähnlichen Funktionen gruppieren, wie etwa Webserver.

1. Wählen Sie im Portal oben links die Option **Ressource erstellen** aus.

2. Geben Sie in das Suchfeld **Anwendungssicherheitsgruppe** ein. Wählen Sie in den Suchergebnissen die Option **Anwendungssicherheitsgruppe** aus.

3. Wählen Sie auf der Seite **Anwendungssicherheitsgruppe** die Option **Erstellen** aus.

4. Geben Sie unter **Anwendungssicherheitsgruppe erstellen** die folgenden Informationen auf der Registerkarte **Grundlagen** ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    |**Projektdetails** |  |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Name | Geben Sie **myAsgWebServers** ein. |
    | Region | Wählen Sie **(USA) USA, Osten** aus. | 

5. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

6. Klicken Sie auf **Erstellen**.

7. Wiederholen Sie Schritt 4, und geben Sie dabei die folgenden Werte an:

    | Einstellung | Wert |
    | ------- | ----- |
    |**Projektdetails** |  |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |  |
    | Name | Geben Sie **myAsgMgmtServers** ein. |
    | Region | Wählen Sie **(USA) USA, Osten** aus. |

8. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

9. Klicken Sie auf **Erstellen**.

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Eine Netzwerksicherheitsgruppe schützt den Netzwerkdatenverkehr in Ihrem virtuellen Netzwerk.

1. Wählen Sie im Portal oben links die Option **Ressource erstellen** aus.

2. Geben Sie im Suchfeld **Netzwerksicherheitsgruppe** ein. Wählen Sie in den Suchergebnissen **Netzwerksicherheitsgruppe** aus.

3. Wählen Sie auf der Seite **Netzwerksicherheitsgruppe** die Option **Erstellen** aus.

4. Geben Sie unter **Netzwerksicherheitsgruppe erstellen** die folgenden Informationen auf der Registerkarte **Grundlagen** ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myNSG** ein. |
    | Location | Wählen Sie **(USA) USA, Osten** aus. | 

5. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

6. Klicken Sie auf **Erstellen**.

## <a name="associate-network-security-group-to-subnet"></a>Zuordnen einer Netzwerksicherheitsgruppe zu einem Subnetz

In diesem Abschnitt ordnen Sie die Netzwerksicherheitsgruppe dem Subnetz des zuvor erstellten virtuellen Netzwerks zu.

1. Beginnen Sie oben im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** mit der Eingabe von **myNsg**. Wenn **myNsg** in den Suchergebnissen angezeigt wird, wählen Sie diesen Eintrag aus.

2. Wählen Sie auf der Übersichtsseite von **myNSG** unter **Einstellungen** die Option **Subnetze** aus.

3. Wählen Sie auf der Seite **Einstellungen** die Option **Zuordnen** aus:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Zuordnen der Netzwerksicherheitsgruppe zum Subnetz" border="true":::

3. Wählen Sie unter **Subnetz zuordnen** die Option **Virtuelles Netzwerk** und anschließend **myVNet** aus. 

4. Wählen Sie **Subnetz** > **Standard** und dann **OK** aus.

## <a name="create-security-rules"></a>Erstellen von Sicherheitsregeln

1. Wählen Sie unter **Einstellungen** von **myNSG** die Option **Sicherheitsregeln für eingehenden Datenverkehr** aus.

2. Wählen Sie unter **Sicherheitsregeln für eingehenden Datenverkehr** die Option **+ Hinzufügen** aus:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Eingangssicherheitsregel hinzufügen" border="true":::

3. Erstellen Sie eine Sicherheitsregel, die für die Anwendungssicherheitsgruppe **myAsgWebServers** die Ports 80 und 443 zulässt. Geben Sie unter **Eingangssicherheitsregel hinzufügen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | `Source` | Übernehmen Sie den Standardwert **Beliebig**. |
    | Source port ranges | Übernehmen Sie den Standardwert **(*)** . |
    | Destination | Wählen Sie **Anwendungssicherheitsgruppe** aus. |
    | Ziel-Anwendungssicherheitsgruppe | Wählen Sie **myAsgWebServers** aus. |
    | Dienst | Übernehmen Sie den Standardwert **Benutzerdefiniert**. |
    | Zielportbereiche | Geben Sie **80,443** ein. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Aktion | Übernehmen Sie den Standardwert **Zulassen**. |
    | Priorität | Übernehmen Sie den Standardwert **100**. |
    | Name | Geben Sie **Allow-Web-All** ein. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Sicherheitsregel für eingehenden Datenverkehr" border="true":::

3. Führen Sie Schritt 2 erneut aus, und verwenden Sie dabei die folgenden Werte:

    | Einstellung | Wert |
    | ------- | ----- |
    | `Source` | Übernehmen Sie den Standardwert **Beliebig**. |
    | Source port ranges | Übernehmen Sie den Standardwert **(*)** . |
    | Destination | Wählen Sie **Anwendungssicherheitsgruppe** aus. |
    | Ziel-Anwendungssicherheitsgruppe | Wählen Sie **myAsgMgmtServers** aus. |
    | Dienst | Übernehmen Sie den Standardwert **Benutzerdefiniert**. |
    | Zielportbereiche | Geben Sie **3389** ein. |
    | Protocol | Wählen Sie **Alle** aus. |
    | Aktion | Übernehmen Sie den Standardwert **Zulassen**. |
    | Priorität | Übernehmen Sie den Standardwert **110**. |
    | Name | Geben Sie **Allow-RDP-All** ein. |

    > [!CAUTION]
    > In diesem Artikel wird RDP (Port 3389) für den virtuellen Computer, der der Anwendungssicherheitsgruppe **myAsgMgmtServers** zugewiesen ist, im Internet verfügbar gemacht. 
    >
    > In Produktionsumgebungen empfiehlt es sich, eine VPN-basierte oder private Netzwerkverbindung mit den zu verwaltenden Azure-Ressourcen herzustellen oder Azure Bastion für die Verbindungsherstellung zu verwenden, anstatt den Port 3389 im Internet verfügbar zu machen.
    >
    > Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](../bastion/bastion-overview.md).

Nachdem Sie die Schritte 1 bis 3 durchgeführt haben, überprüfen Sie die Regeln, die Sie erstellt haben. Ihre Liste sollte wie die Liste im folgenden Beispiel aussehen:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Sicherheitsregeln" border="true":::

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie zwei virtuelle Computer im virtuellen Netzwerk.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

1. Wählen Sie im Portal oben links die Option **Ressource erstellen** aus.

2. Wählen Sie **Compute** und dann **Virtueller Computer** aus.

3. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |  |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **myResourceGroup** aus. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVMWeb** ein. |
    | Region | Wählen Sie **(USA) USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Übernehmen Sie die Standardeinstellung (keine Redundanz erforderlich). |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie **Standard_D2s_V3** aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |   |
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |

4. Wählen Sie die Registerkarte **Netzwerk** aus.

5. Geben Sie auf der Registerkarte **Netzwerk** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **Standard (10.0.0.0/24)** aus. |
    | Öffentliche IP-Adresse | Übernehmen Sie den Standardwert einer neuen öffentlichen IP-Adresse. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Keine**. | 

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

Führen Sie die Schritte 1 bis 7 erneut aus, geben Sie jedoch in Schritt 3 dem virtuellen Computer den Namen **myVMMgmt**. Die Bereitstellung des virtuellen Computers dauert einige Minuten. 

Führen Sie den nächsten Schritt erst durch, nachdem der virtuelle Computer bereitgestellt wurde.

## <a name="associate-network-interfaces-to-an-asg"></a>Zuordnen von Netzwerkschnittstellen zu einer Anwendungssicherheitsgruppe

Beim Erstellen der virtuellen Computer im Portal wird für jeden virtuellen Computer eine Netzwerkschnittstelle erstellt und mit dem jeweiligen virtuellen Computer verbunden. 

Fügen Sie die Netzwerkschnittstelle für die einzelnen virtuellen Computer einer der Anwendungssicherheitsgruppen hinzu, die Sie zuvor erstellt haben:

1. Beginnen Sie oben im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** mit der Eingabe von **myVMWeb**. Wenn der virtuelle Computer **myVMWeb** in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.

2. Wählen Sie unter **Einstellungen** die Option **Netzwerk**.  

3. Wählen Sie die Registerkarte **Anwendungssicherheitsgruppen** und dann **Anwendungssicherheitsgruppen konfigurieren** aus.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Anwendungssicherheitsgruppen konfigurieren" border="true":::

4. Wählen Sie unter **Anwendungssicherheitsgruppen konfigurieren** die Option **myAsgWebServers** aus. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Auswählen von Anwendungssicherheitsgruppen" border="true":::

5. Führen Sie die Schritte 1 und 2 erneut aus. Suchen Sie dabei nach dem virtuellen Computer **myVMMgmt**, und wählen Sie die Anwendungssicherheitsgruppe **myAsgMgmtServers** aus.

## <a name="test-traffic-filters"></a>Testen von Datenverkehrsfiltern

1. Stellen Sie eine Verbindung mit dem virtuellen Computer **myVMMgmt** her. Geben Sie oben im Portal im Suchfeld **myVMMgmt** ein. Wenn **myVMMgmt** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus. Wählen Sie die Schaltfläche **Verbinden** aus.

2. Wählen Sie **RDP-Datei herunterladen** aus.

3. Öffnen Sie die heruntergeladene RDP-Datei, und wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

4. Klicken Sie auf **OK**.

5. Während des Verbindungsprozesses wird unter Umständen eine Zertifikatwarnung angezeigt. Sollte eine Warnung angezeigt werden, wählen Sie **Ja** bzw. **Weiter** aus, um mit der Verbindungsherstellung fortzufahren.

    Die Verbindung wird erfolgreich hergestellt, da an Port 3389 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe **myAsgMgmtServers** zugelassen wird. 
    
    Die Netzwerkschnittstelle für **myVMMgmt** ist mit der Anwendungssicherheitsgruppe **myAsgMgmtServers** verknüpft und lässt die Verbindung zu.

6. Öffnen Sie eine PowerShell-Sitzung für **myVMMgmt**. Stellen Sie mithilfe des folgenden Beispiels eine Verbindung mit **myVMWeb** her: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    Die RDP-Verbindung von **myVMMgmt** mit **myVMWeb** ist erfolgreich, da virtuelle Computer im selben Netzwerk standardmäßig über jeden Port miteinander kommunizieren können.
    
    Es ist nicht möglich, über das Internet eine RDP-Verbindung mit dem virtuellen Computer **myVMWeb** herzustellen. Die Sicherheitsregel für **myAsgWebServers** verhindert eingehende Verbindungen aus dem Internet an Port 3389. Eingehender Datenverkehr aus dem Internet wird standardmäßig für alle Ressourcen verweigert.

7. Geben Sie den folgenden Befehl über eine PowerShell-Sitzung auf dem virtuellen Computer **myVMWeb** ein, um Microsoft IIS auf dem virtuellen Computer **myVMWeb** zu installieren:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Trennen Sie nach Abschluss der IIS-Installation die Verbindung mit dem virtuellen Computer **myVMWeb**. Dadurch befinden Sie sich in der Remotedesktopverbindung des virtuellen Computers **myVMMgmt**.

9. Trennen Sie die Verbindung mit dem virtuellen Computer **myVMMgmt**.

10. Beginnen Sie oben im Azure-Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** mit der Eingabe von **myVMWeb** über Ihren Computer. Wenn **myVMWeb** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus. Notieren Sie die **öffentliche IP-Adresse** Ihres virtuellen Computers. Die im folgenden Beispiel dargestellte Adresse lautet 23.96.39.113. Ihre Adresse lautet jedoch anders:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Öffentliche IP-Adresse" border="true":::
    
11. Navigieren Sie auf Ihrem Computer in einem Internetbrowser zu `http://<public-ip-address-from-previous-step>`, um sich zu vergewissern, dass Sie vom Internet aus auf den Webserver **myVMWeb** zugreifen können. 

Die Willkommensseite von IIS wird angezeigt, da an Port 80 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe **myAsgWebServers** zugelassen wird. 

Die für **myVMWeb** angefügte Netzwerkschnittstelle ist mit der Anwendungssicherheitsgruppe **myAsgWebServers** verknüpft und lässt die Verbindung zu. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld **Suche** die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen **myResourceGroup** ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie:

* eine Netzwerksicherheitsgruppe erstellt und dem Subnetz eines virtuellen Netzwerks zugeordnet. 
* Anwendungssicherheitsgruppen für das Web und die Verwaltung erstellt.
* zwei virtuelle Computer erstellt.
* die Netzwerkfilterung der Anwendungssicherheitsgruppe getestet.

Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](./network-security-groups-overview.md) sowie unter [Verwalten einer Netzwerksicherheitsgruppe](manage-network-security-group.md).

Azure leitet standardmäßig Datenverkehr zwischen Subnetzen weiter. Sie können Datenverkehr zwischen Subnetzen aber beispielsweise auch über einen virtuellen Computer weiterleiten, der als Firewall fungiert. 

Im nächsten Tutorial erfahren Sie, wie Sie eine Routingtabelle erstellen.
> [!div class="nextstepaction"]
> [Erstellen einer Routingtabelle](./tutorial-create-route-table-portal.md)

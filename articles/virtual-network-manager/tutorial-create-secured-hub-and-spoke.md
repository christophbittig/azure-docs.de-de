---
title: 'Tutorial: Erstellen eines geschützten Hub-and-Spoke-Netzwerks'
description: In diesem Tutorial erfahren Sie, wie Sie mit Azure Virtual Network Manager ein Hub-and-Spoke-Netzwerk erstellen. Anschließend sichern Sie Ihre sämtlichen virtuellen Netzwerke mit einer Sicherheitsrichtlinie.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 672b1218a52ee94e226c5b7c763c8e7622b60267
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101528"
---
# <a name="tutorial-create-a-secured-hub-and-spoke-network"></a>Tutorial: Erstellen eines geschützten Hub-and-Spoke-Netzwerks

In diesem Tutorial erstellen Sie mit Azure Virtual Network Manager eine Hub-and-Spoke-Netzwerktopologie. Anschließend stellen Sie ein Gateway für virtuelle Netzwerke im virtuellen Hubnetzwerk bereit, damit Ressourcen in den virtuellen Spokenetzwerken über VPN mit Remotenetzwerken kommunizieren können. Außerdem konfigurieren Sie eine Sicherheitskonfiguration, um ausgehenden Netzwerkdatenverkehr zum Internet an den Ports 80 und 443 zu blockieren. Schließlich überprüfen Sie anhand der Einstellungen für virtuelle Netzwerke und Computer, ob die Konfigurationen ordnungsgemäß angewendet wurden.

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen mehrerer Netzwerke
> * Bereitstellen eines Gateways für virtuelle Netzwerke
> * Erstellen einer Hub-and-Spoke-Netzwerktopologie
> * Erstellen einer Sicherheitskonfiguration, die Datenverkehr an den Ports 80 und 443 blockiert
> * Überprüfen, ob Konfigurationen angewendet wurden

## <a name="prerequisite"></a>Voraussetzung

* Bevor Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst eine [Azure Virtual Network Manager-Instanz erstellen](create-virtual-network-manager-portal.md).

## <a name="create-virtual-networks"></a>Erstellen virtueller Netzwerke

Die folgenden Schritte führen Sie durch die Erstellung von drei virtuellen Netzwerken. Eins befindet sich in der Region *USA, Westen*, die anderen beiden in der Region *USA, Osten*.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach **Virtuelles Netzwerk**. Wählen Sie dann **Erstellen** aus, um mit dem Konfigurieren des virtuellen Netzwerks zu beginnen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet.png" alt-text="Screenshot: Seite zum Erstellen eines virtuellen Netzwerks":::

1. Geben Sie auf der Registerkarte *Grundlagen* die folgenden Informationen an, oder wählen Sie sie aus:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-basic.png" alt-text="Screenshot: Registerkarte mit Grundlagen für das virtuelle Hub-and-Spoke-Netzwerk":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie das Abonnement aus, in dem Sie dieses virtuelle Netzwerk bereitstellen möchten. |
    | Ressourcengruppe | Wählen Sie eine Ressourcengruppe zum Speichern des virtuellen Netzwerks aus, oder erstellen Sie eine neue Ressourcengruppe. In dieser Schnellstartanleitung wird eine Ressourcengruppe namens **myAVNMResourceGroup** verwendet.
    | Name | Geben Sie **VNet-A-WestUS** als Namen des virtuellen Netzwerks ein. |
    | Region | Wählen Sie die Region **USA, Westen** aus. |

 1. Legen Sie auf der Registerkarte *IP-Adressen* den folgenden Netzwerkadressraum fest:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-addresses.png" alt-text="Screenshot: Registerkarte „IP-Adressen“ für das virtuelle Hub-and-Spoke-Netzwerk":::

    | Einstellung | Wert |
    | -------- | ----- |
    | IPv4-Adressraum | Geben Sie **10.3.0.0/16** als Adressraum ein. |
    | Subnetzname | Geben Sie den Namen **default** für das Subnetz ein. |
    | Subnetzadressraum | Geben Sie den Subnetzadressraum **10.3.0.0/24** ein. |

1. Wählen Sie **Überprüfen + erstellen** und dann **Erstellen** aus, um das virtuelle Netzwerk bereitzustellen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet-validation.png" alt-text="Screenshot: Seite zur Überprüfung des virtuellen Hub-and-Spoke-Netzwerks":::

1. Wiederholen Sie die Schritte 2–5, um zwei weitere virtuelle Netzwerke mit den folgenden Informationen in derselben Ressourcengruppe zu erstellen:

    **Zweites virtuelles Netzwerk**:
    * Name: **VNet-A-EastUS**
    * Region: **USA, Osten**
    * IPv4-Adressraum: **10.4.0.0/16**
    * Subnetzname: **default**
    * Subnetzadressraum: **10.4.0.0/24**

    **Drittes virtuelles Netzwerk**:
    * Name: **VNet-B-EastUS**
    * Region: **USA, Osten**
    * IPv4-Adressraum: **10.5.0.0/16**
    * Subnetzname: **default**
    * Subnetzadressraum: **10.5.0.0/24**

## <a name="deploy-a-virtual-network-gateway"></a>Bereitstellen eines Gateways für virtuelle Netzwerke

Stellen Sie im virtuellen Hubnetzwerk ein Gateway für virtuelle Netzwerke bereit. Dieses Gateway für virtuelle Netzwerke ist für die Spokeeinstellung *Hub als Gateway verwenden* erforderlich.

1. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach **Gateway für virtuelle Netzwerke**. Wählen Sie dann **Erstellen** aus, um mit dem Konfigurieren des Gateways für virtuelle Netzwerke zu beginnen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-gateway.png" alt-text="Screenshot: Seite zum Erstellen eines Gateways für virtuelle Netzwerke":::

1. Geben Sie auf der Registerkarte *Grundlagen* die folgenden Einstellungen an, oder wählen Sie sie aus:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-basics.png" alt-text="Screenshot: Registerkarte „Grundlagen“ auf der Seite zum Erstellen eines Gateways für virtuelle Netzwerke" lightbox="./media/tutorial-create-secured-hub-and-spoke/gateway-basics-expanded.png":::

1. Wählen Sie **Überprüfen + erstellen** und nach erfolgreicher Validierung **Erstellen** aus. Die Bereitstellung eines Gateways für virtuelle Netzwerke kann etwa 30 Minuten dauern. Sie können mit dem nächsten Abschnitt fortfahren, während Sie auf den Abschluss dieser Bereitstellung warten.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-validation.png" alt-text="Screenshot: Seite zur Validierung beim Erstellen eines Gateways für virtuelle Netzwerke":::

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

1. Wechseln Sie zu Ihrer Azure Virtual Network Manager-Instanz. In diesem Tutorial wird davon ausgegangen, dass Sie mithilfe der [Schnellstartanleitung](create-virtual-network-manager-portal.md) bereits eine Instanz erstellt haben.

1. Wählen Sie unter *Einstellungen* zunächst **Netzwerkgruppen** und dann **+ Hinzufügen** aus, um eine neue Netzwerkgruppe zu erstellen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Screenshot: Schaltfläche zum Hinzufügen einer Netzwerkgruppe":::

1. Geben Sie auf der Registerkarte *Grundlegende Einstellungen* folgende Informationen ein:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-basics.png" alt-text="Screenshot: Registerkarte „Grundlagen“ zum Erstellen einer Netzwerkgruppe":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myNetworkGroupB** als Netzwerkgruppennamen ein. |
    | BESCHREIBUNG | Geben Sie eine Beschreibung dieser Netzwerkgruppe an. |

1. Wählen Sie die Registerkarte **Bedingungsanweisungen**. Wählen Sie in der Dropdownliste als *Parameter* die Option **Name** aus. Wählen Sie für *Operator* die Option **Enthält** aus. Geben Sie unter *Bedingung* die Zeichenfolge **VNet-** ein. Diese Bedingungsanweisung fügt dieser Netzwerkgruppe die drei zuvor erstellten virtuellen Netzwerke hinzu.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-conditional.png" alt-text="Screenshot: Registerkarte „Bedingungsanweisungen“ zum Erstellen einer Netzwerkgruppe":::

1. Wählen Sie **Auswerten** aus, wenn Sie die ausgewählten virtuellen Netzwerke überprüfen müssen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/evaluate-vnet.png" alt-text="Screenshot: Seite mit gültigen virtuellen Netzwerken":::

1. Wählen Sie **Überprüfen + erstellen** und nach erfolgreicher Validierung **Erstellen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-validation.png" alt-text="Screenshot: Seite zum Überprüfen beim Erstellen einer Netzwerkgruppe":::

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Erstellen einer Hub-and-Spoke-Konnektivitätskonfiguration

1. Wählen Sie unter *Einstellungen* die Option **Konfiguration** und anschließend **+ Konfiguration hinzufügen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-configuration.png" alt-text="Screenshot: Schaltfläche „Konfiguration hinzufügen“ für Network Manager":::

1. Wählen Sie im Dropdownmenü **Konnektivität** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Screenshot: Dropdownmenü „Konfiguration“":::

1. Geben Sie die folgenden Informationen für die Konnektivitätskonfiguration ein, und wählen Sie sie aus:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/connectivity-configuration.png" alt-text="Screenshot: Seite „Konnektivitätskonfiguration hinzufügen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **HubA** als Namen der Konfiguration ein. |
    | Beschreibung | Geben Sie eine Beschreibung der Funktion dieser Konnektivitätskonfiguration an. |
    | Topologie | Wählen Sie **Hub-and-Spoke** aus. |

1. Wenn Sie die *Hub-and-Spoke*-Topologie auswählen, werden weitere Felder angezeigt. Wählen Sie die folgenden Einstellungen aus:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/hub-configuration.png" alt-text="Screenshot: Auswählen eines Hubs für die Konnektivitätskonfiguration":::

    | Einstellungen | Wert |
    | -------- | ----- |
    | Hub | Wählen Sie **VNet-A-West** als virtuelles Hubnetzwerk aus. |
    | Vorhandene Peerings | Lassen Sie diese Option **deaktiviert**. |
    | Spokenetzwerkgruppen | Wählen Sie **Netzwerkgruppen hinzufügen** aus, und fügen Sie **myNetworkGroupB** zur Konfiguration hinzu. |

1. Nachdem Sie die Netzwerkgruppe hinzugefügt haben, wählen Sie die folgenden Optionen aus. Wählen Sie dann „Hinzufügen“ aus, um die Konnektivitätskonfiguration zu erstellen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-options.png" alt-text="Screenshot: Einstellungen für die Netzwerkgruppenkonfiguration":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Transitivität | Aktivieren Sie das Kontrollkästchen **Peering innerhalb der Netzwerkgruppe aktivieren**. Diese Einstellung ermöglicht es virtuellen Spokenetzwerken in der Netzwerkgruppe in derselben Region, direkt miteinander zu kommunizieren. |
    | Globales Mesh | Lassen Sie diese Option **deaktiviert**. Da sich beide Spokes in derselben Region befinden, ist diese Einstellung nicht erforderlich. |
    | Gateway | Wählen Sie **Hub als Gateway verwenden** aus. |

## <a name="deploy-the-connectivity-configuration"></a>Bereitstellen der Konnektivitätskonfiguration

Stellen Sie sicher, dass das Gateway für virtuelle Netzwerke erfolgreich bereitgestellt wurde, bevor Sie die Konnektivitätskonfiguration bereitstellen. Wenn Sie eine Hub-and-Spoke-Konfiguration mit aktivierter Option **Hub als Gateway verwenden** bereitstellen und kein Gateway vorhanden ist, tritt bei der Bereitstellung ein Fehler auf. Weitere Informationen finden Sie unter [Verwenden eines Hubs als Gateway](concept-connectivity-configuration.md#use-hub-as-a-gateway).

1. Wählen Sie zunächst unter *Einstellungen* die Option **Bereitstellungen** und anschließend **Konfiguration bereitstellen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Screenshot: Seite „Bereitstellungen“ in Network Manager":::

1. Wählen Sie den Konfigurationstyp **Konnektivität** und die Konfiguration **HubA** aus, die Sie im letzten Abschnitt erstellt haben. Wählen Sie **USA, Westen** und **USA, Osten** als Zielregionen und dann **Bereitstellen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-configuration.png" alt-text="Screenshot: Seite „Konfiguration bereitstellen“":::

1. Wählen Sie **OK** aus, um zu bestätigen, dass Sie eine vorhandene Konfiguration überschreiben und die Sicherheitsadministratorkonfiguration bereitstellen möchten. 

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Screenshot: Meldung zur Bestätigung der Bereitstellung":::

1. Die Bereitstellung sollte jetzt in der Liste für diese Regionen angezeigt werden. Die Bereitstellung der Konfiguration kann etwa 15 bis 20 Minuten dauern.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Screenshot: „Bereitstellung wird ausgeführt“ in der Bereitstellungsliste":::

## <a name="create-security-configuration"></a>Erstellen einer Sicherheitskonfiguration

1. Wählen Sie unter *Einstellungen* erneut die Option **Konfiguration** und anschließend **+ Konfiguration hinzufügen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-security-configuration.png" alt-text="Screenshot: Hinzufügen einer weiteren Konfiguration für Network Manager":::

1. Wählen Sie im Menü die Option **Sicherheitsadministrator** aus, um mit dem Erstellen einer Sicherheitsadministratorkonfiguration zu beginnen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-drop-down.png" alt-text="Screenshot: Sicherheitsadministrator im Dropdownmenü":::

1. Geben Sie den Namen **mySecurityConfig** für die Konfiguration ein, und wählen Sie dann **+ Regelsammlung hinzufügen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-admin-configuration.png" alt-text="Screenshot: Seite „Sicherheitsadministratorkonfiguration“":::

1. Geben Sie den Namen **myRuleCollection** für die Regelsammlung ein, und wählen Sie **myNetworkGroupB** als Zielnetzwerkgruppe aus. Klicken Sie anschließend auf **+ Regel hinzufügen**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule-collection.png" alt-text="Screenshot: Seite „Regelsammlung hinzufügen“":::

1. Geben Sie die folgenden Einstellungen ein, wählen Sie sie aus, und wählen Sie dann **Hinzufügen** aus:

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule.png" alt-text="Screenshot: Seite „Regel hinzufügen“":::

1. Wählen Sie **Speichern** aus, um die Regelsammlung zur Konfiguration hinzuzufügen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/save-rule-collection.png" alt-text="Screenshot: Schaltfläche „Speichern“ für eine Regelsammlung":::

1. Wählen Sie **Hinzufügen** aus, um die Sicherheitsadministratorkonfiguration zu erstellen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-configuration.png" alt-text="Screenshot: Schaltfläche „Hinzufügen“ zum Erstellen der Konfiguration":::

## <a name="deploy-the-security-admin-configuration"></a>Bereitstellen der Sicherheitsadministratorkonfiguration

1. Wählen Sie zunächst unter *Einstellungen* die Option **Bereitstellungen** und anschließend **Konfiguration bereitstellen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployments.png" alt-text="Screenshot: Seite „Sicherheitsbereitstellungen“ in Virtual Network Manager":::

1. Wählen Sie den Konfigurationstyp **Sicherheitsadministrator** und die Konfiguration **mySecurityConfig** aus, die Sie im letzten Abschnitt erstellt haben. Wählen Sie **USA, Westen** und **USA, Osten** als Zielregionen und dann **Bereitstellen** aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-security.png" alt-text="Screenshot: Bereitstellen einer Sicherheitskonfiguration":::

1. Wählen Sie **OK** aus, um zu bestätigen, dass Sie eine vorhandene Konfiguration überschreiben und die Sicherheitsadministratorkonfiguration bereitstellen möchten.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Screenshot: Bestätigungsmeldung für die Bereitstellung einer Sicherheitskonfiguration":::

1. Die Bereitstellung sollte jetzt in der Liste für die ausgewählte Region angezeigt werden. Die Bereitstellung der Konfiguration kann etwa 15 bis 20 Minuten dauern.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-in-progress.png" alt-text="Screenshot: „Sicherheitsbereitstellung wird ausgeführt“ in der Bereitstellungsliste":::

## <a name="verify-deployment-of-configurations"></a>Überprüfen der Bereitstellung von Konfigurationen

### <a name="verify-from-a-virtual-network"></a>Überprüfen über ein virtuelles Netzwerk

1. Wechseln Sie zum virtuellen Netzwerk **VNet-A-WestUS**, und wählen Sie unter *Einstellungen* die Option **Network Manager** aus. Wie Sie sehen, wird die Konnektivitätskonfiguration **HubA** angewendet.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-connectivity-configuration.png" alt-text="Screenshot: auf das virtuelle Netzwerk angewendete Konnektivitätskonfiguration":::

1. Wählen Sie unter *Einstellungen* die Option **Peerings** aus. Die VNet-Peerings, die von Virtual Network Manager erstellt wurden, weisen im Namen die Zeichenfolge *AVNM* auf.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-peerings.png" alt-text="Screenshot: von Virtual Network Manager erstellte VNet-Peerings":::

1. Wählen Sie die Registerkarte **Sicherheitsadministrator** aus, um die Sicherheitsadministratorregeln anzuzeigen, die auf dieses virtuelle Netzwerk angewendet werden.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-admin-configuration.png" alt-text="Screenshot: auf das virtuelle Netzwerk angewendete Sicherheitsadministratorregeln":::

### <a name="verify-from-a-vm"></a>Überprüfen über einen virtuellen Computer

1. Stellen Sie eine Windows-Test-VM in **VNet-A-EastUS** bereit. 

1. Wechseln Sie zu der in *VNet-A-EastUS* erstellten Test-VM, und wählen Sie unter *Einstellungen* die Option **Netzwerk** aus. Wählen Sie **Portregeln für ausgehenden Datenverkehr** aus. Daraufhin wird die angewendete Sicherheitsadministratorregel angezeigt.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-security-rules.png" alt-text="Screenshot: Netzwerksicherheitsregeln der Test-VM":::

1. Wählen Sie den Namen der Netzwerkschnittstelle aus.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-network-settings.png" alt-text="Screenshot: Netzwerkeinstellungen der Test-VM":::

1. Wählen Sie dann unter *Support + Problembehandlung* die Option **Effektive Routen** aus, um die Routen für die VNet-Peerings anzuzeigen. Die Route `10.3.0.0/16` mit dem nächsten Hop `VNetGlobalPeering` ist die Route zum virtuellen Hubnetzwerk. Die Route `10.5.0.0/16` mit dem nächsten Hop `ConnectedGroup` ist die Route zum anderen virtuellen Spokenetzwerk. Alle virtuellen Spokenetzwerke befinden sich in einer *ConnectedGroup*, wenn die **Transitivität** aktiviert ist.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/effective-routes.png" alt-text="Screenshot: effektive Routen von der Netzwerkschnittstelle der Test-VM" lightbox="./media/tutorial-create-secured-hub-and-spoke/effective-routes-expanded.png" :::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure Virtual Network Manager-Instanz nicht mehr benötigen, müssen Sie sicherstellen, dass alle der folgenden Bedingungen erfüllt sind, bevor Sie die Ressource löschen:

* In keiner Region sind Konfigurationen bereitgestellt.
* Alle Konfigurationen wurden gelöscht.
* Alle Netzwerkgruppen wurden gelöscht.

Verwenden Sie die [Prüfliste zum Entfernen](concept-remove-components-checklist.md) von Komponenten, um sicherzustellen, dass keine untergeordneten Ressourcen mehr vorhanden, bevor Sie die Ressourcengruppe löschen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Netzwerkdatenverkehr mit einer [Sicherheitsadministratorkonfiguration](how-to-block-network-traffic-portal.md) blockieren.

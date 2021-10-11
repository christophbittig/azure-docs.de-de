---
title: 'Tutorial: Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer: Azure-Portal'
titlesuffix: Azure Virtual Network
description: In diesem Tutorial erfahren Sie, wie Sie beide Routingpräferenzoptionen für einen virtuellen Computer im Azure-Portal konfigurieren.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368933"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>Tutorial: Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer im Azure-Portal

In diesem Artikel erfahren Sie, wie Sie die beiden [Routingpräferenzoptionen](routing-preference-overview.md) (Internet und globales Microsoft-Netzwerk) für einen virtuellen Computer (VM) konfigurieren. Diese Konfiguration wird mit zwei virtuellen Netzwerkschnittstellen erreicht. Eine Netzwerkschnittstelle wird mit einer öffentlichen IP-Adresse konfiguriert, die über das globale Microsoft-Netzwerk weitergeleitet wird. Die andere Netzwerkschnittstelle wird mit einer öffentlichen IP-Adresse konfiguriert, die über ein ISP-Netzwerk weitergeleitet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers mit einer öffentlichen IP-Adresse mit der Routingpräferenz **Microsoft-Netzwerk**.
> * Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz **Internet**.
> * Erstellen einer sekundären Netzwerkschnittstelle für die VM.
> * Ordnen Sie die öffentliche IP-Adresse für die Routingpräferenz **Internet** der sekundären Netzwerkschnittstelle des virtuellen Computers zu.
> * Ordnen Sie die sekundäre Netzwerkschnittstelle dem virtuellen Computer zu.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-machine"></a>Erstellen des virtuellen Computers

In diesem Abschnitt erstellen Sie eine VM und eine öffentliche IP-Adresse. Wählen Sie während der Konfiguration der öffentlichen IP-Adresse **Microsoft-Netzwerk** als Routingpräferenz aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie im Portalsuchfeld **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

3. Wählen Sie unter **Virtuelle Computer** die Option **+ Erstellen** und dann **+ Virtueller Computer** aus.

4. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **TutorVMMixRoutePref-rg** ein. Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie die Option **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen2** aus. |
    | Azure Spot-Instanz | Übernehmen Sie die Standardeinstellung (deaktiviert). |
    | Size | Wählen Sie eine Größe aus. |
    | **Administratorkonto** |   |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Übernehmen Sie die Standardeinstellung **RDP (3389)** . </br> _**Bei Produktionsworkloads wird vom Öffnen von Port 3389 über das Internet abgeraten**_. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="Screenshot: Erstellen einer VM":::

5. Wählen Sie **Weiter: Datenträger** und dann **Weiter: Netzwerk** aus, oder wählen Sie die Registerkarte **Netzwerk** aus.

6. Geben Sie auf der Registerkarte „Netzwerk“ die folgenden Informationen ein, bzw. wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerkschnittstelle** |   |
    | Virtuelles Netzwerk | Behalten Sie den Standard **(neu) TutorVMMixRoutePref-rg-vnet** bei. |
    | Subnet | Übernehmen Sie die Standardeinstellung **(neu) Standard (10.1.0.0/24)** . |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myPublicIP** unter **Name** ein. </br> Wählen Sie unter **SKU** die Option **Standard** aus. </br> Wählen Sie unter **Routingpräferenz** die Option **Microsoft-Netzwerk** aus. </br> Klicken Sie auf **OK**. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="Screenshot: Erstellen einer öffentlichen IP-Adresse mit Routingpräferenz „Microsoft“.":::

7. Klicken Sie auf **Überprüfen + erstellen**.

8. Klicken Sie auf **Erstellen**.

## <a name="create-the-public-ip-address"></a>Erstellen der öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie eine öffentliche IP-Adresse mit der Routingpräferenz **Internet**.

1. Geben Sie im Portalsuchfeld **Öffentliche IP-Adresse** ein. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

2. Wählen Sie **+ Erstellen** aus.

3. Geben Sie in **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | IP-Version | Übernehmen Sie den Standardwert **IPv4**. |
    | SKU | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif | Übernehmen Sie den Standardwert **Regional**. |
    | **IPv4-IP-Adresskonfiguration** |   |
    | Name | Geben Sie **myPublicIP-IR** ein. |
    | Routingpräferenz | Wählen Sie **Internet** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **TutorVMMixRoutePref-rg** aus. |
    | Standort | Wählen Sie **USA, Westen 2** aus. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="Screenshot: Erstellen einer öffentlichen IP-Adresse mit Routingpräferenz „Internet“.":::

4. Wählen Sie **Erstellen** aus.

## <a name="create-the-secondary-nic"></a>Erstellen der sekundären Netzwerkschnittstelle

In diesem Abschnitt erstellen Sie eine sekundäre Netzwerkschnittstelle für den virtuellen Computer, den Sie zuvor erstellt haben.

1. Geben Sie im Portalsuchfeld **Netzwerkschnittstelle** ein. Wählen Sie in den Suchergebnissen **Netzwerkschnittstellen** aus.

2. Wählen Sie **+ Erstellen** aus.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **TutorVMMixRoutePref-rg** aus. |
    | Name | Geben Sie **myVMNic2** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Virtuelles Netzwerk | Wählen Sie **TutorVMMixRoutePref-rg-vnet** aus. |
    | Subnet | Wählen Sie **TutorVMMixRoutePref-rg-vnet/default (10.1.0.0/24)** aus. |
    | Netzwerksicherheitsgruppe | Wählen Sie **myVM-nsg** aus. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="Screenshot: Erstellen der sekundären Netzwerkschnittstelle.":::

4. Wählen Sie die Registerkarte **Überprüfen + erstellen** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Überprüfen + erstellen**.

5. Klicken Sie auf **Erstellen**.

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>Zuordnen der öffentlichen IP-Adresse zur sekundären Netzwerkschnittstelle

In diesem Abschnitt ordnen Sie die öffentliche IP-Adresse der Routingpräferenz **Internet**, die Sie zuvor erstellt haben, der Netzwerkschnittstelle zu, die Sie im vorherigen Abschnitt erstellt haben.

1. Geben Sie im Portalsuchfeld **Öffentliche IP-Adresse** ein. Wählen Sie in den Suchergebnissen **Öffentliche IP-Adressen** aus.

2. Wählen Sie **myPublicIP-IR** aus.

3. Wählen Sie auf der Seite **Übersicht** von **myPublic-IR** die Option **Zuordnen** aus.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="Screenshot der Übersichtsseite „myPublicIP-IR“ mit der Schaltfläche „Zuordnen“.":::

4. Wählen Sie unter **Öffentliche IP-Adresse zuordnen** im Pulldownfeld **Ressourcentyp** die Option **Netzwerkschnittstelle** aus.

5. Wählen Sie im Pulldownfeld **Netzwerkschnittstelle** die Option **myVMNic2** aus.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="Screenshot: Auswählen der Ressource, die der öffentlichen IP-Adresse zugeordnet werden soll.":::

6. Klicken Sie auf **OK**.

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>Zuordnen der sekundären Netzwerkschnittstelle zum virtuellen Computer

In diesem Abschnitt ordnen Sie die sekundäre Netzwerkschnittstelle, die Sie zuvor erstellt haben, dem virtuellen Computer zu.

1. Geben Sie im Portalsuchfeld **Virtueller Computer** ein. Wählen Sie in den Suchergebnissen **Virtuelle Computer** aus.

2. Wählen Sie **myVM** aus.

3. Beenden Sie **myVM**, wenn es ausgeführt wird, andernfalls fahren Sie mit dem nächsten Schritt fort.

4. Wählen Sie in **myVM** unter **Einstellungen** die Option **Netzwerk** aus.

5. Wählen Sie unter **Netzwerk** von **myVM** die Option **Netzwerkschnittstelle anfügen** aus.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="Screenshot der Übersichtsseite für myVM-Netzwerke.":::

6. Wählen Sie unter **Netzwerkschnittstelle anfügen** im Pulldownfeld die Option **myVMNic2** aus.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="Screenshot von „Netzwerkschnittstelle anfügen“.":::

7. Klicken Sie auf **OK**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie die öffentlichen IP-Adressen und die VMs wie folgt:

1. Geben Sie oben im Portal im Suchfeld **Ressourcengruppe** ein.

2. Wählen Sie in den Suchergebnissen **Ressourcengruppen** aus.

3. Wählen Sie **TutorVMMixRoutePref-rg** aus.

4. Wählen Sie die Option **Ressourcengruppe löschen**.

5. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie ein Präfix für öffentliche IP-Adressen erstellen:
> [!div class="nextstepaction"]
> [Konfigurieren der Routingpräferenz für einen Kubernetes-Cluster mithilfe der Azure CLI](routing-preference-azure-kubernetes-service-cli.md)
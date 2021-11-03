---
title: Erstellen einer Hub-Spoke-Topologie mit Azure Virtual Network Manager (Vorschauversion)
description: Erfahren Sie, wie Sie eine Hub-Spoke-Netzwerktopologie mit Azure Virtual Network Manager erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c07eccfda6492a5957ce9d2f0df6e0133e5b9d3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095499"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview"></a>Erstellen einer Hub-Spoke-Topologie mit Azure Virtual Network Manager (Vorschauversion)

In diesem Artikel erfahren Sie, wie Sie eine Hub-Spoke-Netzwerktopologie mit Azure Virtual Network Manager erstellen. Bei dieser Konfiguration wählen Sie ein virtuelles Netzwerk aus, das als Hub fungieren soll. Die virtuellen Spoke-Netzwerke verfügen dann standardmäßig über ein bidirektionales Peering ausschließlich mit dem Hub. Sie können auch eine direkte Konnektivität zwischen den virtuellen Spoke-Netzwerken aktivieren und es diesen Netzwerken ermöglichen, das virtuelle Netzwerkgateway im Hub zu verwenden.

> [!IMPORTANT]
> *Azure Virtual Network Manager* ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Informieren Sie sich über die [Hub-and-Spoke](concept-connectivity-configuration.md#hub-and-spoke-topology)-Netzwerktopologie.
* Erstellen Sie eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifizieren Sie virtuelle Netzwerke, die Sie in der Hub-Spoke-Konfiguration verwenden möchten, oder erstellen Sie neue [virtuelle Netzwerke](../virtual-network/quick-create-portal.md). 

## <a name="create-a-network-group"></a><a name="group"></a> Erstellen einer Netzwerkgruppe

In diesem Abschnitt lernen Sie, wie Sie eine Netzwerkgruppe mit den virtuellen Netzwerken erstellen, die Sie für die Hub-Spoke-Netzwerktopologie verwenden werden.

1. Wechseln Sie zu Ihrer Azure Virtual Network Manager-Instanz. In diesem Leitfaden wird davon ausgegangen, dass Sie mithilfe der [Schnellstartanleitung](create-virtual-network-manager-portal.md) bereits eine Instanz erstellt haben.

1. Wählen Sie unter *Einstellungen* zunächst **Netzwerkgruppen** und dann **+ Hinzufügen** aus, um eine neue Netzwerkgruppe zu erstellen.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Screenshot der Schaltfläche „Netzwerkgruppe hinzufügen“":::

1. Geben Sie auf der Registerkarte *Grundlagen* einen **Namen** und eine **Beschreibung** für die Netzwerkgruppe ein.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="Screenshot der Registerkarte „Grundlagen“ beim Hinzufügen einer Netzwerkgruppe":::

1. Um ein virtuelles Netzwerk manuell hinzuzufügen, wählen Sie die Registerkarte **Statische Gruppenmitglieder** aus. Weitere Informationen finden Sie unter [statische Mitglieder](concept-network-groups.md#static-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="Screenshot der Registerkarte „Statische Gruppenmitglieder“":::

1. Um virtuelle Netzwerke dynamisch hinzuzufügen, wählen Sie die Registerkarte **Bedingungsanweisungen** aus. Weitere Informationen finden Sie unter [dynamische Mitgliedschaft](concept-network-groups.md#dynamic-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="Screenshot der Registerkarte „Bedingungsanweisungen“":::

1. Wenn Sie mit den für die Netzwerkgruppe ausgewählten virtuellen Netzwerken zufrieden sind, wählen Sie **Überprüfen + erstellen** aus. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus.
 
## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Erstellen einer Hub-Spoke-Konnektivitätskonfiguration

In diesem Abschnitt erfahren Sie, wie Sie eine Hub-Spoke-Konfiguration mit der im vorherigen Abschnitt erstellten Netzwerkgruppe erstellen.

1. Wählen Sie unter *Einstellungen* die Option **Konfiguration** und anschließend **+ Konfiguration hinzufügen** aus.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Screenshot der Konfigurationsliste":::

1. Wählen Sie im Dropdownmenü **Konnektivität** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Screenshot des Dropdownmenüs „Konfiguration“":::

1. Geben Sie auf der Seite *Konnektivitätskonfiguration hinzufügen* die folgenden Informationen ein, oder wählen Sie sie aus:

    :::image type="content" source="./media/how-to-create-hub-and-spoke/connectivity-configuration.png" alt-text="Screenshot der Seite „Konnektivitätskonfiguration hinzufügen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie einen *Namen* für diese Konfiguration ein. |
    | BESCHREIBUNG | *Optional* Geben Sie eine Beschreibung der Aufgaben dieser Konfiguration ein. |
    | Topologie | Wählen Sie die **Hub-Spoke**-Topologie aus. |
    | Hub | Wählen Sie ein virtuelles Netzwerk aus, das als virtuelles Hub-Netzwerk fungieren soll. |
    | Vorhandene Peerings | Aktivieren Sie dieses Kontrollkästchen, wenn Sie alle zuvor erstellten VNET-Peerings zwischen den virtuellen Netzwerken in der in dieser Konfiguration definierten Netzwerkgruppe entfernen möchten. |

1. Wählen Sie dann **+ Netzwerkgruppen hinzufügen** aus. 

1. Wählen Sie auf der Seite *Netzwerkgruppen hinzufügen* die Netzwerkgruppen aus, die Sie dieser Konfiguration hinzufügen möchten. Wählen Sie dann **Hinzufügen** aus, um die Einstellung zu speichern.

1. Die folgenden drei Optionen werden neben dem Namen der Netzwerkgruppe unter *Spoke-Netzwerkgruppen* angezeigt:
    
    :::image type="content" source="./media/how-to-create-hub-and-spoke/spokes-settings.png" alt-text="Screenshot: Einstellungen für Spoke-Netzwerkgruppen" lightbox="./media/how-to-create-hub-and-spoke/spokes-settings-expanded.png":::

    * *Direkte Konnektivität:* Wählen Sie **Peering innerhalb einer Netzwerkgruppe aktivieren** aus, wenn Sie das VNET-Peering zwischen virtuellen Netzwerken innerhalb einer Netzwerkgruppe in derselben Region einrichten möchten.
    * *Global Mesh:* Wählen Sie **Mesh-Konnektivität regionsübergreifend aktivieren** aus, wenn Sie das VNET-Peering für alle virtuellen Netzwerke in der Netzwerkgruppe regionsübergreifend einrichten möchten.
    * *Gateway:* Wählen Sie **Hub als Gateway verwenden** aus, wenn Sie im virtuellen Hubnetzwerk über ein virtuelles Netzwerkgateway verfügen, das von dieser Netzwerkgruppe zum Übergeben von Datenverkehr an die lokale Umgebung verwendet werden soll.

    Wählen Sie die Einstellungen aus, die Sie für die einzelnen Netzwerkgruppen aktivieren möchten.

1. Wählen Sie abschließend **Hinzufügen** aus, um die Hub-Spoke-Konnektivitätskonfiguration zu erstellen.

## <a name="deploy-the-hub-and-spoke-configuration"></a>Bereitstellen der Hub-Spoke-Konfiguration

Damit diese Konfiguration in Ihrer Umgebung übernommen werden kann, müssen Sie die Konfiguration in den Regionen bereitstellen, in denen Ihre ausgewählten virtuellen Netzwerke erstellt wurden.

1. Wählen Sie zunächst unter *Einstellungen* **Bereitstellungen** aus und anschließend **Konfiguration bereitstellen**.

1. Wählen Sie unter *Konfiguration bereitstellen* die folgenden Einstellungen aus:

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="Screenshot der Seite „Konfiguration bereitstellen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Konfigurationstyp | Wählen Sie **Konnektivität** aus. |
    | Configurations | Wählen Sie den Namen der Konfiguration aus, die Sie im vorherigen Abschnitt erstellt haben. |
    | Zielregionen | Wählen Sie alle Regionen aus, in denen sich die virtuellen Netzwerke befinden, die Sie für die Konfiguration ausgewählt haben. |

1. Wählen Sie **Bereitstellen** und dann **OK** aus, um die Konfiguration an die ausgewählten Regionen zu übergeben.

1. Das Bereitstellen der Konfiguration kann bis zu 15-20 Minuten dauern. Über die Schaltfläche **Aktualisieren** können Sie den Status der Bereitstellung überprüfen.

## <a name="confirm-deployment"></a>Überprüfen der Bereitstellung

1. Lassen Sie sich die [Angewendete Konfiguration anzeigen](how-to-view-applied-configurations.md).

1. Um die *direkte Konnektivität* zwischen Spokes zu testen, stellen Sie in jedem virtuellen Spokes-Netzwerk einen virtuellen Computer bereit. Initiieren Sie dann eine ICMP-Anforderung von einem virtuellen Computer an einen anderen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [SecurityAdmin-Regeln](concept-security-admins.md)
- Erfahren Sie, wie Sie Netzwerkdatenverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-portal.md) blockieren.

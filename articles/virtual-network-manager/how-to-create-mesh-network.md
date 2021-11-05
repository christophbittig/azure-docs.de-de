---
title: Erstellen einer Meshnetzwerktopologie mit Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie, wie Sie eine Meshnetzwerktopologie mit Azure Virtual Network Manager erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fea577a1aa3d2a26d218a7d7c97140d5f58661f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021342"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview"></a>Erstellen einer Meshnetzwerktopologie mit Azure Virtual Network Manager (Vorschau)

In diesem Artikel erfahren Sie, wie Sie mit Azure Virtual Network Manager eine Meshnetzwerktopologie erstellen. Mit dieser Konfiguration können alle virtuellen Netzwerke derselben Region in derselben Netzwerkgruppe miteinander kommunizieren. Sie können die regionsübergreifende Konnektivität aktivieren, indem Sie in der Konnektivitätskonfiguration die globale Mesh-Einstellung aktivieren.

> [!IMPORTANT]
> *Azure Virtual Network Manager* ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Erfahren Sie mehr über die [Mesh](concept-connectivity-configuration.md#mesh-network-topology)-Netzwerktopologie.
* Erstellen Sie eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifizieren Sie virtuelle Netzwerke, die Sie in der Mesh-Konfiguration verwenden möchten, oder erstellen Sie neue [virtuelle Netzwerke](../virtual-network/quick-create-portal.md).

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

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

## <a name="create-a-mesh-connectivity-configuration"></a>Erstellen einer Mesh-Konnektivitätskonfiguration

In diesem Abschnitt erfahren Sie, wie Sie eine Mesh-Konfiguration mit der im vorherigen Abschnitt erstellten Netzwerkgruppe erstellen.

1. Wählen Sie unter *Einstellungen* die Option **Konfiguration** und anschließend **+ Konfiguration hinzufügen** aus.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Screenshot der Konfigurationsliste":::

1. Wählen Sie im Dropdownmenü **Konnektivität** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Screenshot des Dropdownmenüs „Konfiguration“":::

1. Geben Sie auf der Seite *Konnektivitätskonfiguration hinzufügen* die folgenden Informationen ein, oder wählen Sie sie aus:

    :::image type="content" source="./media/how-to-create-mesh-network/connectivity-configuration.png" alt-text="Screenshot der Seite „Konnektivitätskonfiguration hinzufügen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie einen *Namen* für diese Konfiguration ein. |
    | BESCHREIBUNG | *Optional* Geben Sie eine Beschreibung der Aufgaben dieser Konfiguration ein. |
    | Topologie | Wählen Sie die **Meshtopologie** aus. |
    | Global Mesh | Wählen Sie diese Option aus, um regionsübergreifende Konnektivität zwischen virtuellen Netzwerken in derselben Netzwerkgruppe zu aktivieren. |

1. Wählen Sie dann **+ Netzwerkgruppen hinzufügen** aus. 

1. Wählen Sie auf der Seite *Netzwerkgruppen hinzufügen* die Netzwerkgruppen aus, die Sie dieser Konfiguration hinzufügen möchten. Wählen Sie dann **Hinzufügen** aus, um die Einstellung zu speichern.

1. Wählen Sie **Hinzufügen** erneut aus, um die Meshkonnektivitätskonfiguration zu erstellen.

## <a name="deploy-the-mesh-configuration"></a>Bereitstellen der Mesh-Konfiguration

Damit diese Konfiguration in Ihrer Umgebung übernommen werden kann, müssen Sie die Konfiguration in den Regionen bereitstellen, in denen Ihre ausgewählten virtuellen Netzwerke erstellt werden.

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

1. Weitere Informationen finden Sie unter [Angewendete Konfigurationen anzeigen](how-to-view-applied-configurations.md).

1. Um die Konnektivität zwischen den virtuellen Netzwerken zu testen, stellen Sie in jedem virtuellen Netzwerk eine Test-VM zur Verfügung und initiieren Sie eine ICMP-Anforderung zwischen diesen VMs.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [SecurityAdmin-Regeln](concept-security-admins.md)
- Erfahren Sie, wie Sie Netzwerkdatenverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-portal.md) blockieren.

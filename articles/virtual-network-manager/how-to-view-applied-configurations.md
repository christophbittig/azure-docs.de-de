---
title: Anzeigen der von Azure Virtual Network Manager angewendeten Konfigurationen (Vorschau)
description: Erfahren Sie, wie Sie Konfigurationen anzeigen, die von Azure Virtual Network Manager angewendet werden.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0d0f0d0ed5eb100aff4df02a65ab4902da1b7c16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095087"
---
# <a name="view-configurations-applied-by-azure-virtual-network-manager-preview"></a>Anzeigen der von Azure Virtual Network Manager angewendeten Konfigurationen (Vorschau)

Azure Virtual Network Manager bietet ihnen verschiedene Möglichkeiten, um zu überprüfen, ob Konfigurationen ordnungsgemäß angewendet werden. In diesem Artikel erfahren Sie, wie Sie Konfigurationen überprüfen können, die sowohl auf der Ebene des virtuellen Netzwerks als auch des virtuellen Computers angewendet werden. Wir gehen auch auf Vorgänge ein, die im Aktivitätsprotokoll angezeigt werden.

## <a name="virtual-network-visibility"></a>Aktivieren der Netzwerksichtbarkeit

Nachdem Ihre Konfiguration von Virtual Network Manager bereitgestellt wurde, können Sie die angewendete Konfiguration aus der Virtuellen Netzwerkressource anzeigen. 

1. Wechseln Sie zum virtuellen Netzwerk und wählen Sie unter **Einstellungen** die Option *Network Manager* aus. Auf der Registerkarte „Konnektivität“ werden alle Konnektivitätskonfigurationen angezeigt, die dem virtuellen Netzwerk zugeordnet sind. 

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-connectivity.png" alt-text="Screenshot der Konnektivitätskonfiguration, die dem virtuellen Netzwerk zugeordnet ist.":::

2. Wählen Sie die Registerkarte **SecurityAdmin** aus, um alle Sicherheitsregeln zu sehen, die derzeit auf Ihr virtuelles Netzwerk angewendet werden.

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-security.png" alt-text="Screenshot der Sicherheitsregeln, die dem virtuellen Netzwerk zugeordnet sind.":::

## <a name="virtual-machine-visibility"></a>Sichtbarkeit des virtuellen Computers

Auf der Ebene des virtuellen Computers können Sie die von Virtual Network Manager angewendeten Sicherheitsregeln und die effektiven Routen für die Konnektivitätskonfigurationen anzeigen.

### <a name="applied-security-rules"></a>Angewendete Sicherheitsregeln

1. Wechseln Sie zu einem virtuellen Computer in einem virtuellen Netzwerk, auf den eine Konfiguration von Virtual Network Manager angewendet wird. Wählen Sie im linken Menü unter *Einstellungen* die Option **Netzwerk** aus.

    :::image type="content" source="./media/how-to-view-applied-configurations/virtual-machine.png" alt-text="Screenshot der Übersichtsseite des virtuellen Computers.":::

1. Es wird eine Liste der eingehenden Netzwerksicherheitsgruppen sowie ein Abschnitt für Eingangssicherheitsregeln angezeigt, die von Virtual Network Manager angewendet werden.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-inbound-rules.png" alt-text="Screenshot der Sicherheitsregeln für ausgehenden Datenverkehr des virtuellen Computers.":::

1. Wählen Sie die Registerkarte **Regeln für ausgehenden Port** aus, um die Sicherheitsregeln für ausgehenden Datenverkehr des virtuellen Computers.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-outbound-rules.png" alt-text="Screenshot der Sicherheitsregeln für eingehenden Datenverkehr des virtuellen Computers.":::

### <a name="effective-routes"></a>Effektive Routen

1. Um die effektiven Routen für die angewendete Konnektivitätskonfiguration zu sehen, wählen Sie den Namen der Netzwerkschnittstelle unter den *Netzwerkeinstellungen* des virtuellen Computers aus.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Screenshot: Auswählen der Netzwerkschnittstellenkarte für virtuelle Computer.":::

1. Wählen Sie unter *Support + Problembehandlung* die Option **Effektive Routen**.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface.png" alt-text="Screenshot: Schaltfläche &quot;Effektive Routen&quot; von einer VM-Netzwerkschnittstellenkarte.":::

1. Routen mit dem Typ *ConnectedGroup* des nächsten Hops sind entweder Teil der Gitterkonfiguration oder wenn [*Direkte Verbindung*](concept-connectivity-configuration.md#direct-connectivity) für eine Netzwerkgruppe aktiviert ist. Routen zwischen den virtuellen Hub- und Spoke-Netzwerken werden als Typ des nächsten Hops *VNetPeering* oder *GlobalVNetPeering* angezeigt.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-routes.png" alt-text="Screenshot: effektive Routen mit verbundenen Gruppen und Hubrouten." lightbox="./media/how-to-view-applied-configurations/effective-routes-expanded.png":::

    > [!NOTE]
    > Der Adressraum des virtuellen Hubnetzwerks ist auch in der **Verbundene Gruppe** *enthalten*. Wenn das Peering virtueller Netzwerke zwischen den virtuellen Hub- und Spoke-Netzwerken fehlschlägt, können sie daher weiterhin miteinander kommunizieren, da sie sich in einer verbundenen Gruppe finden.
    > 

### <a name="effective-security-rules"></a>Effektive Sicherheitsregeln

1. Um die effektiven Routen für die angewendete Sicherheitsregelkonfiguration zu sehen, wählen Sie den Namen der Netzwerkschnittstelle unter den *Netzwerkeinstellungen* des virtuellen Computers aus.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Screenshot: Auswählen der Netzwerkschnittstellenkarte für Sicherheitsregeln.":::

1. Wählen Sie unter *Support + Problembehandlung* die Option **Effektive Sicherheitsregeln** aus.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface-security-rules.png" alt-text="Screenshot: Schaltfläche &quot;Effektive Sicherheitsregeln&quot; für eine VM-Netzwerkschnittstellenkarte.":::

1. Wählen Sie den Namen des Azure Virtual Network-Managers aus, um die dem virtuellen Computer zugeordneten Sicherheitsadministratorregeln zu sehen.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-security-rules.png" alt-text="Screenshot der effektiven Sicherheitsregeln, die dem virtuellen Computer zugeordnet sind-":::

## <a name="activity-log"></a>Aktivitätsprotokoll

Sie können das Aktivitätsprotokoll für Ihre Azure Virtual Network Manager-Ressource anzeigen, um die Änderungen zu sehen, die Sie oder Ihr Netzwerkadministrator vorgenommen haben. Wechseln Sie zum Anzeigen des Aktivitätsprotokolls zu Ihrer Network Manager-Ressource im Azure-Portal. Wählen Sie im linken Aufgabenbereich **Aktivitätsprotokoll** aus. Passen Sie bei Bedarf den *Zeitraum* an, und fügen Sie weitere Filter hinzu, um die Liste der Vorgänge einzugrenzen. Sie können das *Aktivitätsprotokoll* auch anzeigen, indem Sie im oberen Bereich des Azure-Portals nach dem Dienst suchen.

:::image type="content" source="./media/how-to-view-applied-configurations/activity-log.png" alt-text="Screenshot der Seite &quot;Aktivitätsprotokoll&quot; für Network Manager.":::

### <a name="list-of-operations"></a>Liste der Vorgänge

Die folgende Liste enthält Vorgänge, die im Aktivitätsprotokoll angezeigt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- |
| Commit | Für die Bereitstellung einer Konfiguration wurde ein Committed für eine Region(en) verwendet. |
| Löschen von ConnectivityConfiguration | Löschen einer Konnektivitätskonfiguration aus Network Manager. |
| Löschen von NetworkGroups | Löschen einer Netzwerkgruppe aus Dem Netzwerk-Manager.|
| Regeln entfernen | Löschen einer Regel aus einer Regelsammlung. |
| Löschen von RuleCollections | Löschen einer Regelsammlung aus einer Sicherheitsadministratorkonfiguration. |
| Löschen von SecurityAdminConfigurations | Löschen einer Sicherheitsadministratorkonfiguration aus Network Manager. |
| ListDeploymentStatus | Anzeigen des Bereitstellungsstatus einer Konnektivitäts- oder Sicherheitsadministratorkonfiguration. |
| ListActiveConnectivityConfiguration | Anzeigen der Liste der Konnektivitätskonfigurationen, die auf das virtuelle Netzwerk angewendet werden.|
| ListActiveSecurityAdminRules | Anzeigen der Liste der Sicherheitsadministratorkonfigurationen, die auf das virtuelle Netzwerk angewendet werden. |
| Löschen von ConnectivityConfiguration | Erstellen von ConnectivityConfiguration |
| Schreiben von NetworkGroups | Erstellen einer neuen Netzwerkgruppe. |
| Schreiben von NetworkManager | Erstellen einer neuen Azure Virtual Network Manager-Instanz. |
| Schreibregeln | Erstellen einer neuen Sicherheitsregel, die einer Regelsammlung hinzugefügt werden soll. |
| Schreiben von RuleCollections | Erstellen einer neuen Regelsammlung, die einer Sicherheitsadministratorkonfiguration hinzugefügt werden soll. |
| Schreiben von SecurityAdminConfiguration | Erstellen einer neuen Sicherheitsadministratorkonfiguration. |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz über das Azure-Portal.
- Häufig gestellte Fragen finden Sie unter [Network Manager FAQ](faq.md).

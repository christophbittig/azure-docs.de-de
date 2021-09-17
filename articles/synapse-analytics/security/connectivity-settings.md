---
title: Konnektivitätseinstellungen für Azure Synapse
description: In diesem Artikel lernen Sie, wie Sie Konnektivitätseinstellungen in Azure Synapse Analytics konfigurieren.
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861633"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Konnektivitätseinstellungen für Azure Synapse Analytics

In diesem Artikel werden die Konnektivitätseinstellungen in Azure Synapse Analytics beschrieben und erläutert, wie sie ggf. konfiguriert werden.

## <a name="public-network-access"></a>Zugriff aus öffentlichen Netzwerken 

Sie können das Feature für den Zugriff aus öffentlichen Netzwerken verwenden, um eingehende Verbindungen von öffentlichen Netzwerken mit Ihrem Azure Synapse-Arbeitsbereich zuzulassen. 

- Wenn der Zugriff aus öffentlichen Netzwerken **deaktiviert** ist, können Sie nur über [private Endpunkte](synapse-workspace-managed-private-endpoints.md) eine Verbindung mit Ihrem Arbeitsbereich herstellen. 
- Wenn der Zugriff aus öffentlichen Netzwerken **aktiviert** ist, können Sie auch aus öffentlichen Netzwerken eine Verbindung mit Ihrem Arbeitsbereich herstellen. Sie können dieses Feature sowohl während als auch nach der Erstellung Ihres Arbeitsbereichs verwalten. 

> [!IMPORTANT]
> Dieses Feature ist nur für Azure Synapse-Arbeitsbereiche verfügbar, die einem [verwalteten virtuellen Netzwerk von Azure Synapse Analytics](synapse-workspace-managed-vnet.md) zugeordnet sind. Sie können Ihren Synapse-Arbeitsbereich jedoch immer noch für das öffentliche Netzwerk öffnen, unabhängig von seiner Zuordnung zum verwalteten VNet. 

Wenn Sie die Option **Deaktivieren** auswählen, werden keine Firewallregeln angewendet, die Sie konfigurieren können. Darüber hinaus werden Ihre Firewallregeln in der Netzwerkeinstellung im Synapse-Portal abgeblendet angezeigt. Ihre Firewallkonfigurationen werden erneut angewendet, wenn Sie den Zugriff aus öffentlichen Netzwerken erneut aktivieren. 

> [!TIP]
> Wenn Sie die Aktivierung wiederherstellen, sollten Sie etwas Zeit verstreichen lassen, bevor Sie die Firewallregeln bearbeiten.

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>Konfigurieren des Zugriffs aus öffentlichen Netzwerken beim Erstellen Ihres Arbeitsbereichs

1.    Wählen Sie die Registerkarte **Netzwerk** aus, wenn Sie Ihren Arbeitsbereich im [Azure-Portal](https://aka.ms/azureportal) erstellen.
2.    Wählen Sie unter „Verwaltetes virtuelles Netzwerk“ die Option **Aktivieren** aus, um Ihren Arbeitsbereich einem verwalteten virtuellen Netzwerk zuzuordnen und den Zugriff aus öffentlichen Netzwerken zuzulassen. 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="Synapse-Arbeitsbereich erstellen, Registerkarte „Netzwerk“, Einstellung „Verwaltetes virtuelles Netzwerk“" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. Wählen Sie unter **Zugriff aus öffentlichen Netzwerken** die Option **Deaktivieren** aus, um öffentlichen Zugriff auf Ihren Arbeitsbereich zu verweigern. Wählen Sie **Aktivieren** aus, wenn Sie öffentlichen Zugriff auf Ihren Arbeitsbereich zulassen möchten.

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="Synapse-Arbeitsbereich erstellen, Registerkarte „Netzwerk“, Einstellung „Zugriff aus öffentlichen Netzwerken“" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    Schließen Sie den Rest des Erstellungsablaufs des Arbeitsbereichs ab.

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>Konfigurieren des Zugriffs aus öffentlichen Netzwerken nach dem Erstellen Ihres Arbeitsbereichs

1.    Wählen Sie Ihren Synapse-Arbeitsbereich im [Azure-Portal](https://aka.ms/azureportal) aus.
2.    Wählen Sie in der Navigation auf der linken Seite **Netzwerk** aus.
3.    Wählen Sie **Deaktiviert** aus, um Zugriff aus öffentlichen Netzwerken auf Ihren Arbeitsbereich zu verweigern. Wählen Sie **Aktiviert** aus, wenn Sie öffentlichen Zugriff auf Ihren Arbeitsbereich zulassen möchten.

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="In einem vorhandenen Synapse-Arbeitsbereich die Registerkarte „Netzwerk“, und die Einstellung „Zugriff aus öffentlichen Netzwerken“ ist aktiviert" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    Bei Deaktivierung werden die **Firewallregeln** abgeblendet, um anzuzeigen, dass Firewallregeln nicht wirksam sind. Firewallregelkonfigurationen bleiben erhalten. 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="In einem vorhandenen Synapse-Arbeitsbereich die Registerkarte „Netzwerk“, und die Einstellung „Zugriff aus öffentlichen Netzwerken“ ist deaktiviert; beachten Sie die Firewallregeln." lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    Wählen Sie **Speichern** aus, um die Änderung zu speichern. Eine Benachrichtigung bestätigt, dass die Netzwerkeinstellung erfolgreich gespeichert wurde.

## <a name="connection-policy"></a>Verbindungsrichtlinie
Die Verbindungsrichtlinie für Synapse SQL in Azure Synapse Analytics ist auf *Standard* festgelegt. Dies kann in Azure Synapse Analytics nicht geändert werden. Weitere Informationen dazu, wie sich dies auf Verbindungen mit Synapse SQL in Azure Synapse Analytics auswirkt, finden Sie [hier](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>TLS-Mindestversion
Synapse SQL in Azure Synapse Analytics ermöglicht Verbindungen unter Verwendung aller TLS-Versionen. Sie können die TLS-Mindestversion für Synapse SQL in Azure Synapse Analytics nicht festlegen.

## <a name="next-steps"></a>Nächste Schritte

 - Erstellen Sie einen [Azure Synapse-Arbeitsbereich](./synapse-workspace-ip-firewall.md).
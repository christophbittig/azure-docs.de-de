---
title: Installieren einer Azure Firewall-Instanz in einem Virtual WAN-Hub
titleSuffix: Azure Virtual WAN
description: Erfahren Sie, wie Sie Azure Firewall in einem Virtual WAN-Hub konfigurieren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: bf3aae99eb62a76885040589560c505ba2a8a2c3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487933"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Konfigurieren von Azure Firewall in einem Virtual WAN-Hub

Ein **gesicherter Hub** ist ein Virtual WAN-Hub mit Azure Firewall. Dieser Artikel führt Sie durch die Schritte zum Konvertieren eines Virtual WAN-Hubs in einen gesicherten Hub, indem Sie Azure Firewall direkt über die Azure Virtual WAN-Portalseiten installieren.

## <a name="before-you-begin"></a>Voraussetzungen

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie bereits ein virtuelles WAN mit einem oder mehreren Hubs bereitgestellt haben.

Führen Sie die Schritte in den folgenden Artikeln aus, um ein neues virtuelles WAN und einen neuen Hub zu erstellen:

* [Erstellen eines virtuellen WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Erstellen eines Hubs](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Anzeigen von virtuellen Hubs

Auf der Seite **Übersicht** für Ihr virtuelles WAN wird eine Liste der virtuellen Hubs und der gesicherten Hubs angezeigt. Die folgende Abbildung zeigt ein virtuelles WAN ohne geschützte Hubs.

:::image type="content" source="./media/howto-firewall/overview.png" alt-text="Screenshot der Seite „Übersicht“ für ein virtuelles WAN mit einer Liste virtueller Hubs" lightbox="./media/howto-firewall/overview.png":::

## <a name="convert-to-secured-hub"></a>Umwandlung in einen gesicherten Hub

1. Wählen Sie auf der Seite **Übersicht** für Ihr virtuelles WAN den Hub aus, den Sie in einen gesicherten Hub konvertieren möchten. Auf der Seite des virtuellen Hubs werden zwei Optionen zum Bereitstellen von Azure Firewall in diesem Hub angezeigt. Wählen Sie eine der beiden Optionen aus.

   :::image type="content" source="./media/howto-firewall/security.png" alt-text="Screenshot der Seite „Übersicht“ für das virtuelle WAN, auf der Sie „In gesicherten Hub konvertieren“ oder „Azure Firewall“ auswählen können" lightbox="./media/howto-firewall/security.png":::

1. Nachdem Sie eine der Optionen ausgewählt haben, wird die Seite **In gesicherten Hub umandeln** angezeigt. Wählen Sie einen Hub zum Konvertieren aus, und wählen Sie dann unten auf der Seite **Weiter: Azure Firewall** aus.

   :::image type="content" source="./media/howto-firewall/select-hub.png" alt-text="Screenshot der Konvertierung in einen sicheren Hub mit ausgewähltem Hub" lightbox="./media/howto-firewall/select-hub.png":::
1. Nachdem Sie den Workflow abgeschlossen haben, wählen Sie **Bestätigen** aus.

   :::image type="content" source="./media/howto-firewall/confirm.png" alt-text="Screenshot des Bereichs „In gesicherter Hub konvertieren“, in dem „Bestätigen“ ausgewählt ist" lightbox="./media/howto-firewall/confirm.png":::
1. Nachdem der Hub in einen geschützer Hub konvertiert wurde, können Sie ihn auf der Seite **Übersicht** des virtuellen WAN anzeigen.

   :::image type="content" source="./media/howto-firewall/secured-hub.png" alt-text="Screenshot der Ansicht des geschützter Hubs" lightbox="./media/howto-firewall/secured-hub.png":::

## <a name="view-hub-resources"></a>Anzeigen von Hubressourcen

Wählen Sie auf der Seite **Übersicht** des virtuellen WAN den gesicherten Hub aus. Auf der Seite des virtuellen Hubs können Sie alle zugehörigen Ressourcen anzeigen, einschließlich Azure Firewall.

Wählen Sie zum Anzeigen der Einstellungen für Azure Firewall auf dem gesicherten Hub unter **Sicherheit** die Option **Einstellungen für geschützte virtuelle Hubs** aus.

:::image type="content" source="./media/howto-firewall/hub-settings.png" alt-text="Screenshot der Einstellungen für den gesicherten virtuellen Hub" lightbox="./media/howto-firewall/hub-settings.png":::

## <a name="configure-additional-settings"></a>Konfigurieren zusätzlicher Einstellungen

Um zusätzliche Azure Firewall-Einstellungen für den virtuellen Hub zu konfigurieren, wählen Sie den Link zu **Azure Firewall Manager** aus. Informationen zu Firewall-Richtlinien finden Sie unter [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

:::image type="content" source="./media/howto-firewall/additional-settings.png" alt-text="Screenshot der Übersicht zur Verwaltung der Routeneinstellungen des Sicherheitsanbieters für diesen gesicherten virtuellen Hub in Azure Firewall Manager" lightbox="./media/howto-firewall/additional-settings.png":::

Wenn Sie zur Seite **Übersicht** für den Hub zurückkehren möchten, können Sie zurücknavigieren, indem Sie auf den Pfad klicken, wie in der nachstehenden Abbildung dargestellt.

:::image type="content" source="./media/howto-firewall/arrow.png" alt-text="Screenshot der Rückkehr zur Übersicht" lightbox="./media/howto-firewall/arrow.png":::

## <a name="upgrade-to-azure-firewall-premium"></a>Upgrade auf Azure Firewall Premium
Es ist jederzeit möglich, ein Upgrade von Azure Firewall Standard auf Premium unter Befolgung dieser [Anleitungen](https://docs.microsoft.com/azure/firewall/premium-migrate#migrate-a-secure-hub-firewall) auszuführen. Für diesen Vorgang sind Wartungsfenster erforderlich, da minimale Ausfallzeiten auftreten. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).

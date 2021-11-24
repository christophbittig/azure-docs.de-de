---
title: Integrieren virtueller Azure Stack Hub-Computer in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die VM-Erweiterung zur Update- und Konfigurationsverwaltung für Azure Monitor auf virtuellen Azure Stack Hub-Computern bereitstellen und diese mit Microsoft Sentinel überwachen.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 570f297cfdd16aaac18f36d11d989c1dfa732e6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518905"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-microsoft-sentinel"></a>Verbinden von virtuellen Azure Stack Hub-Computern mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit Microsoft Sentinel können Sie Ihre virtuellen Computer, die in Azure und Azure Stack Hub ausgeführt werden, zentral an einem Ort überwachen. Zum Durchführen des Onboardings Ihrer Azure Stack-Computer in Microsoft Sentinel müssen Sie zunächst auf den vorhandenen virtuellen Azure Stack Hub-Computern die VM-Erweiterung hinzufügen. 

Nachdem Sie die Azure Stack Hub-Computer verbunden haben, steht Ihnen ein Katalog von Dashboards zur Anzeige der Erkenntnisse, die Sie aus Ihren Daten gewinnen, zur Auswahl. Diese Dashboards können einfach an Ihre Anforderungen angepasst werden.

## <a name="add-the-virtual-machine-extension"></a>Hinzufügen der VM-Erweiterung 

Fügen Sie die VM-Erweiterung zur **Update- und Konfigurationsverwaltung für Azure Monitor** den in Azure Stack Hub ausgeführten virtuellen Computern hinzu. 

1. Melden Sie sich auf einer neuen Browserregisterkarte beim [Azure Stack Hub-Portal](/azure-stack/user/azure-stack-use-portal#access-the-portal) an.

1. Wechseln Sie zur Seite **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus, der mit Microsoft Sentinel geschützt werden soll. Informationen zum Erstellen eines virtuellen Computers in Azure Stack Hub finden Sie unter [Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal](/azure-stack/user/azure-stack-quick-windows-portal) oder [Schnellstart: Erstellen Sie einen virtuellen Linux-Server mit dem Azure Stack Hub-Portal](/azure-stack/user/azure-stack-quick-linux-portal).

1. Wählen Sie **Erweiterungen**. Die Liste der auf dieser VM installierten VM-Erweiterungen wird angezeigt.

1. Wählen Sie die Registerkarte **Hinzufügen**. Das Menüblatt **Neue Ressource** wird geöffnet und zeigt die Liste der verfügbaren Erweiterungen für virtuelle Computer an. 

1. Wählen Sie die Erweiterung **Azure Monitor, Aktualisieren und Konfigurationsverwaltung** aus und wählen Sie **Erstellen**. Das Konfigurationsfenster **Erweiterung installieren** wird geöffnet.

   ![Einstellungen für die Update- und Konfigurationsverwaltung für Azure Monitor](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Wenn die Erweiterung **Azure Monitor, Update- und Konfigurationsverwaltung** in Ihrem Marketplace nicht aufgeführt ist, können Sie sich an Ihren Azure Stack Hub-Betreiber wenden, damit sie zur Verfügung gestellt wird.

1. Wählen Sie im Microsoft Sentinel-Menü die Option **Arbeitsbereichseinstellungen** und dann **Erweitert** aus, und kopieren Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** . 

1. Fügen Sie ihn im Azure Stack Hub-Fenster **Erweiterung installieren** in den angegebenen Feldern ein, und wählen Sie **OK**.

1. Nach Abschluss der Installation wird die Erweiterung mit dem Status **Bereitstellung erfolgreich** angezeigt. Es kann bis zu einer Stunde dauern, bis der virtuelle Computer im Microsoft Sentinel-Portal angezeigt wird.

Weitere Informationen zur Installation und Konfiguration des Agents für Windows finden Sie unter [Verbinden von Windows-Computern](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

Informationen zum Beheben von Problemen mit dem Agent für Linux finden Sie unter [Behandeln von Problemen mit dem Log Analytics-Agent für Linux](../azure-monitor/agents/agent-linux-troubleshoot.md).

Im Microsoft Sentinel-Portal in Azure wird unter **Virtuelle Computer** eine Übersicht aller virtuellen und physischen Computer mit dem jeweiligen Status angezeigt. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Erweiterung nicht mehr benötigt wird, können Sie sie über das Azure Stack Hub-Portal von der VM entfernen.

So entfernen Sie die Erweiterung:

1. Öffnen Sie das **Azure Stack Hub-Portal**.

1. Wechseln Sie zur Seite **Virtuelle Computer**, und wählen Sie die VM aus, von der Sie die Erweiterung entfernen möchten.

1. Wählen Sie **Erweiterungen** und dann die Erweiterung **Microsoft.EnterpriseCloud.Monitoring** aus.

1. Wählen Sie **Deinstallieren** und bestätigen Sie die Auswahl.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).
- Streamen Sie Daten aus [CEF-Appliances (Common Event Format)](connect-common-event-format.md) an Microsoft Sentinel.

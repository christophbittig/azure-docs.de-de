---
title: Erstellen von Aktivitätsprotokollwarnungen für Labs
description: Dieser Artikel enthält die Schritte zum Erstellen von Aktivitätsprotokollwarnungen für Labs in Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 0ebb5ae118d6485afd697ff9b5db7113a101b358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628164"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Erstellen von Aktivitätsprotokollwarnungen für Labs in Azure DevTest Labs
In diesem Artikel wird erläutert, wie Sie Aktivitätsprotokollwarnungen für Labs in Azure DevTest Labs erstellen (z. B. wenn eine VM erstellt oder gelöscht wird).

## <a name="create-alerts"></a>Erstellen von Warnungen
In diesem Beispiel erstellen Sie eine Warnung für alle Verwaltungsvorgänge in einem Lab mit einer Aktion, die eine E-Mail an die Abonnementbesitzer sendet. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie in die Suchleiste im Azure-Portal **Monitor** ein, und wählen Sie dann **Monitor** in der Ergebnisliste aus. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Suche nach „Monitor“":::        
1. Klicken Sie im Menü auf der linken Seite auf **Warnungen** und dann in der Symbolleiste auf **Neue Warnungsregel**. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Seite „Warnungen“":::    
1. Klicken Sie auf der Seite **Warnungsregel erstellen** auf **Ressource auswählen**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Auswählen einer Ressource für die Warnung":::        
1. Wählen Sie unter **Nach Ressourcentyp filtern** **DevTest Labs** aus und anschließend in der Liste das gewünschte Lab, und klicken Sie dann auf **Fertig**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Auswählen des Labs als Ressource":::
1. Klicken Sie dann zurück auf der Seite **Warnungsregel erstellen** auf **Bedingung auswählen**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Link „Bedingung auswählen“":::    
1. Wählen Sie auf der Seite **Signallogik konfigurieren** ein von DevTest Labs unterstütztes Signal aus. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Signal auswählen":::
1. Filtern Sie nach der **Ereignisstufe** (Ausführlich, Information, Warnung, Fehler, Kritisch, Alle), dem **Status** (Fehler, Gestartet, Erfolgreich) sowie danach, **wer das Ereignis initiiert hat**. 
1. Klicken Sie auf **Fertig**, um die Konfiguration der Bedingung abzuschließen. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Fertig konfigurierte Signallogik":::
1. Sie haben den Bereich (Lab) und die Bedingung für die Warnung angegeben. Nun müssen Sie eine Aktionsgruppe mit Aktionen angeben, die ausgeführt werden sollen, wenn die Bedingung erfüllt ist. Klicken Sie zurück auf der Seite **Warnungsregel erstellen** auf **Aktionsgruppe auswählen**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Link „Aktionsgruppe auswählen“":::
1. Klicken Sie auf der Symbolleiste auf den Link **Aktionsgruppe erstellen**. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Link „Aktionsgruppe erstellen“":::
1. Führen Sie auf der Seite **Aktionsgruppe hinzufügen** die folgenden Schritte aus:
    1. Geben Sie einen **Namen** für die Aktionsgruppe ein.
    1. Geben Sie einen **Kurznamen** für die Aktionsgruppe ein. 
    1. Wählen Sie die **Ressourcengruppe** aus, in der die Warnung erstellt werden soll. 
    1. Geben Sie einen **Namen für die Aktion** ein. 
    1. Wählen Sie den **Aktionstyp** aus (in diesem Beispiel **E-Mail an Azure Resource Manager-Rolle**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Seite „Aktionsgruppe hinzufügen“":::
    1. Wählen Sie auf der Seite **E-Mail an Azure Resource Manager-Rolle** die gewünschte Rolle aus. In diesem Beispiel ist dies die Rolle **Besitzer**. Wählen Sie anschließend **OK** aus. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Auswählen der Rolle":::            
    1. Wählen Sie im Bereich **Aktionsgruppe hinzufügen** die Option **OK** aus. 
1. Geben Sie als Nächstes auf der Seite **Warnungsregel erstellen** einen Namen für die Warnungsregel an, und klicken Sie anschließend auf **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Fertig erstellte Warnungsregel":::

## <a name="view-alerts"></a>Anzeigen von Warnungen 
1. Unter **Warnungen** werden Ihnen (in diesem Beispiel) Warnungen für alle Verwaltungsvorgänge angezeigt. Es kann einige Zeit dauern, bis Warnungen angezeigt werden. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Bildschirmaufnahme der im Dashboard angezeigten Warnungen":::
1. Wenn Sie auf die Zahl in einer Spalte klicken (z. B. in **Warnungen gesamt**), werden die ausgelösten Warnungen angezeigt. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Alle Warnungen":::
1. Wenn Sie auf eine Warnung klicken, werden Details dazu angezeigt. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Warnungsdetails":::
1. In diesem Beispiel erhalten Sie auch eine E-Mail mit dem folgenden Inhalt: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Warnungs-E-Mail":::

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Erstellung von Aktionsgruppen mit verschiedenen Aktionstypen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md).
- Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md).
- Weitere Informationen zur Festlegung von Warnungen für Aktivitätsprotokolle finden Sie unter [Warnungen zum Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md).

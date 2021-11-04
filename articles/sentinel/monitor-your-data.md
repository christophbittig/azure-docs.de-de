---
title: Visualisieren Ihrer Daten mithilfe von Azure Monitor-Arbeitsmappen in Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Daten mithilfe von Arbeitsmappen in Azure Sentinel visualisieren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ef77535810671c0f442aa90eade688778c381750
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064485"
---
# <a name="use-azure-monitor-workbooks-to-visualize-and-monitor-your-data"></a>Visualisieren und Überwachen Ihrer Daten mithilfe von Azure Monitor-Arbeitsmappen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Nachdem Sie Ihre [Datenquellen mit Azure Sentinel verbunden](quickstart-onboard.md) haben, können Sie die Daten mithilfe der Azure Sentinel-Einführung von Azure Monitor-Arbeitsmappen visualisieren und überwachen, die für Vielseitigkeit bei der Erstellung benutzerdefinierter Dashboards sorgt. Zwar werden Arbeitsmappen in Azure Sentinel anders angezeigt, dennoch kann es für Sie hilfreich sein, zu erfahren, wie Sie [interaktive Berichte mit Azure Monitor-Arbeitsmappen erstellen](../azure-monitor/visualize/workbooks-overview.md) können. Azure Sentinel ermöglicht das Erstellen von benutzerdefinierten Arbeitsmappen für Ihre Daten und enthält auch integrierte Arbeitsmappenvorlagen, mit denen Sie schnell Erkenntnisse zu den Daten erhalten, sobald Sie eine Verbindung mit einer Datenquelle herstellen.

In diesem Artikel wird beschrieben, wie Sie Ihre Daten in Azure Sentinel visualisieren können.

> [!div class="checklist"]
> * Verwenden integrierter Arbeitsmappen
> * Erstellen neuer Arbeitsmappen

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen mindestens über die Berechtigung **Arbeitsmappenleser** oder **Arbeitsmappenmitwirkender** für die Ressourcengruppe des Azure Sentinel-Arbeitsbereichs verfügen.

> [!NOTE]
> Die Arbeitsmappen, die in Azure Sentinel angezeigt werden, werden in der Ressourcengruppe des Azure Sentinel-Arbeitsbereichs gespeichert und sind durch den Arbeitsbereich markiert, in dem Sie erstellt wurden.

## <a name="use-built-in-workbooks"></a>Verwenden integrierter Arbeitsmappen

1. Wechseln Sie zu **Workbooks** (Arbeitsmappen), und wählen Sie dann **Templates** (Vorlagen) aus, um die vollständige Liste der in Azure Sentinel integrierten Arbeitsmappen anzuzeigen. 

    Wenn Sie feststellen möchten, welche für die Datentypen relevant sind, mit denen Sie eine Verbindung hergestellt haben, wird Ihnen im Feld **Required data types** (Erforderliche Datentypen) in jeder Arbeitsmappe der Datentyp neben einem grünen Häkchen aufgelistet, wenn Sie bereits relevante Daten an Azure Sentinel streamen.

    [ ![Go to workbooks](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox) (Zu Arbeitsmappen wechseln)

1. Wählen Sie **View template** (Vorlage anzeigen) aus, um die mit Ihren Daten aufgefüllte Vorlage anzuzeigen.

1. Wenn Sie die Arbeitsmappe bearbeiten möchten, klicken Sie auf **Save** (Speichern), und wählen Sie dann den Speicherort aus, an dem Sie die JSON-Datei für die Vorlage speichern möchten.

   > [!NOTE]
   > Dadurch wird eine Azure-Ressource erstellt, die auf der relevanten Vorlage basiert, und die JSON-Datei der Arbeitsmappe, nicht die Daten, gespeichert.


1. Wählen Sie **View saved workbook** (Gespeicherte Arbeitsmappe anzeigen) aus. 

    [ ![View workbooks.](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox) (Arbeitsmappen anzeigen)

    Wählen Sie auf der Symbolleiste der Arbeitsmappe die Schaltfläche **Bearbeiten** aus, um die Arbeitsmappe entsprechend Ihren Anforderungen anzupassen. Wenn Sie fertig sind, wählen Sie **Speichern** aus, um Ihre Änderungen zu speichern.

    Weitere Informationen finden Sie unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Um die Arbeitsmappe zu klonen: Wählen Sie **Edit** (Bearbeiten) und dann **Save as** (Speichern unter) aus, und speichern Sie sie unter einem anderen Namen in demselben Abonnement und in derselben Ressourcengruppe.
> Geklonte Arbeitsmappen werden auf der Registerkarte **My workbooks** (Meine Arbeitsmappen) angezeigt.
>
## <a name="create-new-workbook"></a>Neue Arbeitsmappe erstellen

1. Klicken Sie auf **Workbooks** (Arbeitsmappen), und wählen Sie dann **Add workbook** (Arbeitsmappe hinzufügen), um eine komplett neue Arbeitsmappe zu erstellen.

    [ ![Neue Arbeitsmappe.](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Um die Arbeitsmappe zu bearbeiten, wählen Sie **Edit** (Bearbeiten) aus, und fügen Sie dann nach Bedarf Text, Abfragen und Parameter hinzu. Weitere Informationen zum Anpassen der Arbeitsmappe finden Sie unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md). 

1. Stellen Sie bei der Erstellung einer Abfrage sicher, dass **Data source** (Datenquelle) auf **Logs** (Protokolle) und **Resource type** (Ressourcentyp) auf **Log Analytics** (Protokollanalyse) festgelegt ist, und wählen Sie dann die relevanten Arbeitsbereiche aus. 

1. Nachdem Sie die Arbeitsmappe erstellt haben, speichern Sie diese in dem Abonnement und in der Ressourcengruppe Ihres Azure Sentinel-Arbeitsbereichs.

1. Wenn Sie möchten, dass andere Personen in Ihrer Organisation die Arbeitsmappe verwenden, wählen Sie unter **Save to** (Speichern in) die Option **Shared reports** (Freigegebene Berichte) aus. Wenn Sie möchten, dass diese Arbeitsmappe nur für Sie verfügbar ist, wählen Sie **My reports** (Meine Berichte) aus.

1. Um zwischen Arbeitsmappen in Ihrem Arbeitsbereich zu wechseln, wählen Sie das **Öffnen** ![Symbol für das Öffnen einer Arbeitsmappen.](./media/tutorial-monitor-data/switch.png) in der Symbolleiste einer beliebigen Arbeitsmappe. Der Bildschirm wechselt zu einer Liste von anderen Arbeitsmappen, zu denen Sie wechseln können.

    Öffnen Sie die Arbeitsmappe, die Sie aktualisieren möchten:

    [ ![Arbeitsmappen wechseln.](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Aktualisieren Sie Ihre Arbeitsmappendaten

Aktualisieren Sie die Arbeitsmappe, um aktualisierte Daten anzuzeigen. Wählen Sie in der Symbolleiste eine der folgenden Optionen aus:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Aktualisieren**, um die Arbeitsmappendaten manuell zu aktualisieren.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Automatische Aktualisierung**, um die automatische Aktualisierung der Arbeitsmappe in einem konfigurierten Intervall festzulegen.

    - Die unterstützten Aktualisierungsintervalle reichen von **5 Minuten** bis zu **1 Tag**.

    - Die automatische Aktualisierung wird angehalten, während Sie eine Arbeitsmappe bearbeiten, und die Intervalle werden jedes Mal neu gestartet, wenn Sie vom Bearbeitungsmodus zurück in den Ansichtsmodus wechseln.

    - Automatische Aktualisierungsintervalle werden ebenfalls neu gestartet, wenn Sie Ihre Daten manuell aktualisieren.

    > [!TIP]
    > Die automatische Aktualisierung ist standardmäßig deaktiviert. Um die Leistung zu optimieren, wird die automatische Aktualisierung zudem beim Schließen einer Arbeitsmappe deaktiviert und nicht im Hintergrund ausgeführt. Aktivieren Sie die automatische Aktualisierung ggf. wieder, wenn Sie die Arbeitsmappe das nächste Mal öffnen.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Drucken einer Arbeitsmappe oder Speichern als PDF

Verwenden Sie das Optionsmenü rechts neben dem Titel der Arbeitsmappe, um eine Arbeitsmappe zu drucken oder als PDF zu speichern.

1. Klicken Sie auf das Optionsmenü > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Print content** (Inhalt drucken). 
2. Passen Sie bei Bedarf die Druckeinstellungen auf der entsprechenden Seite an, oder klicken Sie auf **Save as PDF**( Als PDF speichern), um die Datei lokal zu speichern.

Beispiel:

[ ![Arbeitsmappe drucken oder als PDF speichern.](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Löschen von Arbeitsmappen

Um eine gespeicherte Arbeitsmappe (entweder eine gespeicherte Vorlage oder eine angepasste Arbeitsmappe) zu löschen, wählen Sie diese auf der Seite „Arbeitsmappen“ aus, und wählen Sie **Löschen** aus. Dadurch wird die gespeicherte Arbeitsmappe entfernt.

> [!NOTE]
> Dadurch werden sowohl die Arbeitsmappenressource als auch alle Änderungen entfernt, die Sie an der Vorlage vorgenommen haben. Die ursprüngliche Vorlage bleibt verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihre Daten in Azure Sentinel mithilfe von Azure-Arbeitsmappen visualisieren.

Informationen zum Automatisieren Ihrer Reaktionen auf Bedrohungen finden Sie unter [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](tutorial-respond-threats-playbook.md).

Informationen zu beliebten integrierten Arbeitsmappen finden Sie unter [Häufig verwendete Azure Sentinel Arbeitsmappen](top-workbooks.md). 

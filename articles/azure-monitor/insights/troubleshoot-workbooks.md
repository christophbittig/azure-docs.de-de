---
title: Problembehandlung für auf Azure Monitor-Arbeitsmappen basierende Erkenntnisse
description: Enthält eine Anleitung zur Problembehandlung für auf Azure Monitor-Arbeitsmappen basierende Erkenntnisse zu Diensten wie Azure Key Vault, Azure Cosmos DB, Azure Storage und Azure Cache for Redis.
services: azure-monitor
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 005d59f2f9afb8dfa19c642ef89386be51005fd7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131826"
---
# <a name="troubleshooting-workbook-based-insights"></a>Problembehandlung für arbeitsmappenbasierte Erkenntnisse

Dieser Artikel unterstützt Sie bei der Diagnose und Behandlung einiger bekannter Probleme, die bei der Verwendung von auf Azure Monitor-Arbeitsmappen basierenden Erkenntnissen auftreten können.


## <a name="why-can-i-only-see-200-resources"></a>Warum werden nur 200 Ressourcen angezeigt?

Die Anzahl der ausgewählten Ressourcen ist auf 200 begrenzt, unabhängig von der Anzahl der ausgewählten Abonnements.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Was passiert, wenn ich auf eine kürzlich angeheftete Kachel im Dashboard klicke?

* Wenn Sie irgendwo auf die Kachel klicken, gelangen Sie zu der Registerkarte, von der aus die Kachel angeheftet wurde. Wenn Sie beispielsweise einen Graphen auf der Registerkarte „Overview“ (Übersicht) anheften, wird beim Klicken auf diese Kachel im Dashboard diese Standardansicht geöffnet. Wenn Sie jedoch einen Graphen aus Ihrer eigenen gespeicherten Kopie anheften, wird die Ansicht Ihrer gespeicherten Kopie geöffnet.
* Das Filtersymbol oben links im Titel öffnet die Registerkarte „Configure tile settings“ (Kacheleinstellungen konfigurieren).
* Das Ellipsensymbol oben rechts zeigt Ihnen die Optionen „Customize title data“ (Titeldaten anpassen), „customize“ (Anpassen), „Refresh“ (Aktualisieren) und „remove from dashboard“ (Aus Dashboard entfernen) an.

## <a name="what-happens-when-i-save-a-workbook"></a>Was passiert, wenn ich eine Arbeitsmappe speichere?

* Wenn Sie eine Arbeitsmappe speichern, können Sie eine neue Kopie der Arbeitsmappe mit Ihren Bearbeitungen erstellen. Der Titel der Arbeitsmappe wird dann geändert. Beim Speichern wird die Arbeitsmappe nicht überschrieben; die aktuelle Arbeitsmappe wird immer die Standardansicht sein.
* Eine **nicht gespeicherte** Arbeitsmappe ist nur die Standardansicht.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Warum werden meine gesamten Abonnements im Portal nicht angezeigt?

Beim Start des Portals werden nur die Daten für ausgewählte Abonnements im Portal angezeigt. Navigieren Sie nach oben rechts, und klicken Sie auf das Notebook mit einem Filtersymbol, um die ausgewählten Abonnements zu ändern. Über diese Option wird die Registerkarte **Directory + subscriptions** (Verzeichnis + Abonnements) angezeigt.

![Directory + subscription (Verzeichnis + Abonnement)](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Was ist der Zeitbereich?

Der Zeitbereich zeigt Ihnen Daten aus einem bestimmten Zeitrahmen an. Wenn der Zeitbereich beispielsweise 24 Stunden beträgt, zeigt dieser Daten der letzten 24 Stunden an.

## <a name="what-is-time-granularity-time-grain"></a>Was ist Zeitgranularität (Aggregationsintervall)?

Zeitgranularität bezeichnet den Zeitunterschied zwischen zwei Datenreihenpunkten. Wenn beispielsweise das Aggregationsintervall auf eine Sekunde festgelegt ist, bedeutet das, dass jede Sekunde Metriken gesammelt werden.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Was ist die Zeitgranularität, wenn ein Teil der Arbeitsmappen an ein Dashboard angeheftet wird?

Die Standardzeitgranularität ist auf „automatisch“ festgelegt, diese kann derzeit nicht geändert werden.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Wie kann ich die Zeitspanne oder den Zeitbereich des Arbeitsmappenschritts auf meinem Dashboard ändern?

Standardmäßig ist die Zeitspanne/der Zeitbereich auf Ihrer Dashboardkachel auf 24 Stunden festgelegt. Klicken Sie auf die Ellipsen oben rechts, wählen Sie **Customize tile data** (Kacheldaten anpassen) aus, aktivieren Sie das Kontrollkästchen „override the dashboard time settings at the title level“ (Dashboardzeiteinstellungen auf Kachelebene überschreiben), und wählen Sie dann eine Zeitspanne aus dem Dropdownmenü aus, um.diesen Wert zu ändern.  

![Auswählen des Ellipsensymbols in der rechten Ecke der Kachel und Auswählen von „Customize tile data“ (Kacheldaten anpassen)](./media/storage-insights-overview/fqa-data-settings.png)

![Auswählen des Dropdownmenüs „timespan“ (Zeitspanne) in „Configure tile settings“ (Kacheleinstellungen konfigurieren) zur Änderung der Zeitspanne/des Zeitbereichs](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Wie ändere ich den Titel der Arbeitsmappe oder des Arbeitsmappenschritts, die ich an das Dashboard angeheftet habe?

Der Titel der Arbeitsmappe oder des Arbeitsmappenschritts, der an ein Dashboard angeheftet ist, behält den gleichen Namen wie in der Arbeitsmappe. Sie müssen Ihre eigene Kopie der Arbeitsmappe speichern, um den Titel zu ändern. Dann können Sie die Arbeitsmappe benennen, bevor Sie auf „Speichern“ klicken.

![Wählen Sie oben „Speichern“ aus, um eine Kopie der Arbeitsmappe zu speichern und den Namen der Arbeitsmappe zu ändern.](./media/storage-insights-overview/fqa-change-workbook-name.png)

Wählen Sie „Edit“ (Bearbeiten) in diesem Schritt aus, und wählen Sie dann das Zahnrad unten in den Einstellungen aus, um den Namen eines Schritts in Ihrer gespeicherten Arbeitsmappe zu ändern.

![Auswählen von „Edit“ (Bearbeiten) unten im Arbeitsmappenschritt zum Öffnen der Einstellungen](./media/storage-insights-overview/fqa-edit.png)
![Auswählen des Zahnrads unten in den Einstellungen zur Änderung des Namen eines Schritts](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können, und vieles mehr.

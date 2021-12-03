---
title: 'Tutorial: Erstellen einer Protokollabfragewarnung für eine Azure-Ressource'
description: Tutorial zum Erstellen einer Protokollabfragewarnung für eine Azure-Ressource.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 09/16/2021
ms.openlocfilehash: 526abdb9a5e3c16fe58551c2aa8784caf329d1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350878"
---
# <a name="tutorial-create-a-log-query-alert-for-an-azure-resource"></a>Tutorial: Erstellen einer Protokollabfragewarnung für eine Azure-Ressource
Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Durch Warnungsregeln für Protokollabfragen wird eine Warnung erstellt, wenn von einer Protokollabfrage ein bestimmtes Ergebnis zurückgegeben wird. So können Sie beispielsweise eine Warnung erhalten, wenn ein auf einem virtuellen Computer bestimmtes Ereignis erstellt wird, oder eine Warnung senden, wenn übermäßig viele anonyme Anforderungen an ein Speicherkonto gesendet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Zugreifen auf vordefinierte Protokollabfragen, die für die Unterstützung von Warnungsregeln für verschiedene Arten von Ressourcen konzipiert sind
> * Erstellen einer Warnungsregel für eine Protokollabfrage
> * Erstellen einer Aktionsgruppe zum Definieren von Benachrichtigungsdetails


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes: 

- Eine zu überwachende Azure-Ressource. Sie können jede Ressource in Ihrem Azure-Abonnement verwenden, die Diagnoseeinstellungen unterstützt. Um zu überprüfen, ob eine Ressource Diagnoseeinstellungen unterstützt, navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option **Diagnoseeinstellungen** vorhanden ist.


Wenn Sie eine Azure-Ressource verwenden, bei der es sich nicht um einen virtuellen Computer handelt, benötigen Sie Folgendes:

- Eine Diagnoseeinstellung, um die Ressourcenprotokolle von Ihrer Azure-Ressource an einen Log Analytics-Arbeitsbereich zu senden. Weitere Informationen finden Sie unter [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](../essentials/tutorial-resource-logs.md).

Wenn Sie einen virtuellen Azure-Computer verwenden, benötigen Sie Folgendes:

- Eine Datensammlungsregel, um Gastprotokolle und Metriken an einen Log Analytics-Arbeitsbereich zu senden. Weitere Informationen finden Sie unter [Tutorial: Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer](../vm/tutorial-monitor-vm-guest.md).

   
 
 ## <a name="select-a-log-query-and-verify-results"></a>Auswählen einer Protokollabfrage und Überprüfen der Ergebnisse
Die Daten werden mithilfe einer in Kusto Query Language (KQL) geschriebenen Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Erkenntnisse und Lösungen in Azure Monitor stellen Protokollabfragen bereit, um Daten aus einem bestimmten Dienst abzurufen, aber Sie können auch im Azure-Portal über Log Analytics direkt mit Protokollabfragen und deren Ergebnissen arbeiten. 

Wählen Sie im Menü Ihrer Ressource die Option **Protokolle** aus. Log Analytics wird mit dem Fenster **Abfragen** geöffnet, das vordefinierte Abfragen für Ihren **Ressourcentyp** enthält. Wählen Sie **Warnungen** aus, um speziell für Warnungsregeln entworfene Abfragen anzuzeigen.

> [!NOTE]
> Sollte das Fenster **Abfragen** nicht geöffnet werden, klicken Sie rechts oben auf **Abfragen**. 

:::image type="content" source="media/tutorial-log-alert/queries.png" lightbox="media/tutorial-log-alert/queries.png"alt-text="Log Analytics mit dem Fenster „Abfragen“":::

Wählen Sie eine Abfrage aus, und klicken Sie auf **Ausführen**, um sie im Abfrage-Editor zu laden und Ergebnisse zurückzugeben. Bei Bedarf können Sie die Abfrage ändern und erneut ausführen. Im Anschluss sehen Sie beispielsweise die Abfrage zum Anzeigen anonymer Anforderungen (**Show anonymous requests**) für Speicherkonten. Sie können den Authentifizierungstyp (**AuthenticationType**) ändern oder nach einer anderen Spalte filtern.

:::image type="content" source="media/tutorial-log-alert/query-results.png" lightbox="media/tutorial-log-alert/query-results.png"alt-text="Abfrageergebnisse":::


## <a name="create-alert-rule"></a>Erstellen einer Warnungsregel
Nachdem Sie Ihre Abfrage überprüft haben, können Sie die Warnungsregel erstellen. Wählen Sie **Neue Warnungsregel** aus, um eine neue Warnungsregel zu erstellen, die auf der aktuellen Protokollabfrage basiert. Der **Bereich** ist bereits auf die aktuelle Ressource festgelegt. Dieser Wert muss nicht geändert werden.

:::image type="content" source="media/tutorial-log-alert/create-alert-rule.png" lightbox="media/tutorial-log-alert/create-alert-rule.png"alt-text="Erstellen einer Warnungsregel":::
## <a name="configure-condition"></a>Konfigurieren der Bedingung

Auf der Registerkarte **Bedingung** ist die **Protokollabfrage** bereits angegeben. Im Abschnitt **Messung** wird die Messung der Datensätze aus der Protokollabfrage definiert. Wenn die Abfrage keine Zusammenfassung beinhaltet, besteht die einzige Option darin, die **Anzahl** von **Tabellenzeilen** zu ermitteln. Beinhaltet die Abfrage mindestens eine zusammengefasste Spalte, können Sie die Anzahl von **Tabellenzeilen** oder eine Berechnung verwenden, die auf einer der zusammengefassten Spalten basiert. **Aggregation granularity** (Aggregationsgranularität) definiert das Zeitintervall, für das die gesammelten Werte aggregiert werden. 

:::image type="content" source="media/tutorial-log-alert/alert-rule-condition.png" lightbox="media/tutorial-log-alert/alert-rule-condition.png"alt-text="Warnungsregelbedingung":::

### <a name="configure-dimensions"></a>Konfigurieren der Dimensionen
Unter **Split by dimensions** (Nach Dimensionen aufteilen) können Sie separate Warnungen für verschiedene Ressourcen erstellen. Diese Einstellung ist nützlich, wenn Sie eine Warnungsregel erstellen, die für mehrere Ressourcen gilt. Wenn der Bereich auf eine einzelne Ressource festgelegt ist, wird diese Einstellung in der Regel nicht verwendet.

:::image type="content" source="media/tutorial-log-alert/alert-rule-dimensions.png" lightbox="media/tutorial-log-alert/alert-rule-dimensions.png"alt-text="Dimensionen der Warnungsregel":::


## <a name="configure-alert-logic"></a>Konfigurieren der Warnungslogik
Konfigurieren Sie in der Warnungslogik den **Operator** und den **Schwellenwert** für den Vergleich mit dem von der Messung zurückgegebenen Wert.  Ist dieser Wert „true“, wird eine Warnung erstellt. Wählen Sie einen Wert für **Häufigkeit der Auswertung** aus, um zu definieren, wie oft die Protokollabfrage ausgeführt und ausgewertet werden soll. Je niedriger der Wert für die Häufigkeit, desto höher die Kosten für die Warnungsregel. Wenn Sie eine Häufigkeit auswählen, werden die voraussichtlichen monatlichen Kosten sowie eine Vorschau der Abfrageergebnisse für einen gewissen Zeitraum angezeigt.

Wenn die Messung also beispielsweise auf **Tabellenzeilen** basiert, kann die Warnungslogik **Größer als 0** verwendet werden, um anzugeben, dass mindestens ein Datensatz zurückgegeben wurde. Wenn es sich bei der Messung um einen Spaltenwert handelt, muss die Logik möglicherweise größer oder kleiner als ein bestimmter Schwellenwert sein. Im folgenden Beispiel sucht die Protokollabfrage nach anonymen Anforderungen für ein Speicherkonto. Im Falle einer anonymen Anforderung soll eine Warnung ausgelöst werden. In diesem Fall wird die Warnung ausgelöst, wenn eine einzelne Zeile zurückgegeben wird. Die Warnungslogik muss also **Größer als 0** lauten.

:::image type="content" source="media/tutorial-log-alert/alert-rule-alert-logic.png" lightbox="media/tutorial-log-alert/alert-rule-alert-logic.png"alt-text="Warnungslogik":::



## <a name="configure-actions"></a>Konfigurieren von Aktionen
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Konfigurieren der Details
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="media/tutorial-log-alert/alert-rule-details.png" lightbox="media/tutorial-log-alert/alert-rule-details.png"alt-text="Warnungsregeldetails":::

Klicken Sie auf **Warnungsregel erstellen**, um die Warnungsregel zu erstellen.

## <a name="view-the-alert"></a>Anzeigen der Warnung
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun wissen, wie Sie eine Protokollabfragewarnung für eine Azure-Ressource erstellen, können Sie sich als Nächstes mit Arbeitsmappen zum Erstellen interaktiver Visualisierungen von Überwachungsdaten befassen.

> [!div class="nextstepaction"]
> [Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md)
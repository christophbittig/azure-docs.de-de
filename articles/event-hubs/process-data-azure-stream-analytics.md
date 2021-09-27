---
title: Verarbeiten von Daten aus Event Hubs mit Azure Stream Analytics | Microsoft-Dokumentation
description: In diesem Artikel wird veranschaulicht, wie Sie Daten aus Ihrem Azure-Event Hub mit einem Azure Stream Analytics-Auftrag verarbeiten.
ms.date: 09/15/2021
ms.topic: article
ms.openlocfilehash: 31f8e6b7c5e45006b359c5e9545703f454dbacad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679804"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Verarbeiten von Daten aus einem Event Hub mit Azure Stream Analytics 
Der Azure Stream Analytics-Dienst vereinfacht das Erfassen, Verarbeiten und Analysieren von Streamingdaten von Azure Event Hubs und ermöglicht damit Erkenntnisse für Aktionen in Echtzeit. Mit dieser Integration können Sie schnell eine Analysepipeline über einen heißen Pfad erstellen. Sie können im Azure-Portal eingehende Daten visualisieren und eine Stream Analytics-Abfrage schreiben. Wenn die Abfrage fertig ist, können Sie sie mit nur wenigen Klicks in die Produktion verschieben. 

## <a name="key-benefits"></a>Hauptvorteile
Hier sind die wichtigsten Vorteile der Integration von Azure Event Hubs mit Azure Stream Analytics: 
- **Datenvorschau:** Sie können eine Vorschau der eingehenden Daten von einem Event Hub im Azure-Portal anzeigen.
- **Abfragetests:** Sie können eine Transformationsabfrage vorbereiten und direkt im Azure-Portal testen. Informationen zur Syntax der Abfrage finden Sie in der Dokumentation zur [Stream Analytics-Abfragesprache](/stream-analytics-query/built-in-functions-azure-stream-analytics).
- **Abfragebereitstellung in der Produktion:** Sie können die Abfrage in der Produktion bereitstellen, indem Sie einen Azure Stream Analytics-Auftrag erstellen und starten.

## <a name="end-to-end-flow"></a>Der vollständige Ablauf

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zu Ihrem **Event Hubs-Namespace** und dann zu dem **Event Hub** mit den eingehenden Daten. 
1. Wählen Sie auf der Event Hub-Seite **Daten verarbeiten** aus.  

    ![Kachel „Daten verarbeiten“](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Wählen Sie auf der Kachel **Enable real-time insights from events** (Echtzeiterkenntnisse von Ereignissen aktivieren) die Option **Erkunden** aus. 

    ![Stream Analytics auswählen](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Daraufhin wird eine Abfrageseite mit Werten angezeigt, die bereits für die folgenden Felder festgelegt wurden:
    1. Ihr **Event Hub** hat eine Eingabe für die Abfrage.
    1. Ein Beispiel, das eine **SQL-Abfrage** mit SELECT-Anweisung ist. 
    1. Ein Alias für die **Ausgabe** ermöglicht das Verweisen auf Ihre Abfragetestergebnisse. 

        ![Abfrage-Editor](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Wenn Sie dieses Feature zum ersten Mal verwenden, werden Sie auf dieser Seite nach Ihrer Berechtigung zum Erstellen einer Consumergruppe und einer Richtlinie für Ihren Event Hub gefragt, bevor Sie eine Vorschau der eingehende Daten anzeigen können.
1. Wählen Sie **Erstellen** im Bereich **Eingabevorschau** aus, wie in der vorherigen Abbildung gezeigt. 
1. Es wird sofort eine Momentaufnahme der neuesten eingehenden Daten auf dieser Registerkarte angezeigt.
    - Der Serialisierungstyp in Ihren Daten wird automatisch erkannt (JSON/CSV). Sie können ihn manuell in JSON, CSV oder AVRO ändern.
    - Sie können eine Vorschau der eingehenden Daten im Tabellenformat oder unformatiert anzeigen. 
    - Wenn die angezeigten Daten nicht aktuell sind, wählen Sie **Aktualisieren** aus, um die neuesten Ereignisse anzuzeigen. 

        Im folgenden finden Sie ein Beispiel für Daten im **Tabellenformat**: ![Ergebnisse im Tabellenformat](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Dies ist ein Beispiel für **unformatierte** Daten: 

        ![Ergebnisse im Rohformat](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Wählen Sie **Testabfrage**, um die Momentaufnahme der Testergebnisse Ihrer Abfrage auf der Registerkarte **Testergebnisse** zu sehen. Sie können die Ergebnisse auch herunterladen.

    ![Testen der Abfrageergebnisse](./media/process-data-azure-stream-analytics/test-results.png)
1. Schreiben Sie eine eigene Abfrage zum Transformieren der Daten. Weitere Informationen finden Sie in der [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference).
1. Nachdem Sie die Abfrage getestet haben, können Sie sie in die Produktion verschieben, indem Sie **Abfrage bereitstellen** auswählen. Um die Abfrage bereitzustellen, erstellen Sie einen Azure Stream Analytics-Auftrag. Legen Sie eine Ausgabe für den Auftrag fest, und starten Sie ihn. Geben Sie zum Erstellen eines Stream Analytics-Auftrags einen Namen für den Auftrag an, und wählen Sie **Erstellen** aus.

      ![Erstellen eines Azure Stream Analytics-Auftrags](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Es wird empfohlen, dass Sie eine Consumergruppe und eine Richtlinie für jeden neuen Azure Stream Analytics-Auftrag erstellen, den Sie auf der Event Hubs-Seite erstellen. Consumergruppen erlauben nur fünf Leser gleichzeitig. Wenn Sie also für jeden Auftrag eine dedizierte Consumergruppe bereitstellen, vermeiden Sie mögliche Fehler durch das Überschreiten dieses Grenzwerts. Eine dedizierte Richtlinie ermöglicht das Rotieren Ihres Schlüssels oder das Widerrufen von Berechtigungen ohne Auswirkungen auf andere Ressourcen. 
1. Ihr Stream Analytics-Auftrag wurde damit erstellt – die Abfrage ist identisch mit Ihrer getesteten, und die Eingabe ist Ihr Event Hub. 

9.  Legen Sie zum Fertigstellen der Pipeline die **Ausgabe** der Abfrage fest, und wählen **Starten** aus, um den Auftrag zu starten.

    > [!NOTE]
    > Vergessen Sie nicht, vor dem Starten des Auftrags den Ausgabealias durch den Ausgabenamen zu ersetzen, den Sie in Azure Stream Analytics erstellt haben.

      ![Festlegen der Ausgabe und Starten des Auftrags](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Bekannte Einschränkungen
Beim Ausführen von Abfragetests dauert das Laden der Testergebnisse etwa 6 Sekunden. Wir arbeiten daran, die Leistung beim Testen zu verbessern. Nach der Bereitstellung in der Produktion liegt die Latenz von Azure Stream Analytics unter einer Sekunde.

## <a name="streaming-units"></a>Streamingeinheiten
Ihr Azure Stream Analytics-Auftrag nutzt standardmäßig drei Streamingeinheiten (SUs). Wählen Sie zum Anpassen dieser Einstellung im linken Menü auf der Seite des **Stream Analytics-Auftrags** im Azure-Portal **Skalieren** aus. Weitere Informationen zu Streamingeinheiten finden Sie unter [Verstehen und Anpassen von Streamingeinheiten](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skalieren von Streamingeinheiten](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Stream Analytics-Abfragen unter [Stream Analytics-Abfragesprache](/stream-analytics-query/built-in-functions-azure-stream-analytics).

---
title: Planen Ihrer Aufträge
description: Verwenden Sie die Auftragsplanung, um Ihre Tasks zu verwalten.
ms.topic: how-to
ms.date: 07/16/2021
ms.custom: seodec18
ms.openlocfilehash: a247be1826cd68ef2ac1302c1910eae74b2d65b6
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405771"
---
# <a name="schedule-jobs-for-efficiency"></a>Planen von Aufträgen für Effizienz

Die Planung von Batchaufträgen ermöglicht es Ihnen, die Aufträge zu priorisieren, die Sie zuerst ausführen möchten, und gleichzeitig [Tasks zu berücksichtigen, die von anderen Tasks abhängig sind](batch-task-dependencies.md). Durch die Planung Ihrer Aufträge können Sie sicherstellen, dass Sie möglichst wenig Ressourcen verbrauchen. Knoten können stillgelegt werden, wenn sie nicht benötigt werden, und Tasks, die von anderen Tasks abhängig sind, werden mit einem Just-in-Time-Ansatz erledigt, was zur Optimierung der Workflows führt. Sie können Aufträge auch auf „AutoVervollständigen“ festlegen, da nur ein Auftrag gleichzeitig ausgeführt wird und ein neuer Auftrag erst gestartet wird, wenn der vorherige abgeschlossen ist.

Tasks, die Sie mit dem Task im Auftrags-Manager planen, werden einem Auftrag zugeordnet. Der Task im Auftrags-Manager erstellt die Tasks für den Auftrag. Dazu muss sich der Task im Auftrags-Manager beim Batch-Konto authentifizieren. Verwenden Sie das Zugriffstoken AZ_BATCH_AUTHENTICATION_TOKEN. Das Token ermöglicht den Zugriff auf den Rest des Auftrags.

Informationen zum Verwalten eines Auftrags mit der Azure-Befehlszeilenschnittstelle finden Sie unter [az batch job-schedule](/cli/azure/batch/job-schedule). Sie können auch Auftragszeitpläne im Azure-Portal erstellen.

## <a name="schedule-a-job-in-the-azure-portal"></a>Planen eines Auftrags im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie das Batch-Konto aus, in dem Sie Aufträge planen möchten.
1. Wählen Sie im linken Navigationsbereich die Option **Auftragszeitpläne** aus.
1. Wählen Sie **Hinzufügen** aus, um einen neuen Auftragszeitplan zu erstellen.
1. Geben Sie im Formular **Grundeinstellungen** die folgenden Informationen ein:
   - **Auftragszeitplan-ID**: Der eindeutige Bezeichner für diesen Auftragszeitplan
   - **Anzeigename**: Dieser Name ist optional und muss nicht eindeutig sein. Er darf maximal 1024 Zeichen lang sein.
   - **Nicht ausführen bis**: Gibt den frühesten Zeitpunkt für die Auftragsausführung an. Wenn Sie diese Option nicht festlegen, ist der Zeitplan sofort zur Ausführung von Aufträgen verfügbar.
   - **Nicht ausführen nach**: Nach dem hier angegebenen Zeitpunkt werden keine Aufträge mehr ausgeführt. Wenn Sie keinen Zeitpunkt festlegen, erstellen Sie einen Zeitplan für wiederkehrende Aufträge, der aktiv bleibt, bis Sie ihn explizit beenden.
   - **Wiederholungsintervall**: Wählen Sie **Aktiviert** aus, wenn Sie festlegen möchten, wie viel Zeit zwischen den Aufträgen liegen soll. Zu jedem Zeitpunkt kann nur ein Auftrag geplant sein. Wenn also der Zeitpunkt gekommen ist, im Rahmen eines Auftragsplans einen neuen Auftrag zu erstellen, der vorherige Auftrag aber noch ausgeführt wird, erstellt der Batch-Dienst den neuen Auftrag erst, wenn der vorherige Auftrag beendet ist.
   - **Startfenster**: Wählen Sie **Benutzerdefiniert** aus, wenn Sie das Zeitintervall festlegen möchten, in dem ein Auftrag erstellt werden muss. Wenn innerhalb dieses Zeitfensters kein Auftrag erstellt wird, wird bis zur nächsten Wiederholung des Zeitplans kein neuer Auftrag erstellt.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-02.png" alt-text="Screenshot: Optionen zum Hinzufügen von Auftragszeitplänen im Azure-Portal":::  

1. Geben Sie unten im Standardformular den Pool an, in dem der Auftrag ausgeführt werden soll. Wählen Sie **Aktualisieren** aus, um diesen aus einer Liste der Pools in Ihrem Batch-Konto auszuwählen.
1. Geben Sie neben der **Pool-ID** die folgenden Informationen ein:
   - **Task „Auftragskonfiguration“** : Wählen Sie **Aktualisieren** aus, um den Task im Auftrags-Manager sowie die Tasks für die Auftragsvorbereitung- und freigabe zu benennen und zu konfigurieren, falls Sie diese verwenden.
   - **Anzeigename**: Dieser Name ist optional und muss nicht eindeutig sein. Er darf maximal 1024 Zeichen lang sein.
   - **Priorität:** Verwenden Sie den Schieberegler, um eine Priorität für den Auftrag festzulegen, oder geben Sie einen Wert in das Feld ein.
   - **Max. Gesamtbetrachtungszeit**: Wählen Sie **Benutzerdefiniert** aus, wenn Sie eine maximale Ausführungszeit für den Auftrag festlegen möchten. Dann beendet Batch den Auftrag, wenn er nicht innerhalb dieses Zeitrahmens abgeschlossen wird.
   - **Max. Wiederholungszahl für Tasks**: Wählen Sie **Benutzerdefiniert** aus, wenn Sie festlegen möchten, wie oft ein Task wiederholt werden darf. Wenn ein Task so häufig wie nötig wiederholt werden soll, wählen Sie **Unbegrenzt** aus. Dies ist nicht das gleiche wie die mögliche Anzahl der Wiederholungen für einen API-Befehl.
   - **Bei Abschluss aller Tasks**: Die Standardeinstellung ist „NoAction“. Sie können jedoch auch **TerminateJob** auswählen, wenn der Auftrag beendet werden soll, wenn alle Tasks abgeschlossen wurden oder keine Tasks im Auftrag vorhanden sind.
   - **Bei Fehler eines Tasks**: Ein Task schlägt fehl, wenn die Anzahl der Wiederholungsversuche erschöpft oder beim Starten des Tasks ein Fehler aufgetreten ist. Die Standardeinstellung ist „NoAction“. Sie können jedoch auch **PerformExitOptionsJobAction** auswählen, wenn bei einem Fehler die Aktion ausgeführt werden soll, die der Exitbedingung des Tasks zugeordnet ist.

     :::image type="content" source="media/batch-job-schedule/add-job-schedule-03.png" alt-text="Screenshot: Spezifikationsoptionen für einen neuen Auftragszeitplan im Azure-Portal":::

1. Wählen Sie **Speichern** aus, um Ihren Auftragszeitplan zu erstellen.

Um die Ausführung des Auftrags nachzuverfolgen, kehren Sie zu **Auftragszeitpläne** zurück, und wählen Sie den Auftragszeitplan aus. Erweitern Sie **Ausführungsinformationen**, um die Details anzuzeigen. Sie können den Auftragszeitplan auch über diesen Bildschirm beenden, löschen oder deaktivieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zu [Aufträgen und Tasks](jobs-and-tasks.md).
- [Erstellen von Taskabhängigkeiten zum Ausführen von Tasks, die von anderen Tasks abhängen](batch-task-dependencies.md)

---
title: Problembehandlung von „VMs starten/beenden (Vorschau)“
description: In diesem Artikel erfahren Sie, wie Sie Probleme behandeln, die bei der Funktion „VMs starten/beenden (Vorschau)“ für Ihre Azure-VMs auftreten.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2908f9a8383897ae0bda162e6f980e02d6c81bc1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966987"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Behandeln allgemeiner Probleme bei „VMs starten/beenden (Vorschau)“

Dieser Artikel enthält Informationen zur Behandlung und Behebung von Problemen, die bei dem Versuch auftreten können, „VMs starten/beenden (Vorschau)“ zu installieren und zu konfigurieren. Allgemeine Informationen finden Sie in der [Übersicht zu „VMs starten/beenden“](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Allgemeine Validierung und Problembehandlung

In diesem Abschnitt wird erläutert, wie Sie allgemeine Probleme mit den Zeitplanszenarien behandeln und die Ursache ermitteln können.

### <a name="azure-dashboard"></a>Azure-Dashboard

Sie können damit beginnen, dass Sie das freigegebene Azure-Dashboard überprüfen. Das freigegebene Azure-Dashboard, das als Teil von „VMs starten/beenden v2 (Vorschau)“ bereitgestellt wird, ist eine schnelle und einfache Möglichkeit, um den Status jedes Vorgangs zu überprüfen, der auf ihren VMs ausgeführt wird. Um alle jüngst auf Ihren VMs ausgeführten Vorgänge anzuzeigen, sehen Sie auf der Kachel **Recently attempted actions on VMs** (Kürzlich versuchte Aktionen auf VMs) nach. Es gibt eine Wartezeit von ungefähr fünf Minuten, bis die Daten im Bericht angezeigt werden, weil sie aus der Application Insights-Ressource abgerufen werden.

### <a name="logic-apps"></a>Logic Apps

Abhängig davon, welche Logik-Apps Sie für die Unterstützung Ihres Starten/Beenden-Szenarios aktiviert haben, können Sie deren Ausführungsverlauf überprüfen, um zu ermitteln, warum das geplante Starten/Herunterfahren-Szenario für mindestens eine Ziel-VM nicht erfolgreich abgeschlossen wurde. Informationen dazu, wie Sie dies detailliert überprüfen können, finden Sie unter [Ausführungsverlauf von Logik-Apps](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Azure Storage

Sie können die Details zu den Vorgängen überprüfen, die auf den virtuellen Computern ausgeführt werden, die in die Tabelle **requestsstoretable** in dem Azure-Speicherkonto geschrieben werden, das für „VMs starten/beenden v2 (Vorschau)“ verwendet wurde. Führen Sie die folgenden Schritte aus, um diese Datensätze anzuzeigen.

1. Navigieren Sie im Azure-Portal zum Speicherkonto, und wählen Sie unter dem Konto im linken Bereich **Storage-Explorer (Vorschau)** aus.
1. Wählen Sie **TABELLEN** aus, und wählen Sie dann **requeststoretable** aus.
1. Jede Zeile in der Tabelle stellt die Starten/Beenden-Aktion dar, die auf einem virtuellen Azure-Computer ausgeführt wurde, basierend auf dem im Logik-App-Szenario definierten Zielbereich. Sie können die Ergebnisse nach jeder der Datensatzeigenschaften (z. B. TIMESTAMP, ACTION oder TARGETTOPLEVELRESOURCENAME) filtern.

### <a name="azure-functions"></a>Azure Functions

Sie können die neuesten Aufrufdetails für alle Azure Functions überprüfen, die für die Ausführung von VM-Starts und -Beendigungen verantwortlich sind. Sehen wir uns zuerst den Ausführungsflow an.

Der Ausführungsflow für sowohl das **geplante** als auch das **sequenzierte** Szenario wird von derselben Funktion gesteuert. Das Nutzlastschema bestimmt, welches Szenario ausgeführt wird. Für das **geplante** Szenario ist der Ausführungsflow: **Geplant** HTTP > **VirtualMachineRequestOrchestrator**-Warteschlange > **VirtualMachineRequestExecutor**-Warteschlange.

Die HTTP-Funktion **Geplant** wird aus der Logik-App mit dem Nutzlastschema aufgerufen. Sobald die HTTP-Funktion **Geplant** die Anforderung empfängt, sendet Sie die Informationen an die **Orchestrator**-Warteschlangenfunktion, die wiederum mehrere Warteschlangen für jede VM erstellt, um die Aktion auszuführen.

Führen Sie die folgenden Schritte aus, um die Aufrufdetails anzuzeigen.

1. Navigieren Sie im Azure-Portal zu **Azure Functions**.
1. Wählen Sie in der Liste die Funktions-App für „VMs starten/beenden v2 (Vorschau)“ aus.
1. Wählen Sie im linken Bereich **Funktionen** aus.
1. In der Liste werden mehrere Funktionen angezeigt, die für jedes Szenario zugeordnet sind. Wählen Sie die HTTP-Funktion **Geplant** aus.
1. Wählen Sie im linken Bereich **Überwachen** aus.
1. Wählen Sie die neueste Ausführungsablaufverfolgung aus, um die Aufrufdetails und den Meldungsabschnitt für die ausführliche Protokollierung anzuzeigen.
1. Wiederholen Sie dieselben Schritte für jede Funktion, die im Rahmen der weiter oben durchgeführten Überprüfung des Ausführungsflows beschrieben wurde.

Weitere Informationen zum Überwachen von Azure Functions finden Sie unter [Analysieren der Azure Functions-Telemetrie in Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung von Azure Functions und Logik-Apps:

* [Überwachen von Azure Functions](../../azure-functions/functions-monitoring.md).

* [Konfigurieren der Überwachung für Azure Functions](../../azure-functions/configure-monitoring.md).

* [Überwachen von Logik-Apps](../../logic-apps/monitor-logic-apps.md).

* Falls bei der Bereitstellung bzw. Verwendung von „VMs starten/beenden v2 (Vorschau)“ Probleme auftreten oder Sie eine Frage haben, können Sie auf [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) ein Problem melden. Das Erstellen eines Azure-Supportvorfalls über die [Azure-Supportwebsite](https://azure.microsoft.com/support/options/) ist für diese Vorschauversion nicht verfügbar. 

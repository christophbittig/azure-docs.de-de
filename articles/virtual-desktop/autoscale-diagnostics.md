---
title: Einrichten der Diagnose für die Autoskalierungsfunktion in Azure Virtual Desktop
description: Einrichten von Diagnoseberichten für den Skalierungsdienst in Ihrer Azure Virtual Desktop-Bereitstellung.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b5621d829050c6749795df1191ea53d835e16487
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181641"
---
# <a name="set-up-diagnostics-for-the-autoscale-feature-preview"></a>Einrichten der Diagnose für die Autoskalierungsfunktion (Vorschauversion)

> [!IMPORTANT]
> Die automatische Skalierung ist derzeit im Vorschaustadium verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit der Diagnose können Sie potenzielle Probleme überwachen und beheben, bevor diese Ihren Plan für die Autoskalierung (Vorschauversion) beeinträchtigen.

Derzeit können Sie entweder Diagnoseprotokolle für die Autoskalierungsfunktion an ein Azure Storage-Konto senden oder Protokolle mit Event Hub nutzen. Wenn Sie ein Azure Storage-Konto verwenden, stellen Sie sicher, dass es sich in derselben Region befindet wie Ihr Skalierungsplan. Weitere Informationen zu Diagnoseeinstellungen finden Sie unter [Erstellen von Diagnoseeinstellungen](../azure-monitor/essentials/diagnostic-settings.md). Weitere Informationen zur Erfassungszeit von Ressourcenprotokolldaten finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

## <a name="enable-diagnostics-for-scaling-plans"></a>Aktivieren der Diagnose für Skalierungspläne

So aktivieren Sie die Diagnose für Ihren Skalierungsplan

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

2. Wählen Sie **Skalierungspläne** und dann den Skalierungsplan aus, den der Bericht nachverfolgen soll.

3. Wechseln Sie zu **Diagnoseeinstellungen**, und wählen Sie **Diagnoseeinstellung hinzufügen** aus.

4. Geben Sie einen Namen für die Diagnoseeinstellung ein.

5. Wählen Sie als Nächstes **Autoskalierung** aus, und wählen Sie je nach gewünschtem Sendeziel für den Bericht **Speicherkonto** oder **Event Hub** aus.

6. Wählen Sie **Speichern** aus.

## <a name="set-log-location-in-azure-storage"></a>Festlegen des Protokollspeicherorts in Azure Storage

Nachdem Sie Ihre Diagnoseeinstellungen konfiguriert haben, finden Sie die Protokolle wie folgt:

1. Wechseln Sie im Azure-Portal zu der Speichergruppe, an die Sie die Diagnoseprotokolle gesendet haben.

2. Wählen Sie **Container** aus. Ein Ordner namens **insight-logs-autoscaling** sollte geöffnet werden.

3. Wählen Sie den **Ordner „insight-logs-autoscaling“** aus, und öffnen Sie das Protokoll, das Sie überprüfen möchten. Öffnen Sie Ordner in diesem Ordner, bis die JSON-Datei angezeigt wird. Wählen Sie dann alle Elemente in diesem Ordner aus, klicken Sie mit der rechten Maustaste darauf, und laden Sie sie auf Ihren lokalen Computer herunter.

4. Öffnen Sie abschließend die JSON-Datei in einem Text-Editor Ihrer Wahl.

## <a name="view-diagnostic-logs"></a>Anzeigen von Diagnoseprotokollen

Nachdem Sie die JSON-Datei geöffnet haben, lassen Sie uns kurz die Bedeutung der einzelnen Berichtelemente durchgehen:

- Die **CorrelationID** ist die ID, die Sie beim Erstellen eines Supportfalls anzeigen müssen.

- **OperationName** ist der Typ des Vorgangs, der bei Auftreten des Problems ausgeführt wird.

- **ResultType** ist das Ergebnis des Vorgangs. Dieses Element kann aufzeigen, wo Probleme liegen, wenn Sie unvollständige Ergebnisse bemerken.

- **Message** ist die Fehlermeldung, die Informationen zum unvollständigen Vorgang enthält. Diese Meldung kann Links zu wichtigen Dokumentationen für die Problembehandlung enthalten. Lesen Sie sie daher sorgfältig.

Die folgende JSON-Datei ist ein Beispiel für die angezeigten Inhalte beim Öffnen eines Berichts:

```json
{
    "host_Ring": "R0",
    "Level": 4,
    "ActivityId": "c1111111-1111-1111-b111-11111cd1ba1b1",
    "time": "2021-08-31T16:00:46.5246835Z",
    "resourceId": "/SUBSCRIPTIONS/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.DESKTOPVIRTUALIZATION/SCALINGPLANS/TESTPLAN",
    "operationName": "HostPoolLoadBalancerTypeUpdated",
    "category": "Autoscale",
    "resultType": "Succeeded",
    "level": "Informational",
    "correlationId": "35ec619b-b5d8-5b5f-9242-824aa4d2b878",
    "properties": {
        "Message": "Host pool's load balancing algorithm updated",
        "HostPoolArmPath": "/subscriptions/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/resourcegroups/test/providers/microsoft.desktopvirtualization/hostpools/testHostPool ",
        "PreviousLoadBalancerType": "BreadthFirst",
        "NewLoadBalancerType": "DepthFirst"
    }
}. L
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines Skalierungsplans finden Sie unter [Autoskalierung (Vorschauversion) für Azure Virtual Desktop-Sitzungshosts](autoscale-scaling-plan.md).
- [Weisen Sie Ihren Skalierungsplans einem neuen oder vorhandenen Hostpool zu](autoscale-new-existing-host-pool.md).

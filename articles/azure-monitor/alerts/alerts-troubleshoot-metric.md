---
title: Behandeln von Problemen mit Azure-Metrikwarnungen
description: In diesem Artikel werden gängige Probleme mit Azure Monitor-Metrikwarnungen und mögliche Lösungen behandelt.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 09/30/2021
ms.openlocfilehash: df925bd149c8516f4c6af8b49a65969737aaffa2
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352385"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Behandeln von Problemen mit Azure Monitor-Metrikwarnungen 

In diesem Artikel werden häufige Probleme mit [Metrikwarnungen](alerts-metric-overview.md) in Azure Monitor sowie Lösungen zu diesen erläutert.

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. Weitere Informationen über Warnungen finden Sie unter [Überblick über Warnungen in Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Metrikwarnung wurde fälschlicherweise nicht ausgelöst 

Wenn Sie der Meinung sind, dass eine Metrikwarnung hätte ausgelöst werden sollen, dies aber nicht der Fall war und sie nicht im Azure-Portal angezeigt wird, versuchen Sie Folgendes:

1. **Konfiguration:** Überprüfen Sie die Konfiguration der Metrikwarnungsregel, um sicherzustellen, dass sie richtig konfiguriert ist:
    - Überprüfen Sie, ob die Optionen **Aggregationstyp** und **Aggregationsgranularität (Zeitraum)** wie erwartet konfiguriert sind. Die Einstellung **Aggregationstyp** bestimmt, wie Metrikwerte aggregiert werden ([weitere Informationen](../essentials/metrics-aggregation-explained.md#aggregation-types)). Die Einstellung **Aggregationsgranularität (Zeitraum)** steuert, wie weit die in die Auswertung einbezogenen Metrikwerte zurückreichen sollen, wenn die Warnungsregel ausgeführt wird.
    -  Vergewissern Sie sich, dass die Optionen **Schwellenwert** bzw. **Sensibilität** wie erwartet konfiguriert sind.
    - Überprüfen Sie bei einer Regel, für die dynamische Schwellwerte verwendet werden, ob die erweiterten Einstellungen konfiguriert sind. Der Grund ist, dass die **Anzahl von Verstößen** ggf. zu einer Filterung der Warnungen führt und die Option **Vor dem folgenden Datum liegende Daten ignorieren** sich darauf auswirken kann, wie die Schwellenwerte berechnet werden.

       > [!NOTE] 
       > Dynamische Schwellwerte werden erst nach mindestens 3 Tagen und 30 Metrikproben aktiv.

2. **Ausgelöst aber keine Benachrichtigung:** Überprüfen Sie die Liste der [ausgelösten Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) auf die ausgelöste Warnung. Wenn die Warnung in der Liste angezeigt wird, Sie aber ein Problem mit einigen ihrer Aktionen oder Benachrichtigungen haben, finden Sie [hier](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected) weitere Informationen.

3. **Bereits aktiv:** Überprüfen Sie, ob für die Metrikzeitreihe, für die Sie eine Warnung erwarten, bereits eine Warnung ausgelöst wurde. Metrikwarnungen sind zustandsbehaftet. Das bedeutet: Nachdem eine Warnung für eine bestimmte Metrikzeitreihe ausgelöst wurde, werden erst wieder Warnungen für diese Zeitreihe ausgelöst, wenn das Problem nicht mehr vorliegt. Durch diese Entwurfsentscheidung wird Stördatenverkehr reduziert. Die Warnung wird automatisch gelöst, wenn die Warnungsbedingung bei drei aufeinanderfolgenden Auswertungen nicht erfüllt wird.

4. **Verwendete Dimensionen:** Falls Sie einige [Dimensionswerte für eine Metrik](./alerts-metric-overview.md#using-dimensions) ausgewählt haben, überwacht die Warnungsregel jede einzelne Metrikzeitreihe (definiert gemäß einer Kombination von Dimensionswerten) auf das Erreichen des Schwellenwerts. Konfigurieren Sie für die Metrik eine weitere Warnungsregel, ohne Dimensionen auszuwählen, um auch die aggregierten Metrikzeitreihen (ohne Auswahl von Dimensionen) zu überwachen.

5. **Aggregation und Zeitgranularität:** Wenn Sie die Metrik mithilfe von [Metrikdiagrammen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics) visualisieren, stellen Sie Folgendes sicher:
    * Die ausgewählte **Aggregation** im Metrikdiagramm entspricht dem **Aggregationstyp** in Ihrer Warnungsregel.
    * Die ausgewählte **Zeitgranularität** entspricht dem Wert der **Aggregationsgranularität (Zeitraum)** in Ihrer Warnungsregel (nicht auf „Automatisch“ festgelegt).

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Metrikwarnung wurde fälschlicherweise ausgelöst

Wenn Sie der Meinung sind, dass Ihre Metrikwarnung fälschlicherweise ausgelöst wurde, können Ihnen die folgenden Schritte bei der Lösung des Problems helfen.

1. Suchen Sie in der Liste der [ausgelösten Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) nach der ausgelösten Warnung, und klicken Sie darauf, um die Details anzuzeigen. Überprüfen Sie die Informationen unter **Why did this alert fire?** (Warum wurde diese Warnung ausgelöst?), um das Metrikdiagramm, den **Metrikwert** und den **Schwellenwert** für den Zeitpunkt anzuzeigen, zu dem die Warnung ausgelöst wurde.

    > [!NOTE] 
    > Wenn Sie den Bedingungstyp „Dynamische Schwellenwerte“ verwenden und der Meinung sind, dass die verwendeten Schwellenwerte nicht stimmen, können Sie uns über das Stirnrunzeln-Symbol Feedback zukommen lassen. Dieses Feedback beeinflusst die Forschung zu Machine Learning-Algorithmen und hilft bei der Verbesserung der zukünftigen Erkennung.

2. Wenn Sie mehrere Dimensionswerte für eine Metrik ausgewählt haben, wird die Warnung ausgelöst, wenn eine **beliebige** Metrikzeitreihe (definiert gemäß einer Kombination von Dimensionswerten) den Schwellenwert erreicht. Weitere Informationen zur Verwendung von Dimensionen in Metrikwarnungen finden Sie [hier](./alerts-metric-overview.md#using-dimensions).

3. Überprüfen Sie die Konfiguration der Warnungsregel, um sicherzustellen, dass sie richtig konfiguriert ist:
    - Stellen Sie sicher, dass die Einstellungen für **Aggregationstyp**, **Aggregationsgranularität (Zeitraum)** und **Schwellenwert** oder **Sensitivität** wie gewünscht konfiguriert sind.
    - Überprüfen Sie bei einer Regel, die dynamische Schwellwerte verwendet, ob die erweiterten Einstellungen konfiguriert sind, da **Anzahl von Verstößen** ein Filtern von Warnungen bewirken kann und die Option **Vor dem folgenden Datum liegende Daten ignorieren** sich darauf auswirken kann, wie die Schwellenwerte berechnet werden.

   > [!NOTE]
   > Dynamische Schwellwerte werden erst nach mindestens 3 Tagen und 30 Metrikproben aktiv.

4. Stellen Sie Folgendes sicher, wenn Sie die Metrik mit dem [Metrikdiagramm](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics) visualisieren:
    - Die ausgewählte **Aggregation** im Metrikdiagramm entspricht dem **Aggregationstyp** in Ihrer Warnungsregel.
    - Die ausgewählte **Zeitgranularität** entspricht dem Wert der **Aggregationsgranularität (Zeitraum)** in Ihrer Warnungsregel (nicht auf „Automatisch“ festgelegt).

5. Wenn die Warnung ausgelöst wurde, während bereits ausgelöste Warnungen vorhanden waren, die dieselben Kriterien überwachen (und nicht aufgelöst wurden), prüfen Sie, ob die Warnungsregel so konfiguriert wurde, dass Warnungen nicht automatisch aufgelöst werden. Durch eine solche Konfiguration wird die Warnungsregel zustandslos. Das bedeutet, dass die Warnungsregel ausgelöste Warnungen nicht automatisch auflöst und eine ausgelöste Warnung nicht aufgelöst werden muss, bevor sie erneut in derselben Zeitreihe ausgelöst wird.
    Sie können auf eine der folgenden Arten überprüfen, ob die Warnungsregel so konfiguriert ist, dass Warnungen nicht automatisch aufgelöst werden:
    - Bearbeiten Sie die Warnungsregel im Azure-Portal, und überprüfen Sie, ob das Kontrollkästchen „Warnungen automatisch auflösen“ deaktiviert ist (verfügbar im Abschnitt „Details zur Warnungsregel“).
    - Überprüfen Sie das Skript, das zum Bereitstellen der Warnungsregel verwendet wird, oder rufen Sie die Warnungsregeldefinition ab, und überprüfen Sie, ob die Eigenschaft *autoMitigate* auf **false** festgelegt ist.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Metrik für Warnung kann nicht gefunden werden – Metriken für virtuelle Gastcomputer

Um Warnungen für Metriken für Gastbetriebssysteme (z. B. zu Arbeitsspeicher oder Speicherplatz auf dem Datenträger) zu senden, stellen Sie sicher, dass der erforderliche Agent zum Erfassen dieser Daten in Azure Monitor-Metriken installiert ist:
- [Für virtuelle Windows-Computer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Für virtuelle Linux-Computer](../essentials/collect-custom-metrics-linux-telegraf.md)

Weitere Informationen zum Erfassen von Daten aus dem Gastbetriebssystem eines virtuellen Computers finden Sie [hier](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Wenn Sie für Gastmetriken das Senden zu einem Log Analytics-Arbeitsbereich konfiguriert haben, werden die Metriken unter der Log Analytics-Arbeitsbereichsressource angezeigt. Die Daten werden dann **erst** angezeigt, nachdem eine Warnungsregel für deren Überwachung erstellt wurde. Führen Sie hierzu die Schritte zum [Konfigurieren der Metrikwarnung für Protokolle](./alerts-metric-logs.md#configuring-metric-alert-for-logs) aus.

> [!NOTE] 
> Das Überwachen einer Gastmetrik für mehrere virtuelle Computer mit einer einzigen Warnungsregel wird derzeit von Metrikwarnungen nicht unterstützt. Sie können dies mit einer [Protokollwarnungsregel](./alerts-unified-log.md) erreichen. Stellen Sie dazu sicher, dass die Gastmetriken in einem Log Analytics-Arbeitsbereich gesammelt werden, und erstellen Sie eine Protokollwarnungsregel für den Arbeitsbereich.

## <a name="cant-find-the-metric-to-alert-on"></a>Metrik für Warnung kann nicht gefunden werden

Wenn Sie für eine bestimmte Metrik eine Warnung anzeigen möchten, diese aber beim Erstellen einer Warnungsregel nicht angezeigt wird, überprüfen Sie Folgendes:
- Wenn keine Metriken für die Ressource angezeigt werden, [überprüfen Sie, ob der Ressourcentyp für Metrikwarnungen unterstützt wird](./alerts-metric-near-real-time.md).
- Wenn einige Metriken für die Ressource angezeigt werden, eine bestimmte Metrik jedoch nicht gefunden werden kann, [überprüfen Sie, ob diese Metrik verfügbar ist](../essentials/metrics-supported.md). Wenn dies der Fall ist, sehen Sie in der Metrikbeschreibung nach, ob diese nur in bestimmten Versionen oder Editionen der Ressource verfügbar ist.
- Wenn die Metrik für die Ressource nicht verfügbar ist, ist Sie möglicherweise in den Ressourcenprotokollen verfügbar und kann mithilfe von Protokollwarnungen überwacht werden. Hier finden Sie weitere Informationen zum [Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Metrikdimension für Warnung kann nicht gefunden werden

Wenn Sie Warnungen für [spezifische Dimensionswerte einer Metrik](./alerts-metric-overview.md#using-dimensions) ausgeben möchten und diese nicht finden können, sollten Sie den folgenden Hinweis beachten:

1. Es kann einige Minuten dauern, bis die Dimensionswerte in der Liste **Dimensionswerte** angezeigt werden.
2. Die angezeigten Dimensionswerte basieren auf Metrikdaten, die am letzten Tag erfasst wurden.
3. Wenn der Dimensionswert noch nicht ausgegeben wurde oder nicht angezeigt wird, können Sie mithilfe der Option „Benutzerdefinierten Wert hinzufügen“ einen benutzerdefinierten Dimensionswert hinzufügen.
4. Wenn Sie für alle möglichen Werte einer Dimension (einschließlich zukünftiger Werte) eine Warnung ausgeben möchten, wählen Sie die Option „Alle aktuellen und zukünftigen Werte auswählen“ aus.
5. Benutzerdefinierte Metrikdimensionen von Application Insights-Ressourcen sind standardmäßig deaktiviert. Informationen zum Aktivieren der Auflistung von Dimensionen für diese benutzerdefinierten Metriken finden Sie [hier](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Metrikwarnungsregeln sind für eine gelöschte Ressource weiterhin definiert 

Wenn eine Azure-Ressource gelöscht wird, werden die zugehörigen Metrikwarnungsregeln nicht automatisch gelöscht. So löschen Sie Warnungsregeln, die einer gelöschten Ressource zugeordnet sind:

1. Öffnen Sie die Ressourcengruppe, in der die gelöschte Ressource definiert war.
1. Aktivieren Sie in der Liste mit den Ressourcen das Kontrollkästchen **Ausgeblendete Typen anzeigen**.
1. Filtern Sie die Liste nach dem Typ **microsoft.insights/metricalerts**.
1. Wählen Sie die relevanten Warnungsregeln aus, und klicken Sie anschließend auf **Löschen**.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Metrikwarnungen immer anzeigen, wenn meine Bedingung erfüllt wird

Metrikwarnungen sind in der Standardeinstellung zustandsbehaftet. Daher werden keine zusätzlichen Warnungen ausgelöst, wenn für eine angegebene Zeitreihe bereits eine Warnung ausgelöst wurde. Wenn Sie eine bestimmte Metrikwarnungsregel zustandslos machen und bei jeder Auswertung, bei der die Warnungsbedingung erfüllt ist, benachrichtigt werden möchten, führen Sie einen der folgenden Schritte aus:
- Wenn Sie die Warnungsregel programmgesteuert erstellen (z. B. über [Resource Manager](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)), legen Sie die Eigenschaft *autoMitigate* auf „False“ fest.
- Wenn Sie die Warnungsregel über das Azure-Portal erstellen, deaktivieren Sie die Option „Warnungen automatisch auflösen“ (verfügbar im Abschnitt „Details zur Warnungsregel“).

> [!NOTE] 
> Durch die Konfiguration einer Metrikwarnungsregel als zustandslos wird verhindert, dass ausgelöste Warnungen gelöst werden. Daher verbleiben die ausgelösten Warnungen, selbst wenn die Bedingung nicht mehr erfüllt ist, bis zum Ablauf der Beibehaltungsdauer von 30 Tagen im ausgelösten Zustand.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definieren einer Warnungsregel für eine benutzerdefinierte Metrik, die noch nicht ausgegeben wurde

Beim Erstellen einer Metrikwarnungsregel wird der Metrikname anhand der [Metrikdefinitions-API](/rest/api/monitor/metricdefinitions/list) überprüft, um sicherzustellen, dass er vorhanden ist. In einigen Fällen möchten Sie eine Warnungsregel für eine benutzerdefinierte Metrik erstellen, noch bevor diese ausgegeben wird. Ein Beispiel wäre, wenn Sie eine Application Insights-Ressource erstellen (mit einer Resource Manager-Vorlage), die eine benutzerdefinierte Metrik ausgibt, sowie eine Warnungsregel, die diese Metrik überwacht.

Um zu vermeiden, dass bei der Bereitstellung Fehler auftreten, wenn Sie versuchen, die Definitionen der benutzerdefinierten Metrik zu überprüfen, können Sie den Parameter *skipMetricValidation* im Kriterienabschnitt der Warnungsregel verwenden, durch den die Metrikvalidierung übersprungen wird. Im folgenden Beispiel finden Sie Informationen zur Verwendung dieses Parameters in einer Resource Manager-Vorlage. Weitere Informationen finden Sie unter [Erstellen einer Metrikwarnung anhand einer Resource Manager-Vorlage](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```
> [!NOTE] 
> Die Verwendung des Parameters *skipMetricValidation* ist auch erforderlich, wenn sie eine Warnungsregel für eine vorhandene benutzerdefinierte Metrik definiert, die nicht mehr als drei Tage ausgegeben wurde.

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportieren der Azure Resource Manager-Vorlage einer Metrikwarnungsregel über das Azure-Portal

Indem Sie die Resource Manager-Vorlage einer Metrikwarnungsregel exportieren, können Sie die JSON-Syntax und -Eigenschaften besser nachvollziehen. Der Export kann außerdem zur Automatisierung zukünftiger Bereitstellungen verwendet werden.
1. Öffnen Sie die Warnungsregel im Azure-Portal, um die zugehörigen Details anzuzeigen.
2. Klicken Sie auf **Eigenschaften**.
3. Wählen Sie unter **Automatisierung** die Option **Vorlage exportieren** aus.

## <a name="metric-alert-rules-quota-too-small"></a>Kontingent für Metrikwarnungsregeln zu klein

Für die zulässige Anzahl von Metrikwarnungsregeln pro Abonnement gelten [Kontingentgrenzen](../service-limits.md).

Wenn Sie die Kontingentgrenze erreicht haben, helfen möglicherweise die folgenden Schritte bei der Problembehebung:
1. Löschen oder deaktivieren Sie Metrikwarnungsregeln, die nicht mehr verwendet werden.

2. Gehen Sie zur Verwendung von Metrikwarnungsregeln über, die mehrere Ressourcen überwachen. Diese Funktion ermöglicht das Überwachen mehrerer Ressourcen mit nur einer Warnungsregel, sodass nur diese eine auf das Kontingent angerechnet wird. Weitere Informationen zu dieser Funktion und den unterstützten Ressourcentypen finden Sie [hier](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Wenn die Kontingentgrenze erhöht werden muss, stellen Sie eine Supportanfrage mit den folgenden Informationen:

    - Abonnement-ID(s), für die die Kontingentgrenze erhöht werden muss
    - Ressourcentyp für die Erhöhung des Kontingents: **Metrikwarnungen** oder **Metrikwarnungen (klassisch)**
    - Angeforderte Kontingentgrenze

## <a name="check-total-number-of-metric-alert-rules"></a>Überprüfen der Gesamtanzahl von Metrikwarnungsregeln

Führen Sie die folgenden Schritte durch, um den derzeitigen Verbrauch durch Metrikwarnungsregeln zu überprüfen.

### <a name="from-the-azure-portal"></a>Über das Azure-Portal

1. Öffnen Sie den Bildschirm **Warnungen**, und klicken Sie auf **Warnungsregeln verwalten**.
2. Filtern Sie mit dem Dropdown-Steuerelement **Abonnement** nach dem betreffenden Abonnement.
3. Stellen Sie sicher, dass Sie NICHT nach einer bestimmten Ressourcengruppe, einem Ressourcentyp oder einer Ressource filtern.
4. Wählen Sie im Dropdown-Steuerelement **Signaltyp** die Option **Metriken** aus.
5. Vergewissern Sie sich, dass das Dropdown-Steuerelement **Status** auf **Aktiviert** festgelegt ist.
6. Die Gesamtanzahl von Metrikwarnungsregeln wird oberhalb der Liste mit Warnungsregeln angezeigt.

### <a name="from-api"></a>Über eine API

- PowerShell: [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST-API: [Auflisten nach Abonnement](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI: [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Verwalten von Warnungsregeln mithilfe von Resource Manager-Vorlagen, der REST-API, PowerShell oder der Azure CLI

Wenn beim Erstellen, Aktualisieren, Abrufen oder Löschen von Metrikwarnungen mithilfe von Resource Manager-Vorlagen, der REST-API, PowerShell oder der Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) Probleme auftreten, lassen sich diese unter Umständen mit den folgenden Schritten beheben.

### <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

- Sehen Sie sich die Liste mit den [häufigen Azure-Bereitstellungsfehlern](../../azure-resource-manager/templates/common-deployment-errors.md) an, und führen Sie die Problembehandlung entsprechend durch.
- Lesen Sie die [Beispiele zu Azure Resource Manager-Vorlagen für Metrikwarnungen](./alerts-metric-create-templates.md), um sicherzustellen, dass alle Parameter korrekt übergeben werden.

### <a name="rest-api"></a>REST-API

Überprüfen Sie den [Leitfaden zur REST-API](/rest/api/monitor/metricalerts/), um sicherzustellen, dass alle Parameter korrekt übergeben werden.

### <a name="powershell"></a>PowerShell

Stellen Sie sicher, dass Sie die richtigen PowerShell-Cmdlets für Metrikwarnungen verwenden:

- PowerShell-Cmdlets für Metrikwarnungen sind im [Az.Monitor-Modul](/powershell/module/az.monitor/) verfügbar.
- Stellen Sie sicher, dass Sie die auf „V2“ endenden Cmdlets für neue (nicht klassische) Metrikwarnungen verwenden (z. B. [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2)).

### <a name="azure-cli"></a>Azure CLI

Stellen Sie sicher, dass Sie die richtigen CLI-Befehle für Metrikwarnungen verwenden:

- CLI-Befehle für Metrikwarnungen beginnen mit `az monitor metrics alert`. Informationen zur Syntax finden Sie in der [Azure CLI-Referenz](/cli/azure/monitor/metrics/alert).
- Sie können sich das [Beispiel zur Nutzung der CLI für Metrikwarnungen](./alerts-metric.md#with-azure-cli) ansehen.
- Stellen Sie zum Verwenden von Warnungen für benutzerdefinierte Metriken sicher, dass Sie dem Metriknamen den relevanten Metriknamespace als Präfix voranstellen: NAMESPACE.METRIK

### <a name="general"></a>Allgemein

- Im Fall eines Fehlers vom Typ `Metric not found` haben Sie folgende Möglichkeiten:

   - Bei einer Plattformmetrik: Vergewissern Sie sich, dass Sie den Namen der **Metrik** von der [Azure Monitor-Seite mit unterstützten Metriken](../essentials/metrics-supported.md) und nicht den **Metrikanzeigenamen** verwenden.

   - Bei einer benutzerdefinierten Metrik: Vergewissern Sie sich, dass die Metrik bereits ausgegeben wird (Sie können keine Warnungsregel auf der Grundlage einer benutzerdefinierten Metrik erstellen, die noch nicht vorhanden ist). Stellen Sie zudem sicher, dass Sie den Namespace der benutzerdefinierten Metrik angeben (ein Beispiel für eine Resource Manager-Vorlage finden Sie [hier](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)).

- Stellen Sie bei der Erstellung von [Metrikwarnungen in Protokollen](./alerts-metric-logs.md) sicher, dass die entsprechenden Abhängigkeiten enthalten sind. Eine Beispielvorlage finden Sie [hier](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Wenn Sie eine Warnungsregel erstellen, die mehrere Kriterien enthält, beachten Sie die folgenden Einschränkungen:

   - Innerhalb jedes Kriteriums kann jeweils nur ein Wert pro Dimension ausgewählt werden.
   - „\*“ kann nicht als Dimensionswert verwendet werden.
   - Wenn Metriken, die in verschiedenen Kriterien konfiguriert sind, die gleiche Dimension unterstützen, muss auf die gleiche Weise explizit ein konfigurierter Dimensionswert für alle diese Metriken festgelegt werden (ein Beispiel für eine Resource Manager-Vorlage finden Sie [hier](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)).


## <a name="no-permissions-to-create-metric-alert-rules"></a>Keine Berechtigungen zum Erstellen von Metrikwarnungsregeln

Zum Erstellen einer Metrikwarnungsregel benötigen Sie die folgenden Berechtigungen:

- Leseberechtigung für die Zielressource der Warnungsregel
- Schreibberechtigung für die Ressourcengruppe, in der die Warnungsregel erstellt wird (wenn Sie die Warnungsregel über das Azure-Portal erstellen, wird die Warnungsregel standardmäßig in derselben Ressourcengruppe erstellt, in der sich die Zielressource befindet).
- Leseberechtigung für jede Aktionsgruppe, die der Warnungsregel zugeordnet ist (falls zutreffend)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Benennungseinschränkungen für Metrikwarnungsregeln

Beachten Sie die folgenden Einschränkungen für Namen von Metrikwarnungsregeln:

- Namen von Metrikwarnungsregeln können nach der Erstellung nicht geändert werden (keine Umbenennung möglich)
- Namen von Metrikwarnungsregeln müssen innerhalb einer Ressourcengruppe eindeutig sein
- Namen von Metrikwarnungsregeln dürfen die folgenden Zeichen nicht enthalten: * # & + : < > ? @ % { } \ / 
- Namen von Metrikwarnungsregeln dürfen nicht mit einem Leerzeichen oder einem Punkt enden.
- Der kombinierte Name der Ressourcengruppe und der Warnungsregel darf 252 Zeichen nicht überschreiten.

> [!NOTE] 
> Wenn der Name der Warnungsregel nicht alphabetische oder nicht numerische Zeichen enthält (also beispielsweise Leerzeichen, Satzzeichen oder Symbole), werden diese Zeichen beim Abrufen durch bestimmte Clients möglicherweise URL-codiert.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Einschränkungen bei der Verwendung von Dimensionen in einer Metrikwarnungsregel mit mehreren Bedingungen

Metrikwarnungen unterstützen das Warnen bei mehrdimensionalen Metriken und das Definieren mehrerer Bedingungen (bis zu fünf Bedingungen pro Warnungsregel).

Für das Verwenden von Dimensionen in einer Warnungsregel, in der mehrere Bedingungen enthalten sind, gelten die folgenden Einschränkungen:
- Innerhalb jeder Bedingung kann jeweils nur ein Wert pro Dimension ausgewählt werden.
- Sie können die Option „Alle aktuellen und zukünftigen Werte auswählen“ nicht verwenden (Select \*).
- Wenn Metriken, die in verschiedenen Bedingungen konfiguriert sind, dieselbe Dimension unterstützen, dann muss auf gleiche Weise ein konfigurierter Dimensionswert explizit für alle relevanten Bedingungen dieser Metriken festgelegt werden.
Beispiel:
    - Stellen Sie sich eine Metrikwarnungsregel vor, die für ein Speicherkonto definiert ist und zwei Bedingungen überwacht:
        * Gesamte **Transactions** > 5
        * Durchschnittliche **SuccessE2ELatency** > 250 ms
    - Ich möchte die erste Bedingung aktualisieren und nur Transaktionen überwachen, bei denen die Dimension **ApiName** *"GetBlob"* entspricht.
    - Da die Metriken **Transactions** und **SuccessE2ELatency** beide eine Dimension **ApiName** unterstützen, muss ich beide Bedingungen aktualisieren und dafür sorgen, dass beide die Dimension **ApiName** mit dem Wert *"GetBlob"* angeben.

## <a name="setting-the-alert-rules-period-and-frequency"></a>Festlegen von Zeitraum und Häufigkeit der Warnungsregel

Es wird empfohlen, eine *Aggregationsgranularität (Zeitraum)* auszuwählen, die größer ist als die *Häufigkeit der Auswertung*, um die Wahrscheinlichkeit zu verringern, dass die erste Auswertung einer hinzugefügten Zeitreihe in den folgenden Fällen ausgelassen wird:
-   Metrikwarnungsregel, die mehrere Dimensionen überwacht: wenn eine neue Kombination aus Dimensionswerten hinzugefügt wird
-   Metrikwarnungsregel, die mehrere Ressourcen überwacht: wenn dem Bereich eine neue Ressource hinzugefügt wird
-   Metrikwarnungsregel, die eine nicht kontinuierlich ausgegebene Metrik überwacht (seltene Metrik): wenn die Metrik erst nach einem längeren Zeitraum als 24 Stunden wieder ausgegeben wird

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Die Grenzwerte dynamischer Schwellenwerte scheinen nicht den Daten zu entsprechen.

Wenn sich das Verhalten einer Metrik kürzlich geändert hat, werden die Änderungen nicht zwangsläufig in den Grenzwerten dynamischer Schwellenwerte (obere und untere Grenzen) wiedergegeben, da diese auf der Grundlage der Metrikdaten der letzten 10 Tage berechnet werden. Achten Sie beim Anzeigen der Grenzwerte dynamischer Schwellenwerte für eine bestimmte Metrik darauf, den Metriktrend der letzten Woche zu verwenden und nicht nur den für die letzten Stunden oder Tage.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Warum wird die wöchentliche Saisonalität nicht durch dynamische Schwellenwerte erkannt?

Zum Identifizieren der wöchentlichen Saisonalität erfordert das Modell für dynamische Schwellenwerte Verlaufsdaten von mindestens drei Wochen. Sobald genügend Verlaufsdaten verfügbar sind, werden alle wöchentlichen Saisonalitäten ermittelt, die in den Metrikdaten enthalten sind, und das Modell wird entsprechend angepasst. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Bei dynamischen Schwellenwerten wird für eine Metrik eine negative untere Grenze angezeigt, obwohl die Metrik immer positive Werte aufweist.

Wenn eine Metrik große Schwankungen aufweist, wird durch dynamische Schwellenwerte ein breiteres Modell um die Metrikwerte herum erstellt. Dies kann dazu führen, dass die untere Grenze unter 0 (null) liegt. Diese Ausnahme kann insbesondere in den folgenden Fällen auftreten:
1. Die Empfindlichkeit ist auf „niedrig“ festgelegt. 
2. Die Medianwerte liegen nahe bei null.
3. Die Metrik weist ein irreguläres Verhalten mit hoher Varianz auf (es gibt Spitzen oder Abfälle in den Daten).

Wenn die untere Grenze einen negativen Wert aufweist, bedeutet dies, dass die Metrik aufgrund des unregelmäßigen Verhaltens der Metrik vermutlich einen Nullwert erreichen kann. Sie können erwägen, eine höhere Empfindlichkeit oder eine größere *Aggregationsgranularität (Zeitraum)* auszuwählen, um das Modell weniger sensibel zu machen. Sie können auch die Option *Vor dem folgenden Datum liegende Daten ignorieren* verwenden, um kürzliche Unregelmäßigkeiten aus den Verlaufsdaten für das Erstellen des Modells auszuschließen.

## <a name="the-dynamic-thresholds-alert-rule-is-too-noisy-fires-too-much"></a>Die Warnungsregel für dynamische Schwellwerte ist zu empfindlich (wird zu häufig ausgelöst).
Verwenden Sie eine der folgenden Optionen, um die Empfindlichkeit Ihrer Warnungsregel für dynamische Schwellwerte zu reduzieren:
1. Schwellwertempfindlichkeit: Legen Sie die Empfindlichkeit auf *Niedrig* fest, um bei Abweichungen toleranter zu sein.
2. Anzahl von Verstößen (unter *Erweiterte Einstellungen*): Konfigurieren Sie die Warnungsregel so, dass sie nur ausgelöst wird, wenn innerhalb eines bestimmten Zeitraums eine bestimmte Anzahl von Abweichungen auftritt. Dadurch wird die Regel weniger anfällig für vorübergehende Abweichungen.


## <a name="the-dynamic-thresholds-alert-rule-is-too-insensitive-doesnt-fire"></a>Die Warnungsregel für dynamische Schwellwerte ist zu unempfindlich (wird nicht ausgelöst).
Manchmal wird eine Warnungsregel auch dann nicht ausgelöst, wenn eine hohe Empfindlichkeit konfiguriert ist. Dies geschieht in der Regel, wenn die Verteilung der Metrik sehr unregelmäßig ist.
Erwägen Sie eine der folgenden Optionen:
* Wechseln Sie zur Überwachung einer ergänzenden Metrik, die für Ihr Szenario geeignet ist (falls zutreffend). Überprüfen Sie beispielsweise die Änderungen der Erfolgsrate statt Änderungen der Fehlerrate.
* Versuchen Sie, eine andere Aggregationsgranularität (Zeitraum) auszuwählen. 
* Überprüfen Sie, ob sich das Metrikverhalten in den letzten 10 Tagen drastisch geändert hat (ein Ausfall). Eine plötzliche Änderung kann sich auf die oberen und unteren Schwellenwerte auswirken, die für die Metrik berechnet werden, und sie weiter machen. Warten Sie einige Tage, bis der Ausfall nicht mehr in die Schwellenwertberechnung einbezogen wird, oder verwenden Sie die Option *Daten ignorieren vor* (unter *Erweiterte Einstellungen*).
* Wenn Ihre Daten wöchentlich saisonabhängig sind, aber nicht genügend Verlauf für die Metrik verfügbar ist, können die berechneten Schwellenwerte zu breiteren Ober- und Untergrenzen führen. Beispielsweise kann die Berechnung Wochentage und Wochenenden auf die gleiche Weise behandeln und breite Rahmen erstellen, die nicht immer den Daten entsprechen. Dieses Problem sollte sich selbst lösen, sobald genügend Metrikverlauf verfügbar ist. An diesem Punkt wird die richtige Saisonalität erkannt, und die berechneten Schwellenwerte werden entsprechend aktualisiert.


## <a name="next-steps"></a>Nächste Schritte

- Allgemeine Informationen zur Problembehandlung bei Warnungen und Benachrichtigungen finden Sie unter [Behandeln von Problemen bei Azure Monitor-Warnungen](alerts-troubleshoot.md).

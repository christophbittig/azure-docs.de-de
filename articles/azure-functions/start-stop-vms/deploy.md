---
title: Bereitstellen von „VMs starten/beenden v2 (Vorschau)“
description: In diesem Artikel erfahren Sie, wie Sie das Feature „VMs starten/beenden v2 (Vorschau)“ für Ihre Azure-VMs in Ihrem Azure-Abonnement bereitstellen.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 878bead652cf9962febc7622ef98ade0abc6cce5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399691"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Bereitstellen von „VMs starten/beenden v2 (Vorschau)“

Führen Sie die Schritte in diesem Thema nacheinander aus, um das Feature „VMs starten/beenden v2 (Vorschau)“ zu installieren. Nachdem Sie den Installationsprozess abgeschlossen haben, konfigurieren Sie die Zeitpläne, um sie an Ihre Anforderungen anzupassen.

## <a name="permissions-considerations"></a>Überlegungen zu Berechtigungen
Beachten Sie folgende Punkte vor und während der Bereitstellung:
+   Mit der Lösung können Personen mit entsprechenden RBAC-Berechtigungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung) für die Bereitstellung „VMs starten/beenden v2“ Zeitpläne für virtuelle Computer im Bereich von „VMs starten/beenden v2“ hinzufügen, entfernen und verwalten. Dieses Verhalten ist beabsichtigt. In der Praxis bedeutet dies, dass ein Benutzer ohne direkte RBAC-Berechtigung für einen virtuellen Computer weiterhin Vorgänge zum Starten, Beenden und automatischen Beenden auf diesem virtuellen Computer erstellen kann, wenn er über die RBAC-Berechtigung zum Ändern der Lösung „VMs starten/beenden v2“ verfügt, die ihn verwaltet.
+ Alle Benutzer mit Zugriff auf die Lösung „VMs starten/beenden v2“ können Kosten, Einsparungen, den Vorgangsverlauf und andere Daten aufdecken, die in der Application Insights-Instanz gespeichert sind, die von der Anwendung „VMs starten/beenden v2“ verwendet wird.
+ Beim Verwalten einer Lösung „VMs starten/beenden v2“ sollten Sie die Berechtigungen von Benutzern für die Lösung „VMs starten/beenden v2“ berücksichtigen, insbesondere wenn die Benutzer nicht über die Berechtigung zum direkten Ändern der virtuellen Zielcomputer verfügen.
## <a name="deploy-feature"></a>Bereitstellen des Features

Die Bereitstellung wird von der GitHub-Organisation „VMs v2“ [hier](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md) initiiert. Diese Funktion ist zwar für die Verwaltung aller Ihrer virtuellen Computer in Ihrem Abonnement in allen Ressourcengruppen aus einer einzelnen Bereitstellung innerhalb des Abonnements heraus gedacht, doch Sie können auch eine weitere Instanz davon auf Grundlage des Betriebsmodells oder der Anforderungen Ihrer Organisation installieren. Außerdem kann sie so konfiguriert werden, dass VMs aus mehreren Abonnements zentral verwaltet werden können.

Zur Vereinfachung der Verwaltung und Entfernung empfiehlt es sich, „VMs starten/beenden v2 (Vorschau)“ in einer dedizierten Ressourcengruppe bereitzustellen.

> [!NOTE]
> Derzeit unterstützt diese Vorschau nicht das Angeben eines vorhandenen Speicherkontos oder einer Application Insights-Ressource.


> [!NOTE]
> Das Benennungsformat für die Funktions-App und das Speicherkonto hat sich geändert. Um globale Eindeutigkeit zu gewährleisten, wird jetzt eine zufällige und eindeutige Zeichenfolge an die Namen dieser Ressource angefügt.

1. Öffnen Sie Ihren Browser, und navigieren Sie zur [GitHub-Organisation](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md) „VMs starten/beenden v2“.
1. Wählen Sie die Bereitstellungsoption basierend auf der Azure-Cloudumgebung aus, in der Ihre Azure-VMs erstellt werden. Dadurch wird die Azure Resource Manager-Seite für die benutzerdefinierte Bereitstellung im Azure-Portal geöffnet.
1. Wenn Sie dazu aufgefordert werden, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie die folgenden Werte ein:

    |Name |Wert |
    |-----|------|
    |Region |Wählen Sie eine Region in Ihrer Nähe für neue Ressourcen aus.|
    |Ressourcengruppenname |Geben Sie den Namen der Ressourcengruppe an, die die einzelnen Ressourcen für „VMs starten/beenden“ aufnehmen soll. |
    |Ressourcengruppenregion |Geben Sie die Region für die Ressourcengruppe an. Beispiel: **USA, Mitte**.|
    |Name der Azure-Funktions-App |Geben Sie einen Namen ein, der in einem URL-Pfad gültig ist. Der eingegebene Name wird überprüft, um sicherzustellen, dass er in Azure Functions eindeutig ist. |
    |Application Insights-Name |Geben Sie den Namen Ihrer Application Insights-Instanz an, die die Analysen für „VMs starten/beenden“ aufnehmen soll. |
    |Application Insights-Region |Geben Sie die Region für die Application Insights-Instanz an.|
    |Speicherkontoname |Geben Sie den Namen des Azure Storage-Kontos an, das die Ausführungstelemetrie von „VMs starten/beenden“ speichern soll. |
    |E-Mail-Adresse |Geben Sie eine oder mehrere E-Mail-Adressen für den Empfang von Statusbenachrichtigungen an, getrennt durch ein Komma (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Konfiguration der Vorlagenbereitstellung für „VM starten/beenden“.":::

1. Wählen Sie am unteren Rand der Seite **Überprüfen + erstellen** aus.
1. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.
1. Klicken Sie am oberen Bildschirmrand auf das Glockensymbol (Benachrichtigungen), um den Bereitstellungsstatus anzuzeigen. Dieser sollte **Die Bereitstellung wird ausgeführt...** lauten. Warten Sie, bis die Bereitstellung abgeschlossen wurde.
1. Wählen Sie im Benachrichtigungsbereich die Option **Zu Ressourcengruppe wechseln** aus. Ein Bildschirm wird angezeigt, der etwa wie folgt aussieht:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Ressourcenliste der Vorlagenbereitstellung von „VMs starten/beenden“.":::

> [!NOTE]
> Wir erfassen Vorgangs- und Heartbeattelemetriedaten, um Sie bei der Problembehandlung besser unterstützen zu können, wenn Sie sich an das Supportteam wenden. Wir erfassen auch den Ereignisverlauf virtueller Computer, um zu überprüfen, wann der Dienst auf einem virtuellen Computer agiert hat und wie lange für einen virtuellen Computer der Standbymodus aktiviert war, um die Effizienz des Diensts zu bestimmen.

## <a name="enable-multiple-subscriptions"></a>Aktivieren mehrerer Abonnements

Führen Sie nach Abschluss der Bereitstellung von „VMs starten/beenden“ die folgenden Schritte aus, um „VMs starten/beenden v2 (Vorschau)“ zu aktivieren und Aktionen für mehrere Abonnements durchzuführen.

1. Kopieren Sie den Wert des Namens der Azure-Funktions-App, den Sie während der Bereitstellung angegeben haben.

1. Navigieren Sie im Portal zu Ihrem sekundären Abonnement. Wählen Sie das Abonnement und dann **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie in der Dropdownliste **Rolle** die Rolle **Mitwirkender** aus.

1. Geben Sie im Feld **Auswählen** den Namen der Azure-Funktions-App ein. Wählen Sie in den Ergebnissen den Funktionsnamen aus.

1. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

## <a name="configure-schedules-overview"></a>Konfigurieren von Zeitplänen: Übersicht

Zum Verwalten der Automatisierungsmethode, um das Starten und Beenden Ihrer virtuellen Computer zu steuern, konfigurieren Sie eine oder mehrere der enthaltenen Logik-Apps, basierend auf Ihren Anforderungen.

- Geplant: Aktionen zum Starten und Beenden basieren auf einem Zeitplan, den Sie für Azure Resource Manager und klassische VMs angeben. **ststv2_vms_Scheduled_start** und **ststv2_vms_Scheduled_stop** konfigurieren das geplante Starten und Beenden.

- Sequenziert: Aktionen zum Starten und Beenden basieren auf einem Zeitplan für VMs mit vordefinierten Sequenzierungstags. Es werden nur zwei benannte Tags unterstützt: **sequencestart** und **sequencestop**. **ststv2_vms_Sequenced_start** und **ststv2_vms_Sequenced_stop** konfigurieren das sequenzierte Starten und Beenden.

    > [!NOTE]
    > In diesem Szenario werden nur Azure Resource Manager-VMs unterstützt.

- AutoStop: Diese Funktion wird nur zum Ausführen einer Aktion zum Beenden sowohl für Azure Resource Manager als auch für klassische VMs verwendet, basierend auf der CPU-Auslastung. Dabei kann es sich auch um eine geplante *Aktion* handeln, die Warnungen auf virtuellen Computern erstellt, basierend auf der Bedingung, dass die Warnung ausgelöst wird, um die Aktion zum Beenden auszuführen. **ststv2_vms_AutoStop** konfiguriert die Funktion zum automatischen Beenden (AutoStop).

Wenn Sie zusätzliche Zeitpläne benötigen, können Sie eine der bereitgestellten Logic-Apps mit der Option **Klonen** im Azure-Portal duplizieren.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Auswählen der Option „Klonen“ zum Duplizieren einer Logik-App.":::

## <a name="scheduled-start-and-stop-scenario"></a>Szenario mit geplantem Starten und Beenden

Führen Sie die folgenden Schritte aus, um die geplante Aktion zum Starten und Beenden für Azure Resource Manager und klassische VMs zu konfigurieren. Beispielsweise können Sie den Zeitplan **ststv2_vms_Scheduled_start** so konfigurieren, dass die VMs am Morgen gestartet werden, wenn Sie sich im Büro befinden, und dass alle VMs in einem Abonnement beendet werden, wenn Sie am Abend nach Hause gehen, basierend auf dem Zeitplan **ststv2_vms_Scheduled_stop**.

Das Konfigurieren der Logik-App zum reinen Starten der virtuellen Computer wird unterstützt.

Bei jedem Szenario können Sie als Ziel der Aktion ein oder mehrere Abonnements wählen, eine einzelne oder mehrere Ressourcengruppen, und Sie können eine oder mehrere VMs in einer Einschluss- oder Ausschlussliste angeben. Sie können Sie nicht in derselben Logik-App zusammen angeben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Logic Apps**.

1. Wählen Sie in der Liste der Logik-Apps die Option **ststv2_vms_Scheduled_start** aus, um das geplante Starten zu konfigurieren. Um das geplante Beenden zu planen, wählen Sie **ststv2_vms_Scheduled_stop** aus.

1. Wählen Sie im linken Bereich **Logik-App-Designer** aus.

1. Nachdem der Logik-App-Designer angezeigt wird, wählen Sie im Designerbereich **Wiederholung** aus, um den Logik-App-Zeitplan zu konfigurieren. Weitere Informationen zu den spezifischen Wiederholungsoptionen finden Sie unter [Wiederkehrende Aufgabe planen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurieren der Wiederholungshäufigkeit für die Logik-App.":::

1. Wählen Sie im Designerbereich **Function-Try** aus, um die Zieleinstellungen zu konfigurieren. Wenn Sie virtuelle Computer in allen Ressourcengruppen im Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Geben Sie mehrere Abonnements im `subscriptions`-Array an, wobei jeder Wert durch ein Komma getrennt ist, wie im folgenden Beispiel gezeigt.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Wenn Sie virtuelle Computer für bestimmte Ressourcengruppen verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf eine Ressourcengruppe oder mehr angeben.

    In diesem Beispiel wird auch das Ausschließen eines virtuellen Computers veranschaulicht. Sie können den virtuellen Computer ausschließen, indem Sie den Ressourcenpfad der VM angeben oder mit einem Platzhalter.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Im vorliegenden Fall wird die Aktion für alle VMs ausgeführt, außer wenn der VM-Name mit Az und Bz in beiden Abonnements beginnt.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Wenn Sie eine bestimmte Gruppe virtueller Computer im Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf einen virtuellen Computer angeben.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
      }
    }
    ```

1. Klicken Sie im Übersichtsbereich für die Logik-App auf **Aktivieren**.  

## <a name="sequenced-start-and-stop-scenario"></a>Szenario mit sequenziertem Starten und Beenden

In einer Umgebung mit mehreren Komponenten auf mehreren Azure Resource Manager-VMs in einer verteilten Anwendungsarchitektur spielt die Reihenfolge, in der Komponenten nacheinander gestartet und beendet werden, eine wichtige Rolle. Stellen Sie sicher, dass Sie die Tags **Sequenzestart** und **Sequenzstopp** wie auf der [Seite Übersicht](../../azure-functions/start-stop-vms/overview.md#overview) beschrieben auf die Ziel-VMs angewendet haben, bevor Sie dieses Szenario konfigurieren.

1. Wählen Sie in der Liste der Logik-Apps die Option **ststv2_vms_Sequenced_start** aus, um das sequenzierte Starten zu konfigurieren. Um das sequenzierte Beenden zu konfigurieren, wählen Sie **ststv2_vms_Sequenced_stop** aus.

1. Wählen Sie im linken Bereich **Logik-App-Designer** aus.

1. Nachdem der Logik-App-Designer angezeigt wird, wählen Sie im Designerbereich **Wiederholung** aus, um den Logik-App-Zeitplan zu konfigurieren. Weitere Informationen zu den spezifischen Wiederholungsoptionen finden Sie unter [Wiederkehrende Aufgabe planen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurieren der Wiederholungshäufigkeit für die Logik-App.":::

1. Wählen Sie im Designerbereich **Function-Try** aus, um die Zieleinstellungen zu konfigurieren. Wenn Sie virtuelle Computer in allen Ressourcengruppen im Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Geben Sie mehrere Abonnements im `subscriptions`-Array an, wobei jeder Wert durch ein Komma getrennt ist, wie im folgenden Beispiel gezeigt.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Wenn Sie virtuelle Computer für bestimmte Ressourcengruppen verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf eine Ressourcengruppe angeben.

    Dieses Beispiel veranschaulicht außerdem das Ausschließen eines virtuellen Computers anhand seines Ressourcenpfads im Vergleich zu dem Beispiel für geplantes Starten/Beenden, bei dem Platzhalter verwendet wurden.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Wenn Sie eine bestimmte Gruppe virtueller Computer in einem Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf einen virtuellen Computer angeben.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Szenario mit automatischem Beenden (AutoStop)

Das Feature „VMs starten/beenden v2 (Vorschau)“ kann einen Beitrag zur Verwaltung der Kosten für die Ausführung von Azure Resource Manager- und klassischen virtuellen Computern in Ihrem Abonnement leisten, indem VMs, die außerhalb der Spitzenzeiten (z. B. nach Geschäftsschluss) nicht verwendet werden, ausgewertet und automatisch beendet werden, wenn die Prozessorauslastung unter einem angegebenen Prozentsatz liegt.

Die folgenden Eigenschaften von Metrikwarnungen im Anforderungstext unterstützen eine Anpassung:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Weitere Informationen zur Funktionsweise von Azure Monitor-Metrikwarnungen und deren Konfiguration finden Sie unter [Metrikwarnungen in Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Wählen Sie in der Liste der Logik-Apps **ststv2_vms_AutoStop** aus, um automatisches Beenden (AutoStop) zu konfigurieren.

1. Wählen Sie im linken Bereich **Logik-App-Designer** aus.

1. Nachdem der Logik-App-Designer angezeigt wird, wählen Sie im Designerbereich **Wiederholung** aus, um den Logik-App-Zeitplan zu konfigurieren. Weitere Informationen zu den spezifischen Wiederholungsoptionen finden Sie unter [Wiederkehrende Aufgabe planen](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurieren der Wiederholungshäufigkeit für die Logik-App.":::

1. Wählen Sie im Designerbereich **Function-Try** aus, um die Zieleinstellungen zu konfigurieren. Wenn Sie virtuelle Computer in allen Ressourcengruppen im Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Wenn Sie virtuelle Computer für bestimmte Ressourcengruppen verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf eine Ressourcengruppe angeben.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Wenn Sie eine bestimmte Gruppe virtueller Computer im Abonnement verwalten möchten, ändern Sie den Anforderungstext wie im folgenden Beispiel gezeigt. Jeder angegebene Ressourcenpfad muss durch ein Komma getrennt werden. Sie können bei Bedarf einen virtuellen Computer angeben.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Informationen, wie Sie den Status Ihrer virtuellen Azure-Computer überwachen, die über das Feature „VMs starten/beenden v2 (Vorschau)“ verwaltet werden, und andere Verwaltungsaufgaben ausführen, finden Sie im Artikel [Verwalten von „VMs starten/beenden“](manage.md).

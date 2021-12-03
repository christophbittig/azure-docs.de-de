---
title: 'VMs starten/beenden v2 (Vorschau): Übersicht'
description: In diesem Artikel wird Version 2 des Features „VMs starten/beenden(Vorschau)“ beschrieben, das Azure Resource Manager-VMs und klassische VMs nach einem Zeitplan startet oder beendet.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.openlocfilehash: 7edd651e5c6a52f707cfebd87310e0a104fef0d4
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401205"
---
# <a name="startstop-vms-v2-preview-overview"></a>VMs starten/beenden v2 (Vorschau): Übersicht

Mit dem Feature „VMs starten/beenden v2 (Vorschau)“ werden Azure-VMs mehrere Abonnements übergreifend gestartet oder beendet. Damit können Sie Azure-VMs nach benutzerdefinierten Zeitplänen starten und beenden, über [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) Erkenntnisse gewinnen und mithilfe von [Aktionsgruppen](../../azure-monitor/alerts/action-groups.md) optional Benachrichtigungen senden. Das Feature kann in den meisten Szenarien sowohl auf Azure Resource Manager-VMs als auch klassischen VMs aktiviert werden.

Diese neue Version von „VMs starten/beenden v2 (Vorschau)“ ist eine dezentralisierte kostengünstige Automatisierungsoption für Kunden, die Ihre VM-Kosten optimieren möchten. Sie bietet die gleiche Funktionalität wie die mit Azure Automation verfügbare [ursprüngliche Version](../../automation/automation-solution-vm-management.md), ist aber darauf ausgelegt, neuere Technologie in Azure zu nutzen.

## <a name="overview"></a>Übersicht

„VMs starten/beenden v2 (Vorschau)“ wurde neu gestaltet und ist nicht wie die [vorherige Version](../../automation/automation-solution-vm-management.md) von Azure Automation oder Azure Monitor-Protokollen abhängig. Die Ausführung des Startens und Beendens des virtuellen Computers basiert bei dieser Version auf [Azure Functions](../../azure-functions/functions-overview.md).

In Azure Active Directory (Azure AD) wird für diese Azure Functions-Anwendung eine verwaltete Identität erstellt, die „VMs starten/beenden v2 (Vorschau)“ den mühelosen Zugriff auf andere geschützte Azure AD-Ressourcen wie die Logik-Apps und Azure-VMs ermöglicht. Weitere Informationen zu verwalteten Identitäten in Azure AD finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md).

Eine HTTP-Triggerendpunkt-Funktion wird erstellt, um die im Feature enthaltenen Zeitplan- und Sequenzszenarios zu unterstützen, wie in der folgenden Tabelle dargestellt.

|Name |Trigger |BESCHREIBUNG |
|-----|--------|------------|
|Geplant |HTTP |Diese Funktion ist sowohl für ein geplantes als auch sequenziertes Szenario (Unterscheidung nach dem Nutzlastschema) vorgesehen. Dabei handelt es sich um die von der Logik-App aufgerufene Einstiegspunktfunktion, der die Nutzlast zum Verarbeiten des Startens oder Beendens der VM übergeben wird. |
|AutoStop |HTTP |Diese Funktion unterstützt das **AutoStop**-Szenario, das die von der Logik-App aufgerufene Einstiegspunktfunktion ist.|
|AutoStopVM |HTTP |Diese Funktion wird automatisch von der VM-Warnung ausgelöst, wenn die Warnungsbedingung erfüllt ist.|
|VirtualMachineRequestOrchestrator |Warteschlange |Diese Funktion ruft die Nutzlastinformationen aus der **Scheduled**-Funktion ab und orchestriert die Anforderungen zum Starten und Beenden des virtuellen Computers.|
|VirtualMachineRequestExecutor |Warteschlange |Diese Funktion führt den eigentlichen Start- und Beendevorgang auf dem virtuellen Computer aus.|
|CreateAutoStopAlertExecutor |Warteschlange |Diese Funktion ruft die Nutzlastinformationen aus der Funktion **AutoStop** ab, um die Warnung auf dem virtuellen Computer zu erstellen.|
|HeartBeatAvailabilityTest |Timer |Diese Funktion überwacht die Verfügbarkeit der primären HTTP-Funktionen.|
|CostAnalyticsFunction |Timer |Diese Funktion berechnet die monatlichen Kosten für die Ausführung der Lösung zum Starten/Beenden V2.|
|SavingsAnalyticsFunction |Timer |Diese Funktion berechnet die monatliche Gesamtersparnis, die mit der Lösung zum Starten/Beenden V2 erzielt werden.|
|VirtualMachineSavingsFunction |Warteschlange |Diese Funktion führt die Berechnung der tatsächlichen Einsparungen auf einem virtuellen Computer durch, die mit der Lösung zum Starten/Beenden V2 erzielt werden.|

Beispielsweise wird die **Scheduled**-HTTP-Triggerfunktion verwendet, um Plan- und Sequenzszenarios zu behandeln. Entsprechend behandelt die **AutoStop**-HTTP-Triggerfunktion das Szenario für automatische Beendigung.

Die Triggerfunktionen auf Warteschlangenbasis sind zur Unterstützung dieser Funktion erforderlich. Alle Trigger auf Timerbasis werden zum Durchführen des Verfügbarkeitstests und zum Überwachen der Integrität des Systems verwendet.

 Mit [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) werden die Zeitpläne für das Starten und Beenden der VM konfiguriert und verwaltet, indem die Funktion mithilfe einer JSON-Nutzlast aufgerufen wird. Standardmäßig werden während der ersten Bereitstellung insgesamt fünf Logik-Apps für die folgenden Szenarios erstellt:

- **Geplant**: Aktionen zum Starten und Beenden basieren auf einem Zeitplan, den Sie für Azure Resource Manager-VMs und klassische VMs angeben. **ststv2_vms_Scheduled_start** und **ststv2_vms_Scheduled_stop** konfigurieren das geplante Starten und Beenden.

- **Sequenziert**: Aktionen zum Starten und Beenden basieren auf einem Zeitplan für VMs mit vordefinierten Sequenzierungstags. Es werden nur zwei benannte Tags unterstützt: **sequencestart** und **sequencestop**. **ststv2_vms_Sequenced_start** und **ststv2_vms_Sequenced_stop** konfigurieren das sequenzierte Starten und Beenden. 

    Die richtige Methode zum Verwenden der Sequenzfunktionalität besteht darin, ein Tag namens **sequencestart** auf jeder VM zu erstellen, die Sie in einer Sequenz starten möchten. Der Tagwert muss eine ganze Zahl zwischen 1 und N für jede VM im jeweiligen Bereich sein. Das Tag ist optional. Wenn es nicht vorhanden ist, wird die entsprechende VM bei der Sequenzierung nicht berücksichtigt. Dieselben Kriterien gelten für das Beenden von VMs, nur der Tagname lautet in diesem Fall **sequencestop**. Sie müssen beide Tags auf jeder VM konfigurieren, um die Aktion zum Starten und Beenden zu erhalten. Wenn zwei oder mehr VMs denselben Tagwert gemeinsam nutzen, werden diese VMs gleichzeitig gestartet oder beendet.

    Die folgende Tabelle zeigt beispielsweise, dass Starten- und Beenden-Aktionen in aufsteigender Reihenfolge nach dem Wert des Tags verarbeitet werden.

    :::image type="content" source="media/overview/sequence-settings-table.png" alt-text="Tabelle mit Tagbeispielen für Sequenzeinstellungen":::

    > [!NOTE]
    > In diesem Szenario werden nur Azure Resource Manager-VMs unterstützt.

- **AutoStop**: Diese Funktion wird nur verwendet, um sowohl für Azure Resource Manager-VMs als auch klassische VMs basierend auf der CPU-Auslastung eine Aktion zum Beenden auszuführen. Dabei kann es sich auch um eine geplante *Aktion* handeln, die Warnungen auf virtuellen Computern erstellt, und basierend auf der Bedingung wird die Warnung ausgelöst, um die Aktion zum Beenden auszuführen. **ststv2_vms_AutoStop** konfiguriert die AutoStop-Funktion.

Jede Aktion zum Starten/Beenden unterstützt die Zuweisung von einem oder mehreren Abonnements, Ressourcengruppen oder einer Liste von VMs.

Ein von Functions benötigtes Azure Storage-Konto wird auch von „VMs starten/beenden v2 (Vorschau)“ aus zwei Gründen verwendet:

   - Azure Table Storage wird zum Speichern der Metadaten des Ausführungsvorgangs (d. h. der Aktion zum Starten/Beenden der VM) verwendet.

   - Azure Queue Storage wird zur Unterstützung der auf Warteschlangen basierenden Azure Functions-Trigger verwendet.

Alle Telemetriedaten, d. h. Ablaufverfolgungsprotokolle der Funktions-App-Ausführung, werden an Ihre verbundene Application Insights-Instanz gesendet. Sie können die in Application Insights gespeicherten Telemetriedaten mithilfe einer Reihe vordefinierter Visualisierungen in einem freigegebenen [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md) anzeigen.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Freigegebenes Statusdashboard für „VMs starten/beenden“":::

E-Mail-Benachrichtigungen werden auch als Ergebnis von Aktionen gesendet, die auf den VMs ausgeführt werden.

## <a name="new-releases"></a>Neue Versionen

Wenn eine neue Version von „VMs starten/beenden v2 (Vorschau)“ veröffentlicht wird, wird Ihre Instanz automatisch aktualisiert, ohne dass eine manuelle erneute Bereitstellung erforderlich ist.

## <a name="supported-scoping-options"></a>Unterstützte Optionen zur Eingrenzung

### <a name="subscription"></a>Subscription

Die Eingrenzung auf ein Abonnement können Sie verwenden, wenn Sie die Aktion zum Starten und Beenden für alle VMs in einem gesamten Abonnement ausführen müssen, und ggf. können Sie mehrere Abonnements auswählen.  

Sie können auch eine Liste auszuschließender VMs angeben,  die dann von der Aktion ignoriert werden. Außerdem können Sie Platzhalterzeichen verwenden, um alle Namen anzugeben, die gleichzeitig ignoriert werden können.

### <a name="resource-group"></a>Resource group

Die Eingrenzung auf eine Ressourcengruppe können Sie verwenden, wenn Sie die Aktion zum Starten und Beenden für alle VMs in einer oder mehreren Ressourcengruppen durchführen müssen. Geben Sie hierfür einen oder mehrere Ressourcengruppennamen in einem Abonnement oder mehrere Abonnements übergreifend an.

Sie können auch eine Liste auszuschließender VMs angeben,  die dann von der Aktion ignoriert werden. Außerdem können Sie Platzhalterzeichen verwenden, um alle Namen anzugeben, die gleichzeitig ignoriert werden können.

### <a name="vmlist"></a>VMList

Sie können eine Liste mit VMs angeben, wenn Sie die Aktion zum Starten und Beenden für eine bestimmte Gruppe von virtuellen Computern und mehrere Abonnements übergreifend ausführen müssen. Diese Option unterstützt nicht die Angabe einer Liste auszuschließender VMs.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

- Ihrem Konto wurde die Berechtigung [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) für das Abonnement gewährt.

- „VMs starten/beenden v2 (Vorschau)“ ist in allen globalen Azure- und US Government-Cloudregionen verfügbar, die auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) für Azure Functions aufgelistet sind.

## <a name="next-steps"></a>Nächste Schritte

Informationen, wie Sie dieses Feature erneut bereitstellen, finden Sie unter [Bereitstellen von „VMs starten/beenden v2 (Vorschau)“](deploy.md).

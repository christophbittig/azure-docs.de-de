---
title: Verwalten von „VMs starten/beenden v2 (Vorschau)“
description: In diesem Artikel wird beschrieben, wie Sie den Status Ihrer virtuellen Azure-Computer überwachen, die über das Feature „VMs starten/beenden v2 (Vorschau)“ verwaltet werden, und andere Verwaltungsaufgaben ausführen.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 40c3d2dba3d41c7651846d09d01dd7afdce15af9
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967059"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Verwalten von „VMs starten/beenden v2 (Vorschau)“

## <a name="azure-dashboard"></a>Azure-Dashboard

„VMs starten/beenden v2 (Vorschau)“ enthält ein [Dashboard](../../azure-monitor/visualizations.md#azure-dashboards), mit dem Sie den Verwaltungsbereich und die aktuellen Vorgänge mit Ihren VMs besser verstehen können. Es ist eine schnelle und einfache Möglichkeit, den Status jedes Vorgangs zu überprüfen, der auf Ihren Azure-VMs ausgeführt wird. Die Visualisierung in jeder Kachel basiert auf einer Protokollabfrage. Um die Abfrage anzuzeigen, wählen Sie die Option **Auf Blatt „Protokolle“ öffnen** in der rechten Ecke der Kachel aus. Dadurch wird das [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics)-Tool im Azure-Portal geöffnet, von wo aus Sie die Abfrage auswerten und so ändern können, dass sie Ihre Anforderungen erfüllt, wie z. B. benutzerdefinierte [Protokollwarnungen](../../azure-monitor/alerts/alerts-log.md), eine benutzerdefinierte [Arbeitsmappe](../../azure-monitor/visualize/workbooks-overview.md) usw.

Die Protokolldaten, die auf jeder Kachel im Dashboard angezeigt werden, werden stündlich aktualisiert, können mit einer Option für die manuelle Aktualisierung bei Bedarf aktualisiert werden (durch Klicken auf das Symbol **Aktualisieren** für eine bestimmte Visualisierung) oder werden durch eine Aktualisierung des gesamten Dashboards aktualisiert.

Weitere Informationen zum Arbeiten mit einem protokollbasierten Dashboard finden Sie im folgenden [Tutorial](../../azure-monitor/visualize/tutorial-logs-dashboards.md).

> [!NOTE]
> Wenn Probleme während der Bereitstellung bzw. Verwendung von „VMs starten/beenden v2 (Vorschau)“ auftreten oder Sie eine Frage hierzu haben, können Sie ein Issue unter [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) senden. Das Erstellen eines Azure-Supportvorfalls über die [Azure-Supportwebsite](https://azure.microsoft.com/support/options/) ist für diese Vorschauversion nicht verfügbar. 

## <a name="configure-email-notifications"></a>Konfigurieren von E-Mail-Benachrichtigungen

Um E-Mail-Benachrichtigungen nach dem Bereitstellen von „VMs starten/beenden v2 (Vorschau)“ zu ändern, bearbeiten Sie die Aktionsgruppe, die während der Bereitstellung erstellt wurde.

1. Navigieren Sie im Azure-Portal zu **Überwachung** > **Benachrichtigungen**. Wählen Sie **Aktionen verwalten** aus.

1. Wählen Sie auf der Seite **Aktionen verwalten** die Aktionsgruppe mit dem Namen **StartStopV2_VM_Notification** aus.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Screenshot der Seite „Aktionsgruppen“.":::

1. Auf der Seite **StartStopV2_VM_Notification** können Sie die Optionen für Benachrichtigungen per E-Mail/SMS-/Push/Sprache ändern. Fügen Sie dieser Aktionsgruppe weitere Aktionen hinzu, oder aktualisieren Sie Ihre vorhandene Konfiguration, und klicken Sie dann auf **OK**, um Ihre Änderungen zu speichern.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Screenshot der Seite „E-Mail/SMS/Push/Sprache“ mit aktualisierter Beispiel-E-Mail-Adresse":::

    Weitere Informationen zu Aktionsgruppen finden Sie unter [Aktionsgruppen](../../azure-monitor/alerts/action-groups.md).

Der folgende Screenshot zeigt eine Beispiel-E-Mail, die gesendet wird, wenn das Feature virtuelle Computer herunterfährt.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Screenshot einer Beispiel-E-Mail, die gesendet wird, wenn das Feature virtuelle Computer herunterfährt":::

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Behandeln von Problemen bei der VM-Verwaltung finden Sie unter [Behandeln von Problemen mit „VMs starten/beenden v2 (Vorschau)“](troubleshoot.md).
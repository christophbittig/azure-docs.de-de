---
title: Migrieren von Agents einer Vorgängerversion zum neuen Azure Monitor-Agent
description: Leitfaden für die Migration von vorhandenen Agents einer Vorgängerversion zum neuen Azure Monitor-Agent (AMA) und den Datensammlungsregeln (Data Collection Rules, DCR).
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 01c2f7b49b051e9166ad1ceef99fc816e611f579
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356554"
---
# <a name="migrate-from-log-analytics-agents"></a>Migrieren von Log Analytics-Agents
Dieser Artikel enthält einen allgemeinen Leitfaden dazu, wann und wie Sie zum neuen Azure Monitor-Agent (AMA) und den Datensammlungsregeln (Data Collection Rules, DCR) migrieren. Dieser Artikel wird aktualisiert, wenn neue Migrationstools verfügbar sind.


## <a name="review"></a>Überprüfung
- Hilfe zur Entscheidung, ob Sie jetzt oder später zum neuen Azure Monitor-Agent migrieren sollten, finden Sie im Leitfaden unter [Übersicht über den Azure Monitor-Agent](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent).
- Informationen zu den neuen Funktionen des Azure Monitor-Agent, zur Verfügbarkeit vorhandener Funktionen, Dienste und Lösungen und zu aktuellen Einschränkungen finden Sie in der [Übersicht über den Azure Monitor-Agent](./agents-overview.md#azure-monitor-agent).


## <a name="test-migration-by-using-the-azure-portal"></a>Testen der Migration mithilfe des Azure-Portals
1. Um während der Migration eine sichere Bereitstellung zu gewährleisten, beginnen Sie mit dem Testen einiger Ressourcen in Ihrer Nichtproduktionsumgebung, die den vorhandenen Log Analytics-Agent ausführen. Nachdem Sie die für diese Testressourcen gesammelten Daten überprüft haben, können Sie den Rollout in der Produktion durchführen, indem Sie die gleichen Schritte ausführen.
1. Wechseln Sie zu **Monitor** > **Einstellungen** > **Datensammlungsregeln**, und [erstellen Sie neue Datensammlungsregeln](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal), um mit dem Sammeln einiger der vorhandenen Datentypen zu beginnen. Wenn Sie die grafische Benutzeroberfläche im Portal verwenden, werden die folgenden Schritte für alle Zielressourcen ausgeführt:
    - Aktivieren einer systemseitig zugewiesenen verwalteten Identität
    - Installieren der Azure Monitor-Agent-Erweiterung
    - Erstellen und Bereitstellen von Zuordnungen für Datensammlungsregeln
1. Überprüfen Sie, ob die Daten wie erwartet über den Azure Monitor-Agent übertragen werden. Überprüfen Sie die Tabelle **Heartbeat** auf neue Agent-Versionswerte. Stellen Sie sicher, dass sie den Daten entspricht, die auch über den vorhandenen Log Analytics-Agent geleitet werden.


## <a name="at-scale-migration-by-using-policies"></a>Migration im großen Stil mithilfe von Richtlinien
1. Beginnen Sie mit der Analyse Ihres aktuellen Überwachungssetups mit MMA/OMS, und wenden Sie dabei die folgenden Kriterien an:
    - Quellen, z. B. VMs, VM-Skalierungsgruppen und lokale Server
    - Datenquellen, z. B. Leistungsindikatoren, Windows-Ereignisprotokolle und Syslog
    - Ziele, z. B. Log Analytics-Arbeitsbereiche
1. [Erstellen Sie neue Datensammlungsregeln](/rest/api/monitor/datacollectionrules/create#examples) mithilfe der vorhandenen Konfiguration. Es hat sich bewährt, separate Datensammlungsregeln für Windows- und Linux-Quellen zu verwenden. Sie können auch separate Datensammlungsregeln für einzelne Teams mit unterschiedlichen Anforderungen an die Datensammlung einrichten.
1. [Aktivieren Sie eine systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity) auf einer Zielressource.
1. Installieren Sie die Azure Monitor-Agent-Erweiterung. Stellen Sie Zuordnungen der Datensammlungsregeln für alle Zielressourcen mithilfe der [integrierten Richtlinieninitiative](../deploy-scale.md#built-in-policy-initiatives) bereit. Geben Sie die obige Datensammlungsregel als Eingabeparameter an. 
1. Überprüfen Sie, ob die Daten wie erwartet über den Azure Monitor-Agent übertragen werden. Überprüfen Sie die Tabelle **Heartbeat** auf neue Agent-Versionswerte. Stellen Sie sicher, dass sie den Daten entspricht, die auch über den vorhandenen Log Analytics-Agent geleitet werden.
1. Überprüfen Sie alle Downstreamabhängigkeiten wie Dashboards, Warnungen und Runbook Worker. Arbeitsmappen funktionieren jetzt weiterhin, aber mit den Daten vom neuen Agent.
1. [Deinstallieren Sie den Log Analytics-Agent](./agent-manage.md#uninstall-agent) von den Ressourcen. Deinstallieren Sie ihn aber nicht, wenn Sie ihn für System Center Operations Manager-Szenarien oder andere Lösungen benötigen, die über den Azure Monitor-Agent noch nicht verfügbar sind.
1. Bereinigen Sie alle Konfigurationsdateien, Arbeitsbereichsschlüssel und Zertifikate, die zuvor vom Log Analytics-Agent verwendet wurden.



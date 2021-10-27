---
title: Best Practices für Azure Monitor – Warnungen und automatisierte Aktionen
description: Empfehlungen für die Bereitstellung von Azure Monitor-Warnungen und automatisierten Aktionen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 933c05bc48fedf9502d63acd5f5dc3fa1f4c133f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181645"
---
# <a name="deploying-azure-monitor---alerts-and-automated-actions"></a>Bereitstellen von Azure Monitor – Warnungen und automatisierte Aktionen
Dieser Artikel ist Teil des Szenarios [Empfehlungen zum Konfigurieren von Azure Monitor](best-practices.md). Es enthält Hinweise zu Warnungen in Azure Monitor, die Sie proaktiv über wichtige Daten oder Muster in Ihren Überwachungsdaten informieren. Sie können Warnungen im Azure-Portal anzeigen, eine proaktive Benachrichtigung senden oder eine automatisierte Aktion zur Behebung des Problems einleiten. 
## <a name="alerting-strategy"></a>Warnstrategie
Eine Warnungsstrategie definiert Ihre Organisationsstandards für die Typen von Warnungsregeln, die Sie für verschiedene Szenarien erstellen, für die Kategorisierung und Verwaltung von Warnungen nach deren Erstellung sowie für automatisierte Aktionen und Benachrichtigungen, die Sie als Reaktion auf Warnungen ausführen. Das Definieren einer Warnungsstrategie unterstützt Sie beim Definieren der Konfiguration von Warnungsregeln, einschließlich des Schweregrads von Warnungen und Aktionsgruppen.

Unter [Wirkungsvolle Warnungsstrategie](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy) finden Sie Faktoren, die Sie bei der Entwicklung einer Warnungsstrategie berücksichtigen sollten.


## <a name="alert-rule-types"></a>Warnungsregeltypen
Warnungen in Azure Monitor werden über Warnungsregeln erzeugt, die Sie erstellen müssen. Anleitungen zu empfohlenen Warnungsregeln finden Sie in der Dokumentation zur Überwachung der einzelnen Azure-Dienste. Azure Monitor umfasst standardmäßig keine Warnungsregeln. 

Es gibt mehrere Arten von Warnungsregeln, die durch den Typ der verwendeten Daten definiert werden. Jede weist eigene Funktionen auf und mit ist mit unterschiedlich hohen Kosten verbunden. Als grundlegende Strategie sollten Sie den Warnungsregeltyp mit den niedrigsten Kosten verwenden, der die erforderliche Logik bereitstellt.

- [Aktivitätsprotokollregeln:](alerts/activity-log-alerts.md) erstellen eine Warnung als Reaktion auf ein neues Aktivitätsprotokollereignis, das den angegebenen Bedingungen entspricht. Diese Warnungen sind kostenfrei und sollten daher Ihre erste Wahl sein, auch wenn die mit ihnen erkennbaren Bedingungen begrenzt sind. Ausführliche Informationen zum Erstellen einer Aktivitätsprotokollwarnung finden Sie unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](alerts/alerts-activity-log.md).
- [Metrikwarnungsregeln:](alerts/alerts-metric-overview.md) erstellen eine Warnung als Reaktion auf Metrikwerte, die einen Schwellenwert überschreiten. Metrikwarnungen sind zustandsbehaftet. Das bedeutet, dass die Warnung automatisch geschlossen wird, wenn der Wert unter den Schwellenwert fällt, und dass nur dann Benachrichtigungen gesendet werden, wenn sich der Zustand ändert. Es fallen Kosten für Metrikwarnungen an, die im Vergleich zu Protokollwarnungen jedoch häufig erheblich geringer sind. Ausführliche Informationen zum Erstellen von Metrikwarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](alerts/alerts-metric.md).
- [Protokollwarnungsregeln:](alerts/alerts-unified-log.md) erstellen eine Warnung, wenn die Ergebnisse einer Zeitplanabfrage den angegebenen Kriterien entsprechen. Dabei handelt es sich um die kostenintensivsten Warnungsregeln, die jedoch die komplexesten Kriterien ermöglichen. Ausführliche Informationen zum Erstellen von Protokollabfragewarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](alerts/alerts-log.md).
- [Anwendungswarnungen:](app/monitor-web-app-availability.md) ermöglichen Ihnen proaktive Leistungs- und Verfügbarkeitstests Ihrer Webanwendung. Sie können einen einfachen Ping-Test kostenlos ausführen, während für komplexere Tests Kosten anfallen. Unter [Überwachen der Verfügbarkeit von Websites](app/monitor-web-app-availability.md) finden Sie eine Beschreibung der verschiedenen Arten von Tests sowie Details zu deren Erstellung.

## <a name="alert-severity"></a>Schweregrad der Warnung
Jede Warnungsregel definiert den Schweregrad der Warnungen, die sie basierend auf der folgenden Tabelle erstellt. Warnungen im Azure-Portal werden nach Ebene gruppiert, sodass Sie ähnliche Warnungen gemeinsam verwalten und schnell diejenigen mit der größten Dringlichkeit identifizieren können.

| Ebene | Name | BESCHREIBUNG |
|:---|:---|:---|
| Schweregrad 0 | Kritisch  | Verlust der Dienst- oder Anwendungsverfügbarkeit oder schwerwiegende Leistungsbeeinträchtigung. Erfordert sofortige Aufmerksamkeit. |
| Schweregrad 1 | Fehler  | Beeinträchtigung der Leistung oder Verlust der Verfügbarkeit eines Aspekts einer Anwendung oder eines Diensts. Erfordert Aufmerksamkeit, aber nicht sofort. |
| Schweregrad 2 | Warnung | Ein Problem, das keinen aktuellen Verlust der Verfügbarkeit oder Leistung bedeutet, das jedoch zu schwerwiegenderen Problemen führen kann, wenn es nicht behoben wird. |
| Schweregrad 3 | Informational | Weist nicht auf ein Problem hin, sondern auf eine für den Benutzer interessante Information, z. B. den erfolgreichen Abschluss eines regulären Vorgangs. |
| Schweregrad 4 | Ausführlich | Ausführliche Informationen, in der Regel nicht hilfreich 

Sie sollten den Schweregrad der Bedingung bewerten, die jede Regel identifiziert, um eine geeignete Ebene zuzuweisen. Die Arten von Problemen, die Sie den einzelnen Schweregraden zuweisen, und Ihre Standardreaktion auf die einzelnen Probleme sollten in Ihrer Warnungsstrategie definiert werden. 

## <a name="action-groups"></a>Aktionsgruppen
Automatisierte Reaktionen auf Warnungen in Azure Monitor werden in [Aktionsgruppen](alerts/action-groups.md) definiert. Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungen und Aktionen, die ausgelöst werden, wenn eine Warnung ausgelöst wird. Eine einzelne Aktionsgruppe kann mit mehreren Warnungsregeln verwendet werden und eine oder mehrere der folgenden Elemente enthalten:

- Benachrichtigungen. Meldungen, die Operatoren und Administratoren benachrichtigen, dass eine Warnung erstellt wurde.
- Aktionen Automatisierte Prozesse, die versuchen, das erkannte Problem zu beheben. 
## <a name="notifications"></a>Benachrichtigungen
Benachrichtigungen sind Meldungen, die an einen oder mehrere Benutzer gesendet werden, um sie darüber zu informieren, dass eine Warnung erstellt wurde. Da eine einzelne Aktionsgruppe mit mehreren Warnungsregeln verwendet werden kann, sollten Sie einen Satz von Aktionsgruppen für verschiedene Gruppen von Administratoren und Benutzern entwerfen, die die gleichen Warnungen erhalten. Verwenden Sie eine der folgenden Arten von Benachrichtigungen, abhängig von den Präferenzen Ihrer Operatoren und den Standards Ihrer Organisation.

- Email
- sms
- Push an Azure-App
- Sprache
- E-Mail an Azure Resource Manager-Rolle

## <a name="actions"></a>Aktionen
Aktionen sind automatisierte Reaktionen auf eine Warnung. Sie können die verfügbaren Aktionen für jedes unterstützte Szenario verwenden. In den folgenden Abschnitten wird jedoch beschrieben, wie die einzelnen Aktionen in der Regel verwendet werden.

### <a name="automated-remediation"></a>Automatisierte Korrektur
Verwenden Sie die folgenden Aktionen, um zu versuchen, das von der Warnung identifizierte Problem automatisch zu beheben. 

- Automation-Runbook: Starten Sie entweder ein integriertes oder benutzerdefiniertes Runbook in Azure Automation. Beispielsweise sind integrierte Runbooks verfügbar, um eine VM neu zu starten oder hochzuskalieren.
- Azure-Funktion: Starten sie eine Azure-Funktion.


### <a name="itsm-and-on-call-management"></a>ITSM und On-Call-Verwaltung

- ITSM: Verwenden Sie den [ITSM-Connector](), um Arbeitselemente in Ihrem ITSM-Tool basierend auf Warnungen von Azure Monitor zu erstellen. Sie konfigurieren zuerst den Connector und verwenden dann die **ITSM**-Aktion in Warnungsregeln.
- Webhooks: Senden Sie die Warnung an ein System zur Incidentverwaltung, das Webhooks wie PagerDuty und Splunk On-Call unterstützt.
- Sicherer Webhook: ITSM-Integration mit Azure AD-Authentifizierung


## <a name="minimizing-alert-activity"></a>Minimieren von Warnungsaktivitäten
Sie sollten zwar Warnungen für alle wichtigen Informationen in Ihrer Umgebung erstellen, aber auch darauf achten, dass Sie nicht zu viele Warnungen und Benachrichtigungen für Probleme generieren, die dies nicht rechtfertigen. Verwenden Sie die folgenden Richtlinien, um Ihre Alarmaktivität zu minimieren und sicherzustellen, dass kritische Probleme aufgedeckt werden, ohne dass übermäßige Informationen und Benachrichtigungen für die Administratoren erzeugt werden. 

- Bestimmen Sie anhand der Grundsätze unter [Wirkungsvolle Warnungsstrategie](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy), ob ein Symptom ein geeigneter Kandidat für eine Warnung ist.
- Verwenden Sie die Option **Warnungen automatisch auflösen** in Metrikwarnungsregeln, um Warnungen aufzulösen, wenn die Bedingung korrigiert wurde.
- Verwenden Sie die Option **Warnungen unterdrücken** in Protokollabfragewarnungsregeln, die das Erstellen mehrerer Warnungen für dasselbe Problem verhindert.
- Stellen Sie sicher, dass Sie geeignete Schweregrade für Warnungsregeln verwenden, damit Probleme mit hoher Priorität gemeinsam analysiert werden können.
- Schränken Sie Benachrichtigungen für Warnungen mit dem Schweregrad „Warnung“ oder niedriger ein, da sie keine sofortige Aufmerksamkeit erfordern.

## <a name="create-alert-rules-at-scale"></a>Erstellen von Warnungsregeln nach Bedarf
Da Sie in der Regel über Probleme für alle kritischen Azure-Anwendungen und -Ressourcen benachrichtigt werden möchten, sollten Sie Methoden zur bedarfsorientierten Erstellung von Alarmregeln nutzen.

- Azure Monitor unterstützt die Überwachung mehrerer Ressourcen desselben Typs mit einer Metrikwarnungsregel für Ressourcen, die in der gleichen Azure-Region vorhanden sind. Eine Liste der Azure-Dienste, die derzeit für dieses Feature unterstützt werden, finden Sie unter [Bedarfsorientierte Überwachung mithilfe von Metrikwarnungen in Azure Monitor](alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).
- Verwenden Sie für Metrikwarnungsregeln für Azure-Dienste, die nicht mehrere Ressourcen unterstützen, Automatisierungstools wie die CLI und PowerShell mit Resource Manager-Vorlagen, um dieselbe Warnungsregel für mehrere Ressourcen zu erstellen. Siehe hierzu [Beispiele für Resource Manager-Vorlagen für Metrikwarnungsregeln in Azure Monitor](alerts/resource-manager-alerts-metric.md).
- Schreiben Sie Abfragen in Protokollabfragewarnungsregeln, um Daten für mehrere Ressourcen zurückzugeben. Verwenden Sie die Einstellung **Nach Dimensionen teilen** in der Regel, um separate Warnungen für jede Ressource zu erstellen.


> [!NOTE]
> Mit ressourcenbezogenen Protokollabfragewarnungsregeln (diese befinden sich derzeit in der öffentlichen Vorschau) können Sie alle Ressourcen in einem Abonnement oder einer Ressourcengruppe als Ziel für eine Protokollabfragewarnung verwenden. 

## <a name="next-steps"></a>Nächste Schritte

- [Definieren von Warnungen und automatisierten Aktionen anhand von Azure Monitor-Daten](best-practices-alerts.md)
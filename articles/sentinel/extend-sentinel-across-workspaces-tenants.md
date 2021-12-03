---
title: Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft Sentinel zum übergreifenden Abfragen und Analysieren von Daten in Arbeitsbereichen und Mandanten verwenden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3b49f1bdbe14dd13417bfef0348e1fd53543d7ca
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521242"
---
# <a name="extend-microsoft-sentinel-across-workspaces-and-tenants"></a>Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="the-need-to-use-multiple-microsoft-sentinel-workspaces"></a>Manchmal unverzichtbar: die Nutzung mehrerer Microsoft Sentinel-Arbeitsbereiche

Microsoft Sentinel baut auf einem Log Analytics-Arbeitsbereich auf. Sie werden feststellen, dass der erste Schritt beim Onboarding von Microsoft Sentinel darin besteht, den zweckmäßigen Log Analytics-Arbeitsbereich auszuwählen.

Sie können die Vorteile von Microsoft Sentinel in vollem Umfang nutzen, wenn Sie einen einzigen Arbeitsbereich verwenden. Dennoch gibt es Umstände, die es erforderlich machen, dass mehrere Arbeitsbereiche verwendet werden. In der folgenden Tabelle werden einige dieser Situationen aufgelistet und Sie erhalten Vorschläge, wie die Anforderung mit einem einzigen Arbeitsbereich erfüllt werden kann:

| Anforderung | BESCHREIBUNG | Arbeitsbereiche reduzieren – so funktioniert's |
|-------------|-------------|--------------------------------|
| Souveränität und Einhaltung gesetzlicher Bestimmungen | Ein Arbeitsbereich ist an eine bestimmte Region gebunden. Wenn Daten in unterschiedlichen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/geographies/) aufbewahrt werden müssen, um gesetzliche Anforderungen zu erfüllen, ist eine Unterteilung in separate Arbeitsbereiche erforderlich. |  |
| Datenbesitz | Die Grenzen des Datenbesitzes, z. B. nach Tochtergesellschaften oder Partnerunternehmen, lassen sich durch separate Arbeitsbereiche besser nachvollziehen. |  |
| Mehrere Azure-Mandanten | Microsoft Sentinel unterstützt die Datenerfassung von Microsoft- und Azure-SaaS-Ressourcen nur innerhalb einer eigenen Azure Active Directory-Mandantenbegrenzung (Azure AD). Daher wird für jeden Azure AD-Mandanten ein separater Arbeitsbereich benötigt. |  |
| Differenzierte Steuerung des Datenzugriffs | Es kann vorkommen, dass eine Organisation verschiedenen internen oder externen Gruppen Zugriff auf einen Teil der von Microsoft Sentinel gesammelten Daten gewähren muss. Beispiel:<br><ul><li>Zugriff von Ressourcenbesitzern auf Daten, die sich auf ihre Ressourcen beziehen</li><li>Zugriff regionaler oder untergeordneter SOCs auf Daten, die für den jeweiligen Zuständigkeitsbereich relevant sind</li></ul> | Verwenden Sie [Azure RBAC für Ressourcen](resource-context-rbac.md) oder [Azure RBAC auf Tabellenebene](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Differenzierte Einstellungen für die Datenaufbewahrung | Die einzige Möglichkeit, unterschiedliche Aufbewahrungsfristen für verschiedene Datentypen festzulegen, bestand in der Vergangenheit in der Einrichtung mehrerer Arbeitsbereiche. Durch die Einführung von Aufbewahrungseinstellungen auf Tabellenebene ist dies in vielen Fällen nicht mehr erforderlich. | [Aufbewahrungseinstellungen auf Tabellenebene](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) verwenden oder [Datenlöschung](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) automatisieren |
| Getrennte Abrechnung | Durch die Zuordnung von Arbeitsbereichen zu separaten Abonnements können diese verschiedenen Parteien in Rechnung gestellt werden. | Nutzungsberichte und interne Verrechnung |
| Legacy-Architektur | Die Verwendung mehrerer Arbeitsbereiche kann auf einen historischen Designansatz zurückzuführen sein, durch den Einschränkungen oder bewährte Methoden berücksichtigt wurden, die heute nicht mehr gültig sind. Es kann sich aber auch um eine zufällige Designentscheidung handeln, die korrigiert werden kann, um Microsoft Sentinel besser zu unterstützen.<br><br>Beispiele:<br><ul><li>Verwenden eines abonnementbasierten Standardarbeitsbereichs bei der Bereitstellung von Microsoft Defender für Cloud</li><li>Manchmal unverzichtbar: differenzierte Zugriffsteuerung und differenzierte Einstellungen für die Datenaufbewahrung (relativ neu)</li></ul> | Arbeitsbereiche umgestalten |

### <a name="managed-security-service-provider-mssp"></a>Managed Security Service Provider (MSSP)

Ein besonderer Anwendungsfall, der mehrere Arbeitsbereiche erfordert, ist ein MSSP-Microsoft Sentinel-Dienst. In diesem Fall treffen viele, wenn nicht sogar alle der oben genannten Anforderungen zu. Somit sind mehrere mandantenübergreifende Arbeitsbereiche die beste Methode. Der MSSP kann [Azure Lighthouse](../lighthouse/overview.md) verwenden, um die arbeitsbereichsübergreifenden Microsoft Sentinel-Methoden auf Mandanten auszuweiten.

## <a name="microsoft-sentinel-multiple-workspace-architecture"></a>Microsoft Sentinel-Architektur mit mehreren Arbeitsbereichen

Wie aus den obigen Anforderungen hervorgeht, müssen in einigen Fällen mehrere Microsoft Sentinel-Arbeitsbereiche – möglicherweise über mehrere Azure Active Directory-Mandanten (Azure AD) hinweg – zentral von einem einzigen SOC überwacht und verwaltet werden.

- Ein MSSP-Microsoft Sentinel-Dienst.

- Ein globales SOC, das mehrere Niederlassungen bedient, die jeweils über ein eigenes lokales SOC verfügen.

- Ein SOC, das mehrere Azure AD-Mandanten innerhalb einer Organisation überwacht.

Um diese Anforderung zu erfüllen, bietet Microsoft Sentinel arbeitsbereichsübergreifende Funktionen wie die zentrale Überwachung, Konfiguration und Verwaltung. So erhalten Sie eine transparente Sicht auf alle vom SOC abgedeckten Bereiche (siehe das Diagramm unten).

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Arbeitsbereichsübergreifende Architektur":::

Dieses Modell bietet erhebliche Vorteile gegenüber einem vollständig zentralisierten Modell, bei dem alle Daten in einen einzigen Arbeitsbereich kopiert werden:

- Flexible Rollenzuweisung zu globalen und lokalen SOCs oder zu MSSP-Kunden

- Weniger Probleme in Bezug auf Datenbesitz, Datenschutz und Einhaltung von Vorschriften

- Minimale Netzwerklatenz und -kosten

- Einfaches Onboarding und Offboarding neuer Niederlassungen oder Kunden

In den folgenden Abschnitten wird die Verwendung dieses Modells erläutert, insbesondere die:

- Zentrale Überwachung mehrerer Arbeitsbereiche (ggf. mandantenübergreifend) mit transparenten Einblicken in die vom SOC abgedeckten Bereiche

- Zentrale Konfiguration und Verwaltung mehrerer Arbeitsbereiche (ggf. mandantenübergreifend) mithilfe von Automatisierung

## <a name="cross-workspace-monitoring"></a>Arbeitsbereichsübergreifende Überwachung

### <a name="manage-incidents-on-multiple-workspaces"></a>Verwalten von Incidents in mehreren Arbeitsbereichen

Microsoft Sentinel unterstützt eine [arbeitsbereichsübergreifende Incident-Ansicht](./multiple-workspace-view.md), die die zentrale Überwachung und Verwaltung von Incidents in mehreren Arbeitsbereichen ermöglicht. In der zentralen Incident-Ansicht lassen sich Incidents direkt verwalten oder Drilldowns ausführen, um detaillierte Incident-Informationen im Kontext des ursprünglichen Arbeitsbereichs transparent darzustellen.

### <a name="cross-workspace-querying"></a>Arbeitsbereichsübergreifende Abfragen

Microsoft Sentinel unterstützt das Abfragen [mehrerer Arbeitsbereiche in einer einzigen Abfrage](../azure-monitor/logs/cross-workspace-query.md). So lassen sich Daten aus mehreren Arbeitsbereichen mit einer Abfrage suchen und korrelieren.

- Um auf eine Tabelle in einem anderen Arbeitsbereich zu verweisen, verwenden Sie den [workspace()-Ausdruck](../azure-monitor/logs/workspace-expression.md).

- Verwenden Sie den [union-Operator](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) zusammen mit dem workspace()-Ausdruck, um eine Abfrage tabellenübergreifend auf mehrere Arbeitsbereiche anzuwenden.

Sie können gespeicherte [Funktionen](../azure-monitor/logs/functions.md) verwenden, um arbeitsbereichsübergreifende Abfragen zu vereinfachen. Wenn z. B. ein Verweis auf einen Arbeitsbereich lang ist, können Sie den Ausdruck `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` als eine Funktion mit dem Namen `SecurityEventCustomerA` speichern. Anschließend können Sie Abfragen als `SecurityEventCustomerA | where ...` schreiben.

Durch eine Funktion kann auch eine häufig verwendete Union vereinfacht werden. Beispielsweise können Sie den folgenden Ausdruck als eine Funktion mit dem Namen `unionSecurityEvent` speichern:

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Anschließend können Sie eine Abfrage über beide Arbeitsbereiche schreiben, indem Sie mit `unionSecurityEvent | where ...` beginnen.

#### <a name="cross-workspace-analytics-rules"></a>Arbeitsbereichsübergreifende Analyseregeln<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Arbeitsbereichsübergreifende Abfragen können jetzt in geplante Analyseregeln einbezogen werden. Sie können arbeitsbereichsübergreifende Analyseregeln in einem zentralen SOC-Team und mandantenübergreifend (mit Azure Lighthouse) wie bei einem MSSP verwenden. Dabei gelten allerdings folgende Einschränkungen:

- In einer einzelnen Abfrage können **bis zu 20 Arbeitsbereiche** enthalten sein.
- Microsoft Sentinel muss **in jedem Arbeitsbereich bereitgestellt werden**, auf den in der Abfrage verwiesen wird.
- Von einer arbeitsbereichsübergreifenden Analyseregel generierte Warnungen und die auf dieser Grundlage erstellten Incidents sind **nur in dem Arbeitsbereich vorhanden, in dem die Regel definiert wurde**. Sie werden in keinem der anderen Arbeitsbereiche angezeigt, auf die in der Abfrage verwiesen wird.

Von arbeitsbereichsübergreifenden Analyseregeln erstellte Warnungen und Incidents enthalten alle zugehörigen Entitäten – einschließlich Entitäten aus allen Arbeitsbereichen, auf die verwiesen wird, sowie aus dem ursprünglichen Arbeitsbereich (in dem die Regel definiert wurde). Dadurch können sich Analysten ein umfassendes Bild von Warnungen und Vorfällen machen.

> [!NOTE]
> Werden in derselben Abfrage mehrere Arbeitsbereiche abgefragt, kann sich dies negativ auf die Leistung auswirken. Diese Vorgehensweise wird daher nur empfohlen, wenn diese Funktionalität von der Logik vorausgesetzt wird.

#### <a name="cross-workspace-workbooks"></a>Arbeitsbereichsübergreifende Arbeitsmappen<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Über [Arbeitsmappen](./overview.md#workbooks) werden Dashboards und Apps für Microsoft Sentinel bereitgestellt. Beim Arbeiten mit mehreren Arbeitsbereichen werden Überwachung und weitere Aktionen arbeitsbereichsübergreifend bereitgestellt.

Arbeitsmappen können arbeitsbereichsübergreifende Abfragen mit einer von drei Methoden bereitstellen. Je nach Methode werden unterschiedliche Fachkenntnisse der Endbenutzer berücksichtigt:

| Methode  | BESCHREIBUNG | Verwendung |
|---------|-------------|--------------------|
| Arbeitsbereichsübergreifende Abfragen schreiben | Der Ersteller der Arbeitsmappe kann arbeitsbereichsübergreifende Abfragen (oben beschrieben) in die Arbeitsmappe schreiben. | Mit dieser Option können Ersteller von Arbeitsmappen den Benutzer vollständig von der Arbeitsbereichsstruktur abschirmen. |
| Arbeitsbereichsselektor zur Arbeitsmappe hinzufügen | Der Ersteller der Arbeitsmappe kann einen Arbeitsbereichsselektor als Teil der Arbeitsmappe implementieren, wie [hier](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357) beschrieben. | Diese Option bietet dem Benutzer die Kontrolle über die in der Arbeitsmappe enthaltenen Arbeitsbereiche. Dies erfolgt über ein einfach zu bedienendes Dropdownfeld. |
| Arbeitsmappe interaktiv bearbeiten | Ein fortgeschrittener Benutzer, der eine bestehende Arbeitsmappe ändert, kann die darin enthaltenen Abfragen bearbeiten, indem er die Zielarbeitsbereiche mithilfe des Arbeitsbereichsselektors im Editor auswählt. | Über diese Option kann ein Hauptbenutzer bestehende Arbeitsmappen problemlos ändern, um mit mehreren Arbeitsbereichen arbeiten zu können. |
|

#### <a name="cross-workspace-hunting"></a>Arbeitsbereichsübergreifendes Hunting

Microsoft Sentinel bietet vorab geladene Abfragebeispiele, um Ihnen die ersten Schritte zu vereinfachen und Sie mit den Tabellen und der Abfragesprache vertraut zu machen. Diese integrierten Bedrohungssuchabfragen werden von Microsoft-Sicherheitsexperten kontinuierlich entwickelt. Dies geschieht sowohl durch Hinzufügen neuer Abfragen als auch Optimieren vorhandener Abfragen, um Ihnen einen Einstiegspunkt zu bieten, ab dem Sie nach neuen Erkennungen suchen und Anzeichen von Eindringversuchen identifizieren können, die von Ihren Sicherheitstools möglicherweise unentdeckt geblieben sind.  

Mithilfe der arbeitsbereichsübergreifenden Hunting-Funktionen können Ihre Bedrohungsspezialisten neue Hunting-Abfragen erstellen oder vorhandene anpassen, um mehrere Arbeitsbereiche abzudecken. Dazu werden der Union-Operator und der workspace()-Ausdruck wie oben gezeigt verwendet.

## <a name="cross-workspace-management-using-automation"></a>Arbeitsbereichsübergreifende Verwaltung mithilfe von Automatisierung

Um mehrere Microsoft Sentinel-Arbeitsbereiche zu konfigurieren und zu verwalten, müssen Sie die Verwendung der Microsoft Sentinel Management-API automatisieren. Weitere Informationen zur automatisierten Bereitstellung von Microsoft Sentinel-Ressourcen, einschließlich Warnungsregeln, Hunting-Abfragen, Arbeitsmappen und Playbooks, finden Sie unter [Erweitern von Microsoft Sentinel: APIs, Integration und Verwaltungsautomatisierung](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Weitere Informationen finden Sie zudem unter [Bereitstellen und Verwalten von Microsoft Sentinel als Code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) und [Kombinieren von Azure Lighthouse mit DevOps-Funktionen für Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966). Mithilfe einer konsolidierten, von der Community unterstützten Methode können Sie Microsoft Sentinel als Code verwalten und Ressourcen über ein privates GitHub-Repository bereitstellen und konfigurieren.

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Mandantenübergreifende Verwaltung von Arbeitsbereichen mithilfe von Azure Lighthouse

Wie oben erwähnt, können sich die verschiedenen Microsoft Sentinel-Arbeitsbereiche je nach Szenario in unterschiedlichen Azure AD-Mandanten befinden. Sie können [Azure Lighthouse](../lighthouse/overview.md) verwenden, um alle arbeitsbereichsübergreifenden Aktivitäten über Mandantengrenzen hinweg auszuweiten. So können die zum verwaltenden Mandanten gehörenden Benutzer mandantenübergreifend in Microsoft Sentinel-Arbeitsbereichen arbeiten. Nachdem Azure Lighthouse [integriert](../lighthouse/how-to/onboard-customer.md) wurde, verwenden Sie die [Verzeichnis- und Abonnementauswahl](./multiple-tenants-service-providers.md#how-to-access-microsoft-sentinel-in-managed-tenants) im Azure-Portal, um alle Abonnements mit Arbeitsbereichen auszuwählen, die verwaltet werden sollen. Auf diese Weise stellen Sie sicher, dass alle Arbeitsbereiche in den verschiedenen Arbeitsbereichsselektoren im Portal verfügbar sind.

Bei Verwendung von Azure Lighthouse empfiehlt es sich, eine Gruppe für jede Microsoft Sentinel-Rolle zu erstellen und Berechtigungen von jedem Mandanten an diese Gruppen zu delegieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Microsoft Sentinel-Funktionen auf mehrere Arbeitsbereiche und Mandanten ausgeweitet werden können. Eine praktische Anleitung zum Implementieren der arbeitsbereichsübergreifenden Microsoft Sentinel-Architektur finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie über Azure Lighthouse in Microsoft Sentinel [mit mehreren Mandanten](./multiple-tenants-service-providers.md) arbeiten.
- Erfahren Sie, wie Sie nahtlos [Incidents in mehreren Arbeitsbereichen anzeigen und verwalten](./multiple-workspace-view.md).

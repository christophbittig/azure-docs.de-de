---
title: 'Azure Automation-Updateverwaltung: Übersicht'
description: Dieser Artikel bietet eine Übersicht über die Updateverwaltungsfunktion, die Updates für Ihre Windows- und Linux-Computer implementiert.
services: automation
ms.subservice: update-management
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: fed1ce7f236b568458f1eb1b25ce5420c2ad5501
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092154"
---
# <a name="update-management-overview"></a>Übersicht über die Updateverwaltung

Mithilfe der Updateverwaltung in Azure Automation können Sie Betriebssystemupdates für Ihre virtuellen Windows- und Linux-Computer in Azure, in lokalen Umgebungen und in anderen Cloudumgebungen verwalten. Sie können den Status der verfügbaren Updates schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren. 

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../../lighthouse/overview.md) integriert. Die Updateverwaltung kann verwendet werden, um Updatebereitstellungen auf Computern in mehreren Abonnements im gleichen Azure Active Directory-Mandanten (Azure AD) oder mandantenübergreifend mithilfe von Azure Lighthouse zu planen.

Microsoft bietet weitere Funktionen, mit denen Sie Updates für Ihre Azure-VMs oder für Ihre Azure-VM-Skalierungsgruppen verwalten können. Diese sollten Sie in Ihre gesamte Updateverwaltungsstrategie integrieren. 

- Wenn Sie Ihre virtuellen Azure-Computer automatisch bewerten und aktualisieren möchten, um die Sicherheitskonformität mit den monatlichen *kritischen Updates* und *Sicherheitsupdates* zu gewährleisten, lesen Sie [Automatische VM-Gastpatches](../../virtual-machines/automatic-vm-guest-patching.md) (Vorschau). Hierbei handelt es sich um eine alternative Updateverwaltungslösung für Ihre Azure-VMs, damit diese außerhalb der Spitzenzeiten automatisch aktualisiert werden können. Auch VMs innerhalb einer Verfügbarkeitsgruppe, deren Updatebereitstellungen normalerweise manuell über die Updateverwaltung in Azure Automation verwaltet werden müssen, können so automatisch aktualisiert werden. 

- Wenn Sie Azure-VM-Skalierungsgruppen verwalten, informieren Sie sich darüber, wie Sie [automatische Upgrades für Betriebssystemimages](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) durchführen, um den Betriebssystemdatenträger für alle Instanzen der Skalierungsgruppe sicher und automatisch zu aktualisieren. 

Machen Sie sich mit den Informationen in den folgenden Abschnitten vertraut, bevor Sie die Updateverwaltung bereitstellen und die Verwaltung auf Ihren Computern ermöglichen.

## <a name="about-update-management"></a>Informationen zur Updateverwaltung

Das folgende Diagramm veranschaulicht, wie die Updateverwaltung alle verbundenen Windows Server- und Linux-Server bewertet und Sicherheitsupdates darauf anwendet.

![Updateverwaltungs-Workflow](./media/overview/update-mgmt-workflow.png)

Die Updateverwaltung kann in Azure Monitor-Protokolle integriert werden, um Updatebewertungen und -bereitstellungsergebnisse als Protokolldaten von zugewiesenen Azure- und Nicht-Azure-Computern zu speichern. Um diese Daten zu sammeln, werden das Automation-Konto und der Log Analytics-Arbeitsbereich miteinander verknüpft. Darüber hinaus ist der Log Analytics-Agent für Windows und Linux auf dem Computer erforderlich, der so konfiguriert werden muss, dass er an diesen Arbeitsbereich meldet. 

Die Updateverwaltung unterstützt das Sammeln von Informationen über Systemupdates von Agents in einer System Center Operations Manager-Verwaltungsgruppe, die mit dem Arbeitsbereich verbunden ist. Die Registrierung eines Computers für die Updateverwaltung in mehreren Log Analytics-Arbeitsbereichen (auch als „Multi-Homing“ bezeichnet) wird nicht unterstützt.

In der folgenden Tabelle sind die von der Updateverwaltung unterstützten verbundenen Quellen zusammengefasst.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| Windows |Ja |Die Updateverwaltung sammelt Informationen zu Systemupdates aus Windows-Computern mithilfe des Log Analytics-Agents und initiiert die Installation von erforderlichen Updates.<br> Die Rechner müssen sich bei Microsoft Update oder Windows Server Update Services (WSUS) melden. |
| Linux |Ja |Die Updateverwaltung sammelt Informationen zu Systemupdates aus Linux-Computern mithilfe des Log Analytics-Agents und initiiert die Installation von erforderlichen Updates auf unterstützten Distributionen.<br> Die Rechner müssen an ein lokales oder entferntes Repository berichten. |
| Operations Manager-Verwaltungsgruppe |Ja |Die Updateverwaltung sammelt Informationen zu Softwareupdates von Agents in einer verbundenen Verwaltungsgruppe.<br/><br/>Es ist keine direkte Verbindung zwischen dem Operations Manager-Agent und Azure Monitor-Protokollen erforderlich. Protokolldaten werden von der Verwaltungsgruppe an den Log Analytics-Arbeitsbereich weitergeleitet. |

Die Computer, die der Updateverwaltung zugeordnet sind, melden, auf welchem aktuellen Stand sie sind, und zwar basierend auf der Quelle, mit der sie sich synchronisieren sollen. Windows-Rechner müssen so konfiguriert werden, dass sie entweder an [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) oder [Microsoft Update](https://www.update.microsoft.com) berichten, und Linux-Rechner müssen so konfiguriert werden, dass sie an ein lokales oder öffentliches Repository berichten. Sie können die Updateverwaltung ebenfalls mit Microsoft Endpoint Configuration Manager verwenden. Weitere Informationen dazu finden Sie unter [Integrieren der Updateverwaltung mit Windows Endpoint Configuration Manager](mecmintegration.md). 

Wenn der Windows Update Agent (WUA) für das Senden von Meldungen an WSUS konfiguriert ist, können sich die Ergebnisse von den angezeigten Microsoft Update-Ergebnissen unterscheiden. Dies hängt davon ab, wann WSUS zuletzt mit Microsoft Update synchronisiert wurde. Dasselbe gilt für Linux-Computer, die für Meldungen an ein lokales Repository konfiguriert sind (anstatt an ein öffentliches Repository). Auf einem Windows-Computer wird der Konformitätsscan standardmäßig alle 12 Stunden ausgeführt. Für einen Linux-Computer wird der Konformitätsscan standardmäßig jede Stunde durchgeführt. Wenn der Log Analytics-Agent neu gestartet wird, wird ein Konformitätsscan innerhalb von 15 Minuten gestartet. Wenn ein Computer einen Scanvorgang abgeschlossen hat, um die Konformität für das Update zu überprüfen, leitet der Agent die Informationen gesammelt an Azure Monitor-Protokolle weiter. 

Sie können Softwareupdates auf Computern bereitstellen und installieren, für die die Updates erforderlich sind, indem Sie einen geplante Bereitstellung erstellen. Updates, die als *Optional* klassifiziert sind, sind im Bereitstellungsumfang von Windows-Computern nicht enthalten. Nur erforderliche Updates sind im Bereitstellungsumfang enthalten.

Die geplante Bereitstellung definiert, welche Zielcomputer die anwendbaren Updates erhalten. Hierzu werden entweder explizit bestimmte Computer angegeben, oder es wird eine [Computergruppe](../../azure-monitor/logs/computer-groups.md) ausgewählt, die auf Protokollsuchvorgängen einer bestimmten Gruppe von Computern basiert (oder auf einer [Azure-Abfrage](query-logs.md), die virtuelle Azure-Computer auf der Grundlage bestimmter Kriterien dynamisch auswählt). Diese Gruppen unterscheiden sich von der [Bereichskonfiguration](../../azure-monitor/insights/solution-targeting.md), mit der die Zielcomputer gesteuert werden, die die Konfiguration erhalten sollen, um die Updateverwaltung zu aktivieren. Dies verhindert, dass sie die Updatekonformität prüfen und melden und genehmigte erforderliche Updates installieren.

Beim Definieren einer Bereitstellung geben Sie außerdem einen Zeitplan an, um einen Zeitraum zu genehmigen und festzulegen, in dem Updates installiert werden dürfen. Dieser Zeitraum wird das Wartungsfenster bezeichnet. 20 Minuten des Wartungsfensters sind für Neustarts reserviert (sofern ein Neustart erforderlich ist und Sie die entsprechende Neustartoption ausgewählt haben). Wenn das Patchen länger dauert als erwartet und im Wartungsfenster weniger als 20 Minuten verbleiben, wird kein Neustart durchgeführt.

Nachdem die Bereitstellung eines Updatepakets geplant wurde, dauert es 2 bis 3 Stunden, bis das Update bei Linux-Computern zur Bewertung angezeigt wird. Bei Windows-Computern dauert es 12 bis 15 Stunden, bis das Update nach der Veröffentlichung für die Bewertung angezeigt wird. Vor und nach der Updateinstallation wird eine Überprüfung der Updatekonformität durchgeführt und die Protokolldatenergebnisse werden an den Arbeitsbereich weitergeleitet.

Updates werden mit Runbooks in Azure Automation installiert. Sie können diese Runbooks nicht anzeigen, und für diese Runbooks ist keine Konfiguration erforderlich. Bei der Erstellung einer Updatebereitstellung wird ein Zeitplan erstellt, nach dem für die einbezogenen Computer zur angegebenen Zeit ein Masterrunbook für das Update gestartet wird. Das Master-Runbook startet auf jedem Agent ein untergeordnetes Runbook, das die Installation der erforderlichen Updates mit dem Windows Update-Agent (unter Windows) oder über den entsprechenden Befehl für die unterstützte Linux-Distribution initiiert.

Wenn die Datums- bzw. Uhrzeitangabe der Updatebereitstellung erreicht ist, führen die Zielcomputer die Bereitstellung parallel aus. Vor der Installation wird ein Scan ausgeführt, um sicherzustellen, dass die Updates weiterhin erforderlich sind. Für WSUS-Clientcomputer tritt ein Fehler bei der Updatebereitstellung auf, wenn die Updates in WSUS nicht genehmigt wurden.

## <a name="limits"></a>Grenzwerte

Grenzwerte, die für Updateverwaltung gelten, finden Sie unter [Azure Automation Dienstgrenzwerte](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

## <a name="permissions"></a>Berechtigungen

Zum Erstellen und Verwalten von Updatebereitstellungen benötigen Sie bestimmte Berechtigungen. Weitere Informationen zu diesen Berechtigungen finden Sie unter [Rollenbasierter Zugriff: Updateverwaltung](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Updateverwaltungskomponenten

Die Updateverwaltung verwendet die in diesem Abschnitt beschriebenen Ressourcen. Diese Ressourcen werden automatisch Ihrem Automation-Konto hinzugefügt, wenn Sie die Updateverwaltung aktivieren.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid-Runbook-Workergruppen

Nachdem Sie die Updateverwaltung aktiviert haben, werden alle direkt mit dem Log Analytics-Arbeitsbereich verbundenen Windows-Computer automatisch als System Hybrid Runbook Worker konfiguriert, um die Runbooks zu unterstützen, die die Updateverwaltung unterstützen.

Jeder von der Updateverwaltung verwaltete Windows-Computer wird im Bereich „Hybrid Worker-Gruppen“ als Hybrid Worker-Systemgruppe für das Automation-Konto aufgeführt. Die Gruppen verwenden die Benennungskonvention `Hostname FQDN_GUID`. Es ist nicht möglich, diese Gruppen mit Runbooks in Ihrem Konto zu erreichen. Entsprechende Versuche sind nicht erfolgreich. Diese Gruppen sind nur für die ausschließliche Unterstützung der Updateverwaltung bestimmt. Weitere Informationen zur Anzeige der Liste von Windows-Computern, die als Hybrid Runbook Worker konfiguriert sind, finden Sie unter [Anzeigen von Hybrid Runbook Workern](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Sie können die Windows-Computer einer System Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzufügen, um Automation-Runbooks zu unterstützen, wenn Sie für die Updateverwaltung und die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden. Diese Funktionalität wurde in Version 7.2.12024.0 des Hybrid Runbook Worker hinzugefügt.

### <a name="external-dependencies"></a>Externe Abhängigkeiten

Azure Automation Update Management hängt von den folgenden externen Abhängigkeiten ab, um Software-Updates bereitzustellen.

* Windows Server Update Services (WSUS) oder Microsoft Update wird für Software-Update-Pakete und für die Überprüfung der Anwendbarkeit von Software-Updates auf Windows-basierten Computern benötigt.
* Der Windows Update Agent (WUA) Client wird auf Windows-basierten Rechnern benötigt, damit diese eine Verbindung zum WSUS-Server oder Microsoft Update herstellen können.
* Ein lokales oder entferntes Repository zum Abrufen und Installieren von Betriebssystem-Updates auf Linux-basierten Rechnern.

### <a name="management-packs"></a>Management Packs

Die folgenden Management Packs werden auf den Computern installiert, die von der Updateverwaltung verwaltet werden. Wenn Ihre Operations Manager-Verwaltungsgruppe [mit einem Log Analytics-Arbeitsbereich verbunden](../../azure-monitor/agents/om-agents.md) ist, werden die Management Packs in der Operations Manager-Verwaltungsgruppe installiert. Für diese Management Packs fällt kein Konfigurations- oder Verwaltungsaufwand an.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Wenn Sie über eine Operations Manager 1807- oder 2019-Verwaltungsgruppe verfügen, die mit einem Log Analytics-Arbeitsbereich verbunden ist, und in der Verwaltungsgruppe Agents für die Erfassung von Protokolldaten konfiguriert sind, müssen Sie den Parameter `IsAutoRegistrationEnabled` außer Kraft setzen und in der Regel **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** auf `True` festlegen.

Weitere Informationen zu Updates der Management Packs finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor-Protokollen](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Damit die Updateverwaltung Computer mit dem Log Analytics-Agent vollständig verwalten kann, müssen Sie auf den Log Analytics-Agent für Windows oder den Log Analytics-Agent für Linux aktualisieren. Informationen zum Aktualisieren des Agents finden Sie unter [Aktualisieren eines Operations Manager-Agents](/system-center/scom/deploy-upgrade-agents). In Umgebungen, in denen Operations Manager verwendet wird, muss mindestens System Center Operations Manager 2012 R2 UR14 ausgeführt werden.

## <a name="data-collection-frequency"></a>Häufigkeit der Datensammlung

Die Updateverwaltung überprüft verwaltete Computer auf Daten mithilfe der folgenden Regeln. Es kann zwischen 30 Minuten und 6 Stunden dauern, bis im Dashboard aktualisierte Daten von verwalteten Computern angezeigt werden.

* Für jeden Windows-Computer: Die Updateverwaltung führt zweimal am Tag eine Überprüfung jedes Computers durch.

* Für jeden Linux-Computer: Die Updateverwaltung führt jede Stunde eine Überprüfung durch.

Die durchschnittliche Datennutzung von Azure Monitor-Protokollen für einen Computer mit Updateverwaltung beträgt etwa 25 MB pro Monat. Dieser Wert ist lediglich eine Schätzung und kann sich abhängig von Ihrer Umgebung ändern. Es empfiehlt sich, die Umgebung zu überwachen, um die exakte Nutzung nachzuverfolgen. Weitere Informationen zum Analysieren des Datenverbrauchs mit Azure Monitor-Protokollen finden Sie unter [Verwalten von Verbrauch und Kosten](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="update-classifications"></a>Updateklassifizierungen

In der folgenden Tabelle sind die Klassifizierungen definiert, die von der Updateverwaltung für Windows-Updates unterstützt werden.

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates     | Ein Update für ein bestimmtes Problem, mit dem ein entscheidender, nicht sicherheitsrelevanter Fehler behoben wird.        |
|Sicherheitsupdates     | Ein Update für ein produktspezifisches, sicherheitsrelevantes Problem.        |
|Updaterollups     | Eine kumulative Gruppe von Hotfixes, die zur Vereinfachung der Bereitstellung gebündelt sind.        |
|Feature Packs     | Neue Produktfeatures, die nicht im Rahmen eines Produktreleases verteilt werden.        |
|Service Packs     | Eine kumulative Gruppe von Hotfixes, die auf eine Anwendung angewendet werden.        |
|Definitionsupdates     | Ein Update für virenbehaftete oder andere Definitionsdateien.        |
|Tools     | Ein Hilfsprogramm oder Feature, mit dem mindestens eine Aufgabe ausgeführt werden kann.        |
|Aktualisierungen     | Ein Update für eine Anwendung oder Datei, die zurzeit installiert ist.        |

In der nächsten Tabelle sind die für Linux-Updates unterstützten Klassifizierungen definiert.

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates und Sicherheitsupdates     | Updates für ein spezielles oder produktspezifisches, sicherheitsrelevantes Problem.         |
|Andere Updates     | Alle anderen Updates, die nicht kritisch sind oder bei denen es sich nicht um Sicherheitsupdates handelt.        |

> [!NOTE]
> Die Updateklassifizierung für Linux-Computer ist nur verfügbar, wenn sie in den unterstützten öffentlichen Azure-Cloudregionen verwendet wird. Es gibt keine Klassifizierung von Linux-Updates bei Verwendung der Updateverwaltung in den folgenden Regionen mit nationalen Clouds:
>
>* Azure US Government
>* 21Vianet in China
>
> Anstatt klassifiziert zu werden, werden Updates unter der Kategorie **Andere Updates** gemeldet.
>
> Die Updateverwaltung verwendet Daten, die von den unterstützten Verteilungen veröffentlicht werden, insbesondere in deren veröffentlichten [OVAL](https://oval.mitre.org/)-Dateien (Open Vulnerability and Assessment Language). Da der Internetzugriff aus diesen nationalen Clouds eingeschränkt ist, kann die Updateverwaltung nicht auf diese Dateien zugreifen.

Für Linux kann die Updateverwaltung bei der Anzeige von Bewertungsdaten dank der Datenanreicherung in der Cloud zwischen kritischen Updates und Sicherheitsupdates in der Cloud unter der Klassifizierung **Sicherheit** und **Sonstiges** unterscheiden. Für das Patchen verwendet die Updateverwaltung Klassifizierungsdaten, die auf dem Computer verfügbar sind. Im Gegensatz zu anderen Distributionen verfügt CentOS in der RTM-Version standardmäßig nicht über diese Informationen. Wenn Sie CentOS-Computer so konfiguriert haben, dass für den folgenden Befehl Sicherheitsdaten zurückgegeben werden, kann die Updateverwaltung basierend auf Klassifizierungen Patchvorgänge ausführen.

```bash
sudo yum -q --security check-update
```

Aktuell steht keine unterstützte Methode zur Verfügung, mit der unter CentOS eine native Verfügbarkeit von Klassifizierungsdaten implementiert werden kann. Für Kunden, die diese Funktion möglicherweise selbst implementiert haben, wird derzeit nur eingeschränkter Support bereitgestellt.

Sie müssen das Plug-In „yum-security“ installieren, um Updates in Version 6 von Red Hat Enterprise zu klassifizieren. Unter Red Hat Enterprise Linux 7 ist das Plug-In bereits Teil von yum selbst, und Sie müssen also nichts installieren. Weitere Informationen finden Sie im folgenden [Knowledge-Artikel](https://access.redhat.com/solutions/10021) zu Red Hat.

Wenn Sie die Ausführung eines Updates auf einem Linux-Computer planen, um z. B. nur Updates zu installieren, die der Klassifizierung **Sicherheit** entsprechen, unterscheiden sich die installierten Updates von den Updates, die dieser Klassifizierung entsprechen, oder sie sind eine Teilmenge davon. Wenn eine Bewertung der ausstehenden Betriebssystemupdates für Ihren Linux-Computer durchgeführt wird, werden [OVAL](https://oval.mitre.org/)-Dateien (Open Vulnerability and Assessment Language), die vom Linux-Distributionsanbieter bereitgestellt werden, von der Updateverwaltung für die Klassifizierung verwendet.

Die Kategorisierung erfolgt für Linux-Updates in **Sicherheit** oder **Sonstiges** und basiert auf den OVAL-Dateien, die Updates enthalten, die Sicherheitsprobleme oder Sicherheitsrisiken beheben. Wenn aber der Zeitplan für Updates ausgeführt wird, wird er auf dem Linux-Computer ausgeführt, wobei er den geeigneten Paket-Manager wie YUM, APT oder ZYPPER verwendet, um diese zu installieren. Der Paket-Manager für die Linux-Distribution verfügt möglicherweise über einen anderen Mechanismus zum Klassifizieren von Updates, sodass sich die Ergebnisse von denen, die die Updateverwaltung über die OVAL-Dateien erzielt hat, unterscheiden können. Informationen dazu, wie Sie den Computer manuell überprüfen und verstehen, welche Updates vom Paket-Manager als sicherheitsrelevant eingestuft werden, finden Sie unter [Problembehandlung für die Bereitstellung von Linux-Updates](../troubleshoot/update-management.md#updates-linux-installed-different).

>[!NOTE]
> Die Bereitstellung von Updates nach Updateklassifizierung funktioniert möglicherweise nicht ordnungsgemäß für Linux-Distributionen, die von der Updateverwaltung unterstützt werden. Dies ist das Ergebnis eines Problems mit dem Namensschema der OVAL-Datei, das verhindert, dass die Updateverwaltung Klassifizierungen basierend auf Filterregeln ordnungsgemäß abgleicht. Aufgrund der unterschiedlichen Logik, die bei Bewertungen von Sicherheitsupdates verwendet wird, können sich die Ergebnisse von den bei der Bereitstellung angewendeten Sicherheitsupdates unterscheiden. Wenn Sie die Klassifizierung als **Kritisch** und **Sicherheit** festgelegt haben, funktioniert die Updatebereitstellung wie erwartet. Nur die *Klassifizierung von Updates* während einer Bewertung ist betroffen.
>
> Die Updateverwaltung für Windows Server-Computer bleibt davon unberührt, und die Updateklassifizierung und Bereitstellungen bleiben unverändert.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrieren der Updateverwaltung in Configuration Manager

Kunden, die in Microsoft Endpoint Configuration Manager investiert haben, um PCs, Server und mobile Geräte zu verwalten, profitieren auch von der Leistungsstärke und dem Funktionsumfang von Configuration Manager bei der Verwaltung von Softwareupdates. Informationen, wie Sie die Updateverwaltung in Configuration Manager integrieren, finden Sie unter [Integrieren der Updateverwaltung in Windows Endpoint Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Drittanbieterupdates unter Windows

In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu aktualisieren, entweder WSUS oder Windows Update. Mit Tools wie [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) können Sie benutzerdefinierte Updates mit WSUS importieren und veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern aktualisieren. Informationen zum Konfigurieren von Updates Publisher finden Sie unter [Installieren von Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="next-steps"></a>Nächste Schritte

* Bevor Sie die Updateverwaltung aktivieren und verwenden, lesen Sie [Planen Ihrer Bereitstellung der Updateverwaltung](plan-deployment.md).

* Lesen Sie häufig gestellte Fragen zur Updateverwaltung unter [Häufig gestellte Fragen zu Azure Automation](../automation-faq.md).
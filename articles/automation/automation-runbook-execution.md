---
title: Ausführen von Runbooks in Azure Automation
description: Dieser Artikel enthält eine Übersicht über die Verarbeitung von Runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 09/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 11a4892a6cd9a1ddda9cf5857165dd9b52e1392b
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371857"
---
# <a name="runbook-execution-in-azure-automation"></a>Ausführen von Runbooks in Azure Automation

Die Prozessautomatisierung in Azure Automation ermöglicht Ihnen das Erstellen und Verwalten von PowerShell-, PowerShell Workflow- und grafischen Runbooks. Einzelheiten finden Sie unter [Azure Automation-Runbooks](automation-runbook-types.md). 

Automation führt Ihre Runbooks auf der Grundlage der in ihnen definierten Logik aus. Wenn ein Runbook unterbrochen wird, startet es am Anfang neu. Dieses Verhalten erfordert, dass Sie Runbooks schreiben, die einen Neustart unterstützen, wenn vorübergehende Probleme auftreten.

Wenn Sie ein Runbook in Azure Automation starten, wird ein Auftrag erstellt, bei dem es sich um eine einzelne Ausführungsinstanz des Runbooks handelt. Jeder Auftrag erhält Zugriff auf Azure-Ressourcen, indem er eine Verbindung mit Ihrem Azure-Abonnement herstellt. Der Auftrag hat nur Zugriff auf Ressourcen in Ihrem Rechenzentrum, wenn aus der öffentlichen Cloud auf diese Ressourcen zugegriffen werden kann.

Azure Automation weist einen Worker zu, um jeden Auftrag während der Runbookausführung auszuführen. Wenngleich Worker von vielen Automation-Konten gemeinsam genutzt werden, sind die Aufträge von verschiedenen Automation-Konten voneinander isoliert. Sie können nicht steuern, welcher Workerdienst Ihre Auftragsanforderung verarbeitet.

Wenn Sie die Liste der Runbooks im Azure-Portal einsehen, wird der Status jedes Auftrags angezeigt, der für jedes Runbook gestartet wurde. Azure Automation speichert Auftragsprotokolle maximal 30 Tage.

Im folgenden Diagramm wird der Lebenszyklus eines Runbookauftrags für [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks), [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) und [grafische Runbooks](automation-runbook-types.md#graphical-runbooks) gezeigt.

![Auftragsstatus – PowerShell-Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Ausführungsumgebung für Runbooks

Runbooks in Azure Automation können entweder in einer Azure-Sandbox oder einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausgeführt werden. 

Wenn Runbooks für das Authentifizieren und Ausführen von Ressourcen in Azure entworfen sind, werden sie in einer Azure-Sandbox ausgeführt. Azure Automation weist einen Worker zu, um jeden Auftrag während der Runbookausführung in der Sandbox auszuführen. Wenngleich Worker von vielen Automation-Konten gemeinsam genutzt werden, sind die Aufträge von verschiedenen Automation-Konten voneinander isoliert.  Aufträge, die die gleiche Sandbox verwenden, werden durch die Ressourceneinschränkungen der Sandbox gebunden. Interaktive Vorgänge werden von der Azure-Sandboxumgebung nicht unterstützt. Dadurch wird der Zugriff auf alle prozessexternen COM-Server verhindert. Ebenso wird die Ausführung von [WMI-Aufrufen](/windows/win32/wmisdk/wmi-architecture) an den Win32-Anbieter in Ihrem Runbook nicht unterstützt.  Diese Szenarios werden nur unterstützt, wenn das Runbook auf einer Instanz von Windows Hybrid Runbook Worker ausgeführt wird.

Sie können auch einen [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) verwenden, um Runbooks direkt auf dem Computer, der die Rolle hostet, und mit lokalen Ressourcen in der Umgebung auszuführen. Azure Automation speichert und verwaltet Runbooks und übermittelt sie dann an einen oder mehrere zugewiesene Computer.

Durch das Aktivieren von Azure Firewall für [Azure Storage](../storage/common/storage-network-security.md), [Azure Key Vault](../key-vault/general/network-security.md) oder [Azure SQL](../azure-sql/database/firewall-configure.md) wird der Zugriff von Azure Automation-Runbooks für diese Dienste blockiert. Der Zugriff wird auch dann blockiert, wenn die Firewallausnahme zum Zulassen vertrauenswürdiger Microsoft-Dienste aktiviert ist, da Automation nicht zur Liste der vertrauenswürdigen Dienste gehört. Bei aktivierter Firewall kann der Zugriff nur mithilfe eines Hybrid Runbook Workers und eines [VNT-Dienstendpunkts](../virtual-network/virtual-network-service-endpoints-overview.md) erfolgen.

>[!NOTE]
>Für die Ausführung auf einem Linux Hybrid Runbook Worker müssen Ihre Skripts signiert sein, und der Worker muss richtig konfiguriert sein. Alternativ können Sie auch die [Signaturüberprüfung deaktivieren](automation-linux-hrw-install.md#turn-off-signature-validation).

In der folgenden Tabelle sind einige Runbook-Ausführungsaufgaben mit der jeweils empfohlenen Ausführungsumgebung aufgelistet.

|Aufgabe|Empfehlung|Notizen|
|---|---|---|
|Integration in Azure-Ressourcen|Azure-Sandbox|Bei Hosting in Azure ist die Authentifizierung einfacher. Bei der Verwendung einer Hybrid Runbook Worker-Instanz auf einer Azure-VM können Sie die [Runbook-Authentifizierung mit verwalteten Identitäten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) nutzen.|
|Optimale Leistung zum Verwalten von Azure-Ressourcen erzielen|Azure-Sandbox|Das Skript wird in der gleichen Umgebung ausgeführt, was zu weniger Latenz führt.|
|Betriebskosten minimieren|Azure-Sandbox|Kein Computemehraufwand, keine VM erforderlich.|
|Zeitintensives Skript ausführen|Hybrid Runbook Worker|Für Azure-Sandboxes gelten [Ressourcenlimits](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagieren mit lokalen Diensten|Hybrid Runbook Worker|Ermöglicht den direkten Zugriff auf den Hostcomputer oder die Ressourcen in anderen Cloudumgebungen oder der lokalen Umgebung. |
|Software und ausführbare Dateien von Drittanbietern erforderlich.|Hybrid Runbook Worker|Sie verwalten das Betriebssystem und können Software installieren.|
|Datei oder Ordner mit einem Runbook überwachen|Hybrid Runbook Worker|Wenden Sie einen [Watcher-Task](./automation-scenario-using-watcher-task.md) auf einen Hybrid Runbook Worker an.|
|Ressourcenintensives Skript ausführen|Hybrid Runbook Worker| Für Azure-Sandboxes gelten [Ressourcenlimits](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Module mit spezifischen Anforderungen verwenden| Hybrid Runbook Worker|Beispiele:</br> WinSCP: Abhängigkeit von „winscp.exe“ </br> IIS-Verwaltung: Abhängigkeit zum Aktivieren oder Verwalten von IIS|
|Modul mit einem Installationsprogramm installieren|Hybrid Runbook Worker|Module für Sandbox müssen Kopieren unterstützen.|
|Verwenden von Runbooks oder Modulen, die eine andere .NET Framework-Version als 4.7.2 erfordern|Hybrid Runbook Worker|Azure-Sandboxes unterstützen .NET Framework 4.7.2. Upgrades auf eine andere Version werden nicht unterstützt.|
|Skripts ausführen, für die eine Rechteerweiterung erforderlich ist|Hybrid Runbook Worker|Sandboxes lassen keine Rechteerweiterung zu. Mit einem Hybrid Runbook Worker können Sie die Benutzerkontensteuerung deaktivieren und [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) verwenden, wenn Sie den Befehl ausführen, für den eine Rechteerweiterung erforderlich ist.|
|Ausführen von Skripts, die Zugriff auf die Windows-Verwaltungsinstrumentation (WMI) benötigen|Hybrid Runbook Worker|Aufträge, die in Sandboxes in der Cloud ausgeführt werden, haben keinen Zugriff auf WMI-Anbieter. |

## <a name="temporary-storage-in-a-sandbox"></a>Temporäre Speicherung in einer Sandbox

Falls Sie im Rahmen Ihrer Runbooklogik temporäre Dateien erstellen müssen, können Sie den Ordner „Temp“ (`$env:TEMP`) in der Azure-Sandbox für Runbooks verwenden, die in Azure ausgeführt werden. Die einzige Einschränkung besteht darin, dass Sie nicht mehr als 1 GB Speicherplatz nutzen können, weil dies das zulässige Kontingent pro Sandbox ist. Bei der Arbeit mit PowerShell-Workflows kann dieses Szenario zu einem Problem führen, weil für PowerShell-Workflows Prüfpunkte verwendet werden und unter Umständen versucht wird, das Skript in einer anderen Sandbox auszuführen.

Mit der Hybrid-Sandbox können Sie `C:\temp` basierend auf der Verfügbarkeit des Speichers auf einem Hybrid Runbook Worker verwenden. Für Azure-VMs wird aber empfohlen, den [temporären Datenträger](../virtual-machines/managed-disks-overview.md#temporary-disk) unter Windows oder Linux nicht für Daten zu verwenden, die dauerhaft gespeichert werden müssen.

## <a name="resources"></a>Ressourcen

Ihre Runbooks müssen Logik für die Verarbeitung von [Ressourcen](/rest/api/resources/resources) enthalten, z. B. VMs, Netzwerk und Ressourcen im Netzwerk. Ressourcen sind an ein Azure-Abonnement gebunden, und für Runbooks werden für den Zugriff auf Ressourcen die entsprechenden Anmeldeinformationen benötigt. Ein Beispiel für die Verarbeitung von Ressourcen in einem Runbook finden Sie unter [Behandeln von Ressourcen](manage-runbooks.md#handle-resources).

## <a name="security"></a>Sicherheit

In Azure Automation wird [Microsoft Defender für Cloud](../defender-for-cloud/defender-for-cloud-introduction.md) genutzt, um Ihre Ressourcen zu schützen und Kompromittierungen von Linux-Systemen zu erkennen. Dieser Schutz gilt unabhängig davon, ob sich die Ressourcen in Azure befinden, für Ihre gesamten Workloads. Weitere Informationen finden Sie unter [Einführen in die Authentifizierung in Azure Automation](automation-security-overview.md).

Von Defender für Cloud werden Benutzer, die auf einer VM alle Skripts (signiert oder unsigniert) ausführen können, mit Einschränkungen versehen. Wenn Sie ein Benutzer mit Rootzugriff auf eine VM sind, müssen Sie für den Computer explizit eine digitale Signatur konfigurieren oder diesen ausschalten. Andernfalls können Sie ein Skript zum Anwendungen von Betriebssystemupdates erst ausführen, nachdem Sie ein Automation-Konto erstellt und das entsprechende Feature aktiviert haben.

## <a name="subscriptions"></a>Abonnements

Bei einem Azure-[Abonnement](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) handelt es sich um eine Vereinbarung mit Microsoft über die kostenpflichtige Nutzung von mindestens einem cloudbasierten Dienst. Bei Azure Automation ist jedes Abonnement mit einem Azure Automation-Konto verknüpft, und Sie können unter dem Konto [mehrere Abonnements erstellen](manage-runbooks.md#work-with-multiple-subscriptions).

## <a name="credentials"></a>Anmeldeinformationen

Für ein Runbook werden für den Zugriff auf Ressourcen die entsprechenden [Anmeldeinformationen](shared-resources/credentials.md) benötigt – unabhängig davon, ob es sich um Azure- oder Drittanbietersysteme handelt. Diese Anmeldeinformationen werden in Azure Automation, Key Vault usw. gespeichert.  

## <a name="azure-monitor"></a>Azure Monitor

In Azure Automation wird [Azure Monitor](../azure-monitor/overview.md) verwendet, um die Computervorgänge zu überwachen. Für die Vorgänge werden ein Log Analytics-Arbeitsbereich und ein [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md) benötigt.

### <a name="log-analytics-agent-for-windows"></a>Log Analytics-Agent für Windows

Der [Log Analytics-Agent für Windows](../azure-monitor/agents/agent-windows.md) kann mit Azure Monitor genutzt werden, um Windows-VMs und physische Computer zu verwalten. Die Computer können entweder in Azure oder in einer anderen Umgebung, z. B. einem lokalen Rechenzentrum, ausgeführt werden.

>[!NOTE]
>Der Log Analytics-Agent für Windows wurde früher als Microsoft Monitoring Agent (MMA) bezeichnet.

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-Agent für Linux

Der [Log Analytics-Agent für Linux](../azure-monitor/agents/agent-linux.md) ähnelt in der Funktionsweise dem Agent für Windows, aber es werden Linux-Computer mit Azure Monitor verbunden. Der Agent wird mit bestimmten Dienstkonten installiert, die Befehle ausführen, die root-Berechtigungen erfordern. Weitere Informationen finden Sie unter [Dienstkonten](./automation-hrw-run-runbooks.md#service-accounts).

Das Protokoll des Log Analytics-Agents befindet sich unter `/var/opt/microsoft/omsagent/log/omsagent.log`.

## <a name="runbook-permissions"></a>Runbookberechtigungen

Für ein Runbook werden Berechtigungen in Form von Anmeldeinformationen für die Azure-Authentifizierung benötigt. Siehe [Übersicht über die Azure Automation-Kontoauthentifizierung](automation-security-overview.md).

## <a name="modules"></a>Module

Azure Automation enthält die folgenden PowerShell-Module:

* Orchestrator.AssetManagement.Cmdlets: Enthält mehrere interne Cmdlets, die nur verfügbar sind, wenn Sie Runbooks in der Azure-Sandboxumgebung oder auf einem Windows-Hybrid Runbook Worker ausführen. Diese Cmdlets sind dafür konzipiert, anstelle von Azure PowerShell-Cmdlets verwendet zu werden, um mit Ihren Automation-Kontoressourcen zu interagieren.
* Az.Automation: Das empfohlene PowerShell-Modul für die Interaktion mit Azure Automation, das das AzureRM Automation-Modul ersetzt Das Az.Automation-Modul ist beim Erstellen eines Automation-Kontos nicht automatisch enthalten, und muss manuell importiert werden. 
* AzureRM.Automation: Wird standardmäßig installiert, wenn Sie ein Automation-Konto erstellen 

Außerdem werden installierbare Module unterstützt, die auf den Cmdlets basieren, die für Ihre Runbooks und DSC-Konfigurationen erforderlich sind. Details zu den Modulen, die für Ihre Runbooks und DSC-Konfigurationen verfügbar sind, finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Zertifikate

Bei Azure Automation werden [Zertifikate](shared-resources/certificates.md) für die Azure-Authentifizierung verwendet bzw. den Azure- oder Drittanbieterressourcen hinzugefügt. Die Zertifikate werden für den Zugriff durch Runbooks und DSC-Konfigurationen sicher gespeichert.

Für Ihre Runbooks können selbstsignierte Zertifikate verwendet werden, die nicht von einer Zertifizierungsstelle signiert wurden. Weitere Informationen finden Sie unter [Erstellen eines neuen Zertifikats](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Aufträge

Azure Automation unterstützt eine Umgebung zum Ausführen von Aufträgen desselben Automation-Kontos. Für ein einzelnes Runbook können viele Aufträge gleichzeitig ausgeführt werden. Je mehr Aufträge Sie zur gleichen Zeit ausführen, desto häufiger können diese an dieselbe Sandbox weitergeleitet werden. 

Im selben Sandboxprozess ausgeführte Aufträge können sich gegenseitig beeinflussen. Ein Beispiel ist das Ausführen des Cmdlets [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount). Bei Ausführen dieses Cmdlets wird jeder Runbookauftrag im gemeinsam genutzten Sandboxprozess getrennt. Ein Beispiel für die Vorgehensweise bei diesem Szenario finden Sie unter [Gleichzeitige Aufträge verhindern](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>PowerShell-Aufträge, die in einem Runbook gestartet werden, das in einer Azure-Sandbox ausgeführt wird, können möglicherweise nicht im vollständigen [PowerShell-Sprachmodus](/powershell/module/microsoft.powershell.core/about/about_language_modes) ausgeführt werden.

### <a name="job-statuses"></a>Auftragsstatuswerte

Die folgende Tabelle beschreibt die für einen Auftrag möglichen Status. Im Azure-Portal können Sie eine Statuszusammenfassung aller Runbookaufträge anzeigen oder weitere Details zu einem bestimmten Runbookauftrag aufführen. Sie können auch die Integration mit Ihrem Log Analytics-Arbeitsbereich konfigurieren, um den Status von Runbookaufträgen und Auftragsdatenströmen weiterzuleiten. Weitere Informationen zur Integration mit Azure Monitor-Protokollen finden Sie unter [Weiterleiten von Auftragsstatus und Auftragsdatenströmen von Automation an Log Analytics](automation-manage-send-joblogs-log-analytics.md). Ein Beispiel für die Verwendung von Status in einem Runbook finden Sie unter [Abrufen des Auftragsstatus](manage-runbooks.md#obtain-job-statuses).

| Status | BESCHREIBUNG |
|:--- |:--- |
| Wird aktiviert |Der Auftrag wird aktiviert. |
| Abgeschlossen |Der Auftrag wurde erfolgreich abgeschlossen. |
| Fehler |Bei einem grafischen oder PowerShell-Workflow-Runbook ist die Kompilierung fehlgeschlagen. Ein PowerShell-Runbook konnte nicht gestartet werden, oder für den Auftrag ist eine Ausnahme aufgetreten. Weitere Informationen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md).|
| Fehler, auf Ressourcen wird gewartet |Beim Auftrag ist ein Fehler aufgetreten, da der Auftrag dreimal den Grenzwert für die [gleichmäßige Verteilung](#fair-share) erreicht hat und jedes Mal vom gleichen Prüfpunkt oder vom Anfang des Runbooks gestartet wurde. |
| In Warteschlange |Der Auftrag wartet darauf, dass Ressourcen für einen Automation-Worker verfügbar werden, damit er gestartet werden kann. |
| Wird fortgesetzt |Das System setzt den Auftrag fort, nachdem er angehalten wurde. |
| Wird ausgeführt |Der Auftrag wird ausgeführt. |
| Wird ausgeführt, auf Ressourcen wird gewartet |Der Auftrag wurde entladen, da er den Grenzwert für die gleichmäßige Verteilung erreicht hat. Er wird in Kürze vom letzten Prüfpunkt wiederaufgenommen. |
| Wird gestartet |Der Auftrag wurde einem Worker zugewiesen, und das System startet ihn. |
| Beendet |Der Auftrag wurde vom Benutzer beendet, bevor er abgeschlossen wurde. |
| Wird beendet |Das System beendet den Auftrag. |
| Ausgesetzt |Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md). Der Auftrag wurde vom Benutzer, vom System oder von einem Befehl im Runbook angehalten. Wenn ein Runbook keinen Prüfpunkt aufweist, wird der Vorgang am Anfang gestartet. Wenn ein Prüfpunkt vorhanden ist, kann es erneut starten und den Vorgang bei seinem letzten Prüfpunkt fortsetzen. Das Runbook wird nur vom System angehalten, wenn eine Ausnahme auftritt. Standardmäßig ist die Variable `ErrorActionPreference` auf „Continue“ festgelegt. Dies bedeutet, dass der Auftrag bei einem Fehler weiter ausgeführt wird. Wenn diese Einstellungsvariable auf „Stop“ festgelegt wird, wird der Auftrag bei einem Fehler angehalten.  |
| Wird angehalten |Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md). Das System versucht, den Auftrag auf Anforderung des Benutzers anzuhalten. Das Runbook muss den nächsten Prüfpunkt erreichen, bevor es angehalten werden kann. Wenn der letzte Prüfpunkt bereits passiert wurde, wird das Runbook abgeschlossen, bevor es angehalten werden kann. |

## <a name="activity-logging"></a>Aktivitätsprotokollierung

Bei der Ausführung von Runbooks in Azure Automation werden Details in ein Aktivitätsprotokoll für das Automation-Konto geschrieben. Ausführliche Informationen zur Verwendung des Protokolls finden Sie unter [Abrufen von Details aus dem Aktivitätsprotokoll](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Ausnahmen

In diesem Abschnitt werden einige Möglichkeiten zur Behandlung von Ausnahmen oder zeitweiligen Problemen in Ihren Runbooks vorgestellt. Ein Beispiel hierfür ist eine WebSocket-Ausnahme. Durch die richtige Ausnahmebehandlung wird verhindert, dass vorübergehende Netzwerkausfälle zu Fehlern bei ihren Runbooks führen.

### <a name="erroractionpreference"></a>ErrorActionPreference

Die Variable [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) gibt an, wie PowerShell auf einen Fehler ohne Abbruch reagiert. Fehler mit Abbruch führen immer zu einer Beendigung und sind nicht von `ErrorActionPreference` betroffen.

Bei Verwendung von `ErrorActionPreference` für das Runbook verhindert ein Fehler, der normalerweise nicht zu einem Abbruch führt (z. B. `PathNotFound` im Cmdlet [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem)), dass die Ausführung des Runbooks abgeschlossen wird. Im folgenden Beispiel wird die Verwendung von `ErrorActionPreference` veranschaulicht. Der abschließende Befehl [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) wird nie ausgeführt, da das Skript beendet wird.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wird in PowerShell-Skripts verwendet, um Fehler mit Abbruch zu behandeln. Das Skript kann diesen Mechanismus nutzen, um spezifische oder allgemeine Ausnahmen abzufangen. Die `catch`-Anweisung sollte zum Nachverfolgen oder Behandeln von Fehlern verwendet werden. Im folgenden Beispiel wird versucht, eine Datei herunterzuladen, die es nicht gibt. Die Ausnahme `System.Net.WebException` wird abgefangen, und bei anderen Ausnahmen wird der letzte Wert zurückgegeben.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

Mit [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) können Sie einen Fehler mit Abbruch generieren. Dies kann nützlich sein, wenn Sie in einem Runbook eigene Logik definieren. Wenn das Skript ein Kriterium für die Beendigung erfüllt, kann es zum Beenden die `throw`-Anweisung verwenden. Das folgende Beispiel verwendet diese Anweisung, um einen erforderlichen Funktionsparameter zu zeigen.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

In Ihren Runbooks müssen Fehler behandelt werden. Azure Automation unterstützt zwei Arten von PowerShell-Fehlern: mit Abbruch und ohne Abbruch. 

Bei Fehlern mit Abbruch wird die Runbookausführung beendet, sobald sie auftreten. Das Runbook wird mit dem Auftragsstatus „Fehler“ angehalten.

Fehler ohne Abbruch ermöglichen einem Skript, auch nach ihrem Auftreten fortzufahren. Ein Beispiel für einen Fehler ohne Abbruch ist ein Fehler, der auftritt, wenn ein Runbook das Cmdlet `Get-ChildItem` mit einem nicht vorhandenen Pfad verwendet. PowerShell erkennt, dass der Pfad nicht vorhanden ist, löst einen Fehler aus und fährt mit dem nächsten Ordner fort. Vom Fehler wird der Status des Runbookauftrags in diesem Fall nicht auf „Fehler“ festgelegt, sodass der Auftrag unter Umständen sogar abgeschlossen wird. Um ein Runbook zu veranlassen, bei einem Fehler ohne Abbruch anzuhalten, können Sie `ErrorAction Stop` für das Cmdlet verwenden.

## <a name="calling-processes"></a>Aufrufen von Prozessen

In Azure-Sandboxes ausgeführte Runbooks unterstützen keine aufrufenden Prozesse, wie z. B. ausführbare Dateien (**EXE**-Dateien) oder Unterprozesse. Der Grund dafür ist, dass es sich bei einer Azure-Sandbox um einen gemeinsam genutzten Prozess handelt, der in einem Container ausgeführt wird und möglicherweise nicht auf alle zugrunde liegenden APIs Zugriff hat. Für Szenarien, die Software von Drittanbietern oder Aufrufe von Unterprozessen erfordern, sollten Sie das Runbook in einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausführen.

## <a name="device-and-application-characteristics"></a>Geräte- und Anwendungsmerkmale

Runbookaufträge in Azure-Sandboxes haben keinen Zugriff auf Geräte- oder Anwendungsmerkmale. Die am häufigsten verwendete API zur Abfrage von Leistungsmetriken unter Windows ist WMI, wobei einige der gängigen Metriken die Arbeitsspeicher- und CPU-Auslastung sind. Es spielt jedoch keine Rolle, welche API verwendet wird, da in der Cloud ausgeführte Aufträge keinen Zugriff auf die Microsoft-Implementierung von Web-Based Enterprise Management (WBEM) haben. Diese Plattform beruht auf dem Common Information Model (CIM), das die Branchenstandards für die Definition von Geräte- und Anwendungseigenschaften vorgibt.

## <a name="webhooks"></a>webhooks

Externe Dienste wie Azure DevOps Services und GitHub, können ein Runbook in Azure Automation starten. Für diese Art des Starts verwendet der Dienst einen [Webhook](automation-webhooks.md) über eine einzige HTTP-Anforderung. Die Verwendung eines Webhooks ermöglicht den Start von Runbooks ohne Implementierung eines vollständigen Azure Automation-Features.

## <a name="shared-resources"></a><a name="fair-share"></a>Gemeinsam genutzte Ressourcen

Für Azure wird das Konzept der gleichmäßigen Verteilung (Fair Share) verwendet, damit Ressourcen auf alle Runbooks der Cloud verteilt werden. Bei der gleichmäßigen Verteilung werden von Azure alle Aufträge vorübergehend entladen bzw. angehalten, deren Ausführung bereits länger als drei Stunden dauert. Aufträge für [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) und [Python-Runbooks](automation-runbook-types.md#python-runbooks) werden beendet und nicht neu gestartet, und der Auftragsstatus ändert sich in „Stopped“.

Für zeitintensive Azure Automation-Aufgaben sollten Sie einen [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) verwenden. Hybrid Runbook Worker unterliegen nicht der gleichmäßigen Verteilung und keiner Einschränkung hinsichtlich der Ausführungszeit eines Runbooks. Die anderen [Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) für Aufträge gelten sowohl für Azure-Sandboxes als auch für Hybrid Runbook Workers. Für Hybrid Runbook Worker gilt zwar nicht die dreistündige Begrenzung für die gleichmäßige Verteilung, aber Sie sollten Runbooks für die Ausführung auf den Workern entwickeln, die einen Neustart nach unerwarteten lokalen Infrastrukturproblemen unterstützen.

Eine weitere Möglichkeit ist das Optimieren eines Runbooks durch Verwendung untergeordneter Runbooks. Beispielsweise kann Ihr Runbook die gleiche Funktion auf mehreren Ressourcen durchlaufen, z. B. bei einem Datenbankvorgang in mehreren Datenbanken. Sie können diese Funktion in ein [untergeordnetes Runbook](automation-child-runbooks.md) verschieben und veranlassen, dass Ihr Runbook es mit [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) aufruft. Untergeordnete Runbooks werden in separaten Prozessen parallel ausgeführt.

Das Verwenden untergeordneter Runbooks führt dazu, dass die Gesamtdauer der Verarbeitung für das übergeordnete Runbook verringert wird. Ihr Runbook kann mit dem Cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) den Auftragsstatus für ein untergeordnetes Runbook überprüfen, wenn nach Abschluss des untergeordneten Runbooks noch Vorgänge erfolgen müssen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den ersten Schritten mit einem PowerShell-Runbook finden Sie unter [Tutorial: Erstellen eines PowerShell-Runbooks](./learn/powershell-runbook-managed-identity.md).
* Informationen zur Arbeit mit Runbooks finden Sie unter [Verwalten eines Runbooks in Azure Automation](manage-runbooks.md).
* Details zu PowerShell finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation#automation).

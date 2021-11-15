---
title: SQL Assessment
description: Identifizieren Sie Leistungsprobleme, und bewerten Sie, ob Ihre SQL Server-VM so konfiguriert ist, dass sie den bewährten Methoden folgt, indem Sie die SQL-Bewertungsfunktion im Azure-Portal verwenden.
author: ebruersan
ms.author: ebrue
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/02/2021
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0c163f3ea101ec47eda25bc81f78033893cad6a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500277"
---
# <a name="sql-assessment-for-sql-server-on-azure-vms-preview"></a>SQL-Bewertung für SQL Server für Azure-VMs (Vorschau)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Das SQL-Bewertungsfeature im Azure-Portal identifiziert mögliche Leistungsprobleme und wertet aus, ob Ihre SQL Server-Instanz für Azure Virtual Machines (VMs) so konfiguriert ist, dass bewährte Methoden befolgt werden. Dabei wird der [umfangreiche Regelsatz](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-assessment-api/DefaultRuleset.csv) verwendet, der von der [SQL-Bewertungs-API](/sql/sql-assessment-api/sql-assessment-api-overview) bereitgestellt wird. 

Das SQL Bewertungsfeature befindet sich derzeit in der Vorschau.

## <a name="overview"></a>Übersicht

Sobald die SQL-Bewertungsfunktion aktiviert ist, werden Ihre SQL Server-Instanz und -Datenbanken gescannt, um Empfehlungen zu Indizes, veralteten Funktionen, aktivierten oder fehlenden Ablaufverfolgungsflags, Statistiken usw. bereitzustellen. Die Empfehlungen werden auf der [SQL VM-Verwaltungsseite](manage-sql-vm-portal.md) des [Azure-Portals](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) angezeigt. 


Bewertungsergebnisse werden mithilfe von [Microsoft Monitoring Agent (MMA)](../../../azure-monitor/agents/log-analytics-agent.md) in Ihren [Log Analytics-Arbeitsbereich](../../../azure-monitor/logs/quick-create-workspace.md) hochgeladen. Wenn Ihre VM bereits für die Verwendung von Log Analytics konfiguriert ist, verwendet das SQL-Bewertungsfeature die vorhandene Verbindung.  Andernfalls wird die MMA-Erweiterung auf der SQL Server-VM installiert und mit dem angegebenen Log Analytics-Arbeitsbereich verbunden.

Die Ausführungszeit der Bewertung hängt von Ihrer Umgebung ab (Anzahl der Datenbanken, Objekte usw.) und kann von einigen Minuten bis zu einer Stunde dauern. Die Größe des Bewertungsergebnisses hängt ebenfalls von Ihrer Umgebung ab. Die Bewertung wird für Ihre Instanz und alle Datenbanken auf dieser Instanz ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Um das SQL-Bewertungsfeature verwenden zu können, müssen die folgenden Voraussetzungen erfüllt sein: 

- Ihre SQL Server-VM muss bei der [IaaS-Erweiterung von SQL Server im vollständigen Modus](sql-agent-extension-manually-register-single-vm.md#full-mode) registriert sein. 
- Es muss ein [Log Analytics-Arbeitsbereich](../../../azure-monitor/logs/quick-create-workspace.md) im gleichen Abonnement wie Ihre SQL Server-VM vorhanden sein, in den Bewertungsergebnisse hochgeladen werden. 
- Die SQL Server-Version muss 2012 oder höher sein.


## <a name="enable"></a>Aktivieren

Um SQL-Bewertungen zu aktivieren, führen Sie die folgenden Schritte aus: 

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer [SQL Server-VM-Ressource](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie unter **Einstellungen** die Option **SQL-Bewertungen** aus. 
1. Wählen Sie **SQL-Bewertungen aktivieren** oder **Konfiguration** aus, um zur Seite **Konfiguration** zu navigieren. 
1. Aktivieren Sie das Kontrollkästchen **SQL-Bewertungen aktivieren**, und geben Sie Folgendes an:
    1. Den [Log Analytics-Arbeitsbereich](../../../azure-monitor/logs/quick-create-workspace.md), in den Bewertungen hochgeladen werden. Wenn die SQL Server-VM zuvor noch keinem Arbeitsbereich zugeordnet wurde, wählen Sie in der Dropdownliste einen vorhandenen Arbeitsbereich im Abonnement aus. Andernfalls ist der zuvor zugeordnete Arbeitsbereich bereits mit Daten aufgefüllt.  
    1. Den **Zeitplan für die Ausführung**. Sie können Bewertungen nach Bedarf oder automatisch gemäß einem Zeitplan ausführen. Wenn Sie einen Zeitplan auswählen, geben Sie die Häufigkeit (wöchentlich oder monatlich), den Wochentag, die Wiederholung (alle 1 bis 6 Wochen) und die Tageszeit an, zu der Ihre Bewertungen gestartet werden sollen (lokal zur VM-Zeit). 
1. Wählen Sie **Übernehmen** aus, um Ihre Änderungen zu speichern und den Microsoft Monitoring Agent auf Ihrer SQL Server-VM bereitzustellen, sofern er noch nicht bereitgestellt wurde. Eine Benachrichtigung des Azure-Portals informiert Sie, sobald die SQL-Bewertungsfunktion für Ihre SQL Server-VM bereit ist. 
    

## <a name="assess-sql-server-vm"></a>Bewerten der SQL Server-VM

Die Ausführung von Bewertungen erfolgt:
- Nach einem Zeitplan
- On-Demand-Streaming

### <a name="run-scheduled-assessment"></a>Ausführen einer geplanten Bewertung

Wenn Sie auf dem Konfigurationsblatt einen Zeitplan festlegen, wird eine Bewertung automatisch zum angegebenen Datum und zur angegebenen Uhrzeit ausgeführt. Wählen Sie **Konfiguration** aus, um Ihren Bewertungszeitplan zu ändern. Sobald Sie einen neuen Zeitplan bereitstellen, wird der vorherige Zeitplan überschrieben.  

### <a name="run-on-demand-assessment"></a>Ausführen einer Bewertung bei Bedarf

Nachdem das SQL-Bewertungsfeature für Ihre SQL Server-VM aktiviert wurde, ist es möglich, bei Bedarf eine Bewertung auszuführen. Wählen Sie dazu **Bewertung ausführen** auf dem Blatt „SQL-Bewertung“ auf der Seite [„SQL Server-VM-Ressource“ des Azure-Portals](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) aus.


## <a name="view-results"></a>Anzeigen der Ergebnisse

Im Abschnitt **Bewertungsergebnisse** der Seite **SQL-Bewertungen** wird eine Liste der letzten Bewertungsausführungen angezeigt. In jeder Zeile werden die Startzeit einer Ausführung und der Status (geplant, wird ausgeführt, Ergebnisse werden hochgeladen, abgeschlossen oder Fehler) angezeigt. Jede Bewertungsausführung besteht aus zwei Teilen: Auswerten Ihrer Instanz und Hochladen der Ergebnisse in Ihren Log Analytics-Arbeitsbereich. Das Statusfeld deckt beide Teile ab. Bewertungsergebnisse werden in Azure-Arbeitsmappen angezeigt. 

Es gibt drei Möglichkeiten, um auf die Bewertungsergebnisse der Azure-Arbeitsmappe zuzugreifen: 
- Wählen Sie die Schaltfläche **Letzte erfolgreiche Bewertung anzeigen** auf der Seite **SQL-Bewertungen** aus.
- Wählen Sie eine abgeschlossene Ausführung im Abschnitt **Bewertungsergebnisse** der Seite **SQL-Bewertungen** aus. 
- Wählen Sie **Bewertungsergebnisse anzeigen** aus den **10 besten Empfehlungen** auf der Seite **Übersicht** Ihrer SQL-VM-Ressource aus. 

Nachdem Sie die Arbeitsmappe geöffnet haben, können Sie die Dropdownliste verwenden, um vorherige Ausführungen auszuwählen. Sie können die Ergebnisse einer einzelnen Ausführung auf der Seite **Ergebnisse** anzeigen oder historische Trends auf der Seite **Trends** überprüfen.

### <a name="results-page"></a>Seite „Ergebnisse“

Auf der Seite **Ergebnisse** werden die Empfehlungen mithilfe von Registerkarten für *Alle, neue, aufgelöste* organisiert. Verwenden Sie diese Registerkarten, um alle Empfehlungen der aktuellen Ausführung, alle neuen Empfehlungen (das Delta aus vorherigen Ausführungen) oder die aufgelösten Empfehlungen aus vorherigen Ausführungen anzuzeigen. Registerkarten helfen Ihnen beim Nachverfolgen des Fortschritts zwischen den Ausführungen. Auf der Registerkarte *Erkenntnisse* werden die am häufigsten auftretenden Probleme und die Datenbanken mit den meisten Problemen identifiziert. Verwenden Sie diese Angaben, um zu entscheiden, wo Sie Ihre Maßnahmen konzentrieren sollten. 

Im Diagramm werden Bewertungsergebnisse in verschiedene Schweregradkategorien unterteilt: hoch, mittel und niedrig. Wählen Sie jede Kategorie aus, um die Liste der Empfehlungen anzuzeigen, oder suchen Sie im Suchfeld nach Schlüsselbegriffen. Es ist am besten, mit den schwerwiegendsten Empfehlungen zu beginnen und die Liste dann absteigend abzuarbeiten.

Sortieren Sie nach **Name** in der Tabelle, um Empfehlungen nach einer bestimmten Datenbank oder Instanz gruppiert anzuzeigen. Mithilfe des Suchfelds können Sie bestimmte Arten von Empfehlungen basierend auf dem Tagwert oder Schlüsselbegriff (z. B. Leistung) anzeigen. Verwenden Sie oben rechts in der Tabelle den Pfeil nach unten, um die Ergebnisse in eine Excel-Datei zu exportieren. 

Der Abschnitt **bestanden** des Diagramms identifiziert Empfehlungen, die Ihr System bereits befolgt. 

Zeigen Sie ausführliche Informationen zu jeder Empfehlung an, indem Sie das Feld **Meldung** auswählen, z. B. eine lange Beschreibung und relevante Onlineressourcen. 
 
### <a name="trends-page"></a>Seite „Trends“

Es gibt drei Diagramme auf der Seite **Trends**, um Änderungen im Laufe der Zeit anzuzeigen: alle Probleme, neue Probleme und behobene Probleme. Die Diagramme helfen Ihnen, Ihren Fortschritt zu erkennen. Im Idealfall sollte die Anzahl der Empfehlungen abnehmen, während die Anzahl der behobenen Probleme ansteigt. Die Legende zeigt die durchschnittliche Anzahl von Problemen für jeden Schweregrad an. Positionieren Sie den Mauszeiger über den Balken, um die einzelnen Werte für jede Ausführung anzuzeigen. 

Wenn an einem Tag mehrere Ausführungen erfolgt sind, ist nur die letzte Ausführung in den Diagrammen auf der Seite **Trends** enthalten. 

## <a name="known-issues"></a>Bekannte Probleme

Bei der Verwendung von SQL-Bewertungen können einige der folgenden bekannten Probleme auftreten. 

### <a name="configuration-error-for-enable-assessment"></a>Konfigurationsfehler beim Aktivieren der Bewertung

Wenn Ihr virtueller Computer bereits einem Log Analytics-Arbeitsbereich zugeordnet ist, auf den Sie keinen Zugriff haben oder der sich in einem anderen Abonnement befindet, wird auf dem Konfigurationsblatt ein Fehler angezeigt. Im ersten Fall können Sie entweder Berechtigungen für diesen Arbeitsbereich abrufen oder Ihren virtuellen Computer in einen anderen Log Analytics-Arbeitsbereich verschieben, indem Sie [diese Anweisungen](../../../azure-monitor/agents/agent-manage.md) zum Entfernen von Microsoft Monitoring Agent befolgen. Wir arbeiten daran, ein Szenario zu ermöglichen, bei dem sich der Log Analytics-Arbeitsbereich in einem anderen Abonnement befindet.

### <a name="deployment-failure-for-enable-or-run-assessment"></a>Bereitstellungsfehler beim Aktivieren oder Ausführen der Bewertung 

Verwenden Sie den [Bereitstellungsverlauf](../../../azure-resource-manager/templates/deployment-history.md) der Ressourcengruppe, die die SQL-VM enthält, um die Fehlermeldung im Zusammenhang mit der fehlgeschlagenen Aktion anzuzeigen. 
 
### <a name="failed-assessments"></a>Fehlgeschlagene Bewertungen 

**Fehler bei der Bewertungsausführung**: Dies weist darauf hin, dass die SQL-IaaS-Erweiterung beim Ausführen der Bewertung ein Problem festgestellt hat. Die ausführliche Fehlermeldung ist im Erweiterungsprotokoll auf der VM unter `C:\WindowsAzure\Logs\Plugins\Microsoft.SqlServer.Management.SqlIaaSAgent\2.0.X.Y` verfügbar, wobei `2.0.X.Y ` der neueste vorhandene Versionsordner ist.  

**Fehler beim Hochladen des Ergebnisses in den Log Analytics-Arbeitsbereich**: Dies bedeutet, dass Microsoft Monitoring Agent (MMA) die Ergebnisse nicht zeitgerecht hochladen konnte. Stellen Sie sicher, dass die MMA-Erweiterung [ordnungsgemäß bereitgestellt](../../../azure-monitor/visualize/vmext-troubleshoot.md) ist, und sehen Sie sich die Probleme mit Konnektivität und Datenerfassung an, die in diesem [Leitfaden zur Problembehandlung](../../../azure-monitor/agents/agent-windows-troubleshoot.md) aufgeführt sind. 

>[!TIP]
>Wenn Sie TLS 1.0 oder höher in Windows erzwungen und ältere SSL-Protokolle wie [hier](/troubleshoot/windows-server/windows-security/restrict-cryptographic-algorithms-protocols-schannel#schannel-specific-registry-keys) beschrieben deaktiviert haben, müssen Sie auch sicherstellen, dass .NET Framework für die Verwendung starker Kryptografie [konfiguriert](../../../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12) ist. 

**Ergebnis ist aufgrund der Datenaufbewahrung im Log Analytics-Arbeitsbereich abgelaufen**: Dies weist darauf hin, dass die Ergebnisse basierend auf der Aufbewahrungsrichtlinie nicht mehr im Log Analytics-Arbeitsbereich beibehalten werden. Sie können den [Aufbewahrungszeitraum für den Arbeitsbereich ändern](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Registrieren Ihrer SQL Server-VM mit der SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs finden Sie in den Artikeln zur [automatischen Installation](sql-agent-extension-automatic-registration-all-vms.md) und zur [Installation auf einzelnen VMs](sql-agent-extension-manually-register-single-vm.md) bzw. zur [Masseninstallation auf VMs](sql-agent-extension-manually-register-vms-bulk.md).
- Weitere Informationen zu den Funktionen, die durch die SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs verfügbar sind, finden Sie unter [Verwalten von SQL Server-VMs mit dem Azure-Portal](manage-sql-vm-portal.md).

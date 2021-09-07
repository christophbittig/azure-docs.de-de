---
title: Datensicherheit in Azure Automation
description: In diesem Artikel erfahren Sie, wie Azure Automation Ihre Privatsphäre schützt und Ihre Daten sichert.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2d78b83ac7725f07060b3b61b01e57837fb053b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465900"
---
# <a name="management-of-azure-automation-data"></a>Verwaltung von Azure Automation-Daten

Dieser Artikel enthält verschiedene Themen, in denen erläutert wird, wie die Daten in einer Azure Automation-Umgebung geschützt und gesichert werden.

## <a name="tls-12-for-azure-automation"></a>TLS 1.2 für Azure Automation

Um die Sicherheit von Daten bei der Übertragung an Azure Automation zu gewährleisten, wird dringend empfohlen, den Computer für die Verwendung von TLS 1.2 (Transport Layer Security) zu konfigurieren. Im Folgenden sehen Sie eine Liste der Methoden bzw. Clients, die über HTTPS mit dem Automation-Dienst kommunizieren:

* Webhookaufrufe

* Hybrid Runbook Workers, einschließlich Computern, die über die Updateverwaltung sowie „Änderungsnachverfolgung und Bestand“ verwaltet werden.

* DSC-Knoten

Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**. Es wird nicht empfohlen, Ihren Agent explizit so einzurichten, dass nur TLS 1.2 verwendet wird, es sei denn, dies ist erforderlich. Denn dadurch können Sicherheitsfeatures auf Plattformebene deaktiviert werden, mit deren Hilfe neuere, sicherere Protokolle wie TLS 1.3 automatisch erkannt und genutzt werden können, sobald diese verfügbar sind.

Informationen zur TLS 1.2-Unterstützung mit dem Log Analytics-Agent für Windows und Linux, bei dem es sich um eine Abhängigkeit für die Hybrid Runbook Worker-Rolle handelt, finden Sie unter [Übersicht über den Log Analytics-Agent – TLS 1.2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Plattformspezifische Anleitungen

|Plattform/Sprache | Support | Weitere Informationen |
| --- | --- | --- |
|Linux | Linux-Distributionen greifen zur Unterstützung von TLS 1.2 tendenziell auf [OpenSSL](https://www.openssl.org) zurück.  | Überprüfen Sie anhand des [OpenSSL-Änderungsprotokolls](https://www.openssl.org/news/changelog.html), ob Ihre Version von OpenSSL unterstützt wird.|
| Windows 8.0 bis 10 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden.  |
| Windows Server 2012 bis 2016 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden. |
| Windows 7 SP1 und Windows Server 2008 R2 SP1 | Wird unterstützt, ist jedoch standardmäßig deaktiviert. | Details zur Aktivierung finden Sie auf der Seite [Transport Layer Security (TLS) registry settings (Registrierungseinstellungen für Transport Layer Security (TLS))](/windows-server/security/tls/tls-registry-settings).  |

## <a name="data-retention"></a>Beibehaltung von Daten

Wenn Sie eine Ressource in Azure Automation löschen, wird diese zu Überwachungszwecken für viele Tagen aufbewahrt, bevor sie endgültig gelöscht wird. In diesem Zeitraum kann die Ressource weder angezeigt noch verwendet werden. Diese Richtlinie gilt auch für Ressourcen, die zu einem gelöschten Automation-Konto gehören. Die Datenaufbewahrungsrichtlinie gilt für alle Benutzer und kann zurzeit nicht angepasst werden. Wenn Sie jedoch Daten für einen längeren Zeitraum aufbewahren müssen, können Sie [Azure Automation-Auftragsdaten an Azure Monitor-Protokolle weiterleiten](automation-manage-send-joblogs-log-analytics.md).

Die folgende Tabelle zeigt die Aufbewahrungsrichtlinie für unterschiedliche Ressourcen.

| Daten | Richtlinie |
|:--- |:--- |
| Konten |Ein Konto wird 30 Tage nach seiner Löschung durch den Benutzer endgültig entfernt. |
| Objekte |Ein Objekt wird 30 Tage nach seiner Löschung durch den Benutzer endgültig entfernt oder 30 Tage, nachdem ein Benutzer ein Konto gelöscht hat, das das Objekt enthält. Zu den Objekten gehören Variablen, Zeitpläne, Anmeldeinformationen, Zertifikate, Python 2-Pakete und Verbindungen. |
| DSC-Knoten |Ein DSC-Knoten wird 30 Tage nach Aufhebung seiner Registrierung im Automation-Konto über das Azure-Portal oder mit dem Cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) in Windows PowerShell endgültig entfernt. Auch ein Knoten wird nach 30 Tagen endgültig entfernt, nachdem ein Benutzer das Konto gelöscht hat, das den Knoten enthält. |
| Aufträge |Ein Auftrag wird 30 Tage nach der Änderung gelöscht und endgültig entfernt, z. B. nachdem der Auftrag abgeschlossen, beendet oder angehalten wurde. |
| Module |Ein Modul wird 30 Tage nach seiner Löschung durch einen Benutzer endgültig entfernt oder 30 Tage, nachdem ein Benutzer das Konto gelöscht hat, das das Modul enthält. |
| Knotenkonfigurationen/MOF-Dateien |Eine alte Knotenkonfiguration wird 30 Tage nach dem Generieren einer neuen Knotenkonfiguration endgültig entfernt. |
| Knotenberichte |Ein Knotenbericht wird 90 Tage nach dem Generieren eines neuen Berichts für diesen Knoten endgültig entfernt. |
| Runbooks |Ein Runbook wird 30 Tage, nachdem ein Benutzer die Ressource gelöscht hat, oder 30 Tage, nachdem ein Benutzer das Konto gelöscht hat, das die Ressource enthält, endgültig entfernt.<sup>1</sup> |

<sup>1</sup> Das Runbook kann innerhalb des 30-tägigen Zeitfensters wiederhergestellt werden. Hierzu muss ein Azure-Supportfall für den Microsoft Azure-Support erstellt werden. Besuchen Sie die [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und **erstellen Sie eine Supportanfrage**.

## <a name="data-backup"></a>Datensicherung

Wenn Sie ein Automation-Konto in Azure löschen, werden auch alle Objekt in dem Konto gelöscht. Zu den Objekten zählen Runbooks, Module, Konfigurationen, Einstellungen, Aufträge und Objekte. Sie können nicht mehr wiederhergestellt werden, nachdem das Konto gelöscht wurde. Sie können die Inhalte Ihres Automation-Kontos mithilfe der folgenden Informationen sichern, bevor Sie das Konto löschen.

### <a name="runbooks"></a>Runbooks

Sie können Ihre Runbooks entweder unter Verwendung des Azure-Portals oder mithilfe des Cmdlets [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) in Windows PowerShell in Skriptdateien exportieren. Sie können diese Skriptdateien in ein anderes Automation-Konto importieren, wie unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md) besprochen.

### <a name="integration-modules"></a>Integrationsmodule

Integrationsmodule können nicht aus Azure Automation exportiert werden, sondern müssen außerhalb des Automation-Kontos zur Verfügung gestellt werden.

### <a name="assets"></a>Objekte

Sie können keine Azure Automation-Objekte exportieren: Zertifikate, Verbindungen, Anmeldeinformationen, Zeitpläne und Variablen. Stattdessen können Sie das Azure-Portal und Azure-Cmdlets verwenden, um sich die Details dieser Objekte zu notieren. Verwenden Sie dann diese Details, um alle Objekte zu erstellen, die von Runbooks verwendet werden, die Sie in ein anderes Automation-Konto importieren.

Es ist nicht möglich, die Werte verschlüsselter Variablen oder die Kennwortfelder für Anmeldeinformationen mithilfe von Cmdlets abzurufen. Wenn Sie diese Werte nicht kennen, können Sie sie aus einem Runbook abrufen. Informationen zum Abrufen von Variablenwerten finden Sie unter [Variablenobjekte in Azure Automation](shared-resources/variables.md). Weitere Informationen zum Abrufen von Anmeldeinformationswerten finden Sie unter [Anmeldeinformationsobjekte in Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>DSC-Konfigurationen

Sie können Ihre DSC-Konfigurationen unter Verwendung des Azure-Portals oder mithilfe des Cmdlets [Export-AzureRmAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) in Windows PowerShell in Skriptdateien exportieren. Sie können diese Konfigurationen in ein anderes Automation-Konto importiert importieren und darin verwenden.

## <a name="geo-replication-in-azure-automation"></a>Georeplikation in Azure Automation

Georeplikation ist Standard in Azure Automation-Kontos. Bei der Einrichtung Ihres Kontos wählen Sie eine primäre Region aus. Der interne Automation-Georeplikationsdienst weist dem Konto automatisch eine sekundäre Region zu. Der Dienst sichert dann kontinuierlich Kontodaten aus der primären Region in der sekundären Region. Die vollständige Liste der primären und sekundären Regionen finden Sie unter [Business Continuity und Disaster Recovery (BCDR): Azure-Regionspaare](../best-practices-availability-paired-regions.md).

Die vom Automation-Georeplikationsdienst erstellte Sicherung ist eine vollständige Kopie der Automation-Objekte, -Konfigurationen usw. Diese Sicherung kann verwendet werden, wenn die primäre Region ausfällt und Daten verloren gehen. Microsoft versucht im unwahrscheinlichen Fall, dass Daten in einer primären Region verloren gehen, diese wiederherzustellen.

> [!NOTE]
> Azure Automation speichert Kundendaten in der vom Kunden ausgewählten Region. Für BCDR werden für alle Regionen außer „Brasilien, Süden“ und „Asien, Südosten“ die Azure Automation-Daten in einer anderen Region (Azure-Regionspaar) gespeichert. Nur für die Region „Brasilien, Süden“ (Sao Paulo) im Großraum „Brasilien“ und die Region „Asien, Südosten“ (Singapur) im Großraum „Asien-Pazifik“ werden Azure Automation-Daten in derselben Region gespeichert, um die Anforderungen an die Datenresidenz für diese Regionen zu erfüllen.

Externe Kunden können nicht direkt auf den Automation-Georeplikationsdienst zugreifen, wenn es zu einem regionalen Ausfall kommt. Wenn Sie Automation-Konfigurationen und -Runbooks während regionaler Ausfälle beibehalten möchten:

1. Wählen Sie eine sekundäre Region aus, die mit der geografischen Region Ihres primären Automation-Kontos gekoppelt werden soll.

2. Erstellen Sie ein Automation-Konto in der sekundären Region.

3. Exportieren Sie im primären Konto die Runbooks als Skriptdateien.

4. Importieren Sie die Runbooks in Ihr Automation-Konto in der sekundären Region.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu sicheren Objekten in Azure Automation finden Sie unter [Verschlüsseln sicherer Ressourcen in Azure Automation](automation-secure-asset-encryption.md).

* Weitere Informationen zur Georeplikation finden Sie unter [Erstellen und Verwenden der aktiven Georeplikation](../azure-sql/database/active-geo-replication-overview.md).

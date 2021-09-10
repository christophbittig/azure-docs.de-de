---
title: Neuerungen in Azure Automation
description: Wichtige Aktualisierungen für die Azure Automation-Dokumentation (monatlich aktualisiert).
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107931"
---
# <a name="whats-new-in-azure-automation"></a>Neuerungen in Azure Automation

Azure Automation wird kontinuierlich verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. Wenn Sie nach Elementen suchen, die älter als sechs Monate sind, können Sie sie im [Archiv zu den Neuerungen in Azure Automation](whats-new-archive.md) finden.

## <a name="august-2021"></a>August 2021

### <a name="azure-policy-guest-configuration"></a>Azure Policy-Gastkonfiguration

**Typ:** Plan für Änderung

Kunden sollten die Migration von Azure Automation State Configuration zu Azure Policy-Gastkonfiguration bewerten und planen. Weitere Informationen finden Sie unter [Azure Policy-Gastkonfiguration](../governance/policy/concepts/guest-configuration.md).

## <a name="july-2021"></a>Juli 2021

### <a name="preview-support-for-user-assigned-managed-identity"></a>Vorschauunterstützung für benutzerseitig zugewiesene verwaltete Identität

**Typ:** Neues Feature

Azure Automation unterstützt jetzt [benutzerseitig zugewiesene verwaltete Identitäten](automation-secure-asset-encryption.md) für Cloudaufträge in globalen Azure-Regionen, Azure Government-Regionen und Azure China-Regionen. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/).

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Allgemeine Verfügbarkeit von kundenseitig verwalteten Schlüsseln für Azure Automation

**Typ:** Neues Feature

Kunden können die Verschlüsselung von Azure Automation-Objekten mit ihren eigenen verwalteten Schlüsseln verwalten und schützen. Durch die Einführung von kundenseitig verwalteten Schlüsseln können Sie die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie Schlüssel verwenden, die Sie in Azure Key Vault erstellen und verwalten. Diese zusätzliche Verschlüsselung sollte Ihnen helfen, die gesetzlichen Anforderungen oder Complianceanforderungen Ihrer Organisation zu erfüllen.

Weitere Informationen finden Sie unter [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

## <a name="june-2021"></a>Juni 2021

### <a name="security-update-for-log-analytics-contributor-role"></a>Sicherheitsupdate für die Rolle „Log Analytics-Mitwirkender“

**Typ:** Plan für Änderung

Microsoft möchte die Rechte des Automation-Kontos aus der Rolle „Log Analytics-Mitwirkender“ entfernen. Derzeit kann die integrierte Rolle [Log Analytics-Mitwirkender](./automation-role-based-access-control.md#log-analytics-contributor) Berechtigungen an die Rolle [Mitwirkender](./../role-based-access-control/built-in-roles.md#contributor) des Abonnements eskalieren. Da für die ausführenden Konten des Automation-Kontos bereits die Rechte der Rolle „Mitwirkender“ für das Abonnement konfiguriert sind, kann es von einem Angreifer verwendet werden, um neue Runbooks zu erstellen und Code als Mitwirkender für das Abonnement auszuführen.

Aufgrund dieses Sicherheitsrisikos wird empfohlen, die Rolle „Log Analytics-Mitwirkender“ nicht zum Ausführen von Automation-Aufträgen zu verwenden. Erstellen Sie stattdessen die benutzerdefinierte Rolle „Azure Automation-Mitwirkender“, und verwenden Sie diese für Aktionen im Zusammenhang mit dem Automation-Konto. Die Schritte zur Implementierung finden Sie unter der [benutzerdefinierten Rolle „Azure Automation-Mitwirkender“](./automation-role-based-access-control.md#custom-azure-automation-contributor-role).

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>Unterstützung von Automatisierung und State Configuration in „USA, Westen 3“ verfügbar

**Typ:** Neues Feature

Weitere Informationen finden Sie unter [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/); wählen Sie Ihre Geografie aus der Dropdownliste aus.

## <a name="may-2021"></a>Mai 2021

### <a name="startstop-vms-during-off-hours-v1"></a>VMs außerhalb der Geschäftszeiten starten/beenden (v1)

**Typ:** Plan für Änderung

„VMs außerhalb der Geschäftszeiten starten/beenden (v1)“ ist am 21. Mai 2022 veraltet. Kunden sollten „VMs starten/beenden v2 (Vorschau)“ auswerten und die Migration planen. Weitere Informationen finden Sie unter [VMs starten/beenden v2 (Vorschau): Übersicht](../azure-functions/start-stop-vms/overview.md).

## <a name="april-2021"></a>April 2021

### <a name="support-for-update-management-and-change-tracking"></a>Unterstützung von Updateverwaltung und Änderungsnachverfolgung

**Typ:** Neues Feature

Die Regionszuordnung wurde aktualisiert, um Updateverwaltung und Änderungsnachverfolgung in „Norwegen, Osten“, „VAE, Norden“, „USA, Norden-Mitte“, „Brasilien, Süden“ und „Südkorea, Mitte“ zu unterstützen. Weitere Informationen finden Sie unter [Unterstützte Zuordnungen](./how-to/region-mappings.md#supported-mappings).

### <a name="support-for-system-assigned-managed-identities"></a>Unterstützung von systemseitig zugewiesenen verwalteten Identitäten

**Typ:** Neues Feature

Azure Automation unterstützt jetzt [systemseitig zugewiesene verwaltete Identitäten](./automation-security-overview.md#managed-identities-preview) für Cloud- und Hybridaufträge in globalen Azure- und Azure Government-Regionen. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/).

## <a name="march-2021"></a>März 2021

### <a name="new-azure-automation-built-in-policy-definitions"></a>Neue integrierte Richtliniendefinitionen in Azure Automation

**Typ:** Neues Feature

Für Azure Automation wurden fünf neue integrierte Richtliniendefinitionen hinzugefügt:

- Azure Automation-Konten müssen den Zugriff über öffentliche Netzwerke deaktivieren
- Azure Automation-Konten müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden
- Azure Automation-Konten zum Deaktivieren des Zugriffs über öffentliche Netzwerke konfigurieren
- Private Endpunktverbindungen für Azure Automation-Konten konfigurieren
- Für Automation-Konten müssen private Endpunktverbindungen aktiviert sein

Weitere Informationen finden Sie in der [Azure Policy-Referenz](./policy-reference.md).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Unterstützung von Automation und State Configuration in der Region „Indien, Süden“ allgemein verfügbar

**Typ:** Neues Feature

Verwenden Sie Funktionen für die Prozessautomatisierung und State Configuration (Zustandskonfiguration) in der Region „Indien, Süden“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Unterstützung von Automation und State Configuration in der Region „Vereinigtes Königreich, Westen“ allgemein verfügbar

**Typ:** Neues Feature

Verwenden Sie Funktionen für die Prozessautomatisierung und State Configuration (Zustandskonfiguration) in der Region „Vereinigtes Königreich, Westen“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Unterstützung von Automation und State Configuration in der Region „VAE, Mitte“ allgemein verfügbar

**Typ:** Neues Feature

Verwenden Sie Funktionen für die Prozessautomatisierung und State Configuration (Zustandskonfiguration) in der Region „VAE, Mitte“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/).

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>Unterstützung von Automation und State Configuration in den Regionen „Australien, Mitte 2“, „Norwegen, Westen“ und „Frankreich, Süden“ verfügbar

**Typ:** Neues Feature

Weitere Informationen finden Sie auf der [Seite zur Datenresidenz](https://azure.microsoft.com/global-infrastructure/data-residency/). Wählen Sie dort die Geografie für die jeweilige Region aus.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Neue Skripts für die Installation von Hybrid Worker unter Windows und Linux hinzugefügt

**Typ:** Neues Feature

Dem [GitHub-Repository](https://github.com/azureautomation) für Azure Automation wurden zwei neue Skripts für eines der wichtigsten Azure Automation-Szenarien hinzugefügt: die Einrichtung eines Hybrid Runbook Workers auf einem Windows- oder Linux-Computer. Das Skript erstellt einen neuen virtuellen Computer oder verwendet einen bereits vorhandenen, erstellt bei Bedarf einen Log Analytics-Arbeitsbereich, installiert den Log Analytics-Agent für Windows bzw. den Log Analytics-Arbeitsbereich für Linux und registriert den Computer beim Log Analytics-Arbeitsbereich. Das Windows-Skript heißt **Create Automation Windows HybridWorker**, das Linux-Skript **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Aufrufen eines Runbooks über den Webhook einer Azure Resource Manager-Vorlage

**Typ:** Neues Feature

Weitere Informationen finden Sie unter [Verwenden eines Webhooks mit einer ARM-Vorlage](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template).

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure-Updateverwaltung unterstützt jetzt Centos 8.x, Red Hat Enterprise Linux Server 8.x und SUSE Linux Enterprise Server 15

**Typ:** Neues Feature

Weitere Informationen finden Sie in der [vollständigen Liste](./update-management/operating-system-requirements.md) der unterstützten Linux-Betriebssysteme.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Unterstützung regionsinterner Datenresidenz für „Brasilien, Süden“ und „Asien, Südosten“

**Typ:** Neues Feature

In allen Regionen außer „Brasilien, Süden“ und „Asien, Südosten“ werden Azure Automation-Daten zur Gewährleistung von Business Continuity & Disaster Recovery (BCDR) in einer anderen Region (Azure-Regionspaar) gespeichert. Für die Regionen „Brasilien“ und „Asien, Südosten“ werden Azure Automation-Daten jetzt in der gleichen Region gespeichert, um die Datenresidenzanforderungen für diese Regionen zu erfüllen. Weitere Informationen finden Sie unter [Georeplikation in Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation).

## <a name="february-2021"></a>Februar 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Unterstützung von Automation und State Configuration in der Region „Japan, Westen“ allgemein verfügbar

**Typ:** Neues Feature

Automation-Konten und State Configuration sind jetzt in der Region „Japan, Westen“ verfügbar. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Benutzerdefinierte Azure Policy-Konformität zum Erzwingen der Runbookausführung in Hybrid Workern eingeführt

**Typ:** neues Feature

Sie können mit der neuen Azure Policy-Konformitätsregel die Erstellung von Aufträgen, Webhooks und Auftragszeitplänen zulassen, die nur in Hybrid Worker-Gruppen ausgeführt werden.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Updateverwaltung in den Regionen „USA, Osten“, „Frankreich, Mitte“ und „Europa, Norden“ verfügbar

**Typ:** Neues Feature

Das Automation-Feature „Updateverwaltung“ ist jetzt in den Regionen „USA, Osten“, „Frankreich, Mitte“ und „Europa, Norden“ verfügbar. Aktualisierungen der Dokumentation, die diese Änderung betreffen, finden Sie unter [Unterstützte Regionszuordnung](how-to/region-mappings.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Mitwirkung an der Azure Automation-Dokumentation finden Sie bei Interesse im [Leitfaden für Mitwirkende an der Microsoft-Dokumentation](/contribute/).

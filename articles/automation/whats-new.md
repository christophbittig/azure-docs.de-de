---
title: Neuerungen in Azure Automation
description: Wichtige Aktualisierungen für die Azure Automation-Dokumentation (monatlich aktualisiert).
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
ms.openlocfilehash: ea18171bdd957781e22743c3e59690fc8d4703f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432432"
---
# <a name="whats-new-in-azure-automation"></a>Neuerungen in Azure Automation

Azure Automation wird kontinuierlich verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. Wenn Sie nach Elementen suchen, die älter als sechs Monate sind, können Sie sie im [Archiv zu den Neuerungen in Azure Automation](whats-new-archive.md) finden.

## <a name="october-2021"></a>Oktober 2021

### <a name="preview-support-for-hybrid-runbook-worker-extension-for-azure-vms-and-arc-enabled-servers"></a>Vorschauunterstützung der Hybrid Runbook Worker-Erweiterung für virtuelle Azure-Computer und für Server mit Arc-Unterstützung

**Typ:** Neues Feature

Von Azure Automation wurde die native Integration von Benutzer-Hybrid Runbook Worker für virtuelle Azure-Computer und für Azure-fremde Computer über Server mit Arc-Unterstützung veröffentlicht. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support).

### <a name="preview-support-for-azure-active-directory-authentication"></a>Vorschauunterstützung der Azure Active Directory-Authentifizierung

**Typ:** Neues Feature

Von Azure Automation wurde ein kritisches Sicherheitsfeature mit Azure AD-Authentifizierungsunterstützung für alle öffentlichen Endpunkte des Automation-Diensts hinzugefügt. Das Feature wurde über die Hybrid Runbook Worker-Erweiterungsunterstützung für virtuelle Azure-Computer und für Server mit Arc-Unterstützung implementiert.

Dadurch wird die Abhängigkeit von Zertifikaten beseitigt, und Sie können Ihre strengen Überwachungs- und Konformitätsanforderungen erfüllen, indem Sie keine lokalen Authentifizierungsmethoden verwenden. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support).

### <a name="source-control-enabled-to-use-managed-identities"></a>Verwendung verwalteter Identitäten durch die Quellcodeverwaltung ermöglicht

**Typ:** Neues Feature

Von der Quellcodeverwaltungsintegration für Azure Automation können jetzt anstelle eines ausführenden Kontos [verwaltete Identitäten](automation-security-overview.md#managed-identities) verwendet werden. Weitere Informationen finden Sie in den [Voraussetzungen für die Integration der Quellcodeverwaltung](source-control-integration.md#prerequisites).

## <a name="september"></a>September

### <a name="support-for-az-modules-by-default"></a>Standardmäßige Unterstützung von Az-Modulen

**Typ:** Neue Funktion

Von Azure Automation werden jetzt standardmäßig Az-Module unterstützt. Neue erstellte Automation-Konten enthalten die neueste Version von Az-Modulen (standardmäßig 6.4.0). Automation bietet auch eine Option im Azure-Portal: **Version der Az-Module aktualisieren**. Damit können Sie die Az-Module in Ihren vorhandenen Automation-Konten aktualisieren. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-az-module-support).

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

Kunden können die Verschlüsselung von Azure Automation-Objekten mit ihren eigenen verwalteten Schlüsseln verwalten und schützen. Durch die Einführung kundenseitig verwalteter Schlüssel können Sie die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie Schlüssel verwenden, die Sie in Azure Key Vault erstellen und verwalten. Diese zusätzliche Verschlüsselungsebene trägt dazu bei, die gesetzlichen Anforderungen oder Complianceanforderungen Ihrer Organisation zu erfüllen.

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

Azure Automation unterstützt jetzt [systemseitig zugewiesene verwaltete Identitäten](./automation-security-overview.md#managed-identities) für Cloud- und Hybridaufträge in globalen Azure-Regionen und in Azure Government-Regionen. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Mitwirkung an der Azure Automation-Dokumentation finden Sie bei Interesse im [Leitfaden für Mitwirkende an der Microsoft-Dokumentation](/contribute/).

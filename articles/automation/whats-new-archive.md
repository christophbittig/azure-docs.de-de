---
title: Archiv für „Neuerungen in Azure Automation“
description: Die Versionshinweise zu Neuerungen im Abschnitt „Übersicht“ dieses Inhaltssatzes enthalten sechs Monate Aktivität. Danach werden die Elemente aus dem Hauptartikel entfernt und in diesem Artikel abgelegt.
services: automation
ms.topic: overview
ms.date: 10/27/2021
ms.custom: references_regions
ms.openlocfilehash: bd481c65dadbf127682087e4c0cf6ca018b17098
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473944"
---
# <a name="archive-for-whats-new-in-azure-automation"></a>Archiv für „Neuerungen in Azure Automation“

Der primäre Artikel [Neuerungen in Azure Automation](whats-new.md) enthält die Aktualisierungen aus den letzten sechs Monaten, während dieser Artikel alle älteren Informationen enthält.

„Neuerungen in Azure Automation“ enthält Informationen zu:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern

---

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

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure-Updateverwaltung unterstützt jetzt CentOS 8.x, Red Hat Enterprise Linux Server 8.x und SUSE Linux Enterprise Server 15.

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

## <a name="january-2021"></a>Januar 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Unterstützung von Automation und State Configuration in der Region „Schweiz, Westen“ allgemein verfügbar

**Typ:** Neues Feature

Automation-Konten und State Configuration sind jetzt in der Region „Schweiz, Westen“ verfügbar. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Python 3-Skript zum Importieren eines Moduls mit mehreren Abhängigkeiten hinzugefügt

**Typ:** Neues Feature

Das Skript kann aus dem [GitHub-Repository für Azure Automation](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) heruntergeladen werden.
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Unterstützung der Rolle „Hybrid Runbook Worker“ für CentOS 8.x/RHEL 8.x/SLES 15

**Typ:** Neues Feature

Das Feature „Hybrid Runbook Worker“ unterstützt CentOS 8.x-, REHL 8.x- und SLES 15-Distributionen nur für die Prozessautomatisierung in Hybrid Runbook Workern. Unter [Unterstützte Betriebssysteme](automation-linux-hrw-install.md#supported-linux-operating-systems) finden Sie entsprechende Dokumentationsaktualisierungen.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Updateverwaltung und Änderungsnachverfolgung in den Regionen „Australien, Osten“, „Asien, Osten“, „USA, Westen“ und „USA, Mitte“ verfügbar

**Typ:** Neues Feature

Automation-Konten, das Feature „Änderungsnachverfolgung und Bestand“ sowie die Updateverwaltung sind in den Regionen „Australien, Osten“, „Asien, Osten“, „USA, Westen“ und „USA, Mitte“ verfügbar.

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Public Preview von Python 3-Runbooks in der Cloud der US-Regierungsbehörden

**Typ:** Neues Feature Azure Automation führt die Public Preview-Unterstützung für die Ausführung von Python 3-Cloud- und -Hybridrunbooks in den Regionen der US-Regierungscloud ein. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Migration der Azure Automation-Runbooks aus dem TechNet Script Center zu GitHub

**Typ:** Plan für Änderung

Das TechNet Script Center wird eingestellt, und alle Runbooks aus dem Runbookkatalog wurden in unsere [Automation-GitHub-Organisation](https://github.com/azureautomation) verschoben. Weitere Informationen finden Sie unter [Migration der Azure Automation-Runbooks zu GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Dezember 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation und Updateverwaltung: Allgemeine Verfügbarkeit von Private Link

**Typ:** Neues Feature

Für Azure Global und Government-Clouds wurde die allgemeine Verfügbarkeit der Unterstützung von Azure Automation und Updateverwaltung angekündigt. Azure Automation ermöglicht die Unterstützung von Private Link, um die Ausführung eines Runbooks in einer Hybrid Worker-Rolle zu schützen. Dabei wird die Updateverwaltung zum Patchen von Computern verwendet, ein Runbook über einen Webhook aufgerufen und der State Configuration-Dienst verwendet, um die Compliance Ihrer Computer zu gewährleisten. Weitere Informationen finden Sie unter [Unterstützung von Private Link für Azure Automation jetzt allgemein verfügbar](https://azure.microsoft.com/updates/azure-automation-private-link).

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Barrierefreiheit von Azure Automation als „Grade-C“ zertifiziert

**Typ:** Neues Feature

Barrierefreiheitsfunktionen von Microsoft-Produkten unterstützen Behörden dabei, globalen Barrierefreiheitsanforderungen gerecht werden. Suchen Sie auf [Seite mit der Blogankündigung](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) nach **Azure Automation**, um den Bericht zur Barrierefreiheitskonformität des Automation-Diensts zu lesen.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Allgemeine Verfügbarkeit der Unterstützung von Automation und State Configuration in der Region „VAE, Norden“

**Typ:** Neues Feature

Verfügbarkeit von Automation-Konto State Configuration in der Region „VAE, Norden“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Allgemeine Verfügbarkeit der Unterstützung von Automation und State Configuration in der Region „Deutschland, Westen-Mitte“

**Typ:** Neues Feature

Verfügbarkeit von Automation-Konto State Configuration in der Region „Deutschland, Westen-Mitte“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-Unterstützung für Oracle 6 und 7

**Typ:** Neues Feature

Verwalten Sie Computer unter Oracle Linux 6 und 7 mit Automation State Configuration. Unter [Unterstützte Linux-Distributionen](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) finden Sie entsprechende Dokumentationsaktualisierungen.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Public Preview für Python 3-Runbooks in Automation

**Typ:** Neues Feature

Von Azure Automation wird nun die Ausführung von Python 3-Cloudrunbooks und -Hybridrunbooks in der Public Preview-Phase in allen Regionen der globalen Azure-Cloud unterstützt. Weitere Informationen finden Sie in der Ankündigung (https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) ).

## <a name="november-2020"></a>November 2020

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-Unterstützung für Ubuntu 18.04

**Typ:** Neues Feature

Unter [Unterstützte Linux-Distributionen](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) finden Sie entsprechende Dokumentationsaktualisierungen.

## <a name="october-2020"></a>Oktober 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Allgemeine Verfügbarkeit der Unterstützung von Automation und State Configuration in der Region „Schweiz, Norden“

**Typ:** Neues Feature

Verfügbarkeit von Automation-Konto State Configuration in der Region „Schweiz, Norden“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Allgemeine Verfügbarkeit der Unterstützung von Automation und State Configuration in der Region „Brasilien, Südosten“

**Typ:** Neues Feature

Verfügbarkeit von Automation-Konto State Configuration in der Region „Brasilien, Südosten“. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Verfügbarkeit der Updateverwaltung in der Region „USA, Süden-Mitte“

**Typ:** Neues Feature

Die Azure Automation-Regionszuordnung wurde aktualisiert, um das Updateverwaltungsfeature in der Region „USA, Süden-Mitte“ zu unterstützen. Unter [Unterstützte Zuordnungen](how-to/region-mappings.md#supported-mappings) finden Sie entsprechende Dokumentationsaktualisierungen.

## <a name="september-2020"></a>September 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Runbooks vom Typ „VMs außerhalb der Geschäftszeiten starten/beenden“ wurden aktualisiert, um Azure Az-Module zu verwenden

**Typ:** Neues Feature

Runbooks zum Starten/Beenden virtueller Computer wurden aktualisiert, um anstelle von Azure Resource Manager-Modulen Az-Module zu verwenden. In der Übersicht über [VMs außerhalb der Geschäftszeiten starten/beenden](automation-solution-vm-management.md) finden Sie entsprechende Dokumentationsaktualisierungen.

## <a name="august-2020"></a>August 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Veröffentlichung der DSC-Erweiterung zur Unterstützung von Azure Arc

**Typ:** Neues Feature

Verwenden Sie Azure Automation State Configuration, um Konfigurationen zentral zu speichern und den gewünschten Zustand hybrid verbundener Computer mithilfe der DSC-VM-Erweiterung für Server mit Azure Arc-Unterstützung zu verwalten. Weitere Informationen finden Sie unter [Verwaltung von Erweiterungen für virtuelle Computer mit Azure Arc-fähigen Servern](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Juli 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Public Preview-Einführung der Unterstützung von Private Link in Automation

**Typ:** Neues Feature

Stellen Sie mithilfe von Azure Private Link über private Endpunkte eine sichere Verbindung zwischen virtuellen Netzwerken und Azure Automation her. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hybrid Runbook Worker-Unterstützung für Windows Server 2008 R2

**Typ:** Neues Feature

Automation Hybrid Runbook Worker unterstützt das Betriebssystem Windows Server 2008 R2. Unter [Unterstützte Betriebssysteme](automation-windows-hrw-install.md#supported-windows-operating-system) finden Sie entsprechende Dokumentationsaktualisierungen.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Unterstützung der Updateverwaltung für Windows Server 2008 R2

**Typ:** Neues Feature

Die Updateverwaltung unterstützt das Bewerten und Patchen des Betriebssystems Windows Server 2008 R2. Unter [Unterstützte Betriebssysteme](update-management/operating-system-requirements.md) finden Sie entsprechende Dokumentationsaktualisierungen.

### <a name="automation-diagnostic-logs-schema-update"></a>Schemaaktualisierung für Automation-Diagnoseprotokolle

**Typ:** Neues Feature

Das Schema von Azure Automation-Protokolldaten im Log Analytics-Dienst wurde geändert. Weitere Informationen finden Sie unter [Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Unterstützung der Automation-Updateverwaltung durch Azure Lighthouse

**Typ:** Neues Feature

Azure Lighthouse ermöglicht eine delegierte Ressourcenverwaltung mit Updateverwaltung für Dienstanbieter und Kunden. Weitere Informationen erhalten Sie [hier](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Juni 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Verfügbarkeit von Automation und Updateverwaltung in der Region „US Gov Arizona“

**Typ:** Neues Feature

Automation-Konto und Updateverwaltung sind in der Region „US Gov Arizona“ verfügbar. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hybrid Runbook Worker-Onboardingskript aktualisiert, um Az-Module zu verwenden

**Typ:** Neues Feature

Das Runbook „New-OnPremiseHybridWorker“ wurde aktualisiert, um Az-Module zu unterstützen. Weitere Informationen finden Sie im Paket im [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Verfügbarkeit der Updateverwaltung in der Region „China, Osten 2“

**Typ:** Neues Feature

Die Azure Automation-Regionszuordnung wurde aktualisiert, um das Updateverwaltungsfeature in der Region „China, Osten 2“ zu unterstützen. Unter [Unterstützte Zuordnungen](how-to/region-mappings.md#supported-mappings) finden Sie entsprechende Dokumentationsaktualisierungen.

## <a name="may-2020"></a>Mai 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>DNS-Einträge des Automation-Diensts von regionsspezifischen zu Automation-kontospezifischen URLs aktualisiert

**Typ:** Neues Feature

Azure Automation-DNS-Einträge wurden aktualisiert, um Private Link-Instanzen zu unterstützen. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Möglichkeit zur standardmäßigen Beibehaltung der Verschlüsselung von Automation-Runbooks und DSC-Skripts hinzugefügt

**Typ:** Neues Feature

Neben der Verbesserung der Sicherheit von Ressourcen werden auch Runbooks und DSC-Skripts verschlüsselt, um Azure Automation noch sicherer zu machen.

## <a name="april-2020"></a>April 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Ausmusterung des Automation-Watchertasks

**Typ:** Plan für Änderung

Azure Logic Apps ist jetzt die empfohlene und unterstützte Methode für die Überwachung auf Ereignisse, für die Planung wiederkehrender Aufgaben und für die Auslösung von Aktionen. Es wird nicht weiter in den Watchertask investiert. Weitere Informationen finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>März 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Unterstützung von isoliertem Compute der Auswirkungsstufe 5 (Impact Level 5, IL5) in der Azure-Cloud (kommerziell und Government)

**Typ:**

Azure Automation Hybrid Runbook Worker kann in Azure Government verwendet werden, um Workloads der Auswirkungsstufe 5 zu unterstützen. Weitere Informationen finden Sie in unserer [Dokumentation](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Februar 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Unterstützung von Azure Virtual Network-Diensttags eingeführt

**Typ:** Neues Feature

Dank der Unterstützung von Diensttags durch Automation kann der Datenverkehr für den Automation-Dienst für eine Teilmenge von Szenarien zugelassen oder verweigert werden. Weitere Informationen finden Sie in der [Dokumentation](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Ermöglichung der Unterstützung von TLS 1.2 für den Azure Automation-Dienst

**Typ:** Plan für Änderung

Von Azure Automation werden TLS 1.2 und alle Clientaufrufe (über Webhooks, DSC-Knoten und Hybrid Worker) vollständig unterstützt. TLS 1.1 und TLS 1.0 werden zur Gewährleistung der Abwärtskompatibilität mit älteren Clients weiterhin unterstützt, bis die Kunden standardmäßig TLS 1.2 verwenden und vollständig zu TLS 1.2 migrieren.

## <a name="january-2020"></a>Januar 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Public Preview kundenseitig verwalteter Schlüssel für Azure Automation eingeführt

**Typ:** Neues Feature

Kunden können die Verschlüsselung von Azure Automation-Objekten mit ihren eigenen verwalteten Schlüsseln verwalten und schützen. Weitere Informationen finden Sie unter [Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Ausmusterung von ASM-REST-APIs (Azure Service Management) für Azure Automation

**Typ:** Ausmusterung

ASM-REST-APIs (Azure Service Management) für Azure Automation werden am 30. Januar 2020 ausgemustert und danach nicht mehr unterstützt. Weitere Informationen finden Sie in der [Ankündigung](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Mitwirkung an der Azure Automation-Dokumentation finden Sie bei Interesse im [Leitfaden für Mitwirkende an der Microsoft-Dokumentation](/contribute/).

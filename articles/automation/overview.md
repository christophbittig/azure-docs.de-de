---
title: Azure Automation – Übersicht
description: In diesem Artikel wird erläutert, was Azure Automation ist und wie es verwendet wird, um den Lebenszyklus von Infrastruktur und Anwendungen zu automatisieren.
services: automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, Updateverwaltung, Änderungsnachverfolgung, DSC, Bestand, Runbooks, Python, grafisch
ms.date: 10/25/2021
ms.topic: overview
ms.openlocfilehash: d6a703a04aeec9b764f74a7574cde4baea875604
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491486"
---
# <a name="what-is-azure-automation"></a>Was ist Azure Automation?

Automatisierung ist in drei umfassenden Bereichen des Cloudbetriebs erforderlich:

* Bereitstellen und Verwalten: Bereitstellen einer wiederholbaren und konsistenten Infrastruktur als Code.
* Antwort: Erstellen einer ereignisbasierten Automatisierung, um Probleme zu diagnostizieren und zu beheben.
* Orchestrieren: Orchestrieren und Integrieren Ihrer Automatisierung mit anderen Azure- oder Drittanbieterdiensten und -produkten.

Mit Azure Automation wird ein cloudbasierter Dienst für Automatisierung, Updates von Betriebssystemen und Konfiguration bereitgestellt, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Der Dienst umfasst Prozessautomatisierung, Konfigurationsverwaltung, Updateverwaltung, gemeinsam genutzte Funktionen und Features für heterogene Umgebungen.

:::image type="content" source="./media/overview/automation-overview.png" alt-text="Diagramm: Automatisierungsfunktionen" border="true":::

Es gibt mehrere Azure-Dienste, die die oben genannten Anforderungen erfüllen können, wobei jeder Dienst eine Reihe von Funktionen umfasst und als programmierbare Plattform zum Erstellen von Cloudlösungen dient. Azure Bicep und Resource Manager stellen beispielsweise eine Sprache zum Entwickeln wiederholbarer und konsistenter Bereitstellungsvorlagen für Azure-Ressourcen bereit. Azure Automation kann diese Vorlage verarbeiten, um eine Azure-Ressource bereitzustellen und dann eine Reihe von Konfigurationsaufgaben nach der Bereitstellung zu verarbeiten.

Mit Azure Automation haben Sie die volle Kontrolle über Bereitstellung, Ausführung und Außerbetriebnahme von Unternehmensworkloads und -ressourcen.

## <a name="process-automation"></a>Prozessautomatisierung

Die Prozessautomatisierung in Azure Automation ermöglicht es Ihnen, häufig anfallende, zeitaufwendige und fehleranfällige Verwaltungsaufgaben zu automatisieren. Bei Verwendung dieses Diensts können Sie sich auf Aufgaben konzentrieren, die den Geschäftswert erhöhen. Aufgrund der geringeren Fehlerquote und höheren Effizienz können Betriebskosten gesenkt werden. Einzelheiten zur Betriebsumgebung für die Prozessautomatisierung finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).

Die Prozessautomatisierung unterstützt die Integration von Azure-Diensten und anderen Drittanbietersystemen, die für die Bereitstellung, Konfiguration und Verwaltung Ihrer End-to-End-Prozesse erforderlich sind. Mit dem Dienst können Sie grafische, PowerShell- und Python-[Runbooks](automation-runbook-types.md) erstellen. Zur Ausführung von Runbooks direkt auf dem Windows- oder Linux-Computer oder für Ressourcen in der lokalen oder einer anderen Cloudumgebung, um diese lokalen Ressourcen zu verwalten, können Sie einen [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) auf dem Computer bereitstellen.

Mit [Webhooks](automation-webhooks.md) können Sie Anforderungen erfüllen und sowohl Continuous Delivery-Abläufe als auch eine fortlaufende Ausführung von Vorgängen sicherstellen, indem Sie die Automatisierung über Azure Logic Apps, Azure Functions, ITSM-Produkte oder -Dienste, DevOps und Überwachungssysteme auslösen.

## <a name="configuration-management"></a>Konfigurationsverwaltung

Die Konfigurationsverwaltung in Azure Automation wird von zwei Funktionen unterstützt:

* Änderungsnachverfolgung und Bestand
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>Änderungsnachverfolgung und Bestand

„Änderungsnachverfolgung und Bestand“ kombiniert Funktionen, mit denen Sie Änderungen an virtuellen Linux- und Windows-Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren. Weitere Informationen zu diesem Feature finden Sie unter [Änderungsnachverfolgung und Bestand](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Azure Automation State Configuration](automation-dsc-overview.md) ist ein cloudbasiertes PowerShell DSC-Feature (Desired State Configuration), mit dem Dienste für Unternehmensumgebungen bereitgestellt werden. Unter Verwendung dieser Funktion können Sie Ihre DSC-Ressourcen in Azure Automation verwalten und Konfigurationen auf virtuelle oder physische Computer anwenden. Dabei werden die Konfigurationen von einem DSC-Pullserver in der Azure-Cloud abgerufen.

## <a name="update-management"></a>Updateverwaltung

Azure Automation umfasst ein Feature zur [Updateverwaltung](./update-management/overview.md) für Windows- und Linux-Systeme in Hybridumgebungen. Mithilfe der Updateverwaltung erhalten Sie einen Einblick in die Konformität von Updates für Azure, andere Clouds und die lokale Umgebung. Mit diesem Feature können Sie geplante Bereitstellungen erstellen, um die Installation von Updates in einem definierten Wartungsfenster zu orchestrieren. Wenn ein Update nicht auf einem Computer installiert werden soll, können Sie es mithilfe der Funktionen zur Updateverwaltung von einer Bereitstellung ausschließen.

## <a name="shared-capabilities"></a>Gemeinsam genutzte Funktionen

Azure Automation bietet eine Reihe von gemeinsam genutzten Funktionen. Dazu zählen u. a. gemeinsam genutzte Ressourcen, die rollenbasierte Zugriffssteuerung, eine flexible Zeitplanung, die Integration in die Quellcodeverwaltung, Überwachungsfunktionen und das Tagging.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gemeinsam genutzte Ressourcen

Azure Automation besteht aus einem Satz von gemeinsam genutzten Ressourcen, die Ihnen das bedarfsabhängige Automatisieren und Konfigurieren Ihrer Umgebungen erleichtern.

* **[Zeitpläne](./shared-resources/schedules.md)** : Auslösen von Automation-Vorgängen zu vorgegebenen Zeiten.
* **[Module](./shared-resources/modules.md)** : Verwalten von Azure und anderen Systemen. Sie können Module für Microsoft-, Drittanbieter-, Community- oder benutzerdefinierte Cmdlets und DSC-Ressourcen in das Automation-Konto importieren.
* **[Modulkatalog](automation-runbook-gallery.md)** : Unterstützung einer nativen PowerShell-Katalogintegration, um Runbooks anzuzeigen und in das Automation-Konto zu importieren. Mithilfe des Katalogs können Sie im Handumdrehen mit dem Integrieren und Erstellen Ihrer Prozesse aus dem PowerShell-Katalog und Microsoft Script Center beginnen.
* **[Python 2- und Python 3-Pakete](python-packages.md)** : Unterstützung von Python 2- und Python 3-Runbooks für Ihr Automation-Konto.
* **[Anmeldeinformationen](./shared-resources/credentials.md)** : Sicheres Speichern von vertraulichen Informationen, die zur Laufzeit von Runbooks und Konfigurationen verwendet werden können.
* **[Verbindungen](automation-connections.md)** : Speichern von Name/Wert-Paaren gemeinsamer Informationen für Verbindungen mit Systemen. Verbindungen werden vom Autor eines Moduls für die Nutzung in Runbooks und Konfigurationen zur Laufzeit definiert.
* **[Zertifikate](./shared-resources/certificates.md)** : Definition von Informationen, die zur Authentifizierung und zum Schützen von bereitgestellten Ressourcen verwendet werden, wenn Runbooks oder DSC-Konfigurationen zur Laufzeit darauf zugreifen.
* **[Variablen](./shared-resources/variables.md)** : Definition von Inhalten, die für verschiedene Runbooks und Konfigurationen verwendet werden können. Sie können Variablenwerte ändern, ohne die Runbooks oder Konfigurationen bearbeiten zu müssen, die darauf verweisen.

### <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Azure Automation unterstützt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf das Automation-Konto und die zugehörigen Ressourcen zu steuern. Weitere Informationen zur Konfiguration von Azure RBAC für Ihr Automation-Konto, Ihre Runbooks und Ihre Aufträge finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integration der Quellcodeverwaltung

Azure Automation unterstützt eine [Integration in die Quellcodeverwaltung](source-control-integration.md). Diese Funktion ermöglicht die Konfiguration in Form von Code, sodass Runbooks oder Konfigurationen in ein Quellcodeverwaltungssystem eingecheckt werden können.

## <a name="heterogeneous-support-windows-and-linux"></a>Unterstützung heterogener Systeme (Windows und Linux)

Automation ist für physische Windows- und Linux-Server und virtuelle Windows- und Linux-Computer außerhalb von Azure, in Ihrem Unternehmensnetzwerk oder bei einem anderen Cloudanbieter konzipiert. Sie können bereitgestellte Workloads und das Betriebssystem, auf dem diese ausgeführt werden, auf einheitliche Weise automatisieren und konfigurieren. Das Feature „Hybrid Runbook Worker“ von Azure Automation ermöglicht das direkte Ausführen von Runbooks auf dem physischen Server oder virtuellen Computer außerhalb von Azure, auf dem die Rolle gehostet wird, und für Ressourcen in der Umgebung, um diese lokalen Ressourcen zu verwalten.

Über [Arc-fähige Server](../azure-arc/servers/overview.md) bietet es eine konsistente Bereitstellungs- und Verwaltungsumgebung für Ihre Nicht-Azure-Computer. Es ermöglicht die Integration mit dem Automation-Dienst mithilfe des VM-Erweiterungsframeworks, um die Hybrid Runbook Worker-Rolle bereitzustellen und das Onboarding für Updateverwaltung und Änderungsnachverfolgung und Bestand zu vereinfachen.

## <a name="common-scenarios"></a>Häufige Szenarien

Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen. Zu den häufigen Szenarios gehören:

* **Planen von Aufgaben**: Beenden Sie VMs oder Dienste nachts, und aktivieren Sie sie während der täglichen, wöchentlichen oder monatlichen wiederkehrenden Wartungsworkflows.
* **Schreiben von Runbooks**: Erstellen Sie PowerShell-, PowerShell-Workflow-, Python 2-, Python 3- und DSC-Runbooks sowie grafische Runbooks mithilfe einer gemeinsamen Sprache.
* **Erstellen und Bereitstellen von Ressourcen**: Verwenden Sie Runbooks und Azure Resource Manager-Vorlagen, um virtuelle Computer in einer Hybridumgebung bereitzustellen. Führen Sie eine Integration in Entwicklungstools wie Jenkins und Azure DevOps durch.
* **Konfigurieren von VMs**: Bewerten und konfigurieren Sie Windows- und Linux-Computer mit Konfigurationen für die Infrastruktur und Anwendung.
* **Teilen von Informationen**: Übertragen Sie Informationen zur Bereitstellung und Verwaltung von Workloads in Ihrer Organisation in das System.
* **Abrufen von Bestandsinformationen**: Verschaffen Sie sich einen Überblick über den gesamten Bestand an bereitgestellten Ressourcen, um Informationen zu Zielgruppenadressierung, Berichterstellung und Konformität zu erhalten.
* **Ermitteln von Änderungen**: Identifizieren und isolieren Sie Computeränderungen, die zu Fehlkonfigurationen führen können, und verbessern Sie die Konformität Ihrer Abläufe. Beheben oder eskalieren Sie sie an Verwaltungssysteme.
* **Regelmäßige Wartung**: Führen Sie Aufgaben aus, die in festgelegten Zeitintervallen ausgeführt werden müssen, z. B. Löschen veralteter oder alter Daten oder Neuindizieren einer SQL-Datenbank.
* **Reagieren auf Warnungen**: Orchestrieren Sie eine Antwort, wenn kostenbasierte, systembasierte, dienstbasierte und/oder Ressourcenverwendungswarnungen generiert werden.
* **Hybridautomatisierung**: Verwalten oder automatisieren Sie lokale Server und Dienste wie SQL Server, Active Directory oder SharePoint Server.
* **Verwaltung des Azure-Ressourcenlebenszyklus**: für IaaS- und PaaS-Dienste.
* **Dev/Test-Automatisierungsszenarios:** Starten und Skalieren von Ressourcen u. a.
* **Governancebezogene Automatisierung**: automatisches Anwenden oder Aktualisieren von Tags, Sperren u. a.
* **Azure Site Recovery**: Orchestrieren Sie Pre-/Post-Skripts, die in einem Site Recovery DR-Workflow definiert sind.
* **Windows Virtual Desktop**: Orchestrieren Sie die Skalierung von VMs, oder starten/beenden Sie VMs basierend auf der Auslastung.

Je nach Ihren Anforderungen kann mindestens einer der folgenden Azure-Dienste mit Azure Automation integriert oder ergänzt werden, um die Anforderungen vollständig zu erfüllen:

* [Azure Arc-fähige Server](../azure-arc/servers/overview.md) ermöglichen ein vereinfachtes Onboarding von Hybridcomputern für Updateverwaltung, Änderungsnachverfolgung und Bestand und die Hybrid Runbook Worker-Rolle.
* [Azure Alerts-Aktionsgruppen](../azure-monitor/alerts/action-groups.md) können ein Automation-Runbook initiieren, wenn eine Warnung ausgelöst wird.
* [Azure Monitor](../azure-monitor/overview.md) dient dazu, Metriken und Protokolldaten aus Ihrem Automation-Konto zur weiteren Analyse zu sammeln und Maßnahmen für die Telemetrie zu ergreifen. Automatisierungsfeatures wie Updateverwaltung und Änderungsnachverfolgung und Bestand nutzen den Log Analytics-Arbeitsbereich, um Elemente ihrer Funktionalität bereitzustellen.
* [Azure Policy](../governance/policy/samples/built-in-policies.md) enthält Initiativendefinitionen, um die Einhaltung verschiedener Sicherheitsstandards für Ihr Automation-Konto zu gewährleisten und aufrechtzuerhalten.
* [Azure Site Recovery](../site-recovery/site-recovery-runbook-automation.md) kann Azure Automation-Runbooks verwenden, um Wiederherstellungspläne zu automatisieren.

Diese Azure-Dienste können mit Automation-Auftrags- und -Runbookressourcen mithilfe eines HTTP-Webhooks oder einer API-Methode verwendet werden:

* [Azure Logic Apps](/azure/connectors/built-in)
* [Azure Power Apps](/connectors/azureautomation)
* [Azure Event Grid](../event-grid/handler-webhooks.md)
* [Azure Power Automate](/connectors/azureautomation)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Preise für Azure Automation

Informationen zur Preisgestaltung bei Azure Automation finden Sie auf der entsprechenden Seite mit den [Preisen](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Automation-Kontos](./quickstarts/create-account-portal.md)

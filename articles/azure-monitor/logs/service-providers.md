---
title: Azure Monitor-Protokolle für Dienstanbieter | Microsoft-Dokumentation
description: Mit Azure Monitor-Protokollen können Managed Service Provider (MSPs), Großunternehmen, unabhängige Softwarehersteller (Independent Software Vendors, ISVs) und Hostingdienstanbieter Server in der lokalen oder Cloudinfrastruktur des Kunden verwalten und überwachen.
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 076574173f0bfd4daff4845ab354e730ec0844d9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233261"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor-Protokolle für Dienstanbieter

Log Analytics-Arbeitsbereiche in Azure Monitor unterstützen Managed Service Provider (MSPs), Großunternehmen, unabhängige Softwarehersteller (Independent Software Vendors, ISVs) und Hostingdienstanbieter beim Verwalten und Überwachen von Servern in der lokalen oder Cloudinfrastruktur des Kunden.

Zwischen Großunternehmen und Dienstanbietern bestehen viele Gemeinsamkeiten, insbesondere wenn ein zentrales IT-Team für die Verwaltung der IT vieler unterschiedlicher Geschäftseinheiten verantwortlich ist. Der Einfachheit halber wird in diesem Dokument der Begriff *Dienstanbieter* verwendet, jedoch ist die gleiche Funktionalität für Unternehmen und andere Kunden verfügbar.

Für Partner und Dienstanbieter, die am Programm [Cloud-Lösungsanbieter (Cloud Solution Provider, CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) teilnehmen, ist Log Analytics in Azure Monitor einer Dienste, die in Azure CSP-Abonnements verfügbar sind.

Log Analytics in Azure Monitor kann außerdem von Dienstanbietern verwendet werden, die Kundenressourcen über die Funktion der delegierten Azure-Ressourcenverwaltung in [Azure Lighthouse](../../lighthouse/overview.md) verwalten.

## <a name="architectures-for-service-providers"></a>Architekturen für Dienstanbieter

Log Analytics-Arbeitsbereiche ermöglichen Administratoren die Steuerung des Flusses und der Isolierung von [Protokolldaten](../logs/data-platform-logs.md) sowie die Erstellung einer Architektur, die den spezifischen Geschäftsanforderungen Rechnung trägt. In [diesem Artikel](../logs/design-logs-deployment.md) werden die Entwurfs-, Bereitstellungs- und Migrationsaspekte für einen Arbeitsbereich behandelt. Im Artikel [Verwalten von Protokolldaten und Arbeitsbereichen in Azure Monitor](../logs/manage-access.md) erfahren Sie, wie Sie Berechtigungen auf Protokolldaten anwenden und diese Berechtigungen verwalten. Dienstanbieter müssen sich mit zusätzlichen Überlegungen befassen.

Für Dienstanbieter gibt es im Hinblick auf Log Analytics-Arbeitsbereiche drei mögliche Architekturen:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Verteilt: Protokolle werden in Arbeitsbereichen gespeichert, die sich im Mandanten des Kunden befinden

Bei dieser Architektur wird ein Arbeitsbereich in dem Mandanten des Kunden bereitgestellt, der für alle Protokolle dieses Kunden verwendet wird.

Es gibt zwei Möglichkeiten, wie Administratoren des Dienstanbieters Zugriff auf einen Log Analytics-Arbeitsbereich in einem Kundenmandanten erhalten:

- Ein Kunde kann einzelne Benutzer des Dienstanbieters als [Azure Active Directory-Gastbenutzer (B2B)](../../active-directory/external-identities/what-is-b2b.md) hinzufügen. Die Administratoren des Dienstanbieters müssen sich im Azure-Portal im Verzeichnis jedes Kunden anmelden, um Zugriff auf diese Arbeitsbereiche zu erhalten. Dies macht es auch erforderlich, dass die Kunden den Zugriff für jeden Administrator des Dienstanbieters verwalten.
- Für eine größere Skalierbarkeit und Flexibilität können Dienstanbieter [Azure Lighthouse](../../lighthouse/overview.md) verwenden, um auf den Mandanten des Kunden zuzugreifen. Bei dieser Methode sind die Administratoren des Dienstanbieters in einer Azure AD-Benutzergruppe im Mandanten des Dienstanbieters enthalten. Diese Gruppe erhält während des Onboardingprozesses für jeden Kunden Zugriff. Die Administratoren können dann über ihren Mandanten des Dienstanbieters auf die Arbeitsbereiche jedes Kunden zugreifen, statt sich jeweils beim Mandanten der einzelnen Kunden anmelden zu müssen. Wenn Sie auf diese Weise auf die Ressourcen der Log Analytics-Arbeitsbereiche Ihrer Kunden zugreifen, verringert sich der Arbeitsaufwand auf Kundenseite, und die Daten mehrerer Kunden, die über den gleichen Dienstanbieter verwaltet werden, lassen sich über Tools wie z. B. [Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) einfacher erfassen und analysieren. Weitere Informationen finden Sie unter [Überwachen von Kundenressourcen in großem Umfang](../../lighthouse/how-to/monitor-at-scale.md).

Die verteilte Architektur bietet folgende Vorteile:

* Der Kunde kann spezifische Berechtigungsebenen über die [delegierte Azure-Ressourcenverwaltung](../../lighthouse/concepts/architecture.md) überprüfen oder den Zugriff auf die Protokolle über seine [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwalten.
* Protokolle können von Ressourcen jeder Art erfasst werden, nicht nur von agentbasierten VM-Daten. Beispielsweise Azure-Überwachungsprotokolle.
* Jeder Kunde kann andere Einstellungen für seinen Arbeitsbereich verwenden, etwa hinsichtlich Vermerkdauer und Datenbegrenzung.
* Isolation zwischen Kunden im Hinblick auf behördliche Bestimmungen und Compliance.
* Die Gebühr für jeden Arbeitsbereich wird im Abonnement des Kunden erfasst.

Nachteile der verteilten Architektur:

* Durch die zentrale Visualisierung und Analyse von Daten [mehrerer Kundenmandanten](cross-workspace-query.md) mit Tools wie z. B. Azure Monitor-Arbeitsmappen können sich Vorgänge verlangsamen, insbesondere bei der Analyse von Daten für mehr als 50 Arbeitsbereiche.
* Wenn für Kunden kein Onboarding für die delegierte Azure-Ressourcenverwaltung durchgeführt wird, müssen Administratoren des Dienstanbieters im Kundenverzeichnis bereitgestellt werden, und für den Dienstanbieter ist es schwieriger, eine große Anzahl von Kundenmandanten gleichzeitig zu verwalten.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Zentral: Protokolle werden in einem Arbeitsbereich gespeichert, der sich im Mandanten des Dienstanbieters befindet

Bei dieser Architektur werden die Protokolle nicht in den Mandanten des Kunden gespeichert, sondern nur an einem zentralen Speicherort in einem der Abonnements des Dienstanbieters. Die Agents, die auf den VMs des Kunden installiert sind, sind dafür konfiguriert, ihre Protokolle unter Verwendung der Arbeitsbereich-ID und des geheimen Schlüssels an diesen Arbeitsbereich zu senden.

Die zentralisierte Architektur bietet folgende Vorteile:

* Es ist einfach, eine große Anzahl Kunden zu verwalten und sie in verschiedene Back-End-Systeme zu integrieren.
* Der Dienstanbieter ist im vollständigen Besitz der Protokolle und der verschiedenen Artefakte, wie etwa Funktionen und gespeicherte Abfragen.
* Der Dienstanbieter kann Analysen übergreifend über alle Kunden ausführen.

Nachteile der zentralisierten Architektur:

* Diese Architektur eignet sich nur für Agent-basierte VM-Daten, sie deckt PaaS-, SaaS- und Azure-Fabric-Datenquellen nicht ab.
* Es kann schwierig sein, die Daten der Kunden zu trennen, wenn sie in einem einzelnen Arbeitsbereich zusammengeführt werden. Das einzig gute Verfahren dazu ist die Verwendung des FQDNs (vollqualifizierten Domänennamens) des Computers oder der Azure-Abonnement-ID.
* Alle Daten aller Kunden werden im gleichen Bereich mit einer einzelnen Rechnung und mit gleicher Vermerkdauer und gleichen Konfigurationseinstellungen gespeichert.
* Azure-Fabric- und PaaS-Dienste, wie Azure-Diagnose und Azure-Überwachungsprotokolle, erfordern einen Arbeitsbereich im gleichen Mandanten wie die Ressource, sie können also keine Protokolle an den zentralen Arbeitsbereich senden.
* Alle VM-Agents von allen Kunden werden beim zentralen Arbeitsbereich mit der gleichen Arbeitsbereichs-ID und dem gleichen Schlüssel authentifiziert. Es gibt kein Verfahren, Protokolle eines bestimmten Kunden zu blockieren, ohne andere Kunden zu unterbrechen.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid: Protokolle werden in einem Arbeitsbereich gespeichert, der sich im Mandanten des Kunden befindet, und einige von ihnen werden per Pull an einen zentralen Speicherort übertragen.

Die dritte Architektur stellt eine Mischung der zwei Optionen dar. Sie basiert auf der ersten, verteilten Architektur, in der die Protokolle lokal bei jedem Kunden sind, verwendet aber einen Mechanismus zur Erstellung eines zentralen Repositorys für Protokolle. Ein Teil der Protokolle wird zur Berichterstellung und Analyse per Pull an einen zentralen Speicherort übertragen. Bei diesem Teil kann es sich um eine kleine Anzahl Datentypen oder um eine Zusammenfassung der Aktivitäten handeln, wie etwa eine tägliche Statistik.

Es bestehen zwei Optionen zum Implementieren von Protokollen an einem zentralen Speicherort:

1. Zentraler Arbeitsbereich: Der Dienstanbieter kann einen Arbeitsbereich in seinem Mandanten erstellen und ein Skript verwenden, das die [Abfrage-API](https://dev.loganalytics.io/) in Kombination mit der [Datensammlungs-API](../logs/data-collector-api.md) nutzt, um die Daten aus den verschiedenen Arbeitsbereichen an diesen zentralen Speicherort zu bringen. Eine weitere Option, die kein Skript einsetzt, ist die Verwendung von [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).

2. Power BI als zentraler Speicherort: Power BI kann als zentraler Speicherort dienen, wenn die verschiedenen Arbeitsbereiche mithilfe der Integration zwischen dem Log Analytics-Arbeitsbereich und [Power BI](./log-powerbi.md) Daten nach Power BI exportieren.

## <a name="next-steps"></a>Nächste Schritte

* Automatisieren Sie mithilfe von [Resource Manager-Vorlagen](../logs/resource-manager-workspace.md) die Erstellung und Konfiguration von Arbeitsbereichen.

* Automatisieren Sie mit [PowerShell](../logs/powershell-workspace-configuration.md) die Erstellung von Arbeitsbereichen.

* Verwenden Sie [Warnungen](../alerts/alerts-overview.md) für die Integration in vorhandene Systeme.

* Generieren Sie Zusammenfassungsberichte mit [Power BI](./log-powerbi.md).

* Führen Sie das Onboarding von Kunden für die [delegierte Azure-Ressourcenverwaltung](../../lighthouse/concepts/architecture.md) durch.
---
title: Einführung in die Automatisierung in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel werden die Funktionen für Sicherheitsorchestrierungen, Automatisierung und Reaktion (SOAR) von Azure Sentinel vorgestellt und die zugehörigen SOAR-Komponenten Automatisierungsregeln und Playbooks beschrieben.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8d846682062effff84aed263b312e3e3c2c3f72d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037290"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Sicherheitsorchestrierung, Automatisierung und Reaktion (SOAR) in Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden die Funktionen für Sicherheitsorchestrierungen, Automatisierung und Reaktion (SOAR) von Azure Sentinel vorgestellt. Außerdem wird erläutert, wie durch Verwendung von Automatisierungsregeln und Playbooks als Reaktion auf Sicherheitsbedrohungen die Effektivität Ihres Security Operations Center erhöht wird und Sie Zeit und Ressourcen sparen.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel als SOAR-Lösung

### <a name="the-problem"></a>Das Problem

SIEM/SOC-Teams werden regelmäßig mit Sicherheitswarnungen und -vorfällen überschwemmt, und zwar in einem so großen Umfang, dass das verfügbare Personal überfordert ist. Dies führt häufig dazu, dass viele Alarme ignoriert und viele Incidents nicht untersucht werden, wodurch ein Unternehmen anfällig für Angriffe wird, die unbemerkt bleiben.

### <a name="the-solution"></a>Die Lösung

Azure Sentinel ist nicht nur ein SIEM-System (Security Information & Event Management), sondern auch eine Plattform für Sicherheitsorchestrierung, Automatisierung und Reaktion (SOAR). Einer der Hauptzwecke besteht darin, wiederkehrende und vorhersagbare Anreicherungs-, Reaktions- und Problembehandlungsaufgaben zu automatisieren, die in die Verantwortung Ihres Security Operations Center und -Personals (SoC/SecOPs) fallen, und so Zeit und Ressourcen für eine ausführlichere Untersuchung und Suche nach erweiterten Bedrohungen freizugeben. Die Automatisierung nimmt in Azure Sentinel unterschiedliche Form an, von Automatisierungsregeln, die die Automatisierung von Incidentbehandlung und -reaktionen zentral verwalten, bis hin zu Playbooks, die vordefinierte Aktionssequenzen ausführen, um leistungsstarke und flexible erweiterte Automatisierungsfunktionen für Ihre Aufgaben zur Reaktion auf Bedrohungen bereitzustellen.

## <a name="automation-rules"></a>Automatisierungsregeln

Automatisierungsregeln sind ein neues Konzept in Azure Sentinel. Diese Funktion ermöglicht es Benutzern, die Automatisierung der Behandlung von Vorfällen zentral zu verwalten. Mit Automatisierungsregeln können Sie nicht nur Playbooks Incidents zuweisen (nicht nur Alerts wie bisher), sondern auch Antworten für mehrere Analyseregeln gleichzeitig automatisieren, Incidents automatisch kennzeichnen, zuweisen oder schließen, ohne dass Playbooks erforderlich sind, und die Reihenfolge der ausgeführten Aktionen steuern. Automatisierungsregeln optimieren den Einsatz von Automatisierungen in Azure Sentinel und ermöglichen es Ihnen, komplexe Workflows für Ihre Incident-Orchestrierungsprozesse zu vereinfachen.

Lesen Sie die [vollständige Erklärung der Automatisierungsregeln](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Das Feature **Automatisierungsregeln** befindet sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="playbooks"></a>Playbooks

Ein Playbook ist eine Sammlung von Reaktions- und Problembehandlungsmaßnahmen und -logik, die von Azure Sentinel als Routine ausgeführt werden können. Ein Playbook kann dabei helfen, Ihre Bedrohungsreaktion zu automatisieren und zu orchestrieren. Es kann in andere interne und externe Systemen integriert werden oder so konfiguriert werden, dass es automatisch als Reaktion auf bestimmte Alarme oder Incidents ausgeführt wird, wenn es durch eine Analyse- bzw. Automatisierungsregel ausgelöst wird. Es kann auch manuell auf der Seite „Incidents“ als Reaktion auf Warnungen ausgeführt werden.

Playbooks in Azure Sentinel basieren auf Workflows, die in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellt wurden, einem Clouddienst, mit dem Sie Aufgaben und Workflows systemübergreifend im gesamten Unternehmen planen, automatisieren und orchestrieren können. Dies bedeutet, dass Playbooks von der Leistungsfähigkeit und der Anpassbarkeit der Integrations- und Orchestrierungsfunktionen von Logic Apps und den benutzerfreundlichen Entwurfstools sowie der Skalierbarkeit, der Zuverlässigkeit und dem Servicelevel eines Tarif 1-Azure-Diensts profitieren können.

Lesen Sie die [vollständige Erklärung von Playbooks](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Azure Sentinel die Automatisierung verwendet, damit Ihr SOC effektiver und effizienter funktioniert.

- Informationen zur Automatisierung der Behandlung von Incidents finden Sie unter [Automatisierung der Vorfallbehandlung in Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Weitere Informationen zu den erweiterten Automatisierungsoptionen finden Sie unter [Automatisieren der Bedrohungsabwehr mit Playbooks in Azure Sentinel](automate-responses-with-playbooks.md).
- Hilfe bei der Implementierung von Automatisierungsregeln und Playbooks finden Sie im [Tutorial: Einsatz von Playbooks zur Automatisierung von Bedrohungsreaktionen in Azure Sentinel](tutorial-respond-threats-playbook.md).

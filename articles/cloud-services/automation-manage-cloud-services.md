---
title: Verwalten von Azure Cloud Services (klassisch) mithilfe von Azure Automation | Microsoft-Dokumentation
description: Erfahren Sie, wie der Azure Automation-Dienst zur angemessenen Verwaltung von Azure-Clouddiensten verwendet werden kann.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cc352a35308cbc77dc544c780ea7045cff561b02
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824515"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Verwalten von Azure Cloud Services (klassisch) mithilfe von Azure Automation

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur einfacheren Verwaltung Ihrer Azure Cloud Services genutzt werden kann.

## <a name="what-is-azure-automation"></a>Was ist Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet eine äußerst zuverlässige und hoch verfügbare Workflow-Ausführungs-Engine, die nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Wie kann Azure Automation Sie beim Verwalten von Azure Cloud Services unterstützen?
Azure Cloud Services können in Azure Automation über die PowerShell-Cmdlets verwaltet werden, die in den [Azure PowerShell-Tools](/powershell/)zur Verfügung stehen. Diese PowerShell-Cmdlets für Clouddienste sind in Azure Automation integriert und sofort einsatzfähig, sodass Sie alle Aufgaben in Zusammenhang mit der Verwaltung Ihrer Clouddienste innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.

Beispiele für die Verwendung von Azure Automation für die Verwaltung von Azure Cloud Services:

* [Kontinuierliche Bereitstellung eines Clouddiensts, wenn die CSCFG- oder CSPKG-Datei in Azure Blob Storage aktualisiert wird](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Paralleles Neustarten von Clouddienstinstanzen (Upgradedomäne für Upgradedomäne)](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Verwendung zum Verwalten von Azure Cloud Services vertraut gemacht haben, können Sie diese Links verwenden, um weitere Informationen zu Azure Automation zu erhalten.

* [Übersicht über Azure Automation](../automation/automation-intro.md)
* [Mein erstes Runbook](../automation/learn/automation-tutorial-runbook-graphical.md)
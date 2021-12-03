---
title: Onboarding von Servern mit Azure Arc-Unterstützung auf Microsoft Sentinel
description: Hier erfahren Sie, wie Sie Ihre Server mit Azure Arc-Unterstützung zu Microsoft Sentinel hinzufügen und deren Sicherheitsstatus proaktiv überwachen.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9a7f7fb8edb6edf18d9f8d5fed0608520cb3b80f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306489"
---
# <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Onboarding von Servern mit Azure Arc-Unterstützung bei Microsoft Sentinel

Dieser Artikel soll Ihnen helfen, das Onboarding Ihrer Server mit Azure Arc-Unterstützung auf [Microsoft Sentinel](../../sentinel/overview.md) durchzuführen und mit der Erfassung von Sicherheitsereignissen zu beginnen. Microsoft Sentinel ist eine Einzellösung für Warnungserkennung, Einblicke in Bedrohungen, proaktives Hunting und Reaktion auf Bedrohungen im gesamten Unternehmen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass die folgenden Anforderungen erfüllt werden:

- Einen [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/data-platform-logs.md). Weitere Informationen zu Log Analytics-Arbeitsbereichen finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../../azure-monitor/logs/design-logs-deployment.md).

- Microsoft Sentinel ist [in Ihrem Abonnement aktiviert](../../sentinel/quickstart-onboard.md).

- Der Computer oder Server ist mit Servern mit Azure Arc-Unterstützung verbunden.

## <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Onboarding von Servern mit Azure Arc-Unterstützung bei Microsoft Sentinel

Microsoft Sentinel verfügt über eine Reihe von Connectors für Microsoft-Lösungen, die sofort verfügbar sind und Echtzeitintegration bieten. Für physische und virtuelle Computer können Sie den Log Analytics-Agent installieren, mit dem die Protokolle gesammelt und an Microsoft Sentinel weitergeleitet werden. Azure Arc-aktivierte Server unterstützen die Bereitstellung des Protokollanalyse-Agenten mit den folgenden Methoden:

- Verwenden des VM-Erweiterungen-Frameworks.

    Mit dieser Funktion in Servern mit Azure Arc-Unterstützung können Sie die VM-Erweiterung für den Log Analytics-Agent auf einem Nicht-Azure-Windows- und/oder Linux-Server bereitstellen. VM-Erweiterungen können mit den folgenden Methoden auf Ihren hybriden Computern oder Servern verwaltet werden, die von Azure Arc-aktivierten Servern verwaltet werden:

    - Das [Azure-Portal](manage-vm-extensions-portal.md)
    - Die [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md)

- Verwenden von Azure Policy.

    Bei diesem Ansatz verwenden Sie die in der Azure Policy [Protokollanalyse-Agent zu Linux oder Windows Azure Arc Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) integrierte Richtlinie, um zu prüfen, ob der Protokollanalyse-Agent auf dem Azure Arc-aktivierten Server installiert ist. Wenn der Agent nicht installiert ist, wird er automatisch über einen Wartungstask bereitgestellt. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring), um den Log Analytics-Agent zu installieren und zu konfigurieren.

Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux mit Azure Policy zu installieren.

Sobald Ihre Server mit Arc-Unterstützung verbunden sind, beginnt das Streaming Ihrer Daten zu Microsoft Sentinel. Sie können dann mit der Arbeit mit Ihren Daten loslegen. Sie können die Protokolle in den [integrierten Arbeitsmappen](../../sentinel/get-visibility.md) anzeigen und mit der Erstellung von Abfragen in Log Analytics beginnen, um [die Daten zu untersuchen](../../sentinel/investigate-cases.md).

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](../../sentinel/detect-threats-built-in.md).

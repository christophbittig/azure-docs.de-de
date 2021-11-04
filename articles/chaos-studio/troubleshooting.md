---
title: Behandeln von häufig auftretenden Problemen mit Azure Chaos Studio
description: Erfahren Sie, wie Sie häufig auftretende Probleme bei der Nutzung von Azure Chaos Studio beheben.
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: afe37f4e7600f248f6ea3be50b0a1972946e381d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021589"
---
# <a name="azure-chaos-studio-troubleshooting"></a>Problembehandlung für Azure Chaos Studio

Wenn Sie Chaos Studio verwenden, können gelegentlich Probleme auftreten. In diesem Artikel werden die am häufigsten auftretenden Probleme sowie die Schritte zur Problembehandlung ausführlich erläutert.

## <a name="issues-due-to-prerequisites"></a>Probleme aufgrund von fehlenden Voraussetzungen

Einige Probleme werden durch fehlende Voraussetzungen verursacht. 

### <a name="why-do-agent-based-faults-fail-on-my-linux-virtual-machines"></a>Warum können Agent-basierte Fehler auf meinen Linux-VMs fehlschlagen?

Für die Fehler [CPU Pressure](chaos-studio-fault-library.md#cpu-pressure), [Physical Memory Pressure](chaos-studio-fault-library.md#physical-memory-pressure), [Disk I/O pressure](chaos-studio-fault-library.md#disk-io-pressure-linux) und [Arbitrary Stress-ng Stress](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) muss das Hilfsprogramm [stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) auf Ihrer VM installiert sein. Weitere Informationen finden Sie in den Abschnitten zu Fehlervoraussetzungen.

### <a name="my-aks-chaos-mesh-faults-are-failing"></a>Meine AKS Chaos Mesh-Fehler schlagen fehl

Bevor Sie Chaos Mesh-Fehler für AKS verwenden können, müssen Sie Chaos Mesh installieren. Weitere Anweisungen finden Sie im [Tutorial zu Chaos Mesh-Fehlern für AKS](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster).

## <a name="experiment-design-and-creation"></a>Entwerfen und Erstellen von Experimenten

### <a name="why-do-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity-when-i-try-to-create-an-experiment"></a>Warum wird der Fehler `The microsoft:agent provider requires a managed identity` angezeigt, wenn ich versuche, ein Experiment zu erstellen? 

Dieser Fehler tritt auf, wenn der Agent nicht auf Ihrer VM bereitgestellt wurde. Installationsanweisungen finden Sie unter [Erstellen und Ausführen eines Experiments, das Agent-basierte Fehler verwendet](chaos-studio-tutorial-agent-based.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported-what-does-this-mean"></a>Beim Erstellen eines Experiments wird der Fehler `The content media type '<null>' is not supported. Only 'application/json' is supported.` angezeigt. Was bedeutet das?

Wenn Sie den JSON-Code Ihres Experiments manuell erstellen, wird dieser Fehler durch falsch formatierten JSON-Code in Ihrer Experimentdefinition verursacht. Überprüfen Sie den Code auf Syntaxfehler, z. B. auf nicht übereinstimmende geschweifte oder eckige Klammern ({} and \[\]).

## <a name="experiment-execution"></a>Experimentausführung

### <a name="the-execution-status-of-my-experiment-is-failed-how-do-i-determine-what-went-wrong"></a>Der Ausführung meines Experiments ist „fehlgeschlagen“. Woran kann ich erkennen, was schiefgelaufen ist?

Klicken Sie auf der Seite „Experimente“ auf den Namen des Experiments, um zur Detailansicht des Experiments zu navigieren. Klicken Sie im Abschnitt „Verlauf“ auf den Link mit Details zur Ausführungsinstanz, um weitere Informationen zu erhalten.

![Experimentverlauf](images/run-experiment-history.png)

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>Wie erfasse ich Agent-Protokolle auf einer Linux-VM?

Führen Sie diesen Befehl auf der VM aus: `journalctl -u azure-chaos-agent`

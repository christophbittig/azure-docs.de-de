---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 5d31cea42dd51cee65e79c44b2a44615db027aff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298023"
---
Protokolldaten in Azure Monitor werden in einem Log Analytics-Arbeitsbereich gespeichert. Wenn Sie bereits einen Arbeitsbereich in Ihrem Abonnement erstellt haben, können Sie diesen verwenden. Sie können auch den Standardarbeitsbereich verwenden, der in jedem Azure-Abonnement erstellt wird. 

Wenn Sie eine neue Log Analytics-Instanz erstellen möchten, können Sie das folgende Verfahren verwenden. Wenn Sie eine vorhandene Instanz verwenden möchten, fahren Sie mit dem nächsten Abschnitt fort.

Wählen Sie im Azure-Portal unter **Alle Dienste** die Option **Log Analytics-Arbeitsbereiche** aus.

:::image type="content" source="media/azure-monitor-tutorial-workspace/azure-portal.png" lightbox="media/azure-monitor-tutorial-workspace/azure-portal.png" alt-text="Auswählen von „Log Analytics-Arbeitsbereiche“ im Azure-Portal":::

Klicken Sie auf **Erstellen**, um einen neuen Arbeitsbereich zu erstellen.

:::image type="content" source="media/azure-monitor-tutorial-workspace/create-workspace.png" lightbox="media/azure-monitor-tutorial-workspace/create-workspace.png" alt-text="Schaltfläche zum Erstellen eines Arbeitsbereichs":::

Wählen Sie auf der Registerkarte **Grundlagen** ein **Abonnement**, eine **Ressourcengruppe** und eine **Region** für den Arbeitsbereich aus. Diese können sich von den entsprechenden Angaben für die zu überwachende Ressource unterscheiden. Geben Sie unter **Name** einen Namen ein, der in allen Azure Monitor-Abonnements global eindeutig ist.

:::image type="content" source="media/azure-monitor-tutorial-workspace/workspace-basics.png" lightbox="media/azure-monitor-tutorial-workspace/workspace-basics.png" alt-text="„Grundlagen“ für den Arbeitsbereich":::


Klicken Sie zum Erstellen des Arbeitsbereichs auf **Überprüfen und erstellen**.
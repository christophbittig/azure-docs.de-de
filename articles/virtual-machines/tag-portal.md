---
title: Markieren eines virtuellen Computers mithilfe des Azure-Portals
description: Erfahren Sie etwas über das Markieren eines virtuellen Computers mithilfe des Azure-Portals.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 543317b2e1dcc0c55ac970212612f5266e5f84aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695361"
---
# <a name="tagging-a-vm-using-the-portal"></a>Markieren eines virtuellen Computers über das Portal

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel wird das Hinzufügen von Tags zu einer VM über das Portal beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 50 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden.


1. Navigieren Sie im Portal zu Ihrer App.
1. Wählen Sie in **Grundlagen** die Option **Klicken Sie hier, um Markierungen hinzuzufügen** aus.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot des Abschnitts „Grundlagen“ der VM-Seite.":::

1. Fügen Sie einen Wert für **Name** und **Wert** hinzu, und wählen Sie dann **Speichern** aus.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot der Seite zum Hinzufügen eines Schlüssel-Wert-Paars als Tag.":::

### <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Unter [Grundlegendes zu Ihrer Microsoft Azure-Rechnung und „Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen“ erfahren Sie, wie Tags Sie bei der Verwaltung Ihrer Azure-Ressourcenverwendung unterstützen können.](../cost-management-billing/understand/review-individual-bill.md)

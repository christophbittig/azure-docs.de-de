---
title: Markieren einer VM mithilfe einer Vorlage
description: Erfahren Sie etwas über das Markieren eines virtuellen Computers mithilfe einer Vorlage.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: 92be32279b812d4a8ff3baad3e3ccc881fe20049
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695325"
---
# <a name="tagging-a-vm-using-a-template"></a>Markieren einer VM mithilfe einer Vorlage

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel wird das Markieren einer VM in Azure mit einer Resource Manager-Vorlage beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 50 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden.

[Diese Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags) platziert Tags auf den folgenden Ressourcen: Compute (virtueller Computer), Speicher (Storage-Konto) und Netzwerk (öffentliche IP-Adresse, Virtual Network und Netzwerkschnittstelle). Diese Vorlage gilt für einen virtuellen Windows-Computer, kann aber für virtuelle Linux-Computer angepasst werden.

Klicken Sie unter dem **Vorlagenlink** auf die Schaltfläche [Bereitstellen in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags). Dadurch werden Sie zum [Azure-Portal](https://portal.azure.com/) weitergeleitet, in dem Sie diese Vorlage bereitstellen können.

![Einfache Bereitstellung mit Tags](./media/tag/deploy-to-azure-tags.png)

Diese Vorlage enthält die folgenden Tags: *Abteilung*, *Anwendung* und *Erstellt von*. Sie können diese Tags direkt in der Vorlage hinzufügen oder bearbeiten, wenn Sie andere Tagnamen wünschen.

![Azure-Tags in einer Vorlage](./media/tag/azure-tags-in-a-template.png)

Wie Sie sehen können, werden die Tags als Schlüssel-Wert-Paare definiert und durch einen Doppelpunkt (:) getrennt. Die Tags müssen im folgenden Format definiert werden:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Speichern Sie Vorlagendatei nach dem Bearbeiten mit den Tags Ihrer Wahl.

Anschließend können Sie im Abschnitt **Parameter bearbeiten** die Werte für die Tags ausfüllen.

![Bearbeiten von Tags im Azure-Portal](./media/tag/edit-tags-in-azure-portal.png)

Klicken Sie auf **Erstellen** , um diese Vorlage mit Ihren Tagwerten bereitzustellen.

### <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Unter [Grundlegendes zu Ihrer Microsoft Azure-Rechnung und „Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen“ erfahren Sie, wie Tags Sie bei der Verwaltung Ihrer Azure-Ressourcenverwendung unterstützen können.](../cost-management-billing/understand/review-individual-bill.md)

---
title: Verschieben eines Azure-Datenträgerpools (Vorschau) in ein anderes Abonnement
description: Sie erfahren, wie Sie einen Azure-Datenträgerpool in ein anderes Abonnement verschieben.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71b0fd25412f7269e7de717ee2b8f1b4df056011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082776"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>Verschieben eines Datenträgerpools (Vorschau) in ein anderes Abonnement

Das Verschieben eines Azure-Datenträgerpools (Vorschau) umfasst das Verschieben des Datenträgerpools selbst, der im Datenträgerpool enthaltenen Datenträger, der verwalteten Ressourcengruppe des Datenträgerpools und aller in der verwalteten Ressourcengruppe enthaltenen Ressourcen. Derzeit unterstützt Azure nicht das gleichzeitige Verschieben mehrerer Ressourcengruppen in ein anderes Abonnement. 

- Exportieren Sie die Vorlage Ihres vorhandenen Datenträgerpools.
- Löschen Sie den alten Datenträgerpool.
- Verschieben der Azure-Ressourcen, die zum Erstellen eines Datenträgerpools erforderlich sind
- Erneutes Bereitstellen des Datenträgerpools

## <a name="export-your-existing-disk-pool-template"></a>Exportieren der Vorlage Ihres vorhandenen Datenträgerpools

Exportieren Sie die Vorlage aus Ihrem vorhandenen Datenträgerpool, um die erneute Bereitstellung zu vereinfachen. Mit dieser Vorlage können Sie den Datenträgerpool in einem Abonnement Ihrer Wahl mit der gleichen Konfiguration erneut bereitstellen. In [diesem Artikel](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) erfahren Sie, wie Sie eine Vorlage aus einer Ressource exportieren.

## <a name="delete-the-old-disk-pool"></a>Löschen des alten Datenträgerpools

Nachdem Sie die Vorlage exportiert haben, löschen Sie den alten Datenträgerpool. Durch das Löschen des Datenträgerpools werden die Datenträgerpoolressource und die zugehörige verwaltete Ressourcengruppe entfernt. Informationen zum Löschen eines Datenträgerpools finden Sie in [diesem Artikel](disks-pools-deprovision.md).

## <a name="move-your-disks-and-virtual-network"></a>Verschieben Ihrer Datenträger und Ihres virtuellen Netzwerks

Nachdem der Datenträgerpool gelöscht wurde, können Sie das virtuelle Netzwerk und Ihre Datenträger und ggf. Ihre Clients in das Abonnement verschieben, in das Sie wechseln möchten. In [diesem Artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) erfahren Sie, wie Sie Azure-Ressourcen in ein anderes Abonnement verschieben.

## <a name="redeploy-your-disk-pool"></a>Erneutes Bereitstellen Ihres Datenträgerpools

Nachdem Sie Ihre anderen Ressourcen in das Abonnement verschoben haben, aktualisieren Sie die Vorlage Ihres alten Datenträgerpools, sodass alle Verweise auf Ihre Datenträger, das virtuelle Netzwerk, das Subnetz und die Clients jetzt auf ihre neuen Ressourcen-URIs verweisen. Sobald Sie diesen Schritt abgeschlossen haben, stellen Sie die Vorlage für das neue Abonnement erneut bereit. Informationen zum Bearbeiten und Bereitstellen einer Vorlage finden Sie in [diesem Artikel](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwalten Ihres Datenträgerpools finden Sie unter [Verwalten eines Datenträgerpools](disks-pools-manage.md).

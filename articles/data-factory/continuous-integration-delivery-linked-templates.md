---
title: Verwenden von verknüpften Resource Manager-Vorlagen
description: Erfahren Sie, wie Sie verknüpfte Resource Manager-Vorlagen mit Continuous Integration und Continuous Delivery in Azure Data Factory-Pipelines verwenden.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219517"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>Verknüpfte Resource Manager-Vorlagen mit CI/CD

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Sie Continuous Integration und Continuous Delivery (CI/CD) für Ihre Data Factorys eingerichtet haben, werden bei zunehmender Größe der Factory möglicherweise die Grenzen von Azure Resource Manager-Vorlagen überschritten. Ein Beispiel für eine Beschränkung ist die maximale Anzahl von Ressourcen in einer Resource Manager-Vorlage. Um die Verwendung umfangreicher Factorys zu ermöglichen, während die vollständige Resource Manager-Vorlage für eine Factory generiert wird, werden von Data Factory jetzt verknüpfte Resource Manager-Vorlagen generiert. Mit dieser Funktion wird die gesamte Nutzlast einer Factory auf mehrere Dateien aufgeteilt, damit für Sie keine Einschränkungen aufgrund von Grenzwerten bestehen.

## <a name="finding-the-linked-templates"></a>Suchen der verknüpften Vorlagen

Wenn Sie Git konfiguriert haben, werden die verknüpften Vorlagen zusammen mit den vollständigen Resource Manager-Vorlagen im Branch „adf_publish“ in einem neuen Ordner mit dem Namen „linkedTemplates“ generiert und gespeichert:

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="Ordner für verknüpfte Resource Manager-Vorlagen":::

Verknüpfte Resource Manager-Vorlagen verfügen normalerweise über eine Mastervorlage und eine Reihe von untergeordneten Vorlagen, die mit der Mastervorlage verknüpft sind. Die übergeordnete Vorlage hat den Namen „ArmTemplate_master.json“, und für untergeordnete Vorlagen werden Namen nach dem Muster „ArmTemplate_0.json“, „ArmTemplate_1.json“ usw. verwendet. 

## <a name="using-linked-templates"></a>Verwenden der verknüpften Vorlagen
Um anstelle der vollständigen Resource Manager-Vorlage verknüpfte Vorlagen zu verwenden, sollten Sie den CI/CD-Task so aktualisieren, dass dieser nicht auf „ArmTemplateForFactory.json“ (vollständige Resource Manager-Vorlage), sondern auf „ArmTemplate_master.json“ verweist. Für Resource Manager ist es auch erforderlich, dass Sie die verknüpften Vorlagen in ein Speicherkonto hochladen, damit von Azure während der Bereitstellung darauf zugegriffen werden kann. Weitere Informationen finden Sie unter [Bereitstellen von verknüpften Resource Manager-Vorlagen per VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Denken Sie daran, die Data Factory-Skripts vor und nach dem Bereitstellungstask zu Ihrer CI/CD-Pipeline hinzuzufügen.

Wenn Sie Git nicht konfiguriert haben, können Sie über die Option **Export ARM Template** (ARM-Vorlage exportieren) in der Liste **ARM-Vorlage** auf die verknüpften Vorlagen zugreifen.

Bei der Bereitstellung Ihrer Ressourcen geben Sie an, dass es sich bei der Bereitstellung entweder um ein inkrementelles Update oder um ein vollständiges Update handelt. Der Unterschied zwischen diesen beiden Modi besteht darin, wie Resource Manager vorhandene Ressourcen in der Ressourcengruppe behandelt, die nicht in der Vorlage enthalten sind. Weitere Informationen finden Sie unter [Bereitstellungsmodi](../azure-resource-manager/templates/deployment-modes.md).

## <a name="next-steps"></a>Nächste Schritte

- [Continuous Integration und Continuous Delivery (CI/CD) – Übersicht](continuous-integration-delivery.md)
- [Automatisieren von Continuous Integration mit Azure Pipelines-Releases](continuous-integration-delivery-automate-azure-pipelines.md)
- [Manuelles Höherstufen einer Resource Manager-Vorlage für jede Umgebung](continuous-integration-delivery-manual-promotion.md)
- [Verwenden benutzerdefinierter Parameter mit einer Resource Manager-Vorlage](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Verwenden einer Hotfix-Produktionsumgebung](continuous-integration-delivery-hotfix-environment.md)
- [Beispielskript vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md)
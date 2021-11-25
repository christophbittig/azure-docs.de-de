---
title: Manuelle Bereitstellung von Resource Manager Vorlagen
description: Erfahren Sie, wie Sie eine Resource Manager-Vorlage manuell auf mehrere Umgebungen mit kontinuierlicher Integration und Bereitstellung in Azure Data Factory übertragen können.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485ace0d04375a406c8013d60d45635a596964a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319505"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>Manuelles Heraufstufen einer Resource Manager-Vorlage in jede Umgebung

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Führen Sie die folgenden Schritte aus, um eine Resource Manager-Vorlage in jede Umgebung für die kontinuierliche Integration und Bereitstellung in Azure Data Factory zu übertragen.

## <a name="steps-to-manually-promote-a-template"></a>Schritte zum manuellen Heraufstufen einer Vorlage

1. Wechseln Sie in Ihrer Data Factory zu **Hub verwalten**, und wählen Sie im Abschnitt „Quellcodeverwaltung“ die Option **ARM-Vorlage** aus. Wählen Sie im Bereich **ARM-Vorlage** den Eintrag **Export ARM template** (ARM-Vorlage exportieren) aus, um die Resource Manager-Vorlage für Ihre Data Factory in die Entwicklungsumgebung zu exportieren.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="Exportieren einer Resource Manager-Vorlage":::

1. Wählen Sie in den Test- und Produktionsfactorys den Eintrag **Import ARM template** (ARM-Vorlage importieren) aus. Mit dieser Aktion gelangen Sie zum Azure-Portal, in dem sie die exportierte Vorlage importieren können. Wählen Sie **Eigene Vorlage im Editor erstellen** aus, um den Editor für Resource Manager-Vorlagen zu öffnen.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="Erstellen einer eigenen Vorlage"::: 

1. Wählen Sie die Option **Datei laden** und anschließend die generierte Resource Manager-Vorlage aus. Dies ist die Datei **arm_template.json** in der ZIP-Datei, die Sie in Schritt 1 exportiert haben.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="Bearbeiten der Vorlage":::

1. Geben Sie im Abschnitt mit den Einstellungen die Konfigurationswerte ein, z. B. die Anmeldeinformationen für verknüpfte Dienste, die für die Bereitstellung erforderlich sind. Wählen Sie anschließend die Option **Überprüfen und erstellen** aus, um die Resource Manager-Vorlage bereitzustellen.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="Einstellungsabschnitt":::

## <a name="next-steps"></a>Nächste Schritte

- [Continuous Integration und Continuous Delivery (CI/CD) – Übersicht](continuous-integration-delivery.md)
- [Automatisierung der kontinuierlichen Integration mit Azure Pipelines Releases](continuous-integration-delivery-automate-azure-pipelines.md)
- [Benutzerdefinierte Parameter mit einer Resource Manager-Vorlage verwenden](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Verknüpfte Resource Manager-Vorlagen](continuous-integration-delivery-linked-templates.md)
- [Verwenden einer Hotfix-Produktionsumgebung](continuous-integration-delivery-hotfix-environment.md)
- [Beispielskript für vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md)
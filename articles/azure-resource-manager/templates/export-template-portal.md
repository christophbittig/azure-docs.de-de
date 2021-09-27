---
title: Exportieren einer Vorlage im Azure-Portal
description: Verwenden Sie das Azure-Portal, um eine Azure Resource Manager-Vorlage aus Ressourcen in Ihrem Abonnement zu exportieren.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436254"
---
# <a name="use-azure-portal-to-export-a-template"></a>Azure-Portal zum Exportieren einer Vorlage verwenden

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Dieser Artikel zeigt, wie Sie Vorlagen über das **Portal** exportieren können. Für andere Optionen, siehe:

* [Vorlage exportieren mit Azure CLI](export-template-cli.md)
* [Vorlage exportieren mit Azure PowerShell](export-template-powershell.md)
* [REST-API-Export aus Ressourcengruppe](/rest/api/resources/resourcegroups/exporttemplate) und [REST-API-Export aus Bereitstellungshistorie](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportieren einer Vorlage aus einer Ressourcengruppe

So exportieren Sie eine oder mehrere Ressourcen aus einer Ressourcengruppe

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierenden Ressourcen enthält.

1. Wählen Sie mithilfe der Kontrollkästchen mindestens eine Ressource aus.  Wenn Sie alle Kontrollkästchen aktivieren möchten, aktivieren Sie das Kontrollkästchen links neben **Name**. Das Menüelement **Vorlage exportieren** wird erst aktiviert, wenn Sie mindestens eine Ressource ausgewählt haben.

   ![Exportieren aller Ressourcen](./media/export-template-portal/select-all-resources.png)

    Auf dem Screenshot ist nur das Speicherkonto ausgewählt.
1. Wählen Sie **Vorlage exportieren** aus.

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit.

   ![Anzeigen der Vorlage](./media/export-template-portal/show-template.png)

   **Parameter einschließen** ist standardmäßig ausgewählt.  Wenn diese Option ausgewählt ist, werden alle Vorlagenparameter eingeschlossen, wenn die Vorlage generiert wird. Wenn Sie Ihre eigenen Parameter erstellen möchten, deaktivieren Sie dieses Kontrollkästchen, um sie nicht einzuschließen.

## <a name="export-template-from-a-resource"></a>Exportieren einer Vorlage aus einer Ressource

So exportieren Sie eine Ressource

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierende Ressource enthält.

1. Wählen Sie die zu exportierende Ressource aus, um sie zu öffnen.

1. Wählen Sie für diese Ressource im linken Bereich **Vorlage exportieren** aus.

   ![Exportieren einer Ressource](./media/export-template-portal/export-single-resource.png)

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit. Die Vorlage enthält nur die einzelne Ressource. **Parameter einschließen** ist standardmäßig ausgewählt.  Wenn diese Option ausgewählt ist, werden alle Vorlagenparameter eingeschlossen, wenn die Vorlage generiert wird. Wenn Sie Ihre eigenen Parameter erstellen möchten, deaktivieren Sie dieses Kontrollkästchen, um sie nicht einzuschließen.

## <a name="download-template-before-deployment"></a>Vorlage vor der Bereitstellung herunterladen

Das Portal bietet die Möglichkeit, eine Vorlage vor der Bereitstellung herunterzuladen. Diese Option ist nicht über PowerShell oder Azure CLI verfügbar.

1. Wählen Sie den bereitzustellenden Azure-Dienst aus.

1. Geben Sie die Werte für den neuen Dienst ein.

1. Wählen Sie nach erfolgreicher Validierung, aber vor Beginn der Bereitstellung, **Vorlage zur Automatisierung herunterladen** aus.

   ![Herunterladen der Vorlage](./media/export-template-portal/download-before-deployment.png)

1. Die Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit.

## <a name="export-template-after-deployment"></a>Exportieren der Vorlage nach der Bereitstellung

Sie können die Vorlage exportieren, die für die Bereitstellung vorhandener Ressourcen verwendet wurde. Die Vorlage, die Sie erhalten, ist genau dieselbe, die für die Bereitstellung verwendet wurde.

1. Wählen Sie die zu exportierende Ressourcengruppe aus.

1. Wählen Sie den Link unter **Bereitstellungen** aus.

   ![Auswählen des Bereitstellungsverlaufs](./media/export-template-portal/select-deployment-history.png)

1. Wählen Sie eine der Bereitstellungen aus dem Bereitstellungsverlauf aus.

   ![Auswählen der Bereitstellung](./media/export-template-portal/select-details.png)

1. Wählen Sie **Vorlage** aus. Die für diese Bereitstellung verwendete Vorlage wird angezeigt und steht zum Herunterladen zur Verfügung.

   ![Auswählen der Vorlage](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Exportieren von Vorlagen mit der [Azure CLI](export-template-cli.md), [Azure PowerShell](export-template-powershell.md) oder der [REST-API](/rest/api/resources/resourcegroups/exporttemplate).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).
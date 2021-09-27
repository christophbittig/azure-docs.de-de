---
title: Anzeigen der Kosten für verwaltete Onlineendpunkte (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die Kosten für einen verwalteten Onlineendpunkt in Azure Machine Learning anzeigen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, deploy, devplatv2
ms.openlocfilehash: bb07d499007cbbc0cb502611056aaef583cbb16a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588806"
---
# <a name="view-costs-for-an-azure-machine-learning-managed-online-endpoint-preview"></a>Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)

Hier erfahren Sie, wie Sie die Kosten für einen verwalteten Onlineendpunkt (Vorschau) anzeigen. Die Kosten für Ihre Endpunkte fallen für den zugeordneten Arbeitsbereich an. Sie können die Kosten für einen bestimmten Endpunkt mithilfe von Tags anzeigen.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!IMPORTANT]
> In diesem Artikel wird nur das Anzeigen von Kosten für verwaltete Azure Machine Learning-Onlineendpunkte (Vorschau) behandelt. Verwaltete Onlineendpunkte unterscheiden sich von anderen Ressourcen, da dafür Tags zum Nachverfolgen von Kosten verwendet werden müssen. Weitere Informationen zum Anzeigen der Kosten anderer Azure-Ressourcen finden Sie unter [Schnellstart: Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md).

## <a name="prerequisites"></a>Voraussetzungen

- Bereitstellen eines verwalteten Azure Machine Learning-Onlineendpunkts (Vorschau)
- Mindestens Zugriff vom Typ [Abrechnungsleser](../role-based-access-control/role-assignments-portal.md) für das Abonnement, in dem der Endpunkt bereitgestellt wird

## <a name="view-costs"></a>Anzeigen von Kosten

Navigieren Sie zur Seite **Kostenanalyse** für Ihr Abonnement:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Kostenanalyse** für Ihr Abonnement aus.

    [![Kostenanalyse für verwaltete Onlineendpunkte: Screenshot eines Abonnements im Azure-Portal mit der rot umrandeten Schaltfläche „Kostenanalyse“ auf der linken Seite](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png#lightbox)

Erstellen Sie einen Filter, um den Bereich Ihrer Daten auf die Azure Machine Learning-Arbeitsbereichsressource festzulegen:

1. Wählen Sie auf der oberen Navigationsleiste die Option **Filter hinzufügen** aus.

1. Wählen Sie in der ersten Filterdropdownliste die Option **Ressource** als Filtertyp aus.

1. Wählen Sie in der zweiten Filterdropdownliste Ihren Azure Machine Learning-Arbeitsbereich aus.

    [![Kostenanalyse für verwaltete Onlineendpunkte: Screenshot der Kostenanalyseansicht mit der rot umrandeten Schaltfläche „Filter hinzufügen“ oben rechts](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png#lightbox)

Erstellen Sie einen Tagfilter, um Ihren verwalteten Onlineendpunkt und/oder Ihre verwaltete Onlinebereitstellung anzuzeigen:
1. Wählen Sie **Filter hinzufügen** > **Tag** > **azuremlendpoint** aus: „\<your endpoint name>“. 
1. Wählen Sie **Filter hinzufügen** > **Tag** > **azuremldeployment** aus: „\<your deployment name>“.

    > [!NOTE]
    > Die Dollarzeichen in diesem Bild stehen für fiktive Werte und spiegeln nicht die tatsächlichen Kosten wider.

    [![Kostenanalyse für verwaltete Onlineendpunkte: Screenshot der Kostenanalyseansicht mit den rot umrandeten Schaltflächen „Tag“ oben rechts](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte
- [Was sind Endpunkte?](concept-endpoints.md)
- Erfahren Sie, wie Sie Ihren [verwalteten Onlineendpunkt überwachen](./how-to-monitor-online-endpoints.md).
- [Bereitstellen verwalteter Onlineendpunkte mit der Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Bereitstellen verwalteter Onlineendpunkte mit Studio](how-to-use-managed-online-endpoint-studio.md)
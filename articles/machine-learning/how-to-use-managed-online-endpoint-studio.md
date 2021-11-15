---
title: Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie verwaltete Onlineendpunkte (Vorschau) mit Azure Machine Learning Studio erstellen und verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2, studio
ms.author: ssambare
author: shivanissambare
ms.reviewer: laobri
ms.date: 10/21/2021
ms.openlocfilehash: fee2a8211b90c2b7dbc06a1e64f047e28031eaa6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560378"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>Erstellen und Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio

Es wird beschrieben, wie Sie Studio zum Erstellen und Verwalten Ihrer verwalteten Onlineendpunkte (Vorschau) in Azure Machine Learning verwenden. Nutzen Sie verwaltete Onlineendpunkte, um für die Produktion bestimmte Bereitstellungen zu optimieren. Weitere Informationen zu verwalteten Onlineendpunkten finden Sie unter [Was sind Endpunkte?](concept-endpoints.md).

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines verwalteten Onlineendpunkts
> * Anzeigen von verwalteten Onlineendpunkten
> * Hinzufügen einer Bereitstellung zu einem verwalteten Onlineendpunkt
> * Aktualisieren von verwalteten Onlineendpunkten
> * Löschen von verwalteten Onlineendpunkten und Bereitstellungen

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Das Beispielrepository: Klonen Sie das [AzureML-Beispielrepository](https://github.com/Azure/azureml-examples). In diesem Artikel werden die Ressourcen in `/cli/endpoints/online` verwendet.

## <a name="create-a-managed-online-endpoint-preview"></a>Erstellen eines verwalteten Onlineendpunkts (Vorschau)

Verwenden Sie Studio, um direkt in Ihrem Browser einen verwalteten Onlineendpunkt (Vorschau) zu erstellen. Beim Erstellen eines verwalteten Onlineendpunkts in Studio müssen Sie eine erste Bereitstellung definieren. Es ist nicht möglich, einen leeren verwalteten Onlineendpunkt zu erstellen.

1. Wechseln Sie zum [Azure Machine Learning-Studio](https://ml.azure.com).
1. Wählen Sie in der linken Navigationsleiste die Seite **Endpunkte** aus.
1. Wählen Sie **+ Erstellen (Vorschau)** aus.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="Screenshot: Erstellen eines verwalteten Onlineendpunkts über die Registerkarte „Endpunkte“":::

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" lightbox="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" alt-text="Screenshot: Assistent zum Erstellen eines verwalteten Onlineendpunkts":::

### <a name="follow-the-setup-wizard-to-configure-your-managed-online-endpoint"></a>Befolgen Sie die Anweisungen des Setup-Assistenten, um Ihren verwalteten Onlineendpunkt zu konfigurieren.

1. Unser [Beispielmodell](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1/model) und [Bewertungsskript](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py) finden Sie unter [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1) und können es dort verwenden.
1. Im Schritt **Umgebung** können Sie die zusammengestellte Umgebung **AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference** auswählen.

Sie können einen verwalteten Onlineendpunkt in Studio auch über die Seite **Modelle** erstellen. Dies ist eine einfache Möglichkeit, um einer vorhandenen verwalteten Onlinebereitstellung ein Modell hinzuzufügen.

1. Wechseln Sie zum [Azure Machine Learning-Studio](https://ml.azure.com).
1. Wählen Sie auf der linken Navigationsleiste die Seite **Modelle** aus.
1. Wählen Sie ein Modell aus, indem Sie im Kreis neben dem Modellnamen ein Häkchen einfügen.
1. Wählen Sie **Bereitstellen** > **Auf Echtzeit-Endpunkt bereitstellen (Vorschau)** aus.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" alt-text="Screenshot: Erstellen eines verwalteten Onlineendpunkts über die Benutzeroberfläche „Modelle“":::

## <a name="view-managed-online-endpoints-preview"></a>Anzeigen von verwalteten Onlineendpunkten (Vorschau)

Sie können Ihre verwalteten Onlineendpunkte (Vorschau) auf der Seite **Endpunkte** anzeigen. Auf der Seite mit den Endpunktdetails finden Sie wichtige Informationen, z. B. Endpunkt-URI, Status, Testtools, Aktivitätsmonitore, Bereitstellungsprotokolle und Beispielnutzungscode:

1. Wählen Sie in der linken Navigationsleiste die Option **Endpunkte** aus.
1. (Optional) Erstellen Sie unter **Computetyp** einen **Filter**, um nur die Computetypen für **Verwaltet** anzuzeigen.
1. Wählen Sie einen Endpunktnamen aus, um die Seite mit den Endpunktdetails anzuzeigen.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" alt-text="Screenshot: Detailansicht des verwalteten Endpunkts":::

### <a name="test"></a>Test

Verwenden Sie auf der Seite mit den Endpunktdetails die Registerkarte **Test**, um Ihre verwaltete Onlinebereitstellung zu testen. Geben Sie eine Beispieleingabe ein, und zeigen Sie die Ergebnisse an.

1. Wählen Sie auf der Detailseite des Endpunkts die Registerkarte **Test** aus.
1. Wählen Sie in der Dropdownliste die Bereitstellung aus, die Sie testen möchten.
1. Geben Sie eine Beispieleingabe ein.
1. Klicken Sie auf **Test**.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" lightbox="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="Screenshot: Testen einer Bereitstellung durch das Angeben von Beispieldaten direkt in Ihrem Browser":::

### <a name="monitoring"></a>Überwachung

Verwenden Sie die Registerkarte **Überwachung**, um für Ihren verwalteten Onlineendpunkt allgemeine Aktivitätsmonitorgraphen anzuzeigen.

Zur Verwendung der Registerkarte „Überwachung“ müssen Sie beim Erstellen des Endpunkts die Option **Enable Application Insight diagnostic and data collection** (Application Insights-Diagnose und Datenerfassung aktivieren) aktivieren.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="Screenshot: Überwachen von Metriken auf Endpunktebene in Studio":::

Weitere Informationen zum Anzeigen von zusätzlichen Monitoren und Warnungen finden Sie unter [Überwachen verwalteter Onlineendpunkte](how-to-monitor-online-endpoints.md).

## <a name="add-a-deployment-to-a-managed-online-endpoint"></a>Hinzufügen einer Bereitstellung zu einem verwalteten Onlineendpunkt

Sie können zu Ihrem vorhandenen verwalteten Onlineendpunkt eine Bereitstellung hinzufügen.

Gehen Sie auf der **Seite mit den Endpunktdetails** wie folgt vor:

1. Wählen Sie auf der [Seite mit den Endpunktdetails](#view-managed-online-endpoints-preview) die Schaltfläche **+ Bereitstellung hinzufügen** aus.
2. Befolgen Sie die Anleitung, um die Bereitstellung abzuschließen.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" alt-text="Screenshot: Option „Bereitstellung hinzufügen“ auf der Seite „Endpunktdetails“":::

Alternativ können Sie die Seite **Modelle** verwenden, um eine Bereitstellung hinzuzufügen:

1. Wählen Sie auf der linken Navigationsleiste die Seite **Modelle** aus.
1. Wählen Sie ein Modell aus, indem Sie im Kreis neben dem Modellnamen ein Häkchen einfügen.
1. Wählen Sie **Bereitstellen** > **Auf Echtzeit-Endpunkt bereitstellen (Vorschau)** aus.
1. Geben Sie an, dass Sie einen vorhandenen verwalteten Onlineendpunkt bereitstellen möchten.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" lightbox="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" alt-text="Screenshot: Option „Bereitstellung hinzufügen“ auf der Seite „Modelle“":::

> [!NOTE]
> Sie können den Datenverkehrsausgleich zwischen Bereitstellungen auf einem Endpunkt anpassen, wenn Sie eine neue Bereitstellung hinzufügen.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" lightbox="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="Screenshot: Verwenden von Schiebereglern zum Steuern der Verteilung des Datenverkehrs auf mehrere Bereitstellungen":::

## <a name="update-managed-online-endpoints-preview"></a>Aktualisieren von verwalteten Onlineendpunkten (Vorschau)

Sie können den Prozentsatz des Bereitstellungsdatenverkehrs und die Anzahl der Instanzen über Azure Machine Learning Studio aktualisieren.

### <a name="update-deployment-traffic-allocation"></a>Aktualisieren der Datenverkehrszuordnung für Bereitstellungen

Nutzen Sie die **Datenverkehrszuordnung für Bereitstellungen**, um den Prozentsatz an eingehenden Anforderungen zu steuern, die an die einzelnen Bereitstellungen eines Endpunkts gesendet werden.

1. Wählen Sie auf der Seite mit den Endpunktdetails die Option **Datenverkehrszuordnung aktualisieren** aus.
2. Passen Sie Ihren Datenverkehr an, und wählen Sie **Aktualisieren** aus.

> [!TIP]
> Die Summe für **Prozentsatz des Datenverkehrs gesamt** muss entweder 0 % (Datenverkehr deaktivieren) oder 100 % (Datenverkehr aktivieren) ergeben.

### <a name="update-deployment-instance-count"></a>Aktualisieren der Anzahl von Bereitstellungsinstanzen

Befolgen Sie die unten angegebene Anleitung, um eine einzelne Bereitstellung hoch- oder herunterzuskalieren, indem Sie die Anzahl von Instanzen anpassen:

1. Gehen Sie auf der Seite mit den Endpunktdetails wie folgt vor: Suchen Sie nach der Karte für die Bereitstellung, die Sie aktualisieren möchten.
1. Wählen Sie auf der Karte mit den Bereitstellungsdetails das **Symbol „Bearbeiten“** aus.
1. Aktualisieren Sie die Anzahl der Instanzen.
1. Klicken Sie auf **Aktualisieren**.

## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>Löschen von verwalteten Onlineendpunkten und Bereitstellungen (Vorschau)

Hier wird beschrieben, wie Sie einen vollständigen verwalteten Onlineendpunkt (Vorschau) und die zugehörigen Bereitstellungen (Vorschau) löschen. Sie können auch eine einzelne Bereitstellung von einem verwalteten Onlineendpunkt löschen.

### <a name="delete-a-managed-online-endpoint"></a>Löschen eines verwalteten Onlineendpunkts

Beim Löschen eines verwalteten Onlineendpunkts werden auch alle zugehörigen Bereitstellungen gelöscht.

1. Wechseln Sie zum [Azure Machine Learning-Studio](https://ml.azure.com).
1. Wählen Sie in der linken Navigationsleiste die Seite **Endpunkte** aus.
1. Wählen Sie einen Endpunkt aus, indem Sie im Kreis neben dem Modellnamen ein Häkchen einfügen.
1. Klicken Sie auf **Löschen**.

Alternativ können Sie einen verwalteten Onlineendpunkt auch direkt auf der [Seite mit den Endpunktdetails](#view-managed-online-endpoints-preview) löschen. 

### <a name="delete-an-individual-deployment"></a>Löschen einer einzelnen Bereitstellung

Führen Sie die folgenden Schritte aus, um eine einzelne Bereitstellung von einem verwalteten Onlineendpunkt zu löschen. Dies hat Auswirkungen auf die anderen Bereitstellungen des verwalteten Onlineendpunkts:

> [!NOTE]
> Sie können keine Bereitstellung löschen, der Datenverkehr zugeordnet ist. Vor dem Löschen müssen Sie die [Datenverkehrszuordnung](#update-deployment-traffic-allocation) für die Bereitstellung zunächst auf „0 %“ festlegen.

1. Wechseln Sie zum [Azure Machine Learning-Studio](https://ml.azure.com).
1. Wählen Sie in der linken Navigationsleiste die Seite **Endpunkte** aus.
1. Wählen Sie Ihren verwalteten Onlineendpunkt aus.
1. Suchen Sie auf der Seite mit den Endpunktdetails nach der Bereitstellung, die Sie löschen möchten.
1. Wählen Sie das **Symbol „Löschen“** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie verwaltete Azure Machine Learning-Onlineendpunkte verwenden. Informationen zu den nächsten Schritten:

- [Was sind Endpunkte?](concept-endpoints.md)
- [Bereitstellen verwalteter Onlineendpunkte mit der Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Überwachen verwalteter Onlineendpunkte](how-to-monitor-online-endpoints.md)
- [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](./how-to-troubleshoot-online-endpoints.md)
- [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
- [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
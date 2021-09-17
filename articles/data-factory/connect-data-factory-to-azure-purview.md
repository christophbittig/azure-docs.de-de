---
title: Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview
description: Erfahren Sie, wie Sie eine Verbindung zwischen einer Data Factory und Azure Purview herstellen.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/24/2021
ms.openlocfilehash: e38c990622806e5e769626acb84377fc468a25a2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966504"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview (Vorschau)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) ist ein einheitlicher Datengovernancedienst, der Sie bei der Verwaltung und Steuerung von lokalen Daten, von Daten in mehreren Clouds sowie von SaaS-Daten (Software-as-a-Service) unterstützt. Sie können Ihre Data Factory mit Azure Purview verbinden. Diese Verbindung ermöglicht Ihnen die Verwendung von Azure Purview zum Erfassen von Herkunftsdaten sowie die Ermittlung und Untersuchung von Azure Purview-Ressourcen.

## <a name="connect-data-factory-to-azure-purview"></a>Herstellen einer Verbindung zwischen Data Factory und Azure Purview

Sie haben zwei Optionen, um zwischen einer Data Factory und Azure Purview eine Verbindung herzustellen:

- [Herstellen einer Verbindung mit einem Azure Purview-Konto in der Data Factory](#connect-to-azure-purview-account-in-data-factory)
- [Registrieren von Data Factory bei Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Herstellen einer Verbindung mit einem Azure Purview-Konto in der Data Factory

Sie müssen in Ihrer Data Factory die Rolle **Besitzer** oder **Mitwirkender** haben, um eine Verbindung mit einem Azure Purview-Konto herstellen zu können.

So stellen Sie die Verbindung über die Erstellungsbenutzeroberfläche für Data Factory her:

1. Navigieren Sie auf der Benutzeroberfläche für die ADF-Erstellung zu **Verwalten** -> **Azure Purview**, und wählen Sie die Option **Connect to a Purview account** (Mit einem Purview-Konto verbinden) aus. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Screenshot der Registrierung eines Purview-Kontos":::

2. Wählen Sie die Option **Aus Azure-Abonnement** oder **Manuell eingeben** aus. Unter **Aus Azure-Abonnement** können Sie das Konto auswählen, auf das Sie Zugriff haben.

3. Nachdem die Verbindung hergestellt wurde, wird der Name des Purview-Kontos auf der Registerkarte **Purview-Konto** angezeigt.

Die Purview-Verbindungsinformationen werden in der Data Factory-Ressource wie im folgenden Beispiel gespeichert. Wenn Sie die Verbindung programmgesteuert herstellen möchten, können Sie die Data Factory aktualisieren und die Einstellungen vom Typ `purviewConfiguration` hinzufügen.

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

### <a name="register-data-factory-in-azure-purview"></a>Registrieren von Data Factory bei Azure Purview

Informationen zum Registrieren von Data Factory in Azure Purview finden Sie unter [Herstellen einer Verbindung zwischen Azure Data Factory und Azure Purview](../purview/how-to-link-azure-data-factory.md).

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Die verwaltete Identität für Data Factory wird verwendet, um Pushvorgänge für Herkunftsdaten von der Data Factory zu Purview zu authentifizieren. 

- Weisen Sie für Purview-Konten, die **am oder nach dem 18. August 2021** erstellt wurden, der verwalteten Identität für Data Factory die Rolle **Datenkurator** für Ihre Purview-**Stammsammlung** zu. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](../purview/catalog-permissions.md) und unter [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

    Beim Herstellen einer Verbindung zwischen Data Factory und Purview über die Erstellungsbenutzeroberfläche versucht ADF, eine solche Rollenzuweisung automatisch hinzuzufügen. Wenn Sie für die Purview-Stammsammlung die Rolle **Sammlungsadministrator** haben, wird dieser Vorgang erfolgreich durchgeführt.

- Weisen Sie für Purview-Konten, die **vor dem 18. August 2021** erstellt wurden, der verwalteten Identität für Data Factory die in Azure integrierte Rolle [**Datenkurator für Purview**](../role-based-access-control/built-in-roles.md#purview-data-curator) für Ihr Purview-Konto zu. Weitere Informationen zu Legacyberechtigungen für die Zugriffssteuerung in Azure Purview finden Sie [hier](../purview/catalog-permissions.md#legacy-permission-guide).

    Beim Herstellen einer Verbindung zwischen Data Factory und Purview über die Erstellungsbenutzeroberfläche versucht ADF, eine solche Rollenzuweisung automatisch hinzuzufügen. Wenn Sie für das Purview-Konto die in Azure integrierte Rolle **Besitzer** oder **Benutzerzugriffsadministrator** haben, wird dieser Vorgang erfolgreich durchgeführt.

Möglicherweise wird die folgende Warnung angezeigt, wenn Sie die Berechtigung zum Lesen von Purview-Rollenzuweisungsinformationen haben und die erforderliche Rolle nicht zugewiesen wird. Vergewissern Sie sich, dass die Verbindung für das pipelinebasierte Pushen von Herkunftsinformationen ordnungsgemäß festgelegt wurde. Wechseln Sie zu Ihrem Purview-Konto, und überprüfen Sie, ob der verwalteten Identität für Data Factory die Rolle **Datenkurator für Purview** zugewiesen wurde. Fügen Sie die Rollenzuweisung manuell hinzu, wenn dies nicht der Fall ist.

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Screenshot der Warnung beim Registrieren eines Purview-Kontos":::

## <a name="report-lineage-data-to-azure-purview"></a>Übermitteln von Herkunftsdaten an Azure Purview

Nachdem Sie eine Verbindung zwischen der Data Factory und einem Purview-Konto hergestellt haben und Pipelines ausführen, pusht Data Factory Herkunftsinformationen an das Purview-Konto. Ausführliche Informationen zu den unterstützten Funktionen finden Sie unter [Unterstützte Azure Data Factory-Aktivitäten](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Eine exemplarische End-to-End-Vorgehensweise finden Sie im [Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Ermitteln und Erkunden von Daten mit Purview

Sobald Sie die Data Factory mit einem Purview-Konto verbunden haben, können Sie über die Suchleiste oben in der Mitte der Benutzeroberfläche für die Data Factory-Erstellung nach Daten suchen und Aktionen ausführen. Weitere Informationen finden Sie unter [Ermitteln und Erkunden von Daten in ADF mithilfe von Purview](how-to-discover-explore-purview-data.md).

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](tutorial-push-lineage-to-purview.md)

[Ermitteln und Erkunden von Daten in ADF mithilfe von Purview](how-to-discover-explore-purview-data.md)

[Azure Purview: Leitfaden zur Datenkatalogherkunft](../purview/catalog-lineage-user-guide.md)
---
title: Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview
description: Erfahren Sie, wie Sie eine Verbindung zwischen einer Data Factory und Azure Purview herstellen.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 10/25/2021
ms.openlocfilehash: 026dc4cb24c5e7b51fc5eec918cf92d3b6b23090
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846138"
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

Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, erstellen Sie die verwalteten privaten Endpunkte für Purview. Erfahren Sie mehr darüber, wie Sie Data Factory [Zugriff auf ein abgesichertes Purview-Konto](how-to-access-secured-purview-account.md) ermöglichen. Sie können dies entweder während der ersten Verbindung durchführen oder eine bestehende Verbindung später bearbeiten.

Die Purview-Verbindungsinformationen werden in der Data Factory-Ressource wie im folgenden Beispiel gespeichert. Wenn Sie die Verbindung programmgesteuert herstellen möchten, können Sie die Data Factory aktualisieren und die Einstellungen vom Typ `purviewConfiguration` hinzufügen. Wenn Sie die Herkunft aus SSIS-Aktivitäten pushen möchten, fügen Sie zusätzlich ein `catalogUri`-Tag hinzu.

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
    ...
    "identity": {...},
    "tags": {
        "catalogUri": "<PurviewAccountName>.purview.azure.com/catalog //Note: used for SSIS lineage only"
    }
}
```

### <a name="register-data-factory-in-azure-purview"></a>Registrieren von Data Factory bei Azure Purview

Informationen zum Registrieren von Data Factory in Azure Purview finden Sie unter [Herstellen einer Verbindung zwischen Azure Data Factory und Azure Purview](../purview/how-to-link-azure-data-factory.md).

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Die verwaltete Identität für Data Factory wird verwendet, um Pushvorgänge für Herkunftsdaten von der Data Factory zu Purview zu authentifizieren. 

Erteilen Sie der verwalteten Identität der Data Factory die Rolle **Datenkurator** für Ihre Purview-**Stammsammlung**. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](../purview/catalog-permissions.md) und unter [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

Beim Herstellen einer Verbindung zwischen Data Factory und Purview über die Erstellungsbenutzeroberfläche versucht ADF, eine solche Rollenzuweisung automatisch hinzuzufügen. Wenn Sie die Rolle **Sammlungsadministratoren** für die Purview-Stammsammlung innehaben und über Ihr Netzwerk Zugriff auf das Purview-Konto haben, wird dieser Vorgang erfolgreich ausgeführt.

## <a name="monitor-purview-connection"></a>Überwachen der Purview-Verbindung

Sobald die Data Factory mit einem Purview-Konto verbunden wurde, wird die folgende Seite mit Details zu den aktivierten Integrationsfunktionen angezeigt.

:::image type="content" source="./media/data-factory-purview/monitor-purview-connection-status.png" alt-text="Screenshot: Überwachen des Integrationsstatus zwischen Azure Data Factory und Purview.":::

Für **Datenherkunft – Pipeline** kann einer der folgenden Status angezeigt werden:

- **Verbunden**: Die Data Factory wurde erfolgreich mit dem Purview-Konto verbunden. Das bedeutet, dass Data Factory einem Purview-Konto zugeordnet ist und über die Berechtigung zum Pushen der Herkunft verfügt. Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, müssen Sie auch sicherstellen, dass die Integration Runtime, die zum Ausführen der Aktivitäten und Durchführen von Pushbenachrichtigungen für die Herkunft verwendet wird, das Purview-Konto erreichen kann. Weitere Informationen finden Sie unter [Zugriff auf ein gesichertes Azure Purview-Konto von Azure Data Factory aus](how-to-access-secured-purview-account.md).
- **Getrennt**: Die Data Factory kann die Herkunft nicht an Purview pushen, da der verwalteten Identität der Data Factory nicht die Rolle als Purview-Datenkurator gewährt wird. Wechseln Sie zum Beheben dieses Problems zu Ihrem Purview-Konto, um die Rollenzuweisungen zu überprüfen, und gewähren Sie die Rolle ggf. manuell. Weitere Informationen finden Sie im Abschnitt zum [Einrichten der Authentifizierung](#set-up-authentication).
- **Unbekannt**: Data Factory kann des Status nicht feststellen. Mögliche Gründe:

    - Das Purview-Konto ist von Ihrem aktuellen Netzwerk aus nicht erreichbar, da das Konto durch eine Firewall geschützt ist. Sie können die ADF-Benutzeroberfläche stattdessen über ein privates Netzwerk mit Konnektivität zu Ihrem Purview-Konto aufrufen.
    - Sie haben keine Berechtigung zum Überprüfen von Rollenzuweisungen für das Purview-Konto. Sie können sich an den Purview-Kontoadministrator wenden, um die Rollenzuweisungen für Sie überprüfen zu lassen. Weitere Informationen zur benötigten Purview-Rolle finden Sie im Abschnitt zum [Einrichten der Authentifizierung](#set-up-authentication).

## <a name="report-lineage-data-to-azure-purview"></a>Übermitteln von Herkunftsdaten an Azure Purview

Nachdem Sie eine Verbindung zwischen der Data Factory und einem Purview-Konto hergestellt haben und Pipelines ausführen, pusht Data Factory Herkunftsinformationen an das Purview-Konto. Ausführliche Informationen zu den unterstützten Funktionen finden Sie unter [Unterstützte Azure Data Factory-Aktivitäten](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Eine exemplarische End-to-End-Vorgehensweise finden Sie im [Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Ermitteln und Erkunden von Daten mit Purview

Sobald Sie die Data Factory mit einem Purview-Konto verbunden haben, können Sie über die Suchleiste oben in der Mitte der Benutzeroberfläche für die Data Factory-Erstellung nach Daten suchen und Aktionen ausführen. Weitere Informationen finden Sie unter [Ermitteln und Erkunden von Daten in ADF mithilfe von Purview](how-to-discover-explore-purview-data.md).

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](tutorial-push-lineage-to-purview.md)

[Ermitteln und Erkunden von Daten in ADF mithilfe von Purview](how-to-discover-explore-purview-data.md)

[Zugreifen auf ein geschütztes Purview-Konto](how-to-access-secured-purview-account.md)

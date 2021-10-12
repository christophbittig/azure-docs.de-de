---
title: Verbinden eines Synapse-Arbeitsbereichs mit Azure Purview 
description: Hier erfahren Sie, wie Sie einen Synapse-Arbeitsbereich mit einem Azure Purview-Konto verbinden.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/29/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 894df32142cf29e59e40b1e9218f4090bbda93f0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351607"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>Schnellstart: Verbinden eines Synapse-Arbeitsbereichs mit einem Azure Purview-Konto

In dieser Schnellstartanleitung registrieren Sie ein Azure Purview-Konto in einem Synapse-Arbeitsbereich. Dank dieser Verbindung können Sie Azure Purview-Ressourcen ermitteln, mit ihnen über Synapse-Funktionen interagieren und Herkunftsinformationen an Purview pushen.

Sie können die folgenden Aufgaben in Synapse ausführen:
- Suchen von Purview-Objekten anhand von Schlüsselwörtern mithilfe des Suchfelds im oberen Bereich 
- Verstehen der Daten basierend auf den Metadaten, der [Herkunft](../../purview/catalog-lineage-user-guide.md) und den Anmerkungen 
- Verbinden dieser Daten mit Ihrem Arbeitsbereich mit verknüpften Diensten oder Integrationsdatasets 
- Analysieren dieser Datasets mit Synapse Apache Spark, Synapse SQL und Datenflüssen 
- Ausführen von Pipelines und [Pushen von Herkunftsinformationen an Purview](../../purview/how-to-lineage-azure-synapse-analytics.md)

## <a name="prerequisites"></a>Voraussetzungen 
- [Azure Purview-Konto](../../purview/create-catalog-portal.md) 
- [Synapse-Arbeitsbereich](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Berechtigungen für das Herstellen einer Verbindung mit einem Azure Purview-Konto 

Zum Herstellen einer Verbindung zwischen einem Azure Purview-Konto und einem Synapse-Arbeitsbereich benötigen Sie die Rolle **Mitwirkender** im Synapse-Arbeitsbereich (über das IAM im Azure-Portal) sowie Zugriff auf das entsprechende Azure Purview-Konto. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Herstellen einer Verbindung mit einem Azure Purview-Konto  

Führen Sie die folgenden Schritte aus, um eine Verbindung mit einem Azure Purview-Konto herzustellen:

1. Navigieren Sie zu [https://web.azuresynapse.net](https://web.azuresynapse.net), und melden Sie sich bei Ihrem Purview-Arbeitsbereich an. 
2. Navigieren Sie zu **Verwalten** -> **Azure Purview**, und wählen Sie **Connect to a Purview account** (Mit einem Purview-Konto verbinden) aus.
3. Sie können **Aus Azure-Abonnement** oder **Manuell eingeben** auswählen. Unter **Aus Azure-Abonnement** können Sie das Konto auswählen, auf das Sie Zugriff haben.
4. Nachdem die Verbindung hergestellt wurde, wird der Name des Purview-Kontos auf der Registerkarte für das **Azure Purview-Konto** angezeigt. 

Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, erstellen Sie die verwalteten privaten Endpunkte für Purview. Erfahren Sie mehr darüber, wie Sie Azure Synapse [Zugriff auf ein abgesichertes Purview-Konto](how-to-access-secured-purview-account.md) ermöglichen. Sie können dies entweder während der ersten Verbindung durchführen oder eine bestehende Verbindung später bearbeiten.

Die Purview-Verbindungsinformationen werden wie folgt in der Synapse-Arbeitsbereichsressource gespeichert. Wenn Sie die Verbindung programmgesteuert herstellen möchten, können Sie den Synapse-Arbeitsbereich aktualisieren und die Einstellungen vom Typ `purviewConfiguration` hinzufügen.

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
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

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Die verwaltete Identität des Synapse-Arbeitsbereichs wird verwendet, um Pushvorgänge für Herkunftsinformationen aus dem Synapse-Arbeitsbereich an Purview zu authentifizieren.

- Erteilen Sie für Purview-Konten, die **am oder nach dem 18. August 2021** erstellt wurden, der verwalteten Identität des Synapse-Arbeitsbereichs die Rolle **Datenkurator** für Ihre **Purview-Stammsammlung**. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Purview](../../purview/catalog-permissions.md) sowie unter [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

    Wenn Sie einen Synapse-Arbeitsbereich mit Purview in Synapse Studio verbinden, wird von Synapse automatisch versucht, eine solche Rollenzuweisung hinzuzufügen. Wenn Sie die Rolle **Sammlungsadministratoren** für die Purview-Stammsammlung innehaben und über Ihr Netzwerk Zugriff auf das Purview-Konto haben, wird dieser Vorgang erfolgreich ausgeführt.

- Erteilen Sie für Purview-Konten, die **vor dem 18. August 2021** erstellt wurden, der verwalteten Identität des Synapse-Arbeitsbereichs die in Azure integrierte Rolle [**Purview Data Curator (Legacy)** ](../../role-based-access-control/built-in-roles.md#purview-data-curator-legacy) (Datenkurator für Purview (Legacy)) für Ihr Purview-Konto. Weitere Informationen zu Legacyberechtigungen für die Zugriffssteuerung in Azure Purview finden Sie [hier](../../purview/catalog-permissions.md#legacy-permission-guide).

    Wenn Sie einen Synapse-Arbeitsbereich mit Purview in Synapse Studio verbinden, wird von Synapse automatisch versucht, eine solche Rollenzuweisung hinzuzufügen. Wenn Sie für das Purview-Konto über die in Azure integrierte Rolle **Besitzer** oder **Benutzerzugriffsadministrator** verfügen, ist dieser Vorgang erfolgreich.

## <a name="monitor-purview-connection"></a>Überwachen der Purview-Verbindung

Sobald der Synapse-Arbeitsbereich mit einem Purview-Konto verbunden wurde, wird die folgende Seite mit Details zu den aktivierten Integrationsfunktionen angezeigt.

:::image type="content" source="./media/monitor-purview-connection-status.png" alt-text="Screenshot: Überwachen des Integrationsstatus zwischen Azure Synapse und Purview":::

Für **Data Lineage - Synapse Pipeline** (Datenherkunft: Synapse-Pipeline) kann einer der folgenden Status angezeigt werden:

- **Verbunden**: Der Synapse-Arbeitsbereich wurde erfolgreich mit dem Purview-Konto verbunden. Das bedeutet, dass der Synapse-Arbeitsbereich einem Purview-Konto zugeordnet ist und über die Berechtigung zum Pushen der Herkunft verfügt. Wenn Ihr Purview-Konto durch eine Firewall geschützt ist, müssen Sie auch sicherstellen, dass die Integration Runtime, die zum Ausführen der Aktivitäten und Pushvorgänge für die Herkunft verwendet wird, das Purview-Konto erreichen kann. Weitere Informationen finden Sie unter [Zugreifen auf ein geschütztes Azure Purview-Konto](how-to-access-secured-purview-account.md).
- **Getrennt**: Der Synapse-Arbeitsbereich kann die Herkunft nicht an Purview pushen, da der verwalteten Identität des Synapse-Arbeitsbereichs nicht die Rolle als Purview-Datenkurator gewährt wurde. Wechseln Sie zum Beheben dieses Problems zu Ihrem Purview-Konto, um die Rollenzuweisungen zu überprüfen, und gewähren Sie die Rolle ggf. manuell. Weitere Informationen finden Sie im Abschnitt zum [Einrichten der Authentifizierung](#set-up-authentication).
- **Unbekannt**: Azure Synapse kann den Status nicht feststellen. Mögliche Gründe:

    - Das Purview-Konto ist von Ihrem aktuellen Netzwerk aus nicht erreichbar, da das Konto durch eine Firewall geschützt ist. Sie können Synapse Studio stattdessen über ein privates Netzwerk mit Konnektivität zu Ihrem Purview-Konto aufrufen.
    - Sie haben keine Berechtigung zum Überprüfen von Rollenzuweisungen für das Purview-Konto. Sie können sich an den Purview-Kontoadministrator wenden, um die Rollenzuweisungen für Sie überprüfen zu lassen. Weitere Informationen zur benötigten Purview-Rolle finden Sie im Abschnitt zum [Einrichten der Authentifizierung](#set-up-authentication).

## <a name="report-lineage-to-azure-purview"></a>Melden der Herkunft an Azure Purview

Nachdem Sie die Verbindung zwischen dem Synapse-Arbeitsbereich und einem Purview-Konto hergestellt haben, werden beim Ausführen von Pipelines Herkunftsinformationen von Synapse an das Purview-Konto gemeldet. Ausführliche Informationen zu unterstützten Funktionen sowie eine exemplarische End-to-End-Vorgehensweise finden Sie unter [Abrufen der Datenherkunft aus Azure Synapse Analytics in Azure Purview](../../purview/how-to-lineage-azure-synapse-analytics.md).

## <a name="discover-and-explore-data-using-purview"></a>Ermitteln und Erkunden von Daten mit Purview

Nachdem Sie die Verbindung zwischen dem Synapse-Arbeitsbereich und einem Purview-Konto hergestellt haben, können Sie über die Suchleiste oben in der Mitte des Synapse-Arbeitsbereichs nach Daten suchen und Aktionen ausführen. Weitere Informationen finden Sie unter [Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview](how-to-discover-connect-analyze-azure-purview.md).

## <a name="nextsteps"></a>Nächste Schritte 

[Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview](how-to-discover-connect-analyze-azure-purview.md)

[Metadaten und Datenherkunft in Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md)

[Zugreifen auf ein geschütztes Purview-Konto](how-to-access-secured-purview-account.md)

[Registrieren und Überprüfen von Azure Synapse Analytics](../../purview/register-scan-azure-synapse-analytics.md)

[Die Herkunft von Power BI in Azure Purview übernehmen](../../purview/how-to-lineage-powerbi.md)

[Verbinden von Azure Data Share und Azure Purview](../../purview/how-to-link-azure-data-share.md)
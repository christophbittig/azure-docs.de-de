---
title: Zugreifen auf ein geschütztes Azure Purview-Konto
description: Erfahren Sie, wie Sie von Azure Data Factory aus über private Endpunkte auf ein durch eine Firewall geschütztes Azure Purview-Konto zugreifen können
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 09/02/2021
ms.openlocfilehash: 51da9cba732c5654709d0c1e84398035d4675cc8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005725"
---
# <a name="access-a-secured-azure-purview-account-from-azure-data-factory"></a>Zugriff auf ein gesichertes Azure Purview-Konto von Azure Data Factory aus

Dieser Artikel beschreibt, wie man auf ein gesichertes Azure Purview-Konto von Azure Data Factory für verschiedene Integrationsszenarien zugreifen kann.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Bereitstellung von privaten Endpunkten in Azure Purview

Sie können [private Azure-Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure Purview-Konten verwenden, um einen sicheren Zugriff von einem virtuellen Netzwerk (VNet) auf den Katalog über eine private Verbindung zu ermöglichen. Purview bietet verschiedene Arten von privaten Endpunkten für unterschiedliche Zugriffsanforderungen: *Konto* privater Endpunkt, *Portal* privater Endpunkt und *Eingabe* private Endpunkte. Erfahren Sie mehr unter [Purview private Endpunkte konzeptionelle Übersicht](../purview/catalog-private-link.md#conceptual-overview). 

Wenn Ihr Purview-Konto durch eine Firewall geschützt ist und der öffentliche Zugriff verweigert wird, stellen Sie sicher, dass Sie die folgende Checkliste befolgen, um die privaten Endpunkte einzurichten, damit Data Factory erfolgreich eine Verbindung zu Purview herstellen kann. 

| Szenario                                                     | Erforderliche private Purview-Endpunkte                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Pipeline ausführen und Datenverlauf an Purview melden](tutorial-push-lineage-to-purview.md) | Damit die Data Factory-Pipeline Lineage an Purview übermitteln kann, sind die privaten Endpunkte von Purview ***Konto** _ und _*_Eingabe_*_ erforderlich. <br>- Wenn Sie _*Azure Integration Runtime** verwenden, folgen Sie den Schritten im Abschnitt [Verwaltete private Endpunkte für Purview](#managed-private-endpoints-for-purview), um verwaltete private Endpunkte im verwalteten virtuellen Netzwerk von Data Factory zu erstellen.<br>- Wenn Sie **Self-hosted Integration Runtime** verwenden, führen Sie die Schritte in [diesem Abschnitt](../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) aus, um die privaten Endpunkte *Konto* und *Befragung* im virtuellen Netzwerk Ihrer Integrationslaufzeit zu erstellen. |
| [Daten mit Purview auf der ADF UI](how-to-discover-explore-purview-data.md) aufspüren und untersuchen | Um die Suchleiste oben in der Mitte der Data Factory-dokumenterstellung für die Suche nach Purview-Daten und die Durchführung von Aktionen verwenden zu können, müssen Sie private Purview ***Konto** _ und _*_Portal_*_ in dem virtuellen Netzwerk erstellen, in dem Sie Data Factory Studio starten. Folgen Sie den Schritten in [Konto aktivieren* und *Portal* privater Endpunkt](../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts). |

## <a name="managed-private-endpoints-for-purview"></a>Verwaltete private Endpunkte für Purview

[Verwaltete private Endpunkte](managed-virtual-network-private-endpoint.md#managed-private-endpoints) sind private Endpunkte, die im Azure Data Factory Managed Virtual Network erstellt werden und eine private Verbindung zu Azure-Ressourcen herstellen. Wenn Sie die Pipeline und die Berichtslinie zu einem Firewall-geschützten Azure Purview-Konto ausführen, erstellen Sie eine Azure Integration Runtime mit aktivierter Option "Virtual network configuration" und erstellen Sie dann die Purview ***account** _ und _ *_ingestion_** verwaltete private Endpunkte wie folgt.

### <a name="create-managed-private-endpoints"></a>Verwaltete private Endpunkte erstellen

So erstellen Sie verwaltete private Endpunkte für Purview auf der Data Factory dokumenterstellung UI:

1. Gehen Sie zu **Verwalten** -> **Azure Purview**, und klicken Sie auf **Bearbeiten**, um Ihr bestehendes verbundenes Purview-Konto zu bearbeiten, oder klicken Sie auf **Verbinden mit einem Purview-Konto**, um eine Verbindung mit einem neuen Purview-Konto herzustellen.

2. Wählen Sie **Ja** für **Verwaltete private Endpunkte erstellen**. Sie müssen mindestens eine Azure Integration Runtime mit der Option "Virtuelle Netzwerkkonfiguration" in der Datenfabrik aktiviert haben, um diese Option zu sehen.

3. Klicken Sie auf die Schaltfläche **+ Alles erstellen**, um die benötigten privaten Purview-Endpunkte im Stapel zu erstellen, einschließlich des privaten Endpunkts **_Konto_ *_ und des privaten Endpunkts _* _Eingabe_ *_ für die verwalteten Purview-Ressourcen - Blob-Storage, Queue-Storage und Event-Hubs-Namespace. Sie müssen mindestens die Rolle _* Reader** in Ihrem Purview-Konto haben, damit Data Factory die Informationen der verwalteten Purview-Ressourcen abrufen kann.

   :::image type="content" source="./media/how-to-access-secured-purview-account/purview-create-all-managed-private-endpoints.png" alt-text="Erstellen Sie einen verwalteten privaten Endpunkt für Ihr verbundenes Purview-Konto.":::

4. Geben Sie auf der nächsten Seite einen Namen für den privaten Endpunkt an. Dies wird verwendet, um auch Namen für die privaten Erfassungs-Endpunkte mit Namenszusatz zu generieren.

   :::image type="content" source="./media/how-to-access-secured-purview-account/name-purview-private-endpoints.png" alt-text="Benennen Sie die verwalteten privaten Endpunkte für Ihr verbundenes Purview-Konto.":::

5. Klicken Sie auf **Erstellen**, um die privaten Endpunkte zu erstellen. Nach der Erstellung werden 4 private Endpunktanfragen generiert, die [von einem Besitzer von Purview](#approve-private-endpoint-connections) genehmigt werden müssen.

Eine solche Batch-verwaltete Erstellung privater Endpunkte ist nur auf der Purview-Benutzeroberfläche möglich. Wenn Sie die verwalteten privaten Endpunkte programmatisch erstellen möchten, müssen Sie diese PEs einzeln herstellen. Die Informationen zu den verwalteten Purview-Ressourcen finden Sie im Azure-Portal -> Ihr Purview-Konto -> Verwaltete Ressourcen.

### <a name="approve-private-endpoint-connections"></a>Genehmigen von privaten Endpunktverbindungen

Nachdem Sie die verwalteten privaten Endpunkte für Purview erstellt haben, sehen Sie zunächst den Status „Pending" (Warte ab). Der Purview-Besitzer muss die privaten Endpunktverbindungen für jede Ressource genehmigen.

Wenn Sie die Berechtigung haben, die Purview Private Endpoint-Verbindung zu genehmigen, gehen Sie von der Data Factory UI aus: 

1. Gehen Sie zu **Verwaltung** -> **Azure Purview** -> **Bearbeiten**
2. Klicken Sie in der Liste der privaten Endpunkte auf die Schaltfläche **Bearbeiten** (Bleistift) neben dem Namen jedes privaten Endpunkts
3. Klicken Sie auf **Genehmigungen im Azure-Portal verwalten**. Das bringt Sie zur Ressource.
4. Für jede Ressource gehen Sie zu **Netzwerke** -> **Private Endpunktverbindung**, um sie zu genehmigen. Der private Endpunkt wird als `data_factory_name.your_defined_private_endpoint_name` mit der Beschreibung „Angefordert von data_factory_name“ bezeichnet.
5. Wiederholen Sie diesen Vorgang für alle privaten Endpunkte.

Wenn Sie keine Berechtigung zur Genehmigung der privaten Purview-Endpunktverbindung haben, bitten Sie den Purview-Kontoinhaber, wie folgt vorzugehen.

- Gehen Sie für den privaten Endpunkt von *Konto* zum Azure-Portal -> Ihr Purview-Konto -> Vernetzung -> Private Endpunktverbindung, um sie zu genehmigen.
- Für private Endpunkte von *Erfassung* gehen Sie zum Azure-Portal -> Ihr Purview-Konto -> Verwaltete Ressourcen, klicken Sie in den Namensraum Speicherkonto bzw. Event Hubs und genehmigen Sie die private Endpunktverbindung auf der Seite Networking -> Private Endpunktverbindung.

### <a name="monitor-managed-private-endpoints"></a>Verwaltete private Endpunkte überwachen

Sie können die erstellten verwalteten privaten Endpunkte für Purview an zwei Stellen überwachen:

- Gehen Sie zu **Verwaltung** -> **Azure Purview** -> **Bearbeiten**, um Ihr bestehendes verbundenes Purview-Konto zu öffnen. Um alle relevanten privaten Endpunkte zu sehen, müssen Sie mindestens die Rolle **Leser** in Ihrem Purview-Konto haben, damit Data Factory die Informationen der verwalteten Purview-Ressourcen abrufen kann. Andernfalls sehen Sie nur den privaten Endpunkt *Konto* mit einer Warnung.
- Gehen Sie zu **Verwalten** -> **Verwaltete private Endpunkte**, wo Sie alle verwalteten privaten Endpunkte sehen, die unter der Datenfabrik erstellt wurden. Wenn Sie mindestens die Rolle **Leser** in Ihrem Purview-Konto haben, sehen Sie, dass Purview-relevante private Endpunkte zusammen gruppiert sind. Andernfalls werden sie separat in der Liste angezeigt.

## <a name="nextsteps"></a>Nächste Schritte 

- [Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview](connect-data-factory-to-azure-purview.md)
- [Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](tutorial-push-lineage-to-purview.md)
- [Ermitteln und Erkunden von Daten in ADF mithilfe von Purview](how-to-discover-explore-purview-data.md)

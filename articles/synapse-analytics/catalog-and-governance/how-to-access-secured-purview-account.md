---
title: Zugreifen auf ein geschütztes Azure Purview-Konto
description: Erfahren Sie, wie man zu einem von einem Firewall geschützten Azure Purview-Konto durch private Endpunkte von Synapse Zugriff erhält
author: linda33wj
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: how-to
ms.date: 09/02/2021
ms.author: jingwang
ms.reviewer: jrasnick
ms.openlocfilehash: e147ee8cd6483ab32fee0fe393b104669370ab5c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440742"
---
# <a name="access-a-secured-azure-purview-account-from-azure-synapse-analytics"></a>Zugriff auf ein gesichertes Azure Purview-Konto von Azure Synapse Analytics aus

Dieser Artikel beschreibt, wie man auf ein gesichertes Azure Purview-Konto von Azure Synapse Analytics für verschiedene Integrationsszenarien zugreifen kann.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Szenarien für Bereitstellung von privaten Azure Purview-Endpunkten

Sie können [private Azure-Endpunkte](../../private-link/private-endpoint-overview.md) für Ihre Azure Purview-Konten verwenden, um einen sicheren Zugriff von einem virtuellen Netzwerk (VNet) auf den Katalog über eine private Verbindung zu ermöglichen. Purview bietet verschiedene Arten von privaten Endpunkten für unterschiedliche Zugriffsanforderungen: *Konto* privater Endpunkt, *Portal* privater Endpunkt und *Eingabe* private Endpunkte. Erfahren Sie mehr unter [Konzeptionelle Übersicht für Purview Private Endpunkte](../../purview/catalog-private-link.md#conceptual-overview). 

Wenn Ihr Purview-Konto durch einen Firewall geschützt ist und der öffentliche Zugriff verweigert wird, befolgen Sie die folgende Checkliste, um die privaten Endpunkte einzurichten, damit Synapse erfolgreich eine Verbindung zu Purview herstellen kann. 

| Szenario                                                     | Erforderliche private Purview-Endpunkte                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Pipeline ausführen und Herkunft der Daten an Purview melden](../../purview/how-to-lineage-azure-synapse-analytics.md) | Damit die Synapse-Pipeline die Datenherkunft an Purview melden kann, sind ein Purview ***Konto** _ und _*_Erfassung über_*_ private Endpunkte erforderlich. <br>- Wenn Sie _*Azure Integration Runtime** verwenden, folgen Sie den Schritten in Abschnitt [Verwaltete private Endpunkte für Purview](#managed-private-endpoints-for-purview), um verwaltete private Endpunkte im verwalteten virtuellen Netzwerk von Synapse herzustellen.<br>- Wenn Sie **Self-hosted Integration Runtime** verwenden, führen Sie die Schritte in [diesem Abschnitt](../../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) aus, um die privaten Endpunkte *Konto* und *Erfassung* im virtuellen Netzwerk Ihrer Integration Runtime herzustellen. |
| [Entdecken und Erforschen von Daten mithilfe von Purview auf Synapse Studio](how-to-discover-connect-analyze-azure-purview.md) | Wenn Sie die Suchleiste oben in der Mitte Synapse Studio verwenden wollen, um nach Purview-Daten zu suchen und Aktionen auszuführen, müssen Sie ein privates Purview ***Konto** _ und _*_Portal_*_ private Endpunkte in dem virtuellen Netzwerk erstellen, in dem Sie Synapse Studio starten. Folgen Sie den Schritten in [Konto_aktivieren* und *Portal* privater Endpunkt](../../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts). |

## <a name="managed-private-endpoints-for-purview"></a>Verwaltete private Endpunkte für Purview

[Verwaltete private Endpunkte](../security/synapse-workspace-managed-private-endpoints.md) sind private Endpunkte, die in einem verwalteten virtuellen Netzwerk erstellt werden, das Ihrem Azure Synapse-Arbeitsbereich zugeordnet ist. Wenn Sie die Pipeline und die Datenherkunft zu einem Firewall-geschützten Azure Purview-Konto ausführen, sorgen Sie dafür, dass an Ihrem Synapse-Arbeitsbereich die Option „Verwaltetes virtuelles Netzwerk“ aktiviert ist, und erstellen Sie dann das Purview ***Konto** _ und _ *_Datenerfassung_** verwaltete private Endpunkte wie folgt.

### <a name="create-managed-private-endpoints"></a>Verwaltete private Endpunkte erstellen

So erstellen Sie verwaltete private Endpunkte für Purview auf Synapse Studio:

1. Gehen Sie zu **Verwalten** -> **Azure Purview**, und klicken Sie auf **Bearbeiten**, um Ihr bestehendes verbundenes Purview-Konto zu bearbeiten, oder klicken Sie auf **Verbinden mit einem Purview-Konto**, um eine Verbindung mit einem neuen Purview-Konto herzustellen.

2. Wählen Sie **Ja** unter **Verwaltete private Endpunkte erstellen**. Sie benötigen die Berechtigung **workspaces/managedPrivateEndpoint/write**, z. B. als Synapse Administrator oder als Synapse Manager für vernetzte Daten.

3. Klicken Sie auf die Schaltfläche **+ Alle erstellen**, um die benötigten privaten Purview-Endpunkte im Stapel zu erstellen, einschließlich des privaten Endpunkts **_Konto_ *_ und des privaten Endpunkts _* _Erfassung_ *_ für die verwalteten Purview-Ressourcen: Blob-Speicher, Queue-Speicher und Event-Hub Namespace. Sie müssen mindestens die Rolle _* Leser** in Ihrem Purview-Konto haben, damit Synapse die Informationen der verwalteten Purview-Ressourcen abrufen kann.

   :::image type="content" source="./media/purview-create-all-managed-private-endpoints.png" alt-text="Erstellen Sie einen verwalteten privaten Endpunkt für Ihr verbundenes Purview-Konto.":::

4. Geben Sie auf der nächsten Seite einen Namen für den privaten Endpunkt an. Dies wird verwendet, um auch Namen für die privaten Erfassungs-Endpunkte mit Namenszusatz zu generieren.

   :::image type="content" source="./media/name-purview-private-endpoints.png" alt-text="Benennen Sie die verwalteten privaten Endpunkte für Ihr verbundenes Purview-Konto.":::

5. Klicken Sie auf **Erstellen**, um die privaten Endpunkte zu erstellen. Nach der Erstellung werden 4 private Endpunktanfragen generiert, die [von einem Besitzer von Purview](#approve-private-endpoint-connections) genehmigt werden müssen.

Eine solche Batch-verwaltete Erstellung privater Endpunkte ist nur auf Synapse Studio möglich. Wenn Sie die verwalteten privaten Endpunkte programmatisch erstellen möchten, müssen Sie diese PEs einzeln herstellen. Die Informationen zu den verwalteten Purview-Ressourcen finden Sie im Azure-Portal -> Ihr Purview-Konto -> Verwaltete Ressourcen.

### <a name="approve-private-endpoint-connections"></a>Genehmigen von privaten Endpunktverbindungen

Nachdem Sie die verwalteten privaten Endpunkte für Purview erstellt haben, sehen Sie zunächst den Status „Pending" (Warte ab). Der Purview-Besitzer muss die privaten Endpunktverbindungen für jede Ressource genehmigen.

Wenn Sie die Berechtigung haben, die Purview Private Endpoint-Verbindung zu genehmigen, gehen Sie wie folgt aus Synapse Studio vor: 

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

- Gehen Sie zu **Verwaltung** -> **Azure Purview** -> **Bearbeiten**, um Ihr bestehendes verbundenes Purview-Konto zu öffnen. Um alle relevanten privaten Endpunkte zu sehen, müssen Sie mindestens die Rolle **Leser** in Ihrem Purview-Konto haben, damit Synapse die Informationen der verwalteten Purview-Ressourcen abrufen kann. Andernfalls sehen Sie nur den privaten Endpunkt *Konto* mit einer Warnung.
- Gehen Sie zu **Verwalten** -> **Verwaltete private Endpunkte**, wo Sie alle verwalteten privaten Endpunkte sehen, die unter dem Synapse-Arbeitsbereich erstellt wurden. Wenn Sie mindestens die Rolle **Leser** in Ihrem Purview-Konto haben, sehen Sie, dass Purview-relevante private Endpunkte zusammen gruppiert sind. Andernfalls werden sie separat in der Liste angezeigt.

## <a name="nextsteps"></a>Nächste Schritte 

- [Verbinden eines Synapse-Arbeitsbereichs mit Azure Purview](quickstart-connect-azure-purview.md)
- [Metadaten und Datenherkunft in Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md)
- [Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview](how-to-discover-connect-analyze-azure-purview.md)
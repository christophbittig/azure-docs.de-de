---
title: Aktivieren von Notebooks im Azure Cosmos DB-Konto (Vorschau)
description: Die integrierten Notebooks von Azure Cosmos DB ermöglichen es Ihnen, Ihre Daten innerhalb des Portals zu analysieren und zu visualisieren. In diesem Artikel wird beschrieben, wie Sie dieses Feature für Cosmos-Konten aktivieren.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/24/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 19bb4a91a8612feab4218a16eedb2f961b1d0adb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434007"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Aktivieren von Notebooks für Azure Cosmos DB-Konten (Vorschau)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Derzeit ist das Notebookfeature vorübergehend deaktiviert. Das Azure Cosmos DB-Team arbeitet an einer Lösung.  

Integrierte Jupyter Notebooks in Azure Cosmos DB ermöglichen es Ihnen, Ihre Daten über das Azure-Portal zu analysieren und zu visualisieren. In diesem Artikel wird beschrieben, wie Sie dieses Feature für Ihr Azure Cosmos DB-Konto aktivieren. Notebooks für Azure Cosmos DB sind zurzeit in [29 Regionen](#supported-regions) verfügbar. Um Notebooks zu verwenden, [erstellen Sie ein neues Cosmos-Konto](#create-a-new-cosmos-account), oder [aktivieren Sie Notebooks für ein vorhandenes Konto](#enable-notebooks-in-an-existing-cosmos-account) in einer dieser Regionen.

## <a name="create-a-new-cosmos-account"></a>Erstellen eines neuen Cosmos-Kontos
Ab dem 10. Februar 2021 sind für neue Azure Cosmos-Konten, die in einer der [unterstützten Regionen](#supported-regions) erstellt werden, Notebooks automatisch aktiviert. Es ist keine zusätzliche Konfiguration erforderlich, um Notebooks zu aktivieren. Gehen Sie folgendermaßen vor, um ein neues Konto zu erstellen:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Ressourcen erstellen** > **Datenbanken** > **Azure Cosmos DB** aus.
1. Geben Sie die grundlegenden Einstellungen für das Konto ein.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Die Seite „Neues Konto“ für Azure Cosmos DB":::

1. Klicken Sie auf **Überprüfen + erstellen**. Sie können die Optionen **Netzwerk** und **Tags** überspringen. 
1. Überprüfen Sie die Kontoeinstellungen, und wählen Sie anschließend **Erstellen** aus. Die Erstellung des Kontos dauert einige Minuten. Warten Sie, bis auf der Portalseite **Ihre Bereitstellung wurde abgeschlossen.** angezeigt wird.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Der Bereich „Benachrichtigungen“ im Azure-Portal":::

1. Wählen Sie **Zu Ressource wechseln** aus, um zur Seite des Azure Cosmos DB-Kontos zu wechseln.

   :::image type="content" source="../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Seite des Azure Cosmos DB-Kontos":::

1. Navigieren Sie zum Bereich **Daten-Explorer**. Der Notebooks-Arbeitsbereich sollte jetzt angezeigt werden.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Neuer Notebooks-Arbeitsbereich von Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Aktivieren von Notebooks in einem vorhandenen Cosmos-Konto

Sie können auch Notebooks für vorhandene Konten aktivieren. Dieser Schritt muss nur einmal pro Konto durchgeführt werden.

1. Navigieren Sie in Ihrem Cosmos-Konto zum Bereich **Daten-Explorer**.
1. Wählen Sie **Notebooks aktivieren** aus.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Erstellen eines neuen Notebooks-Arbeitsbereichs im Daten-Explorer":::

1. Daraufhin werden Sie aufgefordert, einen neuen Notebooks-Arbeitsbereich zu erstellen. Wählen Sie **Setup abschließen** aus.
1. Ihr Konto ist jetzt für die Verwendung von Notebooks aktiviert!

## <a name="create-and-run-your-first-notebook"></a>Erstellen und Ausführen des ersten Notebooks

Um zu überprüfen, ob Sie Notebooks verwenden können, wählen Sie eines der Notebooks unter den Beispielnotebooks aus. Dadurch wird eine Kopie des Notebooks in Ihrem Arbeitsbereich gespeichert und geöffnet.

In diesem Beispiel wird **GettingStarted.ipynb**. verwendet.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Anzeigen des Notebooks „GettingStarted.ipynb“":::

So führen Sie das Notebook aus:
1. Wählen Sie die erste Codezelle mit Python-Code aus.
1. Klicken Sie auf **Ausführen**, um die Zelle auszuführen. Sie können zum Ausführen der Zelle auch **UMSCHALT+EINGABE** verwenden.
1. Aktualisieren Sie den Ressourcenbereich, um die Datenbank und den Container anzuzeigen, die erstellt wurden.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Ausführen des Notebooks mit ersten Schritten":::

Sie können auch **Neues Notebook** auswählen, um ein neues Notebook zu erstellen, oder eine vorhandene Notebook-Datei (IPYNB) hochladen, indem Sie im Menü **Meine Notebooks** die Option **Datei hochladen** auswählen. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Erstellen oder Hochladen eines neuen Notebooks":::

## <a name="supported-regions"></a>Unterstützte Regionen
Integrierte Notebooks für Azure Cosmos DB sind zurzeit in 29 Azure-Regionen verfügbar. Für neue Azure Cosmos-Konten, die in diesen Regionen erstellt werden, sind Notebooks automatisch aktiviert. Notebooks sind bei Ihrem Konto kostenlos. 

- Australien, Mitte
- Australien, Mitte 2
- Australien (Osten)
- Australien, Südosten
- Brasilien Süd
- Kanada, Mitte
- Kanada, Osten
- Indien, Mitte
- USA (Mitte)
- East US
- USA (Ost) 2
- Frankreich, Mitte
- Frankreich, Süden
- Deutschland, Norden
- Deutschland, Westen-Mitte
- Japan, Westen
- Korea, Süden
- USA Nord Mitte
- Nordeuropa
- USA Süd Mitte
- Asien, Südosten
- Schweiz, Norden
- VAE, Mitte
- UK, Süden
- UK, Westen
- USA, Westen-Mitte
- Europa, Westen
- Indien, Westen
- USA, Westen 2

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die Vorteile von [Jupyter Notebooks in Azure Cosmos DB](../cosmosdb-jupyter-notebooks.md).
* [Erkunden des Katalogs mit Notebookbeispielen](https://cosmos.azure.com/gallery.html)
* [Veröffentlichen von Notebooks im Katalog für Azure Cosmos DB-Notebooks](publish-notebook-gallery.md)
* [Verwenden von Python-Notebookfunktionen und -befehlen](use-python-notebook-features-and-commands.md)
* [Verwenden von C#-Notebookfunktionen und -befehlen](use-csharp-notebook-features-and-commands.md)
* [Importieren von Notebooks aus einem GitHub-Repository](import-github-notebooks.md)

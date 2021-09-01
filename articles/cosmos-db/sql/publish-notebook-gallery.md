---
title: Veröffentlichen von Notebooks im Katalog für Azure Cosmos DB-Notebooks
description: Es wird beschrieben, wie Sie Notebooks aus dem öffentlichen Katalog herunterladen und bearbeiten und Ihre eigenen Notebooks im Katalog veröffentlichen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 016bd8c73e12297988f3a2d47cde0163821d2dcd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115194"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Veröffentlichen von Notebooks im Katalog für Azure Cosmos DB-Notebooks
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Integrierte Jupyter Notebooks für Azure Cosmos DB sind direkt in Ihre Azure Cosmos DB-Konten im Azure-Portal integriert. Mit diesen Notebooks können Sie Ihre Daten über das Azure-Portal analysieren und visualisieren. Integrierte Notebooks für Azure Cosmos DB sind zurzeit in [vielen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) verfügbar. Um Notebooks zu verwenden, [erstellen Sie ein neues Cosmos-Konto](create-cosmosdb-resources-portal.md), oder [aktivieren Sie Notebooks für ein vorhandenes Konto](enable-notebooks.md) in einer dieser Regionen.

Die Notebookumgebung im Azure-Portal enthält einige Beispiele, die vom Azure Cosmos DB-Team veröffentlicht wurden. Darüber hinaus enthält sie einen öffentlichen Katalog, in dem Sie Ihre eigenen Notebooks veröffentlichen und für die gemeinsame Verwendung freigeben können. Nachdem ein Notebook im Katalog veröffentlicht wurde, kann es von allen Azure Cosmos DB-Benutzern angezeigt und verwendet werden. In diesem Artikel wird beschrieben, wie Sie die im öffentlichen Katalog enthaltenen Notebooks verwenden und Ihr eigenes Notebook im Katalog veröffentlichen.

## <a name="download-a-notebook-from-the-gallery"></a>Herunterladen eines Notebooks aus dem Katalog

Führen Sie die folgenden Schritte aus, um Notebooks im Katalog anzuzeigen und in Ihren eigenen Arbeitsbereich für Notebooks herunterzuladen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Azure Cosmos DB-Konto, das für die Notebookumgebung aktiviert ist.

1. Navigieren Sie zur Registerkarte unter **Daten-Explorer** > **Notebooks** > **Katalog** > **Öffentlicher Katalog**.

1. Akzeptieren Sie die [Verhaltensregeln](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/), bevor Sie Notebooks im Katalog anzeigen oder veröffentlichen. Die Bestätigung der Verhaltensregeln wird pro Benutzer und pro Abonnementebene protokolliert. Wenn Sie zu einem anderen Abonnement wechseln, müssen Sie sie vor dem Zugreifen auf den Katalog erneut akzeptieren. Aktivieren Sie das Kontrollkästchen, und wählen Sie **Weiter** aus, um die Verhaltensregeln zu akzeptieren:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Navigieren zum öffentlichen Katalog mit Notebooks und Akzeptieren der Verhaltensregeln":::

1. Als Nächstes können Sie ein bestimmtes Notebook der Registerkarte „Favoriten“ hinzufügen oder es herunterladen. Wenn Sie ein Notebook herunterladen, wird es in Ihren Arbeitsbereich für Notebooks kopiert, wo Sie es ausführen und bearbeiten können.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Herunterladen eines Notebooks aus dem Katalog in Ihren Arbeitsbereich":::

## <a name="publish-a-notebook-to-the-gallery"></a>Veröffentlichen eines Notebooks im Katalog

Wenn Sie eigene Notebooks erstellen oder die vorhandenen Notebooks für ein bestimmtes Szenario anpassen, ist es häufig der Fall, dass diese anschließend im Katalog veröffentlicht werden sollen. Nachdem ein Notebook im Katalog veröffentlicht wurde, können andere Benutzer darauf zugreifen. Sie können den [Katalog mit den Notebookbeispielen erkunden](https://cosmos.azure.com/gallery.html), um die derzeit verfügbaren Notebooks anzuzeigen.

Führen Sie die folgenden Schritte aus, um ein Notebook zu veröffentlichen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Azure Cosmos DB-Konto, das für die Notebookumgebung aktiviert ist.

1. Navigieren Sie zur Registerkarte unter **Daten-Explorer** > **Notebooks** > **Meine Notebooks**.

1. Navigieren Sie zu dem Notebook, das Sie veröffentlichen möchten. Wählen Sie in der Befehlsleiste die Schaltfläche **Speichern** und dann die Option **Im Katalog veröffentlichen** aus:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Auswählen eines Notebooks zum Veröffentlichen im Katalog":::

   Sie können auf die Option **Im Katalog veröffentlichen** auch zugreifen, indem Sie die Schaltfläche **…** neben dem Namen des Notebooks auswählen:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Ein weiterer Ansatz zum Auswählen eines Notebooks, das im Katalog veröffentlicht werden soll.":::

1. Füllen Sie das Formular **Im Katalog veröffentlichen** mit den folgenden Details aus:

   * **Name:** Ein Anzeigename zur Identifizierung Ihres Notebooks.
   * **Beschreibung:** Eine kurze Beschreibung des Zwecks Ihres Notebooks.
   * **Tags:** Tags sind optional und werden zum Filtern von Ergebnissen bei einer Schlüsselwortsuche verwendet.
   * **Titelbild:** Ein Bild, das auf der Titelseite verwendet wird, wenn das Notebook veröffentlicht wird. Sie können eine der folgenden Optionen auswählen:
   * **Benutzerdefiniertes Bild:** Sie können ein Bild von Ihrem Computer hochladen. Wählen Sie eine Bilddatei mit einem Seitenverhältnis von 256 x 144 aus.
   * **URL:** Geben Sie eine öffentlich zugängliche URL an, unter der sich das Bild befindet.
   * **Screenshot erstellen:** Ein Screenshot Ihres geöffneten Notebooks wird automatisch erstellt und in die Vorschau hochgeladen.
   * **Use first display output** (Erste Anzeigeausgabe verwenden): Die Ausgabe der ersten Zelle, in der eine Anzeigeausgabe enthalten ist. Zellen, in denen nur Markdowncode oder Text angezeigt wird, zählen nicht als Anzeigeausgabe.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Ausfüllen des Formulars „Im Katalog veröffentlichen“":::

   > [!NOTE]
   > Wenn Sie die Option **Im Katalog veröffentlichen** über die Schaltfläche **…** neben dem Namen des Notebooks verwenden, werden nicht alle Optionen für das **Titelbild** angezeigt. Der Grund ist, dass das Notebook ggf. nicht geöffnet ist und Azure Cosmos DB nicht über den erforderlichen Zugriff zum Erstellen eines Screenshots oder zum Zugreifen auf die erste Anzeigeausgabe hat.

1. Vergewissern Sie sich, dass die Vorschau wie gewünscht aussieht, und wählen Sie anschließend die Option **Veröffentlichen** aus. Nach der Veröffentlichung wird dieses Notebook im öffentlichen Katalog mit den Azure Cosmos DB-Notebooks angezeigt. Stellen Sie sicher, dass Sie vor dem Veröffentlichen alle vertraulichen Daten bzw. Ausgaben entfernen. Der Notebookinhalt wird vor dem Veröffentlichen auf Verstöße gegen Microsoft-Richtlinien überprüft. Dieser Vorgang dauert normalerweise einige Sekunden. Auf der Registerkarte „Benachrichtigung“ wird eine Nachricht angezeigt, falls Verstöße gefunden werden. Ihr Notebook wird nicht veröffentlicht, wenn Verstöße gefunden werden. Sie müssen den entsprechenden Text dann entfernen und das Veröffentlichen erneut durchführen.

   > [!NOTE]
   > Nachdem die Notebooks im öffentlichen Katalog veröffentlicht wurden, können sie von allen Azure Cosmos DB-Benutzern angezeigt werden. Der Zugriff ist nicht auf die Abonnement- oder Organisationsebene beschränkt.

1. Nach dem Veröffentlichen des Notebooks im Katalog wird es auf der Registerkarte **My published work** (Meine Veröffentlichungen) angezeigt. Sie haben auch die Möglichkeit, veröffentlichte Notebooks aus dem öffentlichen Katalog zu entfernen oder zu löschen.

1. Darüber hinaus können Sie ein Notebook melden, mit dem gegen die Verhaltensregeln verstoßen wird. Falls ein Verstoß ermittelt wird, wird das Notebook von Cosmos DB automatisch aus dem Katalog entfernt. Nach dem Entfernen eines Notebooks wird dies für Benutzer auf der Registerkarte **My published work** (Meine Veröffentlichungen) mit einem entsprechenden Hinweis angezeigt. Navigieren Sie zur Registerkarte unter **Daten-Explorer** > **Notebooks** > **Katalog** > **Öffentlicher Katalog**, um ein Notebook zu melden. Öffnen Sie das zu meldende Notebook, zeigen Sie rechts in der Ecke auf die Schaltfläche **Hilfe**, und wählen Sie die Option **Missbrauch melden** aus.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Melden eines Notebooks, das gegen die Verhaltensregeln verstößt":::

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die Vorteile von [Jupyter Notebooks in Azure Cosmos DB](../cosmosdb-jupyter-notebooks.md).
* [Verwenden von Python-Notebookfunktionen und -befehlen](use-python-notebook-features-and-commands.md)
* [Verwenden von C#-Notebookfunktionen und -befehlen](use-csharp-notebook-features-and-commands.md)
* [Importieren von Notebooks aus einem GitHub-Repository](import-github-notebooks.md)

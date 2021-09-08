---
title: Integration von Arbeitselementen (Vorschau) – Application Insights
description: Erfahren Sie, wie Sie Arbeitselemente in GitHub oder Azure DevOps mit darin eingebetteten Application Insights-Daten erstellen.
ms.topic: conceptual
ms.date: 06/27/2021
ms.openlocfilehash: 02fbb15f417d01d1f9c5b572fdb5553ad1127037
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989133"
---
# <a name="work-item-integration"></a>Integration von Arbeitselementen 

Die Funktion zur Integration von Arbeitselementen ermöglicht Ihnen das einfache Erstellen von Arbeitselementen in GitHub oder Azure DevOps, in die relevante Application Insights-Daten eingebettet sind.


Die neue Arbeitselementintegration bietet gegenüber der [klassischen](#classic-work-item-integration) die folgenden Funktionen:
- Erweiterte Felder wie „Zugewiesene Person“, „Projekte“ oder „Meilensteine“.
- Repositorysymbole, damit Sie zwischen GitHub- und Azure DevOps-Arbeitsmappen unterscheiden können.
- Mehrere Konfigurationen für eine beliebige Anzahl von Repositorys oder Arbeitselementen.
- Bereitstellung über Azure Resource Manager-Vorlagen.
- Vorgefertigte und anpassbare KQL-Abfragen (Keyword Query Language) zum Hinzufügen von Application Insights-Daten zu Ihren Arbeitselementen.
- Anpassbare Arbeitsmappenvorlagen.


## <a name="create-and-configure-a-work-item-template"></a>Erstellen und Konfigurieren einer Arbeitselementvorlage

1. Zum Erstellen einer Arbeitselementvorlage navigieren Sie zu Ihrer Application Insights-Ressource, und wählen Sie auf der linken Seite unter *Konfigurieren* die Option **Arbeitselemente** und dann oben die Option **Neue Vorlage erstellen** aus.

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Screenshot der Registerkarte für Arbeitselemente mit ausgewählter Option zum Erstellen einer neuen Vorlage" lightbox="./media/work-item-integration/create-work-item-template.png":::

    Sie können eine Arbeitselementvorlage auch über die Registerkarte „End-to-End-Transaktionsdetails “ erstellen, falls derzeit keine Vorlage vorhanden ist. Wählen Sie ein Ereignis und dann auf der rechten Seite **Arbeitselement erstellen** aus. Anschließend wählen Sie **Mit Arbeitsmappenvorlage beginnen** aus.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Screenshot der Registerkarte mit End-to-End-Transaktionsdetails mit ausgewählten Optionen zum Erstellen eines Arbeitselements und zum Beginnen mit einer Arbeitsmappenvorlage" lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Nachdem Sie **Neue Vorlage erstellen** ausgewählt haben, können Sie die Nachverfolgungssysteme auswählen, der Arbeitsmappe einen Namen geben, eine Verknüpfung mit dem ausgewählten Nachverfolgungssystem erstellen und eine Region zum Speichern der Verlage auswählen (dies ist standardmäßig die Region, in der sich Ihre Application Insights Ressource befindet). Die URL-Parameter sind die Standard-URL für Ihr Repository, z. B. `https://github.com/myusername/reponame` oder `https://mydevops.visualstudio.com/myproject`.

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Screenshot zum Erstellen einer neuen Arbeitsmappenvorlage für Arbeitselemente":::

    Sie können spezifische Workitem-Eigenschaften direkt in der Vorlage selbst einstellen. Dies umfasst den Zugewiesenen, den Iterationspfad, Projekte und mehr, abhängig von Ihrem Versionskontrollanbieter.

## <a name="create-a-work-item"></a>Erstellen eines Arbeitselements

 Sie können auf die neue Vorlage über alle End-to-End-Transaktionsdetails zugreifen, auf die Sie über „Leistung“, „Fehler“, „Verfügbarkeit“ oder andere Registerkarten zugreifen können.

1. Zum Erstellen eines Arbeitselements navigieren Sie zu den End-to-End-Transaktionsdetails, und wählen Sie ein Ereignis aus. Wählen Sie dann **Arbeitselement erstellen** und anschließend Ihre Arbeitselementvorlage aus.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Screenshot der Registerkarte mit End-to-End-Transaktionsdetails mit ausgewählter Option zum Erstellen eines Arbeitselements" lightbox="./media/work-item-integration/create-work-item.png":::

1. Im Browser wird eine neue Registerkarte für das von Ihnen ausgewählte Nachverfolgungssystem geöffnet. In Azure DevOps können Sie einen Fehler oder eine Aufgabe erstellen, und in GitHub können Sie ein neues Problem in Ihrem Repository erstellen. Es wird automatisch ein neues Arbeitselement mit Kontextinformationen erstellt, die von Application Insights bereitgestellt werden.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Screenshot eines automatisch erstellten GitHub-Problems" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Screenshot eines automatisch erstellten Fehlers in Azure DevOps" lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Bearbeiten einer Vorlage

Zum Bearbeiten der Vorlage navigieren Sie unter *Konfigurieren* zur Registerkarte **Arbeitselemente**, und wählen Sie das Stiftsymbol neben der Arbeitsmappe aus, die Sie aktualisieren möchten.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Screenshot der Registerkarte für Arbeitselemente mit ausgewähltem Stiftsymbol für die Bearbeitung":::

Wählen Sie oben das Bearbeitungssymbol ![Bearbeitungssymbol](./media/work-item-integration/edit-icon.png) aus, um mit dem Bearbeiten der Vorlage zu beginnen. Arbeitselementvorlagen basieren auf [Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md). Die Arbeitselementinformationen werden mithilfe der Sprache KQL (Keyword Query Language) generiert. Sie können die Abfragen ändern, um weiteren wichtigen Kontext für Ihr Team hinzuzufügen. Wenn Sie die Bearbeitung abgeschlossen haben, speichern Sie die Arbeitsmappe, indem Sie auf der oberen Symbolleiste das Symbol zum Speichern ![Symbol zum Speichern](./media/work-item-integration/save-icon.png) auswählen.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Screenshot der Arbeitsmappe mit der Arbeitselementvorlage im Bearbeitungsmodus" lightbox="./media/work-item-integration/edit-workbook.png":::

Sie können mehrere Arbeitselementkonfigurationen erstellen und für jedes Szenario über eine benutzerdefinierte Arbeitsmappe verfügen. Die Arbeitsmappen können auch von Azure Resource Manager bereitgestellt werden, um Standardimplementierungen in allen Umgebungen zu gewährleisten.

## <a name="classic-work-item-integration"></a>Integration von klassischen Arbeitselementen 

1. Wählen Sie in Ihrer Application Insights-Ressource unter *Konfigurieren* die Option **Arbeitselemente** aus.
1. Wählen Sie **Zur klassischen Ansicht wechseln** aus, füllen Sie die Felder mit Ihren Informationen aus, und autorisieren Sie. 

    :::image type="content" source="./media/work-item-integration/classic.png" alt-text="Screenshot: Konfigurieren klassischer Arbeitselemente" lightbox="./media/work-item-integration/classic.png":::

1. Erstellen Sie ein Arbeitselement, indem Sie zu den Details der End-to-End-Transaktion navigieren, ein Ereignis auswählen und dann **Arbeitselement erstellen (klassisch)** wählen. 


### <a name="migrate-to-new-work-item-integration"></a>Migrieren zu einer neuen Arbeitselementintegration

Löschen Sie zum Migrieren Ihre klassische Arbeitselementkonfiguration, und [erstellen und konfigurieren Sie dann eine Arbeitselementvorlage](#create-and-configure-a-work-item-template), um die Integration neu zu erstellen.

Zum Löschen navigieren Sie unter *Konfigurieren* zur Ihrer Application Insights-Ressource, wählen Sie **Arbeitselemente**, dann **Zur klassischen Ansicht wechseln** und dann oben die Option **Löschen* aus.


## <a name="next-steps"></a>Nächste Schritte
[Verfügbarkeitstest](availability-overview.md)


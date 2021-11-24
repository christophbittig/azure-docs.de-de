---
title: Automatische Anwendung von Sensitivitätskennzeichnungen auf Ihre Daten in Azure Purview
description: Erfahren Sie, wie Sie Vertraulichkeitsbezeichnungen erstellen und sie während eines Scans automatisch auf Ihre Daten anwenden.
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6bcb751c58b4ba14e84f529ec268beacc93fdcf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339559"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>Automatische Anwendung von Sensitivitätskennzeichnungen auf Ihre Daten in Azure Purview

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>Erstellen oder erweitern Sie bestehende Sensitivitätskennzeichnungen auf Azure Purview

> [!IMPORTANT]
> Azure Purview-Vertraulichkeitsbezeichnungen befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.
>

Wenn Sie nicht bereits über Vertraulichkeitsbezeichnungen verfügen, müssen Sie sie erstellen und für Azure Purview verfügbar machen. Vorhandene Vertraulichkeitsbezeichnungen können auch geändert werden, um sie für Azure Purview verfügbar zu machen.

### <a name="step-1-licensing-requirements"></a>Schritt 1: Lizenzanforderungen:

Vertraulichkeitsbezeichnungen werden im Microsoft 365 Compliance Center erstellt und verwaltet. Um Sensibilitätskennzeichnungen für die Verwendung in Azure Purview zu erstellen, müssen Sie über eine aktive Microsoft 365-Lizenz verfügen, die den Vorteil bietet, dass Sensibilitätskennzeichnungen automatisch angewendet werden.

Eine vollständige Liste der Lizenzen finden Sie unter den [Sensitivitätskennzeichnungen in den Azure Purview FAQ](sensitivity-labels-frequently-asked-questions.yml). Wenn Sie nicht bereits über die erforderliche Lizenz verfügen, können Sie sich für eine Testversion von [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) registrieren.

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>Schritt 2: Zustimmung zur Verwendung von Sensitivitätskennzeichnungen in Azure Purview

Mit den folgenden Schritten erweitern Sie Ihre Sensitivitätskennzeichnungen und machen sie für die Verwendung in Azure Purview verfügbar, wo Sie Sensitivitätskennzeichnungen auf Dateien und Datenbankspalten anwenden können.

1. Navigieren Sie in Microsoft 365 zur Seite **Information Protection**.</br>
   Wenn Sie Ihr Abonnement für den Informationsschutz erst kürzlich eingerichtet haben, kann es ein paar Stunden dauern, bis die Seite für den **Informationsschutz** angezeigt wird.
1. Wählen Sie im Bereich **Beschriftung auf Assets in Azure Purview erweitern** die Schaltfläche **Einschalten** und wählen Sie dann im angezeigten Bestätigungsdialog **Ja**.

Beispiel:

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="Wählen Sie die Schaltfläche „Einschalten“, um die Sensibilitätskennzeichnungen auf Purview zu erweitern." lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation-small.png" alt-text="Bestätigen der Auswahl zum Erweitern von Vertraulichkeitsbezeichnungen auf Purview" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation.png":::

> [!TIP]
>Wenn die Schaltfläche nicht angezeigt wird und Sie nicht sicher sind, ob die Zustimmung zum Erweitern der Bezeichnung auf Ressourcen in Purview erteilt wurde, finden Sie in [diesem FAQ-Artikel](sensitivity-labels-frequently-asked-questions.yml#how-can-i-determine-if-consent-has-been-granted-to-extend-labeling-to-purview) Informationen zum Ermitteln des Status.
>

Nachdem Sie die Beschriftung auf Assets in Azure Purview ausgeweitet haben, stehen alle veröffentlichten Empfindlichkeitsbezeichnungen zur Verwendung in Purview zur Verfügung.

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>Schritt 3: Erstellen oder Ändern einer vorhandenen Beschriftung zur automatischen Kennzeichnung von Inhalten

**Zum Erstellen neuer Vertraulichkeitsbezeichnungen oder Ändern vorhandener Bezeichnungen gehen Sie folgendermaßen vor**:

1. Öffnen Sie das [Microsoft 365 Compliance Center](https://compliance.microsoft.com/).

1. Wählen Sie unter **Lösungen** die Option **Information Protection** und dann **Bezeichnung erstellen** aus.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="Erstellen von Sensibilitätskennzeichnungen im Microsoft 365 Compliance Center" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. Benennen Sie die Bezeichnung. Unter **Definieren des Bereichs für diese Bezeichnung** gehen Sie folgendermaßen vor:

    - Wählen Sie in allen Fällen **Azure Purview-Ressourcen** aus.
    - Zum Bezeichnen von Dateien wählen Sie außerdem **Dateien und E-Mails** aus. Diese Option ist nicht erforderlich, um nur Datenbank-Assets zu kennzeichnen

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="Automatische Kennzeichnung im Microsoft 365 Compliance Center" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. Folgen Sie den weiteren Anweisungen, um die Etiketteneinstellungen zu konfigurieren.

    Definieren Sie insbesondere die Regeln für die automatische Bezeichnung von Dateien und Datenbankspalten:

    - [Definieren von Regeln für die automatische Bezeichnung von Dateien](#autolabeling-for-files)
    - [Definieren von Regeln für die automatische Bezeichnung von Datenbankspalten](#autolabeling-for-database-columns)

    Weitere Informationen zu den Konfigurationsoptionen finden Sie in der Microsoft 365-Dokumentation unter [Die Funktionen von Sensitivitätskennzeichnungen](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do).

1. Wiederholen Sie die oben aufgeführten Schritte, um weitere Bezeichnungen zu erstellen.

    Um eine untergeordnete Bezeichnung zu erstellen, wählen Sie die übergeordnete Bezeichnung und dann **...**  > **Weitere Aktionen** > **Untergeordnete Bezeichnung hinzufügen**.

1. Zum Ändern vorhandener Bezeichnungen navigieren Sie zu **Information Protection** > **Bezeichnungen** und wählen Ihre Bezeichnung aus.

    Wählen Sie dann **Etikett bearbeiten**, um die **Konfiguration des Empfindlichkeitsetiketts erneut zu öffnen**, und zwar mit allen Einstellungen, die Sie beim Erstellen des Etiketts festgelegt hatten.

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="Bearbeiten einer vorhandenen Vertraulichkeitsbezeichnung" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. Wenn Sie alle Bezeichnungen erstellt haben, zeigen Sie unbedingt die Reihenfolge der Bezeichnungen an, und ordnen Sie sie bei Bedarf neu an.

    Wenn Sie die Reihenfolge einer Bezeichnung ändern möchten, wählen Sie **...** **> Weitere Aktionen** > **Nach oben** oder **Nach unten** aus.

    Weitere Informationen finden Sie unter [Priorität der Bezeichnungen (Reihenfolge wesentlich)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) in der Microsoft 365-Dokumentation.

#### <a name="autolabeling-for-files"></a>Autolabeling für Dateien

Definieren Sie Regeln für die automatische Etikettierung von Dateien, wenn Sie Ihr Etikett erstellen oder bearbeiten.

Aktivieren Sie auf der Seite **Automatische Bezeichnung für Office-Apps** die Option **Automatische Bezeichnung für Office-Apps**, und definieren Sie dann die Bedingungen für die automatische Anwendung Ihrer Bezeichnung auf die Daten.

Beispiel:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="Definieren Sie Regeln für die automatische Beschriftung von Dateien im Microsoft 365 Compliance Center" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

Weitere Informationen finden Sie unter [Konfigurieren der automatischen Zuweisung von Bezeichnungen für Office-Apps](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) in der Microsoft 365-Dokumentation.

#### <a name="autolabeling-for-database-columns"></a>Autolabeling für Datenbankspalten

Definieren Sie Regeln für die automatische Etikettierung von Datenbankspalten, wenn Sie Ihr Etikett erstellen oder bearbeiten.

Unter der **Option „Datenbankspalten“:**

1. Wählen Sie den Schieberegler **Automatische Bezeichnung für Datenbankspalten** aus.

1. Wählen Sie **Vertrauliche Informationstypen überprüfen** aus, um die vertraulichen Informationstypen auszuwählen, die Sie auf Ihre Bezeichnung anwenden möchten.

Beispiel:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="Definieren Sie Regeln für die automatische Beschriftung von SQL-Spalten im Microsoft 365 Compliance Center" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>Schritt 4: Etiketten veröffentlichen

Sobald Sie eine Beschriftung erstellt haben, müssen Sie Ihre Daten in Azure Purview scannen, um die von Ihnen erstellten Beschriftungen auf der Grundlage der von Ihnen definierten Regeln für die automatische Beschriftung automatisch anzuwenden.

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>Scannen Sie Ihre Daten, um automatisch Sensibilitätskennzeichnungen anzubringen

Überprüfen Sie Ihre Daten in Azure Purview, um die von Ihnen erstellten Bezeichnungen automatisch auf Grundlage der von Ihnen definierten Regeln für die automatische Bezeichnung anzuwenden. Es kann bis zu 24 Stunden dauern, bis die Änderungen der Sensibilitätskennzeichnung in Purview angezeigt werden.

Weitere Informationen zum Einrichten von Scans für verschiedene Ressourcen in Azure Purview finden Sie unter:

|`Source`  |Referenz  |
|---------|---------|
|**Dateien in der Ablage** | [Registrieren und Überprüfen von Azure Blob Storage](register-scan-azure-blob-storage-source.md) </br> [Registrieren und Scannen von Azure-Dateien](register-scan-azure-files-storage-source.md) [Registrieren und Scannen von Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registrieren und Überprüfen von Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)</br>[Amazon S3 registrieren und scannen](register-scan-amazon-s3.md) |
|**Datenbankspalten** | [Registrieren und Überprüfen von Azure SQL-Datenbank](register-scan-azure-sql-database.md) </br>[Registrieren und Überprüfen einer verwalteten Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md) </br> [Dedizierte SQL-Pools registrieren und scannen](register-scan-azure-synapse-analytics.md)</br> [Registrieren und Überprüfen von Azure Synapse Analytics-Arbeitsbereichen](register-scan-azure-synapse-analytics.md) </br> [Vorgehensweise beim Registrieren und Scannen von Azure Cosmos DB (SQL-API)](register-scan-azure-cosmos-database.md) </br> [Registrieren und Überprüfen einer Azure MySQL-Datenbank](register-scan-azure-mysql-database.md) </br> [Registrieren und Überprüfen einer Azure Database for PostgreSQL-Datenbank](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>Etiketten auf Assets im Katalog anzeigen

Nachdem Sie die Regeln für die automatische Bezeichnung in Microsoft 365 definiert und Ihre Daten in Azure Purview gescannt haben, werden automatisch Bezeichnungen auf Ihre Ressourcen angewendet.

**So zeigen Sie die Bezeichnungen an, die im Azure Purview Catalog auf Ihre Ressourcen angewendet werden:**

Verwenden Sie im Azure Purview-Katalog die Filteroptionen für **Labels**, um nur Assets mit bestimmten Labels anzuzeigen. Beispiel:

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="Nach Bezeichnung nach Ressourcen suchen" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

Um Details zu einem Vermögenswert einschließlich der gefundenen Klassifizierungen und der angebrachten Kennzeichnung anzuzeigen, klicken Sie auf den Vermögenswert in den Ergebnissen.

Beispiel:

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="Vertraulichkeitsbezeichnung einer Datei in Ihrer Azure Blob Storage-Instanz anzeigen" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Anzeigen von Erkenntnisse-Berichten für die Klassifizierungen und Vertraulichkeitsbezeichnungen

Erhalten Sie Einblicke in Ihre klassifizierten und beschrifteten Daten in Azure Purview, indem Sie die **Klassifizierungs**- und **Sensitivitätsbeschriftungsberichte** verwenden.

> [!div class="nextstepaction"]
> [Klassifizierungs-Erkenntnisse](./classification-insights.md)

> [!div class="nextstepaction"]
> [Vertraulichkeitsbezeichnungs-Erkenntnisse](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Überblick über die Kennzeichnung in Azure Purview](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zur Kennzeichnung](sensitivity-labels-frequently-asked-questions.yml)
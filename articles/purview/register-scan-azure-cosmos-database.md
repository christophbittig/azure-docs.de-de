---
title: Vorgehensweise beim Registrieren und Scannen von Azure Cosmos DB (SQL-API)
description: In diesem Artikel wird der Prozess zum Registrieren einer Azure Cosmos Datenquelle (SQL-API) in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Cosmos-Datenbank
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ba4c12c51ad744e6c68d63c1aaad17f32fbec15e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841978"
---
# <a name="connect-to-azure-cosmos-database-sql-api-in-azure-purview"></a>Herstellen einer Verbindung mit Azure Cosmos-Datenbank (SQL-API) in Azure Purview

In diesem Artikel wird der Prozess zum Registrieren einer Azure Cosmos Datenbank (SQL-API) in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Cosmos-Datenbankquelle

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)|[Ja](#scan) | [Ja](#scan)|[Ja](#scan)|Nein|nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt können Sie die Azure Blob Cosmos-Datenbank (SQL-API) registrieren und einen geeigneten Authentifizierungsmechanismus einrichten, um eine erfolgreiche Überprüfung der Datenquelle sicherzustellen.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Es ist wichtig, die Datenquelle in Azure Purview zu registrieren, bevor Sie eine Überprüfung für die Datenquelle einrichten.

1. Gehen Sie zum [Azure-Portal](https://portal.azure.com), navigieren Sie zur Seite **Purview-Konten** und klicken Sie auf Ihr _Purview-Konto_

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-purview-acct.png" alt-text="Screenshot: Purview-Konto zum Registrieren der Datenquelle":::

1. **Öffnen Sie Purview Studio** und navigieren Sie zu **Data Map --> Sammlungen**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-open-purview-studio.png" alt-text="Screenshot: Navigieren zum Quellen-Link in der Data Map":::

1. Erstellen Sie die [Sammlungshierarchie](./quickstart-create-collection.md) mithilfe des Menüs **Sammlungen**, und weisen Sie den einzelnen untergeordneten Sammlungen nach Bedarf Berechtigungen zu

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collections.png" alt-text="Screenshot: Sammlungsmenü zum Erstellen einer Sammlungshierarchie":::

1. Navigieren Sie im Menü **Quellen** zur entsprechenden Sammlung, und klicken Sie auf das Symbol **Registrieren**, um eine neue Azure Cosmos Datenbank zu registrieren

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-register-data-source.png" alt-text="Screenshot: Sammlung zum Registrieren der Datenquelle":::

1. Wählen Sie die **Azure Cosmos DB (SQL-API)** Datenquelle und dann **Weiter** aus

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-data-source.png" alt-text="Screenshot, der die Auswahl der Datenquelle ermöglicht":::

1. Geben Sie einen geeigneten **Namen** für die Datenquelle an, wählen Sie das entsprechende **Azure-Abonnement**, den **Azure Cosmos DB Kontonamen** und die **Sammlung** aus, und klicken Sie auf **Übernehmen**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-data-source-details.png" alt-text="Screenshot mit den Details, die eingegeben werden müssen, um die Datenquelle zu registrieren":::

1. Das _Azure Cosmos Datenbank_-Speicherkonto wird unter der ausgewählten Sammlung angezeigt

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collection-mapping.png" alt-text="Screenshot der Datenquelle, die der Sammlung zugeordnet ist, um die Überprüfung zu initiieren":::

## <a name="scan"></a>Überprüfen

### <a name="authentication-for-a-scan"></a>Authentifizierung für eine Überprüfung

Um Zugriff zum Überprüfen der Datenquelle zu haben, muss eine Authentifizierungsmethode im Azure Cosmos-Datenbank-Speicherkonto konfiguriert werden.

Es gibt nur eine Möglichkeit, die Authentifizierung für Azure Cosmos Datenbank einzurichten:

**Kontoschlüssel**: Geheimnisse können in einer Azure Key Vault erstellt werden, um Anmeldeinformationen zu speichern, um den Zugriff für Azure Purview zum sicheren Überprüfen von Datenquellen mithilfe der Geheimnisse zu ermöglichen. Ein Geheimnis kann ein Speicherkontoschlüssel, ein SQL Anmeldekennwort oder ein Kennwort sein.

> [!Note]
> Sie müssen eine _Azure Key Vault_ Ressource in Ihrem Abonnement bereitstellen und die MSI eines _Azure Purview Accounts_ mit erforderlicher Zugriffsberechtigung auf Geheimnisse im _Azure Key Vault_ zuweisen.

#### <a name="using-account-key-for-scanning"></a>Verwenden des Kontoschlüssels für die Überprüfung

Sie müssen Ihren Zugriffsschlüssel erhalten und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem Azure Cosmos-Datenbankspeicherkonto
1. Wählen Sie dann **Einstellungen > Schlüssel** aus

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-access-keys.png" alt-text="Screenshot: Zugriffsschlüssel im Speicherkonto":::

1. Kopieren Sie Ihren *Schlüssel*, und speichern Sie ihn separat für die nächsten Schritte

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key.png" alt-text="Screenshot, der die zu kopierenden Zugriffsschlüssel zeigt":::

1. Navigieren zum Schlüsseltresor

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault.png" alt-text="Screenshot: Schlüsseltresor":::

1. Navigieren Sie zu Ihrer Key Vault-Instanz, und wählen Sie **Einstellungen > Geheimnisse** und dann **+ Generieren/Importieren** aus

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-generate-secret.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Generieren eines Geheimnisses":::

1. Geben Sie den **Namen** und den **Wert** als *Schlüssel* von ihrem Speicherkonto ein und wählen Sie **Erstellen** zum Vervollständigen

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault-options.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Eingeben der Geheimniswerte":::

1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Zum Schluss [erstellen Sie neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

### <a name="creating-the-scan"></a>Erstellen der Überprüfung

1. Öffnen Sie Ihr **Purview-Konto,** und klicken Sie auf **Purview Studio öffnen**
1. Navigieren Sie zu **Datenzuordnungs** --> **quelle**, um die Sammlungshierarchie anzuzeigen
1. Klicken Sie unter der zuvor registrierten **Azure Cosmos Datenbank** auf das Symbol **Neue Überprüfung**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-create-scan.png" alt-text="Screenshot: Bildschirm zum Erstellen einer neuen Überprüfung":::

1. Geben Sie einen **Namen** für die Überprüfung an, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und klicken Sie auf **+ Neu** unter den **Anmeldeinformationen**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-option.png" alt-text="Screenshot: Option „Kontoschlüssel“ für die Überprüfung":::

1. Wählen Sie die entsprechende **Schlüsseltresor-Verbindung** und den **geheimen Namen** aus, die beim Erstellen des _Kontoschlüssels_ verwendet wurden. Wählen Sie _Kontoschlüssel_ als **Authentifizierungsmethode** aus

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-details.png" alt-text="Screenshot, der die Kontoschlüsseloptionen zeigt":::

1. Klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-test-connection.png" alt-text="Screenshot: Erfolgreiches Testen der Verbindung":::

### <a name="scoping-and-running-the-scan"></a>Definieren und Ausführen der Überprüfung

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner und Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-new-scan-rule-set.png" alt-text="Neue Überprüfungsregel":::

1. Sie können die **Klassifizierungsregeln** auswählen, die in die Überprüfungsregel aufgenommen werden sollen

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-classification.png" alt-text="Überprüfen von Klassifizierungsregeln":::


    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-scan-rule-set.png" alt-text="Überprüfen der Regelauswahl":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-trigger.png" alt-text="Auslöser für die Überprüfung":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-review-scan.png" alt-text="Überprüfungs-Scan":::

### <a name="viewing-scan"></a>Anzeigen der Überprüfung

1. Navigieren Sie zur _Datenquelle_ in der _Sammlung_ und klicken Sie auf **Details anzeigen**, um den Status der Überprüfung zu prüfen

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-view-scan.png" alt-text="Überprüfung anzeigen":::

1. Die Scandetails geben den Fortschritt der Überprüfung im **Status der letzten Ausführung** an sowie die Anzahl der _gescannten_ und _klassifizierten_ Objekte

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-last-run-status.png" alt-text="Überprüfungsdetails anzeigen":::

1. Der **Status Letzte Ausführung** wird in **In Bearbeitung** und dann in **Abgeschlossen** aktualisiert, sobald die gesamte Überprüfung erfolgreich ausgeführt wurde

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-in-progress.png" alt-text="Anzeigen der Überprüfung in Bearbeitung":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-completed.png" alt-text="Die abgeschlossene Überprüfung anzeigen":::

### <a name="managing-scan"></a>Verwalten der Überprüfung

Überprüfungen können verwaltet oder nach Abschluss erneut ausgeführt werden.

1. Klicken Sie auf den **Überprüfungsnamen**, um die Überprüfung zu verwalten

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan.png" alt-text="Verwalten der Überprüfung":::

1. Sie können _die Überprüfung erneut ausführen_, _die Überprüfung bearbeiten_ oder _die Überprüfung löschen_  

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan-options.png" alt-text="Verwalten von Überprüfungsoptionen":::

1. Sie können eine vollständige _Überprüfung erneut_ ausführen

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-full-scan.png" alt-text="Vollständige Überprüfung":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

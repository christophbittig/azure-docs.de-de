---
title: Verbinden mit und Verwalten von Azure Database for MySQL
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit einer Azure Database for MySQL-Instanz in Azure Purview herstellen und die Features von Purview verwenden, um Ihre Azure Database for MySQL-Quelle zu überprüfen und zu verwalten.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: bcde9aee9719f8dbb127b908c312cc734c559f02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841887"
---
# <a name="connect-to-and-manage-azure-databases-for-mysql-in-azure-purview"></a>Verbinden mit und Verwalten von Azure-Datenbanken für MySQL in Azure Purview

In diesem Artikel wird beschrieben, wie Sie eine Azure Database for MySQL-Instanz registrieren, sich authentifizieren und mit Azure Database for MySQL-Instanzen in Azure Purview interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| [Ja*](#scan) | [Ja](#scan) | [Ja](#scan) | Nein | nein** |

\* Purview basiert auf UPDATE_TIME Metadaten aus der Azure Database for MySQL für inkrementelle Überprüfungen. In einigen Fällen wird dieses Feld möglicherweise nicht in der Datenbank beibehalten, und es wird eine vollständige Überprüfung durchgeführt. Weitere Informationen finden Sie in der Tabelle [INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) für MySQL.

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie eine Azure Database for MySQL-Instanz in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

Befolgen Sie die Anweisungen unter [ERSTELLEN VON DATENBANKEN UND BENUTZERN](../mysql/howto-create-users.md), um eine Anmeldung für Ihre Azure Database for MySQL-Instanz zu erstellen.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure SQL-Datenbank ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential) vom Typ „SQL-Authentifizierung“, indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Azure Database for MySQL-Instanz zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren**.

1. Wählen Sie unter **Quellen registrieren** die Option **Azure Database for MySQL** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Azure Database for MySQL)** (Quellen registrieren (Azure Database for MySQL)) wie folgt vor:

1. Geben Sie unter **Name** einen Namen für die Datenquelle ein. Dies wird der Anzeigename für diese Datenquelle in Ihrem Katalog.
1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.
1. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Azure Database for MySQL zu überprüfen und automatisch Assets zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die Azure Database for MySQL Quelle, die Sie registriert haben aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen aus, um eine Verbindung mit Ihrer Datenquelle herzustellen, und überprüfen Sie die Verbindung, um sicherzustellen, dass Ihre Anmeldeinformation ordnungsgemäß konfiguriert ist.

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner oder Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus. -->

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="Sehen Sie Ihre Überprüfung noch einmal an" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

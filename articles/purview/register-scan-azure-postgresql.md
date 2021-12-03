---
title: Verbinden und Verwalten einer Azure Database for PostgreSQL-Instanz
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit einer Azure Database for PostgreSQL Single Server-Instanz in Azure Purview herstellen und die Features von Purview verwenden, um Ihre Azure Database for PostgreSQL-Quelle zu überprüfen und zu verwalten.
author: evangelinew
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 46e2f60adc895b60370ed7109305bb8966057fa3
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402388"
---
# <a name="connect-to-and-manage-an-azure-database-for-postgresql-in-azure-purview"></a>Verbinden und Verwalten einer Azure Database for PostgreSQL-Instanz in Azure Purview

In diesem Artikel wird beschrieben, wie Sie eine Azure Database for PostgreSQL-Instanz registrieren, die mit der Bereitstellungsoption „Einzelserver“ bereitgestellt wurde, und wie Sie eine Azure Database for PostgreSQL-Instanz in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | Nein | nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

> [!Important]
>  Purview unterstützt für Azure Database for PostgreSQL nur die Bereitstellungsoption „Einzelserver“. Versionen 8.x bis 12.x

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie eine Azure Database for PostgreSQL-Instanz in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Derzeit wird nur die SQL-Authentifizierung unterstützt, um eine Azure Database for PostgreSQL Single Server-Instanz verwalten und mit ihr interagieren zu können.

#### <a name="sql-authentication"></a>SQL-Authentifizierung

Für die Verbindungsherstellung mit einer Azure Database for PostgreSQL-Datenbank sind der vollqualifizierte Servername und Anmeldeinformationen erforderlich. Sie können die Anleitung unter [Schnellstart: Verwenden von Python zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten](../postgresql/connect-python.md) befolgen, um einen Anmeldenamen für Ihre Azure Database for PostgreSQL-Datenbank zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Wenn Sie noch nicht über eine Azure Key Vault-Instanz verfügen, befolgen Sie [diese Anleitung, um eine Azure Key Vault-Instanz zu erstellen](../key-vault/certificates/quick-create-portal.md).
1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure Database for PostgreSQL ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential) vom Typ „SQL-Authentifizierung“, indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Azure Database for PostgreSQL-Datenbank zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Database for PostgreSQL** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Azure Database for PostgreSQL)** (Quellen registrieren (Azure Database for PostgreSQL)) wie folgt vor:

1. Geben Sie unter **Name** einen Namen für die Datenquelle ein. Dies wird der Anzeigename für diese Datenquelle in Ihrem Katalog.
1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.
1. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um eine Datenbank einer Azure Database for PostgreSQL-Instanz so zu überprüfen, dass Ressourcen automatisch identifiziert und Ihre Daten klassifiziert werden. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die Azure Database for PostgreSQL-Quelle aus, die Sie registriert haben.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner oder Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="Auslösen der Überprüfung":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

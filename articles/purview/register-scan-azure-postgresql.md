---
title: Registrieren und Überprüfen einer Azure Database for PostgreSQL-Datenbank
description: In diesem Tutorial wird beschrieben, wie Sie eine Azure Database for PostgreSQL-Datenbank überprüfen.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: 622e89392dc10f12fe81ed1c1085e5b9cc524999
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181297"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>Registrieren und Überprüfen einer Azure Database for PostgreSQL-Datenbank

In diesem Artikel wird beschrieben, wie Sie eine Azure Database for PostgreSQL-Datenbank registrieren und überprüfen.


## <a name="supported-capabilities"></a>Unterstützte Funktionen
- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure Database for PostgreSQL-Datenbanken

- **Herkunft** zwischen Datenressourcen für Kopier- und Datenflussaktivitäten von ADF

### <a name="known-limitations"></a>Bekannte Einschränkungen

Purview unterstützt nur die SQL-Authentifizierung für Azure Database for PostgreSQL-Datenbanken.


## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie ein neues Purview-Konto, wenn Sie noch keines besitzen.

2. Netzwerkzugriff zwischen Ihrem Purview-Konto und der Azure Database for PostgreSQL-Datenbank

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>SQL-Authentifizierung für eine Azure Database for PostgreSQL-Datenbank

Für die Verbindungsherstellung mit einer Azure Database for PostgreSQL-Datenbank sind der vollqualifizierte Servername und Anmeldeinformationen erforderlich. Sie können die Anleitung unter [Schnellstart: Verwenden von Python zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten](../postgresql/connect-python.md) befolgen, um einen Anmeldenamen für Ihre Azure Database for PostgreSQL-Datenbank zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure Database for PostgreSQL ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential) vom Typ „SQL-Authentifizierung“, indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-an-azure-database-for-postgresql-data-source"></a>Registrieren einer Azure Database for PostgreSQL-Datenquelle

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

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

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

> [!NOTE]
> * Beim Löschen Ihrer Überprüfung werden nicht die Katalogressourcen aus den vorherigen Überprüfungen gelöscht.
> * Die Ressource wird nicht mehr mit Schemaänderungen aktualisiert, wenn die Quelltabelle geändert wurde und Sie die Quelltabelle nach dem Bearbeiten der Beschreibung auf der Registerkarte „Schema“ von Purview erneut überprüfen.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
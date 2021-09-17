---
title: Registrieren und Überprüfen einer Azure MySQL-Datenbank
description: In diesem Tutorial wird beschrieben, wie eine Azure MySQL-Datenbank überprüft wird
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: ed3a0abc6bdd99e5c86e67a410d7c9ecadf7a718
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737645"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>Registrieren und Überprüfen einer Azure Database for MySQL

In diesem Artikel wird beschrieben, wie Sie eine Azure MySQL-Datenbank registrieren und überprüfen.


## <a name="supported-capabilities"></a>Unterstützte Funktionen
- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure MySQL-Datenbanken.

- **Herkunft** zwischen Datenressourcen für Kopier- und Datenflussaktivitäten von ADF

### <a name="known-limitations"></a>Bekannte Einschränkungen
Purview unterstützt nur die SQL-Authentifizierung für Azure MySQL-Datenbanken.


## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie ein neues Purview-Konto, wenn Sie noch keines besitzen.

2. Der Netzwerkzugriff zwischen Ihrem Purview-Konto und der Azure MySQL-Datenbank.

### <a name="set-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

Befolgen Sie die Anweisungen unter [ERSTELLEN VON DATENBANKEN UND BENUTZERN,](../mysql/howto-create-users.md) um eine Anmeldung für Ihre Azure MySQL-Datenbank zu erstellen.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure SQL-Datenbank ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential) vom Typ SQL-Authentifizierung, indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden

## <a name="register-an-azure-mysql-database-data-source"></a>Registrieren einer Datenquelle vom Typ „Azure Database for MySQL“

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Azure Database for MySQL zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren**.

1. Wählen Sie unter **Quellen registrieren** die Option **Azure Database for MySQL** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (Azure Database for MySQL)** wie folgt vor:

1. Geben Sie einen **Namen** für die Datenquelle ein. Dies wird der Anzeigename für diese Datenquelle in Ihrem Katalog.
1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.
1. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren. 

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

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

> [!NOTE]
> * Beim Löschen Ihrer Überprüfung werden nicht die Ressourcen aus den vorherigen Überprüfungen von der Azure Database for MySQL gelöscht.
> * Die Ressource wird nicht mehr mit Schemaänderungen aktualisiert, wenn die Quelltabelle geändert wurde und Sie die Quelltabelle nach dem Bearbeiten der Beschreibung auf der Registerkarte „Schema“ von Purview erneut überprüft.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

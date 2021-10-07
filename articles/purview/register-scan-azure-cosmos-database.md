---
title: Vorgehensweise beim Scannen von Azure Cosmos DB (SQL-API)
description: In diesem Leitfaden wird beschrieben, wie Sie eine Überprüfung für Azure Cosmos DB (SQL-API) durchführen.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 14799d5a3ae5878461775e8f581fdda44c140d30
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209963"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Vorgehensweise beim Registrieren und Scannen von Azure Cosmos DB (SQL-API)

In diesem Artikel wird beschrieben, wie Sie ein Azure Cosmos DB-Konto (SQL-API) in Azure Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Für Azure Cosmos DB (SQL-API) werden vollständige und inkrementelle Überprüfungen zum Erfassen der Metadaten und des Schemas unterstützt. Darüber hinaus werden die Daten bei Überprüfungen basierend auf den system- und benutzerdefinierten Klassifizierungsregeln automatisch klassifiziert.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Azure Purview-Datenquellenadministrator sein.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Es gibt nur eine Möglichkeit, die Authentifizierung für Azure Cosmos DB (SQL-API) einzurichten:

- Kontoschlüssel
 
### <a name="account-key"></a>Kontoschlüssel

Wenn **Kontoschlüssel** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihren Zugriffsschlüssel abrufen und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem Cosmos DB-Konto im Azure-Portal. 
1. Wählen Sie **Einstellungen** > **Schlüssel** aus. 
1. Kopieren Sie einen PRIMARY- oder SECONDARY-Schlüssel aus den *Lese-/Schreibschlüsseln* oder  *schreibgeschützten Schlüsseln*, und speichern Sie ihn irgendwo für die nächsten Schritte.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und den **Wert** als *Schlüssel* für Ihr Azure Cosmos DB-Konto ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrieren eines Azure Cosmos DB-Kontos (SQL-API)

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues Azure Cosmos DB-Konto (SQL-API) zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter **Quellen registrieren** die Option **Azure Cosmos DB (SQL-API)** aus.
1. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (Azure Cosmos DB (SQL-API))** wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
2. Wählen Sie Ihr Azure-Abonnement aus, um Azure Cosmos DB zu filtern.
3. Wählen Sie einen geeigneten Namen für das Cosmos DB Konto aus.
4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
5. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::


## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte Azure Cosmos DB-Datenquelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus. 

   :::image type="content" source="media/register-scan-azure-cosmos-database/set-up-scan-cosmos.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Datenbanken festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-cosmos-database/cosmos-database-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-cosmos-database/select-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-cosmos-database/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

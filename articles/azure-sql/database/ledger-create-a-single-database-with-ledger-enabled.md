---
title: Erstellen einer Einzeldatenbank mit aktiviertem Ledger
description: Erstellen Sie eine Einzeldatenbank in Azure SQL-Datenbank mit aktiviertem Ledger über das Azure-Portal.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 8538e725583aea6fe765a7afd6e0ac58f176b443
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063739"
---
# <a name="quickstart-create-a-database-in-azure-sql-database-with-ledger-enabled"></a>Schnellstart: Erstellen einer Datenbank in Azure SQL-Datenbank mit aktiviertem Ledger

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

In diesem Schnellstart erstellen Sie eine [Ledgerdatenbank](ledger-overview.md#ledger-database) in Azure SQL-Datenbank und konfigurieren den [automatischen Digestspeicher mit Azure Blob Storage](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests) über das Azure-Portal. Weitere Informationen zum Ledger finden Sie unter [Azure SQL-Datenbank-Ledger.](ledger-overview.md)

## <a name="prerequisite"></a>Voraussetzung

Sie benötigen ein aktives Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>Erstellen einer Ledgerdatenbank und Konfigurieren des Digestspeichers

Erstellen Sie eine einzelne Ledgerdatenbank auf der [serverlosen Computeebene](serverless-tier-overview.md), und konfigurieren Sie den Upload der Ledgerdigests in ein Azure Storage-Konto.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Diese Schnellstarteinleitung beginnt für die Erstellung eines Singletons im Azure-Portal auf der Azure SQL-Seite.

1. Navigieren Sie zur Seite [SQL-Bereitstellungsoption auswählen](https://portal.azure.com/#create/Microsoft.AzureSQL).

1. Behalten Sie unter **SQL-Datenbanken** für **Einzeldatenbank** den festgelegten Wert **Ressourcentyp** bei, und wählen Sie **Erstellen** aus.

   ![Screenshot des Hinzufügens zu Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. Wählen Sie auf der Registerkarte **Grundeinstellungen** des Formulars **SQL-Datenbank erstellen** unter **Projektdetails** das gewünschte Azure-Abonnement aus.

1. Wählen Sie bei **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie **myResourceGroup** ein, und wählen Sie **OK** aus.

1. Geben Sie für **Datenbankname** **demo** ein.

1. Wählen Sie unter **Server** die Option **Neu erstellen** aus. Füllen Sie das Formular **Neuer Server** mit den folgenden Werten aus:
   - **Servername**: Geben Sie **mysqlserver** ein, und fügen Sie einige weitere Zeichen hinzu, um einen eindeutigen Wert zu erhalten. Wir können keinen exakten Servernamen zur Verwendung angeben, weil Servernamen für alle Server in Azure global eindeutig sein müssen (und nicht nur innerhalb eines Abonnements eindeutig sind). Geben Sie beispielsweise **mysqlserver12345** ein. Sie werden vom Portal informiert, ob dieser Server verfügbar ist oder nicht.
   - **Serveradministratoranmeldung**: Geben Sie **azureuser** ein.
   - **Kennwort:** Geben Sie ein Kennwort ein, das die Anforderungen erfüllt. Geben Sie es erneut in das Feld **Kennwort bestätigen** ein.
   - **Standort**: Wählen Sie in der Dropdownliste einen Standort aus.
   - **Allow Azure services to access this server** (Zugriff durch Azure-Dienste auf diesen Server zulassen): Wählen Sie diese Option aus, um den Zugriff auf den Digestspeicher zuzulassen.
   
   Klicken Sie auf **OK**.
   
1. Behalten Sie für **Möchten Sie einen Pool für elastische SQL-Datenbanken verwenden?** den Wert **Nein** bei.

1. Wählen Sie unter **Compute + Speicher** die Option **Datenbank konfigurieren** aus.

1. Weil in dieser Schnellstartanleitung eine serverlose Datenbank verwendet wird, wählen Sie **Serverlos** und dann **Anwenden** aus. 

      ![Screenshot der Konfiguration einer serverlosen Datenbank](./media/single-database-create-quickstart/configure-database.png)

1. Wählen Sie auf der Registerkarte **Netzwerk** als **Konnektivitätsmethode** die Option **Öffentlicher Endpunkt** aus.
1. Legen Sie bei **Firewallregeln** die Option **Aktuelle Client-IP-Adresse hinzufügen** auf **Ja** fest. Behalten Sie für **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe gestatten** den Wert **Nein** bei.
1. Wählen Sie **Weiter: Sicherheit** unten auf der Seite aus.

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Screenshot der Registerkarte „Netzwerk“ des Bildschirms „SQL-Datenbank erstellen“ im Azure-Portal":::

1. Wählen Sie auf der Registerkarte **Sicherheit** im Abschnitt **Ledger** die Option **Configure ledger** (Ledger konfigurieren) aus.

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="Screenshot der Konfiguration eines Ledgers auf der Registerkarte „Sicherheit“ im Azure-Portal":::

1. Aktivieren Sie im Bereich **Configure ledger** (Ledger konfigurieren) im Abschnitt **Ledger** das Kontrollkästchen **Enable for all future tables in this database** (Für alle zukünftigen Tabellen in dieser Datenbank konfigurieren). Mit dieser Einstellung wird sichergestellt, dass alle zukünftigen Tabellen in der Datenbank Ledgertabellen sind. Aus diesem Grund werden bei allen Daten in der Datenbank sämtliche Anzeichen für Manipulationen angezeigt. Standardmäßig werden neue Tabellen als aktualisierbare Ledgertabellen erstellt, auch wenn Sie `LEDGER = ON` in [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) nicht angeben. Sie können diese Option auch deaktiviert lassen. Sie müssen die Ledgerfunktionalität dann später tabellenweise aktivieren, wenn Sie neue Tabellen mithilfe von Transact-SQL erstellen.

1. Im Abschnitt **Digest Storage** (Digestspeicher) wird **Aktivieren des automatischen Digest-Speichers** automatisch aktiviert. Anschließend werden ein neues Azure Storage-Konto und ein Container erstellt, in dem Ihre Digests gespeichert werden.

1. Wählen Sie **Übernehmen**.

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Screenshot des Bereichs „Ledger konfigurieren (Vorschau)“ im Azure-Portal":::

1. Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus.

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="Screenshot der Überprüfung und Erstellung einer Ledgerdatenbank auf der Registerkarte „Sicherheit“ im Azure-Portal":::

1. Wählen Sie nach der Überprüfung auf der Seite **Überprüfen + erstellen** die Option **Erstellen** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Behalten Sie für die nächsten Schritte die Ressourcengruppe, den Server und die Einzeldatenbank bei. Sie erfahren, wie Sie das Ledgerfeature Ihrer Datenbank mit verschiedenen Methoden verwenden.

Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die erstellte Ressourcengruppe. Mit dieser Aktion werden auch der darin enthaltene Server und die Einzeldatenbank gelöscht.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

So löschen Sie **myResourceGroup** und alle zugehörigen Ressourcen über das Azure-Portal

1. Suchen Sie im Portal nach **Ressourcengruppen**, und wählen Sie die Option aus. Wählen Sie dann **myResourceGroup** in der Liste aus.
1. Wählen Sie auf der Ressourcengruppenseite die Option **Ressourcengruppe löschen** aus.
1. Geben Sie unter **Geben Sie den Ressourcengruppennamen ein** den Namen **myResourceGroup** ein, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie für Ihre Datenbank die Verbindungsherstellung und Abfragen mit unterschiedlichen Tools und Sprachen durch:

- [Erstellen und Verwenden aktualisierbarer Ledgertabellen](ledger-how-to-updatable-ledger-tables.md)
- [Erstellen und Verwenden von Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-how-to-append-only-ledger-tables.md) 

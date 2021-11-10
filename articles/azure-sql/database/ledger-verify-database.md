---
title: Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen
description: In diesem Artikel wird erläutert, wie Sie überprüfen können, ob eine Azure SQL-Datenbank-Tabelle manipuliert wurde.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 272138471499cbd62085301df8e518ea47a7ddc3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058844"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

In diesem Artikel überprüfen Sie die Integrität der Daten in Ihren Azure SQL-Datenbank-Ledgertabellen. Wenn Sie beim [Erstellen Ihrer Datenbank in SQL-Datenbank](ledger-create-a-single-database-with-ledger-enabled.md) die Option **Automatischen Digestspeicher aktivieren** aktiviert haben, befolgen Sie die Anweisungen im Azure-Portal, damit das Transact-SQL-Skript (T-SQL), das für die Überprüfung des Datenbankledgers im [Abfrage-Editor](connect-query-portal.md) erforderlich ist, automatisch generiert wird. Befolgen Sie andernfalls die T-SQL-Anweisungen für [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- [Erstellen einer Datenbank in SQL-Datenbank mit aktiviertem Ledger](ledger-create-a-single-database-with-ledger-enabled.md)
- [Erstellen und Verwenden von aktualisierbaren Ledgertabellen](ledger-how-to-updatable-ledger-tables.md) oder [Erstellen und Verwenden von Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-how-to-append-only-ledger-tables.md)

## <a name="run-ledger-verification-for-sql-database"></a>Durchführen der Ledgerüberprüfung für SQL-Datenbank

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), wählen Sie **Alle Ressourcen** aus, und suchen Sie die Datenbank, die Sie überprüfen möchten. Wählen Sie diese Datenbank in SQL-Datenbank aus.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="Screenshot des Azure-Portals mit ausgewählter Registerkarte „Alle Ressourcen“":::

1. Wählen Sie unter **Sicherheit** die Option **Ledger** aus.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Screenshot des Azure-Portals mit ausgewählter Registerkarte „Ledger“ unter „Sicherheit“":::

1. Wählen Sie im Bereich **Ledger** die Schaltfläche **</> Datenbank überprüfen** und dann im Fenster im bereits ausgefüllten Text das Symbol **Kopieren** aus.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure-Portal mit der Schaltfläche „Datenbank überprüfen“":::

   > > [!IMPORTANT]
   > Wenn Sie keinen automatischen Digestspeicher für Ihre Datenbankdigests konfiguriert haben und Digests stattdessen manuell verwalten, kopieren Sie dieses Skript nicht. Fahren Sie mit Schritt 6 fort.

1. Öffnen Sie im Menü links den **Abfrage-Editor**.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Screenshot der Menüoption „Abfrage-Editor“ im Azure-Portal":::

1. Fügen Sie im Abfrage-Editor das in Schritt 3 kopierte T-SQL-Skript ein, und wählen Sie **Ausführen** aus. Fahren Sie mit Schritt 8 fort.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Screenshot der Menüoption „Ausführen“ für den Abfrage-Editor im Azure-Portal zum Überprüfen der Datenbank":::

1. Wenn Sie den manuellen Digestspeicher verwenden, geben Sie folgenden T-SQL-Code in den Abfrage-Editor ein, um den aktuellen Datenbankdigest abzurufen. Kopieren Sie den Digest für den nächsten Schritt aus den zurückgegebenen Ergebnissen.

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. Fügen Sie im Abfrage-Editor folgenden T-SQL-Code ein, ersetzen Sie `<database_digest>` durch den Digest, den Sie in Schritt 6 kopiert haben, und wählen Sie **Ausführen** aus.

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. Bei der Überprüfung werden im Fenster **Ergebnisse** folgende Meldungen zurückgegeben.

   - Wenn Ihre Datenbank nicht manipuliert wurde, lautet die Meldung:

       ```output
       Ledger verification successful
       ```

   - Wenn Ihre Datenbank manipuliert wurde, wird der folgende Fehler im Fenster **Meldungen** angezeigt:
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[T-SQL mit automatischer Digestspeicherung](#tab/t-sql-automatic)

1. Stellen Sie über [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) eine Verbindung mit Ihrer Datenbank her.

1. Erstellen Sie eine neue Abfrage mit der folgenden T-SQL-Anweisung:

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. Ausführen der Abfrage Sie sehen, dass **digest_locations** den aktuellen Speicherort Ihrer Datenbankdigests und alle früheren Speicherorte zurückgibt. Das **Ergebnis** zeigt eine erfolgreiche oder fehlerhafte Ledgerüberprüfung an.

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="Screenshot der Ausführung der Ledgerüberprüfung mit Azure Data Studio":::

1. Öffnen Sie das Resultset **digest_locations**, um die Speicherorte Ihrer Digests anzuzeigen. Das folgende Beispiel zeigt zwei Digestspeicherorte für diese Datenbank: 

   - **path** gibt den Speicherort der Digests an.
   - **last_digest_block_id** gibt die Block-ID des letzten Digests an, der am Speicherort **path** gespeichert wurde.
   - **is_current** gibt an, ob der Speicherort in **path** der aktuelle (TRUE) oder ein früherer (FALSE) Speicherort ist.

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > Wenn Sie die Ledgerüberprüfung ausführen, überprüfen Sie den Speicherort der **digest_locations**, um sicherzustellen, dass die bei der Überprüfung verwendeten Digests von den erwarteten Speicherorten abgerufen werden. Sie sollten immer sicherstellen, dass kein privilegierter Benutzer die Speicherorte des Digestspeichers an einem ungeschützten Speicherort wie Azure Storage ohne eine konfigurierte und gesperrte Unveränderlichkeitsrichtlinie geändert hat.

1. Bei der Überprüfung wird im Fenster **Ergebnisse** folgende Meldung zurückgegeben.

   - Wenn Ihre Datenbank nicht manipuliert wurde, lautet die Meldung:

       ```output
       Ledger verification successful
       ```

   - Wenn Ihre Datenbank manipuliert wurde, wird der folgende Fehler im Fenster **Meldungen** angezeigt:
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[T-SQL bei manueller Digestspeicherung](#tab/t-sql-manual)

1. Stellen Sie über [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) eine Verbindung mit Ihrer Datenbank her.
1. Erstellen Sie eine neue Abfrage mit der folgenden T-SQL-Anweisung:

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. Ausführen der Abfrage Die Ergebnisse enthalten den neuesten Datenbankdigest und stellen den Hash der Datenbank zum aktuellen Zeitpunkt dar. Kopieren Sie den Inhalt der Ergebnisse, die im nächsten Schritt verwendet werden sollen.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Screenshot zum Abrufen von Digestergebnissen mithilfe von Azure Data Studio":::

1. Erstellen Sie eine neue Abfrage mit der folgenden T-SQL-Anweisung. Ersetzen Sie darin `<YOUR DATABASE DIGEST>` durch den im vorhergehenden Schritt kopierten Hash.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. Führen Sie die Abfrage aus. Das Fenster **Meldungen** enthält die folgende Erfolgsmeldung.

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Screenshot der Meldung nach Ausführung der T-SQL-Abfrage für die Ledgerüberprüfung mithilfe von Azure Data Studio":::

   > [!TIP]
   > Bei Durchführung der Ledgerüberprüfung mit dem neuesten Digest wird nur die Datenbank überprüft – ab dem Zeitpunkt, zu dem der Digest generiert wurde, bis zu dem Zeitpunkt, zu dem die Überprüfung durchgeführt wurde. Wenn Sie überprüfen möchten, ob die Verlaufsdaten in Ihrer Datenbank manipuliert wurden, führen Sie die Überprüfung mithilfe mehrerer Dateien mit Datenbankdigests durch. Beginnen Sie bei dem Zeitpunkt, zu dem Sie die Datenbank überprüfen möchten. Ein Beispiel für eine Überprüfung, bei der mehrere Digests übergeben werden, sieht ungefähr wie die folgende Abfrage aus:

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure SQL-Datenbank-Ledger – Übersicht](ledger-overview.md)
- [SQL-Datenbank-Ledger](ledger-database-ledger.md)
- [Digestverwaltung und Datenbanküberprüfung](ledger-digest-management-and-database-verification.md)
- [Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md)
- [Aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md)
- [Zugreifen auf die in Azure Confidential Ledger gespeicherten Hashes](ledger-how-to-access-acl-digest.md)

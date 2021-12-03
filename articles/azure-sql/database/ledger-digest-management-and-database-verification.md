---
title: Digest-Verwaltung und Datenbanküberprüfung
description: Dieser Artikel enthält Informationen zur Digest-Verwaltung und Datenbanküberprüfung für eine Ledgerdatenbank in Azure SQL-Datenbank.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: df0b87543c213b77a3cf47a7e9020f7f3c0e1866
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059581"
---
# <a name="digest-management-and-database-verification"></a>Digest-Verwaltung und Datenbanküberprüfung

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Der Azure SQL-Datenbank-Ledger ermöglicht eine Form der Datenintegrität, die als *Forward Integrity* („Vorwärtsintegrität“) bezeichnet wird und einen Beweis für die Manipulation von Daten in Ihren Ledgertabellen liefern kann. Wenn beispielsweise eine Banktransaktion in einer Ledgertabelle auftritt, in der ein Saldo auf den Wert `x` aktualisiert wurde, und ein Angreifer die Daten später ändert (Änderung des Saldos von `x` in `y`), wird diese Manipulationsaktivität durch die Datenbanküberprüfung erkannt.  

Für den Prozess der Datenbanküberprüfung werden als Eingabe ein oder mehrere zuvor generierte Datenbank-Digests verwendet. Anschließend werden die Hashes, die im Datenbankledger gespeichert sind, anhand des aktuellen Status der Ledgertabellen neu berechnet. Falls die berechneten Hashes nicht mit den Eingabe-Digests übereinstimmen, wird bei der Überprüfung ein Fehler angezeigt. Der Fehler ist ein Hinweis darauf, dass die Daten manipuliert wurden. Beim Überprüfungsprozess werden alle erkannten Inkonsistenzen gemeldet.

## <a name="database-digests"></a>Datenbankhashes

Der Hash des letzten Blocks im Datenbankledger wird als *Datenbankhash* bezeichnet. Er stellt den Status aller Ledgertabellen in der Datenbank zum Zeitpunkt der Blockgenerierung dar. Die Generierung eines Datenbank-Digests ist effizient, da bei diesem Vorgang nur die Hashes der Blöcke berechnet werden, die vor Kurzem angefügt wurden. 

Datenbank-Digests können entweder automatisch vom System oder manuell vom Benutzer generiert werden. Später können Sie sie dann verwenden, um die Integrität der Datenbank zu überprüfen. 

Datenbank-Digests werden in Form eines JSON-Dokuments generiert, das den Hash des letzten Blocks zusammen mit den Metadaten für die Block-ID enthält. Die Metadaten enthalten den Zeitpunkt der Generierung des Digests und den Commit-Zeitstempel der letzten Transaktion in diesem Block.

Der Überprüfungsprozess und die Integrität der Datenbank hängen von der Integrität der Eingabe-Digests ab. Zu diesem Zweck müssen aus der Datenbank extrahierte Datenbank-Digests in einem vertrauenswürdigen Speicher gespeichert werden, der von Benutzern oder Angreifern mit hohem Berechtigungsgrad, die auf den Azure SQL-Datenbank-Server zugreifen, nicht manipuliert werden kann.

### <a name="automatic-generation-and-storage-of-database-digests"></a>Automatisches Generieren und Speichern von Datenbank-Digests

Der Azure SQL-Datenbank-Ledger ist mit dem [Feature „Unveränderlicher Speicher“ von Azure Blob Storage](../../storage/blobs/immutable-storage-overview.md) und mit [Azure Confidential Ledger](../../confidential-ledger/index.yml) integriert. Im Rahmen dieser Integration werden sichere Speicherdienste in Azure bereitgestellt, um die Datenbank-Digests vor potenziellen Manipulationen zu schützen. Diese Integration bietet Benutzern eine einfache und kostengünstige Möglichkeit, die Digest-Verwaltung zu automatisieren, ohne sich um ihre Verfügbarkeit und geografische Replikation kümmern zu müssen. 

Sie können die automatische Generierung und Speicherung von Datenbank-Digests über das Azure-Portal, PowerShell oder die Azure CLI konfigurieren. Beim Konfigurieren der automatischen Generierung und Speicherung werden Datenbank-Digests nach einem vordefinierten Intervall von 30 Sekunden generiert und in den ausgewählten Speicherdienst hochgeladen. Falls im System während des 30-Sekunden-Intervalls keine Transaktionen erfolgen, wird kein Datenbank-Digest generiert und hochgeladen. Mit diesem Mechanismus wird sichergestellt, dass Datenbank-Digests nur generiert werden, wenn Daten in Ihrer Datenbank aktualisiert wurden.

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="Screenshot: Auswahl für die Aktivierung des Digest-Speichers"::: 

> [!IMPORTANT]
> Konfigurieren Sie nach der Bereitstellung in Ihrem Container eine [Unveränderlichkeitsrichtlinie](../../storage/blobs/immutable-policy-configure-version-scope.md), um sicherzustellen, dass die Datenbank-Digests vor Manipulationen geschützt sind.

### <a name="manual-generation-and-storage-of-database-digests"></a>Manuelles Generieren und Speichern von Datenbank-Digests

Sie können den Azure SQL-Datenbank-Ledger auch nutzen, um einen Datenbank-Digest bedarfsabhängig zu generieren. Auf diese Weise können Sie den Digest manuell in einem beliebigen Dienst oder auf einem beliebigen Gerät speichern, den bzw. das Sie als vertrauenswürdiges Speicherziel ansehen. Beispielsweise können Sie ein lokales WORM-Gerät (Write Once, Read Many) als Ziel auswählen. Sie generieren einen Datenbank-Digest manuell, indem Sie die gespeicherte Prozedur [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) ausführen.

> [!IMPORTANT]
> Zum Generieren von Datenbank-Digests ist die **GENERATE LEDGER DIGEST**-Berechtigung erforderlich. Weitere Informationen zu Berechtigungen im Zusammenhang mit Ledger-Tabellen finden Sie unter [Berechtigungen](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
EXECUTE sp_generate_database_ledger_digest
```

Das zurückgegebene Resultset enthält nur eine Zeile mit Daten. Es sollte wie folgt als JSON-Dokument an einem vertrauenswürdigen Speicherort gespeichert werden:

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>Datenbanküberprüfung

Beim Überprüfungsprozess werden alle Ledger- und Verlaufstabellen überprüft. Die SHA-256-Hashes der darin enthaltenen Zeilen werden neu berechnet und mit den Datenbank-Digest-Dateien verglichen, die an die gespeicherte Prozedur für die Überprüfung übergeben wurden. 

Bei großen Ledgertabellen kann die Datenbanküberprüfung ein ressourcenintensiver Prozess sein. Sie sollten diesen Prozess nur verwenden, wenn Sie die Integrität einer Datenbank überprüfen müssen. 

Der Überprüfungsprozess kann auch stündlich oder täglich ausgeführt werden, falls die Integrität einer Datenbank häufiger überwacht werden muss. Alternativ kann der Prozess auch nur dann ausgeführt werden, wenn die Organisation, die die Daten hostet, eine Überprüfung durchläuft und kryptografische Beweise zur Integrität ihrer Daten bereitstellen muss. Zur Senkung der Überprüfungskosten verfügt der Ledger über Optionen, mit denen festgelegt werden kann, dass nur einzelne Ledgertabellen oder nur ein Teil der Ledgertabellen überprüft werden soll. 

Für die Datenbanküberprüfung können Sie zwei gespeicherte Prozeduren nutzen. Die Vorgehensweise hängt hierbei davon ab, ob Sie [automatischen Digest-Speicher verwenden](#database-verification-that-uses-automatic-digest-storage) oder [Digests manuell verwalten](#database-verification-that-uses-manual-digest-storage).

> [!IMPORTANT]
> Für die Datenbanküberprüfung ist die Berechtigung *View Ledger Content* erforderlich. Weitere Informationen zu Berechtigungen im Zusammenhang mit Ledger-Tabellen finden Sie unter [Berechtigungen](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

### <a name="database-verification-that-uses-automatic-digest-storage"></a>Datenbanküberprüfung mit automatischem Digest-Speicher

Wenn Sie den automatischen Digest-Speicher zum Generieren und Speichern von Datenbank-Digests verwenden, befindet sich der Speicherort des Digest-Speichers in der Systemkatalogsicht [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) (in Form von JSON-Objekten). Die Datenbanküberprüfung umfasst die Ausführung der gespeicherten Systemprozedur [sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql). Geben Sie die JSON-Objekte aus der Systemkatalogsicht [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) an, die für die Speicherung der Datenbank-Digests konfiguriert wurde. 

Bei Verwendung des automatischen Digest-Speichers können Sie die Speicherorte während des gesamten Lebenszyklus der Ledgertabellen ändern.  Wenn Sie beispielsweise zunächst unveränderlichen Azure-Speicher nutzen, um Ihre Digest-Dateien zu speichern, und dann auf Azure Confidential Ledger umstellen möchten, können Sie dies tun. Diese Änderung des Speicherorts wird in [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql)gespeichert. 

Um die Ausführung der Überprüfung bei Verwendung mehrerer Digest-Speicherorte zu vereinfachen, werden mit dem folgenden Skript die Speicherorte der Digests abgerufen, und anschließend wird die Überprüfung anhand dieser Speicherorte ausgeführt.

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-that-uses-manual-digest-storage"></a>Datenbanküberprüfung mit manuellem Digest-Speicher

Wenn Sie manuellen Digest-Speicher zum Generieren und Speichern von Datenbank-Digests verwenden, wird die folgende gespeicherte Prozedur verwendet, um die Ledgerdatenbank zu überprüfen. Der JSON-Inhalt des Digests wird an die gespeicherte Prozedur angefügt. Beim Ausführen der Datenbanküberprüfung können Sie auswählen, ob alle oder nur bestimmte Tabellen der Datenbank überprüft werden sollen. 

Hier ist die Syntax für die gespeicherte Prozedur [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) angegeben:

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

Der folgende Code ist ein Beispiel für die Ausführung der gespeicherten Prozedur [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) mit einer Übergabe von zwei Digests zur Überprüfung: 

```sql
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
'
```

Die Rückgabecodes für `sp_verify_database_ledger` und `sp_verify_database_ledger_from_digest_storage` lauten `0` (Erfolg) und `1` (Fehler).

## <a name="next-steps"></a>Nächste Schritte

- [Azure SQL-Datenbank-Ledger – Übersicht](ledger-overview.md)
- [Aktualisierbare Ledger-Tabellen](ledger-updatable-ledger-tables.md)   
- [Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md)   
- [Datenbank-Ledger](ledger-database-ledger.md)
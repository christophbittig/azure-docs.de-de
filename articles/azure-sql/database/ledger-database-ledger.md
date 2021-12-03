---
title: Datenbank-Ledger
description: Dieser Artikel enthält Informationen zu Ledger-Datenbanktabellen und zugeordneten Ansichten in der Azure SQL-Datenbank.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: f5273208104853a276ae78e8debc685fa2a05c4d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062206"
---
# <a name="what-is-the-database-ledger"></a>Was ist der Datenbankledger?

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Der Datenbankledger ist Teil des Ledgerfeatures von Azure SQL-Datenbank. Der Datenbank-Ledger erfasst schrittweise den Zustand einer Datenbank, die sich im Laufe der Zeit entwickelt, während Aktualisierungen der Ledger-Tabellen stattfinden. Er verwendet logisch eine Blockchain und [Hashbaum-Datenstrukturen](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals). 

Um den Status der Datenbank zu erfassen, speichert der Datenbank-Ledger einen Eintrag für jede Transaktion. Er erfasst Metadaten zur Transaktion, z. B. den Commitzeitstempel und die Identität des Benutzers, der sie ausgeführt hat. Außerdem wird der Hashbaum-Stamm der Zeilen erfasst, die in jeder Ledgertabelle aktualisiert wurden. Diese Einträge werden dann an eine manipulationssichere Datenstruktur angefügt, um die Überprüfung der Integrität in Zukunft zu ermöglichen.

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="Diagramm, das einen Hashbaum für das Ledgerfeature zeigt":::.

Weitere Informationen dazu, wie Azure SQL-Datenbank Ledger Datenintegrität bietet, finden Sie unter [Digest-Verwaltung und Datenbanküberprüfung](ledger-digest-management-and-database-verification.md).

## <a name="where-are-database-transaction-and-block-data-stored"></a>Wo werden Datenbanktransaktions- und Blockdaten gespeichert?

Die Daten zu Transaktionen und Blöcken werden physisch als Zeilen in zwei Systemkatalogsichten gespeichert:

- [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql): Verwaltet eine Zeile mit den Informationen jeder Transaktion im Datenbankbuch. Die Informationen umfassen die ID des Blocks, zu dem diese Transaktion gehört, und die Ordnungszahl der Transaktion innerhalb des Blocks. 
- [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql): Verwaltet eine Zeile für jeden Block im Ledger, einschließlich des Hashbaum-Stamms über den Transaktionen innerhalb des Blocks und des Hashs des vorherigen Blocks, um eine Blockchain zu bilden.

Führen Sie zum Anzeigen des Datenbank-Ledgers die folgenden T-SQL-Anweisungen in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) aus.

> [!IMPORTANT]
> Zum Anzeigen des Datenbank-Ledgers ist die Berechtigung **VIEW LEDGER CONTENT** erforderlich. Weitere Informationen zu Berechtigungen im Zusammenhang mit Ledger-Tabellen finden Sie unter [Berechtigungen](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

Das folgende Beispiel zeigt eine Ledger-Tabelle aus vier Transaktionen, aus denen ein Block in der Blockchain des Datenbank-Ledgers besteht:

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="Screenshot einer Beispiel-Ledgertabelle":::

Ein Block wird alle 30 Sekunden geschlossen, oder wenn der Benutzer manuell einen Datenbank-Digest durch Ausführen der [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) gespeicherten Prozedur generiert. 

Wenn ein Block geschlossen wird, werden neue Transaktionen in einen neuen Block eingefügt. Der Blockgenerierungsprozess führt dann folgende Aktionen aus:

1. Abrufen aller Transaktionen, die zum *geschlossenen* Block gehören, sowohl aus der Warteschlange im Arbeitsspeicher als auch der Systemkatalogsicht [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql).
1. Berechnen des Merkle-Strukturstamms für diese Transaktionen und des Hashs des vorherigen Blocks.
1. Speichern des geschlossenen Blocks in der Systemkatalogsicht [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql). 

Da es sich hierbei um eine reguläre Tabellenaktualisierung handelt, wird die Dauerhaftigkeit automatisch vom System garantiert. Um die einzelne Kette von Blöcken zu verwalten, hat dieser Vorgang nur einen einzigen Thread. Er ist aber auch effizient, da er nur die Hashes über die Transaktionsinformationen berechnet und asynchron erfolgt. Dies verlangsamt nicht die Transaktion.   

## <a name="next-steps"></a>Nächste Schritte

- [Azure SQL-Datenbank-Ledger – Übersicht](ledger-overview.md) 
- [Sicherheitskatalogsichten (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)

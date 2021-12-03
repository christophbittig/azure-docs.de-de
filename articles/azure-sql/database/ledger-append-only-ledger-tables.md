---
title: Append-Only Ledger-Tabellen in Azure SQL Database
description: Dieser Artikel enthält Informationen über das Schema und die Ansichten der nur zum Anfügen vorgesehenen Ledgertabelle in Azure SQL-Datenbank.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 595b42bb924d67a5db3a21d44c670dbcd25c61d7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055793"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Append-Only Ledger-Tabellen in Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Nur zum Anfügen vorgesehenen Ledgertabellen lassen ausschließlich `INSERT`-Vorgänge in Ihren Tabellen zu. Dadurch wird gewährleistet, dass privilegierte Benutzer wie etwa Datenbankadministratoren die Daten nicht durch herkömmliche [DML](/sql/t-sql/queries/queries)-Vorgänge (Data Manipulation Language, Datenbearbeitungssprache) ändern können. Diese Art von Ledgertabellen eignen sich ideal für Systeme, die Datensätze nicht aktualisieren oder löschen, etwa SIEM-Systeme (Security Information & Event Management) oder Blockchain-Systeme, bei denen Daten aus der Blockchain in einer Datenbank repliziert werden müssen. Da es keine `UPDATE`- oder `DELETE`-Vorgänge in einer nur zum Anfügen vorgesehenen Tabelle gibt, ist keine entsprechende Verlaufstabelle wie bei [aktualisierbaren Ledgertabellen](ledger-updatable-ledger-tables.md) notwendig.

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="Diagramm: Architektur von Ledgertabellen":::

Sie können eine nur zum Anfügen vorgesehene Ledgertabelle erstellen, in dem Sie das Argument `LEDGER = ON` in Ihrer [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql)-Anweisung und die Option `APPEND_ONLY = ON` angeben.

> [!IMPORTANT]
> Sobald eine Tabelle als Ledgertabelle erstellt wurde, kann sie nicht in eine Tabelle umgewandelt werden, die nicht über Ledgerfunktionen verfügt. Infolgedessen können Angreifer Ledgerfunktionen nicht vorübergehend entfernen, Änderungen an der Tabelle vornehmen und dann die Ledgerfunktionalität erneut aktivieren.

### <a name="append-only-ledger-table-schema"></a>Append-Only Ledger-Tabellenschema

Eine aktualisierbare Ledger-Tabelle muss über die folgenden [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns)-Spalten verfügen. Diese enthalten Metadaten, die angeben, durch welche Transaktionen Änderungen an der Tabelle vorgenommen wurden, und in welcher Reihenfolge die Vorgänge ausgeführt wurden, mit denen Zeilen von der jeweiligen Transaktion aktualisiert wurden. Beim Erstellen einer nur zum Anfügen vorgesehenen Ledgertabelle werden in Ihrer Ledgertabelle Spalten vom Typ `GENERATED ALWAYS` erstellt. Diese Daten sind für forensische Zwecke nützlich, um zu verstehen, wie Daten im Laufe der Zeit eingefügt wurden.

Wenn Sie die Definitionen der `GENERATED ALWAYS`-Spalten in der [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql)-Anweisung nicht angeben, fügt das System diese automatisch unter Verwendung der folgenden Standardnamen hinzu:

| Standardspaltenname | Datentyp | Beschreibung |
|--|--|--|
| ledger_start_transaction_id | BIGINT | Die ID der Transaktion, mit der eine Zeilenversion erstellt wurde |
| ledger_start_sequence_number | BIGINT | Die Sequenznummer eines Vorgangs innerhalb einer Transaktion, mit der eine Zeilenversion erstellt wurde |

## <a name="ledger-view"></a>Ledger-Ansicht

Für jede Append-Only Ledger-Tabelle generiert das System automatisch eine Ansicht, die als Ledger-Ansicht bezeichnet wird. Die Ledger-Ansicht meldet alle eingefügte Zeilen, die in der Tabelle aufgetreten sind. Die Ledgeransicht ist in erster Linie hilfreich für [aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md) und nicht für Ledgertabellen, die nur zum Anfügen vorgesehen sind, da die nur zum Anfügen vorgesehenen Ledgertabellen nicht über `UPDATE`- oder `DELETE`-Funktionen verfügen. Die Ledger-Ansicht für Append-Only Ledger-Tabellen ist für Konsistenz zwischen aktualisierbaren und Append-Only Ledger-Tabelle verfügbar.

### <a name="ledger-view-schema"></a>Ledger-Ansichtsschema

> [!NOTE]
> Die Spaltennamen der Ledgeransicht können beim Erstellen der Tabelle mithilfe des `<ledger_view_option>`-Parameters mit der Anweisung [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) angepasst werden. Weitere Informationen finden Sie unter den [Optionen für die Ledger-Ansicht](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) und in den entsprechenden Beispielen zu [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true).

| Standardspaltenname | Datentyp | Beschreibung |
| --- | --- | --- |
| ledger_transaction_id | BIGINT | Die Transaktions-ID, mit der eine Zeilenversion erstellt oder gelöscht wurde. |
| ledger_sequence_number | BIGINT | Die Sequenznummer eines Vorgangs auf Zeilenebene innerhalb der Transaktion für die Tabelle. |
| ledger_operation_type_id | TINYINT | Enthält `0` (**EINFÜGEN**) oder `1` (**LÖSCHEN**). Das Einfügen einer Zeile in die Ledgertabelle erzeugt eine neue Zeile in der Ledgeransicht, bei der `0` in dieser Spalte enthalten ist. Das Löschen einer Zeile aus der Ledgertabelle erzeugt eine neue Zeile in der Ledgeransicht, bei der `1` in dieser Spalte enthalten ist. Das Aktualisieren einer Zeile in der Ledger-Tabelle erstellt zwei neue Zeilen in der Ledger-Ansicht. In dieser Spalte enthält eine Zeile `1` (**DELETE**) und die andere Zeile `1` (**INSERT**). Für eine nur zum Anfügen vorgesehene Ledgertabelle sollte kein DELETE-Vorgang ausgeführt werden. |
| ledger_operation_type_desc | nvarchar(128) | Enthält `INSERT` oder `DELETE`. Weitere Informationen finden Sie in der vorherigen Zeile. |

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Verwenden von Append-Only Ledger-Tabellen](ledger-how-to-append-only-ledger-tables.md)
- [Erstellen und Verwenden aktualisierbarer Ledger-Tabellen](ledger-how-to-updatable-ledger-tables.md)

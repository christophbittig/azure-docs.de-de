---
title: Einschränkungen des Azure SQL-Datenbank-Ledgers
description: Einschränkungen des Ledgerfeatures in Azure SQL-Datenbank
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 684ca40f1469b826029f1b0bcc51e33ae0d9c9a5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053688"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Einschränkungen des Azure SQL-Datenbank-Ledgers

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Dieser Artikel bietet eine Übersicht über die Einschränkungen von Ledgertabellen in Azure SQL-Datenbank.

## <a name="limitations"></a>Einschränkungen

| Funktion | Einschränkung |
| :--- | :--- |
| Deaktivieren einer [Ledgerdatenbank](ledger-database-ledger.md)   | Nach ihrer Aktivierung kann eine Ledgerdatenbank nicht mehr deaktiviert werden. |
| Maximale Anzahl von Spalten | Wenn eine [aktualisierbare Ledgertabelle](ledger-updatable-ledger-tables.md) erstellt wird, werden der Ledgertabelle vier Spalten des Typs [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) hinzugefügt. Eine [Ledgertabelle, die nur Anfügevorgänge unterstützt](ledger-append-only-ledger-tables.md), fügt der Ledgertabelle zwei Spalten hinzu. Diese neuen Spalten werden von der maximal unterstützten Anzahl von Spalten in Azure SQL-Datenbank (1.024) abgezogen. |
| Eingeschränkte Datentypen | Die Datentypen XML, SqlVariant, benutzerdefiniert und FILESTREAM werden nicht unterstützt. |
| In-Memory-Tabellen | In-Memory-Tabellen werden nicht unterstützt. |
| Spaltensätze mit geringer Dichte | Sparsespaltensätze werden nicht unterstützt. |
| Ledgerkürzung | Das Löschen älterer Daten in [Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md), oder in der Verlaufstabelle [aktualisierbarer Ledgertabellen](ledger-updatable-ledger-tables.md) wird nicht unterstützt. |
| Umwandeln bestehender Tabellen in Ledgertabellen | Bestehende Tabellen in einer Datenbank, die nicht ledgerfähig sind, können nicht in Ledgertabellen umgewandelt werden. |
|LRS-Unterstützung (lokal redundanter Speicher) für [automatisierte Digestverwaltung](ledger-digest-management-and-database-verification.md) | Die automatisierte Digestverwaltung mit Ledgertabellen mithilfe [unveränderlicher Azure Storage-Blobs](../../storage/blobs/immutable-storage-overview.md) bietet Benutzern nicht die Möglichkeit, [LRS](../../storage/common/storage-redundancy.md#locally-redundant-storage)-Konten zu verwenden.|

## <a name="remarks"></a>Bemerkungen

- Wenn eine Ledgerdatenbank erstellt wird, sind alle neuen Tabellen, die standardmäßig (ohne Angabe der `APPEND_ONLY = ON`-Klausel) in der Datenbank erstellt werden, [aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md). [Ledgertabellen, die nur Anfügevorgänge unterstützen,](ledger-append-only-ledger-tables.md) lassen sich mithilfe von [CREATE TABLE-Anweisungen (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) erstellen.
- Ledgertabellen können keine Dateitabelle (FILETABLE) sein.
- Ledgertabellen können keine Volltextindizes enthalten.
- Ledgertabellen können nicht umbenannt werden.
- Ledgertabellen können nicht in ein anderes Schema verschoben werden.
- Ledgertabellen können nur Nullwerte zulassende Spalten hinzugefügt werden, und auch nur dann, wenn WITH VALUES nicht darauf angewendet wurde.
- Spalten in Ledgertabellen können nicht gelöscht werden.
- Für Ledgertabellen sind nur deterministisch berechnete Spalten zulässig.
- Bestehende Spalten können nicht auf eine Weise geändert werden, die das Spaltenformat ändert.
  - Folgende Änderungen sind zulässig:
    - NULL-Zulässigkeit
    - Sortierung für nvarchar-/ntext-Spalten, wenn die Codepage für char-/text-Spalten nicht geändert wird
    - Die Länge von Spalten mit variabler Länge
    - Geringe Dichte
- SWITCH IN/OUT ist für Ledgertabellen nicht zulässig.
- Die Langzeitaufbewahrung von Sicherungen wird nicht für Datenbanken unterstützt, für die `LEDGER = ON` konfiguriert ist.
- Eine Versionsverwaltung des Typs `LEDGER` oder `SYSTEM_VERSIONING` kann für Ledgertabellen nicht deaktiviert werden.
- Die APIs `UPDATETEXT` und `WRITETEXT` können nicht für Ledgertabellen verwendet werden.
- Eine Transaktion kann bis zu 200 Ledgertabellen aktualisieren.
- Alle Einschränkungen temporaler Tabellen werden an aktualisierbare Ledgertabellen vererbt.
- Die Änderungsnachverfolgung ist für Ledgertabellen nicht zulässig.
- Ledgertabellen dürfen keinen nicht gruppierten Rowstore-Index haben, wenn sie über einen gruppierten Columnstore-Index verfügen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md)
- [Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md)
- [Datenbankledger](ledger-database-ledger.md)
- [Digestverwaltung und Datenbanküberprüfung](ledger-digest-management-and-database-verification.md)
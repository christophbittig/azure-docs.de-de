---
title: Azure SQL-Datenbank – Überwachen von Ereignissen mit ledgerfähigen Tabellen
description: Übersicht über die Überwachungsfunktionen von Azure SQL-Datenbank-Ledger
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 18e3c5ef5d9cb0316118e38f2ffb8dccc8c06fd7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061868"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Azure SQL-Datenbank – Überwachen von Ereignissen mit ledgerfähigen Tabellen

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Wenn Sie forensische Aktivitäten mit ledgerfähigen Tabellen ausführen, werden Daten in der Ledgeransicht und im Datenbankledger erfasst. Auch andere Aktions-IDs werden den SQL-Überwachungsprotokollen hinzugefügt. In den folgenden Tabellen werden diese neuen Überwachungsprotokollierungsereignisse beschrieben. Die Bedingungen, die die Ereignisse auslösen, folgen auf jede Tabelle.

## <a name="enable-ledger"></a>Ledger aktivieren

| Spalte | Wert |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Bedingungen, die das Ereignis auslösen**: Wenn Sie eine neue Ledgertabelle erstellen oder eine herkömmliche Tabelle in eine Ledgertabelle konvertieren.

## <a name="alter-ledger"></a>Ledger ändern

| Spalte | Wert |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Bedingungen, die das Ereignis auslösen**: Wenn Sie eine Ledgertabelle löschen oder umbenennen, eine Ledgertabelle in eine normale Tabelle umwandeln und eine Spalte in einer Ledgertabelle hinzufügen, löschen oder umbenennen.


## <a name="generate-ledger-digest"></a>Ledgerdigest generieren

| Spalte | Wert |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**Bedingung, die das Ereignis auslöst**: Wenn Sie einen Ledgerdigest generieren.

## <a name="verify-ledger"></a>Ledger überprüfen

| Spalte | Wert |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Bedingung, die das Ereignis auslöst**: Wenn Sie einen Ledgerdigest überprüfen.

## <a name="ledger-operation-group"></a>Ledger-Vorgangsgruppe

| Spalte | Wert |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Bedingung, die das Ereignis auslöst**: –

| Spalte | Wert |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Bedingung, die das Ereignis auslöst**: – 

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen von Azure SQL-Datenbank und Azure Synapse Analytics](auditing-overview.md)
- [Azure SQL-Datenbank-Ledger – Übersicht](ledger-overview.md)
- [Schnellstart: Erstellen einer Datenbank in Azure SQL-Datenbank mit aktiviertem Ledger](ledger-create-a-single-database-with-ledger-enabled.md)

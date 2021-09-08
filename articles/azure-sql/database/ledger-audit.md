---
title: Azure SQL-Datenbank – Überwachen von Ereignissen mit ledgerfähigen Tabellen
description: Übersicht über die Überwachungsfunktionen von Azure SQL-Datenbank-Ledger
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: d032b311809449d35a017fdd053fb97447b8479c
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666737"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Azure SQL-Datenbank – Überwachen von Ereignissen mit ledgerfähigen Tabellen

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger befindet sich derzeit in der öffentlichen Vorschauphase und ist in den Regionen West „Europa, Westen“, „Brasilien, Süden“ und „USA, Westen-Mitte“ verfügbar.

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

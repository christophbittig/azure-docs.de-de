---
title: Zugreifen auf die in Azure Confidential Ledger gespeicherten Hashes
description: Erfahren Sie, wie Sie mit dem Azure SQL-Datenbank-Ledger auf die in Azure Confidential Ledger (ACL) gespeicherten Digests zugreifen.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: f011494412b6f9cf1ac186f171b62fe82d02aaaa
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061737"
---
# <a name="access-the-digests-stored-in-confidential-ledger"></a>Zugreifen auf die in Azure Confidential Ledger gespeicherten Digests

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

In diesem Artikel erfahren Sie, wie Sie auf einen in [Azure Confidential Ledger (ACL)](../../confidential-ledger/index.yml) gespeicherten [Azure SQL-Datenbank-Ledgerdigest](ledger-overview.md) zugreifen, um End-to-End-Sicherheits- und -Integritätsgarantien zu erhalten. In diesem Artikel wird erläutert, wie Sie auf die gespeicherten Informationen zugreifen und ihre Integrität überprüfen.

## <a name="prerequisites"></a>Voraussetzungen

- Python 2.7, 3.5.3 oder höher
- Azure SQL-Datenbank mit aktiviertem Ledger. Falls Sie noch keine Datenbank in Azure SQL-Datenbank erstellt haben, finden Sie unter [Schnellstart: Erstellen einer Datenbank in Azure SQL-Datenbank mit aktiviertem Ledger](ledger-create-a-single-database-with-ledger-enabled.md) weitere Informationen.
- [Azure Confidential Ledger-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)
- Eine ausgeführte Instanz von [Azure Confidential Ledger](../../confidential-ledger/index.yml)

## <a name="how-does-the-integration-work"></a>Wie funktioniert die Integration?

Azure SQL Server berechnet die Digests der [Ledgerdatenbanken](ledger-overview.md#ledger-database) in regelmäßigen Abständen und speichert sie in Azure Confidential Ledger. Sie können jederzeit die Integrität der Daten überprüfen. Laden Sie die Digests aus Confidential Ledger herunter und vergleichen Sie sie mit den in einem SQL-Datenbank-Ledger gespeicherten Digests. Die folgenden Schritte erläutern den Prozess.

## <a name="1-find-the-digest-location"></a>1. Suchen des Digestspeicherorts

> [!NOTE]
> Wenn zum Speichern des Digests mehrere Azure Confidential Ledger-Instanzen verwendet wurden, gibt die Abfrage mehrere Zeilen zurück. Wiederholen Sie die Schritte 2 bis 6 für jede Zeile, um die Digests aus allen Instanzen von Confidential Ledger herunterzuladen.

Führen Sie in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) die folgende Abfrage aus. Die Ausgabe zeigt den Endpunkt der Confidential Ledger-Instanz, in der die Digests gespeichert sind.

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%'
```

## <a name="2-determine-the-subledgerid"></a>2. Bestimmen der Unterledger-ID

Der entscheidende Wert in der Abfrageausgabe befindet sich in der Spalte „path“. Sie besteht aus zwei Teilen, nämlich `host name` und `subledgerid`. In der URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` ist beispielsweise der `host name` `https://contoso-ledger.confidential-ledger.azure.com` und die `subledgerid` lautet `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`. Sie verwenden diese Werte in Schritt 4, um die Digests herunterzuladen.

## <a name="3-obtain-an-azure-ad-token"></a>3. Abrufen eines Azure AD-Tokens

Die Confidential Ledger-API akzeptiert ein Azure Active Directory-Bearertoken (Azure AD) als Aufruferidentität. Diese Identität benötigt während der Bereitstellung über Azure Resource Manager Zugriff auf Confidential Ledger. Wenn Sie in Azure SQL-Datenbank einen Ledger aktivieren, erhalten Sie automatisch Administratorzugriff auf Confidential Ledger. Um ein Token zu erhalten, müssen Sie sich mithilfe der [Azure CLI](/cli/azure/install-azure-cli) mit dem Konto authentifizieren, das auch im Azure-Portal verwendet wurde. Nach der Authentifizierung können Sie mit [AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) ein Bearertoken abrufen und die Confidential Ledger-API aufrufen.

Melden Sie sich bei Azure AD mit einer Identität an, die Zugriff auf Confidential Ledger hat.

```azure-cli
az login
```

Rufen Sie das Bearertoken ab.

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-confidential-ledger"></a>4. Herunterladen der Digests aus Confidential Ledger

Das folgende Python-Skript lädt die Digests aus Confidential Ledger herunter. In diesem Skript wird die [Confidential Ledger-Clientbibliothek für Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger) verwendet.

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. Herunterladen der Digests aus SQL Server

> [!NOTE]
> Dieser Schritt ist eine Möglichkeit, um sicherzustellen, dass die im Azure SQL-Datenbank-Ledger gespeicherten Hashes im Lauf der Zeit nicht geändert wurden. Informationen zur umfassenden Überwachung der Integrität des Azure SQL-Datenbank-Ledgers finden Sie unter [Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen](ledger-verify-database.md).

Führen Sie in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) die folgende Abfrage aus. Die Abfrage gibt die Digests der Blöcke aus Genesis zurück.

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. Vergleich

Vergleichen Sie den aus Azure Confidential Ledger abgerufenen Digest mit dem von Ihrer Datenbank in Azure SQL-Datenbank zurückgegebenen Digest. Verwenden Sie dabei `block_id` als Schlüssel. Der Digest von `block_id` = `1` ist beispielsweise der Wert der Spalte `previous_block_hash` in der Zeile `block_id`= `2`. Entsprechend ist dies für `block_id` = `3` der Wert der Spalte `previous_block_id` in der Zeile `block_id` = `4`. Falls der Hashwert nicht übereinstimmt, ist dies ein Anzeichen für potenzielle Datenmanipulation.

Wenn Datenmanipulation vermutet wird, finden Sie unter [Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen](ledger-verify-database.md) weitere Informationen dazu, wie Sie eine umfassende Überprüfung des Azure SQL-Datenbank-Ledgers durchführen.

## <a name="next-steps"></a>Nächste Schritte

- [Azure SQL-Datenbank-Ledger – Übersicht](ledger-overview.md)
- [Datenbankledger](ledger-database-ledger.md)
- [Digestverwaltung und Datenbanküberprüfung](ledger-digest-management-and-database-verification.md)
- [Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md)
- [Aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md)
- [Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen](ledger-verify-database.md)

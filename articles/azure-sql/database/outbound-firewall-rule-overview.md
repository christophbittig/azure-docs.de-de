---
title: Firewallregeln für ausgehenden Datenverkehr (Vorschau)
description: Übersicht über das Feature "Firewallregeln für ausgehenden Datenverkehr".
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
ms.reviewer: vanto
ms.date: 11/10/2021
ms.openlocfilehash: d69536d8ed72dbd3793dd2481897d4534dbc2fdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350371"
---
# <a name="outbound-firewall-rules-for-azure-sql-database-and-azure-synapse-analytics-preview"></a>Firewallregeln für ausgehenden Datenverkehr fürAzure SQL-Datenbank und  Azure Synapse Analytics (Vorschau)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)] 

Firewallregeln für ausgehenden Datenverkehr beschränken den Netzwerkdatenverkehr vom logischen Azure SQL-Server auf eine vom Kunden definierte Liste von Azure Storage-Konten und logischen Azure SQL Servern. Jeder Versuch, auf Speicherkonten oder SQL-Datenbanken zuzugreifen, die nicht in dieser Liste enthalten sind, wird verweigert. Die folgenden Funktionen von Azure SQL Database unterstützen diese Option:

- [Überwachung](auditing-overview.md)
- [Sicherheitsrisikobewertung](sql-vulnerability-assessment.md)
- [I/E Service](database-import-export-azure-services-off.md)
- OPENROWSET
- Masseneinfügen
- [Elastische Abfrage](elastic-query-overview.md)

> [!IMPORTANT]
> Dieser Artikel gilt für Azure SQL-Datenbank und den [dedizierten SQL-Pool (vormals SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) in Azure Synapse Analytics. Diese Einstellungen gelten für alle SQL-Datenbank-Datenbanken und die Datenbanken des dedizierten SQL-Pools (früher „SQL DW“), die dem Server zugeordnet sind. Der Einfachheit halber wird der Begriff „Datenbank“ verwendet, wenn auf Datenbanken sowohl in Azure SQL-Datenbank als auch in Azure Synapse Analytics verwiesen wird. Ebenso bezieht sich der Begriff „Server“ auf den [logischen SQL-Server](logical-servers.md), der Azure SQL-Datenbank und den dedizierten SQL-Pool (vormals SQL DW) in Azure Synapse Analytics hostet. Dieser Artikel gilt *nicht* für Azure SQL Managed Instance oder dedizierte SQL-Pools in Azure Synapse Analytics-Arbeitsbereichen.

## <a name="set-outbound-firewall-rules-in-the-azure-portal"></a>Festlegen von Firewallregeln für ausgehenden Datenverkehr im Azure-Portal

1. Navigieren Sie zum **Abschnitt Ausgehende Netzwerke** auf dem Blatt **Firewalls und virtuelle Netzwerke** für Ihre Azure SQL-Datenbank und wählen Sie **Einschränkungen für ausgehenden Netzwerkbetrieb Konfigurieren** aus.

   ![Screenshot des Abschnitts "Ausgehende Netzwerke"][1]  

   Dadurch wird auf der rechten Seite das folgende Blatt geöffnet:

   ![Screenshot des Blatts "Ausgehende Netzwerke" ohne Auswahl][2]  

1. Aktivieren Sie das Kontrollkästchen **Ausgehende Netzwerke einschränken**, und fügen Sie dann den FQDN für die Storage-Konten (oder SQL-Datenbanken) mithilfe der Schaltfläche **Domäne hinzufügen** hinzu.

   ![Screenshot des Blatts "Ausgehende Netzwerke" mit dem Hinzufügen eines FQDN][3]  

1. Wenn Sie fertig sind, sollte ein Bildschirm ähnlich dem folgenden angezeigt werden. Wählen Sie **OK** aus, um die Einstellungen zu speichern.

   ![Screenshot des Blatts "Ausgehende Netzwerke", nachdem FQDNs hinzugefügt wurden][4]  

## <a name="set-outbound-firewall-rules-using-powershell"></a>Erstellen von Firewallregeln mithilfe von PowerShell

> [!IMPORTANT]
> Azure SQL-Datenbank unterstützt das PowerShell Azure Resource Manager-Modul weiterhin, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Das folgende Skript erfordert das [Azure PowerShell-Modul](/powershell/azure/install-az-ps).

Das folgende PowerShell-Skript zeigt, wie Sie die Einstellung für ausgehende Netzwerke ändern (mithilfe der Eigenschaft **RestrictOutboundNetworkAccess**):

```powershell
# Get current settings for Outbound Networking
(Get-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>).RestrictOutboundNetworkAccess

# Update setting for Outbound Networking
$SecureString = ConvertTo-SecureString "<ServerAdminPassword>" -AsPlainText -Force

Set-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -SqlAdministratorPassword $SecureString  -RestrictOutboundNetworkAccess "Enabled"
```

Verwenden Sie diese PowerShell-Cmdlets, um Firewallregeln für ausgehenden Datenverkehr zu konfigurieren

```powershell
# List all Outbound Firewall Rules
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>

# Add an Outbound Firewall Rule
New-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN testOBFR1

# List a specific Outbound Firewall Rule
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>

#Delete an Outbound Firewall Rule
Remove-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>
```

## <a name="set-outbound-firewall-rules-using-the-azure-cli"></a>Festlegen von Firewallregeln für ausgehenden Datenverkehr mithilfe Azure CLI

> [!IMPORTANT]
> Für alle Skripts in diesem Abschnitt ist die [Azure CLI](/cli/azure/install-azure-cli) erforderlich.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in einer Bash-Shell

Das folgende PowerShell-Skript zeigt, wie Sie die Einstellung für ausgehende Netzwerke (mithilfe der Eigenschaft **RestrictOutboundNetworkAccess**) in einem Bash-shell-Befehl ändern:

```azurecli-interactive
# Get current setting for Outbound Networking 
az sql server show -n sql-server-name -g sql-server-group --query "RestrictOutboundNetworkAccess"

# Update setting for Outbound Networking
az sql server update -n sql-server-name -g sql-server-group --set RestrictOutboundNetworkAccess="Enabled"
```

Verwenden Sie diese CLI-Befehle, um Firewallregeln für ausgehenden Datenverkehr zu konfigurieren

```azurecli-interactive
# List a server's outbound firewall rules.
az sql server outbound-firewall-rule list -g sql-server-group -s sql-server-name

# Create a new outbound firewall rule
az sql server outbound-firewall-rule create -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Show the details for an outbound firewall rule.
az sql server outbound-firewall-rule show -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Delete the outbound firewall rule.
az sql server outbound-firewall-rule delete -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Sicherheit von Azure SQL-Datenbank finden Sie unter [Sichern der SQL-Datenbank](security-overview.md).
- Eine Übersicht über die Azure SQL-Datenbank-Konnektivität finden Sie unter [Verbindungsarchitektur von Azure SQL-Datenbank](connectivity-architecture.md).

<!--Image references-->
[1]: media/outbound-firewall-rules/Step1.jpg
[2]: media/outbound-firewall-rules/Step2.jpg
[3]: media/outbound-firewall-rules/Step3.jpg
[4]: media/outbound-firewall-rules/Step4.jpg

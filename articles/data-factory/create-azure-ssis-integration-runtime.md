---
title: Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie zur Bereitstellung und Ausführung von SSIS-Paketen in Azure die Azure-SSIS-Integration Runtime in Azure Data Factory erstellen.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70e745d18699c6a1acb707528ec38641a1fe7a02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842889"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden die Schritte zur Bereitstellung einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory beschrieben. Die Azure-SSIS IR unterstützt Folgendes:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten eine verwaltete Azure SQL-Instanz verwendet wird (Paketbereitstellungsmodell)

Nach der Bereitstellung einer Azure-SSIS IR können Sie die vertrauten Tools nutzen, um Ihre Pakete in Azure bereitzustellen und auszuführen. Diese Tools sind bereits Azure-fähig und enthalten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) sowie Befehlszeilen-Hilfsprogramme wie [dtutil](/sql/integration-services/dtutil-utility) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Im Tutorial zum [Bereitstellen der Azure-SSIS Integration Runtime](./tutorial-deploy-ssis-packages-azure.md) wird veranschaulicht, wie Sie eine Azure-SSIS IR mit dem Azure-Portal oder der Data Factory-App erstellen. Außerdem wird im Tutorial veranschaulicht, wie Sie optional einen Azure SQL-Datenbank-Server oder eine verwaltete Instanz zum Hosten der SSISDB verwenden. Dieser Artikel baut auf dem Tutorial auf, und es wird beschrieben, wie Sie die folgenden optionalen Aufgaben durchführen:

- Verwenden Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit einem privaten Endpunkt zum Hosten der SSISDB. Als Voraussetzung müssen Sie Berechtigungen und Einstellungen für das virtuelle Netzwerk konfigurieren, damit Ihre Azure-SSIS IR einem virtuellen Netzwerk beitreten kann.

- Verwenden Sie die Azure AD-Authentifizierung (Azure Active Directory) mit der angegebenen system- oder benutzerseitig zugewiesenen verwalteten Identität für Ihre Data Factory, um eine Verbindung mit dem Azure SQL-Datenbank-Server oder der verwalteten Instanz herzustellen. Als Voraussetzung müssen Sie die angegebene system- oder benutzerseitig zugewiesene verwaltete Identität für Ihre Data Factory als Datenbankbenutzer hinzufügen, der eine SSISDB-Instanz erstellen kann.

- Verknüpfen Sie Ihre Azure-SSIS IR mit einem virtuellen Netzwerk, oder konfigurieren Sie eine selbstgehostete Integration Runtime als Proxy für Ihre Azure-SSIS IR, um auf lokale Daten zuzugreifen.

Diese Artikel zeigen, wie man eine Azure-SSIS IR mit Hilfe des [Azure Portals](create-azure-ssis-integration-runtime-portal.md), [der Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md) und einer [Azure Resource Manager-Vorlage](create-azure-ssis-integration-runtime-resource-manager-template.md) bereitstellt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement**. Falls Sie noch nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

- **Azure SQL-Datenbank-Server oder SQL Managed Instance (optional)** . Wenn Sie noch nicht über einen Datenbankserver oder eine verwaltete Instanz verfügen, erstellen Sie einen bzw. eine im Azure-Portal, bevor Sie beginnen. Mit Data Factory wird wiederum eine SSISDB-Instanz auf diesem Datenbankserver erstellt. 

  Wir empfehlen, dass Sie den Datenbankserver oder die verwaltete Instanz in derselben Azure-Region wie die Integration Runtime erstellen. Diese Konfiguration ermöglicht es der Integration Runtime, Ausführungsprotokolle in SSISDB zu schreiben, ohne Azure-Regionen zu überschreiten.

  Beachten Sie Folgendes:

  - Die SSISDB-Instanz kann in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder in einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Wählen zwischen SQL-Datenbank und SQL Managed Instance zum Hosten von SSISDB finden Sie in diesem Artikel im Abschnitt [Vergleich zwischen SQL-Datenbank und SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance). 
  
    Wenn Sie einen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete SQL-Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz mit einem virtuellen Netzwerk verknüpfen. Weitere Informationen finden Sie im Artikel zum [Beitritt einer Azure-SSIS Integration Runtime zu einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Vergewissern Sie sich, dass die Einstellung **Zugriff auf Azure-Dienste zulassen** für den Datenbankserver aktiviert ist. Diese Einstellung gilt nicht, wenn Sie einen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete SQL-Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden. Weitere Informationen finden Sie unter [Schützen der Azure SQL-Datenbank](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Informationen zum Aktivieren dieser Einstellung mithilfe von PowerShell finden Sie unter [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Fügen Sie der Client-IP-Adressenliste in den Firewalleinstellungen für den Datenbankserver die IP-Adresse des Clientcomputers oder einen IP-Adressbereich hinzu, der die IP-Adresse des Clientcomputers enthält. Weitere Informationen finden Sie unter [Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank](../azure-sql/database/firewall-configure.md).

  - Sie können eine Verbindung mit dem Datenbankserver herstellen. Hierzu verwenden Sie die SQL-Authentifizierung mit Ihren Serveradministrator-Anmeldeinformationen oder die Azure AD-Authentifizierung mit dem angegebenen System/der angegebenen verwalteten Identität für Ihre Data Factory. Im letzteren Fall müssen Sie das angegebenen System/die angegebene verwaltete Identität Ihrer Data Factory-Instanz einer Azure AD-Gruppe mit den Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md).

  - Vergewissern Sie sich, dass Ihr Datenbankserver noch nicht über eine SSISDB-Instanz verfügt. Für die Bereitstellung einer Azure-SSIS IR wird die Verwendung einer vorhandenen SSISDB-Instanz nicht unterstützt.

- **Virtuelles Azure Resource Manager-Netzwerk (optional)** . Sie benötigen ein virtuelles Azure Resource Manager-Netzwerk, wenn mindestens eine der folgenden Bedingungen zutrifft:

  - Sie hosten die SSISDB auf einem Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder einer verwalteten Instanz mit einem privaten Endpunkt.

  - Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen, ohne eine selbstgehostete IR zu konfigurieren.

- **Azure PowerShell (optional)** . Befolgen Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps), wenn Sie ein PowerShell-Skript zur Bereitstellung von Azure-SSIS IR ausführen möchten.

### <a name="regional-support"></a>Regionsunterstützung

Eine Liste mit den Azure-Regionen, in denen Data Factory und eine Azure-SSIS Integration Runtime verfügbar sind, finden Sie unter [Data Factory und SSIS IR: Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz

In der folgenden Tabelle werden bestimmte Features eines Azure SQL-Datenbank-Servers und der verwalteten SQL-Instanz in Bezug auf die Azure-SSIS IR-Instanz verglichen:

| Funktion | SQL-Datenbank| Verwaltete SQL-Instanz |
|---------|--------------|------------------|
| **Zeitplanung** | Der SQL Server-Agent ist nicht verfügbar.<br/><br/>Weitere Informationen finden Sie unter [Planen eines Pakets als Teil einer Azure Data Factory-Pipeline](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Der Agent für die verwaltete Instanz ist verfügbar. |
| **Authentifizierung** | Sie können eine SSISDB-Instanz mit einem Benutzer für eine eigenständige Datenbank erstellen, der eine beliebige Azure AD-Gruppe mit der verwalteten Identität Ihrer Data Factory als Member in der Rolle **db_owner** darstellt.<br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen einer SSISDB auf einem Azure SQL-Datenbank-Server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database). | Sie können eine SSISDB-Instanz mit einem Benutzer für eine eigenständige Datenbank erstellen, der die verwaltete Identität Ihrer Data Factory darstellt. <br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen einer SSISDB in einer verwalteten Azure SQL-Datenbank-Instanz](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance). |
| **Dienstebene** | Wenn Sie eine Azure-SSIS IR mit Ihrem Azure SQL-Datenbank-Server erstellen, können Sie die Dienstebene für die SSISDB auswählen. Es gibt mehrere Dienstebenen. | Wenn Sie eine Azure-SSIS IR mit Ihrer verwalteten Instanz erstellen, können Sie die Dienstebene für die SSISDB nicht auswählen. Alle Datenbanken in Ihrer verwalteten Instanz nutzen dieselbe Ressource, die dieser Instanz zugeordnet ist, gemeinsam. |
| **Virtuelles Netzwerk** | Ihre Azure-SSIS IR kann zu einem virtuellen Azure Resource Manager-Netzwerk hinzugefügt werden, wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten verwenden. | Ihre Azure-SSIS IR kann zu einem virtuellen Azure Resource Manager-Netzwerk hinzugefügt werden, wenn Sie eine verwaltete Instanz mit einem privaten Endpunkt verwenden. Das virtuelle Netzwerk ist erforderlich, wenn Sie keinen öffentlichen Endpunkt für Ihre verwaltete Instanz aktivieren.<br/><br/>Wenn Sie Ihre Azure-SSIS IR in dasselbe virtuelle Netzwerk wie Ihre verwaltete Instanz einbinden, sollten Sie sicherstellen, dass sich Ihre Azure-SSIS IR in einem anderen Subnetz als Ihre verwaltete Instanz befindet. Wenn Sie Ihre Azure-SSIS IR in ein anderes virtuelles Netzwerk als Ihre verwaltete Instanz einbinden, empfehlen wir Ihnen, entweder ein Peering virtueller Netzwerke oder eine Verbindung zwischen den virtuellen Netzwerken zu verwenden. Informationen hierzu finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/connect-application-instance.md). |
| **Verteilte Transaktionen** | Dieses Feature wird über elastische Transaktionen unterstützt. MS DTC-Transaktionen (Microsoft Distributed Transaction Coordinator) werden nicht unterstützt. Wenn Ihre SSIS-Pakete MSDTC verwenden, um verteilte Transaktionen zu koordinieren, sollten Sie eine Migration zu elastischen Transaktionen für Azure SQL-Datenbank erwägen. Weitere Informationen finden Sie unter [Verteilte Transaktionen über Clouddatenbanken](../azure-sql/database/elastic-transactions-overview.md). | Wird nicht unterstützt. |
| | | |


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe des Azure-Portals bereitstellen](create-azure-ssis-integration-runtime-portal.md).
- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe von Azure PowerShell bereitstellen](create-azure-ssis-integration-runtime-powershell.md).
- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe einer Azure Resource Manager-Vorlage bereitstellen](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Stellen Sie Ihre SSIS-Pakete in Azure Data Factory bereit, und führen Sie diese aus](create-azure-ssis-integration-runtime-deploy-packages.md).

Sehen Sie sich auch andere Themen zur Azure-SSIS IR in dieser Dokumentation an:

- [Azure SSIS-Integration Runtime:](concepts-integration-runtime.md#azure-ssis-integration-runtime) Dieser Artikel enthält allgemeine Informationen zu Integration Runtimes, einschließlich der Azure-SSIS IR.
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

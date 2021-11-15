---
title: Azure Active Directory-Dienstprinzipal mit Azure SQL
description: AD-Anwendungen (Dienstprinzipale) unterstützen die Azure AD-Benutzererstellung in Azure SQL-Datenbank und Azure SQL Managed Instance.
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2021
ms.openlocfilehash: 3d72b151e73b9adc39f71cd89362027ae2cd9e0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256233"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory-Dienstprinzipal mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Active Directory (Azure AD) unterstützt die Benutzererstellung in Azure SQL-Datenbank (SQL DB) im Auftrag von Azure AD-Anwendungen (Dienstprinzipale).

> [!NOTE]
> Diese Funktionalität wird für SQL Managed Instance bereits unterstützt.

## <a name="service-principal-azure-ad-applications-support"></a>Unterstützung für Dienstprinzipal (Azure AD-Anwendungen)

Dieser Artikel gilt für Anwendungen, die in Azure AD integriert und Teil der Azure AD-Registrierung sind. Diese Anwendungen benötigen oft Authentifizierungs- und Autorisierungszugriff auf Azure SQL zur Ausführung verschiedener Aufgaben. Dieses Feature ermöglicht Dienstprinzipalen das Erstellen von Azure AD-Benutzern in SQL-Datenbank. Es gab eine Einschränkung, die das Erstellen von Azure AD-Objekten im Auftrag von Azure AD Anwendungen verhinderte. Diese Einschränkung wurde aufgehoben.

Beim Registrieren einer Azure AD-Anwendung über das Azure-Portal oder mithilfe eines PowerShell-Befehls werden im Azure AD-Mandanten zwei Objekte erstellt:

- ein Anwendungsobjekt
- ein Dienstprinzipalobjekt

Weitere Informationen zu Azure AD-Anwendungen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) und [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Die folgenden Azure AD-Objekte werden von SQL-Datenbank und SQL Managed Instance unterstützt:

- Azure AD-Benutzer (verwaltet, Partner und Gast)
- Azure AD-Gruppen (verwaltet und Partner)
- Azure AD-Anwendungen 

Der T-SQL-Befehl `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` im Auftrag einer Azure AD-Anwendung wird jetzt bei SQL-Datenbank unterstützt.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funktionalität der Azure AD-Benutzererstellung mithilfe von Dienstprinzipalen

Die Unterstützung dieser Funktionalität ist hilfreich in Automatisierungsprozessen für Azure AD-Anwendungen, in denen Azure AD-Objekte in SQL-Datenbank ohne menschlichen Eingriff erstellt und verwaltet werden. Dienstprinzipale können ein Azure AD-Administrator für den logischen SQL-Server, als Teil einer Gruppe oder einzelner Benutzer, sein. Die Anwendung kann die Erstellung von Azure AD-Objekten in SQL-Datenbank automatisieren, wenn sie als Systemadministrator ausgeführt wird und keine zusätzlichen SQL-Berechtigungen erfordert. Dies ermöglicht eine vollständige Automatisierung der Erstellung eines Datenbankbenutzers. Dieses Feature unterstützt auch vom System zugewiesene verwaltete Identitäten sowie vom Benutzer zugewiesene verwaltete Identitäten in Azure AD, die im Auftrag von Dienstprinzipalen als Benutzer in SQL-Datenbank erstellt werden können. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Aktivieren von Dienstprinzipalen zum Erstellen von Azure AD-Benutzern

Zum Aktivieren einer Azure AD-Objekterstellung in SQL-Datenbank im Auftrag einer Azure AD-Anwendung sind die folgenden Einstellungen erforderlich:

1. Weisen Sie die Serveridentität zu. Die zugewiesene Serveridentität stellt die MSI (Managed Service Identity, verwaltete Dienstidentität) dar. Aktuell unterstützt die Serveridentität für Azure SQL benutzerseitig zugewiesene verwaltete Identitäten (UMIs) nicht.
    - Führen Sie bei einem neuen logischen Azure SQL-Server den folgenden PowerShell-Befehl aus:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Weitere Informationen finden Sie im Befehl [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) oder im Befehl [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) für SQL Managed Instance.

    - Führen Sie bei vorhandenen logischen Azure SQL-Servern den folgenden Befehl aus:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Weitere Informationen finden Sie im Befehl [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) oder im Befehl [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) für SQL Managed Instance.

    - Führen Sie den Befehl „Get-azsqlserver“ aus, um zu überprüfen, ob die Serveridentität dem Server zugewiesen wurde.

    > [!NOTE]
    > Die Serveridentität kann auch mit der REST-API und CLI-Befehlen zugewiesen werden. Weitere Informationen finden Sie unter [az sql server create](/cli/azure/sql/server#az_sql_server_create), [az sql server update](/cli/azure/sql/server#az_sql_server_update) und [Server – REST-API](/rest/api/sql/2020-08-01-preview/servers).

2. Erteilen Sie die Azure AD-Berechtigung [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers) für die Serveridentität, die erstellt oder dem Server zugewiesen wurde.
    - Führen Sie zum Erteilen dieser Berechtigung die Schritte in der Beschreibung für SQL Managed Instance aus, die im folgenden Artikel zu finden ist: [Bereitstellen eines Azure AD-Administrators (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Der Azure AD-Benutzer, der diese Berechtigung erteilt, muss der Azure AD-Rolle **Globaler Administrator** oder **Administrator für privilegierte Rollen** angehören.

> [!IMPORTANT]
> Die Schritte 1 und 2 müssen in der vorstehenden Reihenfolge ausgeführt werden. Erstellen Sie zuerst die Serveridentität, oder weisen Sie sie zu, und erteilen Sie dann die Berechtigung [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers). Wenn Sie einen dieser Schritte oder beide auslassen, tritt während der Erstellung eines Azure AD-Objekts in Azure SQL im Auftrag einer Azure AD-Anwendung ein Ausführungsfehler auf.
>
> Sie können die Rolle **Verzeichnisleseberechtigte** einer Gruppe in Azure AD zuweisen. Die Gruppenbesitzer können dann die verwaltete Identität als Mitglied dieser Gruppe hinzufügen. Dadurch wird die Notwendigkeit eines **Globalen Administrators** oder **Administrators für privilegierte Rollen** umgangen, um die Rolle **Verzeichnisleseberechtigter** zuzuweisen. Weitere Informationen zu diesem Feature finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations"></a>Problembehandlung und Einschränkungen

- Beim Erstellen von Azure AD-Objekten in Azure SQL im Auftrag einer Azure AD-Anwendung ohne Aktivierung der Serveridentität und Erteilen der Berechtigung **Verzeichnisleseberechtigte** können die folgenden Fehler auftreten. Der folgende Beispielfehler betrifft eine PowerShell-Befehlsausführung zum Erstellen eines Azure SQL-Datenbankbenutzers `myapp` im Artikel [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved. Error message:
    'Server identity is not configured. Please follow the steps in "Assign an Azure AD identity to your server and add
    Directory Reader permission to your identity" (https://aka.ms/sqlaadsetup)'"`
      - Führen Sie beim vorstehenden Fehler die Schritte zum [Zuweisen einer Identität zum logischen Azure SQL-Server](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) und [Zuweisen der Berechtigung „Verzeichnisleseberechtigte“ zur Identität des logischen SQL-Servers](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity) aus.
    - Das Festlegen des Dienstprinzipals (Azure AD-Anwendung) als Azure AD-Administrator für SQL-Datenbank wird über das Azure-Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse), [REST-API](/rest/api/sql/2020-08-01-preview/servers) und [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)-Befehle unterstützt.
- Die Verwendung einer Azure AD-Anwendung bei einem Dienstprinzipal aus einem anderen Azure AD-Mandanten schlägt beim Zugriff auf SQL-Datenbank oder SQL Managed Instance fehl, die in einem anderen Mandanten erstellt wurden. Ein dieser Anwendung zugewiesener Dienstprinzipal muss von demselben Mandanten wie der logische SQL-Server oder Managed Instance sein.
- Das [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)-Modul oder höher ist erforderlich, wenn Sie PowerShell verwenden, um eine einzelne Azure AD Anwendung als Azure AD-Administrator für Azure SQL einzurichten. Stellen Sie sicher, dass Sie ein Upgrade auf das neueste Modul ausgeführt haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md)

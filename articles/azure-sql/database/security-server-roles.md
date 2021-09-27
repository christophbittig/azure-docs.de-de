---
title: Serverrollen
titleSuffix: Azure SQL Database
description: Dieser Artikel enthält eine Übersicht über Serverrollen für den logischen Server von Azure SQL-Datenbank.
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: 64c012373187059792174618920b797b2d081aa2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537200"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>Azure SQL-Datenbank-Serverrollen für die Berechtigungsverwaltung

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bei Azure SQL-Datenbank ist der Server ein logisches Konzept, und auf Serverebene können keine Berechtigungen gewährt werden. Zur Vereinfachung der Berechtigungsverwaltung verfügt Azure SQL-Datenbank über eine Gruppe fester Rollen auf Serverebene, die Sie zum Verwalten der Berechtigungen auf einem [logischen Server](logical-servers.md) verwenden können. Bei Rollen handelt es sich um Sicherheitsprinzipale, unter denen Anmeldenamen gruppiert sind.

> [!NOTE]
> In diesem Artikel können *Rollen* wie *Gruppen* im Windows-Betriebssystem angesehen werden.

Für diese speziellen festen Rollen auf Serverebene werden das Präfix **##MS_** und das Suffix **##** genutzt, um eine Unterscheidung gegenüber anderen regulären Prinzipalen zu ermöglichen, die von Benutzern erstellt werden.

Serverberechtigungen sind hierarchisch strukturiert, wie dies auch bei einer lokalen SQL Server-Instanz der Fall ist. Die Berechtigungen, über die diese Rollen auf Serverebene verfügen, können auf Datenbankberechtigungen übertragen werden. Damit die Übertragung der Berechtigungen an die Datenbank effektiv durchgeführt werden kann, muss für einen Anmeldenamen ein Benutzerkonto in der Datenbank vorhanden sein.

Beispielsweise enthält die Rolle **##MS_ServerStateReader##** auf Serverebene die Berechtigung **VIEW SERVER STATE**. Wenn für einen Anmeldenamen, der Mitglied dieser Rolle ist, ein Benutzerkonto in den Datenbanken *master* und *WideWorldImporters* vorhanden ist, verfügt dieser Benutzer in diesen beiden Datenbanken über die Berechtigung **VIEW DATABASE STATE**. 

> [!NOTE]
> Da in Benutzerdatenbanken jede Berechtigung verweigert werden kann, wird die serverweite Gewährung per Rollenmitgliedschaft quasi außer Kraft gesetzt. In der Systemdatenbank *master* können Berechtigungen aber nicht gewährt oder verweigert werden.

Azure SQL-Datenbank verfügt derzeit über drei feste Serverrollen. Die Berechtigungen, die den festen Serverrollen gewährt werden, können nicht geändert werden, und diese Rollen können keine anderen festen Rollen als Mitglieder zugeordnet werden. Sie können SQL-Anmeldenamen auf Serverebene den Rollen auf Serverebene als Mitglieder hinzufügen.

> [!IMPORTANT]
> Jedes Mitglied einer festen Serverrolle kann der gleichen Rolle andere Anmeldenamen hinzufügen.

Weitere Informationen zu Azure SQL-Datenbank-Anmeldenamen und -Benutzern finden Sie unter [Autorisieren des Datenbankzugriffs für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](logins-create-manage.md).
  
## <a name="built-in-server-level-roles"></a>Integrierte Rollen auf Serverebene

In der folgenden Tabelle werden die festen Rollen auf Serverebene und deren Möglichkeiten angezeigt.  
  
|Integrierte Rolle auf Serverebene|Beschreibung|  
|------------------------------|-----------------|  
|**##MS_DefinitionReader##**|Mitglieder der festen Serverrolle **##MS_DefinitionReader##** können alle Katalogsichten lesen, die von **VIEW ANY DEFINITION** abgedeckt werden, bzw. von **VIEW DEFINITION** für alle Datenbanken, für die das Mitglied dieser Rolle über ein Benutzerkonto verfügt.|  
|**##MS_ServerStateReader##**|Mitglieder der festen Serverrolle **##MS_ServerStateReader##** können alle dynamischen Verwaltungssichten (Dynamic Management Views, DMVs) und Funktionen lesen, die von **VIEW SERVER STATE** abgedeckt werden, bzw. von **VIEW DATABASE STATE** für alle Datenbanken, für die das Mitglied dieser Rolle über ein Benutzerkonto verfügt.|
|**##MS_ServerStateManager##**|Mitglieder der festen Serverrolle **##MS_ServerStateManager##** verfügen über die gleichen Berechtigungen, die für die Rolle **##MS_ServerStateReader##** gelten. Darüber hinaus ist auch die Berechtigung **ALTER SERVER STATE** vorhanden, die den Zugriff auf mehrere Verwaltungsvorgänge ermöglicht, z. B. `DBCC FREEPROCCACHE`, `DBCC FREESYSTEMCACHE ('ALL')`, `DBCC SQLPERF()`. |  


## <a name="permissions-of-fixed-server-roles"></a>Berechtigungen von festen Serverrollen

Jeder integrierten Rolle auf Serverebene sind bestimmte Berechtigungen zugewiesen. In der folgenden Tabelle sind die Berechtigungen angegeben, die den Rollen auf Serverebene zugewiesen sind. Darüber hinaus sind die Berechtigungen auf Datenbankebene angegeben, die geerbt werden, wenn in der Datenbank ein Benutzerkonto vorhanden ist.
  
|Feste Rolle auf Serverebene|Berechtigungen auf Serverebene|Berechtigungen auf Datenbankebene (wenn ein Datenbankbenutzer vorhanden ist)  
|-------------|----------|-----------------|  
|**##MS_DefinitionReader##**|VIEW ANY DATABASE, VIEW ANY DEFINITION, VIEW ANY SECURITY DEFINITION|VIEW DEFINITION, VIEW SECURITY DEFINITION|  
|**##MS_ServerStateReader##**|VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|  
|**##MS_ServerStateManager##**|ALTER SERVER STATE, VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|   
  
  
## <a name="working-with-server-level-roles"></a>Verwenden von Rollen auf Serverebene

In der folgenden Tabelle sind die Systemsichten und Funktionen aufgeführt, die Sie beim Verwenden von Rollen auf Serverebene in Azure SQL-Datenbank nutzen können.  
  
|Funktion|type|Beschreibung|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|Metadaten|Gibt an, ob ein SQL-Anmeldename Mitglied der angegebenen Rolle auf Serverebene ist.|  
|[sys.server_role_members &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|Metadaten|Gibt eine Zeile für jedes Mitglied jeder Rolle auf Serverebene zurück.|
|[sys.sql_logins &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|Metadaten|Gibt eine Zeile für jeden SQL-Anmeldenamen zurück.|
|[ALTER SERVER ROLE &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|Get-Help|Ändert die Mitgliedschaft einer Serverrolle.| 

## <a name="examples"></a><a name="_examples"></a> Beispiele

Anhand der Beispiele in diesem Abschnitt wird veranschaulicht, wie Sie Rollen auf Serverebene in Azure SQL-Datenbank verwenden.  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A. Hinzufügen eines SQL-Anmeldenamens zu einer Rolle auf Serverebene

Im folgenden Beispiel wird der SQL-Anmeldename „Jiao“ der Rolle „##MS_ServerStateReader##“ auf Serverebene hinzugefügt. Diese Anweisung muss in der virtuellen Masterdatenbank ausgeführt werden.
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. Auflisten aller Prinzipale (SQL-Authentifizierung), die Mitglieder einer Rolle auf Serverebene sind

Mit der folgenden Anweisung werden alle Mitglieder aller festen Rollen auf Serverebene zurückgegeben, indem die Katalogsichten `sys.server_role_members` und `sys.sql_logins` verwendet werden. Diese Anweisung muss in der virtuellen Masterdatenbank ausgeführt werden.
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. Vollständiges Beispiel: Hinzufügen eines Anmeldenamens zu einer Rolle auf Serverebene, Abrufen von Metadaten für die Rollenmitgliedschaft und Berechtigungen und Ausführen einer Testabfrage

#### <a name="part-1-preparing-role-membership-and-user-account"></a>Teil 1: Vorbereiten der Rollenmitgliedschaft und des Benutzerkontos

Führen Sie diesen Befehl aus der virtuellen Masterdatenbank aus.

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

Hier ist das Resultset.
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

Führen Sie diesen Befehl aus einer Benutzerdatenbank aus.

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>Teil 2: Testen der Rollenmitgliedschaft

Melden Sie sich mit dem Anmeldenamen `Jiao` an, und stellen Sie eine Verbindung mit der im Beispiel verwendeten Benutzerdatenbank her.

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>Einschränkungen von Rollen auf Serverebene

- Es kann bis zu fünf Minuten dauern, bis Rollenzuweisungen wirksam werden. Auch für vorhandene Sitzungen werden Änderungen an Serverrollenzuweisungen erst wirksam, nachdem die Verbindung getrennt und wiederhergestellt wurde. Der Grund ist die verteilte Architektur zwischen der *Masterdatenbank* und den anderen Datenbanken auf demselben logischen Server.
  - Partielle Problemumgehung: Ein Serveradministrator oder ein Azure AD-Administrator kann in den Benutzerdatenbanken, auf die für den Anmeldenamen Zugriff besteht, `DBCC FLUSHAUTHCACHE` ausführen, um die Wartezeit zu verringern und sicherzustellen, dass Serverrollenzuweisungen in einer Datenbank aktuell sind. Auch gerade angemeldete Benutzer müssen nach dem Ausführen von `DBCC FLUSHAUTHCACHE` eine neue Verbindung herstellen, damit die Mitgliedschaftsänderungen wirksam werden.

- `IS_SRVROLEMEMBER()` wird in der *Masterdatenbank* nicht unterstützt.


## <a name="see-also"></a>Weitere Informationen

- [Rollen auf Datenbankebene](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [Sicherheitskatalogsichten &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [Sicherheitsfunktionen &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [Berechtigungen &#40;Datenbank-Engine&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE (Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)

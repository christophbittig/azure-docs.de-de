---
title: Neuigkeiten
titleSuffix: Azure SQL Database
description: Hier erfahren Sie mehr über die neuen Features und Dokumentationsverbesserungen für Azure SQL-Datenbank.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: e57adde4df10a942baff7b7032706a991d46050c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556171"
---
# <a name="whats-new-in-azure-sql-database"></a>Neuigkeiten in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel werden die Dokumentationsänderungen zusammengefasst, die mit neuen Funktionen und Verbesserungen in den neuesten Releases von [Azure SQL-Datenbank](https://azure.microsoft.com/products/azure-sql/database/) in Zusammenhang stehen. Weitere Informationen zu Azure SQL-Datenbank finden Sie in der [Übersicht](sql-database-paas-overview.md). 

Informationen zu Azure SQL Managed Instance finden Sie in den [Neuigkeiten](../managed-instance/doc-changes-updates-release-notes-whats-new.md).

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)



## <a name="preview"></a>Vorschau 

In der folgenden Tabelle sind die Features von Azure SQL-Datenbank aufgeführt, die sich derzeit in der Vorschau befinden: 

| Funktion | Details |
| ---| --- |
| [Erfassung geänderter Daten](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Mit Change Data Capture (CDC) können Sie alle Änderungen nachverfolgen, die an einer Datenbank vorgenommen werden. Obwohl dieses Feature bereits seit einiger Zeit für SQL Server verfügbar ist, befindet sich die Verwendung mit Azure SQL-Datenbank derzeit in der Vorschauphase. |
| [Elastische Aufträge](elastic-jobs-overview.md) | Mit dem Feature für elastische Aufträge wird der SQL Server-Agent ersetzt, wenn Azure SQL-Datenbank als PaaS-Angebot genutzt wird.  |
| [Elastische Abfragen](elastic-query-overview.md) | Das Feature für elastische Abfragen ermöglicht datenbankübergreifende Abfragen in Azure SQL-Datenbank. |
| [Elastische Transaktionen](elastic-transactions-overview.md) | Elastische Transaktionen ermöglichen die Ausführung von Transaktionen, die auf Cloud-Datenbanken in der Azure SQL-Datenbank verteilt sind. |
| [Ledger](ledger-overview.md) | Mit dem Feature „Ledger“ von Azure SQL-Datenbank können Sie Dritten gegenüber (z. B. Prüfern oder anderen Geschäftspartnern) kryptografisch nachweisen, dass Ihre Daten nicht manipuliert wurden. | 
| [Wartungsfenster](maintenance-window.md)| Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan für Azure SQL-Datenbank konfigurieren. |
| [Abfrage-Editor im Azure-Portal](connect-query-portal.md) | Mit dem Abfrage-Editor im Portal können Sie Abfragen für Azure SQL-Datenbank direkt über das [Azure-Portal](https://portal.azure.com) ausführen.|
| [Abfragespeicherhinweise](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | Verwenden Sie Abfragehinweise, um die Abfrageausführung über die OPTION-Klausel zu optimieren. |
| [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md)|Azure SQL-Analyse ist eine erweiterte Cloudüberwachungslösung zum bedarfsorientierten und abonnementübergreifenden Überwachen der Leistung Ihrer gesamten Azure SQL-Datenbankinstanzen in einer zentralen Ansicht. Azure SQL-Analyse erfasst und visualisiert wichtige Leistungsmetriken und umfasst integrierte Logik für die Problembehandlung in Bezug auf die Leistung.|
| [Erkenntnisse zu SQL](../../azure-monitor/insights/sql-insights-overview.md) |  Bei SQL Insights handelt es sich um eine umfassende Lösung zur Überwachung jeglichem Produkt der Azure SQ- Familie. SQL Insights verwendet dynamische Managementansichten, um die Daten offenzulegen, die Sie zur Überwachung des Zustands, zur Diagnose von Problemen und zur Leistungsoptimierung benötigen.|
| [Zonenredundante Konfiguration für die Ebene „Universell“](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | Das Feature für eine zonenredundante Konfiguration nutzt [Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md#availability-zones), um Datenbanken über mehrere physische Standorte innerhalb einer Azure-Region hinweg zu replizieren. Durch die Auswahl der [Zonenredundanz](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) können Sie dafür sorgen, dass Ihre Datenbanken vom Typ „Universell“ und Pools für elastische Datenbanken bei einer viel größeren Anzahl von Fehlern (u. a. schwerwiegende Ausfälle des Rechenzentrums) resilient sind, ohne dass dazu die Anwendungslogik geändert werden muss. Das Feature ist derzeit nur für die Ebene „Universell“ verfügbar. | 
|||

## <a name="general-availability-ga"></a>Allgemeine Verfügbarkeit (General Availability, GA)

In der folgenden Tabelle sind die Features von Azure SQL-Datenbank aufgeführt, die innerhalb der letzten 12 Monate von der Vorschauversion auf die allgemeine Verfügbarkeit (General Availability, GA) umgestellt wurden: 

| Feature | GA-Monat | Details |
| ---| --- |--- |
| [Azure Active Directory – nur Authentifizierung](authentication-azure-ad-only-authentication.md) | November 2021 | Sie können Azure SQL-Datenbank so konfigurieren, dass die Authentifizierung nur über Azure Active Directory möglich ist. | 
| [AAD-Dienstprinzipal](authentication-aad-service-principal.md) |  September 2021 | Azure Active Directory (Azure AD) unterstützt die Benutzererstellung in Azure SQL-Datenbank im Auftrag von Azure AD-Anwendungen (Dienstprinzipale).| 
| [Überwachen von Verwaltungsvorgängen](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  März 2021 | Mit den Azure SQL-Überwachungsfunktionen können Sie Vorgänge überwachen, die von Microsoft-Supporttechnikern durchgeführt werden, wenn diese während einer Supportanfrage auf Ihre SQL-Ressourcen zugreifen müssen. Dies sorgt für mehr Transparenz unter Ihren Mitarbeitern. | 
|||| 


## <a name="documentation-changes"></a>Änderungen an der Dokumentation

Hier erfahren Sie mehr über wichtige Änderungen an der Dokumentation zu Azure SQL-Datenbank.

### <a name="november-2021"></a>November 2021

| Änderungen | Details |
| --- | --- |
| **Reine Azure AD-Authentifizierung** | Das Beschränken der Authentifizierung für Ihre Azure SQL-Datenbank auf Azure Active Directory-Benutzer ist jetzt allgemein verfügbar. Weitere Informationen finden Sie unter [Authentifizierung nur über Azure AD](../database/authentication-azure-ad-only-authentication.md). | 
| | | 


### <a name="october-2021"></a>Oktober 2021

| Änderungen | Details |
| --- | --- |
|**Unterteilung der Informationen zu neuen Funktionen** | Der zuvor kombinierte Artikel zu den **Neuigkeiten** wurde nach Produkt unterteilt: [Neuigkeiten in Azure SQL-Datenbank](doc-changes-updates-release-notes-whats-new.md) und [Neuigkeiten in SQL Managed Instance](../managed-instance/doc-changes-updates-release-notes-whats-new.md). Dadurch können Sie leichter erkennen, welche Features sich derzeit in der Vorschau befinden bzw. allgemein verfügbar sind und welche wichtigen Änderungen an der Dokumentation vorgenommen wurden. Darüber hinaus wurden der Inhalt über [bekannte Probleme in SQL Managed Instance](../managed-instance/doc-changes-updates-known-issues.md) auf eine eigene Seite verschoben.  | 

### <a name="september-2021"></a>September 2021

| Änderungen | Details |
| --- | --- |
| **Unterstützung von Wartungsfenstern für Verfügbarkeitszonen** | Sie können das Feature [Wartungsfenster](maintenance-window.md) jetzt verwenden, wenn Ihre Instanz von Azure SQL-Datenbank in einer Verfügbarkeitszone bereitgestellt wird. Diese Funktion steht derzeit als Vorschau zur Verfügung.  | 
|||


### <a name="july-2021"></a>Juli 2021

| Änderungen | Details |
| --- | --- |
| **Reine Azure AD-Authentifizierung** | Es ist jetzt möglich, die Authentifizierung gegenüber Azure SQL-Datenbank auf Azure Active Directory-Benutzer*innen zu beschränken. Diese Funktion steht derzeit als Vorschau zur Verfügung. Weitere Informationen finden Sie unter [Reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md). | 
|||

### <a name="june-2021"></a>Juni 2021

| Änderungen | Details |
| --- | --- |
| **Abfragespeicherhinweise** | Sie können jetzt Abfragehinweise verwenden, um die Abfrageausführung über die OPTION-Klausel zu optimieren. Diese Funktion steht derzeit als Vorschau zur Verfügung. Weitere Informationen finden Sie unter [Abfragespeicherhinweise](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true). | 
|||

### <a name="may-2021"></a>Mai 2021

| Änderungen | Details |
| --- | --- |
| **Erfassung geänderter Daten** | Die Verwendung von Change Data Capture (CDC) mit Azure SQL-Datenbank befindet sich jetzt in der Vorschauphase. Weitere Informationen finden Sie unter [Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server). | 
| **SQL-Datenbank-Ledger** | Der Ledger von SQL-Datenbank befindet sich in der Vorschauphase. Mit diesem Feature können Sie Dritten gegenüber (z. B. Prüfern oder anderen Geschäftspartnern) kryptografisch nachweisen, dass Ihre Daten nicht manipuliert wurden. Weitere Informationen finden Sie unter [Ledger](ledger-overview.md). | 
|||

### <a name="march-2021"></a>März 2021

| Änderungen | Details |
| --- | --- |
 | **Wartungsfenster** | Mithilfe des Features „Wartungsfenster“ können Sie einen Wartungszeitplan für Azure SQL-Datenbank konfigurieren. Dieses Feature befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Wartungsfenster](maintenance-window.md).|
| **Erkenntnisse zu SQL** | Bei SQL Insights handelt es sich um eine umfassende Lösung zur Überwachung jeglichem Produkt der Azure SQ- Familie. SQL Insights verwendet dynamische Managementansichten, um die Daten offenzulegen, die Sie zur Überwachung des Zustands, zur Diagnose von Problemen und zur Leistungsoptimierung benötigen. Weitere Informationen finden Sie unter [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

## <a name="contribute-to-content"></a>Mitwirkung am Inhalt

Informationen zur Mitwirkung an der Azure SQL-Dokumentation finden Sie im [Leitfaden für Dokumentationsmitwirkende](/contribute/).

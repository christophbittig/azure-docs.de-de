---
title: Datenermittlung und -klassifizierung
description: Datenermittlung und -klassifizierung für Azure SQL-Datenbank, eine verwaltete Azure SQL-Instanz und Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/24/2021
tags: azure-synapse
ms.openlocfilehash: 4fd6360d1d549cd5c184dd5a1f3105d238ab9319
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335801"
---
# <a name="data-discovery--classification"></a>Datenermittlung und -klassifizierung
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Datenermittlung und -klassifizierung ist in Azure SQL-Datenbank, verwaltete Azure SQL-Instanzen und Azure Synapse Analytics integriert. Sie bietet grundlegende Funktionen zum Ermitteln, Klassifizieren und Bezeichnen vertraulicher Daten in Ihren Datenbanken und zum Erstellen von Berichten zu diesen Daten.

Zu den vertraulichsten Daten gehören Geschäfts-, Finanz-, Gesundheits- und personenbezogene Informationen. Sie kann für Folgendes als Infrastruktur gelten:

- Unterstützung bei der Einhaltung von Standards für den Datenschutz und von gesetzlichen Bestimmungen
- Verschiedene Sicherheitsszenarien, z. B. Überwachung vertraulicher Daten
- Steuern des Zugriffs auf und Härten der Sicherheit von Datenbanken mit vertraulichen Daten

> [!NOTE]
> Informationen zu lokalen SQL Server-Instanzen finden Sie unter [SQL-Datenermittlung und -klassifizierung](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Was ist die Datenermittlung und -klassifizierung?

Die Datenermittlung und -klassifizierung unterstützt derzeit die folgenden Funktionen:

- **Ermittlung und Empfehlungen:** Das Klassifizierungsmodul scannt Ihre Datenbank und identifiziert Spalten, die unter Umständen vertrauliche Daten enthalten. Es bietet dann eine einfache Möglichkeit, die Ergebnisse zu überprüfen und die empfohlenen Klassifizierungen über das Azure-Portal anzuwenden.

- **Bezeichnung:** Sie können Klassifizierungsbezeichnungen nach der Vertraulichkeit der Daten dauerhaft auf Spalten anwenden, indem Sie neue Metadatenattribute nutzen, die der SQL Server-Datenbank-Engine hinzugefügt wurden. Diese Metadaten können dann für Überwachungsszenarien auf Grundlage der Vertraulichkeit genutzt werden.

- **Vertraulichkeit von Abfrageergebnissen:** Die Vertraulichkeit von Abfrageergebnissen wird zu Überwachungszwecken in Echtzeit berechnet.

- **Transparenz:** Der Klassifizierungsstatus der Datenbank kann in einem detaillierten Dashboard im Azure-Portal angezeigt werden. Darüber hinaus können Sie einen Bericht (im Excel-Format) herunterladen, der für Compliance- und Überwachungszwecke und andere Anforderungen verwendet werden kann.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Spalten

In diesem Abschnitt werden die Schritte für folgende Maßnahmen beschrieben:

- Ermitteln, Klassifizieren und Bezeichnen von Spalten mit vertraulichen Daten in Ihrer Datenbank
- Anzeigen des aktuellen Klassifizierungsstatus Ihrer Datenbank und Exportieren von Berichten

Die Klassifizierung umfasst zwei Metadatenattribute:

- **Bezeichnungen:** Die wichtigsten Klassifizierungsattribute zum Definieren der Vertraulichkeitsstufe der in der Spalte gespeicherten Daten.  
- **Informationstypen:** Attribute mit detaillierteren Informationen zum Typ der in der Spalte gespeicherten Daten.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definieren und Anpassen der Klassifizierungstaxonomie

Datenermittlung und -klassifizierung verfügt über einen integrierten Satz von Vertraulichkeitsbezeichnungen und einen integrierten Satz von Informationstypen und Ermittlungslogik. Sie können diese Taxonomie anpassen und eine spezielle Gruppe und Rangfolge von Klassifizierungskonstrukten für Ihre Umgebung definieren.

Die Definition und Anpassung Ihrer Klassifizierungstaxonomie erfolgt an zentraler Stelle für die ganze Azure-Organisation. Dieser Speicherort befindet sich im Rahmen Ihrer Sicherheitsrichtlinie in [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md). Nur Benutzer mit Administratorrechten für die Stammverwaltungsgruppe der Organisation können diese Aufgabe ausführen.

Im Rahmen der Richtlinienverwaltung können Sie benutzerdefinierte Bezeichnungen definieren, deren Rangfolge festlegen und sie einer ausgewählten Gruppe von Informationstypen zuordnen. Sie können auch eigene benutzerdefinierte Informationstypen hinzufügen und diese mit Zeichenfolgenmustern konfigurieren. Die Muster werden der Ermittlungslogik hinzugefügt, um diese Datentypen in Ihren Datenbanken zu identifizieren.

Weitere Informationen finden Sie unter [Anpassen der SQL Information Protection-Richtlinie in Microsoft Defender für Cloud (Vorschau)](../../security-center/security-center-info-protection-policy.md).

Nachdem die organisationsweite Richtlinie definiert wurde, können Sie die Klassifizierung einzelner Datenbanken mithilfe Ihrer benutzerdefinierten Richtlinie fortsetzen.

### <a name="classify-your-database"></a>Klassifizieren Ihrer Datenbank

> [!NOTE]
> Im folgenden Beispiel wird Azure SQL-Datenbank verwendet, aber Sie sollten das entsprechende Produkt auswählen, für das Sie die Datenermittlung und -klassifizierung konfigurieren möchten.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Navigieren Sie im Bereich von Azure SQL-Datenbank unter der Überschrift **Sicherheit** zu **Datenermittlung und -klassifizierung**. Die Registerkarte „Übersicht“ enthält eine Zusammenfassung des aktuellen Klassifizierungsstatus der Datenbank. Die Zusammenfassung enthält eine ausführliche Liste aller klassifizierten Spalten, die Sie auch filtern können, um nur bestimmte Schemateile, Informationstypen und Bezeichnungen anzuzeigen. Wenn Sie noch keine Spalten klassifiziert haben, [fahren Sie mit Schritt 4 fort](#step-4).

    ![Übersicht](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Um einen Bericht im Excel-Format herunterzuladen, wählen Sie oben im Fenster im Menü die Option **Exportieren** aus.

1. <a id="step-4"></a>Um mit der Klassifizierung Ihrer Daten zu beginnen, wählen Sie die Registerkarte **Klassifizierung** auf der Seite **Datenermittlung und -klassifizierung** aus.

    Die Klassifizierungs-Engine scannt Ihre Datenbank nach Spalten, die potenziell vertrauliche Daten enthalten, und erstellt eine Liste der empfohlenen Spaltenklassifizierungen.

1. Anzeigen und Anwenden von Klassifizierungsempfehlungen:

   - Um die Liste der empfohlenen Spaltenklassifizierungen anzuzeigen, wählen Sie unten im Bereich den Empfehlungsbereich aus.

   - Zum Akzeptieren einer Empfehlung für eine bestimmte Spalte aktivieren Sie das Kontrollkästchen in der linken Spalte der entsprechenden Zeile. Sie können auch alle Empfehlungen als akzeptiert markieren, indem Sie das Kontrollkästchen ganz links im Tabellenkopf der Empfehlungen aktivieren.

   - Wählen Sie zum Anwenden der ausgewählten Empfehlungen die Option **Ausgewählte Empfehlungen annehmen** aus.

   ![Empfehlungen für die Klassifizierung](./media/data-discovery-and-classification-overview/recommendation.png)

1. Alternativ oder zusätzlich zur empfehlungsbasierten Klassifizierung können Sie Spalten auch manuell klassifizieren:

   1. Wählen Sie im oberen Menü des Bereichs **Klassifizierung hinzufügen** aus.

   1. Wählen Sie im daraufhin geöffneten Kontextfenster das Schema, die Tabelle und dann die Spalte, die Sie klassifizieren möchten, sowie den Informationstyp und die Vertraulichkeitsbezeichnung aus.

   1. Wählen Sie dann am unteren Rand des Kontextfensters die Option **Klassifizierung hinzufügen** aus.

   ![Manuelles Hinzufügen einer Klassifizierung](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Wählen Sie auf der Seite **Klassifizierung** die Option **Speichern** aus, um Ihre Klassifizierung abzuschließen und die Datenbankspalten dauerhaft mit den neuen Klassifizierungsmetadaten (Tags) zu bezeichnen.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Überwachen des Zugriffs auf vertrauliche Daten

Ein wichtiger Aspekt der Klassifizierung ist die Möglichkeit, den Zugriff auf vertrauliche Daten zu überwachen. Die [Azure SQL-Überwachung](../../azure-sql/database/auditing-overview.md) wurde erweitert, um ein neues Feld mit dem Namen `data_sensitivity_information` in das Überwachungsprotokoll zu integrieren. Dieses Feld protokolliert die Vertraulichkeitsklassifizierungen (Bezeichnungen) der Daten, die von einer Abfrage zurückgegeben wurden. Hier sehen Sie ein Beispiel:

[![Überwachungsprotokoll](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png#lightbox)

Dies sind die Aktivitäten, die tatsächlich mit vertraulichen Informationen geprüft werden können:
- ALTER TABLE ... DROP COLUMN
- BULK INSERT
- Delete
- INSERT
- MERGE
- UPDATE
- UPDATETEXT
- WRITETEXT
- DROP TABLE
- BACKUP
- DBCC CloneDatabase
- SELECT INTO
- INSERT INTO EXEC
- TRUNCATE TABLE
- DBCC SHOW_STATISTICS
- sys.dm_db_stats_histogram

Verwenden Sie [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql), um Informationen aus einer Überwachungsdatei zurückzugeben, die in einem Azure Storage-Konto gespeichert ist.

## <a name="permissions"></a><a id="permissions"></a>Berechtigungen

Diese integrierten Rollen können die Datenklassifizierung einer Datenbank lesen:

- Besitzer
- Leser
- Mitwirkender
- SQL-Sicherheits-Manager
- Benutzerzugriffsadministrator

Dies sind die erforderlichen Aktionen, um die Datenklassifizierung einer Datenbank zu lesen:

- Microsoft.Sql/servers/databases/currentSensitivityLabels/*
- Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*
- Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*

Diese integrierten Rollen können die Datenklassifizierung einer Datenbank ändern:

- Besitzer
- Mitwirkender
- SQL-Sicherheits-Manager

Dies ist die erforderliche Aktion, um die Datenklassifizierung einer Datenbank zu ändern:

- Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*

Hier erfahren Sie mehr über rollenbasierte Berechtigungen unter [Azure RBAC](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a>Verwalten von Klassifizierungen

Sie können zum Verwalten von Klassifizierungen T-SQL, eine REST-API oder PowerShell verwenden.

### <a name="use-t-sql"></a>Verwenden von T-SQL

Mit T-SQL können Sie Spaltenklassifizierungen hinzufügen oder entfernen sowie alle Klassifizierungen für die gesamte Datenbank abrufen.

> [!NOTE]
> Bei der Verwendung von T-SQL zur Verwaltung von Bezeichnungen erfolgt keine Überprüfung, ob die einer Spalte hinzugefügten Bezeichnungen in der Datenschutzrichtlinie der Organisation (die in den Empfehlungen im Portal angezeigt werden) enthalten sind. Aus diesem Grund müssen Sie dies überprüfen.

Weitere Informationen zur Verwendung von T-SQL für Klassifizierungen finden Sie in den folgenden Ressourcen:

- Zum Hinzufügen oder Aktualisieren von Klassifizierungen einer oder mehrere Spalten: [VERTRAULICHKEITSKLASSIFIZIERUNG HINZUFÜGEN](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Zum Entfernen der Klassifizierung aus einer oder mehreren Spalten: [VERTRAULICHKEITSKLASSIFIZIERUNG LÖSCHEN](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Zum Anzeigen aller Klassifizierungen in der Datenbank: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Verwenden von PowerShell-Cmdlets
Verwenden Sie PowerShell, um Klassifizierungen und Empfehlungen für Azure SQL-Datenbank und verwaltete Azure SQL-Instanzen zu verwalten.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-Cmdlets für Azure SQL-Datenbank

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>PowerShell-Cmdlets für verwaltete Azure SQL-Instanzen

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Verwenden der REST-API

Sie können die REST-API verwenden, um Klassifizierungen und Empfehlungen programmgesteuert zu verwalten. Die veröffentlichte REST-API unterstützt die folgenden Vorgänge:

- [Erstellen oder aktualisieren:](/rest/api/sql/sensitivitylabels/createorupdate) Erstellt oder aktualisiert die Vertraulichkeitsbezeichnung der angegebenen Spalte.
- [Löschen:](/rest/api/sql/sensitivitylabels/delete) Löscht die Vertraulichkeitsbezeichnung der angegebenen Spalte.
- [Deaktivieren von Empfehlungen:](/rest/api/sql/sensitivitylabels/disablerecommendation) Deaktiviert Vertraulichkeitsempfehlungen für die angegebene Spalte.
- [Aktivieren von Empfehlungen:](/rest/api/sql/sensitivitylabels/enablerecommendation) Aktiviert Vertraulichkeitsempfehlungen für die angegebene Spalte. (Standardmäßig sind Empfehlungen für alle Spalten aktiviert.)
- [Get](/rest/api/sql/sensitivitylabels/get) (Abrufen): Ruft die Vertraulichkeitsbezeichnung der angegebenen Spalte ab.
- [Aktuelle nach Datenbank auflisten:](/rest/api/sql/sensitivitylabels/listcurrentbydatabase) Ruft die aktuellen Vertraulichkeitsbezeichnungen der angegebenen Datenbank ab.
- [Empfohlene nach Datenbank auflisten:](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) Ruft die empfohlenen Vertraulichkeitsbezeichnungen für die angegebene Datenbank ab.

## <a name="retrieve-classifications-metadata-using-sql-drivers"></a>Abrufen von Klassifizierungsmetadaten mit SQL-Treibern

Sie können die folgenden SQL-Treiber verwenden, um Klassifizierungsmetadaten abzurufen:

- [ODBC-Treiber](/sql/connect/odbc/data-classification)
- [OLE DB-Treiber](/sql/connect/oledb/features/using-data-classification)
- [JDBC-Treiber](/sql/connect/jdbc/data-discovery-classification-sample)
- [Microsoft-Treiber für PHP für SQL Server](/sql/connect/php/release-notes-php-sql-driver)

## <a name="faq---advanced-classification-capabilities"></a>FAQ: Erweiterte Klassifizierungsfunktionen

**Frage:** Ersetzt [Azure Purview](../../purview/overview.md) SQL-Datenermittlung und -klassifizierung, oder wird SQL-Datenermittlung und -klassifizierung bald eingestellt?
**Antwort:** Wir unterstützen weiterhin SQL-Datenerkennung und -klassifizierung und empfehlen, [Azure Purview](../../purview/overview.md) zu verwenden, damit Sie über umfangreichere Funktionen für erweiterte Klassifizierungsmöglichkeiten und Data Governance verfügen. Wenn wir uns entscheiden, einen Service, eine Funktion, eine API oder eine SKU einzustellen, erhalten Sie eine Vorankündigung, die einen Migrations- oder Übergangspfad enthält. Weitere Informationen zu Microsoft-Lebenszyklusrichtlinien finden Sie hier.

## <a name="next-steps"></a>Nächste Schritte

- Erwägen Sie, die [Azure SQL-Überwachung](../../azure-sql/database/auditing-overview.md) für die Überwachung und Überprüfung des Zugriffs auf Ihre klassifizierten vertraulichen Daten zu konfigurieren.
- Eine Präsentation mit Informationen zur Datenermittlung und -klassifizierung finden Sie unter [Ermitteln, Klassifizieren, Bezeichnen und Schützen von SQL-Daten | Data Exposed](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Informationen zum Klassifizieren von Azure SQL-Datenbanken und Azure Synapse Analytics mit Azure Purview-Bezeichnungen mithilfe von T-SQL-Befehlen finden Sie unter [Klassifizieren der Azure SQL-Daten mithilfe von Azure Purview-Bezeichnungen](../../sql-database/scripts/sql-database-import-purview-labels.md).

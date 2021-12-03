---
title: Richtlinie zum Schutz von SQL-Informationen in Microsoft Defender for Cloud
description: Erfahren Sie, wie Sie Richtlinien zum Schutz von Informationen in Microsoft Defender für Cloud anpassen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: defender-for-cloud
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 2300d038cdecd6b994775290e7ae1c3afa874aa3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557301"
---
# <a name="sql-information-protection-policy-in-microsoft-defender-for-cloud"></a>Richtlinie zum Schutz von SQL-Informationen in Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Der [Mechanismus zur Datenermittlung und -klassifizierung](../azure-sql/database/data-discovery-and-classification-overview.md) von SQL Information Protection bietet erweiterte Funktionen für die Ermittlung, Klassifizierung, Bezeichnung und Berichterstellung vertraulicher Daten in Ihren Datenbanken. Dieser Mechanismus ist in [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) integriert.

Der Klassifizierungsmechanismus basiert auf den folgenden beiden Elementen:

- **Bezeichnungen**: Die wichtigsten Klassifizierungsattribute zum Definieren der *Vertraulichkeitsstufe* der in der Spalte gespeicherten Daten. 
- **Informationstypen**: Bieten zusätzliche Granularität für den *Typ* der in der Spalte gespeicherten Daten.

Die Information Protection-Richtlinienoptionen in Defender für Cloud stellen einen vordefinierten Satz von Bezeichnungen und Informationstypen zur Verfügung, die als Standardwerte für die Klassifizierungs-Engine dienen. Sie können die Richtlinie wie unten beschrieben an die Anforderungen Ihrer Organisation anpassen.

:::image type="content" source="./media/sql-information-protection-policy/sql-information-protection-policy-page.png" alt-text="Seite mit Ihrer SQL Information Protection-Richtlinie":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Wie kann ich auf die SQL Information Protection-Richtlinie zugreifen?

Es gibt drei Möglichkeiten für den Zugriff auf die Information Protection-Richtlinie:

- **(Empfohlen)** : Über die Seite **Umgebungseinstellungen** von Defender für Cloud
- Über die Sicherheitsempfehlung „Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden“
- Über die Azure SQL-DB-Seite zur Datenermittlung

Jede dieser Möglichkeiten wird in der folgenden Abbildung auf der entsprechenden Registerkarte angezeigt.



### <a name="from-defender-for-clouds-settings"></a>[**Von Defender für die Cloud Einstellungen**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-defender-for-clouds-environment-settings-page"></a>Zugreifen auf die Richtlinie über die Seite „Umgebungseinstellungen“ von Defender für Cloud <a name="sqlip-tenant"></a>

Wählen Sie auf der Seite **Umgebungseinstellungen** von Defender für Cloud die Einstellung **SQL Information Protection** aus.

> [!NOTE]
> Diese Option wird nur Benutzern mit Berechtigungen auf Mandantenebene angezeigt. [Erteilen von mandantenweiten Berechtigungen an sich selbst](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).

:::image type="content" source="./media/sql-information-protection-policy/environment-settings-link-to-information-protection.png" alt-text="Zugreifen auf die SQL Information Protection-Richtlinie über die Seite „Umgebungseinstellungen“ von Microsoft Defender für Cloud.":::



### <a name="from-defender-for-clouds-recommendation"></a>[**Von Defender für die Empfehlung der Cloud**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-defender-for-cloud-recommendation"></a>Zugreifen auf die Richtlinie über die Defender für Cloud-Empfehlung <a name="sqlip-db"></a>

Verwenden Sie die Security Center-Empfehlung „Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden“, um die Seite zur Ermittlung und Klassifizierung von Daten für Ihre Datenbank anzuzeigen. Dort sehen Sie auch die Spalten, in denen Informationen entdeckt wurden, deren Klassifizierung empfohlen wird.

1. Suchen Sie auf der Seite **Empfehlungen** von Defender for Cloud nach der Empfehlung **Sensitive Daten in Ihren SQL-Datenbanken sollten klassifiziert werden**.

    :::image type="content" source="./media/sql-information-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Suchen nach der Empfehlung, die Zugriff auf die SQL Information Protection-Richtlinien bietet":::

1. Wählen Sie auf der Seite mit den Empfehlungsdetails auf der Registerkarte **Fehlerfrei** oder **Fehlerhaft** eine Datenbank aus.

1. Die Seite **Datenermittlung und -klassifizierung** wird geöffnet. Wählen Sie **Konfigurieren** aus.

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Öffnen der SQL Information Protection-Richtlinie aus der relevanten Empfehlung in Microsoft Defender für Cloud":::



### <a name="from-azure-sql"></a>[**Über Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Zugreifen auf die Richtlinie über Azure SQL <a name="sqlip-azuresql"></a>

1. Öffnen Sie Azure SQL im Azure-Portal.

    :::image type="content" source="./media/sql-information-protection-policy/open-azure-sql.png" alt-text="Öffnen von Azure SQL im Azure-Portal":::

1. Wählen Sie eine beliebige Datenbank aus.

1. Öffnen Sie im Menübereich **Sicherheit** die Seite **Datenermittlung und -klassifizierung** (1), und wählen Sie **Konfigurieren** (2) aus.

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-azure-sql.png" alt-text="Öffnen der SQL Information Protection-Richtlinie in Azure SQL":::

--- 

## <a name="customize-your-information-types"></a>Anpassen der Informationstypen

Gehen Sie zum Verwalten und Anpassen von Informationstypen folgendermaßen vor:

1. Wählen Sie **Informationstypen verwalten** aus.

    :::image type="content" source="./media/sql-information-protection-policy/manage-types.png" alt-text="Verwalten von Informationstypen für Ihre Information Protection-Richtlinie":::

1. Um einen neuen Typ hinzuzufügen, wählen Sie **Informationstyp erstellen** aus. Sie können einen Namen, eine Beschreibung und Suchmusterzeichenfolgen für den Informationstyp konfigurieren. Suchzeichenfolgenmuster können optional Schlüsselwörter mit Platzhalterzeichen (mit dem Zeichen „%“) enthalten, anhand derer die automatisierte Ermittlungs-Engine sensible Daten in Ihren Datenbanken basierend auf den Metadaten der Spalten ermittelt.
 
    :::image type="content" source="./media/sql-information-protection-policy/configure-new-type.png" alt-text="Konfigurieren eines neuen Informationstyps für Ihre Information Protection-Richtlinie":::

1. Sie können auch die integrierten Informationstypen ändern, indem Sie zusätzliche Suchmusterzeichenfolgen hinzufügen, einige der vorhandenen Zeichenfolgen deaktivieren oder die Beschreibung ändern. 

    > [!TIP]
    > Sie können integrierte Typen jedoch weder löschen noch ihre Namen ändern. 

1. **Informationstypen** werden in aufsteigender Reihenfolge der Ermittlung aufgeführt, d.h., die Typen, die weiter oben in der Liste stehen, werden bei der Suche nach Übereinstimmungen bevorzugt. Um die Rangfolge zwischen den Informationstypen zu ändern, ziehen Sie die Typen an die entsprechende Position in der Tabelle oder verwenden die Schaltflächen **Nach oben verschieben** und **Nach unten verschieben**, um die Reihenfolge zu ändern. 

1. Wenn Sie fertig sind, klicken Sie auf **OK**.

1. Nachdem die Verwaltung Ihrer Informationstypen abgeschlossen ist, ordnen Sie den relevanten Bezeichnungen die entsprechenden Typen zu, indem Sie für eine bestimmte Bezeichnung auf **Konfigurieren** klicken und nach Bedarf Informationstypen hinzufügen oder löschen.

1. Um Ihre Änderungen anzuwenden, klicken Sie auf der Hauptseite **Bezeichnungen** auf **Speichern**.
 

## <a name="exporting-and-importing-a-policy"></a>Exportieren und Importieren einer Richtlinie

Sie können eine JSON-Datei mit Ihren definierten Bezeichnungen und Informationstypen herunterladen, in einem Editor Ihrer Wahl bearbeiten und die aktualisierte Datei dann importieren. 

:::image type="content" source="./media/sql-information-protection-policy/export-import.png" alt-text="Exportieren und Importieren Ihrer Information Protection-Richtlinie":::

> [!NOTE]
> Sie benötigen Berechtigungen auf Mandantenebene, um eine Richtliniendatei zu importieren. 


## <a name="permissions"></a>Berechtigungen

Um die Information Protection-Richtlinie für Ihren Azure-Mandanten anpassen zu können, benötigen Sie die folgenden Aktionen in der Stammverwaltungsgruppe des Mandanten:
  - Microsoft.Security/informationProtectionPolicies/read
  - Microsoft.Security/informationProtectionPolicies/write 

Weitere Informationen finden Sie unter [Erteilen und Anfordern der mandantenweiten Sichtbarkeit](tenant-wide-permissions-management.md).

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Verwalten von SQL Information Protection mit Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Ruft die effektive SQL Information Protection-Richtlinie für den Mandanten ab.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Legt die effektive SQL Information Protection-Richtlinie für den Mandanten fest.
 

## <a name="next-steps"></a>Nächste Schritte
 
In diesem Artikel haben Sie erfahren, wie Sie eine Information Protection-Richtlinie im Azure Security Center definieren. Weitere Informationen zum Klassifizieren und Schutz von sensiblen Daten in Ihren SQL-Datenbank-Instanzen mit SQL Information Protection finden Sie unter [Azure SQL-Datenbank – Datenermittlung und -klassifizierung](../azure-sql/database/data-discovery-and-classification-overview.md).

Weitere Informationen zu Sicherheitsrichtlinien und zur Datensicherheit in Security Center finden Sie in den folgenden Artikeln:
 
- [Festlegen von Sicherheitsrichtlinien in Microsoft Defender für Cloud](tutorial-security-policy.md): Hier lernen Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren
- [Microsoft Defender für Cloud-Datensicherheit](data-security.md): Erfahren Sie, wie Defender für Cloud Daten verwaltet und schützt

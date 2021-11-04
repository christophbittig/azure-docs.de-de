---
title: Konzepte für Azure Purview-Zugriffsrichtlinien
description: Informationen zu Azure Purview-Zugriffsrichtlinien
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4d3d635b3f38f344ca0ae6cf89e16b0e6714073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094959"
---
# <a name="concepts-for-azure-purview-data-access-policies"></a>Konzepte für Azure Purview-Datenzugriffsrichtlinien

Dieser Artikel hilft Ihnen, die Azure Purview-Datenzugriffsrichtlinien zu verstehen, mit denen Sie den Zugriff auf Ihren gesamten Datenbestand von Azure Purview aus verwalten können.

> [!Note]
> Diese Funktion unterscheidet sich von der Zugriffssteuerung für Azure Purview selbst, die unter [Zugriffssteuerung in Azure Purview](catalog-permissions.md) beschrieben wird.

> [!IMPORTANT]
> Azure Purview-Zugriffsrichtlinien befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="overview"></a>Übersicht

Mit Zugriffsrichtlinien in Azure Purview können Sie den Zugriff auf verschiedene Datensysteme in Ihrem gesamten Datenbestand verwalten. Beispiel:

Ein Benutzer benötigt Lesezugriff auf ein Azure Storage-Konto, das in Azure Purview registriert wurde. Sie können diesen Zugriff direkt in Azure Purview gewähren, indem Sie eine Datenzugriffsrichtlinie über die App **Richtlinienverwaltung** in Purview Studio erstellen.

Datenzugriffsrichtlinien können über Purview auf Datensystemen durchgesetzt werden, die für die Richtlinie registriert wurden.

## <a name="azure-purview-policy-concepts"></a>Azure Purview-Richtlinienkonzepte

### <a name="azure-purview-policy"></a>Azure Purview-Richtlinie

Eine **Richtlinie** ist eine benannte Sammlung von Richtlinienanweisungen. Wenn eine Richtlinie unter der Governance von Purview in einem oder mehreren Datensystemen veröffentlicht wird, wird sie von diesen durchgesetzt. Eine Richtliniendefinition enthält einen Richtliniennamen, eine Beschreibung und eine Liste mit einer oder mehreren Richtlinienanweisungen.

### <a name="policy-statement"></a>Richtlinienanweisung

Eine **Richtlinienerklärung** ist eine für Menschen lesbare Anweisung, die vorschreibt, wie die Datenquelle einen bestimmten Datenvorgang handhaben soll. Die Richtlinienanweisung umfasst **Effekt**, **Aktion, Datenressource** und **Betreff**.

#### <a name="action"></a>Aktion

Eine **Aktion** ist der Vorgang, der im Rahmen dieser Richtlinie zugelassen oder verweigert wird. Zum Beispiel: Lesen oder Schreiben. Diese logischen Aktionen auf hoher Ebene werden einer (oder mehreren) Datenaktionen in dem Datensystem zugeordnet, in dem sie erzwungen werden.

#### <a name="effect"></a>Wirkung

Der **Effekt** gibt an, was die resultierende Wirkung dieser Richtlinie sein sollte. Derzeit ist der einzige unterstützte Wert **Zulassen**.

#### <a name="data-resource"></a>Datenressource

Die **Datenressource** ist der vollständig qualifizierte Datenassetpfad, für den eine Richtlinienanweisung gilt. Es entspricht dem folgenden Format:

*/subscription/\<subscription-id>/resourcegroups/\<resource-group-name>/providers/\<provider-name>/\<data-asset-path>*

Azure Storage-Datenasset-Pfadformat:

*Microsoft.Storage/storageAccounts/\<account-name>/blobservice/default/containers/\<container-name>*

Azure SQL DB-Datenasset-Pfadformat:

*Microsoft.Sql/servers/\<server-name>*

#### <a name="subject"></a>Subject

Die Endbenutzeridentität aus Azure Active Directory (AAD), für die diese Richtlinienanweisung gilt. Diese Identität kann ein Dienstprinzipal, ein einzelner Benutzer, eine Gruppe oder eine Managed Service Identity (MSI) sein.

### <a name="example"></a>Beispiel

Lesen auf Datenasset verweigern: */subscription/finance/resourcegroups/prod/providers/Microsoft.Storage/storageAccounts/finDataLake/blobservice/default/containers/FinData to group Finance-analyst*

In der obigen Richtlinienanweisung lautet der Effekt *Verweigern*, die Aktion ist *Lesen*, die Datenressource ist der Azure Storage-Container *FinData* und der Betreff ist die AAD-Gruppe *Finanzanalyst*. Wenn ein Benutzer dieser Gruppe versucht, Daten aus dem Speichercontainer *FinData* zu lesen, wird die Anfrage abgelehnt.

### <a name="hierarchical-enforcement-of-policies"></a>Hierarchische Durchsetzung von Richtlinien

Die in einer Richtlinienanweisung angegebene Datenressource ist standardmäßig hierarchisch. Dies bedeutet, dass die Richtlinienanweisung für das Datenobjekt selbst und für **alle** im Datenobjekt enthaltenen untergeordneten Objekte gilt. Beispielsweise gilt eine Richtlinienanweisung für den Azure-Speichercontainer für alle darin enthaltenen Blobs.

### <a name="policy-combining-algorithm"></a>Algorithmus zum Kombinieren von Richtlinien 

Azure Purview kann unterschiedliche Richtlinienanweisungen haben, die sich auf dasselbe Datenasset beziehen. Bei der Bewertung einer Entscheidung für den Datenzugriff kombiniert Azure Purview alle anwendbaren Richtlinien und bietet eine konsolidierte Entscheidung. Die Kombinationsstrategie besteht darin, die restriktivste Richtlinie auszuwählen.
Nehmen wir beispielsweise zwei verschiedene Richtlinien für einen Azure Storage-Container *FinData* wie folgt an:

Richtlinie 1 – *Lesen auf Datenasset /subscription/…./containers/FinData To group Finance-analyst zulassen*

Richtlinie 2 - *Lesen auf Datenasset /subscription/…./containers/FinData To group Finance-contractors ablehnen*

Nehmen wir dann an, dass der Benutzer „user1“, der zu den zwei Gruppen *Finanzanalyst* und *Finanzkontraktoren* gehört, einen Aufruf an die Blob-Read-API ausführt. Da beide Richtlinien anwendbar sind, wählt Azure Purview die restriktivste, nämlich *Ablehnen* von *Lesen*. Somit wird die Zugriffsanfrage abgelehnt.

## <a name="policy-publishing"></a>Richtlinienveröffentlichung

Eine neu erstellte Richtlinie ist im Entwurfsmodus vorhanden und nur in Azure Purview sichtbar. Der Akt der Veröffentlichung initiiert die Durchsetzung einer Richtlinie in den angegebenen Datensystemen. Es handelt sich um eine asynchrone Aktion, die bis zu 2 Minuten dauern kann, bis sie für die zugrunde liegenden Datenquellen wirksam ist.

Eine in einer Datenquelle veröffentlichte Richtlinie kann Verweise auf ein Asset enthalten, das zu einer anderen Datenquelle gehört. Solche Verweise werden ignoriert, da das betreffende Asset in der Datenquelle, auf die die Richtlinie angewendet wird, nicht vorhanden ist.

## <a name="azure-purview-to-data-source-action-mapping"></a>Azure Purview zu Datenquellen-Aktionszuordnung

Die folgende Tabelle veranschaulicht, wie Aktionen in Azure Purview-Datenrichtlinien bestimmten Aktionen in Datensystemen zugeordnet werden.

| **Purview-Richtlinien-Aktion** | **Datenquellenspezifische Aktionen**                                                                |
|---------------------------|-------------------------------------------------------------------------------------------------|
|||
| *Lesen*                      |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read                        |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery                      |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed                    |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|||
| *Modify*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write                           |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete                          |
|||
| *SQL-Leistungsmonitor*   |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select                     |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select         |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select                        |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select            |
|                           |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|||
| *SQL-Auditor*               |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select                |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select    |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select             |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Tutorials zum Erstellen von Richtlinien in Azure Purview an, die auf bestimmten Datensystemen wie Azure Storage funktionieren:

* [Datensatz-Bereitstellung durch den Dateneigentümer für Azure Storage](how-to-access-policies-storage.md)

<!--
[Dataset provisioning by data owner for Azure SQL DB](how-to-access-policies-sql.md)
-->

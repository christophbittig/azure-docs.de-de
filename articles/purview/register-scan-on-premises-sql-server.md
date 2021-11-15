---
title: Verbinden und Verwalten lokaler SQL Serverinstanzen
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit lokalen SQL Serverinstanzen in Azure Purview herstellen und die Funktionen von Purview verwenden, um Ihre lokale SQL Serverquelle zu überprüfen und zu verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 5d2a08a1ed51028d63e7bc98825c972b869b50ea
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848892"
---
# <a name="connect-to-and-manage-an-on-premises-sql-server-instance-in-azure-purview"></a>Verbinden und Verwalten einer lokalen SQL Serverinstanz in Azure Purview

In diesem Artikel wird beschrieben, wie Sie lokale SQL Serverinstanzen registrieren und wie Sie eine lokale SQL-Serverinstanz in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | Nein| nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden [Sie im Leitfaden über das Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie eine lokale SQL Serverinstanz in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Es gibt nur eine Möglichkeit, die Authentifizierung für eine lokale SQL Server-Datenquelle einzurichten:

- SQL-Authentifizierung

#### <a name="sql-authentication-to-register"></a>SQL-Authentifizierung für die Registrierung

Das SQL-Konto muss Zugriff auf die **Masterdatenbank** besitzen. Dies liegt daran, dass sich `sys.databases` in der Masterdatenbank befindet. Der Purview-Scanner muss `sys.databases` durchlaufen, um alle SQL-Datenbankinstanzen auf dem Server zu finden.

##### <a name="creating-a-new-login-and-user"></a>Erstellen einer neuen Anmeldung und eines neuen Benutzers

Führen Sie die folgenden Schritte aus, wenn Sie eine neue Anmeldung und einen neuen Benutzer erstellen möchten, der Ihre SQL Server-Datenquelle überprüfen (scannen) kann:

> [!Note]
> Alle nachfolgenden Schritte können mit dem [hier](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) bereitgestellten Code ausgeführt werden.

1. Navigieren Sie zu SQL Server Management Studio (SSMS), stellen Sie eine Verbindung mit dem Server her, navigieren Sie zu „Sicherheit“, wählen und halten Sie „Anmeldung“ (oder über rechte Maustaste), und erstellen Sie eine neue Anmeldung. Wählen Sie „SQL Server-Authentifizierung“ aus.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Eine neue Anmeldung und einen neuen Benutzer erstellen":::

1. Wählen Sie „Serverrollen“ im linken Navigationsbereich aus, und stellen Sie sicher, dass die Rolle „public“ zugewiesen ist.

1. Wählen Sie „Benutzerzuordnung“ im linken Navigationsbereich aus, wählen Sie alle Datenbanken in der Zuordnung aus, und wählen Sie dann die folgende Datenbankrolle aus: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="Benutzerzuordnung":::

1. Klicken Sie zum Speichern auf OK.

1. Navigieren Sie erneut zu dem*der Benutzer*in, den Sie erstellt haben, indem Sie darauf klicken und die Maustaste gedrückt halten (oder die rechte Maustaste verwenden) und die Option **Eigenschaften** auswählen. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Wählen Sie die Option „Altes Kennwort angeben“ aus, und geben Sie das alte Kennwort ein. **Sie müssen Ihr Kennwort ändern, sobald Sie eine neue Anmeldung erstellen.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="Kennwort ändern":::

##### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Speichern Ihres SQL-Anmeldekennworts in einem Schlüsseltresor und Erstellen von Anmeldeinformationen in Purview

1. Navigieren Sie im Azure-Portal1 zu Ihrem Schlüsseltresor. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* aus Ihrer  SQL Server-Anmeldung ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich unter „Quellen und Überprüfen“ die Option **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls nicht, führen Sie die [hier](manage-integration-runtimes.md) beschriebenen Schritte aus, um eine selbstgehostete Integration Runtime für Überprüfungen auf einem lokalen virtuellen Computer oder auf einem virtuellen Azure-Computer mit Zugriff auf Ihr lokales Netzwerk zu erstellen.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie **SQL Server** und dann **Weiter** aus.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Die SQL Server-Datenquelle einrichten":::

1. Geben Sie einen Anzeigenamen an. Dies ist ein Kurzname, den Sie zum Identifizieren Ihres Servers und des Server-Endpunkts verwenden können.

1. Wählen Sie **Fertigstellen**, um die Datenquelle zu registrieren.

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um lokale SQL Serverinstanzen zu überprüfen, um Ressourcen automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Überprüfen im Allgemeinen finden Sie in unserer [Einführung in Scans und Datenerfassung](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte SQL Server-Quelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Tabellen festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

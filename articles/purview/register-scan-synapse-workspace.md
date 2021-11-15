---
title: Verbinden mit und Verwalten von Azure Synapse Analytics-Arbeitsbereichen
description: In diesem Leitfaden wird beschrieben, wie Sie sich in Azure Purview mit Azure Synapse Analytics-Arbeitsbereichen verbinden und mit den Features von Purview Ihre Azure Synapse Analytics-Arbeitsbereichsquelle überprüfen und verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: a5921e2af4445ad645ce48f6102c311c9478b9e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848019"
---
# <a name="connect-to-and-manage-azure-synapse-analytics-workspaces-in-azure-purview"></a>Verbinden mit und Verwalten von Azure Synapse Analytics-Arbeitsbereichen in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Azure Synapse Analytics-Arbeitsbereiche in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| [Ja](#scan) | [Ja](#scan)| [Ja](#scan)| Nein| [Ja – Synapse-Pipelines](how-to-lineage-azure-synapse-analytics.md)|


<!-- 4. Prerequisites
Required. Add any relevant/source-specific prerequisites for connecting with this source. Authentication/Registration should be covered by the sections below and does not need to be covered here.
-->

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) Azure Synapse Analytics-Arbeitsbereiche registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Nur Benutzer mit mindestens der Rolle *Leser* für den Azure Synapse-Arbeitsbereich, die auch *Datenquellenadministratoren* in Azure Purview sind, können einen Azure Synapse-Arbeitsbereich registrieren.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Gehen Sie zu Ihrem Azure Purview-Konto.
1. Wählen Sie im linken Bereich **Quellen** aus.
1. Wählen Sie **Registrieren**.
1. Wählen Sie unter **Quellen registrieren** die Option **Azure Synapse Analytics (mehrere)** aus.
1. Wählen Sie **Weiter**.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Screenshot: Auswählen von Quellen in Azure Purview (einschließlich Azure Synapse Analytics)":::

1. Gehen Sie auf der Seite **Quellen registrieren (Azure Synapse Analytics)** wie folgt vor:

    a. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.  
    b. Wählen Sie optional ein **Abonnement** aus, nach dem gefiltert werden soll.  
    c. Wählen Sie in der Dropdownliste **Arbeitsbereichsname** den Arbeitsbereich aus, mit dem Sie arbeiten.  
    d. In den Dropdownlisten für die Endpunkte werden die SQL-Endpunkte automatisch basierend auf Ihrer Arbeitsbereichsauswahl ausgefüllt.  
    e. Wählen Sie in der Dropdownliste **Sammlung auswählen** die Sammlung aus, mit der Sie arbeiten, oder erstellen Sie optional eine neue Sammlung.  
    f. Wählen Sie **Registrieren** aus, um die Registrierung der Datenquelle abzuschließen.

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Screenshot: Seite „Quellen registrieren (Azure Synapse Analytics)“ zum Eingeben von Details zur Azure Synapse Quelle":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Azure Synapse Analytics-Arbeitsbereiche zu überprüfen, Ressourcen automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

Sie müssen zunächst die Authentifizierung für die Aufzählung Ihrer [dedizierten](#authentication-for-enumerating-dedicated-sql-database-resources) oder [serverlosen](#authentication-for-enumerating-serverless-sql-database-resources) Ressourcen einrichten. Dadurch kann Purview Ihre Arbeitsbereichsressourcen aufzählen und bereichsbezogene Prüfungen durchführen.

Dann müssen Sie [Berechtigungen zum Überprüfen des Inhalts des Arbeitsbereichs](#apply-permissions-to-scan-the-contents-of-the-workspace) erteilen.

### <a name="authentication-for-enumerating-dedicated-sql-database-resources"></a>Authentifizierung zum Aufzählen dedizierter SQL-Datenbank-Ressourcen

1. Navigieren Sie im Azure-Portal zu der Azure Synapse-Arbeitsbereichsressource.  
1. Klicken Sie im linken Bereich auf  **Zugriffssteuerung (IAM)**.

   > [!NOTE]
   > Sie müssen *Besitzer* oder *Benutzerzugriffsadministrator* sein, um eine Rolle für die Ressource hinzufügen zu können.

1. Wählen Sie die Schaltfläche **Hinzufügen** aus.
1. Legen Sie die Rolle auf **Leser** fest, und geben Sie den Namen Ihres Azure Purview-Kontos ein. Dieser stellt die verwaltete Dienstidentität (Managed Service Identity, MSI) dar.
1. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen.

> [!NOTE]
> Wenn Sie in Ihrem Azure Purview-Konto mehrere Azure Synapse-Arbeitsbereiche registrieren und überprüfen möchten, können Sie die Rolle auch von einer höheren Ebene aus (beispielsweise Ressourcengruppe oder Abonnement) zuweisen.

### <a name="authentication-for-enumerating-serverless-sql-database-resources"></a>Authentifizierung zum Aufzählen serverloser SQL-Datenbank-Ressourcen

Es gibt drei Orte, an denen Sie die Authentifizierung festlegen müssen, damit Purview Ihre serverlosen SQL-Datenbank-Ressourcen aufzählen kann: im Synapse-Arbeitsbereich, im zugeordneten Speicher und in den serverlosen Datenbanken. Mit den folgenden Schritten werden Berechtigungen für alle drei Orte festgelegt.

1. Navigieren Sie im Azure-Portal zu der Azure Synapse-Arbeitsbereichsressource.  
1. Klicken Sie im linken Bereich auf  **Zugriffssteuerung (IAM)**. 

   > [!NOTE]
   > Sie müssen *Besitzer* oder *Benutzerzugriffsadministrator* sein, um eine Rolle für die Ressource hinzufügen zu können.
   
1. Wählen Sie die Schaltfläche **Hinzufügen** aus.   
1. Legen Sie die Rolle auf **Leser** fest, und geben Sie den Namen Ihres Azure Purview-Kontos ein. Dieser stellt die verwaltete Dienstidentität (Managed Service Identity, MSI) dar.
1. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen.
1. Navigieren Sie im Azure-Portal zu der **Ressourcengruppe** oder dem **Abonnement**, in der bzw. dem sich der Azure Synapse-Arbeitsbereich befindet.
1. Klicken Sie im linken Bereich auf  **Zugriffssteuerung (IAM)**. 

   > [!NOTE]
   > Sie müssen *Besitzer* oder *Benutzerzugriffsadministrator* sein, um im Feld **Ressourcengruppe** oder **Abonnement** eine Rolle hinzufügen zu können. 

1. Wählen Sie die Schaltfläche **Hinzufügen** aus. 
1. Legen Sie die Rolle auf **Storage-Blobdatenleser** fest, und geben Sie im Feld **Auswählen** den Namen Ihres Azure Purview-Kontos ein. (Dieser stellt die zugehörige MSI dar.) 
1. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen.
1. Navigieren Sie zu Ihrem Azure Synapse-Arbeitsbereich, und öffnen Sie Synapse Studio.
1. Wählen Sie im linken Menü die Registerkarte **Datasets** aus.
1. Wählen Sie die Auslassungspunkte (**...**) neben einer Ihrer Datenbanken aus, und starten Sie ein neues SQL-Skript.
1. Fügen Sie die MSI des Azure Purview-Kontos (dargestellt durch den Kontonamen) für die serverlosen SQL-Datenbanken hinzu: Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="apply-permissions-to-scan-the-contents-of-the-workspace"></a>Erteilen von Berechtigungen zum Überprüfen des Arbeitsbereichsinhalts

Die Authentifizierung für eine Azure Synapse-Quelle kann auf zwei Arten eingerichtet werden:

- Verwenden einer verwalteten Identität
- Verwenden eines Dienstprinzipals

> [!IMPORTANT]
> Diese Schritte für serverlose Datenbanken gelten **nicht** für replizierte Datenbanken. In Synapse sind serverlose Datenbanken, die aus Spark-Datenbanken repliziert werden, derzeit schreibgeschützt. Weitere Informationen finden Sie [hier](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database).

> [!NOTE]
> Die Authentifizierung muss für jede dedizierte SQL-Datenbank in Ihrem Azure Synapse-Arbeitsbereich eingerichtet werden, die Sie registrieren und überprüfen möchten. Die Berechtigungen, die in den folgenden Abschnitten für serverlose SQL-Datenbanken erwähnt werden, gelten für alle Datenbanken in Ihrem Arbeitsbereich. Die Authentifizierung muss also nur einmal eingerichtet werden.

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>Verwenden einer verwalteten Identität für dedizierte SQL-Datenbanken

1. Navigieren Sie zu Ihrem Azure Synapse-Arbeitsbereich.
1. Navigieren Sie zum Abschnitt **Daten**, und suchen Sie nach einer Ihrer dedizierten SQL-Datenbanken.
1. Wählen Sie die daneben angezeigten Auslassungspunkte (**...**) aus, und starten Sie ein neues SQL-Skript.

   > [!NOTE]
   > Die Befehle im folgenden Verfahren müssen von einem *Azure Synapse-Administrator* für den Arbeitsbereich ausgeführt werden. Weitere Informationen zu Azure Synapse Analytics-Berechtigungen finden Sie unter [Einrichten der Zugriffssteuerung für Ihren Azure Synapse-Arbeitsbereich](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Fügen Sie die MSI des Azure Purview-Kontos (dargestellt durch den Kontonamen) als **db_datareader** für die dedizierte SQL-Datenbank hinzu. Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```

#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>Verwenden einer verwalteten Identität für serverlose SQL-Datenbanken

1. Navigieren Sie zu Ihrem Azure Synapse-Arbeitsbereich.
1. Navigieren Sie zum Abschnitt **Daten**, und führen Sie die nächsten Schritte für **jede zu überprüfende Datenbank** aus.
1. Wählen Sie die Auslassungspunkte (**...**) neben Ihrer Datenbank aus, und starten Sie ein neues SQL-Skript.
1. Fügen Sie die MSI des Azure Purview-Kontos (dargestellt durch den Kontonamen) als **db_datareader** für die serverlosen SQL-Datenbanken hinzu. Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="grant-permission-to-use-credentials-for-external-tables"></a>Erteilen der Berechtigung zum Verwenden von Anmeldeinformationen für externe Tabellen

Wenn der Azure Synapse Arbeitsbereich über externe Tabellen verfügt, muss der verwalteten Azure Purview-Identität die Berechtigung „Verweise“ für die Anmeldeinformationen im Bereich der „externe Tabelle“ erteilt werden. Mit der Berechtigung „Verweise“ kann Azure Purview Daten aus externen Tabellen lesen.

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::[scoped_credential] TO [PurviewAccountName];
```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>Verwenden eines Dienstprinzipals für dedizierte SQL-Datenbanken

> [!NOTE]
> Sie müssen zunächst neue *Anmeldeinformationen* vom Typ *Dienstprinzipal* einrichten. Gehen Sie dazu wie unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md) beschrieben vor.

1. Navigieren Sie zu Ihrem **Azure Synapse-Arbeitsbereich**.
1. Navigieren Sie zum Abschnitt **Daten**, und suchen Sie nach einer Ihrer dedizierten SQL-Datenbanken.
1. Wählen Sie die daneben angezeigten Auslassungspunkte (**...**) aus, und starten Sie ein neues SQL-Skript.
1. Fügen Sie die **Dienstprinzipal-ID** als **db_datareader** für die dedizierte SQL-Datenbank hinzu. Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```

> [!NOTE]
> Wiederholen Sie den vorherigen Schritt für alle dedizierten SQL-Datenbanken in Ihrem Synapse-Arbeitsbereich. 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>Verwenden eines Dienstprinzipals für serverlose SQL-Datenbanken

1. Navigieren Sie zu Ihrem Azure Synapse-Arbeitsbereich.
1. Navigieren Sie zum Abschnitt **Daten**, und suchen Sie nach einer Ihrer serverlosen SQL-Datenbanken.
1. Wählen Sie die daneben angezeigten Auslassungspunkte (**...**) aus, und starten Sie ein neues SQL-Skript.
1. Fügen Sie die **Dienstprinzipal-ID** für die serverlosen SQL-Datenbanken hinzu. Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. Fügen Sie die **Dienstprinzipal-ID** als **db_datareader** für jede serverlose SQL-Datenbank hinzu, die Sie überprüfen möchten. Führen Sie dazu in Ihrem SQL-Skript den folgenden Befehl aus:
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="set-up-azure-synapse-workspace-firewall-access"></a>Einrichten des Firewallzugriffs für den Azure Synapse-Arbeitsbereich

1. Navigieren Sie im Azure-Portal zum Azure Synapse-Arbeitsbereich. 

1. Wählen Sie im linken Bereich **Firewalls** aus.

1. Wählen Sie für **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Arbeitsbereich gestatten** die Option **EIN** aus.

1. Wählen Sie **Speichern** aus.

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie in [Purview Studio](https://web.purview.azure.com/resource/) im linken Bereich die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte Datenquelle aus.

1. Wählen Sie **Details anzeigen** und anschließend die Registerkarte **New scan** (Neue Überprüfung) aus. Alternativ können Sie auch auf der Quellkachel das Schnellaktionssymbol **Überprüfen** auswählen.

1. Geben Sie im Detailbereich **Überprüfen** im Feld **Name** einen Namen für die Überprüfung ein.
1. Wählen Sie in der Dropdownliste **Typ** die Arten von Ressourcen aus, die Sie innerhalb dieser Quelle überprüfen möchten. **SQL-Datenbank** ist der einzige Typ, der derzeit in einem Azure Synapse-Arbeitsbereich unterstützt wird.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Screenshot: Detailbereich für die Überprüfung der Azure Synapse-Quelle":::

1. Wählen Sie in der Dropdownliste **Anmeldeinformationen** die Anmeldeinformationen für die Verbindungsherstellung mit den Ressourcen innerhalb Ihrer Datenquelle aus. 
  
1. Sie können in jedem Typ auswählen, ob Sie entweder alle Ressourcen überprüfen möchten oder nur eine Teilmenge (auf der Grundlage des Namens).

1.  die Option **Fortsetzen** aus, um fortzufahren. 

1.  Wählen Sie **Überprüfungsregelsätze** vom Typ **Azure Synapse SQL** aus. Sie können Überprüfungsregelsätze auch inline erstellen.

1. Wählen Sie den Auslöser für die Überprüfung. Sie können die Planung so konfigurieren, dass die Ausführung **wöchentlich/monatlich** oder **einmal** erfolgt.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie anschließend **Speichern** aus, um die Einrichtung abzuschließen.  

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

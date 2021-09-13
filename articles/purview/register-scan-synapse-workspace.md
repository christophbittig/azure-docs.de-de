---
title: Überprüfen von Azure Synapse Analytics-Arbeitsbereichen
description: Hier erfahren Sie, wie Sie einen Azure Synapse-Arbeitsbereich in Ihrem Azure Purview-Datenkatalog überprüfen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: a74e88d72d1e7109b6e0acfa81485476eed9e00b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355098"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>Registrieren und Überprüfen von Azure Synapse Analytics-Arbeitsbereichen

In diesem Artikel erfahren Sie, wie Sie einen Azure Synapse Analytics-Arbeitsbereich in Azure Purview registrieren und eine Überprüfung dafür einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Azure Synapse Analytics-Arbeitsbereichsüberprüfungen unterstützen die Erfassung von Metadaten und Schemas für die darin befindlichen dedizierten und serverlosen SQL-Datenbanken. Darüber hinaus werden die Daten bei den Arbeitsbereichsüberprüfungen automatisch auf der Grundlage von systemseitigen und benutzerdefinierten Klassifizierungsregeln klassifiziert.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie ein Azure Purview-Konto, bevor Sie Ihre Datenquellen registrieren. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure Purview-Kontos](create-catalog-portal.md).
- Sie müssen Datenquellenadministrator für Azure Purview sein.
- Richten Sie die Authentifizierung wie in den folgenden Abschnitten beschrieben ein.

## <a name="register-and-scan-an-azure-synapse-workspace"></a>Registrieren und Überprüfen eines Azure Synapse-Arbeitsbereichs

> [!IMPORTANT]
> Gehen Sie beim Ausführen der Schritte sowie beim Anwenden der Berechtigungen exakt so vor wie in den nächsten Abschnitten beschrieben, um Ihren Arbeitsbereich erfolgreich zu überprüfen.

### <a name="step-1-register-your-source"></a>**Schritt 1:** Registrieren Ihrer Quelle

> [!NOTE]
> Dieser Schritt kann nur von Benutzern ausgeführt werden, die für den Azure Synapse-Arbeitsbereich mindestens über die Rolle *Leser* verfügen und *Datenquellenadministrator* in Azure Purview sind.

So registrieren Sie in Ihrem Datenkatalog eine neue Azure Synapse-Quelle:

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


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**Schritt 2:** Anwenden von Berechtigungen zum Aufzählen der Inhalte des Azure Synapse-Arbeitsbereichs

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>Einrichten der Authentifizierung zum Aufzählen dedizierter SQL-Datenbank-Ressourcen

1. Navigieren Sie im Azure-Portal zu der Azure Synapse-Arbeitsbereichsressource.  
1. Klicken Sie im linken Bereich auf  **Zugriffssteuerung (IAM)**. 

   > [!NOTE]
   > Sie müssen *Besitzer* oder *Benutzerzugriffsadministrator* sein, um eine Rolle für die Ressource hinzufügen zu können.
   
1. Wählen Sie die Schaltfläche **Hinzufügen** aus.   
1. Legen Sie die Rolle auf **Leser** fest, und geben Sie den Namen Ihres Azure Purview-Kontos ein. Dieser stellt die verwaltete Dienstidentität (Managed Service Identity, MSI) dar.
1. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen.

> [!NOTE]
> Wenn Sie in Ihrem Azure Purview-Konto mehrere Azure Synapse-Arbeitsbereiche registrieren und überprüfen möchten, können Sie die Rolle auch von einer höheren Ebene aus (beispielsweise Ressourcengruppe oder Abonnement) zuweisen. 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>Einrichten der Authentifizierung zum Aufzählen serverloser SQL-Datenbank-Ressourcen

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

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**Schritt 3:** Anwenden von Berechtigungen zum Überprüfen des Arbeitsbereichsinhalts

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

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**Schritt 4:** Einrichten des Firewallzugriffs für den Azure Synapse-Arbeitsbereich

1. Navigieren Sie im Azure-Portal zum Azure Synapse-Arbeitsbereich. 

1. Wählen Sie im linken Bereich **Firewalls** aus.

1. Wählen Sie für **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Arbeitsbereich gestatten** die Option **EIN** aus.

1. Wählen Sie **Speichern** aus.

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**Schritt 5:** Einrichten einer Überprüfung für den Arbeitsbereich

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie in Purview Studio im linken Bereich die Registerkarte **Data Map** aus.

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

#### <a name="view-your-scans-and-scan-runs"></a>Betrachten Sie Ihre Überprüfungen und Überprüfungsausführungen

1. Sehen Sie sich die Details der Quelle an, indem Sie auf der Kachel unter dem Quellenabschnitt die Option **Details anzeigen** auswählen. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Screenshot: Detailseite der Azure Synapse Analytics-Quelle"::: 

1. Sehen Sie sich die Details der Überprüfungsausführung an, indem Sie zur Seite mit den Überprüfungsdetails navigieren.

    * Die **Statusleiste** zeigt eine kurze Zusammenfassung des Ausführungsstatus der untergeordneten Ressourcen. Der Status wird in der Überprüfung auf Arbeitsbereichsebene angezeigt.  
    * Grün bedeutet, dass die Überprüfung erfolgreich ausgeführt wurde. Rot gibt an, dass die Überprüfungsausführung nicht erfolgreich war. Grau bedeutet, dass die Überprüfung noch nicht abgeschlossen ist.  
    * Durch Klicken auf die Überprüfungsausführungen können Sie ausführlichere Informationen anzeigen.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Screenshot: Detailseite der Azure Synapse Analytics-Überprüfung" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * Im unteren Bereich der Seite mit den Quellendetails finden Sie eine Zusammenfassung der letzten nicht erfolgreichen Überprüfungsausführungen. Auch hier können durch Klicken auf die Überprüfungsausführungen ausführlichere Informationen angezeigt werden.

#### <a name="manage-your-scans"></a>Verwalten Ihrer Überprüfungen

Wenn Sie eine Überprüfung bearbeiten, löschen oder abbrechen möchten, gehen Sie wie folgt vor:

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** und anschließend die gewünschte Datenquelle aus.

1. Wählen Sie die zu gewünschte Überprüfung und anschließend **Bearbeiten** aus.

   - Wenn Sie Ihre Überprüfung löschen möchten, wählen Sie **Löschen** aus.
   - Wird eine Überprüfung gerade ausgeführt, können Sie sie abbrechen.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)   

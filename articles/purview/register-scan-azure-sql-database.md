---
title: Azure SQL DB registrieren und scannen
description: Dieser Artikel beschreibt den Prozess zur Registrierung einer Azure SQL-Datenbank in Azure Purview, einschließlich Anweisungen zur Authentifizierung und Interaktion mit der Azure SQL DB-Quelle
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: cba37228902600852963068ba4d46e75adf21c57
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853888"
---
# <a name="connect-to-azure-sql-database-in-azure-purview"></a>Verbindung zur Azure SQL-Datenbank in Azure Purview

Dieser Artikel beschreibt den Prozess zur Registrierung einer Azure SQL-Datenquelle in Azure Purview, einschließlich Anweisungen zur Authentifizierung und Interaktion mit der Azure SQL-Datenbankquelle

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)|[Ja](#scan) | [Ja](#scan)|[Ja](#scan)| Nein | nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

### <a name="known-limitations"></a>Bekannte Einschränkungen

* Von Azure Purview werden auf der Registerkarte „Schema“ maximal 300 Spalten unterstützt, und es wird „Additional-Columns-Truncated“ angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt können Sie die Azure SQL DB-Datenquelle registrieren und einen geeigneten Authentifizierungsmechanismus einrichten, um das erfolgreiche Scannen der Datenquelle sicherzustellen.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Es ist wichtig, die Datenquelle in Azure Purview zu registrieren, bevor Sie eine Überprüfung für die Datenquelle einrichten.

1. Gehen Sie zum [Azure-Portal](https://portal.azure.com), navigieren Sie zur Seite **Purview-Konten** und klicken Sie auf Ihr _Purview-Konto_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-purview-acct.png" alt-text="Screenshot: Purview-Konto zum Registrieren der Datenquelle":::

1. **Öffnen Sie Purview Studio** und navigieren Sie zur **Datenkarte**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-open-purview-studio.png" alt-text="Screenshot: Navigieren zum Quellen-Link in der Data Map":::

1. Erstellen Sie die [Sammlungshierarchie](./quickstart-create-collection.md) mithilfe des Menüs **Sammlungen**, und weisen Sie den einzelnen untergeordneten Sammlungen nach Bedarf Berechtigungen zu

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-collections.png" alt-text="Screenshot, der das Sammlungsmenü zur Zuweisung von Zugriffskontrollberechtigungen für die Sammlungshierarchie zeigt":::

1. Navigieren Sie zur entsprechenden Sammlung im Menü **Quellen** und wählen Sie das Symbol **Registrieren**, um eine neue Azure SQL DB zu registrieren

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-data-source.png" alt-text="Screenshot: Sammlung zum Registrieren der Datenquelle":::

1. Wählen Sie die Datenquelle **Azure SQL Database** und wählen Sie **Fortfahren**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-select-ds.png" alt-text="Screenshot, der die Auswahl der Datenquelle ermöglicht":::

1. Geben Sie einen geeigneten **Namen** für die Datenquelle an, wählen Sie das entsprechende **Azure-Abonnement**, **Servername** für den SQL-Server und die **Sammlung** und wählen Sie auf **Anwenden**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-details.png" alt-text="Screenshot mit den Details, die eingegeben werden müssen, um die Datenquelle zu registrieren":::

1. Die Azure SQL Server-Datenbank wird unter der ausgewählten Sammlung angezeigt

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-collections.png" alt-text="Screenshot der Datenquelle, die der Sammlung zugeordnet ist, um die Überprüfung zu initiieren":::

## <a name="scan"></a>Überprüfen

### <a name="authentication-for-a-scan"></a>Authentifizierung für eine Überprüfung

Um Zugang zum Scannen der Datenquelle zu erhalten, muss eine Authentifizierungsmethode in der Azure SQL Database konfiguriert werden.
Die folgenden Optionen werden unterstützt:

* **SQL-Authentifizierung**

* **Verwaltete Identität** - Sobald das Azure Purview-Konto erstellt ist, wird automatisch eine System **Verwaltete Identität** im Azure AD-Tenant erstellt. Je nach Ressourcentyp sind bestimmte RBAC-Rollenzuweisungen erforderlich, damit die Azure Purview-MSI die Überprüfungen durchführen kann.
Wenn Sie eine verwaltete Identität verwenden, hat Ihr Purview-Konto eine eigene verwaltete Identität, die im Grunde genommen Ihr Purview-Name ist, als Sie ihn erstellt haben

* **Dienstprinzipal**: Bei dieser Methode können Sie einen neuen Dienstprinzipal erstellen oder einen vorhandenen Dienstprinzipal in Ihrem Azure Active Directory verwenden.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurieren der Azure AD-Authentifizierung im Datenbankkonto

Der Dienstprinzipal oder die verwaltete Identität muss über die Berechtigung zum Abrufen von Metadaten für die Datenbank, die Schemas und die Tabellen verfügen. Darüber hinaus muss das Abfragen der Tabellen möglich sein, damit Stichproben für die Klassifizierung genommen werden können.

- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Sie müssen einen Azure AD-Benutzer in Azure SQL-Datenbank erstellen, indem Sie genau den Namen der verwalteten Identität von Purview oder Ihren eigenen Dienstprinzipal angeben. Führen Sie dazu die Schritte im Tutorial unter [Erstellen des Dienstprinzipalbenutzers in Azure SQL-Datenbank](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database) aus. Sie müssen der Identität die richtige Berechtigung (z. B. `db_datareader`) zuweisen. SQL-Beispielsyntax für das Erstellen eines Benutzers und Gewähren der Berechtigung:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` ist Ihr eigener Dienstprinzipal oder eine verwaltete Identität von Purview. Weitere Informationen finden Sie unter den [festen Datenbankrollen und ihren Möglichkeiten](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).

#### <a name="using-sql-authentication-for-scanning"></a>SQL-Authentifizierung zum Scannen verwenden

> [!Note]
> Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. **15 Minuten**, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

Sie können die Anleitung unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für Azure SQL-Datenbank zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Screenshot: Schlüsseltresor":::

1. Navigieren Sie zu Ihrer Key Vault-Instanz, und wählen Sie **Einstellungen > Geheimnisse** und dann **+ Generieren/Importieren** aus

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Generieren eines Geheimnisses":::

1. Geben Sie den **Namen** und **Wert** als *Passwort* aus Ihrer Azure SQL-Datenbank ein

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret-sql.png" alt-text="Screenshot, der die Option Key Vault zur Eingabe der Sql-Geheimwerte zeigt":::

1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.

1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text=" Screenshot, der die Option &quot;Schlüsseltresor&quot; zur Einrichtung von Anmeldedaten zeigt ":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault-options.png" alt-text="Screenshot, der die Option des Schlüsseltresors zur Erstellung eines Geheimnisses zeigt":::

#### <a name="using-managed-identity-for-scanning"></a>Verwenden der verwalteten Identität für die Überprüfung

Es ist wichtig, dass Sie Ihrem Purview-Konto die Berechtigung zum Scannen der Azure SQL DB erteilen. Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!Note] 
> Sie müssen Besitzer des Abonnements sein, um einer Azure-Ressource eine verwaltete Identität hinzufügen zu können.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) entweder das Abonnement, die Ressourcengruppe oder die Ressource (z. B. eine Azure-SQL-Datenbank), die Sie mit dem Katalog durchsuchen möchten.

1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **+ Hinzufügen** --> **Rollenzuweisung hinzufügen** aus

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-ds.png" alt-text="Screenshot, der die Azure SQL-Datenbank zeigt":::

1. Setzen Sie die **Rolle** auf **Leser** und geben Sie Ihren _Azure Purview-Kontonamen_ unter dem Eingabefeld **Auswahl** ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-access-managed-identity.png" alt-text="Screenshot: Details zum Zuweisen von Berechtigungen für das Purview-Konto":::

#### <a name="using-service-principal-for-scanning"></a>Verwenden des Dienstprinzipals für die Überprüfung

##### <a name="creating-a-new-service-principal"></a>Erstellen eines neuen Dienstprinzipals

Wenn Sie [einen neuen Dienstprinzipal erstellen](./create-service-principal-azure.md)müssen, müssen Sie eine Anwendung in Ihrem Azure AD Mandanten registrieren und den Zugriff auf den Dienstprinzipal in Ihren Datenquellen bereitstellen. Ihr Azure AD allgemeiner Administrator oder andere Rollen, z. B. Anwendungsadministrator, können diesen Vorgang ausführen.

##### <a name="getting-the-service-principals-application-id"></a>Abrufen der Anwendungs-ID des Dienstprinzipals

1. Kopieren Sie die **Anwendungs-ID (Client)** , die in der **Übersicht** über das bereits erstellten [_Dienstprinzipal_](./create-service-principal-azure.md) vorhanden ist

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-appln-id.png" alt-text="Screenshot: Anwendungs-ID (Client) für das Dienstprinzipal":::

##### <a name="granting-the-service-principal-access-to-your-azure-sql-database"></a>Gewährung des Zugriffs des Service Principal auf Ihre Azure SQL-Datenbank

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="Screenshot, der den Schlüsseltresor zum Hinzufügen eines Geheimnisses für den Service Principal zeigt":::

1. Navigieren Sie zu Ihrer Key Vault-Instanz, und wählen Sie **Einstellungen > Geheimnisse** und dann **+ Generieren/Importieren** aus

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="Screenshot, der die Option des Schlüsseltresors zur Erzeugung eines Geheimnisses für den Dienstherrn zeigt":::

1. Geben Sie den **Namen** Ihrer Wahl und **Wert** als das **Clientgeheimnis** Ihres Dienstauftraggebers ein

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-create-secret.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Eingeben der Geheimniswerte":::

1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.

1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="Screenshot, der die Option Key Vault zum Hinzufügen von Anmeldeinformationen für Service Principal zeigt":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-cred.png" alt-text="Screenshot, der die Option des Schlüsseltresors zur Erstellung eines Geheimnisses für den Dienstprinzipal zeigt":::

### <a name="firewall-settings"></a>Firewalleinstellungen

Wenn auf Ihrem Datenbankserver eine Firewall aktiviert ist, müssen Sie die Firewall aktualisieren, um den Zugriff auf eine der folgenden Arten zu ermöglichen:

1. Lassen Sie Azure-Verbindungen über die Firewall zu.
1. Installieren Sie eine selbstgehostete Integration Runtime, und gewähren Sie ihr Zugriff über die Firewall.

#### <a name="allow-azure-connections"></a>Zulassen von Azure-Verbindungen

Durch Zulassen von Azure-Verbindungen kann Azure Purview den Server erreichen und verbinden, ohne die Firewall selbst zu aktualisieren. Sie können die Schrittanleitung zum [Herstellen von Verbindungen aus Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure) befolgen.

1. Navigieren Sie zu Ihrem Datenbankkonto.
1. Wählen Sie auf der Seite **Übersicht** den Servernamen aus.
1. Wählen Sie **Sicherheit > Firewalls und virtuelle Netzwerke** aus.
1. Wählen Sie **Ja** für **Zugriff von Azure-Diensten und -Ressourcen auf diesen Server erlauben**
:::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-firewall.png" alt-text="Zugriff von Azure-Diensten und -Ressourcen auf diesen Server erlauben." border="true":::

#### <a name="self-hosted-integration-runtime"></a>Selbstgehostete Integration Runtime

Eine selbstgehostete Integration Runtime (SHIR) kann auf einem Computer installiert werden, um eine Verbindung mit einer Ressource in einem privaten Netzwerk herzustellen.

1. [Erstellen und installieren Sie eine selbstgehostete Integration Runtime](./manage-integration-runtimes.md) auf einem privaten Computer oder einem Computer im gleichen VNet wie der Datenbankserver.
1. Überprüfen Sie die Firewall des Datenbankservers, um sicherzustellen, dass der SHIR-Computer über die Firewall Zugriff hat. Fügen Sie die IP-Adresse des Computers hinzu, wenn er noch keinen Zugriff hat.
1. Wenn sich Ihr Azure SQL-Server hinter einem privaten Endpunkt oder in einem VNet befindet, können Sie einen [privaten Endpunkt für die Erfassung](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) verwenden, um die End-to-End-Netzwerkisolation sicherzustellen.

### <a name="creating-the-scan"></a>Erstellen der Überprüfung

1. Öffnen Sie Ihr **Purview-Konto,** und klicken Sie auf **Purview Studio öffnen**
1. Navigieren Sie zu **Datenzuordnungs** --> **quelle**, um die Sammlungshierarchie anzuzeigen
1. Wählen Sie das Symbol **Neuer Scan** unter der zuvor registrierten **Azure SQL DB**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan.png" alt-text="Screenshot: Bildschirm zum Erstellen einer neuen Überprüfung":::

#### <a name="if-using-sql-authentication"></a>Bei Verwendung der SQL-Authentifizierung

1. Geben Sie einen **Namen** für die Überprüfung an, wählen Sie **Datenbankauswahlmethode** als _Manuell eingeben_, geben Sie den **Datenbanknamen** und den zuvor erstellten **Credential** ein, wählen Sie die entsprechende Sammlung für die Überprüfung und wählen Sie **Verbindung testen**, um die Verbindung zu überprüfen. Wenn die Verbindung erfolgreich ist, wählen Sie **Fortfahren**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-auth.png" alt-text="Screenshot, der die SQL-Authentifizierungsoption für die Überprüfung zeigt":::

#### <a name="if-using-managed-identity"></a>Bei Zugriff mithilfe einer verwalteten Identität

1. Geben Sie einen **Namen** für die Prüfung an, wählen Sie die **Purview MSI** unter **Credential**, wählen Sie die entsprechende Sammlung für die Prüfung

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-managed-id.png" alt-text="Screenshot: Option Verwaltete Identität zum Ausführen der Überprüfung":::

1. Klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-test.png" alt-text="Screenshot, der die Ausführung der Überprüfung mit der Option Verwaltete Identität ermöglicht":::

#### <a name="if-using-service-principal"></a>Bei Verwendung eines Dienstprinzipals

1. Geben Sie einen **Namen** für die Überprüfung an, wählen Sie die entsprechende Sammlung für die Überprüfung und wählen Sie das Dropdown-Menü **Berechtigung**, um die zuvor erstellte Berechtigung auszuwählen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp.png" alt-text="Screenshot, der die Option für das Dienstprinzipal zum Aktivieren der Überprüfung zeigt":::

1. Klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

### <a name="scoping-and-running-the-scan"></a>Definieren und Ausführen der Überprüfung

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner und Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Beim Erstellen eines neuen _Scan-Regelsatzes_

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan-rule-set.png" alt-text="Neuer Scan-Regelsatz":::

1. Sie können die **Klassifizierungsregeln** auswählen, die in die Überprüfungsregel aufgenommen werden sollen

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-classification.png" alt-text="Überprüfen von Klassifizierungsregeln":::


    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sel-scan-rule.png" alt-text="Überprüfen der Regelauswahl":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-trigger.png" alt-text="Auslöser für die Überprüfung":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-review-scan.png" alt-text="Überprüfungs-Scan":::

### <a name="view-scan"></a>Ansicht Scan

1. Navigieren Sie zur _Datenquelle_ in der _Sammlung_ und klicken Sie auf **Details anzeigen**, um den Status der Überprüfung zu prüfen

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan.png" alt-text="Überprüfung anzeigen":::

1. Die Scandetails geben den Fortschritt der Überprüfung im **Status der letzten Ausführung** an sowie die Anzahl der _gescannten_ und _klassifizierten_ Objekte

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan-details.png" alt-text="Überprüfungsdetails anzeigen":::

1. Der **Status Letzte Ausführung** wird in **In Bearbeitung** und anschließend in **Abgeschlossen** aktualisiert, sobald die gesamte Überprüfung erfolgreich ausgeführt wurde

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-complete.png" alt-text="Die abgeschlossene Überprüfung anzeigen":::

### <a name="manage-scan"></a>Scan verwalten

Überprüfungen können verwaltet oder nach Abschluss erneut ausgeführt werden

1. Klicken Sie auf den **Überprüfungsnamen**, um die Überprüfung zu verwalten

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage scan.png" alt-text="Verwalten der Überprüfung":::

1. Sie können _die Überprüfung erneut ausführen_, _die Überprüfung bearbeiten_ oder _die Überprüfung löschen_  

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage-scan-options.png" alt-text="Verwalten von Überprüfungsoptionen":::

1. Sie können eine _inkrementelle Überprüfung_ oder eine _vollständige Überprüfung_ erneut ausführen

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-full-inc.png" alt-text="Vollständige oder inkrementelle Überprüfung":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

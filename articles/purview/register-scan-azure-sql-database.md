---
title: Registrieren und Überprüfen von Azure SQL-Datenbank
description: In diesem Tutorial wird beschrieben, wie Sie Azure SQL-Datenbank überprüfen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/08/2021
ms.openlocfilehash: 83fd36ca1f352371e4d0717968512d791443d590
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515534"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrieren und Überprüfen von Azure SQL-Datenbank

In diesem Artikel erfahren Sie, wie Sie eine Datenquelle vom Typ „Azure SQL-Datenbank“ in Purview registrieren und eine Überprüfung dafür einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Datenquelle vom Typ „Azure SQL-Datenbank“ unterstützt die folgenden Funktionen:

- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure SQL-Datenbank

- **Herkunft** zwischen Datenressourcen für Kopier- und Datenflussaktivitäten von ADF

### <a name="known-limitations"></a>Bekannte Einschränkungen

> * Von Azure Purview werden auf der Registerkarte „Schema“ maximal 300 Spalten unterstützt, und es wird „Additional-Columns-Truncated“ angezeigt. 

## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie ein neues Purview-Konto, wenn Sie noch keines besitzen.

1. Netzwerkzugriff zwischen dem Purview-Konto und Azure SQL-Datenbank


### <a name="set-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Authentifizierung zum Überprüfen von Azure SQL-Datenbank Wenn Sie eine neue Authentifizierung erstellen müssen, müssen Sie [den Datenbankzugriff auf SQL-Datenbank](../azure-sql/database/logins-create-manage.md) autorisieren. Es gibt drei Authentifizierungsmethoden, die von Purview aktuell unterstützt werden:

- SQL-Authentifizierung
- Dienstprinzipal
- Verwaltete Identität

#### <a name="sql-authentication"></a>SQL-Authentifizierung

> [!Note]
> Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. **15 Minuten**, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

Sie können die Anleitung unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für Azure SQL-Datenbank zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Azure SQL-Datenbank ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="service-principal-and-managed-identity"></a>Dienstprinzipal und verwaltete Identität

Sie müssen mehrere Schritte ausführen, um zuzulassen, dass von Purview der Dienstprinzipal oder die **verwaltete Purview-Identität** zum Überprüfen von Azure SQL-Datenbank verwendet werden kann.

   > [!Note]
   > Für Purview werden für die Überprüfung die **Anwendungs-ID (Client)** und der **geheime Clientschlüssel** benötigt.

##### <a name="create-or-use-an-existing-service-principal"></a>Erstellen oder Verwenden eines vorhandenen Dienstprinzipals

> [!Note]
> Überspringen Sie diesen Schritt, wenn Sie die **verwaltete Identität** von Purview verwenden.

Zur Nutzung eines Dienstprinzipals können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen erstellen. 

> [!Note]
> Führen Sie die folgenden Schritte aus, falls Sie einen neuen Dienstprinzipal erstellen müssen:
> 1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
> 1. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
> 1. Wählen Sie **App-Registrierungen** aus.
> 1. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.
> 1. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).
> 1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
> 1. Wählen Sie als Umleitungs-URI die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder geschäftliche URL handeln.
> 1. Klicken Sie anschließend auf **Registrieren**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurieren der Azure AD-Authentifizierung im Datenbankkonto

Der Dienstprinzipal oder die verwaltete Identität muss über die Berechtigung zum Abrufen von Metadaten für die Datenbank, die Schemas und die Tabellen verfügen. Darüber hinaus muss das Abfragen der Tabellen möglich sein, damit Stichproben für die Klassifizierung genommen werden können.

- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Bei Verwendung einer verwalteten Identität verfügt Ihr Purview-Konto über eine eigene verwaltete Identität, bei der es sich im Grunde um den Purview-Namen handelt, den Sie bei der Erstellung eingegeben haben. Sie müssen einen Azure AD-Benutzer in Azure SQL-Datenbank erstellen, indem Sie genau den Namen der verwalteten Identität von Purview oder Ihren eigenen Dienstprinzipal angeben. Führen Sie dazu die Schritte im Tutorial unter [Erstellen des Dienstprinzipalbenutzers in Azure SQL-Datenbank](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database) aus. Sie müssen der Identität die richtige Berechtigung (z. B. `db_datareader`) zuweisen. SQL-Beispielsyntax für das Erstellen eines Benutzers und Gewähren der Berechtigung:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` ist Ihr eigener Dienstprinzipal oder eine verwaltete Identität von Purview. Weitere Informationen finden Sie unter den [festen Datenbankrollen und ihren Möglichkeiten](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Hinzufügen des Dienstprinzipals zum Schlüsseltresor und zu den Anmeldeinformationen von Purview

> [!Note]
> Wenn Sie die **verwaltete Identität** von Purview verwenden möchten, können Sie diesen Schritt überspringen, da die standardmäßige verwaltete Identität von Purview bereits in den Anmeldeinformationen **Purview-MSI** enthalten ist.

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

### <a name="firewall-settings"></a>Firewalleinstellungen

Wenn auf Ihrem Datenbankserver eine Firewall aktiviert ist, müssen Sie die Firewall aktualisieren, um den Zugriff auf eine der folgenden Arten zu ermöglichen:

1. Lassen Sie Azure-Verbindungen über die Firewall zu.
1. Installieren Sie eine selbstgehostete Integration Runtime, und gewähren Sie ihr Zugriff über die Firewall.

#### <a name="allow-azure-connections"></a>Zulassen von Azure-Verbindungen

Durch Zulassen von Azure-Verbindungen kann Azure Purview den Server erreichen und verbinden, ohne die Firewall selbst zu aktualisieren. Sie können die Schrittanleitung zum [Herstellen von Verbindungen aus Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure) befolgen.

1. Navigieren Sie zu Ihrem Datenbankkonto.
1. Wählen Sie auf der Seite **Übersicht** den Servernamen aus.
1. Wählen Sie **Sicherheit > Firewalls und virtuelle Netzwerke** aus.
1. Wählen Sie für **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** die Option **Ja** aus.

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Azure-Diensten und -Ressourcen den Zugriff auf diesen Server erlauben" border="true":::

#### <a name="self-hosted-integration-runtime"></a>Selbstgehostete Integration Runtime

Eine selbstgehostete Integration Runtime (SHIR) kann auf einem Computer installiert werden, um eine Verbindung mit einer Ressource in einem privaten Netzwerk herzustellen.

1. [Erstellen und installieren Sie eine selbstgehostete Integration Runtime](./manage-integration-runtimes.md) auf einem privaten Computer oder einem Computer im gleichen VNet wie der Datenbankserver.
1. Überprüfen Sie die Firewall des Datenbankservers, um sicherzustellen, dass der SHIR-Computer über die Firewall Zugriff hat. Fügen Sie die IP-Adresse des Computers hinzu, wenn er noch keinen Zugriff hat.
1. Wenn sich Ihr Azure SQL-Server hinter einem privaten Endpunkt oder in einem VNet befindet, können Sie einen [privaten Endpunkt für die Erfassung](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) verwenden, um die End-to-End-Netzwerkisolation sicherzustellen.

## <a name="register-an-azure-sql-database-data-source"></a>Registrieren einer Datenquelle vom Typ „Azure SQL-Datenbank“

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Azure SQL-Datenbank-Instanz zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure SQL-Datenbank** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Azure SQL Database)** (Quellen registrieren (Azure SQL-Datenbank)) wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.
1. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * Beim Löschen der Überprüfung werden nicht die Ressourcen aus vorherigen Überprüfungen von Azure SQL-Datenbank gelöscht.
> * Die Ressource wird nicht mehr mit Schemaänderungen aktualisiert, wenn die Quelltabelle geändert wird, und die Quelltabelle wird nach dem Bearbeiten der Beschreibung auf der Registerkarte „Schema“ von Purview erneut überprüft.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
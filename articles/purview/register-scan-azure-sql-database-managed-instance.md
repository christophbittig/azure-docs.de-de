---
title: Registrieren und Überprüfen einer verwalteten Azure SQL-Datenbank-Instanz
description: In diesem Tutorial wird beschrieben, wie Sie eine verwaltete Azure SQL-Datenbank-Instanz in Azure Purview überprüfen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 05/08/2021
ms.openlocfilehash: c633d95c615298876a8bedd76ecbd9c96a4f54da
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209686"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registrieren und Überprüfen einer verwalteten Azure SQL-Datenbank-Instanz

In diesem Artikel erfahren Sie, wie Sie eine Datenquelle vom Typ „Verwaltete Azure SQL-Datenbank-Instanz“ in Purview registrieren und eine Überprüfung dafür einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Datenquelle vom Typ „Verwaltete Azure SQL-Datenbank-Instanz“ unterstützt die folgenden Funktionen:

- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in einer verwalteten Azure SQL-Datenbank-Instanz

- **Herkunft** zwischen Datenressourcen für Kopier- und Datenflussaktivitäten von ADF

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie ein neues Purview-Konto, wenn Sie noch keines besitzen.

- [Konfigurieren des öffentlichen Endpunkts in der verwalteten Azure SQL-Instanz](../azure-sql/managed-instance/public-endpoint-configure.md)

    > [!Note]
    > Wir unterstützen jetzt die Überprüfung von verwalteten Azure SQL-Datenbank-Instanzen, die mit privaten Endpunkten konfiguriert sind, mithilfe von privaten Azure Purview-Erfassungsendpunkte und einer selbstgehosteten Integration Runtime-VM. Weitere Informationen zu den Voraussetzungen finden Sie unter [Privates und sicheres Herstellen einer Verbindung mit Azure Purview und Überprüfen von Datenquellen](./catalog-private-link-end-to-end.md).

### <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Authentifizierung zum Überprüfen einer verwalteten Azure SQL-Datenbank-Instanz Wenn Sie eine neue Authentifizierung erstellen müssen, müssen Sie [den Datenbankzugriff auf die verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/database/logins-create-manage.md) autorisieren. Es gibt drei Authentifizierungsmethoden, die von Purview aktuell unterstützt werden:

- SQL-Authentifizierung
- Dienstprinzipal
- Verwaltete Identität

#### <a name="sql-authentication"></a>SQL-Authentifizierung

> [!Note]
> Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. **15 Minuten**, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

Sie können die Anleitung unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für die verwaltete Azure SQL-Datenbank-Instanz zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für die verwaltete Azure SQL-Datenbank-Instanz ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="service-principal-and-managed-identity"></a>Dienstprinzipal und verwaltete Identität

Um Purview die Überprüfung der verwalteten Azure SQL-Datenbank-Instanz mithilfe eines Dienstprinzipals zu ermöglichen, müssen mehrere Schritte ausgeführt werden.

##### <a name="create-or-use-an-existing-service-principal"></a>Erstellen oder Verwenden eines vorhandenen Dienstprinzipals

> [!Note]
> Überspringen Sie diesen Schritt, wenn Sie die **verwaltete Identität** verwenden.

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
- Erstellen Sie einen Azure AD-Benutzer in der verwalteten Azure SQL-Datenbank-Instanz, indem Sie die Voraussetzungen erfüllen und das Tutorial unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) durcharbeiten.
- Zuweisen der Berechtigung `db_datareader` zur Identität

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Hinzufügen des Dienstprinzipals zum Schlüsseltresor und zu den Anmeldeinformationen von Purview

> [!Note]
> Wenn Sie die **verwaltete Identität** verwenden möchten, können Sie diesen Schritt überspringen, da die Purview-Standardidentität bereits in **Purview-MSI** enthalten ist.

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registrieren einer Datenquelle vom Typ „Verwaltete Azure SQL-Datenbank-Instanz“

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie **Verwaltete Azure SQL-Datenbank-Instanz** und anschließend **Weiter** aus.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Einrichten der SQL-Datenquelle":::

1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.

1. Geben Sie den **vollqualifizierten Domänennamen des öffentlichen Endpunkts** und die **Portnummer** ein. Wählen Sie anschließend **Registrieren** aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Hinzufügen einer verwalteten Azure SQL-Datenbank-Instanz":::

    Beispiel: `foobar.public.123.database.windows.net,3342`

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

1. Wählen Sie als Quelle die verwaltete Azure SQL-Datenbank-Instanz aus, die Sie registriert haben.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Tabellen festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> Beim Löschen der Überprüfung werden nicht die Ressourcen aus vorherigen Überprüfungen der verwalteten Azure SQL-Datenbank-Instanz gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

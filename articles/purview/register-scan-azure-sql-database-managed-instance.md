---
title: Verbinden mit und Verwalten einer verwalteten Azure SQL-Datenbank-Instanz
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit einer verwalteten Azure SQL-Datenbank-Instanz herstellen und mit den Features von Purview die verwaltete Azure SQL-Datenbank-Instanz überprüfen und verwalten.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0f1b570ce00a371a4f30e890eed771fa15c22174
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841864"
---
# <a name="connect-to-and-manage-an-azure-sql-database-managed-instance-in-azure-purview"></a>Verbinden mit und Verwalten einer verwalteten Azure SQL-Datenbank-Instanz in Azure Purview

In diesem Artikel wird beschrieben, wie Sie in Azure Purview eine verwaltete Azure SQL-Datenbank-Instanz registrieren, authentifizieren und mit ihr interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einleitungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | Nein | nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf unserer Seite zu [Azure Purview-Berechtigungen](catalog-permissions.md).

* [Konfigurieren des öffentlichen Endpunkts in der verwalteten Azure SQL-Instanz](../azure-sql/managed-instance/public-endpoint-configure.md)

    > [!Note]
    > Wir unterstützen jetzt die Überprüfung von verwalteten Azure SQL-Datenbank-Instanzen, die mit privaten Endpunkten konfiguriert sind, mithilfe von privaten Azure Purview-Erfassungsendpunkte und einer selbstgehosteten Integration Runtime-VM.
    > Weitere Informationen zu den Voraussetzungen finden Sie unter [Privates und sicheres Herstellen einer Verbindung mit Azure Purview und Überprüfen von Datenquellen](./catalog-private-link-end-to-end.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie eine verwaltete Azure SQL-Datenbank-Instanz in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Wenn Sie eine neue Authentifizierung erstellen müssen, müssen Sie [den Datenbankzugriff auf die verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/database/logins-create-manage.md) autorisieren. Es gibt drei Authentifizierungsmethoden, die von Purview aktuell unterstützt werden:

- [Verwaltete Identität](#managed-identity-to-register)
- [Dienstprinzipal](#service-principal-to-register)
- [SQL-Authentifizierung](#sql-authentication-to-register)

#### <a name="managed-identity-to-register"></a>Zu registrierende verwaltete Identität

Ihre verwaltete Purview-Identität ist die Identität des Purview-Kontos, mit dem Sie sich wie beliebige andere Benutzer, Gruppen oder Dienstprinzipale authentifizieren können.

Sie können die Objekt-ID Ihrer verwalteten Identität im Azure-Portal finden, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im Menü auf der linken Seite die Registerkarte **Eigenschaften** aus.
1. Wählen Sie den Wert für **Objekt-ID der verwalteten Identität** aus, und kopieren Sie ihn.

Die verwaltete Identität benötigt die Berechtigung zum Abrufen von Metadaten für die Datenbank, Schemas und Tabellen und zum Abfragen der Tabellen zwecks Klassifizierung.
- Erstellen Sie einen Azure AD-Benutzer in der verwalteten Azure SQL-Datenbank-Instanz, indem Sie die Voraussetzungen erfüllen und das Tutorial unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) durcharbeiten.
- Weisen Sie der Identität die Berechtigung `db_datareader` zu.

#### <a name="service-principal-to-register"></a>Zu registrierender Dienstprinzipal

Um Purview die Überprüfung der verwalteten Azure SQL-Datenbank-Instanz mithilfe eines Dienstprinzipals zu ermöglichen, müssen mehrere Schritte ausgeführt werden.

#### <a name="create-or-use-an-existing-service-principal"></a>Erstellen oder Verwenden eines vorhandenen Dienstprinzipals

Zur Nutzung eines Dienstprinzipals können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen erstellen. Wenn Sie einen vorhandenen Dienstprinzipal verwenden, fahren Sie mit dem nächsten Schritt fort.
Führen Sie die folgenden Schritte aus, falls Sie einen neuen Dienstprinzipal erstellen müssen:

 1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
 1. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
 1. Wählen Sie **App-Registrierungen** aus.
 1. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.
 1. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).
 1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
 1. Wählen Sie als Umleitungs-URI die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder geschäftliche URL handeln.
 1. Klicken Sie anschließend auf **Registrieren**.

#### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurieren der Azure AD-Authentifizierung im Datenbankkonto

Der Dienstprinzipal muss über die Berechtigung zum Abrufen von Metadaten für die Datenbank, Schemas und Tabellen verfügen. Darüber hinaus muss das Abfragen der Tabellen möglich sein, damit Stichproben für die Klassifizierung genommen werden können.
- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Erstellen Sie einen Azure AD-Benutzer in der verwalteten Azure SQL-Datenbank-Instanz, indem Sie die Voraussetzungen erfüllen und das Tutorial unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) durcharbeiten.
- Weisen Sie der Identität die Berechtigung `db_datareader` zu.

#### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Hinzufügen des Dienstprinzipals zum Schlüsseltresor und zu den Anmeldeinformationen von Purview

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="sql-authentication-to-register"></a>SQL-Authentifizierung für die Registrierung

> [!Note]
> Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. **15 Minuten**, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

Sie können die Anleitung unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für die verwaltete Azure SQL-Datenbank-Instanz zu erstellen, falls Sie noch keinen besitzen. Sie benötigen **Benutzername** und **Kennwort** für die nächsten Schritte.

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für die verwaltete Azure SQL-Datenbank-Instanz ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den **Benutzernamen** und das **Kennwort** zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu [Purview Studio](https://web.purview.azure.com/resource/).

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie **Verwaltete Azure SQL-Datenbank-Instanz** und anschließend **Weiter** aus.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Einrichten der SQL-Datenquelle":::

1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Servername** den entsprechenden Server aus.

1. Geben Sie den **vollqualifizierten Domänennamen des öffentlichen Endpunkts** und die **Portnummer** ein. Wählen Sie anschließend **Registrieren** aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Hinzufügen einer verwalteten Azure SQL-Datenbank-Instanz":::

    Beispiel: `foobar.public.123.database.windows.net,3342`

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um eine verwaltete Azure SQL-Datenbank-Instanz so zu überprüfen, dass Ressourcen automatisch identifiziert und Ihre Daten klassifiziert werden. Weitere Informationen zur Überprüfung im Allgemeinen finden Sie in unserer [Einführung in Scans und Datenerfassung](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

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

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

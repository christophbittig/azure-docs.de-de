---
title: Verbinden und Verwalten dedizierter SQL Pools (ehemals SQL DW)
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit dedizierten SQL Pools (ehemals SQL DW) in Azure Purview herstellen und die Funktionen von Purview verwenden, um Ihre dedizierten SQL Pools zu überprüfen und zu verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0978293d82de908ca31706fa40ebfdc489fab8fd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848854"
---
# <a name="connect-to-and-manage-dedicated-sql-pools-in-azure-purview"></a>Verbinden und Verwalten dedizierter SQL Pools in Azure Purview

In diesem Artikel wird beschrieben, wie Sie dedizierte SQL Pools (ehemals SQL DW) registrieren und wie Sie dedizierte SQL Pools in Azure Purview authentifizieren und mit ihnen interagieren können. Weitere Informationen zu Azure Purview finden Sie im [Einleitungsartikel](overview.md)

> [!NOTE]
> Wenn Sie eine dedizierte SQL-Datenbank in einem Synapse-Arbeitsbereich registrieren und scannen möchten, gehen Sie wie [hier](register-scan-synapse-workspace.md) beschrieben vor.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| [Ja](#scan)| [Ja](#scan)| [Ja](#scan)| Nein | nein** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

### <a name="known-limitations"></a>Bekannte Einschränkungen

* Von Azure Purview werden auf der Registerkarte „Schema“ maximal 300 Spalten unterstützt, und es wird „Additional-Columns-Truncated“ angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie dedizierte SQL Pools in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/)registriert werden.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Es gibt drei Möglichkeiten, die Authentifizierung für Azure Blob Storage einzurichten:

- [Verwaltete Identität](#managed-identity-to-register) (empfohlen)
- [Dienstprinzipal](#service-principal-to-register)
- [SQL-Authentifizierung](#sql-authentication-to-register)

    > [!Note]
    > Neue Anmeldenamen können nur mit den Anmeldenamen des Serverebenenprinzipals (im Bereitstellungsprozess erstellt) oder von Mitgliedern der `loginmanager`-Datenbankrolle in der Masterdatenbank erstellt werden. Nach dem Gewähren der Berechtigung dauert es ca. 15 Minuten, bis im Purview-Konto die entsprechenden Berechtigungen zum Überprüfen der Ressourcen verfügbar sind.

#### <a name="managed-identity-to-register"></a>Zu registrierende verwaltete Identität

Ihr Purview-Konto verfügt über eine eigene verwaltete Identität, bei der es sich im Grunde genommen um den Purview-Namen handelt, den Sie bei der Erstellung eingegeben haben. Erstellen Sie einen Azure AD Benutzer im dedizierten SQL-Pool mit genau dem Namen der verwalteten Identität von Purview, indem Sie die Voraussetzungen und das Tutorial [Azure AD Benutzer mit Azure AD Anwendungen Erstellen](../azure-sql/database/authentication-aad-service-principal-tutorial.md)befolgen.

SQL-Beispielsyntax für das Erstellen eines Benutzers und das Gewähren der Berechtigung:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

Die Authentifizierung muss über die Berechtigung zum Abrufen von Metadaten für die Datenbank, die Schemata und die Tabellen verfügen. Darüber hinaus muss das Abfragen der Tabellen möglich sein, damit Stichproben für die Klassifizierung genommen werden können. Wir empfehlen Ihnen, der Identität die Berechtigung `db_datareader` zuzuweisen.

#### <a name="service-principal-to-register"></a>Zu registrierender Dienstprinzipal

Sie können für Überprüfungen eine vorhandene Dienstprinzipalauthentifizierung verwenden oder eine neue erstellen.

Führen Sie die folgenden Schritte aus, falls Sie einen neuen Dienstprinzipal erstellen möchten:
 1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
 1. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
 1. 1. Wählen Sie **App-Registrierungen** aus.
 1. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.
 1. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).
 1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
 1. Wählen Sie als Umleitungs-URI die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder geschäftliche URL handeln.
 1. Klicken Sie anschließend auf **Registrieren**.

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

##### <a name="granting-the-service-principal-access"></a>Erteilen des Zugriffs für den Dienstprinzipal

Darüber hinaus müssen Sie auch im dedizierten SQL-Pool einen Azure AD-Benutzer erstellen, indem Sie die Voraussetzungen unter [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](../azure-sql/database/authentication-aad-service-principal-tutorial.md) erfüllen und das Tutorial durcharbeiten. SQL-Beispielsyntax für das Erstellen eines Benutzers und das Gewähren der Berechtigung:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Für Purview werden für die Überprüfung die **Anwendungs-ID (Client)** und der **geheime Clientschlüssel** benötigt.

#### <a name="sql-authentication-to-register"></a>SQL-Authentifizierung für die Registrierung

Sie können die Anleitung unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) befolgen, um einen Anmeldenamen für Ihren dedizierten SQL-Pool (vormals SQL DW) zu erstellen, falls Sie noch keinen besitzen.

Wenn **SQL-Authentifizierung** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihr Kennwort eingeben und im Schlüsseltresor speichern:

1. Beschaffen Sie das Kennwort für Ihren SQL-Anmeldenamen.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Kennwort* für Ihren SQL-Anmeldenamen ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Zum Schluss[erstellen Sie neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), wobei Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Um einen neuen dedizierten SQL-Pool in Purview zu registrieren, gehen Sie wie folgt vor:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter **Quellen registrieren** die Option **Dedizierter Azure-SQL-Pool (vormals SQL DW)** aus.
1. Wählen Sie **Weiter** aus.

Gehen Sie auf dem Bildschirm **Quellen registrieren** wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
2. Wählen Sie Ihr Azure-Abonnement aus, um dedizierte SQL-Pools zu filtern.
3. Wählen Sie Ihren dedizierten SQL-Pool.
4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
5. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um dedizierte SQL Pools zu überprüfen, um Ressourcen automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Überprüfen im Allgemeinen finden Sie in unserer [Einführung in Scans und Datenerfassung](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte, dedizierte SQL-Poolquelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Tabellen festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

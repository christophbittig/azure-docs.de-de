---
title: Registrieren und Überprüfen von Azure Data Lake Storage (ADLS) Gen1
description: In diesem Artikel wird der Prozess zum Registrieren einer Azure Data Lake Storage Gen1-Datenquelle in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Data Lake Storage Gen1-Quelle
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 743db90c66f4499b356ca0413ab1827aa73d5f2e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852748"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Verbinden zu Azure Data Lake Gen1 in Azure Purview

In diesem Artikel wird der Prozess zum Registrieren einer Azure Data Lake Storage Gen1-Datenquelle in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Data Lake Storage Gen1-Quelle.

> [!Note]
> Azure Data Lake Storage Gen2 ist jetzt allgemein verfügbar. Es wird empfohlen, ab sofort diese SKU zu verwenden. Weitere Informationen hierzu finden Sie auf der [Produktseite](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)|[Ja](#scan) | [Ja](#scan)|[Ja](#scan)| Nein |Eingeschränkt** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt können Sie eine Datenquelle ADLS Gen1 registrieren und einen geeigneten Authentifizierungsmechanismus einrichten, um eine erfolgreiche Überprüfung der Datenquelle sicherzustellen.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Es ist wichtig, die Datenquelle in Azure Purview zu registrieren, bevor Sie eine Überprüfung für die Datenquelle einrichten.

1. Gehen Sie zum [Azure-Portal](https://portal.azure.com), navigieren Sie zur Seite **Purview-Konten,** und klicken Sie auf Ihr _Purview-Konto_

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Screenshot: Purview-Konto zum Registrieren der Datenquelle":::

1. **Öffnen Sie Purview Studio** uns navigieren Sie zu **Data Map --> Quellen**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Screenshot: Link zum Öffnen von Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="Screenshot: Navigieren zum Link Quellen in der Data Map":::

1. Erstellen Sie die [Sammlungshierarchie](./quickstart-create-collection.md) mithilfe des **Menüs Sammlungen**, und weisen Sie den einzelnen untergeordneten Sammlungen nach Bedarf Berechtigungen zu

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="Screenshot: Sammlungsmenü zum Erstellen einer Sammlungshierarchie":::

1. Navigieren Sie im Menü **Quellen** zur entsprechenden Sammlung, und klicken Sie auf das Symbol **Registrieren**, um eine neue ADLS Gen1 zu registrieren

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="Screenshot: Sammlung zum Registrieren der Datenquelle":::

1. Wählen Sie die **Datenquelle Azure Data Lake Storage Gen1** aus, und klicken Sie auf **Weiter**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="Screenshot, der die Auswahl der Datenquelle ermöglicht":::

1. Geben Sie einen geeigneten **Namen** für die Datenquelle an, wählen Sie das entsprechende **Azure-Abonnement**, den vorhandenen **Data Lake-Store Kontonamen** und die **Sammlung** aus, und klicken Sie auf **Übernehmen**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="Screenshot mit den Details, die eingegeben werden müssen, um die Datenquelle zu registrieren":::

1. Das ADLS Gen1-Speicherkonto wird unter der ausgewählten Sammlung angezeigt

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="Screenshot der Datenquelle, die der Sammlung zugeordnet ist, um die Überprüfung zu initiieren":::

## <a name="scan"></a>Überprüfen

### <a name="prerequisites-for-scan"></a>Voraussetzungen für eine Überprüfung

Um Zugriff zum Überprüfen der Datenquelle zu haben, muss eine Authentifizierungsmethode im ADLS Gen1 Storage konfiguriert werden.
Die folgenden Optionen werden unterstützt:

> [!Note]
> Wenn Sie für das Speicherkonto eine Firewall aktiviert haben, müssen Sie beim Einrichten einer Überprüfung als Authentifizierungsmethode Verwaltete Identität verwenden.

* **Verwaltete Identität (empfohlen):** Sobald das Azure Purview-Konto erstellt wurde, wird automatisch eine **verwaltete Systemidentität** in Azure AD Mandanten erstellt. Je nach Ressourcentyp sind bestimmte RBAC-Rollenzuweisungen erforderlich, damit die Azure Purview-MSI die Überprüfungen durchführen kann.

* **Dienstprinzipal** – Bei dieser Methode können Sie einen neuen Dienstprinzipal erstellen oder einen vorhandenen Dienstprinzipal in Ihrem Azure Active Directory verwenden.

### <a name="authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

#### <a name="using-managed-identity-for-scanning"></a>Verwenden der verwalteten Identität für die Überprüfung

Es ist wichtig, Ihrem Purview-Konto die Berechtigung zum Überprüfen der ADLS Gen1 Datenquelle zu erteilen. Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!Note]
> Sie müssen Besitzer des Abonnements sein, um einer Azure-Ressource eine verwaltete Identität hinzufügen zu können.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, die Ressourcengruppe oder die Ressource (z. B. ein Azure Data Lake Storage Gen1-Speicherkonto), das den Katalog überprüfen soll.
1. Klicken Sie auf **Übersicht**, und wählen Sie dann **Daten-Explorer** aus.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Screenshot: Speicherkonto":::

1. Klicken Sie im oberen Navigationsbereich auf **Zugriff**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Screenshot: Daten-Explorer für das Speicherkonto":::

1. Klicken Sie auf **Auswahl** und fügen Sie _Purview Katalog_ hinzu in der **Auswahl für Benutzer oder Gruppe**.
1. Wählen Sie die Berechtigungen **Lesen** und **Ausführen** aus. Achten Sie darauf, dass Sie in den Hinzufügeoptionen **Diesen Ordner und alle untergeordneten Ordner** sowie **Einen Zugriffsberechtigungseintrag und einen Standardberechtigungseintrag** auswählen, wie im folgenden Screenshot zu sehen. Klicken Sie im Menü „Einstellungen“ auf **OK**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="Screenshot: Details zum Zuweisen von Berechtigungen für das Purview-Konto":::

> [!Tip]
> Ein **Zugriffsberechtigungseintrag** ist ein Berechtigungseintrag für _aktuelle_ Dateien und Ordner. Ein **Standardberechtigungseintrag** ist ein Berechtigungseintrag, der von neuen Dateien und Ordnern _geerbt_ wird.
Wenn Sie Berechtigungen nur für aktuell vorhandene Dateien erteilen möchten, **wählen Sie einen Zugriffsberechtigungseintrag aus**.
Wenn Sie die Berechtigung zum Überprüfen von zukünftig hinzugefügten Dateien und Ordnern erteilen möchten, **schließen Sie einen Standardberechtigungseintrag ein**.

#### <a name="using-service-principal-for-scanning"></a>Verwenden des Dienstprinzipals für die Überprüfung

##### <a name="creating-a-new-service-principal"></a>Erstellen eines neuen Dienstprinzipals

Wenn Sie [einen neuen Dienstprinzipal erstellen](./create-service-principal-azure.md)müssen, müssen Sie eine Anwendung in Ihrem Azure AD Mandanten registrieren und den Zugriff auf den Dienstprinzipal in Ihren Datenquellen bereitstellen. Ihr Azure AD allgemeiner Administrator oder andere Rollen, z. B. Anwendungsadministrator, können diesen Vorgang ausführen.

##### <a name="getting-the-service-principals-application-id"></a>Abrufen der Anwendungs-ID des Dienstprinzipals

1. Kopieren Sie die **Anwendungs-ID (Client)**, die in der **Übersicht** über den bereits erstellten [_Dienstprinzipal_](./create-service-principal-azure.md) vorhanden ist

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="Screenshot: Anwendungs-ID (Client) für den Dienstprinzipal":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>Gewähren des Zugriffs auf Ihr ADLS Gen1-Konto für den Dienstprinzipal

Es ist wichtig, ihrem Dienstprinzipal die Berechtigung zum Überprüfen der ADLS Gen2 zu erteilen. Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!Note]
> Sie müssen Besitzer des Abonnements sein, um einer Azure-Ressource eine verwaltete Identität hinzufügen zu können.

1. Geben Sie dem Dienstprinzipal Zugriff auf das Speicherkonto, indem Sie das Speicherkonto öffnen und auf **Übersicht** --> **Daten-Explorer** klicken

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Screenshot: Speicherkonto":::

1. Klicken Sie im oberen Navigationsbereich auf **Zugriff**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Screenshot: Daten-Explorer für das Speicherkonto":::

1. Klicken Sie in der **Auswahl Benutzer oder Gruppe** auf _Auswahl_ und fügen sie den **Dienstprinzipal** hinzu.
1. Wählen Sie die Berechtigungen **Lesen** und **Ausführen** aus. Achten Sie darauf, dass Sie in den Hinzufügeoptionen **Diesen Ordner und alle untergeordneten Ordner** sowie **Einen Zugriffsberechtigungseintrag und einen Standardberechtigungseintrag** auswählen, wie im folgenden Screenshot zu sehen. Klicken Sie im Menü „Einstellungen“ auf **OK**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="Screenshot: Details zum Zuweisen von Berechtigungen für den Dienstprinzipal":::

### <a name="creating-the-scan"></a>Erstellen der Überprüfung

1. Öffnen Sie Ihr **Purview-Konto,** und klicken Sie auf **Purview Studio öffnen**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Screenshot: Öffnen von Purview Studio":::

1. Navigieren Sie zu **Datenzuordnungsquellen,** -->  um die Sammlungshierarchie anzuzeigen

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Screenshot: Sammlungshierarchie":::

1. Klicken Sie unter der zuvor registrierten **ADLS Gen1 Datenquelle** auf das Symbol **Neue Überprüfung**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="Screenshot: Datenquelle mit dem neuen Scansymbol":::

#### <a name="if-using-managed-identity"></a>Bei Zugriff mithilfe einer verwalteten Identität

Geben Sie einen **Namen** für die Überprüfung an, wählen Sie unter **Anmeldeinformationen** die **MSI-Datei Purview** aus, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**.

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="Screenshot: Option Verwaltete Identität zum Ausführen der Überprüfung":::

#### <a name="if-using-service-principal"></a>Bei Verwendung eines Dienstprinzipals

1. Geben Sie einen **Namen** für die Überprüfung an, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und klicken Sie auf **+ Neu** unter den **Anmeldeinformationen**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="Screenshot: Option Dienstprinzipal":::

1. Wählen Sie die entsprechende **Schlüsseltresor-Verbindung** und den **geheimen Namen** aus, die beim Erstellen des _Dienstprinzipals_ verwendet wurden. Die **Dienstprinzipal-ID** ist die **Anwendungs-ID (Client)**, die wie oben angegeben kopiert wurde

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="Screenshot: Option Schlüsseltresor des Dienstprinzipals":::

1. Klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="Screenshot, der die Testverbindung für den Dienstprinzipal zeigt":::

### <a name="scoping-and-running-the-scan"></a>Definieren und Ausführen der Überprüfung

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner und Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wenn Sie einen neuen _Überprüfungsregelsatz_ erstellen,wählen Sie die **Dateitypen** aus, die in die Überprüfungsregel aufgenommen werden sollen. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="Überprüfen von Regelsatz-Dateitypen":::

1. Sie können die **Klassifizierungsregeln** auswählen, die in die Überprüfungsregel aufgenommen werden sollen

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="Überprüfen von Klassifizierungsregeln":::


    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="Überprüfen der Regelauswahl":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="Auslöser für die Überprüfung":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="Auswahl des Überprüfungsauslösers":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="Überprüfungs-Scan":::

### <a name="viewing-scan"></a>Anzeigen der Überprüfung

1. Navigieren Sie zur _Datenquelle_ in der _Sammlung_ und klicken Sie auf **Details anzeigen**, um den Status der Überprüfung zu checken

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="Anzeigen der Überprüfung":::

1. Die Scandetails geben den Fortschritt der Überprüfung im **Status der letzten Ausführung** an sowie die Anzahl der _gescannten_ und _klassifizierten_ Objekte

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="Überprüfungsdetails anzeigen":::

1. Der **Status Letzte Ausführung** wird in **In Bearbeitung** und anschließend in **Abgeschlossen** aktualisiert, sobald die gesamte Überprüfung erfolgreich ausgeführt wurde

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="Anzeigen der Überprüfung in Bearbeitung":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="Die abgeschlossene Überprüfung anzeigen":::

### <a name="managing-scan"></a>Verwalten der Überprüfung

Überprüfungen können verwaltet oder nach Abschluss erneut ausgeführt werden.

1. Klicken Sie auf den **Überprüfungsnamen**, um die Überprüfung zu verwalten

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="Verwalten der Überprüfung":::

1. Sie können _die Überprüfung erneut ausführen_, _die Überprüfung bearbeiten_ oder _die Überprüfung löschen_  

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-options.png" alt-text="Verwalten von Scanoptionen":::

    > [!NOTE]
    > * Beim Löschen Ihrer Überprüfung werden nicht die Katalogressourcen aus den vorherigen Überprüfungen gelöscht.
    > * Die Ressource wird nicht mehr mit Schemaänderungen aktualisiert, wenn die Quelltabelle geändert wurde und Sie die Quelltabelle nach dem Bearbeiten der Beschreibung auf der Registerkarte „Schema“ von Purview erneut überprüfen.

1. Sie können eine _inkrementelle Überprüfung_ oder eine _vollständige Überprüfung_ erneut ausführen.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-full-inc-scan.png" alt-text="Verwalten der vollständigen oder inkrementellen Überprüfung":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-results.png" alt-text="Ergebnisse der Überprüfung verwalten":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

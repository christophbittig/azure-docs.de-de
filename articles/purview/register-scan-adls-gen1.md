---
title: Registrieren und Überprüfen von Azure Data Lake Storage (ADLS) Gen1
description: In diesem Tutorial wird beschrieben, wie Sie Daten aus Azure Data Lake Storage Gen1 in Azure Purview überprüfen.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 1c4801814e77efdb681f32ea35d4dfb68618900b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346502"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registrieren und Überprüfen von Azure Data Lake Storage Gen1

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Storage Gen1 als Datenquelle in Azure Purview registrieren und eine Überprüfung dafür einrichten.

> [!Note]
> Azure Data Lake Storage Gen2 ist jetzt allgemein verfügbar. Es wird empfohlen, ab sofort diese SKU zu verwenden. Weitere Informationen hierzu finden Sie auf der [Produktseite](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Datenquelle vom Typ „Azure Data Lake Storage Gen1“ unterstützt die folgenden Funktionen:

- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure Data Lake Storage Gen1

- **Herkunft** zwischen Datenobjekten für Copy-/Dataflow-Aktivitäten von ADF

Für Dateitypen wie CSV, TSV, PSV und SSV wird das Schema extrahiert, wenn die folgenden Logiken vorhanden sind:

1. Werte in der ersten Zeile sind nicht leer.
2. Werte in der ersten Zeile sind eindeutig.
3. Werte in der ersten Zeile sind weder ein Datum noch eine Zahl.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Azure Purview-Datenquellenadministrator sein.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die folgenden Authentifizierungsmethoden werden für Azure Data Lake Storage Gen1 unterstützt:

- Verwaltete Identität
- Dienstprinzipal

### <a name="managed-identity-recommended"></a>Verwaltete Identität (empfohlen)

Für mehr Benutzerfreundlichkeit und Sicherheit sollten Sie die MSI von Purview für die Authentifizierung bei Ihrem Datenspeicher verwenden.

Wenn Sie eine Überprüfung mithilfe der MSI des Datenkatalogs einrichten, müssen Sie Ihrem Purview-Konto zunächst die Berechtigung zum Überprüfen der Datenquelle erteilen. Dieser Schritt muss *vor* dem Einrichten der Überprüfung durchgeführt werden, andernfalls tritt bei der Überprüfung ein Fehler auf.

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>Hinzufügen der Purview-MSI zu einem Azure Data Lake Storage Gen1-Konto

Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!Note]
> Sie müssen Besitzer des Abonnements sein, um einer Azure-Ressource eine verwaltete Identität hinzufügen zu können.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, die Ressourcengruppe oder die Ressource (z. B. ein Azure Data Lake Storage Gen1-Speicherkonto), das den Katalog überprüfen soll.

2. Klicken Sie auf **Übersicht**, und wählen Sie dann **Daten-Explorer** aus.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Zugriffssteuerung auswählen":::

3. Klicken Sie auf der oberen Navigationsleiste auf **Zugriff**.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klicken auf „Zugriff“":::

4. Klicken Sie auf **Hinzufügen**. Fügen Sie den **Purview-Katalog** unter „Benutzer oder Gruppen auswählen“ aus. Wählen Sie die Berechtigungen **Lesen** und **Ausführen** aus. Achten Sie darauf, dass Sie in den Hinzufügeoptionen **Diesen Ordner und alle untergeordneten Ordner** sowie **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** auswählen, wie im folgenden Screenshot zu sehen. Klicken Sie auf **OK**.
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="MSI-Authentifizierungsdetails":::
   
> [!Tip]
> Ein **Zugriffsberechtigungseintrag** ist ein Berechtigungseintrag für *aktuelle* Dateien und Ordner.
> Ein **Standardberechtigungseintrag** ist ein Berechtigungseintrag, der von neuen Dateien und Ordnern *geerbt* wird.
> 
> Wenn Sie Berechtigungen nur für aktuell vorhandene Dateien erteilen möchten, **wählen Sie einen Zugriffsberechtigungseintrag aus**.
> 
> Wenn Sie die Berechtigung zum Überprüfen von zukünftig hinzugefügten Dateien und Ordnern erteilen möchten, **schließen Sie einen Standardberechtigungseintrag ein**.
> 
> Weitere Informationen finden Sie in der [Dokumentation zu Berechtigungen](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

> [!Note]
> Nachdem Sie die Katalog-MSI in der Datenquelle hinzugefügt haben, warten Sie bis zu 15 Minuten, bis die Berechtigungen angewandt wurden, bevor Sie eine Überprüfung einrichten.

Nach etwa 15 Minuten sollte der Katalog über die erforderlichen Berechtigungen zum Überprüfen der Ressourcen verfügen.

### <a name="service-principal"></a>Dienstprinzipal

Wenn Sie einen Dienstprinzipal verwenden möchten, müssen Sie zunächst anhand der folgenden Schritte einen erstellen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

2. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.

3. Wählen Sie **App-Registrierungen** aus.

4. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.

5. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).

6. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.

7. Wählen Sie als **Umleitungs-URI** die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder funktionsfähige URL handeln.

8. Klicken Sie anschließend auf **Registrieren**.

9. Kopieren Sie die Werte für den Anzeigenamen und die Anwendungs-ID.

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Hinzufügen des Purview-Dienstprinzipals zu einem Azure Data Lake Storage Gen1-Konto

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, die Ressourcengruppe oder die Ressource (z. B. ein Azure Data Lake Storage Gen1-Speicherkonto), das den Katalog überprüfen soll.

2. Klicken Sie auf **Übersicht**, und wählen Sie dann **Daten-Explorer** aus.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Zugriffssteuerung auswählen":::

3. Klicken Sie auf der oberen Navigationsleiste auf **Zugriff**.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klicken auf „Zugriff“":::

4. Klicken Sie auf **Hinzufügen**. Fügen Sie unter „Benutzer oder Gruppe auswählen“ die **Dienstprinzipalanwendung** hinzu. Wählen Sie die Berechtigungen **Lesen** und **Ausführen** aus. Achten Sie darauf, dass Sie in den Hinzufügeoptionen **Diesen Ordner und alle untergeordneten Ordner** sowie **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** auswählen, wie im folgenden Screenshot zu sehen. Klicken Sie auf **OK**.
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="Details zur Dienstprinzipalauthentifizierung":::

> [!Tip]
> Ein **Zugriffsberechtigungseintrag** ist ein Berechtigungseintrag für *aktuelle* Dateien und Ordner.
> Ein **Standardberechtigungseintrag** ist ein Berechtigungseintrag, der von neuen Dateien und Ordnern *geerbt* wird.
>
> Wenn Sie Berechtigungen nur für aktuell vorhandene Dateien erteilen möchten, **wählen Sie einen Zugriffsberechtigungseintrag aus**.
>
> Wenn Sie die Berechtigung zum Überprüfen von zukünftig hinzugefügten Dateien und Ordnern erteilen möchten, **schließen Sie einen Standardberechtigungseintrag ein**.
>
> Weitere Informationen finden Sie in der [Dokumentation zu Berechtigungen](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registrieren von Azure Data Lake Storage Gen1 als Datenquelle

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues ADLS Gen1-Konto zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Datenkatalog.
2. Wählen Sie im linken Navigationsbereich **Data Map** aus.
3. Wählen Sie **Registrieren** aus.
4. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Data Lake Storage Gen1** aus. 
5. Wählen Sie **Weiter** aus.

Führen Sie auf dem Bildschirm „Register sources (Azure Data Lake Storage Gen1)“ (Quellen registrieren (Azure Data Lake Storage Gen1)) die folgenden Schritte aus:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
2. Wählen Sie Ihr Abonnement aus, um die Speicherkonten zu filtern.
3. Wählen Sie ein Speicherkonto aus.
4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
5. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

1. Wählen Sie die Azure Data Lake Storage Gen1-Quelle aus, die Sie registriert haben.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner und Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

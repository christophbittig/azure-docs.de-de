---
title: Registrieren und Überprüfen von Azure Data Lake Storage (ADLS) Gen2
description: In diesem Tutorial wird beschrieben, wie Sie Azure Data Lake Storage Gen2 überprüfen.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: fb277b2468d0cf4df5d28e412a5fb91f777b46bf
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342714"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registrieren und Überprüfen von Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Storage Gen2 als Datenquelle in Azure Purview registrieren und eine Überprüfung dafür einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Datenquelle vom Typ „Azure Data Lake Storage Gen2“ unterstützt die folgenden Funktionen:

- **Vollständige und inkrementelle Überprüfungen** zum Erfassen von Metadaten und Klassifizierungen in Azure Data Lake Storage Gen2

- **Herkunft** zwischen Datenobjekten für Copy-/Dataflow-Aktivitäten von ADF

Für Dateitypen wie CSV, TSV, PSV und SSV wird das Schema extrahiert, wenn die folgenden Logiken vorhanden sind:

1. Werte in der ersten Zeile sind nicht leer.
2. Werte in der ersten Zeile sind eindeutig.
3. Werte in der ersten Zeile sind weder ein Datum noch eine Zahl.

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die folgenden Authentifizierungsmethoden werden für Azure Data Lake Storage Gen2 unterstützt:

- Verwaltete Identität
- Dienstprinzipal
- Kontoschlüssel

#### <a name="managed-identity-recommended"></a>Verwaltete Identität (empfohlen)

Wenn Sie **Verwaltete Identität** auswählen, müssen Sie Ihrem Purview-Konto zum Einrichten der Verbindung zunächst die Berechtigung zum Überprüfen der Datenquelle erteilen:

1. Navigieren Sie zu Ihrem ADLS Gen2-Konto.
1. Wählen Sie im linken Navigationsmenü **Zugriffssteuerung (IAM)** aus. 
1. Klicken Sie auf **+ Hinzufügen**.
1. Legen Sie die **Rolle** auf **Leser von Speicherblobdaten** fest, und geben Sie unter **Auswählen** den Namen Ihres Azure Purview-Kontos ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

> [!Note]
> Weitere Informationen finden Sie in den Schritten unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md).

#### <a name="account-key"></a>Kontoschlüssel

Wenn **Kontoschlüssel** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihren Zugriffsschlüssel abrufen und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem ADLS Gen2-Speicherkonto.
1. Klicken Sie auf **Sicherheit + Netzwerkbetrieb > Zugriffsschlüssel**.
1. Kopieren Sie Ihren *Schlüssel*, und speichern Sie ihn für die nächsten Schritte.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Schlüssel* für Ihr Speicherkonto ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="service-principal"></a>Dienstprinzipal

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

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Gewähren des Zugriffs auf Ihr ADLS Gen2-Konto für den Dienstprinzipal

1. Navigieren Sie zu Ihrem Speicherkonto.
1. Wählen Sie im linken Navigationsmenü **Zugriffssteuerung (IAM)** aus. 
1. Klicken Sie auf **+ Hinzufügen**.
1. Legen Sie die **Rolle** auf **Leser von Speicherblobdaten** fest, und geben Sie unter **Auswählen** den Dienstprinzipalnamen oder die Objekt-ID ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihren Dienstprinzipal festzulegen.
### <a name="firewall-settings"></a>Firewalleinstellungen

> [!NOTE]
> Wenn Sie für das Speicherkonto eine Firewall aktiviert haben, müssen Sie beim Einrichten einer Überprüfung als Authentifizierungsmethode **Verwaltete Identität** verwenden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem ADLS Gen2-Konto
1. Navigieren Sie zu **Einstellungen > Netzwerk**.
1. Wählen Sie unter **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus.
1. Aktivieren Sie im Abschnitt **Ausnahmen** das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**, und wählen Sie dann **Speichern** aus.

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Screenshot der Firewalleinstellung":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Registrieren von Azure Data Lake Storage Gen2 als Datenquelle

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues ADLS Gen2-Konto zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
2. Klicken Sie im linken Navigationsbereich auf **Data Map**.
3. Wählen Sie **Registrieren** aus.
4. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Data Lake Storage Gen2** aus.
5. Wählen Sie **Weiter**.

Führen Sie auf dem Bildschirm **Register sources (Azure Data Lake Storage Gen2)** (Quellen registrieren (Azure Data Lake Storage Gen2)) die folgenden Schritte aus:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
2. Wählen Sie Ihr Abonnement aus, um die Speicherkonten zu filtern.
3. Wählen Sie ein Speicherkonto aus.
4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
5. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.

1. Wählen Sie die Azure Data Lake Storage Gen2-Quelle aus, die Sie registriert haben.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Anmeldeinformationen für die Verbindungsherstellung mit Ihrer Datenquelle aus.

   :::image type="content" source="media/register-scan-adls-gen2/set-up-scan-adls-gen2.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner oder Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-adls-gen2/gen2-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-adls-gen2/gen2-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-adls-gen2/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
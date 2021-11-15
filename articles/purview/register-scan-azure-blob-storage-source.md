---
title: Azure Blob Storage registrieren und scannen
description: In diesem Artikel wird der Prozess zum Registrieren einer Azure Blob Storage Datenquelle in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Blob Storage Gen2-Quelle
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d740ba049353a250468321741d90d1f8b7b88d2b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848987"
---
# <a name="connect-to-azure-blob-storage-in-azure-purview"></a>Verbinden Sie sich mit Azure Blob Storage in Azure Purview

In diesem Artikel wird der Prozess zum Registrieren eines Azure Blob Storage-Kontos in Azure Purview beschrieben, einschließlich Anweisungen zum Authentifizieren und Interagieren mit einer solchen Azure Blob Storage-Quelle

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)|[Ja](#scan) | [Ja](#scan)|[Ja](#scan)| Ja | Eingeschränkt** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

Für Dateitypen wie CSV, TSV, PSV und SSV wird das Schema extrahiert, wenn die folgenden Logiken vorhanden sind:

* Werte in der ersten Zeile sind nicht leer.
* Werte in der ersten Zeile sind eindeutig.
* Werte in der ersten Zeile sind weder ein Datum noch eine Zahl

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt können Sie das Azure Blob Storage-Konto registrieren und einen geeigneten Authentifizierungsmechanismus einrichten, um eine erfolgreiche Überprüfung der Datenquelle sicherzustellen.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Es ist wichtig, die Datenquelle in Azure Purview zu registrieren, bevor Sie eine Überprüfung für die Datenquelle einrichten.

1. Gehen Sie zum [Azure-Portal](https://portal.azure.com), navigieren Sie zur Seite **Purview-Konten** und klicken Sie auf Ihr _Purview-Konto_

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-purview-acct.png" alt-text="Screenshot: Purview-Konto zum Registrieren der Datenquelle":::

1. **Öffnen Sie Purview Studio** uns navigieren Sie zu **Data Map --> Quellen**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-open-purview-studio.png" alt-text="Screenshot: Link zum Öffnen von Purview Studio":::

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sources.png" alt-text="Screenshot: Navigieren zum Link Quellen in der Data Map":::

1. Erstellen Sie die [Sammlungshierarchie](./quickstart-create-collection.md) mithilfe des **Menüs Sammlungen**, und weisen Sie den einzelnen untergeordneten Sammlungen nach Bedarf Berechtigungen zu

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-collections.png" alt-text="Screenshot: Sammlungsmenü zum Erstellen einer Sammlungshierarchie":::

1. Navigieren Sie im Menü **Quellen** zur entsprechenden Sammlung, und klicken Sie auf das Symbol **Registrieren**, um eine neue Azure Blob Datenquelle zu registrieren

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-register-source.png" alt-text="Screenshot: Sammlung zum Registrieren der Datenquelle":::

1. Wählen Sie die **Azure Blob Storage** Datenquelle und dann **Weiter** aus

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-select-data-source.png" alt-text="Screenshot, der die Auswahl der Datenquelle ermöglicht":::

1. Geben Sie einen geeigneten **Namen** für die Datenquelle an, wählen Sie das entsprechende **Azure-Abonnement**, den vorhandenen **Azure Blob Storage Kontonamen** und die **Sammlung** aus, und klicken Sie auf **Übernehmen**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-data-source-details.png" alt-text="Screenshot mit den Details, die eingegeben werden müssen, um die Datenquelle zu registrieren":::

1. Das Azure Blog Storage-Konto wird unter der ausgewählten Sammlung angezeigt

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-data-source-collection.png" alt-text="Screenshot der Datenquelle, die der Sammlung zugeordnet ist, um die Überprüfung zu initiieren":::

## <a name="scan"></a>Überprüfen

### <a name="authentication-for-a-scan"></a>Authentifizierung für eine Überprüfung

Um Zugriff zum Überprüfen der Datenquelle zu haben, muss eine Authentifizierungsmethode im Azure Blob Storage-Konto konfiguriert werden.

Die folgenden Optionen werden unterstützt:

> [!Note]
> Wenn Sie für das Speicherkonto eine Firewall aktiviert haben, müssen Sie beim Einrichten einer Überprüfung als Authentifizierungsmethode Verwaltete Identität verwenden.

- **Verwaltete Identität (empfohlen):** Sobald das Azure Purview-Konto erstellt wurde, wird automatisch eine **verwaltete Systemidentität** in Azure AD Mandanten erstellt. Je nach Ressourcentyp sind bestimmte RBAC-Rollenzuweisungen erforderlich, damit die Azure Purview-MSI die Überprüfungen durchführen kann.

- **Kontoschlüssel**: Geheimnisse können in einer Azure Key Vault erstellt werden, um Anmeldeinformationen zu speichern, um den Zugriff für Azure Purview zum sicheren Überprüfen von Datenquellen mithilfe der Geheimnisse zu ermöglichen. Ein Geheimnis kann ein Speicherkontoschlüssel, ein SQL Anmeldekennwort oder ein Kennwort sein.

   > [!Note]
   > Wenn Sie diese Option verwenden, müssen Sie eine _Azure Key Vault_ Ressource in Ihrem Abonnement bereitstellen und die MSI eines _Azure Purview Accounts_ mit erforderlicher Zugriffsberechtigung auf Geheimnisse im _Azure Key Vault_ zuweisen.

- **Dienstprinzipal**: Bei dieser Methode können Sie einen neuen Dienstprinzipal erstellen oder einen vorhandenen Dienstprinzipal in Ihrem Azure Active Directory verwenden.

#### <a name="using-managed-identity-for-scanning"></a>Verwenden der verwalteten Identität für die Überprüfung

Es ist wichtig, Ihrem Purview-Konto die Berechtigung zum Überprüfen der Azure Blob-Datenquelle zu erteilen. Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!NOTE]
> Wenn Sie für das Speicherkonto eine Firewall aktiviert haben, müssen Sie beim Einrichten einer Überprüfung als Authentifizierungsmethode **Verwaltete Identität** verwenden.

> [!Note]
> Sie müssen Besitzer des Abonnements sein, um einer Azure-Ressource eine verwaltete Identität hinzufügen zu können.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, die Ressourcengruppe oder die Ressource (z. B. ein Azure Blob Storage-Konto), das den Katalog überprüfen soll.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-storage-acct.png" alt-text="Screenshot: Speicherkonto":::

1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **+ Hinzufügen** --> **Rollenzuweisung hinzufügen** aus

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-control.png" alt-text="Screenshot der Seite „Zugriffssteuerung“ für das Speicherkonto":::

1. Legen Sie die **Rolle** auf **Leser von Speicherblobdaten** fest, und geben Sie unter **Auswählen** den _Namen Ihres Azure Purview-Kontos_ ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-assign-permissions.png" alt-text="Screenshot: Details zum Zuweisen von Berechtigungen für das Purview-Konto":::

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Blob Storage-Konto
1. Navigieren Sie zu **Sicherheit und Netzwerk > Netzwerk**

1. Wählen Sie unter **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus.

1. Aktivieren Sie im Abschnitt **Ausnahmen** das Kontrollkästchen **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**, und wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-permission.png" alt-text="Screenshot, der die Ausnahmen zeigt, mit denen vertrauenswürdigen Microsoft-Diensten Zugriff auf das Speicherkonto ermöglicht wird":::

> [!Note]
> Weitere Informationen finden Sie in den Schritten unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md).

#### <a name="using-account-key-for-scanning"></a>Verwenden des Kontoschlüssels für die Überprüfung

Wenn **Kontoschlüssel** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihren Zugriffsschlüssel abrufen und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem Azure Blob Storage-Konto
1. Klicken Sie auf **Sicherheit + Netzwerkbetrieb > Zugriffsschlüssel**.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-keys.png" alt-text="Screenshot: Zugriffsschlüssel im Speicherkonto":::

1. Kopieren Sie Ihren *Schlüssel*, und speichern Sie ihn separat für die nächsten Schritte

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-key.png" alt-text="Screenshot, der die zu kopierenden Zugriffsschlüssel zeigt":::

1. Navigieren zum Schlüsseltresor

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-key-vault.png" alt-text="Screenshot: Schlüsseltresor":::

1. Navigieren Sie zu Ihrer Key Vault-Instanz, und wählen Sie **Einstellungen > Geheimnisse** und dann **+ Generieren/Importieren** aus

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-generate-secret.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Generieren eines Geheimnisses":::

1. Geben Sie den **Namen** und den **Wert** als *Schlüssel* für Ihr Speicherkonto ein

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-secret-values.png" alt-text="Screenshot: Option &quot;Schlüsseltresor&quot; zum Eingeben der Geheimniswerte":::

1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.

1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="using-service-principal-for-scanning"></a>Verwenden des Dienstprinzipals für die Überprüfung

##### <a name="creating-a-new-service-principal"></a>Erstellen eines neuen Dienstprinzipals

Wenn Sie [einen neuen Dienstprinzipal erstellen](./create-service-principal-azure.md)müssen, müssen Sie eine Anwendung in Ihrem Azure AD Mandanten registrieren und den Zugriff auf den Dienstprinzipal in Ihren Datenquellen bereitstellen. Ihr Azure AD allgemeiner Administrator oder andere Rollen, z. B. Anwendungsadministrator, können diesen Vorgang ausführen.

##### <a name="getting-the-service-principals-application-id"></a>Abrufen der Anwendungs-ID des Dienstprinzipals

1. Kopieren Sie die **Anwendungs-ID (Client)** , die in der **Übersicht** über das bereits erstellten [_Dienstprinzipal_](./create-service-principal-azure.md) vorhanden ist

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-appln-id.png" alt-text="Screenshot: Anwendungs-ID (Client) für das Dienstprinzipal":::

##### <a name="granting-the-service-principal-access-to-your-azure-blob-account"></a>Gewähren des Zugriffs auf Ihr Azure Blob-Konto für das Dienstprinzipal

Es ist wichtig, ihrem Dienstprinzipal die Berechtigung zum Überprüfen der Azure Blob-Datenquelle zu erteilen. Sie können die Katalog-MSI auf der Ebene des Abonnements, der Ressourcengruppe oder der Ressource hinzufügen, je nachdem, wofür die Berechtigungen zum Überprüfen erteilt werden sollen.

> [!Note]
> Sie müssen ein Besitzer des Abonnements sein, um einer Azure-Ressource ein Dienstprinzipal hinzufügen zu können.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) das Abonnement, die Ressourcengruppe oder die Ressource (z. B. ein Azure Blob Storage-Konto), das den Katalog überprüfen soll.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-storage-acct.png" alt-text="Screenshot: Speicherkonto":::

1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **+ Hinzufügen** --> **Rollenzuweisung hinzufügen** aus

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-access-control.png" alt-text="Screenshot der Seite „Zugriffssteuerung“ für das Speicherkonto":::

1. Legen Sie die **Rolle** auf **Leser von Speicherblobdaten** fest, und geben Sie unter **Auswählen** das _Dienstprinzipal_ ein. Wählen Sie dann **Speichern** aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-permission.png" alt-text="Screenshot: Details zum Bereitstellen von Speicherkontoberechtigungen für das Dienstprinzipal":::

### <a name="creating-the-scan"></a>Erstellen der Überprüfung

1. Öffnen Sie Ihr **Purview-Konto,** und klicken Sie auf **Purview Studio öffnen**
1. Navigieren Sie zu **Datenzuordnungs** --> **quelle**, um die Sammlungshierarchie anzuzeigen
1. Klicken Sie unter der zuvor registrierten **Azure Blob-Datenquelle** auf das Symbol **Neue Überprüfung**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-new-scan.png" alt-text="Screenshot: Bildschirm zum Erstellen einer neuen Überprüfung":::

#### <a name="if-using-managed-identity"></a>Bei Zugriff mithilfe einer verwalteten Identität

Geben Sie einen **Namen** für die Überprüfung an, wählen Sie unter **Anmeldeinformationen** die **Purview MSI** aus, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-managed-identity.png" alt-text="Screenshot: Option Verwaltete Identität zum Ausführen der Überprüfung":::

#### <a name="if-using-account-key"></a>Bei Verwendung des Kontoschlüssels

Geben Sie einen **Namen** für die Überprüfung an, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und wählen Sie **Authentifizierungsmethode** als _Kontoschlüssel_ und dann **Erstellen** aus

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-acct-key.png" alt-text="Screenshot: Option „Kontoschlüssel“ für die Überprüfung":::

#### <a name="if-using-service-principal"></a>Bei Verwendung eines Dienstprinzipals

1. Geben Sie einen **Namen** für die Überprüfung an, wählen Sie die entsprechende Sammlung für die Überprüfung aus, und klicken Sie auf **+ Neu** unter den **Anmeldeinformationen**

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-sp-option.png" alt-text="Screenshot, der die Option für das Dienstprinzipal zum Aktivieren der Überprüfung zeigt":::

1. Wählen Sie die entsprechende **Schlüsseltresor-Verbindung** und den **geheimen Namen** aus, die beim Erstellen des _Dienstprinzipals_ verwendet wurden. Die **Dienstprinzipal-ID** ist die **Anwendungs-ID (Client)** , die zuvor kopiert wurde

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-service-principal-option.png" alt-text="Screenshot: Option Dienstprinzipal":::

1. Klicken Sie auf **Verbindung testen**. Klicken Sie bei einer erfolgreichen Verbindung auf **Weiter**

### <a name="scoping-and-running-the-scan"></a>Definieren und Ausführen der Überprüfung

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Ordner und Unterordner festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scope-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wenn Sie einen neuen _Überprüfungsregelsatz_ erstellen,wählen Sie die **Dateitypen** aus, die in die Überprüfungsregel aufgenommen werden sollen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-file-types.png" alt-text="Überprüfen von Regelsatz-Dateitypen":::

1. Sie können die **Klassifizierungsregeln** auswählen, die in die Überprüfungsregel aufgenommen werden sollen

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-classification rules.png" alt-text="Überprüfen von Klassifizierungsregeln":::


   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-select-scan-rule-set.png" alt-text="Überprüfen der Regelauswahl":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-trigger.png" alt-text="Auslöser für die Überprüfung":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-review-scan.png" alt-text="Überprüfungs-Scan":::

### <a name="viewing-scan"></a>Anzeigen der Überprüfung

1. Navigieren Sie zur _Datenquelle_ in der _Sammlung_ und klicken Sie auf **Details anzeigen**, um den Status der Überprüfung zu prüfen

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-view-scan.png" alt-text="Überprüfung anzeigen":::

1. Die Scandetails geben den Fortschritt der Überprüfung im **Status der letzten Ausführung** an sowie die Anzahl der _gescannten_ und _klassifizierten_ Objekte

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-details.png" alt-text="Überprüfungsdetails anzeigen":::

1. Der **Status Letzte Ausführung** wird in **In Bearbeitung** und anschließend in **Abgeschlossen** aktualisiert, sobald die gesamte Überprüfung erfolgreich ausgeführt wurde

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-in-progress.png" alt-text="Anzeigen der Überprüfung in Bearbeitung":::

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-scan-completed.png" alt-text="Die abgeschlossene Überprüfung anzeigen":::

### <a name="managing-scan"></a>Verwalten der Überprüfung

Überprüfungen können verwaltet oder nach Abschluss erneut ausgeführt werden

1. Klicken Sie auf den **Überprüfungsnamen**, um die Überprüfung zu verwalten

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-manage-scan.png" alt-text="Verwalten der Überprüfung":::

1. Sie können _die Überprüfung erneut ausführen_, _die Überprüfung bearbeiten_ oder _die Überprüfung löschen_  

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-manage-scan-options.png" alt-text="Verwalten von Überprüfungsoptionen":::

1. Sie können eine _inkrementelle Überprüfung_ oder eine _vollständige Überprüfung_ erneut ausführen 

   :::image type="content" source="media/register-scan-azure-blob-storage-source/register-blob-full-inc-scan.png" alt-text="Vollständige oder inkrementelle Überprüfung":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

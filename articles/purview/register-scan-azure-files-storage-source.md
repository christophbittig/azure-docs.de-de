---
title: Verbinden und Verwalten von Azure Files
description: In diesem Handbuch wird beschrieben, wie Sie eine Verbindung mit Azure Files in Azure Purview herstellen und die Funktionen von Purview verwenden, um Ihre Azure Files-Quelle zu überprüfen und zu verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 95abf27060748255f24b089cfc4fb9229f33bfa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848057"
---
# <a name="connect-to-and-manage-azure-files-in-azure-purview"></a>Verbinden und Verwalten von Azure Files in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Azure Files registrieren und wie Sie Azure Files in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | [Ja](#scan) | Nein | Eingeschränkt** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

Azure Files unterstützt vollständige und inkrementelle Überprüfungen, um die Metadaten und Klassifizierungen basierend auf standardmäßigen System- und benutzerdefinierten Klassifizierungsregeln zu erfassen.

Für Dateitypen wie CSV, TSV, PSV und SSV wird das Schema extrahiert, wenn die folgenden Logiken vorhanden sind:

1. Werte in der ersten Zeile sind nicht leer.
2. Werte in der ersten Zeile sind eindeutig.
3. Werte in der ersten Zeile sind weder ein Datum noch eine Zahl

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Azure Files in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Derzeit gibt es nur eine Möglichkeit zum Einrichten der Authentifizierung für Azure-Dateifreigaben:

- Kontoschlüssel

#### <a name="account-key-to-register"></a>Kontoschlüssel für die Registrierung

Wenn **Kontoschlüssel** als Authentifizierungsmethode ausgewählt wird, müssen Sie Ihren Zugriffsschlüssel abrufen und im Schlüsseltresor speichern:

1. Navigieren Sie zu Ihrem Speicherkonto.
1. Wählen Sie **Einstellungen > Zugriffsschlüssel** aus.
1. Kopieren Sie Ihren *Schlüssel*, und speichern Sie ihn für die nächsten Schritte.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie den **Namen** und **Wert** als *Schlüssel* für Ihr Speicherkonto ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls Ihr Schlüsseltresor noch nicht mit Purview verbunden ist, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Zum Schluss [erstellen Sie neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Schlüssel zum Einrichten Ihrer Überprüfung verwenden.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um ein neues Azure Files-Konto in Ihrem Datenkatalog zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Datenkatalog.
1. Wählen Sie im linken Navigationsbereich die Option **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Files** aus.
1. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Führen Sie auf dem Bildschirm **Register sources (Azure Files)** (Quellen registrieren (Azure Files)) die folgenden Schritte aus:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
2. Wählen Sie Ihr Azure-Abonnement aus, um Azure Storage-Konten zu filtern.
3. Wählen Sie ein Azure Storage-Konto aus.
4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
5. Wählen Sie **Registrieren** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Azure Files zu überprüfen und automatisch Assets zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Überprüfen im Allgemeinen finden Sie in unserer [Einführung in Scans und Datenerfassung](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Überprüfung erstellen und ausführen

Gehen Sie wie folgt vor, um eine neue Überprüfung zu erstellen und auszuführen:

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die von Ihnen registrierte Azure Files-Quelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Wählen Sie die Kontoschlüssel-Anmeldeinformationen aus, um eine Verbindung mit Ihrer Datenquelle herzustellen.

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="Einrichten der Überprüfung":::

1. Sie können den Bereich für Ihre Überprüfung auf bestimmte Datenbanken festlegen, indem Sie die entsprechenden Elemente in der Liste auswählen.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="Festlegen des Bereichs für Ihre Überprüfung":::

1. Wählen Sie dann einen Überprüfungsregelsatz aus. Sie können zwischen der Standardeinstellung des Systems, den vorhandenen benutzerdefinierten Regelsätzen und der Inlineerstellung eines neuen Regelsatzes wählen.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="Überprüfungsregelsatz":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können einen Wiederholungszeitplan einrichten oder die Überprüfung einmalig ausführen.

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

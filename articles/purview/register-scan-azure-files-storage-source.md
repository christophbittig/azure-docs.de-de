---
title: Registrieren und Überprüfen von Azure Files
description: In diesem Leitfaden wird beschrieben, wie Sie eine Überprüfung für Azure Files in Azure Purview durchführen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: 2db2b5343b8a55e29881bf0908fded0a48b90b78
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209906"
---
# <a name="register-and-scan-azure-files"></a>Registrieren und Überprüfen von Azure Files

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Azure Files unterstützt vollständige und inkrementelle Überprüfungen, um die Metadaten und Klassifizierungen basierend auf standardmäßigen System- und benutzerdefinierten Klassifizierungsregeln zu erfassen.

Für Dateitypen wie CSV, TSV, PSV und SSV wird das Schema extrahiert, wenn die folgenden Logiken vorhanden sind:

1. Werte in der ersten Zeile sind nicht leer.
2. Werte in der ersten Zeile sind eindeutig.
3. Werte in der ersten Zeile sind weder ein Datum noch eine Zahl.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Datenquellenadministrator sein, um Überprüfungen einrichten und planen zu können. Ausführlichere Informationen hierzu finden Sie unter [Katalogberechtigungen](catalog-permissions.md).

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Derzeit gibt es nur eine Möglichkeit zum Einrichten der Authentifizierung für Azure-Dateifreigaben:

- Kontoschlüssel

### <a name="account-key"></a>Kontoschlüssel

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

## <a name="register-an-azure-files-storage-account"></a>Registrieren eines Azure Files-Speicherkontos

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

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

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

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

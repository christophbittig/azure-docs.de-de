---
title: Verbindung und Verwalung von Looker
description: In diesem Handbuch wird beschrieben, wie Sie eine Verbindung mit Looker in Azure Purview herstellen und die Funktionen von Purview verwenden, um Ihre Looker-Quelle zu scannen und zu verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 3bb15a424496c04f63537a9dac4622e52a052c46
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848038"
---
# <a name="connect-to-and-manage-looker-in-azure-purview-preview"></a>Verbinden mit und Verwalten von Looker in Azure Purview (Vorschau)

In diesem Artikel wird beschrieben, wie Sie Looker registrieren und wie Sie Looker in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

> [!IMPORTANT]
> Looker als Quelle befindet sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-looker.md)|

> [!Important]
> Die unterstützte Looker-Serverversion ist 7.2

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Looker in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Ein API3-Schlüssel ist erforderlich, um eine Verbindung mit dem Looker-Server herzustellen. Der API3-Schlüssel besteht aus einer öffentlichen Client_ID und einem privaten Client_Geheimnis und folgt einem OAuth2-Authentifizierungsmuster.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog einen neuen Looker-Server zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **Looker** aus. Wählen Sie **Weiter** aus.

:::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="Registrieren einer Looker-Quelle" border="true":::

Gehen Sie auf dem Bildschirm „Registrieren einer Looker-Quelle“ wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie die Looker API-URL in dem Feld **Server API-URL** ein. Der Standardport für API-Anforderungen ist der Port 19999. Außerdem benötigen alle Looker-API-Endpunkte eine HTTPS-Verbindung. Beispiel: 'https://azurepurview.cloud.looker.com'

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="Überprüfen der Looker-Quelle" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Looker zu scannen und automatisch Assets zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Stellen Sie sicher, dass eine selbstgehostete Integration Runtime auf der VM eingerichtet ist, auf der die erwin Mart-Instanz ausgeführt wird. Wenn sie nicht eingerichtet ist, führen Sie die [hier](./manage-integration-runtimes.md) genannten Schritte aus, um eine selbst gehostete Integrationslaufzeit einzurichten.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie den registrierten **Looker**-Server aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. Die **Server-API-URL** wird basierend auf dem Wert, der während der Registrierung eingegeben wurde, automatisch aufgefüllt.

    1. **Anmeldeinformation**: Stellen Sie beim Konfigurieren von Looker-Anmeldeinformationen Folgendes sicher:

        * Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
        * Geben Sie die Client-ID Ihres Looker API3-Schlüssels im Feld Benutzername an
        * Speichern Sie das Client-Geheimnis Ihre Ihres Looker API3-Schlüssels als Geheimnis in Ihrem Schlüsseltresor.

        Um auf die Client-ID und das Client-Geheimkennwort zuzugreifen, navigieren Sie zu Looker -\>Admin -\> Benutzer -\> Wählen Sie **Bearbeiten** für einen Benutzer -\> Wählen Sie **Schlüssel bearbeiten** -\> Verwenden Sie die Client-ID und das Client-Geheimkennwort oder erstellen Sie ein neues.

        :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="Abrufen von Looker-Details" border="true":::

        Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    1. **Projektfilter** – Begrenzen Sie Ihren Scan, indem Sie eine durch Semikolon getrennte Liste von Looker-Projekten bereitstellen. Diese Option wird verwendet, um das Aussehen und die Dashboards nach Ihrem übergeordneten Projekt auszuwählen.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der zu überprüfenden erwin Mart-Instanz ab.

        :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Auslösen der Überprüfung" border="true":::

1. Wählen Sie **Verbindung testen** aus.

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

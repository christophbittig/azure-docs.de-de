---
title: Verbindung zu erwin Mart-Servern herstellen und verwalten
description: Dieser Leitfaden beschreibt, wie Sie sich mit erwin Mart-Servern in Azure Purview verbinden und die Purview-Funktionen zum Scannen und Verwalten Ihrer erwin Mart-Serverquelle nutzen.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0a6aef0cdbf55772ada02bef7090ccc3165b5658
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472445"
---
# <a name="connect-to-and-manage-erwin-mart-servers-in-azure-purview-preview"></a>Verbindung zu und Verwaltung von erwin Mart Servern in Azure Purview (Vorschau)

Dieser Artikel beschreibt, wie man erwin Mart Server registriert und wie man sich authentifiziert und mit erwin Mart Servern in Azure Purview interagiert. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

> [!IMPORTANT]
> erwin Mart Server als Quelle ist derzeit in PREVIEW. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-erwin.md)|

Die erwin-Quelle unterstützt eine vollständige Überprüfung, um Metadaten aus einem erwin-Mart-Server zu extrahieren. Die Metadaten umfassen:

1. Nur logische Modelle mit Entitäten, Attributen und Domänen ODER
1. Nur physische Modelle mit Tabellen, Spalten und Datentypen ODER
1. Logisch-physische Modelle

> [!Important]
> Unterstützt werden die erwin Mart Versionen 9.x bis 2021.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen ein Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Stellen Sie sicher, dass Sie die selbstgehostete Integration Runtime auf der VM ausführen, auf der die erwin Mart-Instanz ausgeführt wird.

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

Dieser Abschnitt beschreibt die Registrierung von erwin Mart-Servern in Azure Purview unter Verwendung des [Purview Studio](https://web.purview.azure.com/).

Die einzige unterstützte Authentifizierung für eine erwin Mart-Quelle ist **Server-Authentifizierung** in Form von Benutzername und Passwort.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu Ihrem Purview-Konto im [Purview Studio](https://web.purview.azure.com/).
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Register sources“ (Quellen registrieren) die Option **erwin** aus. Wählen Sie **Weiter** aus.
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="Registrieren der Quelle „erwin“" border="true":::

Gehen Sie auf dem Bildschirm „Register sources (erwin)“ (Quellen registrieren (erwin)) wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Geben Sie den **Servernamen** für erwin Mart Server ein. Dies ist der Netzwerkhostname, mit dem die Verbindung zur erwin Mart Server-Instanz hergestellt wird. Beispiel: localhost.
1. Geben Sie die **Nummer des Ports** ein, über den die Verbindung zur erwin Mart Server-Instanz hergestellt wird. Standardmäßig ist dies Port 18170.
1. Geben Sie den **Anwendungsnamen** ein.

    >[!Note]
    > Die oben genannten Details finden Sie, indem Sie zu Ihrer Instanz von erwin Data Modeler navigieren. Wählen Sie „Mart“ \> „Connect“ (Verbinden) aus, um Details zum Servernamen, Port und Anwendungsnamen anzuzeigen.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Erwin-Details suchen" border="true":::

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Quelle registrieren" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um erwin Mart-Server zu scannen, um Assets automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Stellen Sie sicher, dass eine selbstgehostete Integration Runtime auf der VM eingerichtet ist, auf der die erwin Mart-Instanz ausgeführt wird. Wenn sie nicht eingerichtet ist, führen Sie die [hier](./manage-integration-runtimes.md) genannten Schritte aus, um eine selbst gehostete Integrationslaufzeit einzurichten.
1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte **erwin** Mart-Instanz aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integrationslaufzeit**: Wählen Sie die konfigurierte, selbst gehostete Integrationslaufzeit.

    1. **Servername, Port** und **Anwendungsname** werden automatisch auf der Grundlage der bei der Registrierung eingegebenen Werte ausgefüllt.

    1. **Anmeldeinformationen:** Wählen Sie die Anmeldeinformationen aus, die für die Verbindung mit Ihrer erwin Mart Server-Instanz konfiguriert sind. Achten Sie beim Erstellen von Anmeldeinformationen auf Folgendes:
        * Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
        * Geben Sie den Benutzernamen Ihres erwin Mart-Servers in das Feld Benutzername ein.
        * Speichern Sie Ihr Benutzerkennwort für die Serverauthentifizierung im Geheimnis des Schlüsseltresors.

        Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    1. **Internetinformationsdienste (IIS) verwenden:** Wählen Sie TRUE oder FALSE aus, um eine Benachrichtigung zu dafür einzurichten, ob die Microsoft-Internetinformationsdienste (IIS) bei der Verbindungsherstellung mit der erwin Mart Server-Instanz verwendet werden müssen. Standardmäßig ist dieser Wert auf False gesetzt.

    1. **Secure Sockets Layer (SSL) verwenden:** Wählen Sie TRUE oder FALSE aus, um eine Benachrichtigung zu dafür einzurichten, ob Secure Sockets Layer (SSL) bei der Verbindungsherstellung mit der erwin Mart Server-Instanz verwendet werden muss. Standardmäßig ist dieser Wert auf Falsefestgelegt.

        > [!Note]
        > Dieser Parameter gilt nur für erwin Mart-Version 9.1 oder höher.

    1. **Durchsuchungsmodus:** Wählen Sie den Modus zum Durchsuchen von erwin Mart aus. Mögliche Optionen sind „Libraries and Models“ (Bibliotheken und Modelle) oder „Libraries only“ (Nur Bibliotheken).

    1. **Modelle** - Erweitern Sie Ihren Suchbereich, indem Sie eine durch Semikolon getrennte Liste von erwin-Modell-Locator-Strings angeben. Beispiel: mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der zu überprüfenden erwin Mart-Instanz ab.

    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Auslösen der Überprüfung" border="true":::

1. Wählen Sie **Verbindung testen** aus.

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, halten Sie sich an die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Dateneinblicke in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

---
title: Herstellen einer Verbindung mit und Verwalten von PostgreSQL
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit PostgreSQL in Azure Purview herstellen und die Purview-Features verwenden, um Ihre PostgreSQL-Quelle zu überprüfen und zu verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 4d87a78c809791f01745f4406b58832258ea4d36
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548537"
---
# <a name="connect-to-and-manage-postgresql-in-azure-purview-preview"></a>Verbinden mit und Verwalten von PostgreSQL in Azure Purview (Vorschau)

In diesem Artikel wird beschrieben, wie Sie PostgreSQL in Azure Purview registrieren, authentifizieren und mit PostgreSQL interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

> [!IMPORTANT]
> PostgreSQL als Quelle befindet sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| Ja|

Die unterstützten PostgreSQL-Serverversionen sind 8.4 bis 12.x.

Bei der Überprüfung der PostgreSQL-Quelle unterstützt Purview folgende Funktionen:

- Extrahieren von Metadaten, einschließlich PostgreSQL-Server, -Datenbanken, -Schemas, -Tabellen, -Sichten und -Tabellenspalten sowie -Sichtspalten.
- Abrufen der Herkunft für Ressourcenbeziehungen zwischen Tabellen und Sichten.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Der PostgreSQL-Benutzer muss über Lesezugriff für Systemtabellen verfügen, um auf erweiterte Metadaten zugreifen zu können.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie PostgreSQL mithilfe von [Purview Studio](https://web.purview.azure.com/) in Azure Purview registrieren.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue PostgreSQL-Quelle zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto im [Purview Studio](https://web.purview.azure.com/resource/).
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **PostgreSQL** aus. Wählen Sie **Weiter**.

Gehen Sie auf dem Bildschirm **Quellen registrieren (PostgreSQL)** wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie einen Namen für den **Server** ein, um eine Verbindung mit einer PostgreSQL-Quelle herzustellen. Hierfür kommt infrage:
    * Ein Hostname, der verwendet wird, um eine Verbindung mit dem Datenbankserver herzustellen. Beispiel: `MyDatabaseServer.com`
    * Eine IP-Adresse Beispiel: `192.169.1.2`
    * Es ist eine vollqualifizierte JDBC-Verbindungszeichenfolge. Zum Beispiel:

        ```
        jdbc:postgresql://COMPUTER_NAME_OR_IP:PORT/DATABASE_NAME
        ```

1. Geben Sie den **Port** ein, der zum Herstellen einer Verbindung mit dem Datenbankserver verwendet wird (für PostgreSQL wird standardmäßig der Port 5432 verwendet).

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-postgresql/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um PostgreSQL zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Der unterstützte Authentifizierungstyp für eine PostgreSQL-Quelle ist die **Standardauthentifizierung**.

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte PostgreSQL-Quelle aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:
        * beim Erstellen von Anmeldeinformationen die **Standardauthentifizierung** auswählen.
        * im Eingabefeld „Benutzername“ den Benutzernamen angeben, der zum Herstellen einer Verbindung mit dem Datenbankserver verwendet wird.
        * das Benutzerkennwort im geheimen Schlüssel speichern, das zum Herstellen einer Verbindung mit dem Datenbankserver verwendet wird.

    1. **Datenbank:** Geben Sie den Namen der zu importierenden Datenbankinstanz an.

    1. **Schema**: Eine durch Semikolons getrennte Liste mit einer Teilmenge von Schemas, die importiert werden sollen. Beispielsweise `schema1; schema2`. Ist diese Liste leer, werden alle Benutzerschemas importiert. Alle Systemschemas (beispielsweise „SysAdmin“) und Objekte werden standardmäßig ignoriert. Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert.
        
        Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“. Beispiel: `A%; %B; %C%; D`
        * Starten mit A oder
        * Endet mit „B“ oder
        * Enthält „C“ oder
        * Ist gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dieser hängt von der Größe der zu überprüfenden PostgreSQL-Quelle ab.

        > [!Note]
        > Faustregel: 1 GB Arbeitsspeicher pro 1000 Tabellen.

        :::image type="content" source="media/register-scan-postgresql/scan.png" alt-text="PostgreSQL überprüfen" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

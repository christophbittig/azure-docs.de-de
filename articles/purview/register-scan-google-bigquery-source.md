---
title: Verbindung zu Google BigQuery-Projekten herstellen und diese verwalten
description: In dieser Anleitung wird beschrieben, wie Sie in Azure Purview eine Verbindung mit Google BigQuery-Projekten herstellen und die Funktionen von Purview zum Scannen und Verwalten Ihrer Google BigQuery-Quelle verwenden.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f28de0520e4b69970549f7258a5a41dd109d2b48
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852577"
---
# <a name="connect-to-and-manage-google-bigquery-projects-in-azure-purview-preview"></a>Eine Verbindung mit Google BigQuery-Projekten in Azure Purview herstellen und diese verwalten (Vorschau)

In diesem Artikel wird beschrieben, wie Sie Google BigQuery-Projekte registrieren und wie Sie Google BigQuery in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

> [!IMPORTANT]
> Google BigQuery als Quelle befindet sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-google-bigquery.md)|

> [!Important]
> Die unterstützte Google BigQuery-Version ist 11.0.0.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Laden Sie den JDBC-Treiber von BigQuery herunter und installieren Sie ihn auf dem Computer, auf dem Ihre selbst gehostete Integrationslaufzeit ausgeführt wird. Den Treiber finden Sie [hier](https://cloud.google.com/bigquery/providers/simba-drivers).

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie ein Google BigQuery-Projekt in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **Google BigQuery** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="BigQuery-Quelle registrieren" border="true":::

Gehen Sie auf dem Bildschirm „Quellen registrieren“ (Google BigQuery) wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie die **ProjectID** ein. Dies sollte eine vollständig qualifizierte Projekt-ID sein. Zum Beispiel: mydomain.com:myProject

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie **Registrieren**.

    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Konfigurieren der BigQuery-Quelle" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um ein Google BigQuery-Projekt zu scannen, um Assets automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Wenn es nicht eingerichtet ist, führen Sie die [hier](./manage-integration-runtimes.md) genannten Schritte aus.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie das registrierte **BigQuery-Projekt** aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Stellen Sie beim Konfigurieren von BigQuery-Anmeldeinformationen Folgendes sicher:

        * Wählen Sie **Windows-Authentifizierung** als Authentifizierungstyp aus
        * Geben Sie die E-Mail-ID des Dienstkontos in das Feld Benutzername ein. Zum Beispiel, `xyz\@developer.gserviceaccount.com`
        * Führen Sie die folgenden Schritte aus, um den privaten Schlüssel zu generieren, kopieren Sie den JSON-Code, und speichern Sie ihn als Wert eines Key Vault-Geheimnisses.

        So erstellen Sie einen neuen privaten Schlüssel von der Cloud-Plattform von Google:
        1. Wählen Sie im Navigationsmenü IAM & Admin -\> Dienstkonten -\> Wählen Sie ein Projekt aus -\> 
        1. Wählen Sie die E-Mail-Adresse des Dienstkontos aus, für das Sie einen Schlüssel erstellen möchten.
        1. Wählen Sie die Registerkarte **Schlüssel** aus.
        1. Wählen Sie das Dropdown-Menü **Schlüssel hinzufügen** und dann Neuen Schlüssel erstellen aus. 
        1. Wählen Sie das JSON-Format.

          > [!Note]
          > Der Inhalt des privaten Schlüssels wird in einer temporären Datei auf dem virtuellen Computer gespeichert, wenn Überprüfungsprozesse ausgeführt werden. Diese temporäre Datei wird gelöscht, nachdem die Überprüfungen erfolgreich abgeschlossen wurden. Im Falle eines Überprüfungsfehlers, wird das System den Vorgang bis zum Erfolg fortsetzen. Bitte stellen Sie sicher, dass der Zugriff auf der VM, auf der SHIR ausgeführt wird, entsprechend eingeschränkt ist.

        Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    1. **Treiberspeicherort**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Dabei sollte es sich um den Pfad zu einem gültigen Speicherort eines JAR-Ordners handeln. 

        > [!Note]
        > Der Treiber sollte für alle Konten auf dem virtuellen Computer zugänglich sein. Installieren Sie ihn bitte nicht in einem Benutzerkonto.

    1. **Dataset**: Geben Sie eine Liste der zu importierenden BigQuery-Datasets an.
        Zum Beispiel: dataset1; dataset2. Wenn die Liste leer ist, werden alle verfügbaren Datasets importiert.
        Zulässige Datensatz-Namensmuster, die die Syntax von SQL-ÄHNLICHEN Ausdrücken verwenden, umfassen die Verwendung von %.

        Beispiel:  A%; %B; %C%; D
        * Starten mit A oder
        * endet mit „B“ oder
        * enthält „C“ oder
        * gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf Ihrer VM für Scanprozesse verfügbar ist. Dies ist abhängig von der Größe des zu überprüfenden Google BigQuery-Projekts.

        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Überprüfen der BigQuery-Quelle" border="true":::

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

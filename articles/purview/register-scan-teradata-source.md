---
title: Verbinden mit und Verwalten von Teradata
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit Teradata herstellen und Ihre Teradata-Quelle mit den Features von Purview überprüfen und verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ebaad16ff413b33f175815a1ddadb2fa1d5b63ae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841921"
---
# <a name="connect-to-and-manage-teradata-in-azure-purview"></a>Verbinden mit und Verwalten von Teradata in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Teradata in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja**](how-to-lineage-teradata.md)|

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

Die Teradata-Datenbankversionen 12.x bis 16.x werden unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Sie müssen den JDBC-Treiber von Teradata manuell auf den virtuellen Computer herunterladen, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Die ausführbare JAR-Datei kann von der Teradata-[Website](https://downloads.teradata.com/) heruntergeladen werden.

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Teradata in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Die einzige unterstützte Authentifizierung für eine Teradata-Quelle ist die **Standardauthentifizierung**. Stellen Sie sicher, dass Sie für die zu überprüfende Teradata-Quelle über Lesezugriff verfügen.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1.  Navigieren Sie zu Ihrem Purview-Konto.
1.  Wählen Sie im linken Navigationsbereich **Data Map** aus.
1.  Wählen Sie **Registrieren** aus.
1.  Wählen Sie unter „Quellen registrieren“ die Option **Teradata** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata-Registrierung" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Teradata)** (Quellen registrieren (Teradata)) wie folgt vor:

1.  Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1.  Geben Sie unter **Host** den Hostnamen für die Verbindungsherstellung mit einer Teradata-Quelle ein. Hierbei kann es sich auch um eine IP-Adresse oder um eine vollqualifizierte Verbindungszeichenfolge für den Server handeln.

1.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1.  Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Registrieren von Teradata" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Teradata zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

1. Wählen Sie im Verwaltungscenter **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Wenn sie nicht eingerichtet ist, führen Sie die [hier](./manage-integration-runtimes.md) genannten Schritte aus, um eine selbst gehostete Integrationslaufzeit einzurichten.

1. Wählen Sie im linken Bereich in [Purview Studio](https://web.purview.azure.com/resource/) die Registerkarte **Data Map** aus.

1. Wählen Sie die registrierte Teradata-Quelle aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:
        * beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
        * im Eingabefeld „Benutzername“ einen Benutzernamen zur Herstellung der Verbindung mit dem Datenbankserver eingeben.
        * das Kennwort für den Datenbankserver im geheimen Schlüssel speichern.

        Weitere Informationen zu Anmeldeinformationen finden Sie [hier](./manage-credentials.md).

    1. **Schema**: Eine durch Semikolons getrennte Liste mit einer Teilmenge von Schemas, die importiert werden sollen. Beispiel: `schema1; schema2`. Ist diese Liste leer, werden alle Benutzerschemas importiert. Alle Systemschemas (beispielsweise „SysAdmin“) und Objekte werden standardmäßig ignoriert. Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert.

        Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“. Beispiel: `A%; %B; %C%; D`
        * Starten mit A oder
        * Endet mit „B“ oder
        * Enthält „C“ oder
        * Ist gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

    1. **Treiberspeicherort**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Dabei sollte es sich um den Pfad zu einem gültigen Speicherort eines JAR-Ordners handeln.

    1. **Maximal verfügbarer Arbeitsspeicher:** Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Abhängig von der Größe der zu überprüfenden Teradata-Quelle.

        > [!Note]
        > Faustregel: 2 GB Arbeitsspeicher pro 1.000 Tabellen.

        :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Einrichten der Überprüfung" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

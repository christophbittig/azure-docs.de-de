---
title: Herstellen einer Verbindung mit und Verwalten von Oracle
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit Oracle in Azure Purview herstellen und die Purview-Features verwenden, um Ihre Oracle-Quelle zu überprüfen und zu verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 935fcf05624fd1849ae62109dbc22a97d4987676
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847278"
---
# <a name="connect-to-and-manage-oracle-in-azure-purview"></a>Herstellen einer Verbindung mit und Verwalten von Oracle in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Oracle in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja**](how-to-lineage-oracle.md)|

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

Unterstützt werden die Oracle-Serverversionen 6i bis 19c.

Der Proxyserver wird beim Überprüfen der Oracle-Quelle nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Laden Sie den Oracle-JDBC-Treiber von [hier](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) manuell auf den virtuellen Computer herunter, auf dem die selbstgehostete Integration Runtime ausgeführt wird.

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Oracle mithilfe von [Purview Studio](https://web.purview.azure.com/) in Azure Purview registrieren.

### <a name="prerequisites-for-registration"></a>Voraussetzungen für die Registrierung

Schreibgeschützter Zugriff auf Systemtabellen ist erforderlich.

Der Benutzer muss über die Berechtigung zum Erstellen einer Sitzung sowie die Rolle SELECT\_CATALOG\_ROLE verfügen. Alternativ kann dem Benutzer die SELECT-Berechtigung für jede einzelne Systemtabelle erteilt werden, von der dieser Connector Metadaten abfragt:

```sql
grant create session to [user];
grant select on all_users to [user];
grant select on dba_objects to [user];
grant select on dba_tab_comments to [user];
grant select on dba_external_locations to [user];
grant select on dba_directories to [user];
grant select on dba_mviews to [user];
grant select on dba_clu_columns to [user];
grant select on dba_tab_columns to [user];
grant select on dba_col_comments to [user];
grant select on dba_constraints to [user];
grant select on dba_cons_columns to [user];
grant select on dba_indexes to [user];
grant select on dba_ind_columns to [user];
grant select on dba_procedures to [user];
grant select on dba_synonyms to [user];
grant select on dba_views to [user];
grant select on dba_source to [user];
grant select on dba_triggers to [user];
grant select on dba_arguments to [user];
grant select on dba_sequences to [user];
grant select on dba_dependencies to [user];
grant select on dba_type_attrs to [user];
grant select on V_$INSTANCE to [user];
grant select on v_$database to [user];
```

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Die einzige unterstützte Authentifizierung für eine Oracle-Quelle ist die **Standardauthentifizierung**.

### <a name="steps-to-register"></a>Schritte zur Registrierung

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Oracle-Quelle zu registrieren:

1. Navigieren Sie in [Purview Studio](https://web.purview.azure.com/resource/) zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Quellen registrieren“ die Option **Oracle** aus. Wählen Sie **Weiter**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Quellen registrieren" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (Oracle)** wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie unter **Host** den Hostnamen ein, mit dem eine Verbindung mit einer Oracle-Quelle hergestellt werden soll. Hierfür kommt infrage:
    * Ein Hostname, der von JDBC verwendet wird, um eine Verbindung mit dem Datenbankserver herzustellen. Beispiel: `MyDatabaseServer.com`
    * Eine IP-Adresse Beispiel: `192.169.1.2`
    * Es ist eine vollqualifizierte JDBC-Verbindungszeichenfolge. Zum Beispiel:

         ```
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=orcl)))
        ```

1. Geben Sie die **Portnummer** ein, die von JDBC verwendet wird, um eine Verbindung mit dem Datenbankserver herzustellen (standardmäßig 1521 für Oracle).

1. Geben Sie den von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver verwendeten **Oracle-Dienstnamen** ein.

1. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Oracle zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte Oracle-Quelle aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:
        * beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
        * Geben Sie im Eingabefeld „Benutzername“ den Benutzernamen an, der von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver verwendet wird.
        * Speichern Sie das Benutzerkennwort, das von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver im geheimen Schlüssel verwendet wird.

    1. **Schema**: Eine durch Semikolons getrennte Liste mit einer Teilmenge von Schemas, die importiert werden sollen. Beispielsweise `schema1; schema2`. Ist diese Liste leer, werden alle Benutzerschemas importiert. Alle Systemschemas (beispielsweise „SysAdmin“) und Objekte werden standardmäßig ignoriert. Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert.

        Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“. Beispiel: `A%; %B; %C%; D`
        * Starten mit A oder
        * Endet mit „B“ oder
        * Enthält „C“ oder
        * Ist gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

    1. **Treiberspeicherort**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Dabei sollte es sich um den Pfad zu einem gültigen Speicherort eines JAR-Ordners handeln.

        > [!Note]
        > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dieser hängt von der Größe der zu überprüfenden Oracle-Quelle ab.

        > [!Note]
        > Faustregel: 1 GB Arbeitsspeicher pro 1000 Tabellen.

        :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Oracle überprüfen" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

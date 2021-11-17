---
title: Verbinden und Verwalten von Hive-Metastore-Datenbanken
description: In diesem Leitfaden wird beschrieben, wie Sie eine Verbindung mit Hive-Metastore-Datenbanken in Azure Purview herstellen und die Funktionen von Purview verwenden, um Ihre Hive Metastore-Datenbankquelle zu überprüfen und zu verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2e40125d65dd2b4b701d5b1049444a52e6cb7dc4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850658"
---
# <a name="connect-to-and-manage-hive-metastore-databases-in-azure-purview"></a>Verbinden und Verwalten von Hive-Metastore-Datenbanken in Azure Purview

In diesem Artikel wird beschrieben, wie Sie Hive Metastore-Datenbanken registrieren und wie Sie Hive Metastore-Datenbanken in Azure Purview authentifizieren und mit ihnen interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| Ja** |

\** Herkunft wird unterstützt, wenn das Dataset als Quelle/Senke in der [Data Factory-Copy-Aktivität](how-to-link-azure-data-factory.md) verwendet wird. 

> [!Important]
> Die unterstützten Plattformen sind Apache Hadoop, Cloudera, Hortonworks und Databricks.
> Hierfür werden die Hive-Versionen 2.x bis 3.x unterstützt. Unterstützte Databricks-Versionen sind 8.0 und höher.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein. Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

* Laden Sie den JDBC-Treiber der Hive Metastore Datenbank herunter, und installieren Sie diesen auf dem Computer, auf dem Ihre selbstgehostete Integrationslaufzeit ausgeführt wird. Wenn beispielsweise eine Datenbank vom Typ „mssql“ verwendet wird, sollten Sie sicherstellen, dass Sie den [JDBC-Treiber für SQL Server von Microsoft](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) herunterladen.

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Hive Metastore Datenbanken in Azure Purview mit [Purview Studio](https://web.purview.azure.com/)registriert werden.

Die einzige unterstützte Authentifizierung für eine Hive-Metastore-Datenbank ist die **Standardauthentifizierung**.

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Wählen Sie **Registrieren** aus.

1. Wählen Sie unter „Quellen registrieren“ die Option „**Hive-Metastore** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Registrieren der Hive-Quelle" border="true":::

Gehen Sie unter „Quellen registrieren“ (Hive-Metastore) wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

1. Geben Sie die **Hive-Cluster-URL** ein. Sie können die Cluster-URL entweder über die Ambari-URL oder über die URL des Databricks-Arbeitsbereichs abrufen. Beispiele hierfür sind „hive.azurehdinsight.net“ und „adb-19255636414785.5.azuredatabricks.net“.

1. Geben Sie die **Hive-Metastore-Server-URL** ein. Beispiele hierfür sind „sqlserver://hive.database.windows.net“ und „jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443“.

1. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).

1. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Konfigurieren der Hive-Quelle" border="true":::

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Hive Metastore Datenbanken zu überprüfen, um Ressourcen automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

1. Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Wenn sie nicht eingerichtet ist, führen Sie die [hier](./manage-integration-runtimes.md] erwähnten Schritte aus, um eine selbstgehostete Integrationslaufzeit  einzurichten.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte **Hive-Metastore-Datenbank** aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:

       * beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
       * Angeben des Metastore-Benutzernamens im entsprechenden Eingabefeld
       * Speichern des Metastore-Kennworts im geheimen Schlüssel

       Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

       **Databricks-Nutzung**: Navigieren Sie zu Ihrem Databricks-Cluster und dann zu „Apps“ > „Launch Web Terminal“ (Webterminal starten). Führen Sie das Cmdlet **cat /databricks/hive/conf/hive-site.xml** aus.

       Sie können über die beiden hier angegebenen Eigenschaften auf den Benutzernamen und das Kennwort zugreifen:

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **Speicherort des Metastore-JDBC-Treibers**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Hierbei sollte es sich um den Pfad zu einem gültigen Speicherort eines JARs-Ordners handeln.

       Wenn Sie eine Überprüfung von Databricks durchführen, helfen Ihnen die entsprechenden Informationen im Abschnitt unten weiter.

       > [!Note]
       > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

    1. **Metastore-JDBC-Treiberklasse**: Geben Sie den Namen der Verbindungstreiberklasse an. Beispiel: example,\com.microsoft.sqlserver.jdbc.SQLServerDriver.

       **Databricks-Nutzung**: Navigieren Sie zu Ihrem Databricks-Cluster und dann zu „Apps“ > „Launch Web Terminal“ (Webterminal starten). Führen Sie das Cmdlet **cat /databricks/hive/conf/hive-site.xml** aus.

       Auf die Treiberklasse kann wie unten dargestellt über die entsprechende Eigenschaft zugegriffen werden.

        :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **Metastore-JDBC-URL**: Geben Sie den Wert der Verbindungs-URL an, und definieren Sie die Verbindung mit der URL des Metastore-Datenbankservers. Beispiel: `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`.

       **Databricks-Nutzung**: Navigieren Sie zu Ihrem Databricks-Cluster und dann zu „Apps“ > „Launch Web Terminal“ (Webterminal starten). Führen Sie das Cmdlet **cat /databricks/hive/conf/hive-site.xml** aus.

       Auf die JDBC-URL kann wie unten gezeigt über die Verbindungs-URL-Eigenschaft zugegriffen werden.

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

       > [!NOTE]
       > Wenn Sie die URL aus *hive-site.xml* kopieren, sollten Sie `amp;` aus der Zeichenfolge entfernen, da bei der Überprüfung sonst ein Fehler angezeigt wird.

       Fügen Sie an diese URL den Pfad zu dem Speicherort an, an dem sich das SSL-Zertifikat auf Ihrer VM befindet. Sie können das SSL-Zertifikat [hier](../mysql/howto-configure-ssl.md) herunterladen.

       Die Metastore-JDBC-URL lautet wie folgt:

       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem`

    1. **Name der Metastore-Datenbank**: Geben Sie den Namen der Hive-Metastore-Datenbank an.

       Wenn Sie eine Überprüfung von Databricks durchführen, helfen Ihnen die entsprechenden Informationen im Abschnitt unten weiter.

       **Databricks-Nutzung**: Navigieren Sie zu Ihrem Databricks-Cluster und dann zu „Apps“ > „Launch Web Terminal“ (Webterminal starten). Führen Sie das Cmdlet **cat /databricks/hive/conf/hive-site.xml** aus.

       Auf den Datenbanknamen kann wie unten gezeigt über die JDBC-URL-Eigenschaft zugegriffen werden. Beispiel: organization1829255636414785

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **Schema**: Geben Sie eine Liste mit den zu importierenden Hive-Schemas an. Beispiel: Schema1; Schema2.

        Ist diese Liste leer, werden alle Benutzerschemas importiert. Alle Systemschemas (beispielsweise „SysAdmin“) und Objekte werden standardmäßig ignoriert.

        Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert. Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“. Beispiel: A%; %B; %C%; D

        * Beginnt mit „A“ oder
        * endet mit „B“ oder
        * enthält „C“ oder
        * gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

    1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der Hive-Metastore-Datenbank ab, die überprüft werden soll.

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="Überprüfen einer Hive-Quelle" border="true":::

1. Wählen Sie **Weiter**.

1. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)

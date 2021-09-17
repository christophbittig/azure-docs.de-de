---
title: Registrieren einer Hive-Metastore-Datenbank und Einrichten von Überprüfungen in Azure Purview
description: In diesem Artikel erfahren Sie, wie Sie eine Hive-Metastore-Datenbank in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 5/17/2021
ms.openlocfilehash: 0a7d8a22cf8f9dcdaac9d3fe07bd6ab006e61818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751902"
---
# <a name="register-and-scan-hive-metastore-database"></a>Registrieren und Überprüfen einer Hive-Metastore-Datenbank

In diesem Artikel erfahren Sie, wie Sie eine Hive-Metastore-Datenbank in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Hive-Metastore-Quelle unterstützt die vollständige Überprüfung, um Metadaten aus einer **Hive-Metastore-Datenbank** zu extrahieren, und ruft die Herkunft für die entsprechenden Datenressourcen ab. Die unterstützten Plattformen sind Apache Hadoop, Cloudera, Hortonworks und Databricks.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Sie müssen den JDBC-Treiber der Hive-Metastore-Datenbank manuell auf Ihren virtuellen Computer herunterladen, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Wenn beispielsweise eine Datenbank vom Typ „mssql“ verwendet wird, sollten Sie sicherstellen, dass Sie den [JDBC-Treiber für SQL Server von Microsoft](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) herunterladen.

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

5.  Hierfür werden die Hive-Versionen 2.x bis 3.x unterstützt. Unterstützte Databricks-Versionen sind 8.0 und höher. 

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die einzige unterstützte Authentifizierung für eine Hive-Metastore-Datenbank ist die **Standardauthentifizierung**.

## <a name="register-a-hive-metastore-database"></a>Registrieren einer Hive-Metastore-Datenbank

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Hive-Metastore-Datenbank zu registrieren:

1.  Navigieren Sie zu Ihrem Purview-Konto.

2.  Wählen Sie im linken Navigationsbereich **Data Map** aus.

3.  Wählen Sie **Registrieren** aus.

4.  Wählen Sie unter „Quellen registrieren“ die Option „**Hive-Metastore** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Registrieren der Hive-Quelle" border="true":::

Gehen Sie unter „Quellen registrieren“ (Hive-Metastore) wie folgt vor:

1.  Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2.  Geben Sie die **Hive-Cluster-URL** ein. Sie können die Cluster-URL entweder über die Ambari-URL oder über die URL des Databricks-Arbeitsbereichs abrufen. Beispiele hierfür sind „hive.azurehdinsight.net“ und „adb-19255636414785.5.azuredatabricks.net“.

3.  Geben Sie die **Hive-Metastore-Server-URL** ein. Beispiele hierfür sind „sqlserver://hive.database.windows.net“ und „jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443“.

4.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

5.  Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

       :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Konfigurieren der Hive-Quelle" border="true":::


## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Klicken Sie im Verwaltungscenter auf Integration Runtimes. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, richten Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime ein.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie die registrierte **Hive-Metastore-Datenbank** aus.

4.  Wählen Sie **+ Neue Überprüfung** aus.

5.  Geben Sie folgende Details an:

    1. **Name**: Der Name der Überprüfung.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:

       - beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
       - Angeben des Metastore-Benutzernamens im entsprechenden Eingabefeld
       - Speichern des Metastore-Kennworts im geheimen Schlüssel

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

        Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert. Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“ wie im folgenden Beispiel: A%; %B; %C%; D

        - beginnt mit „A“ oder    
        - endet mit „B“ oder    
        - enthält „C“ oder    
        - gleich „D“

        Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

     1. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der Hive-Metastore-Datenbank ab, die überprüft werden soll.

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="Überprüfen einer Hive-Quelle" border="true":::

6.  Klicken Sie auf **Continue** (Weiter).

7.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

8.  Sehen Sie sich Ihre Überprüfung noch einmal an, und klicken Sie dann auf **Speichern und ausführen**.

## <a name="viewing-your-scans-and-scan-runs"></a>Anzeigen Ihrer Überprüfungen und Überprüfungsausführungen

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** aus.

2. Wählen Sie die gewünschte Datenquelle aus. Daraufhin wird eine Liste mit vorhandenen Überprüfungen für diese Datenquelle angezeigt.

3. Wählen Sie die Überprüfung aus, für deren Ergebnisse Sie sich interessieren.

4. Auf dieser Seite werden alle vorherigen Überprüfungsausführungen sowie die Metriken und der Status der jeweiligen Überprüfungsausführung angezeigt. Außerdem sehen Sie hier, ob es sich um eine geplante oder um eine manuelle Überprüfung gehandelt hat, auf wie viele Ressourcen Klassifizierungen angewendet wurden, wie viele Ressourcen insgesamt ermittelt wurden, wann die Überprüfung gestartet und beendet wurde und wie lange die Überprüfung insgesamt gedauert hat.

## <a name="manage-your-scans"></a>Verwalten Ihrer Überprüfungen

Gehen Sie zum Verwalten oder Löschen einer Überprüfung wie folgt vor:

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** und anschließend die gewünschte Datenquelle aus.

2. Wählen Sie die Überprüfung aus, die Sie verwalten möchten. Wenn Sie die Überprüfung bearbeiten möchten, wählen Sie **Bearbeiten** aus.

3. Wenn Sie Ihre Überprüfung löschen möchten, wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

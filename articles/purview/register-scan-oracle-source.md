---
title: Registrieren einer Oracle-Quelle und Einrichten von Überprüfungen in Azure Purview
description: In diesem Artikel erfahren Sie, wie Sie eine Oracle-Quelle in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: a9234a2bcce6a754a96d1f323af9f08a9697ff03
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725237"
---
# <a name="register-and-scan-oracle-source"></a>Oracle-Quelle registrieren und überprüfen

In diesem Artikel erfahren Sie, wie Sie eine Oracle-Datenbank in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Oracle-Quelle unterstützt die **vollständige Überprüfung**, um Metadaten aus einer Oracle-Datenbank zu extrahieren, und ruft die **Herkunft** zwischen Datasets ab.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Sie müssen den Oracle JDBC-Treiber [hier](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) manuell auf den virtuellen Computer herunterladen, auf dem die selbstgehostete Integration Runtime ausgeführt wird.

    > [!Note] 
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

5.  Unterstützte Versionen von Oracle-Datenbanken sind 6i bis 19c.

6.  Benutzerberechtigung: Schreibgeschützter Zugriff auf Systemtabellen ist erforderlich. Der Benutzer muss über die Berechtigung zum Erstellen einer Sitzung sowie die Rolle SELECT\_CATALOG\_ROLE verfügen. Alternativ kann dem Benutzer die SELECT-Berechtigung für jede einzelne Systemtabelle erteilt werden, von der dieser Connector Metadaten abfragt:
       > grant create session to \[Benutzer\];\
        grant select on all\_users to \[Benutzer\];\
        grant select on dba\_objects to \[Benutzer\];\
        grant select on dba\_tab\_comments to \[Benutzer\];\
        grant select on dba\_external\_locations to \[Benutzer\];\
        grant select on dba\_directories to \[Benutzer\];\
        grant select on dba\_mviews to \[Benutzer\];\
        grant select on dba\_clu\_columns to \[Benutzer\];\
        grant select on dba\_tab\_columns to \[Benutzer\];\
        grant select on dba\_col\_comments to \[Benutzer\];\
        grant select on dba\_constraints to \[Benutzer\];\
        grant select on dba\_cons\_columns to \[Benutzer\];\
        grant select on dba\_indexes to \[Benutzer\];\
        grant select on dba\_ind\_columns to \[Benutzer\];\
        grant select on dba\_procedures to \[Benutzer\];\
        grant select on dba\_synonyms to \[Benutzer\];\
        grant select on dba\_views to \[Benutzer\];\
        grant select on dba\_source to \[Benutzer\];\
        grant select on dba\_triggers to \[Benutzer\];\
        grant select on dba\_arguments to \[Benutzer\];\
        grant select on dba\_sequences to \[Benutzer\];\
        grant select on dba\_dependencies to \[Benutzer\];\
        grant select on dba\_type\_attrs to \[user\];\
        grant select on V\_\$INSTANCE to \[Benutzer\];\
        grant select on v\_\$database to \[Benutzer\];
    
## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die einzige unterstützte Authentifizierung für eine Oracle-Quelle ist die **Standardauthentifizierung**.

## <a name="register-an-oracle-source"></a>Registrieren einer Oracle-Quelle

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue Oracle-Quelle zu registrieren:

1.  Navigieren Sie zu Ihrem Purview-Konto.
2.  Wählen Sie im linken Navigationsbereich **Data Map** aus.
3.  Wählen Sie **Registrieren** aus.
4.  Wählen Sie unter „Quellen registrieren“ die Option **Oracle** aus. Wählen Sie **Weiter**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Quellen registrieren" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (Oracle)** wie folgt vor:

1.  Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2.  Geben Sie unter **Host** den Hostnamen ein, mit dem eine Verbindung mit einer Oracle-Quelle hergestellt werden soll. Hierfür kommt infrage:
    - Ein Hostname, der von JDBC verwendet wird, um eine Verbindung mit dem Datenbankserver herzustellen. Beispielsweise MyDatabaseServer.com oder eine
    - IP-Adresse. Beispielsweise 192.169.1.2 oder
    - Es ist eine vollqualifizierte JDBC-Verbindungszeichenfolge. Beispiel:
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD\_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT\_DATA=(SERVICE\_NAME=orcl)))

3.  Geben Sie die **Portnummer** ein, die von JDBC verwendet wird, um eine Verbindung mit dem Datenbankserver herzustellen (standardmäßig 1521 für Oracle).

4.  Geben Sie den von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver verwendeten **Oracle-Dienstnamen** ein.

5.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

6.  Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Klicken Sie im Verwaltungscenter auf Integration Runtimes. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie die registrierte Oracle-Quelle aus.

4.  Wählen Sie **+ Neue Überprüfung** aus.

5.  Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:
    - beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.        
    - Geben Sie den Benutzernamen an, der von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver im Eingabefeld „Benutzername“ verwendet wird.        
    - Speichern Sie das Benutzerkennwort, das von JDBC zum Herstellen einer Verbindung mit dem Datenbankserver im geheimen Schlüssel verwendet wird.

    d.  **Schema**: Eine durch Semikolons getrennte Liste mit einer Teilmenge von Schemas, die importiert werden sollen. Beispiel: Schema1; Schema2. Ist diese Liste leer, werden alle Benutzerschemas importiert. Alle Systemschemas (beispielsweise „SysAdmin“) und Objekte werden standardmäßig ignoriert. Wenn die Liste leer ist, werden alle verfügbaren Schemas importiert.
        Zu den zulässigen Schemanamensmustern mit SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“ wie im folgenden Beispiel: A%; %B; %C%; D
       -   beginnt mit „A“ oder        
       -   endet mit „B“ oder        
       -   enthält „C“ oder        
       -   gleich „D“

    Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.

6.  **Treiberspeicherort**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Dabei sollte es sich um den Pfad zu einem gültigen Speicherort eines JAR-Ordners handeln.
    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

7.  **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Abhängig von der Größe der zu überprüfenden SAP S/4HANA-Quelle.

    > [!Note] 
    > Faustregel: 1 GB Arbeitsspeicher pro 1000 Tabellen.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Oracle überprüfen" border="true":::

8.  Klicken Sie auf **Continue** (Weiter).

9.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

10.  Sehen Sie sich Ihre Überprüfung noch einmal an, und klicken Sie dann auf **Speichern und ausführen**.

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
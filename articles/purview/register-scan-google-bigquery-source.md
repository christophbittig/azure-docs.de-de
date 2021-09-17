---
title: Registrieren des Google BigQuery-Projekts und Einrichten von Scans in Azure Purview
description: In diesem Artikel erfahren Sie, wie Sie ein Google BigQuery-Projekt in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/15/2021
ms.openlocfilehash: d88df617e994f4a609b12dff0419fd673f8207d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721934"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>Registrieren und Überprüfen der Google BigQuery-Quelle (Vorschau)

In diesem Artikel erfahren Sie, wie Sie ein Google BigQuery-Projekt in Azure Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Google BigQuery-Quelle unterstützt die vollständige Überprüfung, um Metadaten aus einem Google BigQuery-Projekt zu extrahieren, und ruft die Herkunft zwischen den Datenobjekten ab.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Sie müssen [hier](https://cloud.google.com/bigquery/providers/simba-drivers) den JDBC-Treiber von Google BigQuery manuell auf den virtuellen Computer herunterladen, auf dem die selbstgehostete Integration Runtime ausgeführt wird

    > [!Note]
    > Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

5.  Die unterstützte Google BigQuery-Version ist 11.0.0

## <a name="register-a-google-bigquery-project"></a>Registrieren eines Google BigQuery-Projekts

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues Google BigQuery-Projekt zu registrieren:

1.  Navigieren Sie zu Ihrem Purview-Konto.
2.  Wählen Sie im linken Navigationsbereich **Data Map** aus.
3.  Wählen Sie **Registrieren** aus.
4.  Wählen Sie unter „Quellen registrieren“ die Option **Google BigQuery** aus. Wählen Sie **Weiter aus.** 
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="Registrieren der BigQuery-Quelle" border="true":::
   
Gehen Sie auf dem Bildschirm „Quellen registrieren“ (Google BigQuery) wie folgt vor:

1.  Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2.  Geben Sie die **ProjectID** ein. Dies sollte eine vollqualifizierte Projekt-ID sein. Beispiel: mydomain.com:myProject

3.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

4.  Klicken Sie auf **Registrieren**.
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Konfigurieren der BigQuery-Quelle" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Klicken Sie im Verwaltungscenter auf Integration Runtimes. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, richten Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime ein.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie das registrierte **BigQuery-Projekt** aus.

4.  Wählen Sie **+ Neue Überprüfung** aus.

5.  Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Anmeldeinformationen**: Stellen Sie beim Konfigurieren von BigQuery-Anmeldeinformationen Folgendes sicher:

    - Wählen Sie **Windows-Authentifizierung** als Authentifizierungstyp aus
    - Geben Sie im Feld Benutzername die E-Mail-ID des Dienstkontos an. Beispiel: xyz\@developer.gserviceaccount.com
    - Speichern Sie die Datei mit dem privaten Schlüssel des Dienstkontos im JSON-Format im Geheimnis des Schlüsseltresors

    Klicken Sie zum Erstellen eines neuen privaten Schlüssels auf der Google-Cloudplattform im Navigationsmenü auf IAM & Admin – \> Dienstkonten – \> Projekt auswählen – \>Klicken Sie auf die E-Mail-Adresse des Dienstkontos, für das Sie einen Schlüssel erstellen möchten – \> Klicken Sie auf die Registerkarte –  **Schlüssel** – \> Klicken Sie auf das Dropdownmenü **Schlüssel hinzufügen** und wählen Sie dann Neuen Schlüssel erstellen aus. Wählen Sie jetzt das JSON-Format aus.

      > [!Note]
      > Der Inhalt des privaten Schlüssels wird in einer temporären Datei auf dem virtuellen Computer gespeichert, wenn Überprüfungsprozesse ausgeführt werden. Diese temporäre Datei wird gelöscht, nachdem die Überprüfungen erfolgreich abgeschlossen wurden. Im Falle eines Überprüfungsfehlers, wird das System den Vorgang bis zum Erfolg fortsetzen. Stellen Sie sicher, dass der Zugriff auf dem virtuellen Computer, auf dem SHIR ausgeführt wird, entsprechend eingeschränkt ist.**

    Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    d.  **Treiberspeicherort**: Geben Sie den Pfad zum Speicherort des JDBC-Treibers auf dem virtuellen Computer an, auf dem die selbstgehostete Integration Runtime ausgeführt wird. Dabei sollte es sich um den Pfad zu einem gültigen Speicherort eines JAR-Ordners handeln    
    > [!Note]
    > Der Treiber sollte für alle Konten auf dem virtuellen Computer zugänglich sein. Installieren Sie ihn bitte nicht in einem Benutzerkonto.

    e.  **Dataset**: Geben Sie eine Liste der zu importierenden BigQuery-Datasets an. Zum Beispiel: dataset1; dataset2. Wenn die Liste leer ist, werden alle verfügbaren Datasets importiert.
        Zu den zulässigen Dataset-Namensmustern mit einer SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“, 

    z. B. A%; %B; %C%; D
    - beginnt mit „A“ oder
    - endet mit „B“ oder
    - enthält „C“ oder
    - gleich „D“    
Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.
    
    f.  **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies ist abhängig von der Größe des zu überprüfenden Google BigQuery-Projekts.
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Überprüfen der BigQuery-Quelle" border="true":::

6.  Klicken Sie auf **Verbindung testen**.

7.  Klicken Sie auf **Continue** (Weiter).

8.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

9.  Sehen Sie sich Ihre Überprüfung noch einmal an, und klicken Sie dann auf **Speichern und ausführen**.

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
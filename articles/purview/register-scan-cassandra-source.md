---
title: Registrieren von Cassandra als Quelle und Einrichten von Überprüfungen
description: In diesem Artikel erfahren Sie, wie Sie einen Cassandra-Server in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: dcc0ef65654d8100a1c96cdb0c84a6ac758587a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212727"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Registrieren und Überprüfen einer Cassandra-Quelle (Vorschau)

In diesem Artikel erfahren Sie, wie Sie einen Cassandra-Server in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Cassandra-Quelle unterstützt die vollständige Überprüfung, um Metadaten aus einem Cassandra-Server zu extrahieren, und ruft die Herkunft zwischen Datenobjekten ab.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Unterstützt werden die Cassandra-Serverversionen 3.x bis 4.x.

## <a name="register-a-cassandra-server"></a>Registrieren eines Cassandra-Servers

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog einen neuen Cassandra-Server zu registrieren:

1.  Navigieren Sie zu Ihrem Purview-Konto.
2.  Wählen Sie im linken Navigationsbereich **Data Map** aus.
3.  Wählen Sie **Registrieren** aus.
4.  Wählen Sie unter „Quellen registrieren“ die Option **Cassandra** aus. Wählen Sie **Weiter** aus.
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Registrieren der Cassandra-Quelle" border="true":::
   
Gehen Sie auf dem Bildschirm „Quellen registrieren“ (Cassandra) wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2. Geben Sie im Feld **Host** die Serveradresse ein, unter der der Cassandra-Server ausgeführt wird. Beispiel: 20.190.193.10

3. Geben Sie den vom Cassandra-Server verwendeten Port in das Feld **Port** ein.
4. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

5.  Wählen Sie **Registrieren**.
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Konfigurieren der Cassandra-Quelle" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, richten Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime ein.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie den registrierten **Cassandra**-Server aus.

4.  Wählen Sie **+ Neue Überprüfung** aus.

5.  Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Anmeldeinformation**: Stellen Sie beim Konfigurieren von Cassandra-Anmeldeinformationen Folgendes sicher:

    - Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
    - Geben Sie im Feld „Benutzername“ den Benutzernamen an, in dessen Auftrag die Verbindung hergestellt wird. 
    - Speichern Sie das Kennwort des Cassandra-Benutzers, in dessen Auftrag die Verbindung hergestellt wird, im Geheimnis des Schlüsseltresors.

    Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    d.  **Keyspaces**: Geben Sie eine Liste der Cassandra-Keyspaces an, die importiert werden sollen. Mehrere Keyspaces müssen durch ein Semikolon getrennt werden. Beispiel: keyspace1; keyspace2. Wenn die Liste leer ist, werden alle verfügbaren Keyspaces importiert.
    Zu den zulässigen Keyspacenamensmustern mit einer SQL-LIKE-Ausdruckssyntax zählt auch die Verwendung von „%“, 

    z. B. A%; %B; %C%; D
    - beginnt mit „A“ oder
    - endet mit „B“ oder
    - enthält „C“ oder
    - gleich „D“    
Die Verwendung von „NOT“ oder Sonderzeichen ist nicht zulässig.
    
    f. **Secure Sockets Layer (SSL) verwenden**: Wählen Sie TRUE oder FALSE aus, um eine Benachrichtigung dafür einzurichten, ob Secure Sockets Layer (SSL) bei der Verbindungsherstellung mit dem Cassandra-Server verwendet werden muss. Standardmäßig ist dieser Wert auf Falsefestgelegt.

    g. **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der zu überprüfenden Cassandra-Servers ab.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Überprüfen der Cassandra-Quelle" border="true":::

6.  Wählen Sie **Verbindung testen** aus.

7.  Wählen Sie **Weiter**.

8.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

9.  Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

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
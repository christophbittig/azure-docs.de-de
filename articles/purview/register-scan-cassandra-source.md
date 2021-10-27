---
title: Registrieren und Überprüfen einer Cassandra-Quelle
description: In diesem Artikel wird beschrieben, wie Sie einen Cassandra-Server in Azure Purview registrieren und eine Überprüfung zum Extrahieren von Metadaten einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5f8feffebff71c25f2a0d62d775894e7c4431615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005314"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Registrieren und Überprüfen einer Cassandra-Quelle (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einen Cassandra-Server in Azure Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können mithilfe von Purview vollständige Überprüfungen in Cassandra ausführen, um Metadaten und die Herkunft zwischen Datenressourcen zu extrahieren. 

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Ist dieses Update nicht installiert, können Sie es [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Stellen Sie sicher, dass der Cassandra-Server Version 3.*x* oder 4.*x* hat.

## <a name="register-a-cassandra-server"></a>Registrieren eines Cassandra-Servers

So registrieren Sie einen neuen Cassandra-Server in Ihrem Datenkatalog

1.  Navigieren Sie zu Ihrem Purview-Konto.
2.  Wählen Sie im linken Bereich **Data Map** aus.
3.  Wählen Sie **Registrieren**.
4.  Wählen Sie auf dem Bildschirm **Quellen registrieren** die Option **Cassandra** und anschließend **Weiter** aus:

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Screenshot: Bildschirm „Quellen registrieren“" border="true":::
   
1. Gehen Sie auf dem Bildschirm **Register sources (Cassandra)** (Quellen registrieren (Cassandra)) wie folgt vor:

   1. Geben Sie einen **Namen** ein. Die Datenquelle verwendet diesen Namen im Katalog.

   2. Geben Sie im Feld **Host** die Serveradresse ein, unter der der Cassandra-Server ausgeführt wird. Beispiel: 20.190.193.10

   3. Geben Sie in das Feld **Port** den vom Cassandra-Server verwendeten Port ein.
   4. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Screenshot: Bildschirm „Register sources (Cassandra)“ (Quellen registrieren (Cassandra))" border="true":::
   5.  Wählen Sie **Registrieren**.


## <a name="create-and-run-a-scan"></a>Erstellen und führen Sie eine Überprüfung aus

So erstellen Sie eine neue Überprüfung und führen sie aus

1.  Wählen Sie im Verwaltungscenter **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Ist keine eingerichtet, führen Sie [diese Schritte zum Einrichten einer selbstgehosteten Integration Runtime](./manage-integration-runtimes.md) aus.
    

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie den registrierten Cassandra-Server aus.

4.  Wählen Sie **Neue Überprüfung** aus.

5.  Geben Sie die folgenden Details an:

    a.  **Name**: Geben Sie einen Namen für die Überprüfung an.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Anmeldeinformationen**: Achten Sie beim Konfigurieren der Cassandra-Anmedeinformationen auf Folgendes:

    - Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
    - Geben Sie in das Feld **Benutzername** den Namen des Benutzers ein, für den Sie die Verbindung herstellen. 
    - Speichern Sie im Geheimnis des Schlüsseltresors das Kennwort des Cassandra-Benutzers, für den Sie die Verbindung herstellen.

    Weitere Informationen finden Sie unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md).

    d.  **Keyspaces**: Geben Sie eine Liste der zu importierenden Cassandra-Keyspaces an. Mehrere Keyspaces müssen durch Semikolons getrennt werden. Beispiel: keyspace1; keyspace2. Wenn die Liste leer ist, werden alle verfügbaren Keyspaces importiert.
    
    Sie können Keyspacenamensmuster mit SQL-Ausdruckssyntax vom Typ LIKE verwenden (einschließlich %). 

    Beispiel: A%; %B; %C%; D

    Dieser Ausdruck bedeutet Folgendes:
    - Beginnt mit A. Oder:
    - Endet mit „B“. Oder:
    - Enthält C. Oder:
    - Entspricht D.    

    NOT oder Sonderzeichen dürfen nicht verwendet werden.
    
    e. **Secure Sockets Layer (SSL) verwenden**: Wählen Sie **True** oder **False** aus, um anzugeben, ob Secure Sockets Layer (SSL) bei der Verbindungsherstellung mit dem Cassandra-Server verwendet werden soll. Standardmäßig wird diese Option auf **False** festgelegt.

    f. **Maximal verfügbarer Arbeitsspeicher**: Geben Sie den maximalen Arbeitsspeicher (in GB) an, der auf dem virtuellen Computer für Überprüfungsprozesse verfügbar ist. Dieser Wert hängt von der Größe des zu überprüfenden Cassandra-Servers ab.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Überprüfen der Cassandra-Quelle" border="true":::

6.  Wählen Sie **Verbindung testen** aus.

7.  Wählen Sie **Weiter**.

8.  Wählen Sie einen **Überprüfungstrigger** aus. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

9.  Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

## <a name="view-your-scans-and-scan-runs"></a>Betrachten Sie Ihre Überprüfungen und Überprüfungsausführungen

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** aus.

2. Wählen Sie die Datenquelle aus, deren Überprüfungen Sie anzeigen möchten. Daraufhin wird eine Liste mit vorhandenen Überprüfungen für diese Datenquelle angezeigt.

3. Wählen Sie die Überprüfung aus, deren Ergebnisse Sie anzeigen möchten.

   Auf der daraufhin angezeigten Seite werden alle vorherigen Überprüfungsausführungen zusammen mit den Metriken und dem jeweiligen Status angezeigt. 
   Die Seite enthält außerdem folgende Informationen: 
   - Angabe, ob die Überprüfung geplant war oder manuell durchgeführt wurde 
   - Anzahl der Ressourcen, auf die Klassifizierungen angewendet wurden 
   - Gesamtanzahl der ermittelten Ressourcen 
   - Start- und Endzeit der Überprüfung
   - Dauer der Überprüfung

## <a name="manage-your-scans"></a>Verwalten Ihrer Überprüfungen

So verwalten oder löschen Sie eine Überprüfung

1. Navigieren Sie zum Verwaltungscenter. Wählen Sie im Abschnitt **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** aus. Wählen Sie dann die Datenquelle aus, deren Überprüfung Sie verwalten möchten.

2. Wählen Sie die Überprüfung aus, die Sie verwalten möchten. 
   - Wenn Sie die Überprüfung bearbeiten möchten, wählen Sie **Bearbeiten** aus.

   - Sie können die Überprüfung löschen, indem Sie **Löschen** wählen.

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)

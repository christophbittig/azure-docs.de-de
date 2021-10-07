---
title: Registrieren von erwin Mart und Einrichten von Überprüfungen
description: In diesem Artikel erfahren Sie, wie Sie erwin Mart in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 8077658b644853f9ce0789a1d0863b3bde902818
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211525"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>Registrieren und Überprüfen von erwin Mart Server (Vorschau)

In diesem Artikel erfahren Sie, wie Sie eine erwin Mart Server-Instanz in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die erwin-Quelle unterstützt die vollständige Überprüfung, um Metadaten aus einer erwin Mart Server-Instanz zu extrahieren. Die Metadaten enthalten:

1.  nur logische Modelle mit Entitäten, Attributen und Domänen ODER
2.  Nur physische Modelle mit Tabellen, Spalten und Datentypen ODER
3.  Logisch-physische Modelle

Diese Datenquelle ruft auch die Herkunft von Datenobjekten ab.

> [!Note]
> erwin als Quelle wird derzeit in der Vorschau unterstützt. Registrieren Sie diese Quelle, richten Sie Ihre Überprüfungen in Ihren Purview-Konten ein, die nicht für die Produktion verwendet werden, und geben Sie uns Feedback.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter  
    [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Stellen Sie sicher, dass Sie die selbstgehostete Integration Runtime auf der VM ausführen, auf der die erwin Mart-Instanz ausgeführt wird.

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf der VM installiert ist, auf die selbstgehostete Integration Runtime ausgeführt wird. Sollte diese Komponente nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Unterstützt werden die erwin Mart-Versionen 9.x bis 2021.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die einzige unterstützte Authentifizierung für eine Erwin Mart-Quelle ist die **Serverauthentifizierung** in Form von Benutzername und Kennwort.

## <a name="register-an-erwin-mart"></a>Registrieren einer erwin Mart-Instanz

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue erwin Mart-Instanz zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich **Data Map** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter „Register sources“ (Quellen registrieren) die Option **erwin** aus. Wählen Sie **Weiter** aus.
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="Registrieren der Quelle „erwin“" border="true":::

Gehen Sie auf dem Bildschirm „Register sources (erwin)“ (Quellen registrieren (erwin)) wie folgt vor:

1.  Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2.  Geben Sie den **Servernamen** für erwin Mart Server ein. Dies ist der Netzwerkhostname, mit dem die Verbindung zur erwin Mart Server-Instanz hergestellt wird. Beispiel: localhost.

3.  Geben Sie die **Nummer des Ports** ein, über den die Verbindung zur erwin Mart Server-Instanz hergestellt wird. Standardmäßig ist dies Port 18170.

4.  Geben Sie den **Anwendungsnamen** ein.

    >[!Note]
    > Die oben genannten Details finden Sie, indem Sie zu Ihrer Instanz von erwin Data Modeler navigieren. Wählen Sie „Mart“ \> „Connect“ (Verbinden) aus, um Details zum Servernamen, Port und Anwendungsnamen anzuzeigen.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Erwin-Details suchen" border="true":::
    
5.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

6.  Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Quelle registrieren" border="true":::
    

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Stellen Sie sicher, dass eine selbstgehostete Integration Runtime auf der VM eingerichtet ist, auf der die erwin Mart-Instanz ausgeführt wird. Falls sie nicht eingerichtet ist, richten Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime ein.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie die registrierte **erwin** Mart-Instanz aus.

4.  Wählen Sie **+ Neue Überprüfung** aus.

5.  Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Servername, Port** und **Anwendungsname** werden automatisch auf Basis der Werte ausgefüllt, die bei der Registrierung ausgefüllt wurden.

    d.  **Anmeldeinformationen:** Wählen Sie die Anmeldeinformationen aus, die für die Verbindung mit Ihrer erwin Mart Server-Instanz konfiguriert sind. Achten Sie beim Erstellen von Anmeldeinformationen auf Folgendes:
    - Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
    - Geben Sie in das Feld „Benutzername“ den Benutzernamen Ihrer erwin Mart Server-Instanz ein.
    - Speichern Sie Ihr Benutzerkennwort für die Serverauthentifizierung im Geheimnis des Schlüsseltresors.

    Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    e.  **Internetinformationsdienste (IIS) verwenden:** Wählen Sie TRUE oder FALSE aus, um eine Benachrichtigung zu dafür einzurichten, ob die Microsoft-Internetinformationsdienste (IIS) bei der Verbindungsherstellung mit der erwin Mart Server-Instanz verwendet werden müssen. Standardmäßig ist dieser Wert auf Falsefestgelegt.

    f.  **Secure Sockets Layer (SSL) verwenden:** Wählen Sie TRUE oder FALSE aus, um eine Benachrichtigung zu dafür einzurichten, ob Secure Sockets Layer (SSL) bei der Verbindungsherstellung mit der erwin Mart Server-Instanz verwendet werden muss. Standardmäßig ist dieser Wert auf Falsefestgelegt.

    > [!Note]
    > Dieser Parameter gilt nur für erwin Mart-Version 9.1 oder höher.

    g.  **Durchsuchungsmodus:** Wählen Sie den Modus zum Durchsuchen von erwin Mart aus. Mögliche Optionen sind „Libraries and Models“ (Bibliotheken und Modelle) oder „Libraries only“ (Nur Bibliotheken).

    h.  **Modelle:** Legen Sie den Bereich ihrer Überprüfung durch Bereitstellen einer durch Semikolons getrennten Liste von erwin-Modelllocatorzeichenfolgen fest. Beispiel: mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    i.  **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der zu überprüfenden erwin Mart-Instanz ab.
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Auslösen der Überprüfung" border="true":::
   

6.  Wählen Sie **Verbindung testen** aus.

7.  Wählen Sie **Weiter**.

8.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

9.  Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie **Speichern und ausführen** aus.

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

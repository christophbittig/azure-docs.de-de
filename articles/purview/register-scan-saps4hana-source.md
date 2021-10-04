---
title: Registrieren einer SAP S/4HANA-Quelle und Einrichten von Überprüfungen in Azure Purview
description: In diesem Artikel erfahren Sie, wie Sie eine SAP S/4HANA-Quelle in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 45ea054394c5f74a6a3a34b9a69df5c5b07a632f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212592"
---
# <a name="register-and-scan-a-sap-s4hana-source"></a>Registrieren und Überprüfen einer SAP S/4HANA-Quelle

In diesem Artikel erfahren Sie, wie Sie eine SAP S/4HANA-Quelle in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die SAP S/4HANA-Quelle unterstützt die **vollständige Überprüfung**, um Metadaten aus einer SAP S/4HANA-Datenbank zu extrahieren, und ruft die **Herkunft** zwischen Datasets ab.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Laden Sie den [SAP Connector für Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) (64 Bit) von der SAP-Website herunter, und installieren Sie ihn auf dem Computer mit der selbstgehosteten Integration Runtime. Während der Installation müssen Sie im Fenster **Optionale Einrichtungsschritte** die Option **Assemblys in GAC installieren** auswählen.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="Voraussetzung" border="true":::

5.  Der Connector liest Metadaten aus SAP mithilfe der [SAP Java-Connector-API 3.0 (JCo)](https://support.sap.com/en/product/connectors/jco.html). Stellen Sie darum sicher, dass der Java-Connector auf dem virtuellen Computer verfügbar ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.
    Stellen Sie sicher, dass Sie die richtige JCo-Verteilung für Ihre Umgebung verwenden. Stellen Sie z. B. auf einem Microsoft Windows-Computer sicher, dass die Dateien „sapjco3.jar“ und „sapjco3.dll“ verfügbar sind.

    > [!Note] 
    >Der Treiber muss für alle Konten des virtuellen Computers zugänglich sein. Installieren Sie ihn nicht in einem Benutzerkonto.

6.  Stellen Sie das ABAP-Funktionsmodul zur Metadatenextraktion auf dem SAP-Server bereit, indem Sie die Schritte im [Bereitstellungshandbuch für ABAP-Funktionen](abap-functions-deployment-guide.md) befolgen. Sie benötigen ein ABAP-Entwicklerkonto, um das RFC-Funktionsmodul auf dem SAP-Server zu erstellen. Das Benutzerkonto muss über ausreichende Berechtigungen zum Herstellen einer Verbindung mit dem SAP-Server und zum Ausführen der folgenden RFC-Funktionsmodule verfügen:
    -   STFC_CONNECTION (Konnektivität überprüfen)
    -   RFC_SYSTEM_INFO (Systeminformationen überprüfen)

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Die einzige unterstützte Authentifizierung für eine SAP S/4HANA-Quelle ist die **Standardauthentifizierung**.

## <a name="register-sap-s4hana-source"></a>Registrieren der SAP S/4HANA-Quelle

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog eine neue SAP S/4HANA-Quelle zu registrieren:

1.  Navigieren Sie zu Ihrem Purview-Konto.
2.  Wählen Sie im linken Navigationsbereich **Data Map** aus.
3.  Wählen Sie **Registrieren** aus.
4.  Wählen Sie unter „Quellen registrieren“ die Option **SAP S/4HANA** aus. Wählen Sie **Weiter** aus.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="Registrieren von SAPS/4HANA-Optionen" border="true":::

Gehen Sie auf dem Bildschirm **Quellen registrieren (SAP S/4HANA)** wie folgt vor:

1.  Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2.  Geben Sie den Namen des **Anwendungsservers** zum Herstellen der Verbindung mit der SAP S/4HANA-Quelle ein. Es kann sich auch um eine IP-Adresse des SAP-Anwendungsserverhosts handeln.

3.  Geben Sie die SAP-**Systemnummer** ein. Dies ist eine zweistellige ganze Zahl zwischen 00 und 99.

4.  Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

5.  Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="Registrieren von SAP S/4HANA" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1.  Wählen Sie im Verwaltungscenter „Integration Runtimes“ aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Falls sie nicht eingerichtet ist, erstellen Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime.

2.  Navigieren Sie zu **Quellen**.

3.  Wählen Sie die registrierte SAP S/4HANA-Quelle aus.

4.  Wählen Sie **+ New scan** (+ Neue Überprüfung) aus.

5.  Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  **Anmeldeinformationen**: Wählen Sie die Anmeldeinformationen zur Herstellung der Verbindung mit Ihrer Datenquelle aus. Stellen Sie sicher, dass Sie:

    -   beim Erstellen von Anmeldeinformationen die Standardauthentifizierung auswählen.
    -   Geben Sie im Eingabefeld „Benutzername“ eine Benutzer-ID zum Herstellen der Verbindung mit dem SAP-Server ein.
    -   Speichern Sie das Benutzerkennwort, das verwendet wird, um eine Verbindung mit dem SAP-Server herzustellen, im geheimen Schlüssel.

    d.  **Client-ID**: Geben Sie hier die SAP-Systemclient-ID ein. Der Client wird mit einer dreistelligen ganzen Zahl von 000 bis 999 identifiziert.

    e.  **JCo-Bibliothekspfad**: Geben Sie den Pfad zu dem Ordner an, in dem sich die JCo-Bibliotheken befinden.

    f.  **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Abhängig von der Größe der zu überprüfenden SAP S/4HANA-Quelle.
    > [!Note] 
    > Faustregel: 1 GB Arbeitsspeicher pro 1000 Tabellen.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="SAP S/4HANA überprüfen" border="true":::

6.  Wählen Sie **Weiter**.

7.  Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

8.  Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

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

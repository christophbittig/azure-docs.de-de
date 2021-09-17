---
title: Registrieren von Looker und Einrichten von Überprüfungen in Azure Purview
description: In diesem Artikel erfahren Sie, wie Sie eine Looker-Quelle in Azure Purview registrieren und eine Überprüfung einrichten.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: ed524382bf1fcc0c93fc61dd1c0450d69a16b74c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749406"
---
# <a name="register-and-scan-lookerpreview"></a>Registrieren und Überprüfen von Looker (Vorschau)

In diesem Artikel erfahren Sie, wie Sie eine Looker Server-Instanz in Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Die Looker-Quelle unterstützt die vollständige Überprüfung, um Metadaten von einem Looker-Server zu extrahieren. Sie importiert Metadaten von einem Looker-Server, einschließlich Datenbankverbindungen, LookML-Modellen und den zugehörigen Berichten (Looks und Dashboards). Diese Datenquelle ruft auch die Herkunft von Datenobjekten ab.

> [!Note]
> Looker als Quelle wird derzeit in der privaten Vorschau unterstützt. Registrieren Sie diese Quelle, richten Sie Ihre Überprüfungen in Ihren Purview-Konten ein, die nicht für die Produktion verwendet werden, und geben Sie uns Feedback.

## <a name="prerequisites"></a>Voraussetzungen

1.  Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
    Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

3.  Stellen Sie sicher, dass \"Visual C++ Redistributable 2012 Update 4\" auf der VM installiert ist, auf die selbstgehostete Integration Runtime ausgeführt wird. Sollte diese Komponente nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/download/details.aspx?id=30679) herunterladen.

4.  Die unterstützte Looker-Serverversion ist 7.2

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Ein API3-Schlüssel ist erforderlich, um eine Verbindung mit dem Looker-Server herzustellen. Der API3-Schlüssel besteht aus einer öffentlichen Client_ID und einem privaten Client_Geheimnis und folgt einem OAuth2-Authentifizierungsmuster.

## <a name="register-a-looker-server"></a>Registrieren eines Looker-Servers

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog einen neuen Looker-Server zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
2. Wählen Sie im linken Navigationsbereich **Data Map** aus.
3. Wählen Sie **Registrieren** aus.
4. Wählen Sie unter „Quellen registrieren“ die Option **Looker** aus. Wählen Sie **Weiter** aus.
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="Registrieren einer Looker-Quelle" border="true":::


Gehen Sie auf dem Bildschirm „Registrieren einer Looker-Quelle“ wie folgt vor:

1. Geben Sie einen **Namen** ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.

2. Geben Sie die Looker API-URL in dem Feld **Server API-URL** ein. Der Standardport für API-Anforderungen ist der Port 19999. Außerdem benötigen alle Looker-API-Endpunkte eine HTTPS-Verbindung. Zum Beispiel, „https://azurepurview.cloud.looker.com“

3. Optional: Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung.

4. Wählen Sie „Fertig stellen“ aus, um die Datenquelle zu registrieren.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="Überprüfen der Looker-Quelle" border="true":::

## <a name="creating-and-running-a-scan"></a>Erstellen und Ausführen einer Überprüfung

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Klicken Sie im Verwaltungscenter auf Integration Runtimes. Falls sie nicht eingerichtet ist, richten Sie mit den [hier](./manage-integration-runtimes.md) beschriebenen Schritten eine selbstgehostete Integration Runtime ein.

2. Navigieren Sie zu **Quellen**.

3. Wählen Sie den registrierten **Looker**-Server aus.

4. Wählen Sie **+ Neue Überprüfung** aus.

5. Geben Sie folgende Details an:

    a.  **Name**: Der Name der Überprüfung.

    b.  **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    c.  Die **Server-API-URL** wird basierend auf dem Wert, der während der Registrierung eingegeben wurde, automatisch aufgefüllt.

    d.  **Anmeldeinformation**: Stellen Sie beim Konfigurieren von Looker-Anmeldeinformationen Folgendes sicher:

    - Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
    - Geben Sie die Client-ID Ihres Looker API3-Schlüssels im Feld Benutzername an
    - Speichern Sie das Client-Geheimnis Ihre Ihres Looker API3-Schlüssels als Geheimnis in Ihrem Schlüsseltresor.

    **Hinweis**: Um auf die Client-ID und das Client-Geheimnis zuzugreifen, navigieren Sie zu Looker – \>Admin – \>Benutzer – \>Klicken Sie auf **Edit** bei einem Benutzer –\> Klicken Sie auf **Bearbeiten von Schlüsseln**  -\>. Benutzen Sie die Client-ID oder das Client Geheimnis um ein neues zu erstellen.
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="Abrufen von Looker-Details" border="true":::
    

    Weitere Informationen zu Anmeldeinformationen finden Sie [hier](manage-credentials.md).

    e.  **Projektfilter** – Legen Sie den Bereich ihrer Überprüfung durch Bereitstellen einer durch Semikolons getrennten Liste von Looker fest. Diese Option wird verwendet, um das Aussehen und die Dashboards nach Ihrem übergeordneten Projekt auszuwählen.

    f.  **Maximal verfügbarer Arbeitsspeicher**: Maximaler Arbeitsspeicher (in GB), der auf dem virtuellen Computer des Kunden für Überprüfungsprozesse verfügbar ist. Dies hängt von der Größe der zu überprüfenden erwin Mart-Instanz ab.

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Auslösen der Überprüfung" border="true":::

6. Klicken Sie auf **Testverbindung**.

7. Klicken Sie auf **Continue** (Weiter).

8. Wählen Sie den **Auslöser für die Überprüfung**. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

9. Sehen Sie sich Ihre Überprüfung noch einmal an, und klicken Sie dann auf **Speichern und ausführen**.

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

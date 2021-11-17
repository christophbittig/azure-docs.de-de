---
title: Verbinden mit und Verwalten von Cassandra
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit Cassandra herstellen und Ihre Cassandra-Quelle mit den Features von Purview überprüfen und verwalten.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 80f396e9b0bf53e25b188c42da6be6b8ad7f6061
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851893"
---
# <a name="connect-to-and-manage-cassandra-in-azure-purview-preview"></a>Verbinden mit und Verwalten von Cassandra in Azure Purview (Vorschau)

In diesem Artikel wird beschrieben, wie Sie Cassandra in Azure Purview registrieren, authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-cassandra.md)|

> [!Important]
> Unterstützt werden die Cassandra-Serverversionen 3.*x* oder 4.*x*.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

* Richten Sie die neueste [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) ein.
  Weitere Informationen finden Sie im [Leitfaden zum Erstellen und Konfigurieren einer selbstgehosteten Integrationslaufzeit](../data-factory/create-self-hosted-integration-runtime.md).

* Stellen Sie sicher, dass [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) auf dem virtuellen Computer installiert ist, auf dem auch die selbstgehostete Integration Runtime installiert ist.

* Stellen Sie sicher, dass Visual C++ Redistributable für Visual Studio 2012 Update 4 auf dem Computer mit der selbstgehosteten Integration Runtime installiert ist. Wenn Sie dieses Update nicht installiert haben, [können Sie es hier herunterladen](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie Cassandra in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="steps-to-register"></a>Schritte zur Registrierung

So registrieren Sie einen neuen Cassandra-Server in Ihrem Datenkatalog

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Bereich **Data Map** aus.
1. Wählen Sie **Registrieren**.
1. Wählen Sie auf dem Bildschirm **Quellen registrieren** die Option **Cassandra** und anschließend **Weiter** aus:

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Screenshot: Bildschirm „Quellen registrieren“" border="true":::

1. Gehen Sie auf dem Bildschirm **Register sources (Cassandra)** (Quellen registrieren (Cassandra)) wie folgt vor:

   1. Geben Sie einen **Namen** ein. Die Datenquelle verwendet diesen Namen im Katalog.
   1. Geben Sie im Feld **Host** die Serveradresse ein, unter der der Cassandra-Server ausgeführt wird. Beispiel: 20.190.193.10
   1. Geben Sie in das Feld **Port** den vom Cassandra-Server verwendeten Port ein.
   1. Wählen Sie eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Screenshot: Bildschirm „Register sources (Cassandra)“ (Quellen registrieren (Cassandra))" border="true":::
   1. Wählen Sie **Registrieren**.

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um Cassandra zu überprüfen und automatisch Ressourcen zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

So erstellen Sie eine neue Überprüfung und führen sie aus

1. Wählen Sie im Verwaltungscenter **Integration Runtimes** aus. Vergewissern Sie sich, dass eine selbstgehosteten Integration Runtime eingerichtet ist. Ist keine eingerichtet, führen Sie [diese Schritte zum Einrichten einer selbstgehosteten Integration Runtime](./manage-integration-runtimes.md) aus.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie den registrierten Cassandra-Server aus.

1. Wählen Sie **Neue Überprüfung** aus.

1. Geben Sie die folgenden Details an:

    1. **Name**: Geben Sie einen Namen für die Überprüfung an.

    1. **Verbindung über Integration Runtime herstellen**: Wählen Sie die konfigurierte selbstgehostete Integration Runtime aus.

    1. **Anmeldeinformationen**: Achten Sie beim Konfigurieren der Cassandra-Anmedeinformationen auf Folgendes:

        * Wählen Sie **Standardauthentifizierung** als Authentifizierungsmethode aus.
        * Geben Sie in das Feld **Benutzername** den Namen des Benutzers ein, für den Sie die Verbindung herstellen. 
        * Speichern Sie im Geheimnis des Schlüsseltresors das Kennwort des Cassandra-Benutzers, für den Sie die Verbindung herstellen.

        Weitere Informationen finden Sie unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md).

    1. **Keyspaces**: Geben Sie eine Liste der zu importierenden Cassandra-Keyspaces an. Mehrere Keyspaces müssen durch Semikolons getrennt werden. Beispiel: keyspace1; keyspace2. Wenn die Liste leer ist, werden alle verfügbaren Keyspaces importiert.

        Sie können Keyspacenamensmuster mit SQL-Ausdruckssyntax vom Typ LIKE verwenden (einschließlich %).

        Beispiel: A%; %B; %C%; D

        Dieser Ausdruck bedeutet Folgendes:
        * Beginnt mit A. Oder:
        * Endet mit „B“. Oder:
        * Enthält C. Oder:
        * Entspricht D.

        NOT oder Sonderzeichen dürfen nicht verwendet werden.

    1. **Secure Sockets Layer (SSL) verwenden**: Wählen Sie **True** oder **False** aus, um anzugeben, ob Secure Sockets Layer (SSL) bei der Verbindungsherstellung mit dem Cassandra-Server verwendet werden soll. Standardmäßig wird diese Option auf **False** festgelegt.

    1. **Maximal verfügbarer Arbeitsspeicher**: Geben Sie den maximalen Arbeitsspeicher (in GB) an, der auf dem virtuellen Computer für Überprüfungsprozesse verfügbar ist. Dieser Wert hängt von der Größe des zu überprüfenden Cassandra-Servers ab.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Überprüfen der Cassandra-Quelle" border="true":::

1. Wählen Sie **Verbindung testen** aus.

1. Wählen Sie **Weiter**.

1. Wählen Sie einen **Überprüfungstrigger** aus. Sie können einen Zeitplan einrichten oder die Überprüfung einmalig ausführen.

1. Sehen Sie sich Ihre Überprüfung noch einmal an, und wählen Sie dann **Speichern und ausführen** aus.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)
